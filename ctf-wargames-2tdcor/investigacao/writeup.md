# 🕵️ Investigação

**Categoria:** CTF — Offensive Security / Forense de Logs (Windows Event Log) · **Pontuação:** 100 pts
**Alvo:** `http://45.55.126.134/`
**Flag:** `FIAP{192.168.37.129}`

## Enunciado

> Você, um talentoso hacker ético, recebeu uma dica incomum hoje. Parece que um administrador de sistema distraído esqueceu um arquivo crucial enquanto lidava com uma investigação de segurança no servidor. Este arquivo compactado contém uma mina de ouro de registros de atividade, incluindo um log específico que registra uma movimentação lateral suspeita na rede. O objetivo é simples: obter acesso ao arquivo, analisá-lo e identificar o endereço IP do atacante que se infiltrou na rede.

## Metodologia

### 1. Varredura de diretórios — sem sucesso direto

Diversas rodadas de `gobuster` (wordlists em português, inglês, SecLists) contra a raiz e `/backups/` **não encontraram o arquivo** — reforçando que uma varredura automatizada por wordlist não cobre 100% dos nomes possíveis.

### 2. Descoberta do arquivo

O arquivo foi localizado diretamente na raiz do site, com um nome curto e não convencional — **`investiga.zip`** (abreviação de "investigação", sem acento e truncado, fora do padrão das wordlists testadas):

```bash
curl -s -o investiga.zip "http://45.55.126.134/investiga.zip"
```

> 📝 **Nota metodológica:** nomes de arquivo "esquecidos" em produção costumam fugir de wordlists de diretório padrão — vale sempre complementar a varredura automatizada com tentativas manuais de nomes plausíveis e abreviações do próprio tema do desafio.

### 3. Identificação da proteção do arquivo

```bash
7z l -slt investiga.zip | grep -iE 'Method|Encrypted'
```

```text
Encrypted = +
Method = ZipCrypto Deflate
```

Todas as entradas do ZIP estavam criptografadas com o método legado **ZipCrypto** — conhecido por ser vulnerável a ataque de dicionário/força bruta (e a ataques de plaintext conhecido, embora aqui a quebra tenha sido via dicionário).

### 4. Quebra da senha do arquivo

```bash
zip2john investiga.zip > investiga.hash
john --wordlist=/usr/share/wordlists/rockyou.txt investiga.hash
```

**Senha recuperada:** `cintadalamhati`

### 5. Extração do conteúdo

```bash
unzip -P 'cintadalamhati' investiga.zip -d extraido
```

```text
inflating: extraido/disablestop-eventlog.evtx
inflating: extraido/ev.evtx
inflating: extraido/lateral.evtx
inflating: extraido/logs.evtx
inflating: extraido/lsys.evtx
inflating: extraido/many-events-system.evtx
inflating: extraido/mimikatz-privesc-hashdump.evtx
inflating: extraido/password-spray.evtx
inflating: extraido/psattack-security.evtx
```

Nomes de arquivo como `password-spray.evtx` e `mimikatz-privesc-hashdump.evtx` são **nomes-isca** — chamativos, mas vazios ou irrelevantes. Os dados reais estavam em arquivos com nomes genéricos (ex.: `ev.evtx`).

### 6. Conversão dos logs .evtx para XML

```python
from Evtx.Evtx import Evtx
from Evtx.Views import XML_HEADER

with Evtx("ev.evtx") as log:
    with open("ev.xml", "w", encoding="utf-8") as output:
        output.write(XML_HEADER)
        output.write("<Events>\n")
        for record in log.records():
            output.write(record.xml())
            output.write("\n")
        output.write("</Events>\n")
```

### 7. Identificação dos dois IPs suspeitos

O log revelou dois endereços IP distintos:
- Um realizando **múltiplas tentativas de logon com credenciais diferentes** em várias contas (padrão de *password spraying*/brute force — Event ID 4625, falhas de autenticação).
- Outro acessando repetidamente um **compartilhamento administrativo do Windows** (padrão de **movimentação lateral** via SMB — tipicamente Event ID 4624 com Logon Type 3, ou acesso a shares `ADMIN$`/`C$`).

```bash
grep -i '192.168.37.129' ev.xml | grep -oEi '<Data Name="(TargetUserName|IpAddress)">[^<]*</Data>'
```

```text
<Data Name="IpAddress">192.168.37.129</Data>
<Data Name="IpAddress">192.168.37.129</Data>
... (múltiplas ocorrências, todas o mesmo IP)
```

O IP `192.168.37.129` apareceu de forma consistente associado aos eventos de acesso ao compartilhamento administrativo — o padrão de **movimentação lateral** pedido no enunciado (e não o IP de brute force/password spray, que é um achado distinto, mas não o solicitado).

## Flag

```
FIAP{192.168.37.129}
```

## Lição aprendida

- Um arquivo "esquecido" em produção pode ter um nome curto e não óbvio — varredura por wordlist tem limites; tentativas manuais e inferência a partir do tema do desafio continuam relevantes.
- ZipCrypto é um método de criptografia legado e fraco — `zip2john` + `John the Ripper`/RockYou quebra senhas fracas rapidamente.
- Em triagem forense de `.evtx`, nomes de arquivo "chamativos" podem ser iscas; a evidência real pode estar em arquivos com nomes genéricos — todos devem ser inspecionados.
- Diferenciar padrões de ataque no log é essencial: *password spraying* (muitas tentativas de logon falho, múltiplas contas, um único IP) é um estágio diferente da cadeia de ataque de **movimentação lateral** (acesso bem-sucedido e repetido a um compartilhamento administrativo) — o enunciado pedia especificamente o segundo.

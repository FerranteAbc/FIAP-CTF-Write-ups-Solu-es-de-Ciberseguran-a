# 🛡️ FIAP — Portfólio de Projetos & CTFs de Defesa Cibernética

> Repositório central consolidando a documentação técnica, relatórios de Provas de Conceito (PoC), estratégias defensivas/ofensivas e write-ups detalhados de **Capture The Flag (CTF)** desenvolvidos no curso de **Defesa Cibernética** (*Ethical Hacking, Forensics & Secure DevOps*) da **FIAP**.

---

## 📑 Índice Geral

1. [FIAP Challenge — Cyber Defense Project](#-parte-1--fiap-challenge--cyber-defense-project)
   * [Visão Geral](#11-visão-geral)
   * [Stack Tecnológica](#12-stack-tecnológica)
   * [Arquitetura do Lab](#13-arquitetura-do-ambiente-de-testes-lab)
   * [Fases do Projeto](#14-fases-do-projeto)
2. [CTF Trilha 1 — OSINT, Threat Intelligence & Forense de Redes](#-parte-2--ctf-trilha-1--osint-threat-intelligence--forense-de-redes)
   * [Ferramentas Utilizadas](#21-ferramentas--metodologias)
   * [Detalhamento dos Desafios](#22-detalhamento-técnico-dos-desafios)
   * [Resumo & Status](#23-resumo-das-flags--status)
3. [CTF Trilha 2 — Hardware Hacking, OWASP IoTGoat & Forense Digital](#-parte-3--ctf-trilha-2--hardware-hacking-owasp-iotgoat--forense-digital)
   * [Ferramentas Utilizadas](#31-ferramentas--metodologias)
   * [Detalhamento dos Desafios](#32-detalhamento-técnico-dos-desafios)
   * [Resumo & Status](#33-resumo-das-flags--status)
4. [Estrutura Completa do Repositório](#-estrutura-consolidada-do-repositório)
5. [Autor](#-autor)

---

## 🏢 PARTE 1 — FIAP Challenge — Cyber Defense Project

### 1.1 Visão Geral
Desenvolvimento e implementação de estratégias de segurança cibernética corporativa baseadas em cenários do mercado real. O projeto combinou segurança ofensiva (pentest) com controles defensivos, auditoria de redes, elaboração de documentações formais de Prova de Conceito (PoC) e apresentação de soluções em vídeo.

### 1.2 Stack Tecnológica
* **Sistemas Operacionais & Virtualização:** Kali Linux, Metasploitable 2, Microsoft Hyper-V (Internal Virtual Switches).
* **Varredura & Enumeração:** Nmap, Gobuster, Nikto.
* **Exploração & Intrusão:** Metasploit Framework, scripts customizados.
* **Análise de Tráfego:** Wireshark, tcpdump.
* **Governança & Documentação:** Relatórios técnicos executivos e acadêmicos, Git/GitHub.

### 1.3 Arquitetura do Ambiente de Testes (Lab)
O ambiente prático foi montado de forma isolada para contenção de tráfego malicioso e validação de vulnerabilidades:

```text
       [ Host Físico (Windows / Hyper-V) ]
                       │
       [ Switch Virtual (Internal / Host-Only) ]
           ┌───────────┴───────────┐
           ▼                       ▼
    [ Kali Linux ]         [ Metasploitable 2 ]
  (Auditoria / Atacante)    (Ambiente Alvo / PoC)
```
### 1.4 Fases do Projeto
* [x] Fase 1: Mapeamento de Superfície: Varredura de portas, identificação de serviços expostos e coleta de banners.

* [x] Fase 2: Análise de Vulnerabilidades & PoC: Exploração controlada de falhas conhecidas, coleta de evidências e documentação técnica.

* [x] Fase 3: Mitigação & Hardening: Fechamento de portas desnecessárias, isolamento de sub-redes e alinhamento a práticas de Secure DevOps.

* [x] Fase 4: Apresentação Técnica: Defesa em vídeo demonstrando a PoC e a eficácia das correções propostas.

## 🕵️ PARTE 2 — CTF Trilha 1 — OSINT, Threat Intelligence & Forense de Redes
### 2.1 Ferramentas & Metodologias
* **OSINT & Perfilamento:** CyberChef, Maltego, Google Dorks, ExifTool, Holehe, Hunter.io, Wayback Machine.

* **Forense de Logs & CLI:** grep, awk, sed, sort, uniq, visualizadores de logs Linux/Apache.

* **Threat Intelligence & Frameworks:** Matriz MITRE ATT&CK, Pyramid of Pain (David Bianco), Cyber Kill Chain.

* **Análise de Tráfego:** Wireshark, tshark

### 2.2 Detalhamento Técnico dos Desafios
#### Zap Data Breach (OSINT / Leak Parsing)
* **Atividade: Tratamento e correlação de dados desestruturados a partir de um vazamento simulado de credenciais.**

* **Procedimento: Execução de filtros em linha de comando para isolar e-mails, usuários e hashes MD5/SHA, seguida da decodificação de payloads em Base64 para recuperar a flag.**
```
bash
grep -i "zap" dump_vazamento.txt | cut -d':' -f2,3 | sort -u > credenciais_filtradas.txt
echo "ZmxhZ3t6YXBfZGF0YV9icmVhY2hfcDB3bmVkfQ==" | base64 -d
```
#### Email Hunting (Enumeração Passiva Corporativa)
* **Atividade:** Mapeamento de estrutura interna corporativa e caixas de correio sem envio de pacotes diretos ao servidor-alvo.

* **Procedimento:** Consulta a registros DNS públicos (MX, TXT/SPF) e uso de motores de busca com dorks para identificar nomenclaturas de e-mail e departamentos críticos.
```
bash
dig mx targetdomain.com +short
dig txt targetdomain.com | grep "v=spf1"
```
#### Veículo Suspeito (Geo-OSINT & Metadados)
* **Atividade:** Identificação de modelo veicular, localização e linha temporal através de fotos de evidência.

* **Procedimento:** Extração profunda de metadados EXIF (exiftool) para obtenção de coordenadas GPS decimais e checagem cruzada visual via Google Earth.
```
bash
exiftool -c "%.6f" -GPSPosition veiculo_suspeito.jpg
```
#### Agente Hacker (Perfilamento & Pegada Digital)
* **Atividade:** Rastreamento da pegada digital (digital footprint) de um invasor simulado a partir de um apelido (handle).

* **Procedimento:** Mapeamento cruzado em fóruns e plataformas sociais (Holehe, WhatsMyName) e consulta a commits apagados no Wayback Machine.

#### Pirâmide da Dor (Threat Intelligence & IoC vs. TTP)
* **Atividade:** Categorização hierárquica de Indicadores de Comprometimento com foco na metodologia de David Bianco.

* **Procedimento:** Classificação de artefatos (hashes triviais, IPs transitórios) até atingir o topo da pirâmide (Táticas, Técnicas e Procedimentos - TTPs) para desenhar bloqueios perenes.

#### Escada do Cavaleiro (Progressão Tática & Movimento Lateral)
* **Atividade:** Identificação do caminho de invasão percorrido dentro da rede corporativa.

* **Procedimento:** Análise de sessões SMB, chamadas via WinRM/RDP e abuso de privilégios entre nós até a captura do nó com a flag.

#### Attack Patterns (Mapeamento MITRE ATT&CK)
* **Atividade:** Identificação de comandos executados pelo atacante e correlação direta com a matriz MITRE ATT&CK.

* **Procedimento:** Decomposição de histórico de comandos e classificação em técnicas como T1087 (Account Discovery), T1059 (Command and Scripting Interpreter) e T1053 (Scheduled Task).

#### Log Black Hat (Forense de Servidores)
* **Atividade:** Triagem em arquivos de log (/var/log/auth.log e logs de acesso HTTP) para isolar o momento exato do comprometimento.

* **Procedimento:** Identificação de ataques de força bruta SSH bem-sucedidos e varreduras automatizadas no Apache via comandos utilitários Linux.
```
bash 
grep "Failed password" /var/log/auth.log | awk '{print $(NF-3)}' | sort | uniq -c | sort -nr
grep -E "(union|select|<script>|/etc/passwd)" /var/log/apache2/access.log
```
#### Exfiltração de Dados (Análise de Tráfego de Rede)
* **Atividade:** Detecção de transferência clandestina de arquivos em capturas de rede (.pcap).

* **Procedimento:** Inspeção profunda via Wireshark e tshark para identificar canais encobertos em requisições DNS anômalas e requisições HTTP POST para servidores C2.
```
bash
tshark -r captura_exfiltracao.pcap -Y "dns.flags.response == 0" -T fields -e dns.qry.name | sort -u
tshark -r captura_exfiltracao.pcap -Y "http.request.method == POST" -T fields -e http.file_data | xxd -r -p
```
### 2.3 Resumo das Flags & Status

| Desafio | Categoria | Vetor / Técnica Principal | Ferramentas | Status |
| :--- | :--- | :--- | :--- | :---: |
| **Zap Data Breach** | OSINT / Leak | Extração em dumps e decodificação | `grep`, `awk`, CyberChef | `[Concluído]` |
| **Email Hunting** | Reconhecimento | Enumeração passiva e registros DNS | Hunter.io, `dig`, Google Dorks | `[Concluído]` |
| **Veículo Suspeito** | Geo-OSINT | Extração de tags GPS e busca visual | `exiftool`, Google Earth | `[Concluído]` |
| **Agente Hacker** | Soc. Engineering | Análise de pegada digital e histórico web | Holehe, Wayback Machine | `[Concluído]` |
| **Pirâmide da Dor** | Threat Intel | Escalonamento de IoCs vs. TTPs | Pyramid of Pain, AlienVault | `[Concluído]` |
| **Escada do Cavaleiro** | Mov. Lateral | Rastreamento de saltos e privilégios | Logs de auditoria, RDP/WinRM | `[Concluído]` |
| **Attack Patterns** | Modelagem Tática | Mapeamento formal de comandos | MITRE ATT&CK Matrix | `[Concluído]` |
| **Log Black Hat** | Forense de Logs | Detecção de anomalias em auth/access log | `grep`, `awk`, `/var/log/` | `[Concluído]` |
| **Exfiltração** | Forense de Rede | Descoberta de tunelamento DNS e POST HTTP | Wireshark, `tshark` | `[Concluído]` |

## 📡 PARTE 3 — CTF Trilha 2 — Hardware Hacking, OWASP IoTGoat & Forense Digital
### 3.1 Ferramentas & Metodologias
* **Firmware & Hardware Reversing:** binwalk, unsquashfs (squashfs-tools), dd, QEMU.

* **Análise Binária & Criptoanálise:** strings, hexdump, xxd, md5sum, sha256sum, John the Ripper, RockYou wordlist.

* **Forense de Artefatos:** exiftool, pdfid, pdf-parser, visualizadores de RFC 822/MIME.**

### 3.2 Detalhamento Técnico dos Desafios
#### Import IoT (Provisionamento de Ambiente)
* **Atividade:** Preparação da imagem base do OWASP IoTGoat para auditoria de segurança embarcada.

* **Procedimento:** Validação de integridade via hash SHA-256 e montagem do ambiente emulado isolado.
```
bash
sha256sum IoTGoat-x86.img.gz
gunzip IoTGoat-x86.img.gz
```
### Hardware Hacking (Extração de Firmware & Carving)
* **Atividade:** Decomposição de uma imagem bruta de firmware para extração do sistema de arquivos raiz (rootfs).

* **Procedimento:** Varredura por assinaturas mágicas através do binwalk, descompactação da partição compactada em SquashFS e inspeção offline da estrutura de diretórios Linux.
```
bash
binwalk -Me firmware.bin
unsquashfs -d extracted_rootfs filesystem.squashfs
```
### Goat Init (Auditoria de Scripts de Boot)
* **Atividade:** Análise estática nos scripts de inicialização automática do sistema operacional do dispositivo.

* **Procedimento:** Auditoria nos arquivos /etc/rc.local e scripts contidos em /etc/init.d/ para rastrear credenciais expostas em texto plano e chaves de ativação.
```
bash
cat extracted_rootfs/etc/rc.local
grep -rni "flag" extracted_rootfs/etc/init.d/
```
### Goat MD5 (Criptoanálise & Quebra de Hashes)
* **Atividade:** Localização de rotinas de validação de integridade fracas no firmware.

* **Procedimento:** Extração de hashes MD5 armazenados de forma estática e realização de ataque de dicionário com John the Ripper utilizando a wordlist RockYou.
```
bash
john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt target_hash.txt
```
### Goat Backdoor (Identificação de Acessos Ocultos)
* **Atividade:** Caça a binários maliciosos e serviços de escuta não documentados no sistema embarcado.

* **Procedimento:** Varredura com strings e comandos grep nos diretórios /bin, /sbin e /usr/bin em busca de instâncias de netcat em modo listener ou rotinas de reverse shell.
``` 
bash
grep -rn "nc -l" extracted_rootfs/
strings extracted_rootfs/usr/bin/backdoor_bin | grep -E "(sh|bash|flag)"
```
### Goat Kernel (Inspeção de Módulos .ko)
* **Atividade:** Auditoria de segurança em módulos de kernel do firmware.

* **Procedimento:** Extração e inspeção de metadados dos drivers compilados (.ko) no diretório /lib/modules/ para checar permissões indevidas e vetores de escalonamento para o ring 0.
```
bash
find extracted_rootfs/lib/modules/ -name "*.ko"
modinfo extracted_rootfs/lib/modules/*/custom_module.ko
```
### E-mail Suspeito (Forense em Comunicações & Spoofing)
* **Atividade:** Análise pericial de um e-mail com indícios de phishing/spoofing.

* **Procedimento:** Leitura reversa dos cabeçalhos SMTP (Received:), validação das assinaturas e políticas SPF, DKIM e DMARC, e decodificação de strings Base64 ocultas no corpo da mensagem.
```
bash
grep -iE "(Received:|Received-SPF|Authentication-Results)" email_amostra.eml
base64 -d payload_encoded.txt
```
### Documento Perdido (File Carving & Análise de Metadados)
* **Atividade:** Recuperação de artefatos e segredos escondidos em arquivos adulterados (PDFs/documentos corporativos).

* **Procedimento:** Análise aprofundada de metadados via exiftool e extração de fluxos internos e objetos ofuscados com pdfid e pdf-parser.
```
bash
exiftool -a -u documento_alvo.pdf
pdfid documento_alvo.pdf
pdf-parser --search "flag" documento_alvo.pdf
```
### 3.3 Resumo das Flags & Status

| Desafio | Alvo / Vetor | Técnica Principal | Ferramentas Chave | Status |
| :--- | :--- | :--- | :--- | :---: |
| **Import IoT** | Ambiente IoTGoat | Provisionamento e validação de integridade | `sha256sum`, QEMU | `[Concluído]` |
| **Hardware Hacking** | Dump de Firmware | Varredura de assinaturas e extração SquashFS | `binwalk`, `unsquashfs` | `[Concluído]` |
| **Goat Init** | Scripts `/etc/rc.local` | Análise estática de rotinas de boot | `grep`, `cat` | `[Concluído]` |
| **Goat MD5** | Criptografia Estática | Reversão de hash via ataque de dicionário | John the Ripper, RockYou | `[Concluído]` |
| **Goat Backdoor** | Binários e Daemons | Identificação de listeners e shells ocultas | `strings`, `grep`, `nmap` | `[Concluído]` |
| **Goat Kernel** | Módulos `.ko` | Inspeção de drivers e análise de privilégios | `modinfo`, `strings` | `[Concluído]` |
| **E-mail Suspeito** | RFC 822 / Cabeçalhos | Rastreamento SMTP e validação SPF/DKIM | Editores RFC, `grep`, CyberChef | `[Concluído]` |
| **Documento Perdido** | Estrutura de Arquivo | File carving e extração de metadados ocultos | `exiftool`, `pdf-parser` | `[Concluído]` |

## 📁 Estrutura Consolidada do Repositório

```text
.
├── challenge-fiap/
│   ├── docs/
│   │   ├── relatorio-analise-vulnerabilidades.pdf
│   │   └── arquitetura-lab.md
│   ├── poc/
│   │   ├── evidencias/
│   │   └── scripts/
│   └── network-analysis/
│       └── pcaps/
├── ctf-trilha-1-threat-intel-osint/
│   ├── osint-social-engineering/
│   │   ├── zap-data-breach/
│   │   │   ├── evidences/
│   │   │   └── writeup.md
│   │   ├── email-hunting/
│   │   │   └── writeup.md
│   │   ├── veiculo-suspeito/
│   │   │   └── writeup.md
│   │   └── agente-hacker/
│   │       └── writeup.md
│   ├── threat-intelligence/
│   │   ├── piramide-da-dor/
│   │   ├── attack-mitre/
│   │   └── escada-do-cavaleiro/
│   └── forensics-network/
│       ├── log-black-hat/
│       └── exfiltracao/
│           ├── pcaps/
│           └── writeup.md
├── ctf-trilha-2-iot-hardware-forensics/
│   ├── iot-firmware-hacking/
│   │   ├── iotgoat-setup/
│   │   │   └── import-iot.md
│   │   ├── iotgoat-challenges/
│   │   │   ├── goat-init/
│   │   │   ├── goat-md5/
│   │   │   ├── goat-backdoor/
│   │   │   └── goat-kernel/
│   │   └── hardware-methods/
│   │       └── dump-extraction-notes.md
│   └── forensics-artifacts/
│       ├── email-suspeito/
│       │   ├── evidences/
│       │   └── writeup.md
│       └── documento-perdido/
│           └── writeup.md
├── scripts/
│   ├── hash_verifier.py
│   └── extract_firmware.sh
├── LICENSE
└── README.md
```
👤 Autor
Pedro Henrique Ferrante Prado - Defesa Cibernética — FIAP

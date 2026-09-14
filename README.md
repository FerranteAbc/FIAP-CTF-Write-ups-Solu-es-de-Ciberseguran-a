# 🚩 FIAP CTF — Write-ups & Soluções de Cibersegurança

> Repositório contendo a documentação técnica, metodologias de investigação, scripts e flags capturadas durante os desafios de **Capture The Flag (CTF)** desenvolvidos no curso de **Defesa Cibernética** (*Ethical Hacking, Forensics & Secure DevOps*).

---

## 📌 Visão Geral

Este repositório reúne os relatórios técnicos (*write-ups*) dos laboratórios e desafios de CTF concluídos, cobrindo cenários práticos de Inteligência de Fontes Abertas (**OSINT**), Engenharia Social, Análise Forense de Logs, Threat Intelligence e Resposta a Incidentes.

---

## 🛠️ Ferramentas & Metodologias Utilizadas

| Domínio | Ferramentas / Frameworks |
| :--- | :--- |
| **OSINT & Reconhecimento** | CyberChef, Maltego, Google Dorks, ExifTool, Holehe, Hunter.io |
| **Análise de Logs & Forense** | Wireshark, tcpdump, grep, awk, sed, Volatility |
| **Threat Intelligence & Frameworks** | MITRE ATT&CK, Pyramid of Pain (David Bianco), Cyber Kill Chain |
| **Ambiente de Testes** | Kali Linux, terminais Linux, interpretadores Python / Bash |

---

## 🧩 Categorias & Desafios Resolvidos

### 1. OSINT & Engenharia Social
* **Zap Data Breach:** Investigação e correlação de informações em arquivos vazados na web, cruzamento de identidades e extração de flags a partir de dados públicos expostos.
* **Email Hunting:** Técnicas de enumeração passiva de endereços corporativos, mapeamento de domínios e descoberta de estruturas internas por vetores de e-mail.
* **Veículo Suspeito:** Reconhecimento geográfico, análise de metadados de mídia (EXIF) e busca reversa para identificação veicular em cenários investigativos.
* **Agente Hacker:** Desafio focado em traçar perfis psicológicos, pegada digital (*digital footprint*) e hábitos operacionais de um adversário simulado.

### 2. Threat Intelligence & Modelagem de Ameaças
* **Pirâmide da Dor (Pyramid of Pain):** Classificação e correlação de Indicadores de Comprometimento (IoCs) — desde hashes, IPs e nomes de domínio até ferramentas e procedimentos (TTPs) de adversários.
* **Escada do Cavaleiro:** Mapeamento de movimentos laterais e elevação gradual de privilégios em cenários de intrusão tática.
* **Attack Patterns:** Mapeamento de técnicas de intrusão com base na matriz **MITRE ATT&CK**, correlacionando fases de execução inicial, persistência e evasão de defesas.

### 3. Forense, Logs & Resposta a Incidentes
* **Log Black Hat:** Triagem, parsing e análise investigativa de logs de servidores e sistemas operacionais para identificar acessos não autorizados e rastros deixados pelo invasor.
* **Exfiltração de Dados:** Identificação de tráfego de rede anômalo e técnicas de comunicação encoberta utilizadas para exfiltração de informações (ex: tunelamento DNS, payloads codificados em HTTP).

---

## 📋 Resumo das Flags & Tópicos

| Desafio | Foco Principal | Vetor / Técnica | Status |
| :--- | :--- | :--- | :---: |
| **Zap Data Breach** | OSINT / Vazamento de Dados | Busca de credenciais e correlação de metadados | `[Concluído]` |
| **Email Hunting** | Reconhecimento Passivo | Enumeração de alvos e OSINT corporativo | `[Concluído]` |
| **Veículo Suspeito** | Geo-OSINT / Metadados | Análise EXIF e geolocalização visual | `[Concluído]` |
| **Agente Hacker** | Engenharia Social / Perfilamento | Pegada digital e engenharia social reversa | `[Concluído]` |
| **Pirâmide da Dor** | Threat Intelligence | Análise e escalonamento de IoCs vs. TTPs | `[Concluído]` |
| **Escada do Cavaleiro** | Segurança Ofensiva / Táticas | Movimentação lateral e progressão de privilégios | `[Concluído]` |
| **Attack Patterns** | Modelagem Tática | Mapeamento com matriz MITRE ATT&CK | `[Concluído]` |
| **Log Black Hat** | Forense Digital / Logs | Detecção de anomalias em trilhas de auditoria | `[Concluído]` |
| **Exfiltração** | Análise de Redes / Tráfego | Inspeção de pacotes e canais encobertos | `[Concluído]` |

---

## 📁 Estrutura do Repositório

```text
.
├── osint-social-engineering/
│   ├── zap-data-breach/
│   │   ├── writeup.md
│   │   └── evidences/
│   ├── email-hunting/
│   └── veiculo-suspeito/
├── threat-intelligence/
│   ├── piramide-da-dor/
│   ├── attack-mitre/
│   └── escada-do-cavaleiro/
├── forensics-network/
│   ├── log-black-hat/
│   └── exfiltracao/
│       ├── pcaps/
│       └── writeup.md
└── README.md
```
👤 Autor
Pedro Henrique Ferrante Prado — Defesa Cibernética, FIAP

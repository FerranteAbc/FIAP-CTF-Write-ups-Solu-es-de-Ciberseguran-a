# 🎮 CTF WARGAMES — 2TDCOR (CTF Fase 3)

> Write-ups do **CTF Fase 3** do curso de Defesa Cibernética — FIAP, resolvido na plataforma [2TDCOR WARGAMES](https://2tdcor.cyberwargames.com.br/), estilo CTFd. Prazo de entrega: **10/11/2026**.

**Alvo principal:** `http://45.55.126.134/` (servidor Apache/Debian, expondo HTTP, FTP/ProFTPD, SSH e SMB/Samba — tema "MODERNA CORP").

---

## 📊 Resumo dos 20 desafios

| # | Categoria | Desafio | Pontos | Status | Write-up |
| :-: | :--- | :--- | :-: | :-: | :--- |
| 1 | Hacking Tests | Varredura | 100 | ✅ Concluído | — |
| 2 | Hacking Tests | Buscando Artefatos | 100 | ✅ Concluído | — |
| 3 | Hacking Tests | Analisando Pacotes | 100 | ✅ Concluído | — |
| 4 | Hacking Tests | Sambando | 100 | ✅ Concluído | — |
| 5 | Hacking Tests | 802.11 | 100 | ✅ Concluído | — |
| 6 | Hacking Tests | Injetando | 100 | ✅ Concluído | — |
| 7 | Hacking Tests | LOLBAS | 100 | ✅ Concluído | — |
| 8 | Hacking Tests | Ocultando Artefatos | 100 | ✅ Concluído | — |
| 9 | Hacking Tests | Enumerando | 100 | ✅ Concluído | — |
| 10 | Hacking Tests | Criando Dicionários | 100 | ✅ Concluído | — |
| 11 | CTF | Pensando Fora da Caixa | 100 | ✅ Concluído | [`pensando-fora-da-caixa/`](./pensando-fora-da-caixa/writeup.md) |
| 12 | CTF | Protocolo Vulnerável | 100 | ✅ Concluído | [`protocolo-vulneravel/`](./protocolo-vulneravel/writeup.md) |
| 13 | CTF | Misconfiguration | 100 | ✅ Concluído | [`misconfiguration/`](./misconfiguration/writeup.md) |
| 14 | CTF | Somente Dispositivos Autorizados | 100 | ✅ Concluído | [`somente-dispositivos-autorizados/`](./somente-dispositivos-autorizados/writeup.md) |
| 15 | CTF | A Chave do Sucesso | 100 | ✅ Concluído | — *(GPG — `FIAP{GPG_FULL}`)* |
| 16 | CTF | Lorem Ipsum | 100 | ✅ Concluído | — *(Cifra de César — `FIAP{cifra_de_cesar}`)* |
| 17 | CTF | Protegido | 100 🏅 | ✅ Concluído (first blood) | — *(Ofuscação — `FIAP{ofuscator}`)* |
| 18 | CTF | Investigação | 100 | ✅ Concluído | [`investigacao/`](./investigacao/writeup.md) |
| 19 | CTF | Metadata | 100 | ✅ Concluído | — *(EXIF — `FIAP{eaSy-Met4dados}`)* |
| 20 | CTF | Listagem Permitida | 100 | ✅ Concluído | — *(Directory listing — `FIAP{Ap4ch3-S3rver}`)* |

> Os desafios marcados com "—" na coluna de write-up foram resolvidos durante a GS, porém sem log de comandos detalhado preservado nesta documentação (resolução rápida / pontual). Os 5 com link têm write-up completo, com todos os comandos e evidências.

---

## 🗺️ Mapa de enumeração do alvo

Reconhecimento consolidado de portas e serviços (via `nmap -sS -A -Pn -T4`):

```text
PORT    STATE    SERVICE  VERSION
21/tcp  open     ftp      ProFTPD (Debian)
22/tcp  open     ssh      OpenSSH 9.2p1 Debian (chave pública apenas)
80/tcp  open     http     Apache/2.4.6x (Debian) — "Moderna Bootstrap Template"
139/tcp filtered netbios-ssn
445/tcp filtered microsoft-ds  (Samba 4.17.12-Debian, acessível apesar do "filtered")
```

Diretórios web mapeados via `gobuster` (listagem aberta): `/assets/`, `/backups/`, `/forms/`, `/intranet/` (gate por User-Agent), `/index/`, `/js/`.

## 🧰 Ferramentas utilizadas nesta trilha

`nmap`, `gobuster`, `curl`, `hydra`, `cupp`, Python `ftplib`, `smbclient`, `zip2john` + `john` (RockYou), `7z`, `python-evtx`, `grep`/`awk` sobre XML de eventos do Windows.

---

👤 **Autor:** Pedro Henrique Ferrante Prado — Defesa Cibernética, FIAP (RM 566899)

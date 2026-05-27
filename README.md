<div align="center">

```
███╗   ███╗███████╗████████╗ █████╗      █████╗ ██╗   ██╗████████╗ ██████╗
████╗ ████║██╔════╝╚══██╔══╝██╔══██╗    ██╔══██╗██║   ██║╚══██╔══╝██╔═══██╗
██╔████╔██║█████╗     ██║   ███████║    ███████║██║   ██║   ██║   ██║   ██║
██║╚██╔╝██║██╔══╝     ██║   ██╔══██║    ██╔══██║██║   ██║   ██║   ██║   ██║
██║ ╚═╝ ██║███████╗   ██║   ██║  ██║    ██║  ██║╚██████╔╝   ██║   ╚██████╔╝
╚═╝     ╚═╝╚══════╝   ╚═╝   ╚═╝  ╚═╝    ╚═╝  ╚═╝ ╚═════╝    ╚═╝    ╚═════╝
```

**Automated Recon for Metasploit**

![Bash](https://img.shields.io/badge/Shell-Bash-4EAA25?style=flat-square&logo=gnu-bash&logoColor=white)
![Nmap](https://img.shields.io/badge/Powered%20by-Nmap-0E83CD?style=flat-square)
![Metasploit](https://img.shields.io/badge/Integrado-Metasploit-E34F26?style=flat-square)
![License](https://img.shields.io/badge/Licença-MIT-green?style=flat-square)
![Version](https://img.shields.io/badge/Versão-2.0-blueviolet?style=flat-square)
![Uso Autorizado](https://img.shields.io/badge/⚠%20Uso-Apenas%20Autorizado-red?style=flat-square)

</div>

---

## 📌 O que é

**MetaAutoRecon** é uma ferramenta de reconhecimento automático em Bash que integra `nmap` e `Metasploit` num único pipeline. Com um só comando, ela executa 6 fases de recon, calcula um Risk Score, identifica CVEs conhecidos e sugere módulos do Metasploit — tudo salvo no workspace do MSF e exportado em relatório.

> Arquivo único · 1.409 linhas · Sem instalação complexa

---

## ⚠️ Aviso Legal

> **Esta ferramenta é destinada exclusivamente para:**
> - Testes em sistemas e redes **próprios**
> - Ambientes com **autorização formal e documentada**
> - Laboratórios: HackTheBox, TryHackMe, VMs locais
>
> O uso não autorizado constitui crime:
> - **Lei 12.737/2012** (Crimes Cibernéticos) — até 3 anos de reclusão
> - **Marco Civil da Internet** — Lei 12.965/2014
> - **LGPD** — Lei 13.709/2018

---

## 🚀 Funcionalidades

| Função | Descrição |
|--------|-----------|
| 🔍 **Host Discovery** | Detecta hosts ativos com ping sweep (`nmap -sn`) |
| 🌐 **Port Scan** | Varre top-1000, todas as portas (65535) ou TCP+UDP |
| 🖥️ **Service Detection** | Identifica serviços e versões exatas (`-sV -sC`) |
| 📜 **NSE Scripts** | Executa scripts `safe`: SMB, HTTP, SSH, FTP, SSL |
| 🗄️ **MSF DB Import** | `db_nmap` direto no workspace — hosts e serviços salvos |
| 📊 **Risk Score** | Pontuação automática 0–100 baseada nas portas abertas |
| 🐛 **CVE Lookup** | Base local de versões vulneráveis conhecidas |
| 🔧 **Módulos MSF** | Sugere `auxiliary/scanner/*` por porta detectada |
| 📄 **Relatório** | Exporta `.txt` completo em `~/.metaautorecon/reports/` |
| 🖥️ **Dashboard** | Painel visual hacker no terminal com todas as métricas |

---

## 📋 Pré-requisitos

| Dependência | Obrigatório | Instalação |
|-------------|:-----------:|------------|
| `nmap` | ✅ | `sudo apt install nmap` |
| `msfconsole` | ✅ | `sudo apt install metasploit-framework` |
| `msfdb` | ⚠️ Recomendado | Incluído no Metasploit |
| `jq` | ⚪ Opcional | `sudo apt install jq` |
| `python3` | ⚪ Opcional | Geralmente pré-instalado |

> Testado no **Kali Linux** e **Parrot OS**. **Windows OS**

---

## ⚙️ Instalação

```bash
# 1. Clonar o repositório
git clone https://github.com/kalyel473/metaautorecon.git
cd metaautorecon

# 2. Dar permissão de execução
chmod +x metaautorecon.sh

# 3. (Recomendado) Iniciar o banco de dados do Metasploit
msfdb init
msfdb start

# 4. Executar
./metaautorecon.sh
```

---

## 🖥️ Uso

### Menu interativo

```bash
./metaautorecon.sh
```

Abre o menu principal com todas as opções.

### Linha de comando

```bash
./metaautorecon.sh [opções]
```

| Flag | Descrição | Exemplo |
|------|-----------|---------|
| `-t <alvo>` | IP, CIDR ou hostname | `-t 192.168.1.0/24` |
| `-w <ws>` | Nome do workspace MSF | `-w lab_htb` |
| `-l <lhost>` | Seu IP (LHOST) | `-l 192.168.1.50` |
| `-d` | Dry-run — simula sem executar | `-d` |
| `-v` | Verbose — mais detalhes | `-v` |
| `-h` | Exibe ajuda | `-h` |

### Exemplos

```bash
# Recon completo em uma rede
./metaautorecon.sh -t 192.168.1.0/24

# Alvo específico com workspace nomeado
./metaautorecon.sh -t 10.0.0.5 -w pentest_lab -v

# Testar sem executar nada (dry-run)
./metaautorecon.sh -t 10.0.0.5 -d
```

---

## 🔄 Pipeline Automático

Ao escolher **Reconhecimento Completo**, 6 fases rodam em sequência:

```
[FASE 1] Host Discovery    →  nmap -sn -T4
[FASE 2] Port Scan         →  nmap -T4 --open
[FASE 3] Service Detection →  nmap -sV -sC
[FASE 4] NSE Scripts       →  nmap --script=default,safe
[FASE 5] MSF DB Import     →  msfconsole db_nmap
[FASE 6] Risk Score + CVEs →  análise automática
```

---

## 📊 Risk Score

A ferramenta calcula automaticamente uma pontuação de risco baseada nas portas abertas:

| Score | Classificação | Exemplos de portas |
|:-----:|:-------------:|-------------------|
| 0 – 24 | 🟢 **BAIXO** | Poucas portas comuns |
| 25 – 49 | 🔵 **MÉDIO** | HTTP, FTP, SSH |
| 50 – 74 | 🟡 **ALTO** | MySQL, RDP, VNC |
| 75 – 100 | 🔴 **CRÍTICO** | SMB, RDP + múltiplas portas de risco |

> Portas de maior peso: **RDP (25)**, **SMB (25)**, **Telnet (20)**, **VNC (20)**

---

## 🐛 CVE Lookup

Mapeamento local de versões detectadas para CVEs conhecidos:

| Serviço/Versão | CVE | Descrição |
|----------------|-----|-----------|
| vsftpd 2.3.4 | CVE-2011-2523 | Backdoor |
| Apache 2.4.49 | CVE-2021-41773 | Path Traversal / RCE |
| Apache 2.4.50 | CVE-2021-42013 | Path Traversal / RCE |
| OpenSSH < 7.7 | CVE-2018-15473 | User Enumeration |
| Samba 3.x | CVE-2007-2447 | RCE via usermap_script |
| SMB (Windows) | MS17-010 | EternalBlue |
| RDP | CVE-2019-0708 | BlueKeep |
| Redis (sem auth) | CVE-2022-0543 | Lua sandbox escape |

> ⚠️ Sugestão informativa — confirme cada CVE manualmente antes de qualquer ação.

---

## 🔧 Módulos MSF Sugeridos

A ferramenta sugere apenas módulos `auxiliary/scanner` com base nas portas detectadas:

```
Porta 445  →  auxiliary/scanner/smb/smb_version
Porta 22   →  auxiliary/scanner/ssh/ssh_version
Porta 80   →  auxiliary/scanner/http/http_version
Porta 3389 →  auxiliary/scanner/rdp/rdp_scanner
Porta 3306 →  auxiliary/scanner/mysql/mysql_version
...
```

> Nenhum exploit é executado automaticamente — apenas sugestões para validação manual.

---

## 📁 Estrutura de Arquivos

```
~/.metaautorecon/
├── scans/          # XMLs e .txt gerados pelo nmap
├── reports/        # Relatórios finais exportados
├── resources/      # Resource scripts .rc do Metasploit
└── logs/           # Log de todas as execuções
```

---

## 📄 Exemplo de Relatório

```
╔══════════════════════════════════════════════════════════════╗
║        MetaAutoRecon v2.0 — Relatório de Reconhecimento      ║
╚══════════════════════════════════════════════════════════════╝

Alvo       : 192.168.1.0/24
Risk Score : 78/100 — ALTO

━━━ HOSTS ATIVOS (3) ━━━
  [ONLINE]  192.168.1.1
  [ONLINE]  192.168.1.10
  [ONLINE]  192.168.1.105

━━━ PORTAS ABERTAS (12) ━━━
  [OPEN]  tcp/22
  [OPEN]  tcp/80
  [OPEN]  tcp/445
  ...

━━━ CVEs POTENCIAIS ━━━
  [CVE] MS17-010 (SMB — EternalBlue) — verificar patch
  ...

━━━ MÓDULOS MSF SUGERIDOS ━━━
  [MSF] auxiliary/scanner/smb/smb_version
  ...
```

---

## 🤝 Contribuições

Contribuições são bem-vindas! Para contribuir:

1. Fork o repositório
2. Crie uma branch: `git checkout -b feature/nova-funcionalidade`
3. Commit suas mudanças: `git commit -m 'feat: adiciona nova funcionalidade'`
4. Push para a branch: `git push origin feature/nova-funcionalidade`
5. Abra um Pull Request

---

## 📜 Licença

Distribuído sob a licença **MIT**. Veja `LICENSE` para mais informações.

---

<div align="center">

Desenvolvido para fins de **segurança ofensiva autorizada** e **pesquisa em ambientes controlados**.

**Use com responsabilidade.**

</div>

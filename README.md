# 📚 Network Security — Mundarija

> **TryHackMe | Jr Pentester | Network Security bo'limi**
> Barcha roomlar bo'yicha shaxsiy konspekt va cheatsheet to'plami.

---

## 🗂️ Roomlar Ro'yxati

| # | Room | Asosiy Mavzu | Fayl |
|---|------|--------------|------|
| 01 | 🕵️ Passive Reconnaissance | WHOIS, nslookup, dig, Shodan, crt.sh | [ns01_passive_recon.md](ns01_passive_recon.md) |
| 02 | 🔎 Active Reconnaissance | ping, traceroute, telnet, netcat | [ns02_active_recon.md](ns02_active_recon.md) |
| 03 | 🖥️ Nmap Live Host Discovery | ARP, ICMP, TCP/UDP ping scan | [ns03_nmap_live_host.md](ns03_nmap_live_host.md) |
| 04 | 🔌 Nmap Basic Port Scans | SYN, Connect, UDP, timing | [ns04_nmap_basic_scans.md](ns04_nmap_basic_scans.md) |
| 05 | 🚀 Nmap Advanced Port Scans | NULL, FIN, Xmas, ACK, Idle, Decoy | [ns05_nmap_advanced_scans.md](ns05_nmap_advanced_scans.md) |
| 06 | 🔬 Nmap Post Port Scans | -sV, -O, NSE skriptlar, -oA | [ns06_nmap_post_scans.md](ns06_nmap_post_scans.md) |
| 07 | 🌐 Protocols and Servers | FTP, SMTP, POP3, IMAP, HTTP | [ns07_protocols_servers.md](ns07_protocols_servers.md) |
| 08 | 🔐 Protocols and Servers 2 | Hydra, Tcpdump, SSH, TLS/SSL, MitM | [ns08_protocols_servers2.md](ns08_protocols_servers2.md) |
| 09 | 🏆 Net Sec Challenge | Barcha ko'nikmalar amalda | [ns09_net_sec_challenge.md](ns09_net_sec_challenge.md) |

---

## ⚡ Tezkor Cheatsheet

### 🕵️ Passiv Razvedka
```bash
whois target.com                                    # Domen egasi
nslookup -type=MX target.com                       # Pochta server
dig target.com ANY                                  # Barcha DNS
dig axfr @ns1.target.com target.com                # Zone transfer
curl -s "https://crt.sh/?q=%25.target.com&output=json" | jq -r '.[].name_value' | sort -u
shodan host IP
```

### 🔎 Aktiv Razvedka
```bash
ping -c 4 IP                    # Tirikmi? TTL→OS
traceroute -n IP                # Yo'l kuzatish
telnet IP 80                    # Port + banner
nc -vn IP PORT                  # Netcat
curl -I http://IP               # HTTP header
```

### 🖥️ Nmap Host Discovery
```bash
sudo nmap -PR -sn 192.168.1.0/24    # ARP (lokal)
sudo nmap -PE -sn 10.10.10.0/24     # ICMP ping
nmap -PS80,443 -sn 10.10.10.0/24   # TCP ping
nmap -Pn 10.10.10.1                 # Ping o'tkazib yubor
```

### 🔌 Nmap Port Skan
```bash
sudo nmap -sS -T4 10.10.10.1        # SYN (standart)
nmap -sT -T4 10.10.10.1             # Connect (root yo'q)
sudo nmap -sU --top-ports 20 IP     # UDP
nmap -p- 10.10.10.1                 # Barcha port
nmap -F 10.10.10.1                  # Top 100 (tez)
sudo nmap -A -T4 -p- -oA scan IP   # To'liq skan
```

### 🚀 Nmap Ilg'or Skan
```bash
sudo nmap -sN IP    # NULL
sudo nmap -sF IP    # FIN
sudo nmap -sX IP    # Xmas
sudo nmap -sA IP    # ACK (firewall xaritasi)
sudo nmap -sI ZOMBIE IP  # Idle (eng yashirin)
sudo nmap -D RND:10 IP   # Decoy
sudo nmap -f IP          # Fragment
```

### 🔬 Nmap Post Skan
```bash
nmap -sV IP                          # Versiya
sudo nmap -O IP                      # OS
nmap -sC IP                          # Standart skriptlar
sudo nmap -A IP                      # Hammasi
nmap --script=vuln IP                # Zaifliklar
nmap --script=ftp-anon IP           # FTP anonim
nmap --script=smb-vuln-ms17-010 IP  # EternalBlue
nmap -oA results IP                  # Saqlash
```

### 🌐 Protokollar
```bash
# FTP
ftp IP                               # anonymous kirish
hydra -l admin -P rockyou.txt ftp://IP

# SMTP
telnet IP 25 → VRFY username         # Enum
nmap --script=smtp-enum-users IP

# POP3
telnet IP 110 → USER x → PASS x → RETR 1

# SSH
ssh user@IP
hydra -l admin -P rockyou.txt -t 4 ssh://IP

# Sniffing
sudo tcpdump -i eth0 -A port 21 | grep -E "USER|PASS"
```

---

## 🧠 Eslab Qolish Uchun

| Texnika | Vosita | Qachon |
|---------|--------|--------|
| **Passiv razvedka** | WHOIS, dig, Shodan | Iz qoldirmasdan |
| **Aktiv razvedka** | ping, netcat, telnet | Tizimga to'g'ridan |
| **Host discovery** | nmap -sn | Tirik hostlar |
| **Port skan** | nmap -sS | Ochiq portlar |
| **Yashirin skan** | nmap -sN/-sF/-sX | IDS dan yashirinish |
| **Firewall xarita** | nmap -sA | Firewall qoidalari |
| **Versiya** | nmap -sV | Xizmat versiyasi |
| **Zaiflik** | nmap --script=vuln | CVE topish |
| **Brute force** | Hydra | Parol topish |
| **Sniffing** | Tcpdump | Ochiq trafik |

### TTL → OS:
```
64  → Linux/Unix 🐧
128 → Windows 🪟
255 → Router/Cisco 🔀
```

### Xavfsiz vs Xavfli Protokollar:
```
Xavfli → Xavfsiz:
FTP(21) → SFTP/FTPS(990)
HTTP(80) → HTTPS(443)
SMTP(25) → SMTPS(465)
POP3(110) → POP3S(995)
IMAP(143) → IMAPS(993)
Telnet(23) → SSH(22)
```

---

*Yaratilgan: TryHackMe Jr Pentester — Network Security bo'limi asosida*

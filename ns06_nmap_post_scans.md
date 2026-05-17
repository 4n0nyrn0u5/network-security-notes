# 🔬 Nmap Post Port Scans (Nmap Skan Keyingi Tahlil)

> **Maqsad:** Ochiq portlardagi xizmatlar versiyasi, OS aniqlash va NSE skriptlar orqali chuqur ma'lumot yig'ish.

---

## 📖 Post-Skan nima?

Port ochiqligini aniqlagandan keyin:
- Qaysi **xizmat** ishlayapti?
- Qaysi **versiya**?
- Qaysi **OS**?
- Qanday **zaifliklar** bor?

---

## 🔍 Xizmat va Versiya Aniqlash

### -sV — Xizmat Versiyasi
```bash
nmap -sV 10.10.10.1
nmap -sV --version-intensity 5 10.10.10.1   # 0-9, baland = aniqroq
nmap -sV --version-all 10.10.10.1           # Maksimal intensivlik

# Natija misoli:
# PORT   STATE SERVICE VERSION
# 22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu
# 80/tcp open  http    Apache httpd 2.4.41
# 3306/tcp open mysql  MySQL 5.7.32

# Ko'rsatadi:
# - Xizmat nomi
# - Versiya raqami
# - OS ma'lumoti (ba'zan)
```

---

## 💻 OS Aniqlash

### -O — OS Detection
```bash
sudo nmap -O 10.10.10.1
sudo nmap -O --osscan-guess 10.10.10.1    # Taxminlash
sudo nmap -O --osscan-limit 10.10.10.1   # Faqat aniq natijalar

# Natija misoli:
# OS details: Linux 4.15 - 5.6
# Running: Linux 5.X
# OS CPE: cpe:/o:linux:linux_kernel:5

# Aniq bo'lishi uchun: Kamida 1 ochiq + 1 yopiq port kerak
```

---

## 🚀 Agressiv Skan (-A)

```bash
nmap -A 10.10.10.1

# -A = -sV + -O + --traceroute + default scripts
# Ko'p ma'lumot, lekin ko'p shovqin

sudo nmap -A -T4 10.10.10.1   # Ko'p holda ishlatiladi
```

---

## 📜 NSE — Nmap Scripting Engine (Skript Tizimi)

### NSE nima?
Lua tilida yozilgan skriptlar — Nmap ga qo'shimcha imkoniyatlar beradi.

```bash
# Skriptlar joyi:
ls /usr/share/nmap/scripts/

# Skript kategoriyalari:
auth      → Autentifikatsiya tekshirish
broadcast → Tarmoq broadcast
brute     → Parol brute force
default   → Standart skriptlar
discovery → Qo'shimcha ma'lumot topish
dos       → Denial of Service (ehtiyot bo'ling!)
exploit   → Zaifliklarni ekspluatatsiya
external  → Tashqi xizmat chaqiradi
fuzzer    → Fuzzing
intrusive → Agressiv (iz qoldiradi)
malware   → Zararli dastur aniqlash
safe      → Xavfsiz skriptlar
version   → Versiya aniqlash
vuln      → Zaifliklar tekshirish
```

### Skriptlarni Ishlatish:
```bash
# Standart skriptlar (-sC)
nmap -sC 10.10.10.1

# Bitta skript
nmap --script=http-title 10.10.10.1
nmap --script=ftp-anon 10.10.10.1

# Bir nechta skript
nmap --script=http-title,http-headers 10.10.10.1

# Kategoriya bo'yicha
nmap --script=vuln 10.10.10.1
nmap --script=safe 10.10.10.1
nmap --script=auth 10.10.10.1

# Wildcard
nmap --script=http-* 10.10.10.1    # Barcha http skriptlar
nmap --script=smb-* 10.10.10.1     # Barcha smb skriptlar

# Skript parametrlari
nmap --script=http-brute --script-args http-brute.path=/login 10.10.10.1
```

### Foydali Skriptlar:
```bash
# HTTP
nmap --script=http-title 10.10.10.1          # Sahifa sarlavhasi
nmap --script=http-headers 10.10.10.1        # HTTP headerlar
nmap --script=http-robots.txt 10.10.10.1     # robots.txt
nmap --script=http-enum 10.10.10.1           # Yashirin sahifalar
nmap --script=http-auth-finder 10.10.10.1    # Auth metodlari

# FTP
nmap --script=ftp-anon 10.10.10.1            # Anonim kirish
nmap --script=ftp-brute 10.10.10.1           # Parol brute force

# SSH
nmap --script=ssh-brute 10.10.10.1           # Parol brute force
nmap --script=ssh-auth-methods 10.10.10.1    # Auth metodlari

# SMB
nmap --script=smb-vuln-ms17-010 10.10.10.1  # EternalBlue!
nmap --script=smb-enum-shares 10.10.10.1    # Shared papkalar
nmap --script=smb-os-discovery 10.10.10.1   # OS ma'lumoti

# MySQL
nmap --script=mysql-empty-password 10.10.10.1
nmap --script=mysql-databases 10.10.10.1

# Zaifliklar
nmap --script=vuln 10.10.10.1               # Barcha zaifliklar
nmap --script=vuln,safe 10.10.10.1
```

---

## 📤 Natijalarni Saqlash

```bash
# Normal format (o'qilishi oson)
nmap -oN scan_results.txt 10.10.10.1

# Grepable format (grep, awk uchun)
nmap -oG scan_results.gnmap 10.10.10.1

# XML format (import uchun)
nmap -oX scan_results.xml 10.10.10.1

# Barcha formatlar
nmap -oA scan_results 10.10.10.1
# → scan_results.nmap, scan_results.gnmap, scan_results.xml

# Natijadan qidirish
grep "open" scan_results.gnmap
grep "80/open" scan_results.gnmap
```

---

## 🎯 To'liq Pentest Skani

```bash
# 1-bosqich: Tez skan (qaysi portlar ochiq?)
sudo nmap -sS -T4 --top-ports 1000 -oN quick_scan.txt 10.10.10.1

# 2-bosqich: Barcha portlar
sudo nmap -sS -T4 -p- -oN full_scan.txt 10.10.10.1

# 3-bosqich: Versiya + OS + Skriptlar
sudo nmap -sV -sC -O -T4 -p 22,80,443 -oA detailed_scan 10.10.10.1

# 4-bosqich: Zaifliklar
sudo nmap --script=vuln -p 22,80,443 10.10.10.1

# Yoki hammasi birga:
sudo nmap -A -T4 -p- -oA complete_scan 10.10.10.1
```

---

## 💡 Eslab Qolish Uchun

| Parametr | Vazifasi |
|----------|---------|
| `-sV` | Xizmat versiyasi |
| `-O` | OS aniqlash |
| `-sC` | Standart skriptlar |
| `-A` | Hammasi (-sV -O -sC --traceroute) |
| `--script=vuln` | Zaifliklar |
| `--script=safe` | Xavfsiz skriptlar |
| `-oN` | Normal saqlash |
| `-oA` | Barcha format saqlash |

```bash
# Eng ko'p ishlatiladigan:
sudo nmap -sV -sC -O -T4 10.10.10.1

# To'liq + saqlash:
sudo nmap -A -T4 -p- -oA results 10.10.10.1
```

**NSE skript qidirish:**
```bash
# O'rnatilgan skriptlarni qidirish:
ls /usr/share/nmap/scripts/ | grep http
ls /usr/share/nmap/scripts/ | grep smb
ls /usr/share/nmap/scripts/ | grep ftp

# Skript haqida ma'lumot:
nmap --script-help http-title
```

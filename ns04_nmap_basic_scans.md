# 🔌 Nmap Basic Port Scans (Nmap Asosiy Port Skanlash)

> **Maqsad:** TCP va UDP portlarni skanerlash — qaysi xizmatlar ishlab turganini aniqlash.

---

## 📖 Port Holatlari

| Holat | Ma'nosi |
|-------|---------|
| **Open** | Port ochiq, xizmat ishlayapti |
| **Closed** | Port yopiq, xizmat yo'q (RST javob) |
| **Filtered** | Firewall bloklayapti (javob yo'q) |
| **Unfiltered** | Port mavjud, lekin ochiq/yopiqligini bilmaymiz |
| **Open/Filtered** | Ochiq yoki filtered (aniqlab bo'lmaydi) |
| **Closed/Filtered** | Yopiq yoki filtered |

---

## 🔍 TCP Port Skan Turlari

### 1. TCP Connect Scan (-sT) — Root kerak emas
```bash
nmap -sT 10.10.10.1

# Ishlash prinsipi (3-way handshake):
# Nmap → SYN → Target
# Nmap ← SYN-ACK ← Target  (ochiq)
# Nmap → ACK → Target
# Nmap → RST → Target  (ulanishni yopish)

# Afzalligi: Root huquq shart emas
# Kamchiligi: Ko'p iz qoldiradi (logga yoziladi)
# Ishlatish: Root bo'lmaganda
```

### 2. TCP SYN Scan (-sS) — Eng Ko'p Ishlatiladigan
```bash
sudo nmap -sS 10.10.10.1

# Ishlash prinsipi (yarim ochiq):
# Nmap → SYN → Target
# Nmap ← SYN-ACK ← Target  (ochiq)
# Nmap → RST → Target  (handshake yakunlanmaydi!)

# Afzalligi: Tez, kam iz (log yozilmaydi)
# Kamchiligi: Root/sudo kerak
# Ishlatish: Standart — ko'p hollarda

# Agar SYN-ACK o'rniga RST kelsa → yopiq
# Agar hech narsa kelmasa → filtered
```

### 3. UDP Scan (-sU)
```bash
sudo nmap -sU 10.10.10.1

# UDP → connectionless (ulanishsiz)
# Ochiq port → javob yo'q (yoki UDP javob)
# Yopiq port → ICMP Port Unreachable

# Sekin! (har port uchun timeout kutish)
# Tezlashtirish:
sudo nmap -sU --top-ports 20 10.10.10.1
sudo nmap -sU -F 10.10.10.1   # Top 100 port

# Muhim UDP portlar:
# 53  → DNS
# 67  → DHCP
# 68  → DHCP client
# 69  → TFTP
# 123 → NTP
# 161 → SNMP
# 500 → IPSec VPN
```

---

## ⚙️ Port Tanlash Parametrlari

```bash
# Bitta port
nmap -p 80 10.10.10.1

# Bir nechta port
nmap -p 80,443,8080 10.10.10.1

# Port oraliq
nmap -p 1-1024 10.10.10.1

# Barcha 65535 port
nmap -p- 10.10.10.1
nmap -p 1-65535 10.10.10.1

# Top portlar
nmap --top-ports 10 10.10.10.1   # Top 10
nmap --top-ports 100 10.10.10.1  # Top 100
nmap -F 10.10.10.1               # Top 100 (Fast)

# TCP va UDP birgalikda
nmap -sS -sU -p T:80,U:53 10.10.10.1
```

---

## ⏱️ Tezlik (Timing) Parametrlari

```bash
# -T0 → Paranoid   (juda sekin, IDS dan yashirinish)
# -T1 → Sneaky     (sekin)
# -T2 → Polite     (tarmoqni yuklamaslik)
# -T3 → Normal     (standart)
# -T4 → Aggressive (tez, ishonchli tarmoqda)
# -T5 → Insane     (juda tez, noaniq)

nmap -T4 10.10.10.1    # Ko'p holda ishlatiladi
nmap -T1 10.10.10.1    # Yashirinish uchun
```

---

## 📤 Natijani Saqlash

```bash
nmap -oN scan.txt 10.10.10.1      # Normal (o'qilishi oson)
nmap -oG scan.txt 10.10.10.1      # Grepable (grep uchun)
nmap -oX scan.xml 10.10.10.1      # XML
nmap -oA scan 10.10.10.1          # Barcha formatlar (scan.nmap, scan.gnmap, scan.xml)
```

---

## 🎯 Amaliy Misollar

```bash
# 1. Tez asosiy skan
nmap -T4 -F 10.10.10.1

# 2. To'liq TCP skan (barcha portlar)
sudo nmap -sS -p- -T4 10.10.10.1

# 3. UDP top portlar
sudo nmap -sU --top-ports 20 10.10.10.1

# 4. TCP + UDP birgalikda
sudo nmap -sS -sU -T4 10.10.10.1

# 5. Root bo'lmasdan
nmap -sT -T4 10.10.10.1

# 6. Natijani saqlash
sudo nmap -sS -p- -T4 -oA full_scan 10.10.10.1

# 7. Subnet skan
sudo nmap -sS -T4 192.168.1.0/24
```

---

## 📋 Keng Tarqalgan Portlar

```
21   → FTP
22   → SSH
23   → Telnet
25   → SMTP
53   → DNS
80   → HTTP
110  → POP3
143  → IMAP
443  → HTTPS
445  → SMB
3306 → MySQL
3389 → RDP (Windows Remote Desktop)
5432 → PostgreSQL
6379 → Redis
8080 → HTTP (alternativ)
8443 → HTTPS (alternativ)
27017→ MongoDB
```

---

## 💡 Eslab Qolish Uchun

| Skan | Parametr | Qachon |
|------|----------|--------|
| **SYN** | `-sS` | Standart (root kerak) |
| **Connect** | `-sT` | Root yo'q |
| **UDP** | `-sU` | UDP xizmatlar |
| **Barcha port** | `-p-` | To'liq skan |
| **Top 100** | `-F` | Tez skan |

```bash
# Eng ko'p ishlatiladigan kombinatsiya:
sudo nmap -sS -sV -T4 -p- 10.10.10.1
#          ↑    ↑   ↑   ↑
#         SYN  Ver Tez Barcha port
```

**Port holatlari eslatmasi:**
- `open` = Xizmat ishlayapti → Tekshir!
- `filtered` = Firewall bor → Bypass urinib ko'r
- `closed` = Xizmat yo'q → O'tkazib yubor

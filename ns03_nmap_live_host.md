# 🖥️ Nmap Live Host Discovery (Nmap bilan Tirik Hostlarni Topish)

> **Maqsad:** Tarmoqdagi qaysi hostlar (kompyuter, server, qurilma) yoqilgan va faol ekanligini aniqlash.

---

## 📖 Nmap nima?

**Nmap (Network Mapper)** — tarmoqni skanerlash uchun eng kuchli va keng tarqalgan vosita. Port skanerlash, OS aniqlash, xizmat versiyasi va skriptlar orqali tekshirish imkonini beradi.

```bash
# Asosiy sintaksis:
nmap [parametrlar] [maqsad]

# Maqsad turlari:
nmap 10.10.10.1              # Bitta IP
nmap 10.10.10.1-10           # IP oraliq
nmap 10.10.10.0/24           # Subnet
nmap tryhackme.com           # Domen
nmap 10.10.10.1 10.10.10.2   # Bir nechta IP
```

---

## 🔍 Subnetlar (Tarmoq Bloklari)

```
10.10.10.0/24   → 256 ta IP   (10.10.10.0 - 10.10.10.255)
10.10.10.0/16   → 65536 ta IP (10.10.0.0 - 10.10.255.255)
10.10.10.0/8    → 16M ta IP   (10.0.0.0 - 10.255.255.255)

# Misol: /24 = 254 ta faol host (0 va 255 ajratilgan)
```

---

## 📡 Host Discovery Metodlari

### 1. ARP Scan (Eng Ishonchli — Lokal Tarmoq)
```bash
# ARP (Address Resolution Protocol) — MAC manzil so'rash
# Faqat bir xil tarmoq segmentida ishlaydi

nmap -PR 10.10.10.0/24       # ARP ping scan
nmap -PR -sn 10.10.10.0/24   # Faqat host discovery (port skanersiz)

# arp-scan vositasi:
arp-scan 10.10.10.0/24
arp-scan -l                  # Lokal tarmoq

# Nima uchun ishonchli:
# ARP ni o'chirib bo'lmaydi → har doim javob beradi
```

### 2. ICMP Echo Scan (ping)
```bash
# ICMP Echo Request (oddiy ping)
nmap -PE 10.10.10.0/24

# ICMP Timestamp Request
nmap -PP 10.10.10.0/24

# ICMP Address Mask Request
nmap -PM 10.10.10.0/24

# Muammo: Firewall ICMP ni bloklashi mumkin!
```

### 3. TCP SYN Ping
```bash
# TCP SYN → Ochiq portga yuboriladi
nmap -PS 10.10.10.1          # Port 80 (standart)
nmap -PS22 10.10.10.1        # Port 22
nmap -PS22,80,443 10.10.10.1 # Bir nechta port
nmap -PS1-1024 10.10.10.0/24 # Port oraliq
```

### 4. TCP ACK Ping
```bash
nmap -PA 10.10.10.1          # Port 80 (standart)
nmap -PA22,80,443 10.10.10.1
```

### 5. UDP Ping
```bash
nmap -PU 10.10.10.1          # Port 40125 (standart)
nmap -PU53,161 10.10.10.1    # DNS, SNMP
```

---

## ⚙️ Muhim Parametrlar

```bash
# Faqat host discovery (port skanersiz)
nmap -sn 10.10.10.0/24

# DNS hal qilmasdan (tez)
nmap -n 10.10.10.0/24

# Ping yubormasdan (barcha hostlarni tirik deb hisoblash)
nmap -Pn 10.10.10.1

# Batafsil natija
nmap -v 10.10.10.1           # verbose
nmap -vv 10.10.10.1          # juda batafsil

# Root/sudo kerak bo'lganlar:
# -PR (ARP), -PE (ICMP), -PS (SYN)
# Root bo'lmasangiz: -PA ishlatish
```

---

## 📊 Host Discovery Taqqoslash

| Metod | Parametr | Ishlash sharti | Ishonchlilik |
|-------|----------|----------------|-------------|
| **ARP** | `-PR` | Faqat lokal tarmoq | ⭐⭐⭐⭐⭐ |
| **ICMP Echo** | `-PE` | Firewall yo'q | ⭐⭐⭐ |
| **TCP SYN** | `-PS` | Port ochiq | ⭐⭐⭐⭐ |
| **TCP ACK** | `-PA` | Root emas | ⭐⭐⭐ |
| **UDP** | `-PU` | UDP ochiq | ⭐⭐ |

---

## 🎯 Amaliy Misollar

```bash
# 1. Lokal tarmoqdagi barcha hostlar
sudo nmap -PR -sn 192.168.1.0/24

# 2. ICMP + SYN birgalikda
sudo nmap -PE -PS22,80 -sn 10.10.10.0/24

# 3. Tez skan (DNS yo'q, aggressive timing)
nmap -sn -n --min-rate 1000 10.10.10.0/24

# 4. Root bo'lmasdan
nmap -PA80,443 -sn 10.10.10.0/24

# 5. Natijani faylga saqlash
nmap -sn 10.10.10.0/24 -oN hosts.txt     # Normal format
nmap -sn 10.10.10.0/24 -oG hosts.txt     # Grepable format
nmap -sn 10.10.10.0/24 -oX hosts.xml     # XML format
nmap -sn 10.10.10.0/24 -oA hosts         # Barcha formatlar

# 6. Faqat IP larni ko'rish
nmap -sn 10.10.10.0/24 | grep "Nmap scan report" | awk '{print $5}'
```

---

## 💡 Eslab Qolish Uchun

```bash
# Eng ko'p ishlatiladigan:
sudo nmap -PR -sn 192.168.1.0/24    # Lokal tarmoq
sudo nmap -PE -sn 10.10.10.0/24     # ICMP ping
nmap -PS80,443 -sn 10.10.10.0/24    # TCP ping

# Parametrlar eslatmasi:
-sn   → Faqat host discovery (port skanersiz)
-PR   → ARP (lokal)
-PE   → ICMP Echo
-PS   → TCP SYN
-PA   → TCP ACK
-PU   → UDP
-Pn   → Pingni o'tkazib yubor (barcha tirik deb)
-n    → DNS hal qilmasdan
-v    → Batafsil
-oN   → Faylga saqlash
```

**Tarmoq skanerlash uchun umumiy qoida:**
- Lokal tarmoq → **ARP** (`-PR`) eng ishonchli
- Tashqi tarmoq → **TCP SYN** (`-PS`) + **ICMP** (`-PE`)
- Firewall ko'p → **-Pn** (barcha hostlarni tirik deb hisoblash)

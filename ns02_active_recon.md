# 🔎 Active Reconnaissance (Aktiv Razvedka)

> **Maqsad:** Maqsad tizimga to'g'ridan-to'g'ri so'rovlar yuborib, portlar, xizmatlar va tizim haqida ma'lumot yig'ish.

---

## 📖 Aktiv Razvedka nima?

**Aktiv razvedka** — maqsad serverga to'g'ridan-to'g'ri paket va so'rovlar yuborib ma'lumot to'plash. Iz qoldiradi, shuning uchun faqat ruxsat berilgan tizimlarda ishlatiladi.

```
Passiv: Siz → Ochiq manbalar (iz yo'q)
Aktiv:  Siz → Maqsad server  (iz qoladi!)
```

---

## 🛠️ Asosiy Vositalar

### 1. ping — Tirik ekanligini Tekshirish
```bash
# Asosiy ping
ping tryhackme.com
ping 10.10.10.1

# Soni cheklash
ping -c 4 10.10.10.1        # 4 ta paket (Linux)
ping -n 4 10.10.10.1        # 4 ta paket (Windows)

# TTL (Time To Live) dan OS aniqlash:
# TTL ≈ 64  → Linux/Unix
# TTL ≈ 128 → Windows
# TTL ≈ 255 → Cisco/Network qurilma

# Natija:
# PING 10.10.10.1: 64 bytes → Tirik!
# Request timeout  → O'chiq yoki firewall bloklaydi
```

### 2. traceroute — Yo'lni Kuzatish
```bash
# Linux
traceroute tryhackme.com
traceroute -n 10.10.10.1    # DNS hal qilmasdan (tez)

# Windows
tracert tryhackme.com

# Ko'rsatadi:
# Paket qaysi routerlar orqali o'tishini
# Har bir hop dagi kechikish (ms)
# * * * → Firewall bloklayapti

# Misol natija:
# 1  192.168.1.1    1ms   → Uy router
# 2  10.0.0.1       5ms   → ISP
# 3  * * *               → Bloklangan
# 4  104.26.10.229  20ms  → Maqsad
```

### 3. telnet — Port Tekshirish va Banner Grabbing
```bash
# Port ochiqmi tekshirish
telnet 10.10.10.1 80        # HTTP port
telnet 10.10.10.1 22        # SSH port
telnet 10.10.10.1 25        # SMTP port
telnet 10.10.10.1 21        # FTP port

# HTTP banner grabbing:
telnet 10.10.10.1 80
GET / HTTP/1.1
Host: 10.10.10.1
(Enter x2)
# Javob: Server: Apache/2.4.41 Ubuntu ← versiya!

# SMTP banner grabbing:
telnet 10.10.10.1 25
# Javob: 220 mail.target.com ESMTP Postfix ← server nomi!

# FTP banner grabbing:
telnet 10.10.10.1 21
# Javob: 220 ProFTPD 1.3.5 Server ← versiya!
```

### 4. netcat (nc) — Tarmoq Pichog'i
```bash
# Port tekshirish
nc -vn 10.10.10.1 80        # HTTP
nc -vn 10.10.10.1 22        # SSH
nc -zv 10.10.10.1 1-1000    # 1-1000 portlar skan

# Banner grabbing
nc 10.10.10.1 80
GET / HTTP/1.0
(Enter x2)

# UDP port tekshirish
nc -u 10.10.10.1 53         # DNS (UDP)

# Tinglovchi (listener)
nc -lvnp 4444               # 4444 portda kutish

# Fayl uzatish:
# Qabul qiluvchi:
nc -lvnp 9999 > fayl.txt
# Yuboruvchi:
nc 10.10.10.1 9999 < fayl.txt

# Bayroqlar:
# -l = listen (tinglash)
# -v = verbose (batafsil)
# -n = DNS hal qilmasdan
# -p = port
# -u = UDP
# -z = zero I/O (faqat tekshirish)
```

### 5. Web Brauzer — Veb Razvedka
```
# Brauzer orqali aktiv razvedka:

1. robots.txt → https://target.com/robots.txt
2. sitemap.xml → https://target.com/sitemap.xml
3. F12 → Network tab → Barcha so'rovlar
4. F12 → Sources → JS fayllar, API endpoint lar
5. CTRL+U → Manba kodi → izohlar, hidden input

# Developer Tools:
- Network: API chaqiruvlar, cookie, token
- Console: JS xatolar, ma'lumot
- Application: Cookie, localStorage, sessionStorage
```

---

## 📊 Banner Grabbing (Xizmat Ma'lumoti Olish)

```bash
# Maqsad: Xizmat versiyasini aniqlash

# HTTP
curl -I http://10.10.10.1
# Server: Apache/2.4.41 (Ubuntu)
# X-Powered-By: PHP/7.4.3

# SSH
nc -vn 10.10.10.1 22
# SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.5

# FTP
nc -vn 10.10.10.1 21
# 220 ProFTPD 1.3.5e Server

# SMTP
nc -vn 10.10.10.1 25
# 220 mail.target.com ESMTP Postfix (Ubuntu)
```

---

## 🔍 OS Fingerprinting (OS Aniqlash)

```bash
# TTL orqali (ping natijasidan):
TTL=64  → Linux/Unix/Android
TTL=128 → Windows
TTL=255 → Cisco router, Solaris

# Nmap bilan (keyingi roomda batafsil):
nmap -O 10.10.10.1          # OS detection

# Banner dan:
SSH-2.0-OpenSSH_8.2p1 Ubuntu → Ubuntu Linux
Microsoft FTP Service        → Windows
```

---

## 🎯 Amaliy Misol: To'liq Aktiv Razvedka

```bash
# Maqsad: 10.10.10.1

# 1. Tirikmi?
ping -c 3 10.10.10.1

# 2. Yo'l qanday?
traceroute -n 10.10.10.1

# 3. Portlar tekshirish
nc -zv 10.10.10.1 20-25     # FTP, SSH, SMTP
nc -zv 10.10.10.1 80        # HTTP
nc -zv 10.10.10.1 443       # HTTPS

# 4. Banner grabbing
telnet 10.10.10.1 80
GET / HTTP/1.1
Host: 10.10.10.1

# 5. SSH versiyasi
nc -vn 10.10.10.1 22

# 6. Web tekshirish
curl -I http://10.10.10.1
curl http://10.10.10.1/robots.txt
```

---

## 💡 Eslab Qolish Uchun

| Vosita | Vazifasi | Misol |
|--------|---------|-------|
| **ping** | Tirikmi? TTL→OS | `ping -c 4 IP` |
| **traceroute** | Yo'l kuzatish | `traceroute -n IP` |
| **telnet** | Port + banner | `telnet IP 80` |
| **netcat** | Ko'p maqsadli | `nc -vn IP PORT` |
| **curl** | HTTP banner | `curl -I http://IP` |

**TTL eslatmasi:**
```
64  → Linux 🐧
128 → Windows 🪟
255 → Router/Switch 🔀
```

**Muhim:** Aktiv razvedka iz qoldiradi — faqat **ruxsat berilgan** tizimlarda ishlating!

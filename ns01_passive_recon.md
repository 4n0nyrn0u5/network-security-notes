# 🕵️ Passive Reconnaissance (Passiv Razvedka)

> **Maqsad:** Maqsad tizimga to'g'ridan-to'g'ri murojaat qilmasdan, ochiq manbalardan ma'lumot yig'ish.

---

## 📖 Passiv Razvedka nima?

**Passiv razvedka** — maqsad server yoki tarmoqqa hech qanday paket yubormay, faqat ochiq manbalardan (internet, DNS, WHOIS va boshqalar) ma'lumot to'plash.

```
Aktiv razvedka:  Siz → Maqsad (to'g'ridan so'rov)  ← Iz qoldiradi!
Passiv razvedka: Siz → Ochiq manbalar              ← Iz qoldirmaydi!
```

---

## 🔍 Asosiy Vositalar

### 1. WHOIS — Domen Egasi Ma'lumoti
```bash
whois tryhackme.com

# Natijada ko'rish mumkin:
# - Domen egasi ismi/tashkiloti
# - Ro'yxatdan o'tgan sana
# - Muddati tugash sanasi
# - Name serverlar
# - Registrar (kim orqali ro'yxatdan o'tgan)
# - Aloqa ma'lumotlari (ba'zan yashirilgan)
```

### 2. nslookup — DNS So'rov
```bash
# A yozuv (IPv4)
nslookup tryhackme.com

# Aniq yozuv turi
nslookup -type=A tryhackme.com       # IPv4
nslookup -type=AAAA tryhackme.com    # IPv6
nslookup -type=MX tryhackme.com      # Pochta serveri
nslookup -type=TXT tryhackme.com     # Matn yozuvi
nslookup -type=CNAME tryhackme.com   # Taxallus
nslookup -type=NS tryhackme.com      # Name server

# Boshqa DNS serverdan so'rash
nslookup -type=A tryhackme.com 8.8.8.8    # Google DNS
nslookup -type=A tryhackme.com 1.1.1.1    # Cloudflare DNS
```

### 3. dig — Kuchli DNS Vositasi
```bash
# Asosiy
dig tryhackme.com
dig tryhackme.com A
dig tryhackme.com MX
dig tryhackme.com TXT
dig tryhackme.com NS
dig tryhackme.com ANY      # Barcha yozuvlar

# Qisqa natija
dig tryhackme.com +short

# Boshqa DNS serverdan
dig @8.8.8.8 tryhackme.com

# Teskari qidirish (IP → Domen)
dig -x 104.26.10.229

# Zone transfer urinish
dig axfr @ns1.tryhackme.com tryhackme.com
```

---

## 🌐 OSINT Veb Xizmatlari

### DNSDumpster (dnsdumpster.com)
```
https://dnsdumpster.com

# Ko'rsatadi:
- Barcha subdomenlar
- IP manzillar
- MX yozuvlar
- TXT yozuvlar
- Grafik ko'rinish (xarita)
```

### Shodan (shodan.io)
```bash
# Veb saytda qidirish:
hostname:tryhackme.com
org:"TryHackMe"
ip:104.26.10.229

# Shodan CLI:
shodan host 104.26.10.229
shodan search "apache 2.4"
shodan search "port:22 country:UZ"

# Ko'rsatadi:
# - Ochiq portlar
# - Ishlatilayotgan xizmatlar
# - OS versiyasi
# - Sertifikat ma'lumotlari
# - Zaifliklar (CVE)
```

### crt.sh — SSL Sertifikat Qidirish
```bash
# Brauzerda:
https://crt.sh/?q=%25.tryhackme.com

# Terminal orqali:
curl -s "https://crt.sh/?q=%25.tryhackme.com&output=json" | \
  jq -r '.[].name_value' | sort -u

# Ko'rsatadi: Barcha subdomenlar (sertifikat orqali)
```

### Wayback Machine — Eski Versiyalar
```
https://web.archive.org/web/*/tryhackme.com

# Saytning eski versiyalarini ko'rish
# O'chirilgan sahifalar hali ham mavjud bo'lishi mumkin
```

---

## 📋 Yig'iladigan Ma'lumotlar

```
DNS yozuvlari:
  A      → IP manzillar
  MX     → Pochta serverlari
  NS     → Name serverlar
  TXT    → SPF, DKIM, tasdiqlash kodlari
  CNAME  → Taxalluslar

WHOIS:
  Domen egasi
  Ro'yxatdan o'tish sanasi
  Name serverlar
  Registrar

Subdomenlar:
  crt.sh, DNSDumpster, Shodan

Texnologiya:
  Shodan, Wappalyzer, BuiltWith

Ochiq portlar:
  Shodan → Internetga ochiq portlar
```

---

## 🎯 Amaliy Misol: To'liq Passiv Razvedka

```bash
# Maqsad: tryhackme.com

# 1. WHOIS
whois tryhackme.com | grep -E "Registrar|Creation|Expiry|Name Server"

# 2. DNS yozuvlar
nslookup -type=A tryhackme.com
nslookup -type=MX tryhackme.com
nslookup -type=NS tryhackme.com
nslookup -type=TXT tryhackme.com

# 3. Subdomenlar
curl -s "https://crt.sh/?q=%25.tryhackme.com&output=json" | \
  jq -r '.[].name_value' | sort -u > subdomenlar.txt

# 4. Shodan
shodan host $(nslookup tryhackme.com | grep Address | tail -1 | awk '{print $2}')

# 5. DNS Dump
dig tryhackme.com ANY +noall +answer
```

---

## 💡 Eslab Qolish Uchun

| Vosita | Vazifasi | Buyruq |
|--------|---------|--------|
| **whois** | Domen egasi | `whois domain.com` |
| **nslookup** | DNS so'rov | `nslookup -type=MX domain.com` |
| **dig** | Kuchli DNS | `dig domain.com ANY` |
| **Shodan** | Ochiq portlar | `shodan host IP` |
| **crt.sh** | Subdomenlar | `crt.sh/?q=%25.domain.com` |
| **DNSDumpster** | DNS xarita | dnsdumpster.com |

**Asosiy farq:**
- **Passiv** = Maqsadga tegmaymiz → xavfsiz, iz yo'q
- **Aktiv** = Maqsadga so'rov yuboramiz → iz qolishi mumkin

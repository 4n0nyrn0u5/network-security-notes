# 🚀 Nmap Advanced Port Scans (Nmap Ilg'or Port Skanlash)

> **Maqsad:** Null, FIN, Xmas, Idle scan kabi yashirinroq skan texnikalarini o'rganish — firewall va IDS ni chetlab o'tish.

---

## 📖 Nima Uchun Ilg'or Skanlar?

Oddiy SYN skan ko'p hollarda IDS/IPS tomonidan aniqlanadi. Ilg'or skanlar:
- Firewall qoidalarini chetlab o'tish
- IDS dan yashirinish
- Noodatiy paketlar yuborish orqali port holatini aniqlash

---

## 🔍 TCP Flag lar

```
SYN (S) → Ulanish boshlash
ACK (A) → Tasdiqlash
RST (R) → Reset (ulanishni buzish)
FIN (F) → Ulanishni tugatish
URG (U) → Urgent (shoshilinch)
PSH (P) → Push (ma'lumotni darhol qayta ishlash)
```

---

## 🕵️ Yashirin Skan Turlari

### 1. NULL Scan (-sN) — Hech qanday flag yo'q
```bash
sudo nmap -sN 10.10.10.1

# Paket: Hech qanday TCP flag yo'q

# Ochiq/Filtered port → Javob yo'q
# Yopiq port → RST javob

# Afzalligi: Firewall dan o'tishi mumkin
# Kamchiligi: Windows da ishlamaydi (RST qaytaradi)
```

### 2. FIN Scan (-sF) — Faqat FIN flag
```bash
sudo nmap -sF 10.10.10.1

# Paket: Faqat FIN flag

# Ochiq/Filtered → Javob yo'q
# Yopiq → RST javob

# RFC 793 ga ko'ra: ochiq portlar FIN ga javob bermaydi
```

### 3. Xmas Scan (-sX) — FIN + PSH + URG
```bash
sudo nmap -sX 10.10.10.1

# Paket: FIN + PSH + URG (chiroyli yonaydi = "Xmas tree")

# Ochiq/Filtered → Javob yo'q
# Yopiq → RST javob

# Eng ko'p aniqlanadigan skanlardan biri!
```

### NULL, FIN, Xmas taqqoshlash:
```
Port holati:     NULL/FIN/Xmas ga javob:
Ochiq         →  Javob yo'q (open|filtered)
Yopiq         →  RST
Filtered      →  Javob yo'q (open|filtered)

Muammo: Ochiq va Filtered farqini bilmaymiz!
```

---

### 4. Maimon Scan (-sM)
```bash
sudo nmap -sM 10.10.10.1

# FIN + ACK kombinatsiyasi
# BSD tizimlarida ishlaydi
# Ko'p zamonaviy tizimlarda ishlamaydi
```

### 5. ACK Scan (-sA) — Firewall Xaritasi
```bash
sudo nmap -sA 10.10.10.1

# Maqsad: Portlarni emas, firewall qoidalarini aniqlash!

# Filtered → Javob yo'q (firewall bloklaydi)
# Unfiltered → RST (firewall o'tkazadi)

# Portlar ochiq/yopiqligini aytmaydi
# Faqat firewall himoyalagan portlarni ko'rsatadi
```

### 6. Window Scan (-sW)
```bash
sudo nmap -sW 10.10.10.1

# ACK paket yuboradi
# TCP window qiymatiga qarab ochiq/yopiq aniqlaydi
# Ba'zi tizimlarda -sA dan aniqroq
```

---

## 🥷 Idle/Zombie Scan (-sI) — Eng Yashirin!

```bash
sudo nmap -sI ZOMBIE_IP 10.10.10.1

# Eng yashirin skan! Sizning IP manzilingiz ko'rinmaydi.
# "Zombie" host orqali skan qilinadi.

# Ishlash prinsipi:
# 1. Zombie ning IPID (IP ID) ni bilib oling
# 2. Siz → Zombie: SYN/ACK yuboring → Zombie IPID+1
# 3. Zombie → Maqsad: SYN yuboriladi (sizning nomingizdan emas!)
# 4. Maqsad → Zombie: SYN-ACK (ochiq) yoki RST (yopiq)
# 5. Zombie ning IPID ni tekshiring:
#    IPID+2 = Port ochiq (zombie javob berdi)
#    IPID+1 = Port yopiq

# Zombie host shartlari:
# - Faol bo'lmagan (traffic kam)
# - Ketma-ket IPID ishlatuvchi
# - Misol: Printer, eski Windows

# Zombie topish:
sudo nmap -O --script ipidseq 192.168.1.0/24
```

---

## 🛡️ Firewall va IDS ni Chetlab O'tish

### Fragment Packets (-f)
```bash
sudo nmap -f 10.10.10.1          # 8 baytli fragmentlar
sudo nmap -ff 10.10.10.1         # 16 baytli fragmentlar
sudo nmap -f --mtu 16 10.10.10.1 # Maxsus MTU
# IDS ko'p hollarda fragmentlangan paketlarni yig'a olmaydi
```

### Decoy (Aldov) Scan (-D)
```bash
sudo nmap -D RND:5 10.10.10.1           # 5 ta tasodifiy IP
sudo nmap -D 1.2.3.4,5.6.7.8 10.10.10.1 # Aniq aldov IP lar
sudo nmap -D ME,RND,RND 10.10.10.1      # ME = sizning IP

# Maqsad logida ko'p IP dan skan ko'rinadi
# Qaysi haqiqiy ekanligini bilmaydi
```

### Source Port Spoofing
```bash
sudo nmap --source-port 53 10.10.10.1   # DNS portdan kelayotgandek
sudo nmap --source-port 80 10.10.10.1   # HTTP portdan
# Ba'zi firewall 53-portdan kelgan trafikni o'tkazadi
```

### Randomize Hosts
```bash
nmap --randomize-hosts 10.10.10.0/24
# Hostlarni tartibsiz skanerlaydi
```

---

## ⏱️ Timing va Aggressivlik

```bash
# Kechikish qo'shish
nmap --scan-delay 1s 10.10.10.1       # Har paket orasida 1 soniya
nmap --max-scan-delay 2s 10.10.10.1

# Paketlar soni
nmap --min-rate 100 10.10.10.1        # Min 100 paket/soniya
nmap --max-rate 100 10.10.10.1        # Max 100 paket/soniya
nmap --max-retries 1 10.10.10.1       # Qayta urinish soni
```

---

## 🎯 Amaliy Misollar

```bash
# 1. Firewall xaritasi
sudo nmap -sA -T4 10.10.10.1

# 2. Yashirin FIN skan
sudo nmap -sF -T2 10.10.10.1

# 3. Decoy bilan SYN skan
sudo nmap -sS -D RND:10 10.10.10.1

# 4. Fragment + Decoy
sudo nmap -sS -f -D RND:5 10.10.10.1

# 5. Zombie skan
sudo nmap -sI 192.168.1.100 10.10.10.1

# 6. Source port 53
sudo nmap -sS --source-port 53 10.10.10.1
```

---

## 💡 Eslab Qolish Uchun

| Skan | Parametr | Maqsad |
|------|----------|--------|
| **NULL** | `-sN` | Hech flag yo'q |
| **FIN** | `-sF` | Faqat FIN |
| **Xmas** | `-sX` | FIN+PSH+URG |
| **ACK** | `-sA` | Firewall xaritasi |
| **Idle** | `-sI ZOMBIE` | Eng yashirin |
| **Decoy** | `-D RND:5` | Ko'p IP aldov |
| **Fragment** | `-f` | Paketni bo'lish |

**Asosiy qoida:**
```
NULL/FIN/Xmas:
  Ochiq   → Javob yo'q  ← Firewall ham javob bermaydi (farq bilmaymiz!)
  Yopiq   → RST

ACK:
  Filtered   → Javob yo'q   (firewall bor)
  Unfiltered → RST          (firewall yo'q)
```

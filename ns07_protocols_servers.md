# 🌐 Protocols and Servers (Protokollar va Serverlar)

> **Maqsad:** HTTP, FTP, POP3, SMTP, IMAP protokollarini va ularning zaifliklarini o'rganish.

---

## 📖 Umumiy Ma'lumot

Tarmoq protokollari — kompyuterlar orasida ma'lumot almashish qoidalari. Ko'p protokollar **ochiq matn** (cleartext) da ishlaydi → tinglash xavfi bor!

---

## 🌍 HTTP (HyperText Transfer Protocol)

```
Port: 80 (HTTP), 443 (HTTPS)
Zaiflik: Ochiq matn → Tinglash mumkin

# Qo'lda so'rov:
telnet 10.10.10.1 80
GET / HTTP/1.1
Host: 10.10.10.1
(Enter x2)

# curl bilan:
curl http://10.10.10.1
curl -I http://10.10.10.1          # Faqat headerlar
```

---

## 📁 FTP (File Transfer Protocol)

```
Port: 21 (buyruq), 20 (ma'lumot)
Zaiflik: Ochiq matn → Foydalanuvchi nomi + parol ko'rinadi!
```

```bash
# FTP ga ulanish:
ftp 10.10.10.1
# Username: anonymous    ← Anonim kirish (parolsiz)
# Password: (Enter)

# Yoki:
ftp 10.10.10.1
# Username: admin
# Password: password

# FTP buyruqlari:
ls          → Fayllar ro'yxati
ls -la      → Batafsil ro'yxat
cd /dir     → Papkaga o'tish
get file    → Fayl yuklab olish
put file    → Fayl yuklash
mget *      → Barcha fayllarni yuklab olish
binary      → Ikkilik rejim (rasmlar, arxiv uchun)
ascii       → Matn rejim
passive     → Passive mode
bye/quit    → Chiqish

# Nmap bilan anonim tekshirish:
nmap --script=ftp-anon 10.10.10.1

# Hydra bilan brute force:
hydra -l admin -P /usr/share/wordlists/rockyou.txt ftp://10.10.10.1
```

---

## 📧 SMTP (Simple Mail Transfer Protocol)

```
Port: 25 (SMTP), 587 (Submission), 465 (SMTPS)
Zaiflik: Ochiq matn, foydalanuvchi enumeration
```

```bash
# SMTP ga ulanish:
telnet 10.10.10.1 25
nc 10.10.10.1 25

# SMTP buyruqlari:
HELO attacker.com         → Salomlashish
EHLO attacker.com         → Extended salomlashish (imkoniyatlarni ko'rish)
VRFY username             → Foydalanuvchi bormi? (enumeration!)
EXPN mailing-list         → Email ro'yxati
MAIL FROM: <test@test.com>
RCPT TO: <victim@target.com>
DATA                      → Xabar yozish boshlash
.                         → Xabar tugadi (nuqta + Enter)
QUIT                      → Chiqish

# VRFY bilan foydalanuvchi aniqlash:
VRFY root
# 250 root <root@target>  → Mavjud!
# 550 root... User unknown → Yo'q

# Nmap bilan:
nmap --script=smtp-enum-users 10.10.10.1
nmap --script=smtp-commands 10.10.10.1
```

---

## 📬 POP3 (Post Office Protocol v3)

```
Port: 110 (POP3), 995 (POP3S)
Zaiflik: Ochiq matn → Parol va xabarlar ko'rinadi!
Vazifasi: Serverdan xabarlarni yuklab olish
```

```bash
# POP3 ga ulanish:
telnet 10.10.10.1 110
nc 10.10.10.1 110

# POP3 buyruqlari:
USER username        → Foydalanuvchi nomi
PASS password        → Parol
STAT                 → Xabarlar soni va hajmi
LIST                 → Xabarlar ro'yxati
RETR 1               → 1-xabarni o'qish
DELE 1               → 1-xabarni o'chirish
QUIT                 → Chiqish

# Misol:
telnet 10.10.10.1 110
+OK POP3 server ready
USER admin
+OK
PASS password123
+OK Logged in
STAT
+OK 5 3200  → 5 ta xabar, 3200 bayt
LIST
RETR 1      → Birinchi xabarni o'qi
```

---

## 📮 IMAP (Internet Message Access Protocol)

```
Port: 143 (IMAP), 993 (IMAPS)
Zaiflik: Ochiq matn
Farqi POP3 dan: Xabarlar serverda qoladi, bir nechta qurilmada o'qish
```

```bash
# IMAP ga ulanish:
telnet 10.10.10.1 143
nc 10.10.10.1 143

# IMAP buyruqlari (har buyruq oldida ID kerak):
a1 LOGIN username password    → Kirish
a2 LIST "" "*"                → Papkalar ro'yxati
a3 SELECT INBOX               → INBOX ni ochish
a4 FETCH 1 BODY[]             → 1-xabarni o'qish
a5 FETCH 1:* FLAGS            → Barcha xabarlar holati
a6 LOGOUT                     → Chiqish
```

---

## 🔐 Sniffing (Tinglash) Hujumi

```bash
# Tcpdump bilan tarmoqni tinglash:
sudo tcpdump -i eth0                    # Barcha trafik
sudo tcpdump -i eth0 port 21           # FTP trafik
sudo tcpdump -i eth0 port 25           # SMTP trafik
sudo tcpdump -i eth0 port 110          # POP3 trafik
sudo tcpdump -i eth0 -A port 110       # Matn shaklida ko'rish

# Wireshark bilan:
# Filter: ftp || smtp || pop || imap
# Credentials ko'rinadi!

# Hydra bilan brute force:
hydra -l admin -P rockyou.txt ftp://10.10.10.1
hydra -l admin -P rockyou.txt smtp://10.10.10.1
hydra -l admin -P rockyou.txt pop3://10.10.10.1
hydra -l admin -P rockyou.txt imap://10.10.10.1
```

---

## 📋 Protokollar Xulasasi

| Protokol | Port | Shifrlash | Zaiflik |
|----------|------|-----------|---------|
| HTTP | 80 | ❌ | Trafik ko'rinadi |
| HTTPS | 443 | ✅ | Xavfsiz |
| FTP | 21 | ❌ | Parol ochiq |
| FTPS | 990 | ✅ | Xavfsiz |
| SMTP | 25 | ❌ | Xabar ochiq |
| SMTPS | 465 | ✅ | Xavfsiz |
| POP3 | 110 | ❌ | Parol + xabar ochiq |
| POP3S | 995 | ✅ | Xavfsiz |
| IMAP | 143 | ❌ | Parol + xabar ochiq |
| IMAPS | 993 | ✅ | Xavfsiz |

---

## 💡 Eslab Qolish Uchun

```
FTP:  21  → ftp IP → anonymous kirish / get fayl
SMTP: 25  → telnet IP 25 → VRFY user (enum)
POP3: 110 → telnet IP 110 → USER/PASS/RETR
IMAP: 143 → telnet IP 143 → LOGIN/SELECT/FETCH

Brute force:
hydra -l user -P wordlist.txt PROTOKOL://IP

Sniff:
tcpdump -i eth0 -A port 110
```

**Xavfsiz alternativlar:**
- FTP → SFTP (SSH orqali) yoki FTPS
- SMTP → SMTPS (465) yoki STARTTLS
- POP3 → POP3S (995)
- IMAP → IMAPS (993)

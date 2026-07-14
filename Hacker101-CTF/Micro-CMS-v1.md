<div align="center">

# 🚩 Micro-CMS v1 — CTF Writeup
### [Hacker101](https://ctf.hacker101.com/ctf)

</div>

> **Author:** Razan Arsya Haqiyya  
> **Role:** Cybersecurity Enthusiast  
> **Platform:** Hacker101 CTF  
> **Difficulty:** Easy

---

## 👋 Intro

Halo semuanya! Saya **Razan**, seorang cybersecurity enthusiast. Di sini saya akan membagikan pengalaman saya mengerjakan CTF (*Capture The Flag*).

> ⚠️ **Disclaimer:** Saya bukanlah seorang expert. Tujuan writeup ini hanya untuk *sharing* pengetahuan saya yang sedang mempelajari cybersecurity. Semoga bermanfaat!

---

## 🎯 Target

| Detail | Info |
|--------|------|
| **Challenge** | Micro-CMS v1 |
| **Platform** | [Hacker101 CTF](https://ctf.hacker101.com/ctf) |
| **Category** | Web Exploitation |

---

## 🏁 Flag 1: Stored XSS

### 🔍 Reconnaissance

<img width="717" height="205" alt="Screenshot 2026-07-15 004746" src="https://github.com/user-attachments/assets/bd12a444-5dc7-4852-bdfb-e8309ee76ef5" />

Web ini merupakan aplikasi CMS sederhana untuk membuat halaman baru. User dapat memasukkan **judul** dan **deskripsi** halaman.

> 💡 **Insight:** Karena ada input dari user, kemungkinan besar ada celah **XSS (Cross-Site Scripting)**.

### 🧪 Exploitation

Saya mencoba menuliskan payload XSS di kolom **title**:

```html
hello<script>alert(1);</script>
```

<img width="822" height="485" alt="Screenshot 2026-07-15 005457" src="https://github.com/user-attachments/assets/b61e0783-a02e-440f-863a-91ef0a3a2a90" />

### ✅ Result

Setelah kembali ke halaman **home**, pop-up alert muncul dan saya mendapatkan **flag pertama**!

<img width="1917" height="1025" alt="Screenshot 2026-07-15 005527" src="https://github.com/user-attachments/assets/1efab3d0-fd83-44b0-8073-7f4da70719b7" />

---

## 🏁 Flag 2: SQL Injection (SQLi)

### 🔍 Reconnaissance

Saat mencoba mengedit halaman, saya menyadari bahwa URL memiliki parameter **ID** halaman:

```
/page/edit/1
```

### 🧪 Exploitation

Saya menguji URL dengan menempatkan tanda **single quote (`'`)** di akhir parameter ID:

```
/page/edit/1'
```

### ✅ Result

Boom! Langsung muncul **flag kedua**!

<img width="885" height="256" alt="Screenshot 2026-07-15 010442" src="https://github.com/user-attachments/assets/ac659dfb-7175-461f-986b-66731c4c02af" />

---

## 🏁 Flag 3: IDOR / Unauthorized Access

### 🔍 Reconnaissance

Saya mengecek kembali halaman baru yang sudah saya buat saat mencoba payload XSS. Ternyata **ID pada URL adalah 8**, padahal halaman yang dibuat di web tersebut baru ada **3**.

```
/page/edit/8
```

<img width="743" height="307" alt="Screenshot 2026-07-15 010609" src="https://github.com/user-attachments/assets/48e103b4-d271-4998-a354-04d895c5c42e" />

### 🧪 Exploitation

Saya mencoba mengubah parameter `id` secara manual dari **1 sampai 8**. Ternyata pada **ID 5** memberikan respon berbeda yaitu **Forbidden**.

<img width="1025" height="282" alt="Screenshot 2026-07-15 010627" src="https://github.com/user-attachments/assets/1454b0cf-5cc3-42d4-96ae-abbbee685aaf" />

> 💡 **Insight:** Semua halaman punya fitur edit dengan path `/page/edit/<id>`. Mungkin saja halaman "rahasia" juga bisa diakses lewat path yang sama!

Saya coba akses:

```
/page/edit/5
```
<img width="1062" height="258" alt="Screenshot 2026-07-15 010718" src="https://github.com/user-attachments/assets/67521e95-0d53-4020-b27b-2f8a46980cbc" />

### ✅ Result

Berhasil! Saya mendapatkan **flag ketiga** tanpa otorisasi yang seharusnya diperlukan.

<img width="942" height="512" alt="Screenshot 2026-07-15 010727" src="https://github.com/user-attachments/assets/29cf6dd1-c75b-4796-973b-ebbfedcf688a" />

---

## 🏁 Flag 4: Stored XSS (Markdown Bypass)

### 🔍 Reconnaissance

Saat mengedit halaman, saya menyadari bahwa **isi halaman** mendukung **Markdown**, namun **tidak mengizinkan tag `<script>`**.

<img width="642" height="445" alt="Screenshot 2026-07-15 011730" src="https://github.com/user-attachments/assets/9928ce43-d451-4fd7-a4ad-c6ddbaad9eca" />

### 🧪 Exploitation

Saya harus menemukan cara untuk melewati filter Markdown agar bisa mengeksekusi XSS. Setelah mencari dan mencoba berbagai payload, akhirnya ketemu yang works:

```html
<button onclick=alert('xss')>click</button>
```

<img width="1157" height="515" alt="Screenshot 2026-07-15 012059" src="https://github.com/user-attachments/assets/eb72529b-856d-4acf-8160-e7340601851f" />

### ✅ Result

Setelah mengecek **source code** halaman, flag berhasil didapatkan!

<img width="818" height="222" alt="Screenshot 2026-07-15 012348" src="https://github.com/user-attachments/assets/3e1f035f-60d4-40a8-bad2-2f1d3b77ceee" />

---

## 📝 Lessons Learned

| # | Takeaway |
|---|----------|
| 1 | 🔒 **Input Validation** sangat penting — jangan pernah percaya input dari user! |
| 2 | 🛡️ **Parameterized Queries** bisa mencegah SQL Injection. |
| 3 | 🔐 **Access Control** harus diterapkan di setiap endpoint, bukan hanya di frontend. |
| 4 | 🧹 **Sanitasi Markdown/HTML** harus cukup ketat untuk mencegah XSS bypass. |

---

## 🔗 Referensi

- [Hacker101 CTF](https://ctf.hacker101.com/ctf)
- [OWASP XSS Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html)
- [OWASP SQL Injection](https://owasp.org/www-community/attacks/SQL_Injection)

---

<div align="center">

⭐ **Thanks for reading!**  
*Feel free to connect and discuss!*

</div>
<img width="717" height="205" alt="Screenshot 2026-07-15 004746" src="https://github.com/user-attachments/assets/1430234e-ebdd-4753-a61e-9d3d26b1bdf9" />

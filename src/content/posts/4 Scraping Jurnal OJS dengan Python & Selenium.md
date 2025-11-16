---
title: "Kumpulin Otomatis Paper Jurnal Se-Indonesia"
meta_title: "Kumpulin Otomatis Paper Jurnal Se-Indonesia"
description: "Capek download paper dari banyak jurnal satu per satu? Saya juga. Di artikel ini, saya bagikan pengalaman membuat alat otomatis dengan Python untuk mengumpulkan data artikel jurnal dari ribuan situs OJS. Cocok untuk peneliti, mahasiswa, atau siapa pun yang butuh data"

image: "../../assets/images/post4/cover4.webp"
authors: ["alditpra"]
categories: ["Automation"]
tags: ["Python", "Selenium", "OJS", "Web Scraping", "Tutorial"]
---

> **Disclaimer:** Skrip yang saya bahas di sini adalah hasil eksperimen pribadi untuk keperluan riset. Gunakan dengan bijak dan hormati kebijakan masing-masing situs jurnal. Scraping yang agresif bisa membebani server—jangan jadi "tamu" yang bikin masalah.

Jadi suatu hari saya pingin cari dan kumpulin beberapa paper jurnal untuk dijadikan referensi. Cari web jurnal satu persatu, klik judul paper lalu download file PDFnya, hadeh. Terus saya mikir, **kok bisa ya di era 2025 masih cari manual kayak gini?** Kan harusnya bisa diotomatisasi. Dan akhirnya, setelah trial and error, saya berhasil bikin skrip Python yang bisa ngambil data artikel dari ratusan situs jurnal OJS (Open Journal Systems) secara otomatis.

> Lebih milih ngoding daripada cari manual, soalnya siapa tau besok butuh cari paper lagi.

---

## Kenapa Selenium, Bukan Requests + BeautifulSoup?

Ini pertanyaan pertama yang pasti muncul kalau kamu familiar dengan web scraping. Awalnya saya juga mikir, "Ah gampang, tinggal pakai `requests` sama `BeautifulSoup`, beres." Tapi ternyata enggak sesimpel itu.

Masalah utamanya adalah **banyak situs jurnal modern dilindungi Cloudflare atau anti-bot lainnya**. Setiap kali saya coba pakai `requests`, selalu dapat respons "Access Denied" atau kena *rate limit*. Saya udah coba berbagai cara: fake user agent, pakai proxy, delay request, tetep aja diblokir. Proteksi modern itu jauh lebih canggih dari sekadar checking user agent.

Nah, **Selenium jadi solusi pamungkas**. Karena Selenium ngontrol browser sungguhan (Chrome atau Firefox), dari sisi server, aktivitas scraping kita **terlihat seperti user biasa yang browsing**. Servernya enggak bisa bedain mana bot mana manusia, karena memang teknis-nya ya manusia yang lagi browsing (cuma otomatis aja).

Memang trade-off-nya jelas: **Selenium lebih lambat dan lebih boros resource**. Tapi tingkat keberhasilannya jauh lebih tinggi. Daripada cepat tapi gagal terus, mending agak lambat tapi berhasil. Saya udah banyak ngulik di sini dan kesimpulannya: **untuk situs yang heavily protected, Selenium adalah pilihan paling reliable**.

---

## Bikin Skrip yang Robust, Bukan Asal Jalan

Pengalaman ngoding beberapa tahun ngajarin saya satu hal: **skrip yang baik itu harus fleksibel dan tahan banting**. Apalagi kalau buat scraping, yang kondisinya bisa berubah-ubah: koneksi internet drop, server jurnal lemot, struktur HTML tiba-tiba berubah. Kalau skripnya brittle, bakal sering gagal dan bikin frustasi.

### Konfigurasi di Awal itu Penting Banget

Saya selalu mulai dengan blok konfigurasi di bagian atas skrip. Ini krusial karena **kebutuhan setiap proyek bisa beda-beda**. Kadang saya cuma butuh scrape beberapa jurnal untuk testing, kadang butuh proses ratusan sekaligus. Dengan config yang jelas, saya bisa gampang adjust tanpa harus ubah-ubah kode di banyak tempat.

```python
# --- Konfigurasi ---
FILE_INPUT_JURNAL = "comotdarisini.xlsx"
JUMLAH_JURNAL_DARI_EXCEL = 3  # Set ke None untuk memproses semua daftar jurnal di xlsx
JUMLAH_ISU_TERAKHIR = 3 # ambil semua paper dari 3 issue terakhir
JEDA_REQUEST_DETIK = 3
PAGE_LOAD_TIMEOUT_DETIK = 20
RETRY_ATTEMPTS = 3
```

Parameter `JUMLAH_JURNAL_DARI_EXCEL` sangat berguna waktu development. Saya set ke angka kecil dulu (misalnya 3), test dulu semua jalan lancar, baru kemudian ubah jadi `None` untuk eksekusi penuh. **Enggak ada yang lebih nyebelin daripada nunggu skrip jalan 2 jam baru tau ada bug di menit ke-115**.

### Mekanisme Retry: Karena Internet Enggak Selalu Stabil

Internet kita enggak selalu perfect. Server jurnal bisa lemot, koneksi bisa putus tiba-tiba, atau Cloudflare lagi bad mood. **Tanpa mekanisme retry, satu kegagalan bisa stop seluruh proses**. Bayangin udah jalan 1 jam, tau-tau error karena satu request timeout. Kesel kan?

Makanya saya implement retry mechanism yang sederhana tapi efektif:

```python
def get_page_source(driver, url, retries=RETRY_ATTEMPTS):
    for attempt in range(retries):
        try:
            driver.get(url)
            WebDriverWait(driver, 30).until(
                EC.presence_of_element_located((By.TAG_NAME, "body"))
            )
            time.sleep(JEDA_REQUEST_DETIK)
            return driver.page_source
        except Exception as e:
            logging.warning(f"Gagal memuat {url} (Percobaan {attempt + 1}): {e}")
            if attempt < retries - 1:
                time.sleep(JEDA_REQUEST_DETIK)
            else:
                logging.error(f"Gagal total memuat URL {url} setelah {retries} percobaan.")
    return None
```

Logikanya simpel: coba 3 kali, kalau gagal semua baru log error dan lanjut ke URL berikutnya. **Dengan cara ini, satu atau dua kegagalan enggak akan menghentikan seluruh proses**.

---

## Masalah Terbesar: Struktur OJS yang Enggak Konsisten

Ini yang paling bikin pusing. **OJS itu open source dan banyak versi yang berbeda**. Ada OJS 2, OJS 3, dan masing-masing institusi suka custom theme-nya sendiri. Hasilnya? Struktur HTML dan URL pattern-nya beda-beda semua. Yang berhasil di jurnal A, belum tentu work di jurnal B.

### Tantangan 1: URL Arsip yang Bervariasi

URL halaman arsip bisa macam-macam: ada yang `/archive`, ada yang `/issue/archive`, ada yang `/issues/archive`. Kalau cuma hardcode satu pattern, pasti banyak yang gagal.

Solusinya: **bikin fungsi yang secara pintar generate URL arsip** berdasarkan pola umum OJS:

```python
from urllib.parse import urljoin

def temukan_url_arsip(url_utama):
    cleaned_url = url_utama.rstrip('/')
    if cleaned_url.endswith('/index') or cleaned_url.endswith('/about'):
        base_url = cleaned_url[:-6]
    else:
        base_url = cleaned_url

    url_arsip_lengkap = urljoin(base_url + '/', 'issue/archive')
    return url_arsip_lengkap
```

Pendekatan ini **jauh lebih andal** karena enggak bergantung pada elemen HTML di homepage yang bisa berubah-ubah atau dihapus. Kita langsung construct URL-nya berdasarkan base URL yang kita punya.

### Tantangan 2: Struktur HTML yang Berbeda-beda

Nah, ini yang paling ribet. Untuk mengatasi variasi struktur antar-versi OJS, saya pake **strategi multi-selector**. Konsepnya simpel: saya define beberapa selector yang umum dipakai, terus loop satu per satu sampai ketemu yang cocok.

```python
possible_selectors = [
    'ul.issues_archive a.title',
    'div.obj_issue_summary a.title',
    'div#issues h2 a',
    'td.tocTitle a'
]

for selector in possible_selectors:
    links = soup.select(selector)
    if links:
        logging.info(f"Ditemukan link menggunakan selector: '{selector}'")
        break
```

Ini kayak mencoba beberapa anak kunci sampai nemuin yang pas. Memang enggak elegan, tapi **pragmatis dan work di mayoritas kasus**. Sering salah juga sih, tapi setidaknya success rate-nya lumayan tinggi.

---

## Ekstraksi Data yang Adaptif

Struktur data OJS juga sering enggak konsisten. Misalnya, **kata kunci** bisa ada di `div.item.keywords` (OJS 3) atau `div#articleSubject` (OJS 2). **DOI** bisa berupa teks biasa atau link, kadang pakai label "DOI:", kadang enggak.

Buat ngatasin DOI yang formatnya berantakan, saya kombinasikan selector dengan regex:

```python
import re

raw_doi_text = ""
doi_element_modern = soup.select_one('.item.doi .value')
if doi_element_modern:
    raw_doi_text = doi_element_modern.get_text()

if raw_doi_text:
    match = re.search(r'(10\.\d{4,9}/[-._;()/:A-Z0-9]+)', raw_doi_text, re.I)
    if match:
        doi = 'https://doi.org/' + match.group(1)
```

Regex-nya match pattern standar DOI (yang selalu dimulai dengan `10.`), jadi meskipun ada teks tambahan kayak "DOI:" atau "https://doi.org/" di depannya, tetep bisa di-extract dengan bersih.

---

## Hasil Akhir: Data Siap Pakai dalam Excel

Setelah semua proses selesai, skrip akan generate file Excel `hasil_scraping_multi_jurnal.xlsx` dengan struktur seperti ini:

| No | SINTA | Tanggal Terbit | Judul Artikel                          | Kata Kunci                  | Abstrak                                             | Link PDF | URL Artikel | DOI          | Judul Jurnal                |
| -- | ----- | -------------- | -------------------------------------- | --------------------------- | --------------------------------------------------- | -------- | ----------- | ------------ | --------------------------- |
| 1  | S2    | 2024-03-15     | Analisis Strategi UMKM Pasca Pandemi   | UMKM; strategi; pemulihan   | Artikel ini membahas strategi adaptif UMKM...       | [PDF]    | [Link]      | [DOI]        | Jurnal Ekonomi & Bisnis     |
| 2  | S3    | 2023-11-02     | Implementasi AI dalam Pendidikan Dasar | AI; pendidikan; teknologi   | Penelitian ini mengeksplorasi penerapan AI...       | [PDF]    | [Link]      | N/A          | Jurnal Teknologi Edukasi    |
| 3  | S4    | 2024-01-28     | Pengaruh Sosial Media pada Literasi    | sosial media; literasi; anak | Studi ini menyoroti efek media sosial pada anak... | [PDF]    | [Link]      | [DOI]        | Jurnal Komunikasi & Media   |

> **Note:** Data di atas dummy untuk ilustrasi. Data asli bisa jauh lebih banyak tergantung jumlah jurnal dan issue yang di-scrape. Saya sudah scrap ribuan tapi kelamaan jadi stop aja.

Dari satu website jurnal OJS, kita bisa pilih untuk **ambil hanya satu issue terbaru** (beserta seluruh artikelnya), atau **ambil semua issue** yang pernah diterbitkan. Kalau kita input banyak URL jurnal sekaligus, **jumlah data bisa mencapai ribuan artikel**—atau bahkan lebih, tergantung skala jurnalnya.

Yang penting, **semua data sudah terstruktur rapi dalam Excel**, siap untuk dianalisis atau dimasukkan ke database. Enggak perlu lagi copy-paste manual yang makan waktu berjam-jam.

---

## Kesimpulan: Trial and Error yang Worth It

Membangun scraper untuk jurnal OJS memang penuh tantangan. **Tidak ada standardisasi**, setiap situs punya struktur sendiri, dan proteksi anti-bot semakin ketat. Tapi dengan pendekatan yang terstruktur, fleksibel, dan etis, tugas ini sangat mungkin untuk diotomatisasi.

Skrip ini adalah hasil dari **banyak iterasi dan penyempurnaan** berdasarkan masalah nyata yang saya hadapi. Setiap kasus pasti punya keunikan tersendiri—apa yang work di jurnal A belum tentu work di jurnal B. Tapi **kerangka kerja yang saya share di sini bisa jadi fondasi yang kuat** untuk disesuaikan dengan kebutuhan riset kamu.

Yah, semoga bermanfaat!
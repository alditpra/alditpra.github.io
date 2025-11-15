---
title: "Kenapa Saya Akhirnya Pilih Astro untuk Web Pribadi (Setelah Cobain Segala Macam Framework)"
meta_title: "Opini pribadi dari Astro, Next.js, WordPress, hingga Blogger"
description: "Pandangan tentang berbagai pendekatan arsitektur web, dari Astro, Next.js, Hugo, hingga WordPress dan Blogger. Mana yang cocok untuk kebutuhan Anda?"

image: "../../assets/images/post1/cover1.webp"
authors: ["alditpra"]
categories: ["Web Development"]
tags: ["Astro", "Next.js", "Jamstack", "WordPress"]
---

Jujur aja, saya udah capek bolak-balik ganti platform buat web pribadi. Dulu pernah pakai WordPress, terus iseng nyoba Blogger karena gratis, sempat tertarik sama Next.js karena hype-nya, bahkan pernah pakai Hugo karena katanya super cepat. Tapi masalahnya selalu sama: **biaya hosting yang numpuk** atau **takut kehilangan semua catatan** karena lupa bayar atau platform tiba-tiba berubah kebijakan.

Pengalaman terburuk saya itu waktu web lama saya di shared hosting hilang karena saya telat bayar sebulan. Padahal isinya catatan kuliah, tutorial, dan dokumentasi project yang udah saya kumpulin bertahun-tahun. Backup? Ada sih, tapi versi lama banget. Sejak kejadian itu, saya jadi parno dan mulai mikir: **kok bikin web aja harus ribet dan mahal ya?**

Makanya sekarang web ini saya bangun pakai Astro, code-nya saya taruh di GitHub, dan deploy gratis di **alditpra.github.io**. Artikel ini saya tulis bukan untuk promosi framework tertentu, tapi lebih ke sharing pengalaman saya dalam memilih arsitektur web yang **sustainable, gratis, dan aman dari kehilangan data**. Siapa tahu pengalaman saya bisa bantu teman-teman yang lagi galau milih platform juga.

---

## Kenapa Memilih Arsitektur Web itu Penting?

Banyak yang mikir, "Ah, kan tinggal bikin web aja, pake apa aja sama." Padahal enggak sesimpel itu. Pilihan arsitektur web itu bakal nentuin banyak hal: dari biaya bulanan yang harus dikeluarin, seberapa mudah maintain-nya, sampai seberapa aman data kita dari risiko hilang.

Saya pernah ngalamin sendiri fase "trial and error" yang lumayan panjang. Setiap platform punya kelebihan dan kekurangannya masing-masing, dan yang cocok buat orang lain belum tentu cocok buat kita. Apalagi kalau kebutuhan kita sebenernya simpel: **cuma pengen punya tempat nulis yang permanen, tanpa drama biaya hosting, dan konten tetap aman walau setahun enggak dibuka**.

Nah, sebelum saya jelasin kenapa akhirnya pilih Astro, saya mau cerita dulu pengalaman saya dengan berbagai platform lain. Biar teman-teman bisa dapat gambaran lengkap dan bisa ambil keputusan sendiri sesuai kebutuhan.

---

## Perjalanan Panjang Mencari Platform yang "Pas"

### WordPress: Powerful Tapi Butuh Perawatan Ekstra

WordPress ini sebenernya platform yang powerful banget. Plugin-nya banyak, tema-nya ribuan, dan komunitas support-nya gede. Dulu saya pakai WordPress untuk web pribadi karena mikir, "Kayaknya ini solusi paling mature dan gampang."

Tapi masalahnya muncul setelah beberapa bulan. **Hosting berbayar** yang harus dibayar tiap tahun, plugin yang harus diupdate terus (kalau enggak, website bisa kena hack), dan database yang semakin lama semakin berat. Belum lagi kalau ada update WordPress yang bikin plugin tertentu bermasalah. Pernah suatu hari web saya tiba-tiba error gara-gara update tema yang bentrok sama plugin.

Yang bikin saya mikir ulang: **saya cuma pengen nulis dan share catatan, kok jadi harus ngurusin maintenance server, security, dan biaya hosting segala?** Rasanya overkill banget untuk kebutuhan saya yang simpel.

### Blogger: Gratis Tapi Terbatas dan Bergantung Platform

Setelah bosen sama WordPress, saya coba pindah ke Blogger. Alasannya simpel: **gratis dan tinggal pakai**. Enggak perlu mikirin hosting, enggak perlu install apa-apa, tinggal daftar, nulis, publish. Kelar.

Awalnya sih enak. Tapi lama-lama saya ngerasa **terlalu bergantung sama Google**. Customization-nya terbatas, template-nya kebanyakan outdated, dan yang paling bikin khawatir: **kalau Google tiba-tiba shutdown Blogger gimana?** Bukan enggak mungkin loh, mengingat Google punya track record matiin produk tanpa peringatan (ingat Google Reader, Google Plus?).

Plus, dari sisi developer experience, saya ngerasa terkekang. Enggak bisa custom HTML/CSS/JS secara bebas, enggak bisa optimasi performance sepenuhnya, dan struktur URL-nya agak kaku. Buat yang cuma pengen casual blogging mungkin oke, tapi buat saya yang suka ngutak-atik dan pengen punya kontrol penuh, Blogger terlalu limiting.

### Next.js: Modern dan Powerful, Tapi Kompleks untuk Kebutuhan Sederhana

Fase berikutnya saya tertarik sama Next.js karena hype-nya di komunitas developer. Framework-nya modern, support SSR dan SSG, ecosystem React yang gede, dan banyak tutorial di mana-mana. Saya pikir, "Ini dia solusi modern yang saya cari!"

Jujur, Next.js itu memang keren. Powerful banget buat bikin aplikasi web yang kompleks, punya fitur-fitur canggih, dan performance-nya bisa dioptimasi dengan baik. Tapi untuk **use case saya yang cuma pengen bikin blog statis**, Next.js terasa **overkill**.

Setup awalnya lumayan kompleks kalau belum terbiasa dengan React ecosystem. File structure-nya banyak, konfigurasi-nya detail, dan build time-nya lebih lama dibanding static site generator lain. Yang paling berasa: **bundle size-nya gede** karena harus include React runtime, padahal sebenernya saya enggak butuh interactivity sebanyak itu.

Hosting-nya juga jadi pertimbangan. Kalau deploy di Vercel memang mudah, tapi tetep ada limitation di free tier. Dan kalau mau fully utilize SSR, tetep butuh server atau serverless function yang ada cost-nya.

### Hugo: Cepat Banget, Tapi Sintaks Template-nya Bikin Pusing

Terus saya denger tentang Hugo, static site generator yang katanya **super cepat** dan bisa generate ribuan halaman dalam hitungan detik. Saya langsung tertarik dan nyoba.

Dan bener, Hugo itu cepet banget. Build time-nya kenceng, hot reload-nya smooth, dan hasilnya pure HTML statis yang ringan. Tapi masalahnya ada di **learning curve template-nya**. Hugo pakai Go templating yang sintaks-nya menurut saya agak aneh dan enggak se-intuitif HTML/JS yang biasa saya pakai.

Dokumentasinya sebenernya lengkap, tapi saya ngerasa harus belajar "bahasa baru" lagi cuma untuk bikin template. Buat developer yang udah terbiasa sama Go mungkin enggak masalah, tapi buat saya yang lebih sering pakai JavaScript, ini jadi friction point. Customization yang harusnya simpel jadi butuh waktu ekstra karena harus bolak-balik baca dokumentasi.

Plus, ecosystem plugin/theme Hugo enggak segede Next.js atau WordPress. Jadi kalau butuh fitur tertentu, harus bikin sendiri atau modifikasi theme yang ada.

---

## Kenapa Akhirnya Pilih Astro?

Setelah keliling nyobain berbagai platform, akhirnya saya ketemu Astro. Dan setelah pakai beberapa bulan, saya ngerasa ini **kombinasi yang pas** antara kemudahan, performance, dan flexibility yang saya cari.

### Filosofi "Zero JavaScript by Default"

Yang bikin Astro beda adalah pendekatannya yang **ship HTML murni** secara default, tanpa JavaScript runtime kecuali memang dibutuhkan. Ini berarti website saya jadi super ringan dan cepat, karena browser enggak perlu download dan execute framework overhead.

Konsepnya simpel: **kenapa harus kirim JavaScript kalau kontennya statis?** Blog post saya enggak butuh React re-rendering atau Vue reactivity. User cuma perlu baca text dan lihat gambar. Dengan pendekatan Astro, halaman yang di-render itu pure HTML dan CSS, dengan JavaScript hanya di bagian-bagian tertentu yang memang butuh interactivity.

Hasilnya? **Lighthouse score saya selalu 100** untuk performance. Page load-nya instant, enggak ada blank screen atau loading spinner. User experience-nya jauh lebih baik dibanding waktu saya pakai framework yang heavy.

### Developer Experience yang Menyenangkan

Salah satu alasan terbesar saya betah sama Astro adalah **DX-nya yang enak**. Sintaks Astro component itu mirip-mirip HTML biasa, jadi enggak perlu learning curve yang curam. Kalau udah pernah coding HTML/JS, langsung bisa bikin component Astro.

Contoh component Astro yang simpel:

```astro
---
// Script section (runs at build time)
const title = "Artikel Saya";
const publishDate = new Date("2025-01-15");
---

<article>
  <h1>{title}</h1>
  <time>{publishDate.toLocaleDateString('id-ID')}</time>
  <slot />
</article>

<style>
  article {
    max-width: 800px;
    margin: 0 auto;
  }
  
  h1 {
    color: #333;
    font-size: 2rem;
  }
</style>
```

Lihat? Sintaksnya straightforward. Bagian atas (frontmatter) untuk logic, tengah untuk markup, bawah untuk styling. Enggak ada boilerplate yang ribet, enggak ada config yang overwhelming.

Dan yang paling saya suka: **Astro support banyak UI framework sekaligus**. Kalau suatu saat saya butuh component React atau Vue untuk bagian tertentu, tinggal import aja. Enggak perlu full rewrite. Flexibility-nya tinggi tapi tetep optional.

### Gratis Sepenuhnya dengan GitHub Pages

Ini yang paling penting buat saya: **biaya maintenance-nya nol rupiah**. Karena Astro generate pure static files (HTML, CSS, JS), saya bisa deploy di **GitHub Pages** tanpa bayar sepeser pun.

Setup-nya straightforward:

1. **Push code ke repository GitHub** (misal: `username/username.github.io`)
2. **Enable GitHub Pages** di repository settings
3. **Done** — website langsung live di `username.github.io`

Semua source code dan konten saya tersimpan di Git. Kalau laptop rusak atau hilang, data tetep aman di GitHub. Kalau suatu saat pengen ganti domain atau pindah hosting, tinggal clone repository dan deploy ulang. **Enggak ada risiko kehilangan data** karena lupa bayar atau server mati mendadak.

Yang paling bikin tenang: **GitHub Pages itu reliable**. Uptime-nya tinggi, bandwidth-nya generous untuk static site, dan backup otomatis karena semua di Git. Saya bisa tidur nyenyak tanpa khawatir tagihan hosting atau website tiba-tiba down.

> 💡 **Note:** Selain GitHub Pages, Astro juga bisa deploy gratis di Netlify, Cloudflare Pages, Vercel, atau lainya. Semua platformnya solid dan punya free tier yang cukup. Saya pilih GitHub Pages karena sudah familiar dengan GitHub workflow dan domain `.github.io` yang bersih.

### Content Management yang Sederhana Pakai Markdown

Astro punya fitur **Content Collections** yang menurut saya brilliant. Semua artikel saya ditulis dalam Markdown file, dengan frontmatter untuk metadata. Struktur folder-nya simpel:

```
src/
└── content/
    └── blog/
        ├── artikel-1.md
        ├── artikel-2.md
        └── artikel-3.md
```

Setiap file Markdown punya struktur yang konsisten:

```markdown
---
title: "Kenapa Saya Akhirnya Pilih Astro untuk Web Pribadi (Setelah Cobain Segala Macam Framework)"
meta_title: "Opini pribadi dari Astro, Next.js, WordPress, hingga Blogger"
description: "Pandangan tentang berbagai pendekatan arsitektur web, dari Astro, Next.js, Hugo, hingga WordPress dan Blogger. Mana yang cocok untuk kebutuhan Anda?"
publishDate: 2025-11-15
categories: ["Web Development"]
tags: ["Astro", "Next.js", "Jamstack", "WordPress"]
---

# Isi artikel dimulai di sini

Paragraf pertama...
```

**Keuntungan model gini:**

**Pertama**, konten saya jadi portable. Markdown itu universal format. Kalau suatu saat saya pengen pindah platform, tinggal copy file Markdown-nya. Enggak terkunci di database proprietary atau format khusus platform tertentu.

**Kedua**, gampang di-version control. Semua perubahan artikel tercatat di Git history. Saya bisa track revisi, rollback kalau ada yang salah, atau bahkan collaborate dengan orang lain pakai pull request.

**Ketiga**, menulis jadi fokus. Enggak perlu buka browser, login ke dashboard, tunggu loading, baru bisa nulis. Tinggal buka text editor favorit, tulis Markdown, save, commit, push. Done. Workflow-nya cepat dan enggak ada distraksi.

### Performance yang Konsisten

Salah satu masalah saya dengan WordPress adalah **performance yang inconsistent**. Kadang cepat, kadang lemot, tergantung plugin apa yang aktif atau seberapa banyak visitor. Karena WordPress dynamic, setiap request harus query database dan render HTML on-the-fly. Meskipun wordpress bisa juga sih di kustomisasi sedemikian rupa untuk performa.

Tapi dengan Astro, semua halaman di-generate sekali waktu build, terus di-serve sebagai static files. Enggak ada database query, enggak ada server-side rendering di production. **Setiap visitor dapet exact same files**, yang bisa di-cache aggressive sama CDN. 

> Gimana dengan security? aman lah lha wong static, asal akun github aman juga.

## Kesimpulan

 WordPress cocok kalau kamu butuh CMS yang powerful dengan banyak plugin. saya juga masih suka wordpress kalau perlu buat website cepat dan theme juga banyak. Next.js cocok kalau bikin aplikasi web yang kompleks dengan banyak interactivity. Hugo cocok kalau prioritas kamu adalah build speed dan kamu nyaman dengan Go templating.

Tapi kalau kebutuhan kamu **simpel seperti saya** — pengen punya tempat nulis yang permanen, gratis, cepat, dan enggak ribet — **Astro + GitHub Pages** ini kombinasi yang solid. **Bebas biaya hosting**, konten aman karena di Git, performance excellent, dan maintenance-nya minimal. Saya bisa fokus nulis tanpa khawatir tagihan hosting.

Yang paling penting: **pilih platform yang sesuai kebutuhan dan comfort level kamu**. Enggak usah ikutan hype kalau memang enggak cocok. Platform terbaik itu yang bikin kamu produktif dan tenang, bukan yang paling populer atau paling canggih.

Web ini saya host di **alditpra.github.io** — sepenuhnya gratis, sepenuhnya di bawah kontrol saya, dan yang terpenting: **gak perlu bayar**. Selama GitHub exist dan saya punya akses ke repository, web ini akan tetap online. Enggak ada tagihan yang harus dibayar, enggak ada masa expired, enggak ada drama. 

Simple as that. Semoga bermanfaat!

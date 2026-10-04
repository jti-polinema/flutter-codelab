---
marp: true
theme: default
paginate: true
size: 16:9
---

<style>
/* ====== TEMA JTI POLINEMA ====== */
section {
  font-size: 20px;
  padding: 70px 70px 64px 70px;
  color: #1b1b1b;
  /* Bar oranye di atas + latar putih keabu-abuan lembut */
  background:
    linear-gradient(90deg, #f7941d 0%, #fdb913 100%) 0 0 / 100% 10px no-repeat,
    linear-gradient(155deg, #ffffff 55%, #eef0f2 100%);
  background-repeat: no-repeat;
}
section h1 { font-size: 34px; color: #111; margin: 0.35em 0; }
section h2 { font-size: 28px; color: #333; margin: 0.35em 0; }
section h3 { font-size: 22px; color: #1b2a4a; margin: 0.35em 0; }
section p, section ul, section ol { margin: 0.3em 0; }
section table { font-size: 19px; }
section pre, section code { font-size: 17px; }
section blockquote { font-size: 19px; }
section .katex-display { font-size: 1em; margin: 0.3em 0; }
section .katex { font-size: 1.1em; }

/* Logo + teks institusi di pojok kanan atas (semua slide) */
section::before {
  content: "POLITEKNIK NEGERI MALANG\AJURUSAN TEKNOLOGI INFORMASI";
  position: absolute;
  top: 26px;
  right: 36px;
  width: 360px;
  text-align: right;
  padding-right: 92px;
  white-space: pre;
  font-size: 12px;
  font-weight: 600;
  line-height: 1.45;
  color: #1b2a4a;
  background: url('jti-polinema-logo.png') no-repeat right center / 96px 48px;
  z-index: 10;
}

/* Footer: blok oranye "jti.polinema.ac.id" + bar navy "D4 Teknik Informatika" + nomor halaman */
section::after {
  content: "D4 Teknik Informatika  ·  " counter(page);
  position: absolute;
  left: 0; right: 0; bottom: 0;
  height: 30px;
  padding-left: 200px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #ffffff;
  font-size: 13px;
  background:
    url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="200" height="30"><text x="100" y="20" text-anchor="middle" font-family="Arial" font-size="13" font-weight="bold" fill="%231b2a4a">jti.polinema.ac.id</text></svg>') no-repeat left top / 200px 30px,
    linear-gradient(to right, #f7941d 0 200px, #1b2a4a 200px 100%);
}

/* ====== SLIDE JUDUL (class: lead) ====== */
section.lead {
  justify-content: center;
  text-align: left;
  padding-left: 80px;
}
section.lead h1 {
  font-size: 48px;
  font-weight: 800;
  color: #111;
  border: none;
}
section.lead h2 {
  font-size: 34px;
  font-weight: 600;
  color: #666;
  margin-top: 0;
}
section.lead h3 {
  font-size: 19px;
  font-weight: 500;
  color: #222;
  margin-top: 18px;
}
section.lead p { color: #333; }

/* Hapus garis bawah default h1 pada theme default */
section h1 { border-bottom: none; }

/* Slide padat: konten auto menyusut & tengah */
section.fit {
  display: flex;
  flex-direction: column;
  justify-content: center;
}
section.fit h1 { font-size: 30px; margin: 0.25em 0; }
section.fit h2 { font-size: 24px; margin: 0.25em 0; }
section.fit h3 { font-size: 20px; margin: 0.25em 0; }
section.fit p, section.fit ul, section.fit ol { margin: 0.25em 0; }
section.fit pre { margin: 0.3em 0; line-height: 1.35; }
section.fit pre, section.fit code { font-size: 15px; }
section.fit blockquote { font-size: 17px; margin: 0.3em 0; }
section.fit table { font-size: 17px; }
</style>

<!-- _class: lead -->

# Week 8
## Mid Project Review & Code Review

**Pemrograman Mobile**

Flutter • Git Flow • Pull Request • Code Review • Static Analysis

> Dari kode yang jalan → kode yang lolos review tim

---

# Learning Outcomes

Setelah mengikuti pertemuan ini, mahasiswa mampu:

1. Menjelaskan Git Flow dan strategi branching.
2. Menulis commit dan Pull Request yang berkualitas.
3. Melakukan code review yang konstruktif.
4. Menjalankan static analysis dan linter.
5. Menilai kesiapan project tengah semester.
6. Memverifikasi saran review dari AI.

---

# Mengapa Review?

Project Week 7 rapi, tapi:

```text
Commit "fix bug" tanpa penjelasan, tak bisa dilacak
PR 800 baris tanpa deskripsi, reviewer bingung
Style beda tiap file, review habis untuk spasi
```

Project tim butuh disiplin:

```text
branch kecil -> PR kecil -> review cepat
commit jelas -> riwayat bisa dibaca
analyze bersih -> review fokus ke logika
```

**Tujuan:** kode yang tidak hanya jalan, tapi layak kolaborasi.

---

# Mini Poll

### Commit mana yang paling mudah direview?

**A.** `fix bug`
**B.** `feat(notes): tambah validasi judul kosong`
**C.** `update`
**D.** `final fix beneran`

### Diskusi

> Apa beda "kode jalan" dengan "kode siap direview"?

---

# Bagian 1
## Git Flow & Kolaborasi

---

<!-- _class: fit -->

# Branching Model

*Bagian 1 - Git Flow: satu branch, satu tujuan.*

```text
main      -> selalu rilis, dilindungi
develop   -> integrasi fitur mingguan
feature/* -> kerja harian, pendek
fix/*     -> perbaikan cepat + test
```

Aturan main:

> Branch dari `develop`, PR kembali ke `develop`.
> `main` hanya via release. Jangan commit langsung.

---

<!-- _class: fit -->

# Commit Berkualitas

*Satu commit = satu perubahan logis yang bisa direview.*

```text
baik: feat(notes): validasi judul kosong + test
baik: fix(auth): refresh token sebelum retry 1x
jelek: fix bug / update / final fix beneran
```

Format: `tipe(scope): deskripsi singkat`

> `feat`, `fix`, `refactor`, `test`, `docs`, `chore`.

---

<!-- _class: fit -->

# Anatomi Pull Request

*PR kecil direview cepat. PR besar ditakuti reviewer.*

```text
Judul: feat(notes): validasi + test (maks 1 fitur)
Isi: masalah -> solusi -> cara uji -> screenshot
Ukuran: < 300 baris, < 3 hari kerja
Status: analyze + test hijau sebelum minta review
```

> PR tanpa cara uji = PR yang belum siap.

---

<!-- _class: fit -->

# Issue Tracking

*Setiap PR berawal dari issue yang jelas.*

```text
issue #42 -> branch fix/42-login-crash
  -> commit kecil -> PR "Closes #42"
  -> review -> merge -> issue tertutup
```

Checklist issue yang baik:

- Langkah reproduksi + ekspektasi hasil
- Label dan prioritas yang jelas

---

# Bagian 2
## Code Review & Static Analysis

---

# Apa yang Direview?

Urutkan fokus dari penting ke sepele:

1. **Kebenaran** - logika, edge case, error handling.
2. **Arsitektur** - arah dependensi, layer, kontrak.
3. **Keterbacaan** - nama, fungsi kecil, komentar perlu.
4. **Test** - ada test untuk perilaku baru?
5. **Style** - serahkan ke formatter, bukan debat.

> Jangan habiskan review untuk spasi. Otomatiskan.

---

<!-- _class: fit -->

# Checklist Review 15 Menit

*Temukan minimal temuan berikut saat review sejawat:*

- 2 masalah kebenaran (null, state, error path)
- 1 pelanggaran arsitektur (import lintas layer)
- 1 masalah test (kasus sukses/gagal kurang)
- 1 masalah keterbacaan (nama/fungsi besar)
- 1 saran perbaikan yang konkret + alasan

> Akhiri dengan keputusan: **Approve / Request changes.**

---

<!-- _class: fit -->

# Komentar Review yang Baik

*Keras pada kode, baik pada manusia.*

```text
kurang: "kode ini jelek, perbaiki"
baik: "Fungsi ini 60 baris + 3 tanggung jawab.
  Usul: pecah validasi ke core/validate.dart.
  Alasan: bisa diunit-test tanpa widget."
```

Pola: **observasi -> dampak -> usulan konkret.**

---

<!-- _class: fit -->

# Static Analysis & Linter

*Biarkan mesin menangkap yang mekanis.*

```text
flutter analyze        # wajib bersih, nol warning
dart format --set-exit-if-changed .
flutter test           # hijau sebelum PR
```

Aktifkan di CI agar PR kotor ditolak otomatis.

> Reviewer manusia fokus ke desain, bukan titik koma.

---

<!-- _class: fit -->

# Baca Output Analyze

*Jangan hafal aturan, pahami maksudnya.*

```text
warning: unused_import -> hapus import mati
info: prefer_const -> pakai const agar rebuild murah
error: must_be_immutable -> widget tak boleh mutable
```

Perbaiki dari error dulu, lalu warning, lalu info.

---

# Bagian 3
## Mid Project Review & Praktik

---

<!-- _class: fit -->

# Definisi Selesai Tengah Semester

*Project layak lanjut ke paruh kedua bila:*

- Struktur feature-first + dependency rule terbukti
- `flutter analyze` bersih + test hijau
- PR kecil + commit jelas + issue tertutup
- README + screenshot + cara jalan lengkap
- Review sejawat sudah ditindaklanjuti

> Bukan "fitur banyak", tapi "fondasi bisa dipercaya".

---

<!-- _class: fit -->

# Praktikum: PR Sederhana

*Simulasi alur tim dalam 20 menit:*

```text
1. Buat issue: "Validasi judul kosong + test"
2. Branch: git checkout -b feat/notes-validation
3. Commit kecil + push + buka PR ke develop
4. Isi template: masalah, solusi, cara uji
5. Minta 1 reviewer, perbaiki, merge
6. Pastikan issue tertutup otomatis
```

Satu PR per pasangan, bukan per kelas.

---

<!-- _class: fit -->

# Simulasi Peer Review

*Peran bergantian, timebox ketat:*

- **Author (7 mnt):** demo perubahan + risiko + minta fokus.
- **Reviewer (7 mnt):** pakai checklist 15 menit versi ringkas.
- **Diskusi (6 mnt):** sepakati perbaikan + siapa mengerjakan.

> Target: tiap PR mendapat minimal 3 komentar bermakna.

---

<!-- _class: fit -->

# AI Lab

*Gunakan AI sebagai Co-Reviewer*

Contoh prompt:

```text
Review PR ini sebagai senior Flutter dev.
Fokus: kebenaran, arsitektur, test.
Abaikan style. Beri 5 temuan teratas
+ level (blocker/saran) + usulan kode.
```

### Jangan terima mentah output AI.

---

# AI Verification Challenge

Setelah AI memberi review:

1. **Baca** - pahami tiap temuan, jangan copy-paste.
2. **Verifikasi** - cek silang dengan grep + test nyata.
3. **Tolak/terima** - tolak saran salah + alasan tertulis.
4. **Uji** - analyze + test tetap hijau.
5. **Perbaiki** - terapkan yang valid, catat manual.
6. **Jelaskan** - demo tanpa membaca catatan AI.

### Uji minimal

- 2 temuan AI terbukti benar dengan test
- 1 temuan AI ditolak dengan alasan
- `flutter analyze` + `flutter test`

---

<!-- _class: fit -->

# Anti-Pattern Review

*Hindari:*

### 1. LGTM buta tanpa baca diff

```text
approve 800 baris dalam 1 menit = tak review
```

### 2. Nitpick tanpa prioritas

```text
debat spasi, logika bocor lolos
```

### 3. Review tanpa dijalankan

```text
tak pernah pull + test, hanya baca sekilas
```

---

# Aktivitas Kelompok

## Code Review 15 Menit

Tukar PR antar pasangan, cari minimal:

- 2 masalah kebenaran (null, state, error path)
- 1 pelanggaran arsitektur atau DI bocor
- 1 masalah test yang kurang
- 1 komentar teladan (observasi + usulan)
- 1 keputusan AI yang ditolak/diterima + alasan

Kemudian jawab:

> **Apakah project ini layak lanjut ke paruh kedua? Mengapa?**

---

<!-- _class: fit -->

# Quiz Cepat

### 1. Branch untuk kerja harian?

A. Commit langsung ke `main`
B. `feature/*` dari `develop`
C. Commit ke branch teman
D. Tanpa branch

### 2. PR yang baik berukuran?

A. 800 baris, banyak fitur sekaligus
B. Kecil, satu fitur, ada cara uji
C. Tanpa deskripsi agar cepat
D. Tanpa test agar ringan

---

<!-- _class: fit -->

# Jawaban Quiz

### 1 -> B

```text
fitur dari develop, kembali via PR
```

### 2 -> B

```text
kecil + cara uji -> review cepat
```

### 3 -> Cek `flutter analyze` dulu

```text
mesin untuk style, manusia untuk desain
```

---

<!-- _class: fit -->

# Deliverables

## Repository

```text
08-week-8-mid-project-review/
├── docs/
│   ├── review-checklist.md
│   └── ai-review-vs-keputusan.md
├── README.md
└── screenshots/
```

README minimal berisi:

- status paruh semester + peta issue/PR
- hasil `flutter analyze` + `flutter test`
- screenshot PR + komentar review
- prompt AI + tabel usulan-vs-keputusan

---

<!-- _class: fit -->

# Rubrik Week 8

| Komponen | Bobot |
|---|---:|
| Git Flow + PR + Commit | 25% |
| Code Review Sejawat | 25% |
| Static Analysis + Test Hijau | 15% |
| Mid Review + Tindak Lanjut | 15% |
| Responsible AI Usage | 10% |
| Presentasi & Refleksi | 10% |
| **Total** | **100%** |

---

# Checklist Sebelum Submit

### Kolaborasi

- [ ] Branch dari develop, PR kecil satu fitur
- [ ] Commit jelas, issue tertutup via PR
- [ ] Direview 1 teman + ditindaklanjuti

### Bukti

- [ ] `flutter analyze` bersih + test hijau
- [ ] Screenshot PR + komentar review
- [ ] Status paruh semester di README

### AI

- [ ] Prompt + tabel keputusan dicatat
- [ ] Mampu jelaskan temuan tanpa AI

---

# Exit Ticket

Jawab sebelum kelas berakhir:

### 1. Apa beda LGTM buta dengan approve bertanggung jawab?

### 2. Kapan komentar "request changes" wajib, kapan cukup saran?

### 3. Satu temuan AI yang Anda tolak dan alasannya?

### 4. Satu hal yang Anda perbaiki setelah review sejawat?

---

# Key Takeaways

## Kolaborasi

> Branch kecil, PR kecil, review cepat.

## Review

> Keras pada kode, baik pada manusia.

## Otomatisasi

> Mesin untuk style, manusia untuk desain.

## AI

> AI mengusulkan review.
> **Developer memutuskan.**

---

# Next Week

## Week 9: AI-Assisted Development / Vibe Coding

```text
Project terstruktur (lolos review)
   |
   v
Prompt + konteks + verifikasi
   |
   v
AI generate, developer uji + refactor
```

### Target

> Menggunakan AI cepat tanpa kehilangan kendali mutu.

---

# Build -> Verify -> Explain

## Prinsip Pembelajaran Mobile

```text
         AI
           |
           v
        Generate
           |
           v
        Developer
           |
           v
          Verify
           |
           v
          Test
           |
           v
        Refactor
           |
           v
         Explain
```

## Jangan hanya bisa menulis kode.

### Jadilah developer yang berani direview dan bisa mereview.

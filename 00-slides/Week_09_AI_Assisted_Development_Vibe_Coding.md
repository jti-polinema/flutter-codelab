---
marp: true
theme: default
paginate: true
size: 16:9
---

<style>
section {
  font-size: 20px;
  padding: 70px 70px 64px 70px;
  color: #1b1b1b;
  background:
    linear-gradient(90deg, #f7941d 0%, #fdb913 100%) 0 0 / 100% 10px no-repeat,
    linear-gradient(155deg, #ffffff 55%, #eef0f2 100%);
  background-repeat: no-repeat;
}
section h1 { font-size: 34px; color: #111; margin: 0.35em 0; border-bottom: none; }
section h2 { font-size: 28px; color: #333; margin: 0.35em 0; }
section h3 { font-size: 22px; color: #1b2a4a; margin: 0.35em 0; }
section p, section ul, section ol { margin: 0.3em 0; }
section table { font-size: 18px; }
section pre, section code { font-size: 16px; }
section blockquote { font-size: 19px; }
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
section::after {
  content: "D4 Teknik Informatika  Â·  " counter(page);
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
section.lead { justify-content: center; text-align: left; padding-left: 80px; }
section.lead h1 { font-size: 46px; font-weight: 800; color: #111; border: none; }
section.lead h2 { font-size: 32px; font-weight: 600; color: #666; margin-top: 0; }
section.lead h3 { font-size: 19px; font-weight: 500; color: #222; margin-top: 18px; }
section.lead p { color: #333; }
section.fit { display: flex; flex-direction: column; justify-content: center; }
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

# Week 9
## AI-Assisted Development / Vibe Coding

**Pemrograman Mobile**

Flutter | Prompt | Context | Verify | Refactor | Test

> Dari nge-prompt cepat menuju kode yang dipertanggungjawabkan

---

# Learning Outcomes

Setelah pertemuan ini, mahasiswa mampu:

1. Menjelaskan workflow AI-assisted development.
2. Menulis prompt dan konteks yang efektif.
3. Mengenali halusinasi dan batas AI.
4. Debug dan refactor kode hasil AI.
5. Menguji kode hasil AI.
6. Mendokumentasikan pemakaian AI.

---

# Mengapa Vibe Coding Berbahaya?

Tanpa disiplin, AI terlihat cepat tapi rapuh:

```text
Fitur jadi 5 menit, error 5 hari
Dio dipanggil dari widget, arsitektur bocor
Method halusinasi: tak ada di package
```

Butuh alur insinyur:

```text
prompt jelas -> generate -> verifikasi
-> test -> refactor -> jelaskan
```

**Tujuan:** cepat tanpa kehilangan kendali mutu.

---

# Mini Poll

### Mana prompt yang paling baik?

**A.** `buatkan fitur search yang bagus`

**B.** `Search notes: Riverpod + use case, empty state + test`

**C.** `tolong coding cepat ya`

**D.** `copy dari internet saja`

---

# Diskusi Mini Poll

> Apa beda "kode hasil AI" dengan "kode yang Anda pahami"?

Jawaban singkat: pahami tiap baris, cek API nyata, buktikan lewat test.

---

# Bagian 1
## Workflow AI yang Bertanggung Jawab

---

<!-- _class: fit -->

# Yang AI Kuasai vs Tidak

*Kenali batas agar tahu kapan verifikasi ekstra.*

- **Kuat:** boilerplate, pola umum, draft test, docs.
- **Lemah:** API baru, versi package, logika bisnis khusus.
- **Berbahaya:** token auth, query SQL, aturan keamanan.
- **Selalu cek:** nama method, parameter, import, null-safety.

> AI menebak dari pola lama. Proyek Anda memakai versi kini.

---

<!-- _class: fit -->

# Halusinasi Kode Flutter

*Tiga pola yang sering muncul:*

```text
1. Method fiktif: controller.searchFuzzy()
   -> cek pub.dev / autocomplete IDE
2. Package kadaluarsa: API v1 padahal v3
   -> cek pubspec + CHANGELOG
3. Logika yakin tapi salah: filter case-sensitive
   -> buktikan dengan 1 test kecil
```

> Klaim AI = hipotesis sampai ada bukti test.

---

# Bagian 2
## Prompt dan Context Engineering

---

<!-- _class: fit -->

# Formula Prompt Efektif

*Satu prompt baik = 5 slot terisi singkat.*

```text
Peran:   senior Flutter dev
Konteks: feature-first, Riverpod, SQLite+Dio
Tugas:   1 fitur kecil + batasan jelas
Batas:   jangan ubah arsitektur, maks 80 baris
Output:  kode + 3 test case + cara uji
```

> Prompt kabur menghasilkan kode kabur.

---

<!-- _class: fit -->

# Contoh Buruk vs Baik

```text
buruk: "buatkan search notes yang keren"
baik:  "search judul + empty state,
  domain tanpa SQLite, 1 widget test"
```

> Prompt baik: kecil, terarah, mudah diuji.

---

<!-- _class: fit -->

# Context Engineering

*AI hanya sepintar konteks yang Anda beri.*

- Sertakan: struktur folder, 1 file kontrak, versi package.
- Jangan sertakan: token, secret, 500 file repo.
- Tempel potongan relevan, bukan screenshot kabur.
- Sebut yang TIDAK boleh diubah.

> Konteks bagus = separuh verifikasi selesai.

---

# Bagian 3
## Verifikasi, Debug, Refactor

---

<!-- _class: fit -->

# Checklist Verifikasi

*Jalankan tiap menerima kode AI:*

- [ ] Baca tiap baris, tandai yang tak dipahami.
- [ ] Cek import: domain bebas Flutter/Dio/SQLite?
- [ ] Cek API: method benar ada? (grep + autocomplete).
- [ ] `flutter analyze` bersih, nol warning.
- [ ] Minimal 1 test sukses + 1 gagal.
- [ ] Bisa dijelaskan tanpa buka AI?

---

<!-- _class: fit -->

# Debug Kode Hasil AI

*Reproduksi dulu, salahkan belakangan:*

```text
1. Reproduksi: langkah + input pemicu bug
2. Isolasi: pindah logika ke use case murni
3. Test merah: buat test gagal pembukti bug
4. Perbaiki kecil, jalankan test + analyze
5. Catat akar masalah di docs/ai-log.md
```

> Jangan prompt ulang "fix dong" tanpa bukti error.

---

<!-- _class: fit -->

# Refactor ke Clean Architecture

*Kode AI sering menumpuk logika di widget.*

```text
widget -> notifier -> use case -> repository
```

- Pecah fungsi > 40 baris.
- `domain/` tanpa Flutter/Dio/SQLite.

---

# Bagian 4
## Dokumentasi dan Praktik

---

<!-- _class: fit -->

# Format Dokumentasi AI

*Satu fitur = satu catatan di `docs/ai-log.md`:*

```text
Fitur: search judul notes
Prompt: 3 baris + konteks ditempel
Uji: analyze bersih, 2 test hijau
Putusan: terima + pindah ke use case
```

> Tanpa catatan, pemakaian AI tak bisa dinilai.

---

<!-- _class: fit -->

# Praktikum: Search Notes

*Satu fitur kecil dalam 30 menit:*

```text
1. Tulis prompt 5 slot singkat
2. Generate search + empty state
3. Verifikasi: grep API + cek import
4. Tambah 1 widget + 1 unit test
5. Refactor filter ke use case
```

Target: analyze bersih + test hijau.

---

<!-- _class: fit -->

# AI Lab Challenge

*Minta AI, lalu kritisi:*

```text
"Review kode search sebagai senior dev.
Fokus: benar, arsitektur, test.
3 temuan + blocker/saran."
```

Buktikan 1 temuan benar. Tolak 1 beralasan.

---

<!-- _class: fit -->

# Error Umum dan Solusi

| Gejala | Penyebab | Solusi |
|---|---|---|
| Method tak ada | Halusinasi API | Cek pub.dev |
| Import bocor | Konteks kurang | Tegaskan batas |
| Test merah semua | Prompt besar | Minta 1 fitur + test |
| Tak bisa jelaskan | Terima mentah | Baca + refactor |

---

<!-- _class: fit -->

# Quiz Cepat (1/2)

### 1. Pertama saat kode AI error?

A. Prompt ulang `fix dong`
B. Reproduksi + isolasi + test merah
C. Copy ke file lain
D. Ganti AI lain

---

<!-- _class: fit -->

# Quiz Cepat (2/2)

### 2. Prompt baik itu?

A. Panjang dan kabur
B. 1 tugas kecil + konteks + batas
C. Tanpa batasan
D. Minta 5 fitur sekaligus

---

<!-- _class: fit -->

# Jawaban Quiz

### 1 -> B

```text
reproduksi -> test merah -> perbaiki
```

### 2 -> B

```text
kecil + konteks + batas -> mudah diuji
```

---

<!-- _class: fit -->

# Deliverables

## Repository

```text
09-week-9-ai-assisted-development/
|-- docs/
|   |-- ai-log.md
|-- README.md
|-- screenshots/
```

README minimal berisi:

- fitur AI + prompt inti yang dipakai
- hasil `flutter analyze` + `flutter test`
- screenshot fitur + test hijau
- tabel terima/tolak saran AI + alasan

---

<!-- _class: fit -->

# Rubrik Week 9

| Komponen | Bobot |
|---|---:|
| Prompt + Context Engineering | 25% |
| Verifikasi + Debug Bukti | 25% |
| Refactor + Test Hijau | 20% |
| Dokumentasi AI-Log | 15% |
| Presentasi dan Refleksi | 15% |
| **Total** | **100%** |

---

# Checklist Sebelum Submit

### Prompt

- [ ] 1 fitur kecil, konteks ditempel
- [ ] Batasan arsitektur ditulis jelas
- [ ] Output diminta + cara uji

### Bukti

- [ ] `flutter analyze` bersih + test hijau
- [ ] 1 temuan AI dibuktikan benar
- [ ] 1 temuan AI ditolak beralasan

### AI

- [ ] `docs/ai-log.md` lengkap
- [ ] Mampu jelaskan tanpa buka AI

---

# Exit Ticket

Jawab sebelum kelas berakhir:

### 1. Satu halusinasi AI yang Anda temukan?

### 2. Prompt mana yang paling membantu, mengapa?

### 3. Satu refactor yang Anda lakukan pada kode AI?

### 4. Keputusan terima/tolak AI yang paling sulit?

---

# Key Takeaways

## Kecepatan

> AI mempercepat draf, bukan keputusan.

## Mutu

> Verifikasi + test = kendali mutu Anda.

## Arsitektur

> Rapikan kode AI ke layer yang benar.

## AI

> AI mengusulkan kode.
> **Developer memutuskan.**

---

# Next Week

## Week 10: AI Feature Integration

```text
Fitur AI stabil (teruji + terdokumentasi)
   |
   v
Integrasi LLM / OCR / STT ke aplikasi
   |
   v
UX + privasi + fallback + biaya
```

### Target

> Menambah fitur AI nyata tanpa bocor data pengguna.

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

## Jangan hanya bisa nge-prompt.

### Jadilah developer yang berani menjelaskan kode AI.

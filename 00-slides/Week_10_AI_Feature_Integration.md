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
section.lead {
  justify-content: center;
  text-align: left;
  padding-left: 80px;
}
section.lead h1 {
  font-size: 46px;
  font-weight: 800;
  color: #111;
  border: none;
}
section.lead h2 {
  font-size: 32px;
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

# Week 10
## Integrasi Fitur AI

**Pemrograman Mobile**

Flutter | LLM API | Chat | OCR | STT | Image Understanding

> Dari uji coba AI menuju fitur siap produksi yang aman dan nyaman

---

# Tujuan Pembelajaran

Setelah pertemuan ini, mahasiswa mampu:

1. Menerangkan konsep integrasi model AI ke aplikasi mobile.
2. Merancang antarmuka chat dengan respons streaming.
3. Mengintegrasikan OCR, Speech-to-Text, dan pengertian gambar.
4. Menerapkan prompt design yang aman dan efektif.
5. Menangani privasi, biaya, penanganan kesalahan, dan desain fallback.

---

# Mengapa Integrasi Fitur AI?

AI kini bukan lagi sekadar uji coba, melainkan menjadi bagian inti aplikasi modern:

- **Chat asisten**, tanya jawab dalam waktu nyata
- **OCR**, baca teks dari gambar atau dokumen
- **Speech-to-Text**, ubah input suara menjadi teks
- **Image Understanding**, deskripsi dan analisis gambar
- **Rekomendasi**, saran personalisasi berdasarkan data pengguna

Fitur AI harus aman, telah diuji, dan mempunyai cadangan saat layanan tidak tersedia.

---

# Arsitektur Integrasi AI (Mobile)

```text
+----------------+      HTTPS/wss      +----------------+
|   Flutter UI   | <-----------------> |  Layer API AI  |
|  (Chat, OCR,   |      (Dio/http)     |  (LLM, OCR,    |
|   STT, Gambar) |                     |   STT, Vision) |
+----------------+                     +----------------+
        |                                      |
        | Kunci API (penyimpanan aman)         | Respons
        v                                      v
+----------------+      Opsional      +----------------+
|  Cache Lokal   | <-----------------> |   Backend    |
|  (Hive/SQLite) |     (batch)        |  (autentikasi, |
|                |                     |  log)         |
+----------------+                     +----------------+
```

- Kunci API tidak boleh disimpan langsung di dalam kode sumber
- Gunakan `flutter_secure_storage` untuk menyimpan token dan kunci
- Pertimbangkan biaya per permintaan dan batas laju (rate limit)
---

# Prompt Design untuk Mobile

| Komponen | Contoh | Tujuan |
|----------|--------|--------|
| System prompt | "Kamu asisten kampus yang ramah, jawab dalam Bahasa Indonesia, maksimal dua kalimat" | Menetapkan persona dan batas |
| User prompt | "Jadwal kampus hari Senin?" | Masukan pengguna |
| Konteks | Data jadwal, profil pengguna, lokasi | Membuat respons relevan |
| Batasan | Jangan berikan data pribadi orang lain | Privasi dan keamanan |

Tips: buat prompt pendek dan spesifik, terjemahkan hasil jika perlu, dan validasi hasil sebelum ditampilkan kepada pengguna.

---

# Antarmuka Chat dan UX

```text
[Pengguna: Halo, ada kegiatan kampus besok?]
        |
        v
[AI mengetik...]  <-- respons streaming (animasi gelembung)
        |
        v
[AI: Besok ada mata kuliah Pemrograman Mobile pukul 08.00]
```

Pertimbangan UX:
- Respons streaming dengan `StreamBuilder`
- Indikator "mengetik" saat menunggu jawaban
- Gulir otomatis ke pesan terbaru
- Tombol ulang (regenerate) dan salin
- Kondisi kosong saat belum ada percakapan

---

# OCR, Speech-to-Text, dan Pengertian Gambar

**OCR (Optical Character Recognition):**
- Ambil gambar dari kamera atau galeri
- Kirim ke AI API untuk ekstraksi teks
- Digunakan untuk: KTM, struk, dokumen, papan pengumuman

**Speech-to-Text:**
- Rekam suara pengguna
- Ubah ke teks melalui AI API
- Digunakan untuk: input cepat, aksesibilitas

**Pengertian Gambar:**
- Unggah gambar dan minta deskripsi
- Contoh: "Apa yang ada di gambar ini?"
- Respons: "Gambar menunjukkan mahasiswa di perpustakaan sambil memegang buku."
- Digunakan untuk: deskripsi produk, identifikasi objek, aksesibilitas bagi pengguna dengan disabilitas visual

```dart
final hasil = await aiService.ekstrakTeks(fileGambar);
final teks = await aiService.ucapkanSuara(fileAudio);
```

---

# Privasi, Biaya, Penanganan Kesalahan, dan Fallback

| Risiko | Penanggulangan |
|--------|----------------|
| Bocor data pengguna | Jangan kirim data sensitif ke AI API |
| Biaya melonjak | Batasi permintaan per pengguna per hari, gunakan cache lokal |
| API error / tidak aktif | Tampilkan pesan ramah, opsi coba lagi, mode offline |
| Respons tidak relevan | Terapkan validasi dan cadangan ke data lokal |

Contoh fallback:
```dart
try {
  final respons = await aiService.tanya(pertanyaan);
} catch (e) {
  showDialog(
    konteks: konteks,
    pembuat: (_) => AlertDialog(
      judul: const Text('Layanan AI Sibuk'),
      isi: const Text('Coba lagi nanti atau gunakan mode offline.'),
    ),
  );
}
```
---

<!-- _class: fit -->

# Quiz Cepat (1/2)

### 1. Apa yang harus dihindari saat mengirim data ke AI API?

A. Data umum yang tidak sensitif  
B. NIK, nomor telepon, alamat pengguna  
C. Nama mata kuliah publik  
D. Deskripsi tugas kampus  

---

<!-- _class: fit -->

# Quiz Cepat (2/2)

### 2. Fitur apa yang cocok untuk cadangan offline?

A. Streaming chat waktu nyata  
B. OCR dari gambar  
C. Menampilkan jadwal dari cache lokal  
D. Speech-to-Text  

---

<!-- _class: fit -->

# Jawaban Quiz

### 1 -> B

```text
Jangan pernah mengirim data identitas pengguna ke AI API
Gunakan data anonim atau ID internal saja
```

### 2 -> C

```text
Jadwal dari cache lokal dapat diakses tanpa internet
Fitur AI memerlukan koneksi, jadi butuh cadangan
```

---

<!-- _class: fit -->

# Hasil yang Diharapkan

## Repositori

```text
10-week-10-ai-feature-integration/
|-- lib/
|   |-- ai/
|   |   |-- ai_service.dart
|   |-- fitur/
|   |   |-- layar_chat.dart
|-- docs/
|   |-- ai-log.md
|-- uji/
|-- tangkapan_layar/
|-- README.md
```

README minimal berisi:

- Fitur AI + prompt yang digunakan
- `flutter analyze` + `flutter uji`
- Tangkapan layar fitur chat/OCR/STT
- Tabel risiko + penanggulangan privasi
- Bukti uji coba pengguna (masukan pengguna)

---

<!-- _class: fit -->

# Penilaian Week 10

| Komponen | Bobot |
|----------|-------|
| Integrasi Fitur AI (Chat/OCR/STT/Gambar) | 25% |
| Desain Prompt dan Penanganan Respons | 20% |
| Privasi, Biaya, dan Penanganan Kesalahan | 20% |
| UX dan Fitur Cadangan | 15% |
| Pengujian dan Dokumentasi | 10% |
| Presentasi dan Refleksi | 10% |
| **Total** | **100%** |

---

<!-- _class: fit -->

# Daftar Periksa Sebelum Kumpulkan

### Integrasi AI

- [ ] Fitur AI terintegrasi ke dalam aplikasi (chat atau OCR atau STT)
- [ ] Prompt sistem dan prompt pengguna ditetapkan dengan jelas
- [ ] Respons streaming atau indikator pemuatan diterapkan

### Privasi dan Keamanan

- [ ] Tidak ada data sensitif yang dikirim ke AI API
- [ ] Kunci API disimpan di `flutter_secure_storage`
- [ ] Penanganan kesalahan dan cadangan tersedia

### Pengujian

- [ ] `flutter analyze` bersih
- [ ] Minimal satu uji widget untuk fitur AI
- [ ] Tangkapan layar fitur AI dalam aplikasi

### Dokumentasi

- [ ] `docs/ai-log.md` lengkap (prompt, respons, keputusan)
- [ ] README menjelaskan fitur, cara menguji, dan hasil

---

<!-- _class: fit -->

# Kartu Keluar

Jawab sebelum kelas berakhir:

### 1. Fitur AI apa yang Anda integrasikan dan mengapa?

### 2. Bagaimana Anda melindungi privasi pengguna saat menggunakan AI API?

### 3. Cadangan apa yang Anda sediakan jika layanan AI mengalami kesalahan?

### 4. Satu hal yang Anda pelajari tentang UX fitur AI di seluler?

---

<!-- _class: fit -->

# Poin Utama

## Integrasi

> AI adalah fitur, bukan pengganti arsitektur.

## Privasi

> Jangan kirim data sensitif ke AI API.

## UX

> Streaming + status pemuatan = pengalaman yang lebih baik.

## Cadangan

> Selalu sediakan jalan keluar saat AI tidak tersedia.

## AI

> AI mengusulkan respons.
> **Pengembang memutuskan.**

---

# Minggu Depan

## Minggu 11: Optimasi Performa

```text
Fitur AI terintegrasi
       |
       v
Pemrosesan rebuild widget
       |
       v
Penyimpanan gambar + pemuatan malas
       |
       v
Aplikasi lebih cepat dan efisien
```

### Sasaran

> Optimalkan performa aplikasi setelah fitur AI ditambahkan.
---

# Bangun -> Verifikasi -> Jelaskan

## Prinsip Pembelajaran Mobile

1. AI mengusulkan jawaban
2. Pengembang memverifikasi
3. Uji coba untuk konfirmasi
4. Perbaiki jika belum akurat
5. Jelaskan alasan keputusan

## Jangan hanya bisa memakai AI.

### Jadilah pengembang yang mampu menjelaskan fitur AI yang dibangun.

- Terapkan cache lokal untuk mengurangi panggilan berulang ke AI API
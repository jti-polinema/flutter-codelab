---
marp: true
theme: default
paginate: true
size: 16:9
---

<style>
section {
  font-size: 19px;
  padding: 66px 70px 62px 70px;
  color: #1b1b1b;
  background:
    linear-gradient(90deg, #f7941d 0%, #fdb913 100%) 0 0 / 100% 10px no-repeat,
    linear-gradient(155deg, #ffffff 55%, #eef0f2 100%);
  background-repeat: no-repeat;
}
section h1 { font-size: 32px; color: #111; margin: 0.3em 0; border-bottom: none; }
section h2 { font-size: 25px; color: #333; margin: 0.3em 0; }
section h3 { font-size: 20px; color: #1b2a4a; margin: 0.25em 0; }
section p, section ul, section ol { margin: 0.25em 0; }
section ul, section ol { padding-left: 1.25em; }
section li { margin: 0.16em 0; }
section table { font-size: 16px; }
section pre, section code { font-size: 14px; }
section pre { margin: 0.25em 0; line-height: 1.2; }
section blockquote { font-size: 17px; }
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
section.lead { justify-content: center; text-align: left; padding-left: 80px; }
section.lead h1 { font-size: 44px; font-weight: 800; color: #111; }
section.lead h2 { font-size: 30px; font-weight: 600; color: #666; margin-top: 0; }
section.lead h3 { font-size: 18px; font-weight: 500; color: #222; margin-top: 16px; }
section.lead p { color: #333; }
section.fit { display: flex; flex-direction: column; justify-content: center; }
section.fit h1 { font-size: 28px; margin: 0.2em 0; }
section.fit h2 { font-size: 23px; margin: 0.2em 0; }
section.fit h3 { font-size: 19px; margin: 0.2em 0; }
section.fit p, section.fit ul, section.fit ol { margin: 0.2em 0; }
section.fit li { margin: 0.12em 0; }
section.fit pre { margin: 0.2em 0; line-height: 1.15; }
section.fit pre, section.fit code { font-size: 12px; }
section.fit blockquote { font-size: 16px; margin: 0.2em 0; }
section.fit table { font-size: 15px; }
</style>

<!-- _class: lead -->

# Minggu 13
## CI/CD dan Otomatisasi

**Pemrograman Mobile**

GitHub Actions | Pengujian Otomatis | Build APK/AAB | Rilis Aman

> Ubah setiap perubahan kode menjadi proses yang konsisten, teruji, dan dapat ditelusuri.

---

<!-- _class: fit -->

# Tujuan Pembelajaran

Setelah sesi ini, mahasiswa diharapkan mampu:

1. Menjelaskan peran continuous integration dan continuous delivery/deployment.
2. Membuat workflow GitHub Actions untuk analisis, pengujian, dan build Flutter.
3. Menghasilkan APK/AAB serta menyimpan hasil build sebagai artefak.
4. Menerapkan versioning dan membedakan konfigurasi publik dari rahasia.
5. Meninjau trigger dan permission workflow sebelum mengaktifkannya.

---

<!-- _class: fit -->

# Dari Commit ke Artefak

1. **Perubahan** — commit atau pull request dibuat.
2. **Pemeriksaan** — dependency diambil, kode dianalisis, dan test dijalankan.
3. **Build** — aplikasi dikemas menjadi APK atau AAB.
4. **Distribusi** — artefak disimpan atau diteruskan ke proses rilis.

- CI menjalankan pemeriksaan secara otomatis pada perubahan kode.
- CD mengotomatiskan penyiapan atau pengiriman rilis.
- Build yang hijau membantu; build itu sendiri bukan bukti aplikasi bebas bug.

---

<!-- _class: fit -->

# Kapan Workflow Berjalan?

| Trigger | Contoh kegunaan |
|---|---|
| `pull_request` | Periksa perubahan sebelum digabungkan |
| `push` ke `main` | Uji keadaan branch utama |
| Tag versi, mis. `v1.2.0` | Siapkan artefak rilis |
| Manual (`workflow_dispatch`) | Jalankan ulang atau validasi terkontrol |

- Mulai dengan trigger sesempit kebutuhan.
- Pull request dari fork tidak menerima secrets repository.
- Hindari memberikan permission tulis jika job hanya membaca kode.

---

<!-- _class: fit -->

# Bagian Workflow GitHub Actions

- **Workflow**: file YAML di `.github/workflows/`.
- **Event**: menentukan perubahan yang memulai workflow.
- **Job**: unit pekerjaan pada runner, misalnya Linux.
- **Step**: action yang digunakan atau perintah shell.
- **Artifact**: file build yang dapat diunduh setelah job selesai.

> Setiap workflow harus mudah ditinjau: trigger, permission, langkah, dan keluaran terlihat jelas.

---

<!-- _class: fit -->

# Pipeline Flutter: Pemeriksaan

```yaml
steps:
  - uses: actions/checkout@v4
  - uses: subosito/flutter-action@v2
    with:
      channel: stable
  - run: flutter pub get
  - run: dart format --output=none --set-exit-if-changed .
  - run: flutter analyze --fatal-infos
  - run: flutter test --coverage
```

- Jalankan pemeriksaan yang sama secara lokal sebelum push.
- Test yang gagal harus menghentikan job; jangan mengabaikan exit code.

---

<!-- _class: fit -->

# Pipeline Flutter: Build dan Artefak

```yaml
  - run: flutter build apk --release
  - run: flutter build appbundle --release
  - uses: actions/upload-artifact@v4
    with:
      name: android-builds
      path: |
        build/app/outputs/flutter-apk/app-release.apk
        build/app/outputs/bundle/release/app-release.aab
      if-no-files-found: error
      retention-days: 7
```

- APK mudah dipasang untuk uji internal; AAB digunakan pada alur Play Store.
- Build sukses belum tentu siap rilis: validasi signing, konfigurasi, dan perangkat sasaran.

---

<!-- _class: fit -->

# Versi dan Rilis yang Dapat Dilacak

Contoh versi di `pubspec.yaml`:

```yaml
version: 1.2.0+15
```

- `1.2.0` adalah versi semantik: major, minor, patch.
- `15` adalah build number; naikkan untuk setiap unggahan baru.
- Kaitkan rilis dengan commit/tag, misalnya `v1.2.0`.
- Simpan artefak beserta catatan commit dan hasil test.

---

<!-- _class: fit -->

# Konfigurasi Bukan Selalu Rahasia

- URL layanan atau nama environment biasanya konfigurasi, bukan kredensial.
- Token privat, password, dan keystore harus disimpan sebagai GitHub Secrets.
- Jangan menulis secret di kode, log, commit, atau file konfigurasi yang dilacak.
- `--dart-define` memasukkan nilai ke aplikasi; jangan gunakan untuk menyembunyikan secret.
- Batasi permission dan masa simpan artefak sesuai kebutuhan.

> Kunci yang dikirim ke aplikasi klien dapat diekstrak. Secret server harus tetap di server.

---

<!-- _class: fit -->

# AI sebagai Asisten Workflow

AI dapat membantu membuat draft YAML atau menjelaskan kegagalan, tetapi:

- periksa arti setiap trigger, permission, action, dan perintah shell;
- pastikan test gagal benar-benar membuat job gagal;
- pastikan data sensitif tidak tercetak atau dikirim ke layanan pihak ketiga;
- jalankan workflow dan periksa log serta artefak secara mandiri.

**Tantangan:** minta AI meninjau workflow, lalu bandingkan temuannya dengan file dan log nyata.

---

<!-- _class: fit -->

# Praktikum dan Refleksi

1. Tambahkan workflow CI ke project Flutter yang telah memiliki test.
2. Jalankan pada pull request dan periksa hasil `analyze`, test, serta build.
3. Unduh artefak APK/AAB dan catat commit yang menghasilkannya.
4. Buat catatan versi berikutnya dan rencanakan trigger rilis yang aman.

- Pemeriksaan mana yang paling cepat menemukan masalah?
- Siapa yang dapat mengakses artefak dan secrets workflow?
- Apakah build Anda siap rilis, atau baru berhasil dikompilasi?

---

<!-- _class: fit -->

# Referensi

- GitHub Actions: `docs.github.com/actions`
- Building and testing Flutter: `docs.flutter.dev/deployment/cd`
- Flutter Android deployment: `docs.flutter.dev/deployment/android`
- GitHub encrypted secrets: `docs.github.com/actions/security-guides/using-secrets-in-github-actions`
- Semantic Versioning: `semver.org`

**Ingat:** pipeline yang baik mengulang proses dengan konsisten dan membuat hasilnya dapat ditinjau.

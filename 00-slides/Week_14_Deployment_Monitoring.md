---
marp: true
theme: default
paginate: true
size: 16:9
---

<style>
section {
  font-size: 19px;
  padding: 66px 70px 82px 70px;
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

# Minggu 14
## Deployment & Monitoring

**Pemrograman Mobile**

Google Play Internal Testing | Firebase Crashlytics | Analytics

> Rilis beta yang dapat diuji, diamati, dan diperbaiki.

---

<!-- _class: fit -->

# Tujuan Pembelajaran

Setelah sesi ini, mahasiswa mampu:

1. Menjelaskan alur kandidat rilis hingga pengujian beta.
2. Menyiapkan versi aplikasi dan AAB untuk pengujian internal.
3. Mengirim crash dan event analitik secara bertanggung jawab.
4. Membaca sinyal rilis untuk menentukan tindak lanjut.
5. Menggunakan AI untuk membantu analisis tanpa membocorkan data.

---

<!-- _class: fit -->

# Siklus Rilis Mobile

```text
Perubahan → CI lulus → kandidat rilis (AAB)
          → track internal → uji tester
          → pantau crash & performa
          → perbaiki / lanjutkan rilis
```

- Minggu 13 menghasilkan build; minggu ini mengirimnya ke penguji dan memantaunya.
- Beta tetap rilis terbatas, bukan bukti aplikasi siap untuk semua pengguna.
- Catat versi, build number, commit, dan hasil pemeriksaan.

---

<!-- _class: fit -->

# Release Track: Mulai dari Internal

| Tahap | Audiens | Tujuan |
|---|---|---|
| Internal testing | Tim kecil / penguji terdaftar | Cek instalasi dan alur utama |
| Closed testing | Kelompok terbatas | Umpan balik beta terstruktur |
| Production | Pengguna umum | Distribusi setelah tinjauan |

- Mulai dari internal track dan gunakan tester yang memang diundang.
- Ketersediaan fitur, syarat akun, dan proses peninjauan Play dapat berubah.
- Jangan mengunggah data atau akun produksi yang tidak diperlukan untuk pengujian.

---

<!-- _class: fit -->

# Identitas Build: Versi dan Commit

```yaml
# pubspec.yaml
version: 1.0.0+21
```

- `1.0.0` adalah versi yang terlihat pengguna; `21` adalah build number.
- Naikkan build number pada setiap unggahan baru.
- Kaitkan rilis dengan tag/commit, misalnya `v1.0.0-beta.1`.
- Catatan rilis harus menyebut perubahan dan batasan yang diketahui.

> Versi tanpa commit dan catatan uji sulit ditelusuri saat terjadi masalah.

---

<!-- _class: fit -->

# Bangun Kandidat Rilis

```bash
flutter pub get
dart format --output=none --set-exit-if-changed .
flutter analyze --fatal-infos
flutter test
flutter build appbundle --release
```

- Gunakan konfigurasi release dan signing yang benar untuk project.
- AAB biasanya dihasilkan di `build/app/outputs/bundle/release/`.
- Simpan artefak bersama commit dan hasil CI; jangan kirim ke kanal publik.
- Tinjau panduan signing Flutter dan Play Console untuk konfigurasi aktual.

---

<!-- _class: fit -->

# Gerbang Sebelum Mengunggah

- [ ] Tes dan analisis lulus pada commit kandidat.
- [ ] `version` dan build number sesuai; tidak pernah memakai nomor lama.
- [ ] Nama aplikasi, ikon, izin, dan konfigurasi environment ditinjau.
- [ ] Tidak ada kredensial, data uji sensitif, atau log debug berlebihan.
- [ ] Alur utama sudah diperiksa pada perangkat sasaran.
- [ ] Catatan rilis dan cara melaporkan masalah tersedia.

> Build release tetap perlu ditinjau manusia.

---

<!-- _class: fit -->

# Praktik: Internal Testing di Play Console

1. Buat/pilih aplikasi dan siapkan track **Internal testing**.
2. Tambahkan tester melalui daftar email atau grup yang diizinkan.
3. Unggah AAB kandidat, isi catatan rilis, lalu tinjau peringatan.
4. Bagikan tautan opt-in kepada tester yang terdaftar.
5. Minta tester menginstal dan memeriksa alur utama.
6. Catat versi/build, perangkat, langkah reproduksi, dan hasilnya.

Jika akses Play Console tidak tersedia, distribusikan build uji privat melalui Firebase App Distribution.

---

<!-- _class: fit -->

# Dari Umpan Balik ke Tindak Lanjut

| Laporan | Bukti yang dicatat | Tindakan awal |
|---|---|---|
| Crash saat membuka layar | Versi, perangkat, langkah, stack trace | Reproduksi dan prioritaskan |
| Instalasi gagal | Track, versi Android, pesan error | Periksa tester, artifact, kompatibilitas |
| Fitur tidak bekerja | Langkah, hasil harapan/aktual | Buat issue dan tambahkan test |

- Hindari meminta screenshot yang memuat data pribadi.
- Pisahkan masalah yang dapat direproduksi dari dugaan.
- Jangan mengabaikan laporan hanya karena build berhasil.

---

<!-- _class: fit -->

# Observabilitas: Apa yang Dikumpulkan?

| Sinyal | Contoh | Pertanyaan |
|---|---|---|
| Crash reporting | exception, stack trace, versi | Di mana aplikasi gagal? |
| Analytics | event `beta_feedback_sent` | Apakah alur fitur digunakan? |
| Logging | pesan teknis terbatas | Apa yang terjadi sebelum error? |

- Kumpulkan seperlunya; beri tahu pengguna sesuai kebijakan privasi.
- Jangan log token, password, isi pesan, atau pengenal pribadi.
- Analytics event bukan pengganti crash reporting atau test.

---

<!-- _class: fit -->

# Crashlytics: Tangkap Error Flutter

Pasang paket dan ikuti setup Firebase untuk platform aplikasi:

```bash
flutter pub add firebase_crashlytics firebase_analytics
```

Setelah `Firebase.initializeApp()`:

```dart
import 'dart:ui';

FlutterError.onError =
    FirebaseCrashlytics.instance.recordFlutterFatalError;
PlatformDispatcher.instance.onError = (error, stack) {
  FirebaseCrashlytics.instance.recordError(
    error, stack, fatal: true,
  );
  return true;
};
```

Uji hanya pada build/perangkat uji; pastikan event muncul di dashboard.

---

<!-- _class: fit -->

# Analytics: Event yang Bermakna

```dart
await FirebaseAnalytics.instance.logEvent(
  name: 'beta_feedback_sent',
  parameters: {'screen_name': 'home'},
);
```

- Pilih nama event yang jelas dan jumlahnya terkontrol.
- Catat aksi, bukan isi formulir atau identitas pengguna.
- Dokumentasikan arti parameter agar tim membaca data dengan konsisten.
- Hormati persetujuan, pengaturan privasi, dan kebijakan institusi.

> Data yang tidak perlu sebaiknya tidak dikumpulkan.

---

<!-- _class: fit -->

# Verifikasi Telemetri dengan Aman

1. Jalankan aplikasi uji dan picu event non-sensitif yang terdokumentasi.
2. Kirim satu error non-fatal buatan pada build uji.
3. Untuk crash fatal, gunakan instruksi uji resmi Crashlytics hanya di perangkat uji.
4. Tunggu pemrosesan, lalu periksa event, versi, dan stack trace.
5. Pastikan laporan tidak memuat data pribadi atau rahasia.

- Dashboard dapat membutuhkan waktu untuk menerima dan memproses data.
- Jangan membuat crash buatan pada rilis yang dipakai pengguna.

---

<!-- _class: fit -->

# Membaca Dashboard Rilis

Bandingkan versi/build beta dan cari:

- crash-free users / sessions dan perubahan dibanding build sebelumnya;
- issue baru, jumlah pengguna terdampak, serta stack trace yang berulang;
- perangkat/OS yang terkait dan langkah reproduksi;
- event alur utama yang menunjukkan fitur dapat digunakan;
- laporan tester yang cocok dengan waktu dan versi kejadian.

> Angka agregat memberi sinyal; reproduksi dan konteks menentukan penyebab.

---

<!-- _class: fit -->

# Triage dan Keputusan Rilis

```text
Temukan issue → cek versi & dampak → reproduksi
             → perbaiki + test → build baru
             → uji ulang → lanjut / tahan rilis
```

- Prioritaskan crash yang menghalangi alur utama atau berdampak luas.
- Catat keputusan, bukti, pemilik tindak lanjut, dan versi perbaikan.
- Jika ada risiko, hentikan perluasan distribusi; ikuti prosedur rollback/stop track.
- Jangan menghapus laporan atau menaikkan rilis tanpa memahami dampaknya.

---

<!-- _class: fit -->

# AI sebagai Asisten Analisis Log

AI boleh membantu mengelompokkan stack trace atau menyusun hipotesis.

Sebelum berbagi:

1. Hapus token, email, ID pengguna, URL privat, dan payload sensitif.
2. Berikan versi aplikasi, konteks teknis minimum, dan error yang sudah disamarkan.
3. Minta beberapa hipotesis, bukti pendukung, dan langkah reproduksi.
4. Verifikasi setiap hipotesis dengan kode, test, dan perangkat.

> AI tidak menentukan tingkat risiko rilis dan tidak menggantikan pemeriksaan manusia.

---

<!-- _class: fit -->

# Tantangan: Analisis Insiden Beta

Release `1.0.0+21` menunjukkan crash baru pada halaman detail:

```text
type 'Null' is not a subtype of type 'String'
DetailPage.build → Product.fromJson
```

Diskusikan:

- Informasi apa yang perlu dikumpulkan sebelum menyimpulkan akar masalah?
- Bagaimana menyamarkan log sebelum meminta bantuan AI?
- Test apa yang mencegah regresi?
- Apakah track perlu ditahan sementara? Jelaskan berdasarkan bukti.

---

<!-- _class: fit -->

# Tugas Minggu 14

Pada repository project Flutter:

1. Siapkan kandidat beta dengan versi/build number dan commit yang tercatat.
2. Bangun AAB release dan simpan bukti pemeriksaan/test.
3. Distribusikan ke Internal testing atau Firebase App Distribution.
4. Konfigurasikan Crashlytics dan satu event Analytics non-sensitif.
5. Uji telemetry pada build uji dan catat hasil dashboard.
6. Tulis ringkasan rilis: masalah, dampak, keputusan, dan langkah berikutnya.

Jika layanan berbayar/akun tidak tersedia, dokumentasikan demonstrasi alternatif dan keterbatasannya.

---

<!-- _class: fit -->

# Bukti dan Refleksi

README/`docs/release-report.md` berisi:

- versi, build number, commit/tag, format artefak, dan track distribusi;
- hasil test, perangkat/OS, serta langkah uji tester;
- screenshot dashboard yang sudah disamarkan;
- satu temuan/insiden dan keputusan tindak lanjut;
- AI yang digunakan, konteks yang disamarkan, dan verifikasi manual.

Refleksi: Apa bukti yang membuat Anda menahan atau melanjutkan rilis?

---

<!-- _class: fit -->

# Prinsip Utama

## Rilis
> Mulai dari kelompok kecil; ikat build ke commit dan bukti uji.

## Monitoring
> Kumpulkan data minimum yang membantu menemukan masalah.

## Keputusan
> Triage berdasarkan dampak, reproduksi, dan konteks versi.

## AI
> Samarkan log, verifikasi hipotesis, dan pegang keputusan sendiri.

---

<!-- _class: fit -->

# Referensi

- [Flutter: continuous delivery](https://docs.flutter.dev/deployment/cd)
- [Flutter: Android release](https://docs.flutter.dev/deployment/android)
- [Google Play: internal testing](https://support.google.com/googleplay/android-developer/answer/9845334)
- [Firebase Crashlytics for Flutter](https://firebase.google.com/docs/crashlytics/flutter/get-started)
- [Firebase Analytics for Flutter](https://firebase.google.com/docs/analytics/get-started?platform=flutter)
- [Firebase App Distribution](https://firebase.google.com/docs/app-distribution)

---

<!-- _class: lead -->

# Build → Distribute → Observe

## Rilis beta bukan garis akhir.

Kumpulkan bukti, pahami dampak, perbaiki, lalu rilis dengan sadar.

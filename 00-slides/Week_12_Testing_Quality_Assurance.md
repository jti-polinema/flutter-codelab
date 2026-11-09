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
section pre { margin: 0.25em 0; line-height: 1.25; }
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
section.fit pre { margin: 0.2em 0; line-height: 1.18; }
section.fit pre, section.fit code { font-size: 13px; }
section.fit blockquote { font-size: 16px; margin: 0.2em 0; }
section.fit table { font-size: 15px; }
</style>

<!-- _class: lead -->

# Minggu 12
## Pengujian dan Jaminan Kualitas

**Pemrograman Mobile**

Pengujian Unit | Pengujian Widget | Pengujian Integrasi | Testable Architecture

> Menulis pengujian yang membantu kita mengubah kode dengan lebih percaya diri.

---

<!-- _class: fit -->

# Tujuan Pembelajaran

Setelah pertemuan ini, mahasiswa diharapkan mampu:

1. Memilih jenis pengujian yang sesuai untuk sebuah fitur.
2. Menulis pengujian unit untuk logika bisnis dan kasus tepi.
3. Memeriksa tampilan serta interaksi widget Flutter.
4. Memisahkan dependensi agar kode mudah diuji.
5. Menguji alur aplikasi dan meninjau cakupan kode secara kritis.

---

<!-- _class: fit -->

# Mengapa Kita Menguji Aplikasi?

- Perubahan kecil dapat mengubah perilaku fitur lain tanpa disadari.
- Pengujian memberi bukti bahwa skenario penting masih berjalan.
- Pengujian yang baik mendokumentasikan perilaku yang diharapkan.
- Otomatisasi membuat pemeriksaan berulang lebih cepat dan konsisten.

> Tujuan pengujian bukan membuktikan bahwa aplikasi sempurna. Tujuannya menemukan masalah lebih awal dan membuat dampak perubahan lebih mudah dipahami.

---

<!-- _class: fit -->

# Tiga Jenis Pengujian

| Jenis | Fokus | Contoh |
|---|---|---|
| Unit | Fungsi atau objek secara terpisah | Validasi judul tugas |
| Widget | UI dan interaksi widget | Form menolak input kosong |
| Integrasi | Alur beberapa bagian aplikasi | Tambah tugas, lalu lihat di daftar |

Mulai dari unit test yang cepat. Tambahkan widget dan integration test untuk perilaku yang melibatkan UI atau beberapa bagian aplikasi.

---

<!-- _class: fit -->

# Kode yang Mudah Diuji

- Pisahkan aturan bisnis dari widget.
- Letakkan akses data di belakang kontrak repository.
- Berikan dependensi melalui konstruktor.
- Ganti layanan nyata dengan fake dalam pengujian.

```text
Widget → Service / Use case → Repository → API
                           ↑
                 Fake repository saat test
```

Pemisahan ini membuat pengujian lebih terarah dan tidak bergantung pada jaringan.

---

<!-- _class: fit -->

# Pengujian Unit: Periksa Aturan

```dart
bool isValidTaskTitle(String value) {
  return value.trim().isNotEmpty;
}
```

Kasus yang perlu diperiksa:
- Judul berisi teks diterima.
- Teks kosong ditolak.
- Teks yang hanya berisi spasi ditolak.

Struktur test: **Arrange** (siapkan), **Act** (jalankan), **Assert** (periksa).

---

<!-- _class: fit -->

# Tulis Test yang Bermakna

```dart
test('menolak judul yang hanya berisi spasi', () {
  expect(isValidTaskTitle('   '), isFalse);
});
```

- Nama test menyatakan perilaku yang diperiksa.
- Uji kasus normal, batas, dan kegagalan yang relevan.
- Pastikan test akan gagal jika perilaku yang diuji rusak.
- Hindari test yang hanya menjalankan kode tanpa memeriksa hasil.

Jalankan seluruh pengujian dengan `flutter test`.

---

<!-- _class: fit -->

# Menguji Kode Asinkron

```dart
abstract interface class TaskRepository {
  Future<List<String>> getTasks();
}

class TaskService {
  TaskService(this.repository);
  final TaskRepository repository;

  Future<List<String>> loadTasks() => repository.getTasks();
}
```

Abstraksi repository membuat sumber data dapat diganti saat pengujian.

---

<!-- _class: fit -->

# Gunakan Fake, Bukan API Langsung

```dart
class FakeTaskRepository implements TaskRepository {
  FakeTaskRepository(this.tasks);
  final List<String> tasks;

  @override
  Future<List<String>> getTasks() async => tasks;
}
```

- Hasil test tidak bergantung pada internet atau server.
- Skenario sukses dan error dapat dikendalikan.
- Test lebih cepat dan mudah diulang.
- Pastikan error tidak diam-diam dianggap sebagai data kosong.

---

<!-- _class: fit -->

# Pengujian Widget

```dart
testWidgets('menampilkan judul halaman', (tester) async {
  await tester.pumpWidget(
    const MaterialApp(
      home: Scaffold(body: Text('Daftar tugas')),
    ),
  );

  expect(find.text('Daftar tugas'), findsOneWidget);
});
```

`pumpWidget` merender UI. `find` mencari elemen, sedangkan `expect` memeriksa hasil.

---

<!-- _class: fit -->

# Periksa Interaksi Pengguna

```dart
await tester.enterText(
  find.byKey(const Key('task-title-field')),
  'Baca materi',
);
await tester.tap(find.byKey(const Key('save-task-button')));
await tester.pumpAndSettle();

expect(find.text('Baca materi'), findsOneWidget);
```

Tambahkan `Key` jika kontrol sulit ditemukan dengan teks atau tipe widget. Periksa juga loading, data kosong, input tidak valid, dan error.

---

<!-- _class: fit -->

# Pengujian Integrasi

Uji alur penting pada emulator atau perangkat:

1. Buka aplikasi dan tunggu UI siap.
2. Masukkan judul tugas.
3. Tekan tombol simpan.
4. Pastikan tugas tampil di daftar.

```bash
flutter test integration_test/app_test.dart -d <device_id>
```

Pilih beberapa alur berisiko tinggi. Pengujian integrasi lebih lambat daripada unit dan widget test.

---

<!-- _class: fit -->

# Regresi dan Cakupan Kode

- Saat memperbaiki bug, tambahkan test yang gagal sebelum perbaikan.
- Jalankan ulang suite dengan `flutter test`.
- Buat laporan cakupan dengan `flutter test --coverage`.
- Tinjau cabang penting dan kasus tepi yang belum diperiksa.

> Persentase cakupan bukan ukuran tunggal mutu. Yang lebih penting adalah perilaku penting benar-benar diuji.

---

<!-- _class: fit -->

# Praktikum: Uji Fitur Daftar Tugas

1. Catat skenario sukses, input tidak valid, dan kegagalan.
2. Tulis unit test untuk validasi atau aturan bisnis.
3. Pisahkan akses data dan buat fake repository.
4. Periksa tampilan serta interaksi pada widget test.
5. Uji satu alur utama dengan integration test.
6. Jalankan `flutter test` dan `flutter analyze`, lalu dokumentasikan hasilnya.

Pilih test berdasarkan risiko dan manfaatnya, bukan jumlah baris kode.

---

<!-- _class: fit -->

# Tinjau Test dengan Cermat

Sebelum menyatakan pekerjaan selesai, tanyakan:

- Apakah test akan gagal jika fitur benar-benar rusak?
- Apakah kasus tepi dan error penting sudah diperiksa?
- Apakah test bebas dari ketergantungan pada jaringan atau urutan eksekusi?
- Apakah hasil test mudah dipahami oleh rekan satu tim?

AI dapat membantu memberi ide, tetapi Anda tetap bertanggung jawab memeriksa test dan hasilnya.

---

<!-- _class: fit -->

# Referensi dan Tindak Lanjut

- Flutter testing: `docs.flutter.dev/testing/overview`
- Widget testing: `docs.flutter.dev/cookbook/testing/widget/introduction`
- Integration testing: `docs.flutter.dev/testing/integration-tests`
- Dart test package: `pub.dev/packages/test`

**Setelah sesi ini:** tambahkan test untuk fitur utama project Anda dan dokumentasikan skenario serta hasilnya di `README.md`.

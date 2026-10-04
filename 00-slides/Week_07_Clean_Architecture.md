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
section.fit p, section.fit ul, section.fit ol { margin: 0.25em 0; }
section.fit pre { margin: 0.3em 0; line-height: 1.35; }
section.fit pre, section.fit code { font-size: 15px; }
</style>

<!-- _class: lead -->

# Week 7
## Clean Architecture

**Pemrograman Mobile**

Flutter • SOLID • Feature-First • Repository • Use Case • DI

> Dari aplikasi yang jalan → aplikasi yang terstruktur profesional

---

# Learning Outcomes

Setelah mengikuti pertemuan ini, mahasiswa mampu:

1. Menjelaskan SOLID dan separation of concerns dengan contoh Flutter.
2. Membedakan struktur feature-first vs layer-first.
3. Menjelaskan 3 layer dan aturan dependensi (mengarah ke dalam).
4. Membedakan entity vs model, repository, use case, dan DI.
5. Merefactor project Minggu 5/6 menjadi feature-first.
6. Menguji use case dengan repository palsu.
7. Menilai usulan AI dan mempertahankannya.

---

# Mengapa Arsitektur?

Project Week 6 jalan, tapi:

```text
Widget 400 baris: fetch API + parsing + format tanggal
Notifier memanggil Dio / SQLite langsung
Tambah 1 fitur → edit 5 file yang sudah jalan
Test butuh database & jaringan sungguhan
```

Project nyata butuh struktur:

```text
presentation → domain ← data
logika bisnis murni → bisa diunit-test
satu fitur → satu folder, hapus tanpa takut
```

**Tujuan:** bukan sekadar pindah folder, tapi arah panah dependensi.

---

# Mini Poll

### Widget menampilkan daftar catatan dan langsung memanggil `sqflite`...

Prinsip apa yang dilanggar?

**A.** Single Responsibility
**B.** Dependency Inversion
**C.** Keduanya
**D.** Tidak ada, itu efisien

### Diskusi

> Apa beda "rapi folder" dengan "bersih arsitektur"?

---

# Bagian 1
## SOLID & Tiga Layer

# SOLID Sekilas (1/2)

| Prinsip | Arti praktis |
|---|---|
| **S**ingle Responsibility | satu kelas, satu alasan berubah |
| **O**pen/Closed | tambah perilaku via ekstensi |
| **L**iskov Substitution | pengganti tak merusak aslinya |

Gejala di Flutter:

```text
S → widget 400 baris: API + parsing + format
O → sumber data baru = edit widget lama
L → repo palsu melempar error yang tak pernah ada
```

---

# SOLID Sekilas (2/2)

| Prinsip | Arti praktis |
|---|---|
| **I**nterface Segregation | jangan paksa dependensi tak dipakai |
| **D**ependency Inversion | bergantung pada abstraksi |

Gejala di Flutter:

```text
I → satu interface raksasa AppRepository
    untuk auth + notes + push sekaligus
D → notifier memanggil Dio / sqflite langsung
    alih-alih repository
```

> D adalah fondasi seluruh minggu ini.

---

# Aturan Dependensi

```text
presentation (widget, notifier, router)
      |
      v  bergantung ke
domain (entity, repository interface,
        use case, failure)
      ^
      |  diimplementasikan oleh
data (model, repository impl,
      Dio, SQLite, secure storage)
```

Aturan emas:

> Dependensi hanya mengarah ke dalam.
> `domain` tak tahu Flutter, Dio, SQLite.

### Diskusi

> Mengapa domain harus murni Dart?

---

# Feature-First vs Layer-First

| Struktur | Bentuk | Kapan |
|---|---|---|
| `feature-first` | `features/notes/{data,domain,presentation}` | multi-fitur (pilihan kita) |
| `layer-first` | `lib/{data,domain,presentation}` global | project kecil satu fitur |

Keuntungan feature-first:

```text
satu fitur → dipahami dalam satu folder
hapus fitur → hapus satu folder, tanpa takut
tim → kerja paralel per fitur
```

---

# Kosakata Peran

```text
Entity     → objek bisnis murni (domain)
Model      → representasi JSON/SQLite + mapping (data)
Repository → interface di domain, impl di data
Use case   → satu operasi bisnis (domain)
DI         → wiring via provider Riverpod
```

> Mapping (`toMap/fromMap`) hanya di model.
> Widget tak pernah `new Repository()` sendiri.

---

# Bagian 2
## Audit Project Lama

# Petakan File ke Layer

Isi di README sebelum refactor:

| File | Layer | Masalah? |
|---|---|---|
| `pages/home_page.dart` | presentation | panggil Dio langsung? |
| `data/api_client.dart` | data | OK bila hanya dipakai repo |
| `providers/auth_provider.dart` | presentation | OK bila hanya panggil repo |
| `data/auth_repository.dart` | data | interface + impl tercampur? |

> Tanpa peta ini, refactor = pindah file buta.

---

# Tiga Grep Pemburu Pelanggaran

```text
1. Widget menyentuh data langsung:
   Dio( | openDatabase | FlutterSecureStorage
   di lib/pages, lib/widgets

2. Logika bisnis di build():
   DateFormat | jsonDecode | toIso8601String
   di lib/pages, lib/widgets

3. DI bocor (instansiasi manual):
   Repository( | Dio(BaseOptions
   di lib/pages, lib/providers
```

> Target akhir: ketiga grep NOL hasil di presentation.

---

# Struktur Target

```text
lib/
├── core/
│   └── failures.dart
├── features/
│   └── notes/
│       ├── domain/
│       │   ├── entities/note.dart
│       │   ├── repositories/ (interface!)
│       │   └── usecases/
│       ├── data/
│       │   ├── models/
│       │   └── repositories/ (impl)
│       └── presentation/
│           ├── providers/ (notifier + DI)
│           └── pages/
└── routes.dart
```

### Aturan main

> Commit snapshot dulu. Refactor kode yang sudah jalan.

---

# Bagian 3
## Domain & Data

# Entity Murni

Tanpa import Flutter, tanpa mapping:

```dart
class Note {
  const Note({
    this.id,
    required this.title,
    this.body = '',
    required this.updatedAt,
    this.dirty = false,
  });

  final int? id;
  final String title;
  final String body;
  final DateTime updatedAt;
  final bool dirty;
}
```

> Entity = bahasa bisnis. Bukan bahasa database.

---

# Failure + Kontrak (1/2)

Kegagalan eksplisit, bukan exception bocor:

```dart
sealed class Failure {
  const Failure(this.message);
  final String message;
}

class LocalFailure extends Failure {
  const LocalFailure(super.message);
}

class NetworkFailure extends Failure {
  const NetworkFailure(super.message);
}
```

---

# Failure + Kontrak (2/2)

Kontrak tinggal di domain:

```dart
abstract class NoteRepository {
  Future<({List<Note> notes,
    Failure? failure})> fetchNotes();

  Future<({Note? note,
    Failure? failure})> addNote({
    required String title,
    String body = '',
  });
}
```

> Alternatif industri: `fpdart` / `dartz`
> (`Either<Failure, T>`). Pola kontraknya sama.

---

# Model: Mapping Hanya Di Sini

```dart
class NoteModel extends Note {
  Map<String, Object?> toMap() => {
    'id': id,
    'updated_at':
        updatedAt.toIso8601String(),
    'dirty': dirty ? 1 : 0,
  };

  factory NoteModel.fromMap(map) {
    return NoteModel(
      id: (map['id'] as num?)?.toInt(),
      title: map['title'] as String? ?? '',
      updatedAt: DateTime.tryParse(
        map['updated_at'] as String? ?? '',
      ) ?? DateTime.fromMillisecondsSinceEpoch(0),
    );
  }

  Note toEntity() => Note(...);
}
```

---

# Repository Impl

```dart
class NoteRepositoryImpl
    implements NoteRepository {
  NoteRepositoryImpl({
    required Future<Database>
      Function() openDb,
  }) : _openDb = openDb;

  @override
  Future<...> fetchNotes() async {
    try {
      final db = await _openDb();
      final rows = await db.query(
        'notes',
        orderBy: 'updated_at DESC');
      return (notes: rows
        .map((r) => NoteModel
          .fromMap(r).toEntity())
        .toList(), failure: null);
    } catch (e) {
      return (notes: const [],
        failure: LocalFailure('...$e'));
    }
  }
}
```

> Konversi `.toEntity()` di batas data → domain.

---

# Use Case: Satu Operasi Bisnis

```dart
class GetNotes {
  const GetNotes(this._repository);
  final NoteRepository _repository;

  Future<({List<Note> notes,
    Failure? failure})> call() {
    return _repository.fetchNotes();
  }
}
```

Kapan use case perlu?

```text
perlu → gabung >1 repo / ada aturan bisnis
cukup repo langsung → CRUD satu-baris
```

> Keputusan ini bagian AI Challenge.

---

<!-- _class: fit -->

# Wiring DI dengan Riverpod

*Bagian 4 - Presentation, DI & Verifikasi: satu-satunya tempat wiring:*

```dart
final noteRepositoryProvider = Provider<NoteRepository>((ref) {
  return NoteRepositoryImpl(openDb: openNotesDb);
});
final getNotesProvider = Provider<GetNotes>((ref) {
  return GetNotes(ref.watch(noteRepositoryProvider));
});
final notesProvider = FutureProvider<List<Note>>((ref) async {
  final result = await ref.watch(getNotesProvider).call();
  if (result.failure != null) throw Exception(result.failure!.message);
  return result.notes;
});
```

---

# Halaman Steril

```dart
class NotesPage
    extends ConsumerWidget {
  @override
  Widget build(context, ref) {
    final state =
        ref.watch(notesProvider);
    return state.when(
      loading: () => const Center(
        child:
          CircularProgressIndicator()),
      error: (e, _) => ErrorView(
        message: '$e',
        onRetry: () => ref.invalidate(
          notesProvider),
      ),
      data: (notes) =>
        NotesList(notes),
    );
  }
}
```

> Tanpa SQL, Dio, JSON, format tanggal.

---

# Verifikasi: Definisi Selesai

```text
1. Presentation steril (NOL hasil):
   Dio( | openDatabase | jsonDecode ...

2. Domain steril (NOL hasil):
   package:flutter | package:dio |
   package:sqflite | package:firebase ...

3. flutter analyze bersih + test hijau
```

Refactor selesai bila:

```text
app berjalan IDENTIK (uji 5 mnt/fitur)
+ grep nol + analyze bersih + test hijau
```

---

# Anti-Pattern

Hindari:

### 1. Pindah folder tanpa putus dependensi

```text
widget → Dio tetap ada = bukan Clean
```

### 2. Use case untuk tiap CRUD satu-baris

```text
over-engineering → tim kecil terbebani
```

### 3. Model bocor ke UI

```text
UI hanya kenal entity, tak pernah model
```

### 4. Refactor tanpa snapshot commit

```text
rusak = tak bisa rollback & bandingkan
```

---

# AI Lab
## Gunakan AI sebagai Co-Developer

Contoh prompt:

```text
Project: campus_notify (auth + FCM + pengumuman).
Kondisi: lib/{data, providers, pages},
repo tercampur, widget panggil Dio langsung.
Tugas:
1. Usulkan feature-first Clean Architecture.
2. Tiap file lama → tujuan baru.
3. Tandai over-engineering untuk CRUD sederhana.
4. Wiring DI Riverpod tanpa package tambahan.
Jelaskan trade-off tiap keputusan.
```

### Jangan berhenti pada output AI.

---

# AI Verification Challenge

Setelah AI mengusulkan:

1. **Baca** — pahami arah dependensi usulan.
2. **Verifikasi** — interface di domain? grep, bukan sekilas.
3. **Tolak/terima** — use case per CRUD? tolak + alasan.
4. **Uji** — 3 grep sterilitas + test hijau.
5. **Refactor** — rapikan, sederhanakan yang berlebih.
6. **Jelaskan** — gambar panah tanpa catatan.

### Uji minimal

- entity bebas mapping
- fake repo untuk test domain
- before/after screenshot identik
- `flutter analyze` + `flutter test`

---

# Aktivitas Kelompok

## Code Review 15 Menit

Cari minimal:

- 2 pelanggaran dependensi (widget → Dio/SQLite)
- 2 masalah kontrak (interface di data, model bocor)
- 1 over-engineering (use case tak perlu)
- 1 masalah DI (new Repository di widget)
- 1 improvement dari usulan AI

Kemudian jawab:

> **Apakah struktur ini layak production? Mengapa?**

---

# Quiz Cepat

### 1. Interface repository tinggal di?

A. Data
B. Domain
C. Presentation
D. Core

### 2. Kapan use case dibutuhkan?

A. Selalu, untuk tiap CRUD
B. Saat gabung repo / ada aturan bisnis
C. Tidak pernah
D. Hanya untuk login

### 3. Domain boleh import?

A. Flutter + Dio
B. sqflite + Firebase
C. Pure Dart saja
D. Apa saja

---

# Jawaban Quiz

### 1 → B

Kontrak milik domain; data hanya mengimplementasikan.

### 2 → B

```text
CRUD satu-baris → repo langsung cukup
```

### 3 → C

```text
domain murni → bisa diunit-test murni
```

---

# AI Challenge

Gunakan AI untuk **usulan reorganisasi**:

> "Usulkan feature-first Clean Architecture untuk campus_notify + tandai over-engineering."

Kemudian mahasiswa wajib:

1. memahami arah dependensi usulan
2. menemukan minimal 2 kelemahan usulan AI
3. membuktikan sterilitas dengan grep
4. menyederhanakan yang berlebih
5. mendokumentasikan alasan di `docs/`
6. mencatat perubahan manual

---

# Deliverables

## Repository

```text
week-07-clean-architecture/
├── lib/
├── test/
├── docs/
├── README.md
└── screenshots/
```

README minimal berisi:

- diagram layer + arah dependensi
- hasil 3 grep sterilitas
- screenshot before/after identik
- cara menjalankan
- AI tools yang digunakan
- prompt + tabel usulan-vs-keputusan
- masalah ditemukan dan hasil testing

---

# Rubrik Week 7

| Komponen | Bobot |
|---|---:|
| Entity/Model + Repository I/Impl | 20% |
| Use Case + DI + Dependency Rule | 25% |
| Refactor Identik + Bukti Grep | 15% |
| UI & UX | 10% |
| Testing | 10% |
| Code Quality | 10% |
| Responsible AI Usage | 10% |
| **Total** | **100%** |

---

## Checklist Sebelum Submit

### Struktur
- [ ] Interface di domain, impl di data
- [ ] Mapping hanya di model
- [ ] Widget tak new Repository sendiri

### Bukti
- [ ] 3 grep sterilitas nol hasil
- [ ] Before/after identik + screenshot
- [ ] 2 test lulus (sukses + failure)

### AI
- [ ] Prompt + tabel keputusan dicatat
- [ ] Mampu gambar panah tanpa AI

---

# Exit Ticket

Jawab sebelum kelas berakhir:

### 1. Apa perbedaan:

```text
Entity
vs
Model
```

### 2. Kapan:

```text
Use case perlu
vs
Repo langsung cukup
```

### 3. Mengapa interface di domain, bukan di data?

### 4. Bagian mana usulan AI yang Anda tolak, dan mengapa?

---

# Key Takeaways

## SOLID

> Bergantung pada abstraksi, bukan concretion.

## Layer

> presentation → domain ← data. Selalu ke dalam.

## Pragmatisme

> Use case saat logika tumbuh, bukan untuk tiap CRUD.

## AI

> AI mengusulkan reorganisasi.
> **Developer menilai dan memutuskan.**

---

# Next Week

## Week 8: Mid Project Review & Code Review

```text
Campus Notify (terstruktur)
   ↓
Git Flow + Pull Request
   ↓
Review sejawat + static analysis
   ↓
Project siap paruh kedua semester
```

### Target

> Kode yang tidak hanya jalan dan rapi, tapi lolos review tim.

---

# Build → Verify → Explain

## Prinsip Pembelajaran Mobile

```text
         AI
          ↓
       Generate
          ↓
       Developer
          ↓
         Verify
          ↓
         Test
          ↓
       Refactor
          ↓
        Explain
```

## Jangan hanya bisa memindahkan file.

### Jadilah developer yang memahami ke mana panah dependensi menunjuk.

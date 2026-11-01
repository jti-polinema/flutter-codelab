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

# Minggu 11
## Optimasi Performa

**Pemrograman Mobile**

Flutter DevTools | Profiling | Widget Rebuild | Lazy Loading | Caching

> Dari aplikasi yang berfungsi menuju aplikasi yang cepat, efisien, dan siap produksi

---

# Tujuan Pembelajaran

Setelah pertemuan ini, mahasiswa diharapkan mampu:

1. Menggunakan Flutter DevTools untuk melakukan profiling CPU dan memori.
2. Mengidentifikasi serta mengurangi widget rebuild yang tidak diperlukan.
3. Menerapkan lazy loading, pagination, dan optimasi gambar.
4. Mengoptimalkan jaringan dengan menerapkan caching dan strategi offline-first.
5. Memperbaiki bottleneck performa pada aplikasi yang sudah ada.

---

# Mengapa Optimasi Performa Penting?

Aplikasi mobile modern harus responsif, hemat baterai, dan berjalan lancar di berbagai spesifikasi perangkat. Performa yang buruk dapat menyebabkan:

- **Frame drop (jank)** — UI tidak mulus pada 60/120 FPS
- **Konsumsi memori tinggi** — aplikasi dihentikan oleh sistem operasi
- **Baterai cepat habis** — CPU/GPU bekerja berlebihan
- **Pengalaman pengguna buruk** — uninstall dan rating rendah

> Performa bukan fitur tambahan, melainkan syarat dasar aplikasi profesional.

---

# Pipeline Rendering Flutter

```text
UI Thread (Dart)          Raster Thread (GPU)
+------------------+      +------------------+
|  Build Widget    |      |                  |
|  Layout          | ---> |  Composite       |
|  Paint           |      |  Rasterize       |
+------------------+      +------------------+
      |                          |
      v                          v
  Frame Budget: 16,67 ms (60 FPS) / 8,33 ms (120 FPS)
```

- **UI Thread**: Kode Dart, build, layout, paint — harus selesai dalam waktu < 16 ms
- **Raster Thread**: Rendering GPU — dapat menjadi bottleneck jika gambar besar atau kompleks
- **Jank**: Frame melebihi budget → terjadi frame drop

---

# Kategori Optimasi Utama

| Kategori | Fokus | Tools |
|----------|-------|-------|
| CPU/Build | Widget rebuild, layout berlebih, komputasi berat | DevTools Performance, Timeline |
| Memori | Leak, gambar besar, list tidak dibuang | DevTools Memory, Heap Snapshot |
| Jaringan | Request berulang, payload besar, tidak ada cache | DevTools Network, Dio Interceptor |
| Gambar | Resolusi tidak sesuai, format tidak efisien | DevTools Memory, cached_network_image |

---

# Flutter DevTools: Profiling CPU

```bash
# Dari terminal project
flutter run --profile
# Atau
flutter run -d <device_id> --profile

# Buka DevTools di browser (otomatis terbuka atau)
# http://127.0.0.1:9100
```

**Catatan**: Gunakan mode `--profile` (bukan `--debug` atau `--release`) untuk profiling yang akurat. Mode debug memiliki overhead yang signifikan.

---

# Tab Performance (CPU Profiling)

- **Flame Chart**: Visualisasi call stack per frame — cari frame merah (jank)
- **Timeline Events**: Event build, layout, paint, GC, HTTP
- **Frame Chart**: Bar per frame — hijau (<16 ms), kuning (16–32 ms), merah (>32 ms)
- **Track: UI Thread** — cari `build`, `layout`, `paint` yang lama
- **Track: Raster Thread** — cari `rasterize`, `image decode` yang lama

---

# Tab Memory (Memory Profiling)

- **Heap Size**: Total memori yang dialokasikan
- **Used/External**: Memori Dart + native (gambar, texture)
- **GC Events**: Garbage collection — GC yang sering terjadi = tekanan memori
- **Heap Snapshot**: Ambil snapshot → filter class → cari instance yang tidak dibutuhkan
- **Leak Detection**: Bandingkan snapshot sebelum/sesudah navigasi

---

# Alur Kerja Profiling

1. Jalankan aplikasi dengan `flutter run --profile`
2. Buka DevTools → tab Performance
3. Klik **Record** → gunakan aplikasi secara normal (scroll, navigasi, load data)
4. Klik **Stop** → analisis frame merah/kuning
5. Klik frame bermasalah → lihat Flame Chart → identifikasi fungsi paling atas yang lama
6. Perbaiki kode → ulangi profiling untuk verifikasi

---

# Penyebab Rebuild Berlebih

- `setState` pada widget root yang besar
- Provider/Riverpod listener pada widget yang tidak memerlukannya
- `ListView.builder` tanpa `const` item
- Anonymous function di build method (closure baru tiap frame)
- StatefulWidget yang seharusnya StatelessWidget

---

# Teknik Optimasi: const Constructor

```dart
// Sebelum: rebuild tiap parent rebuild
Widget build(BuildContext context) {
  return Column(
    children: [
      Text('Judul'),           // tidak const
      Icon(Icons.star),        // tidak const
      MyCustomWidget(),        // tidak const
    ],
  );
}

// Sesudah: hanya rebuild jika parameter berubah
Widget build(BuildContext context) {
  return const Column(
    children: [
      Text('Judul'),
      Icon(Icons.star),
      MyCustomWidget(),
    ],
  );
}
```

---

# Teknik Optimasi: Ekstrak Widget ke Class Terpisah

```dart
// Sebelum: semua di satu build method
class MyPage extends StatelessWidget {
  Widget build(BuildContext context) {
    return Column(
      children: List.generate(100, (i) => Text('Item $i')), // rebuild semua
    );
  }
}

// Sesudah: item terpisah, bisa const
class MyPage extends StatelessWidget {
  Widget build(BuildContext context) {
    return Column(
      children: List.generate(100, (i) => const MyItem()), // hanya MyItem rebuild
    );
  }
}

class MyItem extends StatelessWidget {
  const MyItem();
  Widget build(BuildContext context) => const Text('Item');
}
```

---

# Teknik Optimasi: RepaintBoundary

```dart
// Hanya area ini yang di-repaint saat berubah
RepaintBoundary(
  child: ExpensiveWidget(),
)
```

Gunakan untuk: chart, animasi kompleks, area yang sering berubah visual tetapi tidak butuh rebuild layout.

---

# Teknik Optimasi: Riverpod select

```dart
// Hanya rebuild saat value.count berubah, bukan seluruh state
final count = ref.watch(counterProvider.select((s) => s.count));

// Atau gunakan Consumer dengan builder spesifik
Consumer(
  builder: (context, ref, child) {
    final value = ref.watch(provider);
    return Text('$value');
  },
)
```

---

# Teknik Optimasi: Hindari Anonymous Function di Build

```dart
// Buruk: closure baru tiap build
onPressed: () => doSomething()

// Baik: method reference atau tear-off
onPressed: doSomething

// Atau gunakan callback yang sudah didefinisikan
onPressed: _handlePress
```

---

# Lazy Loading dengan ListView.builder

`ListView.builder` hanya membangun item yang terlihat (viewport). Pastikan:

- Gunakan `ListView.builder` bukan `ListView(children: [...])`
- Item widget menggunakan `const` constructor
- Hindari `Expanded` atau `Flexible` di dalam item jika tidak perlu

---

# Pagination (Infinite Scroll)

```dart
class PaginatedList extends ConsumerStatefulWidget {
  const PaginatedList();

  @override
  ConsumerState<PaginatedList> createState() => _PaginatedListState();
}

class _PaginatedListState extends ConsumerState<PaginatedList> {
  final _scrollController = ScrollController();
  final _items = <Item>[];
  var _page = 1;
  var _isLoading = false;
  var _hasMore = true;

  @override
  void initState() {
    super.initState();
    _scrollController.addListener(_onScroll);
    _loadPage();
  }

  void _onScroll() {
    if (_scrollController.position.pixels >=
        _scrollController.position.maxScrollExtent - 200) {
      _loadPage();
    }
  }

  Future<void> _loadPage() async {
    if (_isLoading || !_hasMore) return;
    _isLoading = true;
    try {
      final newItems = await ref.read(itemRepositoryProvider).fetchPage(_page);
      if (newItems.isEmpty) {
        _hasMore = false;
      } else {
        _items.addAll(newItems);
        _page++;
      }
    } finally {
      _isLoading = false;
      if (mounted) setState(() {});
    }
  }

  @override
  Widget build(BuildContext context) {
    return ListView.builder(
      controller: _scrollController,
      itemCount: _items.length + (_hasMore ? 1 : 0),
      itemBuilder: (context, index) {
        if (index == _items.length) {
          return const Center(child: CircularProgressIndicator());
        }
        return ItemTile(item: _items[index]);
      },
    );
  }
}
```

---

# Optimasi Gambar

| Teknik | Deskripsi | Package |
|--------|-----------|---------|
| Resize di server | Minta ukuran yang sesuai (thumbnail), bukan full-res | Backend/CDN |
| Format modern | WebP, AVIF lebih kecil dari JPEG/PNG | Server/CDN |
| Cache lokal | Simpan gambar ke disk, hindari download ulang | `cached_network_image` |
| Placeholder | Tampilkan blur/low-res dulu, lalu swap | `fade_in_image`, `cached_network_image` |
| Precache | Muat gambar sebelum dibutuhkan (mis. halaman berikutnya) | `precacheImage` |
| Memcache | Batasi ukuran cache memori | `PaintingBinding.instance.imageCache` |

---

# Contoh cached_network_image

```dart
CachedNetworkImage(
  imageUrl: item.imageUrl,
  placeholder: (context, url) => Shimmer.fromColors(
    baseColor: Colors.grey[300]!,
    highlightColor: Colors.grey[100]!,
    child: Container(color: Colors.white),
  ),
  errorWidget: (context, url, error) => const Icon(Icons.error),
  memCacheWidth: 300,  // decode di ukuran tampil, bukan full-res
  memCacheHeight: 200,
  fadeInDuration: const Duration(milliseconds: 200),
)
```

---

# Strategi Caching Jaringan

| Strategi | Cocok Untuk | Implementasi |
|----------|-------------|--------------|
| Cache-First | Data jarang berubah (konfigurasi, master data) | Baca cache → jika kosong, fetch API → simpan cache |
| Network-First | Data kritis real-time (chat, harga, stok) | Fetch API → simpan cache → fallback ke cache jika gagal |
| Stale-While-Revalidate | Data semi-real-time (feed, daftar) | Tampilkan cache → fetch background → update UI |
| Cache-Only (Offline) | Mode offline penuh | Hanya baca dari local DB (Hive/SQLite/Drift) |

---

# Repository dengan Cache (Stale-While-Revalidate)

```dart
class ItemRepository {
  final Dio _dio;
  final Box<Item> _localBox; // Hive

  ItemRepository(this._dio, this._localBox);

  Future<List<Item>> getItems() async {
    // 1. Tampilkan cache segera (jika ada)
    final cached = _localBox.values.toList();
    if (cached.isNotEmpty) {
      _emitCached(cached);
    }

    // 2. Fetch fresh data di background
    try {
      final response = await _dio.get('/items');
      final fresh = (response.data as List)
          .map((e) => Item.fromJson(e))
          .toList();

      // 3. Update local cache
      await _localBox.clear();
      await _localBox.addAll(fresh);

      // 4. Emit fresh data
      _emitFresh(fresh);
      return fresh;
    } catch (e) {
      if (cached.isEmpty) rethrow;
      return cached; // Return cache sebagai fallback
    }
  }
}
```

---

# Dio Interceptor untuk Retry dan Cache

```dart
final dio = Dio(BaseOptions(
  baseUrl: 'https://api.example.com',
  connectTimeout: const Duration(seconds: 10),
  receiveTimeout: const Duration(seconds: 15),
));

// Retry interceptor
dio.interceptors.add(RetryInterceptor(
  dio: dio,
  logPrint: print,
  retries: 3,
  retryDelays: [
    const Duration(seconds: 1),
    const Duration(seconds: 2),
    const Duration(seconds: 5),
  ],
));

// Cache interceptor (custom)
dio.interceptors.add(CacheInterceptor(
  storage: CacheStorage(),
  policy: CachePolicy.staleWhileRevalidate,
));
```

---

# Kompresi dan Payload Kecil

- Gunakan `gzip` / `brotli` di server (header `Accept-Encoding`)
- Hanya minta field yang dibutuhkan (GraphQL / query parameter `?fields=id,name,thumb`)
- Batch request jika memungkinkan
- Gunakan ETag / If-None-Match untuk validasi cache

---

# Praktikum: Optimasi Performa Aplikasi Nyata

## Langkah 1: Baseline Profiling
1. Buka project Minggu 7–10 Anda (misalnya `campus_notify`)
2. Jalankan `flutter run --profile`
3. Buka DevTools → tab Performance → klik **Record**
4. Lakukan skenario: buka daftar, scroll cepat, buka detail, kembali, pull-to-refresh
5. Klik **Stop** → catat: jumlah frame merah/kuning, rata-rata frame time, top fungsi di Flame Chart
6. Simpan screenshot Flame Chart sebagai `screenshots/before-profile.png`

## Langkah 2: Identifikasi Masalah
Berdasarkan profiling, identifikasi minimal 3 masalah:
- Widget rebuild berlebih (cari `build` berulang di Flame Chart)
- Gambar besar tidak di-resize (cari `image decode` di Raster Thread)
- Request API berulang tanpa cache (cari `HTTP` event berulang)
- List tidak menggunakan builder (cari `Layout` lama saat scroll)
- Memory leak (Heap naik terus di tab Memory saat navigasi bolak-balik)

Tulis temuan di `docs/performance-findings.md`

---

# Praktikum: Terapkan Optimasi

Pilih dan terapkan minimal 3 optimasi:

1. **Const constructor**: Tambah `const` ke widget statis
2. **Ekstrak widget**: Pecah widget besar jadi widget kecil `const`
3. **RepaintBoundary**: Bungkus area yang sering repaint (mis. chart, animasi)
4. **Riverpod select**: Ganti `ref.watch(provider)` jadi `ref.watch(provider.select(...))`
5. **ListView.builder**: Pastikan semua list pakai builder
6. **CachedNetworkImage**: Ganti `Image.network` dengan `CachedNetworkImage` + `memCacheWidth/Height`
7. **Pagination**: Tambah infinite scroll ke list panjang
8. **Repository cache**: Tambah stale-while-revalidate di repository

---

# Praktikum: Verifikasi Profiling

1. Jalankan lagi `flutter run --profile`
2. Record skenario yang sama
3. Bandingkan: frame merah/kuning berkurang, frame time rata-rata turun
4. Simpan screenshot sebagai `screenshots/after-profile.png`

---

# Memory Profiling

1. Buka tab Memory di DevTools
2. Ambil **Heap Snapshot** di halaman daftar
3. Navigasi ke detail → kembali → ulang 5x
4. Ambil snapshot lagi → bandingkan: apakah ada objek yang menumpuk?
5. Jika ada leak: cari referensi yang tidak dibuang (controller, listener, stream subscription)
6. Perbaiki dengan `dispose()` yang benar

---

<!-- _class: fit -->

# Quiz Cepat (1/2)

### 1. Mode mana yang harus digunakan untuk profiling akurat?

A. `flutter run --debug`  
B. `flutter run --profile`  
C. `flutter run --release`  
D. `flutter run --test`

---

<!-- _class: fit -->

# Quiz Cepat (2/2)

### 2. Teknik apa yang TIDAK mengurangi widget rebuild?

A. Menggunakan `const` constructor  
B. Ekstrak widget ke class terpisah  
C. Menggunakan `setState` di root widget  
D. Riverpod `select`

---

<!-- _class: fit -->

# Jawaban Quiz

### 1 -> B

```text
Mode --profile memberikan performa mendekati release
tanpa overhead debug, cocok untuk profiling akurat
```

### 2 -> C

```text
setState di root widget justru memicu rebuild seluruh subtree
Gunakan const, ekstrak widget, atau Riverpod select
```

---

<!-- _class: fit -->

# Hasil yang Diharapkan

## Repositori

```text
11-week-11-performance-optimization/
|-- lib/
|   |-- features/
|   |   |-- optimized_list.dart
|   |-- repository/
|   |   |-- item_repository.dart
|-- docs/
|   |-- performance-findings.md
|   |-- performance-report.md
|-- test/
|-- screenshots/
|   |-- before-profile.png
|   |-- after-profile.png
|-- README.md
```

README minimal berisi:

- Baseline metrics (frame time, jank count, memory usage)
- Optimasi yang diterapkan + alasan
- `flutter analyze` + `flutter test`
- Tangkapan layar before/after profiling
- Hasil verifikasi performa

---

<!-- _class: fit -->

# Penilaian Minggu 11

| Komponen | Bobot |
|----------|-------|
| Profiling dan Identifikasi Masalah | 20% |
| Penerapan Optimasi (min. 3 teknik) | 30% |
| Verifikasi Hasil (before/after metrics) | 20% |
| Optimasi Gambar dan Jaringan | 15% |
| Dokumentasi dan Pengujian | 10% |
| Presentasi dan Refleksi | 5% |
| **Total** | **100%** |

---

<!-- _class: fit -->

# Daftar Periksa Sebelum Kumpulkan

### Profiling

- [ ] Baseline profiling dilakukan dengan `flutter run --profile`
- [ ] Minimal 3 masalah performa teridentifikasi
- [ ] Screenshot Flame Chart before/after tersedia

### Optimasi

- [ ] Minimal 3 teknik optimasi diterapkan
- [ ] Const constructor ditambahkan ke widget statis
- [ ] Widget diekstrak ke class terpisah
- [ ] Riverpod select digunakan untuk granular rebuild
- [ ] CachedNetworkImage dengan memCacheWidth/Height diterapkan
- [ ] Pagination/infinite scroll ditambahkan (jika list panjang)
- [ ] Repository cache (stale-while-revalidate) diterapkan

### Memori

- [ ] Memory profiling dilakukan (Heap Snapshot before/after navigasi)
- [ ] Tidak ada memory leak (objek tidak menumpuk)
- [ ] Semua controller/listener/subscription di-dispose dengan benar

### Dokumentasi

- [ ] `docs/performance-findings.md` lengkap
- [ ] `docs/performance-report.md` lengkap (baseline, optimasi, hasil)
- [ ] README menjelaskan fitur, cara menguji, dan hasil
- [ ] `flutter analyze` bersih
- [ ] Minimal satu widget test untuk fitur yang dioptimasi

---

<!-- _class: fit -->

# Kartu Keluar

Jawab sebelum kelas berakhir:

### 1. Optimasi mana yang memberikan dampak terbesar dan mengapa?

### 2. Bagaimana Anda memverifikasi bahwa optimasi benar-benar bekerja (bukan placebo)?

### 3. Trade-off apa yang Anda hadapi (misalnya kompleksitas kode vs performa)?

### 4. Kapan sebaiknya optimasi premature dihindari?

---

<!-- _class: fit -->

# Poin Utama

## Profiling

> Ukur dulu, optimasi kemudian. Jangan menebak bottleneck.

## Rebuild

> `const` + ekstrak widget + `select` = rebuild minimal.

## Gambar

> Resize di server, cache di client, decode di ukuran tampil.

## Jaringan

> Cache-first untuk data statis, stale-while-revalidate untuk feed, network-first untuk kritis.

## Verifikasi

> Bandingkan metrics before/after dengan skenario yang sama.

---

<!-- _class: fit -->

# Minggu Depan

## Minggu 12: Testing & Quality Assurance

```text
Aplikasi dioptimasi
        |
        v
Unit test + Widget test
        |
        v
Integration test
        |
        v
Coverage & Regression prevention
```

### Sasaran

> Pastikan kualitas aplikasi terjaga melalui testing otomatis yang komprehensif.

---

<!-- _class: fit -->

# Bangun -> Verifikasi -> Jelaskan

## Prinsip Pembelajaran Mobile

1. AI mengusulkan jawaban
2. Pengembang memverifikasi
3. Uji coba untuk konfirmasi
4. Perbaiki jika belum akurat
5. Jelaskan alasan keputusan

## Jangan hanya bisa memakai tools.

### Jadilah pengembang yang mampu menjelaskan keputusan optimasi yang diambil.

- Profiling adalah bukti, bukan tebakan
- Setiap optimasi harus memiliki rationale teknis
- Trade-off harus didokumentasikan dan dibenarkan

---

# Referensi

- [Flutter DevTools Overview](https://docs.flutter.dev/tools/devtools/overview)
- [Flutter UI Performance](https://docs.flutter.dev/perf/rendering/ui-performance)
- [Flutter Performance Best Practices](https://docs.flutter.dev/perf/best-practices)
- [cached_network_image](https://pub.dev/packages/cached_network_image)
- [Dio HTTP Client](https://pub.dev/packages/dio)
- [Learn Dart in Y Minutes](https://learnxinyminutes.com/dart/)
- [Dart Language Tour](https://dart.dev/guides/language/language-tour)
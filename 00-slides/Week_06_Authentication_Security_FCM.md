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
</style>

<!-- _class: lead -->

# Week 6
## Authentication, Security & FCM

**Pemrograman Mobile**

Flutter • Secure Storage • JWT • Firebase Cloud Messaging

> Dari aplikasi anonim → aplikasi yang personal, aman, dan real-time

---

# Learning Outcomes

Setelah mengikuti pertemuan ini, mahasiswa mampu:

1. Menjelaskan alur autentikasi (Firebase Auth / JWT / OAuth) dan beda ID token vs access vs refresh token.
2. Menyimpan token aman dengan secure storage + refresh otomatis.
3. Menjelaskan arsitektur FCM: app server, Firebase, perangkat.
4. Meminta notification permission dan mengelola token lifecycle.
5. Membedakan notification payload vs data payload pada 3 app state.
6. Menangani klik notifikasi (deep link) dan topic messaging.
7. Menerapkan prinsip keamanan dasar aplikasi mobile.

---

# Mengapa Auth + Push?

Aplikasi Week 5 menyimpan data, tapi:

```text
Tanpa auth:
  siapa pun membuka → semua catatan terlihat

Tanpa push:
  jadwal berubah → user tidak tahu sampai buka aplikasi
```

Aplikasi kampus nyata butuh keduanya:

```text
Dengan auth + FCM:
  Login → data personal → token aman
  Pengumuman → push → klik → halaman tujuan
```

**Tujuan:** personal, aman, dan berkomunikasi real-time.

---

# Mini Poll

### Jika refresh token disimpan di SharedPreferences lalu HP dicuri...

Apa risikonya?

**A.** Tidak ada, SharedPreferences terenkripsi
**B.** Penyerang bisa membaca token dan menyamar sebagai user
**C.** Aplikasi otomatis logout
**D.** Minta AI memblokir HP

### Diskusi

> Mengapa banner notifikasi muncul saat aplikasi diminimize, tapi tidak saat aplikasi terbuka?

---

# Bagian 1
## Authentication & Token

# Pilihan Pola Login

Aturan praktis:

| Pola | Cara kerja singkat | Kapan dipakai |
|---|---|---|
| `Firebase Auth` | SDK menukar kredensial → ID token JWT | login cepat tanpa server auth sendiri |
| `JWT + refresh` | server terbitkan access pendek + refresh panjang | backend kampus sendiri |
| `OAuth Google` | authorization code → ditukar token | login sosial / SSO kampus |

Week 6 memakai:

> Mock auth provider + JWT simulasi (siap diganti Firebase Auth)

---

# Tiga Token, Tiga Peran

```text
ID token      → bukti identitas (siapa dia)
Access token  → izin akses berumur pendek (±15 menit)
Refresh token → tiket berumur panjang untuk access baru
```

Alur refresh:

```text
Request API --Bearer access--> 401 expired?
  Ya --> tukar refresh --> access baru --> ulangi 1x
  Refresh ikut mati --> logout --> /login
```

> Refresh token **hanya** di secure storage. Tidak pernah di SharedPreferences.

---

# TokenStore: Satu Pintu Token

```dart
class TokenStore {
  static const _accessKey = 'access_token';
  static const _refreshKey = 'refresh_token';

  Future<void> save({
    required String access,
    required String refresh,
  }) async {
    await _storage.write(
        key: _accessKey, value: access);
    await _storage.write(
        key: _refreshKey, value: refresh);
  }

  Future<String?> readAccess() =>
      _storage.read(key: _accessKey);
  Future<void> clear() =>
      _storage.deleteAll();
}
```

> UI tidak pernah menyentuh storage langsung.

---

# Dio: Refresh Otomatis Sekali

```dart
onError: (e, handler) async {
  if (e.response?.statusCode == 401) {
    final refresh =
        await store.readRefresh();
    if (refresh == null)
      return handler.next(e);
    try {
      final renewed =
          await auth.refresh(refresh);
      await store.save(
          access: renewed,
          refresh: refresh);
      final retry = await dio.fetch(
        e.requestOptions
          ..headers['Authorization'] =
              'Bearer $renewed',
      );
      return handler.resolve(retry);
    } catch (_) {
      await store.clear();
    }
  }
  handler.next(e);
}
```

> Refresh gagal → paksa login ulang.

---

# Guard Route dengan GoRouter

Belum login selalu ke `/login`:

```dart
GoRouter(
  redirect: (context, state) {
    final loggedIn = container
        .read(authStateProvider).value
        ?? false;
    final goingLogin =
        state.matchedLocation == '/login';
    if (!loggedIn && !goingLogin)
      return '/login';
    if (loggedIn && goingLogin)
      return '/';
    return null;
  },
);
```

### Pertanyaan

> Mengapa guard membaca provider, bukan membaca storage langsung?

---

# Bagian 2
## Firebase Cloud Messaging

# Arsitektur FCM

```text
App Server --kirim--> Firebase Cloud Messaging
FCM --push--> Perangkat Android / iOS
Aplikasi --daftar token--> Backend (per user)
```

Empat langkah wajib:

```text
1. Minta izin notifikasi
2. Ambil registration token (getToken)
3. Kirim token ke backend (POST /devices)
4. Backend panggil FCM API ke token / topik
```

> Tanpa langkah 3, backend tidak tahu harus kirim ke mana.

---

# Permission: Android 13+ & iOS

Izin runtime hukumnya wajib:

```dart
final settings = await FirebaseMessaging
    .instance
    .requestPermission(
  alert: true,
  badge: true,
  sound: true,
);
```

Status yang diterima:

```text
authorized    → boleh tampil
provisional   → iOS: tampil diam-diam dulu
denied        → arahkan user ke Settings
```

> Selalu cek status, jangan asumsikan diizinkan.

---

# Token Lifecycle (1/2)

Token bisa berubah: reinstall, hapus data, rotasi keamanan.

```dart
// 1. Ambil token kini, kirim ke backend
final token = await FirebaseMessaging
    .instance
    .getToken();
if (token != null)
  await onToken(token);
```

> Token dikirim ke backend (`POST /devices`), bukan hanya dicetak ke log.

---

# Token Lifecycle (2/2)

```dart
// 2. WAJIB: pantau perubahan
FirebaseMessaging.instance
    .onTokenRefresh
    .listen(onToken);

// 3. Langganan topik kampus
await FirebaseMessaging.instance
    .subscribeToTopic(
        'pengumuman-kampus');
```

> Tanpa `onTokenRefresh`, backend menyimpan token basi.

---

# Uji Kirim Pertama

Dari Firebase Console → Messaging:

```text
1. Buat campaign percobaan
2. Isi title + body
3. Target: aplikasi Android Anda
4. Kirim saat aplikasi background
   → banner sistem harus muncul
5. Klik banner → aplikasi terbuka
```

Simpan bukti:

```text
screenshots/fcm-console-test.png
```

### Diskusi

> Mengapa uji pertama dilakukan saat background, bukan foreground?

---

# Bagian 3
## Payload, App State & Klik

# Notification vs Data Payload

| Jenis | Isi | Perilaku sistem |
|---|---|---|
| `notification` | `title` + `body` | tampil otomatis saat background/terminated |
| `data` | key-value bebas mis. `{route: ...}` | selalu ke handler, tak tampil otomatis |

Payload gabungan untuk Campus App:

```json
{
  "notification": {
    "title": "Jadwal berubah",
    "body": "Kelas Mobile pindah A2 13.00"
  },
  "data": {
    "route": "/pengumuman/3",
    "id": "3"
  }
}
```

> `notification` untuk manusia, `data.route` untuk deep link.

---

# Tiga App State, Tiga Handler

| State | Arti | Handler |
|---|---|---|
| Foreground | aplikasi terbuka | `onMessage` → tampil manual |
| Background | diminimize | banner otomatis + `onMessageOpenedApp` |
| Terminated | dimatikan | `getInitialMessage()` |

```text
Foreground  → sistem TIDAK tampilkan banner!
              wajib via flutter_local_notifications
Background  → banner otomatis, klik → rute
Terminated  → dibuka dari notif → rute
```

---

# Background Handler: Top-Level

Berjalan di isolate terpisah:

```dart
@pragma('vm:entry-point')
Future<void>
    firebaseMessagingBackgroundHandler(
  RemoteMessage message,
) async {
  // JANGAN akses BuildContext / Riverpod
  // Tugas: catat / simpan ringan saja
}

void registerBackgroundHandler() {
  FirebaseMessaging.onBackgroundMessage(
    firebaseMessagingBackgroundHandler,
  );
}
```

> Method kelas sebagai handler = error. Harus fungsi top-level.

---

# Foreground: Tampilkan Manual

```dart
FirebaseMessaging.onMessage
    .listen((message) async {
  final route =
      message.data['route'] ?? '/';
  await _local.show(
    message.hashCode,
    message.notification?.title ??
        'Pengumuman',
    message.notification?.body ?? '',
    const NotificationDetails(
        android: androidDetails),
    payload: route,
  );
});

// Background → diklik:
FirebaseMessaging.onMessageOpenedApp
    .listen((message) {
  go(message.data['route'] ?? '/');
});

// Terminated → dibuka dari notif:
final initial = await FirebaseMessaging
    .instance
    .getInitialMessage();
```

---

# Matriks Pengujian Wajib

Uji payload yang sama di 3 state, isi di README:

| State | Diharapkan | Cara uji |
|---|---|---|
| Foreground | banner lokal, klik → `/pengumuman/3` | aplikasi terbuka, kirim |
| Background | banner sistem, klik → rute benar | tekan Home, kirim, klik |
| Terminated | terbuka ke rute benar | swipe-close, kirim, klik |

> Tidak ada tabel bukti = belum selesai.

---

# Topic Messaging

```dart
await FirebaseMessaging.instance
    .subscribeToTopic(
        'pengumuman-kampus');
await FirebaseMessaging.instance
    .unsubscribeFromTopic(
        'pengumuman-kampus');
```

Aturan:

```text
Topik  → broadcast (semua mhs, satu kelas, UKM)
Token  → personal (nilai, tagihan, pribadi)
```

> Nama topik tanpa spasi. Pesan personal jangan via topik.

---

# Anti-Pattern Keamanan

Hindari:

### 1. Token di SharedPreferences

```text
SharedPreferences tidak terenkripsi!
→ selalu flutter_secure_storage
```

### 2. Secret di-hardcode / di-log

Token penuh di `print` atau screenshot = bocor.

### 3. Abaikan `onTokenRefresh`

Backend menyimpan token basi → push gagal diam-diam.

### 4. Handler background berupa method

Harus top-level + `@pragma('vm:entry-point')`.

---

# AI Lab
## Gunakan AI sebagai Co-Developer

Contoh prompt:

```text
Flutter Campus Notification App.
Stack: firebase_messaging,
flutter_local_notifications,
flutter_secure_storage,
go_router, Riverpod.
Buatkan PushService:
- permission + getToken + onTokenRefresh
- onMessage manual + openedApp + initialMessage
- subscribe topic pengumuman-kampus
- background handler top-level
Tandai beda Android 13+ vs iOS dan
bagian yang tak boleh sentuh BuildContext.
```

### Jangan berhenti pada output AI.

---

# AI Verification Challenge

Setelah AI menghasilkan draf:

1. **Baca** — pahami lifecycle token & handler.
2. **Verifikasi** — handler top-level + pragma?
3. **Uji** — 3 app state dengan payload sama.
4. **Tolak/terima** — boleh beda dari AI jika berargumen.
5. **Perbaiki** — token bocor di log? perbaiki.
6. **Jelaskan** — mampu menerangkan tanpa AI.

### Uji minimal

- token terpotong di halaman Debug
- reinstall → token diperbarui
- klik 3 state → rute benar
- `flutter analyze` + `flutter test`

---

# Aktivitas Kelompok

## Code Review 15 Menit

Cari minimal:

- 2 bug token (bocor di log, simpan di prefs)
- 2 bug FCM (tanpa onTokenRefresh, tanpa banner foreground)
- 1 bug navigasi (klik terminated nyasar)
- 1 masalah arsitektur (UI akses storage langsung)
- 1 improvement dari draf AI

Kemudian jawab:

> **Apakah aplikasi notifikasi ini layak production? Mengapa?**

---

# Quiz Cepat

### 1. Refresh token disimpan di?

A. SharedPreferences
B. `flutter_secure_storage`
C. Hardcode di Dart
D. Query URL

### 2. Banner foreground?

A. Otomatis oleh sistem
B. Manual via local notification
C. Tidak mungkin
D. Hanya via AI

### 3. Klik saat terminated ditangani?

A. `onMessage`
B. `onMessageOpenedApp`
C. `getInitialMessage()`
D. `getToken()`

---

# Jawaban Quiz

### 1 → B

Hanya secure storage (Keychain/Keystore) yang terenkripsi.

### 2 → B

```text
foreground → onMessage → tampil manual
```

### 3 → C

```text
terminated → getInitialMessage() → data.route
```

---

# AI Challenge

Gunakan AI untuk membuat **draf PushService**:

> "Buatkan PushService + permission + token lifecycle + 3 handler + topik, tandai beda Android vs iOS."

Kemudian mahasiswa wajib:

1. memahami lifecycle token
2. menemukan minimal 2 kelemahan draf AI
3. membuktikan 3 app state manual
4. memperbaiki kebocoran token/log
5. mendokumentasikan alasan di `docs/`
6. mencatat perubahan manual

---

# Deliverables

## Repository

```text
week-06-authentication-security-fcm/
├── lib/
├── test/
├── docs/
├── README.md
└── screenshots/
```

README minimal berisi:

- deskripsi aplikasi & pola login
- tabel uji 3 app state + screenshot
- token terpotong (bukan penuh)
- cara menjalankan
- AI tools yang digunakan
- prompt + perbaikan manual
- masalah ditemukan dan hasil testing

---

# Rubrik Week 6

| Komponen | Bobot |
|---|---:|
| Auth + Secure Storage + Refresh | 20% |
| FCM: Permission & Token Lifecycle | 25% |
| Payload & 3 App State + Deep Link | 15% |
| UI & UX | 10% |
| Testing | 10% |
| Code Quality | 10% |
| Responsible AI Usage | 10% |
| **Total** | **100%** |

---

## Checklist Sebelum Submit

### Auth
- [ ] Guard route login berfungsi
- [ ] Token hanya di secure storage
- [ ] 401 → refresh 1x → retry / logout

### FCM
- [ ] Permission diminta eksplisit
- [ ] onTokenRefresh terkirim ke backend
- [ ] 3 app state teruji + tabel bukti
- [ ] Topik untuk broadcast, token untuk personal

### AI
- [ ] Prompt + perbaikan manual dicatat
- [ ] Mampu menjelaskan lifecycle tanpa AI

---

# Exit Ticket

Jawab sebelum kelas berakhir:

### 1. Apa perbedaan:

```text
ID token vs Access token vs Refresh token
```

### 2. Apa fungsi:

```text
onTokenRefresh
vs
getInitialMessage
```

### 3. Kapan memakai topik vs token perangkat? Beri contoh aplikasi kampus.

### 4. Bagian mana draf AI yang Anda tolak, dan mengapa?

---

# Key Takeaways

## Auth

> Token aman + refresh otomatis + guard route.

## FCM

> Permission → token → backend → push → klik → rute.

## App State

> Foreground manual, background otomatis, terminated via initial message.

## AI

> AI membuat draf FCM.
> **Developer membuktikan perilakunya.**

---

# Next Week

## Week 7: Clean Architecture

```text
Campus Notify (berfungsi)
   ↓
Pisahkan layer: presentation / domain / data
   ↓
Repository + UseCase + DI
   ↓
Project siap skala production
```

### Target

> Aplikasi yang tidak hanya jalan, tapi terstruktur profesional.

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

## Jangan hanya bisa login.

### Jadilah developer yang memahami siapa yang login dan ke mana pesannya pergi.

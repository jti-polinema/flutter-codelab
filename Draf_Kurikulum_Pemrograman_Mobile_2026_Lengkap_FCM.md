# Draf Rekomendasi Kurikulum Mata Kuliah Pemrograman Mobile 2026

## Sarjana Terapan Teknik Informatika

> Draft untuk diskusi tim pengajar.

Mata kuliah diarahkan menjadi **Modern Mobile Application Development with AI**. Flutter tetap menjadi framework utama, tetapi fokus diperluas ke software engineering, AI-assisted development, FCM, testing, CI/CD, deployment, monitoring, security, dan final project.

## 1. Arah dan Filosofi Kurikulum

Flutter tetap menjadi framework utama. AI diposisikan sebagai **co-developer**, bukan pengganti kemampuan pemrograman. Mahasiswa boleh menggunakan AI untuk draft kode, alternatif desain, debugging, testing, dan dokumentasi, tetapi wajib membaca, menjelaskan, memverifikasi, memperbaiki, dan menguji hasil AI.

## 2. Rekomendasi Teknologi

- Framework: Flutter
- Bahasa: Dart
- State Management: Riverpod
- Routing: GoRouter
- Networking: Dio + REST API
- Backend/Cloud: REST API, Firebase/Supabase sebagai opsi
- Authentication: Firebase Auth / JWT / OAuth
- Push Notification: Firebase Cloud Messaging (FCM)
- Testing: Unit Test, Widget Test, Integration Test
- Version Control: Git + GitHub
- Automation: GitHub Actions
- AI Coding: Cursor / Claude Code / OpenClaw / GitHub Copilot atau tool setara

## 3. Capaian Pembelajaran

Mahasiswa mampu:
1. Membangun aplikasi mobile lintas platform menggunakan Flutter.
2. Menerapkan declarative UI, responsive design, navigation, dan state management.
3. Mengintegrasikan REST API, database lokal, authentication, dan FCM.
4. Menerapkan Clean Architecture, SOLID, repository pattern, dan dependency injection.
5. Menggunakan AI coding assistant secara bertanggung jawab.
6. Melakukan debugging, refactoring, code review, dan testing.
7. Membangun CI/CD dan melakukan deployment.
8. Menerapkan security dan privacy.
9. Menghasilkan aplikasi final yang terdokumentasi dan dapat didemonstrasikan/deploy.

## 4. Pola Pembelajaran Mingguan

**Learning Outcomes → Konsep → Demo → Guided Codelab → AI Challenge → Verification/Refactoring → Testing → Reflection/Mini Assignment**

# 5. Outline Materi 16 Minggu

## Minggu 1 — Mobile Development Ecosystem & Flutter Refresh

### Subtopik
- Evolusi mobile development
- Native vs cross-platform
- Flutter architecture
- Dart language refresh
- Project structure
- Widget tree
- Hot Reload vs Hot Restart
- VS Code & Android Studio
- Emulator & physical device
- Git repository

### Praktikum
Instalasi Flutter, konfigurasi Android SDK, membuat project, menjalankan emulator/perangkat fisik, dan membuat repository GitHub.

### AI Challenge
Meminta AI menjelaskan struktur project dan memverifikasi penjelasan terhadap source code.

## Minggu 2 — Declarative UI & Responsive Design

### Subtopik
- Widget
- StatelessWidget
- StatefulWidget
- Material Design 3
- Cupertino
- Row
- Column
- Expanded
- Responsive layout
- Theme
- Dark mode

### Praktikum
Membangun dashboard mobile responsif.

### AI Challenge
Menghasilkan variasi UI dengan AI lalu memperbaiki accessibility, responsiveness, dan konsistensi desain.

## Minggu 3 — Navigation & State Management

### Subtopik
- Navigation
- GoRouter
- Named route
- Riverpod
- ConsumerWidget
- Provider
- AsyncValue
- Loading/Error/Success state

### Praktikum
Aplikasi ToDo dengan navigation dan Riverpod.

### AI Challenge
AI membuat state management; mahasiswa melakukan code review dan refactoring.

## Minggu 4 — Networking & REST API

### Subtopik
- HTTP
- Dio
- REST API
- JSON
- Serialization
- Model
- Repository Pattern
- Error handling
- Loading state
- Pagination dasar

### Praktikum
Konsumsi REST API menggunakan Dio.

### AI Challenge
Meminta AI membuat Repository/API layer kemudian menguji error handling dan memperbaikinya.

## Minggu 5 — Local Storage & Offline First

### Subtopik
- SharedPreferences
- Hive
- SQLite/Drift
- Caching
- Repository
- Offline-first concept
- Sync dasar

### Praktikum
Aplikasi Offline Notes dengan penyimpanan lokal.

### AI Challenge
Membandingkan strategi persistence yang diusulkan AI dan memilih solusi berdasarkan kebutuhan aplikasi.

## Minggu 6 — Authentication, Security & Push Notification (FCM)

### Subtopik Authentication
- Firebase Authentication
- JWT
- OAuth
- Google Login
- Secure Storage
- Token refresh

### Subtopik Firebase Cloud Messaging
- Firebase Cloud Messaging
- FCM architecture
- Device registration token
- Notification permission
- Android notification channel
- Foreground notification
- Background notification
- Terminated-state notification
- Notification payload vs data payload
- Topic messaging
- User/device targeting
- Token refresh
- Handling notification click
- Deep linking
- Integrasi FCM dengan backend
- Security consideration

### Praktikum
Membuat **Campus Notification App**:

```text
Dosen membuat pengumuman
        ↓
Backend
        ↓
Firebase Cloud Messaging
        ↓
Mahasiswa menerima notification
        ↓
Klik notification
        ↓
Aplikasi terbuka ke halaman pengumuman
```

### AI Challenge
AI membuat implementasi FCM, kemudian mahasiswa memeriksa permission, token lifecycle, foreground/background/terminated behavior, payload, deep linking, dan error handling.

### Integrasi Final Project
FCM dapat digunakan untuk alert Smart Farming, pengumuman Smart Campus, status pesanan, reminder, atau alarm energi.

## Minggu 7 — Clean Architecture

### Subtopik
- SOLID
- Feature-first architecture
- Presentation layer
- Domain layer
- Data layer
- Repository
- Use Case
- Dependency Injection
- Separation of concerns

### Praktikum
Refactoring project menjadi Clean Architecture.

### AI Challenge
AI mengusulkan struktur arsitektur; mahasiswa menilai trade-off dan memperbaiki hasilnya.

## Minggu 8 — Mid Project Review & Code Review

### Subtopik
- Git Flow
- Branching
- Pull Request
- Code review
- Static analysis
- Linter
- Commit quality
- Issue tracking

### Praktikum
Sprint Review dan peer code review.

### AI Challenge
AI menjadi reviewer awal, tetapi keputusan final perbaikan dibuat dan dijelaskan mahasiswa.

## Minggu 9 — AI-assisted Development / Vibe Coding

### Subtopik
- AI coding workflow
- Prompt engineering
- Context engineering
- AI limitations
- Hallucination
- Code verification
- Debugging AI code
- Refactoring AI code
- AI-generated documentation

### Praktikum
Membangun fitur menggunakan AI coding assistant.

### AI Challenge
Mahasiswa menunjukkan prompt, context, hasil AI, perubahan manual, alasan perubahan, dan hasil testing.

## Minggu 10 — AI Feature Integration

### Subtopik
- LLM API
- Prompt design
- Chat interface
- Streaming response
- OCR
- Speech-to-Text
- Image understanding/caption
- AI UX

### Praktikum
Membuat AI Chat atau fitur AI pada aplikasi mobile.

### AI Challenge
Optimasi prompt dan UX serta membandingkan beberapa pendekatan.

## Minggu 11 — Performance Optimization

### Subtopik
- Flutter DevTools
- CPU profiling
- Memory
- Widget rebuild
- Lazy loading
- Pagination
- Image optimization
- Network optimization
- Caching

### Praktikum
Profiling dan optimasi aplikasi.

### AI Challenge
AI menemukan bottleneck kemudian rekomendasinya divalidasi menggunakan profiling.

## Minggu 12 — Testing & Quality Assurance

### Subtopik
- Unit test
- Widget test
- Integration test
- Mock API
- Testable architecture
- Coverage
- Regression testing

### Praktikum
Membangun test suite aplikasi.

### AI Challenge
AI membuat draft test; mahasiswa memeriksa behavior dan menambah edge case.

## Minggu 13 — CI/CD & Automation

### Subtopik
- GitHub Actions
- Build APK
- Build AAB
- Automated testing
- Release automation
- Environment variables/secrets
- Semantic versioning

### Praktikum
Membuat pipeline build dan test otomatis.

### AI Challenge
AI membuat workflow YAML; mahasiswa memeriksa security, trigger, artifact, dan failure handling.

## Minggu 14 — Deployment & Monitoring

### Subtopik
- Google Play Console
- Internal testing
- AAB
- Release track
- Firebase Crashlytics
- Analytics
- Logging
- Crash analysis
- Release monitoring

### Praktikum
Deployment beta dan analisis crash.

### AI Challenge
AI membantu menganalisis crash log, tetapi keputusan teknis tetap dibuat mahasiswa.

## Minggu 15 — Secure Mobile Development

### Subtopik
- OWASP Mobile Top 10
- API security
- Secure storage
- Certificate pinning
- Obfuscation
- Secrets management
- Privacy
- Data minimization

### Praktikum
Security hardening aplikasi.

### AI Challenge
AI melakukan security review awal, lalu mahasiswa memverifikasi temuan.

## Minggu 16 — Final Project Expo

### Subtopik
- Demo product
- GitHub repository
- Architecture review
- AI workflow review
- Testing evidence
- Deployment
- Technical presentation
- Reflection

### Praktikum
Software Expo: demonstrasi aplikasi, repository, arsitektur, testing, deployment, dan dokumentasi.

### AI Challenge
Mahasiswa menjelaskan seluruh penggunaan AI selama proyek, termasuk keputusan yang ditolak atau diperbaiki.

# 6. Posisi FCM dalam Kurikulum

FCM ditempatkan pada **Minggu 6**, bersama Authentication & Security. Mahasiswa sebelumnya telah memperoleh REST API pada Minggu 4 dan local storage pada Minggu 5, sehingga sudah memahami backend, data, authentication, dan lifecycle aplikasi.

FCM tidak diajarkan sekadar sebagai "mengirim notifikasi". Materi mencakup token lifecycle, permission, foreground/background/terminated state, notification vs data payload, topic messaging, click handling, deep linking, dan integrasi backend.

# 7. AI-Augmented Codelab

Codelab tahun sebelumnya tetap dapat digunakan sebagai fondasi. Tidak perlu membuang seluruh modul. Materi yang masih benar dipertahankan, sedangkan langkah tutorial di-upgrade menjadi AI-Augmented Codelab.

### Format setiap modul
1. Learning Outcomes
2. Teori singkat
3. Demo
4. Guided implementation
5. AI Prompt Challenge
6. AI Verification Checklist
7. Refactoring Challenge
8. Testing
9. Reflection
10. Mini Project / Industry Challenge

# 8. Model Tugas: Codelab + AI Lab + Industry Challenge

### Codelab
Guided learning untuk membangun fundamental.

### AI Lab
Mahasiswa menyelesaikan variasi kasus dengan AI dan melakukan verification.

### Industry Challenge
Kasus terbuka yang menyerupai pekerjaan nyata.

Penilaian diarahkan pada kemampuan mengambil keputusan teknis, membaca kode, debugging, refactoring, testing, dan menjelaskan alasan desain.

# 9. Rekomendasi Rubrik Penilaian

| Komponen | Bobot |
|---|---:|
| Analisis kebutuhan | 10% |
| UI/UX dan responsive design | 15% |
| Arsitektur dan engineering practice | 20% |
| Pemanfaatan AI secara bertanggung jawab | 15% |
| Kualitas kode dan dokumentasi | 20% |
| Testing dan QA | 10% |
| Presentasi/demo | 10% |
| **Total** | **100%** |

Penilaian penggunaan AI didasarkan pada kualitas prompt/context, kemampuan memverifikasi hasil AI, debugging, refactoring, testing, dan dokumentasi perubahan — bukan banyaknya kode yang dihasilkan AI.

# 10. Contoh Final Project

Pilihan proyek:

- Smart Campus
- Smart Farming
- Smart Waste Management
- Inventory
- Healthcare Monitoring
- Smart Tourism
- Renewable Energy Monitoring
- Aplikasi bisnis/layanan dengan kebutuhan nyata

### Minimum fitur
- UI responsif
- State management
- REST API
- Local persistence
- Authentication
- FCM push notification
- Clean Architecture
- Testing
- GitHub repository
- CI/CD
- Deployment beta

Fitur AI menjadi enhancement sesuai konteks proyek.

# 11. Rekomendasi Implementasi untuk Tim Pengajar

1. Pertahankan Codelab tahun sebelumnya sebagai baseline.
2. Audit API/package dan kode agar sesuai Flutter/Dart versi yang digunakan saat semester berjalan.
3. Tambahkan FCM sebagai modul Minggu 6.
4. Buat AI-Augmented version untuk setiap codelab.
5. Tetapkan aturan penggunaan AI sejak awal semester.
6. Wajibkan repository Git sebagai bukti proses.
7. Gunakan Pull Request/code review untuk milestone.
8. Gunakan automated testing/CI sebagai bagian penilaian.
9. Jadikan final project sebagai portofolio.
10. Evaluasi kurikulum setiap tahun berdasarkan perubahan Flutter, Android/iOS, AI tooling, dan kebutuhan industri.

# 12. Kesimpulan

Kurikulum 16 minggu ini mempertahankan Flutter sebagai fondasi praktis yang sesuai untuk pendidikan Sarjana Terapan, tetapi memperluas kompetensi ke software engineering modern.

Penambahan FCM pada Minggu 6 memperkuat kemampuan mahasiswa membangun aplikasi yang berkomunikasi dengan pengguna secara real-time.

Integrasi AI dilakukan sebagai workflow engineering yang harus diverifikasi, bukan sebagai jalan pintas untuk menghasilkan kode.

Dengan pendekatan **Codelab + AI Lab + Industry Challenge**, mahasiswa tidak hanya mampu membuat aplikasi, tetapi juga memahami, menguji, memperbaiki, mengamankan, melakukan deployment, dan mempertanggungjawabkan aplikasi yang mereka bangun.

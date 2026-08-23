# Rencana Update Materi Codelab Pemrograman Mobile 2026

## 1. Tujuan

Materi codelab disusun ulang agar sesuai dengan arah kurikulum terbaru: Modern Mobile Application Development with AI. Flutter tetap menjadi framework utama, tetapi fokus pembelajaran diperluas ke software engineering, AI-assisted development, FCM, testing, CI/CD, deployment, monitoring, security, dan final project.

## 2. Prinsip Utama Kurikulum

- Flutter tetap menjadi framework utama.
- AI diposisikan sebagai co-developer, bukan pengganti kemampuan pemrograman.
- Penggunaan Gen AI tidak diperkenankan untuk materi fundamental seperti sintaks dasar Dart, layouting, widget, dan materi dasar lainnya.
- Asesmen untuk materi fundamental dilakukan melalui ujian daring tertutup berbasis multiple choice.
- Untuk materi lanjutan atau project yang bersifat advanced, penggunaan Gen AI diperbolehkan secara bijak sesuai kebijakan dosen pengampu di media komunikasi kelas.
- Mahasiswa wajib membaca, menjelaskan, memverifikasi, memperbaiki, dan menguji hasil AI ketika AI dibolehkan.
- Pembelajaran menekankan pemahaman teknis, refactoring, testing, dan pengambilan keputusan desain.

## 3. Kebijakan AI dan Asesmen

### 3.1 Materi Fundamental
- Materi fundamental tidak menggunakan AI sebagai alat utama belajar.
- Fokus penilaian adalah pemahaman logika, sintaks, struktur widget, dan prinsip desain UI yang benar.
- Ujian dilakukan secara tertutup dan berbasis multiple choice untuk mengukur kemampuan dasar secara objektif.

### 3.2 Materi Advanced dan Project
- Pada materi lanjutan, project, dan tugas berbasis riset atau implementasi nyata, AI dapat dimanfaatkan sesuai kebijakan dosen.
- Penggunaan AI harus tetap disertai verifikasi, dokumentasi, dan tanggung jawab teknis mahasiswa.
- Mahasiswa tetap harus menjelaskan keputusan teknis, alasan pemilihan solusi, dan hasil evaluasi atas output AI.

## 4. Platform Auto-Grading dan Portfolio

### 4.1 Keputusan KBK
- Auto grading tidak disarankan menggunakan GitHub Classroom karena platform tersebut segera retired.
- Pihak pengajar perlu beralih ke platform yang masih aktif dan sesuai kebutuhan evaluasi coding.
- Rekomendasi utama: Codio, GitLab, atau platform lain yang mendukung auto grading dan evaluasi kode secara terstruktur.

### 4.2 Rekomendasi Platform
- Codio: sangat cocok untuk tugas coding, grading otomatis, dan lingkungan kerja terkontrol.
- GitLab: dapat digunakan untuk repository project, CI/CD, dan evaluasi dengan workflow yang lebih fleksibel.
- Platform lain yang relevan: LMS yang mendukung autograding atau runner berbasis container untuk kode.

### 4.3 Alur Implementasi yang Disarankan
1. Mahasiswa mengerjakan tugas di environment yang disediakan oleh platform auto grading atau di repository kelas yang terkelola dengan baik.
2. Tugas dapat dipush ke repository pribadi mahasiswa agar hasil kerja tetap terarsip dan menjadi portfolio.
3. Proses grading dilakukan berdasarkan kualitas implementasi, hasil eksekusi, dan validasi fitur.
4. Dosen mengawasi kualitas proses kerja, bukan hanya nilai akhir dari hasil otomatis.

### 4.4 Portfolio Mahasiswa
- Setiap mahasiswa diwajibkan memiliki repository pribadi di GitHub atau GitLab.
- Hasil tugas, project, dan final project harus dipush ke repository tersebut.
- Repository pribadi berfungsi sebagai bukti perkembangan, portofolio, dan dokumentasi proses pembelajaran.
- Portfolio ini penting untuk evaluasi pembelajaran, refleksi, dan kebutuhan industri masa depan.

### 4.5 Struktur Repository Tugas Mingguan untuk Portofolio
Agar repository mahasiswa tetap rapi dan mudah dinilai, disarankan struktur berikut:

```text
nim-mobile-course/
├── README.md
├── 01-week-1-mobile-development-ecosystem-flutter-refresh/
│   ├── README.md
│   ├── lib/
│   ├── test/
│   └── screenshots/
├── 02-week-2-declarative-ui-responsive-design/
│   ├── README.md
│   ├── lib/
│   ├── test/
│   └── screenshots/
├── 03-week-3-navigation-state-management/
│   ├── README.md
│   ├── lib/
│   ├── test/
│   └── screenshots/
├── 04-week-4-networking-rest-api/
│   ├── README.md
│   ├── lib/
│   ├── test/
│   └── screenshots/
├── 05-week-5-local-storage-offline-first/
│   ├── README.md
│   ├── lib/
│   ├── test/
│   └── screenshots/
├── 06-week-6-authentication-security-fcm/
│   ├── README.md
│   ├── lib/
│   ├── test/
│   └── screenshots/
├── 07-week-7-clean-architecture/
│   ├── README.md
│   ├── lib/
│   ├── test/
│   └── screenshots/
├── 08-week-8-mid-project-review/
│   ├── README.md
│   ├── docs/
│   └── screenshots/
├── 09-week-9-ai-assisted-development/
│   ├── README.md
│   ├── lib/
│   ├── docs/
│   └── screenshots/
├── 10-week-10-ai-feature-integration/
│   ├── README.md
│   ├── lib/
│   ├── docs/
│   └── screenshots/
├── 11-week-11-performance-optimization/
│   ├── README.md
│   ├── lib/
│   ├── docs/
│   └── screenshots/
├── 12-week-12-testing-quality-assurance/
│   ├── README.md
│   ├── test/
│   ├── coverage/
│   └── screenshots/
├── 13-week-13-ci-cd-automation/
│   ├── README.md
│   ├── .github/
│   ├── workflows/
│   └── screenshots/
├── 14-week-14-deployment-monitoring/
│   ├── README.md
│   ├── docs/
│   └── screenshots/
├── 15-week-15-secure-mobile-development/
│   ├── README.md
│   ├── docs/
│   └── screenshots/
├── 16-week-16-final-project-expo/
│   ├── README.md
│   ├── lib/
│   ├── docs/
│   ├── screenshots/
│   └── demo-video/
├── notes/
│   ├── reflections/
│   ├── learning-journal/
│   └── resources/
└── portfolio-summary.md
```

Aturan penyusunan:
- Dokumen `PLAN.md` adalah dokumen perencanaan internal pengajar. Nama atau teks `PLAN.md` tidak boleh ditampilkan atau dirujuk dalam codelab maupun modul yang dibaca mahasiswa; tuliskan instruksi, struktur, atau kebijakan yang relevan secara langsung di modul.
- Setiap folder tugas per minggu harus berisi README singkat yang menjelaskan tujuan, fitur utama, stack teknologi, dan hasil yang dicapai.
- Gunakan folder `screenshots` atau `docs` untuk bukti visual dan dokumentasi.
- Simpan hasil test, coverage, dan file dokumentasi di folder masing-masing agar portofolio mudah dibaca.
- Hindari menumpuk semua projek dalam satu folder tanpa pembagian minggu.
- README utama repository harus berisi ringkasan seluruh progres, link tiap tugas, dan refleksi umum.

### 4.6 Catatan Implementasi
- Platform auto grading bukan pengganti proses pembelajaran dan pembimbingan.
- Platform harus dipilih yang aman, stabil, serta mendukung workflow pengajaran yang tidak terbatas pada sistem lama.
- Keputusan evaluasi tetap berada di tangan dosen pengampu, terutama untuk tugas advanced dan project yang memerlukan interpretasi lebih luas.

## 5. Referensi Pendukung untuk Dart Dasar

Materi dasar Dart perlu didukung dengan referensi resmi dan ringkas agar mahasiswa belajar mandiri dengan format yang efektif. Rekomendasi referensi:
- learnxinyminutes.com/dart
- Official Dart Guide dari situs resmi Dart

Referensi ini dapat dimasukkan pada bagian "Referensi Pendukung" di setiap codelab atau halaman pendukung materi dasar.

## 6. Teknologi yang Direkomendasikan

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

## 4. Capaian Pembelajaran

Mahasiswa mampu:
1. Membangun aplikasi mobile lintas platform menggunakan Flutter.
2. Menerapkan declarative UI, responsive design, navigation, dan state management.
3. Mengintegrasikan REST API, database lokal, authentication, dan FCM.
4. Menerapkan Clean Architecture, SOLID, repository pattern, dan dependency injection.
5. Menggunakan AI coding assistant secara bertanggung jawab.
6. Melakukan debugging, refactoring, code review, dan testing.
7. Membangun CI/CD dan deployment.
8. Menerapkan security dan privacy.
9. Menghasilkan aplikasi final yang terdokumentasi dan dapat didemonstrasikan/deploy.

## 5. Pola Pembelajaran Mingguan

Learning Outcomes → Konsep → Demo → Guided Codelab → AI Challenge → Verification/Refactoring → Testing → Reflection/Mini Assignment

## 6. Outline Materi 16 Minggu (Satu Pertemuan per Minggu)

### Minggu 1 — Mobile Development Ecosystem & Flutter Refresh

- Evolusi mobile development
- Native vs cross-platform
- Flutter architecture
- Peran Dart dalam Flutter
- Project structure dan widget tree
- Hot Reload vs Hot Restart
- Dart refresh: variabel, tipe data, fungsi, class, dan null safety
- Git repository untuk project

Praktikum:
- Instalasi Flutter dan Android SDK
- Membuka project baru
- Menjalankan emulator atau perangkat fisik
- Membuat proyek Flutter pertama dan mengubah UI default
- Membuat repository GitHub untuk project

Outcome:
- Mahasiswa memahami ekosistem mobile, dasar Dart, dan struktur aplikasi Flutter.

AI Challenge:
- Mahasiswa tidak menggunakan AI pada materi dasar; pembelajaran dan asesmen dilakukan lewat praktik dan ujian tertutup.

### Minggu 2 — Declarative UI & Responsive Design

- Widget dasar: StatelessWidget, StatefulWidget
- Material Design 3 dan Cupertino
- Container, Row, Column, Expanded
- Prinsip declarative UI
- Responsive layout
- Theme, dark mode, dan styling
- Accessibility dasar

Praktikum:
- Membangun layout sederhana dengan widget dasar
- Membangun dashboard mobile responsif
- Menambahkan tema dan dark mode

Outcome:
- Mahasiswa mampu membangun UI dasar yang responsif dan konsisten.

AI Challenge:
- AI hanya digunakan untuk eksplorasi opsi desain setelah mahasiswa memahami konsep utama; hasil harus diverifikasi.

### Minggu 3 — Navigation & State Management

- Navigation dasar dalam Flutter
- Route dan named route
- GoRouter dasar
- State management dasar
- Riverpod, Provider, ConsumerWidget
- AsyncValue
- Loading, Error, Success state

Praktikum:
- Membuat aplikasi multi-page sederhana
- Membangun aplikasi ToDo dengan navigasi dan Riverpod
- Menambahkan state loading/error untuk UI

Outcome:
- Mahasiswa mampu membangun navigasi aplikasi dan state management yang rapi.

AI Challenge:
- AI membantu membuat boilerplate, namun mahasiswa harus membaca, meninjau, dan memperbaiki hasilnya.

### Minggu 4 — Networking & REST API

- HTTP, REST API, dan JSON
- Model data dan serialization
- Repository pattern dasar
- Dio dan konfigurasi request
- Error handling
- Loading state, error state, dan empty state
- Pagination dasar

Praktikum:
- Mengambil data dari API dummy
- Menampilkan data ke UI
- Mengintegrasikan API dengan project aplikasi
- Membuat UI loading/error untuk call API

Outcome:
- Mahasiswa paham bahwa API diakses melalui layer data dan repository.

AI Challenge:
- AI dapat membantu merancang repository layer, tetapi mahasiswa harus menguji error handling dan memperbaiki logic.

### Minggu 5 — Local Storage & Offline First

- Local storage dasar
- SharedPreferences
- Hive / SQLite / Drift
- Caching dan sync dasar
- Repository untuk data lokal
- Offline-first concept

Praktikum:
- Menyimpan data sederhana ke local storage
- Mengembangkan aplikasi Offline Notes
- Menampilkan data saat aplikasi offline

Outcome:
- Mahasiswa memahami cara menyimpan data sederhana di perangkat dan menerapkan offline-first.

AI Challenge:
- Mahasiswa membandingkan pilihan storage yang diusulkan AI dan memilih yang paling sesuai kebutuhan aplikasi.

### Minggu 6 — Authentication, Security & Push Notification (FCM)

- Authentication: Firebase Auth, JWT, OAuth, Google Login
- Secure storage dan token refresh
- Prinsip keamanan dasar aplikasi mobile
- FCM architecture
- Notification permission, token lifecycle, foreground/background/terminated behavior
- Notification payload vs data payload
- Click handling, deep link, topic messaging

Praktikum:
- Menyiapkan login sederhana dengan auth provider
- Menyimpan token dengan aman
- Membuat Campus Notification App
- Mengintegrasikan FCM ke aplikasi dan backend sederhana
- Menangani click notification

Outcome:
- Mahasiswa memahami autentikasi, keamanan dasar, dan push notification real time.

AI Challenge:
- AI membantu membuat implementasi awal FCM, lalu mahasiswa memvalidasi token lifecycle, payload, dan behavior notifikasi.

Catatan penting:
- FCM ditempatkan pada Minggu 6, bersama Authentication & Security.
- FCM tidak diajarkan hanya sebagai fitur mengirim notifikasi, tetapi mencakup lifecycle token, payload, foreground/background/terminated behavior, dan integrasi backend.

### Minggu 7 — Clean Architecture

- SOLID dan separation of concerns
- Feature-first architecture
- Presentation, domain, dan data layer
- Repository, Use Case, Dependency Injection
- Refactoring project ke Clean Architecture

Praktikum:
- Mengidentifikasi layer dalam project yang sudah dibuat
- Menyusun struktur folder yang lebih rapi
- Refactoring project ke arsitektur yang lebih terstruktur

Outcome:
- Mahasiswa mengetahui cara memisahkan concern dalam project secara profesional.

AI Challenge:
- AI dapat menyarankan reorganisasi project; mahasiswa menilai trade-off dan menjelaskan keputusan final.

### Minggu 8 — Mid Project Review & Code Review

- Git Flow dan branching
- Pull Request
- Commit quality
- Issue tracking
- Code review
- Static analysis dan linter
- Mid project review

Praktikum:
- Review repository mahasiswa dan membuat PR sederhana
- Sprint review dan peer code review
- Menilai format code dan struktur project

Outcome:
- Mahasiswa memahami praktik kolaborasi dan pengelolaan project profesional.

AI Challenge:
- AI dapat menjadi reviewer awal, tetapi keputusan final tetap diambil mahasiswa.

### Minggu 9 — AI-assisted Development / Vibe Coding

- AI coding workflow
- Prompt engineering dan context engineering
- Keterbatasan AI
- Hallucination dan validasi kode
- Debugging AI-generated code
- Refactoring AI code
- Dokumentasi hasil AI

Praktikum:
- Membuat prompt yang efektif untuk fitur sederhana
- Membangun fitur menggunakan AI assistant
- Menilai hasil AI secara kritis dan memperbaikinya

Outcome:
- Mahasiswa memahami bagaimana AI digunakan secara bertanggung jawab.

AI Challenge:
- Mahasiswa menunjukkan prompt, konteks, hasil AI, perbaikan manual, dan hasil testing.

### Minggu 10 — AI Feature Integration

- LLM API
- Prompt design
- Chat interface dan UX
- Streaming response
- OCR
- Speech-to-Text
- Image understanding / caption
- AI UX dan limitation

Praktikum:
- Membuat interface chat sederhana dengan AI
- Menambahkan fitur AI pada aplikasi berbasis konteks proyek
- Menilai pengalaman pengguna pada fitur AI

Outcome:
- Mahasiswa memahami dasar integrasi fitur AI ke aplikasi mobile.

AI Challenge:
- Mahasiswa membandingkan beberapa pendekatan AI dan memilih solusi yang paling sesuai.

### Minggu 11 — Performance Optimization

- Flutter DevTools
- CPU profiling
- Memory profiling
- Widget rebuild
- Lazy loading
- Pagination
- Image optimization
- Network optimization dan caching

Praktikum:
- Menguji performa aplikasi
- Mencari widget yang terlalu sering rebuild
- Profiling dan optimasi aplikasi yang sudah dibuat

Outcome:
- Mahasiswa mampu mengidentifikasi bottleneck performa dan memperbaikinya.

AI Challenge:
- AI dapat menyarankan strategi optimasi, tetapi validasi dilakukan dengan profiling nyata.

### Minggu 12 — Testing & Quality Assurance

- Unit test dan widget test
- Testable architecture
- Test strategy untuk aplikasi Flutter
- Integration test
- Mock API
- Coverage dan regression testing

Praktikum:
- Menulis test dasar untuk fungsi dan widget
- Membangun test suite aplikasi
- Menambahkan edge case pada fitur utama

Outcome:
- Mahasiswa memahami pentingnya testing sebagai bagian kualitas aplikasi.

AI Challenge:
- AI dapat membuat draft test, tetapi mahasiswa harus memeriksa behavior dan menambahkan case yang relevan.

### Minggu 13 — CI/CD & Automation

- GitHub Actions atau platform CI alternatif
- Build APK dan AAB
- Automated testing
- Release automation
- Environment variable dan secrets
- Semantic versioning

Praktikum:
- Menyiapkan pipeline build dasar
- Mengintegrasikan test di CI
- Menjalankan pipeline otomatis untuk aplikasi proyek

Outcome:
- Mahasiswa memahami otomatisasi build dan deployment pipeline.

AI Challenge:
- AI membantu membuat YAML pipeline awal, tetapi mahasiswa memeriksa keamanan dan arti trigger build.

### Minggu 14 — Deployment & Monitoring

- Google Play Console
- Internal testing
- AAB dan release track
- Firebase Crashlytics
- Analytics, logging, crash analysis
- Monitoring dan release monitoring

Praktikum:
- Menyiapkan release internal aplikasi
- Menjalankan deployment beta dan mengecek logging/error

Outcome:
- Mahasiswa paham alur release aplikasi mobile dan pemantauan pasca release.

AI Challenge:
- AI dapat membantu menganalisis log, tetapi keputusan teknis tetap ada di mahasiswa.

### Minggu 15 — Secure Mobile Development

- OWASP Mobile Top 10
- API security
- Secure storage
- Secrets management
- Certificate pinning
- Obfuscation
- Privacy, data minimization, dan hardening aplikasi

Praktikum:
- Melakukan review keamanan aplikasi
- Security hardening aplikasi akhir
- Menilai risiko pada final project

Outcome:
- Mahasiswa mengenali celah keamanan pada aplikasi mobile dan dapat melakukan hardening dasar.

AI Challenge:
- AI dapat membantu melakukan review awal, tetapi mahasiswa memverifikasi temuan dan keputusan final.

### Minggu 16 — Final Project Expo

- Final project preparation
- Demo product
- Repository readiness
- Dokumentasi arsitektur proyek
- Presentasi final project
- Technical presentation
- AI workflow review
- Reflection dan evaluasi proses pembelajaran

Praktikum:
- Menyiapkan demo aplikasi
- Menyiapkan repository dan dokumentasi
- Software expo: demo aplikasi, repository, testing, deployment, dan dokumentasi

Outcome:
- Mahasiswa siap mempresentasikan proyek mereka dan menjelaskan proses pembuatan secara teknis.

AI Challenge:
- Mahasiswa menjelaskan bagian yang dibantu AI, bagian yang diperbaiki, dan alasan keputusan teknis final.

## 7. Posisi FCM dalam Kurikulum

FCM ditempatkan pada Minggu 6, bersama Authentication & Security. Mahasiswa sebelumnya telah memperoleh REST API pada Minggu 4 dan local storage pada Minggu 5, sehingga sudah memahami backend, data, authentication, dan lifecycle aplikasi.

FCM tidak diajarkan hanya sebagai fitur mengirim notifikasi. Materi mencakup token lifecycle, permission, foreground/background/terminated state, notification vs data payload, topic messaging, click handling, deep linking, dan integrasi backend.

## 8. AI-Augmented Codelab

Codelab tahun sebelumnya tetap dapat digunakan sebagai fondasi. Tidak perlu membuang seluruh modul. Materi yang masih benar dipertahankan, sedangkan langkah tutorial di-upgrade menjadi AI-Augmented Codelab.

Format setiap modul:
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

## 9. Model Tugas: Codelab + AI Lab + Industry Challenge

### Codelab
Guided learning untuk membangun fundamental.

### AI Lab
Mahasiswa menyelesaikan variasi kasus dengan AI dan melakukan verification.

### Industry Challenge
Kasus terbuka yang menyerupai pekerjaan nyata.

Penilaian diarahkan pada kemampuan mengambil keputusan teknis, membaca kode, debugging, refactoring, testing, dan menjelaskan alasan desain.

## 10. Rekomendasi Rubrik Penilaian

| Komponen | Bobot |
|---|---:|
| Analisis kebutuhan | 10% |
| UI/UX dan responsive design | 15% |
| Arsitektur dan engineering practice | 20% |
| Pemanfaatan AI secara bertanggung jawab | 15% |
| Kualitas kode dan dokumentasi | 20% |
| Testing dan QA | 10% |
| Presentasi/demo | 10% |
| Total | 100% |

Penilaian penggunaan AI didasarkan pada kualitas prompt/context, kemampuan memverifikasi hasil AI, debugging, refactoring, testing, dan dokumentasi perubahan — bukan banyaknya kode yang dihasilkan AI.

## 11. Contoh Final Project

Pilihan proyek:
- Smart Campus
- Smart Farming
- Smart Waste Management
- Inventory
- Healthcare Monitoring
- Smart Tourism
- Renewable Energy Monitoring
- Aplikasi bisnis/layanan dengan kebutuhan nyata

Minimum fitur:
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

## 12. Rekomendasi Implementasi untuk Tim Pengajar

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

## 13. Kesimpulan

Kurikulum 16 minggu ini mempertahankan Flutter sebagai fondasi praktis yang sesuai untuk pendidikan Sarjana Terapan, tetapi memperluas kompetensi ke software engineering modern.

Penambahan FCM pada Minggu 6 memperkuat kemampuan mahasiswa membangun aplikasi yang berkomunikasi dengan pengguna secara real-time.

Integrasi AI dilakukan sebagai workflow engineering yang harus diverifikasi, bukan sebagai jalan pintas untuk menghasilkan kode.

Dengan pendekatan Codelab + AI Lab + Industry Challenge, mahasiswa tidak hanya mampu membuat aplikasi, tetapi juga memahami, menguji, memperbaiki, mengamankan, melakukan deployment, dan mempertanggungjawabkan aplikasi yang mereka bangun.

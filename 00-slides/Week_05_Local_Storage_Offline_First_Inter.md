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
  padding: 70px 70px 90px 70px;
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
  content: "POLITEKNIK NEGERI MALANG\A Department of Information Technology";
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
  height: 46px;
  padding-left: 210px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #ffffff;
  font-size: 15px;
  background:
    url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="210" height="46"><text x="105" y="29" text-anchor="middle" font-family="Arial" font-size="14" font-weight="bold" fill="%231b2a4a">jti.polinema.ac.id</text></svg>') no-repeat left top / 210px 46px,
    linear-gradient(to right, #f7941d 0 210px, #1b2a4a 210px 100%);
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

# Week 5
## Local Storage & Offline First

**Mobile Programming**

Flutter • SharedPreferences • SQLite • Offline-First

> From apps that need the internet → apps that stay useful offline

---

# Learning Outcomes

After this session, students will be able to:

1. Explain on-device key-value, relational, and NoSQL storage.
2. Persist simple preferences with `SharedPreferences`.
3. Implement note CRUD with SQLite (`sqflite`) behind a local repository.
4. Apply the offline-first pattern: cache-first, dirty flags, sync queue.
5. Handle loading, data, error, and empty states for local data.
6. Test a local repository with a fake repository.
7. Compare AI-suggested storage options and defend the choice.

---

# Why Local Storage?

The Week 4 app dies without the internet.

```text
Without local storage:
UI ←→ Provider ←→ API (offline = error screen)
```

Real apps must keep working:

```text
With local storage:
UI ←→ Provider ←→ Repository ←→ SQLite (offline = still works)
                                    ↕ sync when online
                                  REST API
```

**Goal:** reading and writing always work; synchronization follows.

---

# Mini Poll

### If a user writes a note in airplane mode, then closes the app...

What should happen?

**A.** The note is lost, that is fine
**B.** The app crashes
**C.** The note is stored locally & sent when online
**D.** Ask AI to recover the note

### Discussion

> What breaks if a list of 1000 notes is stored as one JSON string in SharedPreferences?

---

# Part 1
## Types of Local Storage

# Storage Options

A practical rule:

| Need | Choice | Example |
|---|---|---|
| Small key-value settings | `SharedPreferences` | theme, language |
| Relational data | `SQLite` via `sqflite` | notes, tasks |
| Lightweight NoSQL | `Hive` | object cache |
| Reactive relational | `Drift` | large apps + streams |

Week 5 uses:

> `SharedPreferences` (preferences) + `SQLite` (notes)

---

# What Is SharedPreferences?

Small, primitive key-value storage.

```dart
final prefs =
    await SharedPreferences.getInstance();

await prefs.setBool('dark_mode', true);
final dark =
    prefs.getBool('dark_mode') ?? false;
```

Good for:

```text
dark_mode       → bool
language        → String
last_opened_at  → String
```

> Small values only. Not a database.

---

# When SharedPreferences Is Not Enough

A note list ≠ settings.

```text
SharedPreferences:
  "notes" → "[{...1000 items...}]"   ← fragile!

Problems:
  - updating 1 note rewrites everything
  - no querying / sorting / filtering
  - partial sync is impossible
```

Rule:

- collections → always a database
- preferences → SharedPreferences

### Question

> What breaks if 1000 notes are stored as one JSON string?

---

# Part 2
## SQLite & Local Repository

# Opening the Database

One opener function for every repository:

```dart
Future<Database> openNotesDb() async {
  final dir = await getDatabasesPath();
  return openDatabase(
    p.join(dir, 'offline_notes.db'),
    version: 1,
    onCreate: (db, version) async {
      await db.execute('''
        CREATE TABLE notes(
          id INTEGER PRIMARY KEY AUTOINCREMENT,
          title TEXT NOT NULL,
          body TEXT NOT NULL DEFAULT '',
          updated_at TEXT NOT NULL,
          dirty INTEGER NOT NULL DEFAULT 0
        )
      ''');
    },
  );
}
```

---

# Note Model

The `dirty` field marks unsynchronized notes:

```dart
class Note {
  final int? id;
  final String title;
  final String body;
  final DateTime updatedAt;
  final bool dirty;

  Map<String, Object?> toMap() => {
    'id': id,
    'title': title,
    'body': body,
    'updated_at':
        updatedAt.toIso8601String(),
    'dirty': dirty ? 1 : 0,
  };
}
```

> Mapping happens only in the model, never in the UI.

---

# Null Safety in fromMap

Real databases can hold incomplete rows.

```dart
factory Note.fromMap(
  Map<String, Object?> map,
) {
  return Note(
    id: (map['id'] as num?)?.toInt(),
    title: map['title'] as String? ?? '',
    body: map['body'] as String? ?? '',
    updatedAt: DateTime.tryParse(
      map['updated_at'] as String? ?? '',
    ) ?? DateTime.fromMillisecondsSinceEpoch(0),
    dirty:
      ((map['dirty'] as num?)?.toInt()
        ?? 0) == 1,
  );
}
```

Rules:

- use `as String?`, not `as String`
- provide sensible defaults

---

# Local Repository

The repository separates the UI from the database.

```text
UI (Widget)
   ↓ calls
Provider (Riverpod)
   ↓ uses
NoteRepository
   ↓ uses
sqflite / SharedPreferences
```

---

The UI does not know:

- the database file name
- SQL
- SharedPreferences

The UI only knows:

```dart
Future<List<Note>> fetchNotes()
Future<Note> addNote(title: ...)
Future<void> deleteNote(id)
```

---

# Repository Example

```dart
class NoteRepository {
  NoteRepository({Future<Database> Function()? openDb})
      : _openDb = openDb ?? openNotesDb;

  final Future<Database> Function() _openDb;

  Future<List<Note>> fetchNotes() async {
    final db = await _openDb();
    final rows = await db.query(
      'notes',
      orderBy: 'updated_at DESC',
    );
    return rows.map(Note.fromMap).toList();
  }
}
```

> The constructor accepts `openDb` so tests can inject a fake database.

---

# Benefits of a Local Repository:

- SQL queries in one place
- data source is easy to replace
- easy to mock for testing

---

# Repository + Riverpod Example

```dart
final noteRepositoryProvider =
    Provider<NoteRepository>(
  (ref) => NoteRepository(),
);

final notesProvider =
    FutureProvider<List<Note>>(
  (ref) => ref
      .watch(noteRepositoryProvider)
      .fetchNotes(),
);
```

```text
Widget → watch(notesProvider) → Repository → SQLite
```

After mutations:

```dart
ref.invalidate(notesProvider);
```

---

# Part 3
## Offline-First: Cache & Sync

---

# Three Core Mechanisms

```text
1. Cache-first read
   show local data instantly
   → background refresh → persist

2. Dirty flag
   unuploaded changes → dirty = 1

3. Sync queue
   process in order when online
   → mark clean on server 2xx
```

```text
UI → Provider → Repository → SQLite
                    ↕ sync
                 REST API
```

---

# Cache-First Reads

Never leave the UI blank offline.

```dart
Future<List<Post>> loadPostsCacheFirst()
    async {
  final cached =
      await readCachedPosts();
  // 1. return the cache immediately
  // 2. background: fetch via Dio
  //    → persist → invalidate
  refreshPostsInBackground();
  return cached;
}
```

> Week 4 provided the API; Week 5 provides the cache.

---

# Syncing Dirty Notes

The server is simulated — the mechanism is what counts:

```dart
Future<int> syncNotes(
  NoteRepository repo,
) async {
  final dirtyCount =
      await repo.countDirty();
  if (dirtyCount == 0) return 0;

  // real project: send each dirty
  // note to the REST API here
  await Future.delayed(
    const Duration(seconds: 1),
  );
  await repo.markAllSynced();
  return dirtyCount;
}
```

---

# Conflict Rules

Without an explicit rule, sync silently overwrites data.

Common options:

```text
last-write-wins  → newest updated_at wins
server-wins      → server is always right
client-wins      → local is always right
manual-merge     → user decides
```

Week 5 uses:

> `last-write-wins` based on `updated_at`

### Discussion

> When is `last-write-wins` dangerous? (e.g. balances, stock)

---

## UI States for Local Data
Same as Week 4, local data also has 4 states:

```dart
return state.when(
  loading: () => const Center(
    child:
        CircularProgressIndicator(),
  ),
  data: (notes) {
    if (notes.isEmpty) {
      return const Center(
        child: Text(
          'No notes yet.',
        ),
      );
    }
    return NotesList(notes);
  },
  error: (e, _) => ErrorView(
    message: '$e',
    onRetry: () => ref.invalidate(
      notesProvider,
    ),
  ),
);
```
Offline extra: a `dirty`-count badge.

---

# Deterministic Offline Simulation

Never depend on classroom Wi-Fi.

```text
1. Enable airplane mode
2. Open the app → notes still render
3. Add a note → dirty badge grows
4. Disable airplane mode → run sync
5. Badge returns to 0
```

Plus a `forceOffline` provider toggle:

> Demos and tests never depend on network conditions.

# Case Study
## Offline Notes

```text
NotesPage
   ↓ watch(notesProvider)
NoteRepository.fetchNotes()
   ↓ query SQLite
List<Note> → UI + dirty badge
```

Conditions to demo:

```text
offline → full read/write
add     → dirty = 1
sync    → dirty = 0
cache   → posts render without internet
```

---

# Anti-Patterns

Avoid:

### 1. Collections in SharedPreferences

```text
storing 1000 notes = 1 giant string
```

### 2. SQL queries in the UI

All `db.query` calls belong in the repository.

### 3. Forgetting to invalidate the provider

```dart
// after add/delete:
ref.invalidate(notesProvider);
```

### 4. Tests touching a real database

Use a fake repository via overrides.

---

# AI Lab
## Use AI as a Co-Developer

Example prompt:

```text
Flutter Offline Notes app: note CRUD + theme preference.

Compare SharedPreferences, Hive, sqflite, and Drift:
- query complexity, relations, reactivity
- type-safety, boilerplate, testability

Give a final recommendation in 1 table + schema
for 1000+ notes. Explain each trade-off.
```

### Do not stop at the AI output.

---

# AI Verification Challenge

After the AI produces a recommendation:

1. **Read** — understand each storage option.
2. **Verify** — try `flutter pub add` + its schema.
3. **Accept/reject** — you may differ from AI if argued.
4. **Test** — airplane mode, dirty flag, sync.
5. **Refactor** — clean up repository and provider.
6. **Explain** — able to explain without AI.

### Minimum tests

- full airplane mode
- missing fields in `fromMap`
- dirty badge before/after sync
- `flutter analyze` + `flutter test`

---

# Group Activity

## 15-Minute Code Review

Find at least:

- 2 potential mapping bugs (`fromMap`/`toMap`)
- 2 sync issues (dirty never zero, conflict without rules)
- 1 architecture issue (SQL in UI)
- 1 UX state issue (no dirty badge)
- 1 improvement from the AI recommendation

Then answer:

> **Is this offline app production-ready? Why?**

---

# Quick Quiz

### 1. When to use SharedPreferences?

A. A list of 1000 notes
B. Small key-value settings
C. Complex relational queries
D. Real-time data streaming

### 2. What is the dirty flag for?

A. Prettier UI
B. Marking unsynchronized data
C. Deleting the database
D. Faster internet

### 3. Cache-first means?

A. Always fetch network first
B. Show local first, refresh in background
C. Delete cache on every open
D. Disable the database

---

# Quiz Answers

### 1 → B

SharedPreferences is only for small values like theme and language.

### 2 → B

`dirty = 1` = a sync queue waiting for connectivity.

### 3 → B

```text
cache → render instantly → refresh → persist
```

---

# AI Challenge

Use AI to build a **comparison table**:

> "Compare SharedPreferences, Hive, sqflite, and Drift for Offline Notes + theme preference, with a final recommendation."

Then students must:

1. understand each option
2. try the install and schema
3. find at least 2 flaws in the AI recommendation
4. make their own final choice
5. document reasons in `docs/`
6. record manual changes

---

# Deliverables

## Repository

```text
week-05-local-storage-offline-first/
├── lib/
├── test/
├── docs/
├── README.md
└── screenshots/
```

Minimum README contains:

- app description & storage used
- repositories/providers used
- offline screenshots + dirty badge
- how to run
- AI tools used
- main prompt + comparison table
- manual changes
- issues found and test results

---

# Week 5 Rubric

| Component | Weight |
|---|---:|
| SharedPreferences & SQLite | 20% |
| Local Repository & Offline-First | 25% |
| States: Loading/Error/Empty | 15% |
| UI & UX | 10% |
| Testing | 10% |
| Code Quality | 10% |
| Responsible AI Usage | 10% |
| **Total** | **100%** |

---

## Checklist Before Submit

### Data
- [ ] Preferences via SharedPreferences
- [ ] Note CRUD via SQLite + repository
- [ ] Dirty flag + sync working
- [ ] Conflict rule documented

### UI
- [ ] Fully works offline
- [ ] Dirty badge accurate
- [ ] No queries in widgets

### AI
- [ ] Prompt recorded & comparison table present
- [ ] Final decision + reasons
- [ ] Code understood by student

---

# Exit Ticket

Answer before class ends:

### 1. What is the difference between:

```text
SharedPreferences
vs
SQLite
```

### 2. What is the role of:

```text
Cache-first
vs
Dirty flag
```

### 3. Name 3 offline-first mechanisms with examples.

### 4. Which part of the AI recommendation did you reject, and why?

---

# Key Takeaways

## Storage

> Choose storage by need, not by habit.

## Architecture

> The repository separates UI from the database.

## Offline-First

> Read/write always works, sync follows.

## AI

> AI suggests storage options.
> **Developers compare and decide.**

---

# Next Week

## Week 6: Authentication, Security & FCM

```text
Offline Notes
   ↓
Login (Firebase Auth / JWT)
   ↓
Secure token (secure storage)
   ↓
Real-time notification (FCM)
```

### Goal

> Apps that are personal, secure, and communicate with users in real time.

---

# Build → Verify → Explain

## Mobile Learning Principle

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

## Do not just store data.

### Be a developer who understands the data flow being stored.

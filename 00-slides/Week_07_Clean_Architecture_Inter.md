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

**Mobile Programming**

Flutter • SOLID • Feature-First • Repository • Use Case • DI

> From an app that runs → a professionally structured app

---

# Learning Outcomes

After this session, students will be able to:

1. Explain SOLID and separation of concerns with Flutter examples.
2. Distinguish feature-first vs layer-first structures.
3. Explain 3 layers and the dependency rule (pointing inward).
4. Distinguish entity vs model, repository, use case, and DI.
5. Refactor the Week 5/6 project into feature-first.
6. Test use cases with a fake repository.
7. Assess AI proposals and defend them.

---

# Why Architecture?

The Week 6 project runs, but:

```text
400-line widget: API fetch + parsing + date format
Notifier calling Dio / SQLite directly
1 new feature → edit 5 working files
Tests need a real database & network
```

Real projects need structure:

```text
presentation → domain ← data
pure business logic → unit-testable
one feature → one folder, removable safely
```

**Goal:** not moving folders, but dependency arrow direction.

---

# Mini Poll

### A widget lists notes and calls `sqflite` directly...

Which principle is violated?

**A.** Single Responsibility
**B.** Dependency Inversion
**C.** Both
**D.** None, it is efficient

### Discussion

> What is the difference between "tidy folders" and "clean architecture"?

---

# Part 1
## SOLID & Three Layers

# SOLID at a Glance (1/2)

| Principle | Practical meaning |
|---|---|
| **S**ingle Responsibility | one class, one reason to change |
| **O**pen/Closed | add behavior by extension |
| **L**iskov Substitution | replacements must not break the original |

Smells in Flutter:

```text
S → 400-line widget: API + parsing + format
O → new data source = edit old widgets
L → fake repo throwing errors that never existed
```

---

# SOLID at a Glance (2/2)

| Principle | Practical meaning |
|---|---|
| **I**nterface Segregation | never force unused dependencies |
| **D**ependency Inversion | depend on abstractions |

Smells in Flutter:

```text
I → one giant AppRepository interface
    for auth + notes + push at once
D → notifier calling Dio / sqflite directly
    instead of a repository
```

> D is the foundation of this entire week.

---

# The Dependency Rule

```text
presentation (widgets, notifiers, router)
      |
      v  depends on
domain (entities, repository interfaces,
        use cases, failures)
      ^
      |  implemented by
data (models, repository impls,
      Dio, SQLite, secure storage)
```

Golden rule:

> Dependencies only point inward.
> `domain` knows no Flutter, Dio, SQLite.

### Discussion

> Why must domain be pure Dart?

---

# Feature-First vs Layer-First

| Structure | Shape | When |
|---|---|---|
| `feature-first` | `features/notes/{data,domain,presentation}` | multi-feature (our choice) |
| `layer-first` | `lib/{data,domain,presentation}` globally | tiny single-feature projects |

Feature-first benefits:

```text
one feature → understood in one folder
remove a feature → delete one folder, no fear
teams → work in parallel per feature
```

---

# Role Vocabulary

```text
Entity     → pure business object (domain)
Model      → JSON/SQLite representation + mapping (data)
Repository → interface in domain, impl in data
Use case   → one business operation (domain)
DI         → wiring via Riverpod providers
```

> Mapping (`toMap/fromMap`) only in models.
> Widgets never `new` a repository themselves.

---

# Part 2
## Auditing the Old Project

# Map Files to Layers

Fill in the README before refactoring:

| File | Layer | Problem? |
|---|---|---|
| `pages/home_page.dart` | presentation | calls Dio directly? |
| `data/api_client.dart` | data | OK if only used by repos |
| `providers/auth_provider.dart` | presentation | OK if only calls repos |
| `data/auth_repository.dart` | data | interface + impl mixed? |

> Without this map, refactoring = blind file moving.

---

# Three Violation-Hunting Greps

```text
1. Widgets touching data directly:
   Dio( | openDatabase | FlutterSecureStorage
   in lib/pages, lib/widgets

2. Business logic in build():
   DateFormat | jsonDecode | toIso8601String
   in lib/pages, lib/widgets

3. Leaking DI (manual instantiation):
   Repository( | Dio(BaseOptions
   in lib/pages, lib/providers
```

> End goal: all three greps return ZERO hits in presentation.

---

# Target Structure

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
│           ├── providers/ (notifiers + DI)
│           └── pages/
└── routes.dart
```

### Ground rule

> Commit a snapshot first. Refactor working code.

---

# Part 3
## Domain & Data

# Pure Entity

No Flutter import, no mapping:

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

> Entities speak business. Not database.

---

# Failures + Contract (1/2)

Explicit failures, no leaking exceptions:

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

# Failures + Contract (2/2)

The contract lives in domain:

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

> Industry alternative: `fpdart` / `dartz`
> (`Either<Failure, T>`). Same contract pattern.

---

# Models: Mapping Only Here

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

> Convert with `.toEntity()` at the data → domain boundary.

---

# Use Case: One Business Operation

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

When is a use case needed?

```text
needed → combines >1 repo / owns rules
repo direct suffices → one-line CRUD
```

> This decision is part of the AI Challenge.

---

<!-- _class: fit -->

# Riverpod DI Wiring

*Part 4 - Presentation, DI & Verification: the single wiring point:*

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

# Sterile Pages

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

> No SQL, Dio, JSON, or raw date formatting.

---

# Verification: Definition of Done

```text
1. Sterile presentation (ZERO hits):
   Dio( | openDatabase | jsonDecode ...

2. Sterile domain (ZERO hits):
   package:flutter | package:dio |
   package:sqflite | package:firebase ...

3. flutter analyze clean + green tests
```

Refactor is done when:

```text
app behaves IDENTICALLY (5 min/feature)
+ zero greps + clean analyze + green tests
```

---

# Anti-Patterns

Avoid:

### 1. Moving folders without cutting dependencies

```text
widget → Dio still there = not Clean
```

### 2. A use case for every one-line CRUD

```text
over-engineering → small teams suffer
```

### 3. Models leaking into the UI

```text
UI only knows entities, never models
```

### 4. Refactoring without a snapshot commit

```text
broken = cannot roll back & compare
```

---

# AI Lab
## Use AI as a Co-Developer

Example prompt:

```text
Project: campus_notify (auth + FCM + announcements).
Current: lib/{data, providers, pages},
mixed repos, widgets calling Dio directly.
Tasks:
1. Propose feature-first Clean Architecture.
2. Each old file → new destination.
3. Flag over-engineering for simple CRUD.
4. Riverpod DI wiring, no extra package.
Explain the trade-off of each decision.
```

### Never stop at the AI output.

---

# AI Verification Challenge

After the AI proposes:

1. **Read** — understand the proposed arrow direction.
2. **Verify** — interface in domain? grep, don't skim.
3. **Reject/accept** — use case per CRUD? reject + reason.
4. **Test** — 3 sterility greps + green tests.
5. **Refactor** — tidy up, simplify excess.
6. **Explain** — draw arrows without notes.

### Minimum tests

- mapping-free entities
- fake repo for domain tests
- identical before/after screenshots
- `flutter analyze` + `flutter test`

---

# Group Activity

## 15-Minute Code Review

Find at least:

- 2 dependency violations (widget → Dio/SQLite)
- 2 contract issues (interface in data, leaking models)
- 1 over-engineering (unneeded use case)
- 1 DI issue (new Repository in widget)
- 1 improvement from the AI proposal

Then answer:

> **Is this structure production-ready? Why?**

---

# Quick Quiz

### 1. Where does the repository interface live?

A. Data
B. Domain
C. Presentation
D. Core

### 2. When is a use case needed?

A. Always, for every CRUD
B. When combining repos / owning business rules
C. Never
D. Only for login

### 3. What may domain import?

A. Flutter + Dio
B. sqflite + Firebase
C. Pure Dart only
D. Anything

---

# Quiz Answers

### 1 → B

Contracts belong to domain; data only implements them.

### 2 → B

```text
one-line CRUD → direct repo suffices
```

### 3 → C

```text
pure domain → purely unit-testable
```

---

# AI Challenge

Use AI for a **reorganization proposal**:

> "Propose feature-first Clean Architecture for campus_notify + flag over-engineering."

Then students must:

1. understand the proposed arrow direction
2. find at least 2 flaws in the AI proposal
3. prove sterility with grep
4. simplify what is excessive
5. document the rationale in `docs/`
6. record manual changes

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

Minimum README content:

- layer diagram + arrow direction
- 3 sterility grep results
- identical before/after screenshots
- how to run
- AI tools used
- prompt + proposal-vs-decision table
- issues found and test results

---

# Week 7 Rubric

| Component | Weight |
|---|---:|
| Entity/Model + Repository I/Impl | 20% |
| Use Case + DI + Dependency Rule | 25% |
| Identical Refactor + Grep Evidence | 15% |
| UI & UX | 10% |
| Testing | 10% |
| Code Quality | 10% |
| Responsible AI Usage | 10% |
| **Total** | **100%** |

---

## Checklist Before Submit

### Structure
- [ ] Interfaces in domain, impls in data
- [ ] Mapping only in models
- [ ] Widgets never new repositories

### Evidence
- [ ] 3 sterility greps return zero hits
- [ ] Identical before/after + screenshots
- [ ] 2 tests pass (success + failure)

### AI
- [ ] Prompt + decision table recorded
- [ ] Able to draw arrows without AI

---

# Exit Ticket

Answer before class ends:

### 1. What is the difference between:

```text
Entity
vs
Model
```

### 2. When:

```text
Use case needed
vs
Direct repo suffices
```

### 3. Why does the interface belong in domain, not data?

### 4. Which part of the AI proposal did you reject, and why?

---

# Key Takeaways

## SOLID

> Depend on abstractions, not concretions.

## Layers

> presentation → domain ← data. Always inward.

## Pragmatism

> Use cases when logic grows, not for every CRUD.

## AI

> AI proposes the reorganization.
> **Developers assess and decide.**

---

# Next Week

## Week 8: Mid Project Review & Code Review

```text
Campus Notify (structured)
   ↓
Git Flow + Pull Requests
   ↓
Peer review + static analysis
   ↓
Project ready for the semester's second half
```

### Goal

> Code that not only runs and is tidy, but passes team review.

---

# Build → Verify → Explain

## The Mobile Learning Principle

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

## Don't just be able to move files.

### Be a developer who knows where the dependency arrows point.

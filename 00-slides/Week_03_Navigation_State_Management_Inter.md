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

# Week 3
## Navigation & State Management

**Mobile Programming**

Flutter • GoRouter • Riverpod

> From a single-screen app → a structured multi-page application

---

# Learning Outcomes

After this session, students will be able to:

1. Explain navigation concepts in mobile applications.
2. Implement navigation using `GoRouter`.
3. Distinguish between local state, shared state, and asynchronous state.
4. Implement state management using `Riverpod`.
5. Handle `loading`, `data`, and `error` states.
6. Integrate navigation and state management.
7. Review and verify AI-generated code.

---

# Why Navigation & State Management?

Real-world applications almost never have only one screen.

```text
Login
  ↓
Home
 ├── Profile
 ├── Notifications
 ├── Products
 │    └── Product Detail
 └── Settings
```

As the number of screens and data grows:

- navigation becomes more complex
- more state is introduced
- communication between widgets becomes more difficult
- code becomes harder to maintain

**Goal:** to create a structure that remains simple as the application grows larger.

---

# Mini Poll

### If an app has 15 pages...

How would you navigate between pages?

**A.** `Navigator.push()` everywhere
**B.** All pages in one widget
**C.** Structured routing
**D.** Have AI create all the navigation

### Discussion

> What's the problem if each page manages its own navigation?

---

# Part 1
## Navigation

---

# What Is Navigation?

Navigation is the mechanism for moving between screens/pages.

```text
Home
  │
  ├──→ Profile
  ├──→ Settings
  └──→ Product Detail
             │
             └──→ Checkout
```

Navigation should handle:

- page transitions
- parameters
- deep links
- back navigation
- authentication guards
- nested navigation

---

# Basic Flutter Navigation

Flutter provides `Navigator`.

```dart
Navigator.push(
  context,
  MaterialPageRoute(
    builder: (_) => const DetailPage(),
  ),
);
```

Go back:

```dart
Navigator.pop(context);
```

---

### Advantages

- simple
- easy to understand
- suitable for small applications

#

### Disadvantages

- routing is scattered
- harder to manage as the application grows
- deep links more complex

---

# Why GoRouter?

## GoRouter

Concept:

```text
URL / Route
      ↓
GoRouter
      ↓
Page / Screen
```

Example:

```text
/
├── /login
├── /home
├── /profile
└── /product/:id
```

---

## GoRouter

Benefits:

- centralized routing
- named routes
- path parameterss
- query parameterss
- redirect / route guards
- deep linking
- nested routes

---

# Route Concepts

### Static path

```text
/home
/profile
/settings
```

### Path parameters

```text
/product/123
/product/456
```

Route:

```text
/product/:id
```

### Query parameters

```text
/products?category=food
```

---

# GoRouter Structure

```dart
final router = GoRouter(
  routes: [
    GoRoute(
      path: '/',
      name: 'home',
      builder: (context, state) =>
          const HomePage(),
    ),

    GoRoute(
      path: '/detail/:id',
      name: 'detail',
      builder: (context, state) {
        final id =
            state.pathParameters['id'];
        return DetailPage(id: id!);
      },
    ),
  ],
);
```

**Note:** routes are defined in one place.

---

# Named Routes

Rather than:

```dart
context.go('/detail/123');
```

you can use:

```dart
context.goNamed(
  'detail',
  pathParameters: {
    'id': '123',
  },
);
```

### Why is this useful?

If the URL structure changes, using named routes makes the code more maintainable.

---

# `go()` vs `push()`

### `context.go()`

```dart
context.go('/home');
```

Changes the current location.

### `context.push()`

```dart
context.push('/detail/123');
```

Adds a page to the navigation stack.

```text
go()
Home ─────────→ Detail

push()
Home → Detail → Back → Home
```

Use each according to the application's navigation-stack requirements.

---

# Route Parameters

For example:

```text
/product/101
```

Route:

```text
/product/:id
```

Get parameters:

```dart
final id =
    state.pathParameters['id'];
```

Then:

```dart
ProductDetailPage(
  productId: id!,
)
```

---

### Question

What happens if the `id` is invalid?

> Navigation also requires validation.

---

# Route Guard / Redirect

Example:

```text
User membuka /profile
          ↓
      Logged in?
       ↙       ↘
     YES        NO
      ↓          ↓
   Profile      Login
```

Concept:

```dart
redirect: (context, state) {
  final loggedIn = authState.loggedIn;

  if (!loggedIn) {
    return '/login';
  }

  return null;
}
```

---

# Part 2
## State Management

---

# What Is State?

State is data or a condition that can change while the application is running.

```text
counter = 0
counter = 1
counter = 2
```

Other examples:

- user authentication status
- product list
- dark/light theme
- shopping cart items
- loading status
- API results
- error
- notification count

---

# Problems Without State Management

Imagine you have:

```text
HomePage
   │
   ├── Widget A
   ├── Widget B
   ├── Widget C
   └── Widget D
```

All of them need:

```text
user
cart
notifications
products
```

---

# Problems Without State Management

If data keeps being passed through widgets:

```text
A → B → C → D → E
```

The code can become:

- hard to read
- hard to test
- hard to maintain
- highly coupled

---

# Types of State

### 1. Local State

Used by a single widget.

```text
isPasswordVisible
selectedTab
counter
```

### 2. Shared / App State

Used by multiple parts of the application.

```text
currentUser
shoppingCart
theme
```

---

# Types of State

### 3. Async State

Data whose processing is asynchronous.

```text
Loading
   ↓
Data

atau

Loading
   ↓
Error
```

---

# Why Riverpod?

## Riverpod

State management and dependency management for Flutter/Dart.

```text
Provider
   ↓
State / Dependency
   ↓
Widget
```

Benefits:

- dependencies are more explicit
- can be used across widgets
- supports async state
- testable
- suitable for growing applications

---

# Simple Provider

```dart
final counterProvider =
    StateProvider<int>((ref) => 0);
```

Read:

```dart
final count =
    ref.watch(counterProvider);
```

Update:

```dart
ref.read(
  counterProvider.notifier,
).state++;
```

---

# ConsumerWidget

```dart
class HomePage extends ConsumerWidget {

  const HomePage({super.key});

  @override
  Widget build(
    BuildContext context,
    WidgetRef ref,
  ) {
    final count =
        ref.watch(counterProvider);

    return Text('$count');
  }
}
```
---

## Core concept

```text
ref.watch()
    ↓
Widget depends on state
    ↓
State changes
    ↓
Widget rebuilds
```

---

# `watch()` vs `read()`

### `watch()`

Listens for changes.

```dart
ref.watch(counterProvider);
```

### `read()`

Accesses without listening.

```dart
ref.read(counterProvider);
```

Often used when performing an action:

```dart
ref
  .read(counterProvider.notifier)
  .state++;
```

### Remember

> `watch` = react to changes  
> `read` = access without a subscription

---

# Async State

API data is not immediately available.

```text
Request
   ↓
Loading
   ↓
 ┌───────┐
 ↓       ↓
Data    Error
```

Riverpod provides the pattern:

```dart
AsyncValue<T>
```

The UI can handle:

```text
Loading → Progress indicator
Data    → Display data
Error   → Display a message
```

---

# AsyncValue

General pattern:

```dart
final state = ref.watch(
  productsProvider,
);

return state.when(
  loading: () =>
      const CircularProgressIndicator(),

  data: (products) =>
      ProductList(products),

  error: (error, stack) =>
      Text('Error: $error'),
);
```

### Advantage

Asynchronous states become explicit.

---

# Navigation + State

Combine them:

```text
Home
 │
 │ choose the product ID=101
 ↓
Detail
 │
 │ provider fetches data
 ↓
Loading
 │
 ├──→ Data
 │
 └──→ Error
```

### Navigation determines:

> which page is displayed

### State management determines:

> what data and state are displayed

---

# Case Study
## ToDo Application

```text
Home
 │
 ├── Add Task
 ├── Task Detail
 └── Filter
```

State:

```text
tasks
filter
loading
error
```

Contoh:

```text
All
 ├── Learn Flutter ✓
 ├── Complete the assignment
 └── Review AI-generated code
```

---

# Simple Architecture

```text
UI
 │
 ├── HomePage
 ├── AddTaskPage
 └── DetailPage
       │
       ↓
   Riverpod Provider
       │
       ↓
   Repository
       │
       ↓
   Data Source
```

For Week 3, the main focus is:

> Navigation + State Management

Clean Architecture will be covered in greater depth in Week 7.

---

# Anti-Patterns

Avoid:

### 1. Putting all state in one widget

```text
HomePage
 └── 500 lines of code
```

### 2. Passing all data through constructors

```text
A → B → C → D → E
```

### 3. Using global state for everything

Not all state needs to be global.

### 4. Using AI-generated code without verification

Code that looks correct ≠ correct code.

---

# AI Lab
## Use AI as a Co-Developer

Example prompt:

```text
I am building a Flutter application.

Gunakan:
- GoRouter
- Riverpod
- ConsumerWidget

Create navigation:
Home → Detail/:id

The detail state must have:
Loading, Data, Error.

Explain the reasoning behind each part of the code.
```

### Do not stop at the AI output.

---

# AI Verification Challenge

After AI generates the code:

1. **Read** — understand every part.
2. **Verify** — check the APIs and patterns used.
3. **Run** — make sure the application actually runs.
4. **Test** — try normal and error conditions.
5. **Refactor** — make simple if needed.
6. **Explain** — be able to explain the code without AI.

### Test minimal

- empty ID
- ID not found
- API error
- user presses Back
- state changes

---

# Group Activity

## Code Review — 15 Minutes

Find at least:

- 2 potential bugs
- 2 maintainability issues
- 1 state-management issue
- 1 navigation issue
- 1 improvement to the AI-generated code

Then answer:

> **Is this code production-ready? Why or why not?**

---

# Quick Quiz

### 1. What is GoRouter used for?

A. Database  
B. Navigation/routing  
C. HTTP client  
D. Package manager

### 2. `ref.watch()` used what for?

A. Remove a provider  
B. Listen for state changes  
C. Close the application  
D. Create a route

### 3. `AsyncValue` suitable what for?

A. Asynchronous data  
B. Application colors  
C. Icon  
D. Padding

---

# Quiz Answers

### 1 → B

GoRouter is used for routing/navigation.

### 2 → B

`watch()` make the widget react to provider changes.

### 3 → A

`AsyncValue` suitable to represent:

```text
Loading
Data
Error
```

---

# AI Challenge

Use AI to create the **initial version**:

> “Create a GoRouter and Riverpod structure for a Flutter ToDo app with Home, Add Task, Detail Task, and Settings.”

Students must then:

1. understand the code
2. running the code
3. find at least 2 weaknesses
4. fix the code
5. add their own feature
6. document their manual changes

---

# Deliverables

## Repository

```text
week-03-navigation-state/
├── lib/
├── test/
├── README.md
└── screenshotss/
```

README must contain at least:

- application description
- routes/providers used
- screenshots
- how to run
- AI tools used
- main prompt
- manual changes
- issues found and testing results

---

# Week 3 Rubric

| Components | Weight |
|---|---:|
| Navigation / GoRouter | 20% |
| Riverpod / State Management | 25% |
| Async & Error State | 15% |
| UI & UX | 10% |
| Testing | 10% |
| Code Quality | 10% |
| Responsible AI Usage | 10% |
| **Total** | **100%** |

---

## Pre-Submission Checklist

### Navigation
- [ ] All routes work
- [ ] Named routes are used
- [ ] Parameters can be passed
- [ ] Back navigation works
- [ ] Invalid routes are handled

### State
- [ ] State is placed appropriately
- [ ] Providers are used correctly
- [ ] `watch()` dan `read()` digunakan sesuai kebutuhan
- [ ] Loading/Data/Error are handled

### AI
- [ ] Prompt is documented & AI output is verified
- [ ] Ada manual changes
- [ ] The code is understood by the student

---

# Exit Ticket

Answer before the class ends:

### 1. What is the difference between:

```text
Navigator
vs
GoRouter
```

### 2. When should you use:

```text
Local State
vs
Shared State
```

### 3. What are the roles of:

```text
ref.watch()
ref.read()
```

### 4. Why must AI-generated code still be verified?

---

# Key Takeaways

## Navigation

> GoRouter helps make routing more structured.

## State Management

> Riverpod helps manage state and dependencies explicitly.

## Async

> `AsyncValue` membuat Loading/Data/Error lebih jelas.

## AI

> AI generates code.  
> **The developer is responsible for the correctness of the code.**

---

# Next Week

## Week 4: Networking & REST API

```text
Flutter
   ↓
Dio
   ↓
REST API
   ↓
JSON
   ↓
Model
   ↓
Repository
   ↓
UI
```

### Target

> The Flutter application will not only store local data, but will also start communicating with a backend.

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

## Don't just learn how to build applications.

### Become a developer who understands the application they build.

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
</style>

<!-- _class: lead -->

# Week 6
## Authentication, Security & FCM

**Mobile Programming**

Flutter • Secure Storage • JWT • Firebase Cloud Messaging

> From an anonymous app → a personal, secure, real-time app

---

# Learning Outcomes

After this session, students will be able to:

1. Explain auth flows (Firebase Auth / JWT / OAuth) and ID token vs access vs refresh token.
2. Store tokens securely with secure storage + automatic refresh.
3. Explain the FCM architecture: app server, Firebase, devices.
4. Request notification permission and manage the token lifecycle.
5. Distinguish notification payload vs data payload across 3 app states.
6. Handle notification clicks (deep links) and topic messaging.
7. Apply basic mobile security principles.

---

# Why Auth + Push?

The Week 5 app stores data, but:

```text
Without auth:
  anyone opens it → all notes visible

Without push:
  schedule changes → user unaware until opening the app
```

A real campus app needs both:

```text
With auth + FCM:
  Login → personal data → secure token
  Announcement → push → tap → destination page
```

**Goal:** personal, secure, and real-time communication.

---

# Mini Poll

### If a refresh token is stored in SharedPreferences and the phone is stolen...

What is the risk?

**A.** None, SharedPreferences is encrypted
**B.** The attacker can read the token and impersonate the user
**C.** The app logs out automatically
**D.** Ask AI to block the phone

### Discussion

> Why does a notification banner appear when the app is minimized, but not when the app is open?

---

# Part 1
## Authentication & Tokens

# Login Pattern Options

A practical rule:

| Pattern | How it works (brief) | When to use it |
|---|---|---|
| `Firebase Auth` | SDK exchanges credentials → JWT ID token | fast login without your own auth server |
| `JWT + refresh` | server issues short access + long refresh | your own campus backend |
| `OAuth Google` | authorization code → exchanged for tokens | social login / campus SSO |

Week 6 uses:

> Mock auth provider + simulated JWT (ready to swap for Firebase Auth)

---

# Three Tokens, Three Roles

```text
ID token      → proof of identity (who they are)
Access token  → short-lived permission (±15 minutes)
Refresh token → long-lived ticket for a new access token
```

Refresh flow:

```text
API request --Bearer access--> 401 expired?
  Yes --> exchange refresh --> new access --> retry once
  Refresh dead too --> logout --> /login
```

> Refresh tokens live **only** in secure storage. Never in SharedPreferences.

---

# TokenStore: One Token Gateway

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

> The UI never touches storage directly.

---

# Dio: Single Automatic Refresh

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

> Failed refresh → force re-login.

---

# Route Guard with GoRouter

Unauthenticated users always land on `/login`:

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

### Question

> Why does the guard read the provider instead of reading storage directly?

---

# Part 2
## Firebase Cloud Messaging

# FCM Architecture

```text
App Server --sends to--> Firebase Cloud Messaging
FCM --pushes to--> Android / iOS devices
App --registers token--> Backend (per user)
```

Four mandatory steps:

```text
1. Request notification permission
2. Fetch the registration token (getToken)
3. Send the token to the backend (POST /devices)
4. Backend calls the FCM API for a token / topic
```

> Without step 3, the backend does not know where to send.

---

# Permission: Android 13+ & iOS

Runtime permission is mandatory:

```dart
final settings = await FirebaseMessaging
    .instance
    .requestPermission(
  alert: true,
  badge: true,
  sound: true,
);
```

Possible statuses:

```text
authorized    → may display
provisional   → iOS: deliver quietly first
denied        → direct the user to Settings
```

> Always check the status; never assume permission.

---

# Token Lifecycle (1/2)

Tokens change: reinstall, data wipe, security rotation.

```dart
// 1. Fetch the current token, send to backend
final token = await FirebaseMessaging
    .instance
    .getToken();
if (token != null)
  await onToken(token);
```

> Send the token to the backend (`POST /devices`), not just to the log.

---

# Token Lifecycle (2/2)

```dart
// 2. MANDATORY: watch for rotation
FirebaseMessaging.instance
    .onTokenRefresh
    .listen(onToken);

// 3. Subscribe to the campus topic
await FirebaseMessaging.instance
    .subscribeToTopic(
        'campus-announcement');
```

> Without `onTokenRefresh`, the backend keeps a stale token.

---

# First Send Test

From Firebase Console → Messaging:

```text
1. Create a trial campaign
2. Enter title + body
3. Target: your Android app
4. Send while the app is in background
   → the system banner must appear
5. Tap the banner → the app opens
```

Keep the evidence:

```text
screenshots/fcm-console-test.png
```

### Discussion

> Why is the first test done in background, not foreground?

---

# Part 3
## Payloads, App States & Clicks

# Notification vs Data Payload

| Type | Content | System behavior |
|---|---|---|
| `notification` | `title` + `body` | auto-shown in background/terminated |
| `data` | free key-value, e.g. `{route: ...}` | always to handler, never auto-shown |

Combined payload for the Campus App:

```json
{
  "notification": {
    "title": "Schedule changed",
    "body": "Mobile class moved to A2 1 PM"
  },
  "data": {
    "route": "/announcement/3",
    "id": "3"
  }
}
```

> `notification` for humans, `data.route` for the deep link.

---

# Three App States, Three Handlers

| State | Meaning | Handler |
|---|---|---|
| Foreground | app is open | `onMessage` → display manually |
| Background | minimized | automatic banner + `onMessageOpenedApp` |
| Terminated | killed | `getInitialMessage()` |

```text
Foreground  → the system shows NO banner!
              mandatory via flutter_local_notifications
Background  → automatic banner, tap → route
Terminated  → opened from notification → route
```

---

# Background Handler: Top-Level

It runs in a separate isolate:

```dart
@pragma('vm:entry-point')
Future<void>
    firebaseMessagingBackgroundHandler(
  RemoteMessage message,
) async {
  // NEVER touch BuildContext / Riverpod
  // Job: log / persist lightly only
}

void registerBackgroundHandler() {
  FirebaseMessaging.onBackgroundMessage(
    firebaseMessagingBackgroundHandler,
  );
}
```

> A class method as handler = error. It must be top-level.

---

# Foreground: Display Manually

```dart
FirebaseMessaging.onMessage
    .listen((message) async {
  final route =
      message.data['route'] ?? '/';
  await _local.show(
    message.hashCode,
    message.notification?.title ??
        'Announcement',
    message.notification?.body ?? '',
    const NotificationDetails(
        android: androidDetails),
    payload: route,
  );
});

// Background → tapped:
FirebaseMessaging.onMessageOpenedApp
    .listen((message) {
  go(message.data['route'] ?? '/');
});

// Terminated → opened from notification:
final initial = await FirebaseMessaging
    .instance
    .getInitialMessage();
```

---

# Mandatory Test Matrix

Test the same payload in 3 states, fill in the README:

| State | Expected | How to test |
|---|---|---|
| Foreground | local banner, tap → `/announcement/3` | app open, send |
| Background | system banner, tap → correct route | press Home, send, tap |
| Terminated | opens to the correct route | swipe-close, send, tap |

> No evidence table = not done.

---

# Topic Messaging

```dart
await FirebaseMessaging.instance
    .subscribeToTopic(
        'campus-announcement');
await FirebaseMessaging.instance
    .unsubscribeFromTopic(
        'campus-announcement');
```

Rules:

```text
Topic → broadcast (all students, one class, club)
Token → personal (grades, bills, private)
```

> Topic names contain no spaces. Never send personal messages via topic.

---

# Security Anti-Patterns

Avoid:

### 1. Tokens in SharedPreferences

```text
SharedPreferences is NOT encrypted!
→ always flutter_secure_storage
```

### 2. Hardcoded / logged secrets

A full token in `print` or a screenshot = leaked.

### 3. Ignoring `onTokenRefresh`

The backend keeps a stale token → pushes fail silently.

### 4. Background handler as a method

Must be top-level + `@pragma('vm:entry-point')`.

---

# AI Lab
## Use AI as a Co-Developer

Example prompt:

```text
Flutter Campus Notification App.
Stack: firebase_messaging,
flutter_local_notifications,
flutter_secure_storage,
go_router, Riverpod.
Generate a PushService:
- permission + getToken + onTokenRefresh
- manual onMessage + openedApp + initialMessage
- subscribe topic campus-announcement
- top-level background handler
Mark Android 13+ vs iOS differences and
parts that must never touch BuildContext.
```

### Never stop at the AI output.

---

# AI Verification Challenge

After the AI produces a draft:

1. **Read** — understand token lifecycle & handlers.
2. **Verify** — top-level handler + pragma?
3. **Test** — 3 app states with the same payload.
4. **Reject/accept** — you may differ if you argue it.
5. **Fix** — token leaking into logs? fix it.
6. **Explain** — able to explain without AI.

### Minimum tests

- truncated token on the Debug page
- reinstall → token updated
- 3-state taps → correct route
- `flutter analyze` + `flutter test`

---

# Group Activity

## 15-Minute Code Review

Find at least:

- 2 token bugs (leaked in logs, stored in prefs)
- 2 FCM bugs (no onTokenRefresh, no foreground banner)
- 1 navigation bug (terminated tap misrouted)
- 1 architecture issue (UI touching storage directly)
- 1 improvement from the AI draft

Then answer:

> **Is this notification app production-ready? Why?**

---

# Quick Quiz

### 1. Where is the refresh token stored?

A. SharedPreferences
B. `flutter_secure_storage`
C. Hardcoded in Dart
D. URL query

### 2. Foreground banner?

A. Automatic by the system
B. Manual via local notification
C. Impossible
D. Only via AI

### 3. Terminated tap is handled by?

A. `onMessage`
B. `onMessageOpenedApp`
C. `getInitialMessage()`
D. `getToken()`

---

# Quiz Answers

### 1 → B

Only secure storage (Keychain/Keystore) is encrypted.

### 2 → B

```text
foreground → onMessage → display manually
```

### 3 → C

```text
terminated → getInitialMessage() → data.route
```

---

# AI Challenge

Use AI to draft a **PushService**:

> "Generate PushService + permission + token lifecycle + 3 handlers + topic, marking Android vs iOS differences."

Then students must:

1. understand the token lifecycle
2. find at least 2 flaws in the AI draft
3. prove all 3 app states manually
4. fix token/log leaks
5. document the rationale in `docs/`
6. record manual changes

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

Minimum README content:

- app description & login pattern
- 3-state test table + screenshots
- truncated token (never full)
- how to run
- AI tools used
- prompt + manual fixes
- issues found and test results

---

# Week 6 Rubric

| Component | Weight |
|---|---:|
| Auth + Secure Storage + Refresh | 20% |
| FCM: Permission & Token Lifecycle | 25% |
| Payload & 3 App States + Deep Link | 15% |
| UI & UX | 10% |
| Testing | 10% |
| Code Quality | 10% |
| Responsible AI Usage | 10% |
| **Total** | **100%** |

---

## Checklist Before Submit

### Auth
- [ ] Login route guard works
- [ ] Tokens only in secure storage
- [ ] 401 → refresh once → retry / logout

### FCM
- [ ] Permission requested explicitly
- [ ] onTokenRefresh delivered to backend
- [ ] 3 app states tested + evidence table
- [ ] Topics for broadcast, tokens for personal

### AI
- [ ] Prompt + manual fixes recorded
- [ ] Able to explain the lifecycle without AI

---

# Exit Ticket

Answer before class ends:

### 1. What is the difference between:

```text
ID token
vs
Access token vs Refresh token
```

### 2. What is the role of:

```text
onTokenRefresh
vs
getInitialMessage
```

### 3. When to use a topic vs a device token? Give campus examples.

### 4. Which part of the AI draft did you reject, and why?

---

# Key Takeaways

## Auth

> Secure tokens + automatic refresh + route guard.

## FCM

> Permission → token → backend → push → tap → route.

## App States

> Foreground manual, background automatic, terminated via initial message.

## AI

> AI drafts the FCM code.
> **Developers prove its behavior.**

---

# Next Week

## Week 7: Clean Architecture

```text
Campus Notify (working)
   ↓
Split layers: presentation / domain / data
   ↓
Repository + UseCase + DI
   ↓
Project ready for production scale
```

### Goal

> An app that not only runs, but is professionally structured.

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

## Don't just be able to log in.

### Be a developer who knows who logged in and where the message goes.

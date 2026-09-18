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

# Week 4
## Networking & REST API

**Mobile Programming**

Flutter • Dio • REST API • JSON

> From local data → apps that communicate with a backend

---

# Learning Outcomes

After this session, students will be able to:

1. Explain HTTP, REST API, and JSON concepts.
2. Create data models and `fromJson`/`toJson` serialization.
3. Implement API requests using `Dio`.
4. Apply the basic repository pattern for the data layer.
5. Handle loading, data, error, and empty states.
6. Implement basic pagination.
7. Verify AI-generated networking code.

---

# Why Networking?

Week 3 apps stored data locally.

```text
Without backend:
UI ←→ Provider ←→ Memory
```

Real apps need server data:

```text
With backend:
Flutter → Dio → REST API
  → Database → Back as JSON
```

**Goal:** the UI never talks directly to the internet, but through a repository.

---

# Mini Poll

### If a product API takes 5 seconds...

What should the user see?

**A.** Blank screen with no info  
**B.** Frozen / crashing app  
**C.** Clear loading indicator  
**D.** Ask AI to speed up the internet

### Discussion

> What is wrong with calling `http.get()` directly inside `build()`?

---

# Part 1
## HTTP & REST API

# What Is HTTP?

HTTP is a request–response protocol.

```text
Client (Flutter)
  │  GET /products
  ↓
Server (REST API)
  │  200 OK + JSON
  ↓
Client displays data
```

Main methods:

| Method | Purpose |
|---|---|
| GET | Read data |
| POST | Create data |
| PUT/PATCH | Update data |
| DELETE | Delete data |

---

# What Is REST API?

REST exposes resources through URLs.

```text
GET    /products       → product list
GET    /products/101   → product detail
POST   /products       → add product
PUT    /products/101   → update product
DELETE /products/101   → delete product
```

Responses use status codes:

```text
200 OK
201 Created
400 Bad Request
401 Unauthorized
404 Not Found
500 Server Error
```

> The frontend must handle every status, not only 200.

---

# What Is JSON?

JSON is the text data format sent by APIs.

```json
{
  "id": 101,
  "title": "Milk Coffee",
  "price": 18000,
  "inStock": true
}
```

In Dart it becomes `Map<String, dynamic>`:

```dart
final map = jsonDecode(responseBody);
final title = map['title'] as String;
```

Practical rules:

- keys are `String`
- values can be `String`, `num`, `bool`, `List`, `Map`, `null`
- always validate types before use

---

# Part 2
## Models & Serialization

---

# Why Do We Need Models?

Without models, code is full of raw strings:

```dart
Text(item['titel'].toString()) // typo = null
```

With models, structure is explicit and safe:

```dart
Text(product.title)
```

```text
JSON (Map)
   ↓ fromJson
Product (Object)
   ↓ toJson
JSON (Map)
```

**Principle:** JSON parsing happens in only one place — the model.

# Example Product Model

```dart
class Product {
  final int id;
  final String title;
  final double price;

  const Product({
    required this.id,
    required this.title,
    required this.price,
  });

  factory Product.fromJson(
    Map<String, dynamic> json,
  ) {
    return Product(
      id: json['id'] as int,
      title: json['title'] as String,
      price: (json['price'] as num)
          .toDouble(),
    );
  }
}
```

> All casting and conversion live here, not in the UI.

---

# Null Safety & Optional Fields

Real APIs are often inconsistent.

```dart
factory Product.fromJson(
  Map<String, dynamic> json,
) {
  return Product(
    id: json['id'] as int? ?? 0,
    title:
        json['title'] as String? ??
        'Unnamed',
    price: (json['price'] as num?)
        ?.toDouble() ?? 0.0,
  );
}
```

Rules:

- use `as String?`, not `as String`, for risky fields
- provide sensible defaults
- never let `null` explode in the UI

### Question

> What is the best default when `price` is missing: `0.0` or an explicit error?

---

# Manual vs Codegen

### Manual `fromJson`

- fits Week 4 material
- easy to understand
- enough for small models

### Codegen (`json_serializable` / `freezed`)

```dart
@JsonSerializable()
class Product {
  final int id;
  final String title;
}
```

- fits large projects
- reduces boilerplate
- needs `build_runner`

For Week 4, the main focus is:

> Understand manual parsing before using generators.

---

# Part 3
## Dio & Requests

# Why Dio?

The built-in `http` is enough, but `Dio` is more productive.

```text
http              Dio
  │                 │
  get()             + baseUrl
                    + timeout
                    + interceptor
                    + cancel token
                    + FormData
```

Dio advantages:

- centralized base URL
- global timeout
- interceptors (auth, log)
- typed errors via `DioException`
- cleaner query parameters

---

# Dio Configuration

```dart
final dio = Dio(
  BaseOptions(
    baseUrl:
        'https://api.example.com',
    connectTimeout: const Duration(
      seconds: 10,
    ),
    receiveTimeout: const Duration(
      seconds: 10,
    ),
    headers: {
      'Content-Type':
          'application/json',
    },
  ),
);
```

**Note:** all requests share the same configuration.

---

# GET: Fetch Product List

```dart
Future<List<Product>> fetchProducts()
    async {
  final response = await dio.get(
    '/products',
  );

  final list =
      response.data as List;

  return list
      .map(
        (e) => Product.fromJson(
          e as Map<String, dynamic>,
        ),
      )
      .toList();
}
```

Flow:

```text
dio.get → response.data → List
  → fromJson → List<Product>
```

---

# GET with Query & Path

Query parameters:

```dart
await dio.get(
  '/products',
  queryParameters: {
    'category': 'food',
    'page': 1,
  },
);
```

```text
/products?category=food&page=1
```

Path parameter:

```dart
await dio.get('/products/101');
```

> Avoid manual URL string concatenation when `queryParameters` works.

# Error Handling with Dio

Never use an empty `try/catch`.

```dart
try {
  final res =
      await dio.get('/products');
  return parse(res.data);
} on DioException catch (e) {
  switch (e.type) {
    case DioExceptionType
        .connectionTimeout:
      throw const NetworkFailure(
        'Timeout. Check connection.',
      );
    case DioExceptionType.badResponse:
      throw ServerFailure(
        'Server: ${e.response?.statusCode}',
      );
    default:
      throw const NetworkFailure(
        'Failed to load data.',
      );
  }
}
```

### Remember

> User-facing messages ≠ technical logs for developers.

---

# Error Types You Must Handle

```text
No Internet        → "No connection"
Timeout            → "Request timeout"
401 Unauthorized   → "Session expired, login again"
404 Not Found      → "Data not found"
500 Server Error   → "Server problem"
Parsing Error      → "Invalid data format"
```

Status check example:

```dart
if (e.response?.statusCode == 401) {
  // refresh token / redirect to login
}
```

### Discussion

> When is automatic retry right, and when should we show a "Retry" button?

---

# Part 4
## Basic Repository Pattern

---

# What Is a Repository?

A repository separates UI from data sources.

```text
UI (Widget)
   ↓ calls
Provider (Riverpod)
   ↓ uses
Repository
   ↓ uses
Dio / Data Source
   ↓
REST API
```

The UI does not know:

- base URL
- Dio
- JSON
- status codes

The UI only knows:

```dart
Future<List<Product>> getProducts()
```

---

# Repository Example

```dart
class ProductRepository {
  final Dio dio;

  ProductRepository(this.dio);

  Future<List<Product>> getProducts()
      async {
    final response =
        await dio.get('/products');

    final list =
        response.data['data'] as List;

    return list
        .map(
          (e) => Product.fromJson(e),
        )
        .toList();
  }
}
```

Benefits:

- centralized API logic
- easy to swap data sources
- easy to mock for testing

# Repository + Riverpod

```dart
final dioProvider = Provider<Dio>(
  (ref) => Dio(
    BaseOptions(
      baseUrl:
          'https://api.example.com',
    ),
  ),
);

final productRepoProvider =
    Provider<ProductRepository>(
  (ref) => ProductRepository(
    ref.watch(dioProvider),
  ),
);

final productsProvider =
    FutureProvider<List<Product>>(
  (ref) => ref
      .watch(productRepoProvider)
      .getProducts(),
);
```

```text
Widget → watch(productsProvider)
  → Repository → Dio → API
```

---

# Part 5
## UI States: Loading, Error, Empty

---

# Four Mandatory States

Every API screen must cover 4 conditions:

```text
Loading → shimmer / spinner
Data    → product list
Empty   → "No products yet"
Error   → message + retry button
```

Never leave users guessing:

- white screen = bug or loading?
- empty list = error or truly empty?

---

# `AsyncValue.when` Example

```dart
final state =
    ref.watch(productsProvider);

return state.when(
  loading: () => const Center(
    child:
        CircularProgressIndicator(),
  ),
  data: (products) {
    if (products.isEmpty) {
      return const Center(
        child: Text(
          'No products yet.',
        ),
      );
    }
    return ProductList(products);
  },
  error: (e, _) => ErrorView(
    message: '$e',
    onRetry: () => ref.invalidate(
      productsProvider,
    ),
  ),
);
```

---

# Basic Pagination

Large APIs are never sent at once.

```text
page=1 (10 items) → display
scroll down
page=2 (10 items) → append
...
```

Concept:

```dart
await dio.get(
  '/products',
  queryParameters: {
    'page': page,
    'limit': 10,
  },
);
```

Pagination states:

```text
initial → loading more → loaded
                    ↘ error
```

> For Week 4: understand `page` + `limit` + list append.

# Case Study
## Product List from API

```text
ProductListPage
   ↓ watch(productsProvider)
Repository.getProducts()
   ↓ dio.get('/products')
API → JSON
   ↓ fromJson
List<Product> → UI
```

Conditions to demo:

```text
success → show list
empty   → empty view
timeout → error + retry
offline → connection message
```

---

# Anti-Patterns

Avoid:

### 1. Calling Dio in `build()`

```text
build() → GET → rebuild → GET → loop
```

### 2. Parsing JSON in the UI

All `json['...']` belongs in models/repositories.

### 3. `try/catch` that swallows errors

```dart
catch (e) {} // user learns nothing
```

### 4. No timeout

Requests hang forever when the server dies.

---

# AI Lab
## Use AI as a Co-Developer

Example prompt:

```text
I am building a Flutter app with Dio + Riverpod.

Endpoint: GET https://api.example.com/products
Response: { "data": [{ "id": 1, "title": "...", "price": 10000 }] }

Create:
1. Null-safe Product model + fromJson
2. ProductRepository with Dio
3. FutureProvider + UI with loading/error/empty/retry

Explain the reasoning and risks of each part.
```

### Do not stop at the AI output.

---

# AI Verification Challenge

After AI generates the code:

1. **Read** — understand model, repository, provider.
2. **Verify** — check base URL, types, null handling.
3. **Run** — turn off the internet, try timeout, try 404.
4. **Test** — empty response, missing fields, server errors.
5. **Refactor** — move parsing to the model, clean error messages.
6. **Explain** — explain the flow without AI.

### Minimum tests

- airplane mode
- field `price` = `null`
- `data` = `[]`
- status 500
- retry after error

---

# Group Activity

## Code Review — 15 Minutes

Find at least:

- 2 parsing bugs
- 2 error-handling issues
- 1 architecture issue (API logic in UI)
- 1 UI-state issue
- 1 improvement to the AI-generated code

Then answer:

> **Is this repository layer production-ready? Why or why not?**

---

# Quick Quiz

### 1. What is the repository pattern for?

A. Drawing UI  
B. Separating UI from data sources  
C. Replacing Flutter  
D. Speeding up the internet

### 2. `DioExceptionType.badResponse` means?

A. No error  
B. Server responded with an error status  
C. JSON is always correct  
D. The app must close

### 3. Pagination uses?

A. `page` + `limit`  
B. One giant request  
C. Screenshots  
D. Hardcoded data

---

# Quiz Answers

### 1 → B

Repositories separate UI from Dio/JSON/API.

### 2 → B

`badResponse` = the server answered, but the status is an error (e.g. 404/500).

### 3 → A

```text
?page=1&limit=10
→ display → page++ → append
```

---

# AI Challenge

Use AI to create the **initial version**:

> "Create a repository + provider + UI for a product catalog app from a dummy REST API with loading, empty, error, retry, and basic pagination."

Students must then:

1. understand the code
2. run the code
3. find at least 2 weaknesses
4. fix error handling
5. add their own feature (search/filter)
6. document manual changes

---

# Deliverables

## Repository

```text
week-04-networking-rest-api/
├── lib/
├── test/
├── README.md
└── screenshots/
```

README must contain at least:

- app description & API used
- models/repositories/providers used
- loading/data/empty/error screenshots
- how to run
- AI tools used
- main prompt
- manual changes
- issues found and testing results

---

# Week 4 Rubric

| Component | Weight |
|---|---:|
| Model & Serialization | 20% |
| Dio & Repository | 25% |
| State: Loading/Error/Empty | 15% |
| UI & UX | 10% |
| Testing | 10% |
| Code Quality | 10% |
| Responsible AI Usage | 10% |
| **Total** | **100%** |

---

## Pre-Submission Checklist

### Data
- [ ] Null-safe `fromJson` model
- [ ] Repository separates UI from Dio
- [ ] Timeout configured
- [ ] Basic pagination understood

### UI
- [ ] Loading / Data / Empty / Error render correctly
- [ ] Retry button works
- [ ] No requests in `build()`

### AI
- [ ] Prompt documented & AI output verified
- [ ] Manual changes present
- [ ] Code understood by student

---

# Exit Ticket

Answer before the class ends:

### 1. What is the difference between:

```text
http
vs
Dio
```

### 2. What are the roles of:

```text
Model
vs
Repository
```

### 3. Name the 4 mandatory API screen states with UI examples.

### 4. Why must AI error handling still be tested manually?

---

# Key Takeaways

## Networking

> Dio makes HTTP requests more structured.

## Architecture

> Repositories separate UI from data sources.

## UI State

> Loading/Data/Empty/Error must be explicit.

## AI

> AI drafts the initial repository.  
> **Developers test error handling and fix it.**

---

# Next Week

## Week 5: Local Storage & Offline First

```text
REST API
   ↓
Repository
   ↓
Cache (Hive / SQLite)
   ↓
UI still works offline
```

### Target

> Apps stay useful without internet through caching and basic sync.

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

## Don't just call APIs.

### Become a developer who understands the data flow being called.

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

# Week 11
## Performance Optimization

**Mobile Programming**

Flutter DevTools | Profiling | Widget Rebuild | Lazy Loading | Caching

> From a working app to a fast, efficient, production-ready app

---

# Learning Objectives

After this session, you will be able to:

1. Use Flutter DevTools for CPU and memory profiling.
2. Identify and reduce unnecessary widget rebuilds.
3. Apply lazy loading, pagination, and image optimization.
4. Optimize networking with caching and offline-first strategies.
5. Fix performance bottlenecks in an existing application.

---

# Why Performance Optimization Matters

Modern mobile apps must be responsive, battery-efficient, and smooth across device specifications. Poor performance leads to:

- **Frame drops (jank)** — UI not smooth at 60/120 FPS
- **High memory usage** — app killed by the OS
- **Battery drain** — CPU/GPU overworked
- **Poor user experience** — uninstalls and low ratings

> Performance is not a nice-to-have feature; it is a baseline requirement for professional apps.

---

# Flutter Rendering Pipeline

```text
UI Thread (Dart)          Raster Thread (GPU)
+------------------+      +------------------+
|  Build Widget    |      |                  |
|  Layout          | ---> |  Composite       |
|  Paint           |      |  Rasterize       |
+------------------+      +------------------+
      |                          |
      v                          v
  Frame Budget: 16.67 ms (60 FPS) / 8.33 ms (120 FPS)
```

- **UI Thread**: Dart code, build, layout, paint — must complete in < 16 ms
- **Raster Thread**: GPU rendering — can become a bottleneck with large or complex images
- **Jank**: Frame exceeds budget → frame drop occurs

---

# Main Optimization Categories

| Category | Focus | Tools |
|----------|-------|-------|
| CPU/Build | Widget rebuild, excessive layout, heavy computation | DevTools Performance, Timeline |
| Memory | Leaks, large images, unbounded lists | DevTools Memory, Heap Snapshot |
| Network | Repeated requests, large payloads, no cache | DevTools Network, Dio Interceptor |
| Images | Wrong resolution, inefficient format | DevTools Memory, cached_network_image |

---

# Flutter DevTools: CPU Profiling

```bash
# From project terminal
flutter run --profile
# Or
flutter run -d <device_id> --profile

# Open DevTools in browser (auto-opens or)
# http://127.0.0.1:9100
```

**Note**: Use `--profile` mode (not `--debug` or `--release`) for accurate profiling. Debug mode has significant overhead.

---

# Performance Tab (CPU Profiling)

- **Flame Chart**: Call stack visualization per frame — look for red frames (jank)
- **Timeline Events**: Build, layout, paint, GC, HTTP events
- **Frame Chart**: Bar per frame — green (<16 ms), yellow (16–32 ms), red (>32 ms)
- **Track: UI Thread** — find slow `build`, `layout`, `paint`
- **Track: Raster Thread** — find slow `rasterize`, `image decode`

---

# Memory Tab (Memory Profiling)

- **Heap Size**: Total allocated memory
- **Used/External**: Dart memory + native (images, textures)
- **GC Events**: Garbage collection — frequent GC = memory pressure
- **Heap Snapshot**: Take snapshot → filter class → find unneeded instances
- **Leak Detection**: Compare snapshots before/after navigation

---

# Profiling Workflow

1. Run the app with `flutter run --profile`
2. Open DevTools → Performance tab
3. Click **Record** → use the app normally (scroll, navigate, load data)
4. Click **Stop** → analyze red/yellow frames
5. Click a problematic frame → view Flame Chart → identify the top slow function
6. Fix the code → repeat profiling to verify

---

# Causes of Excessive Rebuilds

- `setState` on a large root widget
- Provider/Riverpod listeners on widgets that do not need them
- `ListView.builder` without `const` items
- Anonymous functions in the build method (new closure every frame)
- StatefulWidget that should be a StatelessWidget

---

# Optimization Technique: const Constructor

```dart
// Before: rebuilds every parent rebuild
Widget build(BuildContext context) {
  return Column(
    children: [
      Text('Title'),           // not const
      Icon(Icons.star),        // not const
      MyCustomWidget(),        // not const
    ],
  );
}

// After: only rebuilds if parameters change
Widget build(BuildContext context) {
  return const Column(
    children: [
      Text('Title'),
      Icon(Icons.star),
      MyCustomWidget(),
    ],
  );
}
```

---

# Optimization Technique: Extract Widgets to Separate Classes

```dart
// Before: everything in one build method
class MyPage extends StatelessWidget {
  Widget build(BuildContext context) {
    return Column(
      children: List.generate(100, (i) => Text('Item $i')), // rebuilds all
    );
  }
}

// After: items separate, can be const
class MyPage extends StatelessWidget {
  Widget build(BuildContext context) {
    return Column(
      children: List.generate(100, (i) => const MyItem()), // only MyItem rebuilds
    );
  }
}

class MyItem extends StatelessWidget {
  const MyItem();
  Widget build(BuildContext context) => const Text('Item');
}
```

---

# Optimization Technique: RepaintBoundary

```dart
// Only this area repaints when changed
RepaintBoundary(
  child: ExpensiveWidget(),
)
```

Use for: charts, complex animations, areas that frequently change visually but do not need layout rebuild.

---

# Optimization Technique: Riverpod select

```dart
// Only rebuilds when value.count changes, not the entire state
final count = ref.watch(counterProvider.select((s) => s.count));

// Or use Consumer with a specific builder
Consumer(
  builder: (context, ref, child) {
    final value = ref.watch(provider);
    return Text('$value');
  },
)
```

---

# Optimization Technique: Avoid Anonymous Functions in Build

```dart
// Bad: new closure every build
onPressed: () => doSomething()

// Good: method reference or tear-off
onPressed: doSomething

// Or use a pre-defined callback
onPressed: _handlePress
```

---

# Lazy Loading with ListView.builder

`ListView.builder` only builds visible items (viewport). Ensure:

- Use `ListView.builder` not `ListView(children: [...])`
- Item widget uses `const` constructor
- Avoid `Expanded` or `Flexible` inside items unless needed

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

# Image Optimization

| Technique | Description | Package |
|-----------|-------------|---------|
| Server-side resize | Request appropriate size (thumbnail), not full-res | Backend/CDN |
| Modern formats | WebP, AVIF smaller than JPEG/PNG | Server/CDN |
| Local caching | Store images on disk, avoid re-download | `cached_network_image` |
| Placeholders | Show blur/low-res first, then swap | `fade_in_image`, `cached_network_image` |
| Precache | Load images before needed (e.g., next page) | `precacheImage` |
| Memcache limits | Limit memory cache size | `PaintingBinding.instance.imageCache` |

---

# Example cached_network_image

```dart
CachedNetworkImage(
  imageUrl: item.imageUrl,
  placeholder: (context, url) => Shimmer.fromColors(
    baseColor: Colors.grey[300]!,
    highlightColor: Colors.grey[100]!,
    child: Container(color: Colors.white),
  ),
  errorWidget: (context, url, error) => const Icon(Icons.error),
  memCacheWidth: 300,  // decode at display size, not full-res
  memCacheHeight: 200,
  fadeInDuration: const Duration(milliseconds: 200),
)
```

---

# Network Caching Strategies

| Strategy | Best For | Implementation |
|----------|----------|----------------|
| Cache-First | Rarely changing data (config, master data) | Read cache → if empty, fetch API → store cache |
| Network-First | Critical real-time data (chat, prices, stock) | Fetch API → store cache → fallback to cache on failure |
| Stale-While-Revalidate | Semi-real-time data (feed, lists) | Show cache → fetch background → update UI |
| Cache-Only (Offline) | Full offline mode | Only read from local DB (Hive/SQLite/Drift) |

---

# Repository with Cache (Stale-While-Revalidate)

```dart
class ItemRepository {
  final Dio _dio;
  final Box<Item> _localBox; // Hive

  ItemRepository(this._dio, this._localBox);

  Future<List<Item>> getItems() async {
    // 1. Show cache immediately (if exists)
    final cached = _localBox.values.toList();
    if (cached.isNotEmpty) {
      _emitCached(cached);
    }

    // 2. Fetch fresh data in background
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
      // If error and no cache, throw error
      if (cached.isEmpty) rethrow;
      return cached; // Return cache as fallback
    }
  }
}
```

---

# Dio Interceptor for Retry and Cache

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

# Compression and Small Payloads

- Use `gzip` / `brotli` on server (header `Accept-Encoding`)
- Request only needed fields (GraphQL / query param `?fields=id,name,thumb`)
- Batch requests when possible
- Use ETag / If-None-Match for cache validation

---

# Practical: Optimizing Real App Performance

## Step 1: Baseline Profiling
1. Open your Week 7–10 project (for example, `campus_notify`)
2. Run `flutter run --profile`
3. Open DevTools → Performance tab → click **Record**
4. Perform scenarios: open list, fast scroll, open detail, back, pull-to-refresh
5. Click **Stop** → note: red/yellow frame count, average frame time, top functions in Flame Chart
6. Save Flame Chart screenshot as `screenshots/before-profile.png`

## Step 2: Identify Issues
Based on profiling, identify at least 3 issues:
- Excessive widget rebuilds (look for repeated `build` in Flame Chart)
- Large images not resized (look for `image decode` in Raster Thread)
- Repeated API requests without cache (look for repeated `HTTP` events)
- Lists not using builder (look for slow `Layout` during scroll)
- Memory leaks (Heap keeps growing in Memory tab during back-and-forth navigation)

Document findings in `docs/performance-findings.md`

---

# Practical: Apply Optimizations

Select and apply at least 3 optimizations:

1. **Const constructors**: Add `const` to static widgets
2. **Extract widgets**: Break large widgets into small `const` widgets
3. **RepaintBoundary**: Wrap frequently repainting areas (charts, animations)
4. **Riverpod select**: Replace `ref.watch(provider)` with `ref.watch(provider.select(...))`
5. **ListView.builder**: Ensure all lists use builder
6. **CachedNetworkImage**: Replace `Image.network` with `CachedNetworkImage` + `memCacheWidth/Height`
7. **Pagination**: Add infinite scroll to long lists
8. **Repository cache**: Add stale-while-revalidate to repository

---

# Practical: Verify with Profiling

1. Run `flutter run --profile` again
2. Record the same scenarios
3. Compare: fewer red/yellow frames, lower average frame time
4. Save screenshot as `screenshots/after-profile.png`

---

# Memory Profiling

1. Open Memory tab in DevTools
2. Take a **Heap Snapshot** on the list page
3. Navigate to detail → back → repeat 5 times
4. Take another snapshot → compare: any accumulating objects?
5. If a leak is found: find unreleased references (controllers, listeners, stream subscriptions)
6. Fix with proper `dispose()`

---

<!-- _class: fit -->

# Quick Quiz (1/2)

### 1. Which mode should be used for accurate profiling?

A. `flutter run --debug`  
B. `flutter run --profile`  
C. `flutter run --release`  
D. `flutter run --test`

---

<!-- _class: fit -->

# Quick Quiz (2/2)

### 2. Which technique does NOT reduce widget rebuilds?

A. Using `const` constructor  
B. Extracting widgets to separate classes  
C. Using `setState` on root widget  
D. Riverpod `select`

---

<!-- _class: fit -->

# Quiz Answers

### 1 -> B

```text
--profile mode provides near-release performance
without debug overhead, suitable for accurate profiling
```

### 2 -> C

```text
setState on root widget triggers rebuild of entire subtree
Use const, extract widgets, or Riverpod select instead
```

---

<!-- _class: fit -->

# Expected Deliverables

## Repository

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

README must include:

- Baseline metrics (frame time, jank count, memory usage)
- Applied optimizations + rationale
- `flutter analyze` + `flutter test`
- Before/after profiling screenshots
- Verification results

---

<!-- _class: fit -->

# Week 11 Assessment

| Component | Weight |
|-----------|--------|
| Profiling and Issue Identification | 20% |
| Optimization Implementation (min. 3 techniques) | 30% |
| Result Verification (before/after metrics) | 20% |
| Image and Network Optimization | 15% |
| Documentation and Testing | 10% |
| Presentation and Reflection | 5% |
| **Total** | **100%** |

---

<!-- _class: fit -->

# Pre-Submission Checklist

### Profiling

- [ ] Baseline profiling done with `flutter run --profile`
- [ ] At least 3 performance issues identified
- [ ] Flame Chart before/after screenshots available

### Optimizations

- [ ] At least 3 optimization techniques applied
- [ ] Const constructors added to static widgets
- [ ] Widgets extracted to separate classes
- [ ] Riverpod select used for granular rebuilds
- [ ] CachedNetworkImage with memCacheWidth/Height applied
- [ ] Pagination/infinite scroll added (if long list)
- [ ] Repository cache (stale-while-revalidate) implemented

### Memory

- [ ] Memory profiling done (Heap Snapshot before/after navigation)
- [ ] No memory leaks (objects not accumulating)
- [ ] All controllers/listeners/subscriptions properly disposed

### Documentation

- [ ] `docs/performance-findings.md` complete
- [ ] `docs/performance-report.md` complete (baseline, optimizations, results)
- [ ] README explains features, how to test, and results
- [ ] `flutter analyze` clean
- [ ] At least one widget test for optimized feature

---

<!-- _class: fit -->

# Exit Ticket

Answer before class ends:

### 1. Which optimization had the biggest impact and why?

### 2. How did you verify optimizations actually work (not placebo)?

### 3. What trade-offs did you face (for example, code complexity vs performance)?

### 4. When should premature optimization be avoided?

---

<!-- _class: fit -->

# Key Takeaways

## Profiling

> Measure first, optimize later. Don't guess bottlenecks.

## Rebuilds

> `const` + extract widgets + `select` = minimal rebuilds.

## Images

> Resize on server, cache on client, decode at display size.

## Network

> Cache-first for static data, stale-while-revalidate for feeds, network-first for critical.

## Verification

> Compare before/after metrics with identical scenarios.

---

<!-- _class: fit -->

# Next Week

## Week 12: Testing & Quality Assurance

```text
App optimized
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

### Goal

> Ensure app quality maintained through comprehensive automated testing.

---

<!-- _class: fit -->

# Build -> Verify -> Explain

## Mobile Learning Principles

1. AI suggests answers
2. Developer verifies
3. Test to confirm
4. Fix if not accurate
5. Explain reasoning

## Don't just use tools.

### Be a developer who can explain optimization decisions.

- Profiling is evidence, not guessing
- Every optimization must have technical rationale
- Trade-offs must be documented and justified

---

# References

- [Flutter DevTools Overview](https://docs.flutter.dev/tools/devtools/overview)
- [Flutter UI Performance](https://docs.flutter.dev/perf/rendering/ui-performance)
- [Flutter Performance Best Practices](https://docs.flutter.dev/perf/best-practices)
- [cached_network_image](https://pub.dev/packages/cached_network_image)
- [Dio HTTP Client](https://pub.dev/packages/dio)
- [Learn Dart in Y Minutes](https://learnxinyminutes.com/dart/)
- [Dart Language Tour](https://dart.dev/guides/language/language-tour)
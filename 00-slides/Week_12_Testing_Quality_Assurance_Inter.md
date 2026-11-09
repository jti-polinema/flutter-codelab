---
marp: true
theme: default
paginate: true
size: 16:9
---

<style>
section {
  font-size: 19px;
  padding: 66px 70px 62px 70px;
  color: #1b1b1b;
  background:
    linear-gradient(90deg, #f7941d 0%, #fdb913 100%) 0 0 / 100% 10px no-repeat,
    linear-gradient(155deg, #ffffff 55%, #eef0f2 100%);
  background-repeat: no-repeat;
}
section h1 { font-size: 32px; color: #111; margin: 0.3em 0; border-bottom: none; }
section h2 { font-size: 25px; color: #333; margin: 0.3em 0; }
section h3 { font-size: 20px; color: #1b2a4a; margin: 0.25em 0; }
section p, section ul, section ol { margin: 0.25em 0; }
section ul, section ol { padding-left: 1.25em; }
section li { margin: 0.16em 0; }
section table { font-size: 16px; }
section pre, section code { font-size: 14px; }
section pre { margin: 0.25em 0; line-height: 1.25; }
section blockquote { font-size: 17px; }
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
section.lead { justify-content: center; text-align: left; padding-left: 80px; }
section.lead h1 { font-size: 44px; font-weight: 800; color: #111; }
section.lead h2 { font-size: 30px; font-weight: 600; color: #666; margin-top: 0; }
section.lead h3 { font-size: 18px; font-weight: 500; color: #222; margin-top: 16px; }
section.lead p { color: #333; }
section.fit { display: flex; flex-direction: column; justify-content: center; }
section.fit h1 { font-size: 28px; margin: 0.2em 0; }
section.fit h2 { font-size: 23px; margin: 0.2em 0; }
section.fit h3 { font-size: 19px; margin: 0.2em 0; }
section.fit p, section.fit ul, section.fit ol { margin: 0.2em 0; }
section.fit li { margin: 0.12em 0; }
section.fit pre { margin: 0.2em 0; line-height: 1.18; }
section.fit pre, section.fit code { font-size: 13px; }
section.fit blockquote { font-size: 16px; margin: 0.2em 0; }
section.fit table { font-size: 15px; }
</style>

<!-- _class: lead -->

# Week 12
## Testing and Quality Assurance

**Mobile Programming**

Unit Testing | Widget Testing | Integration Testing | Testable Architecture

> Write tests that help us change code with greater confidence.

---

<!-- _class: fit -->

# Learning Objectives

By the end of this session, you should be able to:

1. Choose the right type of test for a feature.
2. Write unit tests for business logic and edge cases.
3. Check Flutter widget output and interactions.
4. Separate dependencies to make code easier to test.
5. Test an app flow and critically review code coverage.

---

<!-- _class: fit -->

# Why Test an App?

- A small change can quietly affect another feature.
- Tests provide evidence that important scenarios still work.
- Good tests document the behavior we expect.
- Automation makes repeated checks faster and more consistent.

> Testing cannot prove an app is perfect. It helps us catch issues earlier and understand the impact of a change.

---

<!-- _class: fit -->

# Three Types of Tests

| Type | Focus | Example |
|---|---|---|
| Unit | An isolated function or object | Validate a task title |
| Widget | UI and widget interaction | Form rejects empty input |
| Integration | A flow across app components | Add a task and see it in the list |

Start with fast unit tests. Add widget and integration tests for behavior involving UI or multiple parts of the app.

---

<!-- _class: fit -->

# Code That Is Easy to Test

- Keep business rules out of widgets.
- Put data access behind a repository contract.
- Provide dependencies through constructors.
- Replace real services with fakes in tests.

```text
Widget → Service / use case → Repository → API
                           ↑
                    Fake repository in tests
```

This separation makes tests focused and independent of the network.

---

<!-- _class: fit -->

# Unit Tests: Check the Rule

```dart
bool isValidTaskTitle(String value) {
  return value.trim().isNotEmpty;
}
```

Scenarios to check:
- A title with text is accepted.
- Empty text is rejected.
- Whitespace-only text is rejected.

Test structure: **Arrange** (prepare), **Act** (run), **Assert** (check).

---

<!-- _class: fit -->

# Write Useful Tests

```dart
test('rejects a whitespace-only title', () {
  expect(isValidTaskTitle('   '), isFalse);
});
```

- Name the behavior being checked.
- Cover relevant normal, boundary, and failure cases.
- Make sure the test would fail if the behavior broke.
- Avoid tests that run code without checking a result.

Run the full suite with `flutter test`.

---

<!-- _class: fit -->

# Testing Async Code

```dart
abstract interface class TaskRepository {
  Future<List<String>> getTasks();
}

class TaskService {
  TaskService(this.repository);
  final TaskRepository repository;

  Future<List<String>> loadTasks() => repository.getTasks();
}
```

A repository abstraction lets tests replace the data source.

---

<!-- _class: fit -->

# Use a Fake, Not a Live API

```dart
class FakeTaskRepository implements TaskRepository {
  FakeTaskRepository(this.tasks);
  final List<String> tasks;

  @override
  Future<List<String>> getTasks() async => tasks;
}
```

- Test results do not depend on the internet or server.
- Success and error scenarios are under your control.
- Tests are faster and easier to repeat.
- Make sure errors are not silently treated as empty data.

---

<!-- _class: fit -->

# Widget Testing

```dart
testWidgets('shows the page title', (tester) async {
  await tester.pumpWidget(
    const MaterialApp(
      home: Scaffold(body: Text('Task list')),
    ),
  );

  expect(find.text('Task list'), findsOneWidget);
});
```

`pumpWidget` renders the UI. `find` locates elements, and `expect` checks the result.

---

<!-- _class: fit -->

# Check User Interactions

```dart
await tester.enterText(
  find.byKey(const Key('task-title-field')),
  'Read the material',
);
await tester.tap(find.byKey(const Key('save-task-button')));
await tester.pumpAndSettle();

expect(find.text('Read the material'), findsOneWidget);
```

Add a `Key` when a control is hard to find by text or widget type. Also check loading, empty data, invalid input, and error states.

---

<!-- _class: fit -->

# Integration Testing

Test an important flow on an emulator or device:

1. Open the app and wait for the UI.
2. Enter a task title.
3. Tap the save button.
4. Confirm the task appears in the list.

```bash
flutter test integration_test/app_test.dart -d <device_id>
```

Choose a few high-risk flows. Integration tests take longer than unit and widget tests.

---

<!-- _class: fit -->

# Regression and Code Coverage

- When fixing a bug, add a test that fails before the fix.
- Rerun the suite with `flutter test`.
- Generate a coverage report with `flutter test --coverage`.
- Review important branches and uncovered edge cases.

> Coverage percentage is not a quality score by itself. What matters is whether important behavior is actually tested.

---

<!-- _class: fit -->

# Lab: Test a Task List Feature

1. Write success, invalid-input, and failure scenarios.
2. Add unit tests for validation or a business rule.
3. Isolate data access and create a fake repository.
4. Check the UI and interactions in a widget test.
5. Test one key flow with an integration test.
6. Run `flutter test` and `flutter analyze`, then document the results.

Choose tests for their risk and value, not their line count.

---

<!-- _class: fit -->

# Review Tests Carefully

Before calling the work done, ask:

- Would a test fail if the feature actually broke?
- Have important edge cases and errors been checked?
- Are the tests independent of network access and execution order?
- Can a teammate understand what each test is checking?

AI can help suggest ideas, but you are responsible for reviewing the tests and results.

---

<!-- _class: fit -->

# References and Next Steps

- Flutter testing: `docs.flutter.dev/testing/overview`
- Widget testing: `docs.flutter.dev/cookbook/testing/widget/introduction`
- Integration testing: `docs.flutter.dev/testing/integration-tests`
- Dart test package: `pub.dev/packages/test`

**After this session:** add tests for a key feature in your project and document the scenarios and results in `README.md`.

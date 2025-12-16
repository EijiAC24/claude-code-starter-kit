# Dart & Flutter Style Guidelines

> Based on Effective Dart, Flutter team style guide, and best practices for 2025.

## Trigger Keywords

- Dart, Flutter, dart, flutter
- Widget, StatelessWidget, StatefulWidget
- pubspec, .dart

---

## Naming Conventions

| Type | Convention | Example |
|------|------------|---------|
| Classes, Enums | UpperCamelCase | `UserProfile`, `HttpClient` |
| Files, Packages | lowercase_with_underscores | `user_profile.dart` |
| Variables | lowerCamelCase | `userName`, `itemCount` |
| Constants | lowerCamelCase | `defaultTimeout` |
| Private | _lowerCamelCase | `_privateField` |

### Boolean: Use `is_`, `has_`, `can_`, `should_` prefixes

---

## Code Formatting

```bash
dart format .
dart format --output=none --set-exit-if-changed .
```

- Max 80 chars/line
- Use trailing commas for better formatting
- Always use braces for multi-line

---

## Import Order

1. `dart:` imports
2. `package:` imports (alphabetized)
3. Relative imports
4. Exports

---

## Type Annotations

```dart
// Public APIs: explicit types
String getUserName(int userId) { ... }

// Local: let Dart infer
var items = <String>[];
final user = User(name: 'John');

// Null safety
String? nullableName;
late String description;
void createUser({required String name}) {}
```

---

## Flutter Best Practices

### Widget Structure

```dart
class MyWidget extends StatelessWidget {
  const MyWidget({super.key, required this.title, this.onTap});

  final String title;
  final VoidCallback? onTap;

  @override
  Widget build(BuildContext context) { ... }
}
```

### Use const

```dart
return const Padding(
  padding: EdgeInsets.all(16),
  child: Text('Hello'),
);
```

### Prefer SizedBox over Container

```dart
const SizedBox(height: 16);  // Not Container(height: 16)
```

### Extract Widgets

```dart
Widget build(BuildContext context) {
  return Column(children: [
    _buildHeader(),
    _buildContent(),
  ]);
}
```

---

## State Management

### StatefulWidget Lifecycle

```dart
class _MyState extends State<MyWidget> {
  late final TextEditingController _controller;

  @override
  void initState() {
    super.initState();
    _controller = TextEditingController();
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }
}
```

### Provider Pattern

```dart
// Model
class CounterModel extends ChangeNotifier {
  int _count = 0;
  int get count => _count;
  void increment() { _count++; notifyListeners(); }
}

// Consume
Consumer<CounterModel>(
  builder: (context, counter, child) => Text('${counter.count}'),
)
```

---

## Async Patterns

```dart
// async/await
Future<User> fetchUser(int id) async {
  try {
    final response = await api.get('/users/$id');
    return User.fromJson(response.data);
  } on DioException catch (e) {
    throw UserFetchException('Failed: ${e.message}');
  }
}

// Parallel execution
await Future.wait([fetchUser(userId), fetchStats()]);
```

---

## Error Handling

### Result Pattern (Dart 3.0+)

```dart
sealed class Result<T> {}
class Success<T> extends Result<T> { final T data; }
class Failure<T> extends Result<T> { final Object error; }

// Usage
switch (result) {
  case Success(:final data): showUser(data);
  case Failure(:final error): showError(error.toString());
}
```

---

## Project Structure

```
lib/
├── main.dart
├── core/          # constants, theme, utils
├── data/          # models, repositories, services
├── presentation/  # screens, widgets, providers
└── l10n/          # localization
```

---

## Linting

```yaml
# analysis_options.yaml
include: package:flutter_lints/flutter.yaml

linter:
  rules:
    - always_declare_return_types
    - prefer_const_constructors
    - prefer_final_fields
    - require_trailing_commas
```

---

## Performance Tips

```dart
// Use const
const MyWidget();

// Avoid rebuilding subtrees
Consumer<Model>(
  builder: (context, model, child) => Column(
    children: [Text(model.title), child!],
  ),
  child: const ExpensiveWidget(),
)

// ListView.builder for long lists
ListView.builder(itemCount: items.length, itemBuilder: ...)

// Dispose controllers
@override
void dispose() {
  _controller.dispose();
  super.dispose();
}
```

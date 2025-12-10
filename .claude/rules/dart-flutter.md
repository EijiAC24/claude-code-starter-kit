---
paths: "**/*.dart", lib/**/*.dart, test/**/*.dart
---

# Dart & Flutter Style Guidelines

> Based on Effective Dart, Flutter team style guide, and best practices for 2025.

## Naming Conventions

| Type | Convention | Example |
|------|------------|---------|
| Classes, Enums, Typedefs | UpperCamelCase | `UserProfile`, `HttpClient` |
| Extensions | UpperCamelCase | `StringExtension` |
| Libraries, Packages | lowercase_with_underscores | `my_package` |
| Files, Directories | lowercase_with_underscores | `user_profile.dart` |
| Variables, Parameters | lowerCamelCase | `userName`, `itemCount` |
| Constants | lowerCamelCase | `defaultTimeout`, `maxRetries` |
| Private members | _lowerCamelCase | `_privateField`, `_internalMethod` |
| Type Parameters | Single uppercase letter or UpperCamelCase | `T`, `E`, `Key`, `Value` |

### Acronyms
```dart
// Two-letter acronyms: keep uppercase
class IOStream {}
var httpRequest = HttpRequest();
var uiHandler = UIHandler();

// Longer acronyms: capitalize like words
class HttpConnection {}  // Not HTTPConnection
var jsonParser = JsonParser();  // Not JSONParser
```

### Boolean Naming
```dart
// Use is_, has_, can_, should_ prefixes
bool isLoading = false;
bool hasPermission = true;
bool canSubmit = false;
bool shouldRefresh = true;

// In classes
class User {
  final bool isActive;
  final bool hasVerifiedEmail;
}
```

---

## Code Formatting

### Use dart format
```bash
# Format single file
dart format lib/main.dart

# Format entire project
dart format .

# Check formatting without changes
dart format --output=none --set-exit-if-changed .
```

### Line Length
- Maximum 80 characters per line
- URLs in comments may exceed this limit

### Curly Braces
```dart
// GOOD: Always use braces for multi-line
if (isLoading) {
  showSpinner();
  disableButton();
}

// GOOD: Single-line if without else can omit braces
if (isLoading) return;

// BAD: Multi-statement without braces
if (isLoading)
  showSpinner();  // Avoid this
```

### Trailing Commas
```dart
// GOOD: Trailing commas for better formatting
Widget build(BuildContext context) {
  return Container(
    padding: const EdgeInsets.all(16),
    child: Column(
      children: [
        Text('Hello'),
        Text('World'),  // Trailing comma
      ],
    ),
  );
}
```

---

## Import Organization

### Order
1. `dart:` imports
2. `package:` imports
3. Relative imports
4. Exports (separate section)

### Example
```dart
// 1. Dart SDK imports
import 'dart:async';
import 'dart:io';

// 2. Package imports (alphabetized)
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';

// 3. Relative imports
import '../models/user.dart';
import '../services/api_service.dart';
import 'widgets/custom_button.dart';

// 4. Exports
export 'src/models.dart';
export 'src/utils.dart';
```

### Import Aliases
```dart
// Use lowercase_with_underscores for prefixes
import 'package:http/http.dart' as http;
import 'dart:math' as math;
```

---

## Type Annotations

### When to Use
```dart
// GOOD: Type annotations for public APIs
String getUserName(int userId) {
  return _users[userId]?.name ?? 'Unknown';
}

// GOOD: Let Dart infer for local variables
var items = <String>[];  // Type is List<String>
final user = User(name: 'John');  // Type is User

// GOOD: Annotate when inference isn't clear
Map<String, List<int>> groupedIds = {};

// BAD: Redundant annotation
String name = 'John';  // Just use: var name = 'John';
```

### Null Safety
```dart
// Non-nullable by default
String name;  // Must be initialized

// Nullable types
String? nullableName;

// Late initialization
late String description;

// Required named parameters
void createUser({required String name, required String email}) {}

// Null-aware operators
String displayName = user?.name ?? 'Anonymous';
user?.save();  // Only calls if user is not null
```

---

## Flutter Best Practices

### Widget Structure
```dart
class MyWidget extends StatelessWidget {
  const MyWidget({
    super.key,
    required this.title,
    this.onTap,
  });

  final String title;
  final VoidCallback? onTap;

  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: onTap,
      child: Text(title),
    );
  }
}
```

### Use const Constructors
```dart
// GOOD: const for immutable widgets
return const Padding(
  padding: EdgeInsets.all(16),
  child: Text('Hello'),
);

// GOOD: const in lists
children: const [
  Icon(Icons.star),
  SizedBox(width: 8),
  Text('Favorite'),
],
```

### Prefer SizedBox over Container
```dart
// GOOD: SizedBox for spacing (const constructor)
const SizedBox(height: 16),
const SizedBox(width: 8),
const SizedBox.shrink(),  // Empty placeholder

// AVOID: Container for simple spacing
Container(height: 16),  // Not const, more overhead
```

### Extract Widgets
```dart
// BAD: Deep nesting
Widget build(BuildContext context) {
  return Scaffold(
    body: Column(
      children: [
        Container(
          child: Row(
            children: [
              // ... deep nesting
            ],
          ),
        ),
      ],
    ),
  );
}

// GOOD: Extract into separate widgets
Widget build(BuildContext context) {
  return Scaffold(
    body: Column(
      children: [
        _buildHeader(),
        _buildContent(),
        _buildFooter(),
      ],
    ),
  );
}

Widget _buildHeader() {
  return Container(/* ... */);
}
```

---

## State Management

### StatefulWidget Lifecycle
```dart
class MyStatefulWidget extends StatefulWidget {
  const MyStatefulWidget({super.key});

  @override
  State<MyStatefulWidget> createState() => _MyStatefulWidgetState();
}

class _MyStatefulWidgetState extends State<MyStatefulWidget> {
  late final TextEditingController _controller;

  @override
  void initState() {
    super.initState();
    _controller = TextEditingController();
    _loadData();
  }

  @override
  void didUpdateWidget(MyStatefulWidget oldWidget) {
    super.didUpdateWidget(oldWidget);
    // Handle widget updates
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return TextField(controller: _controller);
  }

  Future<void> _loadData() async {
    // Load data
  }
}
```

### Provider Pattern
```dart
// Model
class CounterModel extends ChangeNotifier {
  int _count = 0;
  int get count => _count;

  void increment() {
    _count++;
    notifyListeners();
  }
}

// Provide
ChangeNotifierProvider(
  create: (_) => CounterModel(),
  child: MyApp(),
)

// Consume
Consumer<CounterModel>(
  builder: (context, counter, child) {
    return Text('Count: ${counter.count}');
  },
)

// Read without rebuilding
context.read<CounterModel>().increment();
```

---

## Async Patterns

### async/await
```dart
// GOOD: Use async/await
Future<User> fetchUser(int id) async {
  try {
    final response = await api.get('/users/$id');
    return User.fromJson(response.data);
  } on DioException catch (e) {
    throw UserFetchException('Failed to fetch user: ${e.message}');
  }
}

// GOOD: Parallel execution
Future<void> loadDashboard() async {
  final results = await Future.wait([
    fetchUser(userId),
    fetchStats(),
    fetchNotifications(),
  ]);
  // Process results
}
```

### Stream Handling
```dart
// Listen to stream
StreamSubscription<int>? _subscription;

@override
void initState() {
  super.initState();
  _subscription = counterStream.listen(
    (value) => setState(() => _count = value),
    onError: (error) => _handleError(error),
  );
}

@override
void dispose() {
  _subscription?.cancel();
  super.dispose();
}

// StreamBuilder in UI
StreamBuilder<User>(
  stream: userStream,
  builder: (context, snapshot) {
    if (snapshot.hasError) {
      return ErrorWidget(snapshot.error!);
    }
    if (!snapshot.hasData) {
      return const CircularProgressIndicator();
    }
    return UserCard(user: snapshot.data!);
  },
)
```

---

## Error Handling

### Custom Exceptions
```dart
// Define custom exceptions
class AppException implements Exception {
  const AppException(this.message, {this.code});

  final String message;
  final String? code;

  @override
  String toString() => 'AppException: $message (code: $code)';
}

class NetworkException extends AppException {
  const NetworkException(super.message, {super.code});
}

class ValidationException extends AppException {
  const ValidationException(super.message, {this.field, super.code});
  final String? field;
}
```

### Result Pattern
```dart
// Sealed class for results (Dart 3.0+)
sealed class Result<T> {
  const Result();
}

class Success<T> extends Result<T> {
  const Success(this.data);
  final T data;
}

class Failure<T> extends Result<T> {
  const Failure(this.error, {this.stackTrace});
  final Object error;
  final StackTrace? stackTrace;
}

// Usage
Future<Result<User>> fetchUser(int id) async {
  try {
    final user = await api.getUser(id);
    return Success(user);
  } catch (e, stack) {
    return Failure(e, stackTrace: stack);
  }
}

// Handle result
final result = await fetchUser(123);
switch (result) {
  case Success(:final data):
    showUser(data);
  case Failure(:final error):
    showError(error.toString());
}
```

---

## Testing

### Widget Testing
```dart
testWidgets('Counter increments', (WidgetTester tester) async {
  // Arrange
  await tester.pumpWidget(const MyApp());

  // Assert initial state
  expect(find.text('0'), findsOneWidget);
  expect(find.text('1'), findsNothing);

  // Act
  await tester.tap(find.byIcon(Icons.add));
  await tester.pump();

  // Assert
  expect(find.text('0'), findsNothing);
  expect(find.text('1'), findsOneWidget);
});
```

### Unit Testing
```dart
group('UserService', () {
  late UserService service;
  late MockApi mockApi;

  setUp(() {
    mockApi = MockApi();
    service = UserService(api: mockApi);
  });

  test('fetchUser returns user for valid id', () async {
    // Arrange
    when(mockApi.get('/users/1')).thenAnswer(
      (_) async => Response(data: {'id': 1, 'name': 'John'}),
    );

    // Act
    final user = await service.fetchUser(1);

    // Assert
    expect(user.id, 1);
    expect(user.name, 'John');
    verify(mockApi.get('/users/1')).called(1);
  });
});
```

---

## Project Structure

```
lib/
├── main.dart                 # Entry point
├── app.dart                  # App widget
├── core/
│   ├── constants/           # App constants
│   ├── theme/               # ThemeData, colors, text styles
│   ├── utils/               # Utility functions
│   └── extensions/          # Dart extensions
├── data/
│   ├── models/              # Data models
│   ├── repositories/        # Data repositories
│   └── services/            # API services
├── presentation/
│   ├── screens/             # Full-page widgets
│   ├── widgets/             # Reusable widgets
│   └── providers/           # State management
└── l10n/                    # Localization

test/
├── unit/                    # Unit tests
├── widget/                  # Widget tests
└── integration/             # Integration tests
```

---

## Linting

### analysis_options.yaml
```yaml
include: package:flutter_lints/flutter.yaml

analyzer:
  errors:
    missing_required_param: error
    missing_return: error
  exclude:
    - "**/*.g.dart"
    - "**/*.freezed.dart"

linter:
  rules:
    - always_declare_return_types
    - avoid_print
    - avoid_relative_lib_imports
    - prefer_const_constructors
    - prefer_const_declarations
    - prefer_final_fields
    - prefer_final_locals
    - require_trailing_commas
    - sort_constructors_first
    - sort_unnamed_constructors_first
    - unnecessary_late
    - use_key_in_widget_constructors
```

---

## Performance Tips

### Build Optimization
```dart
// Use const where possible
const MyWidget();

// Avoid rebuilding entire subtrees
Consumer<Model>(
  builder: (context, model, child) {
    return Column(
      children: [
        Text(model.title),  // Rebuilds
        child!,              // Doesn't rebuild
      ],
    );
  },
  child: const ExpensiveWidget(),  // Built once
)

// Use RepaintBoundary for expensive paints
RepaintBoundary(
  child: CustomPaint(/* ... */),
)

// ListView.builder for long lists
ListView.builder(
  itemCount: items.length,
  itemBuilder: (context, index) => ItemTile(items[index]),
)
```

### Memory Management
```dart
// Dispose controllers
@override
void dispose() {
  _animationController.dispose();
  _scrollController.dispose();
  _focusNode.dispose();
  super.dispose();
}

// Cancel subscriptions
_subscription?.cancel();

// Use WeakReference for caches if needed
final _cache = <String, WeakReference<ExpensiveObject>>{};
```

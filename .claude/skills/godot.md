# Godot & GDScript Style Guidelines

> Based on official GDScript style guide and Godot Engine best practices.

## Trigger Keywords

- Godot, GDScript, gdscript
- .gd, .tscn, .tres
- Node2D, Node3D, CharacterBody
- signal, @export, @onready

---

## Naming Conventions

| Type | Convention | Example |
|------|------------|---------|
| Files | snake_case | `player_controller.gd` |
| Classes | PascalCase | `class_name PlayerController` |
| Nodes | PascalCase | `PlayerSprite`, `HealthBar` |
| Functions | snake_case | `get_player_health()` |
| Variables | snake_case | `player_speed` |
| Constants | SCREAMING_SNAKE_CASE | `MAX_SPEED` |
| Signals | snake_case (past tense) | `health_changed` |
| Private | _snake_case | `_internal_value` |

### Boolean: Use `is_`, `has_`, `can_`, `should_` prefixes

---

## Code Order

```gdscript
@tool
class_name PlayerController
extends CharacterBody2D

## Docstring

# Signals
signal health_changed(new_health: int)

# Enums
enum State { IDLE, RUNNING, JUMPING }

# Constants
const MAX_SPEED := 300.0

# @export variables
@export var speed := 200.0

# Public variables
var current_state: State = State.IDLE

# Private variables
var _health: int

# @onready variables
@onready var sprite: Sprite2D = $Sprite2D

# Built-in methods (_ready, _process, etc.)
# Public methods
# Private methods
# Signal callbacks
```

---

## Type Hints

```gdscript
# Always use static typing
var health: int = 100
var speed: float = 200.0
var items: Array[String] = []

# Inferred types
var count := 0
var position := Vector2.ZERO

# Function signatures
func calculate_damage(base: int, multiplier: float) -> int:
    return int(base * multiplier)

# Typed arrays
var enemies: Array[Enemy] = []
var vertices: PackedVector2Array = PackedVector2Array()
```

---

## Signals

```gdscript
# Declaration
signal health_changed(old_value: int, new_value: int)

# Connect
func _ready() -> void:
    button.pressed.connect(_on_button_pressed)

# Emit
health_changed.emit(old_health, _health)
```

---

## Node References

```gdscript
# @onready for child nodes
@onready var sprite: Sprite2D = $Sprite2D
@onready var health_bar: ProgressBar = $UI/HealthBar

# @export for external nodes
@export var target: Node3D

# Safe access
if is_instance_valid(target):
    target.take_damage(10)
```

---

## Resource Management

```gdscript
# Preload (small, frequent)
const BULLET_SCENE: PackedScene = preload("res://scenes/bullet.tscn")

# Load (large, conditional)
var scene: PackedScene = load("res://levels/%s.tscn" % level_name)

# Async loading
ResourceLoader.load_threaded_request("res://large_texture.png")
```

---

## Error Handling

```gdscript
# Assertions (dev only)
assert(value >= 0, "Value cannot be negative")

# Runtime checks
if not FileAccess.file_exists(path):
    push_error("File not found: %s" % path)
    return {}

# Logging
push_warning("Something unexpected")
push_error("Something went wrong")
```

---

## Scene Organization

```
GameScene (Node2D)
├── World
│   ├── TileMap
│   ├── Entities
│   └── Items
├── UI (CanvasLayer)
└── Systems
    ├── SaveManager
    └── EventBus
```

---

## Common Patterns

### State Machine

```gdscript
func transition_to(state_name: String) -> void:
    if current_state:
        current_state.exit()
    current_state = states[state_name]
    current_state.enter()
```

### Event Bus (Autoload)

```gdscript
# EventBus.gd
signal player_died
signal level_completed(level: int)
signal item_collected(item_type: String, amount: int)
```

### Object Pooling

```gdscript
func get_object() -> Node:
    for obj in _pool:
        if not obj.visible:
            return obj
    return scene.instantiate()
```

---

## Performance Tips

```gdscript
# Cache node references
@onready var sprite: Sprite2D = $Sprite2D  # Not $Sprite2D every frame

# Use typed arrays
var enemies: Array[Enemy] = []

# Disable processing when not needed
set_process(false)
set_physics_process(false)

# Avoid creating objects in _process
const DIRECTION := Vector2(1, 0)  # Not var direction := Vector2(1, 0)
```

---
paths: "**/*.gd", "**/*.tscn", "**/*.tres"
---

# Godot & GDScript Style Guidelines

> Based on official GDScript style guide, Godot Engine best practices, and community conventions.

## Naming Conventions

| Type | Convention | Example |
|------|------------|---------|
| Files | snake_case | `player_controller.gd`, `main_menu.tscn` |
| Classes | PascalCase | `class_name PlayerController` |
| Nodes | PascalCase | `PlayerSprite`, `HealthBar` |
| Functions | snake_case | `get_player_health()`, `_on_button_pressed()` |
| Variables | snake_case | `player_speed`, `max_health` |
| Constants | SCREAMING_SNAKE_CASE | `MAX_SPEED`, `DEFAULT_GRAVITY` |
| Signals | snake_case (past tense) | `health_changed`, `player_died` |
| Enums | PascalCase (values SCREAMING_SNAKE_CASE) | `State.IDLE`, `Direction.UP` |
| Private members | _snake_case | `_internal_value`, `_process_input()` |

### Boolean Naming
```gdscript
# Use is_, has_, can_, should_ prefixes
var is_jumping := false
var has_key := false
var can_attack := true
var should_respawn := false
```

---

## Code Order

Organize GDScript files in this order:

```gdscript
# 1. Tool mode (if applicable)
@tool

# 2. Class name
class_name PlayerController

# 3. Extends statement
extends CharacterBody2D

# 4. Docstring
## Player controller handling movement and combat.
## Manages input, physics, and state transitions.

# 5. Signals
signal health_changed(new_health: int)
signal died

# 6. Enums
enum State { IDLE, RUNNING, JUMPING, ATTACKING }

# 7. Constants
const MAX_SPEED := 300.0
const JUMP_VELOCITY := -400.0
const GRAVITY := 980.0

# 8. Exported variables (by category)
@export_group("Movement")
@export var speed := 200.0
@export var jump_force := 400.0

@export_group("Combat")
@export var max_health := 100
@export var attack_damage := 10

# 9. Public variables
var current_state: State = State.IDLE
var velocity_component: Vector2

# 10. Private variables
var _health: int
var _is_invincible := false

# 11. @onready variables
@onready var sprite: Sprite2D = $Sprite2D
@onready var animation_player: AnimationPlayer = $AnimationPlayer
@onready var collision_shape: CollisionShape2D = $CollisionShape2D

# 12. Built-in virtual methods
func _ready() -> void:
    _health = max_health

func _process(delta: float) -> void:
    _update_animation()

func _physics_process(delta: float) -> void:
    _apply_gravity(delta)
    _handle_movement()
    move_and_slide()

func _input(event: InputEvent) -> void:
    if event.is_action_pressed("jump"):
        _jump()

# 13. Public methods
func take_damage(amount: int) -> void:
    if _is_invincible:
        return
    _health -= amount
    health_changed.emit(_health)
    if _health <= 0:
        _die()

func heal(amount: int) -> void:
    _health = mini(_health + amount, max_health)
    health_changed.emit(_health)

# 14. Private methods
func _apply_gravity(delta: float) -> void:
    if not is_on_floor():
        velocity.y += GRAVITY * delta

func _handle_movement() -> void:
    var direction := Input.get_axis("move_left", "move_right")
    velocity.x = direction * speed

func _jump() -> void:
    if is_on_floor():
        velocity.y = -jump_force
        current_state = State.JUMPING

func _die() -> void:
    died.emit()
    queue_free()

# 15. Signal callbacks (alphabetized)
func _on_area_entered(area: Area2D) -> void:
    pass

func _on_timer_timeout() -> void:
    pass

# 16. Subclasses
class InnerHelper:
    var value: int
```

---

## Type Hints

### Always Use Static Typing
```gdscript
# Variables
var player_name: String = "Hero"
var health: int = 100
var speed: float = 200.0
var is_active: bool = true
var items: Array[String] = []
var stats: Dictionary = {}

# Inferred types (use := for inference)
var count := 0  # int
var position := Vector2.ZERO  # Vector2
var enemies := []  # Array (generic)

# Function parameters and return types
func calculate_damage(base: int, multiplier: float) -> int:
    return int(base * multiplier)

func get_player() -> Player:
    return _player

func find_enemies() -> Array[Enemy]:
    return _enemies.filter(func(e): return e.is_alive)
```

### Typed Arrays (Godot 4.x)
```gdscript
var enemies: Array[Enemy] = []
var positions: Array[Vector2] = []
var names: Array[String] = []

# PackedArrays for performance
var vertices: PackedVector2Array = PackedVector2Array()
var indices: PackedInt32Array = PackedInt32Array()
var colors: PackedColorArray = PackedColorArray()
```

---

## Formatting

### Indentation
- Use **tabs** for indentation (Godot default)
- Align continuation lines with opening delimiter

```gdscript
# Multi-line function calls
var result = some_function(
    argument_one,
    argument_two,
    argument_three
)

# Multi-line arrays
var items := [
    "sword",
    "shield",
    "potion",
]

# Multi-line dictionaries
var config := {
    "resolution": Vector2i(1920, 1080),
    "fullscreen": true,
    "vsync": true,
}
```

### Line Length
- Keep lines under **100 characters**
- Use parentheses for line continuation

```gdscript
# Long conditions
if (
    player.health > 0
    and player.has_item("key")
    and not player.is_stunned
):
    open_door()

# Long string
var message := (
    "This is a very long message that needs to be "
    + "split across multiple lines for readability."
)
```

### Blank Lines
- **2 blank lines** between functions
- **1 blank line** between logical sections within functions
- No blank lines after `func` declaration before code

---

## Signals

### Declaration
```gdscript
# Simple signal
signal jumped

# Signal with parameters (typed)
signal health_changed(old_value: int, new_value: int)
signal item_collected(item: Item, quantity: int)
```

### Connecting Signals
```gdscript
# Method 1: In code (preferred for dynamic connections)
func _ready() -> void:
    button.pressed.connect(_on_button_pressed)
    health_component.health_changed.connect(_on_health_changed)

# Method 2: Callable with bind
timer.timeout.connect(_on_timer_timeout.bind(enemy_id))

# Method 3: Lambda (for simple one-liners)
button.pressed.connect(func(): visible = false)

# Disconnect when needed
func _exit_tree() -> void:
    if button.pressed.is_connected(_on_button_pressed):
        button.pressed.disconnect(_on_button_pressed)
```

### Emitting Signals
```gdscript
func take_damage(amount: int) -> void:
    var old_health := _health
    _health -= amount
    health_changed.emit(old_health, _health)

    if _health <= 0:
        died.emit()
```

---

## Node References

### @onready Pattern
```gdscript
# GOOD: @onready for child nodes
@onready var sprite: Sprite2D = $Sprite2D
@onready var collision: CollisionShape2D = $CollisionShape2D
@onready var animation: AnimationPlayer = $AnimationPlayer

# GOOD: Typed and with path
@onready var health_bar: ProgressBar = $UI/HealthBar
@onready var label: Label = $UI/Container/Label

# AVOID: get_node in _ready (use @onready instead)
func _ready() -> void:
    # Don't do this
    sprite = get_node("Sprite2D")
```

### Exported Node References
```gdscript
# For nodes that aren't children (set in editor)
@export var target: Node3D
@export var spawn_point: Marker3D
@export var ui_manager: UIManager
```

### Safe Node Access
```gdscript
# Check before using
if is_instance_valid(target):
    target.take_damage(10)

# Get node or null
var player := get_node_or_null("Player") as Player
if player:
    player.heal(20)
```

---

## Resource Management

### Preloading
```gdscript
# GOOD: Preload for small, frequently used resources
const BULLET_SCENE: PackedScene = preload("res://scenes/bullet.tscn")
const HIT_SOUND: AudioStream = preload("res://audio/hit.wav")
const PLAYER_TEXTURE: Texture2D = preload("res://textures/player.png")

# Use preloaded resources
func shoot() -> void:
    var bullet := BULLET_SCENE.instantiate() as Bullet
    add_child(bullet)
```

### Loading
```gdscript
# GOOD: Load for large or conditionally needed resources
func load_level(level_name: String) -> void:
    var path := "res://levels/%s.tscn" % level_name
    var scene: PackedScene = load(path)
    get_tree().change_scene_to_packed(scene)

# Async loading for large resources
func _load_resources_async() -> void:
    ResourceLoader.load_threaded_request("res://large_texture.png")
    # Check status later
    var status := ResourceLoader.load_threaded_get_status("res://large_texture.png")
    if status == ResourceLoader.THREAD_LOAD_LOADED:
        var texture := ResourceLoader.load_threaded_get("res://large_texture.png")
```

---

## Error Handling

### Assertions (Development)
```gdscript
func set_health(value: int) -> void:
    assert(value >= 0, "Health cannot be negative")
    assert(value <= max_health, "Health exceeds maximum")
    _health = value
```

### Runtime Checks
```gdscript
func load_save_file(path: String) -> Dictionary:
    if not FileAccess.file_exists(path):
        push_error("Save file not found: %s" % path)
        return {}

    var file := FileAccess.open(path, FileAccess.READ)
    if file == null:
        push_error("Failed to open file: %s" % FileAccess.get_open_error())
        return {}

    var json := JSON.new()
    var error := json.parse(file.get_as_text())
    if error != OK:
        push_error("JSON parse error: %s" % json.get_error_message())
        return {}

    return json.data
```

### Logging
```gdscript
# Use appropriate logging levels
print("Debug info")  # Debug only
push_warning("Something unexpected but recoverable")
push_error("Something went wrong")
printerr("Critical error to stderr")

# Conditional debug output
func _debug_log(message: String) -> void:
    if OS.is_debug_build():
        print("[DEBUG] %s" % message)
```

---

## Scene Organization

### Node Structure
```
GameScene (Node2D)
├── World (Node2D)
│   ├── TileMap
│   ├── Entities (Node2D)
│   │   ├── Player
│   │   └── Enemies
│   └── Items (Node2D)
├── UI (CanvasLayer)
│   ├── HUD
│   ├── PauseMenu
│   └── DialogBox
├── Audio (Node)
│   ├── MusicPlayer (AudioStreamPlayer)
│   └── SFXPlayer (AudioStreamPlayer)
└── Systems (Node)
    ├── SaveManager
    └── EventBus
```

### Autoloads (Singletons)
```gdscript
# Global.gd - Autoload for game state
extends Node

signal game_paused
signal game_resumed

var score: int = 0
var high_score: int = 0

func add_score(points: int) -> void:
    score += points
    if score > high_score:
        high_score = score

func reset() -> void:
    score = 0
```

---

## Common Patterns

### State Machine
```gdscript
class_name StateMachine
extends Node

@export var initial_state: State

var current_state: State
var states: Dictionary = {}


func _ready() -> void:
    for child in get_children():
        if child is State:
            states[child.name.to_lower()] = child
            child.state_machine = self

    if initial_state:
        current_state = initial_state
        current_state.enter()


func _process(delta: float) -> void:
    if current_state:
        current_state.update(delta)


func _physics_process(delta: float) -> void:
    if current_state:
        current_state.physics_update(delta)


func transition_to(state_name: String) -> void:
    if not states.has(state_name):
        push_error("State not found: %s" % state_name)
        return

    if current_state:
        current_state.exit()

    current_state = states[state_name]
    current_state.enter()
```

### Object Pooling
```gdscript
class_name ObjectPool
extends Node

@export var scene: PackedScene
@export var pool_size: int = 20

var _pool: Array[Node] = []


func _ready() -> void:
    for i in pool_size:
        var instance := scene.instantiate()
        instance.set_process(false)
        instance.hide()
        add_child(instance)
        _pool.append(instance)


func get_object() -> Node:
    for obj in _pool:
        if not obj.visible:
            return obj

    # Pool exhausted - create new (or return null)
    push_warning("Pool exhausted, creating new instance")
    var instance := scene.instantiate()
    add_child(instance)
    _pool.append(instance)
    return instance


func release(obj: Node) -> void:
    obj.set_process(false)
    obj.hide()
```

### Event Bus
```gdscript
# EventBus.gd (Autoload)
extends Node

signal player_died
signal level_completed(level: int)
signal item_collected(item_type: String, amount: int)
signal enemy_spawned(enemy: Node)
signal score_changed(new_score: int)

# Usage elsewhere:
# EventBus.player_died.emit()
# EventBus.item_collected.connect(_on_item_collected)
```

---

## Performance Tips

### Avoid in _process / _physics_process
```gdscript
# BAD: Creating objects every frame
func _process(delta: float) -> void:
    var direction := Vector2(1, 0)  # Creates new Vector2 each frame

# GOOD: Reuse or use constants
const DIRECTION := Vector2(1, 0)
var _direction := Vector2.ZERO

func _process(delta: float) -> void:
    _direction.x = 1
    _direction.y = 0
```

### Use Typed Arrays
```gdscript
# Faster iteration with typed arrays
var enemies: Array[Enemy] = []

# PackedArrays for large data
var points: PackedVector2Array = PackedVector2Array()
```

### Cache Node References
```gdscript
# BAD: get_node every frame
func _process(delta: float) -> void:
    $Sprite2D.rotation += delta  # Slow!

# GOOD: Cache with @onready
@onready var sprite: Sprite2D = $Sprite2D

func _process(delta: float) -> void:
    sprite.rotation += delta  # Fast!
```

### Disable Processing When Not Needed
```gdscript
func _ready() -> void:
    set_process(false)
    set_physics_process(false)

func activate() -> void:
    set_process(true)
    set_physics_process(true)

func deactivate() -> void:
    set_process(false)
    set_physics_process(false)
```

---
paths: src/**/*.{ts,tsx,js,jsx}
---

# Software Design Patterns Reference

> Based on Gang of Four patterns, SOLID principles, and modern TypeScript practices.

## SOLID Principles

### S - Single Responsibility Principle
> A class should have only one reason to change.

```typescript
// BAD: Multiple responsibilities
class UserService {
  createUser(data: UserData): User { ... }
  sendWelcomeEmail(user: User): void { ... }
  generateReport(users: User[]): Report { ... }
}

// GOOD: Separated responsibilities
class UserService {
  createUser(data: UserData): User { ... }
}

class EmailService {
  sendWelcomeEmail(user: User): void { ... }
}

class ReportService {
  generateUserReport(users: User[]): Report { ... }
}
```

### O - Open/Closed Principle
> Open for extension, closed for modification.

```typescript
// BAD: Requires modification for new types
function calculateArea(shape: Shape): number {
  if (shape.type === 'circle') {
    return Math.PI * shape.radius ** 2;
  } else if (shape.type === 'rectangle') {
    return shape.width * shape.height;
  }
  // Must modify this function for new shapes
}

// GOOD: Extensible without modification
interface Shape {
  calculateArea(): number;
}

class Circle implements Shape {
  constructor(private radius: number) {}
  calculateArea(): number {
    return Math.PI * this.radius ** 2;
  }
}

class Rectangle implements Shape {
  constructor(private width: number, private height: number) {}
  calculateArea(): number {
    return this.width * this.height;
  }
}

// Add new shapes without changing existing code
class Triangle implements Shape {
  constructor(private base: number, private height: number) {}
  calculateArea(): number {
    return (this.base * this.height) / 2;
  }
}
```

### L - Liskov Substitution Principle
> Subtypes must be substitutable for their base types.

```typescript
// BAD: Square violates LSP for Rectangle
class Rectangle {
  constructor(protected width: number, protected height: number) {}
  setWidth(w: number) { this.width = w; }
  setHeight(h: number) { this.height = h; }
  getArea(): number { return this.width * this.height; }
}

class Square extends Rectangle {
  setWidth(w: number) {
    this.width = w;
    this.height = w; // Unexpected behavior!
  }
  setHeight(h: number) {
    this.width = h;
    this.height = h;
  }
}

// GOOD: Separate abstractions
interface Shape {
  getArea(): number;
}

class Rectangle implements Shape {
  constructor(private width: number, private height: number) {}
  getArea(): number { return this.width * this.height; }
}

class Square implements Shape {
  constructor(private side: number) {}
  getArea(): number { return this.side ** 2; }
}
```

### I - Interface Segregation Principle
> Clients should not depend on interfaces they don't use.

```typescript
// BAD: Fat interface
interface Worker {
  work(): void;
  eat(): void;
  sleep(): void;
}

// Robot can't eat or sleep!
class Robot implements Worker {
  work(): void { ... }
  eat(): void { throw new Error('Robots do not eat'); }
  sleep(): void { throw new Error('Robots do not sleep'); }
}

// GOOD: Segregated interfaces
interface Workable {
  work(): void;
}

interface Eatable {
  eat(): void;
}

interface Sleepable {
  sleep(): void;
}

class Human implements Workable, Eatable, Sleepable {
  work(): void { ... }
  eat(): void { ... }
  sleep(): void { ... }
}

class Robot implements Workable {
  work(): void { ... }
}
```

### D - Dependency Inversion Principle
> Depend on abstractions, not concretions.

```typescript
// BAD: High-level depends on low-level
class UserService {
  private database = new MySQLDatabase(); // Concrete dependency

  getUser(id: string): User {
    return this.database.query(`SELECT * FROM users WHERE id = ${id}`);
  }
}

// GOOD: Depend on abstractions
interface Database {
  query<T>(sql: string): T;
}

class UserService {
  constructor(private database: Database) {} // Injected abstraction

  getUser(id: string): User {
    return this.database.query<User>(`SELECT * FROM users WHERE id = ?`, [id]);
  }
}

// Now can use any database implementation
const mysqlService = new UserService(new MySQLDatabase());
const postgresService = new UserService(new PostgresDatabase());
const mockService = new UserService(new MockDatabase()); // For testing
```

---

## Creational Patterns

### Singleton
> Ensure only one instance exists.

```typescript
class Logger {
  private static instance: Logger;
  private logs: string[] = [];

  private constructor() {} // Private constructor

  static getInstance(): Logger {
    if (!Logger.instance) {
      Logger.instance = new Logger();
    }
    return Logger.instance;
  }

  log(message: string): void {
    this.logs.push(`[${new Date().toISOString()}] ${message}`);
  }
}

// Usage
const logger1 = Logger.getInstance();
const logger2 = Logger.getInstance();
console.log(logger1 === logger2); // true

// Modern alternative: Module-level singleton
// logger.ts
class Logger { ... }
export const logger = new Logger(); // Single instance per module
```

**Use when**: Config managers, connection pools, caches, logging.

### Factory Method
> Create objects without specifying exact class.

```typescript
interface Button {
  render(): void;
  onClick(handler: () => void): void;
}

class PrimaryButton implements Button {
  render(): void { console.log('Rendering primary button'); }
  onClick(handler: () => void): void { /* ... */ }
}

class SecondaryButton implements Button {
  render(): void { console.log('Rendering secondary button'); }
  onClick(handler: () => void): void { /* ... */ }
}

class DangerButton implements Button {
  render(): void { console.log('Rendering danger button'); }
  onClick(handler: () => void): void { /* ... */ }
}

// Factory
type ButtonType = 'primary' | 'secondary' | 'danger';

function createButton(type: ButtonType): Button {
  const buttons: Record<ButtonType, () => Button> = {
    primary: () => new PrimaryButton(),
    secondary: () => new SecondaryButton(),
    danger: () => new DangerButton(),
  };
  return buttons[type]();
}

// Usage
const button = createButton('primary');
button.render();
```

**Use when**: Object creation logic is complex, need to decouple creation from usage.

### Builder
> Construct complex objects step by step.

```typescript
class QueryBuilder {
  private query: Query = { table: '', conditions: [], orderBy: [], limit: null };

  select(table: string): this {
    this.query.table = table;
    return this;
  }

  where(condition: string, value: unknown): this {
    this.query.conditions.push({ condition, value });
    return this;
  }

  orderBy(column: string, direction: 'ASC' | 'DESC' = 'ASC'): this {
    this.query.orderBy.push({ column, direction });
    return this;
  }

  limit(count: number): this {
    this.query.limit = count;
    return this;
  }

  build(): string {
    let sql = `SELECT * FROM ${this.query.table}`;
    if (this.query.conditions.length) {
      sql += ` WHERE ${this.query.conditions.map(c => c.condition).join(' AND ')}`;
    }
    if (this.query.orderBy.length) {
      sql += ` ORDER BY ${this.query.orderBy.map(o => `${o.column} ${o.direction}`).join(', ')}`;
    }
    if (this.query.limit) {
      sql += ` LIMIT ${this.query.limit}`;
    }
    return sql;
  }
}

// Usage - fluent API
const query = new QueryBuilder()
  .select('users')
  .where('status = ?', 'active')
  .where('age > ?', 18)
  .orderBy('created_at', 'DESC')
  .limit(10)
  .build();
```

**Use when**: Many optional parameters, complex object construction.

---

## Structural Patterns

### Adapter
> Make incompatible interfaces work together.

```typescript
// Legacy API returns XML
interface LegacyAPI {
  getXMLData(): string;
}

// New system expects JSON
interface ModernAPI {
  getJSONData(): object;
}

// Adapter bridges the gap
class APIAdapter implements ModernAPI {
  constructor(private legacyAPI: LegacyAPI) {}

  getJSONData(): object {
    const xml = this.legacyAPI.getXMLData();
    return this.xmlToJson(xml);
  }

  private xmlToJson(xml: string): object {
    // Convert XML to JSON
    return JSON.parse(convertXmlToJson(xml));
  }
}

// Usage
const legacyApi = new OldPaymentGateway();
const modernApi = new APIAdapter(legacyApi);
const data = modernApi.getJSONData(); // Works with new system
```

**Use when**: Integrating legacy code, third-party libraries with different interfaces.

### Facade
> Provide simple interface to complex subsystem.

```typescript
// Complex subsystems
class VideoDecoder { decode(file: string): VideoStream { ... } }
class AudioDecoder { decode(file: string): AudioStream { ... } }
class SubtitleParser { parse(file: string): Subtitles { ... } }
class Renderer { render(video: VideoStream, audio: AudioStream): void { ... } }

// Facade simplifies usage
class MediaPlayer {
  private videoDecoder = new VideoDecoder();
  private audioDecoder = new AudioDecoder();
  private subtitleParser = new SubtitleParser();
  private renderer = new Renderer();

  play(videoFile: string, subtitleFile?: string): void {
    const video = this.videoDecoder.decode(videoFile);
    const audio = this.audioDecoder.decode(videoFile);
    const subtitles = subtitleFile
      ? this.subtitleParser.parse(subtitleFile)
      : null;

    this.renderer.render(video, audio);
    if (subtitles) {
      this.renderer.showSubtitles(subtitles);
    }
  }

  stop(): void {
    this.renderer.stop();
  }
}

// Usage - simple API
const player = new MediaPlayer();
player.play('movie.mp4', 'movie.srt');
```

**Use when**: Simplifying complex APIs, providing unified interface to subsystems.

### Decorator
> Add behavior dynamically without modifying original.

```typescript
interface Coffee {
  cost(): number;
  description(): string;
}

class SimpleCoffee implements Coffee {
  cost(): number { return 2; }
  description(): string { return 'Coffee'; }
}

// Decorators
class MilkDecorator implements Coffee {
  constructor(private coffee: Coffee) {}
  cost(): number { return this.coffee.cost() + 0.5; }
  description(): string { return `${this.coffee.description()}, Milk`; }
}

class SugarDecorator implements Coffee {
  constructor(private coffee: Coffee) {}
  cost(): number { return this.coffee.cost() + 0.2; }
  description(): string { return `${this.coffee.description()}, Sugar`; }
}

class WhipDecorator implements Coffee {
  constructor(private coffee: Coffee) {}
  cost(): number { return this.coffee.cost() + 0.7; }
  description(): string { return `${this.coffee.description()}, Whip`; }
}

// Usage - stack decorators
let coffee: Coffee = new SimpleCoffee();
coffee = new MilkDecorator(coffee);
coffee = new SugarDecorator(coffee);
coffee = new WhipDecorator(coffee);

console.log(coffee.description()); // "Coffee, Milk, Sugar, Whip"
console.log(coffee.cost());        // 3.4
```

**Use when**: Adding features dynamically, extending functionality without subclassing.

### Proxy
> Control access to another object.

```typescript
interface Image {
  display(): void;
}

class RealImage implements Image {
  constructor(private filename: string) {
    this.loadFromDisk();
  }

  private loadFromDisk(): void {
    console.log(`Loading ${this.filename}`);
  }

  display(): void {
    console.log(`Displaying ${this.filename}`);
  }
}

// Lazy loading proxy
class ImageProxy implements Image {
  private realImage: RealImage | null = null;

  constructor(private filename: string) {}

  display(): void {
    if (!this.realImage) {
      this.realImage = new RealImage(this.filename); // Load only when needed
    }
    this.realImage.display();
  }
}

// Caching proxy
class CachedAPIProxy {
  private cache = new Map<string, { data: unknown; expiry: number }>();

  constructor(private api: API, private ttl: number = 60000) {}

  async get(endpoint: string): Promise<unknown> {
    const cached = this.cache.get(endpoint);
    if (cached && cached.expiry > Date.now()) {
      return cached.data;
    }

    const data = await this.api.get(endpoint);
    this.cache.set(endpoint, { data, expiry: Date.now() + this.ttl });
    return data;
  }
}
```

**Use when**: Lazy loading, access control, caching, logging.

---

## Behavioral Patterns

### Strategy
> Define family of algorithms, make them interchangeable.

```typescript
interface PaymentStrategy {
  pay(amount: number): Promise<PaymentResult>;
}

class CreditCardPayment implements PaymentStrategy {
  constructor(private cardNumber: string) {}

  async pay(amount: number): Promise<PaymentResult> {
    // Process credit card payment
    return { success: true, transactionId: 'cc-123' };
  }
}

class PayPalPayment implements PaymentStrategy {
  constructor(private email: string) {}

  async pay(amount: number): Promise<PaymentResult> {
    // Process PayPal payment
    return { success: true, transactionId: 'pp-456' };
  }
}

class CryptoPayment implements PaymentStrategy {
  constructor(private walletAddress: string) {}

  async pay(amount: number): Promise<PaymentResult> {
    // Process crypto payment
    return { success: true, transactionId: 'crypto-789' };
  }
}

// Context
class PaymentProcessor {
  private strategy: PaymentStrategy;

  setStrategy(strategy: PaymentStrategy): void {
    this.strategy = strategy;
  }

  async checkout(amount: number): Promise<PaymentResult> {
    if (!this.strategy) {
      throw new Error('Payment strategy not set');
    }
    return this.strategy.pay(amount);
  }
}

// Usage
const processor = new PaymentProcessor();
processor.setStrategy(new CreditCardPayment('4111-1111-1111-1111'));
await processor.checkout(99.99);
```

**Use when**: Multiple algorithms for same task, need runtime algorithm selection.

### Observer
> Define one-to-many dependency between objects.

```typescript
type EventHandler<T> = (data: T) => void;

class EventEmitter<Events extends Record<string, unknown>> {
  private listeners = new Map<keyof Events, Set<EventHandler<unknown>>>();

  on<K extends keyof Events>(event: K, handler: EventHandler<Events[K]>): () => void {
    if (!this.listeners.has(event)) {
      this.listeners.set(event, new Set());
    }
    this.listeners.get(event)!.add(handler as EventHandler<unknown>);

    // Return unsubscribe function
    return () => this.off(event, handler);
  }

  off<K extends keyof Events>(event: K, handler: EventHandler<Events[K]>): void {
    this.listeners.get(event)?.delete(handler as EventHandler<unknown>);
  }

  emit<K extends keyof Events>(event: K, data: Events[K]): void {
    this.listeners.get(event)?.forEach((handler) => handler(data));
  }
}

// Type-safe usage
interface UserEvents {
  login: { userId: string; timestamp: Date };
  logout: { userId: string };
  profileUpdate: { userId: string; changes: Partial<User> };
}

const userEvents = new EventEmitter<UserEvents>();

const unsubscribe = userEvents.on('login', ({ userId, timestamp }) => {
  console.log(`User ${userId} logged in at ${timestamp}`);
});

userEvents.emit('login', { userId: '123', timestamp: new Date() });
unsubscribe(); // Clean up
```

**Use when**: Event systems, reactive programming, pub/sub patterns.

### Command
> Encapsulate request as object.

```typescript
interface Command {
  execute(): void;
  undo(): void;
}

class AddTextCommand implements Command {
  constructor(
    private editor: TextEditor,
    private text: string,
    private position: number
  ) {}

  execute(): void {
    this.editor.insertAt(this.position, this.text);
  }

  undo(): void {
    this.editor.deleteAt(this.position, this.text.length);
  }
}

class DeleteTextCommand implements Command {
  private deletedText: string = '';

  constructor(
    private editor: TextEditor,
    private position: number,
    private length: number
  ) {}

  execute(): void {
    this.deletedText = this.editor.getTextAt(this.position, this.length);
    this.editor.deleteAt(this.position, this.length);
  }

  undo(): void {
    this.editor.insertAt(this.position, this.deletedText);
  }
}

// Command history for undo/redo
class CommandHistory {
  private history: Command[] = [];
  private position = -1;

  execute(command: Command): void {
    // Remove any commands after current position (for redo)
    this.history = this.history.slice(0, this.position + 1);
    command.execute();
    this.history.push(command);
    this.position++;
  }

  undo(): void {
    if (this.position >= 0) {
      this.history[this.position].undo();
      this.position--;
    }
  }

  redo(): void {
    if (this.position < this.history.length - 1) {
      this.position++;
      this.history[this.position].execute();
    }
  }
}
```

**Use when**: Undo/redo, queuing operations, macro recording.

### State
> Alter behavior when internal state changes.

```typescript
interface OrderState {
  name: string;
  next(order: Order): void;
  cancel(order: Order): void;
  ship(order: Order): void;
}

class PendingState implements OrderState {
  name = 'pending';

  next(order: Order): void {
    order.setState(new ProcessingState());
  }

  cancel(order: Order): void {
    order.setState(new CancelledState());
  }

  ship(order: Order): void {
    throw new Error('Cannot ship pending order');
  }
}

class ProcessingState implements OrderState {
  name = 'processing';

  next(order: Order): void {
    order.setState(new ShippedState());
  }

  cancel(order: Order): void {
    order.setState(new CancelledState());
  }

  ship(order: Order): void {
    order.setState(new ShippedState());
  }
}

class ShippedState implements OrderState {
  name = 'shipped';

  next(order: Order): void {
    order.setState(new DeliveredState());
  }

  cancel(order: Order): void {
    throw new Error('Cannot cancel shipped order');
  }

  ship(order: Order): void {
    throw new Error('Already shipped');
  }
}

class Order {
  private state: OrderState = new PendingState();

  setState(state: OrderState): void {
    console.log(`Order: ${this.state.name} -> ${state.name}`);
    this.state = state;
  }

  getState(): string {
    return this.state.name;
  }

  next(): void { this.state.next(this); }
  cancel(): void { this.state.cancel(this); }
  ship(): void { this.state.ship(this); }
}
```

**Use when**: Object behavior depends on state, state machine implementations.

---

## Pattern Selection Guide

| Problem | Pattern |
|---------|---------|
| Need single instance | Singleton |
| Complex object construction | Builder |
| Create objects by type | Factory |
| Incompatible interfaces | Adapter |
| Simplify complex API | Facade |
| Add features dynamically | Decorator |
| Lazy loading / caching | Proxy |
| Interchangeable algorithms | Strategy |
| Event notifications | Observer |
| Undo/redo support | Command |
| State-dependent behavior | State |

## Anti-Patterns to Avoid

- **Over-engineering**: Don't add patterns for simple problems
- **Pattern fever**: Not every solution needs a GoF pattern
- **Wrong abstraction**: Wait until the pattern emerges naturally
- **Premature optimization**: Start simple, refactor when needed
- **God object**: If a class does everything, it's not using any pattern correctly

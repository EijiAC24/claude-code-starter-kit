---
paths: src/**/*.{ts,tsx,js,jsx}
---

# Software Design Patterns Reference

> Based on Gang of Four (GoF) patterns. Use appropriate patterns to solve common problems.

## When to Use This Reference

- Before designing new classes or modules
- When refactoring complex code
- When you recognize a recurring problem

---

## Creational Patterns

*Focus: Object creation mechanisms*

### Singleton
**Use when:** Need exactly one instance globally (config, logger, connection pool)
```typescript
class Logger {
  private static instance: Logger;
  private constructor() {}

  static getInstance(): Logger {
    if (!Logger.instance) {
      Logger.instance = new Logger();
    }
    return Logger.instance;
  }
}
```

### Factory Method
**Use when:** Object creation logic is complex or varies by context
```typescript
interface Button { render(): void; }

class ButtonFactory {
  static create(type: 'primary' | 'secondary'): Button {
    switch (type) {
      case 'primary': return new PrimaryButton();
      case 'secondary': return new SecondaryButton();
    }
  }
}
```

### Builder
**Use when:** Constructing complex objects step-by-step
```typescript
const query = new QueryBuilder()
  .select(['name', 'email'])
  .from('users')
  .where('active', true)
  .limit(10)
  .build();
```

### Prototype
**Use when:** Creating objects by cloning existing ones is more efficient

### Abstract Factory
**Use when:** Creating families of related objects without specifying concrete classes

---

## Structural Patterns

*Focus: Class and object composition*

### Adapter
**Use when:** Making incompatible interfaces work together
```typescript
// Old API returns { firstName, lastName }
// New code expects { fullName }
class UserAdapter {
  constructor(private legacyUser: LegacyUser) {}

  get fullName(): string {
    return `${this.legacyUser.firstName} ${this.legacyUser.lastName}`;
  }
}
```

### Facade
**Use when:** Simplifying complex subsystems with a unified interface
```typescript
// Instead of calling 5 different services
class OrderFacade {
  async placeOrder(items: Item[], userId: string): Promise<Order> {
    const user = await this.userService.get(userId);
    const inventory = await this.inventoryService.check(items);
    const payment = await this.paymentService.process(user, items);
    const shipping = await this.shippingService.schedule(user.address);
    return this.orderService.create({ user, items, payment, shipping });
  }
}
```

### Decorator
**Use when:** Adding behavior to objects dynamically
```typescript
// TypeScript decorators
@logged
@cached(60)
async function fetchUser(id: string): Promise<User> { ... }
```

### Composite
**Use when:** Treating individual objects and compositions uniformly (tree structures)

### Proxy
**Use when:** Controlling access to an object (lazy loading, access control, logging)

### Bridge
**Use when:** Separating abstraction from implementation

### Flyweight
**Use when:** Sharing common state among many similar objects

---

## Behavioral Patterns

*Focus: Communication between objects*

### Strategy
**Use when:** Switching algorithms at runtime
```typescript
interface PaymentStrategy {
  pay(amount: number): Promise<void>;
}

class PaymentProcessor {
  constructor(private strategy: PaymentStrategy) {}

  setStrategy(strategy: PaymentStrategy) {
    this.strategy = strategy;
  }

  async process(amount: number) {
    await this.strategy.pay(amount);
  }
}

// Usage
processor.setStrategy(new CreditCardPayment());
processor.setStrategy(new PayPalPayment());
```

### Observer
**Use when:** One-to-many dependency where changes notify all dependents
```typescript
// Event emitter pattern
class EventBus {
  private listeners = new Map<string, Set<Function>>();

  subscribe(event: string, callback: Function) {
    if (!this.listeners.has(event)) {
      this.listeners.set(event, new Set());
    }
    this.listeners.get(event)!.add(callback);
  }

  emit(event: string, data: unknown) {
    this.listeners.get(event)?.forEach(cb => cb(data));
  }
}
```

### Command
**Use when:** Encapsulating requests as objects (undo/redo, queuing)
```typescript
interface Command {
  execute(): void;
  undo(): void;
}

class CommandHistory {
  private history: Command[] = [];

  execute(command: Command) {
    command.execute();
    this.history.push(command);
  }

  undo() {
    const command = this.history.pop();
    command?.undo();
  }
}
```

### State
**Use when:** Object behavior depends on its state
```typescript
interface OrderState {
  next(order: Order): void;
  cancel(order: Order): void;
}

class Order {
  private state: OrderState = new PendingState();

  setState(state: OrderState) { this.state = state; }
  next() { this.state.next(this); }
  cancel() { this.state.cancel(this); }
}
```

### Template Method
**Use when:** Defining algorithm skeleton with customizable steps

### Chain of Responsibility
**Use when:** Passing requests through a chain of handlers (middleware)

### Iterator
**Use when:** Traversing collections without exposing internals

### Mediator
**Use when:** Reducing direct dependencies between objects

### Memento
**Use when:** Capturing and restoring object state

### Visitor
**Use when:** Adding operations to object structures without modifying them

---

## Quick Selection Guide

| Problem | Pattern |
|---------|---------|
| Need single instance | Singleton |
| Complex object creation | Builder, Factory |
| Incompatible interfaces | Adapter |
| Simplify complex API | Facade |
| Add behavior dynamically | Decorator |
| Switch algorithms | Strategy |
| React to state changes | Observer |
| Undo/redo support | Command, Memento |
| State-dependent behavior | State |
| Request processing chain | Chain of Responsibility |

## Anti-patterns to Avoid

- **Over-engineering**: Don't use patterns for simple problems
- **Pattern fever**: Not every solution needs a GoF pattern
- **Wrong pattern**: Match the pattern to the actual problem
- **Premature abstraction**: Wait until the pattern is clearly needed

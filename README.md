# Design Patterns Guide with Java SE

A comprehensive guide covering all Gang of Four (GoF) design patterns plus modern patterns, with Java SE implementations.

## Table of Contents

### 1. Creational Patterns
- Singleton Pattern
- Factory Method Pattern
- Abstract Factory Pattern
- Builder Pattern
- Prototype Pattern
- Object Pool Pattern (Modern)
- Dependency Injection (Modern)

### 2. Structural Patterns
- Adapter Pattern
- Bridge Pattern
- Composite Pattern
- Decorator Pattern
- Facade Pattern
- Flyweight Pattern
- Proxy Pattern
- Module Pattern (Modern)
- Front Controller Pattern (Modern)

### 3. Behavioral Patterns
- Chain of Responsibility Pattern
- Command Pattern
- Interpreter Pattern
- Iterator Pattern
- Mediator Pattern
- Memento Pattern
- Observer Pattern
- State Pattern
- Strategy Pattern
- Template Method Pattern
- Visitor Pattern
- Specification Pattern (Modern)
- Null Object Pattern (Modern)

## 1. Creational Patterns

### Singleton Pattern
Ensures a class has only one instance and provides a global point of access to it.

```java
public class Singleton {
    private static volatile Singleton instance;
    private static final Object lock = new Object();
    
    private Singleton() {
        if (instance != null) {
            throw new IllegalStateException("Already initialized.");
        }
    }
    
    public static Singleton getInstance() {
        Singleton result = instance;
        if (result == null) {
            synchronized (lock) {
                result = instance;
                if (result == null) {
                    instance = result = new Singleton();
                }
            }
        }
        return result;
    }
}
```

### Factory Method Pattern
Defines an interface for creating objects but lets subclasses decide which class to instantiate.

```java
public interface Product {}

public class ConcreteProductA implements Product {}
public class ConcreteProductB implements Product {}

public abstract class Creator {
    abstract Product factoryMethod();
    
    public void someOperation() {
        Product product = factoryMethod();
        // Use the product
    }
}

public class ConcreteCreatorA extends Creator {
    @Override
    Product factoryMethod() {
        return new ConcreteProductA();
    }
}
```

### Abstract Factory Pattern
Provides an interface for creating families of related or dependent objects without specifying their concrete classes.

```java
public interface GUIFactory {
    Button createButton();
    Checkbox createCheckbox();
}

public interface Button {
    void paint();
}

public interface Checkbox {
    void paint();
}

public class WindowsFactory implements GUIFactory {
    @Override
    public Button createButton() {
        return new WindowsButton();
    }
    
    @Override
    public Checkbox createCheckbox() {
        return new WindowsCheckbox();
    }
}

public class MacFactory implements GUIFactory {
    @Override
    public Button createButton() {
        return new MacButton();
    }
    
    @Override
    public Checkbox createCheckbox() {
        return new MacCheckbox();
    }
}
```

### Builder Pattern
Separates the construction of a complex object from its representation.

```java
public class Computer {
    private String cpu;
    private String ram;
    private String storage;
    
    public static class Builder {
        private Computer computer = new Computer();
        
        public Builder cpu(String cpu) {
            computer.cpu = cpu;
            return this;
        }
        
        public Builder ram(String ram) {
            computer.ram = ram;
            return this;
        }
        
        public Builder storage(String storage) {
            computer.storage = storage;
            return this;
        }
        
        public Computer build() {
            return computer;
        }
    }
}

// Usage
Computer computer = new Computer.Builder()
    .cpu("Intel i7")
    .ram("16GB")
    .storage("1TB SSD")
    .build();
```

### Prototype Pattern
Creates new objects by cloning an existing object, known as the prototype.

```java
public interface Prototype extends Cloneable {
    Prototype clone();
}

public class ConcretePrototype implements Prototype {
    private String field;
    
    public ConcretePrototype(String field) {
        this.field = field;
    }
    
    @Override
    public Prototype clone() {
        return new ConcretePrototype(this.field);
    }
}
```

## 2. Structural Patterns

### Bridge Pattern
Separates an abstraction from its implementation so that both can vary independently.

```java
public interface DrawAPI {
    void drawCircle(int x, int y, int radius);
}

public abstract class Shape {
    protected DrawAPI drawAPI;
    
    protected Shape(DrawAPI drawAPI) {
        this.drawAPI = drawAPI;
    }
    
    abstract public void draw();
}

public class Circle extends Shape {
    private int x, y, radius;
    
    public Circle(int x, int y, int radius, DrawAPI drawAPI) {
        super(drawAPI);
        this.x = x;
        this.y = y;
        this.radius = radius;
    }
    
    @Override
    public void draw() {
        drawAPI.drawCircle(x, y, radius);
    }
}
```

### Composite Pattern
Composes objects into tree structures to represent part-whole hierarchies.

```java
public interface Component {
    void operation();
}

public class Leaf implements Component {
    @Override
    public void operation() {
        // Leaf-specific operation
    }
}

public class Composite implements Component {
    private List<Component> children = new ArrayList<>();
    
    public void add(Component component) {
        children.add(component);
    }
    
    @Override
    public void operation() {
        for (Component child : children) {
            child.operation();
        }
    }
}
```

### Flyweight Pattern
Uses sharing to support large numbers of fine-grained objects efficiently.

```java
public interface Flyweight {
    void operation(String extrinsicState);
}

public class ConcreteFlyweight implements Flyweight {
    private final String intrinsicState;
    
    public ConcreteFlyweight(String intrinsicState) {
        this.intrinsicState = intrinsicState;
    }
    
    @Override
    public void operation(String extrinsicState) {
        // Operation using both states
    }
}

public class FlyweightFactory {
    private Map<String, Flyweight> flyweights = new HashMap<>();
    
    public Flyweight getFlyweight(String key) {
        return flyweights.computeIfAbsent(key, 
            k -> new ConcreteFlyweight(k));
    }
}
```

## 3. Behavioral Patterns

### Chain of Responsibility Pattern
Passes a request along a chain of handlers until one handles it.

```java
public abstract class Handler {
    protected Handler successor;
    
    public void setSuccessor(Handler successor) {
        this.successor = successor;
    }
    
    public abstract void handleRequest(Request request);
}

public class ConcreteHandler1 extends Handler {
    @Override
    public void handleRequest(Request request) {
        if (canHandle(request)) {
            // Handle request
        } else if (successor != null) {
            successor.handleRequest(request);
        }
    }
}
```

### Mediator Pattern
Defines an object that encapsulates how a set of objects interact.

```java
public interface Mediator {
    void notify(Component sender, String event);
}

public abstract class Component {
    protected Mediator mediator;
    
    public Component(Mediator mediator) {
        this.mediator = mediator;
    }
}

public class ConcreteMediator implements Mediator {
    private Component1 component1;
    private Component2 component2;
    
    @Override
    public void notify(Component sender, String event) {
        if (sender == component1) {
            component2.react(event);
        } else {
            component1.react(event);
        }
    }
}
```

### State Pattern
Allows an object to alter its behavior when its internal state changes.

```java
public interface State {
    void handle(Context context);
}

public class Context {
    private State state;
    
    public void setState(State state) {
        this.state = state;
    }
    
    public void request() {
        state.handle(this);
    }
}

public class ConcreteStateA implements State {
    @Override
    public void handle(Context context) {
        context.setState(new ConcreteStateB());
    }
}
```

### Visitor Pattern
Represents an operation to be performed on elements of an object structure.

```java
public interface Visitor {
    void visit(ConcreteElementA element);
    void visit(ConcreteElementB element);
}

public interface Element {
    void accept(Visitor visitor);
}

public class ConcreteElementA implements Element {
    @Override
    public void accept(Visitor visitor) {
        visitor.visit(this);
    }
}

public class ConcreteVisitor implements Visitor {
    @Override
    public void visit(ConcreteElementA element) {
        // Do something with element A
    }
    
    @Override
    public void visit(ConcreteElementB element) {
        // Do something with element B
    }
}
```

### Modern Patterns

#### Specification Pattern
Combines business rules by chaining boolean expressions.

```java
public interface Specification<T> {
    boolean isSatisfiedBy(T item);
    
    default Specification<T> and(Specification<T> other) {
        return item -> isSatisfiedBy(item) && other.isSatisfiedBy(item);
    }
}

public class PriceSpecification implements Specification<Product> {
    private double maxPrice;
    
    public PriceSpecification(double maxPrice) {
        this.maxPrice = maxPrice;
    }
    
    @Override
    public boolean isSatisfiedBy(Product product) {
        return product.getPrice() <= maxPrice;
    }
}
```

#### Null Object Pattern
Provides a default object instead of null references.

```java
public interface Logger {
    void log(String message);
}

public class NullLogger implements Logger {
    @Override
    public void log(String message) {
        // Do nothing
    }
}

public class RealLogger implements Logger {
    @Override
    public void log(String message) {
        System.out.println(message);
    }
}
```

## Best Practices

1. **Choose Patterns Wisely**
   - Don't force patterns where they're not needed
   - Consider the maintenance implications
   - Think about future extensibility

2. **Pattern Combinations**
   - Patterns can and should be combined when appropriate
   - Look for synergies between patterns
   - Document pattern interactions

3. **Implementation Guidelines**
   - Keep implementations as simple as possible
   - Follow SOLID principles
   - Use meaningful names that reflect the pattern's role

4. **Documentation**
   - Document why you chose a particular pattern
   - Explain how the pattern is implemented
   - Note any deviations from standard implementation

## Anti-Patterns to Avoid

1. Pattern Overuse
2. God Objects
3. Golden Hammer
4. Premature Optimization
5. Hardcoding

## References

- "Design Patterns: Elements of Reusable Object-Oriented Software" by Gang of Four
- "Head First Design Patterns" by Freeman & Freeman
- "Patterns of Enterprise Application Architecture" by Martin Fowler

## License

This guide is available under the MIT License. Feel free to use and modify it for your projects.

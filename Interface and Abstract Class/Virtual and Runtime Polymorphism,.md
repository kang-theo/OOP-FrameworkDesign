In C#，`virtual` keyword is used to modify a class's methods, properties, indexers, or events, indicating that these members can be **overridden** in derived classes. It is a core mechanism for implementing **runtime polymorphism**, allowing subclasses to provide custom implementations while maintaining interface consistency defined by the base class.  


### **I. Core Features and Syntax**
1. **Declaration and Overriding**  
   - Declare a virtual method in the base class:  
     ```csharp
     public class Animal {
         public virtual void Speak() {
             Console.WriteLine("Animal speaks");
         }
     }
     ```
   - Override the method in the derived class using `override`:  
     ```csharp
     public class Dog : Animal {
         public override void Speak() {
             Console.WriteLine("Dog barks");
         }
     }
     ```

2. **Runtime Dynamic Binding**  
   - When calling a `virtual` method through a base class reference, the implementation of the object's **actual type** is executed:  
     ```csharp
     Animal animal = new Dog();
     animal.Speak();  // Output: "Dog barks" (dynamically bound to Dog's implementation)
     ```

3. **Default Implementation**  
   - A `virtual` method must have a base class implementation (cannot be abstract), and subclasses can choose to override it:  
     ```csharp
     public class Shape {
         public virtual double Area() { return 0; }  // Base class default implementation
     }
     public class Circle : Shape {
         private double radius;
         public override double Area() { return Math.PI * radius * radius; }  // Subclass override
     }
     ```


### **II. Comparison with Other Modifiers**
| **Modifier** | **Meaning**                                                                 | **Relation to `virtual`**                                  |
|------------|--------------------------------------------------------------------------|-------------------------------------------------------|
| `virtual`  | Allows a method to be overridden by subclasses.                            | Must have a base implementation; subclasses use `override` to override. |
| `abstract` | Forces subclasses to implement the method (used in abstract classes).      | Cannot have a base implementation; subclasses must override. |
| `override` | Overrides a `virtual` or `abstract` method from the base class.            | Must match the base method signature exactly; can use `sealed` to prevent further overriding. |
| `sealed`   | Prevents subclasses from overriding the method.                            | Must be used on an `override` method to prevent modification by subsequent derived classes. |
| `new`      | Hides a base class method with the same name (no polymorphism, creates a new method version). | Unrelated to `virtual`; used to resolve naming conflicts. |


### **III. Application Scenarios**
#### 1. **Frameworks and Plugin Systems**  
   - Base classes define generic behaviors, and subclasses override to implement specific logic:  
     ```csharp
     public abstract class PaymentProcessor {
         public virtual void Validate() { /* Default validation logic */ }
         public abstract void Process(decimal amount);  // Abstract method must be implemented by subclasses
     }
     ```

#### 2. **Template Method Pattern**  
   - The base class defines an algorithm skeleton, and subclasses override specific steps:  
     ```csharp
     public class DocumentProcessor {
         public virtual void LoadDocument() { /* Default loading logic */ }
         public virtual void ParseDocument() { /* Default parsing logic */ }
         public void Process() {  // Template method
             LoadDocument();
             ParseDocument();
             SaveResults();
         }
         private void SaveResults() { /* Fixed step */ }
     }
     ```

#### 3. **Data Access Layer Abstraction**  
   - The base class provides generic data operations, and subclasses optimize for different databases:  
     ```csharp
     public class DbContext {
         public virtual IQueryable<T> Query<T>() where T : class { /* Default implementation */ }
     }
     public class SqlDbContext : DbContext {
         public override IQueryable<T> Query<T>() where T : class { /* SQL-optimized implementation */ }
     }
     ```


### **IV. Considerations**
1. **Performance Overhead**  
   - `virtual` methods use a virtual function table (vtable) for dynamic invocation, which is slightly slower than non-virtual methods (usually negligible except in high-frequency loops).

2. **Risk of Calling in Constructors**  
   - Calling a `virtual` method in a base class constructor may execute the subclass implementation before the subclass is fully initialized:  
     ```csharp
     public class Base {
         public Base() {
             Initialize();  // Calls virtual method
         }
         public virtual void Initialize() { }
     }
     public class Derived : Base {
         private int value;
         public override void Initialize() {
             value = 10;  // Called during base constructor, before Derived is fully constructed
         }
     }
     ```

3. **Version Compatibility**  
   - Adding a new `virtual` method does not break existing subclasses, but modifying the signature of an existing `virtual` method may cause compilation errors in subclasses.


### **V. Example Code**
```csharp
// Base class: Shape
public class Shape {
    public virtual double Area() {
        Console.WriteLine("Calculating default area...");
        return 0;
    }
}

// Subclass: Circle
public class Circle : Shape {
    private double radius;
    public Circle(double radius) { this.radius = radius; }
    
    public override double Area() {
        Console.WriteLine("Calculating circle area...");
        return Math.PI * radius * radius;
    }
}

// Subclass: Rectangle (does not override Area)
public class Rectangle : Shape {
    private double width, height;
    public Rectangle(double width, double height) {
        this.width = width;
        this.height = height;
    }
}

// Test
public static void Main() {
    Shape circle = new Circle(5);
    Shape rectangle = new Rectangle(3, 4);
    
    Console.WriteLine(circle.Area());  // Output: "Calculating circle area..." + 78.5398...
    Console.WriteLine(rectangle.Area());  // Output: "Calculating default area..." + 0
}
```


### **VI. Summary**
- **Core Value**: `virtual` methods are the foundation of polymorphism in C#, allowing code to select execution logic based on the actual object type at runtime, enhancing code extensibility and maintainability.  
- **Usage Principles**:  
  - Use `virtual` when a base class method needs a default implementation but allows subclass customization.  
  - Use `abstract` when the base class cannot provide a default implementation and requires subclass implementation.  
  - Avoid using `virtual` in constructors, static methods, or sealed classes.

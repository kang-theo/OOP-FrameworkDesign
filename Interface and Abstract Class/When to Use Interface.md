> When you need to define a contract between two objects that won’t change over time, using an **interface** is appropriate. Abstract base classes are better suited for defining the common base type for a group of related types.  


### **I. Core Distinction: Protocol (Interface) vs. Base Type (Abstract Class)**
| **Scenario**            | **Interface**                          | **Abstract Class**                   |
|-------------------------|--------------------------------------|--------------------------------------|
| **Design Intent**        | Define "what can be done" (protocol, capability) | Define "what it is" (type, inheritance hierarchy) |
| **Immutability Requirement** | The protocol should stay stable once published (adding methods breaks implementations) | Allows extension via virtual methods/default implementations, better compatibility |
| **Code Reuse**           | No implementation logic (except C# 8.0+ default methods) | Includes shared implementations and state |
| **Inheritance Limitation** | Supports multiple implementations (a class can implement multiple interfaces) | Only single inheritance (a class can inherit from one abstract class) |


### **II. Typical Application Scenarios**
#### 1. **Interfaces: Stable Cross-Type Protocols**
- **Use Cases**:  
  - Define unchanging public contracts (e.g., standard APIs, plugin systems).  
  - Different types need shared behaviors without a common base class (e.g., `IEnumerable<T>`, `IDisposable`).  

- **Example**:  
  ```csharp
  // Define a stable encryption interface (protocol)
  public interface IEncryptor {
      byte[] Encrypt(string data);
      string Decrypt(byte[] encryptedData);
  }

  // Diverse implementations can freely combine behaviors
  public class AesEncryptor : IEncryptor { /* Implementation */ }
  public class RsaEncryptor : IEncryptor { /* Implementation */ }
  ```

#### 2. **Abstract Classes: Evolving Type Hierarchies**
- **Use Cases**:  
  - A group of types shares common state and behaviors (e.g., `System.IO.Stream`).  
  - Enforce contractual obligations on subclasses while allowing default base implementations.  

- **Example**:  
  ```csharp
  // Define a base shape class (shared state and behavior)
  public abstract class Shape {
      public double X { get; set; }
      public double Y { get; set; }
      
      public void Move(double x, double y) { /* Shared implementation */ }
      public abstract double Area();  // Forced subclass implementation
  }

  public class Circle : Shape { /* Inherit and implement abstract method */ }
  public class Rectangle : Shape { /* Inherit and implement abstract method */ }
  ```


### **III. Version Evolution Strategies**
#### 1. **Interface Evolution Limitations**
- Adding methods breaks all implementations, requiring careful design.  
- C# 8.0+ allows default methods but may cause multiple implementation conflicts (diamond problem).  

#### 2. **Abstract Class Evolution Advantages**
- Adding virtual methods doesn’t affect subclasses, ensuring better compatibility.  
- Only adding abstract methods breaks subclasses (use with caution).  


### **IV. Combined Usage: Interface + Abstract Class**
The best practice is to **define protocols with interfaces and implement partial logic with abstract classes**:  
```csharp
// 1. Define a stable interface (protocol)
public interface IAnimal {
    void Eat();
    void Sleep();
}

// 2. Abstract class implements partial logic as a base type
public abstract class Mammal : IAnimal {
    public virtual void Eat() { Console.WriteLine("Chewing..."); }  // Default implementation
    public abstract void Sleep();  // Forced subclass implementation
}

// 3. Concrete classes inherit and extend
public class Dog : Mammal {
    public override void Sleep() { Console.WriteLine("Snoring..."); }
}
```


### **V. Decision Tree Summary**
1. **Do you need to define a stable cross-type protocol?**  
   ✅ Yes → Use an interface (e.g., `IEnumerable<T>`, `IComparable<T>`).  

2. **Do you need to share state or implementation logic?**  
   ✅ Yes → Use an abstract class (e.g., `DbContext`, `ControllerBase`).  

3. **Do you need to combine multiple behaviors?**  
   ✅ Yes → Use interfaces (e.g., `IDisposable`, `IFormattable`).  

4. **Do you need to control subclass evolution?**  
   ✅ Yes → Use an abstract class (via `sealed`, `abstract`, etc.).  


### **VI. Common Misconceptions**
- **Misconception 1: Interfaces are "lighter" and should be preferred**  
  Interface calls may involve extra indirection (virtual method tables), and performance isn’t necessarily better than abstract classes.  

- **Misconception 2: Abstract classes are outdated**  
  Abstract classes remain irreplaceable when sharing implementations and state, such as `ControllerBase` in ASP.NET Core.  

- **Misconception 3: Default interface methods can fully replace abstract classes**  
  Default methods can’t solve state management or constructor issues, where abstract classes remain essential.  

**Final Principle**: Choose interfaces or abstract classes based on design intent, combining them when necessary to balance flexibility and constraints.

The KISS principle was coined by the U.S. Navy in the 1960s and has since become a foundational idea in engineering.

**Definition:**

> **“Most systems work best if they are kept simple rather than made complex. Therefore, simplicity should be a key goal in design.”**

In software, this means writing code that is:

- Easy to read
- Easy to understand
- Easy to change

The simpler the code, the fewer the bugs. The fewer the bugs, the more reliable the system.

---

```java
interface Operation {
    double calculate(double a, double b);
}

class Addition implements Operation {
    public double calculate(double a, double b) { return a + b; }
}

class Subtraction implements Operation {
    public double calculate(double a, double b) { return a - b; }
}

class Calculator {
    public double execute(Operation op, double a, double b) {
        return op.calculate(a, b);
    }
}

public class Main {
    public static void main(String[] args) {
        Calculator calc = new Calculator();
        Operation add = new Addition();
        Operation sub = new Subtraction();

        double r1 = calc.execute(add, 2, 3);
        double r2 = calc.execute(sub, 9, 4);

        System.out.println("Result 1: " + r1);
        System.out.println("Result 2: " + r2);
    }
}
```

>  Side Note - Those two lines are classic **polymorphism/Strategy** wiring. What’s happening:
- **Right side (`new Addition()` / `new Subtraction()`)**: you’re **instantiating concrete classes** that implement `Operation`. Objects are created on the heap; default constructors run.
- **Left side (`Operation add` / `Operation sub`)**: the **reference type is the interface**. This is an **upcast** to `Operation`, which:
    - Lets you treat different operations uniformly.
    - Enables **dynamic dispatch**: calling `op.calculate(a,b)` will invoke `Addition.calculate` or `Subtraction.calculate` at **runtime**, based on the actual object.
- **Compile-time vs runtime types**:
    - Compile-time type: `Operation`
    - Runtime type: `Addition` / `Subtraction`
    - You can only call methods declared on `Operation`; class-specific extras (if any) are hidden unless you downcast.

This design is flexible. You can add more operations. You can inject behaviors. It is also completely **over-engineered** for a four-function calculator.

```java
class Calculator {
    public double calculate(String operator, double a, double b) {
        switch (operator) {
            case "+":
                return a + b;
            case "-":
                return a - b;
            case "*":
                return a * b;
            case "/":
                if (b == 0) throw new IllegalArgumentException("Division by zero");
                return a / b;
            default:
                throw new UnsupportedOperationException("Unknown operator: " + operator);
        }
    }
}
```

More Examples 

```java
// Bad Code: Process order logic is combined into a single function
public class OrderProcessor {
  public static double processOrder(Item[] order, double taxRate) {
    double total = 0;
    for (Item item : order) {
      total += item.price * item.quantity; // Calculate item totals
    }
    total += total * taxRate; // Add tax
    return total; // Return final total
  }
}
```

```java
class Item {
  double price;
  int quantity;
  Item(double price, int quantity) {
    this.price = price;
    this.quantity = quantity;
  }
}

public class OrderProcessor {
  public static double calculateSubtotal(Item[] order) {
    double subtotal = 0;
    for (Item item : order) {
      subtotal += item.price * item.quantity; // Calculate subtotal
    }
    return subtotal;
  }

  public static double calculateTotal(double subtotal, double taxRate) {
    return subtotal + subtotal * taxRate; // Add tax to subtotal
  }

  public static void main(String[] args) {
    Item[] order = {
        new Item(100, 2), // Item 1: Price = 100, Quantity = 2
        new Item(50, 3) // Item 2: Price = 50, Quantity = 3
    };
    double taxRate = 0.1; // 10% tax
    double subtotal = calculateSubtotal(order);
    double total = calculateTotal(subtotal, taxRate);
    System.out.println("Subtotal: " + subtotal); // Output: Subtotal: 350.0
    System.out.println("Total: " + total); // Output: Total: 385.0
  }
}
```

> This is also not very good as will calculating subtotal and total we are hardcoding the operation and not passing a operation strategy to tell how to do the calculation.
---

## How to Apply the KISS Principle

#### 1. Write Code for Humans, Not Machines
 - Optimizing for readability and clarity helps everyone on the team. Your future self will thank you.
#### 2. Avoid Premature Abstraction
- Abstractions should emerge from repetition or clear need, not from imagination.
#### 3. Favor Composition Over Inheritance
- Simple, flat structures often work better than deep hierarchies.
#### 4. Keep Functions Short
- Small functions are easier to understand and test. If a function is hard to name, it’s probably doing too much.
#### 5. Use Familiar Constructs
- Stick to patterns and structures that are widely recognized. Do not reinvent the wheel when a simple List, Map, or loop can do the job.

---

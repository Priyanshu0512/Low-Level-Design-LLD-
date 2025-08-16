> **“Always implement things when you actually need them, never when you just foresee that you need them.”** — Ron Jeffries, co-founder of Extreme Programming

YAGNI is a principle that encourages you to **resist the temptation to build features or add flexibility until you are absolutely sure you need them**.

In simple terms: **Don’t build for tomorrow. Build for today.**

---
### Why Premature Work Is Harmful?

#### 1. Wasted Time and Effort
Every hour spent building features that are not needed is time not spent building what actually matters.
#### 2. Increased Complexity
Extra flexibility adds more moving parts. It becomes harder to understand, test, and modify your code.
#### 3. Delayed Value
By working on “someday” features, you delay shipping the features users need today.
#### 4. Higher Maintenance Costs
Even unused features have a cost. They can introduce bugs, require updates, and get in the way of refactoring.

---
Suppose you're in an interview, and the interviewer asks you to design a PaymentProcessor class. The requirements are simple:
	• The system should only support debit and credit card payments.
	• The interviewer stresses that the focus is on meeting current requirements without unnecessary complexity.

```java
// Bad Code: Adds unnecessary payment methods not required by the interviewer
class PaymentProcessor {
  private String paymentMethod;
  public PaymentProcessor(String paymentMethod) {
    this.paymentMethod = paymentMethod;
  }
  // Processes payment but includes logic for unsupported future payment methods
  public void processPayment(double amount) {
    if (paymentMethod.equalsIgnoreCase("CreditCard")) {
      System.out.println(
          "Processing payment of $" + amount + " via Credit Card.");
    } else if (paymentMethod.equalsIgnoreCase("DebitCard")) {
      System.out.println(
          "Processing payment of $" + amount + " via Debit Card.");
    } else if (paymentMethod.equalsIgnoreCase("PayPal")) {
     
      System.out.println("Processing payment of $" + amount + " via PayPal.");
    } else if (paymentMethod.equalsIgnoreCase("Crypto")) {
      System.out.println(
          "Processing payment of $" + amount + " via Cryptocurrency.");
    } else {
      System.out.println("Payment method not supported.");
    }
  }
}

public class Main {
  public static void main(String[] args) {
    PaymentProcessor processor = new PaymentProcessor("CreditCard");
    processor.processPayment(
        100); 
    PaymentProcessor invalidProcessor = new PaymentProcessor("PayPal");
    invalidProcessor.processPayment(
        50);
  }
}
```

```java
class PaymentProcessor {
  private String paymentMethod;
  public PaymentProcessor(String paymentMethod) {
    this.paymentMethod = paymentMethod;
  }

  public void processPayment(double amount) {
    if (paymentMethod.equalsIgnoreCase("CreditCard")) {
      System.out.println(
          "Processing payment of $" + amount + " via Credit Card.");
    } else if (paymentMethod.equalsIgnoreCase("DebitCard")) {
      System.out.println(
          "Processing payment of $" + amount + " via Debit Card.");
    }
    else {
      System.out.println("Payment method not supported.");
    }
  }
}

public class Main {
  public static void main(String[] args) {
    // Interviewer's requirement: Only DebitCard and CreditCard payments
    PaymentProcessor processor = new PaymentProcessor("CreditCard");
    processor.processPayment(
        100); // Output: Processing payment of $100 via Credit Card.
  }
}```

> The YAGNI principle can be valuable in various aspects of software development. It promotes simplicity, reduces unnecessary complexity, and helps teams focus on delivering essential functionality. By considering to YAGNI, developers can enhance productivity, maintainability, and overall project success. However, it's important to make a balance and not misinterpret YAGNI as an excuse for neglecting foresight or architectural considerations.

---

# When to Bend the Rule

Like all principles, YAGNI has exceptions. Sometimes, planning ahead is justified.

Here are cases where it’s acceptable to go beyond current needs:
- **Security and compliance requirements:** You may need to prepare for data protection, auditing, or regulatory constraints up front.
- **Architecture with known long-term constraints:** For example, if you are writing code for a high-availability system, some abstractions or patterns may be required from day one.
- **Reusable libraries or frameworks:** If you are building tools for other developers, some flexibility may be expected.

---

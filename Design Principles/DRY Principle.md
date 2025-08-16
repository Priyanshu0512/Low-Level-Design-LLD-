
> **“Every piece of knowledge must have a single, unambiguous, authoritative representation within a system.”** — The Pragmatic Programmer

The DRY principle encourages you to avoid duplicating logic, behavior, or knowledge.

It applies not only to code, but also to:
- Business rules
- Configuration
- Data models
- Documentation
- Tests

Whenever the same concept appears in more than one place, you introduce redundancy. Redundancy makes your system harder to maintain and more prone to bugs.

---

# Why Repetition Is a Problem

#### 1. Harder to Maintain
When a rule or piece of logic changes, you must find and update every occurrence. Missing even one leads to inconsistent behavior.
#### 2. Higher Risk of Bugs
More copies mean more chances to introduce errors. A typo or mismatch in one version of the logic can cause unexpected failures.
#### 3. Bloated Codebase
Redundant logic adds noise to your codebase, making it harder to understand what is unique versus what is shared.
#### 4. Poor Test Coverage
When logic is repeated in many places, each version needs its own set of tests. This increases the testing effort and complexity.

---

## How to Apply DRY

#### Step 1: Create a Utility Class

```java 
public class EmailValidator {
    public static boolean isValid(String email) {
        return email != null &&
               email.contains("@") &&
               email.contains(".") &&
               (email.endsWith(".com") || email.endsWith(".org"));
    }
}```

#### Step 2: Use It Across Modules
```java
if (EmailValidator.isValid(user.getEmail())) {
    // Proceed with business logic
}
```

---

```java
class SubmitButton{
    void onClick(){
        System.out.println("Form submitted");
    }
}

class CancelButton{
    void onClick(){
        System.out.println("Action canceled");
    }
}

public class Main {
    public static void main(String[] args){
        SubmitButton submit = new SubmitButton();
        submit.onClick();

        CancelButton cancel = new CancelButton();
        cancel.onClick();
    }
}
```

 Here `onclick` function is repeated twice;

```java
abstract class Button{
    abstract void onClick();
}
class SubmitButton extends Button{ 
    @Override
    void onClick(){
        System.out.println("Form submitted");
    }
}

class CancelButton extends Button{
    @Override
    void onClick(){
        System.out.println("Action canceled");
    }
}

public class Main {
    public static void main(String[] args){
        SubmitButton submit = new SubmitButton();
        submit.onClick();

        CancelButton cancel = new CancelButton();
        cancel.onClick();
    }
}```

```java 
class Button{
    private String label;
    public Button(String label){
        this.label = label;
    }

    public void render(){
        // render implementation
    }
}


public class Main{
    public static void main(String[] args){
        Button submitButton = new Button("Submit");
        submitButton.render();

        Button cancelButton = new Button("Cancel");
        cancelButton.render();
    }
}
```

> In this approach, if you introduce new buttons you do not need to repeat the `onClick()` implementation each time. The code simply needs to `override` onClick() method of the abstract class and implement its own logic for each new button type. This ensures that the common behaviour is defined in the abstract class while specific behaviour for each button type is implemented in the subclass. 
  
---

## When It Is Okay to Repeat

The DRY principle is a guideline, not a strict rule. There are times when repetition is acceptable.
#### 1. Avoid Premature Abstractions
Do not extract shared code too early. Let duplication reveal itself first. Abstractions created too soon can be misleading or hard to maintain.

> “Duplication is far cheaper than the wrong abstraction.” — Sandi Metz
#### 2. Keep Tests Readable
In some cases, repeating a bit of test code improves clarity. Tests should be easy to read and understand.
#### 3. Keep It Simple
If a line of code is extremely simple and unlikely to change, it may be better to repeat it rather than create a new layer of indirection.

---
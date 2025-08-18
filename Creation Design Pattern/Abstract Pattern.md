
>The **Abstract Factory Design Pattern** is a **creational pattern** that provides an interface for creating families of related or dependent objects without specifying their concrete classes.

---

The **Abstract Factory Pattern** encapsulates object creation into **factory interfaces**.

Each concrete factory implements the interface and produces a complete set of related objects. This ensures that your code remains **extensible, consistent, and loosely coupled** to specific product implementations.


## Problem 

```java

class WindowsButton {
    public void paint() {
        System.out.println("Painting a Windows-style button.");
    }

    public void onClick() {
        System.out.println("Windows button clicked.");
    }
}

class WindowsCheckbox {
    public void paint() {
        System.out.println("Painting a Windows-style button.");
    }

    public void onSelect() {
        System.out.println("Windows button clicked.");
    }
}


class MacOSButton {
    public void paint() {
        System.out.println("Painting a macOS-style button.");
    }

    public void onClick() {
        System.out.println("macOS button clicked.");
    }
}

class MacOSCheckbox {
    public void paint() {
        System.out.println("Painting a macOS-style checkbox.");
    }

    public void onSelect() {
        System.out.println("macOS checkbox selected.");
    }
}

public class App {
    public static void main(String[] args) {
        String os = System.getProperty("os.name");

        if (os.contains("Windows")) {
            WindowsButton button = new WindowsButton();
            WindowsCheckbox checkbox = new WindowsCheckbox();
            button.paint();
            checkbox.paint();
        } else if (os.contains("Mac")) {
            MacOSButton button = new MacOSButton();
            MacOSCheckbox checkbox = new MacOSCheckbox();
            button.paint();
            checkbox.paint();
        }
    }
}

```


### Why this is problematic??

1. **Tight coupling to concrete classes** - This means everywhere you create UI components , you must check the OS manually.
2. **No Abstraction or Polymorphism** - There's no common interface like `Button` or `Checkbox` to work with.
3. **Violation of Open/Closed Principle** - Any new variant requires modifying existing code.

---

## Implementing Abstract Factory

```java

interface Button{
    void paint();
    void onClick();
}

interface Checkbox{
    void paint();
    void onSelect();
}

class WindowsButton implements Button{
    @Override
    public void paint(){
        // implements paints
    }

    @Override 
    public void onClick(){
        // implements onClick
    }
}

class WindowsCheckbox implements Checkbox{
    @Override
    public void paint(){
        // implements paints
    }

    @Override 
    public void onSelect(){
        // implements onClick
    }
}


class MacOSButton implements Button {
    @Override
    public void paint(){
        // implements paints
    }

    @Override
    public void  onClick(){
        // implements onClick
    }
}

class MacOSCheckbox implements Checkbox {
    @Override
    public void paint(){
        // implements paints
    }

    @Override
    public void onSelect(){
        // implements onselect
    }
}


interface GUIFactory {
    Button createButton();
    Checkbox createCheckbox();
}

class WindowsFactory implements GUIFactory {
    @Override
    public Button createButton() {
        return new WindowsButton();
    }

    @Override
    public Checkbox createCheckbox() {
        return new WindowsCheckbox();
    }
}

class MacOSFactory implements GUIFactory {
    @Override
    public Button createButton() {
        return new MacOSButton();
    }

    @Override
    public Checkbox createCheckbox() {
        return new MacOSCheckbox();
    }
}


class Application{
    private final Button button;
    private final Checkbox checkbox;

    public Application(GUIFactory factory){
        this.button = factory.createButton();
        this.checkbox = factory.createCheckbox();
    }

    public void renderUI(){
        button.paint();
        checkbox.paint();
    }
}


public class AppLauncher{
    public static void main(String[]args){

        String os = System.getProperty("os.name");
        GUIFactory factory;

        if(os.contains("Windows")){
            factory = new WindowsFactory();
        }
        else {
            factory = new MacOSFactory();
        }

        Application app = new Application(factory);
        app.renderUI();
    }
}

```


- Instead of instantiating objects directly (with `new`), you use an **abstract factory** interface that declares methods for creating related objects.
- Concrete factories implement this interface and produce objects that belong to a particular family.
- The client code only interacts with the factory interface and product interfaces, not the concrete classes. This ensures **loose coupling**.

### What We Achieved

- **Platform independence:** Application code never references platform-specific classes
- **Consistency:** Buttons and checkboxes always match the selected OS style
- **Open/Closed Principle:** Add support for Linux or Android without modifying existing factories or components
- **Testability & Flexibility:** Factories can be swapped easily for testing or theming

---


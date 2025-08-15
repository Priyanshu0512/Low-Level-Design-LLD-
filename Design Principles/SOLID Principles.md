The Solid principles provide a blueprint for writing code that's easy to adjust ,extend and maintain over time.
They were introduced by **Rober C Martin** in early 2000s.

---

## S: Single Responsibility 

> A class should have one and only one reason to change.

This means that a class must have a single responsibility. This makes code easier to maintain and in future in further modifications are required in class with other responsibility or task only those needs to be updated. Keep the classes decoupled.Each class has a focused responsibility.

```java
class BreadBaker{
	public void bakeBread(){
		System.out.println("Baking bread");
	}
}
class InventoryManager{
	public void manageInventory(){
		System.out.println("Managing Inventory");
	}
}
class SupplyOrder{
	public void orderSupply(){
		System.out.println("Ordering Supplies");
	}
}

public class Bakery{
	public static void main(String[]args){
	BreadBaker baker = new BreadBaker();
	InventoryManager inventoryManager = new InventoryManager();
	SupplyOrder supplyOrder = new SupplyOrder();

	baker.bakeBread();
	inventoryManager.manageInventory();
	supplyOrder.orderSupply();

	}
}
```

Now each class has a well defined responsibility. Changes to one won't effect others functionality.

---

## O: Open/Closed Principle(OCP)

> Software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification.

```java
class Shape{
    private String type;
    
    public double calculateArea(){
        if(type.equals("circle")){
            // circle area calculation
        }
        else if( type.equals("rectangle")){
            // recntagle area calculation
        }
    }
}
```

Now in the above code if you want to add support for triangle you need to modify the `calculateArea` function which violates the Open/Closed Principle.

```java
abstract class Shape{
    abstract double calculateArea();
}

class Circle  extends Shape{
    private double radius;

    @Override
    public double calculateArea(){
        return Math.PI* radius* radius;
    }
}

class Rectangle extends Shape{
    private double width;
    private double height;

    @Override
    public double calculateArea(){
        return width* height;
    }

}
class Triangle extends Shape{
    private double base;
    private double height;

    @Override
    public double calculateArea(){
        return 0.5*base* height;
    }

}
```

By introducing an abstraction (`Shape` class) and separating the concrete implementations (`Rectangle` and `Circle` classes), we can add new shapes without modifying the existing code.

The `ShapeCalculator` class can now work with any shape that implements the `Shape` interface, allowing for easy extensibility.
**It prevents breaking of existing code.**

---

## L: Liskov Substitution Principle(LSP)

> Objects of a superclass should be replaceable with objects of its subclasses without affecting the correctness of the program.

This means if you have a base class and a derived class, you should be able to use instances of the derived class wherever instances of the base class are expected, without breaking the application.


```java
class Vehicle {
    public void startEngine(){
        //engine starts
    }
}

class Car extends Vehicle{
    @Override
    public void startEngine(){
        // Car specific engine starting logic
    }
}

class Bicycle extends Vehicle{
    @Override
    public void startEngine(){
        throw new UnsupportedOperationException("Bicycles don't have engines");
    }
}

public class Main{
    public static void main(String[]args){
        Vehicle car = new Car();
        Vehicle bicycle = new Bicycle();

        try{
            bicycle.startEngine();
        }
        catch(UnsupportedOperationException e){
            System.out.println("Error: "+ e.getMessage());
        }

    }
}```

In this example, the `Bicycle` class violates the LSP because it provides an implementation for the `start_engine` method, which doesn't make sense for a bicycle.
If we try to substitute a `Bicycle` instance where a `Vehicle` instance is expected, it might lead to unexpected behavior or errors. Also we need to handle exception everywhere where `startEngine` is called on the class `bicycle`.

> Note - When a subclass cannot fulfill the contract of its parent class, it leads to breakdown in polymorphism, making code less reliable and predictable. They bicycle class when forced to implement the `startEngine()` method , it must either provide a meaningless implementation or throw an exception, both of which are undesirable outcomes. 

```java 
abstract class Vehicle{
    public void move(){
        // movement logic
    }
}

abstract class EngineVehicle extends Vehicle{
    public void startEngine(){
        // engine start logic
    }
}

abstract class NonEngineVehicle extends Vehicle{
    // No engine related method
}


class Car extends EngineVehicle{
    @Override
    public void startEngine(){
        // Card specific engine starting logic
    }
}

class Bicycle extends NonEngineVehicle{
    // No need to implement engine-related methods. bicycle specific methods.
}

public class Main{
    public static void main(String[]args){
        EngineVehicle car = new Car();

        car.startEngine();
        car.move();

        NonEngineVehicle bicyle = new Bicycle();
        bicyle.move();
    }
}
```

This design ensures that 
1. Each subtype fully satisfies the behavioral contract of its parent type.
2. Client code can interact with either vehicle type without unexpected behaviour.
3.  The inheritance hierarchy accurately models the real-world domain.

---

## I: Interface Segregation Principle(ISP)

> No client should be forced to depend on interfaces they don't use.

- The main idea behind ISP is to **prevent the creation of "fat" or "bloated"** interfaces that include methods that are not required by all clients.
- It states  that do not force any client to implement an interface which is irrelevant to them.
- By segregating interfaces into smaller, more specific ones, clients only depend on the methods they actually need, promoting loose coupling and better code organization. 

```java
interface Machine{
    void print();
    void scan();
    void fax();
}


class AllInOnePrinter implements Machine{
    @Override
    public void print(){
        // Printing functionality
    }
    @Override
    public void scan(){
        // scanning functionality
    }

    @Override
    public void fax(){
        // Fax functionality
    }

}

class BasicPrinter implements Machine{
    @Override
    public void print(){
        // Printing functionality
    }
    @Override
    public void scan(){
        throw new UnsupportedOperationException("cannot Scan");
    }
    @Override
    public void fax(){
        throw new UnsupportedOperationException("cannot fax");
    }
}
```

Now here although `BasicPrinter`  does not require  `scan` and `fax` methods they need to be implemented despite being not required hence this needs to be handled everywhere. This code becomes harder to implement.


```java
interface Printer{
    void print();
}

interface Scanner{
    void scan();
}

interface FaxMachnine{
    void fax();
}

class BasicPrinter implements Printer{
    @Override
    public void print(){
        // Printing func
    }
}

class AllInOnePrinter implements Printer, Scanner, FaxMachnine{
    @Override
    public void print(){
        // print func
    }

    @Override
    public void fax(){
        // fax func
    }

    @Override 
    public void scan(){
        // Scan func
    }
}
```

The above code follows
- Follows the principles of **interface cohesion** - Each interface has a specific responsibility, leading to better cohesion and separation of concerns. 
- By segregating interfaces, we ensure that classes only implement methods that are relevant to their functionality, leading to a more robust and maintainable system.

---

## D: Dependency Inversion Principle(DIP)

> High-level modules should not depend on low-level modules; both should depend on abstractions.

This means that a particular class should not depend directly on another class, but on an **abstraction (interface) of this class.**
Applying this principle reduces dependency on specific implementations and makes our code more reusable.

```java
class EmailNotifier{
    public void sendEmail(String message){
        // Configure SMTP
        // Setup email Templates
        // Send email implementation
    }
}

class DatabaseLogger{
    public void logTransactions(){
        // logging logic
    }
}


class OrderService{
    private EmailNotifier emailNotifier;
    private DatabaseLogger logger;


    public OrderService(){
        this.emailNotifier = new EmailNotifier();
        this.logger = new DatabaseLogger();
    }


    public void placeOrder(Order order){
        emailNotifier.sendEmail("Order # "+ order.getId());
        logger.logTransactions("Order Placed"+ order.getId());
    }
}
```

- **High-level module:** `OrderService` → handles business logic of placing an order.
- **Low-level modules:** `EmailNotifier` (sending emails) and `DatabaseLogger` (logging transactions).
- **Missing abstraction:** There’s no `Notifier` interface or `Logger` interface.

- `OrderService` **creates** and **directly depends on** concrete classes.
- If tomorrow you want:
    - To send SMS instead of Email,
    - Or log to a file instead of a database, you must **open and change `OrderService` code**, breaking the **Open/Closed Principle** too.
#### Why this is a problem
- **Tight coupling:** `OrderService` is locked to specific implementations.
- **Poor testability:** You can’t easily mock `EmailNotifier` or `DatabaseLogger` in unit tests without changing `OrderService`.
- **Low flexibility:** You can’t swap out implementations at runtime or via configuration.

```java
interface NotificationService{
    void sendNotification(String message);
}

interface LoggingService{
    void logMessage(String message);
    void logError(String error);
}

interface InventoryService{
    void updateStock(Order order);
    boolean checkAvailability(Product product);
}


class EmailNotifier implements NotificationService{
    @Override
    public void sendNotification(String message){
        // Email specific implementation
    }
}

class SMSNotifier implements NotificationService{
    @Override
    public void sendNotification(String message){
        // SMS specific implemenation
    }
}

class DatabaseLogger implements LoggingService{
    @Override
    public void logMessage(String message){
        //Logging implementation
    }
    @Override
    public void logError(String error){
        // error implementation
    }
}


class Product {
    // Add fields and methods as needed
}

class Order {
    public Product getProuct() {
        // Dummy implementation, replace with actual logic
        return new Product();
    }
    public int getId() {
        // Dummy implementation, replace with actual logic
        return 1;
    }
}

class WareHouseInventoryService implements InventoryService {
    @Override 
    public boolean checkAvailability(Product product) {
         return true;
    }
    @Override
    public void updateStock(Order order) { 
        // update implementation
    }
}

class OrderService {
    private final NotificationService  notificationService;
    private final LoggingService loggingService;
    private final InventoryService inventoryService;
    
    public OrderService(
        NotificationService notificationService,
        LoggingService loggingService,
        InventoryService inventoryService
    ){
        this.notificationService = notificationService;
        this.loggingService = loggingService;
        this.inventoryService = inventoryService;
    }

    public void placeOrder(Order order){
        try{
            if(inventoryService.checkAvailability(order.getProuct())){
                inventoryService.updateStock((order));
                notificationService.sendNotification(("Order #"+ order.getId() +"placed successfully."));
                loggingService.logMessage("Error processing order"+ order.getId());
            }
        }
        catch(Exception e){
            loggingService.logError("Error processing order:"+ order.getId()+" " + e.getMessage());
            throw e;
        }

    }
}

class App {
    public static void main(String[] args) {
        NotificationService emailNotifier = new EmailNotifier();
        LoggingService logger = new DatabaseLogger();
        InventoryService inventory = new WareHouseInventoryService();
        OrderService orderService = new OrderService(emailNotifier, logger, inventory);
        orderService.placeOrder(new Order());
    }
}
```

### Why this now follows DIP (and is testable)
- `OrderService` depends only on **abstractions** (`NotificationService`, `LoggingService`, `InventoryService`), not concrete classes.
- You can swap implementations (Email ↔ SMS, DB ↔ File logger, Warehouse ↔ In-memory) without touching `OrderService`.
- In tests, inject mocks/fakes for fast, isolated unit testing.

---



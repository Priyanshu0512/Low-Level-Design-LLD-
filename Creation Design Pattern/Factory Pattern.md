>The **Factory Method Design Pattern** is a **creational pattern** that provides an interface for creating objects in a **superclass**, but allows **subclasses** to alter the type of objects that will be created.

---

It’s particularly useful in situations where:

- The exact type of object to be created isn't known until runtime.
- Object creation logic is **complex**, **repetitive**, or needs **encapsulation**.
- You want to follow the **Open/Closed Principle,** open for extension, closed for modification.

```java
class EmailNotification{
    public void send(String message){
        System.out.println("Sending an Email notification");
    }
}

class SMSNotification{
    public void send(String message){
        System.out.println("Sending an SMS notification");
    }
}

class PushNotification{
    public void send(String message){
        System.out.println("Sending an Push notification");
    }
}

class SlackNotification{
    public void send(String message){
        System.out.println("Sending an Slack notification");
    }
}

class NotificationService {
    public void sendNotification(String type, String message) {
        if (type.equals("EMAIL")) {
            EmailNotification email = new EmailNotification();
            email.send(message);
        } else if (type.equals("SMS")) {
            SMSNotification sms = new SMSNotification();
            sms.send(message);
        } else if (type.equals("Push")) {
            PushNotification sms = new PushNotification();
            sms.send(message);
        } else if (type.equals("Slack")) {
            SlackNotification sms = new SlackNotification();
            sms.send(message);
        }
    }
}
```

This becomes a nightmare to maintain:
- Every time you add a new notification channel, you must **modify the same core logic**.
- Testing becomes cumbersome because the logic is intertwined with object creation.
- It violates key design principles, especially the **Open/Closed Principle**—the idea that classes should be open for extension but closed for modification.
- Introducing new behaviour or properties means updating the creation code everywhere. Example - If in the `send` method the String argument changes to an JSON (object) then this needs to changed at all the places which is not favourable.

---
## What is Factory Method?

The **Factory Method Pattern** takes the idea of object creation and hands it off to **subclasses**. Instead of one central factory deciding what to create, you **delegate the responsibility to specialized classes** that know exactly what they need to produce. 

> Basically it is various subclasses which are creating the objects and their is one factory class which delegates the creation request to these classes.

---
## Implementing Factory Method

```java 
// Define Product interface
interface Notification {
    public void send(String message);
}

// Define Concrete Products
class EmailNotification implements Notification{
    @Override
    public void send(String message){
        System.out.println("Sending email:"+ message);
    }
}

class SMSNotification implements Notification{
    @Override
    public  void send(String message){
        System.out.println(":Sending SMS:" + message);
    }
}

class PushNotification implements Notification {
    @Override
    public void send(String message) {
        System.out.println("Sending push notification: " + message);
    }
}

// Define Abstract Creator

abstract class NotificationCreator{

    //Factory method
    public abstract Notification createNotification();

    //Common Logic
    public void send(String message){
        Notification notification = createNotification();
        notification.send(message);
    }
}

// Concrete Creators
class EmailNotificationCreator extends NotificationCreator{
    @Override
    public Notification createNotification(){
        return new EmailNotification();
    }
}

class SMSNotificationCreator extends NotificationCreator {
    @Override
    public Notification createNotification() {
        return new SMSNotification();
    }
}

class PushNotificationCreator extends NotificationCreator {
    @Override
    public Notification createNotification() {
        return new PushNotification();
    }
}

```


Each one **extends the abstract creator** and implements the `createNotification()` method to return its specific notification type.

This is where the Factory Method magic happens:
- EmailNotificationCreator → returns `new EmailNotification()`
- SMSNotificationCreator → returns `new SMSNotification()`
- PushNotificationCreator → returns `new PushNotification()`

```java
public class FactoryMethod {
    public static void main(String[]args){
        NotificationCreator creator;

        creator = new EmailNotificationCreator();
        creator.send("Email Notification");

        creator = new SMSNotificationCreator();
        creator.send("SMS Notification");

        creator = new PushNotificationCreator();
        creator.send("Push notification");
    }
}
```


- The **`Notification` interface** defines the type of product.
- The **concrete products** (Email, SMS, Push) implement this interface.
- The **abstract creator (`NotificationCreator`)** declares the **factory method** (`createNotification`) and implements common logic (`send`).
- The **concrete creators** override the factory method to produce the right product.
- The **factory method pattern** is demonstrated because object creation is delegated to subclasses, while the parent class provides a consistent workflow.

---
## Disadvantages of Factory Method

1. **Class Explosion**
    - Every new product requires a new creator subclass.
    - For many products, this leads to a large number of small classes → harder to manage.
2. **Complexity Overhead**
    - For simple cases, it’s over-engineering compared to a Simple Factory.
    - Example: If you only have 2–3 notification types, a Simple Factory is enough.
3. **Tighter Hierarchy Dependency**
    - You must follow the inheritance structure.
    - If you later want multiple factory strategies without subclassing, the pattern becomes restrictive.
4. **Indirection Can Be Overkill**
    - Debugging or tracing which class creates what can be less transparent.
    - Might make the system harder to read for new developers.

---


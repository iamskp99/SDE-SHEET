# Single Responsibility Principle

The **Single Responsibility Principle (SRP)** is one of the SOLID principles of object-oriented design. It states that **a class should have only one reason to change**, meaning it should only have one responsibility. In other words, a class should focus on a single part of the functionality of a program.

### Problem: Violating SRP

Suppose we have a `Report` class that handles multiple responsibilities: generating report data, formatting the report, and saving it to a file.

```python
class Report:
    def __init__(self, title, content):
        self.title = title
        self.content = content

    def generate(self):
        return f"{self.title}\n{self.content}"

    def format_as_html(self):
        return f"<h1>{self.title}</h1><p>{self.content}</p>"

    def save_to_file(self, filename):
        with open(filename, "w") as file:
            file.write(self.generate())
```

#### Why This Violates SRP
- **Responsibilities**:
  1. Generating the report data (`generate`).
  2. Formatting the report (`format_as_html`).
  3. Saving the report to a file (`save_to_file`).

If we need to change the way reports are saved (e.g., save to a database instead of a file) or formatted (e.g., JSON instead of HTML), this class will require modification. This violates SRP because the class has multiple reasons to change.

---

### Solution: Apply SRP

We can refactor this by dividing the responsibilities into separate classes.

```python
class Report:
    def __init__(self, title, content):
        self.title = title
        self.content = content

    def generate(self):
        return f"{self.title}\n{self.content}"

class ReportFormatter:
    @staticmethod
    def format_as_html(report: Report):
        return f"<h1>{report.title}</h1><p>{report.content}</p>"

class ReportSaver:
    @staticmethod
    def save_to_file(report: Report, filename):
        with open(filename, "w") as file:
            file.write(report.generate())
```

#### How It Follows SRP:
- **`Report`**: Handles report data generation.
- **`ReportFormatter`**: Handles formatting the report.
- **`ReportSaver`**: Handles saving the report.

---

### Usage

```python
# Create a report
report = Report("Monthly Report", "This is the content of the report.")

# Format the report as HTML
formatted_report = ReportFormatter.format_as_html(report)
print(formatted_report)
# Output: <h1>Monthly Report</h1><p>This is the content of the report.</p>

# Save the report to a file
ReportSaver.save_to_file(report, "report.txt")
```

---

### Benefits of Following SRP
1. **Improved Maintainability**:
   - If you need to change how reports are saved, you modify `ReportSaver` without touching `Report` or `ReportFormatter`.
2. **Ease of Testing**:
   - Each class can be tested independently.
3. **Reusability**:
   - `ReportFormatter` and `ReportSaver` can be reused in other parts of the application.

By separating the responsibilities, each class has only one reason to change, adhering to the Single Responsibility Principle.

# Abstract Method

An **abstract method** is a method that is declared in an abstract class but does not have an implementation. It is meant to be overridden by subclasses. Abstract methods serve as a template for enforcing a contract that subclasses must follow, ensuring they implement specific behavior.

In Python, abstract methods are defined in classes that inherit from the `ABC` (Abstract Base Class) module, which is part of the `abc` standard library.

---

### Key Points About Abstract Methods:
1. **No Implementation**: Abstract methods have no body (implementation) in the abstract base class.
2. **Enforced Implementation**: Subclasses that inherit the abstract class must override all its abstract methods, or the subclass itself becomes abstract and cannot be instantiated.
3. **Use of `@abstractmethod`**: Abstract methods are decorated with `@abstractmethod` to indicate they must be overridden in derived classes.

---

### Example of an Abstract Method:

```python
from abc import ABC, abstractmethod

# Abstract Base Class
class Animal(ABC):
    @abstractmethod
    def make_sound(self):
        """Each animal must implement this method"""
        pass

# Subclasses must override the abstract method
class Dog(Animal):
    def make_sound(self):
        return "Woof!"

class Cat(Animal):
    def make_sound(self):
        return "Meow!"

# Usage
dog = Dog()
cat = Cat()

print(dog.make_sound())  # Output: Woof!
print(cat.make_sound())  # Output: Meow!
```

---

### Attempting to Instantiate an Abstract Class or Incomplete Subclass

If you try to instantiate an abstract class or a subclass that has not implemented all abstract methods, Python will raise a `TypeError`:

```python
# This will raise an error because Animal has an abstract method
animal = Animal()
```

```python
# This will also raise an error
class Bird(Animal):
    pass

bird = Bird()  # TypeError: Can't instantiate abstract class Bird with abstract method make_sound
```

---

### Why Use Abstract Methods?
Abstract methods are useful for:
1. Enforcing a common interface for subclasses.
2. Encouraging proper structure and consistency in your codebase.
3. Supporting polymorphism, allowing objects of different subclasses to be used interchangeably.

# Open/Closed Principle (OCP)

The Open/Closed Principle (OCP) is one of the SOLID principles of object-oriented design. It states that software entities like classes, modules, and functions should be **open for extension but closed for modification**. This means you should be able to add new functionality to a system without modifying existing code.

Here’s an example in Python:

---

### Problem: Without OCP
Let’s say we have a `PaymentProcessor` class that processes different payment methods like credit cards and PayPal.

```python
class PaymentProcessor:
    def process_payment(self, payment_method):
        if payment_method == "credit_card":
            print("Processing credit card payment...")
        elif payment_method == "paypal":
            print("Processing PayPal payment...")
        else:
            print("Unsupported payment method!")
```

If a new payment method (e.g., Apple Pay) needs to be added, we would have to modify the `process_payment` method. This violates the OCP because the class isn't closed for modification.

---

### Solution: Apply OCP
We can refactor this by introducing an abstract base class for payment methods and making the `PaymentProcessor` class open for extension.

```python
from abc import ABC, abstractmethod

# Abstract base class for payment methods
class PaymentMethod(ABC):
    @abstractmethod
    def process(self):
        pass

# Implementation of different payment methods
class CreditCardPayment(PaymentMethod):
    def process(self):
        print("Processing credit card payment...")

class PayPalPayment(PaymentMethod):
    def process(self):
        print("Processing PayPal payment...")

class ApplePayPayment(PaymentMethod):
    def process(self):
        print("Processing Apple Pay payment...")

# Payment processor
class PaymentProcessor:
    def process_payment(self, payment_method: PaymentMethod):
        payment_method.process()

# Usage
processor = PaymentProcessor()

# Process different payment methods
processor.process_payment(CreditCardPayment())
processor.process_payment(PayPalPayment())
processor.process_payment(ApplePayPayment())
```

---

### Advantages
1. The `PaymentProcessor` class doesn’t need to change if a new payment method is introduced.  
2. Adding a new payment method involves only creating a new class that implements the `PaymentMethod` interface.  

This demonstrates how the Open/Closed Principle improves flexibility and maintainability in your code.

# Liskov Substitution Principle

The **Liskov Substitution Principle (LSP)** states that **objects of a superclass should be replaceable with objects of a subclass without affecting the correctness of the program.** This means a subclass must fulfill the expectations set by its parent class, ensuring that it behaves consistently in all contexts where the parent is used.

---

### Problem: Violating LSP

Let’s consider a base class `Bird` with a method `fly`. We create a subclass `Penguin` which does not logically support flying.

```python
class Bird:
    def fly(self):
        print("This bird is flying.")

class Sparrow(Bird):
    def fly(self):
        print("Sparrow is flying.")

class Penguin(Bird):
    def fly(self):
        raise NotImplementedError("Penguins can't fly.")
```

#### Why This Violates LSP:
1. A `Penguin` is a `Bird`, but it cannot fulfill the behavior of the `fly` method defined in the `Bird` class.
2. Code expecting all `Bird` instances to fly would break when a `Penguin` object is used.
   
For example:

```python
def make_bird_fly(bird: Bird):
    bird.fly()

# Works fine
sparrow = Sparrow()
make_bird_fly(sparrow)  # Output: Sparrow is flying.

# Breaks the program
penguin = Penguin()
make_bird_fly(penguin)  # Raises NotImplementedError
```

---

### Solution: Apply LSP

Refactor the design by creating a more specific hierarchy. Not all birds can fly, so we introduce a `FlyableBird` class to handle birds that can fly, separating the functionality.

```python
from abc import ABC, abstractmethod

# Base class
class Bird(ABC):
    @abstractmethod
    def make_sound(self):
        pass

# FlyableBird class for birds that can fly
class FlyableBird(Bird):
    @abstractmethod
    def fly(self):
        pass

# Specific bird classes
class Sparrow(FlyableBird):
    def make_sound(self):
        print("Chirp chirp!")

    def fly(self):
        print("Sparrow is flying.")

class Penguin(Bird):
    def make_sound(self):
        print("Honk honk!")

# Now penguins are not required to implement `fly`.
```

---

### How It Follows LSP:

1. **Refined Abstraction**:
   - `FlyableBird` ensures that only birds that can fly implement the `fly` method.
2. **Behavioral Substitution**:
   - `Sparrow` can replace `FlyableBird` instances without breaking code.
   - `Penguin` does not try to override `fly`, avoiding a mismatch with the base class's expectations.

---

### Usage

```python
def make_bird_sound(bird: Bird):
    bird.make_sound()

def make_flyable_bird_fly(flyable_bird: FlyableBird):
    flyable_bird.fly()

# Examples
sparrow = Sparrow()
penguin = Penguin()

make_bird_sound(sparrow)  # Output: Chirp chirp!
make_bird_sound(penguin)  # Output: Honk honk!

make_flyable_bird_fly(sparrow)  # Output: Sparrow is flying.
# make_flyable_bird_fly(penguin)  # This would now raise a type error, as expected.
```

---

### Benefits of Following LSP:
1. **Consistency**:
   - Subclasses behave as expected in all contexts where the base class is used.
2. **Reduced Errors**:
   - Prevents situations where subclasses break functionality due to unsupported behavior.
3. **Improved Design**:
   - Clearer abstraction makes it easier to understand and maintain the hierarchy.

By segregating `FlyableBird` from `Bird`, we ensure that only birds that can actually fly implement the `fly` method, adhering to the Liskov Substitution Principle.

# Interface Segregation Principle (ISP)

The **Interface Segregation Principle (ISP)** is another of the SOLID principles. It states that **a class should not be forced to implement interfaces it does not use.** Instead of creating a large, all-encompassing interface, break it into smaller, more specific ones to keep implementations lean and focused.

---

### Problem: Violating ISP

Imagine we have an `Animal` interface (or abstract class) that defines methods for different kinds of animals, like flying, swimming, and walking.

```python
from abc import ABC, abstractmethod

class Animal(ABC):
    @abstractmethod
    def fly(self):
        pass

    @abstractmethod
    def swim(self):
        pass

    @abstractmethod
    def walk(self):
        pass

# Implementing classes
class Dog(Animal):
    def fly(self):
        raise NotImplementedError("Dogs cannot fly.")

    def swim(self):
        print("Dog is swimming.")

    def walk(self):
        print("Dog is walking.")

class Bird(Animal):
    def fly(self):
        print("Bird is flying.")

    def swim(self):
        raise NotImplementedError("Birds cannot swim.")

    def walk(self):
        print("Bird is walking.")
```

#### Why This Violates ISP:
- Both `Dog` and `Bird` are forced to implement methods they don't use (`fly` for Dog and `swim` for Bird).
- This leads to unnecessary code (`NotImplementedError`) and makes the design harder to maintain.

---

### Solution: Apply ISP

Break the `Animal` interface into smaller, more specific interfaces for flying, swimming, and walking animals.

```python
from abc import ABC, abstractmethod

# Separate interfaces
class Flyable(ABC):
    @abstractmethod
    def fly(self):
        pass

class Swimmable(ABC):
    @abstractmethod
    def swim(self):
        pass

class Walkable(ABC):
    @abstractmethod
    def walk(self):
        pass

# Implementing classes
class Dog(Swimmable, Walkable):
    def swim(self):
        print("Dog is swimming.")

    def walk(self):
        print("Dog is walking.")

class Bird(Flyable, Walkable):
    def fly(self):
        print("Bird is flying.")

    def walk(self):
        print("Bird is walking.")
```

---

### How It Follows ISP:
- **Dog**:
  - Implements only the `Swimmable` and `Walkable` interfaces.
- **Bird**:
  - Implements only the `Flyable` and `Walkable` interfaces.
- No unnecessary or unused methods are forced on any class.

---

### Usage

```python
# Create instances
dog = Dog()
bird = Bird()

# Dog behavior
dog.walk()  # Output: Dog is walking.
dog.swim()  # Output: Dog is swimming.

# Bird behavior
bird.walk()  # Output: Bird is walking.
bird.fly()   # Output: Bird is flying.
```

---

### Benefits of Following ISP:
1. **Reduced Code Complexity**:
   - Classes implement only what they need, leading to simpler, cleaner code.
2. **Improved Maintainability**:
   - Changes to one interface don’t affect classes that don’t use it.
3. **Better Modularity**:
   - Interfaces can be reused independently in other parts of the application.

By segregating the interfaces, we avoid forcing classes to implement methods they don't use, adhering to the Interface Segregation Principle.

# Dependency Inversion Principle (DIP)

The **Dependency Inversion Principle (DIP)** is one of the SOLID principles. It states that **high-level modules should not depend on low-level modules; both should depend on abstractions.** Additionally, abstractions should not depend on details; details should depend on abstractions.

In simple terms, DIP promotes decoupling by ensuring that higher-level components don’t depend directly on lower-level components, but rather on interfaces or abstract classes.

---

### Problem: Violating DIP

Suppose we have a `FileLogger` class that writes logs to a file, and a `UserService` class that uses this logger.

```python
class FileLogger:
    def log(self, message):
        print(f"Logging to file: {message}")

class UserService:
    def __init__(self):
        self.logger = FileLogger()  # Direct dependency on FileLogger

    def register_user(self, username):
        # Logic to register the user
        self.logger.log(f"User {username} registered.")
```

#### Why This Violates DIP:
1. The `UserService` class depends directly on the `FileLogger` class (a low-level module).
2. If the logging mechanism changes (e.g., to a database or a cloud service), the `UserService` class must be modified.
3. There’s no abstraction between the high-level `UserService` and the low-level `FileLogger`.

---

### Solution: Apply DIP

We can introduce an abstraction (interface) for the logging functionality and make both `UserService` and `FileLogger` depend on this abstraction.

#### Refactored Code:

```python
from abc import ABC, abstractmethod

# Abstraction
class Logger(ABC):
    @abstractmethod
    def log(self, message):
        pass

# Low-level module
class FileLogger(Logger):
    def log(self, message):
        print(f"Logging to file: {message}")

# Another low-level module
class ConsoleLogger(Logger):
    def log(self, message):
        print(f"Logging to console: {message}")

# High-level module
class UserService:
    def __init__(self, logger: Logger):
        self.logger = logger  # Depends on abstraction, not implementation

    def register_user(self, username):
        # Logic to register the user
        self.logger.log(f"User {username} registered.")
```

---

### How It Follows DIP:
1. **Abstraction Dependency**:
   - `UserService` depends on the `Logger` interface, not a specific implementation like `FileLogger` or `ConsoleLogger`.
2. **Easily Swappable Dependencies**:
   - You can easily switch the logging mechanism without modifying the `UserService` class.

---

### Usage

```python
# Using FileLogger
file_logger = FileLogger()
user_service_with_file_logger = UserService(file_logger)
user_service_with_file_logger.register_user("Alice")
# Output: Logging to file: User Alice registered.

# Using ConsoleLogger
console_logger = ConsoleLogger()
user_service_with_console_logger = UserService(console_logger)
user_service_with_console_logger.register_user("Bob")
# Output: Logging to console: User Bob registered.
```

---

### Benefits of Following DIP:
1. **Decoupling**:
   - High-level classes are not tightly coupled to low-level classes.
2. **Flexibility**:
   - Adding a new logging mechanism (e.g., `DatabaseLogger`) doesn’t require changes in `UserService`.
3. **Testability**:
   - The `Logger` abstraction makes it easier to test the `UserService` class by injecting mock loggers.

By introducing an abstraction (`Logger`), both the high-level (`UserService`) and low-level (`FileLogger`, `ConsoleLogger`) modules depend on a common interface, adhering to the Dependency Inversion Principle.







# Strategy Design Pattern

The **Strategy Design Pattern** is a behavioral design pattern that defines a family of algorithms, encapsulates each one, and makes them interchangeable. This pattern enables the algorithm to vary independently from the clients that use it.

Here are some common **uses of the Strategy Design Pattern**:

---

### 1. **Dynamic Algorithm Selection**
   - When an application needs to choose from multiple algorithms at runtime based on certain conditions or user preferences.
   - **Example:** A payment processing system where different strategies handle different payment methods (e.g., credit card, PayPal, cryptocurrency).

---

### 2. **Behavior Customization**
   - When a class's behavior needs to be customized without modifying the class itself.
   - **Example:** A sorting utility where the sorting strategy (e.g., quicksort, mergesort, bubblesort) can be switched dynamically.

---

### 3. **Avoiding Conditional Logic**
   - When there are many `if-else` or `switch` statements for choosing behaviors, the Strategy Pattern replaces them with polymorphism.
   - **Example:** A game where different attack strategies (e.g., melee, ranged, magic) are encapsulated in separate strategy classes.

---

### 4. **Promoting Open/Closed Principle**
   - Adding new behaviors without modifying the existing code.
   - **Example:** A discount system for an e-commerce platform where new discount strategies (e.g., percentage-based, flat-rate, buy-one-get-one) can be added without altering existing logic.

---

### 5. **Reusable Code**
   - When you need to reuse specific behavior across multiple contexts without duplicating code.
   - **Example:** Logging strategies (e.g., console logging, file logging, remote server logging) that can be applied to different components.

---

### 6. **UI Design and Rendering**
   - When different rendering or layout strategies are needed based on the device or platform.
   - **Example:** A charting library that uses different strategies for rendering bar charts, line charts, and pie charts.

---

### 7. **Testing and Mocking**
   - When behavior needs to be swapped out during testing.
   - **Example:** Replacing a production-level strategy (e.g., real database connection) with a mock strategy during unit tests.

---

### 8. **Middleware and Request Processing**
   - When different processing or transformation strategies are required for requests.
   - **Example:** A middleware pipeline in a web server where authentication, logging, or compression strategies are applied dynamically.

---

### Example Code in Python:

```python
from abc import ABC, abstractmethod

# Strategy Interface
class PaymentStrategy(ABC):
    @abstractmethod
    def pay(self, amount):
        pass

# Concrete Strategies
class CreditCardPayment(PaymentStrategy):
    def pay(self, amount):
        print(f"Paid {amount} using Credit Card.")

class PayPalPayment(PaymentStrategy):
    def pay(self, amount):
        print(f"Paid {amount} using PayPal.")

# Context
class PaymentProcessor:
    def __init__(self, strategy: PaymentStrategy):
        self.strategy = strategy

    def set_strategy(self, strategy: PaymentStrategy):
        self.strategy = strategy

    def process_payment(self, amount):
        self.strategy.pay(amount)

# Usage
processor = PaymentProcessor(CreditCardPayment())
processor.process_payment(100)  # Paid 100 using Credit Card.

processor.set_strategy(PayPalPayment())
processor.process_payment(200)  # Paid 200 using PayPal.
```

This example shows how you can swap out strategies dynamically without modifying the `PaymentProcessor` class.


# Observer Design Pattern

The **Observer Design Pattern** is a behavioral design pattern used to establish a one-to-many dependency between objects, where one object (the subject) notifies a group of observer objects about any state changes. This pattern allows for loosely coupled systems by promoting separation of concerns between the subject and its observers.

---

### Key Components of the Observer Pattern

1. **Subject (Publisher)**  
   - Maintains a list of observers and provides methods to attach, detach, and notify them of state changes.
   - Example: A news agency or stock market system.

2. **Observers (Subscribers)**  
   - Objects that are interested in the subject's state and are notified when the subject's state changes.
   - Example: A news reader or stock investor.

3. **Concrete Subject**  
   - Implements the subject's interface and keeps track of its state. It notifies the observers when the state changes.

4. **Concrete Observer**  
   - Implements the observer's interface and updates its state based on the subject's state.

---

### Uses of the Observer Pattern

1. **Event-Driven Systems**
   - Notify multiple components when an event occurs (e.g., user input or system updates).
   - **Example:** A GUI button that notifies all registered listeners when clicked.

2. **Real-Time Data Feeds**
   - Notify clients when real-time data changes.
   - **Example:** Stock price updates, weather tracking systems, or sports scores.

3. **Publish-Subscribe Mechanisms**
   - A core pattern for messaging systems like Kafka, RabbitMQ, or event-driven architecture.
   - **Example:** A notification system where users subscribe to topics of interest.

4. **Model-View-Controller (MVC) Frameworks**
   - Notify views of changes in the model.
   - **Example:** A data model updates all views displaying the data in a software application.

5. **Distributed Systems**
   - Keep replicas or caches in sync by notifying them of changes.
   - **Example:** A distributed database system where nodes synchronize with updates.

6. **Game Development**
   - Notify various components (e.g., UI, physics engine) of game state changes.
   - **Example:** A game observer monitoring player score or health changes.

---

### Example Code in Python

Here’s how you can implement the Observer Pattern in Python:

```python
from abc import ABC, abstractmethod

# Subject
class Subject:
    def __init__(self):
        self._observers = []

    def attach(self, observer):
        self._observers.append(observer)

    def detach(self, observer):
        self._observers.remove(observer)

    def notify(self):
        for observer in self._observers:
            observer.update(self)

# Observer Interface
class Observer(ABC):
    @abstractmethod
    def update(self, subject):
        pass

# Concrete Subject
class WeatherStation(Subject):
    def __init__(self):
        super().__init__()
        self._temperature = None

    @property
    def temperature(self):
        return self._temperature

    @temperature.setter
    def temperature(self, value):
        self._temperature = value
        self.notify()

# Concrete Observers
class PhoneDisplay(Observer):
    def update(self, subject):
        print(f"PhoneDisplay: The current temperature is {subject.temperature}°C.")

class WindowDisplay(Observer):
    def update(self, subject):
        print(f"WindowDisplay: The current temperature is {subject.temperature}°C.")

# Usage
weather_station = WeatherStation()

phone_display = PhoneDisplay()
window_display = WindowDisplay()

weather_station.attach(phone_display)
weather_station.attach(window_display)

weather_station
```

# Decorator Design Pattern

<img width="1491" alt="Screenshot 2025-01-14 at 1 13 19 AM" src="https://github.com/user-attachments/assets/245daa9f-c138-4cb9-855b-63f3a4a664ae" />

<img width="1475" alt="Screenshot 2025-01-14 at 1 15 48 AM" src="https://github.com/user-attachments/assets/4250b6ee-4a36-4f88-ad4d-6368e5b84ada" />


Another example

The **Decorator Design Pattern** is a structural design pattern that allows you to dynamically add new behaviors or responsibilities to an object without modifying its structure. It achieves this by wrapping an object with another object (a decorator) that adds the new behavior.

---

### Key Concepts of the Decorator Pattern

1. **Component (Interface/Abstract Class)**  
   - Defines the common interface for objects that can have responsibilities added to them dynamically.
   
2. **Concrete Component**  
   - The base object to which additional responsibilities can be attached.

3. **Decorator (Abstract Class)**  
   - Maintains a reference to a Component object and defines the interface that is consistent with the Component.

4. **Concrete Decorators**  
   - Extends the functionality of the Component by adding new behavior.

---

### Uses of the Decorator Pattern

1. **Adding Responsibilities Dynamically**
   - When you want to extend the behavior of objects dynamically at runtime, rather than statically at compile time.
   - **Example:** Adding scrollbars, borders, or shadows to a GUI component like a text box.

2. **Open/Closed Principle**
   - When you want to keep the component class closed for modification but open for extension.
   - **Example:** Adding different types of encryption to a data transmission object without altering its original implementation.

3. **Replacing Inheritance**
   - When inheritance would result in a complex and rigid class hierarchy, the Decorator Pattern provides a more flexible alternative.
   - **Example:** Instead of creating multiple subclasses for variations of behavior, decorators can combine these variations.

4. **Logging and Monitoring**
   - Dynamically adding logging or monitoring capabilities to an existing object.
   - **Example:** Adding a logging decorator to a database operation.

5. **Modifying Behavior Based on Context**
   - When the behavior of an object needs to change depending on external factors.
   - **Example:** Adding seasonal discounts to a pricing object.

---

### Example Code in Python

Here’s an implementation of the **Decorator Pattern**:

```python
from abc import ABC, abstractmethod

# Component
class Coffee(ABC):
    @abstractmethod
    def cost(self) -> float:
        pass

    @abstractmethod
    def description(self) -> str:
        pass

# Concrete Component
class SimpleCoffee(Coffee):
    def cost(self) -> float:
        return 5.0

    def description(self) -> str:
        return "Simple Coffee"

# Decorator
class CoffeeDecorator(Coffee):
    def __init__(self, coffee: Coffee):
        self._coffee = coffee

    def cost(self) -> float:
        return self._coffee.cost()

    def description(self) -> str:
        return self._coffee.description()

# Concrete Decorators
class MilkDecorator(CoffeeDecorator):
    def cost(self) -> float:
        return self._coffee.cost() + 1.0

    def description(self) -> str:
        return self._coffee.description() + ", Milk"

class SugarDecorator(CoffeeDecorator):
    def cost(self) -> float:
        return self._coffee.cost() + 0.5

    def description(self) -> str:
        return self._coffee.description() + ", Sugar"

class WhippedCreamDecorator(CoffeeDecorator):
    def cost(self) -> float:
        return self._coffee.cost() + 1.5

    def description(self) -> str:
        return self._coffee.description() + ", Whipped Cream"

# Usage
coffee = SimpleCoffee()
print(f"{coffee.description()} - ${coffee.cost()}")

# Add Milk
coffee = MilkDecorator(coffee)
print(f"{coffee.description()} - ${coffee.cost()}")

# Add Sugar
coffee = SugarDecorator(coffee)
print(f"{coffee.description()} - ${coffee.cost()}")

# Add Whipped Cream
coffee = WhippedCreamDecorator(coffee)
print(f"{coffee.description()} - ${coffee.cost()}")
```

---

### Output

```
Simple Coffee - $5.0
Simple Coffee, Milk - $6.0
Simple Coffee, Milk, Sugar - $6.5
Simple Coffee, Milk, Sugar, Whipped Cream - $8.0
```

---

### Advantages of the Decorator Pattern

1. **Flexibility**: Behavior can be added to individual objects without affecting others.
2. **Avoids Subclass Explosion**: Reduces the need for multiple subclasses to achieve similar functionality.
3. **Open/Closed Principle**: New decorators can be added without modifying existing code.

---

### Disadvantages

1. **Complexity**: Can lead to a system with many small, similar objects, making it harder to understand.
2. **Debugging**: Wrapping objects in multiple decorators may make debugging more challenging.

The Decorator Pattern is ideal when you need to add functionality dynamically without altering the object's structure or creating an extensive inheritance hierarchy.

# Factory Design Pattern

The **Factory Design Pattern** is a **creational design pattern** that provides an interface or method for creating objects in a super class, but allows subclasses to alter the type of objects that will be created. It helps in decoupling the client code from the specific classes it needs to instantiate.

---

### Key Concepts of the Factory Pattern

1. **Factory Method**:  
   - A method responsible for creating objects of specific types. Subclasses override this method to create instances of different classes.

2. **Product**:  
   - The type of object that the factory method creates.

3. **Creator (Factory)**:  
   - The class that contains the factory method.

4. **Concrete Creator**:  
   - A subclass of the factory that overrides the factory method to instantiate specific types of products.

---

### Types of Factory Patterns

1. **Simple Factory**:  
   - A static method or class responsible for creating objects based on input parameters. This is not a true design pattern but a programming idiom.

2. **Factory Method**:  
   - A class delegates the instantiation of objects to subclasses by defining a common interface.

3. **Abstract Factory**:  
   - A super-factory that creates families of related or dependent objects without specifying their concrete classes.

---

### Uses of the Factory Pattern

1. **Decoupling Object Creation**  
   - When the client code should not know the details of how objects are created or which concrete classes are used.
   - **Example**: Database connection objects (e.g., MySQL, PostgreSQL, SQLite).

2. **When the Exact Type is Determined at Runtime**  
   - When you need to decide which class to instantiate based on runtime conditions.
   - **Example**: A shape factory that creates circles, rectangles, or triangles based on user input.

3. **When Managing Complex Object Creation**  
   - When object creation requires significant setup or configuration.
   - **Example**: Creating objects that depend on external resources like files or databases.

4. **Promoting Code Reusability and Maintainability**  
   - Centralizing object creation logic reduces duplication and makes changes easier.
   - **Example**: UI widgets for different platforms (e.g., Windows, macOS, Linux).

---

### Example Code in Python

Here’s an implementation of the **Factory Method Pattern**:

```python
from abc import ABC, abstractmethod

# Product Interface
class Shape(ABC):
    @abstractmethod
    def draw(self):
        pass

# Concrete Products
class Circle(Shape):
    def draw(self):
        return "Drawing a Circle"

class Rectangle(Shape):
    def draw(self):
        return "Drawing a Rectangle"

class Triangle(Shape):
    def draw(self):
        return "Drawing a Triangle"

# Creator (Factory)
class ShapeFactory(ABC):
    @abstractmethod
    def create_shape(self) -> Shape:
        pass

# Concrete Creators
class CircleFactory(ShapeFactory):
    def create_shape(self) -> Shape:
        return Circle()

class RectangleFactory(ShapeFactory):
    def create_shape(self) -> Shape:
        return Rectangle()

class TriangleFactory(ShapeFactory):
    def create_shape(self) -> Shape:
        return Triangle()

# Client Code
def draw_shape(factory: ShapeFactory):
    shape = factory.create_shape()
    print(shape.draw())

# Usage
circle_factory = CircleFactory()
rectangle_factory = RectangleFactory()
triangle_factory = TriangleFactory()

draw_shape(circle_factory)    # Output: Drawing a Circle
draw_shape(rectangle_factory) # Output: Drawing a Rectangle
draw_shape(triangle_factory)  # Output: Drawing a Triangle
```

---

### Advantages of the Factory Pattern

1. **Encapsulation**: Hides the object creation logic from the client.
2. **Loose Coupling**: Decouples client code from specific classes.
3. **Scalability**: Easy to add new product types by introducing new factories.
4. **Centralized Control**: Centralizes object creation, making maintenance easier.

---

### Disadvantages

1. **Complexity**: Adds extra layers and complexity, especially with Abstract Factory.
2. **Overhead**: For simple applications, it might be overkill.

---

### Example of a Simple Factory

```python
class ShapeSimpleFactory:
    @staticmethod
    def create_shape(shape_type: str) -> Shape:
        if shape_type == "circle":
            return Circle()
        elif shape_type == "rectangle":
            return Rectangle()
        elif shape_type == "triangle":
            return Triangle()
        else:
            raise ValueError("Unknown shape type")

# Usage
shape = ShapeSimpleFactory.create_shape("circle")
print(shape.draw())  # Output: Drawing a Circle
```

The **Factory Design Pattern** is particularly useful when creating objects is complex, dependent on conditions, or involves multiple steps, ensuring clean and maintainable code.




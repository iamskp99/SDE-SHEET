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

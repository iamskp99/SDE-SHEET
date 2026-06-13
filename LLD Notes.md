# SOLID Principles

1) Single Responsibility Principle

This principle tells us that every module should have only one responsibility. For example. if we make a single class that handles all the functionality then that will make our changes tedious. So, its better to seperate components.
And make the functionality such that every seperated componenet can be merged via code.

2) Open/Closed Principle

This principle tells us that , every module should be open for extenstion but their code should not be modified. Like, the designing should be done in such a way that it follows this principle.

3) Liskov's substitution Principle

It suggests that wherever Parents classes are being used as a type decleration , we can substitute them with their child class and nothing in the functionality should break.

4) Interface segregation Principle

It states that a class should not be forced to implement interfaces it does not use. Instead of creating a large, all-encompassing interface, break it into smaller, more specific ones to keep implementations lean and focused.

5) Dependency Inversion Principle

It tells that abstraction or interface classes should be encouraged in the design instead of components.

# Design Patterns

1) Strategy Design Pattern (Behavriol Pattern)

<img width="1116" height="489" alt="Screenshot 2026-01-05 at 12 25 50 AM" src="https://github.com/user-attachments/assets/f549f08c-cee8-4415-a39a-e2972cb19847" />

2) Observer Pattern (Behavriol Pattern)

<img width="1188" height="593" alt="Screenshot 2026-01-05 at 12 40 48 AM" src="https://github.com/user-attachments/assets/ee787f53-a7b3-4bea-9ae3-2c6eca02db73" />

3) Decorator Design Pattern (Structural Pattern)

```
from abc import ABC
# Decorator Design Pattern


class Pizza(ABC):
    def cost(self):
        pass

    def description(self):
        pass


class ToppingDecorator(Pizza):
    def __init__(self, pizza):
        self._pizza = pizza

    def cost(self):
        pass

    def description(self):
        pass


class Margerita(Pizza):
    def __init__(self,c,d):
        self.c = c
        self.d = d

    def cost(self):
        return self.c

    def description(self):
        return self.d


class OliveTopping(ToppingDecorator):
    def cost(self):
        return self._pizza.cost()+10

    def description(self):
        return self._pizza.description() + " With Olive"

pizza = Margerita(1,"Margerita")
print(pizza.cost(),pizza.description())
pizza = OliveTopping(pizza)
print(pizza.cost(),pizza.description())

# 1 Margerita
# 11 Margerita With Olive

```

4) Factory Method Pattern (Creational Design Pattern)

Used when we want to encapsulate object creation and related creation logic at one place.

There are two types:

a) Simple Factory Pattern

<img width="721" height="536" alt="Screenshot 2026-01-06 at 12 41 38 AM" src="https://github.com/user-attachments/assets/3f5eefca-5dd1-4db5-ae4f-d65f51c241a8" />
<img width="532" height="552" alt="Screenshot 2026-01-06 at 12 41 26 AM" src="https://github.com/user-attachments/assets/5f0af34d-15a5-4dfe-8e4c-06559ac9b2c7" />


As you can see, if we have to add new shape creation then we will have to change ShapeFactory therefore it violates Open/Closed Principle.
Also if the creation logic contains multiple steps then it will violate Single Responsibility Principle.


b) Factory Method Pattern

<img width="557" height="471" alt="Screenshot 2026-01-06 at 12 46 45 AM" src="https://github.com/user-attachments/assets/900166db-231e-433c-b52d-7f9f7bc498b6" />

<img width="702" height="520" alt="Screenshot 2026-01-06 at 12 48 42 AM" src="https://github.com/user-attachments/assets/559b760a-4bea-4180-a7be-11597054b3b3" />

<img width="786" height="520" alt="Screenshot 2026-01-06 at 1 06 36 AM" src="https://github.com/user-attachments/assets/aced60c9-1bf0-417e-b9dc-660053ea12a3" />

It solves SRP violation.


*Give an example where it is actually solving something*

A good Factory Method example is one where:

1. The client should not know the concrete class.
2. Object creation is non-trivial.
3. New types can be added without modifying existing code.

### Example: Payment Gateway Integration

Suppose your e-commerce system supports multiple payment providers:

* Stripe
* Razorpay
* PayPal

#### Without Factory

```python
if provider == "stripe":
    gateway = StripeGateway(api_key, timeout=5)
elif provider == "paypal":
    gateway = PayPalGateway(client_id, secret)
elif provider == "razorpay":
    gateway = RazorpayGateway(key, secret)

gateway.pay(amount)
```

Problems:

* Every place that creates a gateway needs this logic.
* Adding a new provider requires modifying existing code everywhere.
* Client is tightly coupled to concrete implementations.

---

### With Factory Method

```python
from abc import ABC, abstractmethod

class PaymentGateway(ABC):
    @abstractmethod
    def pay(self, amount):
        pass


class StripeGateway(PaymentGateway):
    def pay(self, amount):
        print(f"Stripe payment: {amount}")


class PayPalGateway(PaymentGateway):
    def pay(self, amount):
        print(f"PayPal payment: {amount}")
```

Factory:

```python
class PaymentCreator(ABC):
    @abstractmethod
    def create_gateway(self) -> PaymentGateway:
        pass

    def process_payment(self, amount):
        gateway = self.create_gateway()
        gateway.pay(amount)
```

Concrete creators:

```python
class StripeCreator(PaymentCreator):
    def create_gateway(self):
        return StripeGateway()


class PayPalCreator(PaymentCreator):
    def create_gateway(self):
        return PayPalGateway()
```

Usage:

```python
creator = StripeCreator()
creator.process_payment(1000)
```

---

### Why is the creator useful here?

Imagine tomorrow Stripe initialization becomes:

```python
StripeGateway(
    api_key,
    connection_pool,
    retry_policy,
    metrics_client
)
```

The client code remains:

```python
creator.process_payment(1000)
```

All creation complexity is hidden inside the creator.

---

### An even better real-world example: Cloud Storage

You are designing Google Drive-like storage.

```python
class Storage(ABC):
    @abstractmethod
    def upload(self, file):
        pass
```

Implementations:

```python
S3Storage
GCSStorage
AzureBlobStorage
```

Factory:

```python
class StorageCreator(ABC):
    @abstractmethod
    def create_storage(self):
        pass
```

Client:

```python
storage = creator.create_storage()
storage.upload(file)
```

Now deployment configuration decides:

```python
AWSStorageCreator
GCSStorageCreator
AzureStorageCreator
```

The upload workflow stays unchanged.

---

For an SDE-2 interview, the strongest justification is:

> Factory Method becomes valuable when object creation contains configuration, dependency wiring, validation, connection setup, caching, or provider-specific initialization. If creation is simply `return Circle(5)`, the factory adds little value and is mostly a teaching example. If creation involves constructing a Stripe client, Kafka producer, Redis client, S3 storage, etc., then the factory meaningfully separates creation from usage.


5) Chain of Responsibility Design Pattern

```





```






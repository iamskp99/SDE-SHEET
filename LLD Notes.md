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







# Strategy Pattern in Python

The Strategy Pattern is a behavioral design pattern that:

- Defines a family of algorithms/behaviors
- Encapsulates each algorithm/behavior
- Makes them interchangeable within that family

This pattern lets you select an algorithm's implementation at runtime without altering the code that uses the algorithm.

## Key Benefits

1. **Flexibility**: Behaviors can be switched at runtime
2. **Reusability**: Behaviors can be shared across different classes
3. **Maintainability**: New behaviors can be added without modifying existing code
4. **Testability**: Behaviors can be tested independently




## SimUDuck Example

We'll implement the Strategy Pattern using a duck simulation game called SimUDuck. The game features:

- Various duck species
- Different flying behaviors
- Different quacking behaviors

### Initial Problem: Using Inheritance



```python
from abc import ABC, abstractmethod


class Duck(ABC):

    def quack(self):
        print("Quack")
    
    def swim(self):
        print("Swim")
    
    @abstractmethod
    def display(self): ...
    

```


```python
class MallardDuck(Duck):
    def display(self):
        return "Mallard Duck"
    
class RedHeadDuck(Duck):
    def display(self):
        return "RedHead Duck"

class RubberDuck(Duck):

    def quack(self):
        return "Squeak"

    def display(self):
        return "Rubber Duck"

```

### Problem 1: Adding New Behavior

The client requests to add flying capability to the ducks. A natural first approach might be to add it to the base class:



```python
from abc import ABC, abstractmethod


class Duck(ABC):

    def quack(self):
        print("Quack")
    
    def swim(self):
        print("Swim")
    
    def fly(self):
        print("Fly")
    
    @abstractmethod
    def display(self): ...
    

```


```python
class MallardDuck(Duck):
    def display(self):
        return "Mallard Duck"
    
class RedHeadDuck(Duck):
    def display(self):
        return "RedHead Duck"

class RubberDuck(Duck):
    def display(self):
        return "Rubber Duck"

    def quack(self):
        return "Squeak"

```


```python
rubber_duck = RubberDuck()
rubber_duck.fly()
```

    Fly


### Problem 2: Inheritance Limitations

**Issues Discovered:**

1. Not all ducks can fly (e.g., rubber ducks)
2. Different ducks might fly differently
3. Inheritance forces behaviors on subclasses
4. Code reuse becomes difficult
5. Changes affect all subclasses


### Problem 3: Interface Approach

A common solution might be to use interfaces/abstract classes:

- Create `Flyable` and `Quackable` interfaces
- Let ducks implement only what they need

**New Issues:**

1. Code duplication across classes that share behavior
2. Behavior changes require updating multiple classes
3. Runtime behavior changes are difficult
4. Still not truly flexible



```python
from abc import ABC, abstractmethod


class Duck(ABC):

    def swim(self):
        print("Swim")
    
    @abstractmethod
    def display(self):
        pass


class Flyable(ABC):
    
    @abstractmethod
    def fly(self): ...


class Quackable(ABC):

    @abstractmethod
    def quack(self): ...

```

### Python doesn't have extends or inherit keyword both cases can be covered with simple sub-classing. Here `Quackable` and `Flyable` can be considered interfaces where we need implements and Duck can be extended



```python
class MallardDuck(Duck, Flyable, Quackable):

    def fly(self):
        print("MD Fly")
    
    def quack(self):
        print("MD quack")
    
    def display(self):
        print("Mallard Duck")


class RedHeadDuck(Duck, Flyable, Quackable):

    def fly(self):
        print("RedHead Fly")
    
    def quack(self):
        print("RedHead quack")
    
    def display(self):
        print("RedHead Duck")


class RubberDuck(Duck, Quackable):
    
    def display(self):
        print("Rubber Duck")
    
    def quack(self):
        print("Squeak")


class DecoyDuck(Duck):
    
    def display(self):
        print("Decoy Duck")
```

<div style="color: orange; font-size: 1.5rem;">
<ul>
<li>Ohh it looks good but wait, What if we had 48 duck categories which can fly and now we need to change the their fly behaviour now we need to update 48 classes
</li>
<li>
Duck can have different fly behavior
</li>
</ul>
</div>


## Strategy Pattern Solution

### Design Principles Applied:

1. **"Identify what varies and encapsulate it"**

   - Flying behavior varies → Create FlyBehavior
   - Quacking behavior varies → Create QuackBehavior

2. **"Program to an interface, not an implementation"**

   - Use behavior interfaces
   - Implement concrete behaviors
   - Compose objects with behaviors

3. **"Favor composition over inheritance"**
   - Ducks have behaviors (HAS-A)
   - Instead of ducks being behaviors (IS-A)



```python
from abc import abstractmethod, ABC

class FlyBehavior(ABC):
    
    @abstractmethod
    def fly(self): ...


class FlyWithWings(FlyBehavior):

    def fly(self):
        return "can fly."


class FlyWithNoWings(FlyBehavior):

    def fly(self):
        return "Fly without wings"


class NoWayFly(FlyBehavior):

    def fly(self):
        return "Cann't fly."


class QuackBehavior(ABC):

    @abstractmethod
    def quack(self): ...


class Quack(QuackBehavior):

    def quack(self):
        return "quacks"


class Squeak(QuackBehavior):

    def quack(self):
        return "squeak"


class MuteQuack(QuackBehavior):

    def quack(self):
        return "cann't quack"

```

### Benefits of This Design

1. **Behavior Reuse**
   - Behaviors are independent and reusable
   - Multiple duck types can share behaviors
2. **Easy Maintenance**
   - New behaviors can be added without changing existing code
   - Behaviors can be modified independently
3. **Runtime Flexibility**
   - Behaviors can be changed at runtime
   - Objects can switch strategies dynamically

### Implementation



```python
class Duck:

    def __init__(self, quack_behavior: QuackBehavior, fly_behavior: FlyBehavior):
        self._quack_behavior = quack_behavior
        self._fly_behavior = fly_behavior
    
    def quack(self):
        return self._quack_behavior.quack()

    def fly(self):
        return self._fly_behavior.fly()
    
    # if case you want to set behavior at run time
    # which is not very common in case of duck

    def set_quack(self, behavior: QuackBehavior):
        self._quack_behavior = behavior
    
    def set_fly(self, behavior: FlyBehavior):
        self._fly_behavior = behavior
    
    def details(self):
        return {
            "type": self.__class__.__name__,
            "quack": self.quack(),
            "fly": self.fly()
        }


class MallardDuck(Duck):

    def __init__(self):
        return super().__init__(Quack() ,FlyWithWings())


class RubberDuck(Duck):

    def __init__(self):
        return super().__init__(MuteQuack(), NoWayFly())


mallard = MallardDuck()
rubber_duck = RubberDuck()

print(mallard.details())
print(rubber_duck.details())

```

    {'type': 'MallardDuck', 'quack': 'quacks', 'fly': 'can fly.'}
    {'type': 'RubberDuck', 'quack': "cann't quack", 'fly': "Cann't fly."}


## Conclusion

The Strategy Pattern helps us achieve several important goals:

1. Encapsulation of varying behaviors
2. Runtime flexibility in behavior selection
3. Easy addition of new behaviors
4. Improved code maintenance and reusability

This pattern is especially useful when:

- You have a set of similar algorithms
- You need to vary algorithms at runtime
- You want to isolate algorithm logic from code that uses the algorithm


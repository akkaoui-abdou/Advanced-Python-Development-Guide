```markdown
# Advanced Python Development Guide

A comprehensive guide covering advanced Python concepts including OOP, design patterns, testing, performance optimization, database integration, web frameworks, concurrency, and security best practices.

## Table of Contents
1. [Advanced OOP Concepts](#advanced-oop-concepts)
2. [Design Patterns](#design-patterns)
3. [Testing & Debugging Strategies](#testing--debugging-strategies)
4. [Performance Optimization](#performance-optimization)
5. [Database Integration & ORM](#database-integration--orm)
6. [Web Development Frameworks](#web-development-frameworks)
7. [Concurrency & Threading](#concurrency--threading)
8. [Security Best Practices](#security-best-practices)

---

## Advanced OOP Concepts

### 1. Inheritance (Single, Multiple, Multilevel)
Allows classes to acquire properties and methods from other classes.

```python
class Animal:
    def eat(self):
        print("Eating...")

class Dog(Animal):
    def bark(self):
        print("Barking...")
```

### 2. Method Overriding
Child classes can rewrite parent class methods.

```python
class Animal:
    def sound(self):
        print("Generic animal sound")

class Dog(Animal):
    def sound(self):
        print("Woof!")
```

### 3. Encapsulation
Hide internal state using private attributes.

```python
class Bank:
    def __init__(self):
        self.__balance = 0  # Private attribute

    def deposit(self, amount):
        self.__balance += amount
```

### 4. Polymorphism
Objects implementing the same interface behave differently.

```python
class Cat:
    def sound(self):
        return "Meow"

class Dog:
    def sound(self):
        return "Woof"

for animal in (Cat(), Dog()):
    print(animal.sound())
```

### 5. Abstract Classes
Define methods that must be implemented by subclasses.

```python
from abc import ABC, abstractmethod

class Vehicle(ABC):
    @abstractmethod
    def move(self):
        pass

class Car(Vehicle):
    def move(self):
        print("Car is moving")
```

### 6. Composition & Aggregation
Build functionality by referencing other objects.

```python
# Composition
class Engine:
    def start(self):
        print("Engine starts")

class Car:
    def __init__(self):
        self.engine = Engine()  # Composition

# Aggregation
class Department:
    pass

class Employee:
    def __init__(self, department):
        self.department = department  # Aggregation
```

### 7. Mixins
Small classes that add extra functionality to other classes.

```python
class LogMixin:
    def log(self, msg):
        print(f"[LOG]: {msg}")

class Service(LogMixin):
    def run(self):
        self.log("Service running")
```

### 8. Properties
Clean attribute access management using getters/setters.

```python
class Person:
    def __init__(self, age):
        self._age = age

    @property
    def age(self):
        return self._age

    @age.setter
    def age(self, value):
        if value < 0:
            raise ValueError("Age cannot be negative")
        self._age = value
```

### 9. Magic Methods
Define how objects behave with operators.

```python
class Vector:
    def __init__(self, x, y):
        self.x, self.y = x, y

    def __add__(self, other):
        return Vector(self.x + other.x, self.y + other.y)
```

---

## Design Patterns

### Creational Patterns

#### Singleton
Ensure a class has only one instance.

```python
class Singleton:
    _instance = None
    
    def __new__(cls, *args, **kwargs):
        if not cls._instance:
            cls._instance = super().__new__(cls)
        return cls._instance
```

#### Factory Method
Delegate object creation to subclasses or functions.

```python
class Product: pass
class ConcreteProductA(Product): pass
class ConcreteProductB(Product): pass

def product_factory(kind: str) -> Product:
    if kind == "A": return ConcreteProductA()
    if kind == "B": return ConcreteProductB()
    raise ValueError(kind)
```

#### Builder
Construct complex objects step-by-step.

```python
class House:
    def __init__(self): self.parts = []
    def __repr__(self): return f"House({self.parts})"

class HouseBuilder:
    def __init__(self): self.house = House()
    def add_wall(self): self.house.parts.append("wall"); return self
    def add_roof(self): self.house.parts.append("roof"); return self
    def build(self): return self.house
```

### Structural Patterns

#### Adapter
Convert one interface to another.

```python
class OldAPI:
    def do_old(self): return "old"

class Adapter:
    def __init__(self, old): self.old = old
    def do_new(self): return self.old.do_old()
```

#### Facade
Provide a simplified interface to a complex subsystem.

```python
class SubA: def a(self): return "A"
class SubB: def b(self): return "B"

class Facade:
    def __init__(self): self.a, self.b = SubA(), SubB()
    def operation(self): return f"{self.a.a()} + {self.b.b()}"
```

#### Proxy
Control access to an object.

```python
class RealSubject:
    def request(self): return "real"

class Proxy:
    def __init__(self): self._real = RealSubject()
    def request(self):
        print("Proxy: logging")
        return self._real.request()
```

### Behavioral Patterns

#### Strategy
Define interchangeable algorithms.

```python
from typing import Callable

def sort_asc(data): return sorted(data)
def sort_desc(data): return sorted(data, reverse=True)

class Context:
    def __init__(self, strategy: Callable):
        self.strategy = strategy
    def do(self, data): return self.strategy(data)
```

#### Observer
Objects subscribe to events and get notified.

```python
class Subject:
    def __init__(self): self.observers = []
    def attach(self, o): self.observers.append(o)
    def notify(self, msg): [o.update(msg) for o in self.observers]

class Observer:
    def update(self, msg): print("Observer got:", msg)
```

#### State
Object behavior changes with its internal state.

```python
class State:
    def handle(self, ctx): raise NotImplementedError

class StateA(State):
    def handle(self, ctx):
        print("A -> to B"); ctx.state = StateB()

class StateB(State):
    def handle(self, ctx):
        print("B -> to A"); ctx.state = StateA()

class Context:
    def __init__(self, state: State): self.state = state
    def request(self): self.state.handle(self)
```

---

## Testing & Debugging Strategies

### Unit Testing
Test small, isolated pieces of code.

**Using unittest:**
```python
import unittest

class TestMath(unittest.TestCase):
    def test_add(self):
        self.assertEqual(1 + 1, 2)
```

**Using pytest (preferred):**
```python
def test_add():
    assert 1 + 1 == 2
```

### Integration Testing
Test multiple components working together.

```python
def test_api_client_integration():
    response = client.get("/users")
    assert response.status_code == 200
```

### Mocking & Patching
Avoid calling real external systems.

```python
from unittest.mock import patch

@patch("requests.get")
def test_api(mock_get):
    mock_get.return_value.json.return_value = {"name": "John"}
    assert fetch_user() == {"name": "John"}
```

### Debugging Tools
- **`pdb`**: Interactive debugger
- **`breakpoint()`**: Python 3.7+ debugger
- **Logging**: Professional debugging with `logging` module
- **Static Analysis**: `pylint`, `flake8`, `mypy`, `bandit`

---

## Performance Optimization

### Profiling
Always measure before optimizing.

```bash
# CPU profiling
python -m cProfile myscript.py

# Memory profiling
python -m memory_profiler script.py
```

### Code Optimization Techniques

1. **Use Built-in Functions**: Leverage C-optimized operations
2. **List Comprehensions**: Faster than traditional loops
3. **Generators**: Save memory with large datasets
4. **Caching**: Use `functools.lru_cache`
5. **Efficient Data Structures**: Choose the right tool for the job
6. **Local Variables**: Faster than global access

### Algorithmic Optimization
- Replace O(n²) algorithms with O(n log n) or O(n)
- Use sets/dicts for O(1) lookups
- Vectorize with NumPy for numerical computations

### Memory Optimization
- Use `__slots__` to reduce memory footprint
- Use iterators instead of loading entire datasets
- Explicitly delete large objects with `del`

### Parallelism
- **Multiprocessing** for CPU-bound tasks
- **AsyncIO** for I/O-bound tasks
- **ThreadPoolExecutor** for I/O concurrency

---

## Database Integration & ORM

### Connection Examples

**SQLite:**
```python
import sqlite3
conn = sqlite3.connect("mydb.sqlite")
```

**PostgreSQL:**
```python
import psycopg2
conn = psycopg2.connect(dbname="testdb", user="admin", 
                         password="secret", host="localhost", port=5432)
```

### SQLAlchemy ORM

**Model Definition:**
```python
from sqlalchemy import Column, Integer, String
from sqlalchemy.orm import declarative_base

Base = declarative_base()

class User(Base):
    __tablename__ = "users"
    id = Column(Integer, primary_key=True)
    name = Column(String)
    email = Column(String)
```

**CRUD Operations:**
```python
# Create
user = User(name="John", email="john@example.com")
session.add(user)
session.commit()

# Read
users = session.query(User).filter(User.name == "John").all()

# Update
user.name = "John Doe"
session.commit()

# Delete
session.delete(user)
session.commit()
```

### Async Database Access
```python
from sqlalchemy.ext.asyncio import create_async_engine, AsyncSession

engine = create_async_engine("sqlite+aiosqlite:///example.db")
AsyncSessionLocal = sessionmaker(engine, class_=AsyncSession)

async with AsyncSessionLocal() as session:
    result = await session.execute(select(User))
    users = result.scalars().all()
```

---

## Web Development Frameworks

### Django (Full-Stack)
Batteries-included framework for large applications.

```python
from django.http import HttpResponse

def home(request):
    return HttpResponse("Hello Django!")
```

### Flask (Microframework)
Lightweight and flexible for small apps and APIs.

```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def home():
    return "Hello Flask!"
```

### FastAPI (Modern Async API)
High-performance API framework with automatic docs.

```python
from fastapi import FastAPI
app = FastAPI()

@app.get("/users/{user_id}")
async def read_user(user_id: int):
    return {"user_id": user_id}
```

### Framework Comparison
| Framework | Best For | Performance | Ease of Use |
|-----------|----------|-------------|-------------|
| Django | Full-stack apps, enterprise | Medium | Easy |
| Flask | Small apps, APIs | Medium | Very Easy |
| FastAPI | APIs, async services | Very High | Medium |
| Sanic | Async APIs | Very High | Medium |

---

## Concurrency & Threading

### Threading (I/O-bound tasks)
```python
import threading

def task():
    print("Running in thread")

t = threading.Thread(target=task)
t.start()
t.join()
```

### Multiprocessing (CPU-bound tasks)
```python
from multiprocessing import Process

def compute(n):
    return n * n

p = Process(target=compute, args=(5,))
p.start()
p.join()
```

### AsyncIO (High-concurrency I/O)
```python
import asyncio

async def fetch_data(n):
    await asyncio.sleep(1)
    return n

async def main():
    results = await asyncio.gather(*(fetch_data(i) for i in range(5)))
    print(results)

asyncio.run(main())
```

### ThreadPoolExecutor & ProcessPoolExecutor
```python
from concurrent.futures import ThreadPoolExecutor, ProcessPoolExecutor

# ThreadPool for I/O
with ThreadPoolExecutor(max_workers=5) as executor:
    results = list(executor.map(fetch, range(5)))

# ProcessPool for CPU
with ProcessPoolExecutor(max_workers=4) as executor:
    results = list(executor.map(compute, range(5)))
```

---

## Security Best Practices

### 1. Input Validation
Never trust user input.

```python
import re
username = request.GET.get('username', '')
if not re.match(r'^[a-zA-Z0-9_]{3,20}$', username):
    raise ValueError("Invalid username")
```

### 2. SQL Injection Prevention
Use parameterized queries.

```python
# Unsafe
cursor.execute(f"SELECT * FROM users WHERE id={user_id}")

# Safe
cursor.execute("SELECT * FROM users WHERE id=%s", (user_id,))
```

### 3. Password Management
Use strong hashing algorithms.

```python
from passlib.hash import bcrypt
hashed = bcrypt.hash("mysecretpassword")
bcrypt.verify("mysecretpassword", hashed)
```

### 4. Environment Configuration
Never hardcode secrets.

```python
import os
DB_PASSWORD = os.getenv("DB_PASSWORD")
```

### 5. HTTPS & Secure Cookies
Always encrypt data in transit.

```python
# Flask example
app.config.update(
    SESSION_COOKIE_HTTPONLY=True,
    SESSION_COOKIE_SECURE=True,
    SESSION_COOKIE_SAMESITE='Lax'
)
```

### 6. CSRF Protection
Enable CSRF protection in web frameworks.

```python
# Django
{% csrf_token %}

# Flask with Flask-WTF
csrf = CSRFProtect(app)
```

### 7. Rate Limiting
Prevent brute-force attacks.

```python
from flask_limiter import Limiter
limiter = Limiter(app, key_func=get_remote_address)
```

### 8. Security Libraries
| Library | Purpose |
|---------|---------|
| `cryptography` | Encryption & signatures |
| `bcrypt` / `argon2-cffi` | Password hashing |
| `PyJWT` | JSON Web Tokens |
| `passlib` | Password management |
| `bandit` | Static security analysis |

### 9. Dependency Management
Regularly update and scan dependencies.

```bash
pip install safety
safety check
```

### 10. Secure Deployment
- Run applications with non-root users
- Use firewalls and network isolation
- Monitor logs for anomalies
- Containerize with minimal images
- Use secrets management systems

---

## Summary

This guide covers essential advanced Python topics for professional development. Key takeaways:

1. **Master OOP principles** for clean, maintainable code
2. **Apply design patterns** appropriately for common problems
3. **Implement comprehensive testing** strategies
4. **Optimize performance** through profiling and proper techniques
5. **Use ORMs effectively** for database operations
6. **Choose the right web framework** for your use case
7. **Handle concurrency** with the appropriate model
8. **Prioritize security** at all levels of development

Remember: Always profile before optimizing, test thoroughly, and prioritize security from the start of your project.
```

This README is now properly structured for GitHub with clear sections, code examples, and a professional format that's easy to navigate.

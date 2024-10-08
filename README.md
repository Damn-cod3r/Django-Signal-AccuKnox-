# Django-Signal-AccuKnox-
In Django, signals are a way to decouple applications and allow them to communicate with each other when certain events occur. They act as a notification system, enabling different parts of your application to respond to specific actions without being directly tied together.


```markdown
# Django Project Overview

Welcome to the Django Project! This repository contains the implementation of a Django application that demonstrates the use of Django signals and custom classes in Python. Below you will find detailed instructions on setting up the project, understanding its components, and contributing.

## Table of Contents

- [Features](#features)
- [Requirements](#requirements)
- [Installation](#installation)
- [Usage](#usage)
  - [Django Signals](#django-signals)
  - [Custom Classes in Python](#custom-classes-in-python)
- [Explanations and Questions](#explanations-and-questions)
- [Contributing](#contributing)
- [License](#license)

## Features

- Demonstrates the synchronous behavior of Django signals.
- Shows how signals run in the same thread as the caller.
- Illustrates the transaction management in Django signals.
- Implements a custom `Rectangle` class that supports iteration.

## Requirements

To run this project, ensure you have the following installed:

- Python 3.x
- Django 3.x or higher
- Virtualenv (recommended)

## Installation

Follow these steps to set up your development environment:

1. **Clone the repository**:
   ```bash
   git clone https://github.com/yourusername/your-repo.git
   cd your-repo
   ```

2. **Create a virtual environment**:
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows use `venv\Scripts\activate`
   ```

3. **Install dependencies**:
   ```bash
   pip install django
   ```

4. **Run migrations** (if applicable):
   ```bash
   python manage.py migrate
   ```

5. **Run the development server**:
   ```bash
   python manage.py runserver
   ```

Your Django application should now be up and running at `http://127.0.0.1:8000/`.

## Usage

### Django Signals

This project demonstrates how to use Django signals effectively. Below are examples illustrating key concepts.

#### 1. Django Signals Execution

By default, Django signals are executed synchronously. Here’s a sample implementation:

```python
import time
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.contrib.auth.models import User

@receiver(post_save, sender=User)
def slow_signal_handler(sender, instance, **kwargs):
    print("Signal started")
    time.sleep(5)  # Simulating a slow task
    print("Signal finished")

# Creating a new user, signal should block for 5 seconds
user = User.objects.create(username="test_user")
print("User created")
```

**Explanation**: When a user is created, the signal triggers and executes immediately, blocking the process for 5 seconds before proceeding to print "User created." This confirms that the signal handler is executed synchronously.

#### 2. Threading with Signals

Django signals run in the same thread as the caller:

```python
import threading
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.contrib.auth.models import User

@receiver(post_save, sender=User)
def thread_checking_signal_handler(sender, instance, **kwargs):
    print(f"Signal handler thread ID: {threading.get_ident()}")

# Caller code
def create_user():
    print(f"Caller thread ID: {threading.get_ident()}")
    user = User.objects.create(username="test_user")

create_user()
```

**Explanation**: When you run this code, the thread IDs printed inside and outside the signal handler will match, proving that the signal runs in the same thread as the caller.

#### 3. Transaction Management

Signals run in the same transaction as the caller:

```python
from django.db import transaction
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.contrib.auth.models import User

@receiver(post_save, sender=User)
def check_transaction_status(sender, instance, **kwargs):
    print(f"Inside transaction: {transaction.get_autocommit()}")

def create_user():
    print(f"Outside transaction before commit: {transaction.get_autocommit()}")
    user = User.objects.create(username="test_user")
    print(f"Outside transaction after commit: {transaction.get_autocommit()}")

create_user()
```

**Explanation**: This code shows the state of the transaction during and after user creation, indicating that the signal is executed in the same transaction as the caller.

### Custom Classes in Python

This project includes a custom `Rectangle` class that supports iteration:

```python
class Rectangle:
    def __init__(self, length: int, width: int):
        self.length = length
        self.width = width

    def __iter__(self):
        yield {'length': self.length}
        yield {'width': self.width}

# Example usage
rect = Rectangle(5, 10)

for dimension in rect:
    print(dimension)
```

**Explanation**: When you iterate over the `Rectangle` instance, the `__iter__` method is called, yielding the length and width as dictionaries in the desired format.

## Explanations and Questions

### Question 1: By default, are Django signals executed synchronously or asynchronously?

**Answer**: By default, Django signals are executed synchronously. This means the signal is triggered and executed immediately within the same process that triggers it.

### Question 2: Do Django signals run in the same thread as the caller?

**Answer**: Yes, Django signals run in the same thread as the caller. This can be proven by comparing the thread ID of the caller and the thread ID within the signal handler.

### Question 3: By default, do Django signals run in the same database transaction as the caller?

**Answer**: Yes, Django signals run in the same database transaction as the caller by default. However, this depends on when the signal is fired (e.g., `pre_save` is fired before the transaction is committed, while `post_save` is fired after).

## Contributing

We welcome contributions! Please follow these steps:

1. Fork the repository.
2. Create a new branch (`git checkout -b feature-branch`).
3. Make your changes and commit them (`git commit -m 'Add new feature'`).
4. Push to the branch (`git push origin feature-branch`).
5. Open a pull request.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

```

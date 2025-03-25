# Topic: Django Signals

## Question 1: By default are django signals executed synchronously or asynchronously? 

**Answer:**  
Django signals are executed **synchronously** by default, meaning they run in the same execution flow as the function that triggers them.  
To prove this, we can use the `time.sleep` function to introduce a delay in the signal and observe if the main function waits for it to complete.

### Example:

```python
import time
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.contrib.auth.models import User

def test_signal():
    print("Before saving user")
    user = User.objects.create(username="test_user")
    print("After saving user")

@receiver(post_save, sender=User)
def my_signal_handler(sender, instance, **kwargs):
    print("Signal started")
    time.sleep(3)  # Simulate a long-running task
    print("Signal completed")

test_signal()
```

**Expected Output:**
```none
Before saving user
Signal started
Signal completed
After saving user
```


This proves that Django signals are executed synchronously because the main thread waits for the signal handler to complete before proceeding.

## Question 2: Do Django signals run in the same thread as the caller?
**Answer:**  
 Yes, Django signals run in the same thread as the caller by default.

To verify this, we can print the thread IDs of both the main function and the signal handler.
### Example:
```python
import threading
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.contrib.auth.models import User

def test_signal():
    print(f"Main thread ID: {threading.get_ident()}")
    user = User.objects.create(username="test_user")

@receiver(post_save, sender=User)
def my_signal_handler(sender, instance, **kwargs):
    print(f"Signal thread ID: {threading.get_ident()}")

test_signal()
```

**Expected Output:**
```none
Main thread ID: 140736248518464
Signal thread ID: 140736248518464
```
Since both the main function and signal handler run in the same thread, this confirms that Django signals execute in the same thread as the caller.


## Question 3: By default, do Django signals run in the same database transaction as the caller?
**Answer:** 
Answer: Yes, Django signals run in the same database transaction as the caller if they are connected before the transaction is committed.

To prove this, we can check whether the signal executes before or after the transaction commits.
### Example:
```python
from django.db import transaction
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.contrib.auth.models import User

def test_signal():
    with transaction.atomic():
        user = User.objects.create(username="test_user")
        print("Inside transaction: User saved")
    print("Outside transaction: Committed")

@receiver(post_save, sender=User)
def my_signal_handler(sender, instance, **kwargs):
    print("Signal executed")

test_signal()

```
**Expected Output:**
```none
Inside transaction: User saved
Signal executed
Outside transaction: Committed
```
Since the signal executes before the transaction completes, it confirms that Django signals run in the same database transaction as the caller.

## Topic: Custom Classes in Python
Description: Create a Rectangle class with iteration capabilities.
```python
class Rectangle:
    def __init__(self, length: int, width: int):
        self.length = length
        self.width = width
    
    def __iter__(self):
        yield {"length": self.length}
        yield {"width": self.width}


# Example usage:
rect = Rectangle(10, 5)
for item in rect:
    print(item)
```
**Expected Output:**
```none

{'length': 10}
{'width': 5}
```

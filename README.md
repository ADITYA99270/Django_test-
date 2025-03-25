# Topic: Django Signals

## Question 1: By default, are Django signals executed synchronously or asynchronously?

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

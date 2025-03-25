# Topic: Django Signals

Question 1: By default, are Django signals executed synchronously or asynchronously?
Answer: Django signals are executed synchronously by default, meaning they run in the same execution flow as the function that triggers them.
        To prove this, we can use the time.sleep function to introduce a delay in the signal and observe if the main function waits for it to complete.
import time < br>
from django.db.models.signals import post_save < br>
from django.dispatch import receiver< br>
from django.contrib.auth.models import User< br>

def test_signal():< br>
    print("Before saving user")< br>
    user = User.objects.create(username="test_user")< br>
    print("After saving user")< br>

@receiver(post_save, sender=User)< br>
def my_signal_handler(sender, instance, **kwargs):< br>
    print("Signal started")< br>
    time.sleep(3)  # Simulate a long-running task< br>
    print("Signal completed")< br>

test_signal()< br>

Expected Output:< br>

Main thread ID: 140736248518464< br>
Signal thread ID: 140736248518464< br>

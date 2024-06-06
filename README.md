# TC2037-Evidence4
## Description
For this project, I selected the "FizzBuzz" problem, a popular exercise used in coding interviews that tests basic programming logic. The challenge was enhanced by incorporating multithreading to demonstrate concurrency handling, making it relevant for understanding complex, high-performance application systems where synchronized execution is crucial.

The primary challenge in multithreaded FizzBuzz is ensuring correct synchronization so the sequence prints accurately depending on each number's divisibility.

## Models:
### 1. Function-Specific Threads
Each thread in the FizzBuzz implementation is assigned a distinct role based on the divisibility rule it handles:
- `fizz()`: Handles numbers divisible by 3 (not by 5).
- `buzz()`: Manages numbers divisible by 5 (not by 3).
- `fizzbuzz()`: Processes numbers divisible by both 3 and 5.
- `number()`: Deals with numbers divisible by neither.
### 2. Synchronization Mechanisms
- Utilizing `wait()` to pause thread execution
- `notify_all()` to resume, avoiding race conditions.
### 3. Safe State Management
- The `current` number tracker is protected by a condition variable's lock, ensuring mutual exclusion.
- Threads modify the `current` counter in a controlled environment, preventing data corruption.

## Implementation:
The solution is implemented in Python using the threading module, chosen for its support and easy to manage threading. Python's threading allows for a clear demonstration of synchronization mechanisms like locks and condition variables, which are essential for correct concurrency management.

```python
#Import the threading module to handle multi-threaded execution
import threading

#Define the FizzBuzz class to manage the FizzBuzz problem logic
class FizzBuzz:
    #Initialize the class with a maximum number 'n'
    def __init__(self, n):
        self.n = n                  #The maximum number up to which to count
        self.current = 1            #Variable to keep the current count in the sequence
        self.condition = threading.Condition()  #Condition object to synchronize threads

    #Method to handle numbers that are divisible by 3 but not by 5
    def fizz(self, printFizz):
        while True:
            with self.condition:
                #Wait until it's this thread's turn to act
                while self.current <= self.n and (self.current % 3 != 0 or self.current % 5 == 0):
                    self.condition.wait()
                #End the thread if the maximum number is reached
                if self.current > self.n:
                    return
                printFizz()
                self.current += 1
                #Notify all threads that they can proceed
                self.condition.notify_all()

    #Method for handling numbers divisible by 5 but not by 3
    def buzz(self, printBuzz):
        while True:
            with self.condition:
                while self.current <= self.n and (self.current % 5 != 0 or self.current % 3 == 0):
                    self.condition.wait()
                if self.current > self.n:
                    return
                printBuzz()
                self.current += 1
                self.condition.notify_all()

    #Method for handling numbers divisible by both 3 and 5
    def fizzbuzz(self, printFizzBuzz):
        while True:
            with self.condition:
                while self.current <= self.n and (self.current % 15 != 0):
                    self.condition.wait()
                if self.current > self.n:
                    return
                printFizzBuzz()
                self.current += 1
                self.condition.notify_all()

    #Method for handling numbers not divisible by either 3 or 5
    def number(self, printNumber):
        while True:
            with self.condition:
                while self.current <= self.n and (self.current % 3 == 0 or self.current % 5 == 0):
                    self.condition.wait()
                if self.current > self.n:
                    return
                printNumber(self.current)
                self.current += 1
                self.condition.notify_all()

#Printing functions for each type of output
def printFizz():
    print("fizz", end=',')

def printBuzz():
    print("buzz", end=',')

def printFizzBuzz():
    print("fizzbuzz", end=',')

def printNumber(number):
    print(number, end=',')

#Main block to start and manage threads
if __name__ == "__main__":
    n = 30  #Set the maximum number of the sequence
    fb = FizzBuzz(n)  #Create an instance of FizzBuzz
    threads = [  #Create threads for each function
        threading.Thread(target=fb.fizz, args=(printFizz,)),
        threading.Thread(target=fb.buzz, args=(printBuzz,)),
        threading.Thread(target=fb.fizzbuzz, args=(printFizzBuzz,)),
        threading.Thread(target=fb.number, args=(printNumber,))
    ]

    for thread in threads:  #Start all threads
        thread.start()
    for thread in threads:  #Wait for all threads to finish
        thread.join()
```

## Test
By testing number 30 the test focused on each thread's responsability:
- Numbers divisible by 3 only output "Fizz."
- Numbers divisible by 5 only output "Buzz."
- Numbers divisible by both output "FizzBuzz."
= All other numbers non divisible are printed as is.

[Test Here](https://colab.research.google.com/drive/1FMS7YZkI4r5zjp2Uwb9_RcocgGB0qR9I?usp=sharing)

<img width="585" alt="image" src="https://github.com/M4urici02002/TC2037-Evidence4/assets/106397627/344488e9-0acf-429e-bfd9-5e71c4283f64">

## Analysis
Functional programming could simplify FizzBuzz by focusing on immutable data and pure functions. This style avoids changing data, making the program easier to understand and less prone to errors.

The time complexity for this multithreaded approach remains O(n), as each number from 1 to n is processed exactly once. However, the real-world performance could be influenced by the overhead associated with thread management and synchronization.


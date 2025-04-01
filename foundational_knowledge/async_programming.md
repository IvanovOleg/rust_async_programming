# Asynchronous programming
It is a programming approach that allows us to use the down time of one task to work on another task.

### Asynchronous functions states:
* pending
* done


## State: Pending

* Start laundry
    * Wait for loundry to finish
* Order food
    * Wait for food to arrive

## State: Done
* Laundry washed
* Food obtained

Async runtime periodically looks at all running functions and checks their state. If the state is pending, it will come back later and check again. If the state is Done, it will collect the return value from a function and move on.

## When to use async programming?
Interleaving tasks is great, but we don't always need it. F.e. a student shouldn't do his homework while watching a movie. Constant context switching between doing your home work and watching a movie is not free. It takes time to make that switch. Bigger problem is doing each activity requires your full attention. There is no built-in downtime in each of these tasks. In programming we call it **CPU-bound** tasks since CPU needs to constantly work on those task in order to make a progress.

### CPU-bound Tasks

* Programs filled with CPU-bound tasks
    * An async coded version will run slower than a non-async version
    * It has the added overhead of context switching between tasks

## I/O-bound Tasks
The CPU is waiting for input and output operations to complete.

### Dealing with Wait Time
* Non-async code
    * Does nothing
    * Wasted CPU cycles
* Async code
    * Works on another task

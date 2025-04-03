# Asynchronous Primitives
Establised parameters that are used to synchronize the running of async threads.

### Dealing with limited resources
* Semapthore
* Mutex

Both ensure that only one entity can interact with a resource at a time.

### Informing
The ability of one thread to inform other threads that a particular event has occured is an async primitive called **notify**. It sends a notification to all threads listening for the event.

### Aligning
**barrier** waits for all wanted threads to arrive before allowing any single thread to make progress.


Async primitives allow us to accomplish sophisticated goals.

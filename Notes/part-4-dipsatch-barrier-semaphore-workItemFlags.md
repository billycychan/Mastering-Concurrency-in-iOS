# Mastering Concurrency in iOS - Part 4 (Dispatch Barrier, Semaphore, Work Item Flags)

## DispatchWorkItemFlags
- A set of behaviours for a work item
- Its qos class, whether to create a barrier or spawn a new detached thread.
- assignCurrentContext
- detached
- enforceQoS
- inheritQoS
- noQoS
- barrier

`.barrier` : Causes the work item to act as a barrier block when submitted to a concurrent queue. In a concurrent queue, multiple tasks are executed simultaneously on different threads.

When the work item with the barrier flag starts executing, all the tasks in the queue are temporarily suspended and will be resumed once this work item is finished.

## Dispatch Semaphore

### Critical Section
- Critical Section is the part of a program which tries to access shared resources.
- When critical section is accessed by multiple threads at the same time, there are strong chances of data inconsistency
- Solution -> Exclusive Access -> Semaphores

## Semaphore
- A counter
- wait() -1
- signal() +1

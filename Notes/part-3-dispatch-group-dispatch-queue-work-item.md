# Mastering Concurrency in iOS - Part 3 (Dispatch Group, Dispatch Work Item)


## Dispatch Group
- Multiple tasks can be grouped togerther
- We can wait for the tasks to be finished, or can continue with some other task and can get notified when tasks in the gorup complets.

[Dispatch Group code](https://gist.github.com/pallavtrivedi03/02f9b3c4a2601e23b7947e6d54d907d3)


### Functions
- enter()
- leave()
- wait()
- notify()


## Dispatch Work Item
- Encapsulates a block of code
- can be dispatched on both DispatchQueue and DispatchGroup
- Provides the flexibility to cancel the task(unless execution has started)

### cancel
- If it is set "true" before execution task won't execute
- If work item is cancelled during execution, 'cancel' will  return true but exectuion won't abort
- 'wait()' and 'notify()' work the same way

[Dispatch Work Item code](https://gist.github.com/pallavtrivedi03/0ccac69a18f29ba178683ed374e266a5)
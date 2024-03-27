# Common Questions on Concurrency, GCD, Operation Queue | Swift (Mastering Concurrency in iOS - 6)

## Sychronous vs Asynchronous
- Sychronous blocks the current 
- Async does not block the current thread

## Serial Queue vs Concurrent Queue
- One task at a time
- Multiple task a time

## Serial vs Concurrent and COncurrent vs Asynchronous

- Serial vs Concurrent affects the destination queue to which dispatch is being done that how will the execution take place there serially or concurrently.

- Sychronous vs Asynchronous affects the curent thread from which the dispatch is being done

## Predit the output

## What is QoS, and where should you use which one?

- qos tells the system how it should utilize the resources. Which thread should be used access to various streams like IO, timer, questing, scheduling priorty.
- User Interactive -> UI, Animation
- User Initiated -> Immeidate Result
- Utility -> long running task
- Background -> not visible to user

## How can you make multiple API calls together and proceed only on the completion of all?
- Dispatch group, enter(), leave(), notify()
- Zip operator

## Fix the code

- Critical session
- Race condition
- Solved by DispatchBarrier / Sephamore

## Difference between GCD and Operation Queue
- These two are NOT comparable
- GCD is lower level of abstration
- Operation Queue has been built on top of that
- GCD is preffered when the task is not complex 
  - not interesed in the state of execution
  - fire and forget
  - less control of the tasks
- Operations Queues
  - We need more control, dependencies
  - More cmplex

## Can we cancel the task in GCD
- Dispatch Work Item
  - you could cancel the task
  - Deboucing

## How can you make an operation asynchronous?
- Subclassing the Operation and override the start and cancel method.

```swift
// from https://gist.github.com/sanghapark/8a463ea54985cae2568ef4b42ffca4c4
class AsyncOperation: Operation {
  enum State: String {
    case Ready, Executing, Finished
    fileprivate var keyPath: String {
      return "is" + rawValue
    }
  }
  
  var state = State.Ready {
    willSet {
      willChangeValue(forKey: newValue.keyPath)
      willChangeValue(forKey: state.keyPath)
    }
    didSet {
      didChangeValue(forKey: oldValue.keyPath)
      didChangeValue(forKey: state.keyPath)
    }
  }
  
}
extension AsyncOperation {
  override var isReady: Bool {
    return super.isReady && state == .Ready
  }
  
  override var isExecuting: Bool {
    return state == .Executing
  }
  
  override var isFinished: Bool {
    return state == .Finished
  }
  
  override var isAsynchronous: Bool {
    return true
  }
  
  override func start() {
    if isCancelled { state = .Finished; return }
    main()
    state = .Executing
  }
  
  override func cancel() {
    state = .Finished
  }
}

```

## How can you add dependencies between tasks?
- `.addDependency` method in Operation
- `Semaphores` / barrier

## How to make a class thread safe?
- Identify the critial sessions
  - barriers, semaphore, dispatchGroup
- actors

## Can you update the UI on background thread?
- UIKit is tied to main thread, main run loop, view draw cacle.
- Graphics rendering. Re-render on the display, lighting up pixels, one go. 
- Background thread -> flickering.
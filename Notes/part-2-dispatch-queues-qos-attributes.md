# Mastering Concurrency in iOS - Part 2 (Dispatch Queues, Quality of Service, Attributes)

## Dispatch Queue
1. **Main Queues**
- System Created
  - Serial
  - Uses Main Thread ONLY
  - UIKit is tied to Main Thread
2. **System provided global concurrent queuens**
  - System Created
  - Concurrent
  - Do not use Main Thread
  - Priorities are decided through QoS
3. **Custom Queue**

## Quality of Service
Tell systems how it should utilize the resources rquired for your to job to be executed

- User Interactive
  - Animations
  - Involved in updating UI?
- User Initiated
  - Immediate Results
    -  Scrolling dataview, rednering UI data shows
  - Data required for seamless user experience?
- Utility
  - Long running tasks (downloading sth)
  - Is user aware of the progeress?
- Background
  - Not visible to user
  - Is ser aware of the tasks



```swift
// Main Thread
DispatchQueue.main.async {
    print(Thread.isMainThread ? "Execution on Main Thread" : "Execution on some other thread")
}

// other thread
DispatchQueue.global().async {
    print(Thread.isMainThread ? "Execution on Main Thread" : "Execution on some other thread")
}

// other thread with qos
DispatchQueue.global(qos: .userInteractive).async {
    print(Thread.isMainThread ? "Execution on Main Thread" : "Execution on some other thread")
}


```

```swift
// Although we can not predict the exact output, the code in .userInteractive queue will get finished first

DispatchQueue.global(qos: .background).async {
    for i in 11 ... 21 {
        print(i)
    }
}

DispatchQueue.global(qos: .userInteractive).async {
    for i in 0 ... 10 {
        print(i)
    }
}

```

## Atributes
- `.conrurrent`
- `.initiallyInactive`

## Target Queue
- A queue that your custom queue will behind the scenes
- A dipsatch queue's priority is inherited from its target queue.
- If we do not sepcify a target queue, its default priority global queue by default.

``` swift
let a: DispatchQueue = DispatchQueue(label: "A")
let b: DispatchQueue = DispatchQueue(label: "B", attributes: [.concurrent, .initiallyInactive])

b.setTarget(queue: a)

b.activate()

a.async {
    for i in 0 ... 5 {
        print(i)
    }
}

a.async {
    for i in 6 ... 10 {
        print(i)
    }
}


b.async {
    for i in 11 ... 15 {
        print(i)
    }
}


b.async {
    for i in 16 ... 30 {
        print(i)
    }
}

// print 0 ... 30
```

## Auto Release Frequency
- .inherit
- .workItem
- .nenver
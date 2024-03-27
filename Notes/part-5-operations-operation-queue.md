# Mastering Concurrency in iOS - Part 5 (Operations and Operation Queue)

## Operation
- An abstract class that represents the code and data associated with a single task
  
- Operations
  - Block Operation
    - with block
  - NSInvocationOperation
    - with selector
  - Custom Operation

  ## States
  - isready
  - isExecuting
  - isCancelled
  - isFinished
  
```swift
class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        print("About to begin operation")
        testOperations()
        print("Operation executed")
        
        
    }
    
    func testOperations() {
        let operation: BlockOperation = BlockOperation {
            print("First test")
            sleep(3)
        }
        operation.start()
    }

    /**
About to begin operation
First test
Operation executed
    **/
}


```

### Running on main thread

```swift
class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        print("About to begin operation")
        testOperations()
        print("Operation executed")
        
        
    }
    
    func testOperations() {
        let operation: BlockOperation = BlockOperation()
        operation.addExecutionBlock {
            print("First block executed")
        }
        operation.addExecutionBlock {
            print("Second block executed")
        }
        operation.addExecutionBlock {
            print("Third block executed")
        }
        operation.start()
    }
}

/**
 About to begin operation
 First block executed
 Second block executed
 Third block executed
 Operation executed
 
 */
```

### Running on non-main thread
```swift
class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        print("About to begin operation")
        testOperations()
        print("Operation executed")
        
        
    }
    
    func testOperations() {
        let operation: BlockOperation = BlockOperation()
        operation.addExecutionBlock {
            print("First block executed")
        }
        operation.addExecutionBlock {
            print("Second block executed")
        }
        operation.addExecutionBlock {
            print("Third block executed")
        }
        DispatchQueue.global().async {
            operation.start()
            print("Did this run main thread - \(Thread.isMainThread)")
        }
    }
}

/**
 About to begin operation
 Operation executed
 First block executed
 Second block executed
 Third block executed
 Did this run main thread - false
 */

```

### CompletionBlock
```swift
operation.completionBlock {
    print("Execution Completed")
}
```

### We can use `.addDependency` to maintain the order of opreation in the OperationQueue
```swift
class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        print("About to begin operation")
        testOperations()
        print("Operation executed")
    }
    
    func testOperations() {
        let operationQueue: OperationQueue = OperationQueue()
        
        //1 = Serial
//        operationQueue.maxConcurrentOperationCount = 1
        
        let operation1: BlockOperation = BlockOperation()
        operation1.addExecutionBlock {
            for i in 0 ... 10 {
                print(i)
            }
        }
        operation1.completionBlock = {
            print("Operation 1 exectued")
        }
        
        let operation2: BlockOperation = BlockOperation()
        
        operation2.addExecutionBlock {
            for i in 11 ... 20 {
                print(i)
            }
        }
        operation2.completionBlock = {
            print("Operation 2 exectued")
        }
        
        operation2.addDependency(operation1)
        
        operationQueue.addOperation(operation1)
        operationQueue.addOperation(operation2)

    }
}

```
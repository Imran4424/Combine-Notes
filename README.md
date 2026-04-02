# What is Reactive Programming
Reactive programming is a programming paradigm that focuses on responding to and managing asynchronous data and events in a declarative and data-driven way.

### Key Concepts in Reactive Programming
- Observable
- Observer
- Operators

## Observable
- An entity that produces data events
- Example: User input, sensor data, API responses

## Observer
- An entity that listens to events emitted by observables
- Example: Application components, Views

## Operators
- Functions to transform and manipulate data
- Example: map, filter, merge

### Benefits of Reactive Programming
- Improves code readability
- Handling complex asynchronous scenarios
- Real-time and event-driven applications

### Use Cases
- Web and mobile applications
- Real-time dashboards
- IoT and sensor data processing

## Reactive vs Imperative

### Immutable vs Mutable
| Reactive Programming   | Imperative Programming |
| -------------------------------------------- | -------------------------------------------- |
| Emphasizes immutability | Often uses mutable variables |
| Data is treated as unchangeable; changes result in the creation of new data | Data can be modified in-place, leading to potential issues like race conditions and unintended changes |
| Reduces the risk of side effects and simplified concurrent access | --- |

### Control Flow
| Reactive Programming   | Imperative Programming |
| -------------------------------------------- | -------------------------------------------- |
| Declarative Approach (Data-driven approach) | Explicit step-by-step instructions |
| Focuses on "What" to do rather than "how" to do it | Focuses on "how" to achieve a specific task, often involving loops and conditionals |
| Expresses operations as transformations on data streams | --- |

### Synchronous vs Asynchronous
| Reactive Programming   | Imperative Programming |
| -------------------------------------------- | -------------------------------------------- |
| Well-suited for handling synchronous operations | Typically synchronous |
| Efficiently manages asynchronous events and data streams | Blocking operations can lead to performance bottlenecks in applications requiring high concurrency |
| Enables non-blocking, event-driven processing | --- |

# Overview of Combine Framework
Combine is an Apple-provided Swift-based framework for handling asynchronous and event-driven code in Swift

## Introduction to Combine
- Introduced by Apple at WWDC 2019
- The goal of Combine is to simplify and streamline Reactive Programming.

## Key concepts of Combine
- Publishers
- Subscribers
- Operators
- Subjects

### Publishers
Publisher is a source of data that emits items over time.

- Initiates data streams
- Emits data to Subscribers
- Examples: Sensors, API endpoints, user inputs.

### Subscribers
The subscriber receives and reacts to data emitted by Publishers.

- Listen to data streams
- Processes and responds to emitted items.
- Examples: UI components, data processors.

### Operators
Functions that transform, filter, or combine data streams.

- Modify the data emitted by Publishers
- Enable complex data manipulation
- examples: map, filter, merge, combineLatest, flatMap, and so on (Higher order functions)

### Subjects

Subjects are a special type of Combine component that can act as both publisher and subscriber.

- Acts as a bridge between Publishers and Subscribers.
- Can multicast data to multiple subscribers.
- If a Publisher is <Int, Never> and Subscriber is <String, never> then their Subject needs to be <Int, String>

**Use cases**
- Real-time data streaming
- User Interface updates
- Event-driven applications
- Reactive web services

### Benefits of Combine
- Improved code readability
- Enhanced error handling
- Better support for asynchronous operations
- Asynchronous and event-driven
- Easy data manipulation
- Scalability and responsiveness
- Built-in seamless integration with SwiftUI for a reactive User Interface

# Publishers

## Build in Publishers

### `Just` Publisher

`Just` publisher that emits an output to each subscriber just once, and then finishes.

```swift
struct Just<Output>
```

It sends exactly one value and immediately sends a finished completion signal. By default, it emits the value immediately upon subscription.

```swift
// here Output is a generic type
struct Just<Output>
```

Signature of Sequence Publisher

```swift
// Output is a generic type, one single value
// Never - Error-free type, meaning it can not fail or throw errors
Just<Output, Never>
```

#### Common uses

**Default/Fallback Values**
Often used within a `.catch` block to provide a default value if a primary publisher fails.

**Starting Pipeline**
Useful for initializing a chain of publishers when we already have a value and want to apply operators like `.map` or `.flatMap`.

**Mocking/Testing**
Helpful for creating placeholder data or "fake" publishers during development.

```swift
import Combine

let publisher = Just("I am a iOS Programmer")

// sink is a way to subscribe to the publisher
// sink is going to return us the subscription in the form of something called AnyCancellable
let cancellable = publisher.sink { value in
  print(value)
}

// cancel the subscription after the purpose is fulfilled
cancellable.cancel()

// cancellable is not always required, since it will be called automatically when we go out of scope.
// like if the above code is written in a function


// but it is nice to called manually when working on a project that deals with nested references
```

#### What is AnyCancellable?

`AnyCancellable` is a class that acts as a Type-erasing wrapper that conforms `Cancellable` Protocol.

The primary purpose of `AnyCancellable` is to provide a way to cancel or release the subscription when it’s no longer needed. This helps prevent memory leaks and ensures that resources are deallocated properly. 
We can store “AnyCancellable” instances in properties or collections, and when the objects holding the subscriptions are deallocated, or we manually cancel the subscription, the associated resources are released.

##### Cancellable

`Cancellable` is a protocol that represents an activity or action that can be canceled.

`Cancellables are an important part of working with Combine, similar to how disposables are an important part of working with RxSwift.

When we subscribe to a publisher, we often get an object conforming to the Cancellable protocol. This object can be used to cancel the subscription when we are no longer interested in receiving values from the publisher.

By conforming to the Cancellable protocol, an object must implement a single method cancel(). Calling cancel() on a Cancellable instance will stop the publisher from sending any more values to the subscriber and will clean up any resources associated with the subscription.

Point to note that `AnyCancellable` isn't a subscription. This is particularly useful for storing multiple cancellables in a single collection, such as an array or a set, without worrying about their specific types. An `AnyCancellable` instance automatically calls `cancel()` when deinitialized.

#### Subscription

`Subscription` is a protocol that inherits from the Cancellable protocol.

A `Subscription` represents a connection between a publisher and a subscriber. When a subscriber subscribes to a publisher, the publisher provides a `Subscription` object to the subscriber. This object allows the subscriber to control the flow of data by requesting more values or canceling the subscription altogether.

Since `Subscription` inherits from `Cancellable`, it also has the `cancel()` method. This means that a subscriber can cancel the subscription by calling `cancel()` on the Subscription object when it no longer needs to receive values from the publisher.

### Sequence Publisher

While Just publisher publishes a single value, Sequence publisher publishes a sequence of values.

To create a sequence publisher, we just need to create an array, then append it with `.publisher` modifier

```swift
import Combine
import SwiftUI

let numbersPublisher = [1, 2, 3, 4, 5, 6, 7].publisher

// We can also map the sequence publisher from one type of data to another type of data
let doublePublisher = numbersPublisher.map { Double($0 * 2) }

doublePublisher.sink { value in
  print(value)
}
```

Signature of Sequence Publisher

```swift
// Output - Generic array type (Sequence)
// Error - Never - Neve is a special type that represents a value that will never be produced or an event that will never occur
// (In this context, it will never throw an error)
SequencePublisher<[Any], Never>
```

### Empty Publisher

An empty publisher is a publisher that emits no values but can finish immediately or never finish. It never calls the `receiveValue` closure.

```swift
struct Empty<Output, Failure> where Failure : Error
```

By default, it sends a finished completion event immediately, but we can configure it to never complete (acting as a `Never` publisher). It can be typed to represent any Output and Failure type, making it flexible.

#### Common uses

**Placeholder**
In `flatMap` or `switch` statements, it provides a publisher when no data is available.

**Error Resolution**
To resolve an optional value scenario by providing an empty stream instead of an error or a value.

**Default Value**
When we need a publisher to return something, but nothing is actually needed.

```swift
import Combine

// Publisher that will never emit values or errors
let neverPublisher = Empty<String, Never>()

// Publisher that finishes immediately with no values
let emptyPublisher = Empty<String, Never>(completeImmediately: true)

// Example with a sink
let cancellable = emptyPublisher.sink(
    receiveCompletion: { completion in
        print("Completion: \(completion)") // Prints: Completion: .finished
    },
    receiveValue: { value in
        print("Value: \(value)") // Never prints
    }
)

// Using with `replaceEmpty(with:)` to provide a default
let numbers: [Int] = []
let defaultPublisher = numbers.publisher.replaceEmpty(with: 0)

defaultPublisher.sink(
    receiveCompletion: { print("Completion: \($0)") },
    receiveValue: { print("Value: \($0)") } // Prints: Value: 0
)
```
### Fail Publisher

`Fail` is a built-in publisher that immediately terminates with a specific error. This is useful when we need to return an error condition early in a function that expects a publisher return type.

```swift
// Fail has both Output and Error types
// Although the output type will never actually produce
Fail<Output, Error>
```

```swift
struct Fail<Output, Failure> where Failure : Error
```

Fail publisher does not emit any data values. Instead, it sends a failure completion to the subscriber instantly.

Because it sends an error, it is a "terminal" publisher. Once the failure is sent, the subscription ends, and no further values can be emitted.

```swift
import Combine
import Foundation

// Define a custom error type for demonstration
enum CustomError: Error, CustomStringConvertible {
    case failed
    var description: String {
        return "A custom error occurred"
    }
}

func potentiallyFailingPublisher(shouldFail: Bool) -> AnyPublisher<Int, CustomError> {
    if shouldFail {
        // Return a Fail publisher with the specified error
        return Fail(error: CustomError.failed)
            .eraseToAnyPublisher()
    } else {
        // Return a Just publisher (which cannot fail) and set its failure type
        return Just(100)
            .setFailureType(to: CustomError.self)
            .eraseToAnyPublisher()
    }
}

```

### Timer Publisher

Timer publisher is a combine publisher which emits the current `Date` (which is time) at regular intervals. Unlike standard publishers, it is connectable, meaning it won't start ticking until we explicitly tell it to.

```swift
// every: The time interval (in seconds) between events. Here, we are using 1.0 seconds
// on: The RunLoop where time actually attaches (RunLoop.main, RunLoop.current)
// in: The RunLoop.Mode (.default, .common, .tracking)
Timer.publish(every: 1.0, on: .main, in: .default).autoconnect()
// .autoconnect() to start the timer immediately when the first subscriber attaches.
// there is another trigger .connect() to trigger the timer publisher at a specific time
```

#### The Main RunLoop (RunLoop.main)
This is the default choice for UI-related tasks.
- **Best for:** Updating labels, progress bars, or any UI element in SwiftUI/UIKit.
- **Behavior:** It ensures that the `sink` or `receiveValue` block executes on the main thread, making it thread-safe for UI updates.
- **Caveat:** If the main thread is blocked by a heavy calculation, the timer may fire late.

#### The Current RunLoop (RunLoop.current)
This refers to the RunLoop of the thread that is currently executing the code.

- **Best for:** Short-lived background tasks where we are already managed on a specific thread.
- **Behavior:** If we call this on the main thread, it is identical to `.main`. If called on a background thread, the timer will live there.
- **Warning:** Most background threads (like those from Global Dispatch Queues) do not have a running RunLoop by default. Unless we are managing a custom `Thread` object, `RunLoop.current` on a background thread often won't fire.

#### Comparison of RunLoop Modes

Regardless of which RunLoop we choose, the **Mode** (`in:`) is often more important than the RunLoop itself:

| Mode | Description | Use Case |
| -------------- | -------------- | -------------- |
| `.default` | The standard mode for most operations. | General background tasks. |
| `.common` | A configurable group of modes. | **Recommended**. Keeps the timer firing even while the user is scrolling a List or ScrollView. |
| `.tracking` | Used specifically during UI tracking. | Use if we only want the timer to run while the user is touching the screen. |

### Deferred

In Swift's Combine framework, a Deferred publisher is a wrapper that waits for a subscriber before it creates the actual publisher we want to use.

According to Apple docs, `Deferred` publisher that awaits subscription before running the supplied closure to create a publisher for the new subscriber.

```swift
struct Deferred<DeferredPublisher> where DeferredPublisher : Publisher
```

The `Deferred` Publisher takes a closure as a parameter, which must return a `DeferredPublisher`. It will call this closure only when a subscriber subscribes. 
In this way, it defers any publisher processing until a subscriber causes downstream demand for an event.

```swift
let publisher = Deferred {
    Just("Deferred Value")
}
```

#### Key Characteristics
- **Lazy Initialization:** It doesn't run its setup closure or create the underlying publisher until a subscriber attaches.
- **Fresh Instance per Subscriber:** Every time a new subscriber attaches, the `Deferred` closure runs again, providing that subscriber with a unique instance of the underlying publisher.
- **One-Shot to Multi-Shot:** It is frequently used to make "one-shot" publishers (like `Future`) behave more like standard publishers that can be retried or restarted.

#### When to Use It
- **Expensive Operations:** To avoid performing heavy setup or network requests until they are actually required.
- **Retrying:** If we use the `.retry()` operator on a `Future`, it won't work because the `Future` already has its result. Wrapping it in `Deferred` allows `.retry()` to trigger the creation of a new `Future` on failure.
- **Dynamic Generation:** When the publisher we want to return depends on a state that might change between the time we define the pipeline and the time it is actually subscribed to.

In simple words, `Deferred` Publisher is actually a publisher wrapper that wraps a publisher inside a closure.

### Future Publisher

In Combine, `Future` is the publisher we use for a one-shot async result. It publishes either one value and finishes or an error. We create it with a closure that receives a `promise`, and we complete that promise with a `Result` like `.success(...)` or `.failure(...)`.
It is effectively a wrapper for a task that will finish in the future, such as a network request or a long-running calculation.

```swift
final class Future<Output, Failure> where Failure : Error
```

```swift
import Combine

enum MyError: Error {
    case failed
}

func loadName() -> Future<String, MyError> {
    Future { promise in
        DispatchQueue.global().asyncAfter(deadline: .now() + 1) {
            let ok = true

            if ok {
                promise(.success("Ava"))
            } else {
                promise(.failure(.failed))
            }
        }
    }
}

// Now calling it like any other publisher

let cancellable = loadName()
    .sink(
        receiveCompletion: { print($0) },
        receiveValue: { print($0) }
    )
```

#### Key Characteristics
- **One-Shot:** A `Future` publisher emits exactly one value (or one error) and then immediately finishes. It does not emit a stream of values over time.
- **Eager Execution:** The work typically starts immediately upon creation, not when we subscribe. This is a key difference from many other publishers that are "lazy".
- **Replays Result:** Because the work happens immediately, the result is stored. If multiple subscribers attach to the same `Future` later, they all receive the same cached result (or error) without re-triggering the work.
- **Promise-Based:** We initialize it with a closure that takes a `Promise`. A `Promise` is just a function we call (`promise(.success(value))` or `promise(.failure(error))`) when our async work is done.

#### Common Use Cases
- **Wrapping Legacy Async Code:** It is the primary way to bridge callback-based APIs (like older networking or file I/O) into Combine pipelines.
- **Single Network Requests:** Perfect for HTTP calls where we expect a single response (data) or an error.
- **Expensive Calculations:** Use it to perform a heavy task on a background queue and publish the result once finished.

#### Future Publisher with Deferred

Using `Deferred` with a `Future` is a very common and important pattern in Swift's Combine framework. It solves the fundamental problem of **eager vs. lazy execution**.

The problem is `Future` is Eager. By design, a `Future` executes its closure **immediately** upon initialization, regardless of whether anyone is actually subscribed to it yet.

If a `Future` publisher is to wrap an expensive operation — like a network request or a heavy database query—it will fire off the moment we create the object. Furthermore, a `Future` caches its result. If a second subscriber attaches later, it just gets the cached result; the work is not run again.

The solution is to wrap `Future` with `Deferred` to make it Lazy.
`Deferred` is a publisher that waits for a subscriber before it creates the underlying publisher.
By wrapping a `Future` publisher inside a `Deferred`, it guarantees two things:

 1. The work inside the `Future` will not start until someone calls `.sink` or `.assign` on it.
 2. Every new subscriber gets a brand new `Future`, meaning the work (like an API call) is re-executed for each subscriber, rather than returning a cached result.

Here's the code example:

```swift
import Combine
import Foundation

var cancellables = Set<AnyCancellable>()

// --------------------------------------------------------
// 1. EAGER FUTURE (The Problem)
// --------------------------------------------------------
let eagerFuture = Future<String, Never> { promise in
    // THIS PRINTS IMMEDIATELY, even without a subscriber!
    print("🚀 Eager Future: Starting network request...")
    promise(.success("Eager Data"))
}

// --------------------------------------------------------
// 2. DEFERRED FUTURE (The Solution)
// --------------------------------------------------------
let lazyFuture = Deferred {
    Future<String, Never> { promise in
        // THIS WAITS. It only prints when someone subscribes.
        print("⏳ Deferred Future: Starting network request...")
        promise(.success("Lazy Data"))
    }
}

print("--- Publishers created. No subscriptions yet. ---")

// --------------------------------------------------------
// 3. SUBSCRIBING
// --------------------------------------------------------

// Now we subscribe to the Deferred Future. 
// THIS is what actually triggers the print statement and the work.
lazyFuture
    .sink { completion in
        print("Deferred Future Finished")
    } receiveValue: { value in
        print("Received: \(value)")
    }
    .store(in: &cancellables)
```

#### When to use this pattern
- **Network Requests:** Wrapping a legacy callback-based API request in Combine. We don't want the network call to fire when we create the view model; we want it to fire when the view actually subscribes to it.
- **Location/Permissions:** Requesting user permissions where we want the prompt to show up only when a specific flow is triggered.
- **Retry Logic:** If a `Future` fails, the `retry()` operator won't work properly because the `Future` just returns its cached failure. Wrapping it in a `Deferred` allows `retry()` to actually spin up a fresh `Future` and try the operation again.

### Record

### PassthroughSubject
### CurrentValueSubject

### Operators as Publishers
#

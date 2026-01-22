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
- An entity that lisens to events emitted by observables
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
| Reactive Programming                                                        | Imperative Programming                                                                                 |
| --------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| Emphasizes immutability                                                     | Often uses mutable variables                                                                           |
| Data is treated as unchangeable; changes result in the creation of new data | Data can be modified in-place, leading to potential issues like race conditions and unintended changes |
| Reduces the risk of side effects and simplified concurrent access           | ---                                                                                                    |

### Control Flow
| Reactive Programming                                    | Imperative Programming                                                              |
| ------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| Declarative Approach (Data-driven approach)             | Explicit step-by-step instructions                                                  |
| Focuses on "What" to do rather than "how" to do it      | Focuses on "how" to achieve a specific task, often involving loops and conditionals |
| Expresses operations as transformations on data streams | ---                                                                                 |

### Synchronous vs Asynchronous
| Reactive Programming                                     | Imperative Programming                                                                             |
| -------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| Well-suited for handling synchronous operations          | Typically synchronous                                                                              |
| Efficiently manages asynchronous events and data streams | Blocking operations can lead to performance bottlenecks in applications requiring high concurrency |
| Enables non-blocking, event-driven processing            | ---                                                                                                |

# Overview of Combine Framework
Combine is an apple provided swift based framework for handling asynchronous and event-driven code in Swift

## Introduction to Combine
- Introduced by Apple in WWDC 2019
- Goal of combine is to simplify and streamline Reactive Programming.

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
Subscriber receives and reacts to data emitted by Publishers.

- Listen to data streams
- Processes and responds to emitted items.
- Examples: UI components, data processors.

### Operators
Functions that transform, filter or combine data streams.

- Modify the data emitted by Publishers
- Enable complex data manipulation
- examples: map, filer, merge, combineLatest, flatMap and so on (Higher order functions)

### Subjects

Subjects is a special type of Combine component which can act as both publisher and subscriber.

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
- Build-in seamless integration with SwiftUI for reactive User Interface

# Publishers

### `Just` Publisher

`Just` publisher that emits an output to each subscriber just once, and then finishes.

It sends exactly one value and immediately sends a finished completion signal. By default, it emits the value immediately upon subscription.

```swift
// here Output is generic type
struct Just<Output>
```

Signature of Sequence Publisher

```swift
// Output is generic type one singe value
// Never - Error free type, meaning it can not fail or throw errors
Just<Output, Never>
```

#### Common uses

**Default/Fallback Values**
Often used within a `.catch` block to provide a default value if a primary publisher fails.

**Starting Pipeline**
Useful for initializing a chain of publishers when you already have a value and want to apply operators like `.map` or `.flatMap`.

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


// but it is nice to called manually when working on a project which deal with nested references
```

#### What is AnyCancellable?

`AnyCancellable` is a class which acts as a Type-erasing wrapper that conforms `Cancellable` Protocol.

The primary purpose of `AnyCancellable` is to provide a way to cancel or release the subscription when it’s no longer needed. This helps prevent memory leaks and ensures that resources are deallocated properly. 
We can store “AnyCancellable” instances in properties or collections, and when the objects holding the subscriptions are deallocated or we manually cancel the subscription, the associated resources are released.

##### Cancellable

`Cancellable` is a protocol that represents an activity or action that can be canceled.

`Cancellables are an important part of working with Combine, similar to how disposables are an important part of working with RxSwift.

When we subscribe to a publisher, we often get an object conforming to the Cancellable protocol. This object can be used to cancel the subscription when we are no longer interested in receiving values from the publisher.

By conforming to the Cancellable protocol, an object must implement a single method cancel(). Calling cancel() on a Cancellable instance will stop the publisher from sending any more values to the subscriber and will clean up any resources associated with the subscription.

Point to note that `AnyCancellable` isn't a subscription. This is particularly useful for storing multiple cancellables in a single collection, such as an array or a set, without worrying about their specific types. An `AnyCancellable` instance automatically calls `cancel()` when deinitialized.

#### Subscripton

`Subscription` is a protocol that inherits from the Cancellable protocol.

A `Subscription` represents a connection between a publisher and a subscriber. When a subscriber subscribes to a publisher, the publisher provides a `Subscription` object to the subscriber. This object allows the subscriber to control the flow of data by requesting more values or canceling the subscription altogether.

Since `Subscription` inherits from `Cancellable`, it also has the `cancel()` method. This means that a subscriber can cancel the subscription by calling `cancel()` on the Subscription object when it no longer needs to receive values from the publisher.

### Sequence Publisher

While Just publisher publishes one single value, Sequence publisher publishes sequence of values.

To create a sequence publisher we just need to create an array then append it with `.publisher` modifier

```swift
import Combine
import SwiftUI

let numbersPublisher = [1, 2, 3, 4, 5, 6, 7].publisher

// we can also map the sequence publisher from one type of data to another type of data
let doublePublisher = numbersPublisher.map { Double($0 * 2) }

doublePublisher.sink { value in
  print(value)
}
```

Signature of Sequence Publisher

```swift
// Output - Generic array type (Sequence)
// Error - Never - Neve is a special type that represents a value that will never be produced or an event that will never occur
// (In this context it will never thrown a error)
SequencePublisher<[Any], Never>
```

### Empty Publisher

An empty publisher is a publisher that emits no values but can finish immediately or never finish. It never calls the `receiveValue` closure.

By default, it sends a finished completion event immediately, but you can configure it to never complete (acting as a `Never` publisher). It can be typed to represent any Output and Failure type, making it flexible.

#### Common uses

**Placeholder**
In `flatMap` or `switch` statements, it provides a publisher when no data is available.

**Error Resolution**
To resolve an optional value scenario by providing an empty stream instead of an error or a value.

**Default Value**
When you need a publisher to return something, but nothing is actually needed.

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

### Timer Publisher
### Future Publisher
### PassthroughSubject
### CurrentValueSubject
### Deferred
### Operators as Publishers

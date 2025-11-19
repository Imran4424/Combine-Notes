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

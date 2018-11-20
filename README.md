<p align="center"><img src="https://raw.githubusercontent.com/slikts/concurrency-glossary/master/logo.svg?sanitize=true" width="400" height="136" alt="Concurrency Glossary"></p>

This is an informal top-level overview of terms used in concurrent computing. The motivations are to provide a unified resource on a topic of wide relevance, to disambiguate overloaded terms and to aid natural understanding by contextualizing certain terms more broadly than their conventional meanings.

🚧 Work in progress

# Layers of abstraction

Abstraction means forming simplified conceptual models of problems that enable general or reusable solutions. For example, functions are an abstraction with very general applicability to solving the problem of structuring and reasoning about code. Abstractions with high generality are called rich abstractions.

Problems in complex domains like computing are solved using many stacked layers of abstraction. Program execution can broadly be decomposed into a *computational model* that is implemented in a *machine model* using an *execution model*, where the machine model is at the bottom of the stack. The layered architecture is relevant to understanding that contradictory or seemingly exclusive concepts can co-exist at different layers of abstraction, like concurrent execution on a serial machine. When the contradictions between an abstraction and the underlying model are exposed and the abstracted-away details can't be ignored, it's called a leak in the abstraction.

Dealing with leaking abstractions is a key aspect of concurrency modeling and programming in general, so this glossary emphasizes the possible contradictions or branching paths in the design space of concurrency models by grouping the abstractions as dichotomous pairs of terms. The topics are organized in an ascending order of abstraction level instead of alphabetically.

# Concurrent (order-independent) vs sequential

Concurrency is about *independent* computations that can be executed in an arbitrary order with the same outcome. The opposite of concurrent is sequential, meaning that sequential computations depend on being executed step-by-step to produce correct results.

Concurrency can be *limited* or constrained by depending on communication or interaction between computations, and minimizing dependencies while preserving correctness is the central problem of concurrency modeling. The requirements for communication are derived either from modeling real-world dependencies between concurrent processes, or from the need for efficiency and reusing limited resources.

Concurrent programming means factoring a program into independent modules or units of concurrency. Depending on the context, there are different terms for units of concurrency, like tasks, coroutines, processes, threads or actors.

The wide relevance of concurrency is based on the need to map independent processes in the real world to computational models, and on the hardware trends towards increased parallelism and the rise of distributed computing and networking.

## Multi- prefix

There's a set of concurrency-related terms that start with a contraction of the word "multiple": multi-tasking, multi-programming, multi-processing, multi-threading and multi-core. Multi-threading generally means using system threads and multi-core refers to hardware concurrency, but the other terms are ambiguous and can either just mean concurrency in general or something more specific like using system processes or avoiding busy-waiting, so the ambiguous terminology should preferably be avoided.

# Parallel vs serial

Parallelism refers to executing multiple computations at the same time, while serial execution is one-at-a-time. Parallelization and serialization refer to composing computations either in parallel or serially.

## Overlapping vs interleaved lifetimes (true parallelism vs pseudo-parallelism)

Parallel computations have overlapping lifetimes, and true parallelism means that the overlap is physically instant or simultaneous, while pseudo-parallelism means that the overlap is just conceptual. True parallelism requires hardware support (like a multi-core processor), while pseudo-parallelism means that the parallel computations are an abstraction over serially interleaved sub-computations. True parallelism is essentially an implementation detail, while pseudo-parallelism can be part of the computational model.

The colloquial meanings of "concurrent" and "parallel" are largely synonymous, which is a source of significant confusion that extends even to computer science literature, where concurrency may be misleadingly described in terms that imply or explicitly refer to overlapping lifetimes. The crux of the relation between concurrency and parallelism is just that order-independent (concurrent) computations can be parallelized without changing the correctness of the outcome, but concurrency alone doesn't imply parallelism, or vice versa. For example, computations can be parallel without being independent, as in the case of SIMD, which executes parallel computations in lockstep.

## Compute-bound vs i/o-bound

A computation being compute-bound or i/o-bound refers to its boundaries in time being more defined either by computing (for example, numerical calculations) or communicating (mainly just waiting to receive inputs or to send outputs). In broad terms, true parallelism is more relevant to increasing throughput in compute-bound use cases, while pseudo-parallelism can be adequate for increasing throughput and reducing latency in i/o-bound use cases.

# Scheduling (flow control, temporal composition)

Scheduling means assigning processor time to different logically executable computations or, less formally, "making this happen after that". Even though the term "scheduling" is commonly used in a limited context like referring to system components called runtime schedulers, scheduling in a general sense as composing computations in time is an intrinsic part of computing.

Since scheduling assigns a limited resource (processor time), the logic it follows is based on balancing conflicting goals or priorities. The typical goals in concurrency modeling are maximizing *throughput* (total amount of computation) or *fairness* (prioritizing or equalizing computation) or minimizing *response time* (time before beginning execution) or *latency* (time before completing execution).

## Synchronization (coordination or communication by blocking or waiting)

Synchronous means "at the same time", and synchronization is scheduling that blocks executions to wait for a dependency. Interdependent concurrent computations are coordinated by synchronizing the points of execution, which limits the independence or concurrency of the computations. Problems that require little to no synchronization are called *embarrassingly parallel* due to the potential for parallelization.

# Preemptive vs cooperative

Preemption refers to preempting or interrupting running computations to be resumed later, while cooperation means computations either run to completion or yield control back at suspension points.

A typical example of preemptive scheduling is operating system processes on a single-core processor; the processes allocate exclusive blocks of memory and can be executed independently, and the operating system periodically switches the active execution context between the processes to give an illusion of parallelism.

Preemption and cooperation are often used in the same systems at different layers of abstraction; for example, cooperative computations might also be periodically preempted to ensure a more fair distribution of processor time.

## Internal vs external

The internal vs external distinction refers to the source of initiative in control flow; in preemptive scheduling, the initiative is external because it comes from a system component that is external to the units of concurrency; in cooperative scheduling, the initiative is internal. The internal vs external distinction also maps to other pairs of concepts like synchronous vs asynchronous, pull vs push, direct vs inverted, opaque vs transparent and local vs remote or distributed.

# Control flow vs dataflow (imperative vs declarative)

Control flow refers to the path the point of execution takes in a program, and sequential programming that focuses on *explicit* control flow using control structures like loops or conditionals is called imperative programming. In an imperative model, data may follow the control flow, but the main question is about the order of execution.

Dataflow abstracts over explicit control flow by placing the emphasis on the routing and transformation of data and is part of the declarative programming paradigm. In a dataflow model, control follows data and computations are executed *implicitly* based on data availability.

Concurrency control refers to the use of explicit mechanisms like locks to synchronize interdependent concurrent computations. Dataflow is also used to abstract over explicit concurrency control.

## Control dependencies vs data dependencies

Dependence analysis is a formal theory for determining ordering constraints between computations, and the theory distinguishes between control and data dependencies, where data dependencies are also known as *true* dependencies. Dataflow models focus on data dependencies and allow avoiding spurious control dependencies like accidental locking, so dataflow models inherently simplify modeling more concurrent or independent computations.

Despite the advantages of dataflow models, imperative concurrency modeling is more conventional, which can be explained by the more direct correspondence between imperative computational models and machine models, which enables flexibility and, by extension, efficiency. The flexibility comes at a cost of the model being harder to reason about, however, so there is a trend towards the adoption of more declarative models.

# Nondeterministic vs deterministic

Nondeterminism means that the path of execution isn't fully determined by the specification of the computation, so the same input can produce different outcomes, while deterministic execution is guaranteed to be the same, given the same input. Related terms to "nondeterministic" are "probabilistic" and "stochastic".

The *source* of nondeterminism must be outside of the specification; for example, a common source of nondeterminism is a random number generator. Languages like C allow for "unspecified behavior" where the compiler implementers can apply different optimizations to the execution model, so the same source code (specification) could compile to different executables. Concurrency is a natural source of nondeterminism since the order and timings of independent processes can vary; for example, a server with multiple independent clients can receive requests at random times.

## Internal nondeterminism vs external (observable) nondeterminism

Computations can have internally nondeterministic paths of execution, but the nondeterminism can be abstracted away by the computational model and not observable externally. Nondeterministic interleaving of concurrent computations is a key source of complexity in concurrency modeling, and observable nondeterminism is generally an undesirable property in concurrency models since it can lead to irreproducible error states.

A common example of nondeterminism is a race condition, which refers to uncontrolled contention for a shared resource, such as simultaneously accessing and updating the same memory location in an unspecified order (data race). Unintentional race conditions can be a pernicious problem to debug since the states and timings that cause them can be hard or impossible to reliably reproduce (irreproducibility).

The conventional approach to concurrency modeling is using concurrency control primitives like locks, which means leaving it up to programmer discipline to avoid accidental observable nondeterminism. Locking can have *coarse-* or *fine-grained* synchronization granularity, where coarse-grained granularity trades concurrency for deterministic execution while fine-grained granularity is more concurrent but less predictable.

Declarative concurrency models reduce the need to make trade-offs between concurrency and correctness by imposing a data dependency-based ordering of execution or, in other words, trading expressive power of state (shared mutable state) for observable determinism. An example of a deterministic concurrency model is functional reactive programming (FRP) as defined by Conal Elliott, which imposes ordering constraints based on the concept of continuous time.

# Thread of execution and the blocking thread

The thread of execution (thread of control, logical thread) refers to the logical path of execution taken by the program, so it's broadly synonymous with control flow. There is also a more specific abstraction of the *blocking thread* or a sequence of operations with a call stack.

The blocking thread or threaded state is a core abstraction of imperative programming and is used in every mainstream imperative or mixed-paradigm language, and it's also recreated in functional languages as monadic composition of computations.

In a low-level or "close to metal" language like C, the blocking thread offers the advantage of relatively close correspondence between the computational and machine models (specifically, between hardware instructions and thread steps), simplifying compilation and enabling flexible use of the machine.

At a higher level, the sequential chaining of steps or effects or the concept of "doing one thing after another" is relatively simple for humans to compose and reason about. Functional-only languages like Haskell and Idris even feature syntactic extensions (do notation) that allow sequencing operations in a threaded-state style.

## System threads vs user-level threads (green, lightweight threads)

The term "thread", like "process", is most commonly associated with the operating system level, but also exists above it at the user or application level as either green or lightweight threads. System or kernel threads are relatively heavyweight in that allocating memory for the stack limits the possible concurrency, and context switching between system threads using system calls adds significant overhead; conversely, threads at a user level are more lightweight in both aspects and thus can offer better scalability for certain use cases.

The difference between green and lightweight threads is that green threads aren't necessarily lightweight; an example of green threads is classic Java Virtual Machine threads, while Go goroutines are an example of lightweight threads.

## Hybrid user and system threads

User-level threads can still be parallelized by being scheduled or multiplexed onto different system threads; an example of such scheduling is the the M:N model used for Go goroutines, also called *hybrid threading*. M refers to the number of user threads while N is the number of system threads. Alternative models are 1:1, which means using system threads as the unit of concurrency, and N:1, which means using a single system thread. A notable example of the 1:1 model is the Apache HTTP server, while JavaScript is a notable example of the N:1 model.

# Shared state vs message passing (distributed state)

Shared-state concurrency means that concurrent computations communicate through reading and updating a shared location in memory. Shared state requires explicit synchronization to avoid accidental race conditions. For example, the state may be inconsistent while updating, so simultaneously reading it will cause undefined behavior (data race). The higher-level concept of preventing interference between concurrent computations when using shared state is called *atomicity* or *linearizability*.

Message passing provides primitives for sending and receiving messages without requiring explicit synchronization because the state is distributed instead of shared. An example of a message passing primitive is a message queue.

The advantage of message passing over shared state is avoiding problems like deadlocks (circular dependencies that can result from acquiring multiple locks), but a message passing model can still express accidental nondeterminism due to message timing and order.

## Shared-memory system threads vs message passing user or hybrid threads

System threads can utilize both hardware parallelism and shared memory (and have been seen as the go-to approach for concurrency due to it), but this flexibility has significant trade-offs that make message passing without system threads more optimal for use cases that don't take advantage of the flexibility, such as servers that mostly wait for input and output.

Hybrid threading allows taking advantage of hardware parallelism while offering better scaling for i/o-bound use cases, and message passing allows even programs hosted on a single system thread to avoid blocking by communicating with an underlying thread pool dedicated to waiting for i/o (notably, that is the approach that allows JavaScript to be used for concurrency).

# Asynchronous vs synchronous (non-blocking, concurrent vs blocking, sequential)

Asynchrony means "not happening at the same time", and *asynchronous message passing* is a communication model that does not require the sending and receiving to be synchronized, meaning that the sender isn't blocked until the receiver is ready.

A sequential model like the blocking thread can be extended to support concurrency by overloading synchronous calls with asynchronous semantics, meaning that the call site does not create a dependency on the results of the asynchronous computation; instead, a runtime scheduler passes the results to a handler at a later time or asynchronously. A synchronous call with added asynchronous semantics is called an asynchronous call.

Languages that support first-class functions can model asynchronous messaging with continuation passing style by passing a result handler function (continuation) to the asynchronous call.

## System threads vs events

Events are messages used with a runtime scheduler called an event loop that dispatches messages from a message queue (message bus) to event handlers.

Event-based or event-driven models are typically used to implement concurrency while avoiding the expensive blocking of system threads (expensive due to the overhead of stack-based memory allocation), expensive system thread context switches and the complexity of managing locks with shared memory (the conventional communication model for system threads). A low-level counterpart of event handlers are hardware interrupt handlers.

# Communicating sequential processes (CSP)

CSP is a high-level concurrency model (introduced in 1978 by Tony Hoare) that combines the sequential thread (threaded state) abstraction with synchronous message passing communication. The message passing primitive is a blocking queue that blocks both on send and receive, meaning that the concurrent computations perform a *rendezvous*.

# Actor model

The actor model is a similarly high-level abstraction as CSP but differs in that it uses asynchronous message passing communication (mailboxes), where each actor can have a named identity, zero to more mailboxes, and can send messages to their own mailboxes.

<!--
# Push vs pull

# Reactive vs interactive

# Linearizability vs serializability

# Wait-free vs lock-free
-->

# Resources

- [Programming Paradigms for Dummies: What Every Programmer Should Know][dummies]
- [Railway Oriented Programming][rop]
- [Functional Programming with Effects][effects]
<!--- [Robust Composition: Towards a Unified Approach to Access Control and Concurrency Control][markm]-->

# Bibliography

- *Encyclopedia of Parallel Computing* by David Padua (Ed.) 2011

# License

Licensed under [CC BY-SA 2.0][license].

[license]: https://creativecommons.org/licenses/by-sa/2.0/
[dummies]: https://www.info.ucl.ac.be/~pvr/VanRoyChapter.pdf
[rop]: https://fsharpforfunandprofit.com/rop/
[effects]: https://www.youtube.com/watch?v=GZRL5Z40w60
[markm]: http://erights.org/talks/thesis/markm-thesis.pdf

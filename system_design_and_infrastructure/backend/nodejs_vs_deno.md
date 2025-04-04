<div align='center'>
  <h1>NodeJS vs Deno</h1>
</div>

# Table of Contents

- Why NodeJS
  - NodeJS Runtime Environment
    - Node API
    - V8 Engine
      - Pipeline
      - Call Stack
      - Memory Heap
    - Event Queue
    - Event Loop
      - Timer Phase:
      - Pending Callbacks Phase:
      - Idle Phase:
      - Poll Phase:
      - Check Phase:
      - Close Callbacks Phase:
- Why Deno
  - Deno Features
   
# Why NodeJS

JavaScript needs a ' runtime environment ' to run `asynchronous (non-blocking) operations` and to run JavaScript code outside the browser (e.g. on a mobile device) on the server/backend side.

Node.js (and also Deno) is a [cross-platform, open-source JavaScript runtime environment](https://en.wikipedia.org/wiki/Node.js) (not a framework or programming language) written in C++ that uses Google's V8 JavaScript engine to `enable asynchronous operations to be executed outside the browser`. It is `asynchronous by default and single-threaded`, it traditionally uses an `event-driven model with a single-threaded event loop` for handling asynchronous (non-blocking) I/O-bound operations. Therefore, it is designed to handle data I/O-bound (intensive) and real-time operations more efficiently than CPU-bound (intensive) operations such as video processing and image editing. 

- Pros: NodeJS can be used for both the Backend and Frontend. It allows fast development and is optimal for data I/O-bound and real-time operations.
- Cons: NodeJS is single-threaded, i.e., it runs JS code in a single thread. Although it can run CPU-bound operations through the [worker_threads module](https://nodejs.org/api/worker_threads.html#worker-threads), it is not optimal for it.

- Notes:
  - `Single-threaded`: instructions are executed one task at a time, sequentially, in order, in a single call stack (no parallelism) and, therefore, in a synchronous way. Suitable for `minimal data I/O operations`.
  - `Concurrency` is a general concept that involves managing multiple tasks at once, but not necessarily simultaneously (in parallel) since it can switch between tasks. It encompasses multiple paradigms, such as multithreading, multiprocessing, event-driven programming, and asynchronous programming.
  - `Multithreading` is a specific form of concurrency where multiple threads run within a single process to perform multiple tasks in parallel, `sharing the same memory space`. It is suitable for `data I/O-bound (intensive) operations`.
    - `Thread`: is a lightweight unit of execution within a process, and it can be scheduled to run on different CPU cores for parallel processing.
  - `Multiprocessing`: is a specific form of concurrency where multiple processes run `independently and simultaneously`, `each with its own memory space`, potentially using multiple CPUs to achieve parallelism. It allows `true parallelism`, making it suitable for `CPU-bound (intensive) operations`.
  - `Asynchronous` is a specific form of concurrency that happens in a non-blocking manner, where a program does not block the execution of another program during runtime. It is efficient for data I/O-bound (intensive) operations such as network requests and file system access. It does not run tasks in parallel (simultaneously).
  
## NodeJS Runtime Environment

The runtime environment of NodeJS consists of a `Node API`, an `Event Queue`, an `Event Loop`, and the `Google's V8 Engine`.

- `Node API`: provides a set of built-in modules (path, os, fs) and asynchronous functions such as timers, I/O operations, network requests, system interaction, and performance monitoring. This API uses C bindings to facilitate communication between the `LIBUV library` and the V8 engine.
  - `Global object`: while the browser's Web API has the `window object`, NodeJS has the `global object`.
  
- `Google's V8 JavaScript Engine`: is the JS engine that parses JS source code into optimized machine code (binary code) with Just-In-Time (JIT) compilation at run time.
  - A typical pipeline within a JS engine looks as follows: `Source Code` -> `Parser` -> `AST` -> `Interpreter` -> `Bytecode` -> `Profiler (monitor)` -> `JIT Compiler` -> `Optimized Code`.
  - Call Stack: used to keep track of local variables (whose size/length is fixed) and the order of function calls and their execution following the LIFO method. Each function call creates a new stack frame that stores its primitive types (numbers, booleans, strings, etc.) and references (the memory address or pointer) to objects, while `the actual object is stored in the heap`.
  - Memory Heap: is a fixed-size region in memory used for dynamic memory allocation, i.e., to allocate memory for storing, without order, dynamic objects (hash tables, arrays, functions, etc.) created at run time since they do not have a fixed size/length (is unknown). It enables efficient storage and retrieval of data during a program's execution. Memory management is managed by the `garbage collector`.

- `Event Queue`: this queue holds callback functions from asynchronous operations that are ready to be executed by the engine's Call Stack.

- `Event Loop`: it continuously checks if the `Call Stack` is empty. If it is, and there are tasks in the `Event Queue` (macrotasks), the Event loop takes the first task from the Event Queue and pushes it onto the Call Stack for execution. However, before executing a macro task, the event loop first processes any tasks in the `process.nextTick queue` (highest priority), followed by tasks in the `microtask queue` (which includes promises, MutationObserver callbacks, etc.), and finally moves on to tasks in the `macrotask queue`. It allows NodeJS to run multiple tasks on a single thread. This is achieved by using a built-in asynchronous I/O-bound library in C called `LIBUV`. NodeJS is traditionally single-threaded, however, the [worker_threads](https://nodejs.org/api/worker_threads.html) module enables multithreading capabilities for handling `CPU-bound operations in parallel` since it uses a separate event loop. ["Workers (threads) are useful for performing CPU-intensive JavaScript operations. They do not help much with I/O-intensive work. The Node.js built-in asynchronous I/O operations are more efficient than Workers can be."](https://nodejs.org/api/worker_threads.html#worker-threads)
  - The Node.js event loop has six phases, which are:
    - Timer Phase:
    - Pending Callbacks Phase:
    - Idle Phase:
    - Poll Phase:
    - Check Phase:
    - Close Callbacks Phase:

# Why Deno

While Node.js is a runtime for JavaScript, Deno is a `runtime for both JavaScript and TypeScript`. Deno has a built-in TypeScript compiler that enables TypeScript to run out of the box. 

`Deno is built on V8, Rust, and Tokio`. While NodeJS runs using C/C++, Deno runs using Rust. It uses Google's V8 engine to run JavaScript outside the browser. It uses a `rusty_v8` program so that the V8 engine can communicate with the Rust codebase. To run multiple tasks, an event loop is required, this is achieved by using a third-party asynchronous I/O-bound library in Rust called Tokio that enables multiprocessing through the use of `Thread Pool and workers`. 

- Notes:
  - Rust is a programming language designed at Mozilla that has a performance comparable to that of C. While C programs are prone to human errors that can cause buffer overflows and dangling pointers, Rust includes safety guarantees around memory and concurrency (e.g., multithreading) with a small overhead.

## Deno Features

- Modules: Unlike Node.js which uses CommonJS syntax, Deno uses ECMAScript (ESM) syntax by default. Modules can be imported using a URL with the import keyword instead of the required keyword.

- Tooling: Deno has a [built-in test runner](https://docs.deno.com/runtime/fundamentals/testing/), a [code formatter](https://docs.deno.com/runtime/reference/cli/formatter/), [file watchers](https://deno.land/x/denon@2.5.0), and others.

- [Security and permissions](https://docs.deno.com/runtime/fundamentals/security/#:~:text=Deno%20is%20secure%20by%20default,with%20the%20runtime%20permission%20prompt.): Unlike Node.js, Deno scripts do not have access to the file system, network connectivity, environment variables, or other sensitive APIs unless explicitly enabled by the user.

- Browser compatibility: Deno follows web standards, which means that it supports many of the APIs that are available in browsers, such as fetch for `HTTP requests`, `WebSocket` for real-time communication, `EventTarget` for event handling, and `TextEncoder/TextDecoder` for encoding and decoding strings into binary data and vice-versa.

- Async: The rust backend of Deno always translates asynchronous code into a promise named `rust future`. 

- Killer feature: a standalone executable for running modules on any machine without requiring external dependencies: ```bash deno compile --output myapp myapp.ts```

```bash
deno run module.js
deno run module.ts
```

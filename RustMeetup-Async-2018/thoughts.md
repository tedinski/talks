
## Concurrency vs Parallelism

- Concurrency is really about asynchrony
- Asynchrony is driven by event loops

- Secretly, everything is concurrent (just synchronous)

- Async is how we use single-threaded concurrency
  - synchronous calls
  - synchronization primitives

## Why do we want concurrency?

- Synchronous threading model
  - Compute vs I/O
  - Idleness waste
  - Over-subscription waste
  - Worse latency, worse throughput

- Asynchrony to the rescue
  - Event loops scale
  - Can even do concurrent requests

## Concurrency models

- We've seen the synchronous threaded model
- Callback-based
- Green threads

## Promises / Futures

- Callbacks without the hastle

## Comparison

- Node.js eager
- C# eager, parallel
- Rust lazy, single or parallel

## What is an async function

- Has gaps

## How do we do things concurrently?

- Obviously, we can pursure multiple async functions at once
- But how to compose them?



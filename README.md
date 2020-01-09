## Javascript-like C++ promise library

This library provides a `Promise` class that aims to mimic the behavior of the typical
JavaScript promise object. It does not implement any particular Javascript standard promise
API (Promises/A, native promise, etc), but follows the main principles. This document assumes that
the reader has a basic understanding of how typical Javascript promises work.

IMPORTANT NOTE: there is one major difference, though. Most modern Javascript promises (including JS Native promises) resolve asynchronously, i.e. their `resolve()` method does not directly call the `then()` handlers,
but schedules the calls on the next message loop iteration. The same happens when a `then()/catch()` handler
is attached to an already resolved/rejected promise. This may be a bit less efficient, but makes the behavior symmetric and more predictable. This library resolves synchronously, because it is unaware of the
message loop that is used in the application.

A brief overview of the API follows. For practical usage examples and details on the behavior,
please see the included tests in `tests/promise-test.cpp`

## The Promise<T, L> class
### Template parameters
Intuitively, the T class is the type of the value, held by the promise object. It can be `void` as well.

As for what `L` is - some explanation is needed. When `.then()` and `.fail()` handlers
are attached to a promise, they are added to internal lists. For performance reasons,
these lists are implemented as static arrays. This avoids dynamic memory 
allocation and deallocation when promise objects are created and destroyed. This comes at
the cost of having a fixed limit of `.then` and `.fail` handlers that can be attached to
a _single_ promise object. Note that this does not affect the promise chain length. This limit
applies only to multiple handlers of each type, attached to a single Promise object.
Rarely more than one or two handlers of each type are used. The `L` constant is the size of 
these arrays, and its default value is 4. You can currently increase it only per-promise by overriding
the default value for the L parameter.

### Lifetime
The Promise object is internally reference counted, so copying it is very lightweight and its lifetime is
automatically managed. Normally you don't need to create Promise objects on the heap with `operator new`, and
pass them by pointers. They are designed to be allocated on the stack or as a member.
You can regard the Promise<T> class as a fancy shared pointer.

### Error class
Error is a special class that carries information about an error. It has a type code, error code and an
optional message string. It is reference-counted, so it's very lightweight to copy around and
its lifetime is automatically managed.
If a particular Error object is not passed to a `fail()` handler during its lifetime,
an "unhandled promise error" warning will be printed to `stderr`. The user can configure the library
to call a function instead, by defining the macro `PROMISE_ON_UNHANDLED_ERROR` to the name of the function
to be called. The function has the following prototype: `myHandler(const std::string& msg, int type, int code)`.
In this case, `PROMISE_ON_UNHANDLED_ERROR` has to be defined as:

`#define PROMISE_ON_UNHANDLED_ERROR myHandler`

The handler function doesn't need to be aware of the Promise library - this is the reason its prototype is specified to not take the Error object directly.

### `Promise::resolve(T val)`
### `Promise::resolve()`
Resolves the promise and causes `.then()` handlers to be executed down the promise chain.

### `Promise::reject(Error err)`
Rejects the promise and causes `.fail()` handlers to be executed down the promise chain.


### `Promise::then(F&& func)`
The provided functor is called with the value of the promise, when the promise is
resolved. The return value of `then()` is a `Promise` object of the type, returned
by the functor. If the functor returns a promise, the return type of the type of that promise.

### `Promise::fail(F&& func)`
The provided functor is called with an Error object. The return value is a promise of the same type as the
one on which `fail()` is called. If the functor returns a promise that is eventually rejected, further error handlers down the chain will be called, if any. If there are none, a warning will be printed on stdout (see above). If the returned promise is eventually resolved, further `.then()` handlers down the chain will be executed.

### `Promise::done()`
Returns the state of the promise, as one of the three values of the `ResolvedState` enum - pending, succeeded or
failed. The pending state `kNotResolved` has the value of zero, so the value can be conveniently used in boolean
expressions to signify whether the promise is is pending or resolved/failed, i.e. "done".

### `Promise::succeeded(), Promise::failed()`
Convenience methods to check the state of the promise.

### `Promise::error()`
Returns the Error object with which the promise was rejected. If the promise is not in `kRejected` state, an assertion is triggered. Therefore, this method should only be called after a check if the promise is actually
in rejected state.

### `Promise::when(...), Promise::when(std::vector<Promise<P>>)`
Returns a Promise<void> that is:
 - Resolved when all the provided promises are resolved.
 - Rejected if at least one of the provided promises is rejected.
The difference between the two methods is that the one that takes multiple arguments can take promises of different
types, where as the one that takes a vector operates on promises of the same type.



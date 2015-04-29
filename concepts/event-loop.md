---
currentMenu: event_loop
---

# Overview
The *event loop* is the core of Evflow and of any asynchronous application. Its the event loop's responsibility to control the execution flow of the application and keep track of event callbacks that need to be invoked.

At its most basic level, the event loop is a scheduler for executing callback functions.

## Conventional event loops
Evflow's event loop takes on a different architecture than the event loops found in most other asynchronous libraries. Typically, an event loop allows you to register events you would like to listen to with the event loop directly. When the event loop is run, it waits for incoming events by whatever means necessary and invokes a callback function when the event is triggered.

For example, in an asynchronous web server, you would probably open a new server socket and register the socket with an event loop. You could then listen for incoming connections from a web browser asynchronously without blocking the main process.

Similarly, if a certain web page request required making a database query, you could open up a connection to the database, send the query, and register the database connection with the event loop. You could then receive the query result asynchronously without blocking requests for other web pages in the meantime.

## Evflow's view on event loops
Usually, this means the event loop must be hard-coded to be able to listen to as many kinds of events that you want to support. In Evflow, different types of events are abstracted and instead leaves it to the [event devices](event-devices.md) to listen for specific types of events. This makes it easy to augment existing event loops with new functionality and create asynchronous code for completely new types of real-time events.

# Using implementations
Depending on the application, different kinds of event loops may grant you improved performance or more useful features. Currently, Evflow only provides one event loop implementation, but more may be included in the future. All implementations should implement the `LoopInterface` interface so that using an event loop is implementation-independent.

The default implementation in Evflow is `NativeLoop`, named after the fact that it is written in native PHP and does not depend on any PHP extensions. It implements `LoopInterface`, so we can use it like every other event loop. Let's look at an example of how to use it:

```php
use Evflow\NativeLoop;

$loop = new NativeLoop();

$loop->futureTick(function () {
    echo "Hello, world!\n";
});

$loop->run();
```

The output of this code is pretty simple:

```
Hello, world!
```

What exactly is happening here? First, we create a new *event loop instance*. This object contains the event loop code and can be used for scheduling callbacks.

Next, we use the `futureTick()` method to schedule an anonymous function to be asynchronously called later. But how does it work? The given function is added to `$loop`'s queue of functions to be called at some future time, and when the event loop starts running, it will call our function at the appropriate time. We will explore how it decides what time is appropriate a little later.

Finally, we call the event loop's `run()` method to start the event loop. At this point the loop starts listening for events and executing callbacks. In this example we aren't listening for any events, so the only thing the event loop does is call our function we passed to `futureTick()` earlier. If we tweak our example a little, you can see the order of execution more clearly:

```php
use Evflow\NativeLoop;

$loop = new NativeLoop();

$loop->futureTick(function () {
    echo "Hello from callback!\n";
});

echo "Hello synchronously!\n";
$loop->run();
```

The output of this code is not what you might think:

```
Hello synchronously!
Hello from callback!
```

In this example, the message "Hello synchronously!" is displayed first, despite appearing in our code after our anonymous function. This illustrates another concept to keep in mind: all asynchronous functions are called only while the event loop is running, specifically when `run()` is called. If you were to write code after the event loop is run, it would be executed only after all asynchronous callbacks have been called.

# Instances and global state
As we have seen, event loops are self-contained objects that have no global scope. In order for the magical asynchronous apps that you've been hearing so much about to work, all components of a program need to share the same event loop. Doing so helps the event loop to make wise choices on when to schedule callbacks to be executed and when to listen for incoming events.

Typically an application only needs one event loop per process or thread. Here we introduce the static `Loop` class, which manages an event loop instance that is globally accessible to a program. We call this event loop instance the *global loop*, which is typically the only loop most applications should use.

## The `Loop` static class
The `Loop` class is a simple, but powerful, static class that introduces minimal global scope in exchange for looser coupling between asynchronous code and the event loop without adding unnecessary complexity. It offers us some useful features unique to the global loop, but at its core it is little more than a static holder for a regular event loop instance.

Before we can use the global loop, it must be initialized. At this step an event loop instance is created and promoted to be the global loop:

```php
use Evflow\Loop;
Loop::init();
```

By default, the global loop is an instance of `NativeLoop`. If we want to use a different event loop implementation, we can pass our own instance to the `init()` function and it will be promoted to the global loop. Any event loop implementing `LoopInterface` (*remember him?*) can be promoted to the global loop:

```php
use Evflow\Loop;
use FooBar\EpicLoop;

$loop = new EpicLoop(42);
Loop::init($loop);
```

Once the global loop has been initialized, we can access it using the `instance()` static method:

```php
\Evflow\Loop::instance()->futureTick(function () {
    echo "Howdy, boys.\n";
});
```

The `Loop` class also provides a few static shortcut methods for methods common to the loop interface; these have the same name as their instance counterparts. For example, the above snippet can be shortened to:

```php
\Evflow\Loop::futureTick(function () {
    echo "Howdy, boys.\n";
});
```

The call will be delegated to the global loop instance appropriately.

## Loop autostarting
As we saw earlier, to actually have our callback functions get executed, we need to run the event loop using the `run()` method. We can do that on the global loop as well:

```php
\Evflow\Loop::run();
```

The `Loop` class offers a feature for the global loop called *autostarting* that does this step for you. If the global loop has been initialized, you must want to use it, so the `Loop` class automatically calls `run()` after the last statement in the entire program finishes. If you want to disable this feature and start the loop at a different time, just call the `disableAutoStart()` method:

```php
\Evflow\Loop::disableAutoStart();
```

You will then have to call `run()` manually for the event loop to be executed.

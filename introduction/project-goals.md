---
currentMenu: project_goals
---

# Project goals
The purpose of the Evflow project is twofold: to experiment, discover and design a standard method for writing and running asynchronous PHP code, and to provide a solid, capable implementation thereof for general use.

# The design
The design we are developing is fairly unique in the asynchronous world, and not without reason. We think there are some fundamental design considerations that affect the power and extensibility of an asynchronous environment that even popular, low-level libraries miss out on. Not that libraries such as [libev](http://libev.schmorp.de) are bad, but in the PHP environment we think that there is a better way.

# The implementation
Since the design is still a work in progress, you can imagine the implementation is as well. At its current state, the implementation is a long way off from being stable enough for a production-level application, but is still quite powerful and serves as a way to test the design in real code and feed the results back into the design.

Unlike some libraries, the Evflow implementation is meant to be only an event library; it should do one thing, and one thing well. Relevant features could be added in the future beyond a basic event loop to make it more useful. Some things that should be or are being looked into are:

- Using generators to make creating arbitrary async functions simpler and more powerful
    + Many ideas could be borrowed from Luke Hoban's excellent [Async Functions for ECMAScript](http://github.com/lukehoban/ecmascript-asyncawait) proposal.
    + Nikita Popov already wrote [an awesome article](http://nikic.github.io/2012/12/22/Cooperative-multitasking-using-coroutines-in-PHP.html) on using generators for multitasking. Many raw ideas should be borrowed from him.
- Loop classes that use [libev](http://libev.schmorp.de), [libuv](https://github.com/joyent/libuv), or [libevent](http://libevent.org) as a back-end to implement event handling
- Synchronizing with event loops in other (forked) processes
    + Dealing with forks is easier with Kris Wallsmith's [Spork](https://github.com/kriswallsmith/spork) library.
- Task workers using [pthreads](http://pthreads.org)
- POSIX process signals & interrupts
- [The many excellent ideas](http://wiki.dlang.org/Event_system) the D language community has for their own language; some of which could be borrowed

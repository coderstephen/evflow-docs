# What is Evflow?
Evflow is a library for PHP that enables you to write asynchronous applications that can handle multiple I/O operations simultaneously and handle incoming events from the computer instantly, all without requiring you to manage multiple processes or threading. Concurrency is the name of the game, and pure, portable PHP is the road to fame. Evflow has no extension or native library dependencies and is designed from the ground up to be flexible and extensible.

Unlike other asynchronous libraries, Evflow takes a slightly different approach than popular asynchronous projects like [NodeJS](https://nodejs.org). Evflow casts aside the idea of the "God object"-like event loop and allows you to write your own kinds of events for the event to listen for.

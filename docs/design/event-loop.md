# The event loop
The event loop is the core of Evflow and of any asynchronous application. Its
the event loop's responsibility to control the execution flow of the application
and keep track of event callbacks that need to be invoked.

### Typical event loops
Evflow's event loop takes on a different architecture than the event loops found
in most other asynchronous libraries. Typically, an event loop allows you to
register events you would like to listen to with the event loop directly. When
the event loop is run, it waits for incoming events by whatever means necessary
and invokes a callback function when the event is triggered.

For example, in an asynchronous web server, you would probably open a new server
socket and register the socket with an event loop. You could then listen for
incoming connections from a web browser asynchronously without blocking the main
process.

Similarly, if a certain web page request required making a database query, you
could open up a connection to the database, send the query, and register the
database connection with the event loop. You could then receive the query result
asynchronously without blocking requests for other web pages in the meantime.

### Evflow's event loop
Usually, this means the event loop must be hard-coded to be able to listen to
as many kinds of events that you want to support.

## Event devices
Event devices are where the magic of Evflow happens. Since the event loop is
completely event-agnostic, the event loop uses event devices to listen for
different types of events. The event loop contains the loop-specific code, while
event devices contain the event-specific code for polling for events and
selecting streams that are ready for reading or writing.

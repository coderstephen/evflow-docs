# Logging
Asynchronous programming is tough stuff and debugging asynchronous programs can
be difficult. To aid debugging, Evflow's event loop has built-in logging
capabilities. The `EventLoop` class supports sending logs to any
[PSR-3](http://www.php-fig.org/psr/psr-3/)-compliant logger.

To enable logging, call the `setLogger()` method on the event loop instance and
pass in a logger object that implements `Psr\Log\LoggerInterface`. For example,
we can use [Monolog](https://github.com/Seldaek/monolog) to write logs to
standard output:

```
use Evflow\Loop;
use Monolog\Handler\StreamHandler;
use Monolog\Logger;

$logger = new Logger('EventLoop');
$logger->pushHandler(new StreamHandler('php://stdout', Logger::DEBUG));
Loop::instance()->setLogger($logger);
```

## A note on performance
The event loop only writes log information if a logger has been registered. If a
logger has not been registered all log statements are ignored, but each log call
still has a function call overhead. In the overall scheme of things, this
shouldn't affect the overall performance of the event loop.

Keep in mind that if you do enable logging, all log operations are synchronous
and will block the event loop. If you are writing logs to a file on disk or over
a network, the speed of those devices will impact the performance of the event
loop.

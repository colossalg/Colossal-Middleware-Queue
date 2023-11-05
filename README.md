# Colossal-Middleware-Queue
A PSR-15 middleware allowing multiple other PSR-15 middleware to be treated as a single entity.

## Usage

```php
namespace Dummy;

use Psr\Http\Message\{
    ResponseInterface,
    ServerRequestInterface
};
use Psr\Http\Server\{
    MiddlewareInterface,
    RequestHandlerInterface
};

class DummyMiddlewareA
{
    public function process(ServerRequestInterface $request, RequestHandlerInterface $handler): ResponseInterface
    {
        echo 'A';
        return $handler->process($request);
    }
}

class DummyMiddlewareB
{
    public function process(ServerRequestInterface $request, RequestHandlerInterface $handler): ResponseInterface
    {
        echo 'B';
        return $handler->process($request);
    }
}

class DummyHandler
{
    public function handle(ServerRequestInterface $request): ResponseInterface
    {
        echo 'Handler';
        // Return a ResponseInterface
    }
}
    
```

```php
// ---------------------------------------------------------------------------- //
// Creating and using the MiddlewareQueue is trivial.                           //
// ---------------------------------------------------------------------------- //

use Colossal\MiddlewareQueue\MiddlewareQueue;
use Dummy\{
    DummyMiddlewareA,
    DummyMiddlewareB,
    DummyHandler
};

$dummyMiddlewareA = new DummyMiddlewareA();
$dummyMiddlewareB = new DummyMiddlewareB();

$middlewareQueue = new MiddlewareQueue();
$middlewareQueue->enqueue($dummyMiddlewareA);
$middlewareQueue->enqueue($dummyMiddlewareB);

// ---------------------------------------------------------------------------- //
// The resulting MiddlewareQueue can now be used as a single middleware.        //
// A call to process will now do the following (in order):                      //
//   - Call $dummyMiddlewareA->process() passing the request passed to          //
//     $middlewareQueue->process().                                             //
//   - Call $dummyMiddlewareB->process() passing the request passed to          //
//     $middlewareQueue->process().                                             //
//   - Call RequestHandlerInterface::handle() on the handler passed to          //
//     $middlewareInterface->process().                                         //
// ---------------------------------------------------------------------------- //

$middlewareQueue->process($request, $dummyHandler);
// Prints:
// A
// B
// Handler

```

## Development Tips

### Running PHPStan Code Quality Analysis

Run the PHPStan code quality analysis with the following command:

```bash
>> .\vendor\bin\phpstan --configuration=phpstan.neon
```

### Running PHP Code Sniffer Code Style Analysis

Run the PHP Code Sniffer code style analysis with the following commands:

```bash
>> .\vendor\bin\phpcs --standard=phpcs.xml src
```

To fix automatically resolve issues found by PHP Code Sniffer run the following commands:

```bash
>> .\vendor\bin\phpcbf --standard=phpcs.xml src
```

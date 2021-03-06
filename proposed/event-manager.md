Event Manager interfaces
========================

Event Dispatching allows developer to inject logic into an application easily.
Many frameworks implement some form of a event dispatching that allows users to
inject functionality without the need to extend classes.

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be
interpreted as described in [RFC 2119][].

[RFC 2119]: http://tools.ietf.org/html/rfc2119

## Goal

Having common interfaces for dispatching and handling events, allows developers
to create libraries that can interact with many frameworks in a common fashion.

Some examples:

* Security framework that will prevent saving/accessing data when a user
does'nt have permission.
* A Common full page caching system
* Logging package to track all actions taken within the application

## Terms

*   **Event** - An action that about to take place (or has taken place).  The
event name MUST only contain the characters `A-Z`, `a-z`, `0-9`, `_`, and '.'.
It is RECOMMENDED that words in event names be separated using '.'
ex. 'foo.bar.baz.bat'

*   **Listener** - A list of callbacks that are passed the EventInterface and
MAY return a result.  Listeners MAY be attached to the EventManager with a
priority.  Listeners MUST BE called based on priority until a listener calls
```stop()``` on the event.

## Components

There are 2 interfaces needed for managing events:

1. An event object which contains all the information about the event.
2. The event manager which holds all the listeners.

### EventInterface

The EventInterface defines the methods needed to dispatch an event.  Each event
MUST contain a event name in order trigger the listeners. Each event MUST have a
target which is an object that is the context the event is being triggered for,
and MUST have the name of the event accessible.

```php

namespace Psr\EventManager;

/**
 * Representation of an event
 */
interface EventInterface
{
    /**
     * @param string $name
     * @param mixed $target
     */
    public function __construct($name, $target);

    /**
     * Get event name
     *
     * @return string
     */
    public function getName();

    /**
     * Get target/context from which event was triggered
     *
     * @return null|string|object
     */
    public function getTarget();

    /**
     * Stops further events from continuing
     */
    public functiion stop();

    /**
     * Used by the EM to check if the stop flag has been set
     *
     * @return bool
     */
    public function hasStopped();
}
```

### EventManagerInterface

The Event Manager holds all the attached listeners and is responsible for calling
all the listeners when an event is fired.  When ```trigger()``` is called, the EM
MUST call all listeners in order based on the priority from when they were attached.
The EM MUST stop called listeners if the events ```stop()``` is called on the event.
The EM will confirm a listener requested an event from stopping by calling
```hasStopped()``` on the event.

```php

namespace Psr\EventManager;

/**
 * Interface for EventManager
 */
interface EventManagerInterface
{
    /**
     * Attaches a listener to an event
     *
     * @param string $event the event to attach too
     * @param callable $callback a callable function
     * @param int $priority the priority at which the $callback executed
     * @return bool true on success false on failure
     */
    public function attach($event, $callback, $priority = 0);

    /**
     * Detaches a listener from an event
     *
     * @param string $event the event to attach too
     * @param callable $callback a callable function
     * @return bool true on success false on failure
     */
    public function detach($event, $callback);

    /**
     * Trigger an event
     *
     * Can accept an EventInterface or will create one if not passed
     *
     * @param  string|EventInterface $event
     * @param  object|string $target
     * @return mixed
     */
    public function trigger($event, $target = null);
}
```

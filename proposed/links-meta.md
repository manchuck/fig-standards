# Link Definition Meta Document

## 1. Summary

Hypermedia links are becoming an increasingly important part of the web, in both HTML contexts
and various API format contexts. However, there is no single common hypermedia format, nor
is there a common way to represent Links between formats.

This specification aims to provide PHP developers with a simple, common way of representing a
hypermedia link independently of the serialization format that is used. That in turn allows
a system to serialize a response with hypermedia links into one or more wire formats independently
of the process of deciding what those links should be.

### Open questions

The following questions are still outstanding, in the opinion of the Editor, and should be resolved.

* How do we support "empty" attributes, as HTML5 permits but few other systems do?
* Should Href be a string, or can/should we use PSR-7 URI objects? I'm very very tempted to go with the latter.
* Is there wording we should clean up around rel definitions?
* Should the rel definition information move from the interfaces to the spec, or stay in the interface docblocks where
  people can easily find it when using it?
* Currently, technically, URL templates would be disallowed. That's a problem for, say, HAL. How do we want to square
  that, especially if Href becomes an object?
* Should we allow rels to be multi-value, or force multiple rels to be multiple objects? (IE, each uri/rel combination
  becomes a distinct object.)

## 2. Scope

### 2.1 Goals

* This specification aims to extract and standardize hypermedia link representation between different
formats.

### 2.2 Non-Goals

* This specification does not seek to standardize or favor any particular hypermedia serialization format.

## 3. Design Decisions

### Why no mutator methods?

One of the key targets for this specification is PSR-7 Response objects.  Response objects by design must be
immutable.  Other value-object implementations likely would also require an immutable interface. Therefore,
this specification focuses only on accessor methods that allow links to be extracted from a source object.
How they got into that object is irrelevant.

In practice, immutable objects will likely incorporate with*()-style methods much like PSR-7 does. The definition
of those interfaces is out of the scope of this specification, however.

### Why is rel on a Link object multi-value?

Different hypermedia standards handle multiple links with the same relationship differently. Some have a single
link that has multiple rel's defined. Others have a single rel entry that then contains multiple links.

Defining each Link uniquely but allowing it to have multiple rels provides a most-compatible-denominator definition.
A single LinkInterface object may be serialized to one or more link entries in a given hypermedia format, as
appropriate.  However, specifying multiple link objects each with a single rel yet the same URI is also legal, and
a hypermedia format can serialize that as appropriate, too.

### Why is a LinkCollectionInterface needed?

In many contexts, a set of links will be attached to some other object.  Those objects may be used in situations
where all that is relevant is their links, or some subset of their links. For example, various different value
objects may be defined that represent different REST formats such as HAL, JSON-LD, or Atom.  It may be useful
to extract those links from such an object uniformly for further processing. For instance, next/previous links
may be extracted from an object and added to a PSR-7 Response object as Link headers.  Alternatively, many links
would make sense to represent with a "preload" link relationship, which would indicate to an HTTP 2-compatible
web server that the linked resources should be streamed to a client in anticipation of a subsequent request.

All of those cases are independent of the payload or encoding of the object. By providing a common interface
to access such links, we enable generic handling of the links themselves regardless of the value object or
domain object that is producing them.

## 4. People

### 4.1 Editor(s)

* Larry Garfield

### 4.2 Sponsors

* Evert Pot
* Matthew Weier O'Phinney (coordinator)

### 4.3 Contributors

## 5. Votes

## 6. Relevant links

* [What's in a link?](http://evertpot.com/whats-in-a-link/) by Evert Pot
* [FIG Link Working Group List](https://groups.google.com/forum/#!forum/php-fig-link)

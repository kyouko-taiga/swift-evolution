# Remove open Access Modifier

* Proposal: [SE-NNNN](nnnn-remove-open-access-modifier.md)
* Author: [Dimitri Racordon](https://github.com/kyouko-taiga), Joanna Carter
* Status: **Awaiting review**
* Review manager: TBD

## Introduction

Swift allows classes, methods, properties and subscripts to be marked as `final`, hence disallowing their subclassing/overriding inside **and** outside of their defining module.

It also features two access levels `open`, which allows an entity to be accessed outside its defining module, and `public`, which gives the same access level the former **and** allows the entity to be subclassed/overridden.

There's a clear overlap between `open` and `public`, as they essentially represent the same access control within the boundaries of a module, and do not add any benefit from outside them, as `final` is sufficient to prohibit subclassing/overriding.

Swift-evolution thread: ['Public' class visibility specifiers](https://lists.swift.org/pipermail/swift-evolution/Week-of-Mon-20170220/032576.html)


## Motivation

Swift has currently 5 access levels, in the form of:

* `private`, restricting the use of an entity to its enclosing declaration;
* `fileprivate`, restricting the use of an entity to its defining source file:
* `internal`, restricting the use of an entity to its defining module;
* `public`, allowing the entity to be accessed anywhere;
* `open`, allowing the entity to be accessed anywhere **and** allowing the entity to be subclassed/overridden outside of their defining module.

From inside a module, `open` and `public` represent exactly the same access level (everything is visible **and** can be subclassed/overridden).
From outside a module, `public` is actually akin to `final`.

```swift
// Module 1
// ========

open class OpenClass {}
public class PublicClass {}

public final class PublicFinalClass {}

// The first two classes above have the same access level.
class derivedFromOpen: OpenClass {}
class derivedFromPublic: PublicClass {}

// Module 2
// ========

class derivedFromOpenOutside: OpenClass {}

class derivedFromPublicOutside: PublicClass {}
// Error: Cannot inherit from non-open class ...

class derivedFromPublicFinalOutside: PublicFinalClass {}
// Error: Inheritance from a final class ...
```

Hence, the sole use-case of using both `public` and `final` is for an entity that *inside* its defining module should not be subclassed/overridden.

```swift
// Module 1
// ========

class derivedFromPublicFinal : PublicFinalClass {}
// Error: Inheritance from a final class ...
```

We believe this use case is rare and not worth the additional complexity of having an `open` access level in the language.
Besides, `open` is only applicable on classes and class members while the others can be used on other entities (protocols, structures, ...).
This asymmetry adds to the complexity of an `open` access level.

In order to simplify the syntax of Swift, we propose to **remove the `open` access modifier**.

## Proposal

Remove the `open` access modifier from Swift.

## Source compatibility

This is a breaking change, as the `open` keyword would disappear from the language.
However, a fixit would be quite easy to put in place, simply replacing `open` with `public`.

## Alternative considered

Not removing the `open` access modifier from the language and keep the status quo.

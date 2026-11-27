---
name: gof-design-patterns
description: Chooses and implements the 23 Gang of Four design patterns (creational, structural and behavioural) in C#, Rust, Python, Dart, Go, GenAIScript, TypeScript and C, with the trade-offs and the simpler alternative where one exists. Use when the user asks which pattern fits a problem, wants a named pattern such as Strategy, Builder, Observer or Visitor implemented in a given language, asks how two patterns differ or combine, or is refactoring code towards or away from a pattern. Do NOT use for architecture styles such as microservices, CQRS or event sourcing, for framework-specific idioms with no pattern question, or for general code review.
license: Apache-2.0
metadata:
  version: "3.0.0"
  source: "Design Patterns: Elements of Reusable Object-Oriented Software (1994)"
  author: Erich Gamma, Richard Helm, Ralph Johnson, John Vlissides
  attribution: Patterns restated with original implementations. No text reproduced from the book.
---

# Gang of Four design patterns

The 23 patterns from the 1994 book, with implementations in eight languages: C#, Rust, Python, Dart, Go, GenAIScript, TypeScript and C.

A pattern is a name for a shape of code that solves a recurring problem. The name is most of the value: it lets two engineers agree on a design in a sentence. The code is the easy part. The judgement is whether the problem is actually there yet, because a pattern applied to a problem you do not have is just indirection.

## Choosing a pattern

Start from the problem, not the catalogue. Ask what varies, and what should not have to change when it does. If nothing varies yet, the answer is usually no pattern.

Load `resources/pattern-selection-guide.md` for the decision tree and the problem categories. For a quick match:

| What you need | Pattern | Resource |
|---|---|---|
| Pick an algorithm at runtime | Strategy | behavioral-patterns.md |
| Behaviour that depends on internal state | State | behavioral-patterns.md |
| Notify several listeners of a change | Observer | behavioral-patterns.md |
| Undo and redo | Command, with Memento | behavioral-patterns.md |
| A chain of handlers, each may act | Chain of Responsibility | behavioral-patterns.md |
| Many objects talking to each other | Mediator | behavioral-patterns.md |
| New operations over a fixed structure | Visitor | behavioral-patterns.md |
| Fixed steps, varying details | Template Method | behavioral-patterns.md |
| Walk a collection without exposing it | Iterator | behavioral-patterns.md |
| Evaluate a small language | Interpreter | behavioral-patterns.md |
| Build an object with many optional parts | Builder | creational-patterns.md |
| Decide the concrete type at runtime | Factory Method | creational-patterns.md |
| Families of objects that must match | Abstract Factory | creational-patterns.md |
| Copy a configured object | Prototype | creational-patterns.md |
| Exactly one instance | Singleton | creational-patterns.md |
| Fit an interface you cannot change | Adapter | structural-patterns.md |
| Add behaviour without subclassing | Decorator | structural-patterns.md |
| A simple front to a messy subsystem | Facade | structural-patterns.md |
| Control or defer access to an object | Proxy | structural-patterns.md |
| Trees treated the same as leaves | Composite | structural-patterns.md |
| Vary an abstraction and its implementation separately | Bridge | structural-patterns.md |
| Share many small identical objects | Flyweight | structural-patterns.md |

## What has aged

The book predates generics in most languages, first-class functions in the mainstream, and dependency injection containers. Some of its advice reads differently now, and the answer should say so:

- **Singleton** is usually better as one instance registered with the DI container, or passed in. A global accessor hides the dependency and makes tests share state.
- **Strategy, Command and Template Method** are often a function or a lambda in a language that has them. Reach for the class form when the strategy carries state or several methods.
- **Iterator** is built into every language here except C.
- **Visitor** competes with pattern matching over a closed set of types (C# switch expressions, Rust enums, TypeScript discriminated unions, Dart sealed classes). Prefer matching when the set of types is closed and the operations grow.
- **Abstract Factory plus Singleton**, a combination the older literature recommends, mostly becomes a factory registered once in the container.

## Implementing one

Give complete, runnable code in the language asked for, written the way that language is normally written: traits and enums in Rust, implicit interfaces and function types in Go, ABCs or protocols in Python. A Java-shaped class hierarchy translated literally into Go or Rust is a poor answer even if it compiles. `resources/language-guide.md` has the idioms for each language.

Alongside the code, say:

- why this pattern fits the problem, in a sentence or two;
- what it costs: extra types, indirection, and where it makes the code harder to follow;
- the simpler alternative, if there is one, and when that would be enough;
- how to test it, briefly.

Where the user asks for a pattern that does not fit, say so and show what would. Doing what was asked badly is not more helpful than a short correction.

## Patterns that work together

Some pairs come up often enough to name:

- Composite with Iterator or Visitor, for walking and operating on trees.
- Command with Memento, for undo.
- Factory Method with Strategy, where the factory picks the strategy.
- Decorator with a factory, so callers get the decorated object without building the stack.
- Observer with Mediator, so listeners do not need to know each other.

`resources/pattern-selection-guide.md` has worked examples of each.

## Resources

| Resource | What is in it |
|---|---|
| `resources/pattern-selection-guide.md` | Decision tree, problem categories, combinations |
| `resources/creational-patterns.md` | Singleton, Factory Method, Abstract Factory, Builder, Prototype |
| `resources/structural-patterns.md` | Adapter, Bridge, Composite, Decorator, Facade, Flyweight, Proxy |
| `resources/behavioral-patterns.md` | The eleven behavioural patterns |
| `resources/language-guide.md` | Idioms for each of the eight languages |
| `resources/patterns-reference.md` | Longer descriptions, structure diagrams, relationships |

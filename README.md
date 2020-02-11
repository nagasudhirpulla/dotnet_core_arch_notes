>If you think good architecture is expensive, try bad architecture.

> *Brian Foote and Joseph Yoder*
---
> … with proper design, the features come cheaply. This approach is arduous, but continues to succeed.

> *Dennis Ritchie*
---
## Micosoft ebook on clean architecture of Monolithic applications
https://aka.ms/webappebook

## Choosing between traditional and SPA applications
Use traditional web apps when:
- Application’s client-side requirements are simple or even read-only
- Application needs to function in browsers without JavaScript support
- Dev Team is unfamiliar with JavaScript or TypeScript development techniques

Use a SPA when:
- Application must expose a rich user interface with many features
- Dev team is familiar with JavaScript and/or TypeScript development
- Application must already expose an API for other (internal or public) clients

A hybrid approach is also possible, the simplest being more rich SPA-like sub-applications within a larger traditional web application.

SPAs can be written in C# and run on browser using WebAssembly (Razor Components). But this is still in beginning phase.

Factor | Traditional Web App | Single Page Application
--- | --- | ---
*Required Team Familiarity with JavaScript/TypeScript* | Minimal | Required
*Support Browsers without Scripting* | Supported | Not Supported
*Minimal Client-Side Application Behavior* | Well-Suited | Overkill
*Rich, Complex User Interface Requirements* | Limited | Well-Suited

> Atwood’s Law: Any application that can be written in JavaScript, will eventually be written in JavaScript.

> *Jeff Atwood*

## Common Design Principles
> If builders built buildings the way programmers wrote programs, then the first woodpecker that came along would destroy civilization.

> *Gerald Weinberg*
---
### Separation of Concerns (SOC)
``` Separation of concerns is a key consideration behind the use of layers in application architectures. ```
- Apps can follow SOC by separating core business behavior from infrastructure and user interface logic.
- This helps ensure that the business model is easy to test and can evolve without being tightly coupled to low-level implementation details

### Encapsulation
``` Achieves loose coupling and modularity, since objects and packages can be replaced with alternative implementations so long as the same **interface** is maintained. ```
- In classes, encapsulation is achieved by limiting outside access to the internal state. A well-defined function (or property setter) can be exposed to manipulate the object state rather than giving direct access to the internal state
- Application components and applications should expose well-defined **interfaces**, rather than allowing their state to be modified directly.

### Dependency Inversion
``` The direction of dependency within the application should be in the direction of abstraction, not implementation details. ```
![Dependency inversion Figure](https://github.com/nagasudhirpulla/dotnet_core_arch_notes/raw/master/inverted_dependency_graph.png)
- Dependency inversion is a key part of building loosely-coupled applications
- The resulting applications are more testable, modular, and maintainable as a result. The practice of dependency injection is made possible by following the dependency inversion principle

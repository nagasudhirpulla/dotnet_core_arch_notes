>If you think good architecture is expensive, try bad architecture.
>
> *- Brian Foote and Joseph Yoder*
---
> … with proper design, the features come cheaply. This approach is arduous, but continues to succeed.
> 
> *- Dennis Ritchie*
---
## Micosoft ebook on clean architecture of Monolithic applications
https://aka.ms/webappebook

## Jason Taylor NorthwindTraders Clean architecture example
https://github.com/jasontaylordev/NorthwindTraders

## Choosing between traditional and SPA applications
Use traditional web apps when:
- Application’s client-side requirements are simple or even read-only
- Application needs to function in browsers without JavaScript support
- Dev Team is unfamiliar with JavaScript or TypeScript development techniques

Use a SPA when:
- Application must expose a rich user interface with many features
- Dev team is familiar with JavaScript and/or TypeScript development
- Application must already expose an API for other (internal or public) clients

A hybrid approach is also possible, where more rich SPA-like sub-applications within a larger traditional web application.

SPAs can be written in C# and run on browser using WebAssembly (Razor Components). But this is still in beginning phase.

Factor | Traditional Web App | Single Page Application
--- | --- | ---
*Required Team Familiarity with JavaScript/TypeScript* | Minimal | Required
*Support Browsers without Scripting* | Supported | Not Supported
*Minimal Client-Side Application Behavior* | Well-Suited | Overkill
*Rich, Complex User Interface Requirements* | Limited | Well-Suited
---
> Atwood’s Law: Any application that can be written in JavaScript, will eventually be written in JavaScript.
> 
> *- Jeff Atwood*
---
## Common Design Principles
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
![Dependency inversion Figure](https://github.com/nagasudhirpulla/dotnet_core_arch_notes/raw/master/assets/inverted_dependency_graph.png)
- Dependency inversion is a key part of building loosely-coupled applications
- The resulting applications are more testable, modular, and maintainable as a result. The practice of dependency injection is made possible by following the dependency inversion principle

### Explicit Dependencies
``` Methods and classes should explicitly require any collaborating objects they need in order to function correctly ```
- One example is that the class constructor should specify the client whatever dependencied that are to be injected (possibly nothing if the class is just using a default constructor)
- By following the explicit dependencies principle, our classes and methods are being honest with their clients about what they need in order to function

### Single Responsibility
```Objects should have only one responsibility and that they should have only one reason to change ```
- For example, Presentation responsibility should remain in the UI project, while data access responsibility should be kept within an infrastructure project. Business logic should be kept in the application core project, where it can be easily tested and can evolve independently from other responsibilities.

### Don't Repeat Yourself (DRY)
``` The application should avoid specifying behavior related to a particular concept in multiple places as this is a frequent source of errors ```

### Persistence Ignorance (PI)
``` App code is unaffected by the choice of persistence technology ```

### Domain Driven Design (DDD)
``` DDD is a way of looking at software from top-down```
- Our focus should'nt be on technology, but the business or activity we want to achieve through the software
- An approach can be to create models of our domain and make the software confirm to that

### Bounded Contexts
- Bounded contexts are a central pattern in Domain-Driven Design
- Use different db contexts for the respective conceptual module of a large application. The modules can share data in the application layer rather than a shared db
---
> If builders built buildings the way programmers wrote programs, then the first woodpecker that came along would destroy civilization.
> 
> *- Gerald Weinberg*
---
## Layers in an application
Logical layering is a common technique for improving the organization of code in enterprise software applications

## Scaling Up and Scaling down
- **Scaling up** means adding additional CPU, memory, disk space, or other resources to the server hosting the app. 
- **Scaling out** means adding additional instances of app servers, like physical servers, VMs, or containers. When the app is hosted across multiple instances, a load balancer is used to assign requests to individual app instances.

## Clean Architecture
- Clean architecture puts the business logic and application model at the center of the application
- Instead of having business logic depend on data access or other infrastructure concerns, this dependency is inverted: infrastructure and implementation details depend on the Application Core
- This is achieved by defining abstractions, or interfaces, in the Application Core, which are then implemented by in the Infrastructure layer.

![Clean Architecture Project Layers](https://github.com/nagasudhirpulla/dotnet_core_arch_notes/raw/master/assets/clean_architecture_project_layers.png)

![Clean Architecture Projects in Solution](https://github.com/nagasudhirpulla/dotnet_core_arch_notes/raw/master/assets/clean_architecture_projects_in_solution.png)

Because the Application Core doesn’t depend on Infrastructure, it’s very easy to write automated unit tests for this layer

Since the UI layer doesn’t have any direct dependency on types of Infrastructure project, it’s easy to swap out implementations, either to facilitate testing or for changing application requirements

### Application Core Layer Types
- Entities (business model classes that are persisted) and Aggregates
- Interfaces
- Services
- DTOs (Data Transfer Objects)
- Specifications
- Exceptions

### Infrastructure Layer Types
- EF Core types (DbContext, Migrations)
- Data access implementation types (Repositories)
- Infrastructure-specific services (FileLogger, SmtpNotifier, etc.)
#### Notes
- Types in Infrastructure layer should implement interfaces defined in the Application Core
- Infrastructure Layer should have a reference to the Application Core project.

### UI Layer Types
- Controllers
- Filters
- Pages
- Services
- Views
- ViewModels
- Startup
#### Notes
- UI layer in an entry point for ASP.NET Core MVC-based application
- UI layer is an ASP.NET Core MVC (or Razor Pages) project
- This project should reference the Application Core project, and its types should interact with infrastructure strictly through interfaces defined in Application Core
- No direct instantiation of (or static calls to) Infrastructure layer types should be permitted in the UI layer

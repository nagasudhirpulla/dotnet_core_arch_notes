# Mediatr

## Purpose
MediatR allows us to decouple our controllers from our business logic by having our controller actions send a request message to a handler.
Mediatr library implements the **Mediator** design pattern.

## Inspiration
- Using mediatr library, we can implement CQRS (command query responsibility segregation) elegantly and make controller code very thin.
- This keeps code clean, maintainable and reduce the lines of code in controllers and other classes

Mediatr supports Request / Response messages and Notification broadcast messages

## Approach 1
- Add Mediatr to services DI container in **ConfigureServices** method of StartUp.cs file

```cs
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services.AddMediatR(Assembly.GetExecutingAssembly());
    // ...
}
```


## Links
- https://www.c-sharpcorner.com/article/command-mediator-pattern-in-asp-net-core-using-mediatr2/

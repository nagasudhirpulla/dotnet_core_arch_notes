# Mediatr

## Purpose
MediatR allows us to decouple our controllers from our business logic by having our controller actions send a request message to a handler.
Mediatr library implements the **Mediator** design pattern.

## Inspiration
- Using mediatr library, we can implement CQRS (command query responsibility segregation) elegantly and make controller code very thin.
- This keeps code clean, maintainable and reduce the lines of code in controllers and other classes

Mediatr supports both Request / Response messages and Broadcast messages

## Request / Response Implementation
- Add Mediatr to services DI container in **ConfigureServices** method of StartUp.cs file

```cs
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services.AddMediatR(Assembly.GetExecutingAssembly());
    // ...
}
```

- Create an ```IRequest<T>``` class which is like a request payload. T is the response that the handler should return.
```cs
 public class CreateProductCommand : IRequest<int>
{
    public string ProductName { get; set; }
    public decimal? UnitPrice { get; set; }
    // ...
}
```

- Create an ```IRequestHandler<T1, T2>``` class which acts a handler that handles the request. T1 is request type and T2 is response type. As shown below, we can also exploit dependency injection in request handlers.
```cs
public class CreateProductCommandHandler : IRequestHandler<CreateProductCommand, int>
{
    private readonly INorthwindDbContext _context;
    public CreateProductCommandHandler(INorthwindDbContext context)
    {    _context = context;    }

    public async Task<int> Handle(CreateProductCommand request, CancellationToken cancellationToken)
    {
        // ... all the request handling logic
        
        return entity.ProductId;
    }
}
```
## Links
- https://www.c-sharpcorner.com/article/command-mediator-pattern-in-asp-net-core-using-mediatr2/
- [Handler Code in NorthWindTraders](https://github.com/jasontaylordev/NorthwindTraders/blob/master/Src/Application/Products/Commands/CreateProduct/CreateProductCommandHandler.cs)


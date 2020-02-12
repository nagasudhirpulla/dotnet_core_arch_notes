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
    // inject required dependencies if required
    private readonly INorthwindDbContext _context;
    public CreateProductCommandHandler(INorthwindDbContext context)
    {    _context = context;    }

    public async Task<int> Handle(CreateProductCommand request, CancellationToken cancellationToken)
    {
        // ... request handling logic
        return entity.ProductId;
    }
}
```
## Notification Pattern Implementation
- Add Mediatr to services DI container in **ConfigureServices** method of StartUp.cs file

```cs
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services.AddMediatR(Assembly.GetExecutingAssembly());
    // ...
}
```

- Create an ```INotification``` class which is like a broadcast payload.
```cs
public class NewUser : INotification  
{  
    public string Username { get; set; }  
    public string Password { get; set; }  
}  
```

- Create ```INotificationHandler<T>``` handlers to handle the broadcasted message. T is the message type that is to be handled.
```cs
public class NewUserHandler : INotificationHandler<NewUser>  
{  
    public Task Handle(NewUser notification, CancellationToken cancellationToken)  
    {  
        //Save to log  
        Debug.WriteLine(" ****  Save user in database  *****");  
        return Task.FromResult(true);  
    }
} 

public class EmailHandler : INotificationHandler<NewUser>  
{  
    public Task Handle(NewUser notification, CancellationToken cancellationToken)  
    {  
        //Send email  
        Debug.WriteLine(" ****  Email sent to user  *****");  
        return Task.FromResult(true);  
    }  
} 

public class LogHandler : INotificationHandler<NewUser>  
{  
    public Task Handle(NewUser notification, CancellationToken cancellationToken)  
    {  
        //Save to log  
        Debug.WriteLine(" ****  User save to log  *****");  
        return Task.FromResult(true);  
    }  
}  
```

- Call the **Publish** method on the injected mediatr in controller methods.
```cs
public class AccountsController : Controller  
{
    // inject mediatr
    private readonly IMediator _mediator;  
    public AccountsController(IMediator mediator)  
    {  _mediator = mediator;  }  

    // ... controller code

    [HttpPost]  
    public ActionResult Register(NewUser user)  
    {
        // ... controller action code
        _mediator.Publish(user);  
        return RedirectToAction("Login");  
    }  
}  
```
## Links
- https://www.c-sharpcorner.com/article/command-mediator-pattern-in-asp-net-core-using-mediatr2/
- [Request Handler Code in NorthWindTraders](https://github.com/jasontaylordev/NorthwindTraders/blob/master/Src/Application/Products/Commands/CreateProduct/CreateProductCommandHandler.cs)


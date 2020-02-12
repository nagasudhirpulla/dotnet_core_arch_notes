# Mediatr

## Purpose
MediatR allows us to decouple our controllers from our business logic by having our controller actions send a request message to a handler.
Mediatr library implements the **Mediator** design pattern. 
Downside of using Mediatr is that, it violates the Explicit Dependency Principle.

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

- call **Send** method inside controller methods
```cs
[Authorize]
public class ProductsController : BaseController
{
    // ... controller code
    
    [HttpPost]
    public async Task<ActionResult<int>> Create([FromBody] CreateProductCommand command)
    {
        var productId = await Mediator.Send(command);
        return Ok(productId);
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

- Call the **Publish** method in controller methods.
```cs
public class AccountsController : Controller  
{
    // ... controller code

    [HttpPost]  
    public ActionResult Register(NewUser user)  
    {
        // ... controller action code
        Mediatr.Publish(user);  
        return RedirectToAction("Login");  
    }  
}  
```

## Request Pre-processing, Post-processing and Pipeline behaviours
Mediatr can also do request pre-processing, post-processing and pipeline beaviours, through which can do a lot things like handling, statistics etc.

- Add all Pre, Post and Pipeline behaviours to the service DI container
```cs
public static class DependencyInjection
{
    public static IServiceCollection AddApplication(this IServiceCollection services)
    {
        // ... other code
        services.AddTransient(typeof(IPipelineBehavior<,>), typeof(RequestPerformanceBehaviour<,>));
        services.AddTransient(typeof(IPipelineBehavior<,>), typeof(RequestValidationBehavior<,>));
        return services;
    }
}
```

- Define Request Pre-processors if any
```cs
public class RequestLogger<TRequest> : IRequestPreProcessor<TRequest>
{
    // injecting dependencies into the Pre-Processor via constructor
    private readonly ILogger _logger;
    private readonly ICurrentUserService _currentUserService;

    public RequestLogger(ILogger<TRequest> logger, ICurrentUserService currentUserService)
    {
        _logger = logger;
        _currentUserService = currentUserService;
    }

    //
    // pre-processing logic
    //
    public Task Process(TRequest request, CancellationToken cancellationToken)
    {
        var name = typeof(TRequest).Name;

        _logger.LogInformation("Northwind Request: {Name} {@UserId} {@Request}", 
            name, _currentUserService.UserId, request);

        return Task.CompletedTask;
    }
}
```

- Define Request Post-Processors if any
```cs
public class GenericRequestPostProcessor<TRequest, TResponse> : IRequestPostProcessor<TRequest, TResponse>
{
    private readonly TextWriter _writer;

    public GenericRequestPostProcessor(TextWriter writer)
    {
        _writer = writer;
    }

    public Task Process(TRequest request, TResponse response, CancellationToken cancellationToken)
    {
        return _writer.WriteLineAsync("- All Done");
    }
}
```

- Define Request Pipeline Behaviours if any
```cs
public class RequestPerformanceBehaviour<TRequest, TResponse> : IPipelineBehavior<TRequest, TResponse>
{
    // injecting dependencies to the handler via constructor
    private readonly Stopwatch _timer;
    private readonly ILogger<TRequest> _logger;
    private readonly ICurrentUserService _currentUserService;

    public RequestPerformanceBehaviour(ILogger<TRequest> logger, ICurrentUserService currentUserService)
    {
        _timer = new Stopwatch();

        _logger = logger;
        _currentUserService = currentUserService;
    }

    // pipeline behaviour code
    public async Task<TResponse> Handle(TRequest request, CancellationToken cancellationToken, RequestHandlerDelegate<TResponse> next)
    {
        _timer.Start();

        var response = await next();

        _timer.Stop();

        if (_timer.ElapsedMilliseconds > 500)
        {
            var name = typeof(TRequest).Name;

            _logger.LogWarning("Northwind Long Running Request: {Name} ({ElapsedMilliseconds} milliseconds) {@UserId} {@Request}", 
                name, _timer.ElapsedMilliseconds, _currentUserService.UserId, request);
        }

        return response;
    }
}
```
## Links
- https://www.c-sharpcorner.com/article/command-mediator-pattern-in-asp-net-core-using-mediatr2/
- [Request Handler Code in NorthWindTraders](https://github.com/jasontaylordev/NorthwindTraders/blob/master/Src/Application/Products/Commands/CreateProduct/CreateProductCommandHandler.cs)
- [Mediatr Send example in NorthWindTraders](https://github.com/jasontaylordev/NorthwindTraders/blob/master/Src/WebUI/Controllers/ProductsController.cs)
- [Official Mediatr Pipeline wireup to DI container example](https://github.com/jbogard/MediatR/blob/master/samples/MediatR.Examples.AspNetCore/Program.cs)
- [Official Request Pipeline Behaviour class example](https://github.com/jbogard/MediatR/blob/master/samples/MediatR.Examples/GenericPipelineBehavior.cs)
- [Official Request Pre-processor implementation example](https://github.com/jbogard/MediatR/blob/master/samples/MediatR.Examples/GenericRequestPreProcessor.cs)
- [Official Request Post-processor implementation example](https://github.com/jbogard/MediatR/blob/master/samples/MediatR.Examples/GenericRequestPostProcessor.cs)
- [Pipeline, Pre-Processor behaviour classes in  NorthWindTraders](https://github.com/jasontaylordev/NorthwindTraders/tree/master/Src/Application/Common/Behaviours)
- [Wiring Up Pipeline behaviours in NorthWindTraders](https://github.com/jasontaylordev/NorthwindTraders/blob/28e05758d93cb838c68b91d73d8c3f28ceafe42f/Src/Application/DependencyInjection.cs#L15)

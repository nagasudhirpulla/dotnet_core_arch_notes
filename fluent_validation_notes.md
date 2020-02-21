# Fluent Validation
## Purpose
- Fluent Validation library helps us to define both simple and complex model validations easily
- We can remove Data Annotations from View Models / Commands / Requests and decouple validation logic in separate class file
- We can also remove the ModelState.IsValid in all controller methods and instead handle that in a Pipeline behaviour for all requests in common thereby obeying the DRY principle

## Motivation
- Make Model Validation clean by maintaining validations in a separate file and avoid ModelState.IsValid in controller actions.
- Also we can move model validations from UI layer to Application core, i.e., we can support model validation in other platforms like console apps etc.

## Using like ModelState.IsValid
We can replace ModelState.IsValid with fluent validation if we want to strictly replace ModelState.IsValid - https://youtu.be/fAJrVf8f6M4?t=3185

- Add FluentValidation to the services DI container
```cs
services
.AddControllersWithViews()
.AddNewtonsoftJson()
.AddFluentValidation(fv => fv.RegisterValidatorsFromAssemblyContaining<INorthwindDbContext>());
```

- Request / View Model Validation using ```AbstractValidator<TRequest>```
```cs
public class CreateCustomerCommandValidator : AbstractValidator<CreateCustomerCommand>
{
    public CreateCustomerCommandValidator()
    {
        RuleFor(x => x.Id).Length(5).NotEmpty();
        RuleFor(x => x.CompanyName).MaximumLength(40).NotEmpty();
        RuleFor(x => x.Region).MaximumLength(15);
        // ...
    }
}
```

- Create Pipeline behaviour for validating all mediatr requests
```cs
public class RequestValidationBehavior<TRequest, TResponse> : IPipelineBehavior<TRequest, TResponse>
        where TRequest : IRequest<TResponse>
{
    private readonly IEnumerable<IValidator<TRequest>> _validators;

    public RequestValidationBehavior(IEnumerable<IValidator<TRequest>> validators)
    {
        _validators = validators;
    }

    public Task<TResponse> Handle(TRequest request, CancellationToken cancellationToken, RequestHandlerDelegate<TResponse> next)
    {
        var context = new ValidationContext(request);

        var failures = _validators
            .Select(v => v.Validate(context))
            .SelectMany(result => result.Errors)
            .Where(f => f != null)
            .ToList();

        if (failures.Count != 0)
        {
            throw new ValidationException(failures);
        }

        return next();
    }
}
```

- Calling command from controller action
```cs
[HttpPost]
public async Task<IActionResult> Create([FromBody]CreateCustomerCommand command)
{
    await Mediator.Send(command);
    return NoContent();
}
```

- If we did not wire mediatr to intercept all requests and validate them, we can explicitly to validation as shown below
```cs
// ...
var validator = new CreateCustomerCommandValidator();
var validationResult = validator.Validate(createCustCommInstance);
if(!validationResult.IsValid){
  // ... do something if request is not valid
}
// ...
```
## Links
- [Adding FluentValidation to services DI container in NorthWindTraders](https://github.com/jasontaylordev/NorthwindTraders/blob/28e05758d93cb838c68b91d73d8c3f28ceafe42f/Src/WebUI/Startup.cs#L46)
- [Fluent Validation Pipeline Behaviour in NorthWindTraders](https://github.com/jasontaylordev/NorthwindTraders/blob/master/Src/Application/Common/Behaviours/RequestValidationBehavior.cs)
- [Command Validator Example in NorthWindTraders](https://github.com/jasontaylordev/NorthwindTraders/blob/master/Src/Application/Customers/Commands/CreateCustomer/CreateCustomerCommandValidator.cs)
- [Calling the above example command in controller of NorthWindTraders](https://github.com/jasontaylordev/NorthwindTraders/blob/28e05758d93cb838c68b91d73d8c3f28ceafe42f/Src/WebUI/Controllers/CustomersController.cs#L37)

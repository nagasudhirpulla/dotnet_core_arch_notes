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

## Links
- [Adding FluentValidation to services DI container in NorthWindTraders](https://github.com/jasontaylordev/NorthwindTraders/blob/28e05758d93cb838c68b91d73d8c3f28ceafe42f/Src/WebUI/Startup.cs#L46)
- [Fluent Validation Pipeline Behaviour in NorthWindTraders](https://github.com/jasontaylordev/NorthwindTraders/blob/master/Src/Application/Common/Behaviours/RequestValidationBehavior.cs)
- [Command Validator Example in NorthWindTraders](https://github.com/jasontaylordev/NorthwindTraders/blob/master/Src/Application/Customers/Commands/CreateCustomer/CreateCustomerCommandValidator.cs)
- [Calling the above example command in controller of NorthWindTraders](https://github.com/jasontaylordev/NorthwindTraders/blob/28e05758d93cb838c68b91d73d8c3f28ceafe42f/Src/WebUI/Controllers/CustomersController.cs#L37)

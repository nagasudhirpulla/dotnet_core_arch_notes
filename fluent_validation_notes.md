# Fluent Validation
## Purpose
- Fluent Validation library helps us to define both simple and complex model validations easily
- We can remove Data Annotations from View Models / Commands / Requests and decouple validation logic in separate class file
- We can also remove the ModelState.IsValid in all controller methods and instead handle that in a Pipeline behaviour for all requests in common thereby obeying the DRY principle

## Motivation
Make Model Validation clean by maintaining validations in a separate file and avoid ModelState.IsValid in controller actions.



## Links
- [Fluent Validation Pipeline Behaviour in NorthWindTraders](https://github.com/jasontaylordev/NorthwindTraders/blob/master/Src/Application/Common/Behaviours/RequestValidationBehavior.cs)
- [Command Validator Example in NorthWindTraders](https://github.com/jasontaylordev/NorthwindTraders/blob/master/Src/Application/Customers/Commands/CreateCustomer/CreateCustomerCommandValidator.cs)
- [Calling the above example command in controller of NorthWindTraders](https://github.com/jasontaylordev/NorthwindTraders/blob/28e05758d93cb838c68b91d73d8c3f28ceafe42f/Src/WebUI/Controllers/CustomersController.cs#L37)

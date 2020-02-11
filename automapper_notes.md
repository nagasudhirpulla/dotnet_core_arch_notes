Steps - 
https://stackoverflow.com/questions/40275195/how-to-set-up-automapper-in-asp-net-core

Nice article - 
https://code-maze.com/automapper-net-core/

wiring automapper in northwindtraders repo

https://github.com/jasontaylordev/NorthwindTraders/blob/28e05758d93cb838c68b91d73d8c3f28ceafe42f/Src/Application/DependencyInjection.cs#L13
```cs
services.AddAutoMapper(Assembly.GetExecutingAssembly());
```
Mapping is done in individual DTO class definition as shown below - 
https://github.com/jasontaylordev/NorthwindTraders/blob/28e05758d93cb838c68b91d73d8c3f28ceafe42f/Src/Application/Products/Queries/GetProductsFile/ProductRecordDto.cs#L17

# Automapper
Automapper maps properties of 2 classes. Typically this can be used to map Entity and DTO.

## Approach 1
- Add Automapper in ConfigureServices method
```cs
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services.AddAutoMapper(typeof(Startup));
    // ...
}
```

- Create Class that extends **Profile**
```cs
public class MappingProfile : Profile {
    public MappingProfile() {
        // Add as many of these lines as you need to map your objects
        CreateMap<User, UserDto>();
        CreateMap<UserDto, User>();
    }
}
```

- Inject automapper in controller constructor and use in methods
```cs
public class UserController : Controller {

    // Create a field to store the mapper object
    private readonly IMapper _mapper;

    // Assign the object in the constructor for dependency injection
    public UserController(IMapper mapper) {
        _mapper = mapper;
    }

    public async Task<IActionResult> Edit(string id) {
        // ...
        var uDTO = _mapper.Map<UserDto>(user);
        // .... 
    }
}
```
### Links
- https://stackoverflow.com/questions/40275195/how-to-set-up-automapper-in-asp-net-core
- https://code-maze.com/automapper-net-core/

## Approach 2
This approach is taken from NorthwindTraders Repository

- Add Automapper to services DI container
[Dependency Injection](https://github.com/jasontaylordev/NorthwindTraders/blob/28e05758d93cb838c68b91d73d8c3f28ceafe42f/Src/Application/DependencyInjection.cs#L13)
```cs
// ...
services.AddAutoMapper(Assembly.GetExecutingAssembly());
// ...
```

- Create Interface IMapFrom
Mappings can be defined in classes that implement IMapFrom interface
```cs
public interface IMapFrom<T>
{   
    void Mapping(Profile profile) => profile.CreateMap(typeof(T), GetType());
}
```

- Create class MappingProfile that extends Profile
This class will wire all the mappings of the classes that 
```cs
public class MappingProfile : Profile
{
    public MappingProfile()
    {
        ApplyMappingsFromAssembly(Assembly.GetExecutingAssembly());
    }

    private void ApplyMappingsFromAssembly(Assembly assembly)
    {
        var types = assembly.GetExportedTypes()
            .Where(t => t.GetInterfaces().Any(i => 
                i.IsGenericType && i.GetGenericTypeDefinition() == typeof(IMapFrom<>)))
            .ToList();

        foreach (var type in types)
        {
            var instance = Activator.CreateInstance(type);
            var methodInfo = type.GetMethod("Mapping");
            methodInfo?.Invoke(instance, new object[] { this });
        }
    }
}
```
and https://github.com/jasontaylordev/NorthwindTraders/tree/master/Src/Application/Common/Mappings

Mapping is done in individual DTO class definition as shown below - 
https://github.com/jasontaylordev/NorthwindTraders/blob/28e05758d93cb838c68b91d73d8c3f28ceafe42f/Src/Application/Products/Queries/GetProductsFile/ProductRecordDto.cs#L17

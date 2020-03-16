# SimpleEndpoints ![Build Master](https://github.com/dasiths/SimpleEndpoints/workflows/Build%20Master/badge.svg?branch=master) [![NuGet](https://img.shields.io/nuget/v/SimpleEndpoints.svg)](https://www.nuget.org/packages/SimpleEndpoints)
 
 ### A simple, convention-based, endpoint per action pattern implementation for AspNetCore 3.0+

 
<img src="./assets/logo.png" alt="Logo" width="200"/>

## Getting Started

1. Install and reference the Nuget `SimpleEndpoints`

In the NuGet Package Manager Console, type:

```
    Install-Package SimpleEndpoints
```

2. Define your request and response models
```C#
    public class SimpleMessage
    {
        public string Message { get; set; }
    }

    public class SimpleResponse
    {
        public string Message { get; set; }
    }
```
3. Create your endpoint and implement your business logic
```C#
    public class SimpleMessageEndpoint : AsyncGetEndpoint<SimpleMessage, SimpleResponse>
    {
        protected override async Task<ActionResult<SimpleResponse>> HandleAsync(SimpleMessage requestModel, CancellationToken cancellationToken = default)
        {
            return await Task.FromResult(new SimpleResponse()
            {
                Message = "Hello " + requestModel.Message
            });
        }
    }
```
4. In the `ConfigureServices()` method in your `Startup.cs` add the following
```C#
    public void ConfigureServices(IServiceCollection services)
    {
        // Other services go here
		
        services.AddControllers((options) =>
        {
            options.AddEndpointRoutingConvention(); // This is required to translate endpoint names
        });
    }
```

5. Navigate to the url `https://localhost:port_number/simplemessage?message=world` and see the result.

---

## Want more ?

Checkout the [Examples folder](https://github.com/dasiths/SimpleEndpoints/tree/master/src/SimpleEndpoints.Example) for more. Await more examples in the coming weeks.

The Endpoints are automatically inherited from a [`ControllerBase`](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.controllerbase?view=aspnetcore-3.1) and decorated with [`ApiController` attribute](https://www.strathweb.com/2018/02/exploring-the-apicontrollerattribute-and-its-features-for-asp-net-core-mvc-2-1/). You can decorate the endpoint class/action method with the usual (Route, HttpGet, FromQuery etc) attributes to customise and extend the functionality. Endpoints fully support [AspNetCore routing conventions](https://docs.microsoft.com/en-us/aspnet/core/mvc/controllers/routing?view=aspnetcore-3.1).

---

Feel free to contribute and raise issues as you see fit :)

- Creator: Dasith Wijesiriwardena (http://dasith.me)
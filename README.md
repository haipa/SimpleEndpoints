# SimpleEndpoints ![Build Master](https://github.com/dasiths/SimpleEndpoints/workflows/Build%20Master/badge.svg?branch=master) [![NuGet](https://img.shields.io/nuget/v/SimpleEndpoints.svg)](https://www.nuget.org/packages/SimpleEndpoints) [![Downloads](https://img.shields.io/nuget/dt/SimpleEndpoints.svg)](https://www.nuget.org/packages/SimpleEndpoints/)
 
 ### A simple, convention-based, endpoint per action pattern implementation for AspNetCore 3.0+
 
<img src="./assets/logo.png" alt="Logo" width="200"/>

## Motivation

The aim of this pattern is to get away from the bloated god controllers that have a million action methods and so many dependencies. By following the SimpleEndpoints pattern we keep the endpoint scoped to a small feature and lightweight which makes it easier to understand and manage. The aim is not to blur the line between the controllers and the domain layer. You can choose to dispatch the request to the domain from the endpoint or handle it in the endpoint itself. Make an informed choice based to the context.

More about it in the [blog post here](https://dasith.me/2020/03/21/simple-endpoints/).

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
3. Create your endpoint and implement your business logic (You can choose to handle in place or dispatch to a domain layer)
```C#
    public class SimpleMessageEndpoint : AsyncGetEndpoint<SimpleMessage, SimpleResponse>
    {
        public override async Task<ActionResult<SimpleResponse>> HandleAsync(SimpleMessage requestModel, CancellationToken cancellationToken = default)
        {	
	    // Handle in place or dispatch to the domain i.e. return await _someDomainService.HandleAsync(requestModel)
	
            return new SimpleResponse()
            {
                Message = "Hello " + requestModel.Message
            };
        }
    }
```
4. In the `ConfigureServices()` method in your `Startup.cs` add the following
```C#
    public void ConfigureServices(IServiceCollection services)
    {
        // Other services go here
		
        services.AddControllers();
        services.AddSimpleEndpointsRouting(); // This is required to translate endpoint names
    }
```

5. Navigate to the URL `https://localhost:port_number/simplemessage?message=world` and see the result.

---

## Want more ?

Checkout the [Examples folder](https://github.com/dasiths/SimpleEndpoints/tree/master/src/SimpleEndpoints.Example) for more. Await more examples in the coming weeks.

The Endpoints are automatically inherited from a [`ControllerBase`](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.controllerbase?view=aspnetcore-3.1) and decorated with [`ApiController` attribute](https://www.strathweb.com/2018/02/exploring-the-apicontrollerattribute-and-its-features-for-asp-net-core-mvc-2-1/). You can decorate the endpoint class/action method with the usual (Route, HttpGet, FromQuery etc) attributes to customise and extend the functionality. Endpoints fully support [AspNetCore routing conventions](https://docs.microsoft.com/en-us/aspnet/core/mvc/controllers/routing?view=aspnetcore-3.1).

If you really need an endpoint with a custom route, a mix of parameters coming from the Route/Query/Body or need full control over of any aspect then you can do something like this. Each of these class/method attributes works independently of each other and you can pick and choose them as required.

```c#
    [SimpleEndpoint(HttpVerb.Get)] // define the HTTP method being used
    [Route("custom/[endpoint]")] // use your own route template (i.e this results in "custom/mysimple")
    public class MySimpleEndpoint : SimpleEndpointBase // Extend the SimpleEndpointBase class
    {
        // definew your custom signature binding attributes
        [Route("custom-method")]
        public ActionResult<GreetingResponse> MyMethodName([FromQuery] int id, [FromBody] GreetingRequest requestModel) // Mix of binding attributes
        {
            return new GreetingResponse()
            {
                Greeting = $"Hello {requestModel.name} with id {id}"
            };
        }
    }
```

I've had good success with creating a folder structure like below.

![Folder Structure](/assets/simple-ednpoints-folderstructure.png)

You can take this one step further and create a **folder per feature group**, then put each endpoint specific folder inside that if you want as well. I recommend keeping the view models in the same folder as it's easier to find related code when they sit next to each other.

---

Feel free to contribute and raise issues as you see fit :)

- Creator: Dasith Wijesiriwardena (https://dasith.me)

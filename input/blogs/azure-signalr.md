title: Building Responsive Apps, A Guide to SignalR Integration with Azure
created: Friday, November 24, 2023
tags:
  - dotnet
  - signalR
  - azure
image: /images/signalr_azure.jpg
short: Dive into the world of real-time application development as we explore the seamless integration of SignalR with Azure services. Discover how this powerful combination empowers developers to create dynamic, interactive applications with ease. From project setup to implementation, this article guides you through the process of leveraging SignalR on Azure for responsive and engaging user experiences. Uncover the magic of instant communication and collaboration in the cloud.
---
# Introduction

SignalR is an impressive library and service from Microsoft designed for real-time applications.

In this example, we'll explore how the SignalR library can be integrated with Azure's hosted SignalR service. To begin, create a .NET 8 Web API project using the .NET 8 SDK.

When using Azure-hosted SignalR, the workflow should follow these steps:

1. The client connects to the server application to obtain the SignalR endpoint (hosted on Azure) and an access token.
2. The SignalR library connects to the Azure Negotiate endpoint using the provided access token.
3. Once connected, communication between the client and server is enabled.

# Code

Let's dive into the coding process.

First, create a configuration class named `AppSettings` to hold our configuration details:


```csharp
public class AppSettings
{
    public string AzureSignalREndpoint { get; set; }
}
```


Next, define an interface for IHubContextStore:

```csharp
public interface IHubContextStore
{
    public ServiceHubContext ChatHubContext { get; }
}
```

Now, implement the SignalRService class:

```csharp
using AzureSignalR;
using Microsoft.Azure.SignalR.Management;

public class SignalRService(AppSettings configuration, ILoggerFactory loggerFactory) : IHostedService, IHubContextStore
{
    private const string ChatHub = "Chat";
    public ServiceHubContext ChatHubContext { get; private set; }

    async Task IHostedService.StartAsync(CancellationToken cancellationToken)
    {
        using var serviceManager = new ServiceManagerBuilder()
            .WithOptions(o=>o.ConnectionString = configuration.AzureSignalREndpoint)
            .WithLoggerFactory(loggerFactory)
            .BuildServiceManager();
        ChatHubContext = await serviceManager.CreateHubContextAsync(ChatHub, cancellationToken);
    }

    Task IHostedService.StopAsync(CancellationToken cancellationToken)
    {
        return Task.WhenAll(Dispose(ChatHubContext));
    }

    private static Task Dispose(ServiceHubContext hubContext)
    {
        if (hubContext == null)
        {
            return Task.CompletedTask;
        }
        return hubContext.DisposeAsync();
    }
}
```

and finally our Program.cs


```csharp

using AzureSignalR;
using Microsoft.AspNetCore.Mvc;

var builder = WebApplication.CreateBuilder(args);

var config = builder.Configuration.GetSection("AppSettings").Get<AppSettings>();
builder.Services.AddSingleton(config);

builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

builder.Services.AddCors(x => 
    x.AddDefaultPolicy(p=>
        p.AllowAnyHeader().AllowAnyMethod().AllowAnyOrigin()
    )
);

builder.Services.AddSignalR().AddAzureSignalR(options =>
{
    options.ConnectionString = config.AzureSignalREndpoint;
});

builder.Services
    .AddSingleton<SignalRService>()
    .AddHostedService(sp => sp.GetService<SignalRService>())
    .AddSingleton<IHubContextStore>(sp => sp.GetService<SignalRService>());

var app = builder.Build();

app.UseCors();

if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();

app.MapGet("/login", async ([FromServices]IHubContextStore store) =>
{
    var negotiateResponse = await store.ChatHubContext.NegotiateAsync();
    return negotiateResponse;
});

app.UseAzureSignalR(routes =>
{
    routes.MapHub<Chat>("/chat");
});

app.Run();

```


This Program.cs file performs the following key tasks:

1. Configuration Setup: Retrieves the configuration settings from the AppSettings class.
2. Swagger Support: Adds support for Swagger to document and test the API endpoints.
3. CORS Configuration: Enables Cross-Origin Resource Sharing (CORS) to allow communication between different origins.
4. SignalR Registration: Registers SignalR services and configures them to use the Azure SignalR endpoint.
5. SignalR Service Registration: Registers the SignalRService class as a singleton and hosted service, making it available for the application's lifetime.
6. Azure SignalR Usage: Configures the application to use Azure SignalR with specified hub routes.
7. /login Endpoint: Defines an endpoint to negotiate and obtain the SignalR configuration (URL and access token) for the client.
8. Application Run: Starts the application.

This Program.cs file acts as the entry point for the application, orchestrating the setup and configuration of various services, including SignalR integration with Azure.


For the client side, create an HTML file with the following content:

```html
<html>
<head>
    <style>
        #messages {
            list-style: none
        }

        #messages li {
            margin-top: 10px;
        }

        #messages .user {
            background-color: #e1fce8;
            border-radius: 10px;
            padding: 10px;
        }

        #messages .system {
            text-align: right;
            background-color: #e1f0fc;
            border-radius: 10px;
            padding: 10px;
        }
    </style>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/6.0.1/signalr.js"></script>
    <script src="./index.js"></script>
</head>
<body>
    <ul id="messages"></ul>
    <textarea id="text" rows="10" cols="50"></textarea>
    <br>
    <button id="btn">
        Send
    </button>

    <script>
        init();
    </script>
</body>
</html>
```

Create a JavaScript file named index.js with the following content:

```javascript
function ajax(method, url, cb) {
  var xmlhttp = new XMLHttpRequest();

  xmlhttp.onreadystatechange = function () {
    if (xmlhttp.readyState == XMLHttpRequest.DONE) {
      if (xmlhttp.status == 200) {
        return cb(null, JSON.parse(xmlhttp.responseText));
      }
      cb(xmlhttp.status + " error");
    }
  };

  xmlhttp.open(method, url, true);
  xmlhttp.send();
}

function addtext(message, cls) {
  var li = document.createElement("li");
  var text = document.createTextNode(message);
  li.appendChild(text);
  li.classList = [cls];
  document.getElementById("messages").appendChild(li);
}

function bindConnectionMessage(connection) {
  var messageCallback = function (name, message) {
    if (!message) return;
    addtext(message, "system");
  };  
  connection.on("echo", messageCallback);
}


var connection;


function connect(url, token) {
  connection = new signalR.HubConnectionBuilder()
    .withUrl(url, { accessTokenFactory: () => token })
    .build();

  bindConnectionMessage(connection);
  connection
    .start()
    .catch(function (error) {
      console.error(error.message);
    });
}

ajax("GET", "http://localhost:5065/login", function (err, data) {
  connect(data.url, data.accessToken);
});

function init() {
  document.getElementById("btn").addEventListener("click", async (e) => {
    try {
      var message = document.getElementById("text").value;
      addtext(message, "user");
      document.getElementById("text").value = "";
      await connection.invoke("echo", "user", message);
    } catch (err) {
      console.error(err);
    }
  });
}

```

This simple client is sufficient for testing our Azure SignalR instance. When both the server and client are running, the result should resemble the image below:



# Conclusion:

In conclusion, leveraging SignalR in conjunction with Azure's hosted SignalR service empowers developers to create dynamic, real-time applications with ease. The seamless integration of SignalR into the Azure environment streamlines the process of building responsive and interactive applications that can handle communication between clients and servers in real-time.

Throughout this article, we've covered the essential steps, from project setup and configuration to the implementation of SignalR services. The code examples provided demonstrate how to connect clients to the Azure SignalR service, facilitating instant communication and collaboration.

By adopting this approach, developers can harness the power of SignalR to enhance user experiences, making applications more engaging and responsive. Whether you're building a chat application, live updates for a dashboard, or any other real-time feature, SignalR on Azure proves to be a robust solution.

As you embark on your real-time application development journey, keep in mind the flexibility and scalability that Azure SignalR brings to the table. Consider exploring additional Azure services to complement your real-time solutions and meet the specific needs of your application.

In summary, SignalR on Azure opens up a realm of possibilities for developers looking to create modern, responsive applications that thrive in the world of real-time communication.




sample code can be found here: https://github.com/dejandjenic/AzureSignalR


title: Building Scalable Serverless Applications, Hosting .NET Minimal APIs with Google Cloud Functions
created: Monday, July 31, 2023
tags:
  - dotnet
  - gcp
  - cloud function
  - minimal api
  - .net7
short: This article showcases how to host a .NET Minimal API with Google Cloud Functions, offering a scalable, serverless solution. It outlines the benefits of both technologies, such as serverless architecture and simplified code. The step-by-step guide covers project setup, local testing, deployment, and the addition of Swagger for interactive API documentation. The combination empowers developers to build efficient applications that automatically scale with incoming traffic.
---
# Building Scalable Serverless Applications: Hosting .NET Minimal APIs with Google Cloud Functions

## Introduction:

In today's fast-paced digital landscape, developers are constantly seeking efficient ways to build and deploy applications that are scalable, cost-effective, and require minimal maintenance. The combination of .NET Minimal APIs and Google Cloud Functions presents a compelling solution to meet these demands. By leveraging the simplicity and performance of .NET Minimal APIs with the serverless architecture of Google Cloud Functions, developers can create robust applications that scale seamlessly with incoming traffic while enjoying the benefits of a pay-as-you-go billing model.

In this article, we will delve into the process of hosting a .NET Minimal API on Google Cloud Functions, unlocking the power of serverless computing for your .NET applications. We will walk you through the necessary steps to set up and deploy your .NET Minimal API, as well as explore the unique advantages that both technologies offer. Whether you are a seasoned .NET developer or just starting your journey into serverless computing, this guide will equip you with the knowledge and tools needed to build modern, scalable, and responsive applications with ease.

Let's get started on this exciting journey of hosting a .NET Minimal API with Google Cloud Functions and unlock the full potential of serverless computing for your projects!

Hosting a .NET Minimal API with Google Cloud Functions can be a powerful combination to create scalable, serverless applications. In this article, we'll walk through the steps to set up and deploy a .NET Minimal API on Google Cloud Functions. Before we begin, ensure you have basic knowledge of .NET Minimal APIs, Google Cloud Functions, and the Google Cloud Platform (GCP).



## Benefits of Google Cloud Functions:
Serverless Architecture: Google Cloud Functions is a serverless computing platform, which means you don't need to worry about managing servers or infrastructure. Google takes care of server provisioning, scaling, and maintenance, allowing you to focus on writing code and building applications.

Auto-scaling: Google Cloud Functions automatically scales your application in response to incoming traffic. It provisions and scales resources as needed, ensuring optimal performance during peak times while saving costs during periods of low or no traffic.

Pay-per-Use Billing: With serverless computing, you only pay for the actual compute resources used during the execution of your functions. This fine-grained billing model allows you to optimize costs and eliminate the need to pay for idle resources.

Easy Integration: Google Cloud Functions integrates seamlessly with other Google Cloud services, enabling you to build powerful and interconnected applications. You can easily trigger functions based on events from Cloud Storage, Pub/Sub, Firestore, HTTP requests, and more.

Fast Deployment: Deploying functions is quick and straightforward. Google Cloud Functions abstracts away the complexity of infrastructure setup and configuration, allowing you to deploy and update functions with ease.

Global Reach: Google Cloud Functions provides the option to deploy functions across multiple regions, ensuring low-latency access to your applications for users around the world.

## Benefits of .NET Minimal APIs:
Simplified Code: .NET Minimal APIs offer a simplified and more concise syntax compared to traditional ASP.NET Web APIs. This leads to cleaner and more readable code, making it easier for developers to understand and maintain the application.

Improved Performance: The lightweight nature of .NET Minimal APIs results in faster startup times and reduced memory consumption. This is especially beneficial for serverless architectures where fast startup is crucial to handle incoming requests efficiently.

Built-in Dependency Injection: .NET Minimal APIs come with built-in support for dependency injection, making it easier to manage and inject services into your application components. This promotes better code organization and testability.

Rapid Development: With a minimalist approach, .NET Minimal APIs eliminate boilerplate code, allowing developers to focus on building the core business logic of their application without unnecessary distractions.

Versatility: .NET Minimal APIs are versatile and can be hosted on various platforms, including traditional web servers, containerized environments, and, as demonstrated in this article, serverless platforms like Google Cloud Functions.

Cross-platform Compatibility: .NET Core provide cross-platform support, enabling developers to build and deploy .NET Minimal APIs on Windows, macOS, and Linux systems.

By combining the benefits of Google Cloud Functions and .NET Minimal APIs, developers can create highly scalable, cost-efficient, and easily maintainable serverless applications that respond quickly to user requests and adapt to varying workloads. Whether you're building microservices, APIs, or event-driven applications, this combination empowers developers to focus on application logic rather than infrastructure management.

## 1. Prerequisites
Before you start, make sure you have the following prerequisites in place:

A Google Cloud Platform (GCP) account.
The Google Cloud SDK (gcloud) installed on your local machine.
.NET 7 SDK or later installed on your local machine.
Basic knowledge of C# and .NET Minimal APIs.

##  2. Create a .NET Minimal API
First, let's create a simple .NET Minimal API project:

Open a terminal or command prompt and run the following command to install project template

```bash
dotnet new install Google.Cloud.Functions.Templates
```

Open a terminal or command prompt and run the following command to create a new .NET Minimal API project:
```bash
dotnet new gcf-http -n MyMinimalApiProject
```

Navigate to the project directory:
```bash
cd MyMinimalApiProject
```

Open the project in your preferred code editor.

In the Function.cs file, you'll find a GCP cloud function template.

```csharp
using Google.Cloud.Functions.Framework;
using Microsoft.AspNetCore.Http;
using System.Threading.Tasks;

namespace MyMinimalApiProject;

public class Function : IHttpFunction
{
    /// <summary>
    /// Logic for your function goes here.
    /// </summary>
    /// <param name="context">The HTTP context, containing the request and the response.</param>
    /// <returns>A task representing the asynchronous operation.</returns>
    public async Task HandleAsync(HttpContext context)
    {
        await context.Response.WriteAsync("Hello, Functions Framework.");
    }
}

```

Employing Swagger proves highly beneficial as it facilitates seamless integration with your API and offers a user-friendly UI tool for testing purposes. With Swagger, developers can effortlessly interact with your API, making it easier to explore and validate its functionalities.

In your preferred code editor, open the MyMinimalApiProject.csproj file and add the necessary packages to include Swagger support in your project:

```xml
<Project Sdk="Microsoft.NET.Sdk">
	<PropertyGroup>
		<OutputType>Exe</OutputType>
		<TargetFramework>net7.0</TargetFramework>
	</PropertyGroup>

	<ItemGroup>
		<PackageReference Include="Microsoft.AspNetCore.OpenApi" Version="7.0.8" />
		<PackageReference Include="Swashbuckle.AspNetCore" Version="6.5.0" />
		<PackageReference Include="Google.Cloud.Functions.Hosting" Version="2.1.0" />
		<None Include="appsettings*.json" CopyToOutputDirectory="PreserveNewest" />
	</ItemGroup>
</Project>

```

Create a new file named MathService.cs with the following content:

```csharp
using System;

namespace MyMinimalApiProject
{
    public interface IMathService
    {
        double Pow(int baseNum, int pow);
        double Sqrt(long baseNum);
    }

    public class MathService : IMathService
    {
        public double Pow(int baseNum, int pow)
        {
            return Math.Pow(baseNum, pow);
        }

        public double Sqrt(long baseNum)
        {
            return Math.Sqrt(baseNum);
        }
    }

}


```

Create a new file named Startup.cs with the following content:

```csharp
public class Startup : FunctionsStartup
{

    public override void Configure(WebHostBuilderContext context, IApplicationBuilder app)
    {
        base.Configure(context, app);

        app.UseSwagger();
        app.UseSwaggerUI();


        app.UseRouting();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapGet("/pow/{baseNum}/{pow}", (IMathService service, int baseNum, int pow) => service.Pow(baseNum, pow))
            .WithName("Pow")
            .WithOpenApi();

            endpoints.MapGet("/sqrt/{baseNum}", (IMathService service, long baseNum) => service.Sqrt(baseNum))
            .WithName("Sqrt")
            .WithOpenApi();
        }
        );
        
    }


    public override void ConfigureServices(WebHostBuilderContext context, IServiceCollection services)
    {
        base.ConfigureServices(context, services);

        services.AddScoped<IMathService, MathService>();

        services.AddEndpointsApiExplorer();
        services.AddSwaggerGen();

    }
}
```

In the Function.cs file, modify it to register the Startup class and respond with a 404 response for unhandled requests:

```csharp
[FunctionsStartup(typeof(Startup))]

public class Function : IHttpFunction
{
    public async Task HandleAsync(HttpContext context)
    {
        context.Response.StatusCode = 404;
        await context.Response.WriteAsync("Notfound");
    }
}
```

With these modifications, your .NET Minimal API project is now equipped with Swagger support, providing interactive documentation for your API. It includes two endpoints for calculating the power and square root of numbers using the IMathService interface. By using Swagger, developers can seamlessly interact with your API, making it easier to explore and test its functionalities. Now, let's proceed to test the API locally and deploy it to Google Cloud Functions to unlock the power of serverless computing for your application.

## 3. Test the .NET Minimal API Locally
Before deploying to Google Cloud Functions, ensure your .NET Minimal API works as expected locally:

Run the following command to build and run the project:
```bash
dotnet run
```
By default, the API will be available at http://localhost:8080.

Use a tool like curl, Postman, or a web browser to test your API endpoints or you can use swagger ui that we added in previous step by navigating to http://localhost:8080/swagger/index.html

## 4. Set Up Google Cloud Functions
Now, let's set up Google Cloud Functions for deployment:

Open a terminal or command prompt.

Authenticate with your Google Cloud account using the following command and follow the instructions on-screen:

```bash
gcloud auth login
```

Set your Google Cloud project as the default project for deployment:
```bash
gcloud config set project YOUR_PROJECT_ID
```
Replace YOUR_PROJECT_ID with the actual ID of your Google Cloud project.

## 5. Deploy the .NET Minimal API to Google Cloud Functions
With everything set up, it's time to deploy the .NET Minimal API to Google Cloud Functions:

Open a terminal or command prompt.

Run the following command to deploy the function:

```bash
gcloud functions deploy myFunctionName --runtime dotnet7 --trigger-http --allow-unauthenticated
```
Replace myFunctionName with a name of your choice.

Wait for the deployment process to finish.

Once deployed, the command will provide you with the URL of the deployed function.

## 6. Test the Deployed .NET Minimal API
Now that your .NET Minimal API is deployed as a Google Cloud Function, test it using the provided URL:

Use a tool like curl, Postman, or a web browser to access the API using the provided URL.

Ensure that the API behaves as expected and returns the desired responses.

Congratulations! You have successfully hosted a .NET Minimal API with Google Cloud Functions. You now have a scalable and serverless solution that automatically scales based on the incoming traffic, making it cost-effective and efficient.

code created for this article is available at https://github.com/dejandjenic/MyMinimalApiProject
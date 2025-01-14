title: Testing with JWT in .NET APIs
created: Tuesday, 14 January 2025
tags:
  - dotnet
  - testing
  - JWT
short: Learn how to create and test a .NET Minimal API with JWT authentication and authorization, including secure endpoints, configuration, and integration testing using the TestJWTLibrary.
image: /images/jwt.png
---

#### Introduction to JWT and Its Use in .NET APIs


**JSON Web Tokens (JWT)** are a compact, URL-safe means of representing claims between two parties. In the context of web APIs, JWT is commonly used for authentication and authorization. When a client logs in, a server generates a JWT containing user information and permissions (claims), which the client includes in subsequent requests.

In .NET APIs, JWT is integrated through middleware for authentication and authorization, allowing developers to enforce access rules based on claims and roles.

<br> 

#### Setting Up a Minimal API with JWT Authentication

Let's start by creating a .NET Minimal API project. This example will include two endpoints:

- /users: Returns a list of users and requires the "admin" claim.
- /me: Returns information about the currently authenticated user and requires only authentication without specific claims.


**Step 1**: Update Project Dependencies
Update the csproj file to include the necessary packages for JWT handling, Swagger for API documentation, and OpenAPI integration.


```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>net8.0</TargetFramework>
        <Nullable>enable</Nullable>
        <ImplicitUsings>enable</ImplicitUsings>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.OpenApi" Version="8.0.11"/>
        <PackageReference Include="Swashbuckle.AspNetCore" Version="6.6.2"/>
        <PackageReference Include="Microsoft.AspNetCore.Authentication.JwtBearer" Version="8.0.11" />
    </ItemGroup>

</Project>

```

**Step 2**: Implement API Logic in Program.cs
Add the following code to your Program.cs file. This configures the endpoints and sets up authentication and authorization.


```csharp
var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
// Learn more about configuring Swagger/OpenAPI at https://aka.ms/aspnetcore/swashbuckle
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

builder.Services.AddAuthentication();
builder.Services.AddAuthorization();

builder.Services.AddAuthentication("Bearer").AddJwtBearer();

var app = builder.Build();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();

app.MapGet("/users", () => new UserInfo[]
    {
        new()
        {
            Name = "me"
        },
        new()
        {
            Name = "you"
        }
    })
    .WithName("users")
    .WithOpenApi()
    .RequireAuthorization(p =>
    {
        p.RequireRole("admin");
    });

app.MapGet("/me", (HttpContext context) => new UserInfo
    {
        Name = context.User.Identity.Name
    })
    .WithName("me")
    .WithOpenApi()
    .RequireAuthorization();

app.Run();

public class UserInfo
{
    public string Name { get; set; }
}


```

**Step 3**: Configure Authentication in appsettings.json
Add the following configuration to appsettings.json to define the JWT authentication settings.


```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "Authentication": {
    "Schemes": {
      "Bearer": {
        "Authority": "https://auth.example.com",
        "ValidAudiences": [
          "https://api.example.com"
        ],
        "ValidIssuer": "https://auth.example.com"
      }
    }
  }
}

```

<br>

**Summary**:
So far, we've created a minimal API with two endpoints secured using JWT authentication and authorization. The /users endpoint requires an "admin" role, while /me only requires the user to be authenticated.





#### Adding Integration Tests with TestJWTLibrary

**Step 1**: Add a Test Project
Create a test project in the solution and reference the API project. For this example, we use xUnit. Add the TestJWTLibrary NuGet package to generate JWT tokens for testing purposes.

**Step 2**: Implement a Test Server
The test server uses WebApplicationFactory to spin up the API for testing with a custom configuration.


```csharp
public class TestServer(TestJWTLibrary.Generator generator) : WebApplicationFactory<Program>
{
    protected override void ConfigureWebHost(IWebHostBuilder builder)
    {
        base.ConfigureWebHost(builder);
        generator.ConfigureAuthentication(builder);
        builder.UseEnvironment("Test");
    }
}
```

**Step 3**: Write Integration Tests
Here are some test examples:



```csharp

public class APITest
{
    TestJWTLibrary.Generator generator = new();
    
    [Fact]
    public async Task AuthorizedRoleTest()
    {
        await using var factory = new TestServer(generator);
        var client = factory.CreateDefaultClient();
        client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", generator.GenerateJwt(additionalClaims:new Claim("role","admin")));
        var response = await client.GetAsync("/users");
        response.StatusCode.Should().Be(HttpStatusCode.OK);
    }
    
    [Fact]
    public async Task ForbbidenRoleTest()
    {
        await using var factory = new TestServer(generator);
        var client = factory.CreateDefaultClient();
        client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", generator.GenerateJwt());
        var response = await client.GetAsync("/users");
        response.StatusCode.Should().Be(HttpStatusCode.Forbidden);
    }
    
    [Fact]
    public async Task UnAuthorizedRoleTest()
    {
        await using var factory = new TestServer(generator);
        var client = factory.CreateDefaultClient();
        var response = await client.GetAsync("/users");
        response.StatusCode.Should().Be(HttpStatusCode.Unauthorized);
    }
    
    [Fact]
    public async Task MeTest()
    {
        await using var factory = new TestServer(generator);
        var client = factory.CreateDefaultClient();
        client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", generator.GenerateJwt());
        var response = await client.GetFromJsonAsync<UserInfo>("/me");
        response.Name.Should().Be("john.doe");
    }
    
    [Fact]
    public async Task MeSpecificNameTest()
    {
        await using var factory = new TestServer(generator);
        var client = factory.CreateDefaultClient();
        client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", generator.GenerateJwt(userId:"test"));
        var response = await client.GetFromJsonAsync<UserInfo>("/me");
        response.Name.Should().Be("test");
    }
    
    [Fact]
    public async Task MeUnAuthorizedTest()
    {
        await using var factory = new TestServer(generator);
        var client = factory.CreateDefaultClient();
        var response = await client.GetAsync("/me");
        response.StatusCode.Should().Be(HttpStatusCode.Unauthorized);
    }
    
    [Fact]
    public async Task UnAuthorizedTest()
    {
        await using var factory = new TestServer(generator);
        var client = factory.CreateDefaultClient();
        var response = await client.GetAsync("/me");
        response.StatusCode.Should().Be(HttpStatusCode.Unauthorized);
    } 
}
```

#### Summary
In this article, we covered how to set up a .NET Minimal API with JWT-based authentication and authorization. We implemented two secure endpoints and wrote integration tests using TestJWTLibrary. This library simplifies creating JWTs for test scenarios, eliminating the need for long-lived tokens or external authentication systems.

Explore the TestJWTLibrary on GitHub: [TestJWTLibrary](https://github.com/dejandjenic/TestJWTLibrary)

Happy coding! 🎉
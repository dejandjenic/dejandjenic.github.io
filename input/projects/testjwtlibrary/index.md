title: TestJWTLibrary
created: Tuesday, 14 January 2025
tags:
  - dotnet
  - testing
  - JWT
short: TestJWTLibrary is a lightweight .NET library designed for generating JWT tokens specifically for testing purposes. It simplifies authentication and authorization testing by allowing developers to create customizable tokens with specific claims, roles, and user identities without relying on external authentication systems
---
### TestJWTLibrary


TestJWTLibrary is a lightweight .NET library designed for generating JWT tokens specifically for testing purposes. It simplifies authentication and authorization testing by allowing developers to create customizable tokens with specific claims, roles, and user identities without relying on external authentication systems


#### Usage

* Install TestJWTLibrary from nuget

inside your tests instantiate generator class

```csharp
TestJWT.Generator generator = new();
```

setup test server 

```csharp
public class TestServer(TestJWT.Generator generator) : WebApplicationFactory<Program>
{
    protected override void ConfigureWebHost(IWebHostBuilder builder)
    {
        base.ConfigureWebHost(builder);
        generator.ConfigureAuthentication(builder);
    }
}
```

create test

```csharp
[Fact]
public async Task AuthorizedTest()
{
    await using var factory = new TestServer(generator);
    var client = factory.CreateDefaultClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", generator.GenerateJwt());
    var response = await client.GetAsync("/users");
    response.StatusCode.Should().Be(HttpStatusCode.OK);
}
```

## Default values

by default generator assumes these value
* issuer="https://auth.example.com"
* audience = "https://api.example.com"

GenerateJwt method by default assumes these settings

* audience = "https://api.example.com"
* userId = "john.doe"
* 60

list of additional claims can be passed to GenerateJWT method

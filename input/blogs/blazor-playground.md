title: Exploring Blazor and Deployment Options
created: Sunday, August 13, 2023
tags:
  - blazor
  - wasm
  - dotnet
  - github
  - docker
short: Exploring Blazor and Deployment Options
pinned: true
image: /images/blazor-docker-github-pages.png
pinOrder: 2
---
### Exploring Blazor and Deployment Options

#### Introduction:
In this article, I'm excited to delve into the world of Blazor, Microsoft's innovative web framework that enables developers to build interactive and dynamic web applications using C# and .NET instead of traditional web languages. In this article, I'll walk you through the process of creating a Blazor application that provides various useful functionalities like base64 encoding/decoding, URL encoding/decoding, JWT decoding, string escaping, HTML encoding/decoding, hash generation, and even GUID generation. As we progress, I'll also touch upon the deployment options, specifically focusing on hosting the application on GitHub Pages and containerizing it using Docker.

#### Solution Overview
To make our exploration practical and hands-on, we'll implement a set of simple yet powerful tools every developer frequently uses. These tools include functionalities such as base64 encoding/decoding, URL encoding/decoding, JWT decoding, string escaping, HTML encoding/decoding, hash generation, and even GUID generation. And we're going to build all of these into a Blazor WebAssembly application.

#### Development
##### Reusable Control: Editor.razor
We start by designing a reusable control, Editor, which provides input and output text fields, action buttons for encoding/decoding, and a swap button to easily switch between input and output. This control simplifies the process of creating pages with different functionalities by eliminating repetitive UI code.


```csharp
@using System.Web

<textarea  rows="15" cols="40" style="width:100%" @bind="ModelObject.Source"></textarea>
<button class="btn btn-primary" @onclick="Action1Handler">@Action1Text</button>
@if (Action2Enabled)
{
    <button class="btn btn-primary" @onclick="Action2Handler">@Action2Text</button>
}
@if (SwapEnabled)
{
    <button class="btn btn-primary" @onclick="Swap"><i class="bi bi-arrow-down-up"></i> Swap</button>
}
<textarea  rows="15" cols="40" style="width:100%" @bind="ModelObject.Destination"></textarea>



@code {

    class Model
    {
        public string Source { get; set; }
        public string Destination { get; set; }
    }

    private Model ModelObject = new();

    [Parameter]
    public string Action1Text { get; set; }
    [Parameter]
    public string Action2Text { get; set; }

    [Parameter]
    public Func<string, string> Action1 { get; set; }

    [Parameter]
    public Func<string, string> Action2 { get; set; }

    [Parameter]
    public bool Action2Enabled { get; set; } = true;
    [Parameter]
    public bool SwapEnabled { get; set; } = true;

    [Parameter]
    public string DefaultText { get; set; }

    private void Action1Handler()
    {
        ModelObject.Destination = Action1(ModelObject.Source);
    }

    private void Action2Handler()
    {
        ModelObject.Destination = Action2(ModelObject.Source);
    }

    private void Swap()
    {
        (ModelObject.Destination, ModelObject.Source) = (ModelObject.Source, ModelObject.Destination);
    }

    protected override void OnParametersSet()
    {
        base.OnParametersSet();
        ModelObject = new Model()
        {
                Source = DefaultText
        };
    }
}

```

##### Base64 Encoding: Base64.razor
In the Base64.razor page, we showcase base64 encoding and decoding. Users can input text, click the "Encode" button to see the base64 encoded result, and click the "Decode" button to revert the encoded text back to its original form.



```csharp
@page "/base64"
@using System.Web

<PageTitleWithH1>Base64 Encode</PageTitleWithH1>

<Editor
    Action1Text="Encode"
    Action2Text="Decode"
    Action1=@(s => Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(s)))
    Action2=@(s => System.Text.Encoding.UTF8.GetString(Convert.FromBase64String(s)))>
</Editor>
```

##### Escape String: Escape.razor
The Escape.razor page illustrates how to escape and unescape strings using regular expressions. Users can input text, click the "Escape" button to escape special characters, and click the "Unescape" button to revert the process.


```csharp
@page "/escape"

<PageTitleWithH1>Escape</PageTitleWithH1>

<Editor
    Action1Text="Escape"
    Action2Text="Unescape"
    Action1=@(s =>  System.Text.RegularExpressions.Regex.Escape(s))
    Action2=@(s => System.Text.RegularExpressions.Regex.Unescape(s))>
</Editor>

```

##### Guid Generation: Guid.razor
In the Guid.razor page, we demonstrate the generation of GUIDs (Globally Unique Identifiers). Users can specify the number of GUIDs they want to generate, and the application will produce a list of randomly generated GUIDs.


```csharp
@page "/guid"

<PageTitleWithH1>Guid</PageTitleWithH1>

<Editor
    Action1Text="Generate"
    Action2Enabled=false
    SwapEnabled=false
    DefaultText="10"
    Action1=@(s =>  
    {
        var count = int.TryParse(s,out var i)?i:10;
        count=Math.Max(1,count);
        count=Math.Min(100,count);
        return string.Join("\r\n",Enumerable.Range(1,count).Select(x=> System.Guid.NewGuid().ToString()));
    })>
</Editor>


```

### Hash Generation: Hash.razor
The Hash.razor page showcases the creation of SHA256 hashes. Users can input text, and the application will display the SHA256 hash of the input text.

```csharp
@page "/hash"
@using System.Security.Cryptography

<PageTitleWithH1>Hash</PageTitleWithH1>

<Editor
    Action1Text="SHA256 hash"
    Action2Enabled=false
    SwapEnabled=false
    Action1=@(s =>  CreateHash(s))>
</Editor>

@code
{
    public static string CreateHash(ReadOnlySpan<char> input)
    {
        var encoding = System.Text.Encoding.UTF8;
        var inputByteCount = encoding.GetByteCount(input);
        using var sha256 = new SHA256Managed();

        Span<byte> bytes = inputByteCount < 1024
            ? stackalloc byte[inputByteCount]
            : new byte[inputByteCount];
        Span<byte> destination = stackalloc byte[sha256.HashSize / 8];

        encoding.GetBytes(input, bytes);

        sha256.TryComputeHash(bytes, destination, out int _bytesWritten);

        return BitConverter.ToString(destination.ToArray()).ToLower().Replace("-","");
    }
}


```

##### HTML Encoding: Html.razor
With the Html.razor page, we demonstrate HTML encoding and decoding. Users can input text, click the "Encode" button to encode special characters, and click the "Decode" button to revert the encoding.


```csharp
@page "/html"
@using System.Web

<PageTitleWithH1>Html Encode</PageTitleWithH1>

<Editor
    Action1Text="Encode"
    Action2Text="Decode"
    Action1=@(s => HttpUtility.HtmlEncode(s))
    Action2=@(s => HttpUtility.HtmlDecode(s))>
</Editor>

```

##### JSON Formatting: Json.razor
The Json.razor page focuses on JSON formatting. Users can input JSON data, click the "Beautify" button to format it with proper indentation, and click the "Uglify" button to remove extra whitespace.


```csharp
@page "/json"
@using System.Web
@using System.Text.Json
@using System.Dynamic

<PageTitleWithH1>Json beautify</PageTitleWithH1>

<Editor
    Action1Text="Beautify"
    Action2Text="Uglify"
    Action1=@(json => {
                       var parsedJson = JsonSerializer.Deserialize<ExpandoObject>(json);
                       var options = new JsonSerializerOptions() { WriteIndented = true };
                       return JsonSerializer.Serialize(parsedJson, options);
            })
    Action2=@(json => {
                          var parsedJson = JsonSerializer.Deserialize<ExpandoObject>(json);
                          var options = new JsonSerializerOptions() { WriteIndented = false };
                          return JsonSerializer.Serialize(parsedJson, options);
            })>
</Editor>
```

##### JWT Parsing: Jwt.razor
In the Jwt.razor page, we explore JSON Web Tokens (JWT) by providing a token for parsing. The application will display the claims contained in the JWT payload.


```csharp
@page "/jwt"
@using System.Text.Json;

<PageTitleWithH1>JWT</PageTitleWithH1>

<Editor Action1Text="Parse"
        Action2Enabled=false
        SwapEnabled=false
        Action1=@(s =>
        {
        try{
            var token = new System.IdentityModel.Tokens.Jwt.JwtSecurityTokenHandler().ReadJwtToken(s);
            var options = new JsonSerializerOptions() { WriteIndented = true };
            return JsonSerializer.Serialize( token.Payload.Claims,options);
        }
        catch(Exception ex){
            return ex.Message;
        }
        })
        Action2=@(s => System.Text.RegularExpressions.Regex.Unescape(s))>
</Editor>

```

##### URL Encoding: Url.razor
Lastly, the Url.razor page demonstrates URL encoding and decoding. Users can input a URL, click the "Encode" button to encode special characters, and click the "Decode" button to revert the encoding.


```csharp
@page "/url"
@using System.Web

<PageTitleWithH1>Url Encode</PageTitleWithH1>

<Editor
    Action1Text="Encode"
    Action2Text="Decode"
    Action1=@(s => HttpUtility.UrlEncode(s))
    Action2=@(s => HttpUtility.UrlDecode(s))>
</Editor>

```


##### Navigation Menu Enhancement
To improve the user experience, we've also enhanced the navigation menu. It now lists each functionality, making it easier for users to jump to the desired page.


```html
<div class="top-row ps-3 navbar navbar-dark">
    <div class="container-fluid">
        <a class="navbar-brand" href="">Blazor Playground</a>
        <button title="Navigation menu" class="navbar-toggler" @onclick="ToggleNavMenu">
            <span class="navbar-toggler-icon"></span>
        </button>
    </div>
</div>

<div class="@NavMenuCssClass nav-scrollable" @onclick="ToggleNavMenu">
    <nav class="flex-column">
        <div class="nav-item px-3">
            <NavLink class="nav-link" href="" Match="NavLinkMatch.All">
                <span class="oi oi-home" aria-hidden="true"></span> Home
            </NavLink>
        </div>
        <div class="nav-item px-3">
            <NavLink class="nav-link" href="html">
                <span class="oi oi-plus" aria-hidden="true"></span> Html encode
            </NavLink>
        </div>
        <div class="nav-item px-3">
            <NavLink class="nav-link" href="url">
                <span class="oi oi-list-rich" aria-hidden="true"></span> Url encode
            </NavLink>
        </div>
        <div class="nav-item px-3">
            <NavLink class="nav-link" href="base64">
                <span class="oi oi-flash" aria-hidden="true"></span> Base64
            </NavLink>
        </div>
        <div class="nav-item px-3">
            <NavLink class="nav-link" href="json">
                <span class="oi oi-fork" aria-hidden="true"></span> Json
            </NavLink>
        </div>
        <div class="nav-item px-3">
            <NavLink class="nav-link" href="hash">
                <span class="oi oi-layers" aria-hidden="true"></span> Hash
            </NavLink>
        </div>
        <div class="nav-item px-3">
            <NavLink class="nav-link" href="guid">
                <span class="oi oi-globe" aria-hidden="true"></span> Guid
            </NavLink>
        </div>
        <div class="nav-item px-3">
            <NavLink class="nav-link" href="escape">
                <span class="oi oi-lock-locked" aria-hidden="true"></span> Escape
            </NavLink>
        </div>
        <div class="nav-item px-3">
            <NavLink class="nav-link" href="jwt">
                <span class="oi oi-key" aria-hidden="true"></span> JWT
            </NavLink>
        </div>
    </nav>
</div>

@code {
    private bool collapseNavMenu = true;

    private string? NavMenuCssClass => collapseNavMenu ? "collapse" : null;

    private void ToggleNavMenu()
    {
        collapseNavMenu = !collapseNavMenu;
    }
}

```

#### Deployment: GitHub Pages and Docker
For deployment, we've covered two options: GitHub Pages and Docker.

##### GitHub Pages Deployment
To host our application on GitHub Pages, we've set up a GitHub Action workflow. This workflow builds the application, modifies the base address for subdirectory hosting, copies the index.html to a 404.html file, and deploys the output to the gh-pages branch. This enables us to access our Blazor application using a public URL.


```
name: Blazor Playground

on:
  push:
    branches:
    - main  # default branch

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v1
    - name: Setup .NET Core
      uses: actions/setup-dotnet@v2
      with:
        dotnet-version: '7.0'
    - name: Publish
      run: dotnet publish -c Release -o release
    - name: Set base
      run: sed -i 's/<base href="\/"/<base href="\/BlazorPlayground\/"/g' release/wwwroot/index.html >> release/wwwroot/index.html
    - name: No Jekyll
      run: touch release/wwwroot/.nojekyll
    - name: copy index.html to 404.html
      run: cp release/wwwroot/index.html release/wwwroot/404.html
    - name: Deploy
      uses: peaceiris/actions-gh-pages@v2.5.0
      env:
        ACTIONS_DEPLOY_KEY: ${{ secrets.ACTIONS_DEPLOY_KEY }}
        PUBLISH_BRANCH: gh-pages  # deploying branch
        PUBLISH_DIR: ./release/wwwroot
```


##### Docker Deployment
Alternatively, we've containerized our Blazor application using Docker. The Dockerfile builds the application, copies the output to an Nginx-based image, and configures Nginx to serve the static files. This containerized application can be deployed to any environment that supports Docker containers.


```bash
FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build
	
WORKDIR /src



COPY ["BlazorPlayground.csproj", "BlazorPlayground.csproj"]

RUN dotnet restore "BlazorPlayground.csproj"



COPY ["/", "/"]
RUN sed -i 's/<base href="\/"/<base href="\/BlazorPlayground\/"/g' release/wwwroot/index.html >> release/wwwroot/index.html

RUN dotnet build "BlazorPlayground.csproj" --no-restore -c Release -o /app



FROM build AS publish
RUN dotnet publish "BlazorPlayground.csproj" --no-restore -c Release -o /app




FROM nginx:alpine AS final
WORKDIR /usr/share/nginx/html

COPY --from=publish /app/wwwroot .
COPY ["nginx.conf", "/etc/nginx/nginx.conf"]

```

```bash
events { }
http {
    include mime.types;

    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}

```

# Conclusion
In this article, we've journeyed through the exciting world of Blazor, building a versatile web application that offers a range of essential tools. We've explored encoding, decoding, hashing, and more, showcasing Blazor's capabilities in a practical and engaging manner. Moreover, we've discussed deployment options, making our application accessible through GitHub Pages and containerization. This hands-on experience with Blazor and deployment strategies equips developers with valuable insights for their future projects.

For the complete code and the deployed version of the application, check out the [GitHub repository](https://github.com/dejandjenic/BlazorPlayground/) and the [live application](https://dejandjenic.github.io/BlazorPlayground/). Happy coding!
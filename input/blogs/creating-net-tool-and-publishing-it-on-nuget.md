title: Creating .net tool and publishing it on nuget
created: Thursday, July 13, 2023
tags:
  - dotnet
  - github
  - nuget
short: This article provides a step-by-step guide to creating a .NET tool and publishing it on NuGet via GitHub. The process involves setting up a Console Application project, modifying the project file, adding code functionality, configuring the GitHub repository, preparing for publishing, and finally publishing the tool on NuGet. Key steps include creating the project, modifying the project file to enable packaging as a .NET tool, adding code to implement desired functionality, configuring the GitHub repository and secrets, setting up a GitHub Action workflow for automatic builds and publishing, and finally using the published tool by installing it globally. By following these steps, developers can easily create their own .NET tools and share them with others in the community.
pinned: true
pinOrder: 1
image: /images/dotnet_tool_nuget.png
---
### Creating .net tool and publishing it on nuget

#### Introduction

.NET tools are valuable for building and deploying reusable components. In this article, we will walk through the process of creating a new project to demonstrate the creation of a .NET tool and publishing it on NuGet. We will cover the steps involved in setting up the project, modifying the project file, adding code functionality, configuring the GitHub repository, preparing for publishing, and finally publishing the tool on NuGet.

#### Prerequisites
To follow along with this tutorial, make sure you have the following:

1. A development environment set up with .NET SDK installed.
1. An active GitHub account.
1. An API key from NuGet.org for publishing packages.
1. Basic knowledge of C# and .NET.

#### Create a Console Application Project

Start by creating a new Console Application project using the .NET SDK. Modify the .csproj file with the following content:

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net7.0</TargetFramework>
    <ImplicitUsings>enable</ImplicitUsings>
    <Nullable>enable</Nullable>
  </PropertyGroup>
</Project>
```

Additionally, create a README.md file and describe what your tool is used for.

#### Modify the Project File

Enhance the project file (csproj) to prepare it for packaging as a .NET tool. Add the following lines within the existing <PropertyGroup>:

```xml
<PackAsTool>true</PackAsTool>
<ToolCommandName>your command name</ToolCommandName>
<PackageOutputPath>./nupkg</PackageOutputPath>
<GeneratePackageOnBuild>true</GeneratePackageOnBuild>
<PackageId>Your package id</PackageId>
<Authors>Your Name</Authors>
<Company>Your Email</Company>
<Product>Your product name</Product>
<PackageReadmeFile>README.md</PackageReadmeFile>
```

#### Add Functionality to the Tool

Open the Program.cs file and add the following code:

```csharp
using System;
using System.Linq;
using System.Xml;

try
{
    if (!args.Any())
    {
        Console.WriteLine("No file specified");
        return;
    }

    XmlDocument doc = new XmlDocument();
    doc.Load(args[0]);

    var coverage = doc.SelectSingleNode("/coverage/@line-rate").Value;
    if (decimal.TryParse(coverage, out var dCoverage))
    {
        var format = args.Length > 1 ? args[1] : "total_coverage={0}";
        Console.WriteLine(string.Format(format, dCoverage * 100));
    }
    else
    {
        Console.WriteLine("Value could not be extracted");
    }
}
catch (Exception ex)
{
    Console.WriteLine($"An error occurred: {ex.Message}");
}

```

The above code tries to extract code coverage from a Cobertura file, multiplies it by 100, and writes a formatted string to the console output.

#### Configure Your GitHub Repository

Now, create a new GitHub repository and push your code to it. Once the repository is set up, follow these steps:

1. Create your own API key on NuGet.org.
1. Go to your GitHub repository settings and add your NuGet API key as a secret.
1. Add the provided script to your repository, which includes a function to retrieve the build version. name file GetBuildVersion.psm1 and store it in build directory relative to your project root
1. Navigate to the Actions tab in your repository and create a new workflow using the provided YAML script.


```bash
Function GetBuildVersion {
    Param (
        [string]$VersionString
    )

    # Process through regex
    $VersionString -match "(?<major>\d+)(\.(?<minor>\d+))?(\.(?<patch>\d+))?(\-(?<pre>[0-9A-Za-z\-\.]+))?(\+(?<build>\d+))?" | Out-Null

    if ($matches -eq $null) {
        return "1.0.0-build"
    }

    # Extract the build metadata
    $BuildRevision = [uint64]$matches['build']
    # Extract the pre-release tag
    $PreReleaseTag = [string]$matches['pre']
    # Extract the patch
    $Patch = [uint64]$matches['patch']
    # Extract the minor
    $Minor = [uint64]$matches['minor']
    # Extract the major
    $Major = [uint64]$matches['major']

    $Version = [string]$Major + '.' + [string]$Minor + '.' + [string]$Patch;
    if ($PreReleaseTag -ne [string]::Empty) {
        $Version = $Version + '-' + $PreReleaseTag
    }

    if ($BuildRevision -ne 0) {
        $Version = $Version + '.' + [string]$BuildRevision
    }

    return $Version
}
```



```yaml

name: .NET

on:
  create:
    branches: 
      - release/**
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3
    
    - name: Setup NuGet
      uses: NuGet/setup-nuget@v1.0.5
    
    - name: Get Build Version
      run: |
        Import-Module .\build\GetBuildVersion.psm1
        Write-Host $Env:GITHUB_REF
        $version = GetBuildVersion -VersionString $Env:GITHUB_REF
        echo "BUILD_VERSION=$version" | Out-File -FilePath $Env:GITHUB_ENV -Encoding utf-8 -Append
      shell: pwsh
      
    - name: Setup .NET
      uses: actions/setup-dotnet@v3
      with:
        dotnet-version: 7.0.x
    - name: Restore dependencies
      run: dotnet restore CodeCoverageExtractor/CodeCoverageExtractor.csproj
    - name: Build
      run: dotnet build --no-restore   -p:Version=$BUILD_VERSION CodeCoverageExtractor/CodeCoverageExtractor.csproj
      
    - name: Publish
      if: startsWith(github.ref, 'refs/heads/release')
      run: nuget push **\*.nupkg -Source 'https://api.nuget.org/v3/index.json' -ApiKey ${{secrets.NUGET_KEY}}
```

#### Publish Your .NET Tool via NuGet

Once the GitHub Action workflow is set up, it will automatically trigger a build whenever you create a branch with a release/ prefix or push changes to the main branch. The build process will generate a .nupkg file in the nupkg directory.

Wait a few minutes for NuGet to verify your package. After verification, you can install and use the tool globally by invoking the following command:

```bash
dotnet tool install -g CodeCoverageExtractor
codecoverageextractor path_to_cobertura_file format

```

Remember to name your new branch from `main` and increase the version for any new releases.

#### Conclusion

In this article, we covered the process of creating a .NET tool, configuring the project file, adding functionality, setting up the GitHub repository, and publishing the tool on NuGet. By following these steps, you can easily create your own .NET tools and share them with the development community. Happy coding!
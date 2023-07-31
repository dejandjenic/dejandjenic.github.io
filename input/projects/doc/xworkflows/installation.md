title: xworkflows
created: Monday, July 31, 2023
tags:
  - xworkflows
short: xworkflows
---
<a href="index.html">Introduction</a><br>
<a href="installation.html">Installation</a><br>
<a href="basic.html">Basic concepts</a><br>
<a href="starting.html">Getting Started</a><br>
<a href="entities.html">Entities</a><br>
<a href="actions.html">Actions</a><br>
<a href="events.html">Events</a><br>
<a href="workflow.html">Workflow</a><br>
<a href="errors.html">Error handling</a><br>
<a href="advanced.html">Advanced usage</a><br>
<a href="examples.html">Examples</a><br>

# Installation
XWorkflows can be easily integrated into your .NET projects using NuGet, a popular package manager for .NET.

## Prerequisites
Before proceeding with the installation, ensure that you have the following prerequisites:

.NET SDK - Make sure you have the latest .NET SDK installed on your development machine.
## Package Installation
To add XWorkflows to your project, follow these steps:

* Using Package Manager Console:
Open the Package Manager Console in Visual Studio (Tools > NuGet Package Manager > Package Manager Console) and run the following command:

```bash
Install-Package XWorkflows
```
* Using .NET CLI:
Alternatively, you can use the .NET CLI to install the package. Open a command prompt or terminal and navigate to your project's root directory. Run the following command:

```bash
dotnet add package XWorkflows
```
## Verify Installation
Once the package is installed, you can verify that it was added to your project by checking the packages.config file for a reference to XWorkflows.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="XWorkflows" version="x.y.z" targetFramework="netx.y" />
</packages>
```

## Compatibility
XWorkflows is compatible with .NET Standard 2.0 and later versions, ensuring that it can be used in a wide range of .NET applications.

## Update Package
To update XWorkflows to the latest version, use one of the following commands:

Package Manager Console:
```bash
Update-Package XWorkflows
```

.NET CLI:
```bash
dotnet add package XWorkflows --version x.y.z
```
Make sure to replace x.y.z with the desired version number.
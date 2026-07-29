# Vicgital.Core.Configuration

Shared Core configuration library for Vicgital services. Provides a consistent way to build application configuration (JSON files, environment variables, Azure App Configuration).

## Features

- **`ConfigurationBuilder`** — static helpers for building an `IConfiguration` from:
  - `appsettings.json` + `appsettings.{ASPNETCORE_ENVIRONMENT}.json` + environment variables
  - The above plus Azure App Configuration, via a connection string or an endpoint + `TokenCredential`
  - Defaults to the `dev` environment if `ASPNETCORE_ENVIRONMENT` is not set

## Requirements

- .NET 10.0

## Installation

This package is published to the Vicgital GitHub Packages feed. A `nuget.config` pointing at that feed is included in this repo:

```xml
<packageSources>
  <add key="github" value="https://nuget.pkg.github.com/vicgital/index.json" />
</packageSources>
```


```
dotnet add package Vicgital.Core.Configuration
```

## Usage

### Build configuration

```csharp
using Vicgital.Core.Configuration;

// appsettings.json + appsettings.{ASPNETCORE_ENVIRONMENT}.json + environment variables
var config = ConfigurationBuilder.BuildConfiguration();

// ...or including Azure App Configuration
var config = ConfigurationBuilder.BuildAzureAppConfiguration(connectionString);
```

## Project structure

```
src/Vicgital.Core.Configuration/
├── ConfigurationBuilder.cs               # IConfiguration builders (JSON, env vars, Azure App Configuration)
```


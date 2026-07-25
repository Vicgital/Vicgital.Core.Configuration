# Vicgital.Core.Configuration

Shared Core configuration library for Vicgital services. Provides a consistent way to build application configuration (JSON files, environment variables, Azure App Configuration) and expose it through dependency injection, along with a simple SQL Server connection helper.

## Features

- **`ConfigurationBuilder`** — static helpers for building an `IConfiguration` from:
  - `appsettings.json` + `appsettings.{ASPNETCORE_ENVIRONMENT}.json` + environment variables
  - The above plus Azure App Configuration, via a connection string or an endpoint + `TokenCredential`
  - Defaults to the `dev` environment if `ASPNETCORE_ENVIRONMENT` is not set
- **`IAppConfiguration` / `AppConfiguration`** — strongly-typed configuration value access (`string`, `string` with default, `int` with default) over an injected `IConfiguration`
- **`IDatabaseConnection` / `DatabaseConnection`** — resolves a `DbConnectionString` setting via `IAppConfiguration` and opens a `Microsoft.Data.SqlClient` connection
- **`ServiceCollectionExtensions`** — `AddAppConfiguration` and `AddDatabaseConnection` extension methods for wiring everything into an `IServiceCollection`

## Requirements

- .NET 10.0

## Installation

This package is published to the Vicgital GitHub Packages feed. A `nuget.config` pointing at that feed is included in this repo:

```xml
<packageSources>
  <add key="github" value="https://nuget.pkg.github.com/vicgital/index.json" />
</packageSources>
```

Set the `GIT_PACKAGES_READ_ONLY_PAT` environment variable to a GitHub PAT with `read:packages` scope before restoring, then:

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

### Register with dependency injection

```csharp
using Microsoft.Extensions.DependencyInjection;
using Vicgital.Core.Configuration;
using Vicgital.Core.Configuration.Extensions;
using Vicgital.Core.Configuration.Services;

var config = ConfigurationBuilder.BuildConfiguration();

var services = new ServiceCollection();
services.AddAppConfiguration(config);
services.AddDatabaseConnection(); // requires a "DbConnectionString" setting

var provider = services.BuildServiceProvider();
var appConfig = provider.GetRequiredService<IAppConfiguration>();

var setting = appConfig.GetValue("Setting1");
var withDefault = appConfig.GetValue("Setting1", "fallback");
var intSetting = appConfig.GetValue("IntSetting", 42);
```



## Project structure

```
src/Vicgital.Core.Configuration/
├── ConfigurationBuilder.cs               # IConfiguration builders (JSON, env vars, Azure App Configuration)
├── Database/
│   ├── IDatabaseConnection.cs
│   └── DatabaseConnection.cs             # SQL Server connection via IAppConfiguration
├── Extensions/
│   └── ServiceCollectionExtensions.cs    # AddAppConfiguration, AddDatabaseConnection
└── Services/
    ├── IAppConfiguration.cs
    └── AppConfiguration.cs               # Typed configuration value access
```


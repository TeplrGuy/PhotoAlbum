# Configuration & Externalized Settings Inventory

PhotoAlbum configuration comes from the standard ASP.NET Core JSON files, launch profiles, container settings, and Bicep infrastructure parameters. Development and production environments are represented, and the administrator password is expected from an external configuration source rather than checked-in settings.

## Configuration Sources

| Source | Type | Path/Location | Notes |
|---|---|---|---|
| Base application settings | JSON | `PhotoAlbum/appsettings.json` | Connection string, upload settings, logging, admin username, allowed hosts |
| Development settings | JSON | `PhotoAlbum/appsettings.Development.json` | Detailed errors and increased development logging |
| Launch profiles | JSON | `PhotoAlbum/Properties/launchSettings.json` | Local HTTP and HTTPS URLs; sets Development environment |
| Container build | Dockerfile | `Dockerfile` | .NET 9 runtime/build images and port 8080 |
| Deployment definition | Azure Developer CLI YAML | `azure.yaml` | Connects the web service, container app, Bicep infrastructure, and deployment hooks |
| Infrastructure parameters | Bicep | `infra/main.bicep` | Container App settings, SQL connection string, managed identity, and resource sizing |
| Test overrides | C# test setup | `PhotoAlbum.Tests/Unit/Services/PhotoServiceTests.cs` | Uses EF Core InMemory and temporary upload paths |

No external configuration repository or Key Vault reference was identified.

## Build Profiles

| Profile | Activation | Purpose | Key Dependencies/Plugins |
|---|---|---|---|
| Debug | Default .NET/MSBuild configuration; selectable with `-c Debug` | Development builds | None conditionally added |
| Release | Selectable with `-c Release`; used by Docker build and publish | Optimized deployment build | None conditionally added |

No conditional compilation symbols or package profiles were identified.

## Runtime Profiles

| Profile | Activation Method | Config Files | Key Overrides |
|---|---|---|---|
| Development | `ASPNETCORE_ENVIRONMENT=Development` in local launch profiles | `appsettings.json`, `appsettings.Development.json` | Detailed errors and verbose framework logging |
| Production | `ASPNETCORE_ENVIRONMENT=Production` in Container App infrastructure | `appsettings.json` plus environment variables | SQL connection string supplied through environment; deployment uses container port 8080 |
| Test setup | In-memory configuration in test code; `IsTestEnvironment` can suppress startup migrations | `appsettings.json` plus test code | InMemory EF Core provider and temporary file path in service tests |

## Properties Inventory

### PhotoAlbum

| Property Key | Default | Profiles | Source |
|---|---|---|---|
| `ConnectionStrings:DefaultConnection` | LocalDB connection string for `PhotoAlbumDb` | Production overrides with Azure SQL connection string | `appsettings.json`; production Bicep environment |
| `FileUpload:MaxFileSizeBytes` | `10485760` | Base | `appsettings.json`; service fallback also uses 10485760 |
| `FileUpload:AllowedMimeTypes` | `image/jpeg`, `image/png`, `image/gif`, `image/webp` | Base | `appsettings.json`; service fallback |
| `FileUpload:MaxFilesPerUpload` | `10` | Base | `appsettings.json` |
| `FileUpload:UploadPath` | `wwwroot/uploads` | Base; tests may override | `appsettings.json`; service fallback |
| `Logging:LogLevel:Default` | `Information` | Development overrides to `Debug` | `appsettings.json`, `appsettings.Development.json` |
| `Logging:LogLevel:Microsoft.AspNetCore` | `Warning` | Development overrides to `Information` | `appsettings.json`, `appsettings.Development.json` |
| `Logging:LogLevel:Microsoft.EntityFrameworkCore` | Not set | Development sets `Information` | `appsettings.Development.json` |
| `DetailedErrors` | Not set | Development sets `true` | `appsettings.Development.json` |
| `Admin:Username` | `admin` | Base; may be overridden externally | `appsettings.json`; application fallback |
| `Admin:Password` | Not set | Must be supplied externally for login | Environment variable or user secrets convention; no deployment binding identified |
| `AllowedHosts` | `*` | Base | `appsettings.json` |
| `IsTestEnvironment` | `false` when absent | Set to `true` to skip migration on startup | Runtime configuration read in `Program.cs` |
| Form multipart body limit | `10485760` bytes | All runtime environments | Hard-coded in `Program.cs` |
| Form value length limit | `10485760` | All runtime environments | Hard-coded in `Program.cs` |
| Form multipart boundary limit | `128` | All runtime environments | Hard-coded in `Program.cs` |
| `ASPNETCORE_ENVIRONMENT` | Framework default when unset | Development locally; Production in deployed container | Launch profiles and Bicep |

## Startup Parameters & Resource Requirements

| Service | JVM/Runtime Options | Memory | Instance Count |
|---|---|---|---|
| PhotoAlbum container | `dotnet PhotoAlbum.dll`; listens on container port 8080 | 0.5 CPU and 1 GiB configured | Minimum 1; maximum 3 replicas |
| PhotoAlbum local launch | `dotnet run` through launch profile | Not specified | 1 local process |

No explicit .NET GC, heap, or runtime tuning parameters were identified.

## Startup Dependency Chain

1. The application creates the local uploads directory during startup.
2. Outside test mode, it applies EF Core migrations before accepting requests; successful database connectivity is therefore required for startup.
3. The application configures middleware and begins serving requests.

No health-check wait mechanism or readiness probe is configured in the inspected application and infrastructure sources.

## Secrets & Sensitive Configuration

| Secret Reference | Type | Storage (masked) |
|---|---|---|
| `Admin:Password` | Administrator credential | Not present in checked-in app settings; expected from external configuration, with no deployed binding identified |
| Azure SQL administrator credential | Provisioning credential | Constructed by Bicep; actual value omitted. The application connection string uses Active Directory Default instead |
| `ConnectionStrings:DefaultConnection` in production | Database connection | Supplied as a Container App environment setting; uses Active Directory Default and contains no database password |

### Secrets Provisioning Workflow

The application reads its administrator password from configuration, but the repository does not define a production secret store or bind the setting into the Container App. The infrastructure declares a system-assigned managed identity and configures the SQL connection string for Active Directory Default; SQL authorization provisioning is not evident in the inspected Bicep. No Key Vault, Vault, or cloud secret-manager reference was found. The SQL administrator credential is generated during infrastructure provisioning and is not used in the application's configured connection string.

## Feature Flags

| Flag Name | Default | Controlled By |
|---|---|---|
| `IsTestEnvironment` | `false` | Application configuration; suppresses startup migrations when enabled |

No feature-flag framework or rollout toggles were identified.

## Framework & Runtime Versions

| Component | Version | Source |
|---|---|---|
| .NET target framework | 9.0 | `PhotoAlbum/PhotoAlbum.csproj`, `PhotoAlbum.Tests/PhotoAlbum.Tests.csproj` |
| ASP.NET Core shared framework | 9.0 | Web SDK target framework; Docker runtime image |
| Entity Framework Core Design | 9.0.9 | `PhotoAlbum/PhotoAlbum.csproj` |
| Entity Framework Core SQL Server | 9.0.9 | `PhotoAlbum/PhotoAlbum.csproj` |
| Entity Framework Core InMemory | 9.0.9 | `PhotoAlbum.Tests/PhotoAlbum.Tests.csproj` |
| SixLabors.ImageSharp | 3.1.11 | `PhotoAlbum/PhotoAlbum.csproj` |
| .NET SDK container image | 9.0 | `Dockerfile` |
| .NET ASP.NET runtime container image | 9.0 | `Dockerfile` |
| EF Core migration tooling | Project package 9.0.9 | Project dependency and migration source |

# Configuration & Externalized Settings Inventory

Configuration spans JSON settings, local launch profiles, framework-default environment/command-line providers, container packaging and two Azure provisioning paths. Sensitive values are masked; declared cloud bindings are distinguished from settings consumed by application code.

## Configuration Sources

| Source | Type | Path/Location | Notes |
|---|---|---|---|
| Base settings | Runtime JSON | `PhotoAlbum/appsettings.json:1-26` | Connection, upload, logging, admin username and hosts. |
| Development settings | Runtime JSON | `PhotoAlbum/appsettings.Development.json:1-10` | Detailed errors and logging overrides. No Production JSON found. |
| Default builder providers | JSON, user secrets, environment, command line | `PhotoAlbum/Program.cs:6`, `PhotoAlbum/PhotoAlbum.csproj:7` | Standard CreateBuilder defaults; no custom provider setup. Environment `__` maps to `:`. User-secrets values were not read. |
| Local launch profiles | Development launch settings | `PhotoAlbum/Properties/launchSettings.json:3-21` | HTTP/HTTPS launch URLs and Development environment; not deployment settings. |
| Legacy XML | XML connection strings | `PhotoAlbum/Web.config:1-6` | Duplicates LocalDB declaration; no XML configuration provider is added, so it is not the connection source used by Program. |
| Container packaging | Dockerfile | `Dockerfile:1-19` | Runtime/SDK images, workdir and entrypoint. |
| azd service and hooks | YAML | `azure.yaml:3-40` | Bicep infrastructure and per-OS postprovision/predeploy hooks. |
| Infrastructure | Bicep and compiled ARM JSON | `infra/main.bicep:3-14,119-179`; `infra/main.json:1-10` | Azure resource definitions; JSON is generated, not a separate application settings source. |
| Provision inputs | Parameter JSON / azd environment | `infra/main.parameters.json:4-13` | Environment name, location and image substitution. |
| Imperative setup/deploy | Shell variables and CLI arguments | `azure-setup.sh:12-21,210-220`; `deploy-to-azure.sh:12-30,157-170` | Separate Azure CLI path; script sources `.env`, application does not load dotenv. |
| Deployment environment example | `.env` template | `.env.example:1-6` | Resource-group placeholder only; setup generates more resource identifiers. |
| Test settings | In-memory configuration | `PhotoAlbum.Tests/Unit/Services/PhotoServiceTests.cs:35-46` | Fixture-local upload settings, independent from runtime builder. |

No external configuration repository, Azure App Configuration, Key Vault provider, Vault or AWS secret-store binding is evidenced by these sources.

## Build Profiles

| Profile | Activation | Purpose | Key Dependencies/Plugins |
|---|---|---|---|
| Debug | Solution default / `-c Debug` | Development compilation; Any CPU, x64 and x86 solution mappings | Same declared packages; no conditional package groups (`PhotoAlbum.sln:17-49`, `PhotoAlbum/PhotoAlbum.csproj:3-17`). |
| Release | `-c Release`; Docker build/publish | Container packaging | Same packages; publish sets `UseAppHost=false` (`Dockerfile:11-14`). |
| Application MSBuild settings | Unconditional | net9.0, nullable and implicit usings | Web SDK; design tooling is private (`PhotoAlbum/PhotoAlbum.csproj:1-16`). |
| Test project | Selecting test project | Non-packable test assembly, global Xunit using | Test SDK, runner, coverage, integration host and InMemory provider (`PhotoAlbum.Tests/PhotoAlbum.Tests.csproj:3-24`). |

No repository `global.json`, central package-management props or custom conditional compilation symbols were found. Solution architecture mappings do not themselves establish architecture-specific package selection.

## Runtime Profiles

| Profile | Activation Method | Config Files | Key Overrides |
|---|---|---|---|
| Development | Local `http` / `https` launch profile | Base plus Development JSON | Detailed errors, verbose logging, development middleware branch (`PhotoAlbum/Properties/launchSettings.json:9-20`, `PhotoAlbum/appsettings.Development.json:2-7`, `PhotoAlbum/Program.cs:64`). |
| Production | Framework default when environment unset; explicitly injected by cloud artifacts | Base plus environment variables; no Production JSON | SQL connection, unused blob settings; HSTS and exception handler (`infra/main.bicep:141-158`, `PhotoAlbum/Program.cs:64-68`). |
| Test fixture / bypass | Unit tests construct services directly; separate `IsTestEnvironment` configuration flag can bypass host migrations | Fixture in-memory settings | InMemory provider and test-specific file path; not an automatically selected Test runtime profile (`PhotoAlbum.Tests/Unit/Services/PhotoServiceTests.cs:24-52`, `PhotoAlbum/Program.cs:45-47`). |

ASP.NET Core selects one environment name, not composable Spring-style profiles. Standard application configuration precedence is command-line over environment over Development user secrets over environment-specific JSON over base JSON; no custom ordering is added (`PhotoAlbum/Program.cs:6`).

## Properties Inventory

### PhotoAlbum runtime properties

| Property Key | Default | Profiles | Source |
|---|---|---|---|
| `ConnectionStrings:DefaultConnection` | `[MASKED connection string]`; LocalDB, trusted auth, database PhotoAlbumDb, MARS enabled | Cloud replaces with Azure SQL connection | `PhotoAlbum/appsettings.json:2-4`, `PhotoAlbum/Program.cs:22-23`, `infra/main.bicep:117,143-144`. |
| `FileUpload:MaxFileSizeBytes` | `10485760` (long, bytes) | Base / fixture; environment override possible | `PhotoAlbum/appsettings.json:6`, `PhotoAlbum/Services/PhotoService.cs:35`, `PhotoAlbum.Tests/Unit/Services/PhotoServiceTests.cs:37`. |
| `FileUpload:AllowedMimeTypes:0..3` | `image/jpeg`, `image/png`, `image/gif`, `image/webp` (string array) | Base / fixture | Loaded into a field but not used by content acceptance logic (`PhotoAlbum/appsettings.json:7-12`, `PhotoAlbum/Services/PhotoService.cs:19,36-37,131-141`). |
| `FileUpload:MaxFilesPerUpload` | `10` (integer) | Base | No application consumer/enforcement identified (`PhotoAlbum/appsettings.json:13`, `PhotoAlbum/Pages/Index.cshtml.cs:55-65`). |
| `FileUpload:UploadPath` | `wwwroot/uploads` (string path) | Base / fixture override | Service and serving page consume it; startup separately hardcodes default directory (`PhotoAlbum/appsettings.json:14`, `PhotoAlbum/Services/PhotoService.cs:34`, `PhotoAlbum/Pages/PhotoFile.cshtml.cs:29`, `PhotoAlbum/Program.cs:39`). |
| `Logging:LogLevel:Default` | `Information` | Development: `Debug` | `PhotoAlbum/appsettings.json:18`, `PhotoAlbum/appsettings.Development.json:5`. |
| `Logging:LogLevel:Microsoft.AspNetCore` | `Warning` | Development: `Information` | `PhotoAlbum/appsettings.json:19`, `PhotoAlbum/appsettings.Development.json:6`. |
| `Logging:LogLevel:Microsoft.EntityFrameworkCore` | No explicit base entry | Development: `Information` | `PhotoAlbum/appsettings.Development.json:7`. |
| `DetailedErrors` | No explicit base entry | Development: `true` (bool) | `PhotoAlbum/appsettings.Development.json:2`. |
| `Admin:Username` | `admin` (string) | All; external override possible | `PhotoAlbum/appsettings.json:22-24`, `PhotoAlbum/Pages/Login.cshtml.cs:42`. |
| `Admin:Password` | Unset; any supplied value `[MASKED]` | External/user-secrets configuration required | No password stored in base JSON or deployment bindings (`PhotoAlbum/Pages/Login.cshtml.cs:43-49`, `infra/main.bicep:141-158`). |
| `AllowedHosts` | `*` (string) | Base | `PhotoAlbum/appsettings.json:25`. |
| `IsTestEnvironment` | `false` when absent (bool) | Explicit caller/configuration only | `PhotoAlbum/Program.cs:45-47`. |
| `AzureStorageBlob:Endpoint` | Unset locally; storage primary blob endpoint in deployment | Cloud injected through `AzureStorageBlob__Endpoint` | No runtime storage consumer identified (`infra/main.bicep:147-149`, `deploy-to-azure.sh:164`, `PhotoAlbum/Services/PhotoService.cs:147-159`). |
| `AzureStorageBlob:ContainerName` | Unset locally; `photos` in deployment | Cloud injected through `AzureStorageBlob__ContainerName` | Not used by local storage implementation (`infra/main.bicep:151-153`, `deploy-to-azure.sh:165`). |

The legacy XML `connectionStrings/add[name=DefaultConnection]` contains a masked equivalent LocalDB connection, but is not registered as an application configuration provider (`PhotoAlbum/Web.config:3-5`, `PhotoAlbum/Program.cs:6,22-23`). See [business-workflows.md](business-workflows.md) for validation behavior rather than interpreting the upload settings as guaranteed rules.

### Deployment inputs and generated identifiers

| Property Key | Default | Profiles | Source |
|---|---|---|---|
| `environmentName` / `AZURE_ENV_NAME` | Required; 1–64 characters | azd | `infra/main.bicep:5-8`, `infra/main.parameters.json:5-6`. |
| `location` / `AZURE_LOCATION` | Resource-group location in Bicep; azd substitution | azd | `infra/main.bicep:10-11`, `infra/main.parameters.json:8-9`. |
| `webImageName` / `SERVICE_WEB_IMAGE_NAME` | `mcr.microsoft.com/azuredocs/containerapps-helloworld:latest` placeholder | Initial provision; replaced for app deployment | `infra/main.bicep:13-14`, `infra/main.parameters.json:11-12`. |
| `RESOURCE_GROUP` | Blank in example; generated in setup; deployment argument overrides `.env` | Imperative scripts | `PhotoAlbum` does not consume this key (`.env.example:6`, `azure-setup.sh:13-14`, `deploy-to-azure.sh:12-30`). |
| `SQL_DATABASE_NAME`, `SQL_SERVER_NAME`, `STORAGE_ACCOUNT_NAME`, `ACR_NAME`, `ACR_LOGIN_SERVER`, `ACA_NAME` | Setup-generated identifiers; SQL database PhotoAlbumDb | Script-generated `.env` | Deployment queries resource group independently (`azure-setup.sh:17-21,213-219`, `deploy-to-azure.sh:42-46`). |
| `AZURE_RESOURCE_GROUP`, `AZURE_CONTAINER_REGISTRY_ENDPOINT`, `AZURE_CONTAINER_REGISTRY_NAME`, `AZURE_CONTAINERAPP_NAME` | Deployment outputs | azd hooks | Output-to-hook resource lookup (`infra/main.bicep:224-227`, `infra/hooks/postprovision.sh:8-10`, `infra/hooks/predeploy.sh:8-10`). |
| `AZURE_CONTAINERAPP_FQDN`, `AZURE_CONTAINERAPP_URL`, `AZURE_SQL_SERVER_NAME`, `AZURE_SQL_SERVER_FQDN`, `AZURE_SQL_DATABASE_NAME`, `AZURE_STORAGE_ACCOUNT_NAME`, `AZURE_STORAGE_BLOB_ENDPOINT` | Deployment-derived identifiers/endpoints | azd outputs | `infra/main.bicep:228-234`; not proof of a running deployment. |
| Deployment resource options | ACR Basic/admin enabled; SQL Basic/2 GiB; StorageV2/Standard_LRS; blob public-access capability enabled, container access None; public networks | Bicep path | `infra/main.bicep:44-111`. Script path additionally grants storage Contributor (`azure-setup.sh:157-169`). |
| Deployment tags | `azd-env-name`; Container App additionally `azd-service-name=web` | azd | `infra/main.bicep:18-20,125`. |

## Startup Parameters & Resource Requirements

| Service | JVM/Runtime Options | Memory | Instance Count |
|---|---|---|---|
| Local PhotoAlbum | Launch URLs HTTP 5134 / HTTPS 7055; `ASPNETCORE_ENVIRONMENT=Development` | No memory/CPU limits declared | One process per launch (`PhotoAlbum/Properties/launchSettings.json:8-20`). |
| Container PhotoAlbum | `dotnet PhotoAlbum.dll`, `/app`, port 8080; cloud `ASPNETCORE_ENVIRONMENT=Production` | 1 GiB, 0.5 CPU in cloud configuration | Min 1 / max 3 replicas (`Dockerfile:16-19`, `infra/main.bicep:137-158,173-178`, `deploy-to-azure.sh:167-170`). |
| SQL resource | Managed Basic tier, 2 GiB database cap in Bicep | Database cap is storage, not RAM; CPU/RAM unspecified | One declared database/server (`infra/main.bicep:56-87`). |
| Test project | net9.0 test runner; direct fixture setup | No explicit resource limits | No deployed service (`PhotoAlbum.Tests/PhotoAlbum.Tests.csproj:3-16`). |

No JVM, .NET GC heap limits, custom container probes, persistent volume mounts or explicit application autoscale rule is declared. Multipart body/value limits are hardcoded to 10,485,760 bytes and boundary length 128, independent of upload property overrides (`PhotoAlbum/Program.cs:29-34`). Local minimum hardware requirements are not specified.

## Startup Dependency Chain

1. **azd resource graph:** Log Analytics → managed environment → Container App; storage output supplies endpoint configuration and ACR output controls registry binding. Azure SQL is separately provisioned; directory admin and role assignments depend on the app's identity (`infra/main.bicep:23-40,119-170,186-219`). This is an IaC dependency graph, not a runtime readiness guarantee.
2. **Identity propagation:** postprovision hooks sleep 60 seconds, check AcrPull, optionally wait another 60 seconds, then warn and continue if still absent. No SQL or Blob connectivity probe is performed (`infra/hooks/postprovision.sh:28-60`, `infra/hooks/postprovision.ps1:27-59`).
3. **Image registry preparation:** predeploy hooks configure system-managed-identity ACR pull; shell failure warns and continues. PowerShell uses try/catch without explicit native exit-code checking (`infra/hooks/predeploy.sh:22-30`, `infra/hooks/predeploy.ps1:21-32`). Script deployment independently waits 15 seconds after adding AcrPull (`deploy-to-azure.sh:124-145`).
4. **Application startup:** create the default upload directory → apply database migrations unless `IsTestEnvironment` → configure middleware → begin serving. Migration failure logs and rethrows, preventing startup; no application-level migration retry or readiness wait is configured (`PhotoAlbum/Program.cs:38-61,63-92`).

The imperative setup path also waits 5 seconds after ACR provisioning and 30 seconds for identity propagation (`azure-setup.sh:48-49,84-86`). All waits are fixed sleeps, not evidence of successful service readiness.

## Secrets & Sensitive Configuration

| Secret Reference | Type | Storage (masked) |
|---|---|---|
| `Admin:Password` / `Admin__Password` | Administrator login credential | External configuration `[MASKED]`; absent from committed base settings and cloud env bindings (`PhotoAlbum/Pages/Login.cshtml.cs:43-49`, `infra/main.bicep:141-158`). |
| `ConnectionStrings:DefaultConnection` / `ConnectionStrings__DefaultConnection` | Database connection configuration | JSON/environment `[MASKED]`; deployed form uses directory identity rather than embedding a SQL password (`infra/main.bicep:117,143-144`). |
| `sqlAdminPassword` | Provisioning administrator credential | Bicep expression `[MASKED]`; derived in template rather than a secure external input (`infra/main.bicep:59,68`). |
| `SQL_ADMIN_PASSWORD` | Script-generated SQL administrator credential | Process variable `[MASKED]`; setup prints it in summary (`azure-setup.sh:98-106,197-200`). |
| `ACR_PASSWORD` | Registry credential for local setup login | Retrieved through Azure CLI; pipe to Docker password-stdin `[MASKED]` (`azure-setup.sh:51-62`). |
| `STORAGE_ACCOUNT_KEY` | Storage account key for container creation | Retrieved through Azure CLI; passed to container-create command `[MASKED]` (`azure-setup.sh:171-181`). |
| User-secrets store | Development secret mechanism | Store ID declared in project; values not inspected (`PhotoAlbum/PhotoAlbum.csproj:7`). |

### Secrets Provisioning Workflow

Azure CLI/azd use the operator's existing Azure authentication; no CI credential retrieval workflow was identified. IaC creates a system-assigned app identity, assigns AcrPull and Storage Blob Data Contributor, and configures that identity as SQL directory administrator. The deployed SQL connection uses directory-default authentication; blob endpoint/name are ordinary environment values, not storage credentials (`infra/main.bicep:117,127-158,186-219`).

The imperative path retrieves registry credentials for local Docker login, creates a SQL admin credential, grants identity permissions and retrieves a storage key solely for provisioning the photos container. The generated `.env` contains resource identifiers, not the SQL password; nevertheless the setup summary prints that password (`azure-setup.sh:51-62,98-106,126-132,157-181,197-220`). No Key Vault secret injection, secret encryption workflow or admin-login password binding is declared. Admin authentication therefore requires an additional external password setting; storage identity permissions remain unused by the current local-file runtime.

## Feature Flags

| Flag Name | Default | Controlled By |
|---|---|---|
| `IsTestEnvironment` | false when absent | Configuration; skips startup migrations (`PhotoAlbum/Program.cs:45-47`). |
| Development environment branch | Production unless selected externally | Hosting environment; toggles nondevelopment HSTS/error handling (`PhotoAlbum/Program.cs:64-68`). |
| Conditional ACR registry configuration | Absent for placeholder/non-ACR image | IaC checks whether image contains configured registry hostname (`infra/main.bicep:162-170`). |

No FeatureManagement/remote feature-flag framework, A/B rollout, or local-vs-blob storage toggle is implemented. The above are configuration conditionals, not a feature-flag service.

## Framework & Runtime Versions

| Component | Version | Source |
|---|---|---|
| .NET / ASP.NET Core target | net9.0; exact installed patch not pinned | `PhotoAlbum/PhotoAlbum.csproj:1-6`, `PhotoAlbum.Tests/PhotoAlbum.Tests.csproj:4`. |
| EF SQL Server / Design | 9.0.9 | `PhotoAlbum/PhotoAlbum.csproj:11-15`. |
| ImageSharp | 3.1.11 | `PhotoAlbum/PhotoAlbum.csproj:16`. |
| Test host / EF InMemory | 9.0.9 | `PhotoAlbum.Tests/PhotoAlbum.Tests.csproj:12-13`. |
| Test SDK / xUnit / runner / coverlet | 17.12.0 / 2.9.2 / 2.8.2 / 6.0.2 | `PhotoAlbum.Tests/PhotoAlbum.Tests.csproj:11-16`. |
| Docker runtime / SDK base | `aspnet:9.0` / `sdk:9.0`; no digest pin | `Dockerfile:1,5`. |
| Solution format / Visual Studio metadata | Format 12.00 / 17.0.31903.59 | Not installed MSBuild version (`PhotoAlbum.sln:2-5`). |
| ARM template generator | Bicep 0.38.3.11034 | Generated artifact metadata, not installed CLI verification (`infra/main.json:4-9`). |
| AVM workspace / managed environment / registry | 0.9.1 / 0.8.1 / 0.6.0 | `infra/main.bicep:23,33,45`. |
| AVM SQL / storage / Container App / role assignment | 0.9.1 / 0.14.3 / 0.11.0 / 0.1.1 | `infra/main.bicep:62,90,119,186,197`. |

Azure CLI, azd, Docker engine and installed SDK/MSBuild versions are not pinned by the examined configuration. No build or deployment command was run for this inventory.

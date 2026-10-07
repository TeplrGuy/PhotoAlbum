# PhotoAlbum

## Summary

| Metric | Value |
|--------|-------|
| Total Issues | 19 |
| Mandatory Blockers | 3 |
| Potential Issues | 10 |

## Component Information

| Property | Value |
|----------|-------|
| Language | C# |
| Frameworks | net9.0 |
| Build tools | MSBuild |

## Cloud Readiness Issues

| Issue Name | Criticality | Story Points | Occurrences |
|------------|-------------|--------------|-------------|
| Windows authentication detected | Mandatory | 3 | [1](#Windows_authentication_detected) |
| Local or network IO operations detected | Potential | 3 | [18](#Local_or_network_IO_operations_detected) |
| Local application configuration detected | Potential | 1 | [3](#Local_application_configuration_detected) |
| Connection string is detected | Potential | 3 | [1](#Connection_string_is_detected) |
| SQL database connection detected | Potential | 3 | [1](#SQL_database_connection_detected) |
| Hardcoded sensitive data detected | Optional | 3 | [2](#Hardcoded_sensitive_data_detected) |
| Static content detected | Optional | 3 | [1](#Static_content_detected) |
| Connection strings without configuration builders detected | Optional | 3 | [1](#Connection_strings_without_configuration_builders_detected) |

### Issue Details

<details id="Windows_authentication_detected">
<summary><b>Windows authentication detected</b> — affected files</summary>

- `PhotoAlbum/Web.config`

</details>

<details id="Local_or_network_IO_operations_detected">
<summary><b>Local or network IO operations detected</b> — affected files</summary>

- `PhotoAlbum/Program.cs (line 41)`
- `PhotoAlbum/Program.cs (line 39)`
- `PhotoAlbum/Services/PhotoService.cs (line 149)`
- `PhotoAlbum/Services/PhotoService.cs (line 200)`
- `PhotoAlbum/Services/PhotoService.cs (line 243)`
- `PhotoAlbum/Services/PhotoService.cs (line 147)`
- `PhotoAlbum/Services/PhotoService.cs (line 198)`
- `PhotoAlbum/Services/PhotoService.cs (line 241)`
- `PhotoAlbum/Services/PhotoService.cs (line 157)`
- `PhotoAlbum/Pages/PhotoFile.cshtml.cs (line 60)`
- `PhotoAlbum/Pages/PhotoFile.cshtml.cs (line 66)`
- `PhotoAlbum/Pages/PhotoFile.cshtml.cs (line 58)`
- `PhotoAlbum.Tests/Unit/Services/PhotoServiceTests.cs (line 30)`
- `PhotoAlbum.Tests/Unit/Services/PhotoServiceTests.cs (line 209)`
- `PhotoAlbum.Tests/Unit/Services/PhotoServiceTests.cs (line 123)`
- `PhotoAlbum.Tests/Unit/Services/PhotoServiceTests.cs (line 31)`
- `PhotoAlbum.Tests/Unit/Services/PhotoServiceTests.cs (line 248)`
- `PhotoAlbum.Tests/Unit/Services/PhotoServiceTests.cs (line 246)`

</details>

<details id="Local_application_configuration_detected">
<summary><b>Local application configuration detected</b> — affected files</summary>

- `PhotoAlbum/appsettings.json`
- `PhotoAlbum/appsettings.json`
- `PhotoAlbum/appsettings.json`

</details>

<details id="Connection_string_is_detected">
<summary><b>Connection string is detected</b> — affected files</summary>

- `PhotoAlbum/appsettings.json`

</details>

<details id="SQL_database_connection_detected">
<summary><b>SQL database connection detected</b> — affected files</summary>

- `PhotoAlbum/Web.config`

</details>

<details id="Hardcoded_sensitive_data_detected">
<summary><b>Hardcoded sensitive data detected</b> — affected files</summary>

- `PhotoAlbum/Pages/Login.cshtml.cs (line 55)`
- `PhotoAlbum/Pages/Login.cshtml.cs (line 42)`

</details>

<details id="Static_content_detected">
<summary><b>Static content detected</b> — affected files</summary>

- `PhotoAlbum/PhotoAlbum.csproj`

</details>

<details id="Connection_strings_without_configuration_builders_detected">
<summary><b>Connection strings without configuration builders detected</b> — affected files</summary>

- `PhotoAlbum/Web.config`

</details>

## DotNET Upgrade Issues [View Details](scenarios/dotnet-version-upgrade/assessment.md)

| Issue Category | Criticality | Story Points | Occurrences |
|----------------|-------------|--------------|-------------|
| Binary incompatible for selected .NET version | Mandatory | 1 | [3](#Binary_incompatible_for_selected_NET_version) |
| Project's target framework(s) needs to be changed | Mandatory | 1 | [2](#Project_s_target_framework_s_needs_to_be_changed) |
| NuGet package upgrade is recommended | Potential | 1 | [4](#NuGet_package_upgrade_is_recommended) |
| Behavioral change in selected .NET version | Potential | 1 | [1](#Behavioral_change_in_selected_NET_version) |
| NuGet package is deprecated | Optional | 1 | [1](#NuGet_package_is_deprecated) |

### Issue Details

<details id="Binary_incompatible_for_selected_NET_version">
<summary><b>Binary incompatible for selected .NET version</b> — affected files</summary>

- `PhotoAlbum/Services/PhotoService.cs (line 35, col 8)`
- `PhotoAlbum/Program.cs (line 45, col 0)`

</details>

<details id="Project_s_target_framework_s_needs_to_be_changed">
<summary><b>Project's target framework(s) needs to be changed</b> — affected files</summary>

- `PhotoAlbum/PhotoAlbum.csproj`
- `PhotoAlbum.Tests/PhotoAlbum.Tests.csproj`

</details>

<details id="NuGet_package_upgrade_is_recommended">
<summary><b>NuGet package upgrade is recommended</b> — affected files</summary>

- `PhotoAlbum/PhotoAlbum.csproj`
- `PhotoAlbum.Tests/PhotoAlbum.Tests.csproj`

</details>

<details id="Behavioral_change_in_selected_NET_version">
<summary><b>Behavioral change in selected .NET version</b> — affected files</summary>

- `PhotoAlbum/Program.cs (line 65, col 4)`

</details>

<details id="NuGet_package_is_deprecated">
<summary><b>NuGet package is deprecated</b> — affected files</summary>

- `PhotoAlbum.Tests/PhotoAlbum.Tests.csproj`

</details>

## Security Issues

> **Note:** These issues were generated by AI and may contain inaccuracies or incomplete information. Please review carefully.

| Issue Name | Criticality | Story Points | Files |
|------------|-------------|--------------|-------|
| CWE-820: Missing Synchronization | Potential | 8 | [2](#CWE-820_Missing_Synchronization) |
| CWE-1057: Data Access Operations Outside of Expected Data Manager Component | Potential | 5 | [2](#CWE-1057_Data_Access_Operations_Outside_of_Expected_Data_Manager_Component) |
| CWE-606: Unchecked Input for Loop Condition | Potential | 3 | [1](#CWE-606_Unchecked_Input_for_Loop_Condition) |
| CWE-778: Insufficient Logging | Potential | 3 | [1](#CWE-778_Insufficient_Logging) |
| CWE-259: Use of Hard-coded Password | Optional | 5 | [1](#CWE-259_Use_of_Hard-coded_Password) |
| CWE-798: Use of Hard-coded Credentials | Optional | 5 | [1](#CWE-798_Use_of_Hard-coded_Credentials) |

### Security Issue Details

<details id="CWE-820_Missing_Synchronization">
<summary><b>CWE-820: Missing Synchronization</b> — affected files</summary>

- `PhotoAlbum/Pages/PhotoFile.cshtml.cs`
- `PhotoAlbum/Services/PhotoService.cs`

</details>

<details id="CWE-1057_Data_Access_Operations_Outside_of_Expected_Data_Manager_Component">
<summary><b>CWE-1057: Data Access Operations Outside of Expected Data Manager Component</b> — affected files</summary>

- `PhotoAlbum/Pages/PhotoFile.cshtml.cs`
- `PhotoAlbum/Services/PhotoService.cs`

</details>

<details id="CWE-606_Unchecked_Input_for_Loop_Condition">
<summary><b>CWE-606: Unchecked Input for Loop Condition</b> — affected files</summary>

- `PhotoAlbum/Pages/Index.cshtml.cs`

</details>

<details id="CWE-778_Insufficient_Logging">
<summary><b>CWE-778: Insufficient Logging</b> — affected files</summary>

- `PhotoAlbum/Pages/Login.cshtml.cs`

</details>

<details id="CWE-259_Use_of_Hard-coded_Password">
<summary><b>CWE-259: Use of Hard-coded Password</b> — affected files</summary>

- `infra/main.bicep`

</details>

<details id="CWE-798_Use_of_Hard-coded_Credentials">
<summary><b>CWE-798: Use of Hard-coded Credentials</b> — affected files</summary>

- `infra/main.bicep`

</details>

---

## Codebase Insights

> **Note:** These documents are generated by AI and may contain inaccuracies or incomplete information. Please review carefully.

1. **[Architecture Diagram](facts/architecture-diagram.md)** — Understand the big picture: system layers and component relationships
2. **[Dependency Map](facts/dependency-map.md)** — Know what the project depends on and where the risks are
3. **[API & Service Contracts](facts/api-service-contracts.md)** — See how services communicate and what contracts they expose
4. **[Data Architecture](facts/data-architecture.md)** — Explore data models, storage, and data flow patterns
5. **[Configuration Inventory](facts/configuration-inventory.md)** — Review how the application is configured across environments
6. **[Business Workflows](facts/business-workflows.md)** — Trace end-to-end business processes and domain logic

[Share feedback](https://aka.ms/ghcp-appmod/feedback)

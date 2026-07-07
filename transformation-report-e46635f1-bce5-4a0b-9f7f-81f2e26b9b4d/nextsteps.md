# Next Steps

## Issues resolved
- Transformed DocumentProcessor.Web.csproj to net8.0

## Summary

The solution build completed with no errors across all projects, including the `DocumentProcessor.Web` project. This indicates the transformation to cross-platform .NET was successful.

## Validation Steps

### 1. Restore Dependencies

Run the following command from the solution root to confirm all NuGet packages resolve correctly:

```bash
dotnet restore
```

Review the output for any warnings related to package compatibility or deprecated packages targeting `net4x` frameworks.

### 2. Build the Solution

Perform a full solution build to confirm the error-free state is consistent:

```bash
dotnet build --configuration Release
```

Check the output for any warnings that, while non-blocking, may indicate deprecated APIs or platform-specific code paths that could cause runtime issues.

### 3. Run Unit Tests

If the solution contains test projects, execute them to verify runtime behavior:

```bash
dotnet test --configuration Release --logger trx
```

Review the `.trx` output files for any failed or skipped tests. Skipped tests may indicate platform-conditional logic that was not fully migrated.

### 4. Run the Application Locally

Start the web application and verify it runs correctly on your local machine:

```bash
dotnet run --project src/DocumentProcessor.Web/DocumentProcessor.Web.csproj --configuration Release
```

Confirm the application starts without exceptions and that core functionality behaves as expected.

### 5. Check for Runtime-Only Issues

Some issues do not surface at build time. Manually verify the following areas:

- **File I/O paths**: Ensure no hardcoded Windows-style paths (e.g., `C:\`) remain in configuration files or code.
- **Configuration**: Verify `appsettings.json` and any environment-specific configuration files are present and loading correctly.
- **Authentication and Authorization**: If the application uses Windows Authentication or other Windows-specific providers, confirm these have been replaced or configured appropriately for cross-platform use.
- **Database connections**: If Entity Framework or ADO.NET is used, confirm the connection strings and provider packages are compatible with the target platform.
- **Third-party libraries**: Review any third-party NuGet packages to confirm they support the target .NET version and are not Windows-only.

### 6. Review Compiler Warnings

Even without errors, review all compiler warnings in the build output. Pay particular attention to:

- `CS0618` — Use of obsolete members
- `CS8600`–`CS8625` — Nullable reference type warnings, if nullable context is enabled
- `SYSLIB` prefixed warnings — These indicate usage of APIs that have been replaced or are scheduled for removal

### 7. Target Framework Verification

Open `src/DocumentProcessor.Web/DocumentProcessor.Web.csproj` and confirm the `<TargetFramework>` element targets the intended version, for example:

```xml
<TargetFramework>net8.0</TargetFramework>
```

Ensure all other projects in the solution target a compatible framework version to avoid implicit downgrade warnings.

### 8. Publish the Application

Once local validation is complete, produce a published output to confirm the publish process works correctly:

```bash
dotnet publish src/DocumentProcessor.Web/DocumentProcessor.Web.csproj \
  --configuration Release \
  --output ./publish
```

Review the contents of the `./publish` directory to confirm all expected assemblies, configuration files, and static assets are present.
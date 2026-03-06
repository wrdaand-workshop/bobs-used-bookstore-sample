# Next Steps

## Issues resolved
- Transformed Bookstore.Domain.csproj to net8.0
- Transformed Bookstore.Data.csproj to net8.0
- Transformed Bookstore.Web.csproj to net8.0
- Transformed Bookstore.Cdk.csproj to net8.0
- Transformed Bookstore.Domain.Tests.csproj to net8.0

## Summary

The transformation appears to have completed successfully. No build errors were detected across any of the projects in the solution:

- `Bookstore.Data`
- `Bookstore.Domain.Tests`
- `Bookstore.Cdk`
- `Bookstore.Web`
- `Bookstore.Domain`

The following steps outline how to validate, test, and deploy the migrated solution.

---

## 1. Restore Dependencies

Run a full NuGet package restore to ensure all dependencies are resolved correctly in the new target framework:

```bash
dotnet restore
```

Review the output for any warnings related to package compatibility or deprecated packages that may need to be updated.

---

## 2. Build the Solution

Perform a full solution build to confirm there are no compilation issues:

```bash
dotnet build --configuration Release
```

Ensure the build completes with zero errors and review any warnings, particularly those related to nullable reference types, obsolete APIs, or platform compatibility.

---

## 3. Run Unit Tests

Execute the test project to verify that existing business logic behaves correctly after migration:

```bash
dotnet test app/Bookstore.Domain.Tests/Bookstore.Domain.Tests.csproj --configuration Release --verbosity normal
```

Review the test results carefully. Any failing tests should be investigated to determine whether they indicate a regression introduced during migration or a pre-existing issue.

---

## 4. Validate the Web Application Locally

Run the web application locally to confirm it starts and functions as expected:

```bash
dotnet run --project app/Bookstore.Web/Bookstore.Web.csproj --configuration Release
```

Manually verify the following:
- The application starts without runtime exceptions.
- Key pages and routes load correctly.
- Any database connections (via `Bookstore.Data`) are functioning. Check connection strings in `appsettings.json` or `appsettings.Development.json` to ensure they are correct for the new environment.

---

## 5. Validate the Data Layer

If `Bookstore.Data` uses Entity Framework Core, verify that migrations are up to date:

```bash
dotnet ef migrations list --project app/Bookstore.Data/Bookstore.Data.csproj --startup-project app/Bookstore.Web/Bookstore.Web.csproj
```

If there are pending migrations or if the database schema needs to be updated, apply them:

```bash
dotnet ef database update --project app/Bookstore.Data/Bookstore.Data.csproj --startup-project app/Bookstore.Web/Bookstore.Web.csproj
```

---

## 6. Review the CDK Project

The `Bookstore.Cdk` project likely defines infrastructure. Review its configuration to confirm that any environment-specific values (such as region, account IDs, or resource names) are correctly set for the target deployment environment. Ensure the CDK version referenced is compatible with the current AWS CDK CLI version installed:

```bash
cdk --version
```

Synthesize the CDK stack to validate it produces the expected CloudFormation template:

```bash
cdk synth
```

---

## 7. Deploy the Application

Once all validation steps pass, deploy the infrastructure and application:

1. Deploy the CDK stack to provision or update infrastructure:

```bash
cdk deploy
```

2. Publish the web application for deployment:

```bash
dotnet publish app/Bookstore.Web/Bookstore.Web.csproj --configuration Release --output ./publish
```

Copy the contents of the `./publish` directory to the target hosting environment as appropriate.

---

## 8. Post-Deployment Verification

After deployment, perform the following checks:

- Confirm the application is reachable at the expected URL.
- Review application logs for any runtime errors.
- Re-run any smoke tests or integration tests against the deployed environment.
- Verify database connectivity and data integrity in the production or staging environment.
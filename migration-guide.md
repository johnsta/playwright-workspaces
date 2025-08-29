# Migration Guide from Microsoft Playwright Testing to Playwright Workspaces

This comprehensive guide helps you migrate from Microsoft Playwright Testing to [Playwright Workspaces](https://aka.ms/pww/docs). It lays out common migration scenarios and provides step-by-step instructions for each to minimize friction and accelerate your transition.

> **Note**: If you run into any issues, you can reach us directly at playwrighttesting@microsoft.com. If you have a support plan and need technical help, please create a support request in the Azure Portal.

## Table of Contents
1. [Prerequisites: Common Setup Steps](#prerequisites-common-setup-steps)  
2. [Playwright Test Runner (@playwright/test) with Service Package (`getServiceConfig`)](#playwright-test-runner-playwrighttest-with-service-package-getserviceconfig)  
3. [Playwright Test Runner (@playwright/test) without Service Package using `connectOptions` (not a common scenario)](#playwright-test-runner-playwrighttest-without-service-package-using-connectoptions-not-a-common-scenario)  
4. [Node.js Manual Browser Launch using `connect`](#nodejs-manual-browser-launch-using-connect)  
5. [.NET NUnit Using Base Classes and Service Package](#net-nunit-using-base-classes-and-service-package)  
6. [.NET Manual Browser Launch using `ConnectAsync`](#net-manual-browser-launch-using-connectasync)  
7. [Troubleshooting Migration to Playwright Workspaces](#troubleshooting-migration-to-playwright-workspaces)  
8. [Summary of Major Changes](#summary-of-major-changes)  
9. [References](#references)

## How to Locate Your Microsoft Playwright Testing resources
1. In the Azure Portal, search for **Playwright Testing**.
2. Select **Playwright Testing (classic)**.
3. The blade will list all your Microsoft Playwright Testing resources.

## Prerequisites: Common Setup Steps
Here are the common setup steps you’ll want to complete for a Playwright Workspaces migration:
1. [Create a Playwright Workspaces workspace.](https://aka.ms/pww/docs/create)  
2. [Manage who can access and manage a Playwright Workspaces resource.](https://aka.ms/pww/docs/manage-access)  
3. Configure workspace settings for [optimizing regional latency](https://aka.ms/pww/docs/optimize-regional-latency) (which browser regions to connect to) or [setting up local authentication](https://aka.ms/pww/docs/authentication).  
4. [Update service connections used by your CI pipelines.](https://aka.ms/pww/docs/ci)

## Playwright Test Runner (@playwright/test) with Service Package (`getServiceConfig`)
This is the recommended scenario if you want to use Playwright OSS together with service integration; most users fall into this category.

### Required Steps
1. **Package dependency changes**  
   1. Replace `@azure/microsoft-playwright-testing` with `@azure/playwright`.  
   2. Add `@azure/identity`.  
   3. Update `@playwright/test` to `>= 1.50` (using one of the last three Playwright versions is recommended for best performance).  
2. **Changes in `playwright.service.config.ts`**  
   1. Import `createAzurePlaywrightConfig` from `@azure/playwright` instead of the previous package.  
   2. Import `DefaultAzureCredential` from `@azure/identity`.  
   3. Remove the old service reporter if it’s included.  
   4. Add the new `credential: new DefaultAzureCredential()` parameter in `getServiceConfig` when using the default Entra authentication flow.  
   5. Remove the `runId` parameter if used; you can use `runName` instead if needed.  
3. **Environment variables**  
   1. Update `PLAYWRIGHT_SERVICE_URL` and `PLAYWRIGHT_SERVICE_ACCESS_TOKEN` with their new values.  

### Example Migration PR (before/after changes)
- [Playwright Test Runner JS runner migration comparison](https://github.com/microsoft/playwright-testing-service/compare/users/puagarwa/migrate-playwright-workspace-jsrunner?expand=1)

## Playwright Test Runner (@playwright/test) without Service Package using `connectOptions` (not a common scenario)
This legacy scenario applies if you override [`connectOptions`](https://playwright.dev/docs/api/class-testoptions#test-options-connect-options) in an existing `playwright.config.ts` or add a custom `playwright.service.config.ts`. We recommend adopting the service package for easier integration and more regular updates.

### Required Steps
1. Follow the [Getting Started guide](https://aka.ms/pww/docs/quickstart) for Playwright Workspaces in the Azure portal.

## Node.js Manual Browser Launch using `connect`
Use this scenario if you manage browser launch directly in tests or via custom fixtures and currently call [`browser.connect`](https://playwright.dev/docs/api/class-browsertype#browser-type-connect) with endpoint information.

### Required Steps
1. Modify your existing function that generates `connectOptions` to reflect the changes shown in the example migration PR.  
2. Ensure the `api-version` parameter in the endpoint is updated to `2025-09-01`.  
3. Update `PLAYWRIGHT_SERVICE_URL` and `PLAYWRIGHT_SERVICE_ACCESS_TOKEN` environment variables with the new values.  

### Example Migration PR (before/after changes)
- [JS manual connect migration comparison](ttps://github.com/microsoft/playwright-testing-service/compare/users/puagarwa/migrate-nodejs-manual?expand=1)

## .NET NUnit Using Base Classes and Service Package
Use this scenario if you’re using the service package along with [Playwright NUnit base classes/fixtures](https://playwright.dev/dotnet/docs/test-runners#base-classes-for-playwright) (e.g., `PageTest`).

### Required Steps
1. **Package dependency changes**  
   1. Replace `Azure.Developer.MicrosoftPlaywrightTesting.NUnit` with `Azure.Developer.Playwright.NUnit`. Use latest package version available.
   2. Add `Azure.Identity`.  
   3. Update `Microsoft.Playwright.NUnit` to `>= 1.50` (using one of the last three Playwright versions is recommended for best performance).  
2. **Changes in `PlaywrightServiceSetup.cs`**  
   1. Update `using` statements to reference `Azure.Developer.Playwright.NUnit` instead of the previous package.  
   2. Include `Azure.Developer.Playwright` and `Azure.Identity`.  
   3. Change the base class in `SetUpFixture` from `PlaywrightServiceNUnit` to `PlaywrightServiceBrowserNUnit`.  
   4. Add the new `credential: new DefaultAzureCredential()` parameter in `getServiceConfig` when using the default Entra auth flow.  
   5. Remove the `runId` parameter if used; you can use `runName` instead.  
3. Create `CloudBrowserPageTest.cs` to override the base test class (see example migration PR).  
4. Update your test classes to inherit from `CloudBrowserPageTest`.  
5. Remove the old logger `microsoft-playwright-testing` and `TestRunParameters` from `.runsettings` (the service no longer requires `.runsettings`).  
6. Update `PLAYWRIGHT_SERVICE_URL` and `PLAYWRIGHT_SERVICE_ACCESS_TOKEN` environment variables with the new values.  

### Example Migration PR (before/after changes)
- [.NET NUnit migration comparison](https://github.com/microsoft/playwright-testing-service/compare/users/puagarwa/migrate-dotnet-nunit?expand=1)

## .NET Manual Browser Launch using `ConnectAsync`
Use this scenario if you handle browser launch yourself in .NET (e.g., with custom fixtures) and call [`Browser.ConnectAsync`](https://playwright.dev/dotnet/docs/api/class-browsertype#browser-type-connect).

### Required Steps
1. Modify the function that builds `connectOptions` to match the changes shown in the example migration PR.  
2. Update the `api-version` parameter in the endpoint is updated to `2025-09-01`.  
3. Update `PLAYWRIGHT_SERVICE_URL` and `PLAYWRIGHT_SERVICE_ACCESS_TOKEN` environment variables with the new values.  

### Example Migration PR (before/after changes)
- [.NET manual connect migration comparison](https://github.com/microsoft/playwright-testing-service/compare/users/puagarwa/migrate-dotnet-lib-manual?expand=1)

## Troubleshooting Migration to Playwright Workspaces
See the troubleshooting guide for common problems and resolution tips: [Migration Troubleshooting Guide](https://aka.ms/pww/migration-troubleshooting)

## Summary of Major Changes
- Azure resource provider changed from `Microsoft.AzurePlaywrightService` to `Microsoft.LoadTestService`
- All resource operations via azure portal itself.
- Reporting is not supported in Playwright Workspaces at this time. We recommend [publishing Playwright HTML reports using Azure Storage static website hosting](https://playwright.dev/docs/next/ci-intro#publishing-report-on-the-web) for a low‑cost, scalable solution.  
- Workspace ID format is now a GUID without the `region_` prefix.
- Package name Changes
   - `Azure.Developer.MicrosoftPlaywrightTesting.NUnit` with `Azure.Developer.Playwright.NUnit`
   - `@azure/microsoft-playwright-testing` with `@azure/playwright`
   - getServiceConfig renamed to createAzurePlaywrightConfig
   - timeout renamed to connectTimeout
   - useCloudHostedBrowsers param is removed
   - use runName inplace of runId
- API version updated to `2025-09-01`.

## References
- [Playwright Workspaces documentation](https://aka.ms/pww/docs)

# Troubleshooting Common Migration Issues

This section describes issues you might encounter when migrating from Microsoft Playwright Testing to Playwright Workspaces—and how to resolve them.

If you run into any problems during your migration, [please report them here](https://github.com/Azure/playwright-workspaces/issues) so we can provide guidance and update this guide.

## On this page

1. [ReferenceError: __dirname is not defined in ES module scope](#1-referenceerror-__dirname-is-not-defined-in-es-module-scope)
2. [No dashboard/report link shown at the end of a run](#2-no-dashboardreport-link-shown-at-the-end-of-a-run)
3. ['useCloudHostedBrowsers' does not exist in type 'PlaywrightServiceAdditionalOptions'](#3-usecloudhostedbrowsers-does-not-exist-in-type-playwrightserviceadditionaloptions)
4. ['timeout' does not exist in type 'PlaywrightServiceAdditionalOptions'](#4-timeout-does-not-exist-in-type-playwrightserviceadditionaloptions)
5. [TypeError: (0 , _playwright.getServiceConfig1) is not a function](#5-typeerror-0--_playwrightgetserviceconfig1-is-not-a-function)

## Known issues

### 1. ReferenceError: __dirname is not defined in ES module scope

**Symptom:** When running in an ES module (ESM) context, the run fails with an error similar to:

```
ReferenceError: __dirname is not defined in ES module scope
	at file:///Users/foo/Projects/bar/v5/node_modules/.pnpm/@azure+playwright@1.0.0-beta.2_@playwright+test@1.51.0/node_modules/@azure/playwright/src/core/playwrightServiceUtils.ts:7:20
	at ModuleJob.run (node:internal/modules/esm/module_job:271:25)
	at onImport.tracePromise.__proto__ (node:internal/modules/esm/loader:578:26)
	at requireOrImport (/Users/foo/Projects/bar/v5/node_modules/.pnpm/playwright@1.51.0/node_modules/playwright/lib/transform/transform.js:230:24)
```

**Cause:** `__dirname` is not available in Node.js ESM by default, and an internal dependency referenced it.

**Resolution:** Update to a version of `@azure/playwright` that includes the fix (see tracking issue: https://github.com/Azure/azure-sdk-for-js/issues/35532). For example:
- npm: `npm install -D @azure/playwright@latest`
- pnpm: `pnpm add -D @azure/playwright@latest`
- yarn: `yarn add -D @azure/playwright@latest`


### 2. No dashboard/report link shown at the end of a run

**Symptom:** When a run completes, no dashboard or report URL appears in the output.

**Cause:** Detailed diagnostics reporting isn’t supported in Playwright Workspaces (Azure App Testing) at this time, so a dashboard/report link isn’t emitted.

**Resolution:** In the Azure portal, you can view basic run details—browser count and duration—under your Playwright Workspace > Test runs. For richer, shareable diagnostics (screenshots, traces, logs, etc.), we recommend [publishing Playwright HTML reports using Azure Storage static website hosting](https://playwright.dev/docs/next/ci-intro#publishing-report-on-the-web), a low‑cost and scalable approach.

👉 We're tracking this limitation and collecting feedback:  
[GitHub Discussion: Built-in Reporting for Playwright Workspaces](https://github.com/azure/playwright-workspaces/issues/23)


### 3. 'useCloudHostedBrowsers' does not exist in type 'PlaywrightServiceAdditionalOptions'

**Symptom:** When service config use this additional param, the run fails with an error similar to:

```
'useCloudHostedBrowsers' does not exist in type 'PlaywrightServiceAdditionalOptions'
```

**Cause:** `useCloudHostedBrowsers` is removed from options as its not required anymore.

**Resolution:** Remove `useCloudHostedBrowsers` if used in playwright.service.config.ts

### 4. 'timeout' does not exist in type 'PlaywrightServiceAdditionalOptions'

**Symptom:** When service config use this additional param, the run fails with an error similar to:

```
'timeout' does not exist in type 'PlaywrightServiceAdditionalOptions'
```

**Cause:** `timeout` is remained to connectTimeout from package version 1.0.0

**Resolution:** Replace `timeout` with `connectTimeout` if used in playwright.service.config.ts

### 5. 'TypeError: (0 , _playwright.getServiceConfig1) is not a function'

**Symptom:** method not found in package:

```
'TypeError: (0 , _playwright.getServiceConfig) is not a function'
```

**Cause:** `getServiceConfig` is renamed to createAzurePlaywrightConfig.

**Resolution:** Rename `getServiceConfig` to `createAzurePlaywrightConfig` in playwright.service.config.ts

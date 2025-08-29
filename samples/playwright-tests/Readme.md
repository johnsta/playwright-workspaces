# Using Playwright Test Runner with Playwright Workspaces

This sample demonstrates how to run Playwright tests using cloud-hosted browsers provided by [Playwright Workspace](https://aka.ms/pww/docs).

## How to Use this Sample

1. **Clone this repository and navigate to the sample**

    ```bash
    git clone https://github.com/Azure/playwright-workspaces.git
    cd playwright-workspaces/samples/playwright-tests
    ```

2. **Install dependencies**

    ```bash
    npm install
    ```

3. **Create a Playwright Workspace**  
   Follow the [Getting Started guide](https://aka.ms/pww/docs/quickstart) to create your workspace.

4. **Set the Playwright Service endpoint**

    - **macOS / Linux**:

        ```bash
        export PLAYWRIGHT_SERVICE_URL="wss://<your-service-endpoint>"
        ```

    - **Windows PowerShell**:

        ```powershell
        $env:PLAYWRIGHT_SERVICE_URL = "wss://<your-service-endpoint>"
        ```

5. **Authenticate with Azure**

    ```bash
    az login
    ```

6. **Run the full test suite using the Playwright Workspaces configuration**

    ```bash
    npx playwright test --config=playwright.service.config.ts --workers=20
    ```

    > 💡 Adjust the `--workers` value based on your system resources and workspace quota. Use `--workers=1` when debugging or running locally.

    To run a single test file:
    ```
    npx playwright test tests/example.spec.ts --config=playwright.service.config.ts
    ```

## Need Help?

If you run into issues, open an issue in this repository or refer to the [Playwright Workspaces documentation](https://aka.ms/pww/docs).

---
name: openhandle-quickstart
description: Integrate Openhandle public social data into an application's existing feature or a requested new feature. Use for provider replacement, reusable server client setup, Test verification, MCP connection, or moving an Openhandle integration from Test to Live.
---

# Add Openhandle Social Data

Connect Openhandle to the product behavior the user needs. Use Test first on that application path. Keep all credentials on the server.

## Before you start

1. Read the repository instructions.
2. Inspect the framework, package manager, server boundary, environment-variable pattern, and nearby request code.
3. Identify the requested platform, resource, and product behavior from the prompt and application.
4. Locate existing provider calls and the feature that consumes their data.
5. Check https://openhandle.dev/docs/api-reference for support of the requested platform and operation before implementing them. Explain unsupported requests without inventing an endpoint or substituting another platform.

Choose the scope from the user's request:

- **Replace a provider:** Replace the selected feature's data access. Preserve its existing behavior and application contract. Map Openhandle fields, pagination, and errors to that contract. Report capability gaps before replacing behavior that depends on them.
- **Add a feature:** Connect the requested data to the product's route, action, job, or interface using existing patterns.
- **Configure the client:** Set up reusable server configuration when the user asks only for configuration.
- **Unspecified setup:** Configure the reusable server client first. Ask one focused question about the intended feature before adding product behavior or replacing a provider. Continue independent setup while awaiting the answer.

Use an explicit request and clear application context without asking the user to repeat them. A generic setup request does not select a provider to replace. Do not invent a lookup page, demo route, or test endpoint to make setup appear complete. A successful sample request proves connectivity, not product integration.

## Get a Test credential

Check whether `OPENHANDLE_TEST_KEY` is available to the application runtime without displaying its value or reading secret files. Use the project's existing secret configuration if it uses another variable name.

A missing shell variable does not prove that the application lacks a key. Inspect configuration references and use a presence-only check inside the existing application container or runtime when accessible. Do not dump environment variables. If deployment credentials are inaccessible, report availability as unverified.

If it is unavailable, ask the user to:

1. Create a free account at https://app.openhandle.dev/signup (100 free requests, no card needed), or sign in at https://app.openhandle.dev/signin.
2. Open **API Keys**.
3. Create a **Test** key.
4. Store it as `OPENHANDLE_TEST_KEY`, or the existing configured variable, through the project's normal secret workflow.
5. Confirm when the variable is available.

Never ask the user to paste an API key into chat. Never write a real secret into source control, examples, logs, browser code, mobile code, or test fixtures. Add only a placeholder to an example environment file when the project uses one.

Test keys start with `oh_test_`. They only access deterministic synthetic data. Test requests have a `$0.000` actual charge.

Do not invent a working key or treat a placeholder as a credential. SDK installation does not create a key. Report key creation only when confirmed by the service.

While credentials are pending, complete the authorized implementation and mocked tests using documented fixtures. Do not replace missing credentials with a synthetic success in application code. Stop before authenticated verification and report real Test verification as incomplete. State the remaining credential action and how to run verification after configuration.

## Install the client

Choose the SDK by the runtime that makes the server request. Use that runtime's existing package manager and dependency conventions.

- TypeScript or JavaScript: install `@openhandle/sdk`.
- Go 1.27 or newer: install `github.com/openhandlehq/openhandle-go`.
- Python 3.10 or newer: install `openhandle`.
- PHP 8.2 or newer: install `openhandle/sdk` with Composer.
- Rust 1.85 or newer: install the `openhandle` crate with Cargo. It runs on Tokio.
- Other runtimes: use the REST API at `https://api.openhandle.dev/v1`.

For example, when Laravel owns data access in a Laravel + Next.js monorepo, install `openhandle/sdk` in Laravel. A JavaScript package manager does not make Next.js the integration boundary.

Use the SDK patterns at https://openhandle.dev/docs/sdks.md. Use the operation-specific examples at https://openhandle.dev/docs/api-reference.

## Implement the integration

1. Reuse or create one Openhandle client at the existing server boundary.
2. Read the Test credential through the application's server configuration.
3. For a selected feature, add or replace the smallest data-access function it needs.
4. Connect it to the existing server route, action, loader, or component pattern.
5. Map response data to the application's contract. Preserve response metadata when the application needs it; REST returns accounting metadata in headers.
6. Keep cursor values opaque.
7. Treat missing metrics as `null`, never `0`.
8. Branch on documented error codes, never error messages.
9. Avoid exposing the Openhandle key through client bundles or public responses.

Use documented synthetic identifiers for the selected platform and operation during Test. Keep fixture identifiers in tests or verification inputs, not hardcoded into product behavior. For an Instagram profile request, `northstar_forge_test` is a documented fixture. Do not choose the feature from this example.

Client-only setup ends after configuration and applicable checks. Feature integration must reach the existing consumer or the requested new product behavior.

## Verify

Run the repository's relevant formatter, type checker, and tests.

When a Test credential is available, make one Test request through the implemented application path. For client-only setup, verify the client directly without adding a public endpoint. Verify:

- The request succeeds with synthetic data.
- The environment is `test`.
- The actual charge is `0.000`.
- The feature handles loading, empty, private, not-found, and error states when applicable.
- The key never reaches browser or mobile code.

Use https://openhandle.dev/docs/test-environment/catalog.md to select stable success and error scenarios.

For REST, inspect the Openhandle response headers on the server:

| Response header | Expected Test result |
| --- | --- |
| `Openhandle-Environment` | `test` |
| `Openhandle-Cost` | `0.000`, the actual charge |
| `X-Request-ID` | A nonempty request identifier |
| `Openhandle-Dataset-Version` | A nonempty synthetic dataset version |

These are response headers, not environment selectors to send. The credential selects Test or Live. Use the SDK's documented response metadata for SDK requests. MCP exposes `environment`, `requestId`, `actualCharge`, `liveEquivalentPrice`, and `datasetVersion`. Do not confuse the Live-equivalent price with the actual charge.

Report mocked tests and real Test requests separately. Test verification confirms synthetic behavior; real public data requires the requested Live transition.

## Connect the MCP server when requested

Openhandle runs a remote MCP server over Streamable HTTP. Every public endpoint is one tool, with the same inputs and answer shape as the REST API. Configure it only when the user asks for MCP or for an agent that should call Openhandle directly.

```text
URL: https://api.openhandle.dev/mcp
```

Pick the setup that matches the user's client:

- Claude Code: run `claude mcp add --transport http openhandle https://api.openhandle.dev/mcp`. Then tell the user to type `/mcp`, pick `openhandle`, approve in the browser, and choose Test.
- Cursor: add to `.cursor/mcp.json`: `{"mcpServers": {"openhandle": {"url": "https://api.openhandle.dev/mcp"}}}`. Cursor asks to sign in on first use.
- VS Code: add to `.vscode/mcp.json`: `{"servers": {"openhandle": {"type": "http", "url": "https://api.openhandle.dev/mcp"}}}`. Start the server and approve in the browser.
- Windsurf: add to `mcp_config.json`: `{"mcpServers": {"openhandle": {"serverUrl": "https://api.openhandle.dev/mcp"}}}`.
- Claude or ChatGPT apps: tell the user to add the URL as a custom connector in the client settings and approve in the browser.
- Headless or CI: send a Test API key as the bearer token instead of OAuth: `Authorization: Bearer $OPENHANDLE_TEST_KEY`.

Interactive clients sign in with OAuth 2.1 and choose Test or Live on the approval screen. Approval creates one API key named after the client. The user can disconnect it under Settings, MCP server in the dashboard, which revokes the key and every token.

Then follow this sequence:

1. Read `openhandle://test/quickstart`.
2. Call `find_test_data` for the required operation.
3. Call the matching endpoint tool with the returned input.
4. Verify the result metadata: `environment`, `requestId`, `actualCharge`, `liveEquivalentPrice`, `datasetVersion`.

Do not add an environment parameter, header, or prompt setting. The credential or the approval choice selects the environment. Read https://openhandle.dev/setup-mcp.md for the full client list and https://openhandle.dev/docs/mcp.md for the OAuth details.

## Move to Live only when requested

Do not create or configure a Live key during the initial Test setup.

When the user explicitly requests Live access, ask them to verify the workspace-owner email. Then ask them to create a Live key in the dashboard and store it as `OPENHANDLE_LIVE_KEY` through the normal secret workflow.

Keep the API contract unchanged. Replace the Test credential and synthetic identifiers with the Live credential and real public identifiers. Confirm payment and request-cap settings before billable rollout.

Read https://openhandle.dev/docs/test-environment/test-to-live.md before the change.

## Finish

Summarize the configured runtime, changed files, replaced provider or connected product behavior, and verification results. Distinguish client configuration, feature integration, mocked tests, real Test verification, and Live readiness. State any pending feature decision or credential action. Never include a secret in the summary.

Use https://openhandle.dev/llms.txt as the documentation index when additional details are required.

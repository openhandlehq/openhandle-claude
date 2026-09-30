# Openhandle for Claude Code

Ask Claude Code for public Instagram, TikTok, X, and Reddit data, and for your own Openhandle balance, usage, and request history.

## Install

Run these commands in Claude Code:

```text
/plugin marketplace add openhandlehq/openhandle-claude
/plugin install openhandle@openhandle
```

Then type `/mcp`, pick `openhandle`, and approve in the browser. Pick **Test** or **Live** on that screen:

- **Test** answers every tool with synthetic data. It is never charged.
- **Live** reads real public data and uses your balance.

No account yet? Sign up free at [app.openhandle.dev](https://app.openhandle.dev/signup).

## What you get

| Part | What it does |
| --- | --- |
| `openhandle` MCP server | Every public API endpoint as one tool, plus account tools, at `https://api.openhandle.dev/mcp` |
| `openhandle` skill | Teaches Claude to pick tools, stay in Test or Live, read costs, and explain errors |
| `openhandle-quickstart` skill | Adds Openhandle to your application code with an SDK or the REST API |

Try these prompts:

```text
Find a public Instagram profile in the test data and show me its last three posts.
What did I spend on Openhandle this week, per platform?
Why did my requests fail today?
```

## Account tools

The plugin requests the `mcp read:account` scope. `read:account` lets Claude read your balance, usage, request history, and API keys. The account tools are free. They cannot top up, create keys, or change settings, and they never return key secrets.

Usage, requests, and keys cover the environment you approved. The balance is shared by Test and Live.

## Use an API key instead

Scripts and headless setups can skip the browser. Add the server with a key instead of installing the plugin:

```bash
claude mcp add --transport http openhandle https://api.openhandle.dev/mcp \
  --header "Authorization: Bearer $OPENHANDLE_TEST_KEY"
```

The key prefix decides Test or Live.

## Links

- [MCP server docs](https://openhandle.dev/docs/mcp)
- [Test with MCP](https://openhandle.dev/docs/test-environment/mcp)
- [Pricing](https://openhandle.dev/pricing)

This repository is synchronized from the Openhandle monorepo by `openhandle-sdk-sync[bot]`. Open issues here; changes land through the sync.

## License

MIT. See [LICENSE](LICENSE).

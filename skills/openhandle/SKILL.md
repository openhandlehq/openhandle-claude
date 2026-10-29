---
name: openhandle
description: Use when the user asks for public Instagram, TikTok, X (Twitter), Reddit, or YouTube data (profiles, posts, comments, followers, search, trends, transcripts), or about their Openhandle account (balance, usage, spend, request history, failed requests, API keys). Covers tool choice, cost, freshness, paging, and errors for the openhandle MCP server.
---

# Openhandle

The `openhandle` MCP server gives you two groups of tools.

- **Platform tools** read public social data. Each name starts with the platform, for example `instagram_get_profile`, `tiktok_search_posts`, `twitter_list_followers`, `reddit_profile_get`, and `youtube_post_transcript_get`. Each tool is one REST endpoint with the same inputs and answers.
- **Account tools** read the user's own Openhandle workspace: `account_get_balance`, `account_get_usage`, `account_list_requests`, `account_get_request`, and `account_list_api_keys`. They are free and never touch social platforms.

If no Openhandle tools are visible, tell the user to connect Openhandle and approve in the browser. In Claude Code they run `/mcp` and pick `openhandle`. In ChatGPT they open the Openhandle plugin and connect it.

## Live data

- Platform tools return real public data. Each call uses the user's balance.
- Every result has `environment` in its metadata. If it is `test`, the answer is synthetic and real handles return not found. Tell the user, and tell them to connect again and pick Live.

## Inputs

- Profiles take `identifier` as `@username` or the raw platform ID. Posts take the native post ID, or the shortcode on Instagram. Never pass a URL. Take the handle or ID out of a URL first.
- `freshness` controls cache age: `live`, `24h` (default), `7d`, or `30d`. Older answers cost less and a `30d` cache hit is free. Use `live` only when the user needs data from this minute.
- List tools return a cursor. Pass it back as `cursor` for the next page. Fetch more pages only when the user needs them, because each page is one billed call.

## Answers

- `null` means the platform does not expose the value. It does not mean zero. Say "not available", never "0".
- YouTube lists show rounded counts and relative dates. When `metrics.isApproximate` is `true`, say "about". `createdAt` is exact; `approximateCreatedAt` is an estimated day. For exact numbers, read the single post with `youtube_post_get`.
- Error answers carry a stable `code`, such as `PROFILE_NOT_FOUND` or `PROFILE_PRIVATE`. Explain the code in plain words. These definitive answers are billed. Provider failures and internal errors are free.
- Result metadata has `requestId`, `actualCharge`, and `liveEquivalentPrice`. When the user asks what a call cost, read `actualCharge`.

## Account questions

- "How much credit do I have left?" → `account_get_balance`.
- "What did I spend this week?" or "Which endpoints do I use most?" → `account_get_usage` with `from` and `to` as `YYYY-MM-DD`. Set `groupBy` to `operation`, `platform`, `api_key`, or `source`.
- "Why did my requests fail?" → `account_list_requests` with `status` set to `client_error` or `server_error`, then `account_get_request` for one `requestId`.
- "Which keys are close to their cap?" → `account_list_api_keys`. Secrets are never returned.

If an account tool says the connection lacks the `read:account` scope, tell the user to disconnect Openhandle and connect again. If it says the user may not view the data, the user who approved the connection no longer has that access in the workspace.

## Limits

- The tools read public data only. They cannot post, follow, like, or read private accounts.
- The tools cannot top up the balance, create keys, or change settings. Send the user to https://app.openhandle.dev for that.

# Session Token Counter

A Hermes Desktop plugin: the accumulated token usage of the session you are looking at,
pinned to the right of the status bar.

```
Σ 1.3M tok (37%)
```

Hover it for the exact numbers.

```
input: 1 234 567
output: 45 678
total: 1 280 245
context: 37%
```

## What it shows

| Field | Meaning |
|---|---|
| `input` / `output` / `total` | Tokens the gateway reports for the focused session |
| `context` | Occupancy of the current context window, in percent |

The indicator follows the focused chat: switch tiles or sessions and it re-reads that
session's usage. It needs no polling — it renders `host.state.focusedUsage`, the same
live `UsageStats` the core status bar reads.

## Requirements

- Hermes Desktop from a build whose plugin SDK exposes `host.state.focusedUsage`
  (Hermes **0.20.2** or newer).
- No gateway-side or Python code: this is a Desktop-only, single-file plugin.

## Install

**In-app (recommended).** Settings → Plugins → **Install from Git**, and use the identifier:

```
Vsemagush/session-token-counter
```

**One-click link.** The `repo` probe shows what will be installed before anything is written:

```html
<a href="hermes://plugin/install?repo=Vsemagush/session-token-counter&enable=1">Install in Hermes</a>
```

**By hand.** Copy `desktop/plugin.js` to the app-level plugin root and reload:

```
$HERMES_HOME/desktop-plugins/session-token-counter/plugin.js
```

(`~/.hermes/desktop-plugins/...` by default; the folder name must match the plugin `id`.)
Then run ⌘K → **Reload desktop plugins**. The app hot-reloads the file on every later save.

## Limitations

- `total` is what the gateway reports for the session, not a billing figure. A resumed
  session's counter reflects what the running backend has accumulated, not necessarily
  every token ever spent in that session's history.
- `context` is window occupancy, not spend: the same transcript is re-sent on every model
  call, so accumulated input tokens grow far faster than the context window.
- The tooltip says `-` for any field the backend has not reported yet.

## Development

No build step: the file is plain ESM, loaded uncompiled by the app. Only
`@hermes/plugin-sdk`, `react` and `react/jsx-runtime` resolve as imports.

```bash
node --test tests/plugin.test.mjs  # registration, labels, tooltips, missing values
hermes plugins validate .          # the admission checks the catalog CI runs
```

## License

MIT — see [LICENSE](LICENSE).

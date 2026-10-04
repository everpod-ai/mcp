# Everpod MCP server

Everpod runs an AI agent on a pod: a private, always-on cloud computer of its own. This is Everpod's MCP server, for an agent you already use, such as Claude Code or Codex, to work with your Everpod account for you.

A key lets an agent or an app you trust see your pods and start a new one for you, which you then pay for on everpod.ai. It can't pay, change or cancel a plan, delete anything, or open your agent's control panel.

The server is hosted at `https://everpod.ai/mcp`. There is nothing to install or run. The full reference is at [everpod.ai/docs/api](https://everpod.ai/docs/api).

## Connect

To make a key, open [everpod.ai/account/keys](https://everpod.ai/account/keys) and sign in with your email address and the code we send you. If you have no account yet, signing in makes one. An MCP client connects to `https://everpod.ai/mcp` over HTTP and sends the key as the header `Authorization: Bearer YOUR_KEY`.

### Claude Code

```
claude mcp add --transport http --scope user everpod https://everpod.ai/mcp \
  --header "Authorization: Bearer YOUR_KEY"
```

### Codex

```
export EVERPOD_API_KEY=YOUR_KEY
codex mcp add everpod --url https://everpod.ai/mcp \
  --bearer-token-env-var EVERPOD_API_KEY
```

### Another MCP client

Give it the address and the header. As a JSON entry, in the form Claude Code's `.mcp.json` takes, with the key read from an environment variable so that it is never written into a file you share:

```json
{
  "mcpServers": {
    "everpod": {
      "type": "http",
      "url": "https://everpod.ai/mcp",
      "headers": { "Authorization": "Bearer ${EVERPOD_API_KEY}" }
    }
  }
}
```

Then ask the agent, for example:

```
Start an Everpod pod called Otto and tell me where to pay.
```

## Tools

- `list_pods`: List the pods on the owner's Everpod account, oldest first. Each has its status and the link its owner needs: pay_url while it is awaiting payment, url (the pod's page on everpod.ai) once it is paid.
- `get_pod`: Read one pod by its id: its status and the link its owner needs. This is how to check whether a started pod has been paid for, and whether it is ready.
- `start_pod`: Start a new pod for the owner, under the name they want for their agent. This call charges nothing: the pod stays unpaid until its owner opens pay_url in their browser and pays there. While the account has an unpaid pod, calling again returns that same pod, renamed if the name differs.

## From code

The same three operations are REST routes, with client libraries for [JavaScript](https://github.com/everpod-ai/sdk-node) and [Python](https://github.com/everpod-ai/sdk-python).

## Questions

[support@everpod.ai](mailto:support@everpod.ai)

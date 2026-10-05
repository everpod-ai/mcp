# Everpod MCP server

Everpod is an easy way to get your own always-on, persistent cloud computer for AI agents, working in minutes: with a managed OpenClaw agent on it, or as a developer pod with Claude Code and Codex installed. This is Everpod's MCP server, for an agent you already use, such as Claude Code or Codex, to work with your Everpod account for you and start either kind of pod.

A key lets an agent or an app you trust see your pods and start a new one for you, which you then pay for on everpod.ai. It can't pay, change or cancel a plan, delete anything, open your agent's control panel, or reach a developer pod's machine.

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

### OpenClaw

```
openclaw mcp set everpod '{"url": "https://everpod.ai/mcp",
  "transport": "streamable-http",
  "headers": {"Authorization": "Bearer YOUR_KEY"}}'
```

An OpenClaw agent can run this itself, and has the tools from its next message. Or install the [Everpod skill from ClawHub](https://clawhub.ai/everpod/skills/everpod), and the agent connects itself, starts a new agent or a developer pod for you, and can move itself onto a pod: `openclaw skills install @everpod/everpod`.

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

```
Start an Everpod developer pod called atlas, with alex as my username, and tell me where to pay.
```

For a developer pod, the agent can take you from nothing to a machine you are logged in to; the steps that open the machine are yours. [Get a developer pod with your agent](https://everpod.ai/docs/api#developer-pod) has each step. For an agent that takes skills, the same steps are a skill, `everpod-developer-pod`, from [github.com/everpod-ai/skills](https://github.com/everpod-ai/skills). Claude Code installs it with `claude plugin marketplace add everpod-ai/skills` and then `claude plugin install everpod-developer-pod@everpod`; Codex with `codex plugin marketplace add everpod-ai/skills` and then `codex plugin add everpod-developer-pod@everpod`. For another agent, copy its folder into the agent's skills folder.

## Tools

- `list_pods`: List the pods on the owner's Everpod account, oldest first. Each has its kind, its status and the link its owner needs: pay_url while it is awaiting payment, url (the pod's page on everpod.ai) once it is paid.
- `get_pod`: Read one pod by its id: its status and the link its owner needs. This is how to check whether a started pod has been paid for, whether it is ready, and for a developer pod, which of its owner's steps to connect it are done.
- `start_pod`: Start a new pod for the owner: an OpenClaw pod, or with kind 'developer' a developer pod. This call charges nothing: the pod stays unpaid until its owner opens pay_url in their browser and pays there. While the account has an unpaid pod, calling again returns that same pod, changed to the name and kind now asked for.

## From code

The same three operations are REST routes, with client libraries for [JavaScript](https://github.com/everpod-ai/sdk-node) and [Python](https://github.com/everpod-ai/sdk-python).

## Questions

[support@everpod.ai](mailto:support@everpod.ai)

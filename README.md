# 🪙 erc20-mcp

`erc20-mcp` is the token module of **[ROXI OS](https://github.com/Roxi-OS)**, the Reasoning-Oriented Execution Interface for onchain agents.

It turns token intents (transfer, approve, mint, deploy, read supply or allowance) into **ready-to-sign call data**. It never sends anything itself: each `prepare-*` result is passed to [`metamask-mcp`](https://github.com/Roxi-OS/metamask-mcp), where you review and sign it.

📖 Docs: [roxi-ten.vercel.app/docs/servers/erc20-mcp](https://roxi-ten.vercel.app/docs/servers/erc20-mcp)

---

## ✨ Features

- **Prepare, don't execute** — tools return encoded calls; signing stays in your wallet.
- **Reads and writes** — total supply and allowance reads, plus transfers, approvals, mints and new token deployments.
- **Readable results** — `resolve-*` tools decode raw call results into human-readable amounts.
- **Contract generation** — includes a `generate-erc20-contract` prompt for drafting new tokens.
- **Model-agnostic** — works with Claude, GPT, or any client that speaks the Model Context Protocol (MCP).

## 🧱 How it fits

```
Agent → erc20-mcp → chainlist-mcp → metamask-mcp → Transfer or approval
```

---

## 🚀 Quick start

Requires **Node.js 20+**.

Add it to your MCP client (for example Claude Desktop's `claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "erc20": {
      "command": "npx",
      "args": ["-y", "erc20-mcp"]
    }
  }
}
```

### From source

```bash
git clone https://github.com/Roxi-OS/erc20-mcp-main.git
cd erc20-mcp-main
pnpm install
pnpm build
```

Then point your client at the build:

```json
{
  "mcpServers": {
    "erc20": {
      "command": "node",
      "args": ["/absolute/path/to/erc20-mcp-main/build/index.js"]
    }
  }
}
```

---

## ⚙️ Tools

| Tool | Description |
| --- | --- |
| `prepare-transfer` | Prepare a transfer of ERC-20 tokens. |
| `prepare-approve` | Prepare an approval for someone to spend ERC-20 tokens. |
| `prepare-mint` | Prepare minting ERC-20 tokens. |
| `prepare-deploy-erc20-contract` | Prepare the deployment of a new ERC-20 token. |
| `prepare-total-supply` | Prepare a read of the token's total supply. |
| `resolve-total-supply` | Decode the total supply result. |
| `prepare-allowance` | Prepare a read of an allowance. |
| `resolve-allowance` | Decode the allowance result. |

---

## 🙏 Credits

Based on the original work of xiawpohr, extended for ROXI OS.

## ⚖️ License

MIT

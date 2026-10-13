# 🪙 erc20-mcp

[![npm](https://img.shields.io/npm/v/@roxi-os/erc20-mcp?label=npm&color=c5f24a)](https://www.npmjs.com/package/@roxi-os/erc20-mcp)
[![license](https://img.shields.io/badge/license-MIT-blue)](./LICENSE)
[![docs](https://img.shields.io/badge/docs-roxi--os.si-black)](https://roxi-os.si/docs/servers/erc20-mcp)

**The token module of [ROXI OS](https://roxi-os.si)**, the Reasoning-Oriented Execution Interface.

`erc20-mcp` turns token requests (transfer, approve, mint, deploy, check supply or allowance) into **ready-to-sign call data**. It never sends anything itself: every result is handed to [`metamask-mcp`](https://github.com/ROXI-OS-SI/metamask-mcp), where you review and sign it.

---

## ✨ Features

- **Prepare, don't execute:** tools return encoded calls; signing always happens in your wallet.
- **Human amounts:** pass `"12.5"`, not raw integers; amounts are converted using the token's decimals.
- **Writes and reads:** transfers, approvals, mints and deployments, plus total supply and allowance.
- **Readable results:** `resolve-*` tools decode raw call results into normal amounts.
- **Contract template:** a `generate-erc20-contract` prompt for drafting new tokens.

---

## 🚀 Quick start

Requires **Node.js 20+**, an MCP client such as Claude Desktop, and [`metamask-mcp`](https://github.com/ROXI-OS-SI/metamask-mcp) to send what this server prepares.

```json
{
  "mcpServers": {
    "metamask": { "command": "npx", "args": ["-y", "@roxi-os/metamask-mcp"] },
    "erc20": { "command": "npx", "args": ["-y", "@roxi-os/erc20-mcp"] }
  }
}
```

Restart the client, connect your wallet, then try:

```
Send 5 USDC to 0x… on Sepolia.
Approve 0x… to spend 20 USDC.
What's the total supply of 0x…?
```

Full walkthrough: [Send ERC-20 tokens](https://roxi-os.si/docs/guides/send-tokens).

---

## 🧱 How it works

Every `prepare-*` tool returns three fields:

```json
{
  "prepareForAction": "send-transaction",
  "address": "0x1c7D4B196Cb0C7B01d743Fbc6116a902379C7238",
  "data": "0xa9059cbb…"
}
```

`prepareForAction` names the `metamask-mcp` tool that finishes the job; the agent passes `address` to it as `to`.

| `prepareForAction` | Used by | What happens |
| --- | --- | --- |
| `send-transaction` | transfer, approve, mint | MetaMask asks you to sign |
| `call` | total supply, allowance | Free read, then decode with `resolve-*` |
| `deploy-contract` | new token | MetaMask asks you to sign |

---

## ⚙️ Tools

### Writes

| Tool | Inputs | Finish with |
| --- | --- | --- |
| `prepare-transfer` | `tokenAddress`, `tokenDecimal`, `to`, `amount` | `send-transaction` |
| `prepare-approve` | `tokenAddress`, `tokenDecimal`, `spender`, `amount` | `send-transaction` |
| `prepare-mint` | `tokenAddress`, `tokenDecimal`, `to`, `amount` | `send-transaction` |
| `prepare-deploy-erc20-contract` | `bytecode`, `name`, `symbol`, `decimal` | `deploy-contract` |

### Reads

| Tool | Inputs | Finish with |
| --- | --- | --- |
| `prepare-total-supply` | `tokenAddress` | `call`, then `resolve-total-supply` |
| `resolve-total-supply` | `tokenDecimal`, `data` | — |
| `prepare-allowance` | `tokenAddress`, `owner`, `spender` | `call`, then `resolve-allowance` |
| `resolve-allowance` | `tokenDecimal`, `data` | — |

**Prompt:** `generate-erc20-contract` asks the agent to write an ERC-20, using a Solmate-based contract as its example.

> ⚠️ The example contract's `mint` function is public with no access control. Restrict it to an owner, or remove it, before deploying a real token.

Reference: [erc20-mcp docs](https://roxi-os.si/docs/servers/erc20-mcp).

---

## 🛠️ Build from source

```bash
git clone https://github.com/ROXI-OS-SI/erc20-mcp.git
cd erc20-mcp
pnpm install
pnpm build        # on Windows: npx tsc
```

Then point your client at the build:

```json
{
  "mcpServers": {
    "erc20": {
      "command": "node",
      "args": ["/absolute/path/to/erc20-mcp/build/index.js"]
    }
  }
}
```

---

## 🧩 Part of ROXI OS

| Server | Purpose |
| --- | --- |
| [metamask-mcp](https://github.com/ROXI-OS-SI/metamask-mcp) | Wallet connection and signing |
| [chainlist-mcp](https://github.com/ROXI-OS-SI/chainlist-mcp) | Chain metadata and RPC endpoints |
| **erc20-mcp** | ERC-20 transfers, approvals and reads |
| [solc-mcp](https://github.com/ROXI-OS-SI/solc-mcp) | Solidity compilation |

📖 [Documentation](https://roxi-os.si/docs) · 📦 [npm](https://www.npmjs.com/org/roxi-os)

## 🙏 Credits

Based on the original work of xiawpohr, extended for ROXI OS.

## ⚖️ License

MIT

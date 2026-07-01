---
name: yaar-deploy
description: Use when deploying a YAAR agent on 0G testnet with the YAAR CLI, including config creation, wallet setup, dry-run validation, deployment, and resume after interruption.
---

# YAAR Deploy

Use this skill when a user asks an agent to deploy a YAAR agent from a terminal.

## Goal

Deploy a YAAR agent on 0G testnet through the YAAR CLI. The testnet preview CLI package is
`@paul-unifra/yaar-cli`; it exposes the executable command `yaar`.

Do not ask the user to reveal secrets. Do not inspect or print private keys, API tokens, seed
phrases, or secret environment variables.

## Requirements

- Node.js 20 or newer.
- A 0G testnet wallet funded for mint fee, gas, and optional pre-purchase.
- Testnet API URL:

  ```bash
  export YAAR_API_URL=https://yaar-testnet.unifra.io/
  ```

## Install Or Run The CLI

Prefer `npx` for the testnet preview so the user always gets the published test package:

```bash
npx @paul-unifra/yaar-cli@latest --help
```

If the user wants a local executable, install it globally:

```bash
npm install -g @paul-unifra/yaar-cli@latest
yaar --help
```

## Workflow

1. Create the config file if the user does not already have one:

   ```bash
   npx @paul-unifra/yaar-cli@latest config init -o agent.yaml
   ```

2. Ask the user to edit `agent.yaml` with the real agent name, ticker, description, image, links,
   owner, category, and optional pre-purchase amount. Keep config minimal. Do not add secrets to the
   file.

3. Prefer an Open Wallet Standard wallet for signing:

   ```bash
   npx @paul-unifra/yaar-cli@latest wallet list
   npx @paul-unifra/yaar-cli@latest wallet create --name yaar-deployer
   npx @paul-unifra/yaar-cli@latest wallet address --wallet yaar-deployer
   ```

   Ask the user or team to fund the printed 0G address before deploying.

4. Dry-run before sending transactions:

   ```bash
   YAAR_API_URL=https://yaar-testnet.unifra.io/ \
   npx @paul-unifra/yaar-cli@latest deploy -c agent.yaml --ows-wallet yaar-deployer --dry-run --json
   ```

5. Deploy:

   ```bash
   YAAR_API_URL=https://yaar-testnet.unifra.io/ \
   npx @paul-unifra/yaar-cli@latest deploy -c agent.yaml --ows-wallet yaar-deployer
   ```

6. If interrupted, resume with the printed state id:

   ```bash
   YAAR_API_URL=https://yaar-testnet.unifra.io/ \
   npx @paul-unifra/yaar-cli@latest deploy --resume STATE_ID --ows-wallet yaar-deployer
   ```

## Private Key Mode

Use private-key signing only when the user explicitly requests it and their terminal already has the
needed environment configured. Do not ask the user to paste a private key.

```bash
YAAR_API_URL=https://yaar-testnet.unifra.io/ \
YAAR_PRIVATE_KEY=0x... \
npx @paul-unifra/yaar-cli@latest deploy -c agent.yaml --dry-run --json

YAAR_API_URL=https://yaar-testnet.unifra.io/ \
YAAR_PRIVATE_KEY=0x... \
npx @paul-unifra/yaar-cli@latest deploy -c agent.yaml
```

## Minimal Config Shape

```yaml
network:
  name: testnet
  rpc_url: "https://evmrpc-testnet.0g.ai"
  chain_id: 16602

api:
  base_url: "https://yaar-testnet.unifra.io/"

agent:
  name: "My AI Agent"
  ticker: "MYAI"
  description: "An AI agent that helps users complete useful tasks."
  profile_picture: "https://example.com/avatar.png"
  twitter: "myagent"
  website: "https://myagent.example"
  github: ""
  discord: ""
  docs: ""
  category: "AI Agent"

owner:
  type: self
  identifier: ""

tokenomics:
  pre_purchase:
    enabled: false
    amount: "0"
```

## Notes

- Treat `yaar` CLI output as the source of truth for defaults and validation.
- If the CLI reports missing API, RPC, signer, wallet, or auth configuration, show the exact error
  and ask the user to configure their terminal environment.
- Do not add local web-server startup steps unless the user specifically says they are deploying
  through a local development API.
- Private keys are never stored in `.yaar-deploy-*.json`.
- For delegated owners, use `owner.type: evm`, `github`, or `x` and set `owner.identifier`.

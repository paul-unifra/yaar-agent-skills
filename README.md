# YAAR Agent Skills

Agent skills for deploying YAAR agents on 0G.

## Install

```bash
npx skills add paul-unifra/yaar-agent-skills
```

## Use Without Installing

```bash
npx skills use paul-unifra/yaar-agent-skills@yaar-deploy
```

## Available Skills

- `yaar-deploy`: gives an agent the workflow context to install the YAAR CLI, create an agent config,
  choose a signer, run a dry-run, deploy to YAAR testnet, and resume interrupted deployments.

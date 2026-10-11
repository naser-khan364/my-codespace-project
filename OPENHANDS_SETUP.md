# OpenHands in GitHub Codespaces — setup guide

This repository is a **dedicated setup workspace**. Do not clone or modify NAK-Smart-CMMS, NAK-Smart-Furnace, NAK-Smart-Platform, or any other repository while preparing the environment.

## Important feasibility note

Codespaces is a development environment, not a guaranteed OpenHands hosting service. OpenHands' Docker runtime normally needs access to a Docker daemon and additional resources. A 2-core Codespace may be too small or may not support the required nested-container workflow reliably. Confirm Docker availability and available account quota before attempting to start OpenHands. Do not upgrade machine size or create extra Codespaces without the owner's decision.

## 1. Create the Codespace

1. Open <https://github.com/codespaces>.
2. Stop only a Codespace you personally recognize and no longer need. **Do not delete any Codespace** as part of this guide.
3. Open <https://github.com/codespaces/new>.
4. Select `naser-khan364/my-codespace-project`, branch `setup/openhands-codespaces` (after this branch is merged or selected), and the smallest available machine.
5. Wait for the Codespace to finish building.

If the account still reports that the maximum number of running Codespaces has been reached, stop here and resolve the quota issue manually. This repository cannot inspect or stop Codespaces for you.

## 2. Check Docker before installing OpenHands

In the Codespaces terminal, run:

```bash
docker version
docker info
```

Both commands should complete successfully. If Docker is unavailable or permission is denied, do not try privileged workarounds or change other Codespaces. The configured devcontainer includes the Docker-outside-of-Docker feature, but Codespaces policies and runtime limits may still prevent OpenHands' sandbox from working.

## 3. Install / launch OpenHands using current official instructions

Open the official documentation and use the currently documented image names and matching runtime versions:

- Installation: <https://docs.all-hands.dev/usage/installation>
- Official source repository: <https://github.com/All-Hands-AI/OpenHands>
- Docker runtime documentation: <https://docs.all-hands.dev/usage/runtimes/docker>

Do not copy old image tags from old tutorials. OpenHands' application and runtime images must be compatible. The standard Docker deployment exposes the UI on port `3000` and uses the Docker socket to create isolated agent runtime containers.

Before running any Docker command that mounts `/var/run/docker.sock`, understand that this grants the application broad control over the Codespace's Docker daemon. Use only a personal, isolated Codespace and do not expose the UI publicly. In the Ports tab, keep port 3000 private to your account unless you deliberately choose otherwise.

## 4. Configure the model provider

Open the forwarded port 3000 using the Codespaces **Ports** tab. Configure a supported model provider in the OpenHands UI.

- Enter the model provider API key only in the intended OpenHands settings/UI or a supported secret store.
- Never commit API keys, access tokens, passwords, cookies, or `.env` files.
- Do not send a secret in an agent prompt.
- A model provider may charge for usage even when the software is open-source. Check the provider's current pricing and free-tier terms first.

## 5. GitHub access — least privilege

Do not give the agent organization-wide or all-repository access for initial testing.

Preferred first test:
1. Use a **fine-grained personal access token** with an expiration date.
2. Restrict repository access to the single repository you explicitly want the agent to work on.
3. Grant only the permissions required for the planned task (typically repository metadata read, contents read/write, and pull requests read/write; add Actions read only if needed to inspect CI).
4. Store the token only in the intended secret/configuration field. Never write it to this repository or print it in logs.
5. Revoke the token after the experiment if it is no longer needed.

Token settings: <https://github.com/settings/personal-access-tokens>

For the first run, ask the agent to inspect the target repository and report a plan only. Then require a branch and a Pull Request. Do not permit automatic merging, deletion, force-push, or changes to any other repository.

## 6. Cline extension

The devcontainer requests the Cline extension. On first launch, confirm it appears in Extensions. Configure its provider only if you choose to use Cline; Cline is optional and is not required for OpenHands itself.

Official extension: <https://marketplace.visualstudio.com/items?itemName=saoudrizwan.claude-dev>

## 7. Safe first task

After OpenHands is running and GitHub access has been narrowly configured, start with:

> Inspect only the repository I explicitly selected. Do not modify files yet. Report the repository name, current branch, test commands, and a proposed plan. Do not access or change any other repository. Do not print secrets. Wait for approval before making changes. If approved, create a new branch, run relevant tests, and open a Pull Request. Never merge it.

## Troubleshooting checklist

- **Maximum running Codespaces reached:** stop a known, unneeded Codespace manually; do not delete it.
- **Docker command fails:** capture the exact error. Do not use privileged workarounds.
- **Port 3000 does not open:** check the terminal logs and Ports tab; ensure the port is private.
- **Model request fails:** check provider availability, model ID, API limits, and billing in the provider's dashboard. Never paste the key into chat.
- **Agent cannot access GitHub:** verify token expiration, repository selection, and least-privilege permissions.

## Current status

This repository contains setup instructions and a devcontainer definition only. OpenHands has **not** been installed or started by committing these files; a Codespace must be created and its runtime checked first.

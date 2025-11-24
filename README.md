# flyingtail

Tailscale exit node running on Fly.io in Dallas.

## Overview

This deployment uses the official `tailscale/tailscale` Docker image to run a Tailscale exit node. The configuration is minimal - no custom Dockerfile needed, everything is configured through environment variables in `fly.toml`.

## Setup

1. Install flyctl and authenticate:

   ```bash
   brew install flyctl
   flyctl auth login
   ```

2. Create the app:

   ```bash
   flyctl apps create flyingtail
   ```

3. Generate a Tailscale auth key:
   - Go to <https://login.tailscale.com/admin/settings/keys>
   - Generate an auth key with these settings:
     - Reusable: Yes (so the container can restart)
     - Ephemeral: Yes (node disappears when offline)
     - Tags: Add appropriate tags if needed

4. Set the auth key as a secret:

   ```bash
   flyctl secrets set TS_AUTHKEY=tskey-auth-xxxxxxxxxxxxx
   ```

5. Deploy:

   ```bash
   flyctl deploy
   ```

6. Enable the exit node in Tailscale admin:
   - Go to <https://login.tailscale.com/admin/machines>
   - Find your `flyingtail` node
   - Enable "Use as exit node"

## Usage

Connect to the exit node from any device on your Tailnet:

```bash
tailscale set --exit-node=flyingtail
```

Or use the Tailscale GUI to select it as your exit node.

## Monitoring

Check app status:

```bash
flyctl status
flyctl logs
```

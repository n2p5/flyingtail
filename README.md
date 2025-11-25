# flyingtail

Tailscale exit node running on Fly.io in Dallas.

## Overview

This deployment uses the official `tailscale/tailscale` Docker image to run a Tailscale exit node on fly.io in Dallas. The configuration is minimal - no custom Dockerfile needed, everything is configured through environment variables in `fly.toml`.

## Prerequisites

### Tailscale ACL Configuration

Add this to your ACL policy at <https://login.tailscale.com/admin/acls>:

```json
{
  "tagOwners": {
    "tag:exit-node": ["autogroup:admin"]
  },
  "autoApprovers": {
    "exitNode": ["tag:exit-node"]
  }
}
```

This enables auto-approval so the exit node activates immediately on deploy.

### Tailscale Auth Key

Generate a key at <https://login.tailscale.com/admin/settings/keys> with:

- **Reusable**: Yes (allows container restarts)
- **Ephemeral**: Yes (node disappears when offline)
- **Tags**: `tag:exit-node`

## Deploy

Set the `TS_AUTHKEY` environment variable with your Tailscale auth key, then run:

```bash
flyctl apps create flyingtail
flyctl secrets set TS_AUTHKEY=$TS_AUTHKEY --stage
flyctl deploy
```

## Usage

Connect to the exit node from any device on your Tailnet:

```bash
tailscale set --exit-node=flyingtail
```

Or use the Tailscale GUI to select it as your exit node.

## Monitoring

```bash
flyctl status
flyctl logs
```

## Shutdown

```bash
flyctl scale count 0
```

To start again: `flyctl scale count 1` or `flyctl deploy`.

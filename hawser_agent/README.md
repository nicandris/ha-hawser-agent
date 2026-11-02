# Hawser Agent

Lets a [Dockhand](https://github.com/Finsys/dockhand) instance elsewhere on the LAN
see and manage this Home Assistant host's Docker containers.

Runs the official [Hawser](https://github.com/Finsys/hawser) agent in **Standard mode**:
it listens on port 2376 and Dockhand connects to it. The binary is copied from the
official `ghcr.io/finsys/hawser` image at the version pinned in the Dockerfile, so
nothing is downloaded when the add-on starts.

## Setup

1. Info tab: turn **Protection mode off**. Supervisor only grants Docker access then.
2. Configuration tab: set `token` to a long random string, then start the add-on.
3. In Dockhand: Settings → Environments → Add environment → **Hawser (Standard)**,
   host `<this HA host's IP>`, port `2376`, and the same token.

## Options

| Option | Default | |
|---|---|---|
| `token` | — | Required. Shared secret Dockhand sends; Hawser refuses to listen on the LAN without one. |
| `log_level` | `info` | `debug`, `info`, `warn` or `error`. |
| `request_timeout` | 30 s | Raise if container recreates on slow storage time out. |

The agent id is generated once and kept in `/data/agent_id`, so Dockhand sees the same
agent across restarts. Stacks Dockhand deploys here are stored in `/data/stacks`.

## Credits

[Hawser](https://github.com/Finsys/hawser) and its logo are by Finsys (MIT).

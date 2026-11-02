# Hawser Agent — Home Assistant add-on

[![CI](https://github.com/nicandris/ha-hawser-agent/actions/workflows/ci.yaml/badge.svg)](https://github.com/nicandris/ha-hawser-agent/actions/workflows/ci.yaml)
![Add-on version](https://img.shields.io/badge/dynamic/yaml?url=https%3A%2F%2Fraw.githubusercontent.com%2Fnicandris%2Fha-hawser-agent%2Fmain%2Fhawser_agent%2Fconfig.yaml&query=%24.version&label=add-on)
![Architectures](https://img.shields.io/badge/arch-amd64%20%7C%20aarch64-blue)

Run the official [Hawser](https://github.com/Finsys/hawser) agent on your Home Assistant OS /
Supervised host, so the [Dockhand](https://github.com/Finsys/dockhand) instance you already run
elsewhere can manage the host's Docker containers, images, volumes and networks.

> [!IMPORTANT]
> **Set a `token`.** The add-on will not start without one. Dockhand sends it on every request,
> and whoever holds it gets full control of your Home Assistant host. Use a long random string
> and enter the same value in Dockhand.

- **Official binary, nothing downloaded at runtime.** Hawser is copied from the official
  `ghcr.io/finsys/hawser` image at the tag pinned in [`Dockerfile`](hawser_agent/Dockerfile),
  onto the [Home Assistant base image](https://github.com/hassio-addons/app-base). The add-on
  makes no network calls of its own when it starts.
- **Versioned with Hawser.** Add-on `0.2.53` ships Hawser `0.2.53`; a fourth component
  (`0.2.53.1`) marks a rebuild on a newer base image.
- **Standard mode.** Hawser listens on port 2376 and Dockhand connects to it over your LAN, the
  same model as a Portainer agent on 9001.
- **Stable identity.** The agent id is generated once and kept in the add-on's `/data`, so
  Dockhand sees the same agent across restarts and updates.

## Installation

[![Add repository to my Home Assistant](https://my.home-assistant.io/badges/supervisor_add_addon_repository.svg)](https://my.home-assistant.io/redirect/supervisor_add_addon_repository/?repository_url=https%3A%2F%2Fgithub.com%2Fnicandris%2Fha-hawser-agent)

Or by hand: **Settings → Add-ons → Add-on Store → ⋮ → Repositories**, add

```
https://github.com/nicandris/ha-hawser-agent
```

Then:

1. Install **Hawser Agent**. It builds on the host, so the first install takes a minute.
2. On the **Info** tab, turn **Protection mode off**. Supervisor only grants the add-on Docker
   access with it off.
3. On the **Configuration** tab, set `token`, then start the add-on.
4. In Dockhand: **Settings → Environments → Add environment → Hawser standard**, host
   `<home-assistant-host>`, port `2376`, and the same token.

## Security

Hawser gives whoever reaches port 2376 with the right token full control of the host's Docker,
which on Home Assistant OS means the whole system. Never forward the port to the internet, keep
the token secret, and change it here and in Dockhand together if it leaks.

The connection is plain HTTP inside your LAN. Hawser also supports TLS (`TLS_CERT` /
`TLS_KEY`); this add-on does not expose those options yet.

## Configuration

| Option | Default | |
|---|---|---|
| `token` | — | **Required.** Shared secret Dockhand sends. |
| `log_level` | `info` | `debug`, `info`, `warn` or `error`. |
| `request_timeout` | `30` | Seconds. Raise it if container recreates on slow storage time out. |

Full option notes and troubleshooting: [`hawser_agent/README.md`](hawser_agent/README.md), the
same page shown on the add-on's Info tab. Release notes:
[`hawser_agent/CHANGELOG.md`](hawser_agent/CHANGELOG.md).

## Why not Edge mode?

Hawser's Edge mode (the agent dials out to Dockhand over a WebSocket) suits hosts behind NAT.
A Home Assistant host is normally on the same LAN as Dockhand, where Standard mode is simpler
and has one moving part fewer. Edge mode may be added as an option later.

## Development

CI lints the add-on config and builds both architectures on every push and pull request.
Dependabot watches the Hawser image and the GitHub Actions.

To release a new Hawser version, bump **both**:

- the `ghcr.io/finsys/hawser` tag in `hawser_agent/Dockerfile`, and
- `version` in `hawser_agent/config.yaml`, plus a `CHANGELOG.md` entry.

Dependabot bumps only the tag, and Home Assistant offers an update only when `version` changes.
The Home Assistant base image is pinned in `hawser_agent/build.yaml`, which Dependabot cannot
read, so bump it by hand and release it as a fourth-component version.

## Credits

[Hawser](https://github.com/Finsys/hawser) and its logo are by Finsys, MIT-licensed. The add-on
structure follows my earlier, now archived,
[ha-portainer-agent](https://github.com/nicandris/ha-portainer-agent).

## License

[MIT](LICENSE)

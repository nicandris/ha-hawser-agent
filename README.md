# ha-hawser-agent

Home Assistant add-on repository with one add-on, [Hawser Agent](hawser_agent/README.md):
the [Hawser](https://github.com/Finsys/hawser) agent that lets
[Dockhand](https://github.com/Finsys/dockhand) manage this host's Docker.

Version bumps: change the `ghcr.io/finsys/hawser:<tag>` pin in `hawser_agent/Dockerfile`
**and** `version` in `hawser_agent/config.yaml`, plus a CHANGELOG entry. Dependabot only
bumps the Dockerfile, and Home Assistant offers an update only when `version` changes.

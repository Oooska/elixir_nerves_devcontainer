# elixir_nerves_devcontainer

A Debian-based VS Code devcontainer configured for Elixir + Nerves development. It includes tooling to create Nerves projects and customizing custom Nerves systems.

## Quick start
- Open this folder in VS Code and reopen in container.
- The workspace root is mounted at `/workspaces`. Create a new project inside that folder, for example:
  - mix new my_app
  - mix nerves.new my_nerves_app
  - mix phx.new my_phx_app

## SSH keys
Place a public (and optionally private) SSH key under:
- .ssh/id_rsa.pub  in the repository root (`/workspaces/elixir_nerves_devcontainer/.ssh`)
Set appropriate permissions on the host before adding keys to the container (e.g. chmod 600 for private keys).

## Network
The devcontainer runs in host network mode so Nerves devices on the local network are reachable and can be updated with fwup.
Note:  mdns is not forwarded into the container; use device IP addresses when mdns resolution fails.



# elixir_nerves_devcontainer


A Debian-based VS Code devcontainer for Elixir + Nerves development. Includes all tooling needed to create, build, and update Nerves projects and custom Nerves systems.

## Quick start

- Open this folder in VS Code and reopen in the container.
- The workspace root is mounted at `/workspaces`. Create a new project inside that folder, for example:
  - `mix new my_app`
  - `mix nerves.new my_nerves_app`
  - `mix phx.new my_phx_app`
- Add the new project folder to your workspace in VS Code.

For a step-by-step guide to creating and deploying your first Nerves firmware, see [Nerves Firmware Quickstart](./first_nerves_device.md).

## SSH keys

- To generate a new key: Run `ssh-keygen` in the devcontainer terminal.
- To use an existing SSH key: Place your public (and optionally private) key under `elixir_nerves_devcontainer/.ssh`.


## Network
nmap is installed in the container to make it easier to find nerves devices on the local network. 
e.g. `nmap -sL 192.168.1.1-254 | grep nerves`

- The devcontainer runs in host network mode so Nerves devices on your local network are reachable and can be updated with `fwup`.
- **Note:** mDNS is not forwarded into the container.
- `nmap` is installed to help find Nerves devices on the network:
  - Example: `nmap -sL 192.168.1.1-254 | grep nerves`

### Windows Users

To use host networking mode in Docker for Windows, enable it in Docker Desktop settings:
- Go to Settings > Resources > Network tab > check "Enable host networking."


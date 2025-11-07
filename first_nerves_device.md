

## Nerves Firmware Quickstart (VS Code Devcontainer)
This guide walks you through creating your first Nerves firmware project inside a VS Code devcontainer. For full details, see the [official Nerves documentation](https://hexdocs.pm/nerves/getting-started.html). This guide highlights devcontainer-specific tips and common gotchas.

## 1. Create an SSH Key
In the devcontainer terminal, run:

```bash
ssh-keygen
```
This key will be used for secure communication with your Nerves device.

## 2. Create a New Nerves Project

```bash
cd /workspaces
mix nerves.new hello_nerves
cd hello_nerves
```

In VS Code: Open the Command Palette and select "Add Folder to Workspace...". Choose your new `hello_nerves` folder.


## 3. Configure Wireless Networking

Edit `hello_nerves/config/target.exs`. Find the `config :vintage_net` section and set your WiFi SSID and password using environment variables:

```elixir
config :vintage_net,
  regulatory_domain: "US", # Change to your country code
  config: [
    {"usb0", %{type: VintageNetDirect}},
    {"eth0", %{type: VintageNetEthernet, ipv4: %{method: :dhcp}}},
    {"wlan0",
      %{
        type: VintageNetWiFi,
        vintage_net_wifi: %{
          networks: [
            %{
              key_mgmt: :wpa_psk,
              ssid: System.get_env("WIFI_SSID"),
              psk: System.get_env("WIFI_PSK")
            }
          ]
        },
        ipv4: %{method: :dhcp}
      }
    }
  ]
```

Set your WiFi credentials in the devcontainer terminal:

```bash
export WIFI_SSID=my_wireless_network
export WIFI_PSK=my_wireless_network_password
```
## 4. Build the Project

Set your [Nerves target](https://hexdocs.pm/nerves/supported-targets.html) (e.g., Raspberry Pi Zero):

```bash
export MIX_TARGET=rpi0
mix deps.get
mix firmware
```

## 5. Burn the SD Card

**Note:** The devcontainer cannot access block devices directly, so you cannot use `mix burn` inside the container.

**Option 1: Using fwup on your host machine**

Install [fwup](https://github.com/fhunleth/fwup) on your host. Then run:

```bash
sudo fwup -d /dev/[block device] _build/rpi0_dev/nerves/images/hello_nerves.fw
```

**Option 2: Create a disk image and use an image burner**

```bash
mix firmware.image hello_nerves_firmware.img
```
Use [Balena Etcher](https://etcher.balena.io/) or similar to write the image to your SD card.

## 6. Connect to Your Device

Insert the SD card and power on your device. It should boot and connect to your WiFi network.

**Ways to find your device on the network:**

- **Ping (from local command prompt):**
  ```bash
  ping nerves.local
  ```
- **nmap:**
  ```bash
  nmap -sL 192.168.1.1-254 | grep nerves
  ```
- **Router:**
  Check your router's device map or DHCP table for the Nerves device.

Once you know the device's IP address, you can SSH from the devcontainer:

```bash
ssh <ip_address_of_device>
```

Or from your local machine (using mDNS):

```bash
ssh -i elixir_nerves_devcontainer/.ssh/id_rsa nerves.local
```

For serial or USB connections, see the [official docs](https://hexdocs.pm/nerves/connecting-to-a-nerves-target.html).


## 7. Update Device Remotely

After making firmware changes, you can upload updates to a device already online (no need to reburn the SD card):

```bash
mix upload <ip_address_of_device>
```
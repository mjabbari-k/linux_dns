# DNS Switcher for Rocky Linux 9.5

A set of simple Bash scripts to switch DNS resolvers on active Ethernet (including USB-Ethernet) and Wi-Fi connections using `nmcli` and `ip link`. No background services or daemons are required—just standard NetworkManager and built-in Linux tools.

## Features

- **Quick DNS switching**: Choose from a menu of popular public DNS providers (Google, Cloudflare, Quad9, etc.).
- **Universal interface detection**: Works on wired, USB-Ethernet, and wireless (802.3 & 802.11) without manual interface names.
- **Automatic revert**: Restore automatic (DHCP) DNS settings with a single command.
- **Handles spaces in connection names**: Full support for NetworkManager profiles with spaces.
- **No extra services**: Relies only on `nmcli`, `ip link`, `awk`, and `grep`, so there’s nothing to install or keep running in the background.

## Repository Structure

```text
dns/              # Interactive menu script
├── dns           # Main menu: pick a DNS provider or revert to automatic
├── setdns        # Script to apply custom DNS servers
└── nodns         # Script to revert to automatic DNS
```

## Prerequisites

- Rocky Linux 9.5 (or any RHEL-9 based distro with NetworkManager)
- `nmcli` (NetworkManager CLI)
- `bash`, `awk`, `grep`, `ip` (all standard)
- **No background daemons** beyond the default NetworkManager.

## Installation

1. Clone this repository:

   ```bash
   git clone https://github.com/yourusername/rocky-dns-switcher.git
   cd rocky-dns-switcher
   ```

2. Make scripts executable:

   ```bash
   chmod +x dns setdns nodns
   ```

3. (Optional) Move them into your PATH:

   ```bash
   sudo mv dns setdns nodns /usr/local/bin/
   ```

## Usage

### Interactive Menu (`dns`)

```bash
./dns
```

1. Select a DNS provider from the list (e.g., `1` for Google, `2` for Cloudflare`).  
2. The script calls `setdns <DNS1> <DNS2>` under the hood.  
3. To revert, choose `e` (Obtain DNS server automatically), which invokes `nodns`.

### Direct Commands

- **Set custom DNS**:

  ```bash
  setdns 8.8.8.8 8.8.4.4
  ```

- **Revert to automatic DNS**:

  ```bash
  nodns
  ```

## How It Works

1. **Detect active connection**:
   - First tries:
     ```bash
     nmcli -t -f NAME,TYPE connection show --active |
       grep -E '802-3-ethernet|802-11-wireless' |
       cut -d: -f1
     ```
   - If none found, falls back to:
     ```bash
     ip link show | grep 'state UP' | grep -E 'wlp|eth|enp|enx' | awk '{print $2}'
     ```
   - Maps the interface back to a connection name via NMCLI, preserving names with spaces.

2. **Apply DNS settings** using `nmcli connection modify`:
   - `ipv4.dns "$DNS1 $DNS2"`
   - `ipv4.ignore-auto-dns yes` (to force custom DNS)

3. **Revert DNS** by clearing `ipv4.dns` and restoring `ipv4.ignore-auto-dns no`.

4. **Restart** the connection:
   ```bash
   nmcli connection down "$CONN" && nmcli connection up "$CONN"
   ```

## Contributing

Feel free to open issues or pull requests to add more providers, support IPv6, or improve interface detection.

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.

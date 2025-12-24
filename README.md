# DNS Selector Script — Installation & Usage Guide

This package provides three small shell scripts for managing DNS settings on the currently active NetworkManager connection:

- `dns` — interactive menu for selecting a DNS profile
- `setdns` — apply a specific DNS server list (comma-separated)
- `nodns` — reset DNS back to automatic (DHCP / auto)

The scripts include safety logic to avoid modifying hotspot/AP-mode Wi‑Fi connections.

---

## Features

- Interactive menu-based DNS selector
- Apply DNS settings to the active NetworkManager profile (wired or Wi‑Fi client mode)
- Safe handling for NetworkManager connection names that include **spaces** and **colons (`:`)**
- Hotspot/AP mode detection to prevent disrupting tethering setups

---

## Requirements

- NetworkManager (`nmcli`)
- A Linux system using NetworkManager for connection management
- Root access for installing to `/usr/local/bin`

---

## Installation

Copy the scripts into `/usr/local/bin` and make them executable:

```sh
sudo cp dns setdns nodns /usr/local/bin/
sudo chmod +x /usr/local/bin/dns /usr/local/bin/setdns /usr/local/bin/nodns
```

You can then run:

```sh
dns
```

---

## Usage

### Interactive menu

```sh
dns
```

Choose a profile number to apply DNS, or select `e` to reset to automatic DNS.

### Set DNS directly

```sh
setdns "1.1.1.1,1.0.0.1"
```

### Reset to automatic DNS

```sh
nodns
```

---

## Troubleshooting / Notes

1. **Connection names with spaces or `:`**
   - Spaces are handled through standard shell quoting.
   - NetworkManager’s `nmcli -t` output escapes literal colons as `\:`.  
     These scripts explicitly unescape that when selecting the active connection, so profiles like `Office LAN: Dock` work correctly.

2. **No active connection detected**
   - The scripts detect the active connection associated with your default route interface.
   - If your machine has no default route (no Internet), the scripts may fail with “Could not detect default interface.”

3. **Hotspot / AP mode**
   - If the active connection is Wi‑Fi with `802-11-wireless.mode=ap`, the scripts do nothing and exit cleanly.

---

## Uninstall

```sh
sudo rm -f /usr/local/bin/dns /usr/local/bin/setdns /usr/local/bin/nodns
```

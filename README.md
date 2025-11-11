# DNS Selector Script — Installation & Usage Guide

This script allows you to quickly switch between predefined DNS resolver profiles via a simple menu interface.  
It also includes safety logic preventing NetworkManager from modifying hotspot/AP mode connections.

---

## ✅ Features

- Interactive menu-based DNS selector  
- 17 preset DNS profiles (Google, Cloudflare, Quad9, Yandex, etc.)  
- Fallback handling for systems where a NetworkManager connection cannot be modified  
- Hotspot/AP mode detection (prevents modifying hotspot DNS)  
- Automatic DNS reset option  

---

## ✅ Requirements

- A Linux system with `bash` or `sh`
- `nmcli` (NetworkManager CLI) installed  
- Superuser privileges (`sudo`) for changing system DNS  
- A non-hotspot network connection for applying DNS to NetworkManager profiles  

> ✅ Works on:  
> - Ubuntu / Debian / Pop!_OS / Mint  
> - Fedora / RHEL / Rocky Linux  
> - Arch / Manjaro / EndeavourOS  
> - openSUSE  
> - Any distro using NetworkManager  

> ⚠️ Notes:  
> - If a distro does **not use NetworkManager** (e.g., Alpine, Void Linux with runit, older CentOS minimal images), DNS will be written directly to `/etc/resolv.conf`.  

---

## ✅ Installation

### 1. Download the script

Place `dns.sh` into `/usr/local/bin`:

```sh
sudo mv dns.sh /usr/local/bin/dns
```

> Rename to `dns` for easier typing.

### 2. Make it executable

```sh
sudo chmod +x /usr/local/bin/dns
```

---

## ✅ (Optional) Install companion scripts

If your system uses the original structure:

```sh
sudo mv setdns.sh /usr/local/bin/setdns
sudo mv nodns.sh /usr/local/bin/nodns
sudo chmod +x /usr/local/bin/setdns
sudo chmod +x /usr/local/bin/nodns
```

---

## ✅ Running the script

Run:

```sh
dns
```

You will see something like:

```
                        DNS Resolver Setup
-----------------------------------------------------------
1) Google        2) Cloudflare     3) Shecan      4) Electro
5) Spotify_1    6) Spotify_2      7) Spotify_3   8) 403
9) Alternate    10) Comodo        11) DNS.Watch  12) Level3
13) OpenDNS     14) OpenNIC       15) Quad9      16) Verisign
17) Yandex

e) Revert to automatic DNS
```

Choose a number and press Enter.

---

## ✅ How DNS is Applied

### If NetworkManager is active:
- Detects the current default route’s interface  
- Finds the connection using that interface  
- Applies DNS safely using:  

```
nmcli connection modify <CONNECTION> ipv4.dns "<DNS1,DNS2>" ipv4.ignore-auto-dns yes
nmcli connection up <CONNECTION>
```

### If the active connection is a hotspot/AP:
- The script **will not apply DNS to it**
- Falls back to writing `/etc/resolv.conf`

### If NetworkManager is missing:
- Directly writes to `/etc/resolv.conf`

---

## ✅ Resetting to Automatic DNS

Choose option:

```
e
```

This resets the active NetworkManager connection (unless it’s a hotspot).  
If hotspot or no NM connection exists, it clears `/etc/resolv.conf`.

---

## ✅ Uninstallation

Simply remove the script:

```sh
sudo rm /usr/local/bin/dns
sudo rm /usr/local/bin/setdns
sudo rm /usr/local/bin/nodns
```

---

## ✅ Troubleshooting

### “No active NetworkManager connection found”
- Your system may be using **systemd-resolved**, **dhcpcd**, or another network manager  

### “Permission denied writing /etc/resolv.conf”
- Run with:

```sh
sudo dns
```

### Hotspot DNS not changing
✅ This is intentional — the script protects hotspot connections.

---

## ✅ Compatibility Notes

| Component | Works? | Notes |
|----------|--------|-------|
| NetworkManager | ✅ | Full functionality |
| systemd-resolved | ✅ | DNS overrides resolv.conf |
| dhcpcd | ⚠️ | Uses resolv.conf fallback mode |
| Hotspot/AP-mode | ✅ | Script avoids modifying settings |
| All major distros | ✅ | As long as `nmcli` is present |

---

If you want, I can also generate:
✅ a `.deb` package  
✅ an `.rpm` installer  
✅ auto-setup script  
✅ systemd unit for periodic DNS refresh  

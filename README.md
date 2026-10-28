# MikroTik Desktop Manager v4.0 ISP Pro
**MikroTik Manager by Arafath Rahman (Pieal) — CEO & CTO, CloudCore Network — www.ccn.isp-bd.com**

RouterOS manager + ISP client & billing system (Python + Tkinter, standard library only; a few optional packages add
camera / JPG preview / SSH-password login).

## দ্রুত শুরু (Quick start, বাংলা)
1. `run_windows.bat` চালান (Python 3.11+ লাগবে)।
2. **Router Sync → ＋ Add Device** — Protocol বেছে নিন: API 8728 / API-SSL 8729 / SSH 22 / Telnet 23 / SNMP 161। *Test connection* চাপুন, *Save password* টিক দিন (অটো-সিঙ্কের জন্য), Save।
3. একাধিক রাউটার যোগ করুন → **Sync All**। PPPoE secret গুলো আপনাআপনি **Clients** এ ঢুকে যাবে (নতুন = import), online/offline, IP, uptime আপডেট হবে।
4. **Clients → ＋ Add Client**: PPPoE ID/Password, Package, Bill, Name, Phone 1/2, NID, Photo/NID (Upload বা Camera), এবং *Billing & Due* ট্যাবে **Month drop-down** থেকে মাস বেছে বিল/পেইড/DUE দেখে পেমেন্ট নিন।
5. **Billing**: মাসের সব বিল, Due list, Collection report, রিসিট প্রিন্ট। **Packages**: প্যাকেজ ও দাম।

## What is new in v4.0
| Area | Features |
|---|---|
| **Multi-device, multi-protocol** | Any number of routers. Per device choose **API (8728)**, **API-SSL (8729)**, **SSH (22)**, **Telnet (23)** or **SNMP v1/v2c (161)**. Optional SNMP "companion" on API/SSH/Telnet devices adds temperature / voltage. Background **auto-sync** (1-60 min) + Sync Selected / Sync All, state / CPU / RAM / uptime / PPPoE counts / interface counters per device, failure + recovery alerts in the bell. |
| **What each protocol can do** | API / SSH / Telnet: health, interfaces, PPP active **and** PPP secrets (import clients, push / edit / suspend / enable / delete). SNMP: health, interface traffic, temperature and the online PPPoE users (`<pppoe-user>` interfaces) — read-only, cannot read secrets. |
| **Clients** | PPPoE ID, password, package, bill, name, phone 1 & 2, NID, address, zone, join date, status, note. Add / edit / remove, search, filter (router, package, zone, status, online/offline, due, paid, not-on-router), CSV import/export, right-click menu, show/hide passwords. **Client photo, NID front/back, other document: upload or camera.** |
| **Router ↔ client actions** | Push client to router (create/update secret), Pull from router, Suspend / Enable (disables the secret and drops the session), delete on router too, "re-enable after payment" prompt, optional **auto-suspend overdue** (OFF by default). |
| **Billing** | Monthly bills (auto-generated), **month drop-down → bill / paid / DUE**, partial payments, discount, method (Cash / bKash / Nagad / Rocket / Bank / Card), TrxID, payment history, delete payment, **printable receipt** (browser → Print / PDF), due list, collection report, per-day / per-method / per-router totals, WhatsApp due-reminder (editable Bangla template). |
| **Packages** | Name, speed, price, PPP profile; import profiles from a router; apply price to all clients of a package. |
| **Data** | SQLite database (`%APPDATA%\MikroTikDesktopManager\isp.db`), one-click **backup / restore**, audit log of every change, documents in `files/<client id>/`. |

## Security notes (please read)
* Router passwords are saved **only if you tick "Save password"** and are protected with **Windows DPAPI** (decryptable only by the same Windows user on the same PC). On non-Windows systems they are only base64-obfuscated.
* PPPoE client passwords are stored **in plain text inside `isp.db`** (you need to see/print them). Protect the PC / folder, and keep backups private. Device export (JSON) never includes router passwords.
* Create a dedicated RouterOS user for this app; allow API / SSH / SNMP only from your admin PC. Never expose these services to the internet.
* Auto-suspend is OFF by default; turn it on in *ISP Settings & Data* only after checking the due-day rule.

## Optional packages (`pip install -r requirements-optional.txt`)
`pillow` (JPG previews + camera preview), `opencv-python` (camera capture), `paramiko` (SSH with password; without it SSH uses the system `ssh` with key login).

## Test without a router
```
python tools\mock_router.py     # RouterOS API on 127.0.0.1:8728
python tools\mock_devices.py    # Telnet 127.0.0.1:2323 + SNMP 127.0.0.1:1161 (community "public")
```
Add three devices (API 8728, Telnet 2323, SNMP 1161 → host 127.0.0.1) and press **Sync All**.

## New source files (`src/`)
`snmp.py` SNMP client • `clitools.py` Telnet / SSH + terse parser • `transport.py` one interface for all protocols • `sync.py` multi-router auto-sync •
`db.py` SQLite • `secret.py` DPAPI • `isp_clients.py` / `isp_billing.py` / `isp_routers.py` / `isp_app.py` / `isp_common.py` ISP pages

---
# Previous module reference (v3 Pro)

Windows desktop app (Python + Tkinter, **no third-party packages**) that talks to RouterOS v6/v7 over the
RouterOS API (TCP 8728) or API-SSL (TCP 8729). 

## What's new in v3 Pro
| Area | What you get |
|---|---|
| **Look & feel** | Dark navy "digital" theme, gradient KPI cards, neon colours, rounded panels, colourful icon sidebar with active-item highlight |
| **Dashboard** | 6 live KPI cards with sparklines, Network Traffic (Live) area chart (5 min / 15 min / 1 h, per-interface), current speed + TX/RX donut, System Resources ring gauges (CPU / Memory / Disk / Uptime), Device List, BGP peers donut, PPPoE clients donut, Top-5 interface traffic, Recent Logs, Quick Tools, Topology mini-map |
| **WinBox module** | Full WinBox menu tree in the sidebar (Interfaces, Bridge, PPP, IP, IPv6, Firewall, Routing, BGP, Queues, Wireless + CAPsMAN, Switch, MPLS, RADIUS, System, Files, Logs, Tools — ~130 pages). **Shift+click / right-click → opens any page in its own floating WinBox window**; "WinBox mode" in Settings does this for every page; *WinBox Windows ▾* menu: Cascade / Tile / Close all |
| **Quick Connect** | WinBox-loader page: connect by IP without a profile, MNDP Layer-2 neighbour discovery, saved routers |
| **Devices** | Fleet list with TCP reachability probe (every 20 s), live CPU/RAM/uptime/TX-RX for the connected router, Scan Network (finds RouterOS API/WinBox ports in a CIDR), import/export profiles |
| **Graphical Monitor** | 7 live charts (total + busiest 6 interfaces) |
| **Topology Map** | This router + `/ip/neighbor` devices, click a device to add it |
| **Alerts & Notifications** | Bell badge; alerts for CPU ≥ 85 %, memory ≥ 90 %, link down, BGP not established, error/critical log lines; CSV export |
| **Reports** | HTML report (opens in browser) or CSV files of system, interfaces, IPs, routes, DHCP, PPP, queues, firewall, NAT, users, services, packages |
| **Quick Tools** | WinBox, Terminal, Torch, Bandwidth Test, Ping, Traceroute, DNS Lookup, IP Scan, Export Config, Backup, Restore, Reboot |
| **More tools** | IP Scan, CPU Profile, E-mail, Make Supout.rif, Restore backup |
| **Search** | Top bar search (Ctrl+/ or Ctrl+K): pages, devices, interfaces |
| **Extras** | F11 fullscreen, Auto-Refresh switch (pauses live monitoring), Settings page |

> When no router is connected the dashboard shows clearly-flagged **demo data** so you can preview the design
> (Settings → untick the first option to turn it off).

## Run
1. Install Python 3.11+ (tick *Add python.exe to PATH*; the standard installer includes Tkinter).
2. Double-click `run_windows.bat` (or `python src\mikrotik_manager.py`).
3. **＋** add a router → **Connect** → enter password.  (or use **Quick Connect**)

## Build EXE (on Windows)
Double-click `build_windows.bat` → `dist\MikroTikDesktopManager.exe`.

## Router side
- `/ip service enable api` (8728) or `api-ssl` (8729); restrict `address=` to your admin PC/subnet.
- Dedicated user; policies: `read, write, test, reboot, policy, sensitive, api`.
- Never expose the API port to the internet.

## Test without a router
`python tools\mock_router.py` starts a fake API server on 127.0.0.1:8728 (user `admin`, any password).

## Not possible through the RouterOS API (use real WinBox/SSH)
Winbox's proprietary protocol itself, MAC-Winbox / MAC-Telnet, Safe Mode, packet-sniffer streaming, file upload/download
(use SFTP/FTP), Quick Set wizards. "Reset Configuration" is deliberately left out. This app's WinBox module re-creates
the WinBox *menus and windows* on top of the API.

## Source layout (`src/`)
`mikrotik_manager.py` app shell, sidebar, WinBox windows, classic table/tool pages • `pro_pages.py` dashboard & new pages •
`ui_kit.py` canvas widgets • `services.py` telemetry / alerts / fleet / demo • `menu_spec.py` menu tree •
`routeros_api.py` API client • `mndp.py` discovery • `core.py` shared base

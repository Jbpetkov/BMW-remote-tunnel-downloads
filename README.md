# BMW Remote Tunnel

**Remote BMW diagnostics and coding over your own encrypted tunnel.**
The technician's PC with ISTA or E-Sys sees the ICOM or ENET cable at the customer's car as if it were plugged in locally – no remote desktop, no extra hardware.

🇧🇬 [Български](README.bg.md)

```
 Car ── ICOM / ENET ── Client PC  ══ internet ══  Relay server  ══ internet ══  Technician PC (ISTA / E-Sys)
```

**[⬇ Download the latest version](https://github.com/Jbpetkov/BMW-remote-tunnel-downloads/releases/latest)**

---

## The parts

| File | Who uses it | What it does |
|---|---|---|
| `BMWRemoteTunnel-Client-vX.exe` | the person **at the car** | connects the ICOM / ENET cable to the tunnel |
| `BMWRemoteTunnel-Tech-vX.exe` | the **technician** | gives ISTA / E-Sys a virtual network adapter that leads to the car |
| `BMWRemoteTunnel-Relay-vX.exe`, `bmwremotetunnel-relay-vX-linux-*` | whoever runs the **server** | meeting point for both sides (only needed if you run your own) |
| `BMWRemoteTunnel-Check.exe` | server owner | checks from outside that a server is reachable |
| `install-relay.bat`, `uninstall-relay.bat`, `install-relay-linux.sh` | server owner | install / remove the server |
| `SHA256SUMS.txt` (+ `.sig`) | anyone | checksums to verify the downloads |

All programs are single `.exe` files – nothing to install except the drivers below. Windows 10 / 11.

---

## Quick start

### 1. Technician (once)
1. Install the free **TAP-Windows** network driver (it comes with OpenVPN, or as the standalone *tap-windows* installer from openvpn.net).
2. Start `BMWRemoteTunnel-Tech-vX.exe` and enter the **server address** (`host` or `host:port`, default port 23917).

### 2. Person at the car
1. Connect the ICOM or the ENET cable to the car and to the laptop. Ignition on, battery charger connected.
2. Start `BMWRemoteTunnel-Client-vX.exe`. The first time it offers to install the free **Npcap** driver – click *Install* and then *Next* to the end.
3. Enter the server address the technician gave you and pick the network card with the cable (**Find it** picks it by itself).
4. Click **Start**. A **pairing code** appears – send it to the technician (phone, message…).

### 3. Technician connects
1. Type the code under **New client** (*Code from the client*) and connect.
2. If the client switched on *Ask me before a technician connects*, they click **Allow**.
3. Start ISTA or E-Sys and search for the vehicle as usual.

**Next time no code is needed:** both sides remember each other. The client can keep the program running (**Stay online**, **Start with Windows**) and the technician just clicks **Connect** next to the client's name.

---

## While you work

**Technician**
- **Connection quality** with a simple rating. A warning appears only when the connection is really unstable – then do not start programming (flashing).
- **Connection diagnostics:** server, client, ICOM / ENET at the client, round trip, traffic and drops at a glance.
  - **Run the test** – a short check of what the connection is good for (reading, coding, programming).
  - **Tunnel test** – loads the tunnel for 10 seconds without a car. Not available while ISTA / E-Sys are working.
- **Car** strip and **Car details** window: VIN, model, I-Step, mileage, battery voltage, fault memory of all control units (read-only – faults are never deleted), options.
- **Save report** (HTML, prints to PDF) and **Record the session** (pcap for Wireshark).
- If the ICOM at the client uses a fixed address (e.g. 192.168.x.x), the program offers to move the virtual adapter into that network with one click (**Change**).

**Both**
- **Messages** – a chat between technician and client.
- A short internet drop (up to about 45 s) does **not** end the session; both sides reconnect by themselves.
- **Diagnostic package** (bottom of the window) – one zip with the log of the session. Send it to whoever helps you with a problem.
- The client's laptop does not go to sleep during a session; both sides are warned if it runs on battery.

---

## Security and privacy

- Everything between client and technician is **end-to-end encrypted**. The server only passes encrypted data along and cannot read it.
- The pairing code is only for the first connection; after that both sides recognise each other by their own keys.
- The technician reaches only the car network, not the client's own PC.
- The automatic car reading is **read-only**. It never clears faults or writes to the car; only what you do in ISTA / E-Sys changes the car.
- Logs never contain pairing codes or keys.

## Updates

The programs check this page for new versions and offer to update. An update is accepted **only** if it is signed by the publisher – a tampered file is refused. You can also download the new version by hand.

---

## Your own server (optional)

You need a server reachable from the internet on **TCP and UDP port 23917** (UDP makes the connection faster; without it everything still works over TCP).

### Windows
1. Put `BMWRemoteTunnel-Relay-vX.exe` and `install-relay.bat` in one folder and double-click `install-relay.bat` (click *Yes* for administrator rights). The server starts at every boot and the Windows firewall is opened.
2. On the router forward **TCP 23917 and UDP 23917** to that PC (give the PC a fixed local IP).
3. Check from **another network** (e.g. a laptop on a phone hotspot) with `BMWRemoteTunnel-Check.exe`: you want *TCP open* and *UDP OPEN*.
4. Changing public IP? Use a free dynamic DNS name (No-IP, DuckDNS or the one in your router).

To remove it: `uninstall-relay.bat`.

### Linux (cloud server, Raspberry Pi)
```
sudo sh install-relay-linux.sh
```
Put the script next to the matching `bmwremotetunnel-relay-vX-linux-amd64` (or `-arm64`) file. Running it again updates the server and keeps its data. In the cloud also allow TCP and UDP 23917 in the provider's firewall (e.g. Oracle: *Security List → Ingress Rules*).

---

## Troubleshooting

| Problem | What to do |
|---|---|
| Cannot connect to the server | Check the address. From outside run `BMWRemoteTunnel-Check.exe`. Own server: port forward missing, or your internet uses CGNAT (common on mobile and some providers) – then use a cloud server. |
| ISTA does not find the ICOM | Look at **Car / Connection diagnostics** – is the ICOM / ENET seen at the client? If the program offers **Change** for the adapter address, accept it and search again in ISTA. |
| "ICOM / ENET not seen" | The client checks the cable, the ICOM power and the network card choice (**Find it**). |
| Windows / antivirus warns at the first start | The files are not signed with a paid certificate. Compare the SHA-256 of the file with `SHA256SUMS.txt` (PowerShell: `Get-FileHash <file>`). |
| Something else | Click **Diagnostic package** and send the zip together with a short description. |

---

## Good to know

- Programming (flashing) over the internet depends on your connection. Use the connection rating and the tests; keep a battery charger on the car.
- Both sides should use the same version. Older versions still connect, but new features stay off.
- This project is not affiliated with BMW AG. ISTA, E-Sys and ICOM are products of their owners. Use at your own risk.

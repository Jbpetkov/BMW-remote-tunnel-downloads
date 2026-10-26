# BMW Remote Tunnel – Downloads

Remote BMW diagnostics over your own encrypted tunnel. A technician's PC (ISTA / E-Sys) sees the ICOM or ENET cable at the customer's car as if it were plugged in locally.

**BG:** Отдалечена диагностика на BMW през собствен криптиран тунел.

## Download
Get the latest files from **Releases** (right side of this page).

| File | Who uses it |
|---|---|
| `BMWRemoteTunnel-Client-vX.exe` | the person at the car (Windows) |
| `BMWRemoteTunnel-Tech-vX.exe` | the technician (Windows) |
| `BMWRemoteTunnel-Relay-vX.exe` / `bmwremotetunnel-relay-vX-linux-*` | the server |
| `SHA256SUMS.txt` | checksums to verify the files |

## Notes
- Windows only. Needs the free Npcap driver (the client installs it for you).
- Files are not signed with a paid certificate, so Windows or antivirus may warn on first start. Check the SHA-256 against `SHA256SUMS.txt`.
- Tested against simulators; first real-car use should be read-only.
- The technician gives you the server address and a pairing code.

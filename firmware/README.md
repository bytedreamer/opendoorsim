# OpenDoorSim Firmware Guide

Welcome to the firmware folder for OpenDoorSim! The main firmware files live here.

*For full flashing instructions, please refer to the [Official Documentation Flash Guide](https://docs.shortrange.tech/opendoorsim/user-manuals/flashing-firmware).*

## Flashing Your OpenDoorSim's Firmware

OpenDoorSim's firmware can be flashed to your ESP32 device using the shell script ```./flash_board.sh``` in the parent folder, or the PlatformIO extension in your favorite IDE.






## Reader Interfaces

OpenDoorSim reads credentials over **Wiegand** (default) or **OSDP**. Only one
is brought up per boot — the D0/D1 terminals carry either the Wiegand data
lines or the RS-485 pair — so changing the interface prompts for a reboot, from
either the web UI (Settings → Reader) or the on-device menu (GENERAL → Reader).

### OSDP

OpenDoorSim acts as the ACU (controller) and the reader is the PD: it polls the
reader and card reads arrive as `osdp_RAW` replies, which feed the same
pipeline as Wiegand bits — the Wiegand formats, parity checking, user matching,
display and log all behave identically.

| Setting | Default | Notes |
| --- | --- | --- |
| PD Address | 0 | 0–126; must match the reader's configured address |
| Baud Rate | 9600 | 9600 / 19200 / 38400 / 57600 / 115200 / 230400 |

The Reader tab shows the link state (ONLINE once the reader answers a poll,
OFFLINE after 8 s of silence, per the spec).

Wiring on the v2.2 board goes through the MAX3485 transceiver on UART2:
`TX_OSDP` = GPIO17, `RX_OSDP` = GPIO16, `DE_OSDP` = GPIO4 (DE and #RE tied).

Current OSDP support is **cleartext only** — no Secure Channel — with a single
PD and card reads only. Reader identification (`osdp_ID` / `osdp_CAP`), keypad
entry, LED/buzzer commands and Secure Channel are not implemented yet.

The protocol stack is [OSDP-Embedded](https://github.com/Z-bit-Systems-LLC/OSDP-Embedded),
pinned by tag in `platformio.ini` and fetched by PlatformIO at build time.

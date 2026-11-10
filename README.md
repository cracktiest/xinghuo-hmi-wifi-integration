# Xinghuo HMI Wi-Fi Integration

**Firmware upgrade/downgrade reference for Xinghuo T1 and T5 HMIs used with industrial sewing machines.** The goal is to enable the controller's Wi-Fi network list, join a factory network, and investigate communication with **XinghuoLANServer**.

![Xinghuo HMI Wi-Fi Integration banner](images/banner.png)

> [!IMPORTANT]
> This is an independent field guide, not an official Xinghuo or JUKI service manual. Firmware must match the exact controller hardware and OEM configuration. Back up machine parameters and sewing patterns before upgrading or downgrading, use a stable power supply, and obtain an approved recovery procedure. The timings below are field estimates, not guarantees.

## Overview

There are **two HMI types: T1 and T5**. Their Wi-Fi enablement paths differ:

| | **T1 HMI** | **T5 HMI** |
| --- | --- | --- |
| Mainboard firmware update | **Required first** | Not required for the reported workflow |
| Mainboard firmware file | `TZD_CS01_V218.TFL` | — |
| HMI/interface firmware update | **Required second** | **Required** |
| HMI/interface firmware file | `XH_HMI_T1_V172.fcav` | `XH_HMI_T5_V113.xha` |
| Estimated total update time | **~10 minutes** | **~3 minutes** |
| Expected outcome | Wi-Fi scanning works and SSID list is shown | Wi-Fi SSID list is shown |

**Important distinction:** Seeing available Wi-Fi networks is **not** proof that the HMI has joined a network, obtained an IP address, or connected to XinghuoLANServer. Those are separate validation steps.

![Integration architecture](images/architecture.png)

## Firmware downloads

Obtain firmware from the official **[Xinghuo CNC Technology & Download Center](https://www.xhcnc.cn/technology.html)**, under the mainboard and human-machine-interface program categories. Confirm the downloaded file's **exact name, extension, hardware revision, and OEM compatibility** before use; the latest file listed is not necessarily appropriate for your machine.

| Target | Firmware filename | Purpose |
| --- | --- | --- |
| T1 / CS01 mainboard | `TZD_CS01_V218.TFL` | Enables the mainboard-side Wi-Fi search functionality in the reported setup |
| T1 interface | `XH_HMI_T1_V172.fcav` | Makes the Wi-Fi network list accessible on the T1 HMI |
| T5 interface | `XH_HMI_T5_V113.xha` | Makes the Wi-Fi network list accessible on the T5 HMI |

These exact filenames are based on the project's field procedure. **This repository does not include third-party firmware binaries.** A filename alone does not establish compatibility.

## Prerequisites and safety

Before changing any firmware:

1. Identify and photograph the **machine model**, **mainboard model/revision**, **HMI type (T1 or T5)**, and their currently installed firmware versions.
2. Confirm the selected package is approved for the installed hardware and OEM configuration; **never flash T1 firmware on T5 or vice versa**.
3. Export or back up machine settings, sewing patterns/templates, and other configuration through the manufacturer's supported method.
4. Keep a copy of the original firmware and the manufacturer's recovery instructions. Plan downtime and maintain uninterrupted power throughout flashing.
5. Follow the manufacturer's update method for package transfer and menu navigation. **The precise USB/update-menu steps have not yet been verified for every machine and are intentionally not invented here.**
6. Keep the machine out of production until HMI communication, machine parameters, emergency stop, homing, and a controlled test cycle have all been validated by qualified personnel.

## T1 HMI — upgrade/downgrade workflow (~10 minutes)

**Required order: mainboard first, HMI interface second.**

```text
T1 HMI + compatible CS01 mainboard
              │
              ▼
1. Mainboard firmware
   TZD_CS01_V218.TFL
              │
              ▼
2. HMI / interface firmware
   XH_HMI_T1_V172.fcav
              │
              ▼
3. Restart as required by vendor procedure
              │
              ▼
4. Open Wi-Fi settings and scan for networks
              │
              ▼
5. Verify SSID list → connect → record IP
```

**Steps:**

1. Verify that the mainboard belongs to the compatible **CS01** family and record its firmware version.
2. Using the manufacturer's approved procedure, upgrade or change the **mainboard** firmware with `TZD_CS01_V218.TFL`. Allow the update and any required restart to finish.
3. Using the approved **T1 HMI** update procedure, install `XH_HMI_T1_V172.fcav`. Allow the update and any required restart to finish.
4. Open the network/Wi-Fi settings on the T1 interface and check whether the available SSIDs appear.
5. Connect to a dedicated test network and record the results in the validation table below.

**Field observation:** An earlier T1 V174 test exposed a Wi-Fi menu but could not initially find networks; a subsequent mainboard update allowed scanning. The workflow documented in this README uses the **T1 V172** file specified for the current project. Do not assume that V172/V174/V179N are interchangeable without compatibility confirmation.

## T5 HMI — upgrade/downgrade workflow (~3 minutes)

**Only the HMI interface update is needed in the reported T5 workflow.**

```text
T5 HMI
  │
  ▼
1. HMI / interface firmware
   XH_HMI_T5_V113.xha
  │
  ▼
2. Restart as required by vendor procedure
  │
  ▼
3. Open Wi-Fi settings and scan for networks
  │
  ▼
4. Verify SSID list → connect → record IP
```

**Steps:**

1. Confirm the controller has a **T5 HMI**, and record its installed firmware version.
2. Using the manufacturer's approved T5 update procedure, install `XH_HMI_T5_V113.xha`.
3. Let the update and any required restart complete; verify the resulting HMI firmware version.
4. Open Wi-Fi settings, verify that the SSID list appears, and test connection to a dedicated network.

> [!WARNING]
> “Upgrade/downgrade” describes changing to the specified tested version, which may be higher or lower than the currently installed version. It **does not** mean every earlier or later firmware is safe to install.

## Connect to factory Wi-Fi

After the correct HMI workflow:

1. For initial testing, use an isolated **2.4 GHz Wi-Fi** access point with a simple SSID and compatible security settings, unless the module's supported radio specifications are verified otherwise.
2. Scan from the HMI and verify the expected SSID is shown.
3. Enter credentials and confirm successful association/authentication.
4. If available, record **IP address, MAC address, gateway, subnet mask**, and connection status.
5. Verify the connection from an authorized workstation or access-point logs. A failed ping does not necessarily mean Wi-Fi failed, since ICMP may be disabled.

| Verification | T1 | T5 |
| --- | --- | --- |
| Firmware versions recorded | ☐ | ☐ |
| Wi-Fi menu available | ☐ | ☐ |
| SSID scan lists networks | ☐ | ☐ |
| Joined test Wi-Fi | ☐ | ☐ |
| IP address assigned | ☐ | ☐ |
| Controller reachable, where supported | ☐ | ☐ |
| XinghuoLANServer connection confirmed | ☐ | ☐ |

## XinghuoLANServer network configuration

Once the firmware update is complete **and the HMI is connected to the local factory Wi-Fi**, configure its built-in **Network Setting** screen to point to the XinghuoLANServer. The HMI must be on a network that can reach the server; being on the exact same subnet is not required if routing and firewall rules permit access.

### Field configuration example

The following values were entered on a Xinghuo HMI on **2026-11-11**. They are **site-specific examples**, not universal defaults.

| HMI field | Example setting | Purpose |
| --- | --- | --- |
| **Network Type** | `Wireless Network` | Uses the HMI's Wi-Fi connection |
| **Machine address** | *Keep the existing value displayed by the HMI* | Identifies this machine to the Xinghuo system; do not copy another machine's identifier |
| **Server IP** | `10.5.2.223` | Factory XinghuoLANServer IP in the recorded setup |
| **Server port** | `6002` | XinghuoLANServer port configured in the recorded setup |
| **Save** | HMI button | Saves the entered configuration |
| **Login** | HMI button | Attempts to connect or log in to the configured server |

> [!NOTE]
> The `10.5.2.223:6002` address is from the example machine's configuration screen. Replace it with the actual reachable **XinghuoLANServer** IP and listening port at your site. The photo documents entered settings, **not a confirmed successful server login**. The precise server protocol and available production data are still unverified.

### Configure the HMI

1. Complete the **T1 or T5** firmware procedure above; verify that the Wi-Fi network list is available.
2. On the HMI, open **Network Setting → WiFi** and join the factory's local Wi-Fi SSID.
3. Confirm that the Wi-Fi network can reach the XinghuoLANServer (check IP routing, access-point isolation and firewall policy if necessary).
4. Return to **Network Setting** and choose **Wireless Network** in **Network Type**.
5. **Leave Machine address unchanged** unless your XinghuoLANServer administrator or the manufacturer instructs otherwise.
6. Set **Server IP** to `10.5.2.223` and **Server port** to `6002` **for this example setup**.
7. Press **Save** to store the configuration, then press **Login**.
8. Check the HMI for a connection/login success indication, and independently confirm that the machine appears as online in XinghuoLANServer.
9. Record the result, including any on-screen error, server log entry, firmware versions, and the test date.

### Connection flow

```text
JUKI sewing machine + Xinghuo controller
                │
        T1 or T5 HMI
                │
          Factory Wi-Fi
                │
       Factory LAN / routing
                │
                ▼
    XinghuoLANServer (example)
       10.5.2.223 : 6002
                │
      Confirm login / online status
                │
                ▼
   Optional monitoring / MES integration
            (not yet verified)
```

### Connection verification record

| Check | Result |
| --- | --- |
| HMI firmware version confirmed | ☐ |
| Wi-Fi SSID visible | ☐ |
| Wi-Fi authentication successful | ☐ |
| HMI obtained network connectivity | ☐ |
| `Network Type` set to `Wireless Network` | ☐ |
| Machine address preserved / recorded securely | ☐ |
| XinghuoLANServer IP and port saved | ☐ |
| **Login** completed successfully on HMI | ☐ |
| Machine shown online in XinghuoLANServer | ☐ |
| Production data accessible (if supported) | ☐ — not yet verified |

**Scope:** This guide documents firmware changes, Wi-Fi configuration, and the observed HMI server-settings screen. It does not establish whether XinghuoLANServer provides MQTT, HTTP, database access, or other interfaces. Any downstream factory application integration requires the official protocol or an authorized, validated connection method.

## Troubleshooting

| Symptom | Check |
| --- | --- |
| T1 Wi-Fi menu appears, but no networks | Verify that **CS01 mainboard firmware** was updated first and that HMI/mainboard versions are compatible; test near a 2.4 GHz access point. |
| T1 update fails or HMI does not communicate | Stop; verify OEM firmware compatibility, backups, and approved recovery instructions. |
| T5 Wi-Fi menu/list missing | Confirm the installed HMI is **T5** and the approved `XH_HMI_T5_V113.xha` update completed. |
| Network appears but connection fails | Check credentials, security settings, access-point compatibility, and signal. |
| Connected but no IP address | Check DHCP, isolation rules, and access-point logs. |
| Wi-Fi connected but XinghuoLANServer login fails | Check **Network Type**, server IP/port, server listener, routing, firewall and server-side machine registration; review HMI/server error messages. |
| IP works but no XinghuoLANServer connection | Confirm `Save` then `Login` were used and verify the correct site-specific server address/port. Obtain vendor protocol documentation if the problem persists. |

## Repository structure

```text
xinghuo-hmi-wifi-integration/
├── README.md
└── images/
    ├── banner.png
    └── architecture.png
```

There is deliberately **no `tools/` directory** and no included firmware binaries.

## Licensing, attribution & disclaimer

This repository is an **independent technical guide** and is not affiliated with, approved by, or endorsed by Xinghuo CNC or JUKI. Xinghuo firmware belongs to its respective rights holder(s); obtaining files from the official download center does **not** automatically grant redistribution or modification rights. Link to the official downloads rather than rehosting the firmware unless permission is granted.

**Repository license: none specified at present.** The absence of a repository license does not grant blanket permission to reuse this guide, and no third-party firmware is licensed by this repository.

Firmware changes can alter machine behavior. Work only on authorized equipment, follow manufacturer safety instructions, and validate all safety functions before returning the machine to service.

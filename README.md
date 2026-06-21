# Ender 3 V3 KE Happy Hare MMX MMU(Multi Material Unit)
Guide to install Happy Hare on Ender 3 V3 KE Nebula Pad with MMX MMU.
This is just what worked on my own printer. Use it at your own risk. You can break stuff or void your warranty, so make a backup of your configs before you start.
After build the MMX and flashed ebb42.
ebb42 must be flashed with klipper v0.11.0

## 1. Enable root and SSH into the printer
First enable root from the printer screen (Nebula Pad), then connect to the printer.

ssh root@here enter your printer ip, and use the password:

```bash
ssh root@192.168.8.120
Creality2023
```
If you get error try this:

```bash
ssh-keygen -R 192.168.8.120
```
Now try to connect again and type `yes`.

---

## 2. Install Creality Helper Script

```bash
cd /usr/data
git clone https://github.com/Guilouz/Creality-Helper-Script.git /usr/data/helper-script
```

Run the script:

```bash
sh /usr/data/helper-script/helper.sh
```
In the menu, must with order:

- 1 – Install  
  - 1 – Moonraker  
  - 3 – Mainsail  
  - 4 – Entware  

After that, you need to close the SSH connection and log in again, then:

```bash
sed -i '1s|.*|src/gz entware http://bin.tranducanh.com/mipselsf-k3.4|' /opt/etc/opkg.conf
opkg update
```

Start the helper script again:

```bash
sh /usr/data/helper-script/helper.sh
```

Install:

- 13 – USB camera  

After install, exit the helper script.

---

## 3. Install Happy Hare 3.3.0

Here you need to install Happy Hare 3.3.0 version:

```bash
git clone https://github.com/moggieuk/Happy-Hare.git
cd Happy-Hare
git fetch --tags
git checkout v3.3.0

/usr/data/opt/bin/opkg update
/usr/data/opt/bin/opkg install bash

bash install.sh -i
```
Then select these from Happy Hare menu:

```text
13
4
12
y
n
1
y
y
n
y
n
n
```

Now reboot the printer and open Mainsail page for the printer:
```bash
http://192.168.8.120:4409
```

---

## 4. Fix LED error and mmu_vars

In Mainsail, open `printer.cfg` and comment these lines:

```ini
#[include mmu/base/*.cfg]
#[include mmu/optional/client_macros.cfg]
```

Click Save and restart, then SSH login again.

Fix led error:
```bash
wget -O /usr/share/klipper/klippy/extras/led.py \
  https://raw.githubusercontent.com/Klipper3d/klipper/master/klippy/extras/led.py
```
Fix `mmu_vars.cfg` error:

In `mmu_macro_vars.cfg`, under `[save_variables]` set:

```ini
filename: /usr/data/printer_data/config/mmu/mmu_vars.cfg
```

Click save and restart.

---

## 5. Happy Hare calibration

### Gear calibration (per gate)

```gcode
MMU_SELECT GATE=0
MMU_TEST_MOVE MOVE=100
```

Measure how much filament moved. Example: 98 mm. Then:

```gcode
MMU_CALIBRATE_GEAR MEASURED=98
```

Repeat for each gate (GATE=1,2,3).

### Selector angles

Calibrate each angle for gate
```gcode
MMU_CALIBRATE_SELECTOR ANGLE=0
MMU_CALIBRATE_SELECTOR ANGLE=60
MMU_CALIBRATE_SELECTOR ANGLE=120
MMU_CALIBRATE_SELECTOR ANGLE=180
```

Put those values into `mmu_vars.cfg` in the mmu_selector_angles.

---

## 6. Install Guppy Screen
Start Script helper:

```bash
sh /usr/data/helper-script/helper.sh
```
- 1 – Install  
  - 5 – Klipper Gcode shell command  
 
Back then:

- 3 – Customize  
  - 3 – Guppy Screen  
  - choose `release` and answer `y`

If you want to rotate Guppy Screen, go to `/usr/data/guppyscreen` and edit `guppyconfig.json`, change:

```json
"display_rotate": 0
```

to:

```json
"display_rotate": 2
```

---

## 7. Automatic z offset, bed mesh, and gate filament set

Automatic Z offset:

```gcode
Z_COMPENSATE_TEST
SAVE_CONFIG
```

Bed mesh:

```gcode
G29
SAVE_CONFIG
```

Filament example:

```gcode
MMU_GATE_MAP GATE=0 MATERIAL=ABS COLOR=black TEMP=255 NAME="ABS Black"
```

Do the same for your other gates/materials.

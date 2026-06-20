# ender-3-v3-ke-happy-hare-mmx-mmu
Guide to install Happy Hare on Ender 3 V3 KE Nebula Pad with MMX MMU.
This is just what worked on my own printer. Use it at your own risk. You can break stuff or void your warranty, so make a backup of your configs before you start.

## 1. Enable root and SSH into the printer
first enable the root from the printer screen nebula pad, then connect to the printer:

ssh root@here enter your printer ip, and use the password:

```bash
ssh root@192.168.8.120
Creality2023
```
if you get error try this:

```bash
ssh-keygen -R 192.168.8.120
```
now try to connect again and type yes

## 2. Install Creality Helper Script

```bash
cd /usr/data
git clone https://github.com/Guilouz/Creality-Helper-Script.git /usr/data/helper-script
```
after install run
```bash
sh /usr/data/helper-script/helper.sh
```
now install these with order:
- 1 = moonraker  
- 3 = mainsail  
- 4 = entware 

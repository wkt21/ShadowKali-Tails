# ShadowKali-Tails

**Tails-inspired amnesic Kali Linux live system**  
*by Wkt12*



ShadowKali-Tails is a hardened live-build variant of Kali Linux that implements the core security model of Tails:

1. **Amnesic operation** – no persistent writes by default (everything in RAM)
2. **Forced Tor routing** – all traffic goes through Tor (fail-closed nftables)
3. **Mandatory Access Control** – AppArmor enabled and enforced
4. **No persistent logs** – journald volatile only
5. **Cryptographically verified base** – builds from official Kali live-build
6. **Memory erasure helpers** – free-memory wipe on shutdown + kernel poisoning
7. **Onion service support** – ephemeral by default, optional persistent keys when encrypted persistence is unlocked

## Quick Start (Build)

```bash
# On a Kali system
sudo apt update
sudo apt install -y git live-build cdebootstrap curl

git clone https://gitlab.com/kalilinux/build-scripts/kali-live.git
cd kali-live

# Drop this repository's kali-config/variant-amnesic into place
cp -a /path/to/ShadowKali-Tails/kali-config/variant-amnesic kali-config/

./build.sh --distribution kali-rolling --variant amnesic --verbose


**All files are ready** in the local scaffold.  
As soon as the GitHub connector has write permission, I can push the entire tree in one commit.

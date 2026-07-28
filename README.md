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
```

## Kernel command-line recommendations

Add to the live boot entry:

```
init_on_free=1 page_poison=1 slab_nomerge pti=on
```

These strengthen the kernel’s own free-page poisoning.

## Structure

```
ShadowKali-Tails/
├── README.md
├── LICENSE
└── kali-config/
    └── variant-amnesic/
        ├── package-lists/
        │   └── kali.list.chroot
        ├── hooks/
        │   └── live/
        │       ├── 00-prepare.chroot
        │       ├── 01-apparmor.chroot
        │       ├── 02-tor-proxy.chroot
        │       ├── 03-amnesic.chroot
        │       ├── 04-memory-wipe.chroot
        │       ├── 05-onion-service.chroot
        │       └── 06-persistent-onion.chroot
        └── includes.chroot/
            ├── etc/
            │   ├── tor/torrc
            │   ├── nftables.conf
            │   ├── sysctl.d/99-amnesic.conf
            │   ├── systemd/
            │   │   ├── journald.conf.d/volatile.conf
            │   │   └── system/
            │   │       ├── memory-wipe.service
            │   │       └── shadowkali-onion-persist.service
            │   └── NetworkManager/conf.d/99-tor.conf
            └── usr/local/sbin/
                ├── wipe-memory
                └── shadowkali-onion-persist
```

## Notes

- Onion service private keys are generated in RAM by default and disappear on shutdown.
- Optional encrypted persistence can keep a stable `.onion` address.
- Only v3 onion services are used.
- Unix socket backend preferred for the onion service to avoid local TCP leaks.
- Fail-closed nftables: only the Tor daemon may talk to the clearnet.

Built from the official Kali live-build scripts.

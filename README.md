# ShadowKali-Tails

<img width="1536" height="1024" alt="IMG_5570" src="https://github.com/user-attachments/assets/a25b623a-4931-4331-a56a-3fea06fd2b78" />


**Tails-inspired amnesic Kali Linux live system**  
*by Wkt12 / wkt21*

ShadowKali-Tails is a hardened live-build variant of Kali Linux that implements the core security model of Tails:

1. **Amnesic operation** – no persistent writes by default (everything in RAM)
2. **Forced Tor routing** – all traffic goes through Tor (fail-closed nftables)
3. **Mandatory Access Control** – AppArmor enabled
4. **No persistent logs** – journald volatile only
5. **Cryptographically verified base** – builds from official Kali live-build
6. **Memory erasure helpers** – free-memory wipe on shutdown + kernel page poisoning
7. **Onion service support** – v3, ephemeral by default; optional persistent keys when encrypted persistence is unlocked

> **This is not official Tails or official Kali.** It is a community research / personal variant. Use at your own risk. Always verify the build on a trusted machine.

## Quick Start (Build)

```bash
# On a Kali (or Debian) system with enough disk & RAM
sudo apt update
sudo apt install -y git live-build cdebootstrap curl

git clone https://gitlab.com/kalilinux/build-scripts/kali-live.git
cd kali-live

# Copy the variant
cp -a /path/to/ShadowKali-Tails/kali-config/variant-amnesic kali-config/

# Build
./build.sh --distribution kali-rolling --variant amnesic --verbose
```

The resulting ISO will appear under `images/`.

## Recommended kernel command line

Add these parameters to the live boot entry (isolinux/grub):

```
init_on_free=1 page_poison=1 slab_nomerge pti=on
```

They strengthen the kernel’s free-page poisoning (modern replacement for older multi-process sdmem approaches).

## Architecture overview

| Component              | Mechanism                                                                 |
|------------------------|---------------------------------------------------------------------------|
| Forced Tor             | nftables NAT redirect of all non-tor TCP + DNS to Tor TransPort/DNSPort   |
| Fail-closed            | filter policy drop; only `debian-tor` UID may leave the host              |
| Amnesic                | tmpfs for /tmp, /var/tmp, /var/log; swap masked; history disabled         |
| Memory wipe            | systemd oneshot on shutdown + `sdmem` (or python mmap fallback)           |
| Onion service          | Tor v3 HiddenService, Unix socket backend, keys ephemeral unless persist  |
| Persistence (optional) | Helper detects live-boot persistence and bind-mounts keys                 |

## Onion service usage

By default the Hidden Service is configured but **no backend application** is installed (no nginx, no web server). Tor will publish a `.onion` address; clients connecting to it will receive a connection-refused until you place a listener on the Unix socket:

```
/run/tor/shadowkali_hs.sock
```

or change the `HiddenServicePort` line in `/etc/tor/torrc` to a TCP port and run your own service bound to `127.0.0.1`.

When encrypted persistence is unlocked the script `shadowkali-onion-persist` will keep the same `.onion` address across reboots.

## Repository layout

```
ShadowKali-Tails/
├── README.md
├── LICENSE
├── .gitignore
└── kali-config/
    └── variant-amnesic/
        ├── package-lists/kali.list.chroot
        ├── hooks/live/
        │   ├── 00-prepare.chroot
        │   ├── 01-apparmor.chroot
        │   ├── 02-tor-proxy.chroot
        │   ├── 03-amnesic.chroot
        │   ├── 04-memory-wipe.chroot
        │   ├── 05-onion-service.chroot
        │   └── 06-persistent-onion.chroot
        └── includes.chroot/
            ├── etc/tor/torrc
            ├── etc/nftables.conf
            ├── etc/sysctl.d/99-amnesic.conf
            ├── etc/systemd/...
            └── usr/local/sbin/
                ├── wipe-memory
                └── shadowkali-onion-persist
```

## Limitations & warnings

- This is a **research / personal** configuration, not a audited product like Tails.
- IPv6 is intentionally blocked.
- Full cold-boot protection is best-effort; use the recommended kernel parameters.
- Onion service has no default application backend – you must supply one.
- Always build on a trusted machine and verify the resulting ISO checksums.
- NetworkManager is configured with `dns=none`; DNS goes only through Tor.

## License

See `LICENSE`.

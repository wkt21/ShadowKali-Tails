ShadowKali-Tails/
├── README.md
├── LICENSE                          (already present on GitHub)
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
            │   ├── tor/
            │   │   └── torrc
            │   ├── nftables.conf
            │   ├── sysctl.d/
            │   │   └── 99-amnesic.conf
            │   ├── systemd/
            │   │   ├── journald.conf.d/
            │   │   │   └── volatile.conf
            │   │   └── system/
            │   │       ├── memory-wipe.service
            │   │       └── shadowkali-onion-persist.service
            │   └── NetworkManager/
            │       └── conf.d/
            │           └── 99-tor.conf
            ├── usr/
            │   └── local/
            │       └── sbin/
            │           ├── wipe-memory
            │           └── shadowkali-onion-persist
            └── var/
                └── www/
                    └── onion/
                        └── (index.html generated at build time)

MiniBrowser Linux

A bootable Linux distro that fits in ~11 MB and does exactly one thing:
it boots straight into a graphical web browser (links2).

No installer, no desktop, no systemd, no X11 – just turn it on and surf the web.

Built mostly for fun, and tested on Android via the Limbo PC emulator.
This distro was created by Anthropic's Claude 5 AI agent in about 2 hours.

Features

· 🪶 11 MB ISO – Linux 6.12 (i386) + BusyBox + links2, nothing else
· 🌐 Graphical browser in framebuffer – no X11, drawn directly to the screen
· 🔒 HTTPS works with proper certificate verification
· 🖱️ Mouse support (gpm over PS/2) – on touchscreens it behaves as a relative trackpad
· 📡 Zero‑config networking – DHCP at boot
· ⌨️ Start page doubles as a cheat sheet with exact controls
· 🔧 Rescue shell on tty2 (Ctrl+Alt+F2)

Limitation: no JavaScript (that's why it's 11 MB).
Wikipedia, DuckDuckGo Lite, Hacker News, Flibusta etc. work great; Gmail and JS‑heavy apps don't.

Download

The ISO file minibrowser.iso is available directly in this repository.

Run it on Android (Limbo)

Recommended settings (tested by the author):

Section Setting
User Interface Display: SDL, Keyboard: en-us, Mouse: ps2
Board Machine Type: pc, CPU: qemu32, 1 CPU, RAM: 160 MB, Disable TSC checked
Removable CDROM: select minibrowser.iso
Graphics Video Card: std
Network Network: User, NIC Card: virtio, DNS Server: 8.8.8.8

Typing on a phone? Stock keyboards often send text input that emulators drop.
Use Hacker's Keyboard or Unexpected Keyboard – they send real key events.

Controls

Action Keys / Mouse
Follow link Left‑click, Enter, or →
Link menu Right‑click
Main menu Click top bar, or Esc
Back ← or z
Forward ' or x (NOT →)
Select link ↑ / ↓
Scroll page PgUp / PgDn (or Ctrl+B / Ctrl+F), mouse wheel
Search on page /, then n / N for next/prev
Go to URL g
Bookmarks s
Reload Ctrl+R
Quit q (browser restarts automatically)

How it's built

```
iso/
├── boot/vmlinuz          # Linux 6.12, i386/M686, monolithic
├── boot/initramfs.gz     # entire root filesystem
└── isolinux/             # ISOLINUX bootloader

initramfs/
├── init                  # mounts /proc → DHCP → gpm → links2 loop
├── bin/                  # static busybox (musl, i686)
├── usr/bin/links         # links2 2.30, framebuffer graphics
├── sbin/gpm              # mouse daemon
└── lib/                  # glibc i386 + libjpeg/libpng/zlib/openssl/gpm (zstd for libcrypto)
```

The root filesystem is packed into a cpio archive and gzipped.
The kernel is configured with allnoconfig plus essential drivers (DRM/fb, e1000/rtl8139/virtio‑net, PS/2 input, 32‑bit time compat).
links2 is built with --enable-graphics --with-fb --with-ssl.
# University Management Network (Cisco Packet Tracer)

A beginner-friendly campus network project using standard Packet Tracer devices and services. The project demonstrates two routed LANs, DHCP and DHCP relay, DNS, an HTTP university portal, and email.

## Topology and addressing

- Cisco 2911 router, two Cisco 2960 switches, four Server-PT devices, and five PCs (PC0–PC4).
- Server LAN on SW1: `192.168.2.0/24`, gateway `192.168.2.1`.
- Client LAN on SW2: `192.168.1.0/24`, gateway `192.168.1.1`.
- DHCP server: `192.168.2.2`; DNS server: `192.168.2.3`; web server: `192.168.2.4`; mail server: `192.168.2.5`.
- DHCP relay on R1 forwards client LAN requests to `192.168.2.2`; each DHCP pool allows up to 200 leases.
- Portal DNS: `portal.campus.local` → `192.168.2.4`.
- Email domain: `campus.com` (Packet Tracer's email client rejected the `.local` domain in this project).

## Files

- [`STEP_BY_STEP.md`](STEP_BY_STEP.md): device placement, cabling, service configuration, and verification.
- [`configs/router-config.txt`](configs/router-config.txt): router interface and DHCP relay configuration.
- [`dashboard.html`](dashboard.html): static portal page to paste into the Packet Tracer Web Server's `index.html`.
- [`topology.svg`](topology.svg): logical network diagram.

The Packet Tracer HTTP service serves a static page; it does not run a shared marks database or server-side application. The `.pkt` simulation file is not included in this repository yet.

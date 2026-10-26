# University Management Network (Cisco Packet Tracer)

A campus network prototype demonstrating routed Administration/Services, Student, and Library/Research networks with DHCP, DHCP relay, DNS, a university portal, email, and student Wi-Fi.

## Portal dashboard

`dashboard.html` is a static front-end prototype with:

- Student portal navigation and campus service cards
- Searchable sample library catalogue with demo request buttons
- Examination marks lookup using fictional records (`STU1001`–`STU1003`)
- Admin sign-in interface prototype

The Packet Tracer HTTP service only serves static files. It cannot save shared marks or library requests, run a database, or authenticate the admin form against SMTP/POP3 email accounts. The marks are fictional demo data in the page source; do not add real student records or passwords. A real ERP requires an application backend and database with server-side authentication and authorization.

To use the page in Packet Tracer, open the Web Server → **Services → HTTP**, replace `index.html` with `dashboard.html`, and save. The portal hostname is `portal.campus.local`.

## Network addressing

- Administration and services: `192.168.2.0/24`; gateway `192.168.2.1`; DHCP/DNS Server0 `192.168.2.2`; web/email Server2 `192.168.2.4`.
- Students and Wi-Fi: `192.168.1.0/24`; gateway `192.168.1.1`.
- Library and research: `192.168.3.0/24`; gateway `192.168.3.1`.
- Student and Library DHCP requests are relayed by R1 to `192.168.2.2`.

See [`STEP_BY_STEP.md`](STEP_BY_STEP.md) for the initial build guide and [`configs/router-config.txt`](configs/router-config.txt) for the original two-LAN router setup. The guide/config predate the later Library LAN and server consolidation; update them to match the final Packet Tracer file before presenting.

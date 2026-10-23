# Build the University Network Services Project

This walkthrough uses the standard Packet Tracer devices and service setup shown in the supplied assignments. Build the two LANs first, then add and test the application services.

## 1. Place devices

In a new Cisco Packet Tracer file, place:

- 1 router: Cisco 2911 (a standard 1941/2911 router is suitable)
- 2 switches: Cisco 2960, named SW1 and SW2
- 4 Server-PT devices: DHCP Server, DNS Server, Web Server, Mail Server
- 5 PC-PT devices: PC0, PC1, PC2, PC3, PC4

Use the normal 2960 access switches. No special switch features or extra network layers are needed.

## 2. Cable the topology

Use **Connections → Copper Straight-Through** for each link:

| Device A | Port | Device B | Port |
|---|---|---|---|
| R1 | GigabitEthernet0/0 | SW1 | FastEthernet0/24 |
| R1 | GigabitEthernet0/1 | SW2 | FastEthernet0/24 |
| DHCP Server | FastEthernet0 | SW1 | FastEthernet0/1 |
| DNS Server | FastEthernet0 | SW1 | FastEthernet0/2 |
| Web Server | FastEthernet0 | SW1 | FastEthernet0/3 |
| Mail Server | FastEthernet0 | SW1 | FastEthernet0/4 |
| PC0 | FastEthernet0 | SW1 | FastEthernet0/5 |
| PC1 | FastEthernet0 | SW2 | FastEthernet0/1 |
| PC2 | FastEthernet0 | SW2 | FastEthernet0/2 |
| PC3 | FastEthernet0 | SW2 | FastEthernet0/3 |
| PC4 | FastEthernet0 | SW2 | FastEthernet0/4 |

Keep the switches at their default Layer 2 settings. Wait for the connected links to turn green.

## 3. Configure R1

Click R1 → **CLI**, press Return, and enter the commands in `configs/router-config.txt`. This gives the router one directly connected interface for each LAN and configures DHCP relay on the client-facing side.

The addresses are:

- R1 `GigabitEthernet0/0`: `192.168.2.1/24`
- R1 `GigabitEthernet0/1`: `192.168.1.1/24` (mask `255.255.255.0`)
- DHCP server: `192.168.2.2/24`

Save the router configuration with `copy running-config startup-config` (already included in the file). The router does not need a dynamic routing protocol because both LANs are directly connected.

## 4. Set static addresses on the servers

For each server, click **Desktop → IP Configuration → Static** and enter the following values. Use mask `255.255.255.0` for all four.

| Server | IP address | Default gateway | DNS server |
|---|---|---|---|
| DHCP Server | 192.168.2.2 | 192.168.2.1 | 192.168.2.3 |
| DNS Server | 192.168.2.3 | 192.168.2.1 | 192.168.2.3 |
| Web Server | 192.168.2.4 | 192.168.2.1 | 192.168.2.3 |
| Mail Server | 192.168.2.5 | 192.168.2.1 | 192.168.2.3 |

## 5. Configure DHCP

1. Click DHCP Server → **Services → DHCP** and turn the service **On**.
2. Edit the default `serverPool` for the server LAN: default gateway `192.168.2.1`, DNS `192.168.2.3`, start IP `192.168.2.10`, subnet mask `255.255.255.0`, maximum users `200`. Click **Save**. This pool is for the server LAN and PC0.
3. Add a second pool named `clientPool` for university users on the client LAN: default gateway `192.168.1.1`, DNS `192.168.2.3`, start IP `192.168.1.10`, subnet mask `255.255.255.0`, maximum users `200`. Click **Add**. This gives the client LAN 200 leases, from `192.168.1.10` through `192.168.1.209`, with addresses left over for future static devices.
4. On PC0, select **Desktop → IP Configuration → DHCP**. It should receive an address in `192.168.2.0/24`.
5. On PC1, select DHCP. Before relay is configured, it should fail to obtain an address. This is the demonstration that DHCP broadcasts do not cross the router by themselves.
6. Confirm R1's `GigabitEthernet0/1` has address `192.168.1.1` with mask `255.255.255.0` and `ip helper-address 192.168.2.2` (they are in the supplied config). Renew the DHCP request on PC1. It should receive an address in `192.168.1.0/24`. Configure PC2, PC3, and PC4 the same way.

## 6. Configure DNS and the dashboard web page

1. On DNS Server, open **Services → DNS**, turn DNS **On**, and add these A records:

   - `portal.campus.local` → `192.168.2.4`
   - `mail.campus.local` → `192.168.2.5`

2. On Web Server, open **Services → HTTP** and turn HTTP **On**.
3. Edit the existing `index.html` (or create `index.html`), paste the contents of [dashboard.html](dashboard.html), and click **Save**. Accept the replacement prompt if shown.
4. On PC1, open **Desktop → Web Browser** and enter `http://portal.campus.local`. The name should resolve to the Web Server, and its dashboard page should load. If this fails, try `http://192.168.2.4` first to separate web-server issues from DNS issues.

The dashboard is a static project page. It is served through the simulated network and shows the addressing plan and service checks, but it does not automatically read live router counters. Use the command line and client tests below to demonstrate live state.

## 7. Configure email

1. On Mail Server, open **Services → EMAIL**. Turn **SMTP** and **POP3** on, set the email domain to `campus.com` (the Packet Tracer email client rejected `.local` in this build), and click **Set**.
2. Add the four accounts you created: `sai`, `parth`, `darin`, and `rehan`, each with its own password. Do not put account passwords in this repository. Add the four accounts you created: `sai`, `parth`, `darin`, and `rehan`, each with its own password. Do not put account passwords in this repository.
3. On PC0, open **Desktop → Email → Configure Mail**:
   - Your Name: Sai
   - Email Address: `sai@campus.com`
   - Incoming Mail Server: `192.168.2.5`
   - Outgoing Mail Server: `192.168.2.5`
   - User Name: `sai`
   - Password: the password you assigned to `sai`
4. On PC1, configure the same fields for `parth@campus.com`, user name `parth`, the password assigned to `parth`.
5. From PC0, compose and send a message to `parth@campus.com`. On PC1, click **Receive** and show the message.

If DNS hostname lookup for mail is troublesome, use `192.168.2.5` for the incoming and outgoing server fields while you debug the DNS record.

## 8. Verify and capture results

From PC1's **Desktop → Command Prompt**, run:

1. `ipconfig` — check it has a `192.168.1.x` address, gateway `192.168.1.1`, and DNS `192.168.2.3`.
2. `ping 192.168.1.1` — test the local router interface.
3. `ping 192.168.2.2` and `ping 192.168.2.3` — test the DHCP and DNS servers.
4. `ping portal.campus.local` — show name resolution plus connectivity to the web server.
5. Open `http://portal.campus.local` in the browser.
6. Show the email sent from PC0 and received on PC1.

On R1, run `show ip interface brief` and `show running-config` to show the two LAN interfaces and the DHCP helper command. Capture screenshots of the topology, DHCP pools, a client lease, the dashboard in a browser, the email received, and the successful ping results.

Save the project with **File → Save As** as `University_Network_Services.pkt`.

## Troubleshooting

- Client gets `169.254.x.x`: verify the DHCP service is On, the client pool mask is `255.255.255.0`, and R1 `GigabitEthernet0/1` has `ip helper-address 192.168.2.2`.
- PC0 works but PC1 does not get a lease: confirm `clientPool` uses gateway `192.168.1.1`, start IP `192.168.1.10`, mask `255.255.255.0`, and the helper points to `192.168.2.2`.
- Ping by address works but the hostname does not: verify DNS is On, the A record is spelled correctly, and each DHCP pool advertises DNS `192.168.2.3`.
- Dashboard is blank or shows the default page: open Web Server → Services → HTTP and save the content to `index.html`.
- Email won't send: check SMTP/POP3 are On, the clients use the same `campus.com` domain configured on the mail server, and usernames/passwords match the Mail Server accounts.

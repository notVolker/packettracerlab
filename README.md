# Packet Tracer Lab Roadmap — IT Support / Network Engineer Prep

# Manifesting for a NOC/Network Role, BIGAY NIYO NA SA'KIN 'TO

# First NOC Interview done!

A progressive set of labs, from basic connectivity to a full multi-service network.
Each lab lists: **Goal**, **Topology**, **Tasks**, and **Verification** (what to screenshot for your GitHub repo).

## Progress Tracker

- [x] Lab 1: Basic PC-to-PC Connectivity
- [ ] Lab 2: Star Topology with a Switch
- [ ] Lab 3: Basic Switch CLI Configuration
- [ ] Lab 4: VLANs
- [ ] Lab 5: Trunking Between Switches (802.1Q)
- [ ] Lab 6: Inter-VLAN Routing (Router-on-a-Stick)
- [ ] Lab 7: Spanning Tree Protocol (STP)
- [ ] Lab 8: Static Routing
- [ ] Lab 9: Default Routes
- [ ] Lab 10: Dynamic Routing — RIP
- [ ] Lab 11: Dynamic Routing — OSPF (Single Area)
- [ ] Lab 12: Dynamic Routing — EIGRP
- [ ] Lab 13: DHCP Server (Router-based)
- [ ] Lab 14: DHCP Relay (Across Subnets)
- [ ] Lab 15: DNS Server
- [ ] Lab 16: Web, FTP, and Email Servers
- [ ] Lab 17: Standard ACLs
- [ ] Lab 18: Extended ACLs
- [ ] Lab 19: Port Security
- [ ] Lab 20: SSH Remote Management
- [ ] Lab 21: NAT / PAT (Internet Simulation)
- [ ] Lab 22: WAN Link with Serial/PPP
- [ ] Lab 23: Full Enterprise Network Simulation (Capstone)

---

## LEVEL 1 — Fundamentals

### Lab 1: Basic PC-to-PC Connectivity ✅
**Goal:** Understand IP addressing and cabling basics.
- 2 PCs connected directly with a copper straight-through cable (Packet Tracer auto-detects, but try crossover too and note the difference — real hardware needs crossover for direct PC-PC).
- Assign static IPs in the same subnet (e.g., 192.168.1.10/24 and 192.168.1.20/24).
- Test with `ping` from the command prompt on each PC.
- Then break it: put them in different subnets and see the failure, then fix it.

**Verify:** Successful ping; screenshot of `ipconfig` on both PCs.

**Result:** Initial ping to PC-B failed (different subnet during testing), then succeeded after correcting the IP and cable type back to the same subnet with a crossover cable.

<img width="1235" height="1038" alt="image" src="https://github.com/user-attachments/assets/b5501db3-df92-4956-bd9a-0fb9cf4ad9cf" />

### Lab 2: Star Topology with a Switch
**Goal:** Learn basic LAN topology.
- 1 switch, 4–5 PCs, static IPs on the same subnet.
- All devices ping each other.
- Open the switch's MAC address table (`show mac address-table` in CLI) to see it learning MACs as traffic flows.

**Verify:** Full ping matrix; MAC table screenshot.

**Result:** All 5 PCs successfully pinged each other with 0% packet loss, confirming full connectivity through the switch. The MAC address table was empty before generating traffic and populated with each PC's MAC-to-port mapping only after pings occurred — confirming that switches learn MAC addresses dynamically from observed traffic rather than static configuration.

<img width="954" height="1007" alt="image" src="https://github.com/user-attachments/assets/ce8d73a6-e5f1-4919-bc91-f1ed6df666a7" />

<img width="956" height="1012" alt="image" src="https://github.com/user-attachments/assets/217e03ba-7f00-40d9-b860-e871d41d873d" />

<img width="959" height="1021" alt="image" src="https://github.com/user-attachments/assets/a34e8c9f-79a1-4c4f-92f1-a80705ac8e80" />

### Lab 3: Basic Switch CLI Configuration
**Goal:** Get comfortable with Cisco IOS basics.
- Console into the switch.
- Configure: hostname, banner MOTD, enable secret, console password, vty (telnet) password, `service password-encryption`.
- Assign a management IP (SVI on VLAN 1) so you can reach it via Telnet.
- Save config (`copy run start`) and verify it persists after reload.

**Verify:** `show running-config` screenshot; successful Telnet login from a PC.

---

## LEVEL 2 — Switching

### Lab 4: VLANs
**Goal:** Segment a LAN logically.
- 1 switch, 6 PCs.
- Create VLAN 10 (Sales) and VLAN 20 (IT).
- Assign 3 ports to each VLAN (`switchport mode access`, `switchport access vlan X`).
- Confirm PCs in the same VLAN can ping each other, but PCs in different VLANs cannot (yet).

**Verify:** `show vlan brief` output; ping matrix showing isolation.

### Lab 5: Trunking Between Switches (802.1Q)
**Goal:** Carry multiple VLANs across switches.
- 2 switches connected by one link, configured as a trunk (`switchport mode trunk`).
- Same VLANs (10 and 20) exist on both switches with PCs on each.
- Verify PCs in VLAN 10 on Switch A can reach VLAN 10 PCs on Switch B.

**Verify:** `show interfaces trunk`; cross-switch ping success within same VLAN.

### Lab 6: Inter-VLAN Routing (Router-on-a-Stick)
**Goal:** Let VLANs talk to each other via a router.
- Switch with VLANs 10/20, trunk link to a router's single interface.
- Configure subinterfaces on the router (e.g., `Gig0/0.10`, `Gig0/0.20`) with `encapsulation dot1Q`.
- Each VLAN gets a subinterface as its default gateway.
- Test PC in VLAN 10 pinging PC in VLAN 20.

**Verify:** `show ip interface brief`; successful cross-VLAN ping; traceroute showing router hop.

*Bonus:* Redo this with a Layer 3 switch and SVIs instead of router-on-a-stick — compare configs.

### Lab 7: Spanning Tree Protocol (STP)
**Goal:** Understand loop prevention.
- 3 switches connected in a triangle (redundant links).
- Observe STP electing a root bridge and blocking a port automatically.
- Manually influence root bridge election with `spanning-tree vlan 1 priority 0`.
- Unplug the active link and watch STP reconverge.

**Verify:** `show spanning-tree` before/after; screenshot of blocked port.

---

## LEVEL 3 — Routing

### Lab 8: Static Routing
**Goal:** Manually route between networks.
- 3 routers in a line (R1—R2—R3), each with a LAN of PCs, different subnets.
- Configure static routes on each router so all LANs can reach each other.
- Try a partial config first and see routing fail for unreachable networks, then fix it.

**Verify:** `show ip route`; full ping matrix across all LANs.

### Lab 9: Default Routes
**Goal:** Simplify edge routing.
- Same topology, but on edge routers use `ip route 0.0.0.0 0.0.0.0 <next-hop>` instead of explicit routes for the "rest of the internet."

**Verify:** Compare routing table size vs. Lab 8.

### Lab 10: Dynamic Routing — RIP
**Goal:** Learn distance-vector routing.
- Same 3-router topology, remove static routes, configure RIPv2.
- Watch routers auto-learn neighboring networks.

**Verify:** `show ip protocols`; `show ip route` showing R-marked routes.

### Lab 11: Dynamic Routing — OSPF (Single Area)
**Goal:** Learn link-state routing (most commonly tested in interviews).
- Same topology, configure OSPF area 0 on all routers.
- Set router-ids explicitly.
- Check neighbor adjacencies.

**Verify:** `show ip ospf neighbor`; `show ip route` showing O-marked routes.

### Lab 12: Dynamic Routing — EIGRP
**Goal:** Compare Cisco's hybrid protocol.
- Same topology again with EIGRP.
- Note the differences in configuration effort vs. OSPF and RIP.

**Verify:** `show ip eigrp neighbors`; route table screenshot.

**Reflection doc idea:** Write a short comparison table (RIP vs OSPF vs EIGRP: convergence speed, scalability, config complexity) — great for interview talking points.

---

## LEVEL 4 — Core Network Services

### Lab 13: DHCP Server (Router-based)
**Goal:** Automate IP assignment.
- Configure a router as a DHCP server (`ip dhcp pool`, network, default-router, dns-server).
- Set PCs to "DHCP" instead of static.
- Exclude a range of addresses for static devices (servers/printers).

**Verify:** `ipconfig /all` on PC showing leased address; `show ip dhcp binding`.

### Lab 14: DHCP Relay (Across Subnets)
**Goal:** Handle DHCP when server isn't on the same LAN.
- DHCP server/pool lives on Router A; PCs are on a LAN behind Router B.
- Configure `ip helper-address` on Router B's LAN interface.

**Verify:** PCs on the remote LAN successfully get an IP from the central DHCP server.

### Lab 15: DNS Server
**Goal:** Resolve names to IPs.
- Add a Server device, configure DNS service, add an A record (e.g., `www.mycompany.local` → server's IP).
- Configure PCs' DNS server address (via DHCP option or static).
- Test with `ping www.mycompany.local` and a browser request in Packet Tracer.

**Verify:** Successful name resolution screenshot.

### Lab 16: Web, FTP, and Email Servers
**Goal:** Simulate common enterprise services.
- On a Server, enable HTTP, FTP, and Email (SMTP/POP3) services.
- Access the web page from a PC browser.
- Transfer a file via FTP from PC command line.
- Configure two email accounts and send a message between them (using an Email client on PCs).

**Verify:** Screenshots of browser page load, FTP transfer log, sent/received email.

---

## LEVEL 5 — Security

### Lab 17: Standard ACLs
**Goal:** Basic traffic filtering by source IP.
- Block one specific PC/subnet from reaching a server while allowing others.
- Apply ACL as close to the destination as best practice dictates.

**Verify:** `show access-lists`; ping fail from blocked host, success from allowed host.

### Lab 18: Extended ACLs
**Goal:** Filter by protocol/port (e.g., allow HTTP but block Telnet to a server).
- Create extended ACL permitting TCP/80 but denying TCP/23 to a specific server IP.
- Apply on the correct interface/direction (as close to source as best practice).

**Verify:** Browser access works, Telnet blocked — show both attempts.

### Lab 19: Port Security
**Goal:** Prevent unauthorized devices on switch ports.
- Configure `switchport port-security` with a MAC limit of 1, sticky learning, and a violation action (shutdown).
- Simulate a second device plugging into the port and trigger the violation.

**Verify:** `show port-security interface`; port in err-disabled state screenshot.

### Lab 20: SSH Remote Management
**Goal:** Replace insecure Telnet.
- Configure hostname, domain-name, RSA keys, local user database, and `transport input ssh` on vty lines.
- Disable Telnet.
- Connect from a PC using the SSH client.

**Verify:** Successful SSH session; failed Telnet attempt.

---

## LEVEL 6 — WAN & NAT

### Lab 21: NAT / PAT (Internet Simulation)
**Goal:** Understand address translation — one of the most interview-relevant topics.
- Internal LAN with private addressing (192.168.x.x), router connects to an "ISP" router with public IP space.
- Configure PAT (`ip nat inside source list ... interface ... overload`) so all internal hosts share one public IP.
- Also try static NAT for a "public-facing server."

**Verify:** `show ip nat translations`; ping/browse from internal PC to outside server through NAT.

### Lab 22: WAN Link with Serial/PPP
**Goal:** Simulate a WAN connection between sites.
- Two routers connected via serial interface (simulate a leased line).
- Configure PPP encapsulation with CHAP authentication.
- Add routing (static or OSPF) to reach across the WAN link.

**Verify:** `show interfaces serial` showing line/protocol up; `show ppp` authentication success.

---

## LEVEL 7 — Capstone

### Lab 23: Full Enterprise Network Simulation
**Goal:** Combine everything into one design — this is your portfolio centerpiece.
Build a simulated small company with:
- HQ site: multiple VLANs (Sales, IT, Management, Servers), Layer 3 switching, OSPF core.
- Branch site: connected via simulated WAN (serial or GRE tunnel over the "internet").
- Centralized DHCP + DNS.
- Internal web/FTP/email server farm.
- NAT/PAT for internet-bound traffic.
- ACLs restricting Sales VLAN from reaching the Server VLAN except HTTP.
- Port security on all access-layer switch ports.
- SSH-only remote management (no Telnet anywhere).

**Verify:** Full topology diagram, all `show` command outputs, a written README summarizing design decisions (this is what recruiters/interviewers actually read).

---

## GitHub Documentation Tips
- One folder per lab, one `README.md` each with: **Objective → Topology diagram → Config snippets → Problems you hit → How you fixed them**. That "problems/fixes" section is what makes a portfolio stand out — it shows troubleshooting ability, not just following steps.
- Export configs from Packet Tracer devices (`show running-config` → copy into a `.txt` file) rather than just screenshots — recruiters can skim text faster.
- Use draw.io or Packet Tracer's own topology screenshot for diagrams.
- Pin the capstone lab (Lab 23) at the top of your repo's main README as the "flagship" project.

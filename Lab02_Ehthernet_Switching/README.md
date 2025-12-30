# 🧪 CCNA Lab: MAC Address Learning, ARP, and ICMP

This lab demonstrates how **Layer 2 switches learn MAC addresses dynamically** and how **ARP is required before ICMP (ping)** can be successfully delivered when ARP and MAC tables are initially empty.

---

## 🖧 Network Topology

![Topology](screenshots/ethernet_switching_0.png)

**Explanation:**
- All PCs are in the same subnet: `192.168.1.0/24`
- Two Cisco 2960 switches (SW1 and SW2) are connected via `Gig0/1`
- PCs are connected using FastEthernet access ports
- Initially:
  - PC ARP tables are empty
  - Switch MAC address tables are empty

---

## 1️⃣ ICMP Starts but Requires ARP Resolution

![ICMP requires ARP](screenshots/ethernet_switching_2.png)

**Explanation:**
- PC1 attempts to ping `192.168.1.3` (PC3)
- ICMP Echo Request (Type 8) is generated at Layer 3
- PC1 checks its ARP table to find the MAC address for `192.168.1.3`
- Since no ARP entry exists, the ICMP packet is **buffered**
- The ARP process is triggered to resolve the destination MAC address

---

## 2️⃣ ARP Request Frame (Layer 2 Broadcast)

![ARP request frame](screenshots/ethernet_switching_3.png)

**Explanation:**
- PC1 creates an ARP Request encapsulated in an Ethernet II frame
- Key fields:
  - **Destination MAC:** `FFFF.FFFF.FFFF` (broadcast)
  - **Source MAC:** PC1’s MAC address
  - **Source IP:** `192.168.1.1`
  - **Target IP:** `192.168.1.3`
  - **Target MAC:** `0000.0000.0000` (unknown)
- This broadcast allows all devices on the LAN to receive the request

---

## 3️⃣ ARP Flooding Across the Switching Fabric

![ARP flooding](screenshots/ethernet_switching_1.png)

**Explanation:**
- SW1 receives the ARP broadcast and floods it out all ports except the incoming port
- The ARP request is forwarded across the inter-switch link (`Gig0/1`)
- SW2 floods the ARP request to its access ports
- PC2, PC3, and PC4 receive the ARP request
- Only **PC3** (the device with IP `192.168.1.3`) will respond

---

## 4️⃣ Event List: ARP First, Then ICMP

![Event list](screenshots/ethernet_switching_4.png)

**Explanation:**
- The simulation event list shows the correct sequence:
  1. ICMP attempt begins
  2. ARP request is generated and broadcast
  3. ARP frames traverse SW1 → SW2 → PCs
  4. ARP reply is sent back to PC1
  5. ICMP traffic resumes after ARP resolution
- This confirms that **ARP must complete before ICMP can succeed**

---

## 5️⃣ Successful Ping After ARP Resolution

![Ping success](screenshots/ethernet_switching_5.png)

**Explanation:**
- PC1 successfully receives replies from the destination PC
- 0% packet loss confirms correct ARP resolution and switching behavior
- Subsequent pings are fast because the ARP entry is now cached

---

## 6️⃣ MAC Address Table Learned Dynamically

![MAC address table](screenshots/ethernet_switching_6.png)

**Explanation:**
- The switch MAC address table now contains dynamically learned entries
- Switches learn MAC addresses from the **source MAC field** of incoming frames
- Observations:
  - MACs learned on `Fa0/1` and `Fa0/2` belong to locally connected PCs
  - MAC learned on `Gig0/1` belongs to a PC on the remote switch
- Only devices that **transmit traffic** appear in the MAC table

---

## 🖥️ Verification Commands

```bash
show mac address-table
show mac address-table dynamic
clear mac address-table dynamic

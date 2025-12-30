# 🧪 Lab: MAC Address Learning + ARP + Ping (Cisco Packet Tracer)

This lab demonstrates how switches learn MAC addresses dynamically and how ARP is used before a ping (ICMP) can be delivered when devices have empty ARP and MAC tables.

---

## 🖧 Topology & Addressing

- **Network:** `192.168.1.0/24`
- **Devices:** 2x Switches (SW1, SW2), 4x PCs (PC1–PC4)
- **Key links:**
  - PCs connect to access ports (FastEthernet)
  - SW1 ↔ SW2 connected via `G0/1`

![Topology](screenshots/01-topology.png)

**What this shows (small detail):**
- All PCs are on the same LAN/subnet.
- Switches will forward frames based on MAC addresses (Layer 2).
- The inter-switch link (`G0/1`) is how traffic reaches the PCs on the opposite switch.

---

## 1) Starting the Ping: ICMP is Ready, But ARP is Missing

![ICMP needs ARP](screenshots/02-icmp-needs-arp.png)

**What this shows (small detail):**
- PC1 is trying to send an **ICMP Echo Request (Type 8)** to `192.168.1.3` (PC3).
- Packet Tracer is telling you the key concept:
  - PC1 checks its **ARP table** for the destination IP’s MAC address.
  - Since ARP is empty, PC1 **cannot build the Ethernet frame yet**.
  - So it **buffers** the ICMP packet and starts the ARP process.

---

## 2) ARP Request Frame: Broadcast at Layer 2

![ARP request frame](screenshots/03-arp-request-frame.png)

**What this shows (small detail):**
- This is the actual **ARP Request** being built by PC1.
- The most important fields here:

**Ethernet II (Layer 2):**
- **Destination MAC:** `FFFF.FFFF.FFFF` (broadcast)
- **Source MAC:** PC1’s MAC
- Broadcast means: *every device in the same broadcast domain will receive it.*

**ARP (Layer 3 mapping info inside the frame):**
- **Source IP:** `192.168.1.1` (PC1)
- **Target IP:** `192.168.1.3` (PC3)
- **Target MAC:** `0000.0000.0000` (unknown → that’s the point of ARP)

---

## 3) ARP Flooding Across Both Switches

![ARP flooding across network](screenshots/04-arp-flooding-topology.png)

**What this shows (small detail):**
- You can see the ARP envelope traveling across the topology.
- Since ARP is a **broadcast**, switches behave like this:
  - SW1 receives it and **floods it out every port except the incoming port**.
  - The frame crosses the inter-switch link to SW2.
  - SW2 floods it to its access ports as well.
- **Result:** PC2, PC3, and PC4 all receive the ARP request, but only the PC with `192.168.1.3` should answer (PC3).

---

## 4) Simulation Event List: ARP Happens First, Then ICMP

![Event list ARP then ICMP](screenshots/05-event-list-arp-then-icmp.png)

**What this shows (small detail):**
- The event list confirms the correct order of operations:
  1. **ICMP attempt begins** (PC1 wants to ping)
  2. **ARP is triggered** (because MAC is unknown)
  3. ARP frames propagate across SW1 → SW2 and out to PCs
  4. Once ARP resolves, **ICMP frames appear again** (now the switch can forward unicast properly)

This is the exact “real networking” behavior:  
✅ **No ARP resolution → no successful ping delivery.**

---

## 5) Ping Success (After ARP Resolution)

![Ping success](screenshots/06-ping-success.png)

**What this shows (small detail):**
- The ping now succeeds (0% loss).
- This confirms two things happened correctly:
  - PC1 learned the **destination MAC** via ARP (or learned enough to forward)
  - Switches learned MAC addresses dynamically during the exchange
- After the first ARP resolution, later pings typically feel “instant” because the ARP entry already exists.

---

## 6) MAC Address Table Learned on SW1

![MAC address table](screenshots/07-mac-table-learned.png)

**What this shows (small detail):**
- `show mac address-table` displays what SW1 has learned dynamically.
- Each entry exists because the switch saw that MAC as a **source MAC** on that port.

In your screenshot, SW1 learned:
- A MAC on `Fa0/1` (likely the PC on that port that transmitted)
- A MAC on `Fa0/2` (another locally connected PC that transmitted)
- A MAC on `Gi0/1` (a remote PC whose traffic came from SW2 across the inter-switch link)

**Key concept:**  
Switches learn MACs from **source addresses**, not because “a device exists.”  
So if only some PCs transmit traffic, only those PCs appear in the table.

---

## 🧾 Useful Commands

### View MAC table
```bash
show mac address-table

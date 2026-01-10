# 🧪 CCNA Lab: Router Interface Configuration & Multi-Network Connectivity

This lab demonstrates how to configure a router with multiple interfaces, assign IP addresses for different networks, and verify end-to-end connectivity between hosts on **separate subnets** using static IPv4 addressing.

---

## 🖧 Network Topology & Lab Instructions

![Topology](screenshots/topology1.png)

### Topology Overview
- **Router:** R1
- **Switches:** SW1, SW2, SW3
- **Hosts:** PC1, PC2, PC3
- **Networks Used:**
  - `15.0.0.0 /8` (PC1)
  - `182.98.0.0 /16` (PC2)
  - `201.191.20.0 /24` (PC3)

### Lab Tasks
1. Configure R1’s hostname  
2. View R1’s interfaces and their status  
3. Assign IP addresses to router interfaces  
4. Enable interfaces and add descriptions  
5. Verify interface configuration  
6. Configure static IPs on PCs  
7. Test connectivity using ping  

---

## 1️⃣ PC IP Configuration (Static Addressing)

### PC1 Configuration
![PC1 IP Config](screenshots/06.png)

- IP Address: `15.0.0.1`
- Subnet Mask: `255.0.0.0`
- Connected to SW1

---

### PC2 Configuration
![PC2 IP Config](screenshots/07.png)

- IP Address: `182.98.0.1`
- Subnet Mask: `255.255.0.0`
- Connected to SW2

---

### PC3 Configuration
![PC3 IP Config](screenshots/08.png)

- IP Address: `201.191.20.1`
- Subnet Mask: `255.255.255.0`
- Connected to SW3

---

## 2️⃣ Router Interface Configuration

![Router Interface Config](screenshots/05.png)

R1 was configured with three GigabitEthernet interfaces:

- **GigabitEthernet0/0**
  - Connected to SW1
  - IP Address: `15.255.255.254 /8`
- **GigabitEthernet0/1**
  - Connected to SW2
  - IP Address: `182.98.255.254 /16`
- **GigabitEthernet0/2**
  - Connected to SW3
  - IP Address: `201.191.20.254 /24`

Each interface was assigned:
- An IP address
- A subnet mask
- A descriptive label identifying the connected switch

---

## 3️⃣ Enabling Router Interfaces

![No Shutdown](screenshots/03.png)

- Router interfaces are **administratively down by default**
- The `no shutdown` command was issued on each interface
- Console messages confirm:
  - Interface state changed to **up**
  - Line protocol became **up**

This step allows the router to begin forwarding traffic.

---

## 4️⃣ Interface Verification

![Show IP Interface Brief](screenshots/04.png)

The `show ip interface brief` command confirms:
- All three GigabitEthernet interfaces are **up/up**
- Each interface has the correct IP address
- R1 is properly configured to route traffic between networks

---

## 5️⃣ Connectivity Testing

![Ping Results](screenshots/09.png)

From **PC1**, pings were sent to:
- `182.98.0.1` (PC2)
- `201.191.20.1` (PC3)

### Observations
- The first ping may time out due to ARP resolution
- Subsequent replies succeed
- Confirms successful **Layer 3 routing between all networks**

---

## 📊 Commands Learned & Used

| Command | Mode | Purpose |
|------|------|------|
| `enable` | User EXEC | Enter privileged EXEC mode |
| `configure terminal` | Privileged EXEC | Enter global configuration mode |
| `hostname R1` | Global Config | Set router hostname |
| `interface gigabitEthernet 0/x` | Global Config | Enter interface configuration mode |
| `ip address X.X.X.X Y.Y.Y.Y` | Interface Config | Assign IP address to interface |
| `description` | Interface Config | Document interface connection |
| `no shutdown` | Interface Config | Enable router interface |
| `show ip interface brief` | Privileged EXEC | Verify interface status and IPs |
| `show running-config` | Privileged EXEC | Review current configuration |
| `ping` | PC | Test inter-network connectivity |

---

## 📚 Skills Learned

`Router Configuration` `Inter-Network Routing` `IPv4 Addressing`
`Subnetting (/8 /16 /24)` `Cisco IOS Navigation`
`Interface Configuration` `No Shutdown`
`Show Commands` `Ping & ARP Behavior`
`Packet Tracer Troubleshooting`
`Layer 3 Connectivity`

---

## ✅ Key Takeaways

- Routers enable communication between **different IP networks**
- Interfaces must be configured and enabled before routing can occur
- `show ip interface brief` is a critical verification tool
- Initial ping failures are expected due to ARP
- Clear interface descriptions improve network documentation

---

📌 *This lab reinforces core CCNA routing fundamentals and real-world Cisco IOS workflow.*

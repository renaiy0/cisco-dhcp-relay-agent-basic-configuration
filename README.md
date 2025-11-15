# Cisco DHCP Relay Agent — Simplified Configuration Guide

A clear and concise guide for configuring a DHCP Relay Agent on Cisco routers. This version removes redundancy and focuses only on what you need to know.

---

## 📘 Overview

DHCP Relay allows a router to **forward DHCP requests** from clients in one subnet to a DHCP server in another subnet. This lets you centralize your DHCP server instead of deploying one on every network.

### Why Use DHCP Relay?

* Centralized IP management
* Lower hardware/maintenance costs
* Works across different subnets
* Scales well for growing networks

---

## 🗺️ Network Topology

The setup includes:

* **DHCP Server** (central server)
* **Cisco Router** acting as the relay using `ip helper-address`
* **Client Devices** requesting IP addresses

*(See `/assets/topology.png` for diagram)*

---

## ✅ Prerequisites

Before configuring, ensure you have:

* A Cisco IOS router that supports DHCP relay
* A working DHCP server
* Router CLI access (Console/SSH/Telnet)
* Basic understanding of IP addressing

Files provided:

* `cisco.pkt` (Packet Tracer file)
* `/assets/` screenshots

---

## ⚙️ Configuration Steps

### **1. Configure the DHCP Server**

Set up the DHCP pool with:

* IP address range
* Default gateway
* DNS servers
* Lease duration

*(Reference: `/assets/dhcpset.png`)*

---

### **2. Access Router CLI**

```cisco
Router> enable
Router# configure terminal
```

---

### **3. Configure DHCP Relay (ip helper-address)**

On the client-facing interface:

```cisco
Router(config)# interface GigabitEthernet0/0
Router(config-if)# ip address 192.168.1.1 255.255.255.0
Router(config-if)# ip helper-address 192.168.10.10
Router(config-if)# no shutdown
```

Replace `192.168.10.10` with your actual DHCP server IP.

---

### **4. Configure Server-Side Interface**

```cisco
Router(config)# interface GigabitEthernet0/1
Router(config-if)# ip address 192.168.10.1 255.255.255.0
Router(config-if)# no shutdown
```

*(Reference: `/assets/servconf.png`)*

---

### **5. Save Configuration**

```cisco
Router# write memory
```

Or:

```cisco
Router# copy running-config startup-config
```

---

## ✔️ Verification

### **Check Relay Status**

```cisco
show ip interface GigabitEthernet0/0
```

You should see:
`Helper address is X.X.X.X`

### **Verify Client IP Assignment**

Clients should receive:

* A correct IP from the DHCP pool
* Default gateway
* DNS server

*(See `/assets/proof.png` for example)*

### **Debug DHCP Traffic**

```cisco
debug ip dhcp server packet
```

---

## 🔧 Troubleshooting

### **1. Clients Not Getting IP**

* Check `ip helper-address`
* Ensure router can ping the DHCP server
* Verify DHCP pool availability

### **2. Wrong Subnet Assigned**

* Confirm interface IPs
* Check routing between subnets

### **3. Relay Not Forwarding**

```cisco
show ip interface [interface]
debug ip udp
```

### Useful Commands

```cisco
show ip dhcp binding
show running-config interface [interface]
show ip dhcp server statistics
clear ip dhcp binding *
```

---

## 📁 Repository Structure

```
.
├── README.md
├── cisco.pkt
└── assets/
    ├── dhcpset.png
    ├── proof.png
    ├── routercli.png
    ├── servconf.png
    └── topology.png
```

---

## 📝 Notes

* DHCP uses UDP ports 67 (server) and 68 (client)
* Multiple relay addresses can be configured per interface
* Relay may add DHCP Option 82 depending on platform
* Ensure all subnets are properly routed

---

**Author:** Maliq R.
**Last Updated:** November 2025

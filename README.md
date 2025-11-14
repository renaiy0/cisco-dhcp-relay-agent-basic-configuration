# Cisco DHCP Relay Agent - Basic Configuration

📘 Complete documentation for configuring DHCP Relay Agent on Cisco devices using a single DHCP server. Includes basic concepts, network topology, and step-by-step configuration guide.

## 📋 Table of Contents
- [Overview](#overview)
- [Network Topology](#network-topology)
- [Prerequisites](#prerequisites)
- [Configuration Steps](#configuration-steps)
- [Verification](#verification)
- [Troubleshooting](#troubleshooting)

## 🎯 Overview

DHCP Relay Agent allows a router to forward DHCP requests from clients on one subnet to a DHCP server on a different subnet. This eliminates the need for a DHCP server on every network segment.

### Key Benefits:
- Centralized DHCP server management
- Reduced hardware costs
- Simplified IP address management
- Scalable network design

## 🗺️ Network Topology

![Network Topology](./assets/topology.png)

The topology consists of:
- **DHCP Server**: Centralized server providing IP addresses
- **Router**: Acts as DHCP Relay Agent using `ip helper-address`
- **Clients**: End devices requesting IP addresses via DHCP

## ✅ Prerequisites

- Cisco router with IOS support for DHCP relay
- Basic understanding of IP addressing and subnetting
- Access to router CLI (Console/SSH/Telnet)
- DHCP server configured and operational

### Required Files:
- Cisco Packet Tracer file: `cisco.pkt`
- Network diagrams in `/assets/` folder

## ⚙️ Configuration Steps

### 1. DHCP Server Configuration

![DHCP Settings](./assets/dhcpset.png)

Configure your DHCP server with:
```
- IP Pool range
- Default gateway
- DNS servers
- Lease time
```

### 2. Router CLI Configuration

![Router CLI](./assets/routercli.png)

Access the router and enter configuration mode:

```cisco
Router> enable
Router# configure terminal
```

### 3. Configure IP Helper-Address

On the interface facing the client network:

```cisco
Router(config)# interface GigabitEthernet0/0
Router(config-if)# ip address 192.168.1.1 255.255.255.0
Router(config-if)# ip helper-address 192.168.10.10
Router(config-if)# no shutdown
Router(config-if)# exit
```

**Note**: Replace `192.168.10.10` with your DHCP server's IP address.

### 4. Server Interface Configuration

![Server Configuration](./assets/servconf.png)

Configure the interface connecting to the DHCP server:

```cisco
Router(config)# interface GigabitEthernet0/1
Router(config-if)# ip address 192.168.10.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit
```

### 5. Save Configuration

```cisco
Router(config)# end
Router# write memory
```
or
```cisco
Router# copy running-config startup-config
```

## ✔️ Verification

### Check DHCP Relay Configuration

```cisco
Router# show ip interface GigabitEthernet0/0
```

Look for the line showing "Helper address is X.X.X.X"

### Verify Client IP Assignment

![Proof of Configuration](./assets/proof.png)

Check if clients successfully receive IP addresses:
- Client should get IP from configured DHCP pool
- Default gateway should be set correctly
- DNS servers should be assigned

### Debug DHCP Packets

```cisco
Router# debug ip dhcp server packet
```

## 🔧 Troubleshooting

### Common Issues:

1. **Clients not receiving IP addresses**
   - Verify `ip helper-address` is configured correctly
   - Check DHCP server is reachable: `ping [server-ip]`
   - Ensure DHCP pool is not exhausted

2. **Wrong subnet receiving addresses**
   - Verify interface IP addressing
   - Check routing between router and DHCP server

3. **DHCP relay not forwarding requests**
   ```cisco
   Router# show ip interface [interface-name]
   Router# debug ip udp
   ```

### Useful Commands:

```cisco
show running-config interface [interface-name]
show ip dhcp binding
show ip dhcp server statistics
clear ip dhcp binding *
```

## 📁 Repository Structure

```
.
├── README.md
├── cisco.pkt                    # Packet Tracer simulation file
└── assets/
    ├── dhcpset.png             # DHCP server settings
    ├── proof.png               # Configuration proof/result
    ├── routercli.png           # Router CLI configuration
    ├── servconf.png            # Server configuration
    └── topology.png            # Network topology diagram
```

## 📝 Notes

- DHCP relay uses UDP ports 67 (server) and 68 (client)
- Multiple `ip helper-address` commands can be configured per interface
- The relay agent adds Option 82 (Relay Agent Information) to DHCP packets
- Ensure proper routing between all subnets

## 🤝 Contributing

Feel free to submit issues or pull requests to improve this documentation.

## 📄 License

This documentation is provided as-is for educational purposes.

---

**Author**: [Maliq R.]  
**Last Updated**: November 2025  

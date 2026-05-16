# Redundant Campus Network Lab

** Objective**

The objective of this lab was to design and configure a redundant Layer 2 campus network using VLAN segmentation, trunking, Spanning Tree Protocol (STP), and EtherChannel in EVE-NG.

** Job Skills Learned**

This lab helped develop practical networking and infrastructure skills commonly required in:

- Network Administrator roles
- Network Engineer positions
- CCNA/CCNP support jobs
- IT Infrastructure Support
- Systems & Network Operations

- VLAN creation
- Access port configuration
- VLAN management
- Broadcast domain segmentation
- Cisco IOS CLI navigation
- Basic switch hardening
- Device management configuration
- Console configuration
- Trunk port configuration
- VLAN tagging
- Inter-switch communication
- Native VLAN management
- Link aggregation
- Port-channel configuration
- LACP troubleshooting
- High availability design
- STP optimization
- Root bridge election
- Loop prevention
- Network convergence concepts
- Layer 3 switching
- SVI configuration
- Routing between VLANs
- Gateway configuration

**###  Tools and Technologies Used**

- Network Simulation Tool (EVE-NG) used to simulate enterprise networking environments virtually.
- Cisco Networking Technologies
- Cisco Catalyst Switches (2960/3560)
- Cisco IOS CLI

**Ref: Network Diagram***
<img width="1635" height="863" alt="image" src="https://github.com/user-attachments/assets/319cfb2c-f0b0-40c1-9097-3d147b8602b2" />

**### CONFIGURATION STEPS**

**Physical Connections:**

- SW1 ↔ SW2: Two links (Eth1/2 and Eth1/3)
- SW1 ↔ SW3: Two links (Eth1/4 and Eth1/5)
- SW2 ↔ PC1: Eth1/1
- SW3 ↔ PC2: Eth1/2

---

## **Lab Equipment Requirements:**

- 3 Cisco Catalyst switches (2960, 3560, or equivalent)
- 2 PCs or laptops
- Console cables and Ethernet cables
- Optional: Cisco Packet Tracer or GNS3 for virtual lab

---

## **Part 1: Initial Switch Configuration**

### **Step 1: Basic Switch Configuration**

```
! On all switches (SW1, SW2, SW3):
enable
configure terminal
hostname SW1  ! Change accordingly
no ip domain-lookup

line console 0
logging synchronous
exit

service password-encryption
enable secret cisco123

! Configure management VLAN
vlan 99
 name MANAGEMENT
exit

interface vlan 99
 ip address 192.168.99.1 255.255.255.0
 no shutdown
exit

ip default-gateway 192.168.99.254
```

## **Part 2: VLAN Configuration**

### **Step 2: Create VLANs on All Switches**
```
! On SW1, SW2, and SW3:
vlan 10
 name SALES
vlan 20
 name ENGINEERING
vlan 30
 name HR
vlan 99
 name MANAGEMENT
exit
```

### **Step 3: Configure Access Ports**
```
! On SW2:
interface GigabitEthernet0/1
 description PC1-SALES
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 no shutdown
exit

! On SW3:
interface GigabitEthernet0/1
 description PC2-ENGINEERING
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
 no shutdown
exit
```

## **Part 3: Trunk Configuration**

### **Step 4: Configure Trunk Links Between Switches**

```
! On SW1 interfaces connecting to SW2 and SW3:
interface range GigabitEthernet0/0-3
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,99
 no shutdown
exit

! On SW2 interfaces connecting to SW1:
interface range GigabitEthernet0/0 , GigabitEthernet0/3
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,99
 no shutdown
exit

! On SW3 interfaces connecting to SW1:
interface range GigabitEthernet0/0 , GigabitEthernet0/3
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,99
 no shutdown
exit
```

## **Part 4: EtherChannel Configuration**

### **Step 5: Configure Port-Channel Between SW1 and SW2**
```
! On SW1:
interface port-channel 1
 description SW1-SW2 EtherChannel
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,99
exit

interface range GigabitEthernet0/0 , GigabitEthernet0/2
 channel-group 1 mode active  ! LACP active mode
 no shutdown
exit

! On SW2:
interface port-channel 1
 description SW2-SW1 EtherChannel
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,99
exit

interface range GigabitEthernet0/0 , GigabitEthernet0/3
 channel-group 1 mode passive  ! LACP passive mode
 no shutdown
exit
```
### **Step 6: Configure Port-Channel Between SW1 and SW3**
```
! On SW1:
interface port-channel 2
 description SW1-SW3 EtherChannel
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,99
exit

interface range GigabitEthernet0/1 , GigabitEthernet0/3
 channel-group 2 mode active
 no shutdown
exit

! On SW3:
interface port-channel 1
 description SW3-SW1 EtherChannel
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,99
exit

interface range GigabitEthernet0/0 , GigabitEthernet0/3
 channel-group 1 mode passive
 no shutdown
exit
```
## **Part 5: Spanning Tree Protocol Configuration**

### **Step 7: Configure STP for Optimal Path Selection**
```
! On SW1 (Configure as Root Bridge for all VLANs):
spanning-tree mode rapid-pvst
spanning-tree vlan 1,10,20,30,99 priority 4096

! On SW2:
spanning-tree mode rapid-pvst
spanning-tree vlan 1,10,20,30,99 priority 8192

! On SW3:
spanning-tree mode rapid-pvst
spanning-tree vlan 1,10,20,30,99 priority 8192
```
## **Part 6: Verification and Testing**

### **Step 8: Verification Commands**
```
! Check VLAN configuration
show vlan brief
show vlan id 10

! Verify trunk status
show interfaces trunk
show interfaces switchport

! Verify EtherChannel
show etherchannel summary
show etherchannel port-channel
show lacp neighbor

! Verify STP
show spanning-tree
show spanning-tree vlan 10
show spanning-tree root

! Check MAC address table
show mac address-table
show mac address-table vlan 10
```

## **Step 9: PC Configuration**

```
# PC1 Configuration:
IP Address: 192.168.10.10
Subnet Mask: 255.255.255.0
Default Gateway: 192.168.10.1

# PC2 Configuration:
IP Address: 192.168.20.10
Subnet Mask: 255.255.255.0
Default Gateway: 192.168.20.1
```

## **Step 10: Connectivity Tests**
```
# From PC1:
ping 192.168.10.1      # Test VLAN 10 connectivity
ping 192.168.20.10     # Should fail (different VLAN, no routing configured)

# Test redundancy by disconnecting one EtherChannel link
# Verify connectivity remains
```

## **Part 7: Challenge Tasks (Optional)**

### **Challenge 1: VLAN Trunking Protocol**

```  
! Configure VTP on all switches
vtp domain CCNA-Lab
vtp password cisco123
vtp mode server  ! On SW1
vtp mode client  ! On SW2 and SW3
```

### **Challenge 2: Port Security**
```
! Configure port security on access ports
interface GigabitEthernet0/1 , GigabitEthernet0/2
 switchport port-security
 switchport port-security maximum 2
 switchport port-security violation restrict
 switchport port-security mac-address sticky
exit
```
### **CChallenge 3: Configure SVI for Inter-VLAN Routing**
```
! On SW1 (if Layer 3 capable):
interface vlan 10
 ip address 192.168.10.1 255.255.255.0
 no shutdown
exit

interface vlan 20
 ip address 192.168.20.1 255.255.255.0
 no shutdown
exit

ip routing
```

## **Troubleshooting Checklist:**

1. Verify physical connections
2. Check interface status with `show ip interface brief`
3. Confirm VLAN assignments with `show vlan brief`
4. Verify trunk configuration with `show interfaces trunk`
5. Check EtherChannel status with `show etherchannel summary`
6. Verify STP topology with `show spanning-tree`
7. Test connectivity between devices

## **Expected Results:**

- VLANs 10, 20, 30, and 99 created on all switches
- Trunk links established with proper VLAN tagging
- EtherChannel bundles operating with LACP
- STP preventing loops with SW1 as root bridge
- Devices in same VLAN can communicate
- Network remains functional with single link failure

## **Lab Notes:**

- Save configurations: `copy running-config startup-config`
- Clear configurations if needed: `erase startup-config` then `reload`
- Use `debug` commands cautiously in production
- Document all configurations for future reference

## **Difficulty Level: Intermediate**




























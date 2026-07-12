# DHCP and inter-Vlan Configuration

### Objective
This setup will allow you to understand how devices in a network can automatically
obtain IP addresses and other network configuration details from a DHCP server.

### Software Requirements:
- CISCO Packet Tracer

### Lab Setup

![lab-setup](image.png)

### Step-by-Step Instructions
#### Step 1: Add Devices to the Workspace

1. Open Cisco Packet Tracer.
2. From the End Devices section, drag and drop the following devices onto the
workspace:
- Add the devices required for each VLAN/subnet.
- Add a Server to configure as the DHCP server.
3. From the Network Devices section, drag and drop:
- Drag a Router (e.g., 2911 or 1941) into the workspace.
- Add a Switch (e.g., 2960).
4. From the Wireless Devices section, drag and drop:
-  Drag a Home-Router-PT-AC
#### Step 2: Physically Connect Devices
1. Click on the cable icon and select a copper straight-through cable.
2. Connect the following devices using the copper straight-through cable.
    - Router (GigabitEthernet 0/0) to the Switch (GigabitEthernet 0/1).
    - The Server to the Switch.
    - Connect a PC to  the swictch using a console cable

### Step 3: Configure the VLANs on the Switch
#### Access the Switch:
- Click on the PC to open the Desktop window.
- Select Terminal.
- click on OK
  
Create VLANs:
● Enter global configuration mode and adding a hostname
```
Switch>enable
Switch#configure terminal
Switch(config)#
Switch(config)#hostname Net-SW
```

● Create VLANs for each subnet:

```
Net-SW(config)#vlan 10
Net-SW(config-vlan)#name Staff
Net-SW(config-vlan)#vlan 20
Net-SW(config-vlan)#name Students 
Net-SW(config-vlan)#vlan 30
Net-SW(config-vlan)#name Guest
Net-SW(config-vlan)#vlan 40
Net-SW(config-vlan)#name IT_MGMT
Net-SW(config-vlan)#do wr
```

Assign VLANs to Ports:
● Assign PC ports to respective VLANs
```
Net-SW(config)#interface range FastEthernet 0/1-6
Net-SW(config-if)#switchport mode access
Net-SW(config-if)#switchport access vlan 10
Net-SW(config-if)#exit
Net-SW(config)#interface range FastEthernet 0/7-12
Net-SW(config-if)#switchport mode access
Net-SW(config-if)#switchport access vlan 20
Net-SW(config-if)#exit
Net-SW(config)#interface range FastEthernet 0/13-18
Net-SW(config-if)#switchport mode access
Net-SW(config-if)#switchport access vlan 30
Net-SW(config-if)#exit
Net-SW(config)#interface range FastEthernet 0/19-24
Net-SW(config-if)#switchport mode access
Net-SW(config-if)#switchport access vlan 30
Net-SW(config-if)#exit
```
Configure Trunk Port for Router:
Configure the port connected to the router as a trunk port:
```
Net-SW(config)#interface GigabitEthernet 0/1
Net-SW(config-if)#switchport mode trunk
Net-SW(config-if)#switchport trunk allowed vlan all
```

Step 4: Configure the Router for Inter-VLAN Routing
Access the Router:
- Click on the router to open the configuration window. -
- Go to the CLI tab and enter configuration mode
```
Router>enable
Router#configure terminal
Router(config)#
```
- Configure Sub-Interfaces for Each VLAN:
● For each VLAN, create a sub-interface with the VLAN ID and assign IP addresses (gateway for each VLAN):
```
Router(config)#interface GigabitEthernet0/0.10
Router(config-if)#encapsulation dot1Q 10
Router(config-if)#ip address 192.168.10.1
255.255.255.0
Router(config-if)#exit
Router(config)#interface GigabitEthernet0/0.20
Router(config-if)#encapsulation dot1Q 20
Router(config-if)#ip address 192.168.20.1
255.255.255.0
Router(config-if)#exit
Router(config)#interface GigabitEthernet0/0.30
Router(config-if)#encapsulation dot1Q 30
Router(config-if)#ip address 192.168.30.1
255.255.255.0
Router(config-if)#exit
Router(config)#interface GigabitEthernet0/0.40
Router(config-if)#encapsulation dot1Q 40
Router(config-if)#ip address 192.168.40.1
255.255.255.0
```
- Enable the interface:
```
Router(config)#interface GigabitEthernet0/0
Router(config-if)#no shutdown
Save the configuration:
Router(config-if)# do wr
```

- Save the configuration:
```
Router(config-if)# do wr
```

- Step 5: Configure the DHCP Server
1. Access the Server:
    - Click on the Server to open its configuration window.
    - Go to the Config tab.
2. Assign a Static IP to the Server:
    - Select FastEthernet0 on the left.
    -  Set the IP Address to 192.168.40.2 and Subnet Mask to 255.255.255.0.
    -  Set the Default Gateway to 192.168.40.1.
3. Enable DHCP on the Server:
  - Go to Services > DHCP.
  - Enable the DHCP service by toggling it to “On.”
  - Configure a DHCP pool for each subnet:
  - For VLAN 10 (Staff):
      - Pool Name: Staff
      - Default Gateway: 192.168.10.1
      - Start IP Address: 192.168.10.5
      - Subnet Mask: 255.255.255.0
      - Maximum Users: 200

  - For VLAN 20 (Students):
      - Pool Name: Student
      - Default Gateway: 192.168.20.1
      - Start IP Address: 192.168.20.10
      - Subnet Mask: 255.255.255.0
      - Maximum Users: 200
        
  - For VLAN 30 (Guests):
      -  Pool Name: Guests
      -  Default Gateway: 192.168.30.1
      -   Start IP Address: 192.168.30.10
      -   Subnet Mask: 255.255.255.0
      -   Maximum Users: 20
  - Click Add after each pool to save the settings.
Step 6: Configure DHCP Relay on the Router
- Go back to the Router CLI and enter global configuration mode:
```
router>enable
router>conf t
```
Configure IP Helper on each VLAN sub-interface:
For VLAN 20:
```
Router(config)#interface GigabitEthernet0/0.20
Router(config-subif)#ip helper-address 192.168.40.2
Router(config-subif)#exit
```
For VLAN 30:
```
Router(config)#interface GigabitEthernet0/0.30
Router(config-subif)#ip helper-address 192.168.40.2
Router(config-subif)#exit
```
For VLAN 10:
```
Router(config)#interface GigabitEthernet0/0.10
Router(config-subif)#ip helper-address 192.168.40.2
Router(config-subif)#exit
```
For VLAN 40:
```
Router(config)#interface GigabitEthernet0/0.40
Router(config-subif)#ip helper-address 192.168.40.2
Router(config-subif)#exit
```
Step 7: Configure PCs to Obtain IP Addresses via DHCP
Configure Each PC:
-  Click on each PC to open its configuration.
-  Go to the Desktop tab > IP Configuration.
-  Select DHCP. Each PC should automatically receive an IP address from the correct subnet range based on its VLAN.
   Verify Configuration:
-  From the Command Prompt on each PC, use the ipconfig command to
verify the assigned IP address.
-  Test connectivity between PCs in different VLANs using ping (e.g., ping
192.168.20.10 from a PC in VLAN 10).

____

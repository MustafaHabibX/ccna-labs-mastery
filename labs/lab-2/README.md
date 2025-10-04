<p align="center">
  <a href="/labs/lab-1/README.md">Previous (Lab 1)</a> • 
  <a href="/README.md">Dashboard</a> • 
  <a href="/labs/lab-3/README.md">Next (Lab 3)</a>
</p>

<br>

# Lab 2 – Ethernet LAN Switching

## Learning Objectives

By completing this lab, you will be able to:

- Understand how ARP requests and replies work in a LAN.
- Observe how Ethernet switches learn MAC addresses.
- Verify ICMP communication between PCs using ping.
- Clear dynamic MAC addresses from switches and verify table updates.

<br>

## Table of Contents

- [Tasks for this Lab](#tasks-for-this-lab)
- [Network Topology](#network-topology)
- [Task 1: ARP Request and ICMP Ping](#task-1-arp-request-and-icmp-ping)
- [Task 2: Verify ARP and ICMP with Simulation Mode](#task-2-verify-arp-and-icmp-with-simulation-mode)
- [Task 3: Populate MAC Address Tables](#task-3-populate-mac-address-tables)
- [Task 4: View MAC Address Tables](#task-4-view-mac-address-tables)
- [Task 5: Clear Dynamic MAC Addresses](#task-5-clear-dynamic-mac-addresses)
- [Device Configurations](#device-configurations)
- [Summary](#summary)

<br>

## Tasks for this Lab

**Note:** Both switches start with empty MAC address tables, and all PCs have empty ARP tables.

1. Predict the messages sent when PC1 pings PC3 and identify which devices receive them.
2. Use Packet Tracer's simulation mode to verify your prediction.
3. Generate traffic by pinging all PCs to allow switches to learn MAC addresses.
4. Use 'show' commands to verify MAC addresses on switches.
5. Clear the dynamic MAC addresses on both switches and verify.

<br>

## Network Topology

<p align="center">
  <img src="assets/0.webp" alt="Lab 2 Topology" width="500"/>
</p>

<br>

---

## Task 1: ARP Request and ICMP Ping

**Steps:**

1. Switch to **Simulation Mode** in Cisco Packet Tracer.
<p align="center">
  <img src="assets/1.webp" alt="Packet Tracer Simulation Mode" width="500"/>
</p>

2. Click on **Virtual-PC1**.
3. Open the **Desktop** tab.
<p align="center">
  <img src="assets/2.webp" alt="Desktop tab" width="500"/>
</p>

4. Open the **Command Prompt** application.
<p align="center">
  <img src="assets/3.webp" alt="Command Prompt" width="500"/>
</p>

5. Type `ping 192.168.1.3` and press **Enter**.
<p align="center">
  <img src="assets/4.webp" alt="Pinging PC3" width="500"/>
</p>

```powershell
PC1> ping 192.168.1.3
```

**Explanation:**

- PC1 knows the IP of PC3 but not its MAC address. It sends an **ARP request broadcast** to all devices on the LAN.
- Only PC3 responds with a **unicast ARP reply**, providing its MAC address.
- Switches learn the MAC address of PC3 from the source MAC of the ARP-reply frame.
- Once PC1 knows PC3’s MAC, it can send ICMP echo requests (ping) directly to PC3.

---

## Task 2: Verify ARP and ICMP with Simulation Mode

### PC1 sending ARP request to find MAC of PC3

- **PC1 sends ARP request to SW1**
<p align="center">
  <img src="assets/5.webp" alt="PC1 sends ARP request" width="500"/>
</p>

- **SW1 receives ARP request from PC1**
<p align="center">
  <img src="assets/6.webp" alt="SW1 receives ARP request" width="500"/>
</p>

- **Confirming the frame is an ARP request**
<p align="center">
  <img src="assets/7.webp" alt="Confirming ARP request frame" width="500"/>
</p>

- **SW1 forwards ARP request out all ports except the incoming one**
<p align="center">
  <img src="assets/8.webp" alt="SW1 forwards ARP request" width="500"/>
</p>

- **SW2 forwards ARP request out all ports except the incoming one**
<p align="center">
  <img src="assets/9.webp" alt="SW2 forwards ARP request" width="500"/>
</p>

- **PC3 received the ARP request**

### PC3 sending ARP reply back to PC1

- **PC3 sends ARP reply to SW2**
<p align="center">
  <img src="assets/10.webp" alt="PC3 sends ARP reply to SW2" width="500"/>
</p>

- **SW2 forwards ARP reply to SW1**
<p align="center">
  <img src="assets/11.webp" alt="SW2 forwards ARP reply to SW1" width="500"/>
</p>

- **SW1 forwards ARP reply to PC1**
<p align="center">
  <img src="assets/12.webp" alt="SW1 forwards ARP reply to PC1" width="500"/>
</p>

### ICMP Ping from PC1 to PC3

- **PC1 sends ICMP echo request to SW1**
<p align="center">
  <img src="assets/13.webp" alt="PC1 sends ICMP echo request to SW1" width="500"/>
</p>

- **SW1 forwards ICMP echo request to SW2**
<p align="center">
  <img src="assets/14.webp" alt="SW1 forwards ICMP echo request to SW2" width="500"/>
</p>

- **SW2 forwards ICMP echo request to PC3**
<p align="center">
  <img src="assets/15.webp" alt="SW2 forwards ICMP echo request to PC3" width="500"/>
</p>

- **PC3 sends ICMP echo reply to SW2**
<p align="center">
  <img src="assets/16.webp" alt="PC3 sends ICMP echo reply to SW2" width="500"/>
</p>

- **SW2 forwards ICMP echo reply to SW1**
<p align="center">
  <img src="assets/17.webp" alt="SW2 forwards ICMP echo reply to SW1" width="500"/>
</p>

- **SW1 forwards ICMP echo reply to PC1**
<p align="center">
  <img src="assets/18.webp" alt="SW1 forwards ICMP echo reply to PC1" width="500"/>
</p>

**Result:** First ping triggers ARP; subsequent pings go directly since MAC addresses are learned.

---

## Task 3: Populate MAC Address Tables

Ping each PC from all other PCs to allow SW1 and SW2 to learn MAC addresses:

```plaintext
PC1> ping 192.168.1.3
PC1> ping 192.168.1.4
PC2> ping 192.168.1.3
... (ping for all PCs in each of them)
```

---

## Task 4: View MAC Address Tables

**Steps:**

```plaintext
SW1# show mac address-table
SW2# show mac address-table
```

### Screenshots

- SW1's MAC address table
<p align="center">
  <img src="assets/19.webp" alt="SW1 MAC address table" width="500"/>
</p>

- SW2's MAC address table
<p align="center">
  <img src="assets/20.webp" alt="SW2 MAC address table" width="500"/>
</p>

### Verify MAC addresses on PCs

```plaintext
C:\> ipconfig /all
```

- PC1's MAC address
<p align="center">
  <img src="assets/21.webp" alt="PC1 MAC address" width="500"/>
</p>

- PC2's MAC address
<p align="center">
  <img src="assets/22.webp" alt="PC2 MAC address" width="500"/>
</p>

- PC3's MAC address
<p align="center">
  <img src="assets/23.webp" alt="PC3 MAC address" width="500"/>
</p>

- PC4's MAC address
<p align="center">
  <img src="assets/24.webp" alt="PC4 MAC address" width="500"/>
</p>

---

## Task 5: Clear Dynamic MAC Addresses

**Steps:**

```plaintext
SW1# clear mac address-table dynamic
SW2# clear mac address-table dynamic
```

### Screenshots

- Clearing the MAC address table of SW1
<p align="center">
  <img src="assets/25.webp" alt="Clearing SW1 MAC address table" width="500"/>
</p>

- Confirming that the MAC address table of SW1 is cleared
<p align="center">
  <img src="assets/26.webp" alt="Confirm SW1 MAC address table cleared" width="500"/>
</p>

- Clearing the MAC address table of SW2
<p align="center">
  <img src="assets/27.webp" alt="Clearing SW2 MAC address table" width="500"/>
</p>

- Confirming that the MAC address table of SW2 is cleared
<p align="center">
  <img src="assets/28.webp" alt="Confirm SW2 MAC address table cleared" width="500"/>
</p>

<br>

---

## Device Configurations

- [SW1 Config](configs/SW1.txt)
- [SW2 Config](configs/SW2.txt)
- [PC1 Config](configs/PC1.txt)
- [PC2 Config](configs/PC2.txt)
- [PC3 Config](configs/PC3.txt)
- [PC4 Config](configs/PC4.txt)

<br>

---

## Summary

In this lab, you:

- Observed ARP request and reply behavior in a LAN.
- Learned how switches populate MAC address tables.
- Verified ICMP ping communication between PCs.
- Cleared dynamic MAC addresses and confirmed table updates.

---

<br>
<br>
<br>

<p align="center">
  <a href="/labs/lab-1/README.md">Previous (Lab 1)</a> • 
  <a href="/README.md">Dashboard</a> • 
  <a href="/labs/lab-3/README.md">Next (Lab 3)</a>
</p>

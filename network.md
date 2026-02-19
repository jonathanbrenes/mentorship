# Network
## Packet Capture
### Create a server running a webserver
Deploy this VM. This will automatically create a VM and set a webserver running on port 80
  
  [![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjonathanbrenes%2Fmentorship%2Frefs%2Fheads%2Fmain%2Fnetwork001.json)
- Capture traffic on port 80. Access the VM public IP from a webbrowser, once the test is done, hit ```Ctrl-C``` to stop the capture
  ``` bash
  tcpdump -i any port 80 and not host 168.63.129.16 # Captures network traffic on port 80 from all interfaces while excluding traffic from the IP address 168.63.129.16 which could be sending health probes to the VM.
  ```

  > **Note:** The parameter ```-w``` can be used to save the capture in a pcap file. Caoture the file!
  ``` bash
   tcpdump -w mycapture.pcap -i any port 80 and not host 168.63.129.16
  ```
- Install wireshark on your windows
  
  Open a cmd prompt on windows and install wireshark using winget
  ```batch
  winget install wireshark
  ```
  > **Note:** This will open a pop out window asking for permission. Please accept it!

- Download the file and open it using wireshark

- Make another capture again with tcpdump, don't use any filter this time. Access the webserver again, click refresh few times. Access the VM public IP from a webbrowser, once the test is done, hit ```Ctrl-C``` to stop the capture
  ``` bash
  tcpdump -w mycapture2.pcap -i any port 80 # Captures network traffic on port 80 from all interfaces.
  ```

- Download the new file on windows
  
  Filtering is possible on wireshark
  
  | **Filter Description**                           | **Wireshark Filter**                  |
  |--------------------------------------------------|---------------------------------------|
  | Filter traffic on port 80 (HTTP traffic)         | `tcp.port == 80`                      |
  | Filter traffic from a specific IP address        | `ip.src == 192.168.1.1`               |
  | Filter traffic to a specific IP address          | `ip.dst == 192.168.1.1`               |
  | Filter HTTP traffic from a specific IP address   | `tcp.port == 80 && ip.src == 192.168.1.1` |
  | Filter HTTP traffic to a specific IP address     | `tcp.port == 80 && ip.dst == 192.168.1.1` |
  | Filter all traffic from a specific subnet        | `ip.src == 192.168.1.0/24`            |
  | Filter all traffic to a specific subnet          | `ip.dst == 192.168.1.0/24`            |
  | Filter traffic for a specific TCP port range     | `tcp.port >= 1000 && tcp.port <= 2000`|
  | Filter traffic for a specific UDP port           | `udp.port == 53`                      |
  | Filter traffic for a specific MAC address        | `eth.addr == 00:11:22:33:44:55`       |

---

## Multinic configuration
Deploy this VM. This will automatically create a SLES VM that will be configure for multinic.
  
  [![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjonathanbrenes%2Fmentorship%2Frefs%2Fheads%2Fmain%2Fnetwork002.json)
- Check network configuration
  Important commands
  ```bash
  ip address show
  ip route show
  ip rule show
  ```
- Attach secondary NIC to VM
  Power off the VM, attach network interface. There is already a created network interface on the same resource group. Power on the VM

- Check network configuration using the same commands

Deploy another VM. This will automatically create a RHEL VM that will be configure for multinic.
  
  [![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjonathanbrenes%2Fmentorship%2Frefs%2Fheads%2Fmain%2Fnetwork003.json)
- Check network configuration
  Important commands
  ```bash
  ip address show
  ip route show
  ip rule show
  ```
- Attach secondary NIC to VM
  
  Power off the VM, attach network interface. There is already a created network interface on the same resource group. Power on the VM

- Check network configuration using the same commands
  
  >**Note:** Why this time network configuration doesn't look correct?

- Setting multinic configuration on RHEL

---

## Multinic, Accelerated Networking, and MTU on Azure (RHEL 9)

### Objective
- In this exercise, students will deploy **two RHEL 9 virtual machines** in Azure. Both VMs will use **Accelerated Networking** and **multiple NICs (multinic)**.  
  One VM will act as a **webserver**, exposing an HTTP endpoint that displays the **source IP (client)** and **destination IP (listener)** of each request.  
  Students will:
    - Validate Accelerated Networking on Linux
    - Complete a **proper multinic configuration on RHEL 9**
    - Increase the **MTU size** according to Microsoft guidance
    - Verify connectivity and MTU behavior using documented methods


### Architecture Overview
  - 2 × RHEL 9 VMs
    - `webserver` (primary + secondary NIC)
    - `client` (primary + secondary NIC)
  - Same VNet and subnet
  - Accelerated Networking enabled on **all NICs**
  - Webserver listens on port **80**
  - MTU increased for **intra‑VNet traffic**
  - HTTP responses show:
    - Client IP (source)
    - Server IP (destination)

### Deploy the Environment
   [![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjonathanbrenes%2Fmentorship%2Frefs%2Fheads%2Fmain%2Fnetwork004.json)
   - This template creates:
     - VNet, subnet, NSG
     - Two RHEL 9 VMs
     - Primary and secondary NICs
     - Accelerated Networking enabled
     - Webserver preconfigured via customData



### Part 1: Verify Accelerated Networking on Linux
  - On **both VMs**, validate that Accelerated Networking is active.
    Important checks:
      - Identify Mellanox or MANA devices
      - Confirm SR‑IOV is present

    Commands to run:
    ```bash
    ip link show
    lspci
    ethtool -i <interface>
    ```

    Expected result:
      - Interfaces backed by `mlx4`, `mlx5`, or `mana`
      - No errors indicating fallback to synthetic-only networking

### Part 2: Validate Initial Network State
  - Before any changes, inspect the routing and addressing.
    Run on both VMs:
    ```bash
    ip address show
    ip route show
    ip rule show
    ```

    Observation:
      - Only the **primary NIC** is correctly used for default routing
      - Secondary NIC is present but **not correctly routed**
      - This is expected behavior on RHEL before multinic configuration

    
### Part 3: Multinic Configuration on RHEL 9
  - RHEL does **not** automatically configure multiple NIC routing correctly in Azure.
    Students must configure:
      - Separate routing tables
      - Source-based routing rules
      - Correct default gateway behavior

    Follow the official Microsoft documentation:
    Configure multiple network interfaces in Azure Linux virtual machines  
    https://learn.microsoft.com/troubleshoot/azure/virtual-machines/linux/linux-vm-multiple-virtual-network-interfaces-configuration

    Tasks:
    - Identify NICs (eth0 / eth1)
    - Ensure only one default route exists
    - Add policy routing so return traffic exits via the correct NIC
    - Persist configuration across reboots

    Validation:
    - `ip rule show`
    - `ip route show table <custom-table>`
    - Traffic symmetry is maintained


### Part 4: Webserver Validation
  - From the **client VM**, access the webserver using:
     - Public IP
     - Private IP (preferred for MTU testing)

    The webserver response displays:
     - Client IP (source of the HTTP request)
     - Server IP (listener interface)

    This confirms:
     - Which NIC is used for ingress
     - Whether routing is symmetric
     - Multinic configuration correctness



### Part 5: Increase MTU Size
- Azure supports larger MTU values **only for traffic within the same VNet**.
  Students must follow:
   - Configure MTU for each secondary NIC on both virtual machines
     - https://access.redhat.com/solutions/5846031
     - https://learn.microsoft.com/azure/virtual-network/how-to-virtual-machine-mtu

  Key points from the documentation:
    - Default MTU: 1500
    - Mellanox Accelerated Networking: up to ~3900
    - MANA Accelerated Networking: up to 9000
    - MTU must match on both ends
    - Fragmented packets bypass accelerated path

  Tasks:
    - Identify supported MTU based on NIC type
    - Increase MTU on both VMs on the secondary NIC
    - Persist MTU configuration using NetworkManager

  Validation commands:
  ```bash
  ip link show
  ```


 ### Part 6: MTU Validation Using Microsoft Script
   - To validate Path MTU, students must use the **Microsoft‑provided script**.
       - GetPathMTU – Path MTU Discovery Sample Script  https://learn.microsoft.com/samples/azure-samples/getpathmtu/getpathmtu/
       - You can download directly the script here https://raw.githubusercontent.com/Azure-Samples/GetPathMTU/refs/heads/main/GetPathMtu.sh

  - Tasks:
      - Run the script from client → webserver
      - Confirm it matches configured MTU

  - This step ensures:
      - MTU is truly supported end‑to‑end
      - No silent fragmentation occurs
      - Accelerated Networking remains in effect

    

### Expected Outcomes
  - By the end of this exercise, students should be able to:
    - Explain why multinic requires manual configuration on RHEL
    - Correctly configure source‑based routing
    - Increase and persist MTU on Azure Linux VMs
    - Validate MTU using Microsoft‑supported tooling
    - Understand how Accelerated Networking interacts with MTU and fragmentation


### Discussion Questions
  - Why does RHEL not automatically handle multinic routing in Azure?
  - What happens when MTU is mismatched between VMs?
  - When should MTU remain at 1500?

This exercise mirrors real Azure support and production scenarios.

## Alternative Multinic Configuration Using NetworkManager Policy Routing (RHEL 9)
### Objective
  - This section describes an alternative multinic configuration technique using NetworkManager policy-based routing.

    The goal of this approach is to:
      - Maintain linear traffic paths between servers
        - eth0 → eth0
        - eth1 → eth1
      - Use separate routing tables per interface
      - Preserve Accelerated Networking behavior
      - Increase MTU only on the secondary interface used for intra-VNet traffic

    This configuration is intended to complement the previous multinic exercise and demonstrate a different implementation method using nmcli.

### Determine Interface Addresses
  - Before configuring routing tables, students must identify the IP address assigned to each NIC.
    Run the following command on both virtual machines:
	 
       ```bash
 	   ip a | grep eth
 	   ```

    Example output:
    
       ```bash
       2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 ...
       inet 10.1.0.5/24 scope global eth0
       3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 ...
       inet 10.1.0.6/24 scope global eth1
       ```
    **Important:** Record both addresses for later routing rules
      - eth0 = primary NIC
      - eth1 = secondary NIC used for accelerated intra-VNet communication

    

### Rename Secondary Network Profile
  - In Azure deployments, the second interface may appear as Wired connection 1.
    ```bash
    nmcli connection modify "Wired connection 1" connection.id "System eth1"
    ```
    
### Why Routes and Routing Rules Are Required
  - Azure attaches multiple NICs to the same subnet, but Linux routing decisions are based on the main routing table by default.
  - Without policy routing:
     - The system chooses only one default route.
     - Return traffic may exit through a different NIC than the one used for ingress.
     - Asymmetric routing can occur, causing dropped packets or degraded performance.
     - Accelerated Networking paths may not be preserved.

   - This configuration solves the problem using policy-based routing:
     - Routing Table 100 is associated with eth0.
     - Routing Table 200 is associated with eth1.

   - Routes define where packets go, while routing rules define when a specific table must be used.

   - Purpose of the Routes
     - ```bash
       10.1.0.0/24 table=100 and table=200
       ```
       Ensures local subnet awareness within each routing table.
     - ```bash
       0.0.0.0/0 10.1.0.1 table=100 and table=200
       ```
       Defines the default gateway per interface.
     - ```bash
       10.1.0.0/24 table=254
       ```
       Keeps the main table aware of the subnet so Linux can select a source address before policy routing takes effect.

     - Static /32 routes
       Force communication between secondary NICs to remain on eth1.

   - Purpose of the Routing Rules
     - ```bash
       priority 100 from <eth0-ip> table 100
       ```
       Traffic originating from eth0 from the first server, it uses routing table 100.
     - ```bash
       priority 100 to <eth0-ip> table 100
       ```
       Replies to eth0 stay symmetric from the first server.
     - ```bash
       priority 200 from <eth1-ip> table 200
       ```
       Traffic originating from eth1 from the second server. It uses routing table 200.
     - ```bash
       priority 200 to <eth1-ip> table 200
       ```
       Ensures symmetric return path on eth1 from the second server.
     - ```bash
       priority 150 to <peer-eth1-ip> table 200
       ```
       Forces traffic destined to the peer secondary interface through eth1.

  This design guarantees deterministic routing behavior while keeping Accelerated Networking fully active.

### Configuration Overview
  - This configuration creates:
      - Routing table 100 for eth0
      - Routing table 200 for eth1
      - Source-based routing rules
      - Destination-based rules for linear traffic between servers
      - MTU increased only on eth1
  - Traffic behavior:
      - Requests entering eth0 exit eth0
      - Requests entering eth1 exit eth1
      - Direct communication between secondary NICs remains isolated

### Client VM Configuration Steps
  - Configure Routing Table for eth0
    ```bash
    nmcli connection modify "System eth0" ipv4.route-table 100
    nmcli connection modify "System eth0" +ipv4.routes "10.1.0.0/24 table=100"
    nmcli connection modify "System eth0" +ipv4.routes "10.1.0.0/24 table=254"
    nmcli connection modify "System eth0" +ipv4.routes "0.0.0.0/0 10.1.0.1 table=100"
    nmcli connection modify "System eth0" +ipv4.routing-rules "priority 100 from 10.1.0.5/32 table 100"
    nmcli connection modify "System eth0" +ipv4.routing-rules "priority 100 to 10.1.0.5/32 table 100"
    nmcli connection down "System eth0" && nmcli connection up "System eth0"
    ```

  - Configure Routing Table for eth1
    ```bash
    nmcli connection modify "System eth1" ipv4.route-table 200
    nmcli connection modify "System eth1" +ipv4.routes "10.1.0.0/24 table=200"
    nmcli connection modify "System eth1" +ipv4.routes "0.0.0.0/0 10.1.0.1 table=200"
    nmcli connection modify "System eth1" +ipv4.routing-rules "priority 200 from 10.1.0.6/32 table 200"
    nmcli connection modify "System eth1" +ipv4.routing-rules "priority 200 to 10.1.0.6/32 table 200"
    nmcli connection modify "System eth1" +ipv4.routes "10.1.0.7/32 10.1.0.1 99 table=200"
    nmcli connection modify "System eth1" +ipv4.routing-rules "priority 150 to 10.1.0.7/32 table 200"
    nmcli connection modify "System eth1" mtu 3900
    nmcli connection down "System eth1" && nmcli connection up "System eth1"
    ```

### Webserver VM Configuration Steps
  - Configure Routing Table for eth0
    ```bash
    nmcli connection modify "System eth0" ipv4.route-table 100
    nmcli connection modify "System eth0" +ipv4.routes "10.1.0.0/24 table=100"
    nmcli connection modify "System eth0" +ipv4.routes "10.1.0.0/24 table=254"
    nmcli connection modify "System eth0" +ipv4.routes "0.0.0.0/0 10.1.0.1 table=100"
    nmcli connection modify "System eth0" +ipv4.routing-rules "priority 100 from 10.1.0.4/32 table 100"
    nmcli connection modify "System eth0" +ipv4.routing-rules "priority 100 to 10.1.0.4/32 table 100"
    nmcli connection down "System eth0" && nmcli connection up "System eth0"
    ```

  - Configure Routing Table for eth1
    ```bash
    nmcli connection modify "System eth1" ipv4.route-table 200
    nmcli connection modify "System eth1" +ipv4.routes "10.1.0.0/24 table=200"
    nmcli connection modify "System eth1" +ipv4.routes "0.0.0.0/0 10.1.0.1 table=200"
    nmcli connection modify "System eth1" +ipv4.routing-rules "priority 200 from 10.1.0.7/32 table 200"
    nmcli connection modify "System eth1" +ipv4.routing-rules "priority 200 to 10.1.0.7/32 table 200"
    nmcli connection modify "System eth1" +ipv4.routes "10.1.0.4/32 10.1.0.1 99 table=200"
    nmcli connection modify "System eth1" +ipv4.routing-rules "priority 150 to 10.1.0.6/32 table 200"
    nmcli connection modify "System eth1" mtu 3900
    nmcli connection down "System eth1" && nmcli connection up "System eth1"
    ```

### Validation

  - After applying the configuration, verify routing behavior:
    ```bash
    ip address show
    ip route show
    ip rule show
    ( nmcli connection show "System eth0" && nmcli  conn show "System eth1" ) | egrep "ipv4.routes|ipv4.routing"
    ```
  - MTU and Connectivity Testing Using Ping
     - From the webserver:
       ```bash
       ping 10.1.0.6 -c 10 -M do -s 3872
       ```
     - From the client:
       ```bash
       ping 10.1.0.5 -c 10 -M do -s 3872
       ```

     - While making the ping test, capture traffic to confirm packets traverse the secondary interface in both servers
       ```bash
       tcpdump -i eth1
       ```
     - Expected behavior:
         - No packet fragmentation
         - Successful replies
         - Confirms MTU alignment and Accelerated Networking path usage
  - Testing webserver Using HTTP
     - From the client
       ```bash
       curl  10.1.0.4
       curl  10.1.0.7
       ```
    - Expected behavior
      Traffic using IP address on eth0 will return from the eth0 on the other server
      Traffic using IP address on eth1 will return from the eth1 on the other server
      
      ```bash
      # curl  10.1.0.4
      Client IP (source):  10.1.0.5
      Server IP (listener): 10.1.0.4
      # curl  10.1.0.7
      Client IP (source):  10.1.0.6
      Server IP (listener): 10.1.0.7
      ```
      
> **Notes:**
  - Accelerated Networking must be enabled on both NICs before increasing MTU.
  - MTU changes apply only to traffic within the same VNet.
  - Only the secondary NIC uses jumbo frames in this exercise.
  - This technique demonstrates a fully NetworkManager-based multinic configuration without modifying system routing scripts.

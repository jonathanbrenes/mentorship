# Network
## Packet Capture
### Create a server running a webserver
Deploy this VM. This will automatically create a VM and set a webserver running on port 80
  
  [![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjonathanbrenes%2Fmentorship%2Frefs%2Fheads%2Fmain%2Fnetwork001.json)
- Capture traffic on port 80. Access the VM public IP from a webbrowser, once the test is done, hit ```Ctrl-C``` to stop the capture
  ``` bash
  tcpdump -i any port 80 and not host 168.63.129.16 # Captures network traffic on port 80 from all interfaces while excluding traffic from the IP address 168.63.129.16 which could be sending health probes to the VM.
  ```

  <details>
     <summary>Screenshot: Capture traffic for port 80, exclude from the Wire server</summary>

	<img src="./.attachments/lab10image39.png" alt="Check for updates." size=950x>
  </details> 

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


### Multinic configuration
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
   - Configure MTU for virtual machines for Linux  https://learn.microsoft.com/azure/virtual-network/how-to-virtual-machine-mtu

  Key points from the documentation:
    - Default MTU: 1500
    - Mellanox Accelerated Networking: up to ~3900
    - MANA Accelerated Networking: up to 9000
    - MTU must match on both ends
    - Fragmented packets bypass accelerated path

  Tasks:
    - Identify supported MTU based on NIC type
    - Increase MTU on both VMs
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
      - Identify maximum supported payload size
      - Calculate effective MTU (payload + headers)
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

  

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

- Make another capture again with tcpdump, don't use any filter this time
  ``` bash
  tcpdump -i any port 80 # Captures network traffic on port 80 from all interfaces.
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

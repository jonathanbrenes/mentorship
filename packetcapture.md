# Packet Capture
## Create a server running a webserver
Deploy this VM. This will automatically create a VM and set a webserver running on port 80
  
  [![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjonathanbrenes%2Fmentorship%2Frefs%2Fheads%2Fmain%2Fpacketcapture.json)
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

- Donwload the file and open it using wireshark

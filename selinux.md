# SELinux  
## Fix webserver blocked by SELinux
### Create a server running a webserver with SELinux enabled

Deploy this VM. This ARM template will automatically create a **RHEL 9.4 VM**, install **httpd**, configure the firewall, and deploy a sample HTML page. This environment is used to demonstrate **SELinux port labeling, file contexts, and httpd permissions**.

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjonathanbrenes%2Fmentorship%2Frefs%2Fheads%2Fmain%2Fselinux001.json)

- Validate HTTPD is running
  After deployment, SSH into the VM and validate the service:

  ```bash
  systemctl status httpd
  ss -tulnp | grep httpd
  ```

- Check SELinux mode
  ```bash
  getenforce
  sestatus
  ```

- Show which ports httpd is allowed to listen on
  ```bash
  semanage port -l | grep http_port_t
  ```

- Expected output includes:
  ```
  80, 81, 443, 488, 8008, 8009, 8443, 9000
  ```


### Fix SELinux Blocking Unauthorized Ports

- Find httpd related messages on the logs
  ```bash
  grep httpd /var/log/messages
  ```

- Using the information provided by setroubleshoot create a new local policy to allow the tcp port

- Once fixed restart the httpd service



### Fix File Context Demonstration

Make sure you can connect the server using the configured port used by httpd

- Check again ```/var/log/messages```. Find more SELinux errors
  ```bash
  grep SELinux /var/log/messages
  ```

- Using the information provided by setroubleshoot, fix the problem

### Final check
Finally test again the webserver make sure you can see a "Hello World"


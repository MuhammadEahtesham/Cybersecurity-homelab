# Cyber Security Homelab
A basic Cyber security homelab where I setup an Active Directory (home lab) that includes Splunk, Kali Linux & Atomic Red Team. Explore how a domain environment works, learn how to ingest events to a SIEM and generate telemetry related to attacks seen in the wild to help you detect them in the future.
## Network Diagram
![](https://github.com/MuhammadEahtesham/Cybersecurity-homelab/blob/main/assets/diagram.png)

## Step-by-Step Guide
 ### Part 1 📜 : Install virtual machine and ISOs for given links: 
- VMWare Fusion pro 13 && VMWare Pro Station 17 : https://techdocs.broadcom.com/us/en/vmware-cis/desktop-hypervisors/fusion-pro/13-0.html
- Windows Server 2019 💿 : http://www.microsoft.com/en-us/evalcenter/download-windows-server-2019
- Windows 10 💿 : https://www.microsoft.com/en-us/software-download/windows10
- Ubuntu Server 💿 : https://ubuntu.com/server
- Kali Linux 💿 : https://www.kali.org/get-kali/#kali-virtual-machines

### Part 2 📜 : Install & Configure Splunk and Sysmon
- In VM Network Editor, add NAT network with IP (192.168.10.0/24). Set all the machines to this network.
- In ubuntu server make this config in /etc/netplan/yaml file for static IP. Also, Sets Google Public DNS (8.8.8.8) to resolve domain names.

  ![](https://github.com/MuhammadEahtesham/Cybersecurity-homelab/blob/main/assets/yamlConfig.png)
- Install splunk in host OS and share the  folder to the vmware https://www.splunk.com/en_us/products/splunk-enterprise.html
- In Splunk server, linux command to make the service run everytime the server starts is;

   ```
   sudo ./splunk enable boot-start -user splunk
  ```
- Install:
  + Sysmon -   https://www.splunk.com/en_us/download/universal-forwarder.html
  + Sysmon config - https://github.com/olafhartong/sysmon-modular/blob/master/sysmonconfig.xml
  + Universal forwarder -   https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon
  
  in windows server and windows target machine.
- To instruct splunk folder to what to send to splunk server (Add inputs.conf to C:\Program Files\SplunkUniversalForwarder\etc\system\local\inputs.conf). Here is the inputs.cof file;
  
  https://github.com/MyDFIR/Active-Directory-Project

### Part 3 📜 : Install & Configure Windows Server and windows target machine
- You can check out this repo for detail instruction - https://github.com/MuhammadEahtesham/Active-Directory
- Promote the server to a domain controller
  
![](https://github.com/MuhammadEahtesham/Cybersecurity-homelab/blob/main/assets/AD.png)

- In AD server manage, Tools -> Active Directory users and Computers, added 2 Organization Unit IT & HR and added 2 users in each unit named as Terry and Jenny  

### Part 4 📜 : D-Day
- In kali, Set static IP (192.168.10.150) config, DNS (8.8.8.8) and Nat gateway (192.168.10.250)
- Install crowbar - https://www.kali.org/tools/crowbar/

   ```
  sudo apt-get upgrade && sudo apt-get update
  sudo apt install -y crowbar
  ```
- Create a directory in desktop "ad-project" and add a password.txt which contains 10 words from rockyou.txt and your target machine password
- Enable Remote Desktop Protocol(RDP) in your target machine;
  + About us -> Advanced System Settings -> Allow remote connections to this computer
  + Select users for RDP
- Brute force this device using the folllowing command:
  ```
  crowbar -b rdp -u tsmith -C passwords.txt -s 192.168.10.100/32 
  ```
  ![](https://github.com/MuhammadEahtesham/Cybersecurity-homelab/blob/main/assets/crowbarAttack.png)

- Activity logged in splunk was ;
  
  ![](https://github.com/MuhammadEahtesham/Cybersecurity-homelab/blob/main/assets/splunkActivity.png)

  4625 -> Event when logon failed
  
  4624 -> Event when logon was successful


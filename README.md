# Failed RDP to IP Geolocation Information
This project is designed to analyze and log failed Remote Desktop Protocol (RDP) connection attempts, providing geolocation information for the associated IP addresses.

### Software Used
- [Microsoft Azure](https://azure.microsoft.com/en-us)
- [Microsoft Sentinel](https://azure.microsoft.com/en-us/products/microsoft-sentinel)
- [Log Analytics Workspace](https://learn.microsoft.com/en-us/azure/azure-monitor/logs/log-analytics-workspace-overview)
- [Microsoft Defender for Cloud]
- 
</br>

### Languages Used
- Powershell: Extract RDP failed logon logs from Windows Event Viewer
<br>

### Utlitlies Used
- ipgeolocation.io: IP Address to Geolocation API

## Steps
1. Under [Azure](https://azure.microsoft.com/en-us) create a Azure VM using Windows 10 (created a resource group named Honeypot)
     - Standard 2 vcpus, 8 GiB memory
     - created a username and password
     - Under *Networking* The NIC network security group, changed to Advanced
     - Created a new Network Security group to allow all traffic inbound <br>
<br> <img src="https://i.imgur.com/CSrLtND.png" width="45%" height="45%"> <br>
2. Created a Log Analytics Workspace
     - Selected the resource group and Created
3. Went to Microsoft Defender for Cloud > Enviromental Settings > Chose *honeypot* under Azure Subscriptions > Turn off SQL Servers > Save
     - Went to Data Collection, and selected All Events > Saved
4. Going back to Log Analytics Workspace > Virtual Machines (deprecated) > Connected the VM to the Log Analytics
5. Went to Microsoft Sentinel > Selected the Honeypot Workspace and added
6. While everything is loading and connecting, went to the Windows VM I created <br>
<br> <img src="https://i.imgur.com/sY6JfXL.png" width="35%" height="35%"> <br>
7. Using RDP on my desktop, I connected using the IP 172.191.135.192, logged in
8. What im trying to gather is all the failed lagin attempts that are shown within Event Viewer, ID 4625 <br>
<br> <img src="https://i.imgur.com/FX4xPhj.png" width="35%" height="35%"> <br>
9. So in order for people to connect, I need to take down the Windows FireWall. You can see here once I disabled it, when I pinged from my personal machine using:
```bash
172.191.135.192 -t
```
<br> <img src="https://i.imgur.com/K1AXuGP.png" width="35%" height="35%"> <br>


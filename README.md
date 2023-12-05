# Failed RDP to IP Geolocation Information
This project is designed to analyze and log failed Remote Desktop Protocol (RDP) connection attempts, providing geolocation information for the associated IP addresses.

### Software Used
- [Microsoft Azure](https://azure.microsoft.com/en-us)
- [Microsoft Sentinel](https://azure.microsoft.com/en-us/products/microsoft-sentinel)
- [Log Analytics Workspace](https://learn.microsoft.com/en-us/azure/azure-monitor/logs/log-analytics-workspace-overview)
- 
</br>

### Languages Used
- Powershell: Extract RDP failed logon logs from Windows Event Viewer
<br>

### Utlitlies Used
- ipgeolocation.io: IP Address to Geolocation API

## Steps
1. Under [Azure](https://azure.microsoft.com/en-us) create a Azure VM using Windows 10 (created a resource group named Honey)
     - Standard 2 vcpus, 8 GiB memory
     - created a username and password
     - Under *Networking* The NIC network security group, changed to Advanced
     - Created a new Network Security group to allow all traffic inbound
<img src="https://i.imgur.com/CSrLtND.png" width="65%" height="65%">
       

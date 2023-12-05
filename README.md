# Failed RDP to IP Geolocation Information
This project is designed to analyze and log failed Remote Desktop Protocol (RDP) connection attempts, providing geolocation information for the associated IP addresses.

### Software Used
- [Microsoft Azure](https://azure.microsoft.com/en-us)
- [Microsoft Sentinel](https://azure.microsoft.com/en-us/products/microsoft-sentinel)
- [Log Analytics Workspace](https://learn.microsoft.com/en-us/azure/azure-monitor/logs/log-analytics-workspace-overview)
- [Microsoft Defender for Cloud](https://learn.microsoft.com/en-us/azure/defender-for-cloud/defender-for-cloud-introduction)

### Languages Used
- Powershell: Extract RDP failed logon logs from Windows Event Viewer

### Utlitlies Used
- [ipgeolocation.io](https://ipgeolocation.io/): IP Address to Geolocation API

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
10. Using PowerShell Ise im using a [powershell script](https://github.com/kyhomelab/Failed-RDP-to-IP-Geolocation-Information/blob/main/Custom%20Security%20Log%20Exporter) in order to pull the event viewer ID 4625 into a notepad <br>
- Utlizing the [IP API](https://ipgeolocation.io/ip-location-api.html) I add it to the 2nd line of the Powershell Script <br>
<br> <img src="https://i.imgur.com/QR2YksZ.png" width="35%" height="35%"> <br>
11. Back to Log Analytics > Tables > Create > New Custom Log (MMA-based)
     - I created a notepad on my main desktop copying the contents of the logs that are found: C:\ProgramData\failed_rdp.log
     - I selected the notepad I created on my desktop into the sample log
12. Heading over to Sentinel > Workbooks > Add Workbook > Edit (Remove the two widgets there) > Add Query > Pasted this script:
```bash
FAILED_RDP_WITH_GEO_CL 

| extend username = extract(@"username:([^,]+)", 1, RawData),

         timestamp = extract(@"timestamp:([^,]+)", 1, RawData),

         latitude = extract(@"latitude:([^,]+)", 1, RawData),

         longitude = extract(@"longitude:([^,]+)", 1, RawData),

         sourcehost = extract(@"sourcehost:([^,]+)", 1, RawData),

         state = extract(@"state:([^,]+)", 1, RawData),

         label = extract(@"label:([^,]+)", 1, RawData),

         destination = extract(@"destinationhost:([^,]+)", 1, RawData),

         country = extract(@"country:([^,]+)", 1, RawData)

| where destination != "samplehost"

| where sourcehost != ""

| summarize event_count=count() by timestamp, label, country, state, sourcehost, username, destination, longitude, latitude
```
<br> 

- I run the query, and make sure the *Visualiztion* is set to MAP
<br> <img src="https://i.imgur.com/ulaG0cQ.png" width="45%" height="45%"> <br>
- You get the end result of this:
<br> <img src="https://i.imgur.com/NKxgO6L.png" width="45%" height="45%"> <br>

## Updates
I let this run for 12 ish hours, seeing how many people are trying to get in. The Api only allows for up to 1k calls a day, and I hit the limit, but Sentinel keeps record and here is both the map and the dashboard.
![Honey](https://i.imgur.com/K6DgbOP.png)
![Honey](https://i.imgur.com/BZfa7zg.png)
![Honey](https://i.imgur.com/qV1GASt.png)


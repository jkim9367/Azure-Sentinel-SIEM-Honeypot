# Azure_Sentinel_SIEM_Honeypot
I setup Azure Sentinel (SIEM) and created virtual machine acting as a honey pot. Observed live attacks, RDP Brute Force, from all around the world and use custom PowerShell script to display the attackers' Geolocation information and plot it on the Azure Sentinel Map.

# Overview

In this project, I will setup Azure Sentinel, cloud-based Security Information and Event Management (SIEM) and virtual machine (VM), which will be the honeypot. Purpose is to monitor and log all attacks different IP addresses from all over the world and display it on a geographical map.
Inspecting only failed remote desktop protocols (RDP) logs and put it on the map.

Overview Diagram:
![image](https://github.com/jkim9367/Azure_Sentinel_SIEM_Honeypot/assets/121040101/af80211a-994e-4129-b4e4-72a7d30c388d)

•	Create VM in azure that is vulnerable by turning off the external firewall and windows firewall. Act as a honeypot.
•	Create a log repository in azure, log analytics workspace, which used to ingest the log from the VM using PowerShell to transform logs.
o	Reason why use PowerShell is because in windows’ log only shows IP address of the source of the attack but not the geolocation.
o	I will use the PowerShell to extract the IP address from the Windows’ log and send it through third party API to obtain the country, state, and province and send the custom log with geographic data send it back to VM.
•	And setup azure sentinel (SIEM), which it will be used to create a map with data of origin of the attack on the honeypot.

# Azure_Sentinel_SIEM_Honeypot
I setup Azure Sentinel (SIEM) and created virtual machine acting as a honey pot. Observed live attacks, RDP Brute Force, from all around the world and use custom PowerShell script to display the attackers' Geolocation information and plot it on the Azure Sentinel Map.

# Overview

In this project, I will setup Azure Sentinel, cloud-based Security Information and Event Management (SIEM) and virtual machine (VM), which will be the honeypot. Purpose is to monitor and log all attacks different IP addresses from all over the world and display it on a geographical map.
Inspecting only failed remote desktop protocols (RDP) logs and put it on the map.

Overview Diagram:

![image](https://github.com/jkim9367/Azure_Sentinel_SIEM_Honeypot/assets/121040101/af80211a-994e-4129-b4e4-72a7d30c388d)

•	Create VM in azure that is vulnerable by turning off the external firewall and windows firewall. Act as a honeypot.

•	Create a log repository in azure, log analytics workspace, which used to ingest the log from the VM using PowerShell to transform logs.

  * Reason why use PowerShell is because in windows’ log only shows IP address of the source of the attack but not the geolocation.
  
  * I will use the PowerShell to extract the IP address from the Windows’ log and send it through third party API to obtain the country, state, and province and send the custom log with geographic data send it back to VM.
  
•	And setup azure sentinel (SIEM), which it will be used to create a map with data of origin of the attack on the honeypot.


# Setting up virtual machine honeypot

On the search bar of the home page of the Azure, search “Virtual machine” to create the honeypot.


![image](https://github.com/jkim9367/Azure_Sentinel_SIEM_Honeypot/assets/121040101/902fbc04-80e5-488d-854b-44229e361471)


Click “create”
Resource group – “HoneyPotProject” - it is logical grouping of resources in Azure that share the same lifespan. (Putting everything is same resource group)
VM name – “honeypot”
Region – “(US) West US 3”
Put in admin username and password
Check licensing
Click next


![image](https://github.com/jkim9367/Azure_Sentinel_SIEM_Honeypot/assets/121040101/8f5d8854-952d-4ab7-9c71-bf84ce4f3391)


![image](https://github.com/jkim9367/Azure_Sentinel_SIEM_Honeypot/assets/121040101/4015835d-adde-45d7-a587-c8bf8fd9b499)


In the networking,
Click “advanced” for the “NIC network security group”  (This is like a firewall) 
Click “create new” 
Remove the default inbound rule.


![image](https://github.com/jkim9367/Azure_Sentinel_SIEM_Honeypot/assets/121040101/5dbad210-34ce-4adc-8db3-10769ff9e44e)


In the inbound rules, click “add an inbound rule”.
“destination port range – “ * ” 
“Priority” – “100”
“Name” – “OPEN_FOR_ALL”
This will allow all traffic from internet into the VM 


![image](https://github.com/jkim9367/Azure_Sentinel_SIEM_Honeypot/assets/121040101/24e54648-3a3a-4a28-9c90-b19ddc8fbe2e)


Click “review and create”


![image](https://github.com/jkim9367/Azure_Sentinel_SIEM_Honeypot/assets/121040101/b7a796d9-59d5-4056-897a-3e9f51804748)


Click “create”


# Create Log Analytics Workspace


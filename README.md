# Azure-Sentinel-SIEM-Honeypot
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

Purpose of creating log analytics workspace is to ingest logs from the VM’s window’s event logs and feed it through the custom log with geographic information and Azure SIEM will connect to this log analytics workspace to display geodata.
In the search of azure, look up “log analytics workspace”
Click “create log analytic workspace”


![image](https://github.com/jkim9367/Azure_Sentinel_SIEM_Honeypot/assets/121040101/7921f554-3200-49fa-b3f6-8044f6758148)


In resource group. Select “honeypotproject”
Name “LAW-honeypot”
Region “West US 2”
Click “review create”


![image](https://github.com/jkim9367/Azure_Sentinel_SIEM_Honeypot/assets/121040101/25579fc1-f337-4061-bf37-a1539ce45114)

![image](https://github.com/jkim9367/Azure_Sentinel_SIEM_Honeypot/assets/121040101/bcfd2b0d-23b0-481b-bc83-e260dccb4f35)


Enable ability to gather logs from VM in security center
In Azure’s search bar, look up “security center” 
In the left tab, click “pricing & settings” 
Click “LAW-honeypot”
Click “Azure defender on” to turn on the defender on
Under “Azure defender for”, click off on “SQL server on machines”
Click save
On left pane, click “data collection”
 Select “all event” and save.


# Connect Log Analytics Workspace to the Virtual Machine

In the Azure search bar, go to “log analytics workspace”
Click “LAW-honeypot”
On left pane, click “virtual machine”


![image](https://github.com/jkim9367/Azure_Sentinel_SIEM_Honeypot/assets/121040101/ddf2cbb3-68f6-4ff5-9ab7-0d701ca02833)


Select “honeypot-vm”


![image](https://github.com/jkim9367/Azure_Sentinel_SIEM_Honeypot/assets/121040101/a0a01cd5-0211-4cdd-bcd5-00fcf8f53df3)


Click connect


# Setting Up SIEM

In the Azure search bar, search “sentinel”
Click create Azure Sentinel 


![image](https://github.com/jkim9367/Azure_Sentinel_SIEM_Honeypot/assets/121040101/06f01114-14f9-443c-a601-1f2c9d6ffc44)


Select “LAW-honeypot” and click “add”


#Accessing Virtual Machine

Search virtual machine in azure and copy the “public IP address”


![image](https://github.com/jkim9367/Azure_Sentinel_SIEM_Honeypot/assets/121040101/466a3a89-e65b-46c4-963b-8cf9d4f21628)


From host machine, open “remote desktop connection”
Paste the Ip address and log in with credential with user create earlier.


![image](https://github.com/jkim9367/Azure_Sentinel_SIEM_Honeypot/assets/121040101/182aa2cd-d7f3-49ea-ac37-a221da1826fd)


Before logging in, I will attempt to login with wrong login twice.


![image](https://github.com/jkim9367/Azure_Sentinel_SIEM_Honeypot/assets/121040101/17b75cf6-f087-48ee-ae4f-2fa2259c15ed)


Logging in:


![image](https://github.com/jkim9367/Azure_Sentinel_SIEM_Honeypot/assets/121040101/7c4f823b-4a1e-4839-ba93-bf662122b9d9)


Go back to “remote desktop connection” on the host machine and purposefully fail attempt login to view it in the windows event log.

In the VM, go to “event viewer” – go to “Windows logs” – “Security”
Need to wait until event load.

This is all logs for this VM

I going to pay attention on event ID 4625 which is failed login attempts.


![image](https://github.com/jkim9367/Azure_Sentinel_SIEM_Honeypot/assets/121040101/a7def601-cdd7-4745-9f46-c14e5391afa5)


If you double click the failed attempt, you can see the IP address in the “network information” –> "source network address"


![image](https://github.com/jkim9367/Azure_Sentinel_SIEM_Honeypot/assets/121040101/48d95c17-26ef-49fb-8dbb-ae50c591fc90)


But you can not view any geolocation, so I will use API and PowerScript to program this process.
I will use ipgeolocation.io to do this.
And use Sentinel (SEIM) to read latitude and longitude and plot the attackers in the map.
Here is the example:


![image](https://github.com/jkim9367/Azure_Sentinel_SIEM_Honeypot/assets/121040101/f4c01b7d-ca84-46dc-ab50-41cca5af8b64)


# Turn Off the Firewall of the VM

I am going to turn off the vm’s firewall to be vulnerable.  

Open wf.msc 

Click “windows defender firewall property” 

Select firewall state: “off” for domain profile, private profile and public profile. 


![image](https://github.com/jkim9367/Azure-Sentinel-SIEM-Honeypot/assets/121040101/6df978e6-f1ce-4536-85f8-11f5343ad353)


Then I can test it by pinging the vm.
If it is able to ping, it means firewall has been successfully turned off and it is now vulnerable. 


![image](https://github.com/jkim9367/Azure-Sentinel-SIEM-Honeypot/assets/121040101/d68cd773-6961-485d-b559-f4d7e6a244df)



# PowerShell code extract the IP of the attacker.

I will use the PowerShell to extract the IP address from the Windows’ log and send it through third party API to obtain the country, state, and province and send the custom log with geographic data send it back to VM.

Here is the PowerShell script  
Save it as “Log_Exporter”


![image](https://github.com/jkim9367/Azure-Sentinel-SIEM-Honeypot/assets/121040101/c789737b-74c1-4407-9afc-fc6cd202d051)


Sign up and get api key in the “https://ipgeolocation.io/”
What it does: 
It looks through the security event and get all failed attempts and put it in separate log files.

Running the PowerShell:


![image](https://github.com/jkim9367/Azure-Sentinel-SIEM-Honeypot/assets/121040101/078c9da5-e28b-40a3-8b4f-ed5ede9f5850)


Once this program runs, it will create log file that only contains failed login attempts 
It will in “C:” -> “ProgramData” -> “failed_rdp” text file


![image](https://github.com/jkim9367/Azure-Sentinel-SIEM-Honeypot/assets/121040101/2d49b087-7938-4636-9cfd-d336ddf06f52)


Inside of the log:


![image](https://github.com/jkim9367/Azure-Sentinel-SIEM-Honeypot/assets/121040101/b98aca3a-b85d-4a00-9b39-e12903e980d1)



Inside, there is sample log because we need to use it to train analytics workspace. When I am using the log in the map, I am going to exclude all sample in the query later.
Last 2 failed login attempts are mine.
And rest are sample failed attempts.

This PowerShell is live, so it I fail login attempt, it will log it current time.


# Creating custom log in Log analytics workspace to bring in the custom log


In the azure, go to log analytics workspace and click LAW-honeypot
 
In the Azure portal, select Log “Analytics workspaces” -> your workspace -> “Tables”.

Select “Create and then New custom log (MMA-based)”:


![image](https://github.com/jkim9367/Azure-Sentinel-SIEM-Honeypot/assets/121040101/6218e9b2-b5a3-4860-9c2d-88839f55385f)


First, need log file. For  the “Sample”, Go to the VM and copy and paste the log from the text file in the “C: -> “ProgramData” -> “failed_rdp”

Paste it on the text file and upload it 


![image](https://github.com/jkim9367/Azure-Sentinel-SIEM-Honeypot/assets/121040101/fd860c1e-150a-48dd-8319-fa97d9be9673)


Press next
In the “record delimiter”, press next


![image](https://github.com/jkim9367/Azure-Sentinel-SIEM-Honeypot/assets/121040101/1672d56d-72c4-41ac-9516-e6a5c322d43a)



In the “collection paths”, select “windows” for type. Put “C:\programdata\failed_rdp.log” for the “path”


![image](https://github.com/jkim9367/Azure-Sentinel-SIEM-Honeypot/assets/121040101/8562a95a-1063-4420-9476-72d9470ee28e)


Press next
Names my custom log “FAILED_RDP_WITH_GEO”


![image](https://github.com/jkim9367/Azure-Sentinel-SIEM-Honeypot/assets/121040101/c3e997a6-08f8-439e-aa96-c8d5439195d1)


Finish creating

To view the custom log, on the left panel, go to “Log” and look up the name of the log, which is “FAILED_RDP_WITH_GEO” and run:


![image](https://github.com/jkim9367/Azure-Sentinel-SIEM-Honeypot/assets/121040101/da63ea7d-d9f5-418a-9ccb-672aef0c9c75)


# Creating custom fields/extract fields from raw log data and training LAW


we have more attempt login log data. Now, I will extract the certain fields from it and separating into different categories.
 
To do this, expend and log, and clock 3 dots, and clisk “extrac field from …”


![image](https://github.com/jkim9367/Azure-Sentinel-SIEM-Honeypot/assets/121040101/20aa37aa-599e-4fa4-ab50-0a1d4dc0d0e4)


Highlight the latitude number, in the “field title” input “latitude” and select “numeric” for field type. And click extract. “Save extraction” if all looks correct.


![image](https://github.com/jkim9367/Azure-Sentinel-SIEM-Honeypot/assets/121040101/4d79b513-298d-41b0-89ff-5a45c9016cfa)




# Setting up amp in sentinel with latitude and longitude




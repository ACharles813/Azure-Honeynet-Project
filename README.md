# Building a SOC + Honeynet in Azure (Live Traffic)
![Cloud Honeynet + SOC](https://github.com/ACharles813/Azure-Honeynet-Project/assets/138184708/7df667fe-7eac-4d37-93d1-311dc73ccf70)



## Introduction
The purpose of this project is to create a mini honeynet in Azure and measure the level of activity for 24 hours in an unsecure environinment, and then 24 hours after hardening the environment. To acheieve this, log sources from various resources are ingested into a Log Analytics workspace. The log sources are then used by Microsoft Sentinel to build attack maps, trigger alerts, and create incidents. The following metrics are then measured for 24 hours prior to applying a few security controls and 24 hours after:

- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious Flows allowed into our honeynet)

## Architecture Before Hardening / Security Controls
![Before Hardening Environment](https://github.com/ACharles813/Azure-Honeynet-Project/assets/138184708/ae6455ee-0ce4-43bd-89f1-cfbf535e5478)


## Architecture After Hardening / Security Controls
![After Hardening Environment](https://github.com/ACharles813/Azure-Honeynet-Project/assets/138184708/a7a723fa-8825-4c31-84ad-fc56b679e98d)



The architecture of the mini honeynet in Azure consists of the following components:

- Virtual Network (VNet)
- Network Security Group (NSG)
- Virtual Machines (2 windows, 1 linux)
- Log Analytics Workspace
- Azure Key Vault
- Azure Storage Account
- Microsoft Sentinel

Before security measures are implemented, the metrics above are left as in and exposed to the internet. The Virtual Machines have their Network Security Groups and built-in firewalls open and vulnerable. Other resources are deployed with public endpoints visible to the internet. Data was collected after 24 hours.

To harden the environment, the following measures were taken:

1. Traffic inward to the Network Security Groups are blocked with the exception of my admin workstation (my IP address). This ensures only authorized and trusted sources are allowed to communicate with the virtual machines.
2. Other resources are then protected by enchancing their built-in firewalls. By doing this, unauthorized access is restricted.
3. Private endpoints were implemented in replace of public endpoints to limit access to the Azure Key Vault and storage containers.

## Attack Maps Before Hardening / Security Controls

The attack map below is of Syslog authorization failures.
![(before) Syslog-SSH-Auth-Fail](https://github.com/ACharles813/Azure-Honeynet-Project/assets/138184708/df3976ec-370d-4fdf-bd47-0ed0af63b303)<br>

The attack map below is of Windows Authentification failures via RDP (remote desktop protocol). 
![(before) Windows-RDP-Auth-Fail](https://github.com/ACharles813/Azure-Honeynet-Project/assets/138184708/920a78f7-695a-4399-ae17-18b826d05f2f)<br>

The attack map below is of a Network Security Group that allowed all traffic inbound.
![(before) NSG-Malicious-Allowed-In](https://github.com/ACharles813/Azure-Honeynet-Project/assets/138184708/3e8d93f6-ad91-4bb9-b255-7a67d2504025)<b>



## Metrics Before Hardening / Security Controls

The following table shows the metrics we measured in our insecure environment for 24 hours:<br>
Start Time 2023-06-23 06:36<br>
Stop Time 2023-06-24 06:36

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 81812
| Syslog                   | 9022
| SecurityAlert            | 4
| SecurityIncident         | 369
| AzureNetworkAnalytics_CL | 2195

## Attack Maps Before Hardening / Security Controls

```All map queries actually returned no results due to no instances of malicious activity for the 24 hour period after hardening.```

## Metrics After Hardening / Security Controls

The following table shows the metrics we measured in our environment for another 24 hours, but after we have applied security controls:<br>
Start Time 2023-06-25 13:22<br>
Stop Time	2023-06-26 13:22

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 11281
| Syslog                   | 25
| SecurityAlert            | 0
| SecurityIncident         | 9
| AzureNetworkAnalytics_CL | 0


The following table shows the net change within the metrics mentioned in both tables above.
| Metric                                        | Percent (%) Change
| --------------------------------------------- | --------------------
| Security Events (Windows VMs)                 | -86.21
| Syslog    (Linux VMs)                         | -99.72
| Security Alert (Microsoft Defender for Cloud) | -100.00
| Security Incident   (Sentinel Incidents)      | -97.56
| NSG Inbound Malicious Flows Allowed           | -100.00


## Conclusion

Microsoft Azure was used to create a mini honeynet. Log Analytics workspace was used to house the log sources. To trigger alerts and create incidents from these log courses, Microsoft Sentinel was used. A list of metrics were used to assess the environment create for 24 hours before hardening and then 24 hours after harding the environment. Based on the net change in the table above, incidents and security events were reduced significantly, thus showing that the security controls applied are effective.


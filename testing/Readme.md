# SetupTestbed.ps1
This script creates a "blank slate" for testing/configuring LME.

Using the Azure CLI, it creates the following:
- A resource group
- A virtual network, subnet, and network security group
- 2 VMs: "DC1," a Windows server, and "LS1," a Linux server
- Client VMs: Windows clients "C1", "C2", etc. up to 16 based on user input 
- Promotes DC1 to a domain controller
- Adds C1 to the managed domain
- Adds a DNS entry pointing to LS1

This script does not install LME; it simply creates a fresh environment that's ready to have LME installed.

## Usage
| **Parameter**          | **Alias** | **Description**                                                                        | **Required**                          |
|------------------------|-----------|----------------------------------------------------------------------------------------|---------------------------------------|
| $ResourceGroup         | -g | The name of the resource group that will be created for storing all testbed resources. | Yes                                   |
| $NumClients            | -n | The number of Windows clients to create; maximum 16; defaults to 1                                    | No                                   |
| $AutoShutdownTime      | | The auto-shutdown time in UTC (HHMM, e.g. 2230, 0000, 1900); auto-shutdown not configured if not provided                            | No |
| $AutoShutdownEmail     | | An email to be notified if a VM is auto-shutdown.                                      | No                                    |
| $AllowedSources       | -s | Comma-Seperated list of CIDR prefixes or IP ranges, e.g. XX.XX.XX.XX/YY,XX.XX.XX.XX/YY,etc..., that are allowed to connect to the VMs via RDP and ssh.                                      | Yes                                    |
| $NoPrompt | -y | Switch, run the script with no prompt (useful for automated runs). By default, the script will prompt the user to review paramters and confirm before continuing. | No |

Example:
```
./SetupTestbed.ps1 -ResourceGroup Example1 -NumClients 2 -AutoShutdownTime 0000 -AllowedSources "1.2.3.4,1.2.3.5" -y
```

## Running Using Azure Shell
| **#** | **Step**                                                                                                                                                                 | **Screenshot**                                                                                 |
|-------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------|
| 1     | Open a cloud shell by navigating to portal.azure.com and clicking the shell icon.                                                                                        | ![image](https://github.com/cisagov/lme/assets/106177711/5bd164fc-5e28-4bca-b95d-757c03fde12e) |
| 2     | Select PowerShell.                                                                                                                                                       | ![image](https://github.com/cisagov/lme/assets/106177711/d7efc95c-1780-4ac7-ae0a-6113bb4e65d1) |
| 3     | Upload `SetupTestbed.ps1` by clicking the "Upload/Download files" icon                                                                                                   | ![image](https://github.com/cisagov/lme/assets/106177711/015510af-d932-4dba-ad03-924c666f8424) |
| 4     | Run the script, providing values for the parameters when promoted (see [Usage](#usage)). The script will take ~20 minutes to run to completion.                           | ![image](https://github.com/cisagov/lme/assets/106177711/2a7b6fc8-c9ff-4e18-9442-b9a26b5fda99) |
| 5     | Save the login credentials printed to the terminal at the end. At this point you can login to each VM using RDP (for the Windows servers) or SSH (for the Linux server). | ![image](https://github.com/cisagov/lme/assets/106177711/71ab3487-541d-4a34-9fc9-e0fb9076dda5) |
| 6     | When you're done testing, simply delete the resource group to clean up all resources created.                                                                            | ![image](https://github.com/cisagov/lme/assets/106177711/6d0af5a0-1f75-4dd9-addf-8c68dc76251c) |

# Extra Functionality:
 
## Clean Up ResourceGroup: 

1. open a shell like before 
2. run command: `az group delete --name [NAME_YOUP_ROVIDED_ABOVE]`

## Disable Internet: 
Run the following commands in the azure shell.  

```powershell
./internet_toggle.ps1 -RG [NAME_YOU_PROVIDED_ABOVE] [-NSG OPTIONAL_NSG_GROUP] [-enable]
```

Flags:
  - enable: deletes the DENYINTERNET/DENYLOADBALANCER rules
  - NSG: sets NSG to a custom NSG if desired [NSG1 default]


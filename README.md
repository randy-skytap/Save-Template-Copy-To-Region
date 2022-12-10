# Save-Template-Copy-To-Region

Demonstrates use of Azure runbooks and leveraging [Skytap's API](https://help.skytap.com/api-quick-start.html#rest-api-quick-start) to: 
1. Create template can be created from a live environment
1. Copy template to another region
1. Use projects to organize
1. Delete prior versions of the template copy process

# Preparation 

Often times the 'Before you Begin' steps seems to be the longest part of the journey.  The script can be run from powershell directly but for extending it we can add to Azure Runbooks 

- In Azure create an [Automation Account](https://learn.microsoft.com/en-us/azure/automation/automation-create-standalone-account?tabs=azureportal) and open the resource.
- In Shared Resources 
    - Create Credentials providing Skytap username and [API key](https://help.skytap.com/kb-find-api-token.html)
    - Create variables for 
      - env_to_backup (id of environment to backup)
      - sleeptime (time delay for checking busy state of template)
      - target_region (destination region to copy to region)

- In Process Automation 'Import a runbook' and upload the .ps1 file in this repository.
  
# Description of code


``` powershell
# Get header setup to connect and make requests.  This leverages credential assets already in Azure
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType] 'Tls12'
$cred = Get-AutomationPSCredential -Name 'Skytap_cred'
$content = 'application/json'
$baseAuth = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $cred.UserName, $cred.GetNetworkCredential().Password)))
$header =  @{"Accept" = $content; Authorization=("Basic {0}" -f $baseAuth); "Content_type" = $content}
$uri = 'https://cloud.skytap.com/'

```

# create resource group
az group create --location=$REGION --resource-group=$RGNAME


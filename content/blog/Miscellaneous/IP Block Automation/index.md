---
title: IP Block Automation
date: 2025-04-18
draft: false
author: Kyd1ct
description: How two simple PowerShell scripts significantly reduced the workload for me and my colleagues.
---

## Introduction
Brute-force alerts were a hefty task for me when I first started my job as a SOC Analyst. At the time, the triage process was entirely manual, which made it rather resource-intensive and time-consuming. In my eyes this approach was not an efficient use of time, so I took the initiative to automate it.
I created two separate scripts - one took the generated log and used AbuseIPDB's API to scan the IP addresses, while the other checked if they were already blocked within our environment. 


## Scripts
### AbuseIPDB Checker
The first script is used to automatically grab IP addresses from the log and check them using the **AbuseIPDB** API. The script takes a **CSV** file with the logs and filters all of the data to extract only valid IP addresses using **RegEx**. The script also sorts through duplicates and returns only unique entries.

```powershell
# Function to validate IP addresses
function Validate-IP {
    param (
        [string]$IP
    )
    # Regular expression for validating an IP address (IPv4)
    $regex = '^((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$'
    return $IP -match $regex
}
 
# Read the CSV file
$csvData = Import-Csv -Path $inputFile
 
# Extract valid IPs from the CSV
$ipAddresses = $csvData | ForEach-Object {
    $_.PSObject.Properties.Value | ForEach-Object {
        $ip = $_
        if (Validate-IP -IP $ip -and $ip -notmatch '^\d+$') {
            $ip
        }
    }
} | Sort-Object -Unique
```

The user also has to provide an output file which defaults to **XLSX** format. They also have an option to provide Abuse score threshold (**-t**), minimum days of retained reports (**-md**), or display the user manual (**-help**) if required.

{{< alert icon="circle-info" cardColor="#5D3FD3" iconColor="#21286b" textColor="#f1faee" >}}
Note: The default threshold is set to **20%** and the timeline of the fetched reports is **30 days**.
{{< /alert >}}

The script utilises the API to obtain the **IP Address**, **Confidence Score**, **ISP**, **Usage Type**, **Location**, **Total Reports**, and potential **Tor Node** identification. The script shows appropriate errors to help with troubleshooting if any issues occur.

Finally, it exports the results to Excel and has the functionality to overwrite files with the same name. If no IPs with Abuse Score over the threshold was found, an Excel sheet is not generated and the script displays a message.

The full script can be found on my [GitHub Page](https://github.com/Kyd1ct/Automation_Scripts/blob/main/Powershell/abuseipdb_check.ps1).

### Blocklist Checker
The second script is made specifically for a blocklist accessible through a URL but it can be altered to suit the user's needs. The user has the following options:
- **-i**: Input file
- **-o**: Output file (optional, otherwise results will be displayed in the command line)
- **-e**: Specify if it should expect an Excel sheet. No flag is needed if the IPs are in a .txt file.
- **-r**: Displays unique IPs with semi-colon as a delimiter. 

{{< alert icon="circle-info" cardColor="#5D3FD3" iconColor="#21286b" textColor="#f1faee" >}}
Note: The -e flag is made to work with Excel sheets created by the former script. Some changes to the script will be necessary if your Excel sheets have a different layout.
{{< /alert >}}

The functionality is simple - it iterates through all the provided IPs and checks if they are present within the blocklist. Any missing addresses will be provided to the user so they can be promptly blocked.

The full script can be found on my [GitHub Page](https://github.com/Kyd1ct/Automation_Scripts/blob/main/Powershell/blocklist.ps1).
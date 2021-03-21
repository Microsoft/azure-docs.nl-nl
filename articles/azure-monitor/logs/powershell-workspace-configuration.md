---
title: Log Analytics maken & configureren met Power shell
description: Log Analytics werk ruimten in Azure Monitor gegevens opslaan van servers in uw on-premises of Cloud infrastructuur. U kunt computer gegevens uit Azure Storage verzamelen wanneer deze worden gegenereerd door Azure Diagnostics.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: d876a380bfc2d318cddc0964266cc3f0a870aa16
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102050984"
---
# <a name="create-and-configure-a-log-analytics-workspace-in-azure-monitor-using-powershell"></a>Een Log Analytics-werk ruimte maken en configureren in Azure Monitor met behulp van Power shell
Dit artikel bevat twee code voorbeelden die laten zien hoe u een Log Analytics-werk ruimte maakt en configureert in Azure Monitor.  


> [!NOTE]
> Log Analytics eerder Operational Insights is aangeroepen. Dit is de reden waarom het de naam is die wordt gebruikt in de cmdlets.


## <a name="prerequisites"></a>Vereisten
Deze voor beelden werken met versie 1.0.0 of hoger van de module AZ. OperationalInsights.

## <a name="create-workspace"></a>Werkruimte maken
Met het volgende voorbeeld script maakt u een werk ruimte zonder configuratie van de gegevens bron. 

```powershell
$ResourceGroup = "my-resource-group"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique across all Azure subscriptions - Get-Random helps with this for the example code
$Location = "westeurope"

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
```

## <a name="create-workspace-and-configure-data-sources"></a>Een werk ruimte maken en gegevens bronnen configureren

Met het volgende voorbeeld script wordt een werk ruimte gemaakt en worden meerdere gegevens bronnen geconfigureerd. Deze gegevens bronnen zijn alleen vereist als u virtuele machines bewaken met behulp van de [log Analytics-agent](../agents/log-analytics-agent.md).

Met dit script worden de volgende functies uitgevoerd:

1. Een werkruimte maken
2. De beschik bare oplossingen weer geven
3. Oplossingen toevoegen aan de werk ruimte
4. Opgeslagen Zoek opdrachten importeren
5. Opgeslagen Zoek opdrachten exporteren
6. Een computer groep maken
7. Verzamelen van IIS-logboeken inschakelen op computers waarop de Windows-agent is geïnstalleerd
8. Prestatie meter items voor logische schijven verzamelen van Linux-computers (% used inodes; Beschik bare mega bytes; Percentage gebruikte ruimte; Schijf overdrachten per seconde; Schijf lezen per seconde; Schrijf bewerkingen per seconde)
9. Syslog-gebeurtenissen verzamelen van Linux-computers
10. Fout-en waarschuwings gebeurtenissen verzamelen uit het toepassings gebeurtenis logboek van Windows-computers
11. Het prestatie meter item geheugen beschik bare Mbytes verzamelen van Windows-computers
12. Een aangepast logboek verzamelen

```powershell
$ResourceGroup = "my-resource-group"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique across all Azure subscriptions - Get-Random helps with this for the example code
$Location = "westeurope"

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Perf | where ObjectName == "LogicalDisk" and CounterName == "Current Disk Queue Length" and InstanceName == "C:"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1",
    "description": "Example custom log datasource",
    "inputs": [
        {
            "location": {
            "fileSystemLocations": {
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ],
                "linuxFileTypeLogPaths": [ "/var/logs" ]
                }
            },
        "recordDelimiter": {
            "regexDelimiter": {
                "pattern": "\\n",
                "matchIndex": 0,
                "matchIndexSpecified": true,
                "numberedGroup": null
                }
            }
        }
    ],
    "extractions": [
        {
            "extractionName": "TimeGenerated",
            "extractionType": "DateTime",
            "extractionProperties": {
                "dateTimeExtraction": {
                    "regex": null,
                    "joinStringRegex": null
                    }
                }
            }
        ]
    }
"@

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

# List enabled solutions
(Get-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json

# Create Computer Group based on a query
New-AzOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzOperationalInsightsLinuxPerformanceCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernel syslog collection"
Enable-AzOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs

New-AzOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

> [!NOTE]
> De notatie voor de **CustomLogRawJson** -para meter waarmee de configuratie voor een aangepast logboek wordt gedefinieerd, kan ingewikkeld zijn. Gebruik [Get-AzOperationalInsightsDataSource](/powershell/module/az.operationalinsights/get-azoperationalinsightsdatasource) om de configuratie van een bestaand aangepast logboek op te halen. De eigenschap **Properties** is de configuratie die vereist is voor de para meter **CustomLogRawJson** .

In het bovenstaande voor beeld is regexDelimiter gedefinieerd als ' \\ n ' voor een nieuwe regel. Het logboek scheidings teken kan ook een tijds tempel zijn.  Dit zijn de ondersteunde indelingen:

| Indeling | JSON RegEx-indeling gebruikt twee `\\` voor elke `\` in een standaard-regex, zodat de test in een regex-app verkort `\\` tot `\` |
| --- | --- |
| `YYYY-MM-DD HH:MM:SS` | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` |
| `M/D/YYYY HH:MM:SS AM/PM` | `(([0-1]\\d)|[0-9])/(([0-3]\\d)|(\\d))/((\\d{2})|(\\d{4}))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]\\s(AM|PM|am|pm)` |
| `dd/MMM/yyyy HH:MM:SS` | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|May|Apr|Jul|Jun|Aug|Oct|Sep|Nov|Dec|jan|feb|mar|may|apr|jul|jun|aug|oct|sep|nov|dec)\\/((19|20)[0-9][0-9]))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9])` |
| `MMM dd yyyy HH:MM:SS` | `(((?:Jan(?:uary)?|Feb(?:ruary)?|Mar(?:ch)?|Apr(?:il)?|May|Jun(?:e)?|Jul(?:y)?|Aug(?:ust)?|Sep(?:tember)?|Sept|Oct(?:ober)?|Nov(?:ember)?|Dec(?:ember)?)).*?((?:(?:[0-2]?\\d{1})|(?:[3][01]{1})))(?![\\d]).*?((?:(?:[1]{1}\\d{1}\\d{1}\\d{1})|(?:[2]{1}\\d{3})))(?![\\d]).*?((?:(?:[0-1][0-9])|(?:[2][0-3])|(?:[0-9])):(?:[0-5][0-9])(?::[0-5][0-9])?(?:\\s?(?:am|AM|pm|PM))?))` |
| `yyMMdd HH:mm:ss` | `([0-9]{2}([0][1-9]|[1][0-2])([0-2][0-9]|[3][0-1])\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` |
| `ddMMyy HH:mm:ss` | `(([0-2][0-9]|[3][0-1])([0][1-9]|[1][0-2])[0-9]{2}\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s?([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0-1]?[0-9]|[2][0-3]):([0-5][0-9]):([0-5][0-9])` |
| `MMM  d HH:mm:ss` <br> twee spaties na MMM | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` |
| `dd/MMM/yyyy:HH:mm:ss +zzzz` <br> waar + + of a- <br> zzzz tijd verschuiving | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\/((19|20)[0-9][0-9]):([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])\\s[\\+|\\-][0-9]{4})` |
| `yyyy-MM-ddTHH:mm:ss` <br> De T is een letterlijke letter T | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))T((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` |

## <a name="troubleshooting"></a>Problemen oplossen
Wanneer u een werk ruimte maakt die in de afgelopen 14 dagen is verwijderd en de status voor het [voorlopig verwijderen](../logs/delete-workspace.md#soft-delete-behavior)heeft, kan de bewerking afwijken, afhankelijk van de configuratie van uw werk ruimte:
1. Als u dezelfde naam voor de werk ruimte, de resource groep, het abonnement en de regio opgeeft als in de verwijderde werk ruimte, wordt uw werk ruimte hersteld, met inbegrip van de bijbehorende gegevens, configuratie en verbonden agents.
2. Als u dezelfde naam voor de werk ruimte gebruikt, maar een andere resource groep, abonnement of regio, krijgt u een fout melding *de naam van de werk ruimte is niet uniek* of *conflict*. Volg deze stappen om de werk ruimte eerst te herstellen en permanent verwijderen uit te voeren om de tijdelijke verwijdering te onderdrukken en uw werk ruimte permanent te verwijderen en een nieuwe werk ruimte met dezelfde naam te maken:
   * Uw werkruimte [herstellen](../logs/delete-workspace.md#recover-workspace)
   * Uw werkruimte [permanent verwijderen](../logs/delete-workspace.md#permanent-workspace-delete)
   * Een nieuwe werk ruimte maken met dezelfde werkruimte naam


## <a name="next-steps"></a>Volgende stappen
* [Raadpleeg log Analytics Power shell-cmdlets](/powershell/module/az.operationalinsights/) voor meer informatie over het gebruik van Power shell voor configuratie van log Analytics.

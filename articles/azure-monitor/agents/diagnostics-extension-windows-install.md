---
title: Windows Azure Diagnostics-extensie (WAD) installeren en configureren
description: Meer informatie over het installeren en configureren van de diagnostische Windows-extensie. Lees ook hoe een beschrijving van hoe de gegevens worden opgeslagen in en Azure Storage account.
services: azure-monitor
author: bwren
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 3ff752b673c49047551c48c4c8693b00d7b5edeb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787401"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Windows Azure Diagnostics-extensie (WAD) installeren en configureren
[De diagnostische Azure-extensie](diagnostics-extension-overview.md) is een agent in Azure Monitor die bewakingsgegevens verzamelt van het gastbesturingssysteem en workloads van virtuele Azure-machines en andere rekenbronnen. In dit artikel vindt u meer informatie over het installeren en configureren van de diagnostische Windows-extensie en een beschrijving van hoe de gegevens worden opgeslagen in en Azure Storage account.

De extensie voor diagnostische gegevens is geïmplementeerd als een extensie voor virtuele [machines](../../virtual-machines/extensions/overview.md) in Azure, zodat dezelfde installatieopties worden ondersteund met behulp van Resource Manager-sjablonen, PowerShell en CLI. Zie [Extensies en functies van virtuele machines voor Windows](../../virtual-machines/extensions/features-windows.md) voor meer informatie over het installeren en onderhouden van extensies voor virtuele machines.

## <a name="overview"></a>Overzicht
Wanneer u windows Azure de diagnostische extensie configureert, moet u een opslagaccount opgeven waar alle opgegeven gegevens worden verzonden. U kunt eventueel een of meer gegevenss *sinks toevoegen om* de gegevens naar verschillende locaties te verzenden.

- Azure Monitor sink: prestatiegegevens van gasten verzenden naar Azure Monitor Metrics.
- Event Hub-sink: verzend gastprestaties en logboekgegevens naar Azure Event Hubs om door te sturen buiten Azure. Deze sink kan niet worden geconfigureerd in de Azure Portal.


## <a name="install-with-azure-portal"></a>Installeren met Azure Portal
U kunt de diagnostische extensie installeren en configureren op een afzonderlijke virtuele machine in de Azure Portal die u een interface biedt in plaats van rechtstreeks met de configuratie te werken. Wanneer u de diagnostische extensie inschakelen, wordt automatisch een standaardconfiguratie met de meest voorkomende prestatiemeters en gebeurtenissen gebruikt. U kunt deze standaardconfiguratie wijzigen op basis van uw specifieke vereisten.

> [!NOTE]
> Hieronder worden de meest voorkomende instellingen voor de extensie voor diagnostische gegevens beschreven. Zie Extensieschema voor diagnostische gegevens van Windows voor meer informatie over [alle configuratieopties.](diagnostics-extension-schema-windows.md)

1. Open het menu voor een virtuele machine in de Azure Portal.

2. Klik op **Diagnostische instellingen** in **de sectie Bewaking** van het VM-menu.

3. Klik **op Bewaking op gastniveau inschakelen** als de extensie voor diagnostische gegevens nog niet is ingeschakeld.

   ![Bewaking inschakelen](media/diagnostics-extension-windows-install/enable-monitoring.png)

4. Er wordt een nieuw Azure Storage-account gemaakt voor de VM met de naam wordt gebaseerd op de naam van de resourcegroep voor de VM en er wordt een standaardset prestatiemeters en logboeken voor gasten geselecteerd.

   ![Diagnostische instellingen](media/diagnostics-extension-windows-install/diagnostic-settings.png)

5. Selecteer op **het tabblad Prestatiemeters** de metrische gastgegevens die u wilt verzamelen van deze virtuele machine. Gebruik de **aangepaste** instelling voor geavanceerdere selectie.

   ![Prestatiemeteritems](media/diagnostics-extension-windows-install/performance-counters.png)

6. Selecteer op **het tabblad** Logboeken de logboeken die u van de virtuele machine wilt verzamelen. Logboeken kunnen worden verzonden naar opslag of Event Hubs, maar niet naar Azure Monitor. Gebruik de [Log Analytics-agent om](../agents/log-analytics-agent.md) gastlogboeken te verzamelen voor Azure Monitor.

   ![Schermopname van het tabblad Logboeken met verschillende logboeken geselecteerd voor een virtuele machine.](media/diagnostics-extension-windows-install/logs.png)

7. Geef op **het tabblad Crashdumps** processen op voor het verzamelen van geheugendumps na een crash. De gegevens worden voor de diagnostische instelling naar het opslagaccount geschreven en u kunt eventueel een blobcontainer opgeven.

   ![Crashdumps](media/diagnostics-extension-windows-install/crash-dumps.png)

8. Geef op **het tabblad Sinks** op of de gegevens moeten worden verzenden naar andere locaties dan Azure Storage. Als u **Azure Monitor,** worden prestatiegegevens van gasten verzonden naar Azure Monitor Metrics. U kunt de Event Hubs-sink niet configureren met behulp van Azure Portal.

   ![Schermopname van het tabblad Sinks met de optie Diagnostische gegevens verzenden Azure Monitor ingeschakeld.](media/diagnostics-extension-windows-install/sinks.png)
   
   Als u geen door het systeem toegewezen identiteit hebt ingeschakeld die is geconfigureerd voor uw virtuele machine, ziet u mogelijk de onderstaande waarschuwing wanneer u een configuratie met de Azure Monitor-sink opgeslagen. Klik op de banner om de door het systeem toegewezen identiteit in te stellen.
   
   ![Beheerde entiteit](media/diagnostics-extension-windows-install/managed-entity.png)

9. In de **Agent kunt** u het opslagaccount wijzigen, het schijfquotum instellen en opgeven of diagnostische infrastructuurlogboeken moeten worden verzameld.  

   ![Schermopname van het tabblad Agent met de optie om het opslagaccount in te stellen.](media/diagnostics-extension-windows-install/agent.png)

10. Klik **op Opslaan** om de configuratie op te slaan. 

> [!NOTE]
> Hoewel de configuratie voor de extensie voor diagnostische gegevens kan worden opgemaakt in JSON of XML, wordt elke configuratie die wordt uitgevoerd in de Azure Portal altijd opgeslagen als JSON. Als u XML gebruikt met een andere configuratiemethode en vervolgens de configuratie wijzigt met de Azure Portal, worden de instellingen gewijzigd in JSON.

## <a name="resource-manager-template"></a>Resource Manager-sjabloon
Zie Bewaking en diagnostische gegevens gebruiken met een [Windows-VM](../../virtual-machines/extensions/diagnostics-template.md) en Azure Resource Manager over het implementeren van de diagnostische extensie met Azure Resource Manager sjablonen. 

## <a name="azure-cli-deployment"></a>Azure CLI-implementatie
De Azure CLI kan worden gebruikt voor het implementeren van de Azure Diagnostics-extensie op een bestaande virtuele machine met [az vm extension set,](/cli/azure/vm/extension#az_vm_extension_set) zoals in het volgende voorbeeld. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

De beveiligde instellingen worden gedefinieerd in het [element PrivateConfig](diagnostics-extension-schema-windows.md#privateconfig-element) van het configuratieschema. Hieronder volgt een minimaal voorbeeld van een beveiligd instellingenbestand dat het opslagaccount definieert. Zie [Voorbeeldconfiguratie](diagnostics-extension-schema-windows.md#privateconfig-element) voor volledige details van de persoonlijke instellingen.

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```

De openbare instellingen worden gedefinieerd in het [element Openbaar](diagnostics-extension-schema-windows.md#publicconfig-element) van het configuratieschema. Hieronder volgt een minimaal voorbeeld van een openbaar instellingenbestand waarmee u diagnostische infrastructuurlogboeken, één prestatiemeter en één gebeurtenislogboek kunt verzamelen. Zie [Voorbeeldconfiguratie](diagnostics-extension-schema-windows.md#publicconfig-element) voor volledige informatie over de openbare instellingen.

```JSON
{
  "StorageAccount": "mystorageaccount",
  "WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": 5120,
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
          {
            "counterSpecifier": "\\Processor Information(_Total)\\% Processor Time",
            "unit": "Percent",
            "sampleRate": "PT60S"
          }
        ]
      },
      "WindowsEventLog": {
        "scheduledTransferPeriod": "PT1M",
        "DataSource": [
          {
            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
          }
        ]
      }
    }
  }
}
```



## <a name="powershell-deployment"></a>PowerShell-implementatie
PowerShell kan worden gebruikt om de Azure Diagnostics-extensie te implementeren op een bestaande virtuele machine met behulp van [Set-AzVMDiagnosticsExtension,](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) zoals in het volgende voorbeeld. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

De persoonlijke instellingen worden gedefinieerd in het [element PrivateConfig,](diagnostics-extension-schema-windows.md#privateconfig-element)terwijl de openbare instellingen worden gedefinieerd in het [element Openbaar](diagnostics-extension-schema-windows.md#publicconfig-element) van het configuratieschema. U kunt er ook voor kiezen om de details van het opslagaccount op te geven als parameters van de Set-AzVMDiagnosticsExtension cmdlet in plaats van ze op te nemen in de persoonlijke instellingen.

Hieronder volgt een minimaal voorbeeld van een configuratiebestand waarmee u diagnostische infrastructuurlogboeken, één prestatiemeter en één gebeurtenislogboek kunt verzamelen. Zie [Voorbeeldconfiguratie voor](diagnostics-extension-schema-windows.md#publicconfig-element) volledige details van de persoonlijke en openbare instellingen. 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000,
                "DiagnosticInfrastructureLogs": {
                    "scheduledTransferLogLevelFilter": "Error"
                },
                "PerformanceCounters": {
                    "scheduledTransferPeriod": "PT1M",
                    "PerformanceCounterConfiguration": [
                        {
                            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                            "sampleRate": "PT3M",
                            "unit": "percent"
                        }
                    ]
                },
                "WindowsEventLog": {
                    "scheduledTransferPeriod": "PT1M",
                        "DataSource": [
                        {
                            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                        }
                    ]
                }
            }
        },
        "StorageAccount": "mystorageaccount",
        "StorageType": "TableAndBlob"
    },
    "PrivateConfig": {
        "storageAccountName": "mystorageaccount",
        "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
    }
}
```

Zie ook [PowerShell gebruiken om een Azure Diagnostics in te stellen op een virtuele machine met Windows.](../../virtual-machines/extensions/diagnostics-windows.md)

## <a name="data-storage"></a>Gegevensopslag
De volgende tabel bevat de verschillende typen gegevens die worden verzameld uit de extensie voor diagnostische gegevens en of ze zijn opgeslagen als een tabel of een blob. De gegevens die in tabellen zijn opgeslagen, kunnen ook worden opgeslagen in blobs, afhankelijk van de [instelling StorageType](diagnostics-extension-schema-windows.md#publicconfig-element) in uw openbare configuratie.


| Gegevens | Opslagtype | Description |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Tabel | Wijzigingen in de diagnostische controle en configuratie. |
| WADDirectoriesTable | Tabel | De directories die door de diagnostische monitor worden bewaakt.  Dit omvat IIS-logboeken, logboeken met mislukte IIS-aanvragen en aangepaste directories.  De locatie van het bloblogboekbestand wordt opgegeven in het veld Container en de naam van de blob bevindt zich in het veld RelativePath.  In het veld AbsolutePath worden de locatie en naam van het bestand aangegeven zoals het bestond op de virtuele Azure-machine. |
| WadLogsTable | Tabel | Logboeken die zijn geschreven in code met behulp van de traceerlistener. |
| WADPerformanceCountersTable | Tabel | Prestatiemeters. |
| WADWindowsEventLogsTable | Tabel | Windows-gebeurtenislogboeken. |
| wad-iis-failedreqlogfiles | Blob | Bevat informatie uit logboeken met mislukte IIS-aanvragen. |
| wad-iis-logfiles | Blob | Bevat informatie over IIS-logboeken. |
| 'aangepast' | Blob | Een aangepaste container op basis van het configureren van de directories die worden bewaakt door de diagnostische monitor.  De naam van deze blobcontainer wordt opgegeven in WADDirectoriesTable. |

## <a name="tools-to-view-diagnostic-data"></a>Hulpprogramma's om diagnostische gegevens weer te geven
Er zijn verschillende hulpprogramma's beschikbaar om de gegevens weer te geven nadat deze zijn overgebracht naar de opslag. Bijvoorbeeld:

* Server Explorer in Visual Studio: als u de Azure Tools for Microsoft Visual Studio hebt geïnstalleerd, kunt u het Azure Storage-knooppunt in Server Explorer gebruiken om alleen-lezen blob- en tabelgegevens van uw Azure-opslagaccounts weer te geven. U kunt gegevens weergeven uit uw lokale opslagemulatoraccount en ook uit opslagaccounts die u hebt gemaakt voor Azure. Zie Bladeren door en beheren van [opslagresources met Server Explorer.](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) is een zelfstandige app waarmee u eenvoudig kunt werken met Azure Storage in Windows, OSX en Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) bevat Azure Diagnostics Manager waarmee u de diagnostische gegevens kunt bekijken, downloaden en beheren die worden verzameld door de toepassingen die worden uitgevoerd in Azure.

## <a name="next-steps"></a>Volgende stappen
- Zie Send data from Windows Azure diagnostics extension to Event Hubs (Gegevens verzenden vanuit windows Azure Diagnostics-extensie naar Event Hubs voor meer informatie over het doorsturen van [bewakingsgegevens](diagnostics-extension-stream-event-hubs.md) naar Azure Event Hubs.

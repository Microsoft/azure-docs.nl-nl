---
title: De Windows Azure Diagnostics-extensie (WAD) installeren en configureren
description: Meer informatie over het installeren en configureren van de Windows diagnostische extensie. Daarnaast leert u hoe u een beschrijving van de manier waarop de gegevens worden opgeslagen in en Azure Storage account kunt lezen.
services: azure-monitor
author: bwren
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 174f372f9dbe8dc0449c7f9b9f5b34c6206f92de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101708557"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>De Windows Azure Diagnostics-extensie (WAD) installeren en configureren
[Azure Diagnostics-extensie](diagnostics-extension-overview.md) is een agent in azure monitor die bewakings gegevens van het gast besturingssysteem en werk belastingen van virtuele Azure-machines en andere reken bronnen verzamelt. Dit artikel bevat informatie over het installeren en configureren van de Windows diagnostische uitbrei ding en een beschrijving van de manier waarop de gegevens worden opgeslagen in en Azure Storage-account.

De uitbrei ding van diagnostische gegevens wordt geïmplementeerd als extensie van een [virtuele machine](../../virtual-machines/extensions/overview.md) in azure, zodat deze dezelfde installatie opties ondersteunt met Resource Manager-sjablonen, Power shell en cli. Zie [extensies en functies voor virtuele machines voor Windows](../../virtual-machines/extensions/features-windows.md) voor meer informatie over het installeren en onderhouden van extensies van virtuele machines.

## <a name="overview"></a>Overzicht
Wanneer u de diagnostische uitbrei ding van Windows Azure configureert, moet u een opslag account opgeven waarin alle opgegeven gegevens worden verzonden. U kunt eventueel een of meer gegevens- *sinks* toevoegen om de gegevens naar verschillende locaties te verzenden.

- Azure Monitor Sink: verzend gegevens van gast prestaties naar Azure Monitor metrieken.
- Event hub-Sink: verzend gast prestaties en logboek gegevens naar Azure Event hubs om buiten Azure door te sturen. Deze Sink kan niet worden geconfigureerd in de Azure Portal.


## <a name="install-with-azure-portal"></a>Installeren met Azure Portal
U kunt de diagnostische extensie installeren en configureren op een afzonderlijke virtuele machine in de Azure Portal die u een interface biedt in plaats van rechtstreeks met de configuratie te werken. Wanneer u de uitbrei ding voor diagnostische gegevens inschakelt, wordt automatisch een standaard configuratie gebruikt met de meest voorkomende prestatie meter items en-gebeurtenissen. U kunt deze standaard configuratie aanpassen op basis van uw specifieke vereisten.

> [!NOTE]
> Hieronder worden de meest voorkomende instellingen voor de uitbrei ding van diagnostische gegevens beschreven. Zie voor meer informatie over alle configuratie opties [Windows diagnostische-extensie schema](diagnostics-extension-schema-windows.md).

1. Open het menu voor een virtuele machine in de Azure Portal.

2. Klik op **Diagnostische instellingen** in de sectie **bewaking** van het VM-menu.

3. Klik op **bewaking op gast niveau inschakelen** als de uitbrei ding voor diagnostische gegevens nog niet is ingeschakeld.

   ![Bewaking inschakelen](media/diagnostics-extension-windows-install/enable-monitoring.png)

4. Er wordt een nieuw Azure Storage-account gemaakt voor de virtuele machine met de naam is gebaseerd op de naam van de resource groep voor de virtuele machine en er worden een standaardset met prestatie meter items en logboeken voor de gast geselecteerd.

   ![Diagnostische instellingen](media/diagnostics-extension-windows-install/diagnostic-settings.png)

5. Selecteer op het tabblad **prestatie meter items** de gegevens over de gast die u wilt verzamelen van deze virtuele machine. Gebruik de **aangepaste** instelling voor geavanceerdere selectie.

   ![Prestatiemeteritems](media/diagnostics-extension-windows-install/performance-counters.png)

6. Selecteer op het tabblad **Logboeken** de logboeken die u wilt verzamelen van de virtuele machine. Logboeken kunnen worden verzonden naar opslag-of event hubs, maar niet naar Azure Monitor. Gebruik de [log Analytics-agent](../agents/log-analytics-agent.md) om gast logboeken te verzamelen in azure monitor.

   ![Scherm afbeelding toont het tabblad logboeken met verschillende logboeken die voor een virtuele machine zijn geselecteerd.](media/diagnostics-extension-windows-install/logs.png)

7. Geef op het tabblad **crash dumps** de gewenste processen op voor het verzamelen van geheugen dumps na een crash. De gegevens worden naar het opslag account geschreven voor de diagnostische instelling en u kunt desgewenst een BLOB-container opgeven.

   ![Crashdumps](media/diagnostics-extension-windows-install/crash-dumps.png)

8. Geef op het tabblad **sinks** op of de gegevens naar andere locaties dan Azure Storage moeten worden verzonden. Als u **Azure monitor** selecteert, worden de prestatie gegevens van de gast naar Azure monitor metrieken verzonden. U kunt de Sink voor Event hubs niet configureren met behulp van de Azure Portal.

   ![Scherm afbeelding toont het tabblad sinks met de optie diagnostische gegevens verzenden naar Azure Monitor ingeschakeld.](media/diagnostics-extension-windows-install/sinks.png)
   
   Als u geen door het systeem toegewezen identiteit hebt ingeschakeld die voor uw virtuele machine is geconfigureerd, ziet u mogelijk de volgende waarschuwing wanneer u een configuratie opslaat met de Sink Azure Monitor. Klik op de banner om de door het systeem toegewezen identiteit in te scha kelen.
   
   ![Beheerde entiteit](media/diagnostics-extension-windows-install/managed-entity.png)

9. In de **agent** kunt u het opslag account wijzigen, het schijf quotum instellen en opgeven of Logboeken met diagnostische infra structuur moeten worden verzameld.  

   ![Scherm afbeelding toont het tabblad agent met de optie om het opslag account in te stellen.](media/diagnostics-extension-windows-install/agent.png)

10. Klik op **Opslaan** om de configuratie op te slaan. 

> [!NOTE]
> Hoewel de configuratie voor de diagnostische gegevens in JSON of XML kan worden geformatteerd, worden alle configuraties die in de Azure Portal worden uitgevoerd, altijd opgeslagen als JSON. Als u XML gebruikt met een andere configuratie methode en vervolgens uw configuratie wijzigt met de Azure Portal, worden de instellingen gewijzigd in JSON.

## <a name="resource-manager-template"></a>Resource Manager-sjabloon
Zie [controle en diagnose gebruiken met een Windows-VM en Azure Resource Manager sjablonen](../../virtual-machines/extensions/diagnostics-template.md) voor het implementeren van de diagnostische uitbrei ding met Azure Resource Manager sjablonen. 

## <a name="azure-cli-deployment"></a>Implementatie van Azure CLI
De Azure CLI kan worden gebruikt om de Azure Diagnostics-extensie op een bestaande virtuele machine te implementeren met behulp van [AZ VM extension set](/cli/azure/vm/extension#az-vm-extension-set) , zoals in het volgende voor beeld. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

De beveiligde instellingen worden gedefinieerd in het [PrivateConfig-element](diagnostics-extension-schema-windows.md#privateconfig-element) van het configuratie schema. Hieronder volgt een mini maal voor beeld van een bestand met beveiligde instellingen waarmee het opslag account wordt gedefinieerd. Bekijk de [voorbeeld configuratie](diagnostics-extension-schema-windows.md#privateconfig-element) voor de volledige details van de persoonlijke instellingen.

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```

De open bare instellingen worden gedefinieerd in het [open bare element](diagnostics-extension-schema-windows.md#publicconfig-element) van het configuratie schema. Hieronder volgt een mini maal voor beeld van een bestand met open bare instellingen waarmee u logboeken met diagnostische infra structuur, één prestatie meter item en één gebeurtenis logboek kunt verzamelen. Bekijk de [voorbeeld configuratie](diagnostics-extension-schema-windows.md#publicconfig-element) voor de volledige details van de open bare instellingen.

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
Power shell kan worden gebruikt om de Azure Diagnostics-extensie te implementeren op een bestaande virtuele machine met behulp van [set-AzVMDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) , zoals in het volgende voor beeld. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

De persoonlijke instellingen worden gedefinieerd in het [PrivateConfig-element](diagnostics-extension-schema-windows.md#privateconfig-element), terwijl de open bare instellingen worden gedefinieerd in het [open bare element](diagnostics-extension-schema-windows.md#publicconfig-element) van het configuratie schema. U kunt er ook voor kiezen om de details van het opslag account op te geven als para meters van de cmdlet Set-AzVMDiagnosticsExtension in plaats van deze op te nemen in de persoonlijke instellingen.

Hieronder volgt een mini maal voor beeld van een configuratie bestand dat het verzamelen van logboeken met diagnostische infra structuur, één prestatie meter item en één gebeurtenis logboek mogelijk maakt. Bekijk de [voorbeeld configuratie](diagnostics-extension-schema-windows.md#publicconfig-element) voor de volledige details van de persoonlijke en open bare instellingen. 

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

Zie ook [Power shell gebruiken om Azure Diagnostics in te scha kelen op een virtuele machine waarop Windows wordt uitgevoerd](../../virtual-machines/extensions/diagnostics-windows.md).

## <a name="data-storage"></a>Gegevensopslag
De volgende tabel geeft een lijst van de verschillende typen gegevens die worden verzameld uit de diagnostische uitbrei ding en of ze worden opgeslagen als een tabel of BLOB. De gegevens die zijn opgeslagen in tabellen kunnen ook worden opgeslagen in blobs, afhankelijk van de [instelling para](diagnostics-extension-schema-windows.md#publicconfig-element) in uw open bare configuratie.


| Gegevens | Opslagtype | Description |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Tabel | Diagnostische monitor-en configuratie wijzigingen. |
| WADDirectoriesTable | Tabel | Mappen die door de diagnostische monitor worden bewaakt.  Dit zijn onder andere IIS-logboeken, IIS-aanvraag logboeken en aangepaste directory's.  De locatie van het BLOB-logboek bestand wordt opgegeven in het container veld en de naam van de BLOB bevindt zich in het veld RelativePath.  Het veld AbsolutePath geeft de locatie en de naam van het bestand aan zoals het aanwezig is op de virtuele Azure-machine. |
| WadLogsTable | Tabel | Logboeken geschreven in code met behulp van de traceer-listener. |
| WADPerformanceCountersTable | Tabel | Prestatie meter items. |
| WADWindowsEventLogsTable | Tabel | Windows-gebeurtenis Logboeken. |
| wad-IIS-failedreqlogfiles | Blob | Bevat informatie uit logboeken voor mislukte aanvragen van IIS. |
| wad-IIS-logboek bestanden | Blob | Bevat informatie over IIS-logboeken. |
| instel | Blob | Een aangepaste container die is gebaseerd op het configureren van mappen die worden bewaakt door de diagnostische monitor.  De naam van deze BLOB-container wordt opgegeven in WADDirectoriesTable. |

## <a name="tools-to-view-diagnostic-data"></a>Hulpprogram ma's voor het weer geven van diagnostische gegevens
Er zijn verschillende hulpprogram ma's beschikbaar voor het weer geven van de gegevens nadat deze zijn overgebracht naar opslag. Bijvoorbeeld:

* Server Explorer in Visual Studio: als u de Azure-Hulpprogram Ma's voor micro soft Visual Studio hebt geïnstalleerd, kunt u het knoop punt Azure Storage in Server Explorer gebruiken om alleen-lezen Blob-en tabel gegevens uit uw Azure-opslag accounts weer te geven. U kunt gegevens weer geven vanuit uw lokale opslag emulator-account en ook van opslag accounts die u hebt gemaakt voor Azure. Zie [opslag resources doorzoeken en beheren met Server Explorer](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)voor meer informatie.
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) is een zelfstandige app waarmee u eenvoudig met Azure Storage gegevens kunt werken in Windows, OSX en Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) bevat Azure Diagnostics Manager waarmee u de diagnostische gegevens kunt weer geven, downloaden en beheren die zijn verzameld door de toepassingen die worden uitgevoerd op Azure.

## <a name="next-steps"></a>Volgende stappen
- Zie [gegevens verzenden van de Windows Azure Diagnostics-extensie naar Event hubs](diagnostics-extension-stream-event-hubs.md) voor meer informatie over het door sturen van bewakings gegevens naar Azure Event hubs.

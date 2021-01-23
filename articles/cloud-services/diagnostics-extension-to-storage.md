---
title: Diagnostische gegevens opslaan en weergeven in Azure Storage
description: Meer informatie over het verzamelen van diagnostische gegevens van Azure in een Azure Storage-account, zodat u deze kunt bekijken met een van de beschik bare hulpprogram ma's.
services: azure-monitor
author: bwren
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/01/2016
ms.author: bwren
ms.subservice: diagnostic-extension
ms.openlocfilehash: 4962d5f048cf41eca50a77a0dedad3cef48ac1f0
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740071"
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Diagnostische gegevens opslaan en weergeven in Azure Storage

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

Diagnostische gegevens worden niet permanent opgeslagen, tenzij u deze overdraagt naar de Microsoft Azure-opslagemulator of naar Azure Storage. Eenmaal in de opslag kan het worden weer gegeven met een van de beschik bare hulpprogram ma's.

## <a name="specify-a-storage-account"></a>Een opslag account opgeven
U geeft het opslag account op dat u wilt gebruiken in het ServiceConfiguration. cscfg-bestand. De account gegevens worden gedefinieerd als een connection string in een configuratie-instelling. In het volgende voor beeld ziet u de standaard connection string gemaakt voor een nieuw Cloud service project in Visual Studio:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

U kunt deze connection string wijzigen om account gegevens op te geven voor een Azure-opslag account.

Afhankelijk van het type diagnostische gegevens dat wordt verzameld, gebruikt Azure Diagnostics de Blob service of de Table service. De volgende tabel geeft een lijst van de gegevens bronnen die persistent zijn en de indeling.

| Gegevensbron | Opslag indeling |
| --- | --- |
| Azure-logboeken |Tabel |
| IIS 7,0-logboeken |Blob |
| Logboeken van Azure Diagnostics-infrastructuur |Tabel |
| Logboeken met traceringen van mislukte aanvragen |Blob |
| Windows-gebeurtenis logboeken |Tabel |
| Prestatiemeteritems |Tabel |
| Crashdumps |Blob |
| Aangepaste foutenlogboeken |Blob |

## <a name="transfer-diagnostic-data"></a>Diagnostische gegevens overdragen
Voor SDK 2,5 en hoger kan de aanvraag voor het overdragen van diagnostische gegevens plaatsvinden via het configuratie bestand. U kunt Diagnostische gegevens op geplande intervallen overdragen zoals opgegeven in de configuratie.

Voor SDK 2,4 en eerder kunt u aanvragen voor het overdragen van de diagnostische gegevens via het configuratie bestand en programmatisch. Via de programmatische benadering kunt u ook on-demand overdrachten uitvoeren.

> [!IMPORTANT]
> Wanneer u Diagnostische gegevens overbrengt naar een Azure-opslag account, worden er kosten in rekening gebracht voor de opslag resources die door uw diagnostische gegevens worden gebruikt.
> 
> 

## <a name="store-diagnostic-data"></a>Diagnostische gegevens opslaan
Logboek gegevens worden opgeslagen in BLOB-of tabel opslag met de volgende namen:

**Tabellen**

* **WadLogsTable** : Logboeken geschreven in code met behulp van de traceer-listener.
* **WADDiagnosticInfrastructureLogsTable** : diagnostische monitor-en configuratie wijzigingen.
* **WADDirectoriesTable** : de mappen die door de diagnostische monitor worden bewaakt.  Dit zijn onder andere IIS-logboeken, IIS-aanvraag logboeken en aangepaste directory's.  De locatie van het BLOB-logboek bestand wordt opgegeven in het container veld en de naam van de BLOB bevindt zich in het veld RelativePath.  Het veld AbsolutePath geeft de locatie en de naam van het bestand aan zoals het aanwezig is op de virtuele Azure-machine.
* **WADPerformanceCountersTable** : prestatie meter items.
* **WADWindowsEventLogsTable** : Windows-gebeurtenis Logboeken.

**Blobs**

* **wad-Control-container** – (alleen voor SDK 2,4 en eerder) bevat de XML-configuratie bestanden die de diagnostische gegevens van Azure beheren.
* **wad-IIS-failedreqlogfiles** – bevat informatie uit logboeken voor mislukte aanvragen van IIS.
* **wad-IIS-logboek bestanden** – bevat informatie over IIS-logboeken.
* **aangepast** : een aangepaste container die is gebaseerd op het configureren van mappen die worden bewaakt door de diagnostische monitor.  De naam van deze BLOB-container wordt opgegeven in WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Hulpprogram ma's voor het weer geven van diagnostische gegevens
Er zijn verschillende hulpprogram ma's beschikbaar voor het weer geven van de gegevens nadat deze zijn overgebracht naar opslag. Bijvoorbeeld:

* Server Explorer in Visual Studio: als u de Azure-Hulpprogram Ma's voor micro soft Visual Studio hebt geïnstalleerd, kunt u het knoop punt Azure Storage in Server Explorer gebruiken om alleen-lezen Blob-en tabel gegevens uit uw Azure-opslag accounts weer te geven. U kunt gegevens weer geven vanuit uw lokale opslag emulator-account en ook van opslag accounts die u hebt gemaakt voor Azure. Zie [opslag resources doorzoeken en beheren met Server Explorer](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)voor meer informatie.
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) is een zelfstandige app waarmee u eenvoudig met Azure Storage gegevens kunt werken in Windows, OSX en Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) bevat Azure Diagnostics Manager waarmee u de diagnostische gegevens kunt weer geven, downloaden en beheren die zijn verzameld door de toepassingen die worden uitgevoerd op Azure.

## <a name="next-steps"></a>Volgende stappen
[De stroom in een Cloud Services toepassing traceren met Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)



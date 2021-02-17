---
title: Versie geschiedenis van configuratie schema voor Windows Azure Diagnostics extension (WAD)
description: Relevant voor het verzamelen van prestatie meter items in azure Virtual Machines, VM Scale Sets, Service Fabric en Cloud Services.
ms.subservice: diagnostic-extension
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/29/2020
ms.openlocfilehash: 3b6dc0e19c181ebf4df2778633f981258023e046
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100610700"
---
# <a name="windows-azure-diagnostics-extension-wad-configuration-schema-versions-and-history"></a>Versies en geschiedenis van configuratie schema's voor Windows Azure Diagnostics extension (WAD)
Dit artikel bevat de versie geschiedenis van de [wad-schema versies (Azure Diagnostics extension for Windows)](diagnostics-extension-overview.md) die als onderdeel van de Microsoft Azure SDK worden geleverd.  


## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>De verzend grafiek van de Azure SDK-en diagnose versies  

|Azure SDK-versie | Uitbrei ding van diagnostische gegevens | Model|  
|------------------|-------------------------------|------|  
|1.x               |1.0                            |invoeg toepassing|  
|2,0-2,4         |1.0                            |invoeg toepassing|  
|2,5               |1.2                            |switch|  
|2,6               |1.3                            |"|  
|2.7               |1.4                            |"|  
|2.8               |1.5                            |"|  
|2.9               |1.6                            |"|
|2,96              |1,7                            |"|
|2,96              |1.8                            |"|
|2,96              |1.8.1                          |"|
|2,96              |1,9                            |"|
|2,96              |1.11                           |"|


 Azure Diagnostics versie 1,0 eerst verzonden in een invoeg toepassings model, wat betekent dat wanneer u de Azure SDK installeert, u beschikt over de versie van Azure Diagnostics.  

 Vanaf SDK 2,5 (diagnostische gegevens van versie 1,2) is Azure Diagnostics naar een uitbreidings model gegaan. De hulpprogram ma's voor het gebruik van nieuwe functies zijn alleen beschikbaar in nieuwere Azure-Sdk's, maar bij elke service die Azure diagnosticeert, wordt de meest recente verzend versie rechtstreeks vanuit Azure opgehaald. Iedereen die nog steeds gebruikmaakt van SDK 2,5, laadt bijvoorbeeld de meest recente versie die wordt weer gegeven in de vorige tabel, ongeacht of de nieuwe functies worden gebruikt.  

## <a name="schemas-index"></a>Index schema's  
Verschillende versies van Azure Diagnostics gebruiken verschillende configuratie schema's. Schema 1,0 en 1,2 zijn afgeschaft. Voor meer informatie over versie 1,3 en hoger raadpleegt u [Diagnostische gegevens 1,3 en later configuratie schema](diagnostics-extension-schema-windows.md)  

## <a name="version-history"></a>Versiegeschiedenis

### <a name="diagnostics-extension-111"></a>Uitbrei ding voor diagnostische gegevens 1,11
Er is ondersteuning toegevoegd voor de Azure Monitor sink. Deze sink is alleen van toepassing op prestatie meter items. Hiermee wordt het verzenden van prestatie meter items die zijn verzameld op uw virtuele machine, VMSS of Cloud service, mogelijk om Azure Monitor als aangepaste metrische gegevens. De Azure Monitor Sink ondersteunt:
* Ophalen van alle prestatie meter items die zijn verzonden naar Azure Monitor via de [API voor Azure monitor metrische gegevens.](/rest/api/monitor/metrics/list)
* Waarschuwingen voor alle prestatie meter items die worden verzonden naar Azure Monitor via de nieuwe [Unified Alerts-ervaring](../platform/alerts-overview.md) in azure monitor
* De operator wild behandelen in prestatie meter items als de dimensie ' instance ' op uw metrische waarde. Als u bijvoorbeeld het \* prestatie meter item ' Logical ()/DiskWrites/sec ' hebt verzameld, kunt u de dimensie ' instance ' filteren en splitsen voor het uitzetten of waarschuwen van schrijf bewerkingen per seconde voor elke logische schijf (C:, D:, enzovoort).

Azure Monitor definiëren als een nieuwe sink in de configuratie van de diagnostische gegevens
```json
"SinksConfig": {
    "Sink": [
        {
            "name": "AzureMonitorSink",
            "AzureMonitor": {}
        },
    ]
}
```

```XML
<SinksConfig>  
  <Sink name="AzureMonitorSink">
      <AzureMonitor/>
  </Sink>
</SinksConfig>
```
> [!NOTE]
> Voor het configureren van de Azure Monitor-Sink voor klassieke Vm's en de klassieke CLoud service zijn meer para meters vereist die moeten worden gedefinieerd in de persoonlijke configuratie van de Diagnostics-extensie.
>
> Raadpleeg voor meer informatie de [documentatie over het gedetailleerde diagnose schema voor diagnostische gegevens.](diagnostics-extension-schema-windows.md)

Vervolgens kunt u de prestatie meter items zodanig configureren dat deze worden doorgestuurd naar het Azure Monitor sink.
```json
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "AzureMonitorSink",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT1M",
            "unit": "percent"
        }
    ]
},
```
```XML
<PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
</PerformanceCounters>
```

### <a name="diagnostics-extension-19"></a>Uitbrei ding voor diagnostische gegevens 1,9
Docker-ondersteuning is toegevoegd.


### <a name="diagnostics-extension-181"></a>Uitbrei ding van diagnostische gegevens 1.8.1
Kan een SAS-token opgeven in plaats van een opslag account sleutel in de persoonlijke configuratie. Als er een SAS-token wordt gegeven, wordt de sleutel van het opslag account genegeerd.


```json
{
    "storageAccountName": "diagstorageaccount",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    }
}
```

```xml
<PrivateConfig>
    <StorageAccount name="diagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    <SecondaryStorageAccounts>
        <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>
</PrivateConfig>
```


### <a name="diagnostics-extension-18"></a>Uitbrei ding voor diagnostische gegevens 1,8
Het opslag type is toegevoegd aan PublicConfig. Para kan *Table*, *BLOB*, *TableAndBlob* zijn. De standaard instelling is *tabel* .


```json
{
    "WadCfg": {
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

```xml
<PublicConfig>
    <WadCfg />
    <StorageAccount>diagstorageaccount</StorageAccount>
    <StorageType>TableAndBlob</StorageType>
</PublicConfig>
```


### <a name="diagnostics-extension-17"></a>Uitbrei ding voor diagnostische gegevens 1,7
De mogelijkheid om naar EventHub te sturen is toegevoegd.

### <a name="diagnostics-extension-15"></a>Uitbrei ding voor diagnostische gegevens 1,5
Het sinks-element is toegevoegd en de mogelijkheid om diagnostische gegevens naar [Application Insights](../app/cloudservices.md) te verzenden, waardoor het eenvoudiger wordt om problemen op te sporen in uw toepassing en op het niveau van het systeem en de infra structuur.

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>Azure SDK 2,6 en diagnostische uitbrei ding 1,3
Voor Cloud service projecten in Visual Studio zijn de volgende wijzigingen aangebracht. (Deze wijzigingen zijn ook van toepassing op latere versies van de Azure SDK.)

* De lokale emulator biedt nu ondersteuning voor diagnostische gegevens. Met deze wijziging kunt u Diagnostische gegevens verzamelen en ervoor zorgen dat uw toepassing de juiste traceringen maakt tijdens het ontwikkelen en testen in Visual Studio. Met de connection string `UseDevelopmentStorage=true` wordt het verzamelen van diagnostische gegevens ingeschakeld terwijl u uw Cloud service project in Visual Studio uitvoert met behulp van de Azure Storage-emulator. Alle diagnostische gegevens worden verzameld in het opslag account (Development Storage).
* Het opslag account voor diagnostische gegevens connection string (micro soft. WindowsAzure. plugins. Diagnostics. Connections Tring) wordt eenmaal in het service configuratie bestand (. cscfg) opgeslagen. In azure SDK 2,5 is het opslag account voor diagnostische gegevens opgegeven in het bestand Diagnostics. wadcfgx.

Er zijn enkele belang rijke verschillen tussen de manier waarop de connection string in azure SDK 2,4 en eerder heeft gewerkt en hoe deze werkt in azure SDK 2,6 of hoger.

* In azure SDK 2,4 en eerder werd de connection string tijdens runtime gebruikt door de diagnostische invoeg toepassing om de gegevens van het opslag account voor het overbrengen van Diagnostische logboeken te verkrijgen.
* In azure SDK 2,6 en hoger gebruikt Visual Studio de diagnostische connection string om de diagnostische extensie te configureren met de juiste gegevens over het opslag account tijdens het publiceren. Met de connection string kunt u verschillende opslag accounts definiëren voor verschillende service configuraties die door Visual Studio worden gebruikt bij het publiceren. Omdat de diagnostische invoeg toepassing echter niet meer beschikbaar is (na Azure SDK 2,5), kan het. cscfg-bestand de uitbrei ding voor diagnostische gegevens niet inschakelen. U moet de uitbrei ding afzonderlijk inschakelen via hulpprogram ma's als Visual Studio of Power shell.
* Om het proces voor het configureren van de diagnostische uitbrei ding met Power shell te vereenvoudigen, bevat de pakket uitvoer van Visual Studio ook de open bare configuratie-XML voor de uitbrei ding van diagnostische gegevens voor elke rol. Visual Studio gebruikt de diagnostische connection string om de gegevens van het opslag account die aanwezig zijn in de open bare configuratie in te vullen. De open bare configuratie bestanden worden gemaakt in de map Extensions en volgen het patroon `PaaSDiagnostics.<RoleName>.PubConfig.xml` . Alle implementaties op basis van Power shell kunnen dit patroon gebruiken om elke configuratie aan een rol toe te wijzen.
* De connection string in het cscfg-bestand wordt ook gebruikt door de Azure Portal om toegang te krijgen tot de diagnostische gegevens, zodat deze kunnen worden weer gegeven op het tabblad **bewaking** . De connection string is nodig om de service zo te configureren dat uitgebreide bewakings gegevens in de portal worden weer gegeven.

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Projecten migreren naar Azure SDK 2,6 en hoger
Bij de migratie van Azure SDK 2,5 naar Azure SDK 2,6 of hoger, als u een diagnostische opslag account hebt opgegeven in het. wadcfgx-bestand, blijft het aanwezig. Als u wilt profiteren van de flexibiliteit van het gebruik van verschillende opslag accounts voor verschillende opslag configuraties, moet u de connection string hand matig toevoegen aan uw project. Als u een project migreert vanuit Azure SDK 2,4 of eerder naar Azure SDK 2,6, worden de diagnostische verbindings reeksen bewaard. Houd echter rekening met de wijzigingen in de manier waarop verbindings reeksen worden behandeld in azure SDK 2,6, zoals is opgegeven in de vorige sectie.

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Hoe Visual Studio het opslag account voor diagnostische gegevens bepaalt
* Als er een diagnostische connection string is opgegeven in het. cscfg-bestand, gebruikt Visual Studio dit om de diagnostische extensie te configureren tijdens het publiceren en bij het genereren van de XML-bestanden van de open bare configuratie tijdens het pakket.
* Als er geen diagnostische gegevens connection string is opgegeven in het cscfg-bestand, wordt Visual Studio opnieuw ingesteld voor het gebruik van het opslag account dat is opgegeven in het. wadcfgx-bestand om de diagnostische extensie te configureren tijdens het publiceren en het genereren van de open bare XML-configuratie bestanden wanneer het pakket wordt verpakt.
* De diagnostische connection string in het. cscfg-bestand heeft voor rang op het opslag account in het. wadcfgx-bestand. Als er een diagnostische connection string is opgegeven in het cscfg-bestand, gebruikt Visual Studio dat en wordt het opslag account in. wadcfgx genegeerd.

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>Wat gebeurt er met het bijwerken van de verbindings reeksen voor de ontwikkelings opslag... selectie vakje doen?
Het selectie vakje voor het bijwerken van de **verbindings reeksen voor het ontwikkelen van opslag voor diagnostische gegevens en caching met Microsoft Azure Storage-account referenties bij het publiceren naar Microsoft Azure** biedt u een handige manier om de verbindings reeksen voor een ontwikkelings opslag account bij te werken met het Azure Storage-account dat tijdens het publiceren is opgegeven.

Stel dat u dit selectie vakje inschakelt en de diagnostische connection string opgeeft `UseDevelopmentStorage=true` . Wanneer u het project naar Azure publiceert, werkt Visual Studio automatisch de diagnostische connection string bij met het opslag account dat u in de wizard Publiceren hebt opgegeven. Als er echter een echt opslag account is opgegeven als de diagnostische connection string, wordt dat account in plaats daarvan gebruikt.

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Verschillen in de functionaliteit van diagnostische functies tussen de Azure SDK 2,4 en eerdere en Azure SDK 2,5 en hoger
Als u uw project bijwerkt vanuit Azure SDK 2,4 naar Azure SDK 2,5 of hoger, moet u rekening houdt met de volgende verschillen in de diagnostische functies.

* **Configuratie-api's zijn afgeschaft** : programmatische configuratie van diagnostische gegevens is beschikbaar in azure SDK 2,4 of eerdere versies, maar is afgeschaft in azure SDK 2,5 of hoger. Als uw diagnostische configuratie op dit moment in code is gedefinieerd, moet u die instellingen opnieuw configureren in het gemigreerde project, zodat diagnostische gegevens blijven werken. Het diagnostische configuratie bestand voor Azure SDK 2,4 is Diagnostics. wadcfg en Diagnostics. wadcfgx voor Azure SDK 2,5 en hoger.
* **Diagnostische gegevens voor Cloud service toepassingen kunnen alleen worden geconfigureerd op het niveau van de rol, niet op het niveau van de instantie.**
* **Elke keer dat u uw app implementeert, wordt de diagnostische configuratie bijgewerkt.** dit kan leiden tot pariteits problemen als u de diagnostische configuratie wijzigt van Server Explorer en vervolgens uw app opnieuw implementeert.
* **In azure SDK 2,5 en hoger zijn crash dumps geconfigureerd in het configuratie bestand voor diagnostische gegevens, niet in code** – als u crash dumps in code hebt geconfigureerd, moet u de configuratie hand matig overbrengen van code naar het configuratie bestand, omdat de crash dumps niet worden overgedragen tijdens de migratie naar Azure SDK 2,6.


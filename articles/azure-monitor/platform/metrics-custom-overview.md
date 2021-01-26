---
title: Aangepaste metrische gegevens in Azure Monitor (preview-versie)
description: Meer informatie over aangepaste metrische gegevens in Azure Monitor en hoe deze worden gemodelleerd.
author: anirudhcavale
ms.author: ancav
services: azure-monitor
ms.topic: conceptual
ms.date: 01/25/2021
ms.subservice: metrics
ms.openlocfilehash: ce081896292ec92c41dabc735df828ed167d86e7
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788499"
---
# <a name="custom-metrics-in-azure-monitor-preview"></a>Aangepaste metrische gegevens in Azure Monitor (preview-versie)

Wanneer u resources en toepassingen in azure implementeert, kunt u beginnen met het verzamelen van telemetrie om inzicht te krijgen in de prestaties en de status. In azure zijn enkele metrische gegevens voor u beschikbaar. Deze metrische gegevens worden [Standard of platform](./metrics-supported.md)genoemd. Ze zijn echter beperkt. 

Mogelijk wilt u een aantal aangepaste prestatie-indica toren of bedrijfsspecifieke metrische gegevens verzamelen om meer inzicht te krijgen. Deze **aangepaste** metrische gegevens kunnen worden verzameld via de telemetrie van uw toepassing, een agent die wordt uitgevoerd op uw Azure-resources of zelfs een systeem voor het bewaken van een externe locatie en rechtstreeks aan Azure monitor is verzonden. Nadat u deze hebt gepubliceerd op Azure Monitor, kunt u bladeren, query's en waarschuwingen uitvoeren op aangepaste metrische gegevens voor uw Azure-resources en-toepassingen naast de standaard gegevens die door Azure worden verzonden.

Azure Monitor aangepaste metrische gegevens zijn actueel in open bare preview. 

## <a name="methods-to-send-custom-metrics"></a>Methoden voor het verzenden van aangepaste metrische gegevens

Aangepaste metrische gegevens kunnen via verschillende methoden naar Azure Monitor worden verzonden:
- Instrumenteer uw toepassing met behulp van de Azure-toepassing Insights-SDK en verzend aangepaste telemetrie naar Azure Monitor. 
- Installeer de Azure Monitor agent (preview) op uw [Windows-of Linux Azure-VM](azure-monitor-agent-overview.md) en gebruik een [regel voor gegevens verzameling](data-collection-rule-azure-monitor-agent.md) om prestatie meter items te verzenden naar Azure monitor metrieken.
- Installeer de Windows Azure Diagnostics (WAD)-extensie op [uw Azure-VM](collect-custom-metrics-guestos-resource-manager-vm.md), [schaalset voor virtuele machines](collect-custom-metrics-guestos-resource-manager-vmss.md), [klassieke virtuele](collect-custom-metrics-guestos-vm-classic.md)machine of [klassieke Cloud Services](collect-custom-metrics-guestos-vm-cloud-service-classic.md) en verzend prestatie meter items naar Azure monitor. 
- Installeer de [InfluxData-telegrafa-agent](collect-custom-metrics-linux-telegraf.md) op uw virtuele Azure Linux-machine en verzend metrische gegevens met behulp van de invoeg toepassing Azure monitor-uitvoer.
- Aangepaste metrische gegevens [rechtstreeks naar de Azure Monitor rest API](./metrics-store-custom-rest-api.md)verzenden `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics` .

## <a name="pricing-model-and-retention"></a>Prijs model en retentie

Controleer de [Azure monitor prijs pagina](https://azure.microsoft.com/pricing/details/monitor/) voor meer informatie over wanneer facturering wordt ingeschakeld voor aangepaste metrische gegevens en metrische query's. Specifieke prijs informatie voor alle metrische gegevens, zoals aangepaste metrische gegevens en metrische query's, zijn beschikbaar op deze pagina. In samen vatting zijn er geen kosten verbonden aan het opnemen van standaard metrische gegevens (platform metrieken) in Azure Monitor metrische opslag, maar aangepaste metrische gegevens worden kosten in rekening gebracht wanneer ze algemene Beschik baarheid invoeren. Met metrische API-query's kunnen kosten in rekening worden gebracht.

Aangepaste metrische gegevens worden bewaard voor [dezelfde hoeveelheid tijd als de metrische gegevens](data-platform-metrics.md#retention-of-metrics)van het platform. 

> [!NOTE]  
> Gegevens die worden verzonden naar Azure Monitor via de Application Insights SDK worden gefactureerd als opgenomen logboek gegevens. Alleen extra metrische kosten worden alleen in rekening gebracht als de functie voor het Application Insights onderdeel [waarschuwing inschakelen op aangepaste metrische dimensies](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) is geselecteerd. Met dit selectie vakje worden gegevens naar de data base van de Azure Monitor metriek verzonden met behulp van de aangepaste Metrics-API om de complexere waarschuwingen toe te staan.  Meer informatie over het [Application Insights prijs model](../app/pricing.md#pricing-model) en de [prijzen in uw regio](https://azure.microsoft.com/pricing/details/monitor/).


## <a name="how-to-send-custom-metrics"></a>Aangepaste metrische gegevens verzenden

Wanneer u aangepaste metrische gegevens naar Azure Monitor stuurt, moet u het volgende informatie punt of elke waarde vermelden.

### <a name="authentication"></a>Verificatie
Als u aangepaste metrische gegevens naar Azure Monitor wilt verzenden, moet de entiteit die de metrische gegevens indient, een geldig Azure Active Directory (Azure AD)-token in de **Bearer** -header van de aanvraag hebben. Er zijn een aantal ondersteunde manieren om een geldig Bearer-token te verkrijgen:
1. [Beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md). Geeft een identiteit aan een Azure-resource zelf, zoals een virtuele machine. Managed Service Identity (MSI) is ontworpen om resources machtigingen te geven om bepaalde bewerkingen uit te voeren. Een voor beeld is het toestaan van een resource over het verzenden van metrische gegevens. Aan een resource of het MSI-bestand kunnen **bewakings gegevens** voor de uitgever van machtigingen worden verleend voor een andere resource. Met deze machtiging kan het MSI-bestand ook metrische gegevens verzenden voor andere resources.
2. [Azure AD-Service-Principal](../../active-directory/develop/app-objects-and-service-principals.md). In dit scenario kan een Azure AD-toepassing of-service worden toegewezen aan machtigingen voor het verzenden van metrische gegevens over een Azure-resource.
Azure Monitor valideert het toepassings token met behulp van open bare Azure AD-sleutels om de aanvraag te verifiëren. De rol van uitgever van de bestaande **bewakings metrieken** heeft deze machtiging al. Deze is beschikbaar in de Azure Portal. De Service-Principal, afhankelijk van de resources waarvoor de aangepaste metrische gegevens worden verstrekt, kan de rol van de **Uitgever van metrische gegevens voor bewaking** aan het vereiste bereik krijgen. Voor beelden zijn een abonnement, resource groep of specifieke resource.

> [!TIP]  
> Wanneer u een Azure AD-token aanvraagt om aangepaste metrische gegevens te verzenden, moet u ervoor zorgen dat de doel groep of de bron waarvoor het token wordt aangevraagd, is `https://monitoring.azure.com/` . Zorg ervoor dat u de opvolging '/' opgeeft.

### <a name="subject"></a>Onderwerp
Met deze eigenschap wordt vastgelegd voor welke Azure-Resource-ID de aangepaste metrische gegevens worden gerapporteerd. Deze informatie wordt gecodeerd in de URL van de API-aanroep die wordt gemaakt. Elke API kan alleen metrische waarden voor één Azure-resource indienen.

> [!NOTE]  
> U kunt geen aangepaste metrische gegevens verzenden op basis van de resource-ID van een resource groep of abonnement.


### <a name="region"></a>Regio
Met deze eigenschap wordt vastgelegd in welke Azure-regio de resource waarvoor u de metrische gegevens uitbrengt, wordt geïmplementeerd. Metrische gegevens moeten worden verzonden naar hetzelfde Azure Monitor regionale eind punt als de regio waarin de resource is geïmplementeerd. Aangepaste metrische gegevens voor een virtuele machine die is geïmplementeerd in West-Verenigde Staten, moeten bijvoorbeeld worden verzonden naar het regionale Azure Monitor eind punt Westus. De regio gegevens worden ook gecodeerd in de URL van de API-aanroep.

> [!NOTE]  
> Tijdens de open bare preview zijn aangepaste metrische gegevens alleen beschikbaar in een subset van Azure-regio's. Een lijst met ondersteunde regio's wordt beschreven in een latere sectie van dit artikel.
>
>

### <a name="timestamp"></a>Tijdstempel
Elk gegevens punt dat naar Azure Monitor wordt verzonden, moet worden gemarkeerd met een tijds tempel. Deze tijds tempel legt de datum/tijd vast waarop de metrische waarde wordt gemeten of verzameld. Azure Monitor worden metrische gegevens met tijds tempels geaccepteerd tot 20 minuten in het verleden en vijf minuten in de toekomst. De tijds tempel moet de ISO 8601-indeling hebben.

### <a name="namespace"></a>Naamruimte
Naam ruimten zijn een manier om Vergelijk bare metrische gegevens te categoriseren of groeperen. Met behulp van naam ruimten kunt u isolatie verkrijgen tussen groepen metrische gegevens die verschillende inzichten of prestatie-indica toren kunnen verzamelen. U kunt bijvoorbeeld een naam ruimte hebben met de naam **contosomemorymetrics** die geheugen gebruik houdt van metrische gegevens die uw app profiel. Een andere naam ruimte met de naam **contosoapptransaction** kan alle metrische gegevens over gebruikers transacties in uw toepassing bijhouden.

### <a name="name"></a>Naam
**Naam** is de naam van de metrische gegevens die worden gerapporteerd. Normaal gesp roken is de naam beschrijvend genoeg om te helpen bij het identificeren van wat wordt gemeten. Een voor beeld hiervan is een metrische waarde die het aantal door het geheugen gebruikte bytes op een bepaalde VM meet. Dit kan een metrische naam zijn, zoals het **geheugen bytes dat in gebruik** is.

### <a name="dimension-keys"></a>Dimensie sleutels
Een dimensie is een sleutel-of waardepaar dat helpt bij het beschrijven van aanvullende kenmerken over de metrische gegevens die worden verzameld. Door de aanvullende kenmerken te gebruiken, kunt u meer informatie verzamelen over de metriek, waardoor u meer inzicht kunt krijgen. Zo kan de metrische **geheugen bytes in gebruik** bijvoorbeeld een dimensie sleutel hebben met de naam **process** waarmee wordt vastgelegd hoeveel bytes van geheugen elk proces op een virtuele machine gebruikt. Met deze sleutel kunt u de metrische gegevens filteren om te zien hoeveel geheugen-specifieke processen gebruikmaken van of om de vijf meest voorkomende processen te identificeren op basis van geheugen gebruik.
Dimensies zijn optioneel, niet alle metrische gegevens kunnen dimensies hebben. Een aangepaste metriek kan Maxi maal 10 dimensies hebben.

### <a name="dimension-values"></a>Dimensie waarden
Bij het rapporteren van een metriek gegevens punt voor elke dimensie sleutel op de metrische waarde die wordt gerapporteerd, is er een overeenkomende dimensie waarde. Het is bijvoorbeeld mogelijk dat u het geheugen wilt rapporteren dat wordt gebruikt door de ContosoApp op uw virtuele machine:

* De naam van de metriek zou **geheugen bytes in gebruik** zijn.
* De dimensie sleutel wordt **verwerkt**.
* De dimensie waarde wordt **ContosoApp.exe**.

Wanneer u een metrische waarde publiceert, kunt u slechts één dimensie waarde per dimensie sleutel opgeven. Als u hetzelfde geheugen gebruik voor meerdere processen op de virtuele machine verzamelt, kunt u meerdere metrische waarden voor die tijds tempel rapporteren. Elke metrische waarde zou een andere dimensie waarde voor de **proces** dimensie sleutel opgeven.
Dimensies zijn optioneel, niet alle metrische gegevens kunnen dimensies hebben. Als voor een metrische post dimensie sleutels worden gedefinieerd, zijn overeenkomstige dimensie waarden verplicht.

### <a name="metric-values"></a>Metrische waarden
Azure Monitor worden alle metrische gegevens opgeslagen met granulatie intervallen van één minuut. We begrijpen dat er tijdens een bepaalde minuut een metriek mogelijk meerdere malen moet worden gesampled. Een voorbeeld is CPU-gebruik. Het is ook mogelijk dat er moet worden gemeten voor veel discrete gebeurtenissen. Een voor beeld is latentie van aanmeldings transacties. Als u het aantal onbewerkte waarden wilt beperken dat u wilt verzenden en betalen voor in Azure Monitor, kunt u de waarden lokaal vooraf combi neren en verzenden:

* **Min**: de minimale waargenomen waarde van alle voor beelden en metingen gedurende de minuut.
* **Max**: de Maxi maal waargenomen waarde van alle voor beelden en metingen gedurende de minuut.
* **Sum**: de som van alle waargenomen waarden van alle voor beelden en metingen gedurende de minuut.
* **Aantal**: het aantal voor beelden en metingen dat is gemaakt tijdens de minuut.

Als er bijvoorbeeld gedurende een minuut vier aanmeldings transacties voor uw app zijn, kunnen de resulterende gemeten latenties voor elk als volgt zijn:

|Trans actie 1|Trans actie 2|Trans actie 3|Trans actie 4|
|---|---|---|---|
|7 MS|4 MS|13 MS|16 MS|

De resulterende metrische publicatie voor Azure Monitor zou er als volgt uitzien:
* Min: 4
* Max.: 16
* Sum: 40
* Aantal: 4

Als uw toepassing niet lokaal kan worden geaggregeerd en elke afzonderlijke steek proef of gebeurtenis direct bij de verzameling moet worden opgehaald, kunt u de waarden voor onbewerkte metingen verzenden. Elke keer dat er een aanmeldings transactie plaatsvindt in uw app, kunt u bijvoorbeeld een metriek publiceren om te Azure Monitor met slechts één meting. Voor een aanmeldings transactie die 12 MS duurde, zou de metrische publicatie er als volgt uitzien:
* Min: 12
* Maximum: 12
* Som: 12
* Aantal: 1

Met dit proces kunt u tijdens een bepaalde minuut meerdere waarden voor dezelfde metrische plus dimensie combinatie verzenden. Azure Monitor worden vervolgens alle onbewerkte waarden voor een bepaalde minuut verzonden en samengevoegd.

### <a name="sample-custom-metric-publication"></a>Voor beeld van een aangepaste metrische publicatie
In het volgende voor beeld maakt u een aangepaste metriek met de naam **geheugen bytes** die worden gebruikt onder het **geheugen profiel** metrische naam ruimte voor een virtuele machine. De metriek heeft één dimensie met de naam **process**. Voor de opgegeven tijds tempel verzenden we metrische waarden voor twee verschillende processen:

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"
        ],
        "series": [
          {
            "dimValues": [
              "ContosoApp.exe"
            ],
            "min": 10,
            "max": 89,
            "sum": 190,
            "count": 4
          },
          {
            "dimValues": [
              "SalesApp.exe"
            ],
            "min": 10,
            "max": 23,
            "sum": 86,
            "count": 4
          }
        ]
      }
    }
  }
```
> [!NOTE]  
> Application Insights, de uitbrei ding van de diagnostische gegevens en de InfluxData-telegrafa-agent zijn al geconfigureerd voor het leveren van metrische waarden voor het juiste regionale eind punt en worden alle voor gaande eigenschappen in elke emissie overgedragen.
>
>

## <a name="custom-metric-definitions"></a>Aangepaste metrische definities
U hoeft geen aangepaste metriek vooraf te definiëren in Azure Monitor voordat deze wordt verzonden. Elk gegevens punt dat wordt gepubliceerd, bevat naam ruimte, naam en dimensie-informatie. De eerste keer dat een aangepaste metriek wordt verzonden naar Azure Monitor, wordt automatisch een metrische definitie gemaakt. Deze metrische definitie kan vervolgens worden gedetecteerd voor elke resource waarbij de metrische gegevens worden verzonden via de metrische definities.

> [!NOTE]  
> Azure Monitor biedt nog geen ondersteuning voor het definiëren van **eenheden** voor een aangepaste metriek.

## <a name="using-custom-metrics"></a>Aangepaste metrische gegevens gebruiken
Nadat aangepaste metrische gegevens zijn verzonden naar Azure Monitor, kunt u ze via de Azure Portal door bladeren en query's uitvoeren via de Azure Monitor REST-Api's. U kunt ook waarschuwingen maken om u te waarschuwen wanneer aan bepaalde voor waarden wordt voldaan.

> [!NOTE]
> U moet een rol lezer of Inzender zijn om aangepaste metrische gegevens weer te geven. Zie [bewakings lezer](../../role-based-access-control/built-in-roles.md#monitoring-reader). 

### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Door uw aangepaste metrische gegevens bladeren via de Azure Portal
1.    Ga naar de [Azure Portal](https://portal.azure.com).
2.    Selecteer het deel venster **monitor** .
3.    Selecteer **Metrische gegevens**.
4.    Selecteer een resource waarvoor u aangepaste metrische gegevens hebt verzonden.
5.    Selecteer de metrische naam ruimte voor uw aangepaste metrische gegevens.
6.    Selecteer de aangepaste metriek.

> [!NOTE]
> Zie [aan de slag met Azure Metrics Explorer](./metrics-getting-started.md) voor meer informatie over het weer geven van metrische gegevens in de Azure Portal.

## <a name="supported-regions"></a>Ondersteunde regio’s
Tijdens de open bare preview-periode is de mogelijkheid om aangepaste metrische gegevens te publiceren alleen beschikbaar in een subset van Azure-regio's. Deze beperking betekent dat metrische gegevens alleen voor resources in een van de ondersteunde regio's kunnen worden gepubliceerd. Zie [Azure-geografi](https://azure.microsoft.com/global-infrastructure/geographies/) voor meer informatie over Azure-regio's. De code van de Azure-regio die wordt gebruikt in de onderstaande eind punten is gewoon de naam van de regio met witruimte verwijderd de volgende tabel bevat de set ondersteunde Azure-regio's voor aangepaste metrische gegevens. Ook worden de bijbehorende eind punten vermeld waarvoor metrische gegevens voor resources in deze regio's moeten worden gepubliceerd:

|Azure-regio |Voor voegsel regionale eind punt|
|---|---|
| Alle open bare Cloud regio's | https://<azure_region_code>. monitoring.azure.com |
| **Azure Government** | |
| VS (overheid) - Arizona | https: \/ /usgovarizona.monitoring.Azure.us |
| **China** | |
| China - oost 2 | https: \/ /chinaeast2.monitoring.Azure.cn |

## <a name="latency-and-storage-retention"></a>Latentie en opslag bewaren

Het toevoegen van een gloed nieuwe waarde of een nieuwe dimensie die wordt toegevoegd aan een metriek kan tot 2 tot drie minuten duren. In het systeem moeten gegevens in minder dan 30 seconden 99% van de tijd worden weer gegeven. 

Als u een metriek verwijdert of een dimensie verwijdert, kan de wijziging een week duren om een maand uit het systeem te verwijderen.

## <a name="quotas-and-limits"></a>Quota en limieten
Azure Monitor de volgende gebruiks limieten opleggen voor aangepaste metrische gegevens:

|Categorie|Limiet|
|---|---|
|Actieve tijd reeks/abonnementen/regio|50,000|
|Dimensie sleutels per metriek|10|
|Teken reeks lengte voor metrische naam ruimten, metrische namen, dimensie sleutels en dimensie waarden|256 tekens|

Een actieve tijd reeks wordt gedefinieerd als een unieke combi natie van metrische gegevens, dimensie sleutels of dimensie waarden waarvan de metrische waarden in de afgelopen 12 uur zijn gepubliceerd.

## <a name="next-steps"></a>Volgende stappen
Aangepaste metrische gegevens van verschillende services gebruiken: 
 - [Virtual Machines](collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Schaalset voor virtuele machines](collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Azure Virtual Machines (klassiek)](collect-custom-metrics-guestos-vm-classic.md)
 - [Virtuele Linux-machine met behulp van de Telegraf-agent](collect-custom-metrics-linux-telegraf.md)
 - [REST API](./metrics-store-custom-rest-api.md)
 - [Klassieke Cloud Services](collect-custom-metrics-guestos-vm-cloud-service-classic.md)
 


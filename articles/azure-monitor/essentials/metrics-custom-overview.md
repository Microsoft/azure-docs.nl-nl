---
title: Aangepaste metrische gegevens in Azure Monitor (preview)
description: Meer informatie over aangepaste metrische gegevens in Azure Monitor en hoe deze worden gemodelleerd.
author: anirudhcavale
ms.author: ancav
services: azure-monitor
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: f4ba3763dd781053349417fe3fed3a2848a06fc7
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515835"
---
# <a name="custom-metrics-in-azure-monitor-preview"></a>Aangepaste metrische gegevens in Azure Monitor (preview)

Wanneer u resources en toepassingen in Azure implementeert, wilt u beginnen met het verzamelen van telemetrie om inzicht te krijgen in hun prestaties en status. Azure maakt een aantal metrische gegevens direct beschikbaar. Deze metrische gegevens worden [standaard of platform genoemd.](./metrics-supported.md) Ze zijn echter beperkt van aard. 

Mogelijk wilt u enkele aangepaste prestatie-indicatoren of bedrijfsspecifieke metrische gegevens verzamelen om meer inzicht te krijgen. Deze **aangepaste metrische** gegevens kunnen worden verzameld via de telemetrie van uw toepassing, een agent die wordt uitgevoerd op uw Azure-resources of zelfs via een bewakingssysteem dat buiten het systeem wordt Azure Monitor. Nadat ze zijn gepubliceerd naar Azure Monitor, kunt u aangepaste metrische gegevens voor uw Azure-resources en -toepassingen naast de standaardmetrieken die door Azure worden uitgegeven, metrische gegevens bladeren, opvragen en waarschuwen.

Azure Monitor aangepaste metrische gegevens zijn actueel in openbare preview. 

## <a name="methods-to-send-custom-metrics"></a>Methoden voor het verzenden van aangepaste metrische gegevens

Aangepaste metrische gegevens kunnen op verschillende Azure Monitor worden verzonden:
- Instrumenteren van uw toepassing met behulp van Azure-toepassing Insights SDK en aangepaste telemetrie verzenden naar Azure Monitor. 
- Installeer de Azure Monitor Agent (Preview) op uw Virtuele Windows- [](../agents/data-collection-rule-azure-monitor-agent.md) of [Linux Azure-VM](../agents/azure-monitor-agent-overview.md) en gebruik een regel voor gegevensverzameling om prestatiemeters te verzenden naar Azure Monitor metrische gegevens.
- Installeer de Windows Azure Diagnostics-extensie (WAD) op uw [Azure-VM,](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md) [virtuele-machineschaalset,](../essentials/collect-custom-metrics-guestos-resource-manager-vmss.md)klassieke [VM](../essentials/collect-custom-metrics-guestos-vm-classic.md)of klassieke [Cloud Services](../essentials/collect-custom-metrics-guestos-vm-cloud-service-classic.md) en verzend prestatiemeters naar Azure Monitor. 
- Installeer de [MetricsData Telegraf-agent](../essentials/collect-custom-metrics-linux-telegraf.md) op uw Azure Linux-VM en verzend metrische gegevens met behulp van Azure Monitor uitvoerin plug-in.
- Verzend aangepaste [metrische gegevens rechtstreeks naar de Azure Monitor REST API](./metrics-store-custom-rest-api.md), `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics` .

## <a name="pricing-model-and-retention"></a>Prijsmodel en retentie

Ga naar [Azure Monitor pagina met](https://azure.microsoft.com/pricing/details/monitor/) prijzen voor meer informatie over wanneer facturering wordt ingeschakeld voor query's voor aangepaste metrische gegevens en metrische gegevens. Specifieke prijsdetails voor alle metrische gegevens, inclusief aangepaste metrische gegevens en metrische query's, zijn beschikbaar op deze pagina. Samengevat zijn er geen kosten verbonden aan het opnemen van standaard metrische gegevens (platformmetrieken) in Azure Monitor Metrics Store, maar voor aangepaste metrische gegevens worden kosten in rekening brengen wanneer ze algemeen beschikbaar worden. Voor metrische API-query's worden kosten in rekening brengen.

Aangepaste metrische gegevens worden bewaard voor [dezelfde tijd als metrische platformgegevens.](../essentials/data-platform-metrics.md#retention-of-metrics) 

> [!NOTE]  
> Metrische gegevens die Azure Monitor via de SDK Application Insights, worden gefactureerd als opgenomen logboekgegevens. Er worden alleen extra kosten voor metrische gegevens in rekening gebracht als Application Insights functie Waarschuwingen inschakelen voor aangepaste [dimensies](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) voor metrische gegevens is geselecteerd. Met dit selectievakje worden gegevens naar de Azure Monitor database met metrische gegevens met behulp van de api voor aangepaste metrische gegevens verstuurd om complexere waarschuwingen toe te staan.  Meer informatie over het [Application Insights en](../app/pricing.md#pricing-model) prijzen in [uw regio.](https://azure.microsoft.com/pricing/details/monitor/)


## <a name="how-to-send-custom-metrics"></a>Aangepaste metrische gegevens verzenden

Wanneer u aangepaste metrische gegevens naar Azure Monitor, moet elk gegevenspunt of elke gerapporteerde waarde de volgende informatie bevatten.

### <a name="authentication"></a>Verificatie
Als u aangepaste metrische gegevens wilt verzenden naar Azure Monitor, heeft de entiteit die de metrische gegevens indient, een geldig Azure Active Directory-token (Azure AD) nodig in de **Bearer-header** van de aanvraag. Er zijn een aantal ondersteunde manieren om een geldig Bearer-token te verkrijgen:
1. [Beheerde identiteiten voor Azure-resources.](../../active-directory/managed-identities-azure-resources/overview.md) Geeft een identiteit aan een Azure-resource zelf, zoals een VM. Managed Service Identity (MSI) is ontworpen om resources machtigingen te geven voor het uitvoeren van bepaalde bewerkingen. Een voorbeeld hiervan is het toestaan van een resource om metrische gegevens over zichzelf te zenden. Aan een resource, of de MSI ervan, kunnen machtigingen van **Monitoring Metrics Publisher** worden verleend voor een andere resource. Met deze machtiging kan de MSI ook metrische gegevens voor andere resources zenden.
2. [Azure AD-service-principal](../../active-directory/develop/app-objects-and-service-principals.md). In dit scenario kan aan een Azure AD-toepassing of -service machtigingen worden toegewezen voor het implementeren van metrische gegevens over een Azure-resource.
Als u de aanvraag wilt verifiëren, Azure Monitor het token van de toepassing valideren met behulp van openbare Azure AD-sleutels. De bestaande **rol Uitgever van bewakingsgegevens** heeft deze machtiging al. Deze is beschikbaar in de Azure Portal. De service-principal kan, afhankelijk van de resources waarvoor aangepaste metrische gegevens worden gebruikt, de rol **Uitgever** van metrische bewakingsgegevens krijgen voor het vereiste bereik. Voorbeelden zijn een abonnement, resourcegroep of specifieke resource.

> [!TIP]  
> Wanneer u een Azure AD-token aanvraagt om aangepaste metrische gegevens te implementeren, moet u ervoor zorgen dat de doelgroep of resource waar het token voor wordt aangevraagd, `https://monitoring.azure.com/` is. Zorg ervoor dat u het volgende '/' op te nemen.

### <a name="subject"></a>Onderwerp
Deze eigenschap legt vast voor welke Azure-resource-id de aangepaste metrische gegevens worden gerapporteerd. Deze informatie wordt gecodeerd in de URL van de API-aanroep die wordt gemaakt. Elke API kan alleen metrische waarden verzenden voor één Azure-resource.

> [!NOTE]  
> U kunt geen aangepaste metrische gegevens naar de resource-id van een resourcegroep of abonnement zenden.


### <a name="region"></a>Region
Deze eigenschap legt vast in welke Azure-regio de resource waarvoor u metrische gegevens wilt gebruiken, wordt geïmplementeerd. Metrische gegevens moeten worden weggelaten naar Azure Monitor regionale eindpunt als de regio waarin de resource is geïmplementeerd. Aangepaste metrische gegevens voor een VM die is geïmplementeerd in VS - west moeten bijvoorbeeld worden verzonden naar het regionale Azure Monitor VS - west. De regiogegevens worden ook gecodeerd in de URL van de API-aanroep.

> [!NOTE]  
> Tijdens de openbare preview zijn aangepaste metrische gegevens alleen beschikbaar in een subset van Azure-regio's. Een lijst met ondersteunde regio's wordt beschreven in een latere sectie van dit artikel.
>
>

### <a name="timestamp"></a>Tijdstempel
Elk gegevenspunt dat wordt verzonden Azure Monitor moet worden gemarkeerd met een tijdstempel. Deze tijdstempel legt de Datum/tijd vast waarop de metrische waarde wordt gemeten of verzameld. Azure Monitor accepteert metrische gegevens met tijdstempels tot wel 20 minuten in het verleden en vijf minuten in de toekomst. De tijdstempel moet de ISO 8601-indeling hebben.

### <a name="namespace"></a>Naamruimte
Naamruimten zijn een manier om vergelijkbare metrische gegevens te categoriseren of te groeperen. Door naamruimten te gebruiken, kunt u isolatie bereiken tussen groepen metrische gegevens die verschillende inzichten of prestatie-indicatoren kunnen verzamelen. U kunt bijvoorbeeld een naamruimte hebben met de naam **contosomemorymetrics** waarmee metrische gegevens voor geheugengebruik worden bijgeslagen die uw app profileren. Een andere naamruimte **met de naam contosoapptransaction** kan alle metrische gegevens over gebruikerstransacties in uw toepassing bijhouden.

### <a name="name"></a>Name
**Naam** is de naam van de metrische gegevens die worden gerapporteerd. Normaal gesproken is de naam beschrijvend genoeg om te helpen bepalen wat er wordt gemeten. Een voorbeeld is een metrische gegevens waarmee het aantal geheugen bytes wordt meet dat op een bepaalde VM wordt gebruikt. Deze kan een metrische naam hebben, zoals **Geheugen bytes in gebruik.**

### <a name="dimension-keys"></a>Dimensiesleutels
Een dimensie is een sleutel- of waardepaar waarmee u aanvullende kenmerken kunt beschrijven over de metrische gegevens die worden verzameld. Met behulp van de aanvullende kenmerken kunt u meer informatie verzamelen over de metrische gegevens, waardoor u meer inzicht kunt krijgen. De metrische gegevens **Geheugen bytes in** gebruik kunnen bijvoorbeeld een dimensiesleutel hebben met de naam **Proces** die vast leggen hoeveel bytes geheugen elk proces op een virtueleM verbruikt. Met behulp van deze sleutel kunt u de metrische gegevens filteren om te zien hoeveel geheugenspecifieke processen gebruiken of om de vijf meestgebruikte processen te identificeren op basis van geheugengebruik.
Dimensies zijn optioneel. Mogelijk hebben niet alle metrische gegevens dimensies. Een aangepast metrisch gegeven kan maximaal 10 dimensies hebben.

### <a name="dimension-values"></a>Dimensiewaarden
Wanneer u een metrische gegevenspunt rapporteert, is er voor elke dimensiesleutel voor de metrische gegevens die worden gerapporteerd, een bijbehorende dimensiewaarde. U kunt bijvoorbeeld het geheugen rapporteren dat wordt gebruikt door de ContosoApp op uw VM:

* De metrische naam is **Geheugen bytes in Gebruik.**
* De dimensiesleutel zou **Proces zijn.**
* De dimensiewaarde wordt **ContosoApp.exe**.

Wanneer u een metrische waarde publiceert, kunt u slechts één dimensiewaarde per dimensiesleutel opgeven. Als u hetzelfde geheugengebruik voor meerdere processen op de VM verzamelt, kunt u meerdere metrische waarden voor die tijdstempel rapporteren. Elke metrische waarde geeft een andere dimensiewaarde op voor de **dimensiesleutel** Proces.
Dimensies zijn optioneel, niet alle metrische gegevens kunnen dimensies hebben. Als een metrische post dimensiesleutels definieert, zijn bijbehorende dimensiewaarden verplicht.

### <a name="metric-values"></a>Metrische waarden
Azure Monitor slaat alle metrische gegevens op met een granulariteitsinterval van één minuut. We begrijpen dat gedurende een bepaalde minuut een metrische gegevens mogelijk meerdere keren moeten worden bemonsterd. Een voorbeeld is CPU-gebruik. Of het moet mogelijk worden gemeten voor veel discrete gebeurtenissen. Een voorbeeld hiervan zijn latentie van aanmeldingstransacties. Als u het aantal onbewerkte waarden wilt beperken dat u in een Azure Monitor wilt Azure Monitor, kunt u de waarden lokaal vooraf aggregeren en de volgende waarden naar de volgende gegevens zenden:

* **Min:** de minimaal waargenomen waarde van alle steekproeven en metingen gedurende de minuut.
* **Maximum:** de maximale waargenomen waarde van alle steekproeven en metingen gedurende de minuut.
* **Som:** de som van alle waargenomen waarden uit alle steekproeven en metingen gedurende de minuut.
* **Aantal:** het aantal steekproeven en metingen dat gedurende de minuut is genomen.

Als er bijvoorbeeld gedurende een bepaalde minuut vier aanmeldingstransacties voor uw app zijn geweest, kunnen de resulterende gemeten latentie voor elke transactie er als volgt uit zien:

|Transactie 1|Transactie 2|Transactie 3|Transactie 4|
|---|---|---|---|
|7 ms|4 ms|13 ms|16 ms|

Vervolgens zou de resulterende publicatie van metrische Azure Monitor als volgt zijn:
* Min: 4
* Maximaal: 16
* Som: 40
* Aantal: 4

Als uw toepassing niet vooraf kan worden geaggregeerd en elke afzonderlijke steekproef of gebeurtenis onmiddellijk na het verzamelen moet worden verzonden, kunt u de onbewerkte metingswaarden naar de toepassing zenden. Telkens wanneer er bijvoorbeeld een aanmeldingstransactie plaatsvindt in uw app, publiceert u een metrisch Azure Monitor met slechts één meting. Voor een aanmeldingstransactie die 12 ms duurde, zou de publicatie van het metrische gegevens dus als volgt zijn:
* Min: 12
* Maximaal: 12
* Som: 12
* Aantal: 1

Met dit proces kunt u meerdere waarden voor dezelfde combinatie van metrische gegevens en dimensies gedurende een bepaalde minuut maken. Azure Monitor neemt vervolgens alle onbewerkte waarden die voor een bepaalde minuut worden uitgezonden, samen en aggregeert ze samen.

### <a name="sample-custom-metric-publication"></a>Voorbeeld van publicatie van aangepaste metrische gegevens
In het volgende voorbeeld maakt u een aangepast metrisch gegeven met de naam **Geheugen bytes in Gebruik** onder de metrische naamruimte **Geheugenprofiel** voor een virtuele machine. De metrische gegevens hebben één dimensie met de naam **Proces**. Voor de opgegeven tijdstempel worden metrische waarden voor twee verschillende processen gebruikt:

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
> Application Insights zijn de extensie voor diagnostische gegevens en de Telegraf-agent InfluxData al geconfigureerd om metrische waarden te zenden op basis van het juiste regionale eindpunt en alle voorgaande eigenschappen in elk van deze eigenschappen te dragen.
>
>

## <a name="custom-metric-definitions"></a>Aangepaste metrische definities
U hoeft geen aangepaste metrische gegevens vooraf te Azure Monitor voordat deze worden weggelaten. Elk gepubliceerd metrische gegevenspunt bevat informatie over naamruimte, naam en dimensie. De eerste keer dat er een aangepaste metrische gegevens worden Azure Monitor, wordt er dus automatisch een metrische definitie gemaakt. Deze definitie van metrische gegevens kan vervolgens worden vastgesteld voor elke resource waar de metrische gegevens voor worden ingediend via de metrische definities.

> [!NOTE]  
> Azure Monitor biedt nog geen ondersteuning voor het definiëren **van eenheden** voor aangepaste metrische gegevens.

## <a name="using-custom-metrics"></a>Aangepaste metrische gegevens gebruiken
Nadat aangepaste metrische gegevens zijn verzonden naar Azure Monitor, kunt u deze bladeren via de Azure Portal en query's uitvoeren via Azure Monitor REST API's. U kunt er ook waarschuwingen voor maken om u te waarschuwen wanneer aan bepaalde voorwaarden wordt voldaan.

> [!NOTE]
> U moet de rol lezer of inzender hebben om aangepaste metrische gegevens weer te geven. Zie [Lezer voor bewaking.](../../role-based-access-control/built-in-roles.md#monitoring-reader) 

### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Blader door uw aangepaste metrische gegevens via Azure Portal
1.    Ga naar de [Azure Portal](https://portal.azure.com).
2.    Selecteer het **deelvenster** Monitor.
3.    Selecteer **Metrische gegevens**.
4.    Selecteer een resource voor wie u aangepaste metrische gegevens hebt ingediend.
5.    Selecteer de naamruimte voor metrische gegevens voor uw aangepaste metrische gegevens.
6.    Selecteer de aangepaste metrische gegevens.

> [!NOTE]
> Zie [Aan de slag met Azure Metrics Explorer](./metrics-getting-started.md) voor meer informatie over het weergeven van metrische gegevens in Azure Portal.

## <a name="supported-regions"></a>Ondersteunde regio’s
Tijdens de openbare preview is de mogelijkheid om aangepaste metrische gegevens te publiceren alleen beschikbaar in een subset van Azure-regio's. Deze beperking betekent dat metrische gegevens alleen kunnen worden gepubliceerd voor resources in een van de ondersteunde regio's. Zie [Azure-geografieën](https://azure.microsoft.com/global-infrastructure/geographies/) voor meer informatie over Azure-regio's. De Azure-regiocode die in de onderstaande eindpunten wordt gebruikt, is alleen de naam van de regio waarin de witruimte is verwijderd De volgende tabel bevat de set ondersteunde Azure-regio's voor aangepaste metrische gegevens. Ook worden de bijbehorende eindpunten vermeld waar metrische gegevens voor resources in deze regio's naar moeten worden gepubliceerd:

|Azure-regio |Regionaal eindpunt voorvoegsel|
|---|---|
| Alle openbare cloudregio's | https://<azure_region_code>.monitoring.azure.com |
| **Azure Government** | |
| VS (overheid) - Arizona | https: \/ /usgovarizona.monitoring.azure.us |
| **China** | |
| China - oost 2 | https: \/ /chinaeast2.monitoring.azure.cn |

## <a name="latency-and-storage-retention"></a>Latentie en opslagretentie

Het toevoegen van een geheel nieuwe metrische waarde of een nieuwe dimensie die wordt toegevoegd aan een metrische waarde kan 2 tot 3 minuten duren. Eenmaal in het systeem moeten gegevens in minder dan 30 seconden 99% van de tijd worden weergegeven. 

Als u een metrische gegevens verwijdert of een dimensie verwijdert, kan het een week tot een maand duren om de wijziging uit het systeem te verwijderen.

## <a name="quotas-and-limits"></a>Quota en limieten
Azure Monitor worden de volgende gebruikslimieten opgelegd voor aangepaste metrische gegevens:

|Categorie|Limiet|
|---|---|
|Actieve tijdreeksen/abonnementen/regio|50,000|
|Dimensiesleutels per metrische gegevens|10|
|Tekenreekslengte voor metrische naamruimten, metrische namen, dimensiesleutels en dimensiewaarden|256 tekens|

Een actieve tijdreeks wordt gedefinieerd als een unieke combinatie van metrische gegevens, dimensiesleutels of dimensiewaarden die in de afgelopen 12 uur metrische waarden hebben gepubliceerd.

Als u meer wilt weten over de limiet van 50.000 tijdreeksen, moet u rekening houden met de volgende metrische gegevens:

*Reactietijd van server* met dimensies: *Regio,* *Afdeling,* *CustomerID*

Als u met deze metrische gegevens 10 regio's, 20 afdelingen en 100 klanten hebt die u 10 x 20 x 100 = 2000 tijdreeksen bieden. 

Als u 100 regio's, 200 afdelingen en 2000 klanten hebt, 100 x 200 x 2000 = 40.000.000 tijdreeksen, die alleen al voor deze metrische gegevens ver boven de limiet ligt. 

Nogmaals, deze limiet geldt niet voor afzonderlijke metrische gegevens. Dit is voor de som van al deze metrische gegevens in een abonnement en regio.  

## <a name="design-limitations"></a>Ontwerpbeperkingen

**Gebruik deze Application Insights voor** controledoeleinden: de pijplijn Application Insights maakt achter de schermen gebruik van de API voor aangepaste metrische gegevens. De pijplijn is geoptimaliseerd voor een groot aantal telemetrie met minimale gevolgen voor uw toepassing. Als zodanig beperkt de gegevensstroom of voorbeelden (neemt slechts een percentage van uw telemetriegegevens en negeert de rest) als uw binnenkomende gegevensstroom te groot wordt. Vanwege dit gedrag kunt u het niet gebruiken voor controledoeleinden, omdat sommige records waarschijnlijk worden uitgevallen. 

**Metrische gegevens met een variabele in** de naam: gebruik geen variabele als onderdeel van de metrische naam, bijvoorbeeld een GUID of een tijdstempel. Hierdoor raakt u snel de limiet van 50.000 tijdreeksen. 
 
**Dimensies voor metrische gegevens** met hoge kardinaliteit: metrische gegevens met te veel geldige waarden in een dimensie (een 'hoge kardinaliteit') hebben een veel grotere kans om de limiet van 50.000 te overschrijden. Over het algemeen moet u nooit een voortdurend veranderende waarde gebruiken in een dimensie- of metrische naam. Tijdstempel moet bijvoorbeeld NOOIT een dimensie zijn. Server, klant of productid kan worden gebruikt, maar alleen als u een kleiner aantal van deze typen hebt. Stel uzelf als test de vraag of u dergelijke gegevens in een grafiek in elke grafiek zou kunnen zien.  Als u 10 of misschien zelfs 100 servers hebt, kan het handig zijn om ze allemaal in een grafiek te zien ter vergelijking. Maar als u 1000 hebt, is de resulterende grafiek waarschijnlijk moeilijk of niet onmogelijk te lezen. De best practice is om deze op minder dan 100 geldige waarden te houden. Maximaal 300 is een grijs gebied.  Als u dit aantal wilt doorgenomen, gebruikt u in plaats daarvan Azure Monitor logboeken.   

Als u een variabele in de naam of een dimensie met hoge kardinaliteit hebt, kan het volgende optreden. 
- Metrische gegevens worden onbetrouwbaar vanwege bandbreedtebeperking
- Metrics Explorer werkt niet
- Waarschuwingen en meldingen worden onvoorspelbaar
- De kosten kunnen onverwacht toenemen: Microsoft laadt geen kosten terwijl de aangepaste metrische gegevens met dimensies in openbare preview zijn. Zodra de kosten echter in de toekomst beginnen, worden er onverwachte kosten in rekening gebracht. Het plan is om kosten in rekening te brengen voor het verbruik van metrische gegevens op basis van het aantal bewaakte tijdreeksen en het aantal API-aanroepen.  

## <a name="next-steps"></a>Volgende stappen
Aangepaste metrische gegevens van verschillende services gebruiken: 
 - [Virtual Machines](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Schaalset voor virtuele machines](../essentials/collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Azure Virtual Machines (klassiek)](../essentials/collect-custom-metrics-guestos-vm-classic.md)
 - [Virtuele Linux-machine met behulp van de Telegraf-agent](../essentials/collect-custom-metrics-linux-telegraf.md)
 - [REST API](./metrics-store-custom-rest-api.md)
 - [Klassieke Cloud Services](../essentials/collect-custom-metrics-guestos-vm-cloud-service-classic.md)

---
title: Inleiding tot stroomlogregistratie voor NSG's
titleSuffix: Azure Network Watcher
description: In dit artikel wordt uitgelegd hoe u de functie NSG-stroomlogboeken van Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.openlocfilehash: f737be68a28f95ab5402ba5ea08e85fcf1b04d37
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565896"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Introductie van stroomlogboeken voor netwerkbeveiligingsgroepen

## <a name="introduction"></a>Introductie

[Stroomlogboeken](../virtual-network/network-security-groups-overview.md#security-rules) van netwerkbeveiligingsgroep (NSG) is een functie van Azure Network Watcher waarmee u informatie kunt bijhouden over IP-verkeer dat via een NSG stroomt. Stroomgegevens worden verzonden naar Azure Storage accounts van waaruit u ze kunt openen en exporteren naar elk visualisatiehulpprogramma, SIEM of id's van uw keuze.

![overzicht van stroomlogboeken](./media/network-watcher-nsg-flow-logging-overview/homepage.jpg)

## <a name="why-use-flow-logs"></a>Waarom stroomlogboeken gebruiken?

Het is essentieel om uw eigen netwerk te bewaken, te beheren en te kennen voor niet-gecompromitteerde beveiliging, naleving en prestaties. Kennis van uw eigen omgeving is van cruciaal belang om deze te beveiligen en te optimaliseren. Vaak moet u weten wat de huidige status van het netwerk is, wie er verbinding maakt, waar ze verbinding maken, welke poorten zijn geopend voor internet, verwacht netwerkgedrag, onregelmatig netwerkgedrag en plotselinge toename van verkeer.

Stroomlogboeken zijn de bron van waarheid voor alle netwerkactiviteit in uw cloudomgeving. Of u nu een beginnende start-up bent die resources probeert te optimaliseren of grote ondernemingen die inbraak proberen te detecteren, Flow-logboeken zijn uw beste keuze. U kunt deze gebruiken voor het optimaliseren van netwerkstromen, het bewaken van doorvoer, het controleren van naleving, het detecteren van indringers en meer.

## <a name="common-use-cases"></a>Algemene scenario’s

**Netwerkbewaking:** identificeer onbekend of ongewenst verkeer. Beveer het verkeersniveaus en bandbreedteverbruik. Filter stroomlogboeken op IP en poort om het gedrag van toepassingen te begrijpen. Exporteert stroomlogboeken naar analyse- en visualisatiehulpprogramma's van uw keuze om bewakingsdashboards in te stellen.

**Bewaking en optimalisatie van gebruik:** Identificeer de belangrijkste gespreksmakers in uw netwerk. Combineren met GeoIP-gegevens om verkeer tussen regio's te identificeren. Meer inzicht in de groei van het verkeer voor capaciteitsprognoses. Gebruik gegevens om al te beperkende verkeersregels te verwijderen.

**Naleving:** stroomgegevens gebruiken om netwerkisolatie en naleving van toegangsregels voor ondernemingen te controleren

**Forensische netwerkanalyse & beveiligingsanalyse:** analyseer netwerkstromen van aangetaste IP's en netwerkinterfaces. Stroomlogboeken exporteren naar een SIEM- of IDS-hulpprogramma van uw keuze.

## <a name="how-logging-works"></a>Hoe logboekregistratie werkt

**Sleuteleigenschappen**

- Stroomlogboeken werken op [laag 4 en](https://en.wikipedia.org/wiki/OSI_model#Layer_4:_Transport_Layer) registreren alle IP-stromen die in en uit een NSG gaan
- Logboeken worden verzameld met een interval van 1 minuut via het **Azure-platform** en hebben op geen enkele manier invloed op de resources of netwerkprestaties van klanten.
- Logboeken worden geschreven in de JSON-indeling en tonen uitgaande en binnenkomende stromen per NSG-regel.
- Elke logboekrecord bevat de netwerkinterface (NIC) waar de stroom op van toepassing is, informatie met 5 tuples, de verkeersbeslissing & (alleen versie 2) doorvoergegevens. Zie _Logboekindeling_ hieronder voor meer informatie.
- Stroomlogboeken hebben een retentiefunctie waarmee de logboeken automatisch kunnen worden verwijderd tot een jaar na het maken ervan. 

> [!NOTE]
> Retentie is alleen beschikbaar als u [Algemeen gebruik v2 Storage-accounts (GPv2) gebruikt.](../storage/common/storage-account-overview.md#types-of-storage-accounts) 

**Basisconcepten**

- Software-gedefinieerde netwerken zijn georganiseerd rond virtuele netwerken (VNET's) en subnetten. De beveiliging van deze VNets en subnetten kan worden beheerd met behulp van een NSG.
- Een netwerkbeveiligingsgroep (NSG) bevat een lijst met beveiligingsregels die netwerkverkeer toestaan _of_ weigeren in resources waarmee deze is verbonden. NSG's kunnen worden gekoppeld aan subnetten, afzonderlijke VM's of afzonderlijke netwerkinterfaces (NIC's) die zijn gekoppeld aan VM's (Resource Manager). Zie Overzicht van netwerkbeveiligingsgroep [voor meer informatie.](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)
- Alle verkeersstromen in uw netwerk worden geëvalueerd met behulp van de regels in de toepasselijke NSG.
- Het resultaat van deze evaluaties zijn NSG-stroomlogboeken. Stroomlogboeken worden verzameld via het Azure-platform en hoeven niet te worden gewijzigd in de resources van de klant.
- Opmerking: regels zijn van twee typen: het & beëindigen, elk met verschillende gedragingen voor logboekregistratie.
- - NSG-regels voor weigeren worden beëindigen. De NSG die het verkeer weigert, registreert het in Flow-logboeken en de verwerking in dit geval wordt gestopt nadat een NSG verkeer weigert. 
- - Regels voor het toestaan van NSG's zijn niet-af te ronden, wat betekent dat zelfs als één NSG dit toestaat, de verwerking wordt voortgezet naar de volgende NSG. De laatste NSG die verkeer toestaat, registreert het verkeer in Flow-logboeken.
- NSG-stroomlogboeken worden geschreven naar opslagaccounts van waar ze toegankelijk zijn.
- U kunt stroomlogboeken exporteren, verwerken, analyseren en visualiseren met hulpprogramma's zoals TA, Splunk, Grafana, Stealthwatch, enzovoort.

## <a name="log-format"></a>Logboekindeling

Stroomlogboeken bevatten de volgende eigenschappen:

* **time:** het tijdstip waarop de gebeurtenis is geregistreerd
* **systemId** : systeem-id van netwerkbeveiligingsgroep.
* **category:** de categorie van de gebeurtenis. De categorie is altijd **NetworkSecurityGroupFlowEvent**
* **resourceid:** de resource-id van de NSG
* **operationName** - Altijd NetworkSecurityGroupFlowEvents
* **eigenschappen:** een verzameling eigenschappen van de stroom
    * **Versie:** versienummer van het gebeurtenisschema van het stroomlogboek
    * **flows:** een verzameling stromen. Deze eigenschap heeft meerdere vermeldingen voor verschillende regels
        * **regel:** regel waarvoor de stromen worden vermeld
            * **flows:** een verzameling stromen
                * **mac:** het MAC-adres van de NIC voor de VM waarop de stroom is verzameld
                * **flowTuples:** een tekenreeks die meerdere eigenschappen bevat voor de stroom-tuple in door komma's gescheiden indeling
                    * **Tijdstempel:** deze waarde is het tijdstempel van wanneer de stroom zich in UNIX-epoche-indeling heeft voorgedaan
                    * **Bron-IP:** het bron-IP-adres
                    * **Doel-IP-** het doel-IP-adres
                    * **Bronpoort:** de bronpoort
                    * **Doelpoort:** de doelpoort
                    * **Protocol:** het protocol van de stroom. Geldige waarden zijn **T** voor TCP en **U** voor UDP
                    * **Verkeersstroom:** de richting van de verkeersstroom. Geldige waarden zijn **I** voor inkomende en **O** voor uitgaand verkeer.
                    * **Verkeersbeslissing:** of verkeer is toegestaan of geweigerd. Geldige waarden zijn **A** voor toegestaan en **D** voor geweigerd.
                    * **Stroomtoestand: alleen versie 2:** legt de status van de stroom vast. Mogelijke statussen zijn **B**: Begin, wanneer een stroom wordt gemaakt. Er worden geen statistische gegevens geleverd. **C**: Continu, voor een actieve stroom. Statistische gegevens worden geleverd met intervallen van 5 minuten. **E**: Eind, wanneer een stroom is beëindigd. Er worden statistische gegevens geleverd.
                    * **Pakketten - bron naar doel - alleen versie 2** Het totale aantal TCP-pakketten dat sinds de laatste update van de bron naar de bestemming is verzonden.
                    * **Verzonden bytes - bron naar doel - alleen versie 2** Het totale aantal TCP-pakket bytes dat sinds de laatste update is verzonden van de bron naar het doel. Pakketbytes omvatten de pakket-header en -nettolading.
                    * **Pakketten - Doel naar bron - alleen versie 2** Het totale aantal TCP-pakketten dat sinds de laatste update van het doel naar de bron is verzonden.
                    * **Verzonden bytes - doel naar bron - alleen versie 2** Het totale aantal TCP-pakket bytes dat sinds de laatste update van doel naar bron is verzonden. Pakketbytes omvatten een pakket-header en -nettolading.


**NSG-stroomlogboeken versie 2 (versus versie 1)** 

Versie 2 van de logboeken introduceert het concept stroomtoestand. U kunt configureren welke versie van stroomlogboeken u ontvangt.

Stroomtoestand _B_ wordt vastgelegd wanneer een stroom wordt gestart. Stroomtoestand _C_ en stroomtoestand _E_ zijn statussen die respectievelijk het vervolg van een stroom en stroombeëindiging markeren. De _C-_ en _E-staten_ bevatten informatie over de bandbreedte van het verkeer.

### <a name="sample-log-records"></a>Voorbeeldlogboekrecords

De volgende tekst is een voorbeeld van een stroomlogboek. Zoals u ziet, zijn er meerdere records die de eigenschappenlijst volgen die in de vorige sectie is beschreven.

> [!NOTE]
> Waarden in de *eigenschap flowTuples* zijn een door komma's gescheiden lijst.
 
**Voorbeeld van NSG-stroomlogboekindeling versie 1**
```json
{
    "records": [
        {
            "time": "2017-02-16T22:00:32.8950000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A",
                                    "1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A",
                                    "1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A",
                                    "1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2017-02-16T22:01:32.8960000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A",
                                    "1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A",
                                    "1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
    "records":
    [
        
        {
             "time": "2017-02-16T22:00:32.8950000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A","1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A","1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A","1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:01:32.8960000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A","1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A","1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:02:32.9040000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282492,175.182.69.29,10.1.0.4,28918,5358,T,I,D","1487282505,71.6.216.55,10.1.0.4,8080,8080,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282512,91.224.160.154,10.1.0.4,59046,3389,T,I,A"]}]}]}
        }
        
        
```
**Voorbeeld van NSG-stroomlogboekindeling versie 2**
```json
 {
    "records": [
        {
            "time": "2018-11-13T12:00:35.3899262Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                                    "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                                    "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "DefaultRule_AllowInternetOutBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                                    "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                                    "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                                    "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2018-11-13T12:01:35.3918317Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110437,125.64.94.197,10.5.16.4,59752,18264,T,I,D,B,,,,",
                                    "1542110475,80.211.72.221,10.5.16.4,37433,8088,T,I,D,B,,,,",
                                    "1542110487,46.101.199.124,10.5.16.4,60577,8088,T,I,D,B,,,,",
                                    "1542110490,176.119.4.30,10.5.16.4,57067,52801,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    }
                ]
            }
        }
        
```
**Uitleg van Log Tuple**

![tuple stroomlogboeken](./media/network-watcher-nsg-flow-logging-overview/tuple.png)

**Berekening van steekproefbandbreedte**

Flow tuples from a TCP conversation between 185.170.185.105:35370 and 10.2.0.4:23:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.18 5.105.10.2.0.4.35370,23,T,I,A,E,52.29952,47.27072"

Voor de _vervolg-C-_ en _eind-E-stroom_ worden het aantal byten en pakketten geaggregeerd vanaf het tijdstip van de vorige stroom-tuplerecord. Verwijzend naar het vorige voorbeeldgesprek is het totale aantal overgedragen pakketten 1021+52+8005+47 = 9125. Het totale aantal overgedragen bytes is 588096+29952+4610880+27072 = 5256000.


## <a name="enabling-nsg-flow-logs"></a>NSG-stroomlogboeken inschakelen

Gebruik de relevante koppeling hieronder voor hulp bij het inschakelen van stroomlogboeken.

- [Azure-portal](./network-watcher-nsg-flow-logging-portal.md)
- [PowerShell](./network-watcher-nsg-flow-logging-powershell.md)
- [CLI](./network-watcher-nsg-flow-logging-cli.md)
- [REST](./network-watcher-nsg-flow-logging-rest.md)
- [Azure Resource Manager](./network-watcher-nsg-flow-logging-azure-resource-manager.md)

## <a name="updating-parameters"></a>Parameters bijwerken

**Azure-portal**

Navigeer Azure Portal de sectie NSG-stroomlogboeken in Network Watcher. Klik vervolgens op de naam van de NSG. Hiermee wordt het deelvenster Instellingen voor het Stroomlogboek weergegeven. Wijzig de parameters die u wilt en druk **op Opslaan om** de wijzigingen te implementeren.

**PS/CLI/REST/ARM**

Als u parameters wilt bijwerken via opdrachtregelprogramma's, gebruikt u dezelfde opdracht als voor het inschakelen van stroomlogboeken (hierboven), maar met bijgewerkte parameters die u wilt wijzigen.

## <a name="working-with-flow-logs"></a>Werken met Stroomlogboeken

*Stroomlogboeken lezen en exporteren*

- [&amp;Stroomlogboeken downloaden vanuit de portal](./network-watcher-nsg-flow-logging-portal.md#download-flow-log)
- [Stroomlogboeken lezen met Behulp van PowerShell-functies](./network-watcher-read-nsg-flow-logs.md)
- [NSG-stroomlogboeken exporteren naar Splunk](https://www.splunk.com/en_us/blog/tips-and-tricks/splunking-microsoft-azure-network-watcher-data.html)

Stroomlogboeken zijn gericht op NSG's, maar ze worden niet hetzelfde weergegeven als de andere logboeken. Stroomlogboeken worden alleen opgeslagen in een opslagaccount en volgen het logboekregistratiepad dat in het volgende voorbeeld wordt weergegeven:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

*Stroomlogboeken visualiseren*

- [Azure Traffic Analytics](./traffic-analytics.md) is een native Azure-service voor het verwerken van stroomlogboeken, het extraheren van inzichten en het visualiseren van stroomlogboeken. 
- [[Zelfstudie] NSG-stroomlogboeken visualiseren met Power BI](./network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [[Zelfstudie] NSG-stroomlogboeken visualiseren met Elastic Stack](./network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [[Zelfstudie] NSG-stroomlogboeken beheren en analyseren met Grafana](./network-watcher-nsg-grafana.md)
- [[Zelfstudie] NSG-stroomlogboeken beheren en analyseren met Graylog](./network-watcher-analyze-nsg-flow-logs-graylog.md)

*Stroomlogboeken uitschakelen*

Wanneer het stroomlogboek is uitgeschakeld, wordt de stroomlogboekregistratie voor de gekoppelde NSG gestopt. Maar het stroomlogboek als resource blijft bestaan met alle instellingen en verbanden. Het kan op elk gewenst moment worden ingeschakeld om stroomlogregistratie te starten op de geconfigureerde NSG. Stappen voor het uitschakelen/inschakelen van stroomlogboeken vindt u in [deze handleiding.](./network-watcher-nsg-flow-logging-powershell.md)  

*Stroomlogboeken verwijderen*

Wanneer het stroomlogboek wordt verwijderd, wordt niet alleen de stroomlogboekregistratie voor de gekoppelde NSG gestopt, maar wordt ook de resource voor het stroomlogboek verwijderd met de instellingen en associaties. Als u de stroomlogboekregistratie opnieuw wilt starten, moet er een nieuwe stroomlogboekresource worden gemaakt voor die NSG. Een stroomlogboek kan worden verwijderd met [behulp van PowerShell,](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkwatcherflowlog) [CLI](https://docs.microsoft.com/cli/azure/network/watcher/flow-log#az_network_watcher_flow_log_delete) [of REST API.](https://docs.microsoft.com/rest/api/network-watcher/flowlogs/delete) De ondersteuning voor het verwijderen van stroomlogboeken van Azure Portal is in de pijplijn.    

Wanneer een NSG wordt verwijderd, wordt standaard ook de bijbehorende stroomlogboekresource verwijderd.

> [!NOTE]
> Als u een NSG wilt verplaatsen naar een andere resourcegroep of een ander abonnement, moeten de bijbehorende stroomlogboeken worden verwijderd. Het uitschakelen van de stroomlogboeken werkt alleen niet. Na de migratie van NSG moeten de stroomlogboeken opnieuw worden gemaakt om stroomlogboeken in te kunnen stellen.  

## <a name="nsg-flow-logging-considerations"></a>Overwegingen voor NSG-stroomlogregistratie

**Overwegingen voor opslagaccounts:** 

- Locatie: het gebruikte opslagaccount moet zich in dezelfde regio bevinden als de NSG.
- Prestatielaag: momenteel worden alleen opslagaccounts van de Standard-laag ondersteund.
- Sleutelrotatie zelf beheren: als u de toegangssleutels voor uw opslagaccount wijzigt/roteert, werken NSG-stroomlogboeken niet meer. U kunt dit probleem oplossen door NSG-stroomlogboeken uit te schakelen en opnieuw in te schakelen.

**Kosten voor stroomlogboeken:** NSG-stroomlogboeken worden gefactureerd voor het aantal geproduceerde logboeken. Een groot verkeersvolume kan leiden tot een groot stroomlogboekvolume en de bijbehorende kosten. Prijzen van NSG Flow-logboeken omvatten niet de onderliggende opslagkosten. Het gebruik van de functie voor bewaarbeleid met NSG-stroomlogregistratie betekent dat er gedurende langere tijd afzonderlijke opslagkosten in rekening worden brengen. Als u gegevens voor altijd wilt bewaren en geen bewaarbeleid wilt toepassen, stelt u retentie (dagen) in op 0. Zie Prijzen en Network Watcher [prijzen](https://azure.microsoft.com/pricing/details/network-watcher/) voor [Azure Storage meer](https://azure.microsoft.com/pricing/details/storage/) informatie.

**Problemen met door de gebruiker gedefinieerde binnenkomende TCP-regels:** [netwerkbeveiligingsgroepen (NSG's)](../virtual-network/network-security-groups-overview.md) worden geïmplementeerd als een [stateful firewall.](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true) Vanwege de huidige platformbeperkingen worden door de gebruiker gedefinieerde regels die van invloed zijn op binnenkomende TCP-stromen echter op een staatloze manier geïmplementeerd. Als gevolg dit worden stromen die worden beïnvloed door door de gebruiker gedefinieerde regels voor binnenkomende gegevens, niet-beëindigend. Daarnaast worden byte- en pakkettellingen niet vastgelegd voor deze stromen. Het aantal bytes en pakketten dat wordt gerapporteerd in NSG-stroomlogboeken (en Traffic Analytics) kan daarom verschillen van het werkelijke aantal. Een opt-in-vlag die deze problemen verhelpt, is gepland om beschikbaar te zijn vanaf maart 2021. In de tussentijd kunnen klanten die ernstige problemen hebben als gevolg van dit gedrag zich via ondersteuning aanvragen. U kunt een ondersteuningsaanvraag indienen onder Network Watcher > NSG-stroomlogboeken.  

Binnenkomende stromen die zijn geregistreerd van internet-IP's naar VM's zonder openbare IP-adressen: VM's die geen openbaar IP-adres hebben dat is toegewezen via een openbaar IP-adres dat is gekoppeld aan de NIC als een openbaar IP-adres op exemplaarniveau of die deel uitmaken van een basis-load balancer-back-endpool, gebruiken [standaard-SNAT](../load-balancer/load-balancer-outbound-connections.md) en hebben een IP-adres dat door Azure is toegewezen om uitgaande connectiviteit te vergemakkelijken. Als gevolg hiervan ziet u mogelijk vermeldingen in het stroomlogboek voor stromen van IP-adressen via internet, als de stroom is bestemd voor een poort in het bereik van poorten die zijn toegewezen voor SNAT. Hoewel deze stromen niet naar de VM worden toegestaan, wordt de poging geregistreerd en wordt deze Network Watcher in het NSG-stroomlogboek van de VM weergegeven. We raden u aan om ongewenst inkomende internetverkeer expliciet te blokkeren met NSG.

**Probleem met Application Gateway V2-subnet-NSG:** stroomregistratie op de toepassingsgateway V2-subnet-NSG [wordt momenteel niet](../application-gateway/application-gateway-faq.yml#are-nsg-flow-logs-supported-on-nsgs-associated-to-application-gateway-v2-subnet) ondersteund. Dit probleem heeft geen invloed op Application Gateway V1.

**Niet-compatibele** services: Vanwege de huidige platformbeperkingen wordt een kleine set Azure-services niet ondersteund door NSG-stroomlogboeken. De huidige lijst met incompatibele services is
- [Azure Kubernetes Services (AKS)](https://azure.microsoft.com/services/kubernetes-service/)
- [Logic Apps](https://azure.microsoft.com/services/logic-apps/) 

## <a name="best-practices"></a>Aanbevolen procedures

**Inschakelen op kritieke VNET's/subnetten:** stroomlogboeken moeten worden ingeschakeld op alle kritieke VNET's/subnetten in uw abonnement als een best practice. 

**Schakel NSG-stroomlogregistratie** in op alle NSG's die zijn gekoppeld aan een resource: Stroomlogregistratie in Azure is geconfigureerd op de NSG-resource. Een stroom wordt slechts aan één NSG-regel gekoppeld. In scenario's waarin meerdere NSG's worden gebruikt, wordt u aangeraden NSG-stroomlogboeken in te schakelen op alle NSG's die in het subnet of de netwerkinterface van de resource zijn toegepast, om ervoor te zorgen dat al het verkeer wordt geregistreerd. Zie hoe verkeer wordt geëvalueerd in [netwerkbeveiligingsgroepen](../virtual-network/network-security-group-how-it-works.md) voor meer informatie. 

Enkele veelvoorkomende scenario's:
1. **Meerdere NIC's op een VM:** als er meerdere NIC's zijn gekoppeld aan een virtuele machine, moet stroomlogregistratie op al deze NIC's zijn ingeschakeld
1. NSG op **zowel NIC-** als subnetniveau: als NSG is geconfigureerd op het niveau van de NIC en het subnet, moet stroomlogregistratie worden ingeschakeld op beide NSG's. 

**Opslag inrichten:** opslag moet worden ingericht in afstemming op het verwachte stroomlogboekvolume.

**Naamgeving:** de naam van de NSG moet maximaal 80 chars zijn en de naam van de NSG-regel mag maximaal 65 chars zijn. Als de namen de tekenlimiet overschrijden, kan deze tijdens de logboekregistratie worden afgekapt.

## <a name="troubleshooting-common-issues"></a>Oplossen van algemene problemen

**Ik kan de NSG-stroomlogboeken niet inschakelen**

- **De resourceprovider Microsoft.Insights** is niet geregistreerd

Als u een fout _AuthorizationFailed_ of _GatewayAuthenticationFailed_ hebt ontvangen, hebt u mogelijk de resourceprovider Microsoft Insights niet ingeschakeld in uw abonnement. [Volg de instructies voor](./network-watcher-nsg-flow-logging-portal.md#register-insights-provider) het inschakelen van de Microsoft Insights-provider.

**Ik heb NSG-stroomlogboeken ingeschakeld maar ik zie de gegevens niet in mijn opslagaccount**

- **Configuratietijd**

Het kan tot vijf minuten duren voordat NSG-stroomlogboeken worden weergegeven in uw opslagaccount (als ze correct zijn geconfigureerd). Er wordt een PT1H.json weergegeven die kan worden geopend [zoals hier wordt beschreven](./network-watcher-nsg-flow-logging-portal.md#download-flow-log).

- **Geen verkeer op uw NSG's**

Soms worden er geen logboeken weergegeven omdat uw VM's niet actief zijn of dat er upstreamfilters aanwezig zijn op een App Gateway of andere apparaten die verkeer naar uw NSG's blokkeren.

**Ik wil NSG-stroomlogboeken automatiseren**

Ondersteuning voor automatisering via ARM-sjablonen is momenteel niet beschikbaar voor NSG-stroomlogboeken. Lees de [functieaankondiging](https://azure.microsoft.com/updates/arm-template-support-for-nsg-flow-logs/) voor meer informatie.

## <a name="faq"></a>Veelgestelde vragen

**Wat doen NSG-stroomlogboeken?**

Azure-netwerkbronnen kunnen worden gecombineerd en beheerd via [netwerkbeveiligingsgroepen (NSG's).](../virtual-network/network-security-groups-overview.md) Met NSG-stroomlogboeken kunt u 5-tuple stroominformatie over al het verkeer via uw NSG's in een logboek opslaan. De logboeken van de onbewerkte stroom worden naar een Azure Storage-account geschreven van waaruit ze verder kunnen worden verwerkt, geanalyseerd, opgevraagd of waar nodig kunnen worden geëxporteerd.

**Is het gebruik van stroomlogboeken van invloed op mijn netwerklatentie of prestaties?**

Stroomlogboekengegevens worden buiten het pad van uw netwerkverkeer verzameld en hebben daarom geen invloed op de netwerkdoorvoer of -latentie. U kunt stroomlogboeken maken of verwijderen zonder dat dit van invloed is op de netwerkprestaties.

**Hoe kan ik NSG-stroomlogboeken gebruiken met een opslagaccount achter een firewall?**

Als u een opslagaccount achter een firewall wilt gebruiken, moet u een uitzondering voor vertrouwde Microsoft-services bieden voor toegang tot uw opslagaccount:

- Navigeer naar het opslagaccount door de naam van het opslagaccount te typen in de algemene zoekopdracht in de portal of op de [pagina Opslagaccounts](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)
- Selecteer in  **de**  sectie INSTELLINGEN de optie  **Firewalls en virtuele netwerken**
- Selecteer **geselecteerde netwerken in** Toegang toestaan  **vanuit**. Vink  **vervolgens onder Uitzonderingen** het selectievakje aan naast ****Vertrouwde Microsoft-services toegang tot dit opslagaccount toestaan****
- Als deze optie al is geselecteerd, is er geen wijziging nodig.
- Zoek uw doel-NSG op de [overzichtspagina NSG-stroomlogboeken](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) en schakel NSG-stroomlogboeken in met het bovenstaande opslagaccount geselecteerd.

U kunt de Storage-logboeken na enkele minuten controleren. U ziet dan een bijgewerkte tijdstempel of een nieuw JSON-bestand.

**Hoe kan ik NSG-stroomlogboeken gebruiken met een opslagaccount achter een service-eindpunt?**

NSG-stroomlogboeken zijn compatibel met service-eindpunten zonder extra configuratie. Zie de [zelfstudie over het inschakelen van service-eindpunten](../virtual-network/tutorial-restrict-network-access-to-resources.md#enable-a-service-endpoint) in uw virtuele netwerk.

**Wat is het verschil tussen stroomlogboeken versie 1 & 2?**

Stroomlogboeken versie 2 introduceert het concept stroomtoestand _&_ slaat informatie op over verzonden bytes en pakketten. [Meer informatie](#log-format)

## <a name="pricing"></a>Prijzen

NSG-stroomlogboeken worden in rekening gebracht per GB aan logboeken die zijn verzameld en worden met een gratis laag van 5 GB/maand per abonnement in rekening gebracht. Zie de pagina met prijzen voor Network Watcher huidige [prijzen in uw regio.](https://azure.microsoft.com/pricing/details/network-watcher/)

Opslag van logboeken wordt afzonderlijk in rekening gebracht. Zie Azure Storage Pagina met prijzen voor [blok-blobs](https://azure.microsoft.com/pricing/details/storage/blobs/) voor relevante prijzen.
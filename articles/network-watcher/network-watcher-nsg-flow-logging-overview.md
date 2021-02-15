---
title: Inleiding tot stroom logboek registratie voor Nsg's
titleSuffix: Azure Network Watcher
description: In dit artikel wordt uitgelegd hoe u de functie NSG flow logs van Azure Network Watcher gebruikt.
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
ms.openlocfilehash: 2222c6b020f712282a78ac5f82a87015d4cd86a5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368197"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Introductie van stroomlogboeken voor netwerkbeveiligingsgroepen

## <a name="introduction"></a>Introductie

Stroom logboeken voor [netwerk beveiligings groepen](../virtual-network/network-security-groups-overview.md#security-rules) (NSG) is een functie van Azure Network Watcher waarmee u informatie kunt vastleggen over IP-verkeer dat wordt doorgelopen via een NSG. Stroom gegevens worden verzonden naar Azure Storage accounts van waaruit u toegang hebt, en deze kunt u exporteren naar een wille keurig visualisatie programma, SIEM of ID'S van uw keuze.

![overzicht van stroom logboeken](./media/network-watcher-nsg-flow-logging-overview/homepage.jpg)

## <a name="why-use-flow-logs"></a>Waarom stroom Logboeken gebruiken?

Het is essentieel om uw eigen netwerk te bewaken, beheren en kennen voor ongeoorloofde beveiliging, naleving en prestaties. Het is belang rijk om uw eigen omgeving te beschermen en te optimaliseren. Vaak moet u de huidige status van het netwerk weten, die verbinding maakt, waar ze verbinding mee maken, welke poorten open zijn op het Internet, het verwachte netwerk gedrag, onregelmatig netwerk gedrag en onverwachte toename van het verkeer.

Stroom logboeken zijn de bron van waarheid voor alle netwerk activiteiten in uw cloud omgeving. Of u nu een geplande start wilt maken voor het optimaliseren van resources of een grote onderneming die probeert indringing te detecteren, stroom logboeken zijn de meest relevante treffers. U kunt deze gebruiken voor het optimaliseren van netwerk stromen, het bewaken van de door Voer, het controleren van de naleving, het detecteren van indringingen en meer.

## <a name="common-use-cases"></a>Algemene scenario’s

**Netwerk bewaking**: Identificeer Onbekend of ongewenst verkeer. Verkeers niveaus en bandbreedte gebruik bewaken. Filter stroom logboeken op IP en poort om inzicht te krijgen in het toepassings gedrag. U kunt stroom logboeken exporteren naar analyse-en visualisatie hulpprogramma's van uw keuze om controle dashboards in te stellen.

**Gebruiks bewaking en optimalisatie:** De belangrijkste talks in uw netwerk identificeren. Combi neer met GeoIP-gegevens om verkeer tussen regio's te identificeren. Inzicht in de groei van verkeer voor capaciteits prognoses. Gegevens gebruiken om overtly beperkende verkeers regels te verwijderen.

**Naleving**: stroom gegevens gebruiken voor het controleren van de netwerk isolatie en naleving van bedrijfs toegangs regels

**Netwerk-forensische & beveiligings analyse**: netwerk stromen analyseren van gemanipuleerde ip's en netwerk interfaces. Stroom logboeken exporteren naar een wille keurig SIEM of id-hulp programma van uw keuze.

## <a name="how-logging-works"></a>Hoe logboek registratie werkt

**Sleutel eigenschappen**

- Stroom logboeken worden uitgevoerd op [laag 4](https://en.wikipedia.org/wiki/OSI_model#Layer_4:_Transport_Layer) en alle IP-stromen die in en uit een NSG worden opgenomen, worden geregistreerd
- Logboeken worden verzameld op **1-min-interval** via het Azure-platform en hebben geen invloed op de resources van klanten of netwerk prestaties.
- Logboeken worden geschreven in de JSON-indeling en tonen uitgaand en binnenkomend verkeer per NSG regel.
- Elke logboek record bevat de netwerk interface (NIC) de stroom is van toepassing op 5-tuple-informatie, de verkeers beslissing & (alleen versie 2) doorvoer gegevens. Zie de _logboek indeling_ hieronder voor meer informatie.
- Stroom logboeken hebben een Bewaar functie waarmee de logboeken automatisch kunnen worden verwijderd tot een jaar nadat ze zijn gemaakt. 

> [!NOTE]
> Bewaren is alleen beschikbaar als u gebruikmaakt van [v2-opslag accounts (GPv2) voor algemeen](../storage/common/storage-account-overview.md#types-of-storage-accounts)gebruik. 

**Basisconcepten**

- Software gedefinieerde netwerken zijn ingedeeld rond virtuele netwerken (VNETs) en subnetten. De beveiliging van deze VNets en subnetten kan worden beheerd met behulp van een NSG.
- Een netwerk beveiligings groep (NSG) bevat een lijst met _beveiligings regels_ voor het toestaan of weigeren van netwerk verkeer in bronnen waarmee deze is verbonden. Nsg's kan worden gekoppeld aan subnetten, afzonderlijke Vm's of afzonderlijke netwerk interfaces (NIC) die zijn gekoppeld aan Vm's (Resource Manager). Zie [overzicht van netwerk beveiligings groepen](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)voor meer informatie.
- Alle verkeers stromen in uw netwerk worden geëvalueerd aan de hand van de regels in de desbetreffende NSG.
- Het resultaat van deze evaluaties is NSG-stroom Logboeken. Stroom logboeken worden verzameld via het Azure-platform en hoeven niet te worden gewijzigd in de resources van de klant.
- Opmerking: regels bestaan uit twee typen: het beëindigen van & niet-beëindigd, elk met verschillende gedragingen voor logboek registratie.
- - Regels voor het weigeren van NSG worden beëindigd. De NSG die het verkeer weigert, wordt geregistreerd in de stroom logboeken en de verwerking in dit geval wordt gestopt nadat een NSG verkeer heeft geweigerd. 
- - NSG toestaan regels niet af te sluiten, wat betekent dat zelfs als een NSG dit toestaat, de verwerking wordt voortgezet naar de volgende NSG. De laatste NSG die verkeer toestaat, registreert het verkeer naar stroom Logboeken.
- NSG-stroom logboeken worden geschreven naar opslag accounts waarvan ze toegang hebben.
- U kunt stroom logboeken exporteren, verwerken, analyseren en visualiseren met behulp van hulpprogram ma's zoals TA, Splunk, Grafana, Stealthwatch, enzovoort.

## <a name="log-format"></a>Logboek indeling

Stroom logboeken bevatten de volgende eigenschappen:

* de **tijd** waarop de gebeurtenis is geregistreerd
* **systemId** -systeem-id van de netwerk beveiligings groep.
* **categorie** : de categorie van de gebeurtenis. De categorie is altijd **NetworkSecurityGroupFlowEvent**
* **ResourceID** -de resource-id van de NSG
* **operationname** -altijd NetworkSecurityGroupFlowEvents
* **Eigenschappen** : een verzameling eigenschappen van de stroom
    * **Versie** -versie nummer van het gebeurtenis schema voor het stroom logboek
    * **stromen** : een verzameling stromen. Deze eigenschap heeft meerdere vermeldingen voor verschillende regels
        * **regel** -regel waarvoor de stromen worden weer gegeven
            * **stromen** -een verzameling stromen
                * **Mac** : het MAC-adres van de NIC voor de virtuele machine waarop de stroom is verzameld
                * **flowTuples** : een teken reeks die meerdere eigenschappen voor de stroom-tuple in een door komma's gescheiden indeling bevat
                    * **Tijds tempel** : deze waarde is het tijds tempel van wanneer de stroom is opgetreden in de UNIX-epoche-indeling
                    * **Bron-IP** -het bron-IP-adres
                    * **Doel-IP** -het doel-IP-adres
                    * **Bron poort** : de bron poort
                    * **Doel poort** -de doel poort
                    * **Protocol** -het Protocol van de stroom. Geldige waarden zijn **T** voor TCP en **U** voor UDP
                    * **Verkeers stroom** : de richting van de verkeers stroom. Geldige waarden zijn **I** voor inkomend en **O** voor uitgaand verkeer.
                    * **Beslissing van verkeer** : of verkeer is toegestaan of geweigerd. Geldige **waarden zijn voor** toegestaan en **D** voor geweigerd.
                    * **Stroom status-alleen versie 2** : Hiermee wordt de status van de stroom vastgelegd. Mogelijke statussen zijn **B**: Begin, wanneer een stroom wordt gemaakt. Er worden geen statistische gegevens geleverd. **C**: Continu, voor een actieve stroom. Statistische gegevens worden geleverd met intervallen van 5 minuten. **E**: Eind, wanneer een stroom is beëindigd. Er worden statistische gegevens geleverd.
                    * **Pakketten-bron naar doel-versie 2 alleen** Het totale aantal TCP-of UDP-pakketten dat sinds de laatste update van de bron naar het doel is verzonden.
                    * **Verzonden bytes-bron naar doel-versie 2** Het totale aantal TCP-of UDP-pakket bytes dat sinds de laatste update van de bron naar het doel is verzonden. Pakketbytes omvatten de pakket-header en -nettolading.
                    * **Pakketten-doel naar bron-versie 2 alleen** Het totale aantal TCP-of UDP-pakketten dat sinds de laatste update van de bestemming naar de bron is verzonden.
                    * **Verzonden bytes-bestemming naar bron-versie 2** Het totale aantal bytes van de TCP-en UDP-pakketten dat sinds de laatste update van de bestemming naar de bron is verzonden. Pakketbytes omvatten een pakket-header en -nettolading.


**NSG flow-logboeken versie 2 (VS versie 1)** 

Versie 2 van de logboeken introduceert het concept van de stroom status. U kunt configureren welke versie van de stroom logboeken u ontvangt.

Stroom status _B_ wordt vastgelegd wanneer een stroom wordt gestart. De stroom status _C_ en de stroom status _E_ zijn staten die de voortzetting van een stroom en stroom beëindiging markeren. Zowel de _C_ -als de _E_ -status bevat informatie over de band breedte van het verkeer.

### <a name="sample-log-records"></a>Voorbeeld logboek records

De volgende tekst is een voor beeld van een stroom logboek. Zoals u ziet, zijn er meerdere records die voldoen aan de eigenschappen lijst die in de voor gaande sectie wordt beschreven.

> [!NOTE]
> De waarden in de eigenschap *flowTuples* zijn een door komma's gescheiden lijst.
 
**Voor beeld van versie 1 NSG-stroom logboek indeling**
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
**Voor beeld van versie 2 NSG-stroom logboek indeling**
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
**Uitleg van logboek-tuple**

![stroom logboeken tuple](./media/network-watcher-nsg-flow-logging-overview/tuple.png)

**Berekening van de voor beeld-band breedte**

Verstroom Tuples van een TCP-conversatie tussen 185.170.185.105:35370 en 10.2.0.4:23:

"1493763938, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, B,,,," "1493695838, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, C, 1021, 588096, 8005, 4610880" "1493696138, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, E, 52, 29952, 47, 27072"

Voor voortzetting _C_ en de laatste _E_ stroom status zijn de aantallen voor bytes en pakketten cumulatief van de tijd van de vorige record in de stroom tupel. Verwijzen naar het vorige voor beeld van de conversatie is het totale aantal overgedragen pakketten 1021 + 52 + 8005 + 47 = 9125. Het totale aantal overgebrachte bytes is 588096 + 29952 + 4610880 + 27072 = 5256000.


## <a name="enabling-nsg-flow-logs"></a>NSG-stroomlogboeken inschakelen

Gebruik de relevante koppeling hieronder voor hulp lijnen voor het inschakelen van stroom Logboeken.

- [Azure-portal](./network-watcher-nsg-flow-logging-portal.md)
- [PowerShell](./network-watcher-nsg-flow-logging-powershell.md)
- [CLI](./network-watcher-nsg-flow-logging-cli.md)
- [REST](./network-watcher-nsg-flow-logging-rest.md)
- [Azure Resource Manager](./network-watcher-nsg-flow-logging-azure-resource-manager.md)

## <a name="updating-parameters"></a>Para meters bijwerken

**Azure-portal**

Ga in het Azure Portal naar de sectie NSG-stroom Logboeken in Network Watcher. Klik vervolgens op de naam van de NSG. Hiermee wordt het deel venster instellingen voor het stroom logboek weer gegeven. Wijzig de gewenste para meters en druk op **Opslaan** om de wijzigingen te implementeren.

**PS/CLI/REST/ARM**

Als u para meters wilt bijwerken via opdracht regel Programma's, gebruikt u dezelfde opdracht die wordt gebruikt om stroom Logboeken in te scha kelen (vanaf hierboven), maar met de bijgewerkte para meters die u wilt wijzigen.

## <a name="working-with-flow-logs"></a>Werken met stroom logboeken

*Stroom logboeken lezen en exporteren*

- [&amp;Weergave stroom logboeken downloaden vanuit de portal](./network-watcher-nsg-flow-logging-portal.md#download-flow-log)
- [Stroom logboeken lezen met behulp van Power shell-functies](./network-watcher-read-nsg-flow-logs.md)
- [NSG-stroom logboeken exporteren naar Splunk](https://www.splunk.com/en_us/blog/tips-and-tricks/splunking-microsoft-azure-network-watcher-data.html)

Terwijl flow logboeken doel-Nsg's, worden ze niet weer gegeven als de andere logboeken. Stroom logboeken worden alleen in een opslag account opgeslagen en volgen het pad naar Logboeken dat in het volgende voor beeld wordt weer gegeven:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

*Stroom logboeken visualiseren*

- [Azure Traffic Analytics](./traffic-analytics.md) is een systeem eigen Azure-service voor het verwerken van stroom logboeken, het uitpakken van inzichten en het visualiseren van stroom Logboeken. 
- [Vind NSG-stroom logboeken visualiseren met Power BI](./network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Vind NSG-stroom logboeken visualiseren met elastische stack](./network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Vind NSG-stroom logboeken beheren en analyseren met behulp van Grafana](./network-watcher-nsg-grafana.md)
- [Vind NSG-stroom logboeken beheren en analyseren met behulp van Graylog](./network-watcher-analyze-nsg-flow-logs-graylog.md)


## <a name="nsg-flow-logging-considerations"></a>Aandachtspunten voor NSG flow-logboek registratie

**Overwegingen voor het opslag account**: 

- Locatie: het gebruikte opslag account moet zich in dezelfde regio bevinden als de NSG.
- Prestatie niveau: momenteel worden alleen opslag accounts met een standaard laag ondersteund.
- Zelfs wisseling van sleutels: als u de toegangs sleutels wijzigt/roteert naar uw opslag account, werken NSG-stroom logboeken niet meer. U kunt dit probleem oplossen door de NSG-stroom Logboeken uit te scha kelen en opnieuw in te scha kelen.

**Kosten** voor het vastleggen van de stroom: de logboek registratie voor NSG wordt gefactureerd op het volume van de logboeken die zijn gegenereerd. High Traffic volume kan leiden tot een groot stroom logboek volume en de bijbehorende kosten. De prijzen voor het NSG-stroom logboek bevatten geen onderliggende kosten voor opslag. Het gebruik van de functie voor het Bewaar beleid met de logboek registratie van de NSG-stroom houdt in dat afzonderlijke opslag kosten voor langere Peri Oden worden bespaard. Als u de functie voor het Bewaar beleid niet nodig hebt, raden we u aan deze waarde in te stellen op 0. Zie voor meer informatie [Network Watcher prijzen](https://azure.microsoft.com/pricing/details/network-watcher/) en [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage/) voor meer informatie.

**Problemen met door de gebruiker gedefinieerde binnenkomende TCP-regels**: [netwerk beveiligings groepen (nsg's)](../virtual-network/network-security-groups-overview.md) worden geïmplementeerd als een [stateful firewall](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true). Vanwege de beperkingen van het huidige platform worden door de gebruiker gedefinieerde regels die van invloed zijn op binnenkomende TCP-stromen echter op een staatloze manier geïmplementeerd. Als gevolg hiervan worden stromen beïnvloed door door de gebruiker gedefinieerde binnenkomende regels niet beëindigd. Daarnaast worden geen byte-en pakket aantallen voor deze stromen vastgelegd. Het aantal bytes en pakketten dat in de NSG-stroom Logboeken (en Traffic Analytics) wordt gerapporteerd, kan daarom afwijken van de werkelijke getallen. Een opt-in-vlag waarmee deze problemen worden opgelost, is gepland om uiterlijk op maart 2021 te zijn. In de tussen tijd kunnen klanten die ernstige problemen ondervinden vanwege dit gedrag, via ondersteuning aanvragen voor intrekken, een ondersteunings aanvraag doen onder Network Watcher > NSG-stroom Logboeken.  

**Binnenkomende stromen die zijn geregistreerd van Internet ip's naar vm's zonder open bare ip's**: vm's waaraan geen openbaar IP-adres is toegewezen via een openbaar IP-adres dat is gekoppeld aan de NIC als instantie niveau openbaar IP of die deel uitmaken van een basis Load Balancer back-end-groep, gebruiken [standaard SNAT](../load-balancer/load-balancer-outbound-connections.md) en hebben een IP-adres dat is toegewezen door Azure om uitgaande connectiviteit te vergemakkelijken. Als gevolg hiervan ziet u mogelijk stroom logboek vermeldingen voor stromen van IP-adressen van Internet, als de stroom bestemd is voor een poort in het bereik van poorten die zijn toegewezen voor SNAT. Hoewel Azure deze stromen naar de virtuele machine niet toestaat, wordt de poging geregistreerd en wordt deze weer gegeven in het NSG-stroom logboek van Network Watcher. U wordt aangeraden ongewenste binnenkomend Internet verkeer expliciet met NSG te blok keren.

**Probleem met Application Gateway v2-SUBNET NSG**: flow-logboek registratie op het toepassings gateway v2-subnet NSG wordt momenteel [niet ondersteund](../application-gateway/application-gateway-faq.yml#are-nsg-flow-logs-supported-on-nsgs-associated-to-application-gateway-v2-subnet) . Dit probleem heeft geen invloed op Application Gateway v1.

**Incompatibele Services**: vanwege de beperkingen van het huidige platform worden een kleine set Azure-Services niet ondersteund door NSG-stroom Logboeken. De huidige lijst met incompatibele Services is
- [Azure Kubernetes Services (AKS)](https://azure.microsoft.com/services/kubernetes-service/)
- [Logic Apps](https://azure.microsoft.com/services/logic-apps/) 

## <a name="best-practices"></a>Aanbevolen procedures

**Inschakelen op kritieke VNETs/subnetten**: stroom logboeken moeten worden ingeschakeld op alle kritieke VNETs/subnetten in uw abonnement als controle bare en beveiligings best practice. 

**Schakel logboek registratie van de NSG-stroom in op alle nsg's die zijn gekoppeld aan een resource**: stroom logboek registratie in Azure is geconfigureerd op de NSG-resource. Een stroom wordt alleen gekoppeld aan één NSG-regel. In scenario's waarin meerdere Nsg's worden gebruikt, wordt aangeraden om NSG flow-Logboeken in te scha kelen op alle Nsg's die zijn toegepast op het subnet of de netwerk interface van de resource om ervoor te zorgen dat alle verkeer wordt geregistreerd. Zie [hoe verkeer wordt geëvalueerd](../virtual-network/network-security-group-how-it-works.md) in netwerk beveiligings groepen voor meer informatie. 

Enkele veelvoorkomende scenario's:
1. **Meerdere nic's op een VM**: als er meerdere nic's zijn gekoppeld aan een virtuele machine, moet de stroom logboek registratie worden ingeschakeld
1. **Met NSG op NIC-en subnetniveau**: als NSG is geconfigureerd op de NIC en op het subnet-niveau, moet de stroom logboek registratie worden ingeschakeld op beide nsg's. 

**Opslag inrichten**: opslag moet worden ingericht in afstemming met het verwachte flow-logboek volume.

Naam: de naam van de NSG moet Maxi maal 80 tekens en de namen **van** de NSG-regels tot 65 tekens zijn. Als de namen groter zijn dan de teken limiet, kan deze worden afgekapt tijdens het vastleggen.

## <a name="troubleshooting-common-issues"></a>Oplossen van algemene problemen

**Ik kan de NSG-stroomlogboeken niet inschakelen**

- De resource provider van **micro soft. Insights** is niet geregistreerd

Als u een fout _AuthorizationFailed_ of _GatewayAuthenticationFailed_ hebt ontvangen, hebt u mogelijk de resourceprovider Microsoft Insights niet ingeschakeld in uw abonnement. [Volg de instructies](./network-watcher-nsg-flow-logging-portal.md#register-insights-provider) om de micro soft Insights-provider in te scha kelen.

**Ik heb NSG-stroomlogboeken ingeschakeld maar ik zie de gegevens niet in mijn opslagaccount**

- **Configuratietijd**

Het kan tot vijf minuten duren voordat NSG-stroomlogboeken worden weergegeven in uw opslagaccount (als ze correct zijn geconfigureerd). Er wordt een PT1H.json weergegeven die kan worden geopend [zoals hier wordt beschreven](./network-watcher-nsg-flow-logging-portal.md#download-flow-log).

- **Geen verkeer op uw NSG's**

Soms worden er geen logboeken weergegeven omdat uw VM's niet actief zijn of dat er upstreamfilters aanwezig zijn op een App Gateway of andere apparaten die verkeer naar uw NSG's blokkeren.

**Ik wil NSG-stroomlogboeken automatiseren**

Ondersteuning voor automatisering via ARM-sjablonen is momenteel niet beschikbaar voor NSG-stroomlogboeken. Lees de [aankondiging](https://azure.microsoft.com/updates/arm-template-support-for-nsg-flow-logs/) van de functie voor meer informatie.

## <a name="faq"></a>Veelgestelde vragen

**Wat gebeurt er met NSG-stroom logboeken?**

Azure-netwerk bronnen kunnen worden gecombineerd en beheerd via [netwerk beveiligings groepen (nsg's)](../virtual-network/network-security-groups-overview.md). Met NSG-stroom Logboeken kunt u 5-tuple-stroom gegevens registreren over al het verkeer via uw Nsg's. De onbewerkte stroom logboeken worden naar een Azure Storage-account geschreven, waar ze verder kunnen worden verwerkt, geanalyseerd, opgevraagd of geëxporteerd als dat nodig is.

**Heeft stroom logboeken invloed op mijn netwerk latentie of prestaties?**

Gegevens van stroom logboeken worden verzameld buiten het pad van het netwerk verkeer en zijn daarom niet van invloed op de netwerk doorvoer of-latentie. U kunt stroom logboeken maken of verwijderen zonder risico van invloed op de netwerk prestaties.

**Hoe kan ik NSG-stroom Logboeken gebruiken met een opslag account achter een firewall?**

Als u een opslag account achter een firewall wilt gebruiken, moet u een uitzonde ring voor vertrouwde micro soft-Services voor toegang tot uw opslag account opgeven:

- Navigeer naar het opslag account door de naam van het opslag account in de globale zoek opdracht op de portal of op de [pagina opslag accounts](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts) te typen.
- Selecteer in de sectie  **instellingen**  de optie  **firewalls en virtuele netwerken**
- Selecteer in **toegang toestaan vanuit** de optie  **geselecteerde netwerken**. Tik vervolgens onder  **uitzonde ringen** op het vakje naast * * * * vertrouwde micro soft-Services toegang geven tot dit opslag account * * * *
- Als deze optie al is geselecteerd, is er geen wijziging nodig.
- Zoek uw doel-NSG op de [overzichts pagina van de NSG-stroom logboeken](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) en schakel NSG-stroom Logboeken in met het hierboven geselecteerde opslag account.

U kunt de Storage-logboeken na enkele minuten controleren. U ziet dan een bijgewerkte tijdstempel of een nieuw JSON-bestand.

**Hoe kan ik NSG-stroom Logboeken gebruiken met een opslag account achter een service-eind punt?**

NSG-stroom logboeken zijn compatibel met Service-eind punten zonder extra configuratie. Raadpleeg de [zelf studie over het inschakelen van service-eind punten](../virtual-network/tutorial-restrict-network-access-to-resources.md#enable-a-service-endpoint) in uw virtuele netwerk.

**Wat is het verschil tussen stroom logboeken versie 1 & 2?**

Stroom logboeken versie 2 introduceert het concept van de _stroom status_ & slaat informatie op over verzonden bytes en pakketten. [Meer informatie](#log-format)

## <a name="pricing"></a>Prijzen

NSG-stroom logboeken worden in rekening gebracht per GB aan logboeken die worden verzameld en worden geleverd met een gratis laag van 5 GB/maand per abonnement. Zie de [pagina met prijzen voor Network Watcher](https://azure.microsoft.com/pricing/details/network-watcher/)voor de huidige prijzen in uw regio.

Voor het opslaan van Logboeken wordt afzonderlijk kosten in rekening gebracht. Zie de [pagina met prijzen voor Azure Storage blok-BLOB](https://azure.microsoft.com/pricing/details/storage/blobs/) voor relevante prijzen.
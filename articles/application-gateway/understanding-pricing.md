---
title: Prijs informatie-Azure-toepassing gateway
description: In dit artikel wordt het facturerings proces voor de Azure-toepassing gateway en Web Application firewall beschreven voor v1-v2 Sku's
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: conceptual
ms.custom: references_regions
ms.date: 09/01/2020
ms.author: azhussai
ms.openlocfilehash: 89ba6b7a69c95951a083628f23be68d811c7768c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96601608"
---
# <a name="understanding-pricing-for-azure-application-gateway-and-web-application-firewall"></a>Meer informatie over de prijzen voor Azure-toepassing gateway en Web Application firewall

>[!NOTE]
>De prijzen die in dit artikel worden weer gegeven, zijn voor beelden en zijn alleen bedoeld ter illustratie. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/application-gateway/)voor prijs informatie volgens uw regio.

Azure-toepassing gateway is een oplossing voor taak verdeling op laag 7, waarmee u de levering van een schaal bare, Maxi maal beschik bare webtoepassing op Azure kunt verbeteren.

Er zijn geen kosten vooraf of ontslag kosten gekoppeld aan Application Gateway.
U wordt alleen gefactureerd voor de resources die vooraf zijn ingericht en worden gebruikt op basis van het werkelijke verbruik van elk uur. Kosten die zijn gekoppeld aan Application Gateway, worden onderverdeeld in twee onderdelen: vaste kosten en variabele kosten. De werkelijke kosten binnen elk onderdeel zijn afhankelijk van de gebruikte SKU.

In dit artikel worden de kosten beschreven die aan elke SKU zijn gekoppeld, en wordt aanbevolen dat gebruikers dit document gebruiken voor het plannen en beheren van kosten die zijn gekoppeld aan de Azure-toepassing-gateway.

## <a name="v1-skus"></a>V1 Sku's

Standard Application Gateway en WAF v1 Sku's worden gefactureerd als een combi natie van:

* Vaste kosten

    Kosten op basis van het tijdstip waarop een bepaald type Application Gateway-WAF wordt ingericht en uitgevoerd voor het verwerken van aanvragen.
    Het onderdeel vaste kosten houdt rekening met de volgende factoren:
    * Laag-standaard Application Gateway of WAF
    * Grootte-klein, gemiddeld & groot
    * Aantal instanties-aantal exemplaren dat moet worden geïmplementeerd

    Voor N-instanties zijn de gekoppelde kosten N * kosten van één exemplaar van een bepaalde laag (Standard & WAF) & grootte (kleine, middel grote & groot).

* Variabele kosten

    Kosten op basis van de hoeveelheid gegevens die wordt verwerkt door de Application Gateway-WAF. De aanvraag-en antwoord bytes die door de Application Gateway zijn verwerkt, worden in aanmerking genomen voor facturering.

Totale kosten = vaste kosten + variabele kosten

### <a name="standard-application-gateway"></a>Standaard Application Gateway

De vaste kosten & variabele kosten worden berekend op basis van het type Application Gateway.
In de volgende tabel ziet u voor beelden van prijzen op basis van een moment opname van Amerikaanse prijzen voor Nederland en zijn alleen bedoeld voor illustratie doeleinden.

#### <a name="fixed-cost-east-us-region-pricing"></a>Vaste kosten (regionale prijzen voor de VS-Oost)

|              Application Gateway type             |  Kosten ($/uur)  |
| ------------------------------------------------- | ---------------|
|                     Klein                         |    $0,025      |
|                     Normaal                        |    $0,07       |
|                     Groot                         |    $0,32       |

Maandelijkse prijs schattingen zijn gebaseerd op 730 gebruiks uren per maand.

#### <a name="variable-cost-east-us-region-pricing"></a>Variabele kosten (prijzen Oost-Amerikaanse regio)

|              Verwerkte gegevens             |  Klein ($/GB)  |  Gemiddeld ($/GB) |  Groot ($/GB) |
| --------------------------------------- | -------------- | -------------- | ------------- |
| Eerste 10 TB/maand                       |     $0,008     |      Gratis      |     Gratis      |
| Volgende 30 TB (10 – 40 TB)/maand             |     $0,008     |     $0,007     |     Gratis      |
| Meer dan 40 TB/maand                        |     $0,008     |     $0,007     |     $0,0035   |

Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/application-gateway/)voor meer informatie over prijzen volgens uw regio.

### <a name="waf-v1"></a>WAF V1

Vaste kosten & variabele kosten worden berekend op basis van het ingerichte Application Gateway type.

In de volgende tabel ziet u voor beelden van prijzen op basis van een moment opname van Amerikaanse prijzen voor Nederland en alleen ter illustratie.

#### <a name="fixed-cost-east-us-region-pricing"></a>Vaste kosten (regionale prijzen voor de VS-Oost)

|              Application Gateway type             |  Kosten ($/uur)  |
| ------------------------------------------------- | ---------------|
|                     Klein                         |       NA       |
|                     Normaal                        |     $0,126     |
|                     Groot                         |     $0,448     |

Maandelijkse prijs schattingen zijn gebaseerd op 730 gebruiks uren per maand.

#### <a name="variable-cost-east-us-region-pricing"></a>Variabele kosten (prijzen Oost-Amerikaanse regio)

|              Verwerkte gegevens             |  Klein ($/GB)  |  Gemiddeld ($/GB) |  Groot ($/GB) |
| --------------------------------------- | -------------- | -------------- | ------------- |
| Eerste 10 TB/maand                       |     $0,008     |      Gratis      |     Gratis      |
| Volgende 30 TB (10 – 40 TB)/maand             |     $0,008     |     $0,007     |     Gratis      |
| Meer dan 40 TB/maand                        |     $0,008     |     $0,007     |     $0,0035   |

Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/application-gateway/)voor meer informatie over prijzen volgens uw regio.

> [!NOTE]
> Uitgaande gegevens overdrachten: gegevens die worden verzonden vanuit Azure-data centers van toepassings gateways worden in rekening gebracht tegen standaard [tarieven voor gegevens overdracht](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="example-1-a--standard-application-gateway-with-1-instance-count"></a>Voor beeld 1 (a): standaard Application Gateway met 1 aantal exemplaren

We gaan ervan uit dat u een standaard Application Gateway van het type gemiddeld hebt ingericht met een exemplaar van 1 en 500 GB per maand wordt verwerkt. Uw Application Gateway kosten met de hierboven vermelde prijzen worden als volgt berekend:

Vaste prijs = $0,07 * 730 (uren) = $51,1 maandelijkse prijs schattingen zijn gebaseerd op het gebruik van 730 uur per maand.

Variabele kosten = vrij (gemiddelde laag heeft geen kosten voor de eerste 10 TB verwerkte per maand) totale kosten = $51,1 + 0 = $51,1 

> [!NOTE]
> Ter ondersteuning van scenario's met een hoge Beschik baarheid, moet u Mini maal twee exemplaren voor v1 Sku's instellen. Zie [Sla voor Application Gateway](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/)

### <a name="example-1-b--standard-application-gateway-with--1-instance-count"></a>Voor beeld 1 (b): standaard Application Gateway met het aantal exemplaren van > 1

We gaan ervan uit dat u een standaard Application Gateway van het type gemiddeld hebt ingericht met vijf instanties en 500 GB per maand wordt verwerkt. Uw Application Gateway kosten met de hierboven vermelde prijzen worden als volgt berekend:

Vaste prijs = 5 (aantal exemplaren) * $0,07 * 730 (uur) = $255,5 maandelijkse prijs schattingen zijn gebaseerd op het gebruik van 730 uur per maand.

Variabele kosten = vrij (gemiddelde laag heeft geen kosten voor de eerste 10 TB verwerkte per maand) totale kosten = $255,5 + 0 = $255,5 

### <a name="example-2--waf-application-gateway"></a>Voor beeld 2: WAF Application Gateway

We gaan ervan uit dat u een klein type Standard Application Gateway hebt ingericht en een groot type WAF Application Gateway voor de eerste vijf tien dagen van de maand. De kleine toepassings gateway verwerkt 15 TB tijdens de duur dat deze actief is en de grote WAF toepassings gateway verwerkt 100 TB tijdens de duur dat deze actief is. Uw Application Gateway kosten met de hierboven vermelde prijzen worden als volgt berekend: 

###### <a name="small-instance-standard-application-gateway"></a>Standaard Application Gateway voor kleine instanties

24 uur * 15 dagen = 360 uur

Vaste prijs = $0,025 * 360 (uur) = $9

Variabele kosten = 10 * 1000 * $0.008/GB + 5 * 1000 * $0.008/GB = $120

Totale kosten = $9 + $120 = $129

###### <a name="large-instance-waf-application-gateway"></a>WAF voor grote instanties Application Gateway
24 uur * 15 dagen = 360 uur

Vaste prijs = $0,448 * 360 (uur) = $161,28

Variabele kosten = 60 * 1000 * $0.0035/GB = $210 (de grote laag heeft geen kosten voor de eerste verwerkte 40 TB per maand)

Totale kosten = $161,28 + $210 = $371,28

## <a name="v2-skus"></a>V2 Sku's  

Application Gateway v2 en WAF v2 Sku's ondersteunen automatisch schalen en garanderen standaard hoge Beschik baarheid.

### <a name="key-terms"></a>Sleutel termen

##### <a name="capacity-unit"></a>Capaciteits eenheid 
Capaciteits eenheid is de meting van het capaciteits gebruik voor een Application Gateway over meerdere para meters.

Eén capaciteits eenheid bestaat uit de volgende para meters:
* 2500 permanente verbindingen
* door Voer van 2,22-Mbps
* 1 Compute-eenheid

Als een van deze para meters wordt overschreden, zijn er nog een of meer n capaciteits eenheden nodig, zelfs als de andere twee para meters de limieten van deze enkele capaciteits eenheid niet overschrijden.
De para meter met het hoogste gebruik tussen de drie hierboven wordt intern gebruikt voor het berekenen van capaciteits eenheden, die op zijn beurt worden gefactureerd.

##### <a name="compute-unit"></a>Reken eenheid
Reken eenheid is de meting van verbruikte reken capaciteit. Factoren die invloed hebben op het gebruik van reken eenheden zijn TLS-verbindingen per seconde, berekeningen voor het herschrijven van URL'S en de verwerking van WAF-regels. Het aantal aanvragen dat een Compute-eenheid kan verwerken, is afhankelijk van verschillende criteria, zoals de sleutel grootte van TLS-certificaten, het algoritme voor sleutel uitwisseling, het opnieuw schrijven van kopteksten en in het geval van een WAF-binnenkomende aanvraag grootte.

Richt lijnen voor reken eenheden:
* Standard_v2-elke reken eenheid kan ongeveer 50 verbindingen per seconde met een RSA 2048-bits sleutel TLS-certificaat zijn.

* WAF_v2-elke Compute-eenheid kan ongeveer 10 gelijktijdige aanvragen per seconde ondersteunen voor 70-30% mix van verkeer met 70% aanvragen van minder dan 2 KB GET/POST en resterend. WAF-prestaties worden op dit moment niet beïnvloed door de antwoord grootte.

##### <a name="instance-count"></a>Aantal instanties 
Het vooraf inrichten van resources voor Application Gateway v2 Sku's wordt gedefinieerd in termen van het aantal exemplaren. Elk exemplaar garandeert een minimum van 10 capaciteits eenheden in termen van de verwerkings capaciteit. Hetzelfde exemplaar kan mogelijk meer dan 10 capaciteits eenheden ondersteunen voor verschillende verkeers patronen, afhankelijk van de para meters van de capaciteits eenheid.

Hand matig gedefinieerde schaal en limieten die zijn ingesteld voor automatisch schalen (minimum of maximum) worden ingesteld op basis van het aantal exemplaren. De hand matig ingestelde schaal voor het aantal exemplaren en het minimum aantal instanties in de configuratie voor automatisch schalen, reserveert 10 capaciteits eenheden/exemplaar. Deze gereserveerde CUs wordt gefactureerd zolang de Application Gateway actief is, ongeacht het werkelijke resource verbruik. Als het werkelijke verbruik de limiet van 10 capaciteits eenheden/instanties overschrijdt, worden er extra capaciteits eenheden in rekening gebracht onder de variabele component.

V2 Sku's worden gefactureerd op basis van het verbruik en bestaan uit twee delen:

* Vaste kosten

    Kosten op basis van de tijd waarop de Application Gateway v2/WAF v2 is ingericht en beschikbaar is voor het verwerken van aanvragen. Dit zorgt voor een hoge Beschik baarheid, zelfs als 0 instanties worden gereserveerd door 0 op te geven in het minimum aantal exemplaren als onderdeel van automatisch schalen. 
    
    De vaste kosten omvatten ook de kosten die zijn gekoppeld aan het open bare IP-adres dat aan de Application Gateway is gekoppeld.

    Het aantal exemplaren dat op een bepaald moment wordt uitgevoerd, wordt niet beschouwd als een factor voor vaste kosten voor v2-Sku's. De vaste kosten van het uitvoeren van een Standard_V2 (of WAF_V2) zijn gelijk per uur, ongeacht het aantal exemplaren dat in dezelfde Azure-regio wordt uitgevoerd.

* Kosten van capaciteits eenheden 

    Kosten op basis van het aantal capaciteits eenheden dat is gereserveerd of gebruikt, ook als vereist voor het verwerken van de inkomende aanvragen.  Kosten op basis van verbruik worden per uur berekend.

Totale kosten = vaste kosten + capaciteits eenheid kosten

> [!NOTE]
> Een gedeeltelijk uur wordt in rekening gebracht als een volledig uur.

In de volgende tabel ziet u voor beelden van prijzen op basis van een moment opname van Amerikaanse prijzen voor Nederland en alleen ter illustratie.

#### <a name="fixed-costs-east-us-region-pricing"></a>Vaste kosten (prijzen in de regio VS Oost)

|              V2 SKU             |  Kosten ($/uur)  |
| ------------------------------- | ---------------|
|            Standard_V2          |     $0,246     |
|              WAF_V2             |     $0,443     |

Maandelijkse prijs schattingen zijn gebaseerd op 730 gebruiks uren per maand.

#### <a name="variable-costs-east-us-region-pricing"></a>Variabele kosten (regionale prijzen VS-Oost)

|        Capaciteits eenheid         |  Standard_V2 ($/uur)  |  WAF_V2 ($/uur) |
| ---------------------------- | -------------------- | -------------- |
|             1 CU             |        $0,008        |     $0,0144    |

Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/application-gateway/)voor meer informatie over prijzen volgens uw regio.

> [!NOTE]
> Uitgaande gegevens overdrachten: gegevens die worden verzonden vanuit Azure-data centers van toepassings gateways worden in rekening gebracht tegen standaard [tarieven voor gegevens overdracht](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="example-1-a--manual-scaling"></a>Voor beeld 1 (a) – hand matig schalen 
We gaan ervan uit dat u een Standard_V2 Application Gateway hebt ingericht met hand matig schalen ingesteld op 8 exemplaren voor de hele maand. Gedurende deze periode ontvangt deze een gemiddelde van 88,8-Mbps-gegevens overdracht.

Uw Application Gateway kosten met de hierboven vermelde prijzen worden als volgt berekend:

1 CU kan een doorvoer snelheid van 2,22 Mbps afhandelen.

CUs vereist voor de verwerking van 88,8 Mbps = 88,8/2,22 = 40 CUs 

Voor bereide CUs = 8 (aantal exemplaren) * 10 = 80 

Sinds 80 (gereserveerde capaciteit) > 40 (vereiste capaciteit), is geen extra CUs vereist. 

Vaste prijs = $0,246 * 730 (uur) = $179,58

Variabele kosten = $0,008 * 8 (exemplaar eenheden) * 10 (capaciteits eenheden) * 730 (uur) = $467,2

Totale kosten = $179,58 + $467,2 = $646,78

![Diagram van hand matige schaal 1.](./media/pricing/manual-scale-1.png)

### <a name="example-1-b--manual-scaling-with-traffic-going-beyond-provisioned-capacity"></a>Voor beeld 1 (b): hand matig schalen met verkeer dat groter is dan de ingerichte capaciteit

Stel dat u een Standard_V2 Application Gateway met hand matig schalen hebt ingesteld op 3 instanties voor de hele maand. Gedurende deze periode ontvangt deze een gemiddelde van 88,8-Mbps-gegevens overdracht.

Uw Application Gateway kosten met de hierboven vermelde prijzen worden als volgt berekend:

1 CU kan de door Voer van 2,22 Mbps afhandelen.

CUs vereist voor de verwerking van 88,8 Mbps = 88,8/2,22 = 40 

Voor bereide CUs = 3 (aantal exemplaren) * 10 = 30 

Sinds 40 (vereiste capaciteit) > 30 (gereserveerde capaciteit), is extra CUs vereist.
Het aantal extra CUs-gebruik is afhankelijk van de beschik bare vrije capaciteit bij elk exemplaar.

Als de verwerkings capaciteit gelijk is aan 10 extra CUs beschikbaar is voor gebruik binnen de 3 gereserveerde instanties.

Vaste prijs = $0,246 * 730 (uur) = $179,58

Variabele kosten = $0,008 * (3 (exemplaar eenheden) * 10 (capaciteits eenheden) + 10 (extra capaciteits eenheden)) * 730 (uur) = $233,6

Totale kosten = $179,58 + $233,6 = $413,18

Echter, als de verwerkings capaciteit gelijk is aan slechts 7 extra CUs beschikbaar is voor gebruik binnen de 3 gereserveerde instanties.
In dit scenario wordt de Application Gateway resource geschaald en kan dit leiden tot een toename van de latentie of aanvragen die worden verwijderd.

Vaste prijs = $0,246 * 730 (uur) = $179,58

Variabele kosten = $0,008 * (3 (exemplaar eenheden) * 10 (capaciteits eenheden) + 7 (extra capaciteits eenheden)) * 730 (uur) = $216,08

Totale kosten = $179,58 + $216,08 = $395,66


![Diagram van hand matig schalen 2.](./media/pricing/manual-scale-2.png)

> [!NOTE]
> Bij hand matig schalen kunnen extra aanvragen die de maximale verwerkings capaciteit van de gereserveerde instanties overschrijden, invloed hebben op de beschik baarheid van uw toepassing. In situaties met een hoge belasting kunnen gereserveerde instanties meer dan 10 capaciteits eenheden van de verwerkings capaciteit bieden, afhankelijk van de configuratie en het type van binnenkomende aanvragen. Het is echter raadzaam het aantal instanties in te richten volgens uw verkeers vereisten.

### <a name="example-2--waf_v2-instance-with-autoscaling"></a>Voor beeld 2: WAF_V2-instantie met automatisch schalen

Stel dat u een WAF_V2 hebt ingericht waarvoor automatisch schalen is ingeschakeld en stel het minimum aantal exemplaren in op 6 voor de hele maand. De aanvraag belasting heeft ervoor gezorgd dat het WAF-exemplaar uitschaalt en gebruikmaakt van 65-capaciteits eenheden (uitschalen van 5 capaciteits eenheden, terwijl 60 eenheden zijn gereserveerd) voor de hele maand.
Uw Application Gateway kosten met de hierboven vermelde prijzen worden als volgt berekend:

Maandelijkse prijs schattingen zijn gebaseerd op 730 gebruiks uren per maand.

Vaste prijs = $0,443 * 730 (uur) = $323,39

Variabele kosten = $0,0144 * 65 (capaciteits eenheden) * 730 (uur) = $683,28

Totale kosten = $323,39 + $683,28 = $1006,67

![Diagram van automatisch schalen 2.](./media/pricing/auto-scale-1.png)

> [!NOTE]
> Het daad werkelijke verkeer dat is waargenomen voor uw Application Gateway is waarschijnlijk geen constant patroon van verkeer en de waargenomen belasting van uw Application Gateway zou schommelen op basis van het werkelijke gebruik.

### <a name="example-3-a--waf_v2-instance-with-autoscaling-and-0-min-scale-config"></a>Voor beeld 3 (a): WAF_V2-instantie met automatisch schalen en 0 min schaal configuratie

Stel dat u een WAF_V2 hebt ingericht waarvoor automatisch schalen is ingeschakeld en stel het minimum aantal exemplaren in op 0 voor de hele maand. De belasting van de aanvraag voor de WAF is mini maal, maar is voor de hele maand voortdurend aanwezig per uur. De belasting is lager dan de capaciteit van één capaciteits eenheid.
Uw Application Gateway kosten met de hierboven vermelde prijzen worden als volgt berekend:

Maandelijkse prijs schattingen zijn gebaseerd op 730 gebruiks uren per maand.

Vaste prijs = $0,443 * 730 (uur) = $323,39

Variabele kosten = $0,0144 * 1 (capaciteits eenheden) * 730 (uur) = $10,512

Totale kosten = $323,39 + $10,512 = $333,902

### <a name="example-3-b--waf_v2-instance-with-autoscaling-with-0-min-instance-count"></a>Voor beeld 3 (b): WAF_V2-instantie met automatisch schalen met 0 min aantal exemplaren

Stel dat u een WAF_V2 hebt ingericht waarvoor automatisch schalen is ingeschakeld en stel het minimum aantal exemplaren in op 0 voor de hele maand. Er is echter 0 verkeer dat is bestemd voor het WAF-exemplaar voor de hele maand.
Uw Application Gateway kosten met de hierboven vermelde prijzen worden als volgt berekend:

Vaste prijs = $0,443 * 730 (uur) = $323,39

Variabele kosten = $0,0144 * 0 (capaciteits eenheden) * 730 (uur) = $0

Totale kosten = $323,39 + $0 = $323,39

### <a name="example-3-c--waf_v2-instance-with-manual-scaling-set-to-1-instance"></a>Voor beeld 3 (C) – WAF_V2 instantie waarvoor hand matig schalen is ingesteld op 1 instantie

Stel dat u een WAF_V2 hebt ingericht en ingesteld op hand matig schalen met de mini maal toegestane waarde van 1 exemplaar voor de hele maand. Er is echter 0 verkeer dat is bestemd voor de WAF voor de hele maand.
Uw Application Gateway kosten met de hierboven vermelde prijzen worden als volgt berekend:

Maandelijkse prijs schattingen zijn gebaseerd op 730 gebruiks uren per maand.

Vaste prijs = $0,443 * 730 (uur) = $323,39

Variabele kosten = $0,0144 * 1 (aantal exemplaren) * 10 (capaciteits eenheden) * 730 (uur) = $105,12

Totale kosten = $323,39 + $105,12 = $428,51

### <a name="example-4--waf_v2-with-autoscaling-capacity-unit-calculations"></a>Voor beeld 4: WAF_V2 met automatisch schalen, berekening van capaciteits eenheden

Stel dat u een WAF_V2 hebt ingericht waarvoor automatisch schalen is ingeschakeld en stel het minimum aantal exemplaren in op 0 voor de hele maand. Gedurende deze periode worden er 25 nieuwe TLS-verbindingen per seconde ontvangen met een gemiddelde van 8,88-Mbps-gegevens overdracht.
Uw Application Gateway kosten met de hierboven vermelde prijzen worden als volgt berekend:

Maandelijkse prijs schattingen zijn gebaseerd op 730 gebruiks uren per maand.

Vaste prijs = $0,443 * 730 (uur) = $323,39

Variabele kosten = $0,0144 * 730 (uur) * {Max (25/50, 8.88/2.22)} = $23,36 (4 capaciteits eenheden vereist voor het verwerken van 8,88 Mbps)

Totale kosten = $323,39 + $23,36 = $346,75

### <a name="example-5-a--standard_v2-with-autoscaling-time-based-calculations"></a>Voor beeld 5 (a): Standard_V2 met automatisch schalen, berekeningen op basis van tijd

Stel dat u een standard_V2 hebt ingericht waarvoor automatisch schalen is ingeschakeld en stel het minimum aantal exemplaren in op 0 en dat deze toepassings Gateway gedurende 2 uur actief is.
Gedurende het eerste uur ontvangt het verkeer dat kan worden verwerkt door 10 capaciteits eenheden en tijdens het tweede uur verkeer dat nodig is voor 20 capaciteits eenheden om de belasting te verwerken.
Uw Application Gateway kosten met de hierboven vermelde prijzen worden als volgt berekend:

Vaste prijs = $0,246 * 2 (uur) = $0,492

Variabele kosten = $0,008 * 10 (capaciteits eenheden) * 1 (uur) + $0,008 * 20 (capaciteits eenheden) * 1 (uur) = $0,24

Totale kosten = $0,492 + $0,24 = $0,732


## <a name="monitoring-billed-usage"></a>Gefactureerd gebruik controleren

U kunt de hoeveelheid verbruik voor verschillende para meters (reken eenheid, door Voer & permanente verbindingen) bekijken, evenals de capaciteits eenheden die worden gebruikt als onderdeel van de Application Gateway metrieken in het gedeelte **bewaking** .

![De sectie diagram van metrische gegevens.](./media/pricing/metrics-1.png)

### <a name="useful-metrics-for-cost-estimation"></a>Nuttige metrische gegevens voor de kosten raming

* Huidige capaciteitseenheden

    Aantal verbruikte capaciteits eenheden voor taak verdeling van het verkeer over de drie para meters: huidige verbindingen, door Voer en reken eenheid

* Vaste factureerbare capaciteitseenheden

    Het minimale aantal capaciteits eenheden dat is ingericht volgens de instelling voor het minimum aantal instanties (één exemplaar wordt omgezet in eenheden van 10 capaciteit) in de Application Gateway configuratie.

* Geschatte gefactureerde capaciteitseenheden

    Geschatte gefactureerde capaciteitseenheden staat voor het aantal capaciteitseenheden dat naar schatting wordt gefactureerd. Dit wordt berekend als het verschil tussen Huidige capaciteitseenheden (capaciteitseenheden die nodig zijn om de verkeerstaken te verdelen) en Vaste factureerbare capaciteitseenheden (minimaal aantal capaciteitseenheden dat wordt ingericht).

Meer metrische gegevens, zoals door Voer, huidige verbindingen en reken eenheden, zijn ook beschikbaar om knel punten te begrijpen en het aantal vereiste capaciteits eenheden te schatten. Gedetailleerde informatie is beschikbaar op [Application Gateway metrische](application-gateway-metrics.md) gegevens

#### <a name="example---estimating-capacity-units-being-utilized"></a>Voor beeld: capaciteits eenheden schatten die worden gebruikt

**Geobserveerde metrische gegevens:**

* Reken eenheden = 17,38
* Door Voer = 1.37 M bytes per seconde-10,96 Mbps
* Huidige verbindingen = 123.08 k
* Berekende capaciteits eenheden = Max (17.38, 10.96/2.22, 123.08 k/2500) = 49,232

Gemeten capaciteits eenheden in metrische gegevens = 49,23

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over de werking van prijzen in Azure-toepassing gateway:

* [Pagina met prijzen van Azure-toepassing gateway](https://azure.microsoft.com/pricing/details/application-gateway/)
* [Prijs calculator voor Azure-toepassing gateway](https://azure.microsoft.com/en-us/pricing/calculator/?service=application-gateway)

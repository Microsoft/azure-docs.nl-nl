---
title: Ondersteuning voor Application Gateway High Traffic volume
description: Dit artikel bevat richt lijnen voor het configureren van Azure-toepassing gateway ter ondersteuning van volume scenario's met een hoog netwerk verkeer.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: d8940d791920daca6ef0af186a4bb5e17009637b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100586121"
---
# <a name="application-gateway-high-traffic-support"></a>Ondersteuning voor intensief verkeer in Application Gateway

>[!NOTE]
> In dit artikel worden enkele aanbevolen richt lijnen beschreven om u te helpen bij het instellen van uw Application Gateway voor het afhandelen van extra verkeer voor elk groot verkeers volume dat kan optreden. De drempel waarden voor waarschuwingen zijn louter suggesties en algemene aard. Gebruikers kunnen waarschuwings drempels bepalen op basis van hun workload-en gebruiks verwachtingen.

U kunt Application Gateway met Web Application firewall (WAF) gebruiken voor een schaal bare en veilige manier om verkeer naar uw webtoepassingen te beheren.

Het is belang rijk dat u uw Application Gateway schaalt op basis van uw verkeer en met een bit van een buffer, zodat u voor bereid bent op verkeer pieken of pieken en de invloed die het kan hebben op uw QoS, minimaliseert. De volgende suggesties helpen u bij het instellen van Application Gateway met WAF voor het verwerken van extra verkeer.

Raadpleeg de [documentatie over metrische gegevens](./application-gateway-metrics.md) voor de volledige lijst met metrische gegevens die worden aangeboden door Application Gateway. Zie [metrische gegevens visualiseren](./application-gateway-metrics.md#metrics-visualization) in de Azure Portal en de [documentatie van Azure monitor](../azure-monitor/alerts/alerts-metric.md) voor het instellen van waarschuwingen voor metrische gegevens.

## <a name="scaling-for-application-gateway-v1-sku-standardwaf-sku"></a>Schalen voor Application Gateway v1 SKU (Standard/WAF SKU)

### <a name="set-your-instance-count-based-on-your-peak-cpu-usage"></a>Stel het aantal exemplaren in op basis van het CPU-gebruik van de piek
Als u een v1 SKU-gateway gebruikt, hebt u de mogelijkheid om uw Application Gateway tot 32 exemplaren in te stellen voor schalen. Controleer het CPU-gebruik van uw Application Gateway in de afgelopen maand voor pieken van meer dan 80%. het is een meet waarde die u kunt bewaken. Het is raadzaam om het aantal instanties in te stellen op basis van het piek gebruik en met een extra buffer van 10% tot 20%, zodat er geen verkeer kan worden verwerkt.

:::image type="content" source="./media/application-gateway-covid-guidelines/v1-cpu-utilization-inline.png" alt-text="Metrische gegevens over het CPU-gebruik van v1" lightbox="./media/application-gateway-covid-guidelines/v1-cpu-utilization-exp.png":::

### <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>Gebruik v2 SKU over v1 voor de mogelijkheden voor automatisch schalen en prestaties
De v2-SKU biedt automatisch schalen om ervoor te zorgen dat uw Application Gateway kan worden geschaald naarmate het verkeer toeneemt. Het biedt ook andere aanzienlijke prestatie voordelen, zoals 5x betere TLS-offload-prestaties, snellere implementatie-en update tijden, zone redundantie en meer in vergelijking met v1. Zie onze [v2-documentatie](./application-gateway-autoscaling-zone-redundant.md) voor meer informatie en Raadpleeg de [migratie documentatie](./migrate-v1-v2.md) voor v1 tot v2 voor meer informatie over het migreren van uw bestaande v1 SKU-gateways naar v2 SKU. 

## <a name="autoscaling-for-application-gateway-v2-sku-standard_v2waf_v2-sku"></a>Automatisch schalen voor Application Gateway v2 SKU (Standard_v2/WAF_v2 SKU)

### <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>Stel het maximum aantal instanties in op het Maxi maal mogelijk (125)
 
Voor de SKU van Application Gateway v2 kunt u het maximum aantal exemplaren instellen op de Maxi maal mogelijke waarde van 125, zodat de Application Gateway naar behoefte kan worden uitgebreid. Zo kunt u de mogelijke toename van het verkeer naar uw toepassingen afhandelen. Er worden alleen kosten in rekening gebracht voor de capaciteits eenheden (CUs) die u gebruikt. 

Controleer de grootte van het subnet en het beschik bare IP-adres in uw subnet en stel uw maximum aantal exemplaren in op basis van dat. Als er onvoldoende ruimte beschikbaar is op uw subnet, moet u de gateway opnieuw maken in hetzelfde subnet of een ander subnetwerk dat voldoende capaciteit heeft. 

:::image type="content" source="./media/application-gateway-covid-guidelines/v2-autoscaling-max-instances-inline.png" alt-text="V2-configuratie voor automatisch schalen" lightbox="./media/application-gateway-covid-guidelines/v2-autoscaling-max-instances-exp.png":::

### <a name="set-your-minimum-instance-count-based-on-your-average-compute-unit-usage"></a>Stel het minimum aantal exemplaren in op basis van uw gemiddelde Compute unit-gebruik

Voor de SKU van Application Gateway v2 neemt automatisch schalen zes tot zeven minuten in beslag en het inrichten van een extra set exemplaren die klaar zijn om verkeer te nemen. Als er vervolgens korte pieken in het verkeer zijn, kunnen uw bestaande gateway-exemplaren onder spanning raken. Dit kan leiden tot onverwachte latentie of verlies van verkeer. 

Het is raadzaam om het minimum aantal exemplaren in te stellen op een optimaal niveau. Als u bijvoorbeeld 50 exemplaren nodig hebt voor het afhandelen van het verkeer bij piek belasting, is het instellen van het minimum aantal van 25 tot 30 een goed idee in plaats van op <10, zodat zelfs wanneer er sprake is van korte bursts van verkeer, Application Gateway zou kunnen afhandelen en voldoende tijd moeten bieden om automatisch te kunnen worden geschaald en van kracht worden.

Controleer de metriek van de reken eenheid voor de afgelopen maand. Metrische reken eenheid is een weer gave van het CPU-gebruik van uw gateway en op basis van het piek gebruik gedeeld door 10, kunt u het minimum aantal exemplaren instellen dat vereist is. Houd er rekening mee dat 1 toepassings gateway-exemplaar mini maal 10 reken eenheden kan verwerken

:::image type="content" source="./media/application-gateway-covid-guidelines/compute-unit-metrics-inline.png" alt-text="V2 metrische reken eenheden" lightbox="./media/application-gateway-covid-guidelines/compute-unit-metrics-exp.png":::

## <a name="manual-scaling-for-application-gateway-v2-sku-standard_v2waf_v2"></a>Hand matig schalen voor Application Gateway v2 SKU (Standard_v2/WAF_v2)

### <a name="set-your-instance-count-based-on-your-peak-compute-unit-usage"></a>Stel het aantal exemplaren in op basis van het gebruik van de piek reken eenheid 

In tegens telling tot automatisch schalen, moet u het aantal exemplaren van de toepassings gateway hand matig instellen op basis van de verkeers vereisten. Het is raadzaam om het aantal instanties in te stellen op basis van het piek gebruik en met een extra buffer van 10% tot 20%, zodat er geen verkeer kan worden verwerkt. Als uw verkeer bijvoorbeeld 50 exemplaren op piek vereist, moet u 55 tot 60 exemplaren inrichten voor het afhandelen van onverwachte verkeers pieken die zich kunnen voordoen.

Controleer de metriek van de reken eenheid voor de afgelopen maand. Metrische reken eenheid is een representatie van het CPU-gebruik van uw gateway en op basis van het piek gebruik gedeeld door 10, kunt u het aantal vereiste exemplaren instellen, omdat 1 toepassings gateway-exemplaar mini maal 10 reken eenheden kan verwerken

## <a name="monitoring-and-alerting"></a>Bewaking en waarschuwingen

Als u een melding wilt ontvangen over mogelijke afwijkingen of afwijkingen van het gebruik, kunt u waarschuwingen instellen voor bepaalde metrische gegevens. Raadpleeg de [documentatie over metrische gegevens](./application-gateway-metrics.md) voor de volledige lijst met metrische gegevens die worden aangeboden door Application Gateway. Zie [metrische gegevens visualiseren](./application-gateway-metrics.md#metrics-visualization) in de Azure Portal en de [documentatie van Azure monitor](../azure-monitor/alerts/alerts-metric.md) voor het instellen van waarschuwingen voor metrische gegevens.

## <a name="alerts-for-application-gateway-v1-sku-standardwaf"></a>Waarschuwingen voor de Application Gateway v1-SKU (Standard/WAF)

### <a name="alert-if-average-cpu-utilization-crosses-80"></a>Waarschuwen als gemiddeld CPU-gebruik de 80% overschrijdt

Onder normale omstandigheden mag het CPU-gebruik niet al te vaak 90% overschrijden, omdat dit latentie kan veroorzaken in de websites die achter de Application Gateway worden gehost en omdat dit de clientervaring kan verstoren. U kunt het CPU-gebruik indirect beheren of verbeteren door de configuratie van de Application Gateway te wijzigen door het aantal exemplaren te verhogen of door over te stappen op een grotere SKU-grootte of beide te verplaatsen. Stel een waarschuwing in als de metrische gegevens van het CPU-gebruik hoger zijn dan 80% Average.

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>Waarschuwen als de drempel waarde voor het aantal hosts is overschreden

Met deze metrische waarde wordt het aantal back-endservers aangegeven dat Application Gateway niet kan testen. Hiermee worden problemen onderschept waarbij Application Gateway-exemplaren geen verbinding kunnen maken met de back-end. Waarschuwen als dit aantal meer dan 20% van de back-upcapaciteit overschrijdt. Bijvoorbeeld Als u op dit moment 30 back-endservers hebt in de back-endadresgroep, stelt u een waarschuwing in als het aantal beschadigde hosts groter dan 6 is.

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>Waarschuwing als de reactie status (4xx, 5xx) de drempel waarde overschrijdt 

Maak een waarschuwing wanneer de reactie status van Application Gateway 4xx of 5xx is. Er kan af en toe 4xx of 5xx reacties worden gezien door tijdelijke problemen. U moet de gateway in productie observeren om een statische drempel te bepalen of om een dynamische drempel waarde voor de waarschuwing te gebruiken.

### <a name="alert-if-failed-requests-crosses-threshold"></a>Waarschuwen als de drempel waarde voor mislukte aanvragen wordt overschreden 

Maak een waarschuwing wanneer de drempel waarde voor het aantal mislukte aanvragen wordt overschreden. U moet de gateway in productie observeren om een statische drempel te bepalen of om een dynamische drempel waarde voor de waarschuwing te gebruiken.

### <a name="example-setting-up-an-alert-for-more-than-100-failed-requests-in-the-last-5-minutes"></a>Voor beeld: instellen van een waarschuwing voor meer dan 100 mislukte aanvragen in de afgelopen vijf minuten

Dit voor beeld laat zien hoe u de Azure Portal kunt gebruiken om een waarschuwing in te stellen wanneer het aantal mislukte aanvragen in de afgelopen 5 minuten meer is dan 100.
1. Navigeer naar uw **Application Gateway**.
2. Selecteer in het linkerdeel venster **metrische gegevens** op het tabblad **bewaking** . 
3. Een metrische waarde voor **mislukte aanvragen** toevoegen.
4. Klik op **nieuwe waarschuwings regel** en Definieer uw voor waarde en acties
5. Klik op **waarschuwings regel maken** om de waarschuwing te maken en in te scha kelen

:::image type="content" source="./media/application-gateway-covid-guidelines/create-alerts-inline.png" alt-text="V2 waarschuwingen maken" lightbox="./media/application-gateway-covid-guidelines/create-alerts-exp.png":::

## <a name="alerts-for-application-gateway-v2-sku-standard_v2waf_v2"></a>Waarschuwingen voor de SKU van Application Gateway v2 (Standard_v2/WAF_v2)

### <a name="alert-if-compute-unit-utilization-crosses-75-of-average-usage"></a>Waarschuwen als het gebruik van de reken eenheid met 75% van het gemiddelde gebruik bijsnijdt 

Reken eenheid is de meting van het reken gebruik van uw Application Gateway. Controleer het gebruik van de gemiddelde Compute-eenheid in de afgelopen maand en stel een waarschuwing in als deze 75% van de tijd overschrijdt. Als uw gemiddelde gebruik bijvoorbeeld 10 reken eenheden is, stelt u een waarschuwing in op 7,5. Hiermee wordt u gewaarschuwd als het gebruik toeneemt en u tijd hebt om te reageren. U kunt het minimum verhogen als u denkt dat dit verkeer wordt ondervangen om u te waarschuwen dat verkeer kan toenemen. Volg de bovenstaande suggesties voor schalen om zo nodig uit te schalen.

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>Voor beeld: een waarschuwing instellen voor 75% van het gemiddelde CU-gebruik

In dit voor beeld ziet u hoe u de Azure Portal kunt gebruiken om een waarschuwing in te stellen wanneer 75% van het gemiddelde van CU-gebruik is bereikt. 
1. Navigeer naar uw **Application Gateway**.
2. Selecteer in het linkerdeel venster **metrische gegevens** op het tabblad **bewaking** . 
3. Een metriek toevoegen voor **gemiddelde huidige reken eenheden**. 
4. Als u uw minimum aantal instanties hebt ingesteld op uw gemiddelde CU-gebruik, kunt u een waarschuwing instellen wanneer 75% van de minimum instanties in gebruik zijn. Als uw gemiddelde gebruik bijvoorbeeld 10 CUs is, stelt u een waarschuwing in op 7,5. Hiermee wordt u gewaarschuwd als het gebruik toeneemt en u tijd hebt om te reageren. U kunt het minimum verhogen als u denkt dat dit verkeer wordt ondervangen om u te waarschuwen dat verkeer kan toenemen. 

:::image type="content" source="./media/application-gateway-covid-guidelines/compute-unit-alert-inline.png" alt-text="V2-waarschuwingen voor Compute-eenheden" lightbox="./media/application-gateway-covid-guidelines/compute-unit-alert-exp.png":::

> [!NOTE]
> U kunt instellen dat de waarschuwing wordt uitgevoerd bij een lager of hoger CU-gebruiks percentage, afhankelijk van hoe gevoelig u wilt zijn voor mogelijke verkeers pieken.

### <a name="alert-if-capacity-unit-utilization-crosses-75-of-peak-usage"></a>Waarschuwen als het gebruik van de capaciteits eenheid 75% van het piek gebruik overschrijdt 

Capaciteits eenheden vertegenwoordigen het totale gateway gebruik in termen van door Voer, berekening en aantal verbindingen. Controleer het gebruik van de maximale capaciteits eenheid in de afgelopen maand en stel een waarschuwing in als er 75% van wordt gepasseerd. Als uw maximum gebruik bijvoorbeeld 100 capaciteits eenheden is, stelt u een waarschuwing in op 75 CUs. Volg de bovenstaande twee suggesties om uit te breiden, indien nodig.

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>Waarschuwen als de drempel waarde voor het aantal hosts is overschreden 

Met deze metrische waarde wordt het aantal back-endservers aangegeven dat Application Gateway niet kan testen. Hiermee worden problemen onderschept waarbij Application Gateway-exemplaren geen verbinding kunnen maken met de back-end. Waarschuwen als dit aantal meer dan 20% van de back-upcapaciteit overschrijdt. Bijvoorbeeld Als u op dit moment 30 back-endservers hebt in de back-endadresgroep, stelt u een waarschuwing in als het aantal beschadigde hosts groter dan 6 is.

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>Waarschuwing als de reactie status (4xx, 5xx) de drempel waarde overschrijdt 

Maak een waarschuwing wanneer de reactie status van Application Gateway 4xx of 5xx is. Er kan af en toe 4xx of 5xx reacties worden gezien door tijdelijke problemen. U moet de gateway in productie observeren om een statische drempel te bepalen of om een dynamische drempel waarde voor de waarschuwing te gebruiken.

### <a name="alert-if-failed-requests-crosses-threshold"></a>Waarschuwen als de drempel waarde voor mislukte aanvragen wordt overschreden 

Maak een waarschuwing wanneer de drempel waarde voor het aantal mislukte aanvragen wordt overschreden. U moet de gateway in productie observeren om een statische drempel te bepalen of om een dynamische drempel waarde voor de waarschuwing te gebruiken.

### <a name="alert-if-backend-last-byte-response-time-crosses-threshold"></a>Waarschuwen als laatste byte reactie tijd van de back-end-overschrijdt 

Met deze metriek wordt het tijds interval aangegeven tussen het begin van het tot stand brengen van een verbinding met de back-endserver en het ontvangen van de laatste byte van de antwoord tekst. Maak een waarschuwing als de antwoord latentie van de back-end meer heeft dan de gebruikelijke drempel waarde. Stel dit in om te worden gewaarschuwd wanneer de reactie tijd van de back-end met meer dan 30% van de gebruikelijke waarde wordt verhoogd.

### <a name="alert-if-application-gateway-total-time-crosses-threshold"></a>Waarschuwen als Application Gateway de drempel waarde overschrijdt

Dit is het interval vanaf het moment dat Application Gateway de eerste byte van de HTTP-aanvraag ontvangt naar het tijdstip waarop de laatste reactie byte is verzonden naar de client. Moet een waarschuwing maken als de reactie latentie van de back-end meer heeft dan de gebruikelijke drempel waarde. Ze kunnen bijvoorbeeld instellen dat ze worden gewaarschuwd wanneer de totale tijds duur van meer dan 30% van de gebruikelijke waarde wordt verhoogd.

## <a name="set-up-waf-with-geo-filtering-and-bot-protection-to-stop-attacks"></a>WAF met geo-filtering en bot-beveiliging instellen om aanvallen te stoppen
Als u voor uw toepassing een extra beveiligingslaag wilt, gebruikt u de Application Gateway WAF_v2 SKU voor WAF-mogelijkheden. U kunt de v2-SKU zo configureren dat alleen toegang tot uw toepassingen vanuit een bepaald land/regio of landen/regio's is toegestaan. U stelt een aangepaste regel voor WAF in om verkeer expliciet toe te staan of te blok keren op basis van de geografische locatie. Zie voor meer informatie [aangepaste regels voor geo-filtering](../web-application-firewall/ag/geomatch-custom-rules.md) en [aangepaste regels configureren op Application Gateway WAF_v2 SKU via Power shell](../web-application-firewall/ag/configure-waf-custom-rules.md).

Schakel bot-beveiliging in om bekende beschadigde bots te blok keren. Dit verlaagt de hoeveelheid verkeer die wordt ontvangen naar uw toepassing. Zie [bot Protection with set up instructies](../web-application-firewall/ag/configure-waf-custom-rules.md)(Engelstalig) voor meer informatie.

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Diagnostische gegevens inschakelen op Application Gateway en WAF

Met Diagnostische logboeken kunt u Firewall logboeken, prestatie logboeken en toegangs logboeken weer geven. U kunt deze logboeken in azure gebruiken om toepassings gateways te beheren en op te lossen. Zie de [Diagnostische documentatie](./application-gateway-diagnostics.md#diagnostic-logging)voor meer informatie. 

## <a name="set-up-an-tls-policy-for-extra-security"></a>Een TLS-beleid instellen voor extra beveiliging
Zorg ervoor dat u de meest recente TLS-beleids versie ([AppGwSslPolicy20170401S](./application-gateway-ssl-policy-overview.md#appgwsslpolicy20170401s)) gebruikt. Hiermee worden TLS 1,2 en sterkere cijfers afgedwongen. Zie [TLS-beleids versies en coderings suites configureren via Power shell](./application-gateway-configure-ssl-policy-powershell.md)voor meer informatie.
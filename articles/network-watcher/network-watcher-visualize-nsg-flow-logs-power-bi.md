---
title: Azure NSG-stroom logboeken visualiseren-Power BI
titleSuffix: Azure Network Watcher
description: Informatie over het gebruik van Power BI om stroom logboeken van netwerk beveiligings groepen te visualiseren zodat u informatie kunt bekijken over IP-verkeer in azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 50f08ffc304962cc66f6eed07adcb5b7e4aa6692
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98011149"
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>Stroom logboeken van netwerk beveiligings groepen visualiseren met Power BI

Met stroom logboeken van de netwerk beveiligings groep kunt u informatie weer geven over binnenkomend en IP-verkeer op netwerk beveiligings groepen. In deze stroom logboeken worden uitgaande en inkomende stromen per regel weer gegeven, de NIC waarop de stroom van toepassing is, 5-tuple informatie over de stroom (bron/doel-IP, bron/doel poort, Protocol) en als het verkeer is toegestaan of geweigerd.

Het kan lastig zijn om inzicht te krijgen in gegevens in de stroom logboek registratie door de logboek bestanden hand matig te doorzoeken. In dit artikel bieden we een oplossing voor het visualiseren van uw meest recente stroom logboeken en meer informatie over verkeer in uw netwerk.

> [!Warning]  
> De volgende stappen werken met stroom logboeken versie 1. Zie [Inleiding tot flow-logboek registratie voor netwerk beveiligings groepen](network-watcher-nsg-flow-logging-overview.md)voor meer informatie. De volgende instructies werken niet met versie 2 van de logboek bestanden, zonder aanpassing.

## <a name="scenario"></a>Scenario

In het volgende scenario verbinden we Power BI bureau blad met het opslag account dat we hebben geconfigureerd als de Sink voor de logboek gegevens van de NSG-stroom. Nadat u verbinding hebt gemaakt met ons opslag account, Power BI gedownload en worden de logboeken geparseerd om een visuele weer gave te bieden van het verkeer dat wordt vastgelegd door netwerk beveiligings groepen.

Met behulp van de visuals die zijn opgegeven in de sjabloon, kunt u het volgende bekijken:

* Belangrijkste Talks
* Time Series-gegevens per richting en regel beslissing
* Stromen per netwerk interface MAC-adres
* Stromen op NSG en regel
* Stromen op doel poort

De aangevraagde sjabloon is bewerkbaar zodat u deze kunt wijzigen om nieuwe gegevens, visuele elementen of bewerk query's toe te voegen aan uw behoeften.

## <a name="setup"></a>Instellen

Voordat u begint, moet u de stroom logboek registratie van de netwerk beveiligings groep inschakelen voor een of meer netwerk beveiligings groepen in uw account. Raadpleeg het volgende artikel: [Inleiding tot stroom logboek registratie voor netwerk beveiligings groepen](network-watcher-nsg-flow-logging-overview.md)voor instructies over het inschakelen van Logboeken voor netwerk beveiligings stromen.

U moet ook de Power BI Desktop-Client op uw computer hebben geïnstalleerd en voldoende beschik bare ruimte op uw computer hebben om de logboek gegevens in uw opslag account te downloaden en te laden.

![Visio-diagram][1]

### <a name="steps"></a>Stappen

1. Down load en open de volgende Power BI sjabloon in de sjabloon Power BI Desktop toepassing [Network Watcher PowerBI flow-logboeken](https://aka.ms/networkwatcherpowerbiflowlogstemplate)
1. Voer de vereiste query parameters in
   1. **StorageAccountName** : Hiermee geeft u de naam op van het opslag account met de NSG-stroom logboeken die u wilt laden en visualiseren.
   1. **NumberOfLogFiles** : Hiermee geeft u het aantal logboek bestanden op dat u wilt downloaden en visualiseren in Power bi. Bijvoorbeeld, als 50 is opgegeven, de laatste logboek bestanden van 50. Als we 2 Nsg's hebben ingeschakeld en geconfigureerd voor het verzenden van NSG-stroom logboeken naar dit account, kunnen de afgelopen 25 uur aan logboeken worden weer gegeven.

      ![Power BI-hoofd][2]

1. Voer de toegangs sleutel voor uw opslag account in. U kunt geldige toegangs toetsen vinden door te navigeren naar uw opslag account in de Azure Portal en **toegangs sleutels** te selecteren in het menu instellingen. Klik op **verbinden** en vervolgens op wijzigingen Toep assen.

    ![toegangs sleutels][3]

    ![toegangs sleutel 2][4]

4. Uw logboeken worden gedownload en geparseerd en u kunt nu de vooraf gemaakte visuals gebruiken.

## <a name="understanding-the-visuals"></a>Informatie over de visuele elementen

Die in de sjabloon is opgegeven, zijn een aantal visuele elementen waarmee u de gegevens van het NSG-stroom logboek kunt begrijpen. De volgende afbeeldingen tonen een voor beeld van hoe het dash board eruit ziet wanneer het is gevuld met gegevens. Hieronder worden alle visuele elementen in detail besproken. 

![powerbi][5]
 
In het visuele element toppraten ziet u de IP-adressen die de meeste verbindingen hebben geïnitieerd tijdens de opgegeven periode. De grootte van de vakken komt overeen met het relatieve aantal verbindingen. 

![toptalkers][6]

In de volgende grafieken van de tijd reeks wordt het aantal stromen voor de periode weer gegeven. De bovenste grafiek wordt gesegmenteerd door de richting van de stroom en de laagste wordt gesegmenteerd door de beslissing die is gemaakt (toestaan of weigeren). Met deze Visual kunt u uw verkeers trends in de loop van de tijd onderzoeken en eventuele abnormale pieken of afname in verkeer of verkeers segmentering afwijzen.

![flowsoverperiod][7]

In de volgende grafieken ziet u de stromen per netwerk interface, met de bovenste gesegmenteerd per stroom richting en de lagere gesegmenteerd door besluit. Met deze informatie kunt u inzicht krijgen in welke van uw Vm's het meest ten opzichte van anderen hebben gecommuniceerd, en als verkeer naar een specifieke virtuele machine wordt toegestaan of geweigerd.

![flowspernic][8]

De volgende ring-wiel diagram toont een uitsplitsing van stromen per doel poort. Met deze informatie kunt u de meestgebruikte doel poorten weer geven die binnen de opgegeven periode worden gebruikt.

![donut][9]

In het volgende staaf diagram wordt de stroom op NSG en regel weer gegeven. Met deze informatie kunt u de Nsg's zien die verantwoordelijk is voor het grootste verkeer en de uitsplitsing van verkeer op een NSG per regel.

![barchart][10]
 
De volgende informatie diagrammen bevatten informatie over de Nsg's die aanwezig zijn in de logboeken, het aantal stromen dat is vastgelegd in de periode en de datum van de eerste vastgelegde logboek registratie. Deze informatie geeft u een idee van wat Nsg's worden geregistreerd en het datum bereik van stromen.

![infochart1][11]

![infochart2][12]

Deze sjabloon bevat de volgende Slicers waarmee u alleen de gegevens kunt bekijken die u het belangrijkst vindt. U kunt filteren op uw resource groepen, Nsg's en regels. U kunt ook filteren op 5-tuple-informatie, beslissing en het tijdstip waarop het logboek is geschreven.

![Slicers][13]

## <a name="conclusion"></a>Conclusie

We hebben in dit scenario geleerd dat met behulp van stroom logboeken voor netwerk beveiligings groepen die zijn verschaft door Network Watcher en Power BI, het verkeer kan worden gevisualiseerd en begrepen. Met behulp van de meegeleverde sjabloon Power BI worden de logboeken rechtstreeks vanuit Storage gedownload en lokaal verwerkt. De tijd die nodig is om de sjabloon te laden, is afhankelijk van het aantal aangevraagde bestanden en de totale grootte van de bestanden die worden gedownload.

U kunt deze sjabloon gratis aanpassen aan uw behoeften. Er zijn veel verschillende manieren waarop u Power BI kunt gebruiken met stroom logboeken voor netwerk beveiligings groepen. 

## <a name="notes"></a>Notities

* Logboeken worden standaard opgeslagen in `https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/`

    * Als er andere gegevens in een andere Directory aanwezig zijn, moeten de query's die worden opgehaald en verwerkt de gegevens worden gewijzigd.

* De meegeleverde sjabloon wordt niet aanbevolen voor gebruik met meer dan 1 GB aan logboeken.

* Als u een groot aantal Logboeken hebt, raden we u aan om een oplossing te onderzoeken met behulp van een ander gegevens archief, zoals Data Lake of SQL Server.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het visualiseren van uw NSG-stroom logboeken met de elastische stack door te bezoeken [Azure Network WATCHER NSG-stroom logboeken met behulp van open-source-hulpprogram ma's](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

[1]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure7.png
[8]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure8.png
[9]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure9.png
[10]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure10.png
[11]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure11.png
[12]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure12.png
[13]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure13.png

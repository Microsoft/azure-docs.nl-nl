---
title: Meerdere tijd reeksen bewaken in één metrische waarschuwings regel
description: Waarschuwing op schaal met één waarschuwings regel voor meerdere tijd reeksen
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: bc3e77b560a94b45acf1ae696551dbefbabbdf0a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102038200"
---
# <a name="monitor-multiple-time-series-in-a-single-metric-alert-rule"></a>Meerdere tijd reeksen bewaken in één metrische waarschuwings regel

Eén metrische waarschuwings regel kan worden gebruikt voor het bewaken van een of meer metrische tijd reeksen, waardoor het eenvoudiger is om resources op schaal te controleren.

## <a name="metric-time-series"></a>Metrische tijd-reeks

Een metrische time-reeks is een reeks metingen (of metrische waarden) die gedurende een bepaalde periode zijn vastgelegd. 

Bijvoorbeeld:

- Het CPU-gebruik van een virtuele machine
- De binnenkomende bytes (ingangs) van een opslag account
- Het aantal mislukte aanvragen van een webtoepassing



## <a name="alert-rule-on-a-single-time-series"></a>Waarschuwings regel voor één tijd reeks
Een waarschuwings regel bewaakt één time-reeks wanneer deze voldoet aan de volgende voor waarden:
-   De regel controleert één doel resource 
-   Bevat één voor waarde
-   Hiermee wordt een metrische waarde geëvalueerd zonder dat er dimensies worden gekozen (waarbij de metrische gegevens worden ondersteund)

Een voor beeld van een dergelijke waarschuwings regel (waarbij alleen de relevante eigenschappen worden weer gegeven):
-   Doel resource: *myVM1*
-   Metriek: *percentage CPU*
-   Operator: *groter dan*
-   Drempel waarde: *70*


Voor deze waarschuwings regel wordt één metrische time-reeks gecontroleerd:
-   Percentage CPU waarbij *resource*= ' myVM1 ' > 70%

![Een waarschuwings regel voor één tijd reeks](media/alerts-metric-multiple-time-series-single-rule/simple-alert-rule.png)

## <a name="alert-rule-on-multiple-time-series"></a>Waarschuwings regel voor meerdere tijd reeksen
Een waarschuwings regel bewaakt meerdere time series als er ten minste één van de volgende functies wordt gebruikt: 
-   Meerdere resources
-   Meerdere voor waarden 
-   Meerdere dimensies


## <a name="multiple-resources-multi-resource"></a>Meerdere bronnen (meerdere resources)

Met één metrische waarschuwings regel kunnen meerdere resources worden bewaakt, op voor waarde dat de resources van hetzelfde type zijn en zich in dezelfde Azure-regio bevinden. Het gebruik van dit type regel vermindert de complexiteit en het totale aantal waarschuwings regels dat u moet onderhouden. 

Een voor beeld van een dergelijke waarschuwings regel:
-   Doel resource: *myVM1, myVM2*
-   Metriek: *percentage CPU*
-   Operator: *groter dan*
-   Drempel waarde: *70*

Voor deze waarschuwings regel worden twee metrische time-series afzonderlijk bewaakt:
-   Percentage CPU waarbij *resource*= ' myVM1 ' > 70%
-   Percentage CPU waarbij *resource*= ' myVM2 ' > 70%

![Een regel voor een waarschuwing met meerdere bronnen](media/alerts-metric-multiple-time-series-single-rule/multi-resource-alert-rule.png)
 
In een waarschuwings regel met meerdere bronnen wordt de voor waarde **afzonderlijk** geëvalueerd voor elk van de resources (of nauw keuriger, voor elk van de metrische tijd-reeksen die overeenkomen met elke resource). Dit betekent dat waarschuwingen ook afzonderlijk worden gestart voor elke resource.

Stel dat we de bovenstaande waarschuwings regel hebben ingesteld op het bewaken van de CPU boven 70%. In de geëvalueerde tijds periode (dat wil zeggen, de laatste 5 minuten)
-   Het *percentage CPU* van *myVM1* is groter dan 70% 
-   Het *percentage CPU* van *myVM2* is 50%

De waarschuwings regel wordt geactiveerd op *myVM1*, maar niet *myVM2*. Deze geactiveerde waarschuwingen zijn onafhankelijk. Ze kunnen ook worden omgezet op verschillende tijdstippen, afhankelijk van het individuele gedrag van elk van de virtuele machines.

Zie [bewaking op schaal met behulp van metrische waarschuwingen in azure monitor](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)voor meer informatie over regels voor meerdere resources en de resource typen die voor deze mogelijkheid worden ondersteund.

> [!NOTE] 
> In een metrische waarschuwings regel waarmee meerdere resources worden bewaakt, is slechts één voor waarde toegestaan.

## <a name="multiple-conditions-multi-condition"></a>Meerdere voor waarden (multi-condition)

Met één metrische waarschuwings regel kunnen ook Maxi maal vijf voor waarden per waarschuwings regel worden gecontroleerd. 

Bijvoorbeeld:

- Doel resource: *myVM1*
- Condition1
  - Metriek: *percentage CPU*
  - Operator: *groter dan*
  - Drempel waarde: *70*
- Condition2
  - Metric: *netwerk in totaal*
  - Operator: *groter dan*
  - Drempel waarde: *20 MB*

Voor deze waarschuwings regel worden twee metrische time-series gecontroleerd:

- Percentage CPU waarbij *resource*= ' myVM1 ' > 70%
- Netwerk in totaal waarbij *resource*= ' myVM1 ' > 20 MB

![Een waarschuwings regel voor meerdere voor waarden](media/alerts-metric-multiple-time-series-single-rule/multi-condition-alert-rule.png)
 
Er wordt een operator AND gebruikt tussen de voor waarden. Met de waarschuwings regel wordt een waarschuwing geactiveerd wanneer aan **alle** voor waarden wordt voldaan. De geactiveerde waarschuwing wordt opgelost als aan ten minste één van de voor waarden niet meer wordt voldaan. 

> [!NOTE]
> Er zijn beperkingen bij het gebruik van dimensies in een waarschuwings regel met meerdere voor waarden. Zie [beperkingen bij het gebruik van dimensies in een metrische waarschuwings regel met meerdere voor waarden](alerts-troubleshoot-metric.md#restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions)voor meer informatie.


## <a name="multiple-dimensions-multi-dimension"></a>Meerdere dimensies (Multi-Dimension)

Eén metrische waarschuwings regel kan ook meerdere dimensie waarden van een metriek bewaken. De afmetingen van een metriek zijn naam/waarde-paren die extra gegevens bevatten om de metrische waarde te beschrijven. De waarde van de **trans acties** van een opslag account heeft bijvoorbeeld een dimensie met de naam **API name**, waarmee de naam van de API wordt beschreven door elke trans actie (bijvoorbeeld GetBlob, DeleteBlob, PutPage). Het gebruik van dimensies is optioneel, maar het filtert de metrische gegevens en alleen de bewaking specifieke time series, in plaats van de metriek als een aggregatie van alle gedefinieerde dimensionale waarden. 

U kunt bijvoorbeeld aangeven dat er een waarschuwing moet worden geactiveerd wanneer het aantal trans acties hoog is voor alle API-namen (die de geaggregeerde gegevens zijn), of ze verder wilt opsplitsen in een waarschuwing wanneer het aantal trans acties hoog is voor specifieke API-namen. 

Een voor beeld van een waarschuwings regel voor het bewaken van meerdere dimensies is:

- Doel resource: *myStorage1*
- Metriek: *trans acties*
- Dimensies
  * API-naam = *GetBlob, DeleteBlob, PutPage*
- Operator: *groter dan*
- Drempel waarde: *70*

Voor deze waarschuwings regel worden drie meet tijd-Series bewaakt:

- Trans acties waarbij *resource*= ' myStorage1 ' en *API name*= ' GetBlob ' > 70
- Trans acties waarbij *resource*= ' myStorage1 ' en *API name*= ' DeleteBlob ' > 70
- Trans acties waarbij *resource*= ' myStorage1 ' en *API name*= ' PutPage ' > 70

![Een waarschuwings regel met meerdere dimensies met waarden van een dimensie](media/alerts-metric-multiple-time-series-single-rule/multi-dimension-1.png)

Een waarschuwings regel met metrische gegevens met meerdere dimensies kan ook meerdere dimensie waarden van **verschillende** dimensies van een metriek bewaken. In dit geval bewaakt de waarschuwings regel **afzonderlijk** alle combi Naties van dimensie waarden van de geselecteerde dimensies.

Een voor beeld van dit type waarschuwings regel:

- Doel resource: *myStorage1*
- Metriek: *trans acties*
- Dimensies
  * API-naam = *GetBlob, DeleteBlob, PutPage*
  * Verificatie = *SAS, AccountKey*
- Operator: *groter dan*
- Drempel waarde: *70*

Voor deze waarschuwings regel worden zes metrische time-series afzonderlijk bewaakt:

- Trans acties waarbij *resource*= ' myStorage1 ' en *API name*= ' GetBlob ' en *Authentication*= ' SAS ' > 70
- Trans acties waarbij *resource*= ' myStorage1 ' en *API name*= ' GetBlob ' en *Authentication*= ' AccountKey ' > 70
- Trans acties waarbij *resource*= ' myStorage1 ' en *API name*= ' DeleteBlob ' en *Authentication*= ' SAS ' > 70
- Trans acties waarbij *resource*= ' myStorage1 ' en *API name*= ' DeleteBlob ' en *Authentication*= ' AccountKey ' > 70
- Trans acties waarbij *resource*= ' myStorage1 ' en *API name*= ' PutPage ' en *Authentication*= ' SAS ' > 70
- Trans acties waarbij *resource*= ' myStorage1 ' en *API name*= ' PutPage ' en *Authentication*= ' AccountKey ' > 70

![Een waarschuwings regel met meerdere dimensies met waarden van meerdere dimensies](media/alerts-metric-multiple-time-series-single-rule/multi-dimension-2.png)
 
### <a name="advanced-multi-dimension-features"></a>Geavanceerde functies voor meerdere dimensies

1.  **Alle huidige en toekomstige dimensies selecteren** : u kunt ervoor kiezen om alle mogelijke waarden van een dimensie te bewaken, met inbegrip van toekomstige waarden. Een dergelijke waarschuwings regel schaalt automatisch om alle waarden van de dimensie te bewaken zonder dat u de waarschuwings regel telkens wanneer een dimensie waarde wordt toegevoegd of verwijderd, moet wijzigen.
2.  **Uitgezonderd dimensies** : als u de operator ' ≠ ' (uitsluiten) voor een dimensie waarde selecteert, is dit gelijk aan het selecteren van alle andere waarden van die dimensie, inclusief toekomstige waarden.
3.  **Nieuwe en aangepaste dimensies** : de dimensie waarden die worden weer gegeven in de Azure Portal zijn gebaseerd op metrische gegevens die in de afgelopen dag zijn verzameld. Als de dimensie waarde die u zoekt nog niet is verzonden, kunt u een aangepaste dimensie waarde toevoegen.
4. **Overeenkomende dimensies met een voor voegsel** : u kunt ervoor kiezen om alle dimensie waarden te bewaken die beginnen met een specifiek patroon, door de operator ' begint met ' te selecteren en een aangepast voor voegsel in te voeren.

![Geavanceerde functies voor meerdere dimensies](media/alerts-metric-multiple-time-series-single-rule/advanced-features.png)


## <a name="metric-alerts-pricing"></a>Prijzen voor metrische waarschuwingen

De prijs informatie voor metrische waarschuwings regels is beschikbaar op de [pagina met Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/).

Bij het maken van een metrische waarschuwings regel is de opgegeven prijs schatting gebaseerd op de geselecteerde onderdelen en het aantal bewaakte time-series, dat wordt bepaald op basis van de regel configuratie en huidige metrische waarden. De maandelijkse kosten zijn echter gebaseerd op de werkelijke evaluaties van de time-series en kunnen daarom afwijken van de oorspronkelijke schatting als een bepaalde periode geen gegevens heeft om te evalueren, of als de waarschuwings regel gebruikmaakt van functies die het dynamisch schalen kunnen maken.

Een waarschuwings regel kan bijvoorbeeld een hoge prijs schatting weer geven als deze gebruikmaakt van de functie voor meerdere dimensies, en een groot aantal combi Naties van dimensie waarden worden geselecteerd, wat resulteert in de bewaking van veel tijd reeksen. Maar de werkelijke kosten voor die waarschuwings regel kunnen lager zijn als niet alle tijd reeksen die het resultaat zijn van de combi Naties van dimensie waarden eigenlijk gegevens hebben om te evalueren.

## <a name="number-of-time-series-monitored-by-a-single-alert-rule"></a>Aantal tijd reeksen dat wordt bewaakt door één waarschuwings regel

Om overtollige kosten te voor komen, kan elke waarschuwings regel standaard tot 5000 time-series bewaken. Als u deze limiet van uw abonnement wilt opheffen, opent u een ondersteunings ticket.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over bewaking op schaal met behulp van metrische waarschuwingen en [Dynamische drempel waarden](../alerts/alerts-dynamic-thresholds.md).

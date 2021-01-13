---
title: 'Begrijpen hoe waarschuwingen voor metrische gegevens werken in Azure Monitor:'
description: Bekijk een overzicht van wat u met metrische waarschuwingen kunt doen en hoe ze werken in Azure Monitor.
ms.date: 01/13/2021
ms.topic: conceptual
ms.subservice: alerts
ms.openlocfilehash: d8e0621f88455a68ad0ee1b236f6b423c9d54b52
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179891"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Begrijpen hoe metrische waarschuwingen werken in Azure Monitor

Metrische waarschuwingen in Azure Monitor werken bovenop meerdere dimensies. Deze metrische gegevens zijn mogelijk [platform metrieken](alerts-metric-near-real-time.md#metrics-and-dimensions-supported), [aangepaste metrische gegevens](./metrics-custom-overview.md), [populaire logboeken van Azure monitor geconverteerd naar metrische gegevens](./alerts-metric-logs.md) en Application Insights metrische gegevens. Metrische waarschuwingen bepalen regel matig of er voor waarden in een of meer metrische time-series waar zijn en u wordt gewaarschuwd wanneer aan de evaluaties wordt voldaan. Metrische waarschuwingen zijn stateful, dat wil zeggen dat ze alleen meldingen verzenden wanneer de status wordt gewijzigd.

## <a name="how-do-metric-alerts-work"></a>Hoe werken metrische waarschuwingen?

U kunt een metrische waarschuwings regel definiëren door een doel resource op te geven die moet worden bewaakt, metrische naam, voorwaarde type (statisch of dynamisch) en de voor waarde (een operator en een drempel/gevoeligheid) en een actie groep die moet worden geactiveerd wanneer de waarschuwings regel wordt geactiveerd. Voorwaardentypen beïnvloeden de manier waarop drempelwaarden worden bepaald. Meer [informatie over de drempel waarde voor het type en gevoeligheids opties voor dynamische waarden](alerts-dynamic-thresholds.md).

### <a name="alert-rule-with-static-condition-type"></a>Waarschuwings regel met statisch voorwaarde type

Stel dat u als volgt een eenvoudige waarschuwings regel voor een statische drempel waarde hebt gemaakt:

- Doel resource (de Azure-resource die u wilt bewaken): myVM
- Metriek: percentage CPU
- Voorwaarde type: statisch
- Tijd aggregatie (statistiek dat wordt uitgevoerd boven onbewerkte meet waarden. [Ondersteunde tijd aggregaties](metrics-charts.md#changing-aggregation) zijn min, Max, Gem, totaal, aantal): gemiddeld
- Periode (het venster terugkijken waarover metrische waarden worden gecontroleerd): in de afgelopen 5 minuten
- Frequentie (de frequentie waarmee met de metrische waarschuwing wordt gecontroleerd of aan de voor waarden wordt voldaan): 1 min.
- Operator: groter dan
- Drempel waarde: 70

Vanaf het moment dat de waarschuwings regel wordt gemaakt, wordt de monitor elke 1 minuut uitgevoerd en wordt gekeken naar metrische waarden voor de laatste 5 minuten en wordt gecontroleerd of het gemiddelde van die waarden 70 overschrijdt. Als aan de voor waarde wordt voldaan, is het gemiddelde percentage CPU voor de laatste 5 minuten hoger dan 70, de waarschuwings regel geactiveerd een geactiveerde melding. Als u een e-mail bericht of een actie voor een webhook hebt geconfigureerd in de actie groep die is gekoppeld aan de waarschuwings regel, ontvangt u een geactiveerde melding op beide.

Wanneer u meerdere voor waarden in één regel gebruikt, de regel ' and ' de voor waarden tegelijk. Dat wil zeggen dat een waarschuwing wordt geactiveerd wanneer alle voor waarden in de waarschuwings regel worden geëvalueerd als waar en worden opgelost wanneer een van de voor waarden niet meer waar is. Een voor beeld van dit type waarschuwings regel is om een virtuele machine van Azure te bewaken en een waarschuwing te ontvangen wanneer beide ' percentage CPU is hoger dan 90% ' en ' wachtrij lengte meer dan 300 items ' is.

### <a name="alert-rule-with-dynamic-condition-type"></a>Waarschuwings regel met dynamisch type voor waarde

Stel dat u een regel voor metrische waarschuwing voor eenvoudige dynamische drempel waarden hebt gemaakt:

- Doel resource (de Azure-resource die u wilt bewaken): myVM
- Metriek: percentage CPU
- Voorwaarde type: dynamisch
- Tijd aggregatie (statistiek dat wordt uitgevoerd boven onbewerkte meet waarden. [Ondersteunde tijd aggregaties](metrics-charts.md#changing-aggregation) zijn min, Max, Gem, totaal, aantal): gemiddeld
- Periode (het venster terugkijken waarover metrische waarden worden gecontroleerd): in de afgelopen 5 minuten
- Frequentie (de frequentie waarmee met de metrische waarschuwing wordt gecontroleerd of aan de voor waarden wordt voldaan): 1 min.
- Operator: groter dan
- Gevoeligheid: gemiddeld
- Alle Peri Oden bekijken: 4
- Aantal schendingen: 4

Zodra de waarschuwings regel is gemaakt, krijgen de dynamische drempel waarden machine learning-algoritme historische gegevens te zien die beschikbaar zijn, wordt de drempel waarde berekend die het beste past bij het gedrags patroon van de metrische serie en doorlopend meer op basis van nieuwe gegevens om de drempel waarde nauw keuriger te maken.

Vanaf het moment dat de waarschuwings regel wordt gemaakt, wordt de monitor elke 1 minuut uitgevoerd en wordt de waarde van de waarden in de afgelopen 20 minuten genoteerd in vijf minuten en wordt gecontroleerd of het gemiddelde van de periode waarden in elk van de vier Peri Oden de verwachte drempel overschrijdt. Als wordt voldaan aan de voor waarde, wordt het gemiddelde percentage CPU in de afgelopen 20 minuten (4 5 minuten Peri Oden) van het verwachte gedrag vier keer in de waarschuwings regel geactiveerd. Als u een e-mail bericht of een actie voor een webhook hebt geconfigureerd in de actie groep die is gekoppeld aan de waarschuwings regel, ontvangt u een geactiveerde melding op beide.

### <a name="view-and-resolution-of-fired-alerts"></a>Geactiveerde waarschuwingen weer geven en oplossen

De bovenstaande voor beelden van het starten van waarschuwings regels kunnen ook worden weer gegeven in de Azure Portal op de Blade **alle waarschuwingen** .

Zeg het gebruik op ' myVM ' blijft boven de drempel waarde bij volgende controles. de waarschuwings regel wordt niet meer geactiveerd totdat de voor waarden zijn opgelost.

Na enige tijd wordt het gebruik op ' myVM ' weer gegeven op normaal (onder de drempel waarde). De waarschuwings regel controleert de voor waarde voor twee keer dat er een opgeloste melding wordt verzonden. De waarschuwings regel verzendt een opgelost/gedeactiveerd bericht wanneer niet wordt voldaan aan de waarschuwings voorwaarde voor drie opeenvolgende Peri Oden om ruis in het geval van gaat en neer-voor waarden te verminderen.

Wanneer het opgeloste bericht via webhooks of e-mail wordt verzonden, wordt de status van het waarschuwings exemplaar (Monitor status) in Azure Portal ook ingesteld op opgelost.

> [!NOTE]
>
> Wanneer een waarschuwings regel meerdere voor waarden bewaakt, wordt een gebrande waarschuwing opgelost als aan ten minste één van de voor waarden niet meer wordt voldaan voor drie opeenvolgende Peri Oden.

### <a name="using-dimensions"></a>Dimensies gebruiken

Metrische waarschuwingen in Azure Monitor bieden ook ondersteuning voor het bewaken van meerdere combi Naties van dimensie waarden met één regel. Laten we weten waarom u meerdere dimensie combinaties kunt gebruiken met behulp van een voor beeld.

Stel dat u een App Service abonnement hebt voor uw website. U het CPU-gebruik wilt bewaken op meerdere exemplaren waarop uw website/app wordt uitgevoerd. U kunt dit als volgt doen met behulp van een metrische waarschuwings regel:

- Doel resource: myAppServicePlan
- Metriek: percentage CPU
- Voorwaarde type: statisch
- Dimensies
  - Instantie = InstanceName1, InstanceName2
- Tijd aggregatie: gemiddeld
- Period: in de afgelopen 5 minuten
- Frequentie: 1 min.
- Operator: GreaterThan
- Drempel waarde: 70

Net als voorheen controleert deze regel of het gemiddelde CPU-gebruik voor de laatste 5 minuten 70% overschrijdt. Met dezelfde regel kunt u echter twee exemplaren bewaken waarop uw website wordt uitgevoerd. Elk exemplaar wordt afzonderlijk bewaakt en u ontvangt meldingen afzonderlijk.

Stel dat u een web-app hebt die een enorme vraag ziet, en u moet meer exemplaren toevoegen. Met de bovenstaande regel worden nog steeds slechts twee exemplaren gecontroleerd. U kunt echter een regel als volgt maken:

- Doel resource: myAppServicePlan
- Metriek: percentage CPU
- Voorwaarde type: statisch
- Dimensies
  - Instantie = *
- Tijd aggregatie: gemiddeld
- Period: in de afgelopen 5 minuten
- Frequentie: 1 min.
- Operator: GreaterThan
- Drempel waarde: 70

Met deze regel worden alle waarden van het exemplaar automatisch gecontroleerd, d.w.z. u kunt uw instanties bewaken terwijl ze worden weer gegeven zonder dat u uw metrische waarschuwings regel opnieuw hoeft te wijzigen.

Bij het bewaken van meerdere dimensies kan de regel waarschuwings drempelwaarde voor dynamische gegevens aangepaste drempel waarden maken voor honderden metrische reeksen per keer. Dynamische drempel waarden leiden tot minder waarschuwings regels om te beheren en aanzienlijke tijd besparen op het beheer en het maken van waarschuwings regels.

Stel dat u een web-app hebt met veel instanties en u niet weet wat de meest geschikte drempel waarde is. In de bovenstaande regels wordt altijd de drempel waarde van 70% gebruikt. U kunt echter een regel als volgt maken:

- Doel resource: myAppServicePlan
- Metriek: percentage CPU
- Voorwaarde type: dynamisch
- Dimensies
  - Instantie = *
- Tijd aggregatie: gemiddeld
- Period: in de afgelopen 5 minuten
- Frequentie: 1 min.
- Operator: GreaterThan
- Gevoeligheid: gemiddeld
- Navolgende Peri Oden weer geven: 1
- Aantal schendingen: 1

Deze regel controleert of het gemiddelde CPU-gebruik voor de laatste 5 minuten het verwachte gedrag voor elk exemplaar overschrijdt. Met dezelfde regel kunt u exemplaren controleren wanneer deze worden weer gegeven zonder dat u uw metrische waarschuwings regel opnieuw hoeft te wijzigen. Elk exemplaar krijgt een drempel waarde die past bij het gedrags patroon van de metrische reeks en verandert voortdurend op basis van nieuwe gegevens, zodat de drempel nauw keuriger wordt. Net als voorheen wordt elk exemplaar afzonderlijk bewaakt en ontvangt u meldingen afzonderlijk.

Het verhogen van de doorlopende Peri Oden en het aantal schendingen kan er ook toe leiden dat waarschuwingen worden gefilterd op alleen waarschuwingen voor uw definitie van een aanzienlijke afwijking. Meer [informatie over geavanceerde opties voor dynamische drempel waarden](alerts-dynamic-thresholds.md#what-do-the-advanced-settings-in-dynamic-thresholds-mean).

> [!NOTE]
>
> U kunt het beste een *aggregatie granulatie (punt)* kiezen die groter is dan de *frequentie van de evaluatie*, om de kans te verminderen dat de eerste evaluatie van toegevoegde tijd reeksen in de volgende gevallen wordt gereduceerd:
> - Waarschuwings regel voor metrische gegevens die meerdere dimensies bewaakt: wanneer een nieuwe Dimensiewaardecombinatie wordt toegevoegd
> - Waarschuwings regel voor metrische gegevens die meerdere resources bewaakt: wanneer een nieuwe resource wordt toegevoegd aan het bereik
> - Waarschuwings regel voor metrische gegevens die een metriek bewaakt die niet continu wordt uitgezonden (sparse metrisch): wanneer de metriek wordt verzonden na een periode van meer dan 24 uur waarin deze niet is verzonden



## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Bewaking op schaal met behulp van metrische waarschuwingen in Azure Monitor

Tot nu toe hebt u gezien hoe één metrische waarschuwing kan worden gebruikt om een of meer metrische time-series te bewaken die betrekking hebben op één Azure-resource. Vaak wilt u dezelfde waarschuwings regel op veel resources Toep assen. Azure Monitor biedt ook ondersteuning voor het bewaken van meerdere resources (van hetzelfde type) met één metrische waarschuwings regel, voor resources die zich in dezelfde Azure-regio bevinden. 

Deze functie wordt momenteel ondersteund voor platform metrieken (geen aangepaste metrische gegevens) voor de volgende services in de volgende Azure-Clouds:

| Service | Open bare Azure | Overheid | China |
|:--------|:--------|:--------|:--------|
| Virtuele machines<sup>1</sup>  | **Ja** | **Ja** | Nee |
| SQL server-data bases | **Ja** | **Ja** | **Ja** |
| Elastische Pools van SQL Server | **Ja** | **Ja** | **Ja** |
| NetApp-bestands capaciteits groepen | **Ja** | **Ja** | **Ja** |
| Volumes met NetApp-bestanden | **Ja** | **Ja** | **Ja** |
| Sleutelkluizen | **Ja** | **Ja** | **Ja** |
| Azure Cache voor Redis | **Ja** | **Ja** | **Ja** |
| Data Box edge-apparaten | **Ja** | **Ja** | **Ja** |

<sup>1</sup> wordt niet ondersteund voor metrische gegevens van het netwerk van de virtuele machine (netwerk in totaal, totale netwerk verkeer, inkomende stromen, uitgaande stromen, maximum aanmaak frequentie voor inkomende stromen, maximum aanmaak snelheden voor uitgaande stromen).

U kunt op een van de drie manieren het bewakings bereik opgeven voor één metrische waarschuwings regel. Met virtuele machines kunt u bijvoorbeeld het bereik opgeven als:  

- een lijst met virtuele machines (in één Azure-regio) binnen een abonnement
- alle virtuele machines (in één Azure-regio) in een of meer resource groepen in een abonnement
- alle virtuele machines (in één Azure-regio) in een abonnement

> [!NOTE]
>
> Het bereik van een waarschuwings regel voor metrische gegevens met meerdere resources moet ten minste één resource van het geselecteerde resource type bevatten.

Het maken van metrische waarschuwings regels waarmee meerdere resources worden bewaakt, is vergelijkbaar met [het maken van een andere metrische waarschuwing](alerts-metric.md) waarmee één resource wordt gecontroleerd. Alleen een verschil is dat u alle resources selecteert die u wilt bewaken. U kunt deze regels ook maken via [Azure Resource Manager sjablonen](./alerts-metric-create-templates.md#template-for-a-metric-alert-that-monitors-multiple-resources). U ontvangt afzonderlijke meldingen voor elke bewaakte resource.

> [!NOTE]
>
> In een metrische waarschuwingsregel die meerdere resources controleert is slechts één voorwaarde toegestaan.

## <a name="typical-latency"></a>Normale latentie

Voor metrische waarschuwingen krijgt u doorgaans binnen vijf minuten een melding als u de frequentie van de waarschuwingsregel instelt op één minuut. In het geval van een zware belasting voor meldingssystemen krijgt u mogelijk te maken met een langere latentie.

## <a name="supported-resource-types-for-metric-alerts"></a>Ondersteunde resource typen voor metrische waarschuwingen

In dit [artikel](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported)vindt u de volledige lijst met ondersteunde resource typen.


## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over het maken, weer geven en beheren van metrische waarschuwingen in azure](alerts-metric.md)
- [Meer informatie over het maken van waarschuwingen in azure Montior Metrics Explorer](./metrics-charts.md#create-alert-rules)
- [Meer informatie over het implementeren van metrische waarschuwingen met behulp van Azure Resource Manager sjablonen](./alerts-metric-create-templates.md)
- [Meer informatie over actie groepen](action-groups.md)
- [Meer informatie over het type voor waarde van dynamische drempel waarden](alerts-dynamic-thresholds.md)
- [Meer informatie over het oplossen van problemen met metrische waarschuwingen](alerts-troubleshoot-metric.md)


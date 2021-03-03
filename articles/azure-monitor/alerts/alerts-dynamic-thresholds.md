---
title: Waarschuwingen maken met dynamische drempel waarden in Azure Monitor
description: Waarschuwingen maken met op machine learning gebaseerde Dynamische drempel waarden
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 01/04/2021
ms.openlocfilehash: 9cf4caf77d14dd1296276ca48827c170ef265e9b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101718060"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor"></a>Metrische waarschuwingen met dynamische drempel waarden in Azure Monitor

Een metrische waarschuwing met dynamische drempel waarden detectie maakt gebruik van geavanceerde machine learning (ML) om het historische gedrag van metrische gegevens te leren, identificeert patronen en afwijkingen die duiden op mogelijke service problemen. Het biedt ondersteuning voor zowel een eenvoudige UI als bewerkingen op schaal door gebruikers in staat te stellen waarschuwings regels te configureren via de Azure Resource Manager-API, op een volledig geautomatiseerde manier.

Zodra een waarschuwings regel is gemaakt, wordt deze alleen geactiveerd wanneer de gecontroleerde metrische gegevens niet zoals verwacht functioneren, op basis van de aangepaste drempel waarden.

We horen graag uw feedback en blijven deze op <azurealertsfeedback@microsoft.com> .

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>Waarom en wanneer wordt dynamisch voor waarden type gebruikt?

1. **Schaal bare waarschuwing** : dynamische regels voor drempel waarden kunnen een aangepaste drempel waarde voor honderden metrische reeksen tegelijk maken, maar bieden hetzelfde gemak voor het definiëren van een waarschuwings regel voor één metriek. Ze geven u minder waarschuwingen om te maken en te beheren. U kunt Azure Portal of de Azure Resource Manager-API gebruiken om ze te maken. De schaal bare aanpak is vooral nuttig bij het omgaan met metrische dimensies of bij het Toep assen op meerdere resources, zoals bij alle abonnements bronnen.  Meer [informatie over het configureren van metrische waarschuwingen met dynamische drempel waarden met behulp van sjablonen](./alerts-metric-create-templates.md).

1. **Herkenning van Smart metrische patronen** : met onze ml-technologie kunnen we automatisch metrische patronen detecteren en aanpassen aan metrische wijzigingen in de loop van de tijd, wat vaak een seizoensgebondenheid (per uur/dagelijks/wekelijks) kan zijn. Het aanpassen van het gedrag van metrische gegevens in de loop van de tijd en waarschuwing op basis van afwijkingen van het patroon, wordt de last van het weten van de drempel waarde ' right ' voor elke metriek vrijgegeven. Het ML-algoritme dat wordt gebruikt in dynamische drempel waarden is ontworpen om te voor komen dat er ruis (lage precisie) of grote (lage) drempel waarden zijn die geen verwacht patroon hebben.

1. **Intuïtieve configuratie** : Dynamische drempel waarden maken het mogelijk om metrische waarschuwingen in te stellen met behulp van concepten op hoog niveau, waardoor er geen uitgebreide domein kennis nodig is over de metriek.

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>Waarschuwings regels met dynamische drempel waarden configureren?

Waarschuwingen met dynamische drempel waarden kunnen worden geconfigureerd via metrische waarschuwingen in Azure Monitor. Meer [informatie over het configureren van metrische waarschuwingen](alerts-metric.md).

## <a name="how-are-the-thresholds-calculated"></a>Hoe worden de drempel waarden berekend?

Dynamische drempel waarden worden doorlopend ontdekt bij de gegevens van de metrische reeks en proberen deze te model leren met behulp van een set algoritmen en methoden. Het detecteert patronen in de gegevens, zoals seizoensgebondenheid (per uur/dagelijks/wekelijks), en kan de metrische gegevens van de ruis (zoals CPU of geheugen van de machine) en de metrieken met lage sprei ding (zoals Beschik baarheid en fout percentage) afhandelen.

De drempel waarden worden geselecteerd op een zodanige manier dat een afwijking van deze drempel waarden een afwijking van het meet gedrag aangeeft.

> [!NOTE]
> Dynamische drempel waarden kunnen de seizoensgebondenheid voor elk uur, dagelijks of wekelijks patroon detecteren. Andere patronen, zoals bidirectionele of semi-wekelijkse Seizoensgebonden, worden mogelijk niet gedetecteerd. Voor het detecteren van wekelijkse seizoensgebonden tijd zijn er ten minste drie weken aan historische gegevens vereist. 

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>Wat betekent ' gevoeligheid ' in dynamische drempels?

De gevoeligheid van de waarschuwings drempel is een concept op hoog niveau waarmee de mate van afwijking van het metrische gedrag dat nodig is om een waarschuwing te activeren, wordt bepaald.
Voor deze optie is geen kennis van het domein vereist over de metrische gegevens zoals statische drempel waarden. De beschikbare opties zijn:

- Hoog: de drempel waarden worden nauw keurig en dicht bij het patroon van de metrische serie. Een waarschuwings regel wordt op de kleinste afwijking geactiveerd, wat resulteert in meer waarschuwingen.
- Gemiddeld: minder nauw keurig en evenwichtigere drempel waarden, minder waarschuwingen dan met hoge gevoeligheid (standaard).
- Laag – de drempels worden los van het patroon van de metrische serie op afstand. Een waarschuwings regel wordt alleen geactiveerd op grote afwijkingen, wat resulteert in minder waarschuwingen.

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>Wat zijn de instellings opties voor Opera tors in dynamische drempel waarden?

Waarschuwings regel voor dynamische drempel waarden kan aangepaste drempel waarden maken op basis van het metrische gedrag voor zowel boven-als ondergrenzen met dezelfde waarschuwings regel.
U kunt kiezen welke waarschuwing moet worden geactiveerd op een van de volgende drie voor waarden:

- Groter dan de hoogste drempel waarde of lager dan de onderste drempel waarde (standaard)
- Groter dan de bovenste drempel waarde
- Lager dan de laagste drempel waarde.

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>Wat betekenen de geavanceerde instellingen in dynamische drempel waarden?

Als er een **fout optreedt** , kunt u met dynamische drempel waarden ook ' aantal schendingen voor het activeren van de waarschuwing ' configureren, een minimum aantal afwijkingen dat is vereist binnen een bepaald tijd venster voor het systeem om een waarschuwing te genereren (het standaardtijds venster is vier afwijkingen in 20 minuten). De gebruiker kan mislukte Peri Oden configureren en bepalen wat er moet worden gewaarschuwd door de peri Oden en het tijd venster te wijzigen. Deze mogelijkheid vermindert het waarschuwings geluid dat door tijdelijke pieken wordt gegenereerd. Bijvoorbeeld:

Gebruik de volgende instellingen om een waarschuwing te activeren wanneer het probleem in een periode van vijf minuten doorlopend is gedurende 20 minuten, 4 opeenvolgende tijdstippen in een bepaalde Peri Oden.

![Instellingen voor niet-aaneengesloten Peri Oden voor een doorlopend probleem gedurende 20 minuten, 4 opeenvolgende tijden in een bepaalde periode groepering van vijf minuten](media/alerts-dynamic-thresholds/0008.png)

Gebruik de volgende instellingen om een waarschuwing te activeren wanneer er een schending van de dynamische drempel waarden in 20 minuten van de laatste 30 minuten is opgetreden met een periode van vijf minuten:

![De instellingen voor het mislukken van een probleem met een periode van vijf minuten in de laatste 30 minuten bij het oplossen van problemen met peri Oden](media/alerts-dynamic-thresholds/0009.png)

**Gegevens negeren voor** -gebruikers kunnen eventueel ook een begin datum definiëren van waaruit het systeem moet beginnen met het berekenen van de drempel waarden. Een typische use-case kan zich voordoen wanneer een resource in een test modus werd uitgevoerd en is gepromoveerd tot een productiewerk belasting, en dus het gedrag van elke metriek tijdens de test fase moet worden genegeerd.

## <a name="how-do-you-find-out-why-a-dynamic-thresholds-alert-was-triggered"></a>Hoe kom ik erachter waarom een waarschuwing over dynamische drempel waarden is geactiveerd?

U kunt geactiveerde waarschuwings instanties in de weer gave waarschuwingen verkennen door te klikken op de koppeling in het e-mail bericht of in de browser om de weer gave waarschuwingen in de Azure Portal weer te geven. Meer [informatie over de weer gave waarschuwingen](./alerts-overview.md#alerts-experience).

De weer gave waarschuwingen wordt weer gegeven:

- Alle metrische gegevens op het moment dat de waarschuwing voor dynamische drempel waarden wordt geactiveerd.
- Een grafiek van de periode waarin de waarschuwing is geactiveerd, inclusief de dynamische drempel waarden die op dat moment zijn gebruikt.
- De mogelijkheid om feedback te geven over dynamische drempel waarden waarschuwing en de weer gave van waarschuwingen, waardoor toekomstige detecties kunnen worden verbeterd.

## <a name="will-slow-behavior-changes-in-the-metric-trigger-an-alert"></a>Worden de wijzigingen in de metrische gegevens voor een waarschuwing traag?

Waarschijnlijk niet. Dynamische drempel waarden zijn handig voor het detecteren van belang rijke afwijkingen in plaats van langzaam veranderende problemen.

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>Hoeveel gegevens worden gebruikt om de drempel waarden te bekijken en te berekenen?

Wanneer een waarschuwings regel voor het eerst wordt gemaakt, worden de drempel waarden die worden weer gegeven in de grafiek berekend op basis van voldoende historische gegevens voor het berekenen van het uur of het dagelijks seizoen patroon (10 dagen). Zodra een waarschuwings regel is gemaakt, gebruikt dynamische drempels alle benodigde historische gegevens die beschikbaar zijn en doorlopend en aanpassen op basis van nieuwe gegevens om de drempel waarden nauw keuriger te maken. Dit betekent dat na deze berekening ook wekelijkse patronen worden weer gegeven in de grafiek.

## <a name="how-much-data-is-needed-to-trigger-an-alert"></a>Hoeveel gegevens zijn er nodig om een waarschuwing te activeren?

Als u een nieuwe resource hebt of metrische gegevens ontbreken, worden er door dynamische drempel waarden geen waarschuwingen voor drie dagen geactiveerd en zijn er ten minste 30 steek proeven met metrische gegevens beschikbaar om nauw keurige drempel waarden te garanderen.
Voor bestaande resources met voldoende metrische gegevens kunnen dynamische drempel waarden direct geactiveerd worden.

## <a name="dynamic-thresholds-best-practices"></a>Aanbevolen procedures voor dynamische drempel waarden

Dynamische drempel waarden kunnen worden toegepast op de meeste platformen en aangepaste metrische gegevens in Azure Monitor en is ook afgestemd op de algemene metrische gegevens van de toepassing en infra structuur.
De volgende items zijn aanbevolen procedures voor het configureren van waarschuwingen voor sommige van deze metrische gegevens met behulp van dynamische drempel waarden.

### <a name="dynamic-thresholds-on-virtual-machine-cpu-percentage-metrics"></a>Dynamische drempel waarden voor metrische gegevens van CPU-percentage van virtuele machine

1. Klik in [Azure Portal](https://portal.azure.com)op **monitor**. In de weer gave monitor worden alle bewakings instellingen en-gegevens in één weer gave geconsolideerd.

2. Klik op **waarschuwingen** en vervolgens op **+ nieuwe waarschuwings regel**.

    > [!TIP]
    > De meeste resource-Blades hebben ook **waarschuwingen** in het resource menu onder **bewaking**, u kunt hier ook waarschuwingen van maken.

3. Klik op **doel selecteren** in het context deel venster dat wordt geladen, selecteer een doel resource waarop u een waarschuwing wilt ontvangen. Gebruik het **abonnement** en de vervolg keuzelijst **virtual machines resource type** om de resource te vinden die u wilt bewaken. U kunt ook de zoek balk gebruiken om uw bron te vinden.

4. Wanneer u een doel resource hebt geselecteerd, klikt u op **voor waarde toevoegen**.

5. Selecteer het **CPU-percentage**.

6. U kunt de metriek eventueel verfijnen door de **periode** en **aggregatie** aan te passen. Het wordt afgeraden om het aggregatie type ' maximum ' te gebruiken voor dit metrische type omdat het minder representatief is voor het gedrag. De statische drempel waarde voor het aggregatie type ' maximum ' kan wellicht beter overeenkomen.

7. Er wordt een grafiek weer gegeven met de metrische gegevens voor de afgelopen zes uur. Definieer de waarschuwings parameters:
    1. **Voorwaarde type** : Kies optie Dynamic.
    1. **Gevoeligheid** : Kies gemiddeld/laag gevoeligheid om waarschuwings ruis te verminderen.
    1. **Operator** : Kies groter dan, tenzij gedrag staat voor het toepassings gebruik.
    1. **Frequentie** : overweeg het verlagen op basis van de bedrijfs impact van de waarschuwing.
    1. **Mislukte Peri Oden** (geavanceerde optie): het venster terugkijken moet ten minste 15 minuten zijn. Als de periode bijvoorbeeld is ingesteld op vijf minuten, moeten de mislukte Peri Oden ten minste drie of meer zijn.

8. De metrische grafiek geeft de berekende drempel waarden weer op basis van recente gegevens.

9. Klik op **Gereed**.

10. Vul de **waarschuwings Details** in, zoals de naam, **Beschrijving** en **Ernst** van de **waarschuwings regel**.

11. Voeg een actie groep toe aan de waarschuwing door een bestaande actie groep te selecteren of een nieuwe actie groep te maken.

12. Klik op **gereed** om de waarschuwings regel voor metrische gegevens op te slaan.

> [!NOTE]
> Metrische waarschuwings regels die via de portal zijn gemaakt, worden gemaakt in dezelfde resource groep als de doel resource.

### <a name="dynamic-thresholds-on-application-insights-http-request-execution-time"></a>Dynamische drempel waarden voor de uitvoerings tijd van de HTTP-aanvraag van Application Insights

1. Klik in [Azure Portal](https://portal.azure.com)op **monitor**. In de weer gave monitor worden alle bewakings instellingen en-gegevens in één weer gave geconsolideerd.

2. Klik op **waarschuwingen** en vervolgens op **+ nieuwe waarschuwings regel**.

    > [!TIP]
    > De meeste resource-Blades hebben ook **waarschuwingen** in het resource menu onder **bewaking**, u kunt hier ook waarschuwingen van maken.

3. Klik op **doel selecteren** in het context deel venster dat wordt geladen, selecteer een doel resource waarop u een waarschuwing wilt ontvangen. Gebruik het **abonnement** en de vervolg keuzelijst **Application Insights resource type** om de resource te vinden die u wilt bewaken. U kunt ook de zoek balk gebruiken om uw bron te vinden.

4. Wanneer u een doel resource hebt geselecteerd, klikt u op **voor waarde toevoegen**.

5. Selecteer de **uitvoerings tijd van de HTTP-aanvraag**.

6. U kunt de metriek eventueel verfijnen door de **periode** en **aggregatie** aan te passen. Het wordt afgeraden om het aggregatie type ' maximum ' te gebruiken voor dit metrische type omdat het minder representatief is voor het gedrag. De statische drempel waarde voor het aggregatie type ' maximum ' kan wellicht beter overeenkomen.

7. Er wordt een grafiek weer gegeven met de metrische gegevens voor de afgelopen zes uur. Definieer de waarschuwings parameters:
    1. **Voorwaarde type** : Kies optie Dynamic.
    1. **Operator** : Kies groter dan om waarschuwingen te verminderen die worden geactiveerd tijdens de duur van de verbetering.
    1. **Frequentie** : overweeg het verlagen op basis van de bedrijfs impact van de waarschuwing.

8. De metrische grafiek geeft de berekende drempel waarden weer op basis van recente gegevens.

9. Klik op **Gereed**.

10. Vul de **waarschuwings Details** in, zoals de naam, **Beschrijving** en **Ernst** van de **waarschuwings regel**.

11. Voeg een actie groep toe aan de waarschuwing door een bestaande actie groep te selecteren of een nieuwe actie groep te maken.

12. Klik op **gereed** om de waarschuwings regel voor metrische gegevens op te slaan.

> [!NOTE]
> Metrische waarschuwings regels die via de portal zijn gemaakt, worden gemaakt in dezelfde resource groep als de doel resource.

## <a name="interpreting-dynamic-threshold-charts"></a>Dynamische drempel grafieken interpreteren

Hieronder ziet u een grafiek met een metrische waarde, de dynamische drempel limieten en enkele waarschuwingen die worden geactiveerd wanneer de waarde buiten de toegestane drempel waarden valt.

![Meer informatie over het configureren van metrische waarschuwingen](media/alerts-dynamic-thresholds/threshold-picture-8bit.png)

Gebruik de volgende informatie om de vorige grafiek te interpreteren.

- **Blauwe lijn** : de werkelijke gemeten waarde over tijd.
- **Blauw grijs vlak** : hier wordt het toegestane bereik voor de metrische gegevens weer gegeven. Zolang de metrische waarden binnen dit bereik blijven, wordt er geen waarschuwing weer gegeven.
- **Blauwe stippen** : als u op een deel van de grafiek hebt geklikt en vervolgens met de muis aanwijzer over de blauwe lijn beweegt, ziet u een blauwe stip onder uw cursor met daarin een afzonderlijke geaggregeerde metrische waarde.
- **Pop-up met blauwe stip** : toont de gemeten metrische waarde (de blauwe stip) en de bovenste en onderste waarden van het toegestane bereik.  
- **Rode stip met een zwarte cirkel** : toont de eerste meet waarde uit het toegestane bereik. Dit is de waarde die een metrische waarschuwing activeert en deze in een actieve status plaatst.
- **Rode puntjes**: Geef extra gemeten waarden aan buiten het toegestane bereik. Er worden geen aanvullende metrische waarschuwingen geactiveerd, maar de waarschuwing blijft actief.
- **Rood gebied** : geeft de tijd weer waarop de metrische waarde buiten het toegestane bereik valt. De waarschuwing blijft actief zolang de volgende gemeten waarden buiten het toegestane bereik vallen, maar er worden geen nieuwe waarschuwingen geactiveerd.
- **Einde van het rode gebied** : wanneer de blauwe lijn in de toegestane waarden terugkomt, wordt het rode gebied gestopt en wordt de regel voor de gemeten waarde blauw. De status van de metrische waarschuwing die wordt geactiveerd op het moment van de rode punt met een zwart kader, wordt ingesteld op opgelost.
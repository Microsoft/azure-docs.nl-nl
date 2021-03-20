---
title: Uw service voor metrische gegevens adviseur configureren met de webportal
titleSuffix: Azure Cognitive Services
description: Informatie over het configureren van uw Metrics Advisor-exemplaar en het afstemmen van de resultaten van de anomalie detectie.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 7923dad3d47122c0ceb04d1240736e2b66a0dd64
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92048250"
---
# <a name="how-to-configure-metrics-and-fine-tune-detecting-configuration"></a>Informatie over het configureren van metrische gegevens en het afstemmen van de configuratie van detectie

Gebruik dit artikel om te beginnen met het configureren van uw Metrics Advisor-exemplaar met behulp van de-webportal. Als u de metrische gegevens voor een specifieke gegevensfeed wilt bekijken, gaat u naar de pagina **gegevensfeed** en selecteert u een van de feeds. Hiermee wordt een lijst met metrische gegevens weer gegeven die eraan zijn gekoppeld.

:::image type="content" source="../media/metrics/select-metric.png" alt-text="Een metrische waarde selecteren" lightbox="../media/metrics/select-metric.png":::

Klik op een van de metrische namen om de details ervan weer te geven. In deze gedetailleerde weer gave kunt u overschakelen naar een andere metriek in dezelfde gegevensfeed met behulp van de vervolg keuzelijst in de rechter bovenhoek van het scherm.

Wanneer u de details van een metrische gegevens voor het eerst bekijkt, kunt u een tijd reeks laden door metrische gegevens te laten kiezen door Advisor, of door de waarden op te geven die voor elke dimensie moeten worden opgenomen. 

U kunt ook Peri Oden selecteren en de lay-out van de pagina wijzigen.

> [!NOTE]
> - De begin tijd is inclusief.
> - De eind tijd is exclusief. 

U kunt op het tabblad **incidenten** klikken om afwijkingen weer te geven en een koppeling naar de [incident hub](diagnose-incident.md)te vinden.

## <a name="tune-the-detecting-configuration"></a>De detectie configuratie afstemmen

Een metriek kan een of meer detectie configuraties Toep assen. Er is een standaard configuratie voor elke metrische waarde, die u kunt bewerken of toevoegen op basis van uw bewakings behoeften.

### <a name="tune-the-configuration-for-all-series-in-current-metric"></a>De configuratie voor alle reeksen in de huidige metriek afstemmen

Deze configuratie wordt toegepast op alle reeksen in deze metrische gegevens, met uitzonde ring van items met een afzonderlijke configuratie. Een niveau configuratie wordt standaard toegepast wanneer gegevens onboarded zijn en wordt weer gegeven in het linkerdeel venster. Gebruikers kunnen configuratie op metrische niveau rechtstreeks bewerken op de metrische pagina. 

Er zijn aanvullende para meters als **richting** en **geldige afwijkingen** die kunnen worden gebruikt om de configuratie verder af te stemmen. U kunt ook verschillende detectie methoden combi neren. 

:::image type="content" source="../media/configuration-combination.png" alt-text="Combi natie van configuratie" lightbox="../media/configuration-combination.png":::

### <a name="tune-the-configuration-for-a-specific-series-or-group"></a>De configuratie voor een specifieke serie of groep afstemmen

Klik op **Geavanceerde configuratie** onder de configuratie opties voor het metrieke niveau om de configuratie van het Groepeer niveau weer te geven. U kunt een configuratie voor een afzonderlijke reeks of groep reeksen toevoegen door te klikken op het **+** pictogram in dit venster. De para meters zijn vergelijkbaar met de configuratie parameters op metrische niveau, maar u moet mogelijk ten minste één dimensie waarde voor een configuratie op groeps niveau opgeven om een groep reeksen te identificeren. En geef alle dimensie waarden op voor een configuratie op serie niveau om een specifieke reeks te identificeren. 

Deze configuratie wordt toegepast op de groep reeksen of specifieke reeksen in plaats van de configuratie van het metrieke niveau. Nadat u de voor waarden voor deze groep hebt ingesteld, slaat u deze op.

:::image type="content" source="../media/advanced-configuration.png" alt-text="Geavanceerde configuratie" lightbox="../media/advanced-configuration.png":::

### <a name="anomaly-detection-methods"></a>Methoden voor anomaliedetectie

Met metrische gegevens adviseur kunt u meerdere anomalie detectie methoden bieden. U kunt een gebruiken of combi neren met logische Opera tors door te klikken op de **+** knop. 

**Slimme detectie** 

Slimme detectie wordt aangestuurd door machine learning die patronen uit historische gegevens leert en gebruikt voor toekomstige detectie. Wanneer u deze methode gebruikt, is de **gevoeligheid** de belangrijkste para meter voor het afstemmen van de detectie resultaten. U kunt deze naar een kleinere of grotere waarde slepen om de visualisatie aan de rechter kant van de pagina te beïnvloeden. Kies een optie die past bij uw gegevens en sla deze op. 


In de Slimme detectie modus worden de para meters voor de gevoeligheids-en grens versie gebruikt om het resultaat van de afwijkings detectie nauw keurig af te stemmen.

Gevoeligheid kan de breedte van het verwachte waardebereik van elk punt beïnvloeden. Wanneer dit is toegenomen, is het verwachte waardebereik nauw keuriger en worden er meer afwijkingen gerapporteerd:

:::image type="content" source="../media/metrics/smart-detection-high-sensitivity.png" alt-text="Slimme detectie met hoge gevoeligheid":::

Wanneer de gevoeligheid is uitgeschakeld, wordt het verwachte waardebereik breder en worden er minder afwijkingen gerapporteerd:

:::image type="content" source="../media/metrics/smart-detection-low-sensitivity.png" alt-text="Slimme detectie met lage gevoeligheid":::

**Drempel waarde wijzigen** 

Wijzigings drempelwaarde wordt normaal gesp roken gebruikt wanneer metrische gegevens over het algemeen over een bepaald bereik blijven. De drempel waarde wordt ingesteld op basis van het **wijzigings percentage**. De **wijzigings drempel** modus kan afwijkingen in de scenario's detecteren:

* Uw gegevens zijn normaal gesp roken stabiel en soepel. U wilt een melding ontvangen wanneer er schommelingen zijn.
* Uw gegevens zijn normaal gesp roken goed Insta Biel en schommelen heel veel. U wilt een melding ontvangen wanneer deze te stabiel of plat wordt.

Gebruik de volgende stappen om deze modus te gebruiken:

1. Selecteer **drempel waarde wijzigen** als uw afwijkings methode wanneer u de configuraties voor anomalie detectie instelt voor uw metrische gegevens of tijd reeksen.
    
    :::image type="content" source="../media/metrics/change-threshold.png" alt-text="drempel waarde wijzigen":::

2. Selecteer het **buiten het bereik** of **in de bereik** parameter op basis van uw scenario.

    Als u schommelingen wilt detecteren, selecteert u **buiten het bereik**. Met de onderstaande instellingen wordt bijvoorbeeld elk gegevens punt dat groter is dan 10% in vergelijking met het vorige, gedetecteerd als een uitschieter.
    :::image type="content" source="../media/metrics/out-of-the-range.png" alt-text="para meter buiten bereik":::

    Als u Plate lijnen in uw gegevens wilt detecteren, selecteert u **in het bereik**. Met de onderstaande instellingen wordt bijvoorbeeld elk gegevens punt dat is gewijzigd in 0,01% vergeleken met het vorige, gedetecteerd als een uitschieter. Omdat de drempel zo klein is (0,01%), detecteert deze platte lijnen in de gegevens als uitbijters.

    :::image type="content" source="../media/metrics/in-the-range.png" alt-text="Para meter in bereik":::

3. Stel het percentage van de wijziging in dat als afwijkingen moet worden beschouwd en waarvan eerder vastgelegde gegevens punten worden gebruikt voor vergelijking. Deze vergelijking is altijd tussen het huidige gegevens punt en één gegevens punt N punten vóór het.
    
    De **richting** is alleen geldig als u de modus **voor** het gebruik van het bereik gebruikt:
    
    *  Met detectie wordt gedetecteerd alleen afwijkingen gedetecteerd wanneer (huidige gegevens punt)-(vergelijkings gegevens punt) > **+** drempel percentage.
    * **Hiermee configureert u detectie** zodat alleen afwijkingen worden gedetecteerd wanneer (huidig gegevens punt): (vergelijkt gegevens punt) < **-** drempel percentage.
 
**Harde drempel waarde**

 Harde drempel waarde is een basis methode voor anomalie detectie. U kunt een boven-en/of ondergrens instellen om het verwachte waardebereik te bepalen. Punten die buiten de grens vallen, worden geïdentificeerd als afwijkingen. 


## <a name="preset-events"></a>Vooraf ingestelde gebeurtenissen

Soms kunnen verwachte gebeurtenissen en herhalingen (zoals feest dagen) afwijkende gegevens genereren. Met behulp van vooraf ingestelde gebeurtenissen kunt u tijdens opgegeven tijden vlaggen toevoegen aan de uitvoer van de afwijkings detectie. Deze functie moet worden geconfigureerd nadat de gegevensfeed is onboarded. Elke metriek kan slechts één vooraf ingestelde gebeurtenis configuratie hebben.

> [!Note]
> Bij de vooraf ingestelde gebeurtenis configuratie worden tijdens afwijkende detectie dagen in rekening gebracht en kunnen de resultaten worden gewijzigd. Deze wordt toegepast op de gegevens punten die worden opgenomen na het opslaan van de configuratie. 

Klik op de knop **vooraf ingestelde gebeurtenis configureren** naast de vervolg keuzelijst metrieken op elke pagina metrische gegevens.
 
:::image type="content" source="../media/metrics/preset-event-button.png" alt-text="knop vooraf ingestelde gebeurtenis":::

Configureer de opties op basis van uw gebruik in het venster dat wordt weer gegeven. Zorg ervoor dat **gebeurtenis vakantiedag inschakelen** is geselecteerd om de configuratie te gebruiken. 

De sectie evenement van de **feest dagen** helpt u bij het onderdrukken van onnodige anomalieën die zijn gedetecteerd tijdens de feest dagen. Er zijn twee opties voor de **strategie** optie die u kunt Toep assen:

* **Feestdag onderdrukken**: onderdrukt alle afwijkingen en waarschuwingen in afwijkingen detectie resultaten tijdens de vakantie periode.
* **Feestdag als weekend**: berekent de gemiddelde verwachte waarden van verschillende overeenkomende weekends vóór de feestdag en baseert de afwijkings status van deze waarden.

Er zijn verschillende andere waarden die u kunt configureren:

|Optie  |Beschrijving  |
|---------|---------|
|**Kies één dimensie als land**     | Kies een dimensie die land gegevens bevat. Bijvoorbeeld een land nummer.         |
|**Land nummer toewijzing**     | De toewijzing tussen een standaard [land nummer](https://wikipedia.org/wiki/ISO_3166-1_alpha-2)en de land gegevens van de geselecteerde dimensie.        |
|**Opties voor feest dagen**    | Of u nu rekening moet houden met alle feest dagen, alleen PTO (betaalde vrije tijd) of alleen niet-PTO feest dagen.         |
|**Dagen om uit te breiden**    |  De beïnvloede dagen vóór en na een feestdag.        |


De sectie **cyclus gebeurtenis** kan in sommige scenario's worden gebruikt om onnodige waarschuwingen te helpen verminderen door gebruik te maken van cyclische patronen in de gegevens. Bijvoorbeeld: 

- Metrische gegevens met meerdere patronen of cycli, zoals een wekelijks en een maandelijks patroon. 
- Metrieken die geen duidelijk patroon hebben, maar de gegevens zijn vergelijkbaar met het jaar (YoY), de maand over de maand (MoM), de week over de week (WoW) of de dag van de dag (DoD).
 
Niet alle opties kunnen voor elke granulatie worden geselecteerd. Hieronder vindt u de beschik bare opties per granulatie:

| Granulariteit | YoY | Mama | WoW | DoD |
|:-|:-|:-|:-|:-|
| Jaar | X | X | X | X |
| Maandelijks | X | X | X | X |
| Wekelijks | ✔ | X | X | X |
| Dagelijks | ✔ | ✔ | ✔ | X |
| Per uur | ✔ | ✔ | ✔ | ✔ |
| Per minuut | X | X | X | X |
| Plaats | X | X | X | X |
| Instel | ✔ | ✔ | ✔ | ✔ |

X-niet beschikbaar.  
✔-Beschikbaar.
  
\* Wanneer u een aangepaste granulatie in seconden gebruikt, is alleen beschikbaar als de metriek langer dan een uur is en minder dan een dag.

De cyclus gebeurtenis wordt gebruikt om afwijkingen te verminderen als ze een cyclisch patroon volgen, maar er wordt een afwijkingen gerapporteerd als meerdere gegevens punten het patroon niet volgen. De **strikte modus** wordt gebruikt om anomalie rapportage in te scha kelen als zelfs één gegevens punt het patroon niet volgt. 

:::image type="content" source="../media/metrics/preset-events.png" alt-text="vooraf ingestelde gebeurtenis configuratie":::

## <a name="view-recent-incidents"></a>Recente incidenten weer geven

Met metrische gegevens adviseur worden afwijkingen gedetecteerd op al uw time seriesgegevens wanneer ze worden opgenomen. Niet alle afwijkingen moeten echter worden geëscaleerd, omdat ze mogelijk geen grote gevolgen hebben. Aggregatie wordt uitgevoerd op afwijkingen om gerelateerde items in incidenten te groeperen. U kunt deze incidenten bekijken op het tabblad **incident** op de pagina metrische gegevens. 

Klik op een incident om naar de **analyse pagina incidenten** te gaan, waar u meer informatie over de gegevens kunt zien. Klik op **incidenten beheren in nieuwe incident hub** om de pagina [incident hub](diagnose-incident.md) te vinden waar u alle incidenten kunt vinden onder de specifieke metrische gegevens. 

## <a name="subscribe-anomalies-for-notification"></a>Afwijkings afmelden voor melding

Als u een melding wilt ontvangen wanneer een afwijkend wordt gedetecteerd, kunt u zich abonneren op waarschuwingen voor de metrische gegevens, met behulp van een hook. Zie [waarschuwingen configureren en meldingen ontvangen met behulp van een hook](alerts.md) voor meer informatie.


## <a name="next-steps"></a>Volgende stappen 
- [Waarschuwingen configureren en meldingen ontvangen met behulp van een hook](alerts.md)
- [Anomaliedetectie aanpassen met feedback](anomaly-feedback.md)
- [Een incident diagnosticeren](diagnose-incident.md).


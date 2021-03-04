---
title: Een referentie gids voor de tegels van de weergave ontwerper in Azure Monitor | Microsoft Docs
description: Met behulp van View designer in Azure Monitor kunt u aangepaste weer gaven maken die worden weer gegeven in de Azure Portal en een verscheidenheid aan visualisaties op gegevens in de werk ruimte Log Analytics bevatten. Dit artikel bevat een Naslag Gids voor de instellingen voor de tegels die beschikbaar zijn in uw aangepaste weer gaven.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2018
ms.openlocfilehash: d1d0da70dc1e47d0a1ddb90abbed2eaea83919cd
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040172"
---
# <a name="reference-guide-to-view-designer-tiles-in-azure-monitor"></a>Naslag Gids voor het weer geven van ontwerp tegels in Azure Monitor
Met behulp van View designer in Azure Monitor kunt u verschillende aangepaste weer gaven maken in de Azure Portal die u kan helpen bij het visualiseren van gegevens in uw Log Analytics-werk ruimte. Dit artikel bevat een Naslag Gids voor de instellingen voor de tegels die beschikbaar zijn in uw aangepaste weer gaven.

Zie voor meer informatie over de ontwerp functie voor weer gaven:

* [Weer gave Designer](view-designer.md): bevat een overzicht van de ontwerp weergave en procedures voor het maken en bewerken van aangepaste weer gaven.
* [Naslag informatie over visualisatie onderdelen](view-designer-parts.md): hierin vindt u een Naslag Gids voor de instellingen voor de visualisatie onderdelen die beschikbaar zijn in uw aangepaste weer gaven.


De beschik bare weer gave Designer-tegels worden in de volgende tabel beschreven:  

| Tegel | Beschrijving |
|:--- |:--- |
| [Number](#number-tile) |Het aantal records van een query. |
| [Twee getallen](#two-numbers-tile) |Het aantal records van twee verschillende query's. |
| [Ringdiagram](#donut-tile) | Een grafiek op basis van een query, met een samenvattings waarde in het midden. |
| Lijn diagram en bijschrift | Een lijn diagram dat is gebaseerd op een query en een toelichting met een samenvattings waarde. |
| [Lijn diagram](#line-chart-tile) |Een lijn diagram dat is gebaseerd op een query. |
| [Twee tijd lijnen](#two-timelines-tile) | Een kolom diagram met twee reeksen, elk op basis van een afzonderlijke query. |

In de volgende secties worden de tegel typen en de bijbehorende eigenschappen uitvoerig beschreven.

> [!NOTE]
> Tegels in weer gaven zijn gebaseerd op [logboek query's](../logs/log-query-overview.md) in uw log Analytics-werk ruimte. Dit biedt momenteel geen ondersteuning voor [Cross-resource query's](../logs/cross-workspace-query.md) om gegevens op te halen uit Application Insights.

## <a name="number-tile"></a>Nummer tegel
Op de tegel **getal** wordt zowel het aantal records uit een logboek query als een label weer gegeven.

![Nummer tegel](media/view-designer-tiles/tile-number.png)

| Instelling | Beschrijving |
|:--- |:--- |
| Name |De tekst die boven aan de tegel wordt weer gegeven. |
| Beschrijving |De tekst die wordt weer gegeven onder de naam van de tegel. |
| **Tegel** | |
| Legenda |De tekst die wordt weer gegeven onder de waarde. |
| Query’s uitvoeren |De query die wordt uitgevoerd. Het aantal records dat door de query wordt geretourneerd, wordt weer gegeven. |
| **Geavanceerd** |**Verificatie van de gegevens stroom>** |
| Ingeschakeld |Selecteer deze koppeling als verificatie van de gegevens stroom moet worden ingeschakeld voor de tegel. Deze methode biedt een alternatief bericht als er geen gegevens beschikbaar zijn. Normaal gesp roken gebruikt u de methode om een bericht op te geven tijdens de tijdelijke periode wanneer de weer gave is geïnstalleerd en de gegevens beschikbaar worden. |
| Query’s uitvoeren |De query die wordt uitgevoerd om te bepalen of er gegevens beschikbaar zijn voor de weer gave. Als de query geen resultaten retourneert, wordt een bericht weer gegeven in plaats van de waarde van de hoofd query. |
| Bericht |Het bericht dat wordt weer gegeven als de query voor verificatie van de gegevens stroom geen gegevens retourneert. Als u geen bericht ontvangt, wordt een status bericht over de *beoordeling* weer gegeven. |


## <a name="two-numbers-tile"></a>Tegel met twee getallen
Deze tegel toont het aantal records uit twee verschillende logboek query's en een label voor elke record.

![Tegel met twee getallen](media/view-designer-tiles/tile-two-numbers.png)

| Instelling | Beschrijving |
|:--- |:--- |
| Name |De tekst die boven aan de tegel wordt weer gegeven. |
| Beschrijving |De tekst die wordt weer gegeven onder de naam van de tegel. |
| **Eerste tegel** | |
| Legenda |De tekst die wordt weer gegeven onder de waarde. |
| Query’s uitvoeren |De query die wordt uitgevoerd. Het aantal records dat door de query wordt geretourneerd, wordt weer gegeven. |
| **Tweede tegel** | |
| Legenda |De tekst die wordt weer gegeven onder de waarde. |
| Query’s uitvoeren |De query die wordt uitgevoerd. Het aantal records dat door de query wordt geretourneerd, wordt weer gegeven. |
| **Geavanceerd** |**Verificatie van de gegevens stroom>** |
| Ingeschakeld |Selecteer deze koppeling als verificatie van de gegevens stroom moet worden ingeschakeld voor de tegel. Deze methode biedt een alternatief bericht als er geen gegevens beschikbaar zijn. Normaal gesp roken gebruikt u de methode om een bericht op te geven tijdens de tijdelijke periode wanneer de weer gave is geïnstalleerd en de gegevens beschikbaar worden. |
| Query’s uitvoeren |De query die wordt uitgevoerd om te bepalen of er gegevens beschikbaar zijn voor de weer gave. Als de query geen resultaten retourneert, wordt een bericht weer gegeven in plaats van de waarde van de hoofd query. |
| Bericht |Het bericht dat wordt weer gegeven als de query voor verificatie van de gegevens stroom geen gegevens retourneert. Als u geen bericht ontvangt, wordt een status bericht over de *beoordeling* weer gegeven. |


## <a name="donut-tile"></a>Donut tegel
De **ring** tegel geeft één getal weer dat een kolom waarde in een logboek query samenvatten. In de ring worden de resultaten van de bovenste drie records grafisch weer gegeven.

![Donut tegel](media/view-designer-tiles/tile-donut.png)

| Instelling | Beschrijving |
|:--- |:--- |
| Name |De tekst die boven aan de tegel wordt weer gegeven. |
| Beschrijving |De tekst die wordt weer gegeven onder de naam van de tegel. |
| **Ringdiagram** | |
| Query’s uitvoeren |De query die wordt uitgevoerd voor de ring. De eerste eigenschap is een tekst waarde en de tweede eigenschap is een numerieke waarde. Deze query gebruikt normaal gesp roken het sleutel woord *Measure* om de resultaten samen te vatten. |
| **Ringdiagram** |**> Center** |
| Tekst |De tekst die wordt weer gegeven onder de waarde in de ring. |
| Bewerking |De bewerking die wordt uitgevoerd voor de eigenschap Value om deze als één waarde samen te vatten.<ul><li>Sum: Voeg de waarden van alle records toe met de waarde van de eigenschap.</li><li>Percentage: percentage van de opgetelde waarden van records met de eigenschaps waarde vergeleken met de opgetelde waarden van alle records.</li></ul> |
| Resultaat waarden die worden gebruikt in de middelste bewerking |Selecteer desgewenst het plus teken (+) om een of meer waarden toe te voegen. De resultaten van de query zijn beperkt tot records met de eigenschaps waarden die u opgeeft. Als er geen waarden worden toegevoegd, worden alle records opgenomen in de query. |
| **Ringdiagram** |**> extra opties** |
| Kleuren |De kleur die wordt weer gegeven voor elk van de drie eigenschappen top. Gebruik *geavanceerde kleur toewijzing* om alternatieve kleuren voor specifieke eigenschaps waarden op te geven. |
| Geavanceerde kleur toewijzing |Hiermee wordt een kleur weer gegeven die specifieke eigenschaps waarden vertegenwoordigt. Als de waarde die u opgeeft in de bovenste drie is, wordt de alternatieve kleur weer gegeven in plaats van de standaard kleur. Als de eigenschap zich niet in de bovenste drie bevindt, wordt de kleur niet weer gegeven. |
| **Geavanceerd** |**Verificatie van de gegevens stroom>** |
| Ingeschakeld |Selecteer deze koppeling als verificatie van de gegevens stroom moet worden ingeschakeld voor de tegel. Deze methode biedt een alternatief bericht als er geen gegevens beschikbaar zijn. Normaal gesp roken gebruikt u de methode om een bericht op te geven tijdens de tijdelijke periode wanneer de weer gave is geïnstalleerd en de gegevens beschikbaar worden. |
| Query’s uitvoeren |De query die wordt uitgevoerd om te bepalen of er gegevens beschikbaar zijn voor de weer gave. Als de query geen resultaten retourneert, wordt een bericht weer gegeven in plaats van de waarde van de hoofd query. |
| Bericht |Het bericht dat wordt weer gegeven als de query voor verificatie van de gegevens stroom geen gegevens retourneert. Als u geen bericht ontvangt, wordt een status bericht over de *beoordeling* weer gegeven. |


## <a name="line-chart-tile"></a>Tegel lijn diagram
Deze tegel is een lijn diagram dat gedurende een bepaalde periode meerdere reeksen uit een logboek query weergeeft. 

![Scherm afbeelding van een lijn diagram tegel in de ontwerp functie voor de Azure Monitor weergave.](media/view-designer-tiles/tile-line-chart.png)

| Instelling | Beschrijving |
|:--- |:--- |
| Name |De tekst die boven aan de tegel wordt weer gegeven. |
| Beschrijving |De tekst die wordt weer gegeven onder de naam van de tegel. |
| **Lijn diagram** | |
| Query’s uitvoeren |De query die wordt uitgevoerd voor het lijn diagram. De eerste eigenschap is een tekst waarde en de tweede eigenschap is een numerieke waarde. Deze query gebruikt normaal gesp roken het sleutel woord *Measure* om de resultaten samen te vatten. Als de query het gereserveerde woord *interval* gebruikt, gebruikt de x-as dit tijds interval. Als de query geen gebruik maakt van het *interval* sleutelwoord, gebruikt de x-as uur intervallen. |
| **Lijn diagram** |**> Y-as** |
| Logaritmische schaal gebruiken |Selecteer deze koppeling om een logaritmische schaal voor de y-as te gebruiken. |
| Eenheden |Geef de eenheden op voor de waarden die door de query worden geretourneerd. Deze informatie wordt gebruikt om labels in de grafiek weer te geven die de waardetypen en optioneel voor het converteren van de waarden aangeven. Het **eenheids type** specificeert de categorie van de eenheid en definieert de huidige waarden van het **type eenheid** die beschikbaar zijn. Als u een waarde selecteert in **converteren naar** , worden de numerieke waarden geconverteerd van het **huidige eenheids** type naar het type **converteren naar** . |
| Aangepast label |De tekst die wordt weer gegeven voor de y-as naast het label voor het *eenheids* type. Als er geen label is opgegeven, wordt alleen het *eenheids* type weer gegeven. |
| **Geavanceerd** |**Verificatie van de gegevens stroom>** |
| Ingeschakeld |Selecteer deze koppeling als verificatie van de gegevens stroom moet worden ingeschakeld voor de tegel. Deze methode biedt een alternatief bericht als er geen gegevens beschikbaar zijn. Normaal gesp roken gebruikt u de methode om een bericht op te geven tijdens de tijdelijke periode wanneer de weer gave is geïnstalleerd en de gegevens beschikbaar worden. |
| Query’s uitvoeren |De query die wordt uitgevoerd om te bepalen of er gegevens beschikbaar zijn voor de weer gave. Als de query geen resultaten retourneert, wordt een bericht weer gegeven in plaats van de waarde van de hoofd query. |
| Bericht |Het bericht dat wordt weer gegeven als de query voor verificatie van de gegevens stroom geen gegevens retourneert. Als u geen bericht ontvangt, wordt een status bericht over de *beoordeling* weer gegeven. |


## <a name="line-chart-and-callout-tile"></a>Tegel met lijn diagrammen en bijschriften
Deze tegel heeft een lijn diagram dat meerdere reeksen uit een logboek query in de loop van de tijd en een toelichting met een samenvatte waarde weergeeft. 

![Scherm afbeelding van een lijn diagram en een bijschrift tegel in de ontwerp functie voor de Azure Monitor weergave. de toelichting breidt het lijn diagram uit door een samenvatte waarde weer te geven.](media/view-designer-tiles/tile-line-chart-callout.png)

| Instelling | Beschrijving |
|:--- |:--- |
| Name |De tekst die boven aan de tegel wordt weer gegeven. |
| Beschrijving |De tekst die wordt weer gegeven onder de naam van de tegel. |
| **Lijn diagram** | |
| Query’s uitvoeren |De query die wordt uitgevoerd voor het lijn diagram. De eerste eigenschap is een tekst waarde en de tweede eigenschap is een numerieke waarde. Deze query gebruikt normaal gesp roken het sleutel woord *Measure* om de resultaten samen te vatten. Als de query het gereserveerde woord *interval* gebruikt, gebruikt de x-as dit tijds interval. Als de query geen gebruik maakt van het *interval* sleutelwoord, gebruikt de x-as uur intervallen. |
| **Lijn diagram** |**> bijschrift** |
| Titel van bijschrift | De tekst die boven de waarde van de toelichting wordt weer gegeven. |
| Reeks naam |De waarde van de reeks eigenschap die moet worden gebruikt als de waarde van de toelichting. Als er geen reeks wordt gegeven, worden alle records uit de query gebruikt. |
| Bewerking |De bewerking die wordt uitgevoerd voor de eigenschap Value om deze samen te vatten als één waarde voor de toelichting.<ul><li>Gemiddelde: het gemiddelde van de waarden van alle records.</li><li>Aantal: het aantal records dat door de query wordt geretourneerd.</li><li>Laatste voor beeld: de waarde van het laatste interval dat is opgenomen in de grafiek.</li><li>Max: de maximum waarde van de intervallen die in de grafiek zijn opgenomen.</li><li>Min: de minimum waarde van de intervallen die in de grafiek zijn opgenomen.</li><li>Sum: de som van de waarden van alle records.</li></ul> |
| **Lijn diagram** |**> Y-as** |
| Logaritmische schaal gebruiken |Selecteer deze koppeling om een logaritmische schaal voor de y-as te gebruiken. |
| Eenheden |Geef de eenheden op voor de waarden die door de query moeten worden geretourneerd. Deze informatie wordt gebruikt om grafieklabels weer te geven die de waardetypen aangeven en, eventueel, om de waarden te converteren. Het *eenheids* type specificeert de categorie van de eenheid en definieert de beschik bare waarden van het *huidige eenheids* type. Als u een waarde selecteert in *converteren naar*, worden de numerieke waarden geconverteerd van het *huidige eenheids* type naar het type *converteren naar* . |
| Aangepast label |De tekst die wordt weer gegeven voor de y-as naast het label voor het *eenheids* type. Als er geen label is opgegeven, wordt alleen het *eenheids* type weer gegeven. |
| **Geavanceerd** |**Verificatie van de gegevens stroom>** |
| Ingeschakeld |Selecteer deze koppeling als verificatie van de gegevens stroom moet worden ingeschakeld voor de tegel. Deze methode biedt een alternatief bericht als er geen gegevens beschikbaar zijn. Normaal gesp roken gebruikt u de methode om een bericht op te geven tijdens de tijdelijke periode wanneer de weer gave is geïnstalleerd en de gegevens beschikbaar worden. |
| Query’s uitvoeren |De query die wordt uitgevoerd om te bepalen of er gegevens beschikbaar zijn voor de weer gave. Als de query geen resultaten retourneert, wordt een bericht weer gegeven in plaats van de waarde van de hoofd query. |
| Bericht |Het bericht dat wordt weer gegeven als de query voor verificatie van de gegevens stroom geen gegevens retourneert. Als u geen bericht ontvangt, wordt een status bericht over de *beoordeling* weer gegeven. |


## <a name="two-timelines-tile"></a>Tegel twee tijd lijnen
In de tegel **twee tijd lijnen** worden de resultaten van twee logboek query's in de loop van de tijd weer gegeven als kolom diagrammen. Voor elke reeks wordt een toelichting weer gegeven. 

![Tegel twee tijd lijnen](media/view-designer-tiles/tile-two-timelines.png)

| Instelling | Beschrijving |
|:--- |:--- |
| Name |De tekst die boven aan de tegel wordt weer gegeven. |
| Beschrijving |De tekst die wordt weer gegeven onder de naam van de tegel. |
| Eerste grafiek | |
| Legenda |De tekst die wordt weer gegeven onder de toelichting voor de eerste reeks. |
| Kleur |De kleur die wordt gebruikt voor de kolommen in de eerste reeks. |
| Grafiek query |De query die wordt uitgevoerd voor de eerste reeks. Het aantal records voor elk tijds interval wordt weer gegeven in de grafiek kolommen. |
| Bewerking |De bewerking die wordt uitgevoerd voor de eigenschap Value om deze samen te vatten als één waarde voor de toelichting.<ul><li>Gemiddelde: het gemiddelde van de waarden van alle records.</li><li>Aantal: het aantal records dat door de query wordt geretourneerd.</li><li>Laatste voor beeld: de waarde van het laatste interval dat is opgenomen in de grafiek.</li><li>Max: de maximum waarde van de intervallen die in de grafiek zijn opgenomen.</li></ul> |
| **Tweede grafiek** | |
| Legenda |De tekst die wordt weer gegeven onder de toelichting voor de tweede reeks. |
| Kleur |De kleur die wordt gebruikt voor de kolommen in de tweede reeks. |
| Grafiek query |De query die wordt uitgevoerd voor de tweede reeks. Het aantal records voor elk tijds interval wordt weer gegeven in de grafiek kolommen. |
| Bewerking |De bewerking die wordt uitgevoerd voor de eigenschap Value om deze samen te vatten als één waarde voor de toelichting.<ul><li>Gemiddelde: het gemiddelde van de waarden van alle records.</li><li>Aantal: het aantal records dat door de query wordt geretourneerd.</li><li>Laatste voor beeld: de waarde van het laatste interval dat is opgenomen in de grafiek.</li><li>Max: de maximum waarde van de intervallen die in de grafiek zijn opgenomen. |
| **Geavanceerd** |**Verificatie van de gegevens stroom>** |
| Ingeschakeld |Selecteer deze koppeling als verificatie van de gegevens stroom moet worden ingeschakeld voor de tegel. Deze methode biedt een alternatief bericht als er geen gegevens beschikbaar zijn. Normaal gesp roken gebruikt u de methode om een bericht op te geven tijdens de tijdelijke periode wanneer de weer gave is geïnstalleerd en de gegevens beschikbaar worden. |
| Query’s uitvoeren |De query die wordt uitgevoerd om te bepalen of er gegevens beschikbaar zijn voor de weer gave. Als de query geen resultaten retourneert, wordt een bericht weer gegeven in plaats van de waarde van de hoofd query. |
| Bericht |Het bericht dat wordt weer gegeven als de query voor verificatie van de gegevens stroom geen gegevens retourneert. Als u geen bericht ontvangt, wordt een status bericht over de *beoordeling* weer gegeven. |


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [logboek query's](../logs/log-query-overview.md) voor de ondersteuning van query's in tegels.
* [Visualisatie onderdelen](view-designer-parts.md) toevoegen aan uw aangepaste weer gave.
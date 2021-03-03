---
title: Een referentie gids voor de onderdelen van de weergave ontwerper in Azure Monitor | Microsoft Docs
description: Met behulp van View designer in Azure Monitor kunt u aangepaste weer gaven maken die worden weer gegeven in de Azure Portal en een verscheidenheid aan visualisaties op gegevens in de werk ruimte Log Analytics bevatten. Dit artikel bevat een Naslag Gids voor de instellingen voor de visualisatie onderdelen die beschikbaar zijn in uw aangepaste weer gaven.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2018
ms.openlocfilehash: 074c5dda8ff8d5f837e8ea2a85c183c69421bedb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710869"
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-azure-monitor"></a>Naslag Gids voor het weer geven van visualisatie onderdelen van designer in Azure Monitor
Met behulp van View designer in Azure Monitor kunt u verschillende aangepaste weer gaven maken in de Azure Portal die u kan helpen bij het visualiseren van gegevens in uw Log Analytics-werk ruimte. Dit artikel bevat een Naslag Gids voor de instellingen voor de visualisatie onderdelen die beschikbaar zijn in uw aangepaste weer gaven.

Zie voor meer informatie over de ontwerp functie voor weer gaven:

* [Weer gave Designer](view-designer.md): bevat een overzicht van de ontwerp weergave en procedures voor het maken en bewerken van aangepaste weer gaven.
* [Tegel verwijzing](view-designer-tiles.md): hierin vindt u een verwijzing naar de instellingen voor elke beschik bare tegel in uw aangepaste weer gaven.


De beschik bare tegel typen voor weergave ontwerp worden beschreven in de volgende tabel:

| Weergave type | Beschrijving |
|:--- |:--- |
| [Lijst met query's](#list-of-queries-part) |Hiermee wordt een lijst met logboek query's weer gegeven. U kunt elke query selecteren om de resultaten weer te geven. |
| [Nummer en lijst](#number-and-list-part) |In de koptekst wordt één getal weer gegeven dat een aantal records uit een logboek query weergeeft. In de lijst worden de tien belangrijkste resultaten van een query weer gegeven, met een grafiek die de relatieve waarde van een numerieke kolom aangeeft of de wijziging in de loop van de tijd. |
| [Twee getallen en lijst](#two-numbers-and-list-part) |De header bevat twee getallen die het aantal records van afzonderlijke logboek query's weer geven. In de lijst worden de tien belangrijkste resultaten van een query weer gegeven, met een grafiek die de relatieve waarde van een numerieke kolom aangeeft of de wijziging in de loop van de tijd. |
| [Ring en lijst](#donut-and-list-part) |In de koptekst wordt één getal weer gegeven dat een kolom waarde in een logboek query samenvatten. In de ring worden de resultaten van de bovenste drie records grafisch weer gegeven. |
| [Twee tijd lijnen en lijst](#two-timelines-and-list-part) |In de koptekst worden de resultaten van twee logboek query's gedurende een bepaalde tijd weer gegeven als kolom diagram, met een toelichting waarin een enkel getal wordt weer gegeven dat een kolom waarde in een logboek query samenvatten. In de lijst worden de tien belangrijkste resultaten van een query weer gegeven, met een grafiek die de relatieve waarde van een numerieke kolom aangeeft of de wijziging in de loop van de tijd. |
| [Informatie](#information-part) |De header bevat statische tekst en een optionele koppeling. In de lijst worden een of meer items met een statische titel en tekst weer gegeven. |
| [Lijn diagram, bijschrift en lijst](#line-chart-callout-and-list-part) |In de koptekst wordt een lijn diagram weer gegeven, met meerdere reeksen uit een logboek query gedurende een bepaalde periode en een toelichting met een samenvatte waarde. In de lijst worden de tien belangrijkste resultaten van een query weer gegeven, met een grafiek die de relatieve waarde van een numerieke kolom aangeeft of de wijziging in de loop van de tijd. |
| [Lijn diagram en lijst](#line-chart-and-list-part) |In de koptekst wordt een lijn diagram met meerdere reeksen van een logboek query in de loop van de tijd weer gegeven. In de lijst worden de tien belangrijkste resultaten van een query weer gegeven, met een grafiek die de relatieve waarde van een numerieke kolom aangeeft of de wijziging in de loop van de tijd. |
| [Onderdeel van lijn diagrammen](#stack-of-line-charts-part) |Hiermee worden drie afzonderlijke lijn diagrammen weer gegeven, met meerdere reeksen uit een logboek query gedurende een bepaalde periode. |

In de volgende secties worden de tegel typen en de bijbehorende eigenschappen uitvoerig beschreven.

> [!NOTE]
> Onderdelen in weer gaven zijn gebaseerd op [logboek query's](../logs/log-query-overview.md) in uw log Analytics-werk ruimte. Dit biedt momenteel geen ondersteuning voor [Cross-resource query's](../logs/cross-workspace-query.md) om gegevens op te halen uit Application Insights.

## <a name="list-of-queries-part"></a>Lijst met query onderdelen
In de lijst met onderdeel query's wordt een lijst met logboek query's weer gegeven. U kunt elke query selecteren om de resultaten weer te geven. De weer gave bevat standaard één query en u kunt **+ query** selecteren om extra query's toe te voegen.

![Scherm afbeelding van het visualisatie deel van de lijst met query's in de ontwerp functie voor weer gaven van Azure Monitor.](media/view-designer-parts/view-list-queries.png)

| Instelling | Beschrijving |
|:--- |:--- |
| **Algemeen** | |
| Titel |De tekst die boven aan de weer gave wordt weer gegeven. |
| Nieuwe groep |Selecteer deze koppeling om een nieuwe groep te maken in de weer gave, beginnend bij de huidige weer gave. |
| Vooraf geselecteerde filters |Een door komma's gescheiden lijst met eigenschappen die u wilt opnemen in het filter deel venster links wanneer u een query selecteert. |
| Weergave modus |De eerste weer gave die wordt weer gegeven wanneer de query wordt geselecteerd. U kunt alle beschik bare weer gaven selecteren nadat u de query hebt geopend. |
| **Query's** | |
| Zoekquery |De query die moet worden uitgevoerd. |
| Beschrijvende naam | De beschrijvende naam die wordt weer gegeven. |

## <a name="number-and-list-part"></a>Nummer en lijst onderdeel
In de koptekst wordt één getal weer gegeven dat een aantal records uit een logboek query weergeeft. In de lijst worden de tien belangrijkste resultaten van een query weer gegeven, met een grafiek die de relatieve waarde van een numerieke kolom aangeeft of de wijziging in de loop van de tijd.

![Scherm opname van het aantal en de weer gave van visualisatie onderdelen in de ontwerp functie voor de Azure Monitor weergave.](media/view-designer-parts/view-number-list.png)

| Instelling | Beschrijving |
|:--- |:--- |
| **Algemeen** | |
| Groeps titel |De tekst die boven aan de weer gave wordt weer gegeven. |
| Nieuwe groep |Selecteer deze koppeling om een nieuwe groep te maken in de weer gave, beginnend bij de huidige weer gave. |
| Pictogram |Het afbeeldings bestand dat naast het resultaat in de koptekst wordt weer gegeven. |
| Pictogram gebruiken |Selecteer deze koppeling om het pictogram weer te geven. |
| **Titel** | |
| Legenda |De tekst die boven aan de koptekst wordt weer gegeven. |
| Query’s uitvoeren |De query die moet worden uitgevoerd voor de koptekst. Het aantal records dat door de query wordt geretourneerd, wordt weer gegeven. |
| Navigatie door klikken | Actie die wordt uitgevoerd wanneer u op de koptekst klikt.  Zie [common Settings](#click-through-navigation)(Engelstalig) voor meer informatie. |
| **List** | |
| Query’s uitvoeren |De query die moet worden uitgevoerd voor de lijst. De eerste twee eigenschappen voor de eerste tien records in de resultaten worden weer gegeven. De eerste eigenschap is een tekst waarde en de tweede eigenschap is een numerieke waarde. Balken worden automatisch gemaakt die zijn gebaseerd op de relatieve waarde van de numerieke kolom.<br><br>Gebruik de `Sort` opdracht in de query om de records in de lijst te sorteren. Als u de query wilt uitvoeren en alle records wilt retour neren, kunt u **alles weer geven** selecteren. |
| Grafiek verbergen |Selecteer deze koppeling om de grafiek rechts van de numerieke kolom uit te scha kelen. |
| Sparklines inschakelen |Selecteer deze koppeling om een sparkline weer te geven in plaats van een horizontale balk. Zie [common Settings](#sparklines)(Engelstalig) voor meer informatie. |
| Kleur |De kleur van de balken of sparklines. |
| Naam en waarde scheidings teken |Het scheidings teken voor één teken dat moet worden gebruikt voor het parseren van de eigenschap text in meerdere waarden. Zie [common Settings](#sparklines)(Engelstalig) voor meer informatie. |
| Navigatie door klikken | Actie die wordt uitgevoerd wanneer u op een item in de lijst klikt.  Zie [common Settings](#click-through-navigation)(Engelstalig) voor meer informatie. |
| **List** |**> kolom titels** |
| Name |De tekst die boven aan de eerste kolom wordt weer gegeven. |
| Waarde |De tekst die boven aan de tweede kolom wordt weer gegeven. |
| **List** |**> drempel waarden** |
| Drempel waarden inschakelen |Selecteer deze koppeling om drempels in te scha kelen. Zie [common Settings](#thresholds)(Engelstalig) voor meer informatie. |

## <a name="two-numbers-and-list-part"></a>Twee getallen en een lijst onderdeel
De header heeft twee getallen die een telling van records uit afzonderlijke logboek query's weer geven. In de lijst worden de tien belangrijkste resultaten van een query weer gegeven, met een grafiek die de relatieve waarde van een numerieke kolom aangeeft of de wijziging in de loop van de tijd.

![Twee getallen & lijst weergave](media/view-designer-parts/view-two-numbers-list.png)

| Instelling | Beschrijving |
|:--- |:--- |
| **Algemeen** | |
| Groeps titel |De tekst die boven aan de weer gave wordt weer gegeven. |
| Nieuwe groep |Selecteer deze koppeling om een nieuwe groep te maken in de weer gave, beginnend bij de huidige weer gave. |
| Pictogram |Het afbeeldings bestand dat naast het resultaat in de koptekst wordt weer gegeven. |
| Pictogram gebruiken |Selecteer deze koppeling om het pictogram weer te geven. |
| **Titel navigatie** | |
| Navigatie door klikken | Actie die wordt uitgevoerd wanneer u op de koptekst klikt.  Zie [common Settings](#click-through-navigation)(Engelstalig) voor meer informatie. |
| **Titel** | |
| Legenda |De tekst die boven aan de koptekst wordt weer gegeven. |
| Query’s uitvoeren |De query die moet worden uitgevoerd voor de koptekst. Het aantal records dat door de query wordt geretourneerd, wordt weer gegeven. |
| **List** | |
| Query’s uitvoeren |De query die moet worden uitgevoerd voor de lijst. De eerste twee eigenschappen voor de eerste tien records in de resultaten worden weer gegeven. De eerste eigenschap is een tekst waarde en de tweede eigenschap is een numerieke waarde. Balken worden automatisch gemaakt op basis van de relatieve waarde van de numerieke kolom.<br><br>Gebruik de `Sort` opdracht in de query om de records in de lijst te sorteren. Als u de query wilt uitvoeren en alle records wilt retour neren, kunt u **alles weer geven** selecteren. |
| Grafiek verbergen |Selecteer deze koppeling om de grafiek rechts van de numerieke kolom uit te scha kelen. |
| Sparklines inschakelen |Selecteer deze koppeling om een sparkline weer te geven in plaats van een horizontale balk. Zie [common Settings](#sparklines)(Engelstalig) voor meer informatie. |
| Kleur |De kleur van de balken of sparklines. |
| Bewerking |De bewerking die moet worden uitgevoerd voor de sparkline. Zie [common Settings](#sparklines)(Engelstalig) voor meer informatie. |
| Naam en waarde scheidings teken |Het scheidings teken voor één teken dat moet worden gebruikt voor het parseren van de eigenschap text in meerdere waarden. Zie [common Settings](#sparklines)(Engelstalig) voor meer informatie. |
| Navigatie door klikken | Actie die wordt uitgevoerd wanneer u op een item in de lijst klikt.  Zie [common Settings](#click-through-navigation)(Engelstalig) voor meer informatie. |
| **List** |**> kolom titels** |
| Name |De tekst die boven aan de eerste kolom wordt weer gegeven. |
| Waarde |De tekst die boven aan de tweede kolom wordt weer gegeven. |
| **List** |**> drempel waarden** |
| Drempel waarden inschakelen |Selecteer deze koppeling om drempels in te scha kelen. Zie [common Settings](#thresholds)(Engelstalig) voor meer informatie. |

## <a name="donut-and-list-part"></a>Ring en lijst gedeelte
In de koptekst wordt één getal weer gegeven dat een kolom waarde in een logboek query samenvatten. In de ring worden de resultaten van de bovenste drie records grafisch weer gegeven.

![Ring-en lijst weergave](media/view-designer-parts/view-donut-list.png)

| Instelling | Beschrijving |
|:--- |:--- |
| **Algemeen** | |
| Groeps titel |De tekst die boven aan de tegel wordt weer gegeven. |
| Nieuwe groep |Selecteer deze koppeling om een nieuwe groep te maken in de weer gave, beginnend bij de huidige weer gave. |
| Pictogram |Het afbeeldings bestand dat naast het resultaat in de koptekst wordt weer gegeven. |
| Pictogram gebruiken |Selecteer deze koppeling om het pictogram weer te geven. |
| **Header** | |
| Titel |De tekst die boven aan de koptekst wordt weer gegeven. |
| Subtitel |De tekst die wordt weer gegeven onder de titel boven aan de koptekst. |
| **Ringdiagram** | |
| Query’s uitvoeren |De query die moet worden uitgevoerd voor de ring. De eerste eigenschap is een tekst waarde en de tweede eigenschap is een numerieke waarde. |
| Navigatie door klikken | Actie die wordt uitgevoerd wanneer u op de koptekst klikt.  Zie [common Settings](#click-through-navigation)(Engelstalig) voor meer informatie. |
| **Ringdiagram** |**> Center** |
| Tekst |De tekst die wordt weer gegeven onder de waarde in de ring. |
| Bewerking |De bewerking die moet worden uitgevoerd op de eigenschap Value om deze als één waarde samen te vatten.<ul><li>Sum: de waarden van alle records worden toegevoegd.</li><li>Percentage: de verhouding van de records die worden geretourneerd door de waarden in **resultaat waarden die in de middelste bewerking worden gebruikt** voor het totaal aantal records in de query.</li></ul> |
| Resultaat waarden die worden gebruikt in de middelste bewerking |Selecteer desgewenst het plus teken (+) om een of meer waarden toe te voegen. De resultaten van de query zijn beperkt tot records met de eigenschaps waarden die u opgeeft. Als er geen waarden worden toegevoegd, worden alle records opgenomen in de query. |
| **Extra opties** |**> kleuren** |
| Kleur 1<br>Kleur 2<br>Kleur 3 |Selecteer de kleur voor elk van de waarden die worden weer gegeven in de ring. |
| **Extra opties** |**Geavanceerde kleur toewijzing>** |
| Veldwaarde |Typ de naam van een veld om dit weer te geven als een andere kleur als deze is opgenomen in de ring. |
| Kleur |Selecteer de kleur voor het unieke veld. |
| **List** | |
| Query’s uitvoeren |De query die moet worden uitgevoerd voor de lijst. Het aantal records dat door de query wordt geretourneerd, wordt weer gegeven. |
| Grafiek verbergen |Selecteer deze koppeling om de grafiek rechts van de numerieke kolom uit te scha kelen. |
| Sparklines inschakelen |Selecteer deze koppeling om een sparkline weer te geven in plaats van een horizontale balk. Zie [common Settings](#sparklines)(Engelstalig) voor meer informatie. |
| Kleur |De kleur van de balken of sparklines. |
| Bewerking |De bewerking die moet worden uitgevoerd voor de sparkline. Zie [common Settings](#sparklines)(Engelstalig) voor meer informatie. |
| Naam en waarde scheidings teken |Het scheidings teken voor één teken dat moet worden gebruikt voor het parseren van de eigenschap text in meerdere waarden. Zie [common Settings](#sparklines)(Engelstalig) voor meer informatie. |
| Navigatie door klikken | Actie die wordt uitgevoerd wanneer u op een item in de lijst klikt.  Zie [common Settings](#click-through-navigation)(Engelstalig) voor meer informatie. |
| **List** |**> kolom titels** |
| Name |De tekst die boven aan de eerste kolom wordt weer gegeven. |
| Waarde |De tekst die boven aan de tweede kolom wordt weer gegeven. |
| **List** |**> drempel waarden** |
| Drempel waarden inschakelen |Selecteer deze koppeling om drempels in te scha kelen. Zie [common Settings](#thresholds)(Engelstalig) voor meer informatie. |

## <a name="two-timelines-and-list-part"></a>Twee tijd lijnen en lijst onderdeel
In de koptekst worden de resultaten van twee logboek query's gedurende een bepaalde tijd weer gegeven als kolom diagram, met een toelichting waarin een enkel getal wordt weer gegeven dat een kolom waarde in een logboek query samenvatten. In de lijst worden de tien belangrijkste resultaten van een query weer gegeven, met een grafiek die de relatieve waarde van een numerieke kolom aangeeft of de wijziging in de loop van de tijd.

![Twee tijd lijnen en lijst weergave](media/view-designer-parts/view-two-timelines-list.png)

| Instelling | Beschrijving |
|:--- |:--- |
| **Algemeen** | |
| Groeps titel |De tekst die boven aan de tegel wordt weer gegeven. |
| Nieuwe groep |Selecteer deze koppeling om een nieuwe groep te maken in de weer gave, beginnend bij de huidige weer gave. |
| Pictogram |Het afbeeldings bestand dat naast het resultaat in de koptekst wordt weer gegeven. |
| Pictogram gebruiken |Selecteer deze koppeling om het pictogram weer te geven. |
| **Titel navigatie** | |
| Navigatie door klikken | Actie die wordt uitgevoerd wanneer u op de koptekst klikt.  Zie [common Settings](#click-through-navigation)(Engelstalig) voor meer informatie. |
| **Eerste grafiek <br> tweede grafiek** | |
| Legenda |De tekst die wordt weer gegeven onder de toelichting voor de eerste reeks. |
| Kleur |De kleur die moet worden gebruikt voor de kolommen in de reeks. |
| Query’s uitvoeren |De query die moet worden uitgevoerd voor de eerste reeks. Het aantal records voor elk tijds interval wordt weer gegeven in de grafiek kolommen. |
| Bewerking |De bewerking die moet worden uitgevoerd op de eigenschap Value om deze samen te vatten als één waarde voor de toelichting.<ul><li>Sum: de som van de waarden van alle records.</li><li>Gemiddelde: het gemiddelde van de waarden van alle records.</li><li>Laatste voor beeld: de waarde van het laatste interval dat is opgenomen in de grafiek.</li><li>Eerste voor beeld: de waarde van het eerste interval dat in de grafiek is opgenomen.</li><li>Aantal: het aantal records dat door de query wordt geretourneerd.</li></ul> |
| **List** | |
| Query’s uitvoeren |De query die moet worden uitgevoerd voor de lijst. Het aantal records dat door de query wordt geretourneerd, wordt weer gegeven. |
| Grafiek verbergen |Selecteer deze koppeling om de grafiek rechts van de numerieke kolom uit te scha kelen. |
| Sparklines inschakelen |Selecteer deze koppeling om een sparkline weer te geven in plaats van een horizontale balk. Zie [common Settings](#sparklines)(Engelstalig) voor meer informatie. |
| Kleur |De kleur van de balken of sparklines. |
| Bewerking |De bewerking die moet worden uitgevoerd voor de sparkline. Zie [common Settings](#sparklines)(Engelstalig) voor meer informatie. |
| Navigatie door klikken | Actie die wordt uitgevoerd wanneer u op een item in de lijst klikt.  Zie [common Settings](#click-through-navigation)(Engelstalig) voor meer informatie. |
| **List** |**> kolom titels** |
| Name |De tekst die boven aan de eerste kolom wordt weer gegeven. |
| Waarde |De tekst die boven aan de tweede kolom wordt weer gegeven. |
| **List** |**> drempel waarden** |
| Drempel waarden inschakelen |Selecteer deze koppeling om drempels in te scha kelen. Zie [common Settings](#thresholds)(Engelstalig) voor meer informatie. |

## <a name="information-part"></a>Informatie onderdeel
De header bevat statische tekst en een optionele koppeling. In de lijst worden een of meer items met een statische titel en tekst weer gegeven.

![Informatie weergave](media/view-designer-parts/view-information.png)

| Instelling | Beschrijving |
|:--- |:--- |
| **Algemeen** | |
| Groeps titel |De tekst die boven aan de tegel wordt weer gegeven. |
| Nieuwe groep |Selecteer deze koppeling om een nieuwe groep te maken in de weer gave, beginnend bij de huidige weer gave. |
| Kleur |De achtergrond kleur voor de koptekst. |
| **Header** | |
| Installatiekopie |Het afbeeldings bestand dat wordt weer gegeven in de koptekst. |
| Label |De tekst die wordt weer gegeven in de koptekst. |
| **Header** |**Koppeling>** |
| Label |De tekst van de koppeling. |
| URL |De URL voor de koppeling. |
| **Gegevens items** | |
| Titel |De tekst die wordt weer gegeven voor de titel van elk item. |
| Content |De tekst die voor elk item wordt weer gegeven. |

## <a name="line-chart-callout-and-list-part"></a>Lijn diagram, bijschrift en lijst gedeelte
In de koptekst wordt een lijn diagram met meerdere reeksen uit een logboek query in de loop van de tijd en een toelichting met een samenvatte waarde weer gegeven. In de lijst worden de tien belangrijkste resultaten van een query weer gegeven, met een grafiek die de relatieve waarde van een numerieke kolom aangeeft of de wijziging in de loop van de tijd.

![Lijn diagram, bijschrift en lijst weergave](media/view-designer-parts/view-line-chart-callout-list.png)

| Instelling | Beschrijving |
|:--- |:--- |
| **Algemeen** | |
| Groeps titel |De tekst die boven aan de tegel wordt weer gegeven. |
| Nieuwe groep |Selecteer deze koppeling om een nieuwe groep te maken in de weer gave, beginnend bij de huidige weer gave. |
| Pictogram |Het afbeeldings bestand dat naast het resultaat in de koptekst wordt weer gegeven. |
| Pictogram gebruiken |Selecteer deze koppeling om het pictogram weer te geven. |
| **Header** | |
| Titel |De tekst die boven aan de koptekst wordt weer gegeven. |
| Subtitel |De tekst die wordt weer gegeven onder de titel boven aan de koptekst. |
| **Lijn diagram** | |
| Query’s uitvoeren |De query die moet worden uitgevoerd voor het lijn diagram. De eerste eigenschap is een tekst waarde en de tweede eigenschap is een numerieke waarde. Deze query gebruikt normaal gesp roken het sleutel woord *Measure* om de resultaten samen te vatten. Als de query het gereserveerde woord *interval* gebruikt, gebruikt de x-as van de grafiek dit tijds interval. Als de query het gereserveerde woord *interval* niet bevat, gebruikt de x-as uur intervallen. |
| Navigatie door klikken | Actie die wordt uitgevoerd wanneer u op de koptekst klikt.  Zie [common Settings](#click-through-navigation)(Engelstalig) voor meer informatie. |
| **Lijn diagram** |**> bijschrift** |
| Titel van bijschrift |De tekst die boven de waarde van de toelichting wordt weer gegeven. |
| Reeks naam |Eigenschaps waarde voor de reeks die moet worden gebruikt voor de waarde van de toelichting. Als er geen reeks wordt gegeven, worden alle records uit de query gebruikt. |
| Bewerking |De bewerking die moet worden uitgevoerd op de eigenschap Value om deze samen te vatten als één waarde voor de toelichting.<ul><li>Gemiddelde: het gemiddelde van de waarden van alle records.</li><li>Aantal: het aantal records dat door de query wordt geretourneerd.</li><li>Laatste voor beeld: de waarde van het laatste interval dat is opgenomen in de grafiek.</li><li>Max: de maximum waarde van de intervallen die in de grafiek zijn opgenomen.</li><li>Min: de minimum waarde van de intervallen die in de grafiek zijn opgenomen.</li><li>Sum: de som van de waarden van alle records.</li></ul> |
| **Lijn diagram** |**> Y-as** |
| Logaritmische schaal gebruiken |Selecteer deze koppeling om een logaritmische schaal voor de y-as te gebruiken. |
| Eenheden |Geef de eenheden op voor de waarden die door de query moeten worden geretourneerd. Deze informatie wordt gebruikt om grafieklabels weer te geven die de waardetypen aangeven en, eventueel, om de waarden te converteren. Het *eenheids* type specificeert de categorie van de eenheid en definieert de beschik bare waarden van het *huidige eenheids* type. Als u een waarde selecteert in *converteren naar*, worden de numerieke waarden geconverteerd van het *huidige eenheids* type naar het type *converteren naar* . |
| Aangepast label |De tekst die wordt weer gegeven voor de y-as naast het label voor het *eenheids* type. Als er geen label is opgegeven, wordt alleen het *eenheids* type weer gegeven. |
| **List** | |
| Query’s uitvoeren |De query die moet worden uitgevoerd voor de lijst. Het aantal records dat door de query wordt geretourneerd, wordt weer gegeven. |
| Grafiek verbergen |Selecteer deze koppeling om de grafiek rechts van de numerieke kolom uit te scha kelen. |
| Sparklines inschakelen |Selecteer deze koppeling om een sparkline weer te geven in plaats van een horizontale balk. Zie [common Settings](#sparklines)(Engelstalig) voor meer informatie. |
| Kleur |De kleur van de balken of sparklines. |
| Bewerking |De bewerking die moet worden uitgevoerd voor de sparkline. Zie [common Settings](#sparklines)(Engelstalig) voor meer informatie. |
| Naam en waarde scheidings teken |Het scheidings teken voor één teken dat moet worden gebruikt voor het parseren van de eigenschap text in meerdere waarden. Zie [common Settings](#sparklines)(Engelstalig) voor meer informatie. |
| Navigatie door klikken | Actie die wordt uitgevoerd wanneer u op een item in de lijst klikt.  Zie [common Settings](#click-through-navigation)(Engelstalig) voor meer informatie. |
| **List** |**> kolom titels** |
| Name |De tekst die boven aan de eerste kolom wordt weer gegeven. |
| Waarde |De tekst die boven aan de tweede kolom wordt weer gegeven. |
| **List** |**> drempel waarden** |
| Drempel waarden inschakelen |Selecteer deze koppeling om drempels in te scha kelen. Zie [common Settings](#thresholds)(Engelstalig) voor meer informatie. |

## <a name="line-chart-and-list-part"></a>Lijn diagram en lijst onderdeel
In de koptekst wordt een lijn diagram met meerdere reeksen uit een logboek query in de loop van de tijd weer gegeven. In de lijst worden de tien belangrijkste resultaten van een query weer gegeven, met een grafiek die de relatieve waarde van een numerieke kolom aangeeft of de wijziging in de loop van de tijd.

![Lijn diagram en lijst weergave](media/view-designer-parts/view-line-chart-callout-list.png)

| Instelling | Beschrijving |
|:--- |:--- |
| **Algemeen** | |
| Groeps titel |De tekst die boven aan de tegel wordt weer gegeven. |
| Nieuwe groep |Selecteer deze koppeling om een nieuwe groep te maken in de weer gave, beginnend bij de huidige weer gave. |
| Pictogram |Het afbeeldings bestand dat naast het resultaat in de koptekst wordt weer gegeven. |
| Pictogram gebruiken |Selecteer deze koppeling om het pictogram weer te geven. |
| **Header** | |
| Titel |De tekst die boven aan de koptekst wordt weer gegeven. |
| Subtitel |De tekst die wordt weer gegeven onder de titel boven aan de koptekst. |
| **Lijn diagram** | |
| Query’s uitvoeren |De query die moet worden uitgevoerd voor het lijn diagram. De eerste eigenschap is een tekst waarde en de tweede eigenschap is een numerieke waarde. Deze query gebruikt normaal gesp roken het sleutel woord *Measure* om de resultaten samen te vatten. Als de query het gereserveerde woord *interval* gebruikt, gebruikt de x-as van de grafiek dit tijds interval. Als de query het gereserveerde woord *interval* niet bevat, gebruikt de x-as uur intervallen. |
| Navigatie door klikken | Actie die wordt uitgevoerd wanneer u op de koptekst klikt.  Zie [common Settings](#click-through-navigation)(Engelstalig) voor meer informatie. |
| **Lijn diagram** |**> Y-as** |
| Logaritmische schaal gebruiken |Selecteer deze koppeling om een logaritmische schaal voor de y-as te gebruiken. |
| Eenheden |Geef de eenheden op voor de waarden die door de query moeten worden geretourneerd. Deze informatie wordt gebruikt om grafieklabels weer te geven die de waardetypen aangeven en, eventueel, om de waarden te converteren. Het *eenheids* type specificeert de categorie van de eenheid en definieert de beschik bare waarden van het *huidige eenheids* type. Als u een waarde selecteert in *converteren naar*, worden de numerieke waarden geconverteerd van het *huidige eenheids* type naar het type *converteren naar* . |
| Aangepast label |De tekst die wordt weer gegeven voor de y-as naast het label voor het *eenheids* type. Als er geen label is opgegeven, wordt alleen het *eenheids* type weer gegeven. |
| **List** | |
| Query’s uitvoeren |De query die moet worden uitgevoerd voor de lijst. Het aantal records dat door de query wordt geretourneerd, wordt weer gegeven. |
| Grafiek verbergen |Selecteer deze koppeling om de grafiek rechts van de numerieke kolom uit te scha kelen. |
| Sparklines inschakelen |Selecteer deze koppeling om een sparkline weer te geven in plaats van een horizontale balk. Zie [common Settings](#sparklines)(Engelstalig) voor meer informatie. |
| Kleur |De kleur van de balken of sparklines. |
| Bewerking |De bewerking die moet worden uitgevoerd voor de sparkline. Zie [common Settings](#sparklines)(Engelstalig) voor meer informatie. |
| Naam en waarde scheidings teken |Het scheidings teken voor één teken dat moet worden gebruikt voor het parseren van de eigenschap text in meerdere waarden. Zie [common Settings](#sparklines)(Engelstalig) voor meer informatie. |
| Navigatie door klikken | Actie die wordt uitgevoerd wanneer u op een item in de lijst klikt.  Zie [common Settings](#click-through-navigation)(Engelstalig) voor meer informatie. |
| **List** |**> kolom titels** |
| Name |De tekst die boven aan de eerste kolom wordt weer gegeven. |
| Waarde |De tekst die boven aan de tweede kolom wordt weer gegeven. |
| **List** |**> drempel waarden** |
| Drempel waarden inschakelen |Selecteer deze koppeling om drempels in te scha kelen. Zie [common Settings](#thresholds)(Engelstalig) voor meer informatie. |

## <a name="stack-of-line-charts-part"></a>Onderdeel van lijn diagrammen
Het stapel lijn diagram geeft drie afzonderlijke lijn diagrammen weer, met meerdere reeksen uit een logboek query gedurende een bepaalde periode, zoals hier wordt weer gegeven:

![Stapel lijn diagrammen](media/view-designer-parts/view-stack-line-charts.png)

| Instelling | Beschrijving |
|:--- |:--- |
| **Algemeen** | |
| Groeps titel |De tekst die boven aan de tegel wordt weer gegeven. |
| Nieuwe groep |Selecteer deze koppeling om een nieuwe groep te maken in de weer gave, beginnend bij de huidige weer gave. |
| Pictogram |Het afbeeldings bestand dat naast het resultaat in de koptekst wordt weer gegeven. |
| **Grafiek 1 grafiek <br> 2 <br> grafiek 3** |**> header** |
| Titel |De tekst die boven aan de grafiek wordt weer gegeven. |
| Subtitel |De tekst die wordt weer gegeven onder de titel boven aan de grafiek. |
| **Grafiek 1 grafiek <br> 2 <br> grafiek 3** |**Lijn diagram** |
| Query’s uitvoeren |De query die moet worden uitgevoerd voor het lijn diagram. De eerste eigenschap is een tekst waarde en de tweede eigenschap is een numerieke waarde. Deze query gebruikt normaal gesp roken het sleutel woord *Measure* om de resultaten samen te vatten. Als de query het gereserveerde woord *interval* gebruikt, gebruikt de x-as van de grafiek dit tijds interval. Als de query het gereserveerde woord *interval* niet bevat, gebruikt de x-as uur intervallen. |
| Navigatie door klikken | Actie die wordt uitgevoerd wanneer u op de koptekst klikt.  Zie [common Settings](#click-through-navigation)(Engelstalig) voor meer informatie. |
| **Grafiek** |**> Y-as** |
| Logaritmische schaal gebruiken |Selecteer deze koppeling om een logaritmische schaal voor de y-as te gebruiken. |
| Eenheden |Geef de eenheden op voor de waarden die door de query moeten worden geretourneerd. Deze informatie wordt gebruikt om grafieklabels weer te geven die de waardetypen aangeven en, eventueel, om de waarden te converteren. Het *eenheids* type specificeert de categorie van de eenheid en definieert de beschik bare waarden van het *huidige eenheids* type. Als u een waarde selecteert in *converteren naar*, worden de numerieke waarden geconverteerd van het *huidige eenheids* type naar het type *converteren naar* . |
| Aangepast label |De tekst die wordt weer gegeven voor de y-as naast het label voor het *eenheids* type. Als er geen label is opgegeven, wordt alleen het *eenheids* type weer gegeven. |

## <a name="common-settings"></a>Algemene instellingen
In de volgende secties worden de instellingen beschreven die gemeen schappelijk zijn voor verschillende visualisatie onderdelen.

### <a name="name-and-value-separator"></a><a name="name-value-separator"></a>Naam en waarde scheidings teken
De naam en het scheidings teken voor waarden is het scheidings teken voor één teken dat moet worden gebruikt voor het parseren van de tekst eigenschap van een lijst query in meerdere waarden. Als u een scheidings teken opgeeft, kunt u voor elk veld namen opgeven, gescheiden door hetzelfde scheidings teken in het vak **naam** .

Denk bijvoorbeeld aan een eigenschap met de naam *locatie* die waarden bevat, zoals *Redmond-gebouw 41* en *Bellevue-gebouw 12*. U kunt een streepje (-) opgeven voor de naam en het scheidings teken voor waarden en de *plaats* voor de naam. Deze aanpak parseert elke waarde in twee eigenschappen met de naam *City* en *buil ding*.

### <a name="click-through-navigation"></a><a name="click-through-navigation"></a>Navigatie door klikken
Door klikken en navigeren bepaalt welke actie wordt ondernomen wanneer u op een koptekst of lijst item in een weer gave klikt.  Hiermee opent u een query in de [log Analytics](../logs/log-query-overview.md) of start u een andere weer gave.

In de volgende tabel worden de instellingen voor door klik navigatie beschreven.

| Instelling           | Beschrijving |
|:--|:--|
| Zoeken in Logboeken (automatisch) | De logboek query die moet worden uitgevoerd wanneer u een koptekst item selecteert.  Dit is dezelfde logboek query waarop het item is gebaseerd.
| Zoeken in logboeken        | De logboek query die moet worden uitgevoerd wanneer u een item in een lijst selecteert.  Typ de query in het vak **Navigatie query** .   Gebruik *{selected item}* om de syntaxis op te neemt voor het item dat de gebruiker heeft geselecteerd.  Als de query bijvoorbeeld een kolom bevat met de naam *computer* en de navigatie query *{selected item}* is, wordt een query zoals *computer = "mijn systeem"* uitgevoerd wanneer u een computer selecteert. Als de navigatie query *type = gebeurtenis {geselecteerd item}* is, wordt het query *type = gebeurtenis computer = "mijn systeem"* uitgevoerd. |
| Weergave              | Weer geven om te openen wanneer u een koptekst item of een item in een lijst selecteert.  Selecteer de naam van een weer gave in uw werk ruimte in het vak **weergave naam** . |



### <a name="sparklines"></a><a name="sparklines"></a>Groeperen
Een sparkline is een klein lijn diagram dat de waarde van een vermelding in de loop van de tijd illustreert. Voor visualisatie-onderdelen met een lijst kunt u selecteren of u een horizontale balk wilt weer geven, waarmee de relatieve waarde van een numerieke kolom of een sparkline wordt aangegeven, wat de waarde in de loop van de tijd aangeeft.

In de volgende tabel worden de instellingen voor sparklines beschreven:

| Instelling | Beschrijving |
|:--- |:--- |
| Sparklines inschakelen |Selecteer deze koppeling om een sparkline weer te geven in plaats van een horizontale balk. |
| Bewerking |Als sparklines zijn ingeschakeld, is dit de bewerking die moet worden uitgevoerd op elke eigenschap in de lijst om de waarden voor de sparkline te berekenen.<ul><li>Laatste voor beeld: de laatste waarde voor de reeks gedurende het tijds interval.</li><li>Max: de maximum waarde voor de reeks gedurende het tijds interval.</li><li>Min: de minimum waarde voor de reeks gedurende het tijds interval.</li><li>Sum: de som van de waarden voor de reeks gedurende het tijds interval.</li><li>Samen vatting: gebruikt dezelfde `measure` opdracht als de query in de koptekst.</li></ul> |

### <a name="thresholds"></a><a name="thresholds"></a>Drempel waarden
Door drempel waarden te gebruiken, kunt u een gekleurd pictogram naast elk item in een lijst weer geven. Drempel waarden geven u een snelle visuele indicator van items die een bepaalde waarde overschrijden of binnen een bepaald bereik vallen. U kunt bijvoorbeeld een groen pictogram weer geven voor items met een acceptabele waarde, geel als de waarde binnen een bereik ligt dat een waarschuwing aangeeft en rood als deze een fout waarde overschrijdt.

Wanneer u drempel waarden voor een deel inschakelt, moet u een of meer drempel waarden opgeven. Als de waarde van een item groter is dan een drempel waarde en lager dan de volgende drempel waarde, wordt de kleur voor die waarde gebruikt. Als het item groter is dan de hoogste drempel waarde, wordt een andere kleur gebruikt. 

Elke drempelset heeft één drempel met de waarde **standaard**. Dit is de kleur die wordt ingesteld als geen andere waarden worden overschreden. U kunt drempels toevoegen of verwijderen door de knop **toevoegen** (+) of **verwijderen** (x) te selecteren.

De volgende tabel beschrijft de instellingen voor drempel waarden:

| Instelling | Beschrijving |
|:--- |:--- |
| Drempel waarden inschakelen |Selecteer deze koppeling om een kleur pictogram aan de linkerkant van elke waarde weer te geven. Het pictogram geeft de status van de waarde aan ten opzichte van de opgegeven drempel waarden. |
| Name |De naam van de drempel waarde. |
| Drempelwaarde |De waarde voor de drempelwaarde. De status van elk lijst item wordt ingesteld op de kleur van de hoogste drempel waarde die wordt overschreden door de waarde van het item. Als er geen drempel waarden worden overschreden, wordt een standaard kleur gebruikt. |
| Kleur |De kleur die de drempel waarde aangeeft. |

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [logboek query's](../logs/log-query-overview.md) voor het ondersteunen van query's in visualisatie onderdelen.
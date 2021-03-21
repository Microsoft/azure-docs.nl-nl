---
title: Azure Monitor werkmappen met aangepaste para meters
description: Vereenvoudig complexe rapportage met vooraf samengestelde en aangepaste werkmappen met para meters
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/20/2020
ms.openlocfilehash: 2cb284e1978ad6c890835318c51c6095891397cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101723007"
---
# <a name="interactive-workbooks"></a>Interactieve workbooks

Met werkmappen kunnen auteurs interactieve rapporten en ervaringen maken voor hun consumenten. Interactiviteit wordt op een aantal manieren ondersteund.

## <a name="parameter-changes"></a>Parameter wijzigingen

Wanneer een werkmap gebruiker een para meter bijwerkt, wordt elk besturings element dat gebruikmaakt van de para meter automatisch vernieuwd en opnieuw getekend om de nieuwe status weer te geven. Dit is hoe de meeste van de Azure Portal-rapporten interactiviteit ondersteunen. Werkmappen bieden dit op een directe, doorstuur manier met minimale gebruikers inspanning.

Meer informatie over [para meters in werkmappen](workbooks-parameters.md)

## <a name="grid-tile-chart-selections"></a>Raster, tegel, grafiek selecties

Met werkmappen kunnen auteurs scenario's maken waarbij de volgende grafieken worden bijgewerkt op basis van de inhoud van de rij door te klikken op een rij in een raster.

Een gebruiker kan bijvoorbeeld een raster hebben waarin een lijst met aanvragen wordt weer gegeven en enkele statistieken, zoals fout aantallen. Ze kunnen deze zo instellen dat er wordt geklikt op een rij die overeenkomt met een aanvraag, en resulteert in gedetailleerde grafieken onder bijwerken om naar alleen die aanvraag te filteren.

### <a name="setting-up-interactivity-on-grid-row-click"></a>De rij interactiviteit op raster instellen

1. Schakel de werkmap over naar de bewerkings modus door te klikken op het item werkbalk opdracht _bewerken_ .
2. Gebruik de koppeling _query toevoegen_ om een besturings element voor een logboek query toe te voegen aan de werkmap.
3. Selecteer het query type als _logboek_, resource type (bijvoorbeeld Application Insights) en de resources om het doel te bereiken.
4. De query-editor gebruiken om de KQL voor uw analyse in te voeren

    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc
    ```

5. `Run query` de resultaten weer geven
6. Selecteer het pictogram _Geavanceerde instellingen_ op de query voet tekst (het pictogram ziet eruit als een tand wiel). Hiermee opent u het deel venster Geavanceerde instellingen.
7. Controleer de instelling: `When an item is selected, export a parameter` .
8. Selecteer onder de instelling die u hebt geselecteerd *para meter toevoegen* en vul deze in met behulp van de onderstaande gegevens.
    1. Te exporteren veld: `Request`
    2. Parameter naam: `SelectedRequest`
    3. Standaard waarde: `All requests`
9. Opslaan selecteren

    ![Scherm afbeelding met de geavanceerde editor met instellingen voor het exporteren van velden als para meters.](./media/workbooks-interactive/export-parameters-add.png)

10. Selecteer `Done Editing`.
11. Voeg nog een besturings element query toe met behulp van stap 2 en 3.
12. Gebruik de query-editor om de KQL voor uw analyse in te voeren.
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
13. `Run query` om de resultaten te bekijken.
14. Wijzig de _visualisatie_ in `Area chart` .
15. Kies een rij om te selecteren in het eerste raster. U ziet hoe het vlak diagram onder filtert op de geselecteerde aanvraag.

Het resulterende rapport ziet er als volgt uit in de bewerkings modus:

![Scherm afbeelding van de eerste twee query's in de bewerkings modus.](./media/workbooks-interactive//interactivity-grid-create.png)

In de onderstaande afbeelding ziet u een meer uitgebreid interactief rapport in Lees modus op basis van dezelfde principes. Het rapport gebruikt raster klikken om para meters te exporteren, die op zijn beurt worden gebruikt in twee grafieken en een tekst blok.

![Scherm afbeelding van een rapport met raster klikken in Lees modus.](./media/workbooks-interactive/interactivity-grid-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>De inhoud van een hele rij exporteren

Soms is het wenselijk om de volledige inhoud van de geselecteerde rij te exporteren in plaats van alleen een bepaalde kolom. In dergelijke gevallen moet u de `Field to export` eigenschap in stap 7,1 hierboven uitschakelen. Werkmappen exporteert de volledige celinhoud als een JSON-bestand naar de para meter.

Gebruik op het verwijzende besturings element KQL de `todynamic` functie om de JSON te parseren en toegang te krijgen tot de afzonderlijke kolommen.

## <a name="grid-cell-clicks"></a>Klikken raster cellen

Met werkmappen kunnen auteurs interactiviteit toevoegen via een speciaal type raster kolom weergave met de naam een `link renderer` . Met een koppelings weergave wordt een grid-cel omgezet in een Hyper link op basis van de inhoud van de cel. Werkmappen ondersteunen veel soorten koppelings beeldrenderings, inclusief de Blades voor het openen van resource overzichten, viewers van eigenschappen, app Insights Search, Usage, trans actie tracering, enzovoort.

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>Interactiviteit instellen met behulp van een raster muis klik

1. Schakel de werkmap over naar de bewerkings modus door te klikken op het item werkbalk opdracht _bewerken_ .
2. Gebruik de koppeling _query toevoegen_ om een besturings element voor een logboek query toe te voegen aan de werkmap.
3. Selecteer het query type als _logboek_, resource type (bijvoorbeeld Application Insights) en de resources om het doel te bereiken.
4. De query-editor gebruiken om de KQL voor uw analyse in te voeren

    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```

5. `Run query` de resultaten weer geven
6. Selecteer _kolom instellingen_ om het deel venster instellingen te openen.
7. Stel in het gedeelte _kolommen_ het volgende in:
    1. Voor _beeld_ -kolom renderer: `Link` , weer geven om te openen: `Cell Details` , koppelings label:`Sample`
    2. _Aantal_ -kolom renderer: `Bar` , kleuren palet: `Blue` , minimum waarde: `0`
    3. _Aanvraag_ -kolom weergave: `Automatic`
    4. Selecteer _opslaan en sluiten_ om de wijzigingen toe te passen

    ![Scherm afbeelding van het tabblad van de kolom instelling.](./media/workbooks-interactive/column-settings.png)

8. Klik op een van de `Sample` koppelingen in het raster. Hiermee opent u een deel venster met de details van een voor beeld van een aanvraag.

    ![Scherm afbeelding van het detail venster van de voorbeeld aanvraag.](./media/workbooks-interactive/details.png)

### <a name="link-renderer-actions"></a>Koppeling renderer-acties

| Koppelings actie | Actie bij klikken |
|:------------- |:-------------|
| `Generic Details` | Hiermee worden de rijwaarden in een eigenschappen raster weer gegeven tabblad context |
| `Cell Details` | Hiermee wordt de celwaarde in een eigenschappen raster context tabblad weer gegeven. Dit is handig wanneer de cel een dynamisch type bevat met informatie (bijvoorbeeld JSON met aanvraag eigenschappen zoals locatie, rolinstantie, enzovoort). |
| `Cell Details` | Hiermee wordt de celwaarde in een eigenschappen raster context tabblad weer gegeven. Dit is handig wanneer de cel een dynamisch type bevat met informatie (bijvoorbeeld JSON met aanvraag eigenschappen zoals locatie, rolinstantie, enzovoort). |
| `Custom Event Details` | Hiermee opent u de Application Insights Zoek Details met de aangepaste gebeurtenis-ID ( `itemId` ) in de cel |
| `* Details` | Vergelijkbaar met details van aangepaste gebeurtenissen, met uitzonde ring van afhankelijkheden, uitzonde ringen, pagina weergaven, aanvragen en traceringen. |
| `Custom Event User Flows` | Hiermee opent u de Application Insights Gebruikersstromen ervaring die in de cel is gedraaid op de aangepaste gebeurtenis naam |
| `* User Flows` | Vergelijkbaar met aangepaste gebeurtenis Gebruikersstromen, behalve voor uitzonde ringen, pagina weergaven en aanvragen |
| `User Timeline` | Hiermee opent u de gebruikers tijdlijn met de gebruikers-ID (user_Id) in de cel. |
| `Session Timeline` | Hiermee opent u de Application Insights Zoek ervaring voor de waarde in de cel (bijvoorbeeld zoek naar tekst ' ABC ' waarbij ABC de waarde in de cel is) |
| `Resource overview` | Open de resource-overzicht in de portal op basis van de resource-ID-waarde in de cel |

## <a name="conditional-visibility"></a>Voorwaardelijke zicht baarheid

Met werkmap kunnen gebruikers bepaalde besturings elementen weer geven of verbergen op basis van de waarden van de para meters. Hierdoor kunnen auteurs rapporten zien op basis van de gebruikers invoer of de telemetrie-status. Een voor beeld is het weer geven van alleen een samen vatting wanneer dingen goed zijn, maar de volledige details worden weer gegeven wanneer er iets mis gaat.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>Interactiviteit instellen met behulp van voorwaardelijke zicht baarheid

1. Volg de stappen in de [rij een interactiviteit instellen op raster klikken](#setting-up-interactivity-on-grid-row-click) om twee interactieve besturings elementen in te stellen.
2. Voeg aan de bovenkant een nieuwe para meter toe:
    1. Naam: `ShowDetails`
    2. Parameter type: `Drop down`
    3. Vereist: `checked`
    4. Gegevens ophalen uit: `JSON`
    5. JSON-invoer: `["Yes", "No"]`
    6. Opslaan om wijzigingen door te voeren.

    ![Nadat u de knop para meter toevoegen hebt geselecteerd, wordt het deel venster para meter bewerken weer gegeven.](./media/workbooks-interactive/edit-parameter.png)

3. Parameter waarde instellen op `Yes`

    ![Boven de knop bewerken is de vervolg keuzelijst waarmee u de parameter waarde kunt instellen](./media/workbooks-interactive/set-parameter.png)

4. Selecteer in het besturings element query met het vlak diagram het pictogram _Geavanceerde instellingen_ (tandwiel pictogram)
5. Controleer de instelling `Make this item conditionally visible`
    1. Dit item wordt weer gegeven als de `ShowDetails` waarde `equals` van de para meter `Yes`
6. Selecteer _gereed bewerken_ om wijzigingen door te voeren.
7. Selecteer _gereed bewerken_ op de werk balk van de werkmap om de Lees modus in te voeren.
8. Schakel de waarde van para meter `ShowDetails` in op `No` . U ziet dat de onderstaande grafiek verdwijnt.

In de onderstaande afbeelding ziet u het zicht bare `ShowDetails` geval waar `Yes`

![Scherm afbeelding van de voorwaardelijke zicht baarheid waar de grafiek wordt weer gegeven](./media/workbooks-interactive/interactivity-conditional-visibility-visible.png)

In de onderstaande afbeelding ziet u de verborgen Case waar `ShowDetails` is `No`

![Scherm afbeelding van de voorwaardelijke zicht baarheid waar de grafiek is verborgen](./media/workbooks-interactive/interactivity-conditional-visibility-invisible.png)

## <a name="interactivity-with-multiple-selections-in-grids-and-charts"></a>Interactiviteit met meerdere selecties in rasters en grafieken

Met de stappen voor query's en metrische gegevens kunt u ook een of meer para meters exporteren wanneer een rij (of meerdere rijen) is geselecteerd.

![Scherm afbeelding met de instellingen voor export parameters met meerdere para meters. ](./media/workbooks-interactive/interactivity-export-parameters.png)

1. Ga in de query stap voor het weer geven van het raster naar de geavanceerde instellingen.
2. Schakel het selectie vakje in `When items are selected, export parameters` . Er worden extra besturings elementen weer gegeven.
3. Schakel het selectie vakje in `allow selection of multiple values` .
    1. De weer gegeven visualisatie staat waarden voor meervoudige selectie en geëxporteerde para meters toe als matrices van waarden, zoals bij het gebruik van meerdere vervolg parameters.
    2. Als u de weer gave-visualisatie uitschakelt, wordt alleen het laatst geselecteerde item in acht genomen. Exporteer slechts één waarde per keer.
4. Gebruik de knop *para meter toevoegen* voor elke para meter die u wilt exporteren. Er wordt een pop-upvenster weer gegeven met de instellingen voor de para meter die moeten worden geëxporteerd.

Als enkelvoudige selectie is ingeschakeld, kan de auteur opgeven welk veld van de oorspronkelijke gegevens moet worden geëxporteerd. De velden bevatten de parameter naam, het parameter type en de standaard waarde die moeten worden gebruikt als er niets is geselecteerd (optioneel).

Als meervoudige selectie is ingeschakeld, geeft de auteur aan welk veld van de oorspronkelijke gegevens moet worden geëxporteerd. De velden bevatten de parameter naam, het parameter type, de offerte met en het scheidings teken. De waarden quote with en scheidings teken worden gebruikt bij het omzetten van de pijl waarden in tekst wanneer deze worden vervangen in een query. In meervoudige selectie als er geen waarden zijn geselecteerd, is de standaard waarde een lege matrix.

> [!NOTE]
> Voor meervoudige selectie worden alleen unieke waarden geëxporteerd, maar u ziet geen uitvoer matrix waarden als ' 1, 1, 2, 1 ' u krijgt ' 1, 2 ' als uitvoer waarden.

U kunt de instelling ' veld voor exporteren ' leeg laten in de export instellingen. Als u dat wel doet, worden alle beschik bare velden in de gegevens geëxporteerd als een stringified JSON-object van Key: waardeparen. Voor rasters en titels zijn dit alle velden in het raster. Voor grafieken zijn de beschik bare velden x, y, Series en label (afhankelijk van het type grafiek).

Hoewel het standaard gedrag bestaat uit het exporteren van een para meter als tekst, als u weet dat het veld een abonnement of Resource-ID is, gebruikt u dit als het type export parameter. Hierdoor kan de para meter worden gebruikt downstream in plaatsen waarvoor deze typen para meters zijn vereist.

## <a name="next-steps"></a>Volgende stappen

* [Ga](./workbooks-overview.md#visualizations) voor meer informatie over werkmappen veel uitgebreide visualisaties opties.
* De toegang tot uw werkmap resources [beheren](./workbooks-access-control.md) en delen.
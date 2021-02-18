---
title: Azure Monitor werkmappen-groepen
description: Groepen gebruiken in Azure Monitor werkmappen.
services: azure-monitor
author: lgayhardt
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: lagayhar
ms.openlocfilehash: 2614117d153b1db50d4487bf4045a63af430f3ac
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100610012"
---
# <a name="how-to-use-groups-in-workbooks"></a>Groepen gebruiken in werkmappen

Met een groeps item in een werkmap kunt u een reeks stappen in een werkmap logisch groeperen.

Groepen in werkmappen zijn handig om verschillende dingen te doen:

- Layout
  - In scenario's waarin u wilt dat items verticaal worden georganiseerd, kunt u een groep items maken die allemaal worden gestapeld en de stijl van de groep instellen op een percentage breedte in plaats van de breedte van het percentage voor alle afzonderlijke items.
- Zichtbaarheid
  - In scenario's waarin u veel items wilt verbergen/weer geven, kunt u de zicht baarheid van de hele groep items instellen, in plaats van de zichtbaarheids instellingen voor elk afzonderlijk item in te stellen. Dit kan handig zijn in sjablonen die gebruikmaken van tabbladen, omdat u een groep kunt gebruiken als de inhoud van het tabblad en de volledige groep kan worden verborgen/weer gegeven op basis van een para meter die is ingesteld op het geselecteerde tabblad.
- Prestaties
  - In gevallen waarin u een zeer grote sjabloon hebt met veel secties of tabbladen, kunt u elke sectie omzetten in een eigen subsjabloon en groepen gebruiken om alle subsjablonen binnen de sjabloon op het hoogste niveau te laden. De inhoud van de subsjablonen kan pas worden geladen of uitgevoerd als een gebruiker deze groepen zichtbaar maakt. Meer informatie over [het splitsen van een grote sjabloon in veel sjablonen](#how-to-split-a-large-template-into-many-templates).

## <a name="using-groups"></a>Groepen gebruiken

Selecteer *toevoegen* en vervolgens *groep toevoegen* om een groep toe te voegen aan uw werkmap.

![Selecteer toevoegen en vervolgens groep.](./media/workbooks-groups/add-group.png)

Hieronder ziet u een scherm opname van een groep in de Lees modus met twee items in: een tekst item en een query-item.  

![Groep in de Lees modus.](./media/workbooks-groups/groups-view.png)

Wanneer u de werkmap bewerkt, kunt u zien dat deze twee items daad werkelijk binnen een groeps item staan:

![Groep in de bewerkings modus. ](./media/workbooks-groups/groups-edit.png)

In de bovenstaande scherm afbeelding bevindt de groep zich in de bewerkings modus, met daarin twee items (binnen het onderbroken gebied). Elke stap kan zich bevindt in de modus bewerken of lezen, onafhankelijk van elkaar. De tekst stap bevindt zich bijvoorbeeld in de bewerkings modus terwijl de query stap zich in de Lees modus bevindt.

## <a name="scoping"></a>Bereik

Op dit moment wordt een groep beschouwd als een nieuwe scope in de werkmap. Alle para meters die in de groep zijn gemaakt, zijn alleen zichtbaar in de groep. Dit geldt ook voor samen voegen. u kunt alleen gegevens in hun groep of op het bovenliggende niveau weer geven.

## <a name="group-types"></a>Groeps typen

Met het item werkmap groep kunt u een groep items toevoegen aan een werkmap. Als auteur van een werkmap geeft u op welk type groep u wilt maken. Er zijn twee soorten groepen:

- Dus
  - Met de groep in de werkmap kunt u de inhoud van de items in de groep toevoegen, verwijderen of bewerken. Dit wordt meestal gebruikt voor indelings-en zichtbaarheids doeleinden.
- Van sjabloon
  - De groep in de werkmap wordt geladen van de inhoud van een andere sjabloon op basis van de ID. De inhoud van de sjabloon wordt tijdens runtime geladen en samengevoegd in de werkmap. In de bewerkings modus kunt u de inhoud van de groep niet wijzigen, omdat deze alleen opnieuw wordt geladen vanuit de sjabloon de volgende keer dat het item wordt geladen.  

## <a name="load-types"></a>Typen belasting

Er zijn een aantal verschillende manieren waarop de inhoud van een groep kan worden geladen. Als auteur van een werkmap kunt u opgeven wanneer en hoe de inhoud van een groep wordt geladen.

### <a name="lazy-the-default"></a>Lazy (de standaard instelling)

De groep wordt alleen geladen wanneer het item wordt weer gegeven. Hiermee kan een groep worden gebruikt door tabblad items. Als het tabblad nooit is geselecteerd, wordt de groep nooit zichtbaar, waardoor de inhoud niet is geladen.

Voor groepen die zijn gemaakt op basis van een sjabloon, wordt de inhoud van de sjabloon niet opgehaald en worden de items in de groep pas gemaakt als de groep zichtbaar wordt. De gebruiker krijgt de voortgangs kringen voor de hele groep te zien terwijl de inhoud wordt opgehaald.

### <a name="explicit"></a>Expliciet

In deze modus wordt een knop weer gegeven waar de groep zou zijn, en er wordt geen inhoud opgehaald of gemaakt totdat de gebruiker expliciet op de knop klikt om de inhoud te laden. Dit is handig in scenario's waarin de inhoud kostbaar kan zijn om te worden berekend of zelden wordt gebruikt. De auteur kan de tekst opgeven die op de knop moet worden weer gegeven.

Hieronder ziet u een scherm afbeelding van expliciete laad instellingen met een geconfigureerde knop extra belasting.

![Instellingen voor expliciet laden](./media/workbooks-groups/groups-explicitly-loaded.png)

De groep voordat deze in de werkmap wordt geladen:

![Expliciete groep voordat deze in de werkmap wordt geladen](./media/workbooks-groups/groups-explicitly-loaded-before.png)

De groep na het laden in de werkmap:

![Expliciete groep nadat deze in een werkmap is geladen](./media/workbooks-groups/groups-explicitly-loaded-after.png)

### <a name="always"></a>Altijd

In deze modus wordt de inhoud van de groep altijd geladen en gemaakt zodra de werkmap wordt geladen. Dit wordt meestal gebruikt bij het gebruik van een groep voor indelings doeleinden, waarbij de inhoud altijd zichtbaar is.

## <a name="using-templates-inside-a-group"></a>Sjablonen in een groep gebruiken

Wanneer een groep is geconfigureerd om te worden geladen vanuit een sjabloon, wordt die inhoud standaard geladen, wordt deze alleen geladen wanneer de groep zichtbaar is.

Wanneer een sjabloon in een groep wordt geladen, probeert de werkmap alle para meters samen te voegen die zijn gedeclareerd in de sjabloon die wordt geladen met para meters die al in de groep aanwezig zijn. Alle para meters die al bestaan in de werkmap met identieke namen, worden samengevoegd uit de sjabloon die wordt geladen. Als alle para meters in een parameter stap worden samengevoegd, verdwijnt de volledige stap van de para meters.

### <a name="example-1-all-parameters-have-identical-names"></a>Voor beeld 1: alle para meters hebben identieke namen

Overweeg een sjabloon met twee para meters bovenaan.

- `TimeRange` -een tijds bereik parameter.
- `Filter` -een tekst parameter.

![Parameter item bewerken: para meters op het hoogste niveau](./media/workbooks-groups/groups-top-level-params.png)

Vervolgens laadt een groeps item een tweede sjabloon met de eigen twee para meters en een tekst stap, waarbij de para meters dezelfde naam hebben:

![Een parameter item voor een tweede sjabloon bewerken](./media/workbooks-groups/groups-merged-away.png)

Wanneer de tweede sjabloon in de groep wordt geladen, worden de dubbele para meters samengevoegd. Omdat alle para meters zijn samengevoegd, wordt de stap binnenste para meters ook samengevoegd, wat resulteert in de groep die alleen de tekst stap bevat.

### <a name="example-2-one-parameter-has-an-identical-name"></a>Voor beeld 2: de ene para meter heeft dezelfde naam

Houd rekening met de sjabloon van een groep met twee para meters bovenaan.

- `TimeRange` -een tijds bereik parameter.
- `FilterB` -een tekst parameter, Let erop dat deze niet `Filter` lijkt op de bovenste sjabloon.

![Een groeps item bewerken met het resultaat van het samen voegen van para meters](./media/workbooks-groups/groups-wont-merge-away.png)

Wanneer de item's-sjabloon van de groep wordt geladen, `TimeRange` wordt de para meter uit de groep ingevoegd. De werkmap heeft dan de eerste para meters `TimeRange` en `Filter` de para meter-stap van de groep bevat alleen `FilterB`

![resultaat van para meters die niet worden samengevoegd](./media/workbooks-groups/groups-wont-merge-away-result.png)

Als de geladen sjabloon bevat `TimeRange` en `Filter` (in plaats van `FilterB` ), heeft de resulterende werkmap de stap para meters en een groep met alleen de tekst stap resterend.

## <a name="how-to-split-a-large-template-into-many-templates"></a>Een grote sjabloon splitsen in veel sjablonen

Om de prestaties te verbeteren, is het handig om een grote sjabloon op te splitsen in meerdere kleinere sjablonen waarmee inhoud wordt geladen of op aanvraag van de gebruiker. Hierdoor is de initiële belasting sneller naarmate de sjabloon op het hoogste niveau veel kleiner kan zijn.

Wanneer u een sjabloon splitst in delen, moet u de sjabloon in veel sjablonen (subsjablonen) die allemaal afzonderlijk werken, in feite splitsen. Dus als de sjabloon op het hoogste niveau een `TimeRange` para meter heeft die door andere stappen wordt gebruikt, moet de subsjabloon ook een stap para meters bevatten die een para meter met de exacte naam definieert. Hierdoor kunnen de subsjablonen onafhankelijk van elkaar werken en kunnen ze in grotere sjablonen in groepen worden geladen.

Een grotere sjabloon omzetten in meerdere subsjablonen:

1.  Maak na de gedeelde para meters een nieuwe lege groep aan de bovenkant van de werkmap. Deze nieuwe groep wordt uiteindelijk een subsjabloon.
2. Maak een kopie van de stap gedeelde para meters en gebruik vervolgens *verplaatsen naar groep* om de kopie te verplaatsen naar de groep die u in stap 1 hebt gemaakt. Met deze para meters stap kan de subsjabloon onafhankelijk van de buitenste sjabloon werken en worden deze samengevoegd wanneer deze in de buitenste sjabloon wordt geladen.
    > [!NOTE]
    > Subsjabloon hoeft niet technisch deze para meters te hebben die worden samengevoegd als u de subsjablonen op zichzelf niet wilt laten zien. Het is echter wel moeilijk om deze te bewerken of fouten op te lossen als u dit later moet doen.

3. Verplaats elk item in de werkmap dat u in de subsjabloon wilt plaatsen, naar de groep die u in stap 1 hebt gemaakt.
4. Als de afzonderlijke stappen die u in stap 3 hebt verplaatst, een voorwaardelijke visibilities hebben, wordt dat de zicht baarheid van de buitenste groep (zoals gebruikt in tabbladen). Verwijder deze uit de items in de groep en voeg deze zichtbaarheids instelling toe aan de groep zelf. Sla hier op om te voor komen dat wijzigingen verloren gaan en/of Exporteer en sla een kopie van de JSON-inhoud op.
5. Als u wilt dat de groep wordt geladen vanuit een sjabloon, kunt u de knop werk balk *bewerken* in de groep gebruiken. Hiermee wordt alleen de inhoud van die groep geopend als een werkmap in een nieuw venster. U kunt deze vervolgens opslaan als toepasselijk en de werkmap weergave sluiten (sluit de browser niet, alleen die weer gave om terug te gaan naar de vorige werkmap die u hebt bewerkt).
6. U kunt vervolgens de groeps stap voor het laden van de sjabloon wijzigen en het veld sjabloon-ID instellen op de werkmap of sjabloon die u in stap 5 hebt gemaakt. Als u wilt werken met werkmappen-Id's, moet de bron een resource-ID van een gedeelde werkmap zijn. Druk op *laden* en de inhoud van die groep wordt nu geladen van die subsjabloon in plaats van opgeslagen in de buitenste werkmap.

## <a name="next-steps"></a>Volgende stappen
- [Overzicht van werkmappen](../platform/workbooks-overview.md)
- [JSONPath gebruiken met werkmappen](workbooks-jsonpath.md)
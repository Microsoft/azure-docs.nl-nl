---
title: Gegevens pijplijnen bewaken en beheren-Azure
description: Meer informatie over het gebruik van de app voor bewaking en beheer voor het bewaken en beheren van Azure-gegevens fabrieken en-pijp lijnen.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.assetid: f3f07bc4-6dc3-4d4d-ac22-0be62189d578
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: afab2b03d31045b9f49f357b49d15368cde898da
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96495698"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>Azure Data Factory pijp lijnen bewaken en beheren met behulp van de app voor bewaking en beheer
> [!div class="op_single_selector"]
> * [Azure Portal/Azure PowerShell gebruiken](data-factory-monitor-manage-pipelines.md)
> * [De app voor bewaking en beheer gebruiken](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [Data Factory pijp lijnen bewaken en beheren in](../monitor-visually.md).

In dit artikel wordt beschreven hoe u met behulp van de app voor bewaking en beheer de Data Factory-pijp lijnen kunt controleren, beheren en opsporen. U kunt aan de slag met het gebruik van de toepassing door de volgende video te bekijken:

> [!NOTE]
> De gebruikers interface die in de video wordt weer gegeven, komt mogelijk niet exact overeen met wat u in de portal ziet. Het is iets ouder, maar concepten blijven hetzelfde. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>De app voor bewaking en beheer starten
Als u de app monitor en beheer wilt starten, klikt u op de **& tegel beheren** op de blade **Data Factory** voor uw Data Factory.

![De tegel bewaking op de Data Factory start pagina](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

U ziet dat de app voor bewaking en beheer geopend is in een afzonderlijk venster.  

![App voor controle en beheer](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> Als u ziet dat de webbrowser is vastgelopen bij "autoriseren...", schakelt u het selectie vakje cookies van derden **en site gegevens blok keren** uit, maakt u een uitzonde ring voor **login.microsoftonline.com** en probeert u de app opnieuw te openen.


In de lijst activiteit Windows in het middelste deel venster ziet u een activiteiten venster voor elke uitvoering van een activiteit. Als u bijvoorbeeld de activiteit hebt gepland om vijf uur per uur te worden uitgevoerd, ziet u vijf activiteiten Vensters die zijn gekoppeld aan vijf gegevens segmenten. Ga als volgt te werk als u geen activiteit Vensters in de lijst onderaan ziet:
 
- Werk de filters **begin tijd** en **eind tijd** aan de bovenkant bij zodat deze overeenkomen met de begin-en eind tijd van de pijp lijn en klik vervolgens op de knop **Toep assen** .  
- De Windows-lijst met activiteiten wordt niet automatisch vernieuwd. Klik op de knop **vernieuwen** in de werk balk van de lijst **activiteit Windows** .  

Als u geen Data Factory toepassing hebt om deze stappen te testen met, gaat u naar de zelf studie: [gegevens kopiëren van Blob Storage naar SQL database met Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="understand-the-monitoring-and-management-app"></a>Meer informatie over de app voor bewaking en beheer
Er zijn drie tabbladen aan de linkerkant: **resource Explorer**, **bewakings weergaven** en **waarschuwingen**. Het eerste tabblad (**resource Explorer**) is standaard geselecteerd.

### <a name="resource-explorer"></a>Resource Verkenner
U ziet het volgende:

* De resource Explorer- **structuur weergave** in het linkerdeel venster.
* De **diagram weergave** bovenaan in het middelste deel venster.
* De lijst met **activiteiten** onder in het middelste deel venster.
* De tabbladen **Eigenschappen**, **activiteiten venster Verkenner** en **script** in het rechterdeel venster.

In resource Explorer ziet u alle resources (pijp lijnen, gegevens sets, gekoppelde services) in de data factory in een structuur weergave. Wanneer u een object in resource Explorer selecteert:

* De gekoppelde Data Factory entiteit is gemarkeerd in de diagram weergave.
* [Gekoppelde activiteit Vensters](data-factory-scheduling-and-execution.md) worden onderaan in de lijst met activiteiten gemarkeerd.  
* De eigenschappen van het geselecteerde object worden weer gegeven in de venster Eigenschappen in het rechterdeel venster.
* De JSON-definitie van het geselecteerde object wordt weer gegeven, indien van toepassing. Bijvoorbeeld: een gekoppelde service, een gegevensset of een pijp lijn.

![Resource Verkenner](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Zie het artikel [planning en uitvoering](data-factory-scheduling-and-execution.md) voor gedetailleerde conceptuele informatie over activiteit Vensters.

### <a name="diagram-view"></a>Diagramweergave
De diagram weergave van een data factory biedt één glas venster om een data factory en de bijbehorende activa te controleren en te beheren. Wanneer u een Data Factory entiteit (gegevensset/pijp lijn) in de diagram weergave selecteert:

* De entiteit data factory is geselecteerd in de structuur weergave.
* De bijbehorende activiteit Vensters zijn gemarkeerd in de Windows-lijst activiteit.
* De eigenschappen van het geselecteerde object worden weer gegeven in de venster Eigenschappen.

Wanneer de pijp lijn is ingeschakeld (niet onderbroken), wordt deze weer gegeven met een groene lijn:

![Pijp lijn actief](./media/data-factory-monitor-manage-app/PipelineRunning.png)

U kunt een pijp lijn onderbreken, hervatten of beëindigen door deze te selecteren in de diagram weergave en de knoppen op de opdracht balk te gebruiken.

![Onderbreken/hervatten op de opdracht balk](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
 
Er zijn drie opdracht balk knoppen voor de pijp lijn in de diagram weergave. U kunt de tweede knop gebruiken om de pijp lijn te onderbreken. Als u het onderbreken uitvoert, worden de momenteel actieve activiteiten niet beëindigd en kunnen ze worden voltooid. De derde knop pauzeert de pijp lijn en beëindigt de bestaande activiteiten die worden uitgevoerd. Met de eerste knop hervat u de pijp lijn. Wanneer de pijp lijn is onderbroken, verandert de kleur van de pijp lijn. Een onderbroken pijp lijn ziet er bijvoorbeeld uit zoals in de volgende afbeelding: 

![Pijp lijn onderbroken](./media/data-factory-monitor-manage-app/PipelinePaused.png)

U kunt meerdere pijp lijnen selecteren met behulp van de CTRL-toets. U kunt de opdracht balk knoppen gebruiken om meerdere pijp lijnen tegelijk te onderbreken/hervatten.

U kunt ook met de rechter muisknop op een pijp lijn klikken en opties selecteren om een pijp lijn te onderbreken, te hervatten of te beëindigen. 

![Context menu voor pijp lijn](./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png)

Klik op de optie **pijp lijn openen** om alle activiteiten in de pijp lijn te bekijken. 

![Menu Pijplijn openen](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

In de weer gave geopend pijp lijn ziet u alle activiteiten in de pijp lijn. In dit voor beeld is er slechts één activiteit: Kopieer activiteit. 

![Geopende pijp lijn](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Als u wilt terugkeren naar de vorige weer gave, klikt u op de naam van de data factory in het breadcrumb-menu bovenaan.

Wanneer u in de pipeline-weer gave een uitvoer gegevensset selecteert of wanneer u de muis aanwijzer over de uitvoer gegevensset beweegt, ziet u het pop-upvenster activiteit Windows voor die gegevensset.

![Pop-upvenster activiteit Windows](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

U kunt klikken op een activiteiten venster om de details ervan weer te geven in het venster **Eigenschappen** in het rechterdeel venster.

![Eigenschappen van het activiteiten venster](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

Ga in het rechterdeel venster naar het tabblad **activiteiten venster Verkenner** om meer details weer te geven.

![Scherm afbeelding die laat zien hoe u het tabblad van het activiteiten venster Verkenner opent.](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

U ziet ook de **opgeloste variabelen** voor elke uitvoerings poging voor een activiteit in de sectie **pogingen** .

![Opgeloste variabelen](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Ga naar het tabblad **script** om de JSON-script definitie voor het geselecteerde object te bekijken.   

![Script tabblad](./media/data-factory-monitor-manage-app/ScriptTab.png)

U kunt activiteiten Vensters op drie locaties bekijken:

* De activiteit Windows pop-up in de diagram weergave (middelste deel venster).
* Het venster activiteit Verkenner in het rechterdeel venster.
* De lijst activiteit Windows in het onderste deel venster.

In de activiteit Windows pop-up en activiteiten venster Explorer kunt u naar de vorige week en de volgende week schuiven met behulp van de linker-en rechter pijlen.

![Pijl naar links/rechts in Verkenner-venster](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

Aan de onderkant van de diagram weergave ziet u de volgende knoppen: inzoomen, uitzoomen, passend maken, zoomen 100%, vergren delen. Met de knop **indeling vergren delen** voor komt u dat u per ongeluk tabellen en pijp lijnen verplaatst in de diagram weergave. Het is standaard ingeschakeld. U kunt deze functie uitschakelen en entiteiten verplaatsen in het diagram. Wanneer u deze functie uitschakelt, kunt u de laatste knop gebruiken om automatisch tabellen en pijp lijnen te positioneren. U kunt ook in-of uitzoomen met behulp van het muis wiel.

![Zoom opdrachten voor diagram weergave](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>Lijst Activiteitsvensters
In de lijst activiteit Windows onder in het middelste deel venster worden alle activiteiten vensters weer gegeven voor de gegevensset die u hebt geselecteerd in resource Explorer of in de diagram weergave. De lijst is standaard in aflopende volg orde, wat betekent dat het laatste activiteiten venster bovenaan wordt weer geven.

![Lijst Activiteitsvensters](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Deze lijst wordt niet automatisch vernieuwd. Gebruik daarom de knop Vernieuwen op de werk balk om deze hand matig te vernieuwen.  

Activiteit Vensters kunnen een van de volgende statussen hebben:

<table>
<tr>
    <th align="left">Status</th><th align="left">Substatus</th><th align="left">Beschrijving</th>
</tr>
<tr>
    <td rowspan="8">Wachten</td><td>ScheduleTime</td><td>De tijd voor het uitvoeren van het activiteiten venster kan niet worden uitgevoerd.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>De upstream-afhankelijkheden zijn niet gereed.</td>
</tr>
<tr>
<td>ComputeResources</td><td>De reken resources zijn niet beschikbaar.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Alle exemplaren van de activiteit zijn bezet en andere activiteit Vensters worden uitgevoerd.</td>
</tr>
<tr>
<td>ActivityResume</td><td>De activiteit wordt gepauzeerd en de activiteiten Vensters kunnen niet worden uitgevoerd totdat deze wordt hervat.</td>
</tr>
<tr>
<td>Opnieuw proberen</td><td>De uitvoering van de activiteit wordt opnieuw uitgevoerd.</td>
</tr>
<tr>
<td>Validatie</td><td>De validatie is nog niet gestart.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Er wordt gewacht tot de validatie is uitgevoerd.</td>
</tr>
<tr>
<tr>
<td rowspan="2">Wordt uitgevoerd</td><td>Valideren</td><td>De validatie wordt uitgevoerd.</td>
</tr>
<td>-</td>
<td>Het activiteiten venster wordt verwerkt.</td>
</tr>
<tr>
<td rowspan="4">Mislukt</td><td>Verlopen</td><td>De uitvoering van de activiteit duurde langer dan is toegestaan door de activiteit.</td>
</tr>
<tr>
<td>Geannuleerd</td><td>Het activiteiten venster is geannuleerd door de gebruiker.</td>
</tr>
<tr>
<td>Validatie</td><td>De validatie is mislukt.</td>
</tr>
<tr>
<td>-</td><td>Het activiteiten venster kan niet worden gegenereerd of gevalideerd.</td>
</tr>
<td>Gereed</td><td>-</td><td>Het activiteiten venster is klaar voor gebruik.</td>
</tr>
<tr>
<td>Overgeslagen</td><td>-</td><td>Het activiteiten venster is niet verwerkt.</td>
</tr>
<tr>
<td>Geen</td><td>-</td><td>Er is een activiteiten venster gebruikt dat bestaat uit een andere status, maar dit is opnieuw ingesteld.</td>
</tr>
</table>


Wanneer u op een activiteiten venster in de lijst klikt, ziet u details hierover in de **activiteit Windows Verkenner** of in het venster **Eigenschappen** aan de rechter kant.

![Scherm afbeelding die laat zien hoe Details over een activiteiten venster worden weer gegeven.](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Windows-activiteit vernieuwen
De details worden niet automatisch vernieuwd, dus gebruik de knop Vernieuwen (de tweede knop) op de opdracht balk om de lijst met activiteiten in Windows hand matig te vernieuwen.  

### <a name="properties-window"></a>venster Eigenschappen
Het venster Eigenschappen bevindt zich in het meest rechtse deel venster van de app voor bewaking en beheer.

![venster Eigenschappen](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Hierin worden de eigenschappen weer gegeven voor het item dat u hebt geselecteerd in de lijst resource Verkenner (structuur weergave), diagram weergave of activiteit Windows.

### <a name="activity-window-explorer"></a>Activiteiten venster Verkenner
Het venster **activiteiten venster Verkenner** bevindt zich in het meest rechtse deel venster van de app voor bewaking en beheer. Hierin worden details weer gegeven over het activiteiten venster dat u hebt geselecteerd in het pop-upvenster activiteit Windows of de lijst activiteit Windows.

![Activiteiten venster Verkenner](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

U kunt overschakelen naar een ander activiteiten venster door erop te klikken in de weer gave kalender bovenaan. U kunt ook de pijl knoppen links/rechts bovenaan gebruiken om de activiteit Vensters uit de vorige week of de volgende week weer te geven.

U kunt de werkbalk knoppen in het onderste deel venster gebruiken om het activiteiten venster opnieuw uit te voeren of de details in het deel venster te vernieuwen.

### <a name="script"></a>Script
U kunt het tabblad **script** gebruiken om de JSON-definitie van de geselecteerde Data Factory entiteit (gekoppelde service, gegevensset of pijp lijn) weer te geven.

![Script tabblad](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="use-system-views"></a>Systeem weergaven gebruiken
De bewakings-en beheer-app bevat vooraf ontwikkelde systeem weergaven (Windows **recente** activiteiten, **mislukte activiteit** Vensters, Windows **activiteiten in uitvoering**) waarmee u recente/mislukte/lopende activiteiten Vensters voor uw Data Factory kunt weer geven.

Ga naar het tabblad **controle weergaven** aan de linkerkant door erop te klikken.

![Tabblad controle weergaven](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

Er zijn momenteel drie systeem weergaven die worden ondersteund. Selecteer een optie om de Vensters recente activiteit, mislukte activiteiten of in uitvoering te bekijken in de Windows-lijst met activiteiten (onderaan in het middelste deel venster).

Wanneer u de optie **recente Windows-activiteit** selecteert, ziet u alle recente activiteiten Vensters in aflopende volg orde van de tijd van de **laatste poging**.

U kunt de Windows-weer gave **mislukte activiteit** gebruiken om alle mislukte activiteiten Vensters in de lijst weer te geven. Selecteer een venster voor mislukte activiteiten in de lijst om de details ervan weer te geven in het venster **Eigenschappen** of in het **venster activiteiten Verkenner**. U kunt ook alle logboeken downloaden voor een venster met een mislukte activiteit.

## <a name="sort-and-filter-activity-windows"></a>De activiteit Vensters sorteren en filteren
Wijzig de instellingen voor de **Start tijd** en de **eind tijd** op de opdracht balk om de Windows-activiteit te filteren. Nadat u de begin tijd en eind tijd hebt gewijzigd, klikt u op de knop naast de eind tijd om de Windows-lijst met activiteiten te vernieuwen.

![Begin-en eind tijden](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> Op dit moment zijn alle tijden in UTC-indeling in de app voor bewaking en beheer.
>
>

Klik in de **lijst activiteit Windows** op de naam van een kolom (bijvoorbeeld: status).

![Menu voor de lijst met activiteiten van Windows](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

U kunt het volgende doen:

* Sorteren in oplopende volg orde.
* In aflopende volg orde sorteren.
* Filteren op een of meer waarden (gereed, wachtend, enzovoort).

Wanneer u een filter voor een kolom opgeeft, ziet u de filter knop ingeschakeld voor die kolom. Dit geeft aan dat de waarden in de kolom gefilterde waarden zijn.

![Filteren op een kolom van de Windows-lijst met activiteiten](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

U kunt hetzelfde pop-upvenster gebruiken om filters te wissen. Als u alle filters voor de lijst met activiteiten wilt wissen, klikt u op de knop Filter wissen op de opdracht balk.

![Alle filters voor de Windows-lijst met activiteiten wissen](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="perform-batch-actions"></a>Batch acties uitvoeren
### <a name="rerun-selected-activity-windows"></a>Geselecteerde activiteit Vensters opnieuw uitvoeren
Selecteer een activiteiten venster, klik op de pijl-omlaag voor de eerste opdracht balk knop en **Selecteer opnieuw**  /  **uitvoeren met upstream in pijp lijn**. Wanneer u de optie **opnieuw uitvoeren met upstream in pijplijn** selecteert, worden alle Windows-activiteiten voor upstream ook opnieuw uitgevoerd.
    ![Een activiteiten venster opnieuw uitvoeren](./media/data-factory-monitor-manage-app/ReRunSlice.png)

U kunt ook meerdere activiteit Vensters in de lijst selecteren en deze op hetzelfde moment opnieuw uitvoeren. U kunt de activiteiten Vensters filteren op basis van de status (bijvoorbeeld: **mislukt**), en vervolgens de Windows mislukt-activiteit opnieuw uitvoeren nadat het probleem is verholpen dat ervoor zorgt dat de activiteit Windows mislukt. Zie de volgende sectie voor meer informatie over het filteren van activiteit Vensters in de lijst.  

### <a name="pauseresume-multiple-pipelines"></a>Meerdere pijp lijnen onderbreken/hervatten
U kunt meerdere pijp lijnen meerdere selecteren met behulp van de CTRL-toets. U kunt de opdracht balk knoppen (die zijn gemarkeerd in de rode rechthoek in de volgende afbeelding) gebruiken om ze te onderbreken/hervatten.

![Onderbreken/hervatten op de opdracht balk](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)

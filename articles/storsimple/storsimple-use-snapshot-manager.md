---
title: StorSimple Snapshot Manager gebruikers interface | Microsoft Docs
description: Hierin wordt de StorSimple-Snapshot Manager gebruikers interface beschreven en wordt uitgelegd hoe u deze kunt gebruiken voor het beheren van back-uptaken en de back-upcatalogus.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: c7d91892-2881-41a2-a7a2-908dc3646493
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: 35fa0fc134045585eebd21324352f9b4ff272e87
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94954236"
---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>StorSimple Snapshot Manager gebruikers interface gebruiken voor het beheren van back-uptaken en een back-upcatalogus

## <a name="overview"></a>Overzicht
De StorSimple-Snapshot Manager heeft een intuïtieve gebruikers interface die u kunt gebruiken om back-ups te maken en te beheren. Deze zelf studie biedt een inleiding tot de gebruikers interface en legt uit hoe elk van de onderdelen moet worden gebruikt. Zie [Wat is StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) voor een gedetailleerde beschrijving van de StorSimple-Snapshot Manager.

### <a name="console-description"></a>Console beschrijving
Als u de gebruikers interface wilt weer geven, klikt u op het pictogram StorSimple Snapshot Manager op het bureau blad. Het console venster wordt weer gegeven, zoals wordt weer gegeven in de volgende afbeelding.

![Snapshot Manager deel Vensters StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

Het console venster bevat vijf hoofd elementen. Klik op de juiste koppeling voor een volledige beschrijving van elk element.

* [Menubalk](#menu-bar) 
* [Werk balk](#tool-bar) 
* [Deel venster bereik](#scope-pane) 
* [Resultaten deel venster](#results-pane) 
* [Acties deel venster](#actions-pane) 

Daarnaast ondersteunt de StorSimple-Snapshot Manager [toetsenbord navigatie en een aantal snelkoppelingen](#keyboard-navigation-and-shortcuts).

### <a name="console-accessibility"></a>Console toegankelijkheid
De StorSimple-Snapshot Manager gebruikers interface ondersteunt de toegankelijkheids functies die worden geboden door het Windows-besturings systeem en de micro soft Management Console (MMC), evenals een aantal StorSimple Snapshot Manager – specifieke sneltoetsen. 

* Ga naar sneltoetsen [voor Windows](https://support.microsoft.com/kb/126449)voor een beschrijving van de toegankelijkheids functies van Windows. 
* Ga voor een beschrijving van de toegankelijkheids functies van MMC naar [Toegankelijkheid voor mmc 3,0](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc766075(v=ws.11))
* Ga naar [toetsenbord navigatie en snelkoppelingen](#keyboard-navigation-and-shortcuts)voor een beschrijving van de StorSimple Snapshot Manager toegankelijkheids functies.

## <a name="menu-bar"></a>Menubalk
De menu balk boven aan het console venster bevat [bestanden](#file-menu), [acties](#action-menu), [weer gaven](#view-menu), [Favorieten](#favorites-menu), [Vensters](#window-menu)en [Help](#help-menu) -menu's.

Klik op een item in de menu balk om een lijst met beschik bare opdrachten in het menu weer te geven. In het volgende voor beeld ziet u het menu **weer gave** dat is geselecteerd in de menu balk.

![Het menu weer gave is geselecteerd](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>Menu Bestand
Het menu **bestand** bevat standaard opdrachten van micro soft Management Console (MMC).

#### <a name="menu-access"></a>Menu toegang
Klik op **bestand** in de menu balk om het menu **bestand** weer te geven. Het volgende menu wordt weer gegeven.

![Menu bestand van StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Menu beschrijving
In de volgende tabel worden de items beschreven die worden weer gegeven in het menu **bestand** .

| Menu-item | Beschrijving |
|:--- |:--- |
| Nieuw |Klik op **Nieuw** om een nieuwe console te maken op basis van de StorSimple-Snapshot Manager. |
| Openen |Klik op **openen** om een bestaande console te openen. |
| Opslaan |Klik op **Opslaan** om de huidige console op te slaan. |
| Opslaan als |Klik op **Opslaan als** om een nieuw, gewijzigd exemplaar van de huidige console te maken. Gebruik de optie **Opslaan als** om een weer gave aan te passen en deze op te slaan zodat u deze later kunt ophalen. U kunt bijvoorbeeld StorSimple Snapshot Manager-modules maken die naar specifieke servers verwijzen. |
| Module toevoegen/verwijderen |Klik op **module toevoegen/verwijderen** om modules toe te voegen of te verwijderen en knoop punten in het deel venster **bereik** te organiseren. Ga voor meer informatie naar modules [en uitbrei dingen toevoegen, verwijderen en indelen in MMC 3,0](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc722035(v=ws.11)). |
| Opties |Klik op **Opties** om het console pictogram te wijzigen, geef de gebruikers toegangs modi en machtigingen op of verwijder console bestanden om de beschik bare schijf ruimte te verg Roten. |
| Lijst met bestands paden |Klik op een pad in de genummerde lijst om een bestand opnieuw te openen dat u onlangs hebt geopend. |
| Afsluiten |Klik op **Afsluiten** om het menu **bestand** te sluiten. |

### <a name="action-menu"></a>Actie menu
Gebruik het menu **actie** om een van de beschik bare acties te selecteren. De items die voor u beschikbaar zijn, zijn afhankelijk van de selectie die u hebt gemaakt in het deel venster **bereik** of het deel venster met **resultaten** .

#### <a name="menu-access"></a>Menu toegang
Ga op een van de volgende manieren te werk om het **actie** menu te bekijken:

* Klik met de rechter muisknop op een item in het deel venster **bereik** of in het deel venster met **resultaten** .
* Selecteer een item in het deel venster **bereik** of het deel venster met **resultaten** en klik vervolgens op **actie** op de menu balk. 

Als u bijvoorbeeld het bovenste knoop punt in het deel venster **bereik** selecteert en vervolgens met de rechter muisknop klikt of op **actie** klikt in de menu balk, wordt het volgende menu weer gegeven.

![StorSimple Snapshot Manager actie menu](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

Het deel venster **acties** (rechts van de console) bevat dezelfde lijst met acties als het menu **actie** . Daarnaast bevat het deel venster **acties** de opties in het menu **weer geven** waarmee u een aangepaste weer gave van het **resultaat** venster kunt maken.

![Deel venster acties met het menu weer gave openen](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Menu beschrijving
De volgende tabel bevat een alfabetische lijst met StorSimple-Snapshot Manager acties. 

* In de kolom **actie** worden acties weer gegeven die u kunt uitvoeren op knoop punten en resultaten. 
* In de **Navigatie** kolom wordt uitgelegd hoe u het juiste **actie** menu weergeeft, zodat u de actie kunt selecteren. Sommige acties worden weer gegeven in meerdere **actie** menu's. Selecteer voor deze acties één **Navigatie** optie in de lijst met opsommings tekens. 
* De kolom **Beschrijving** bevat informatie over het gebruik van elke actie in het menu **actie** of in het deel venster acties en legt uit wat het doet.

> [!NOTE]
> Het deel venster **acties** en de **actie** menu's bevatten extra opties, zoals **weer gave**, **nieuw venster van hier**, **vernieuwen**, **lijst exporteren** en **Help**. Deze opties zijn beschikbaar als onderdeel van de MMC en zijn niet specifiek voor StorSimple Snapshot Manager. De tabel bevat beschrijvingen van deze opties.
> 
> 

| Bewerking | Navigatie | Beschrijving |
|:--- |:--- |:--- |
| Verifiëren |Klik op het knoop punt **apparaten** en klik met de rechter muisknop op een apparaat in het **resultaten** venster. |Klik op **verifiëren** om het wacht woord in te voeren dat u voor het apparaat hebt geconfigureerd. |
| Klonen |Vouw **back-upcatalogus** uit, vouw **Cloud momentopnamen** uit, klik op een gedateerde back-up en selecteer vervolgens een volume in het **resultaten** venster. |Klik op **klonen** om een kopie van een Cloud momentopname te maken en op te slaan op een locatie die u opgeeft. |
| Een apparaat configureren |Klik met de rechter muisknop op het knoop punt **apparaten** . |Klik op **een apparaat configureren** om één apparaat of meerdere apparaten te configureren om verbinding te maken met de Windows-host. |
| Back-upbeleid maken |Voer een van de volgende handelingen uit:<ul><li>Klik met de rechter muisknop op **back-upbeleid**.</li><li>Klik of vouw **volume groepen** uit en klik met de rechter muisknop op een volume groep.</li><li>Klik of vouw **back-upcatalogus** uit en klik met de rechter muisknop op een volume groep.</li></ul> |Klik op **back-upbeleid maken** om een geplande back-up voor een volume groep te configureren. |
| Een volume groep maken |Voer een van de volgende handelingen uit:<ul><li>Klik op het knoop punt **volumes** en klik vervolgens met de rechter muisknop op een volume in het **resultaten** venster.</li><li>Klik met de rechter muisknop op het knoop punt **volume groepen** .</li></ul> |Klik op **volume groep maken** om volumes aan een volume groep toe te wijzen. |
| Verwijderen |Klik op een knoop punt of resultaat (dit item wordt weer gegeven in veel **actie** menu's en in deel Vensters met  **acties** ). |Klik op **verwijderen** om het knoop punt of resultaat dat u hebt geselecteerd, te verwijderen. Wanneer het bevestigings venster wordt weer gegeven, bevestigt of annuleert u het verwijderen. |
| Details |Klik op het knoop punt **apparaten** en klik vervolgens met de rechter muisknop op een apparaat in het **resultaten** venster. |Klik op **Details** om de configuratie gegevens voor een apparaat weer te geven. |
| Bewerken |Klik op **back-upbeleid** en klik vervolgens met de rechter muisknop op een beleid in het **resultaten** venster. |Klik op **bewerken** om het back-upschema voor een volume groep te wijzigen. |
| Lijst exporteren |Klik op een knoop punt of resultaat (dit item wordt weer gegeven in alle **actie** menu's en **acties** deel Vensters). |Klik op **lijst exporteren** om een lijst op te slaan in een bestand met door komma's gescheiden waarden (CSV). U kunt dit bestand vervolgens importeren in een spreadsheet toepassing voor analyse. |
| Help |Klik op een knoop punt of resultaat. (Dit item wordt weer gegeven in de deel Vensters alle **actie** menu's en **acties** .) |Klik op **Help** om online-Help te openen in een afzonderlijk browser venster. |
| Nieuw venster vanaf hier |Klik op een knoop punt of resultaat (dit item wordt weer gegeven in alle **actie** menu's en **acties** deel Vensters). |Klik **hier op nieuw venster** om een nieuw StorSimple-Snapshot Manager venster te openen. |
| Vernieuwen |Klik op een knoop punt of resultaat (dit item wordt weer gegeven in alle **actie** menu's en **acties** deel Vensters). |Klik op **vernieuwen** om het momenteel weer gegeven StorSimple-Snapshot Manager venster bij te werken. |
| Apparaat vernieuwen |Klik op het knoop punt **apparaten** en klik met de rechter muisknop op een apparaat in het **resultaten** venster. |Klik op **apparaat vernieuwen** om een specifiek verbonden apparaat te synchroniseren met StorSimple Snapshot Manager. |
| Apparaten vernieuwen |Klik met de rechter muisknop op het knoop punt **apparaten** . |Klik op **apparaten vernieuwen** om de lijst met verbonden apparaten te synchroniseren met StorSimple Snapshot Manager. |
| Volumes opnieuw scannen |Klik met de rechter muisknop op het knoop punt **volumes** . |Klik op **volumes opnieuw scannen** om de lijst met volumes die wordt weer gegeven in het **resultaat** venster bij te werken. |
| Herstellen |Vouw **back-upcatalogus** uit, vouw een volume groep uit, vouw **lokale moment opnamen** of **Cloud momentopnamen** uit en klik met de rechter muisknop op een back-up. |Klik op **herstellen** om de huidige volume groeps gegevens te vervangen door de gegevens van de geselecteerde back-up. |
| Back-up maken |Voer een van de volgende handelingen uit:<ul><li>Vouw **volume groepen** uit en klik met de rechter muisknop op een volume groep.</li><li>Vouw **back-upcatalogus** uit en klik met de rechter muisknop op een volume groep.</li></ul> |Klik op **back-up maken** om onmiddellijk een back-uptaak te starten. |
| Invoer weer geven in-/uitschakelen |Klik met de rechter muisknop op het bovenste knoop punt in het deel venster **bereik** (het knoop punt **StorSimple Snapshot Manager** in de voor beelden). |Klik op **in-/uitschakelen wordt weer gegeven** om de volume groepen en de bijbehorende back-ups weer te geven of te verbergen die zijn geïmporteerd uit het StorSimple Apparaatbeheer service-dash board. |

### <a name="view-menu"></a>Menu Beeld
Gebruik het menu **weer gave** om een aangepaste weer gave van de inhoud van het **resultaten** venster te maken. Het menu **weer gave** bevat **kolommen toevoegen/verwijderen** en opties **aanpassen** .

#### <a name="menu-access"></a>Menu toegang
U kunt het menu **weer geven** openen via de menu balk of in het deel venster **acties** .

![Menu StorSimple Snapshot Manager weer geven](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Menu beschrijving
In de volgende tabel worden de items beschreven die worden weer gegeven in het menu **beeld** .

| Menu-item | Beschrijving |
|:--- |:--- |
| Kolommen toevoegen/verwijderen |Klik op **kolommen toevoegen/verwijderen** om kolommen toe te voegen aan of te verwijderen uit het **resultaten** venster. |
| Aanpassen |Klik op **aanpassen** om items weer te geven of te verbergen in het StorSimple Snapshot Manager console venster. |

### <a name="favorites-menu"></a>Menu Favorieten
Gebruik het menu **Favorieten** om pagina weergaven en taken die u regel matig gebruikt, toe te voegen, te verwijderen en te organiseren. 

#### <a name="menu-access"></a>Menu toegang
U kunt het menu **Favorieten** openen via de menu balk.

![Menu StorSimple-favorieten Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Menu beschrijving
In de volgende tabel worden de items beschreven die in het menu **Favorieten** worden weer gegeven.

| Menu-item | Beschrijving |
|:--- |:--- |
| Toevoegen aan Favorieten |Klik op **toevoegen aan favorieten** om de huidige weer gave toe te voegen aan de lijst met favorieten. |
| Favorieten ordenen |Klik op **Favorieten ordenen** om de inhoud van de map Favorieten in te delen. |

### <a name="window-menu"></a>Venster menu
Gebruik het menu **venster** om StorSimple Snapshot Manager console Vensters toe te voegen en opnieuw in te delen.

#### <a name="menu-access"></a>Menu toegang
U kunt het menu **venster** openen in de menu balk.

![Menu venster StorSimple-Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

De genummerde lijst onder in het menu toont de Vensters die momenteel zijn geopend. Klik op een venster in die lijst om het venster op de voor grond te brengen. 

#### <a name="menu-description"></a>Menu beschrijving
In de volgende tabel worden de items beschreven die worden weer gegeven in het menu venster.

| Menu-item | Beschrijving |
|:--- |:--- |
| Nieuw venster |Klik op **nieuw venster** om een nieuw console venster te openen (naast het bestaande venster). |
| Vensters |Klik op **trapsgewijs** om de geopende console vensters in een trapsgewijze stijl weer te geven. |
| Horizon taal naast elkaar |Klik op **tegel horizon taal** om de geopende console vensters weer te geven in een tegel indeling (of raster). |
| Pictogrammen schikken |Als u meerdere console vensters hebt geopend en verspreid over uw bureau blad, minimaliseert u deze en klikt u vervolgens op **Pictogrammen schikken** om ze in een horizontale rij aan de onderkant van het scherm te rangschikken. |

### <a name="help-menu"></a>Menu Help
Gebruik het menu **Help** om de beschik bare online-Help voor StorSimple Snapshot Manager en de MMC weer te geven. U kunt ook informatie weer geven over de MMC en StorSimple Snapshot Manager software versies die momenteel op uw systeem zijn geïnstalleerd. 

U kunt het menu **Help** openen in de menu balk. U kunt ook toegang krijgen tot StorSimple Snapshot Manager Help-onderwerpen in het deel venster **acties** .

![Menu Help voor StorSimple-Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Menu beschrijving
In de volgende tabel worden de items beschreven die in het menu Help worden weer gegeven.

| Menu-item | Beschrijving |
|:--- |:--- |
| Help bij StorSimple Snapshot Manager |Klik op **Help op StorSimple Snapshot Manager** om StorSimple Snapshot Manager Help in een afzonderlijk venster te openen. |
| Help-onderwerpen |Klik op **Help-onderwerpen** om de online Help van MMC te openen in een afzonderlijk venster. |
| Tech Center-website |Klik op **tech** Center website om de start pagina van micro soft TechNet Tech Center te openen in een afzonderlijk venster. |
| Over micro soft Management Console |Klik op **over micro soft Management Console** om te zien welke versie van micro soft Management Console op uw systeem is geïnstalleerd. |
| Over StorSimple-Snapshot Manager |Klik op **info StorSimple Snapshot Manager** om te zien welke versie van de module op uw systeem is geïnstalleerd. |

## <a name="tool-bar"></a>Werk balk
De werk balk bevindt zich onder de menu balk en bevat navigatie-en taak pictogrammen. Elk pictogram is een snelkoppeling naar een specifieke taak.

### <a name="icon-descriptions"></a>Pictogram beschrijvingen
In de volgende tabel worden de pictogrammen beschreven die worden weer gegeven op de werk balk. 

| Pictogram | Beschrijving |
|:--- |:--- |
| ![Pijl-links](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |Klik op het pictogram met de pijl naar links om terug te keren naar de vorige pagina. |
| ![Pijl-rechts](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |Klik op de pijl-rechts om naar de volgende pagina te gaan (als de pijl grijs is, is de actie niet beschikbaar). |
| ![Pictogram omhoog](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |Klik op het pictogram omhoog om één niveau omhoog te gaan in de console structuur (het deel venster **bereik** ). |
| ![Console structuur weer geven/verbergen](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |Klik op het pictogram console structuur weer geven/verbergen om het deel venster **bereik** weer te geven of te verbergen. |
| ![Lijst exporteren](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |Klik op het pictogram lijst exporteren om een lijst te exporteren naar een CSV-bestand dat u opgeeft. |
| ![Help-pictogram](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |Klik op het pictogram Help om een online Help-onderwerp over MMC te openen. |
| ![Deel venster acties weer geven/verbergen](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |Klik op het pictogram **acties** deel venster weer geven/verbergen om het deel venster **acties** weer te geven of te verbergen. |

## <a name="scope-pane"></a>Deel venster bereik
Het deel venster **bereik** is het meest linkse deel venster in de Snapshot Manager StorSimple-gebruikers interface. Het bevat de console structuur (of knoop punt) en is het primaire navigatie mechanisme voor StorSimple Snapshot Manager. 

### <a name="scope-pane-structure"></a>Structuur van het bereik venster
Het deel venster **bereik** bevat een reeks klikbare objecten (knoop punten) die zijn georganiseerd in een boom structuur. 

![Deel venster bereik](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* Als u een knoop punt wilt uitvouwen of samen vouwen, klikt u op het pijl pictogram naast de naam van het knoop punt.
* Als u de status of de inhoud van een knoop punt wilt weer geven, klikt u op de naam van het knoop punt. De informatie wordt weer gegeven in het deel venster met **resultaten** . 

Het deel venster **bereik** bevat de volgende knoop punten: 

* [Knoop punt apparaten](#devices-node) 
* [Knoop punt volumes](#volumes-node) 
* [Knoop punt volume groepen](#volume-groups-node) 
* [Knoop punt voor back-upbeleid](#backup-policies-node) 
* [Knoop punt voor back-up van catalogus](#backup-catalog-node) 
* [Taak knooppunt](#jobs-node) 

### <a name="scope-pane-tasks"></a>Taken in het deel venster bereik
U kunt het deel venster **bereik** gebruiken om een actie op een specifiek knoop punt te volt ooien. Als u een taak wilt selecteren, voert u een van de volgende handelingen uit:

* Klik met de rechter muisknop op het knoop punt en selecteer de taak in het menu dat wordt weer gegeven.
* Klik op het knoop punt en klik vervolgens op **actie** op de menu balk. Selecteer de taak in het menu dat wordt weer gegeven.
* Klik op het knoop punt en selecteer vervolgens de actie in het deel venster **acties** .

Wanneer u een knoop punt selecteert en een van deze methoden gebruikt om een taken lijst weer te geven, worden alleen de acties weer gegeven die op dat knoop punt kunnen worden uitgevoerd.

### <a name="devices-node"></a>Knoop punt apparaten
Het knoop punt **apparaten** vertegenwoordigt de StorSimple-apparaten en StorSimple virtuele apparaten die zijn verbonden met StorSimple Snapshot Manager. Selecteer dit knoop punt om verbinding te maken met een apparaat en deze te configureren, en importeer de bijbehorende volumes, volume groepen en bestaande back-upkopieën. Meerdere apparaten kunnen worden verbonden met één host.

* Als u het knoop punt wilt uitbreiden, klikt u op het pijl pictogram naast **apparaten**.
* Als u een menu met beschik bare acties wilt weer geven, klikt u met de rechter muisknop op het knoop punt **apparaten** of klikt u met de rechter muisknop op een van de knoop punten in de uitgebreide weer gave.
* Als u een lijst met geconfigureerde apparaten wilt weer geven, klikt u op **apparaten** in het deel venster **bereik** . De lijst met apparaten, samen met informatie over elk apparaat, wordt weer gegeven in het deel venster met **resultaten** .

### <a name="volumes-node"></a>Knoop punt volumes
Het knoop punt **volumes** vertegenwoordigt de stations die overeenkomen met de volumes die zijn gekoppeld door de host, met inbegrip van de schijven die zijn gedetecteerd via iSCSI en die zijn gedetecteerd via een apparaat. Gebruik dit knoop punt om de lijst met beschik bare volumes weer te geven en afzonderlijke volumes aan volume groepen toe te wijzen.

* Als u het knoop punt wilt uitbreiden, klikt u op het pijl pictogram naast **volumes**.
* Als u een menu met beschik bare acties wilt weer geven, klikt u met de rechter muisknop op het knoop punt **volumes** of klikt u met de rechter muisknop op een van de knoop punten in de uitgebreide weer gave.
* Klik op **volumes** in het deel venster **bereik** om een lijst met volumes weer te geven. De lijst met volumes, samen met informatie over elk volume, wordt weer gegeven in het deel venster met **resultaten** .

### <a name="volume-groups-node"></a>Knoop punt volume groepen
Volume groepen worden ook wel consistentie groepen genoemd. Elke volume groep is een groep toepassings gerelateerde volumes waarmee de consistentie van toepassingen tijdens back-upbewerkingen kan worden gewaarborgd. Gebruik het knoop punt **volume groepen** voor het configureren van deze groepen en voor het uitvoeren van interactieve back-ups of het maken van back-upschemas. 

* Als u het knoop punt wilt uitbreiden, klikt u op het pijl pictogram naast **volume groepen**.
* Als u een menu met beschik bare acties wilt zien, klikt u met de rechter muisknop op het knoop punt **volume groepen** of met de rechter muisknop op een van de knoop punten die in de uitgebreide weer gave worden weer gegeven.
* Als u een lijst met volume groepen wilt weer geven, klikt u op **volume groepen** in het deel venster **bereik** . De lijst met volume groepen, samen met informatie over elke volume groep, wordt weer gegeven in het deel venster met **resultaten** .

### <a name="backup-policies-node"></a>Knoop punt voor back-upbeleid
Een back-upbeleid is taak planningen voor lokale en Cloud momentopnamen. Gebruik het knoop punt **back-upbeleid** om op te geven hoe vaak een back-up wordt gemaakt en hoe lang een back-up moet worden bewaard. 

* Klik op het pijl pictogram naast **back-upbeleid** om het knoop punt uit te vouwen.
* Als u een menu met beschik bare acties wilt zien, klikt u met de rechter muisknop op het knoop punt **back-upbeleid** of klikt u met de rechter muisknop op een van de knoop punten in de uitgebreide weer gave.
* Klik op **back-upbeleid** in het deel venster **bereik** om een lijst met back-upbeleid weer te geven. De lijst met back-upbeleiden, samen met informatie over elk beleid, wordt weer gegeven in het deel venster met **resultaten** .

> [!NOTE]
> U kunt Maxi maal 64 back-ups behouden.


### <a name="backup-catalog-node"></a>Knoop punt voor back-up van catalogus
Het **back-upcatalogus** knooppunt bevat lijsten met back-ups op site en off-site van Azure StorSimple-volumes. Dit knoop punt is ingedeeld op volume groep en elke volume groeps container bevat afzonderlijke structuren voor lokale moment opnamen (het knoop punt **lokale moment opnamen** s) en Cloud momentopnamen (het knoop punt voor **Cloud momentopnamen** ). Wanneer dit is uitgevouwen, worden in elke volume groeps container alle geslaagde back-ups weer gegeven die interactief of door een geconfigureerd beleid zijn gemaakt.

* Als u het knoop punt wilt uitbreiden, klikt u op het pijl pictogram naast **back-upcatalogus**.
* Als u een menu met beschik bare acties wilt weer geven, klikt u met de rechter muisknop op het knoop punt **back-upcatalogus** of klikt u met de rechter muisknop op een van de knoop punten in de uitgebreide weer gave
* Als u een lijst met back-upmomentopnamen wilt bekijken, klikt u in het deel venster **bereik** op **back-catalogus** . De lijst met moment opnamen, samen met informatie over elke moment opname, wordt weer gegeven in het deel venster met **resultaten** .

### <a name="local-snapshots-node"></a>Knoop punt lokale moment opnamen
Het knoop punt **lokale moment opnamen** bevat lokale moment opnamen voor een specifieke volume groep. Het knoop punt bevindt zich onder het knoop punt **back-upcatalogus** in het deel venster **bereik** . Lokale moment opnamen zijn tijdgebonden kopieën van volume gegevens die zijn opgeslagen op het Azure StorSimple-apparaat. Normaal gesp roken kan dit type back-up snel worden gemaakt en hersteld. U kunt een lokale moment opname gebruiken, net zoals u een lokale back-up maakt.

* Als u het knoop punt wilt uitbreiden, klikt u op het pijl pictogram naast **lokale moment opnamen**.
* Als u een menu met beschik bare acties wilt weer geven, klikt u met de rechter muisknop op het knoop punt **lokale moment opnamen** of met de rechter muisknop op een van de knoop punten die in de uitgebreide weer gave worden weer gegeven.
* Als u een lijst met lokale moment opnamen wilt zien, klikt u op **lokale moment opnamen** in het deel venster **bereik** . De lijst met moment opnamen, samen met informatie over elke moment opname, wordt weer gegeven in het deel venster met **resultaten** .

### <a name="cloud-snapshots-node"></a>Knoop punt voor Cloud momentopnamen
Het knoop punt voor **Cloud momentopnamen** bevat Cloud momentopnamen voor een specifieke volume groep. Het knoop punt bevindt zich onder het knoop punt **back-upcatalogus** in het deel venster **bereik** . Cloud momentopnamen zijn tijdgebonden kopieën van volume gegevens die in de Cloud zijn opgeslagen. Een Cloud momentopname is gelijk aan een moment opname die wordt gerepliceerd op een ander, off-site opslag systeem. Cloud momentopnamen zijn bijzonder handig in scenario's voor herstel na nood gevallen.

* Als u het knoop punt wilt uitbreiden, klikt u op het pijl pictogram naast **Cloud momentopnamen**.
* Als u een menu met beschik bare acties wilt weer geven, klikt u met de rechter muisknop op het knoop punt voor **Cloud momentopnamen** of klikt u met de rechter muisknop op een van de knoop punten in de uitgebreide weer gave.
* Als u een lijst met Cloud momentopnamen wilt bekijken, klikt u op **Cloud momentopnamen** in het deel venster **bereik** . De lijst met moment opnamen, samen met informatie over elke moment opname, wordt weer gegeven in het deel venster met **resultaten** .

### <a name="jobs-node"></a>Taak knooppunt
Het knoop punt **taken** bevat informatie over geplande, actieve en recent voltooide back-uptaken. 

* Als u het knoop punt wilt uitbreiden, klikt u op het pijl pictogram naast **taken**.
* Als u een menu met beschik bare acties wilt zien, klikt u met de rechter muisknop op het knoop punt **taken** of klikt u met de rechter muisknop op een van de knoop punten in de uitgebreide weer gave.
* Als u een lijst met geplande taken wilt weer geven, vouwt u het knoop punt **taken** uit en klikt u vervolgens op **gepland**. De lijst met eerder geconfigureerde taken en informatie over elke taak wordt weer gegeven in het deel venster met **resultaten** . 
* Als u een lijst met recent voltooide taken wilt weer geven, vouwt u het knoop punt **taken** uit en klikt u vervolgens op **afgelopen 24 uur**. Een lijst met taken die in de afgelopen 24 uur zijn voltooid, wordt weer gegeven in het **resultaten** venster. Het deel venster met **resultaten** bevat ook informatie over elke voltooide taak.
* Als u een lijst met taken wilt zien die momenteel worden uitgevoerd, vouwt u het knoop punt **taken** uit en klikt u vervolgens op **uitvoeren**. De lijst met momenteel actieve taken en informatie over elke taak wordt weer gegeven in het **resultaten** venster.

## <a name="results-pane"></a>Resultaten deel venster
Het deel venster met **resultaten** is het middelste deel venster in de Snapshot Manager StorSimple-gebruikers interface. Het bevat lijsten en gedetailleerde status informatie voor het knoop punt dat u hebt geselecteerd in het deel venster **bereik** .

### <a name="example"></a>Voorbeeld
Als u het volgende voor beeld wilt weer geven, klikt u op het knoop punt **volume groepen** in het deel venster **bereik** . In het deel venster met **resultaten** wordt een lijst met volume groepen weer gegeven met informatie over elke groep.

![Resultaten deel venster](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

U kunt de details configureren die worden weer gegeven in het **resultaten** venster: Klik met de rechter muisknop op een knoop punt in het deel venster **bereik** , klik op **weer geven** en klik vervolgens op **kolommen toevoegen/verwijderen**.

## <a name="actions-pane"></a>Acties deel venster
Het deel venster **acties** is het rechterdeel venster in de Snapshot Manager StorSimple-gebruikers interface. Het bevat een menu met bewerkingen dat u kunt uitvoeren op het knoop punt, de weer gave of de gegevens die u selecteert in het deel venster **bereik** of in het deel venster met **resultaten** . Het deel venster **acties** bevat dezelfde opdrachten als de **actie** menu's die beschikbaar zijn voor items in het deel venster **bereik** en het **resultaten** deel venster. Zie de tabel in de sectie **actie** menu voor een beschrijving van elke actie.

### <a name="examples"></a>Voorbeelden
Als u het volgende voor beeld wilt weer geven, vouwt u in het deel venster **bereik** het knoop punt **taken** uit en klikt u op **gepland**. In het deel venster **acties** worden de beschik bare acties voor het **geplande** knoop punt weer gegeven.

![Voor beeld van geplande taken in het deel venster acties](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

Als u meer opties wilt zien, vouwt u in het deel venster **bereik** het knoop punt **taken** uit, klikt u op **gepland** en klikt u vervolgens op een geplande taak in het **resultaten** venster. In het deel venster **acties** worden de beschik bare acties voor de geplande taak weer gegeven, zoals in het volgende voor beeld wordt weer gegeven.

![Voor beeld van taak acties in het deel venster acties](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Toetsenbord navigatie en snelkoppelingen
StorSimple Snapshot Manager maakt gebruik van de toegankelijkheids functies van het Windows-besturings systeem en de micro soft Management Console (MMC). Het bevat ook een aantal toetsenbord navigatie functies en snelkoppelingen die specifiek zijn voor de StorSimple-Snapshot Manager, zoals beschreven in de volgende secties.

* [Toetsen bord navigatie toetsen](#keyboard-navigation-keys) 
* [Sneltoetsen in de menu balk](#menu-bar-shortcut-keys) 
* [Sneltoetsen in het deel venster bereik](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Toetsen bord navigatie toetsen
In de volgende tabel worden de sleutels beschreven die u kunt gebruiken om te navigeren in de StorSimple-Snapshot Manager gebruikers interface. 

| Navigatie sleutel | Bewerking |
|:--- |:--- |
| Pijl-omlaag |Gebruik de pijl-omlaag om verticaal naar het volgende item in een menu of deel venster te gaan. |
| Enter |Druk op ENTER om een actie te volt ooien en ga vervolgens verder met de volgende stap. U kunt bijvoorbeeld op ENTER drukken om **volgende** te selecteren, op **OK** te klikken of te **maken**, en vervolgens naar de volgende stap in een wizard te gaan. |
| Esc |Druk op de ESC-toets om een menu te sluiten of om een pagina te annuleren en te sluiten. |
| F1 |Druk op de F1-toets om een Help-onderwerp voor het huidige actieve venster weer te geven. |
| F5 |Druk op de toets F5 om een knoop punt te vernieuwen. |
| F6 |Druk op F6 om van het deel venster **bereik** naar het deel venster met **resultaten** te gaan. |
| F10 |Druk op de F10-toets om naar de menu balk te gaan. |
| Pijltoets-links |Gebruik de toets pijl-links om horizon taal te verplaatsen van een menu balk naar de vorige optie. Wanneer u naar het vorige item op de menu balk gaat, wordt het actie-(of context) menu voor het vorige item weer gegeven. |
| Pijltoets-rechts |Gebruik de toets pijl-rechts om horizon taal te verplaatsen van de ene menu balk naar de volgende. Wanneer u naar het volgende item in de menu balk gaat, wordt het actie-(of context) menu voor het nieuwe item weer gegeven. |
| Tab-toets |Gebruik de tab-toets om naar het volgende deel venster op de console of naar de volgende selectie of tekstvak op een pagina te gaan. |
| Pijl-omhoog |Gebruik de pijl-omhoog om verticaal naar het vorige item in een menu of deel venster te gaan. |

### <a name="menu-bar-shortcut-keys"></a>Sneltoetsen in de menu balk
In de volgende tabel worden de toetscombinaties voor de menu balk beschreven. Nadat u op de sneltoetsen hebt gedrukt en het menu wordt geopend, kunt u sneltoetsen gebruiken (de onderstreepte sleutels in het menu). Ga naar de [menu balk](#menu-bar)voor meer informatie over de menu balk.

| Shortcutdimensie | Resultaat | Sneltoets voor menu | Resultaat |
|:--- |:--- |:--- |:--- |
| ALT + F |Hiermee opent u het menu **bestand** . |N |Hiermee opent u een nieuw console-exemplaar. |
|  |O |Hiermee opent u de pagina **systeem beheer** . | |
|  |S |Hiermee slaat u de StorSimple-Snapshot Manager-console op. | |
|  |A |Hiermee opent u de pagina **Opslaan als** . | |
|  |M |Hiermee opent u de pagina **module toevoegen/verwijderen** . | |
|  |P |Hiermee opent u de pagina **Opties** . | |
|  |H |Hiermee opent u online-Help. | |
| ALT + A |Hiermee opent u het menu **actie** . |I |Hiermee wordt de weergave optie importeren in-en uitgeschakeld. |
|  |W |Hiermee opent u een nieuwe StorSimple-Snapshot Manager-console. | |
|  |F |Hiermee wordt de StorSimple-Snapshot Manager-console bijgewerkt. | |
|  |L |Hiermee opent u de pagina **lijst exporteren** . | |
|  |H |Hiermee opent u online-Help. | |
| ALT + V |Hiermee opent u het menu **weer gave** . |A |Hiermee opent u de pagina **kolommen toevoegen/verwijderen** . |
|  |U |Hiermee opent u de pagina **weer gave aanpassen** . | |
| ALT+O |Hiermee opent u het menu **Favorieten** . |A |Hiermee opent **u de pagina toevoegen aan favorieten** . |
|  |O |Hiermee opent u de pagina **Favorieten indelen** . | |
| ALT + W |Hiermee opent u het menu **venster** . |N |Hiermee opent u een ander StorSimple-Snapshot Manager venster. |
|  |C |Geeft alle geopende console vensters weer in een trapsgewijs opmaak profiel. | |
|  |T |Geeft alle geopende console vensters weer in een raster patroon. | |
|  |I |Hiermee rangschikt u pictogrammen in een horizontale rij onder aan het scherm. | |
| ALT + H |Hiermee opent u het menu **Help** . |H |Hiermee opent u online-Help. |
|  |T |Hiermee opent u de micro soft TechNet Tech Center-webpagina. | |
|  |A |Hiermee opent u de pagina **over micro soft Management Console** . | |

### <a name="scope-pane-shortcut-keys"></a>Sneltoetsen in het deel venster bereik
In de volgende tabellen worden de sneltoetsen voor elk knoop punt in het deel venster **bereik** weer gegeven. 

* [Sneltoetsen voor het knoop punt apparaten](#devices-node-shortcut-keys)
* [Sneltoetsen voor het volume knooppunt](#volumes-node-shortcut-keys)
* [Sneltoetsen voor het knoop punt volume groepen](#volume-groups-node-shortcut-keys)
* [Sneltoetsen voor het knoop punt back-upbeleid](#backup-policies-node-shortcut-keys)
* [Sneltoetsen voor back-upcatalogus knooppunt](#backup-catalog-node-shortcut-keys)
* [Sneltoetsen voor taken knooppunt](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Sneltoetsen voor het knoop punt apparaten
| Menu snelkoppeling | Resultaat |
|:--- |:--- |
| C |Hiermee opent **u de pagina een apparaat configureren** . |
| D |Hiermee vernieuwt u de lijst met apparaten en apparaatgegevens. |
| V |Hiermee opent u het menu **weer gave** . |
| W |Hiermee opent u een nieuw StorSimple Snapshot Manager-console, gericht op het knoop punt **Details** . |
| F |Hiermee wordt de StorSimple-Snapshot Manager-console bijgewerkt. |
| L |Hiermee opent u de pagina **lijst exporteren** . |
| H |Hiermee opent u online-Help. |

#### <a name="volumes-node-shortcut-keys"></a>Sneltoetsen voor het volume knooppunt
| Menu snelkoppeling | Resultaat |
|:--- |:--- |
| V |Hiermee wordt de lijst met volumes bijgewerkt. |
| V (twee keer drukken) |Hiermee opent u het menu **weer gave** . |
| W |Hiermee opent u een nieuw StorSimple Snapshot Manager-console, gericht op het knoop punt **volumes** . |
| F |Hiermee wordt de StorSimple-Snapshot Manager-console bijgewerkt. |
| L |Hiermee opent u de pagina **lijst exporteren** . |
| H |Hiermee opent u online-Help. |

#### <a name="volume-groups-node-shortcut-keys"></a>Sneltoetsen voor het knoop punt volume groepen
| Menu snelkoppeling | Resultaat |
|:--- |:--- |
| G |Hiermee opent u de pagina **een volume groep maken** . |
| V |Hiermee opent u het menu **weer gave** . |
| W |Hiermee opent u een nieuw StorSimple Snapshot Manager-console, gericht op het knoop punt **volume groepen** . |
| F |Hiermee wordt de StorSimple-Snapshot Manager-console bijgewerkt. |
| L |Hiermee opent u de pagina **lijst exporteren** . |
| H |Hiermee opent u online-Help. |

#### <a name="backup-policies-node-shortcut-keys"></a>Sneltoetsen voor het knoop punt back-upbeleid
| Menu snelkoppeling | Resultaat |
|:--- |:--- |
| B |Hiermee opent u de pagina **een beleid maken** . |
| V |Hiermee opent u het menu **weer gave** . |
| W |Hiermee opent u een nieuw StorSimple Snapshot Manager-console, gericht op het knoop punt **volume groepen** . |
| F |Hiermee wordt de StorSimple-Snapshot Manager-console bijgewerkt. |
| L |Hiermee opent u de pagina **lijst exporteren** . |
| H |Hiermee opent u online-Help. |

#### <a name="backup-catalog-node-shortcut-keys"></a>Sneltoetsen voor back-upcatalogus knooppunt
| Menu snelkoppeling | Resultaat |
|:--- |:--- |
| W |Hiermee opent u een nieuw StorSimple Snapshot Manager-console, gericht op het knoop punt **volume groepen** . |
| F |Hiermee wordt de StorSimple-Snapshot Manager-console bijgewerkt. |
| H |Hiermee opent u online-Help. |

#### <a name="jobs-node-shortcut-keys"></a>Sneltoetsen voor taken knooppunt
| Menu snelkoppeling | Resultaat |
|:--- |:--- |
| V |Hiermee opent u het menu **weer gave** . |
| W |Hiermee opent u een nieuw StorSimple Snapshot Manager-console, gericht op het knoop punt **taken** . |
| F |Hiermee wordt de StorSimple-Snapshot Manager-console bijgewerkt. |
| L |Hiermee opent u de pagina **lijst exporteren** . |
| H |Online-Help openen |

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [gebruik van StorSimple Snapshot Manager voor het beheren van uw StorSimple-oplossing](storsimple-snapshot-manager-admin.md).
* Meer informatie over het [gebruik van StorSimple Snapshot Manager om apparaten te verbinden en te beheren](storsimple-snapshot-manager-manage-devices.md).
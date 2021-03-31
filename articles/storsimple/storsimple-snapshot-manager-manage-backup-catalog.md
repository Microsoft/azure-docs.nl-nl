---
title: StorSimple Snapshot Manager back-upcatalogus | Microsoft Docs
description: Hierin wordt beschreven hoe u de StorSimple Snapshot Manager MMC-module gebruikt voor het weer geven en beheren van de back-catalogus.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 6abdbfd2-22ce-45a5-aa15-38fae4c8f4ec
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 2efc35e65ca1db2b5241e1d3b2798e068880c87e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90054990"
---
# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>StorSimple Snapshot Manager gebruiken voor het beheren van de back-catalogus

## <a name="overview"></a>Overzicht
Met de primaire functie van StorSimple Snapshot Manager kunt u toepassings consistente back-upkopieën van StorSimple-volumes maken in de vorm van moment opnamen. Moment opnamen worden vervolgens weer gegeven in een XML-bestand met de naam *Backup Catalog*. In de back-upcatalogus worden moment opnamen geordend op volume groep en vervolgens op lokale moment opname of Cloud momentopname.

In deze zelf studie wordt beschreven hoe u het **back-upcatalogus** knooppunt kunt gebruiken om de volgende taken uit te voeren:

* Een volume herstellen
* Een volume of volume groep klonen
* Een back-up verwijderen
* Een bestand herstellen
* De Storsimple Snapshot Manager-Data Base herstellen

U kunt de back-catalogus bekijken door het knoop punt **back-upcatalogus** uit te vouwen in het deel venster **bereik** en vervolgens de volume groep uit te breiden.

* Als u op de naam van de volume groep klikt, wordt in het deel venster met **resultaten** het aantal lokale moment opnamen en de Cloud momentopnamen weer gegeven die beschikbaar zijn voor de volume groep. 
* Als u op **lokale moment opname** of **Cloud momentopname** klikt, wordt in het deel venster met **resultaten** de volgende informatie weer gegeven over elke moment opname van de back-up (afhankelijk van uw **weer gave** -instellingen):
  
  * **Naam** : het tijdstip waarop de moment opname is gemaakt.
  * **Type** : of dit een lokale moment opname of een Cloud momentopname is.
  * **Eigenaar** : de eigenaar van de inhoud. 
  * **Beschikbaar** : of de moment opname momenteel beschikbaar is. **Waar** geeft aan dat de moment opname beschikbaar is en kan worden hersteld. **Onwaar** geeft aan dat de moment opname niet langer beschikbaar is. 
  * **Geïmporteerd** : of de back-up is geïmporteerd. **Waar** geeft aan dat de back-up is geïmporteerd uit de StorSimple-Apparaatbeheer service op het moment dat het apparaat werd geconfigureerd in StorSimple Snapshot Manager; **Onwaar** geeft aan dat deze niet is geïmporteerd, maar is gemaakt door StorSimple Snapshot Manager. (U kunt een geïmporteerde volume groep eenvoudig identificeren omdat er een achtervoegsel wordt toegevoegd dat het apparaat identificeert van waaruit de volume groep is geïmporteerd.)
    
    ![Back-upcatalogus](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)
* Als u **lokale moment opname** of **Cloud momentopname** uitvouwt en vervolgens op een naam van een afzonderlijke moment opname klikt, wordt in het deel venster met **resultaten** de volgende informatie weer gegeven over de moment opname die u hebt geselecteerd:
  
  * **Naam** : het volume dat wordt aangeduid met een stationsletter. 
  * **Lokale naam** : de lokale naam van het station (indien beschikbaar). 
  * **Apparaat** : de naam van het apparaat waarop het volume zich bevindt. 
  * **Beschikbaar** : of de moment opname momenteel beschikbaar is. **Waar** geeft aan dat de moment opname beschikbaar is en kan worden hersteld. **Onwaar** geeft aan dat de moment opname niet langer beschikbaar is. 

## <a name="restore-a-volume"></a>Een volume herstellen
Gebruik de volgende procedure om een volume terug te zetten vanuit een back-up.

#### <a name="prerequisites"></a>Vereisten
Als u dit nog niet hebt gedaan, maakt u een volume en volume groep en verwijdert u het volume. StorSimple Snapshot Manager maakt standaard een back-up van een volume voordat deze wordt verwijderd. Deze voorzorgsmaatregel kan gegevens verlies voor komen als het volume per ongeluk wordt verwijderd of als de gegevens om een of andere reden moeten worden hersteld. 

In StorSimple Snapshot Manager wordt het volgende bericht weer gegeven tijdens het maken van de voorzorgsmaatregel back-up.

![Automatische momentopname bericht](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

> [!IMPORTANT]
> U kunt een volume dat deel uitmaakt van een volume groep niet verwijderen. De optie verwijderen is niet beschikbaar. <br>
> 
> 

#### <a name="to-restore-a-volume"></a>Een volume herstellen
1. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten. 
2. Vouw in het deel venster **bereik** het knoop punt **back-catalogus** uit, vouw een volume groep uit en klik vervolgens op **lokale moment opnamen** of **Cloud momentopnamen**. Er wordt een lijst met back-upmomentopnamen weer gegeven in het **resultaten** venster.
3. Zoek de back-up die u wilt herstellen, klik met de rechter muisknop en klik vervolgens op **herstellen**.
   
    ![Back-upcatalogus herstellen](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 
4. Bekijk op de pagina Bevestiging de details, typ **bevestigen** en klik vervolgens op **OK**. StorSimple Snapshot Manager maakt gebruik van de back-up om het volume te herstellen.
   
    ![Bevestigings bericht herstellen](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 
5. U kunt de herstel actie controleren terwijl deze wordt uitgevoerd. Vouw in het deel venster **bereik** het knoop punt **taken** uit en klik vervolgens op **uitvoeren**. De taak Details worden weer gegeven in het deel venster met **resultaten** . Wanneer de herstel taak is voltooid, worden de taak Details overgebracht naar de lijst van de **afgelopen 24 uur** .

## <a name="clone-a-volume-or-volume-group"></a>Een volume of volume groep klonen
Gebruik de volgende procedure om een duplicaat (kloon) van een volume of volume groep te maken.

#### <a name="to-clone-a-volume-or-volume-group"></a>Een volume of volume groep klonen
1. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten.
2. Vouw in het deel venster **bereik** het knoop punt **back-catalogus** uit, vouw een volume groep uit en klik vervolgens op **Cloud momentopnamen**. Er wordt een lijst met back-ups weer gegeven in het **resultaten** venster.
3. Zoek het volume of de volume groep die u wilt klonen, klik met de rechter muisknop op de naam van het volume of de volume groep en klik op **klonen**. Het dialoog venster **kloon van Cloud momentopname** wordt weer gegeven.
   
    ![Een Cloud momentopname klonen](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Voer de volgende stappen uit om het dialoog venster **Cloud momentopname klonen** te volt ooien: 
   
   1. Typ in het tekstvak **naam** een naam voor het gekloonde volume. Deze naam wordt weer gegeven in het knoop punt **volumes** . 
   2. (Optioneel) Selecteer **station** en selecteer vervolgens een stationsletter in de vervolg keuzelijst.
   3. (Optioneel) Selecteer **map (NTFS)** en typ een mappad of klik op Bladeren en selecteer een locatie voor de map. 
   4. Klik op **Create**.
5. Wanneer het kloon proces is voltooid, moet u het gekloonde volume initialiseren. Start Serverbeheer en start vervolgens schijf beheer. Zie [volumes koppelen](storsimple-snapshot-manager-manage-volumes.md#mount-volumes)voor gedetailleerde instructies. Nadat het volume is geïnitialiseerd, wordt het weer gegeven onder het knoop punt **volumes** in het deel venster **bereik** . Als u het vermelde volume niet ziet, vernieuwt u de lijst met volumes (Klik met de rechter muisknop op het knoop punt **volumes** en klik vervolgens op **vernieuwen**).

## <a name="delete-a-backup"></a>Een back-up verwijderen
Gebruik de volgende procedure om een moment opname te verwijderen uit de back-catalogus. 

> [!NOTE]
> Als u een moment opname verwijdert, worden de back-upgegevens verwijderd die zijn gekoppeld aan de moment opname. Het proces voor het opschonen van gegevens uit de cloud kan echter enige tijd duren.<br>


#### <a name="to-delete-a-backup"></a>Een back-up verwijderen
1. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten.
2. Vouw in het deel venster **bereik** het knoop punt **back-catalogus** uit, vouw een volume groep uit en klik vervolgens op **lokale moment opnamen** of **Cloud momentopnamen**. Er wordt een lijst met moment opnamen weer gegeven in het **resultaten** venster.
3. Klik met de rechter muisknop op de moment opname die u wilt verwijderen en klik vervolgens op **verwijderen**.
4. Wanneer het bevestigings bericht wordt weer gegeven, klikt u op **OK**.

## <a name="recover-a-file"></a>Een bestand herstellen
Als een bestand per ongeluk van een volume wordt verwijderd, kunt u het bestand herstellen door een moment opname op te halen die de verwijdering vooraf bijwerkt, met behulp van de moment opname om een kloon van het volume te maken en vervolgens het bestand van het gekloonde volume naar het oorspronkelijke volume te kopiëren.

#### <a name="prerequisites"></a>Vereisten
Voordat u begint, moet u ervoor zorgen dat u een huidige back-up van de volume groep hebt. Verwijder vervolgens een bestand dat is opgeslagen op een van de volumes in die volume groep. Gebruik tot slot de volgende stappen om het verwijderde bestand te herstellen vanuit de back-up. 

#### <a name="to-recover-a-deleted-file"></a>Een verwijderd bestand herstellen
1. Klik op het Snapshot Manager pictogram StorSimple op het bureau blad. Het venster StorSimple Snapshot Manager console wordt weer gegeven. 
2. Vouw in het deel venster **bereik** het knoop punt **back-catalogus** uit en blader naar een moment opname die het verwijderde bestand bevat. Normaal gesp roken moet u een moment opname selecteren die net vóór het verwijderen is gemaakt.
3. Zoek het volume dat u wilt klonen, klik met de rechter muisknop en klik op **klonen**. Het dialoog venster **kloon van Cloud momentopname** wordt weer gegeven.
   
    ![Een Cloud momentopname klonen](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Voer de volgende stappen uit om het dialoog venster **Cloud momentopname klonen** te volt ooien: 
   
   1. Typ in het tekstvak **naam** een naam voor het gekloonde volume. Deze naam wordt weer gegeven in het knoop punt **volumes** . 
   2. Beschrijving Selecteer **station** en selecteer vervolgens een stationsletter in de vervolg keuzelijst. 
   3. Beschrijving Selecteer **map (NTFS)** en typ een mappad of klik op **Bladeren** en selecteer een locatie voor de map. 
   4. Klik op **Create**. 
5. Wanneer het kloon proces is voltooid, moet u het gekloonde volume initialiseren. Start Serverbeheer en start vervolgens schijf beheer. Zie [volumes koppelen](storsimple-snapshot-manager-manage-volumes.md#mount-volumes)voor gedetailleerde instructies. Nadat het volume is geïnitialiseerd, wordt het weer gegeven onder het knoop punt **volumes** in het deel venster **bereik** . 
   
    Als u het vermelde volume niet ziet, vernieuwt u de lijst met volumes (Klik met de rechter muisknop op het knoop punt **volumes** en klik vervolgens op **vernieuwen**).
6. Open de NTFS-map met het gekloonde volume, vouw het knoop punt **volumes** uit en open vervolgens het gekloonde volume. Zoek het bestand dat u wilt herstellen en kopieer het naar het primaire volume.
7. Nadat u het bestand hebt hersteld, kunt u de NTFS-map met het gekloonde volume verwijderen.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>De StorSimple Snapshot Manager-Data Base herstellen
U moet regel matig een back-up maken van de StorSimple Snapshot Manager-Data Base op de hostcomputer. Als er een nood situatie optreedt of als de hostcomputer om welke reden dan ook mislukt, kunt u deze herstellen vanuit de back-up. Het maken van de back-up van de data base is een hand matig proces.

#### <a name="to-back-up-and-restore-the-database"></a>Back-up maken van de data base en deze herstellen
1. Stop de micro soft StorSimple Management-service:
   
   1. Start Serverbeheer.
   2. Selecteer op het Serverbeheer-dash board de optie **Services** in het menu **extra** .
   3. Selecteer de **micro soft StorSimple Management-service** in het venster **Services** .
   4. Klik in het rechterdeel venster onder **micro soft StorSimple Management service** op **de service stoppen**.
2. Blader op de hostcomputer naar C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > ProgramData is een verborgen map.
   > 
   > 
3. Zoek het XML-bestand van de catalogus, kopieer het bestand en sla de kopie op in een veilige locatie of in de Cloud. Als de host mislukt, kunt u dit back-upbestand gebruiken om het back-upbeleid te herstellen dat u hebt gemaakt in StorSimple Snapshot Manager.
   
    ![Back-upcatalogusbestand van Azure StorSimple](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)
4. Start de micro soft StorSimple Management-service opnieuw: 
   
   1. Selecteer op het Serverbeheer-dash board de optie **Services** in het menu **extra** .
   2. Selecteer de **micro soft StorSimple Management-service** in het venster **Services** .
   3. Klik in het rechterdeel venster onder **micro soft StorSimple Management service** op **Start de service opnieuw**.
5. Blader op de hostcomputer naar C:\ProgramData\Microsoft\StorSimple\BACatalog. 
6. Verwijder het XML-bestand van de catalogus en vervang dit door de back-upversie die u hebt gemaakt. 
7. Klik op het pictogram bureau blad StorSimple Snapshot Manager om StorSimple Snapshot Manager te starten. 

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [het gebruik van StorSimple Snapshot Manager voor het beheren van uw StorSimple-oplossing](storsimple-snapshot-manager-admin.md).
* Meer informatie over [StorSimple-Snapshot Manager taken en-werk stromen](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).


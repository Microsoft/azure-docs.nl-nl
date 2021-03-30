---
title: StorSimple Snapshot Manager en volumes | Microsoft Docs
description: Hierin wordt beschreven hoe u de StorSimple Snapshot Manager MMC-module gebruikt voor het weer geven en beheren van volumes en het configureren van back-ups.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 78896323-e57c-431e-bbe2-0cbde1cf43a2
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: alkohli
ms.openlocfilehash: 309fa85d0a4d877522a89dd8f1e6e71fb2074744
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96022935"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>StorSimple Snapshot Manager gebruiken om volumes weer te geven en te beheren
## <a name="overview"></a>Overzicht
U kunt het knoop punt StorSimple Snapshot Manager **volumes** (in het deel venster **bereik** ) gebruiken om volumes te selecteren en informatie hierover weer te geven. De volumes worden weer gegeven als stations die overeenkomen met de volumes die zijn gekoppeld door de host. Het knoop punt **volumes** bevat lokale volumes en volume typen die worden ondersteund door StorSimple, met inbegrip van volumes die worden gedetecteerd door het gebruik van iSCSI en een apparaat. 

Ga naar [ondersteuning voor meerdere volume typen](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types)voor meer informatie over ondersteunde volumes.

![Volume lijst in resultaten venster](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

Met het knoop punt **volumes** kunt u ook volumes opnieuw scannen of verwijderen nadat StorSimple Snapshot Manager gedetecteerd. 

In deze zelf studie wordt uitgelegd hoe u volumes kunt koppelen, initialiseren en Format teren en vervolgens StorSimple Snapshot Manager gebruiken om het volgende te doen:

* Informatie over volumes weer geven 
* Volumes verwijderen
* Volumes opnieuw scannen 
* Een standaard volume en een back-up configureren
* Een dynamisch gespiegeld volume configureren en een back-up maken

> [!NOTE]
> Alle acties van het **volume** knooppunt zijn ook beschikbaar in het deel venster **acties** .
> 
> 

## <a name="mount-volumes"></a>Volumes koppelen
Gebruik de volgende procedure voor het koppelen, initialiseren en Format teren van StorSimple-volumes. Deze procedure maakt gebruik van schijf beheer, een systeem hulpprogramma voor het beheren van harde schijven en de bijbehorende volumes of partities. Ga naar [schijf beheer](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770943(v=ws.11)) op de website van micro soft TechNet voor meer informatie over schijf beheer.

#### <a name="to-mount-volumes"></a>Volumes koppelen
1. Start de micro soft iSCSI-initiator op de hostcomputer.
2. Geef een van de IP-adressen van de interface op als doel portal of detectie-IP-adres en maak verbinding met het apparaat. Nadat het apparaat is verbonden, zijn de volumes toegankelijk voor uw Windows-systeem. Voor meer informatie over het gebruik van de micro soft iSCSI-initiator gaat u naar de sectie ' verbinding maken met een iSCSI-doel apparaat ' in [micro soft iSCSI-initiator installeren en configureren][1].
3. Gebruik een van de volgende opties om schijf beheer te starten:
   
   * Typ diskmgmt. msc in het vak **uitvoeren** .
   * Start Serverbeheer, vouw het knoop punt **opslag** uit en selecteer vervolgens **schijf beheer**.
   * Start **systeem beheer**, vouw het knoop punt **computer beheer** uit en selecteer vervolgens **schijf beheer**. 
     
     > [!NOTE]
     > U moet Administrator bevoegdheden gebruiken om schijf beheer uit te voeren.
     > 
     > 
4. Maak de volumes online:
   
   1. Klik in schijf beheer met de rechter muisknop op een volume dat **offline** is gemarkeerd.
   2. Klik op **schijf opnieuw activeren**. Als de schijf opnieuw wordt geactiveerd, moet de schijf **online** zijn gemarkeerd.
5. Initialiseer het volume (s):
   
   1. Klik met de rechter muisknop op de gedetecteerde volumes.
   2. Selecteer in het menu de optie **schijf initialiseren**.
   3. Selecteer in het dialoog venster **schijf initialiseren** de schijven die u wilt initialiseren en klik vervolgens op **OK**.
6. Eenvoudige volumes Format teren:
   
   1. Klik met de rechter muisknop op een volume dat u wilt Format teren.
   2. Selecteer in het menu **Nieuw eenvoudig volume**.
   3. Gebruik de wizard Nieuw eenvoudig volume voor het format teren van het volume:
      
      * Geef de grootte van het volume op.
      * Geef een stationsletter op.
      * Selecteer het NTFS-bestands systeem.
      * Geef een clustergrootte van 64 kB op.
      * Voer een snelle formattering uit.
7. Volumes met meerdere partities Format teren. Voor instructies gaat u naar de sectie ' partities en volumes ' in het [implementeren van schijf beheer](/previous-versions/tn-archive/dd163556(v=technet.10)).

## <a name="view-information-about-your-volumes"></a>Informatie over uw volumes weer geven
Gebruik de volgende procedure om informatie weer te geven over lokale en Azure StorSimple-volumes.

#### <a name="to-view-volume-information"></a>Volume gegevens weer geven
1. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten. 
2. Klik in het deel venster **bereik** op het knoop punt **volumes** . Een lijst met lokale en gekoppelde volumes, inclusief alle Azure StorSimple-volumes, wordt weer gegeven in het **resultaten** venster. De kolommen in het deel venster met **resultaten** kunnen worden geconfigureerd. (Klik met de rechter muisknop op het knoop punt **volumes** , selecteer **weer gave** en selecteer vervolgens **kolommen toevoegen/verwijderen**.)
   
    ![De kolommen configureren](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | Kolom met resultaten | Beschrijving |
   |:--- |:--- |
   |  Name |De **naam** kolom bevat de stationsletter die aan elk gedetecteerd volume is toegewezen. |
   |  Apparaat |De kolom **apparaat** bevat het IP-adres van het apparaat dat is verbonden met de hostcomputer. |
   |  Volume naam apparaat |De kolom **volume naam apparaat** bevat de naam van het volume van het apparaat waartoe het geselecteerde volume behoort. Dit is de volume naam die is gedefinieerd in de Azure Portal voor dat specifieke volume. |
   |  Toegangs paden |In de kolom **toegangs paden** wordt het toegangspad naar het volume weer gegeven. Dit is de stationsletter of het koppel punt waarop het volume toegankelijk is op de hostcomputer. |

## <a name="delete-a-volume"></a>Een volume verwijderen
Gebruik de volgende procedure om een volume uit StorSimple Snapshot Manager te verwijderen.

> [!NOTE]
> U kunt een volume niet verwijderen als het deel uitmaakt van een wille keurige volume groep. (De optie verwijderen is niet beschikbaar voor volumes die lid zijn van een volume groep.) U moet de hele volume groep verwijderen om het volume te verwijderen.

#### <a name="to-delete-a-volume"></a>Een volume verwijderen
1. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten.
2. Klik in het deel venster **bereik** op het knoop punt **volumes** . 
3. Klik in het **resultaten** venster met de rechter muisknop op het volume dat u wilt verwijderen.
4. Klik in het menu op **verwijderen**. 
   
    ![Een volume verwijderen](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. Het dialoog venster **volume verwijderen** wordt weer gegeven. Typ **bevestigen** in het tekstvak en klik vervolgens op **OK**.
6. StorSimple Snapshot Manager maakt standaard een back-up van een volume voordat u deze verwijdert. Deze voorzorgsmaatregel kan u beschermen tegen gegevens verlies als het verwijderen onbedoeld is. Met StorSimple Snapshot Manager wordt een voortgangs bericht van **automatische moment opname** weer gegeven terwijl het een back-up van het volume maakt. 
   
    ![Automatische momentopname bericht](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Volumes opnieuw scannen
Gebruik de volgende procedure om de volumes die zijn verbonden met StorSimple Snapshot Manager opnieuw te scannen.

#### <a name="to-rescan-the-volumes"></a>De volumes opnieuw scannen
1. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten.
2. Klik in het deel venster **bereik** met de rechter muisknop op **volumes** en klik vervolgens op **volumes opnieuw scannen**.
   
    ![Volumes opnieuw scannen](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    Met deze procedure wordt de volume lijst gesynchroniseerd met StorSimple Snapshot Manager. Eventuele wijzigingen, zoals nieuwe volumes of verwijderde volumes, worden weer gegeven in de resultaten.

## <a name="configure-and-back-up-a-basic-volume"></a>Een standaard volume configureren en een back-up maken
Gebruik de volgende procedure om een back-up van een standaard volume te configureren, en start vervolgens een back-up onmiddellijk of maak een beleid voor geplande back-ups.

### <a name="prerequisites"></a>Vereisten
Voordat u begint:

* Controleer of het StorSimple-apparaat en de hostcomputer correct zijn geconfigureerd. Ga naar [uw on-premises StorSimple-apparaat implementeren](./storsimple-8000-deployment-walkthrough-u2.md)voor meer informatie.
* Installeer en configureer StorSimple Snapshot Manager. Ga naar [Deploy StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md)voor meer informatie.

#### <a name="to-configure-backup-of-a-basic-volume"></a>De back-up van een basis volume configureren
1. Maak een basis volume op het StorSimple-apparaat.
2. Koppel, Initialiseer en Format teer het volume zoals beschreven in de [koppelings volumes](#mount-volumes). 
3. Klik op het Snapshot Manager pictogram StorSimple op het bureau blad. Het venster StorSimple-Snapshot Manager wordt weer gegeven. 
4. Klik in het deel venster **bereik** met de rechter muisknop op het knoop punt **volumes** en selecteer vervolgens **volumes opnieuw scannen**. Wanneer de scan is voltooid, wordt een lijst met volumes weer gegeven in het **resultaten** venster. 
5. Klik in het **resultaten** venster met de rechter muisknop op het volume en selecteer vervolgens **volume groep maken**. 
   
    ![Een volume groep maken](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. Typ in het dialoog venster **volume groep maken** een naam voor de volume groep, wijs er volumes aan toe en klik vervolgens op **OK**.
7. Vouw in het deel venster **bereik** het knoop punt **volume groepen** uit. De nieuwe volume groep moet worden weer gegeven onder het knoop punt **volume groepen** . 
8. Klik met de rechter muisknop op de naam van de volume groep.
   
   * Als u een interactieve back-uptaak (op aanvraag) wilt starten, klikt u op **back-up maken**. 
   * Als u een automatische back-up wilt plannen, klikt u op **back-upbeleid maken**. Selecteer op de pagina **Algemeen** een volume groep in de lijst. Voer op de pagina **planning** de details van de planning in. Klik op **OK** als u klaar bent. 
9. Als u wilt controleren of de back-uptaak is gestart, vouwt u het knoop punt **taken** in het deel venster **bereik** uit en klikt u vervolgens op het knoop punt dat **wordt uitgevoerd** . De lijst met actieve taken wordt weer gegeven in het **resultaten** venster. 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Een dynamisch gespiegeld volume configureren en een back-up maken
Voer de volgende stappen uit om een back-up te configureren van een dynamisch gespiegeld volume:

* Stap 1: gebruik schijf beheer om een dynamisch gespiegeld volume te maken. 
* Stap 2: gebruik StorSimple Snapshot Manager om een back-up te configureren.

### <a name="prerequisites"></a>Vereisten
Voordat u begint:

* Controleer of het StorSimple-apparaat en de hostcomputer correct zijn geconfigureerd. Ga naar [uw on-premises StorSimple-apparaat implementeren](storsimple-8000-deployment-walkthrough-u2.md)voor meer informatie.
* Installeer en configureer StorSimple Snapshot Manager. Ga naar [Deploy StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md)voor meer informatie.
* Configureer twee volumes op het StorSimple-apparaat. (In de voor beelden zijn de beschik bare volumes **schijf 1** en **schijf 2**.) 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Stap 1: schijf beheer gebruiken om een dynamisch gespiegeld volume te maken
Schijf beheer is een systeem hulpprogramma voor het beheren van harde schijven en de volumes of partities die ze bevatten. Ga naar [schijf beheer](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770943(v=ws.11)) op de website van micro soft TechNet voor meer informatie over schijf beheer.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Een dynamisch gespiegeld volume maken
1. Gebruik een van de volgende opties om schijf beheer te starten: 
   
   * Open het vak **uitvoeren** , typ **diskmgmt. msc** en druk op ENTER.
   * Start Serverbeheer, vouw het knoop punt **opslag** uit en selecteer vervolgens **schijf beheer**. 
   * Start **systeem beheer**, vouw het knoop punt **computer beheer** uit en selecteer vervolgens **schijf beheer**. 
2. Zorg ervoor dat er twee volumes beschikbaar zijn op het StorSimple-apparaat. (In het voor beeld zijn de beschik bare volumes **schijf 1** en **schijf 2**.) 
3. Klik in het venster schijf beheer in de rechter kolom van het onderste deel venster met de rechter muisknop op **schijf 1** en selecteer **Nieuw gespiegeld volume**. 
   
    ![Nieuw gespiegeld volume](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. Klik op de pagina **Nieuw gespiegeld volume** op **volgende**.
5. Selecteer op de pagina **schijven selecteren** de optie **schijf 2** in het **geselecteerde** deel venster, klik op **toevoegen** en klik vervolgens op **volgende**. 
6. Accepteer de standaard instellingen op de pagina stationsletter **of pad toewijzen** en klik op **volgende**. 
7. Selecteer op de pagina **volume Format teren** in het vak **grootte van toewijzings eenheid** de optie **64 KB**. Schakel het selectie vakje **Snelformatteren** in en klik op **volgende**. 
8. Controleer uw instellingen op de pagina **het nieuwe gespiegelde volume volt ooien** en klik vervolgens op **volt ooien**. 
9. Er wordt een bericht weer gegeven om aan te geven dat de standaard schijf wordt geconverteerd naar een dynamische schijf. Klik op **Ja**.
   
    ![Conversie bericht van dynamische schijf](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. Controleer in schijf beheer of schijf 1 en schijf 2 worden weer gegeven als dynamische gespiegelde volumes. (**Dynamisch** moet worden weer gegeven in de kolom Status en de kleur van de capaciteits balk moet worden gewijzigd in rood, wat een gespiegeld volume aangeeft.) 
    
    ![Schijf beheer mirrored dynamische schijven](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Stap 2: StorSimple Snapshot Manager gebruiken om een back-up te configureren
Gebruik de volgende procedure om een dynamisch gespiegeld volume te configureren, en start vervolgens een back-up onmiddellijk of maak een beleid voor geplande back-ups.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Een back-up van een dynamisch gespiegeld volume configureren
1. Klik op het Snapshot Manager pictogram StorSimple op het bureau blad. Het venster StorSimple-Snapshot Manager wordt weer gegeven. 
2. In het deel venster **bereik** klikt u met de rechter muisknop op het knoop punt **volumes** en selecteert u **volumes opnieuw scannen**. Wanneer de scan is voltooid, wordt een lijst met volumes weer gegeven in het **resultaten** venster. Het dynamische gespiegelde volume wordt vermeld als één volume. 
3. Klik in het **resultaten** venster met de rechter muisknop op het dynamische gespiegelde volume en klik vervolgens op **volume groep maken**. 
4. Typ in het dialoog venster **volume groep maken** een naam voor de volume groep, wijs het dynamische gespiegelde volume toe aan deze groep en klik vervolgens op **OK**. 
5. Vouw in het deel venster **bereik** het knoop punt **volume groepen** uit. De nieuwe volume groep moet worden weer gegeven onder het knoop punt  **volume groepen** . 
6. Klik met de rechter muisknop op de naam van de volume groep. 
   
   * Als u een interactieve back-uptaak (op aanvraag) wilt starten, klikt u op **back-up maken**. 
   * Als u een automatische back-up wilt plannen, klikt u op **back-upbeleid maken**. Selecteer op de pagina **Algemeen** de volume groep in de lijst. Voer op de pagina **planning** de details van de planning in. Klik op **OK** als u klaar bent. 
7. U kunt de back-uptaak controleren terwijl deze wordt uitgevoerd. Vouw in het deel venster **bereik** het knoop punt **taken** uit en klik vervolgens op **uitvoeren**. de taak Details worden weer gegeven in het deel venster met **resultaten** . Wanneer de back-uptaak is voltooid, worden de details overgebracht naar de taken lijst van de **afgelopen 24** uur. 

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [gebruik van StorSimple Snapshot Manager voor het beheren van uw StorSimple-oplossing](storsimple-snapshot-manager-admin.md).
* Meer informatie over het [gebruik van StorSimple Snapshot Manager om volume groepen te maken en te beheren](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee338480(v=ws.10)
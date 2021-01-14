---
title: StorSimple Snapshot Manager-volume groepen | Microsoft Docs
description: Hierin wordt beschreven hoe u de StorSimple Snapshot Manager MMC-module gebruikt om volume groepen te maken en te beheren.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 7a232414-6a28-4b81-bd7b-cf61e28b33d7
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: df2d74701e21c3773d96564f1b06d80ddb9fce9d
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209216"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>StorSimple Snapshot Manager gebruiken om volume groepen te maken en te beheren
## <a name="overview"></a>Overzicht
U kunt het knoop punt **volume groepen** in het deel venster **bereik** gebruiken om volumes aan volume groepen toe te wijzen, informatie over een volume groep te bekijken, back-ups te plannen en volume groepen te bewerken.

Volume groepen zijn groepen van gerelateerde volumes die worden gebruikt om ervoor te zorgen dat back-ups toepassings consistent zijn. Zie [volumes en volume groepen](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) en [integratie met Windows Volume Shadow Copy service](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service)voor meer informatie.

> [!IMPORTANT]
> * Alle volumes in een volume groep moeten afkomstig zijn van één Cloud serviceprovider.
> * Wanneer u volume groepen configureert, mag u geen cluster Shared volumes (Csv's) en niet-Csv's in dezelfde volume groep mengen. StorSimple Snapshot Manager biedt geen ondersteuning voor een combi natie van Csv's en niet-Csv's in dezelfde moment opname.

![Knoop punt volume groepen](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Afbeelding 1: StorSimple Snapshot Manager knoop punt voor volume groepen** 

In deze zelf studie wordt uitgelegd hoe u StorSimple Snapshot Manager kunt gebruiken om het volgende te doen:

* Informatie over uw volume groepen weer geven
* Een volume groep maken
* Back-up maken van een volume groep
* Een volume groep bewerken
* Een volume groep verwijderen

Al deze acties zijn ook beschikbaar in het deel venster **acties** .

## <a name="view-volume-groups"></a>Volume groepen weer geven
Als u op het knoop punt **volume groepen** klikt, wordt in het deel venster met **resultaten** de volgende informatie over elke volume groep weer gegeven, afhankelijk van de kolom selecties die u maakt. (De kolommen in het deel venster met **resultaten** kunnen worden geconfigureerd. Klik met de rechter muisknop op het knoop punt **volumes** , selecteer **weer gave** en selecteer vervolgens **kolommen toevoegen/verwijderen**.)

| Kolom met resultaten | Beschrijving |
|:--- |:--- |
| Naam |De **naam** kolom bevat de naam van de volume groep. |
| Toepassing |De kolom **toepassingen** toont het aantal VSS-schrijvers dat momenteel is geïnstalleerd en wordt uitgevoerd op de Windows-host. |
| Geselecteerd |In de **geselecteerde** kolom ziet u het aantal volumes dat is opgenomen in de volume groep. Een nul (0) geeft aan dat er geen toepassing is gekoppeld aan de volumes in de volume groep. |
| Voeren |In de **geïmporteerde** kolom ziet u het aantal geïmporteerde volumes. Als deze eigenschap is ingesteld op **True**, wordt met deze kolom aangegeven dat een volume groep is geïmporteerd uit het Azure Portal en niet is gemaakt in StorSimple Snapshot Manager. |

> [!NOTE]
> StorSimple Snapshot Manager volume groepen worden ook weer gegeven op het tabblad **back-upbeleid** in de Azure Portal.
> 
> 

## <a name="create-a-volume-group"></a>Een volume groep maken
Gebruik de volgende procedure om een volume groep te maken.

#### <a name="to-create-a-volume-group"></a>Een volume groep maken
1. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten.
2. Klik in het deel venster **bereik** met de rechter muisknop op **volume groepen** en klik vervolgens op **volume groep maken**.
   
    ![Een volume groep maken](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    Het dialoog venster **een volume groep maken** wordt weer gegeven.
   
    ![Een dialoog venster voor een volume groep maken](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. Voer de volgende informatie in:
   
   1. Typ in het vak **naam** een unieke naam voor de nieuwe volume groep.
   2. Selecteer in het vak **toepassingen** de optie toepassingen die zijn gekoppeld aan de volumes die u aan de volume groep wilt toevoegen.
      
       Het vak **toepassingen** bevat alleen de toepassingen die gebruikmaken van StorSimple-volumes en waarvoor VSS-schrijvers zijn ingeschakeld. Een VSS Writer wordt alleen ingeschakeld als alle volumes waarvan de schrijver bekend is, StorSimple-volumes zijn. Als het vak toepassingen leeg is, worden er geen toepassingen geïnstalleerd die gebruikmaken van Azure StorSimple-volumes en worden ondersteuning geboden voor VSS-schrijvers. (Momenteel ondersteunt Azure StorSimple micro soft Exchange en SQL Server.) Zie [integratie met Windows Volume Shadow Copy service](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service)voor meer informatie over VSS-schrijvers.
      
       Als u een toepassing selecteert, worden alle volumes die eraan zijn gekoppeld, automatisch geselecteerd. Als u daarentegen volumes selecteert die zijn gekoppeld aan een specifieke toepassing, wordt de toepassing automatisch geselecteerd in het vak **toepassingen** . 
   3. Selecteer in het vak **volumes** StorSimple-volumes die u aan de volume groep wilt toevoegen. 
      
      * U kunt volumes met één of meerdere partities toevoegen. (Meerdere partitie volumes kunnen dynamische schijven of standaard schijven met meerdere partities zijn.) Een volume dat meerdere partities bevat, wordt beschouwd als één eenheid. Als u slechts een van de partities aan een volume groep toevoegt, worden de andere partities op hetzelfde moment automatisch toegevoegd aan die volume groep. Nadat u een meerdere partitie volume aan een volume groep hebt toegevoegd, blijft het volume met meerdere partities als één eenheid worden behandeld.
      * U kunt lege volume groepen maken door er geen volumes aan toe te wijzen. 
      * Maak geen mix van cluster Shared volumes (Csv's) en niet-Csv's in dezelfde volume groep. StorSimple Snapshot Manager biedt geen ondersteuning voor een combi natie van CSV-volumes en niet-CSV-volumes in dezelfde moment opname.
4. Klik op **OK** om de volume groep op te slaan.

## <a name="back-up-a-volume-group"></a>Back-up maken van een volume groep
Gebruik de volgende procedure om een back-up van een volume groep te maken.

#### <a name="to-back-up-a-volume-group"></a>Een back-up maken van een volume groep
1. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten.
2. Vouw in het deel venster **bereik** het knoop punt **volume groepen** uit, klik met de rechter muisknop op de naam van de volume groep en klik vervolgens op **back-up maken**.
   
    ![Direct een back-up maken van de volume groep](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. Selecteer in het dialoog venster **back-up** maken de optie **lokale moment opname** of **Cloud momentopname** en klik vervolgens op **maken**.
   
    ![Dialoog venster back-up maken](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. Als u wilt bevestigen dat de back-up wordt uitgevoerd, vouwt u het knoop punt **taken** uit en klikt u vervolgens op **uitvoeren**. De back-up moet worden weer gegeven.
5. Als u de voltooide moment opname wilt weer geven, vouwt u het knoop punt **Backup Catalog** uit, vouwt u de naam van de volume groep en klikt u vervolgens op **lokale moment opname** of **Cloud momentopname**. De back-up wordt weer gegeven als deze is voltooid.

## <a name="edit-a-volume-group"></a>Een volume groep bewerken
Gebruik de volgende procedure om een volume groep te bewerken.

#### <a name="to-edit-a-volume-group"></a>Een volume groep bewerken
1. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten.
2. Vouw in het deel venster **bereik** het knoop punt **volume groepen** uit, klik met de rechter muisknop op de naam van de volume groep en klik vervolgens op **bewerken**.
3. Het dialoog venster **een volume groep maken** wordt weer gegeven. U kunt de **naam**, de **toepassingen** en de **volume** vermeldingen wijzigen.
4. Klik op **OK** om uw wijzigingen op te slaan.

## <a name="delete-a-volume-group"></a>Een volume groep verwijderen
Gebruik de volgende procedure om een volume groep te verwijderen. 

> [!WARNING]
> Hiermee verwijdert u ook alle back-ups die zijn gekoppeld aan de volume groep.
> 
> 

#### <a name="to-delete-a-volume-group"></a>Een volume groep verwijderen
1. Klik op het pictogram bureau blad om StorSimple Snapshot Manager te starten.
2. Vouw in het deel venster **bereik** het knoop punt **volume groepen** uit, klik met de rechter muisknop op de naam van een volume groep en klik vervolgens op **verwijderen**.
3. Het dialoog venster **volume groep verwijderen** wordt weer gegeven. Typ **bevestigen** in het tekstvak en klik vervolgens op **OK**.
   
    De verwijderde volume groep maakt deel uit van de lijst in het **resultaten** venster en alle back-ups die aan die volume groep zijn gekoppeld, worden uit de back-upcatalogus verwijderd.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [gebruik van StorSimple Snapshot Manager voor het beheren van uw StorSimple-oplossing](storsimple-snapshot-manager-admin.md).
* Meer informatie over het [gebruik van StorSimple Snapshot Manager voor het maken en beheren van back-upbeleid](storsimple-snapshot-manager-manage-backup-policies.md).


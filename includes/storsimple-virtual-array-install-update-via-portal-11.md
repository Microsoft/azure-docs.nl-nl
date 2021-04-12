---
title: bestand opnemen
description: bestand opnemen
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 07/18/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 8ef8b9d3350d0599ab00dfdbb018cf8dd900e316
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95557550"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Updates installeren via Azure Portal

1. Ga naar uw StorSimple Device Manager en selecteer **Apparaten**. Selecteer in de lijst met apparaten die zijn verbonden met uw service, het apparaat dat u wilt bijwerken.

2. Klik onder **instellingen** op **updates voor apparaten**.  

3. Als er software-updates beschikbaar zijn, wordt er een bericht weergegeven. U kunt ook op **Scannen** klikken om naar updates te zoeken. Noteer de software versie die u gebruikt. 

    ![Het deel venster apparaat bijwerken bevat de tekst "er zijn ' nieuwe updates beschikbaar ' (gemarkeerd) en ' laatst gescand/6/22/2018 2:46 PM/software versie/10.0.10296.0 '. De versie is gemarkeerd.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate3m1.png)

    U ontvangt een melding wanneer de scan wordt gestart en voltooid.
 
4. Nadat de updates zijn gescand, klikt u op **Updates downloaden**. Bekijk de opmerkingen bij de release onder **nieuwe updates**. Nadat de updates zijn gedownload, moet u de installatie bevestigen. Klik op **OK**.

    ![Bij het bijwerken van apparaten is de knop updates downloaden gemarkeerd. Bij nieuwe updates is er een koppeling naar release opmerkingen en een bericht om de installatie te bevestigen nadat de updates zijn gedownload. Er is een knop OK.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate6m.png)

    U ontvangt een melding wanneer de upload wordt gestart en wanneer deze is voltooid.

5. Klik onder **updates voor apparaten** op **installeren**.

     ![Updates voor apparaten: "Bevestig de installatie van updates". De knop installeren en de software versie zijn gemarkeerd.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate11m1.png)

6. Onder **nieuwe updates** wordt u gewaarschuwd dat de update is verstoord. Als de virtuele matrix een apparaat met een enkel knooppunt is, wordt het apparaat opnieuw opgestart nadat het is bijgewerkt. Dit verstoort alle actieve IO. Klik op **OK** om de updates te installeren.

    ![Nieuwe updates geeft aan dat het apparaat een downtime heeft wanneer deze updates worden geïnstalleerd. Er is een koppeling naar release opmerkingen en een knop OK.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate12m.png)

    U ontvangt een melding wanneer de installatietaak wordt gestart.

7.  Nadat de installatie taak is voltooid, klikt u op **taak koppeling weer geven** . Met deze actie gaat u naar de Blade **updates installeren** . Hier kunt u gedetailleerde informatie over de taak weergeven. 

    ![Apparaat-updates hebben een koppeling met de label ' updates installeren. Taak weer geven.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate16m1.png)

8. Als u bent begonnen met een virtuele array met software versie update 1 (10.0.10296.0), voert u nu update 1,1 uit en bent u klaar. U kunt de resterende stappen overs Laan. 

    Als u bent begonnen met een virtuele array met software versie update 0,6 (10.0.10293.0), bent u nu bijgewerkt met het bijwerken van 1,0. U ziet een ander bericht dat aangeeft dat er updates beschikbaar zijn. Herhaal stap 4-7 om update 1,1 te installeren.

    


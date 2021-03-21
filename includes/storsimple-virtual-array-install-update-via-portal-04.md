---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 48ce594fa5f4b736e69b5b4ef7a10011fe8031fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96027219"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Updates installeren via Azure Portal

1. Ga naar uw StorSimple Device Manager en selecteer **Apparaten**. Selecteer in de lijst met apparaten die zijn verbonden met uw service, het apparaat dat u wilt bijwerken. 

    ![In recent is Apparaatbeheer MySSDevManager gemarkeerd en geselecteerd, worden apparaten gemarkeerd en geselecteerd en wordt MYSSIS1103 gemarkeerd en geselecteerd.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. Klik op de blade **Instellingen** op **Apparaatupdates**. 

    ![Informatie voor MYSSIS1103 wordt weer gegeven. In instellingen worden updates van apparaten gemarkeerd.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. Als er software-updates beschikbaar zijn, wordt er een bericht weergegeven. U kunt ook op **Scannen** klikken om naar updates te zoeken.

    ![Het deel venster apparaat bijwerken bevat de tekst "er zijn nieuwe updates beschikbaar" en "laatst gescand/18/01/2017 2:42 PM/software versie/10.0.10288.0". De versie is gemarkeerd.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate3m1.png)

    U ontvangt een melding wanneer de scan wordt gestart en wanneer deze is voltooid.

    ![De melding bevat updates voor apparaat MySSIS1103 scannen. 2:12 PM/de bewerking is voltooid. "](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate5m.png)

4. Nadat de updates zijn gescand, klikt u op **Updates downloaden**. 

    ![In het deel venster apparaat bijwerken ziet u het bericht ' er zijn nieuwe updates beschikbaar '. De knop updates downloaden is gemarkeerd.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate6m.png)

5. Controleer op de blade **Nieuwe updates** de informatie die u nodig hebt om de installatie te bevestigen nadat de updates zijn gedownload. Klik op **OK**.

    ![In het deel venster nieuwe updates ziet u "nadat de updates zijn gedownload, moet u de installatie bevestigen". De knop OK is gemarkeerd.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate7m.png)

6. U ontvangt een melding wanneer de upload wordt gestart en wanneer deze is voltooid.

     ![De melding bevat ' updates downloaden voor MySSIS1 van het apparaat... 2:13 PM ".](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate8m.png)

5. Klik op de blade **Apparaatupdates** op **Installeren**.

     ![Updates voor apparaten: "Bevestig de installatie van updates". Er is een knop installeren.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate11m1.png)   

6. Op de blade **Nieuwe updates** krijgt u een waarschuwing dat de update verstorend is. Als de virtuele matrix een apparaat met een enkel knooppunt is, wordt het apparaat opnieuw opgestart nadat het is bijgewerkt. Dit verstoort alle actieve IO. Klik op **OK** om de updates te installeren. 

    ![Het bericht in het deel venster nieuwe updates is ' uw apparaat heeft een downtime wanneer deze updates worden geïnstalleerd '. De knop OK is gemarkeerd.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate12m.png) 

7. U ontvangt een melding wanneer de installatietaak wordt gestart. 

    ![De melding bevat ' updates installeren voor MySSIS1103 van het apparaat. 2:15 PM ".](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate13m.png)

8.  Nadat de installatietaak is voltooid, klikt u op de koppeling **Taak weergeven** op de blade **Apparaatupdates** om de installatie te controleren. 

    ![In het deel venster apparaat bijwerken vindt u een koppeling met de label ' updates installeren. Taak weer geven.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate15m1.png)

    Hiermee gaat u naar de blade **Updates installeren**. Hier kunt u gedetailleerde informatie over de taak weergeven.

    ![Het deel venster installatie updates bevat installatie-informatie, waaronder apparaat, status, duur, start tijd en stop tijd.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate16m1.png)

9. Nadat de updates zijn geïnstalleerd, wordt hierover een bericht weergegeven op de blade **Apparaatupdates**. 

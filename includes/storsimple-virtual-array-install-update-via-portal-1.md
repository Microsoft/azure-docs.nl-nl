---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 3129bbe171329ecf37f8712394cedf9b70188220
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005810"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Updates installeren via Azure Portal

1. Ga naar uw StorSimple Device Manager en selecteer **Apparaten**. Selecteer in de lijst met apparaten die zijn verbonden met uw service, het apparaat dat u wilt bijwerken.

    ![In recent is Apparaatbeheer MySSDevManager gemarkeerd en geselecteerd, worden apparaten gemarkeerd en geselecteerd en wordt MYSSIS1103 gemarkeerd en geselecteerd.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. Klik op de blade **Instellingen** op **Apparaatupdates**.

    ![Informatie voor MYSSIS1103 wordt weer gegeven. In instellingen worden updates van apparaten gemarkeerd.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. Als er software-updates beschikbaar zijn, wordt er een bericht weergegeven. U kunt ook op **Scannen** klikken om naar updates te zoeken. Noteer de software versie die u gebruikt. 

    ![Het deel venster apparaat bijwerken bevat de tekst "er zijn nieuwe updates beschikbaar" en "laatst gescand/11/01/2017 10:56 AM/software versie/10.0.10289.0". De versie is gemarkeerd.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate3m1.png)

    U ontvangt een melding wanneer de scan wordt gestart en voltooid.

    ![De melding bevat ' updates scannen voor MySVAFS110 van het apparaat.... 10:57 AM/de bewerking wordt uitgevoerd. "](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate5m.png)

4. Nadat de updates zijn gescand, klikt u op **Updates downloaden**.

    ![In het deel venster apparaat bijwerken ziet u het bericht ' er zijn nieuwe updates beschikbaar '. De knop updates downloaden is gemarkeerd.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate6m.png)

5. Bekijk de opmerkingen bij de release op de Blade **nieuwe updates** . Nadat de updates zijn gedownload, moet u de installatie bevestigen. Klik op **OK**.

    ![In het deel venster nieuwe updates ziet u "nadat de updates zijn gedownload, moet u de installatie bevestigen". De knop OK is gemarkeerd.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate7m.png)

6. U ontvangt een melding wanneer de upload wordt gestart en wanneer deze is voltooid.

     ![De melding bevat ' updates downloaden voor MySVAFS van het apparaat... 11:07 '.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate8m.png)

5. Klik op de blade **Apparaatupdates** op **Installeren**.

     ![Updates voor apparaten: "Bevestig de installatie van updates". De knop installeren is gemarkeerd.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate11m1.png)

6. Op de blade **Nieuwe updates** krijgt u een waarschuwing dat de update verstorend is. Als de virtuele matrix een apparaat met een enkel knooppunt is, wordt het apparaat opnieuw opgestart nadat het is bijgewerkt. Dit verstoort alle actieve IO. Klik op **OK** om de updates te installeren.

    ![Het bericht in het deel venster nieuwe updates is ' uw apparaat heeft een downtime wanneer deze updates worden geïnstalleerd '. De knop OK is gemarkeerd.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate12m.png)

7. U ontvangt een melding wanneer de installatietaak wordt gestart.

    ![De melding bevat de tekst ' updates installeren voor MySVAFS110.... 11:09 '.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate13m.png)

8.  Nadat de installatietaak is voltooid, klikt u op de koppeling **Taak weergeven** op de blade **Apparaatupdates** om de installatie te controleren. 

    ![In het deel venster apparaat bijwerken vindt u een koppeling met de label ' updates installeren. Taak weer geven. De knop installeren is gemarkeerd.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate15m1.png)

    Met deze actie gaat u naar de Blade **updates installeren** . Hier kunt u gedetailleerde informatie over de taak weergeven.

    ![Het deel venster installatie updates bevat installatie-informatie, waaronder apparaat, status, duur, start tijd en stop tijd.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate16m1.png)

9. Als u bent begonnen met een virtuele array met software versie update 0,6 (10.0.10293.0), voert u update 1 uit en bent u klaar. U kunt de resterende stappen overs Laan. Als u bent begonnen met een virtuele matrix met een software versie vóór update 0,6 (10.0.10293.0), bent u nu bijgewerkt met het bijwerken van 0,6. U ziet een ander bericht dat aangeeft dat er updates beschikbaar zijn. Herhaal stap 4-8 om update 1 te installeren.

    ![Het deel venster apparaat bijwerken bevat de tekst "er zijn nieuwe updates beschikbaar" en "laatst gescand/11/1/2017 10:56 AM/software versie/10.0.10293.0".](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate17.png)


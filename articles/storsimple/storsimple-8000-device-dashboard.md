---
title: Apparaat-samen vatting gebruiken in StorSimple Apparaatbeheer-service
description: Beschrijft de samen vatting van StorSimple Apparaatbeheer service-apparaat en hoe u deze kunt gebruiken om metrische gegevens over de opslag en verbonden initia tors weer te geven en het serie nummer en de IQN te vinden.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 34bbf4d723e46663efe77560245db74d9dea1fc4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017148"
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>Het apparaat samen vatting gebruiken in StorSimple Apparaatbeheer-service

## <a name="overview"></a>Overzicht
De Blade samen vatting van StorSimple biedt u een overzicht van de informatie voor een specifiek StorSimple-apparaat, in tegens telling tot de Blade samen vatting van de service, die u informatie geeft over alle apparaten die in uw Microsoft Azure StorSimple oplossing zijn opgenomen.

Op de Blade samen vatting van apparaten vindt u een overzichts weergave van een StorSimple 8000 Series-apparaat dat is geregistreerd met een bepaalde StorSimple-Apparaatbeheer, waarbij u de problemen met het apparaat markeert waarvoor een systeem beheerder is vereist. In deze zelf studie wordt de Blade samen vatting van apparaten geïntroduceerd, worden de inhoud en functie uitgelegd en worden de taken beschreven die u kunt uitvoeren vanuit deze Blade.

Op de Blade samen vatting van apparaten wordt de volgende informatie weer gegeven:

![Blade overzicht van apparaten](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>Opdracht balk beheer

Op de Blade StorSimple-apparaat ziet u de opties voor het beheren van uw StorSimple-apparaat. U ziet de beheer opdrachten aan de bovenkant van de Blade en aan de linkerkant. Gebruik deze opties om shares of volumes toe te voegen, of om uw apparaat bij te werken of te laten mislukken.

![Opdracht balk beheer](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>Essentials

In het gebied essentiële elementen worden enkele belang rijke eigenschappen vastgelegd, zoals de status, het model, de IQN van het doel en de software versie. 

![Essentiële apparaten](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>Bewaking

* De tegel **waarschuwingen** bevat een moment opname van alle actieve waarschuwingen voor uw apparaat, gegroepeerd op ernst van de waarschuwing.

    ![Waarschuwings tegel](./media/storsimple-8000-device-dashboard/device-summary4.png)

    Klik op de tegel om de Blade **waarschuwingen** te openen en klik vervolgens op een afzonderlijke waarschuwing om aanvullende informatie over die waarschuwing weer te geven, inclusief eventuele aanbevolen acties. U kunt ook de waarschuwing wissen als het probleem is opgelost.

    ![Klik op waarschuwings tegel](./media/storsimple-8000-device-dashboard/device-summary10.png)

* Op de tegel **status en** status vindt u inzicht in de status van het hardwareonderdeel voor een apparaat, met inbegrip van de Apparaatstatus. De apparaatstatus kan offline, online, gedeactiveerd of klaar zijn om te worden ingesteld.

    ![Status-en status tegel](./media/storsimple-8000-device-dashboard/device-summary5.png)

* De tegel **volumes** bevat een samen vatting van het aantal volumes in uw apparaat, gegroepeerd op status.

    ![Tegel volumes](./media/storsimple-8000-device-dashboard/device-summary6.png)

    Klik op de tegel om de Blade **volume** lijst te openen en klik vervolgens op een afzonderlijk volume om de eigenschappen weer te geven of te wijzigen.
    
    ![Klik op volume tegel](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    Zie [volumes beheren](storsimple-8000-manage-volumes-u2.md)voor meer informatie.

* In het **gebruiks** diagram kunt u de primaire opslag weer geven die wordt gebruikt op uw apparaat en de Cloud opslag verbruikt in de afgelopen 7 dagen, de standaard periode.

     ![Gebruiks tegel](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     Als u een andere tijd schaal wilt kiezen, gebruikt u de optie **bewerken** in de rechter bovenhoek van de grafiek.

     ![Gebruiks diagram bewerken](./media/storsimple-8000-device-dashboard/device-summary12.png)

     In deze grafiek kunt u metrische gegevens weer geven voor de totale primaire opslag (de hoeveelheid door hosts naar uw apparaat geschreven) en de totale Cloud opslag die gedurende een bepaalde periode door uw apparaat wordt gebruikt.
  
     In deze context verwijst *primaire opslag* naar de totale hoeveelheid gegevens die is geschreven door de host en kan deze worden onderverdeeld op volume type: *primaire, gelaagde opslag* omvat lokaal opgeslagen gegevens en gelaagde gegevens in de Cloud. *Primaire lokaal vastgemaakte opslag* bevat alleen gegevens die lokaal zijn opgeslagen. *Cloud opslag* is aan de andere kant een meting van de totale hoeveelheid gegevens die in de Cloud is opgeslagen. Deze opslag bevat gelaagde gegevens en back-ups. De gegevens die in de Cloud zijn opgeslagen, worden ontdubbeld en gecomprimeerd, terwijl de primaire opslag de hoeveelheid opslag aangeeft die wordt gebruikt voordat de gegevens worden ontdubbeld en gecomprimeerd. (U kunt deze twee getallen vergelijken om een idee te krijgen van de compressie frequentie.) Voor zowel de primaire als de Cloud opslag zijn de weer gegeven hoeveel heden gebaseerd op de tracerings frequentie die u configureert. Als u bijvoorbeeld een frequentie van één week kiest, toont de grafiek de gegevens voor elke dag in de vorige week.

     Selecteer de optie **Cloud opslag die is gebruikt** voor een overzicht van de hoeveelheid Cloud opslag die gedurende een bepaalde periode is verbruikt. Als u de totale opslag die is geschreven door de host wilt zien, selecteert u de optie **primaire GElaagde opslag** en **gebruikte primaire lokaal vastgemaakte opslag** . 
     Zie [de StorSimple Apparaatbeheer-service gebruiken om uw StorSimple-apparaat te controleren](./storsimple-8000-monitor-device.md)voor meer informatie.


* In de tegel **capaciteit** wordt de primaire opslag weer gegeven die op het apparaat is ingericht en overgebleven ten opzichte van de totale beschik bare opslag. **Ingericht** heeft betrekking op de hoeveelheid opslag ruimte die is voor bereid en toegewezen voor gebruik. het **resterende gedeelte** verwijst naar de resterende capaciteit die op dit apparaat kan worden ingericht. 

    ![Gebruiks tegel 2](./media/storsimple-8000-device-dashboard/device-summary8.png)

    Klik op deze tegel om te zien hoe de capaciteit wordt ingericht op gelaagde en lokaal vastgemaakte volumes. De **resterende gelaagde** capaciteit is de beschik bare capaciteit die kan worden ingericht, inclusief de Cloud, terwijl de **resterende lokale** capaciteit het resterende aantal schijven is dat is gekoppeld aan dit apparaat.

    ![Klik op gebruiks diagram](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de [Blade overzicht](storsimple-8000-service-dashboard.md)van de StorSimple-service.
* Meer informatie over [het gebruik van de StorSimple Apparaatbeheer-service voor het beheren van uw StorSimple-apparaat](storsimple-8000-manager-service-administration.md).
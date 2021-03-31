---
title: Service overzicht-Blade gebruiken voor StorSimple-apparaat van de 8000-serie
description: Hierin wordt de Blade samen vatting van de StorSimple-service beschreven en wordt uitgelegd hoe u deze kunt gebruiken om de status van uw StorSimple-oplossing te controleren.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: 7b4b697b3d27b57212fc59396e1f8111e297d6cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85514945"
---
# <a name="use-the-service-summary-blade-for-storsimple-8000-series-device"></a>De Blade samen vatting van de service gebruiken voor het StorSimple-apparaat van de 8000-serie

## <a name="overview"></a>Overzicht

De Blade StorSimple Apparaatbeheer service overzicht biedt een overzichts weergave van alle apparaten die zijn verbonden met de StorSimple Apparaatbeheer-service, waarbij u de apparaten markeert die de aandacht van een systeem beheerder nodig hebben. In deze zelf studie wordt de Blade service overzicht geïntroduceerd, wordt de inhoud en functie van het dash board uitgelegd en worden de taken beschreven die u vanaf deze pagina kunt uitvoeren.

![Service overzicht](./media/storsimple-8000-service-dashboard/service-summary1.png)


## <a name="management-commands"></a>Opdrachten voor beheer

Op de Blade overzicht van de StorSimple-service ziet u de opties voor het beheren van uw StorSimple Apparaatbeheer-service en de StorSimple 8000 Series-apparaten die zijn geregistreerd bij deze service. U ziet de beheer opdrachten aan de bovenkant van de Blade en aan de linkerkant.

![Opdrachtbalk](./media/storsimple-8000-service-dashboard/service-summary2.png)

Gebruik deze opties voor het uitvoeren van verschillende bewerkingen, zoals het toevoegen van shares of volumes, of het bewaken van de verschillende taken die worden uitgevoerd op de StorSimple-apparaten.


## <a name="essentials"></a>Essentials

In het gebied essentiële elementen worden enkele belang rijke eigenschappen vastgelegd, zoals de resource groep, locatie en het abonnement waarin uw StorSimple-Apparaatbeheer is gemaakt.

![Essentials](./media/storsimple-8000-service-dashboard/service-summary3.png)

## <a name="storsimple-device-manager-service-summary"></a>Samen vatting van StorSimple Apparaatbeheer-service

* De tegel **waarschuwingen** bevat een moment opname van alle actieve waarschuwingen op alle apparaten, gegroepeerd op ernst van de waarschuwing.

    ![Tegel waarschuwingen](./media/storsimple-8000-service-dashboard/service-summary4.png)

    Als u op de tegel klikt, wordt de Blade **waarschuwingen** geopend. Hiermee kunt u op een afzonderlijke waarschuwing klikken om aanvullende informatie over die waarschuwing weer te geven, inclusief eventuele aanbevolen acties. U kunt ook de waarschuwing wissen als het probleem is opgelost.

    ![Klik op waarschuwingen tegel](./media/storsimple-8000-service-dashboard/service-summary8.png)

* In de tegel **capaciteit** wordt de primaire opslag weer gegeven die is ingericht en overgebleven op alle apparaten ten opzichte van de totale opslag ruimte die beschikbaar is op alle apparaten. **Ingericht** heeft betrekking op de hoeveelheid opslag ruimte die is voor bereid en toegewezen voor gebruik. het **resterende gedeelte** verwijst naar de resterende capaciteit die op alle apparaten kan worden ingericht.

    ![Capaciteits tegel](./media/storsimple-8000-service-dashboard/service-summary6.png)

    De **resterende gelaagde** capaciteit is de beschik bare capaciteit die kan worden ingericht, inclusief de Cloud, terwijl de **resterende lokale** capaciteit het resterende aantal schijven is dat is gekoppeld aan de StorSimple-apparaten van de 8000-serie.


* In het **gebruiks** diagram kunt u de relevante metrische gegevens voor uw apparaten bekijken. U kunt de primaire opslag weer geven die wordt gebruikt op alle apparaten en de Cloud opslag die wordt verbruikt door apparaten in de afgelopen 7 dagen, de standaard periode. 

    ![Gebruiks tegel](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    Als u een andere tijd schaal wilt kiezen, gebruikt u de optie **bewerken** in de rechter bovenhoek van de grafiek.

     ![Klik op gebruiks tegel](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![Grafiek gegevens exporteren](./media/storsimple-8000-service-dashboard/service-summary11.png)

* De tegel **apparaten** bevat een samen vatting van het aantal apparaten uit de StorSimple 8000-serie in uw StorSimple Apparaatbeheer gegroepeerd op Apparaatstatus. 

    ![Tegel apparaten](./media/storsimple-8000-service-dashboard/service-summary5.png)

    Klik op deze tegel om de Blade met de lijst met **apparaten** te openen en klik vervolgens op een afzonderlijk apparaat om in te zoomen op het apparaat dat specifiek is voor het apparaat. U kunt ook apparaatspecifieke acties uitvoeren vanuit een bepaalde Blade samen vatting van apparaten. Ga naar de [Blade overzicht van apparaten](storsimple-8000-device-dashboard.md)voor meer informatie over de Blade samen vatting van apparaten.

    ![Klik op apparaten tegel](./media/storsimple-8000-service-dashboard/service-summary9.png)

## <a name="view-the-activity-logs"></a>De activiteiten logboeken weer geven

Als u de verschillende bewerkingen in uw StorSimple Apparaatbeheer wilt bekijken, klikt u op de koppeling **activiteiten logboeken** aan de linkerkant van de Blade StorSimple service overzicht. Hiermee gaat u naar de Blade **activiteiten logboeken** , waar u een samen vatting kunt zien van de recente bewerkingen die zijn uitgevoerd.

![Activiteitenlogboeken](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het gebruik van de StorSimple Apparaatbeheer-service voor het beheren van uw StorSimple-apparaat](storsimple-8000-manager-service-administration.md).


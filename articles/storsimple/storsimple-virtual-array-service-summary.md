---
title: Blade samen vatting van StorSimple Virtual Array-Service | Microsoft Docs
description: Hierin wordt de Blade service overzicht voor StorSimple Apparaatbeheer beschreven en wordt uitgelegd hoe u dit kunt gebruiken om de status van uw virtuele StorSimple-matrix te controleren.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 8a2b9a84-b0e6-48b9-b366-d16f004241a5
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 31220a8f8b012e08d46195b60a26be9cfc261be5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85514529"
---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>De Blade service overzicht gebruiken voor StorSimple Apparaatbeheer verbonden met een virtuele StorSimple-matrix
## <a name="overview"></a>Overzicht
De Blade samen vatting van de service voor de StorSimple-Apparaatbeheer biedt een samen vatting van de virtuele StorSimple-matrices (ook wel StorSimple on-premises virtuele apparaten of virtuele apparaten genoemd) die zijn verbonden met uw service en die de aandacht van de systeem beheerder moeten markeren. In deze zelf studie wordt de Blade service overzicht geïntroduceerd, worden de inhoud en functie uitgelegd en worden de taken beschreven die u kunt uitvoeren vanuit deze Blade.

![Service dashboard](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>Beheer opdrachten en essentiële elementen
Op de Blade samen vatting van StorSimple ziet u de opties voor het beheren van uw StorSimple Apparaatbeheer-service en de virtuele matrices die zijn geregistreerd voor deze service. U ziet de beheer opdrachten aan de bovenkant van de Blade en aan de linkerkant.

Gebruik deze opties voor het uitvoeren van verschillende bewerkingen, zoals het toevoegen van shares of volumes, of het bewaken van de verschillende taken die op de virtuele matrices worden uitgevoerd.

In het gebied essentiële elementen worden enkele belang rijke eigenschappen vastgelegd, zoals de resource groep, locatie en het abonnement waarin uw StorSimple-Apparaatbeheer is gemaakt.

## <a name="storsimple-device-manager-service-summary"></a>Samen vatting van StorSimple Apparaatbeheer-service
* De tegel **waarschuwingen** bevat een moment opname van alle actieve waarschuwingen op alle virtuele apparaten, gegroepeerd op ernst van de waarschuwing. Als u op de tegel klikt, wordt de Blade **waarschuwingen** geopend. Hiermee kunt u op een afzonderlijke waarschuwing klikken om aanvullende informatie over die waarschuwing weer te geven, inclusief eventuele aanbevolen acties. U kunt ook de waarschuwing wissen als het probleem is opgelost.
* In de tegel **capaciteit** wordt de primaire opslag weer gegeven die is ingericht en overgebleven voor alle virtuele apparaten ten opzichte van de totale opslag ruimte die beschikbaar is voor alle virtuele apparaten. **Ingericht** heeft betrekking op de hoeveelheid opslag ruimte die is voor bereid en toegewezen voor gebruik. het **resterende gedeelte** verwijst naar de resterende capaciteit die op alle virtuele apparaten kan worden ingericht. De **resterende gelaagde** capaciteit is de beschik bare capaciteit die kan worden ingericht, inclusief de Cloud, terwijl de **resterende lokale** capaciteit de resterende hoeveelheid is op de schijven die aan de virtuele matrices zijn gekoppeld.
* In het **gebruiks** diagram ziet u de relevante metrische gegevens voor uw virtuele apparaten. U kunt de primaire opslag weer geven die wordt gebruikt voor alle virtuele apparaten, evenals de Cloud opslag die wordt verbruikt door virtuele apparaten in de afgelopen 7 dagen, de standaard periode. Gebruik de optie **bewerken** in de rechter bovenhoek van de grafiek om een andere tijd schaal te kiezen.
* De tegel **apparaten** bevat een samen vatting van het aantal virtuele matrices in uw StorSimple Apparaatbeheer gegroepeerd op Apparaatstatus. Klik op deze tegel om de Blade met de lijst met **apparaten** te openen en klik vervolgens op een afzonderlijk apparaat om in te zoomen op het apparaat dat specifiek is voor het apparaat. U kunt ook specifieke acties uitvoeren vanuit een bepaalde Blade apparaat samen vatting. Ga naar de [Blade overzicht van apparaten](storsimple-virtual-array-device-summary.md)voor meer informatie over de Blade samen vatting van apparaten.

## <a name="view-the-activity-logs"></a>De activiteiten logboeken weer geven
Als u de verschillende bewerkingen in uw StorSimple Apparaatbeheer wilt bekijken, klikt u op de koppeling **activiteiten logboeken** aan de linkerkant van de Blade StorSimple service overzicht. Hiermee gaat u naar de Blade **activiteiten logboeken** , waar u een samen vatting kunt zien van de recente bewerkingen die zijn uitgevoerd.

![Activiteitenlogboeken](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [het gebruik van de lokale web-UI voor het beheren van uw StorSimple-virtuele matrix](storsimple-ova-web-ui-admin.md).


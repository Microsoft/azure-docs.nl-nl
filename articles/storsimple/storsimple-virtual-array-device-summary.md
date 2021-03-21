---
title: Blade samen vatting van StorSimple Virtual array-apparaat | Microsoft Docs
description: Hierin wordt de Blade samen vatting van apparaten voor StorSimple Apparaatbeheer beschreven en wordt uitgelegd hoe u deze kunt gebruiken om de status van uw virtuele StorSimple-matrix te controleren.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: a13c1ea7-6428-4234-84a6-0ebf51670a85
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: manuaery
ms.openlocfilehash: 1ab72788c768568366f2627055015c74028330b5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005874"
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>De Blade samen vatting van apparaten gebruiken voor StorSimple Apparaatbeheer verbonden met virtuele StorSimple-matrix

## <a name="overview"></a>Overzicht

Op de Blade StorSimple Apparaatbeheer apparaat vindt u een overzichts weergave van een StorSimple-virtuele matrix die is geregistreerd met een bepaalde StorSimple-Apparaatbeheer, waarbij de problemen met het apparaat worden gemarkeerd die een systeem beheerder nodig heeft. In deze zelf studie wordt de Blade samen vatting van apparaten geïntroduceerd, worden de inhoud en functie uitgelegd en worden de taken beschreven die u kunt uitvoeren vanuit deze Blade.

Op de Blade samen vatting van apparaten wordt de volgende informatie weer gegeven:

![Apparaatdashboard](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>Beheer

Op de Blade StorSimple-apparaat ziet u de opties voor het beheren van uw StorSimple-apparaat. U ziet de beheer opdrachten aan de bovenkant van de Blade en aan de linkerkant. Gebruik deze opties om shares of volumes toe te voegen, of om uw virtuele matrix bij te werken of te laten mislukken.

In het gebied essentiële elementen worden enkele belang rijke eigenschappen vastgelegd, zoals de status, het model, de software versie en een koppeling naar de **webgebruikersinterface** van de matrix. Als u een intern netwerk hebt, kunt u de [lokale web-UI](storsimple-ova-web-ui-admin.md) rechtstreeks starten om uw virtuele matrix te beheren.

![Essentiële apparaten](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>Overzicht van StorSimple-apparaten

* De tegel **waarschuwingen** bevat een moment opname van alle actieve waarschuwingen voor uw virtuele matrix, gegroepeerd op ernst van de waarschuwing. Klik op de tegel om de Blade **waarschuwingen** te openen en klik vervolgens op een afzonderlijke waarschuwing om aanvullende informatie over die waarschuwing weer te geven, inclusief eventuele aanbevolen acties. U kunt ook de waarschuwing wissen als het probleem is opgelost.

* In de tegel **capaciteit** wordt de primaire opslag weer gegeven die is ingericht en overgebleven op het virtuele apparaat ten opzichte van de totale beschik bare opslag ruimte. **Ingericht** heeft betrekking op de hoeveelheid opslag ruimte die is voor bereid en toegewezen voor gebruik. het **resterende gedeelte** verwijst naar de resterende capaciteit die op dit apparaat kan worden ingericht. De **resterende gelaagde** capaciteit is de beschik bare capaciteit die kan worden ingericht, inclusief de Cloud, terwijl de **resterende lokale** capaciteit het resterende aantal schijven is dat is gekoppeld aan deze virtuele matrix.

* In het **gebruiks** diagram kunt u de primaire opslag weer geven die wordt gebruikt in uw virtuele matrix, evenals de Cloud opslag die in de afgelopen 7 dagen wordt verbruikt, de standaard periode. Gebruik de optie **bewerken** in de rechter bovenhoek van de grafiek om een andere tijd schaal te kiezen.

* De tegel **shares** of **volumes** bevat een samen vatting van het aantal shares of volumes in uw apparaat, gegroepeerd op status. Klik op de tegel om de Blade **shares**  of **volumes** lijst te openen en klik vervolgens op een afzonderlijke share of een volume om de eigenschappen weer te geven of te wijzigen. Zie [shares beheren](storsimple-virtual-array-manage-shares.md) of [volumes beheren](storsimple-virtual-array-manage-volumes.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Leer hoe u het volgende doet:
- [Shares op een virtuele StorSimple-matrix beheren](storsimple-virtual-array-manage-shares.md)
    
- [Volumes op een virtuele StorSimple-matrix beheren](storsimple-virtual-array-manage-volumes.md)


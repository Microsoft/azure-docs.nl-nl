---
title: Release opmerkingen bij StorSimple 8000 Series Update 5,1
description: Hierin worden de nieuwe functies, problemen en tijdelijke oplossingen voor de StorSimple 8000 Series Update 5,1 beschreven.
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.topic: conceptual
ms.date: 03/18/2021
ms.author: alkohli
ms.openlocfilehash: cdb971851ba678ce18f5a1c7954e5620740f3a4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104657566"
---
# <a name="storsimple-8000-series-update-51-release-notes"></a>Release opmerkingen bij StorSimple 8000 Series Update 5,1

## <a name="overview"></a>Overzicht

In de volgende release opmerkingen worden de nieuwe functies beschreven en worden de essentiële open problemen voor de StorSimple 8000 Series Update 5,1 geïdentificeerd. Ze bevatten ook een lijst met de StorSimple software-updates die zijn opgenomen in deze release.

Update 5,1 kan worden toegepast op elk StorSimple-apparaat waarop Update 5 wordt uitgevoerd. Als u een lagere versie dan 5 gebruikt, moet u eerst Update 5 Toep assen en vervolgens 5,1 Toep assen. De versie van het apparaat die is gekoppeld aan update 5,1 is 6.3.9600.17885.

Lees de informatie in de release opmerkingen voordat u de update in uw StorSimple-oplossing implementeert.

> [!IMPORTANT]
>
> * Update 5,1 is een verplichte update die direct moet worden geïnstalleerd. Zie [Update 5,1 Toep assen](storsimple-8000-install-update-51.md)voor meer informatie.
> * Update 5,1 heeft alleen beveiligings updates. Het duurt ongeveer 30 minuten om deze update te installeren. We raden u ten zeerste aan update 5,1 toe te passen om de werking van uw apparaat te controleren.
> * Voor nieuwe releases worden updates mogelijk niet onmiddellijk weer geven omdat er een gefaseerde implementatie van de updates wordt uitgevoerd. Wacht een paar dagen en zoek opnieuw naar updates zodra deze updates binnenkort beschikbaar zullen worden.

## <a name="whats-new-in-update-51"></a>Wat is er nieuw in Update 5,1

De volgende belang rijke verbeteringen en oplossingen voor fouten zijn aangebracht in Update 5,1:

* **Tls 1,2** : door deze StorSimple-update wordt TLS 1,2 op alle clients afgedwongen. Dit is een verplichte update voor alle apparaten uit de StorSimple 8000-serie.

   Als de volgende waarschuwing wordt weer gegeven, moet u de software op het apparaat bijwerken voordat u doorgaat:

   Op een of meer StorSimple-apparaten wordt een oudere software versie uitgevoerd. De meest recente update voor TLS 1,2 is een verplichte update die direct op deze apparaten moet worden geïnstalleerd. TLS 1,2 wordt gebruikt voor alle Azure Portal communicatie en zonder deze update, kan het apparaat niet communiceren met de StorSimple-service.

## <a name="known-issues-in-update-51-from-previous-releases"></a>Bekende problemen in Update 5,1 van eerdere versies

Er zijn geen nieuwe bekende problemen in Update 5,1. Ga naar [Update 3 release opmerkingen](storsimple-update3-release-notes.md#known-issues-in-update-3)voor een lijst met problemen die zijn overgedragen aan update 5,1 van eerdere versies.

## <a name="storsimple-cloud-appliance-updates-in-update-51"></a>Updates StorSimple Cloud Appliance in Update 5,1

Deze update kan niet worden toegepast op de StorSimple Cloud Appliance (ook wel bekend als het virtuele apparaat). Nieuwe Cloud apparaten moeten worden gemaakt met behulp van de update 5,1-installatie kopie. Ga voor meer informatie over het maken van een StorSimple Cloud Appliance naar [een StorSimple Cloud Appliance implementeren en beheren](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Volgende stap

Meer informatie over het [installeren van Update 5,1](storsimple-8000-install-update-51.md) op uw StorSimple-apparaat.

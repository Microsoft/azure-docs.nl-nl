---
title: Opmerkingen bij de release van StorSimple 8000 Series Update 5
description: Hierin worden de nieuwe functies, problemen en tijdelijke oplossingen beschreven voor StorSimple 8000 Series Update 5.
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: 4fed65e12a166c046a0c63d7c4849f86805bbe9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021041"
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>Opmerkingen bij de release van StorSimple 8000 Series Update 5

## <a name="overview"></a>Overzicht

In de volgende release opmerkingen worden de nieuwe functies beschreven en worden de essentiële openstaande problemen voor de StorSimple 8000 Series Update 5 geïdentificeerd. Ze bevatten ook een lijst met de StorSimple software-updates die zijn opgenomen in deze release.

Update 5 kan worden toegepast op elk StorSimple-apparaat met Update 0,1 tot en met update 4. De versie van het apparaat die is gekoppeld aan update 5 is 6.3.9600.17845.

Lees de informatie in de release opmerkingen voordat u de update in uw StorSimple-oplossing implementeert.

> [!IMPORTANT]
> * Update 5 is een verplichte update die direct moet worden geïnstalleerd. Zie [Update 5 Toep assen](storsimple-8000-install-update-5.md)voor meer informatie.
> * Update 5 heeft apparaatsoftware, schijf firmware, beveiliging van het besturings systeem en andere updates van het besturings systeem. Het duurt ongeveer 4 uur om deze update te installeren. Update van de schijf firmware is een storende update en resulteert in een downtime voor uw apparaat. U kunt het beste Update 5 Toep assen om uw apparaat up-to-date te houden.
> * Voor nieuwe releases worden updates mogelijk niet onmiddellijk weer geven omdat er een gefaseerde implementatie van de updates wordt uitgevoerd. Wacht een paar dagen en zoek opnieuw naar updates zodra deze updates binnenkort beschikbaar zullen worden.

## <a name="whats-new-in-update-5"></a>Wat is er nieuw in Update 5

De volgende belang rijke verbeteringen en oplossingen voor fouten zijn aangebracht in Update 5.

* **Gebruik van Azure Active Directory (Aad) voor verificatie met de StorSimple Apparaatbeheer-service** – vanaf Update 5 tot en met de StorSimple-Apparaatbeheer service wordt Azure Active Directory gebruikt voor verificatie. Het oude verificatie mechanisme wordt vervangen door december 2017. Alle gebruikers moeten de nieuwe verificatie-Url's in hun firewall regels opnemen. Ga voor meer informatie naar [verificatie-url's die worden vermeld in de netwerk vereisten voor uw StorSimple-apparaat](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal).

    Als de verificatie-URL niet is opgenomen in de firewall regels, krijgen de gebruikers een kritieke waarschuwing te zien dat hun StorSimple-apparaat niet kan worden geverifieerd bij de service. Als de gebruikers deze waarschuwing zien, moeten ze de nieuwe verificatie-URL toevoegen. Ga naar [StorSimple-netwerk waarschuwingen](storsimple-8000-manage-alerts.md#networking-alerts)voor meer informatie.

* **Nieuwe versie van StorSimple Snapshot Manager** : er wordt een nieuwe versie van StorSimple Snapshot Manager uitgebracht met Update 5 en deze is compatibel met alle StorSimple-apparaten waarop update 4 of hoger wordt uitgevoerd. U wordt aangeraden om deze versie bij te werken. De vorige versie van StorSimple Snapshot Manager wordt gebruikt voor StorSimple-apparaten waarop update 3 of eerder wordt uitgevoerd. [Down load de juiste versie van StorSimple Snapshot Manager](https://www.microsoft.com/en-us/download/details.aspx?id=44220) en Raadpleeg [Deploy StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).


## <a name="issues-fixed-in-update-5"></a>Problemen die zijn opgelost in Update 5

De volgende tabel bevat een overzicht van problemen die zijn opgelost in Update 5.

| Nee | Functie | Probleem | Is van toepassing op het fysieke apparaat | Is van toepassing op virtueel apparaat |
| --- | --- | --- | --- | --- |
| 1 |Externe toegang voor Windows Power shell |In de vorige versie ontvangt een gebruiker een fout melding tijdens het tot stand brengen van een externe verbinding met de StorSimple Cloud Appliance via Windows Power shell. Dit probleem is in deze release veroorzaakt en opgelost. |Nee |Ja |
| 2 |Bandbreedte sjablonen |In eerdere versies was er een probleem met bandbreedte sjablonen dat heeft geleid tot een lagere band breedte dan waarvoor het apparaat is geconfigureerd. Dit probleem wordt opgelost in deze release. |Ja |Ja |
| 3 |Failover |Wanneer een apparaat met een groot aantal volumes is overgeschakeld naar een ander apparaat waarop update 4 wordt uitgevoerd, mislukt het proces bij het Toep assen van de Access Control-records. Dit probleem is opgelost in deze release. |Ja |Ja |



## <a name="known-issues-in-update-5-from-previous-releases"></a>Bekende problemen in Update 5 van eerdere versies

Er zijn geen nieuwe bekende problemen in Update 5. Ga naar [Update 3 release opmerkingen](storsimple-update3-release-notes.md#known-issues-in-update-3)voor een lijst met problemen die zijn overgedragen aan update 5 van eerdere versies.

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>SAS-controller (Serial-Attached SCSI) en firmware-updates in Update 5

Deze release heeft SAS controller-en LSI-Stuur Programma's en firmware-updates. Zie [Update 5](storsimple-8000-install-update-5.md) op uw StorSimple-apparaat installeren voor meer informatie over het installeren van deze updates.

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>Updates StorSimple Cloud Appliance in Update 5

Deze update kan niet worden toegepast op de StorSimple Cloud Appliance (ook wel bekend als het virtuele apparaat). Nieuwe Cloud apparaten moeten worden gemaakt met behulp van de update 5-installatie kopie. Ga voor meer informatie over het maken van een StorSimple Cloud Appliance naar [een StorSimple Cloud Appliance implementeren en beheren](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Volgende stap

Meer informatie over het [installeren van Update 5](storsimple-8000-install-update-5.md) op uw StorSimple-apparaat.


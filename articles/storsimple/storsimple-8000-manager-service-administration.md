---
title: StorSimple Apparaatbeheer Service-beheer | Microsoft Docs
description: Meer informatie over het beheren van uw StorSimple-apparaat met behulp van de StorSimple Apparaatbeheer-service in de Azure Portal.
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
ms.date: 07/12/2017
ms.author: alkohli
ms.openlocfilehash: 69a36f38cc76693f596b45d4df6e232bc01584d5
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967972"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>De StorSimple Apparaatbeheer-service gebruiken voor het beheren van uw StorSimple-apparaat

## <a name="overview"></a>Overzicht

In dit artikel wordt de StorSimple-Apparaatbeheer Service-Interface beschreven, met inbegrip van de manier waarop u verbinding kunt maken, de verschillende beschik bare opties en koppelingen naar de specifieke werk stromen die via deze gebruikers interface kunnen worden uitgevoerd. Deze richt lijnen zijn van toepassing op beide; de fysieke StorSimple en het Cloud apparaat.

Na het lezen van dit artikel leert u het volgende:

* Verbinding maken met StorSimple Apparaatbeheer-service
* Uw StorSimple-apparaat beheren via de StorSimple Apparaatbeheer-service

## <a name="connect-to-storsimple-device-manager-service"></a>Verbinding maken met StorSimple Apparaatbeheer-service

De StorSimple-Apparaatbeheer-service wordt uitgevoerd in Microsoft Azure en er wordt verbinding gemaakt met meerdere StorSimple-apparaten. U gebruikt een centrale Microsoft Azure-portal die in een browser wordt uitgevoerd om deze apparaten te beheren. Ga als volgt te werk om verbinding te maken met de StorSimple-Apparaatbeheer service.

#### <a name="to-connect-to-the-service"></a>Verbinding maken met de service
1. Navigeer naar [https://portal.azure.com/](https://portal.azure.com/) .
2. Meld u met uw Microsoft-account-referenties aan bij de Microsoft Azure-portal (in de rechter bovenhoek van het deel venster).
3. Schuif omlaag in het navigatie deel venster aan de linkerkant om toegang te krijgen tot de StorSimple-Apparaatbeheer service.


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>StorSimple-apparaat beheren met StorSimple Apparaatbeheer service

De volgende tabel bevat een samen vatting van alle algemene beheer taken en complexe werk stromen die kunnen worden uitgevoerd in de StorSimple-gebruikers interface van de Apparaatbeheer-service. Deze taken zijn ingedeeld op basis van de UI-Blades waarop ze worden gestart.

Klik op de juiste procedure in de tabel voor meer informatie over elke werk stroom.

#### <a name="storsimple-device-manager-workflows"></a>StorSimple Apparaatbeheer werk stromen

| Als u dit wilt doen... | Gebruik deze procedure. |
| --- | --- |
| Een service maken</br>Een service verwijderen</br>Service registratie sleutel ophalen</br>Service registratie sleutel opnieuw genereren |[Een StorSimple Apparaatbeheer-service implementeren](storsimple-8000-manage-service.md) |
| De activiteiten logboeken weer geven |[De StorSimple Apparaatbeheer service-samen vatting gebruiken](storsimple-8000-service-dashboard.md) |
| De versleutelings sleutel voor service gegevens wijzigen</br>De bewerkings logboeken weer geven |[Het dash board van de StorSimple Apparaatbeheer-service gebruiken](storsimple-8000-service-dashboard.md) |
| Een apparaat deactiveren</br>Een apparaat verwijderen |[Een apparaat deactiveren of verwijderen](storsimple-8000-deactivate-and-delete-device.md) |
| Meer informatie over herstel na nood gevallen en failover van het apparaat</br>Failover naar een fysiek apparaat</br>Failover naar een virtueel apparaat</br>Bedrijfs continuïteit nood herstel (BCDR) |[Failover en nood herstel voor uw StorSimple-apparaat](storsimple-8000-device-failover-disaster-recovery.md) |
| Back-ups van een volume weer geven</br>Een back-upset selecteren</br>Een back-upset verwijderen |[Back-ups beheren](storsimple-8000-manage-backup-catalog.md) |
| Een volume klonen |[Een volume klonen](storsimple-8000-clone-volume-u2.md) |
| Een back-upset herstellen |[Een back-upset herstellen](storsimple-8000-restore-from-backup-set-u2.md) |
| Over opslag accounts</br>Een opslagaccount toevoegen</br>Een opslag account bewerken</br>Een opslagaccount verwijderen</br>Sleutel rotatie van opslag accounts |[Opslagaccounts beheren](storsimple-8000-manage-storage-accounts.md) |
| Over bandbreedte sjablonen</br>Een bandbreedte sjabloon toevoegen</br>Een bandbreedte sjabloon bewerken</br>Een bandbreedte sjabloon verwijderen</br>Een standaard sjabloon voor band breedte gebruiken</br>Een sjabloon voor een hele dag-band breedte maken die op een opgegeven tijdstip begint |[Bandbreedtesjablonen beheren](storsimple-8000-manage-bandwidth-templates.md) |
| Access Control records</br>Een Access Control Record maken</br>Een Access Control Record bewerken</br>Een Access Control Record verwijderen |[Access Control records beheren](storsimple-8000-manage-acrs.md) |
| Taakdetails weergeven</br>Een taak annuleren |[Taken beheren](storsimple-8000-manage-jobs-u2.md) |
| Waarschuwingen ontvangen</br>Waarschuwingen beheren</br>Waarschuwingen weergeven |[StorSimple-waarschuwingen weer geven en beheren](storsimple-8000-manage-alerts.md) |
| Bewakings grafieken maken |[Uw StorSimple-apparaat bewaken](./storsimple-8000-monitor-device.md) |
| Een volume container toevoegen</br>Een volume container wijzigen</br>Een volume container verwijderen |[Volumecontainers beheren](storsimple-8000-manage-volume-containers.md) |
| Een volume toevoegen</br>Een volume wijzigen</br>Een volume offline halen</br>Een volume verwijderen</br>Een volume bewaken |[Volumes beheren](storsimple-8000-manage-volumes-u2.md) |
| Apparaatinstellingen wijzigen</br>Tijd instellingen wijzigen</br>DNS.md-instellingen wijzigen</br>Netwerk interfaces configureren |[De apparaatconfiguratie voor uw StorSimple-apparaat wijzigen](storsimple-8000-modify-device-config.md) |
| Webproxy-instellingen weer geven |[Webproxy voor uw apparaat configureren](storsimple-8000-configure-web-proxy.md) |
| Beheerders wachtwoord voor het apparaat wijzigen</br>StorSimple Snapshot Manager wacht woord wijzigen |[StorSimple-wacht woorden wijzigen](storsimple-8000-change-passwords.md) |
| Extern beheer configureren |[Extern verbinding maken met uw StorSimple-apparaat](storsimple-8000-remote-connect.md) |
| Waarschuwingsinstellingen configureren |[StorSimple-waarschuwingen weer geven en beheren](storsimple-8000-manage-alerts.md) |
| CHAP configureren voor uw StorSimple-apparaat |[CHAP configureren voor uw StorSimple-apparaat](./storsimple-8000-configure-chap.md) |
| Een back-upbeleid toevoegen</br>Een planning toevoegen of wijzigen</br>Een back-upbeleid verwijderen</br>Een hand matige back-up maken</br>Een aangepast back-upbeleid maken met meerdere volumes en schema's |[Back-upbeleid beheren](storsimple-8000-manage-backup-policies-u2.md) |
| Apparaatbeheer stoppen</br>Apparaatbeheer opnieuw starten</br>Apparaat-controllers uitschakelen</br>De fabrieks instellingen van uw apparaat herstellen</br>(Hierboven gelden alleen voor on-premises apparaten) |[StorSimple Device controller beheren](storsimple-8000-manage-device-controller.md) |
| Meer informatie over StorSimple-hardwareonderdelen</br>Hardwarestatus bewaken</br>(Hierboven gelden alleen voor on-premises apparaten) |[Hardware-onderdelen bewaken](storsimple-8000-monitor-hardware-status.md) |
| Een ondersteunings pakket maken |[Een ondersteunings pakket maken en beheren](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| Software-updates installeren |[Uw apparaat bijwerken](storsimple-update-device.md) |

## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt met de dagelijkse werking van uw StorSimple-apparaat of met een van de hardwareonderdelen, raadpleegt u:

* [Problemen oplossen met behulp van het diagnostische hulp programma](storsimple-8000-diagnostics.md)
* [StorSimple-bewakings indicator-Led's gebruiken](storsimple-monitoring-indicators.md)

Als u de problemen niet kunt oplossen en een service aanvraag wilt maken, raadpleegt u [contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md).
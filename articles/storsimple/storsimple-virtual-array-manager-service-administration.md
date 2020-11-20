---
title: Beheer van virtuele array van Microsoft Azure StorSimple beheer | Microsoft Docs
description: Meer informatie over het beheren van uw StorSimple on-premises virtuele matrix met behulp van de StorSimple Apparaatbeheer-service in de Azure Portal.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 958244a5-f9f5-455e-b7ef-71a65558872e
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: 2395e3c6f2026bc1024de38122bc20959fdcf9b7
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967088"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>De StorSimple Apparaatbeheer-service gebruiken voor het beheren van de virtuele StorSimple-matrix
![proces stroom instellen](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>Overzicht
In dit artikel wordt de StorSimple-Apparaatbeheer Service-Interface beschreven, met inbegrip van hoe u verbinding maakt met deze en de verschillende beschik bare opties, en vindt u koppelingen naar de specifieke werk stromen die kunnen worden uitgevoerd via deze gebruikers interface.

Nadat u dit artikel hebt gelezen, weet u hoe u het volgende kunt doen:

* Verbinding maken met de StorSimple-Apparaatbeheer service
* Navigeren in de StorSimple Apparaatbeheer gebruikers interface
* Uw StorSimple Virtual array beheren via de StorSimple Apparaatbeheer-service

> [!NOTE]
> Als u de beschik bare beheer opties voor het StorSimple 8000-serie apparaat wilt weer geven, gaat u naar [de StorSimple Manager-service gebruiken om uw StorSimple-apparaat te beheren](./storsimple-8000-manager-service-administration.md).
> 
> 

## <a name="connect-to-the-storsimple-device-manager-service"></a>Verbinding maken met de StorSimple-Apparaatbeheer service
De StorSimple-Apparaatbeheer-service wordt uitgevoerd in Microsoft Azure en er wordt verbinding gemaakt met meerdere virtuele StorSimple-matrices. U gebruikt een centrale Microsoft Azure-portal die in een browser wordt uitgevoerd om deze apparaten te beheren. Ga als volgt te werk om verbinding te maken met de StorSimple-Apparaatbeheer service.

#### <a name="to-connect-to-the-service"></a>Verbinding maken met de service
1. Ga naar [https://ms.portal.azure.com](https://ms.portal.azure.com).
2. Meld u met uw Microsoft-account-referenties aan bij de Microsoft Azure-portal (in de rechter bovenhoek van het deel venster).
3. Navigeer naar Browse-> ' filter ' op StorSimple Device Managers om al uw Apparaatbeheer in een bepaald abonnement weer te geven.

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>De StorSimple Apparaatbeheer-service gebruiken om beheer taken uit te voeren
De volgende tabel bevat een samen vatting van alle algemene beheer taken en complexe werk stromen die kunnen worden uitgevoerd op de Blade StorSimple Apparaatbeheer service overzicht. Deze taken zijn ingedeeld op basis van de Blades waarop ze worden gestart.

Klik op de juiste procedure in de tabel voor meer informatie over elke werk stroom.

#### <a name="storsimple-device-manager-workflows"></a>StorSimple Apparaatbeheer werk stromen
| Als u dit wilt doen... | Gebruik deze procedure |
| --- | --- |
| Een service maken</br>Een service verwijderen</br>De serviceregistratiesleutel ophalen</br>De service registratie sleutel opnieuw genereren |[De StorSimple Apparaatbeheer-service implementeren](storsimple-virtual-array-manage-service.md) |
| De activiteiten logboeken weer geven |[Het StorSimple-service overzicht gebruiken](storsimple-virtual-array-service-summary.md) |
| Een virtuele matrix deactiveren</br>Een virtuele matrix verwijderen |[Een virtuele matrix deactiveren of verwijderen](storsimple-virtual-array-deactivate-and-delete-device.md) |
| Herstel na nood gevallen en failover van het apparaat</br>Failover-vereisten</br>Bedrijfs continuïteit nood herstel (BCDR)</br>Fouten tijdens nood herstel |[Herstel na nood gevallen en failover van het apparaat voor uw virtuele StorSimple-matrix](storsimple-virtual-array-failover-dr.md) |
| Back-ups maken van shares en volumes</br>Een hand matige back-up maken</br>Het back-upschema wijzigen</br>Bestaande back-ups weer geven |[Back-up maken van de virtuele StorSimple-matrix](storsimple-virtual-array-backup.md) |
| Shares van een back-upset klonen</br>Volumes van een back-upset klonen</br>Herstel op item niveau (alleen bestands server) |[Klonen vanuit een back-up van uw virtuele StorSimple-matrix](storsimple-virtual-array-clone.md) |
| Over opslag accounts</br>Een opslagaccount toevoegen</br>Een opslag account bewerken</br>Een opslagaccount verwijderen |[Opslag accounts beheren voor de virtuele StorSimple-matrix](storsimple-virtual-array-manage-storage-accounts.md) |
| Access Control records</br>Een Access Control Record toevoegen of wijzigen </br>Een Access Control Record verwijderen |[Access Control records beheren voor de virtuele StorSimple-matrix](storsimple-virtual-array-manage-acrs.md) |
| Taakdetails weergeven |[StorSimple virtuele-matrix taken beheren](storsimple-virtual-array-manage-jobs.md) |
| Waarschuwingsinstellingen configureren</br>Waarschuwingen ontvangen</br>Waarschuwingen beheren</br>Waarschuwingen weergeven |[Waarschuwingen voor de virtuele StorSimple-matrix weer geven en beheren](storsimple-virtual-array-manage-alerts.md) |
| Het beheerders wachtwoord van het apparaat wijzigen |[Het beheerders wachtwoord voor de StorSimple Virtual array wijzigen](storsimple-virtual-array-change-device-admin-password.md) |
| Software-updates installeren |[Uw virtuele matrix bijwerken](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> U moet de [lokale web-UI](storsimple-ova-web-ui-admin.md) gebruiken voor de volgende taken:
> 
> * [De versleutelings sleutel voor de service gegevens ophalen](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [Een ondersteunings pakket maken](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [Een virtuele matrix stoppen en opnieuw starten](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>Volgende stappen
Voor informatie over de Web-UI en hoe u deze kunt gebruiken, gaat u naar [de StorSimple-webgebruikersinterface gebruiken om de virtuele StorSimple-matrix te beheren](storsimple-ova-web-ui-admin.md).
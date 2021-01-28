---
title: Nieuwe authenticatie voor StorSimple virtuele matrices
description: Meer informatie over AAD-verificatie, de gekoppelde nieuwe service registratie sleutel en wijzigingen aan de firewall regels die van toepassing zijn op de StorSimple-apparaten.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 75332498ac59dc46a7a079eff4c25e02b2a6cb9b
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986934"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>De nieuwe verificatie voor uw StorSimple gebruiken

## <a name="overview"></a>Overzicht

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

De StorSimple-Apparaatbeheer-service wordt uitgevoerd in Microsoft Azure en er wordt verbinding gemaakt met meerdere virtuele StorSimple-matrices. Tot nu toe heeft StorSimple-Apparaatbeheer service een Access Control service (ACS) gebruikt om de service te verifiëren op uw StorSimple-apparaat. Het ACS-mechanisme wordt binnenkort afgeschaft en vervangen door een Azure Active Directory (AAD)-verificatie.

De informatie in dit artikel is alleen van toepassing op virtuele matrices uit de StorSimple-serie van 1200. In dit artikel worden de details van de AAD-verificatie en de bijbehorende nieuwe service registratie sleutel en wijzigingen aan de firewall regels beschreven die van toepassing zijn op de StorSimple-apparaten.

De AAD-verificatie vindt plaats in StorSimple Virtual arrays (model 1200) waarop update 1 of hoger wordt uitgevoerd.

Als gevolg van de introductie van de AAD-verificatie, treden er wijzigingen op in:

- URL-patronen voor firewall regels.
- Service registratie sleutel.

Deze wijzigingen worden gedetailleerd beschreven in de volgende secties.

## <a name="url-changes-for-aad-authentication"></a>URL-wijzigingen voor AAD-verificatie

Om ervoor te zorgen dat de service gebruikmaakt van authenticatie op basis van AAD, moeten alle gebruikers de nieuwe verificatie-Url's opnemen in hun firewall regels.

Als u virtuele StorSimple-matrix gebruikt, moet u ervoor zorgen dat de volgende URL is opgenomen in de firewall regels:

| URL-patroon                         | Cloud | Onderdeel/functionaliteit         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Openbare Azure-peering |AAD-verificatie service      |
| `https://login.microsoftonline.us` | Amerikaanse overheid |AAD-verificatie service      |

Voor een volledige lijst met URL-patronen voor virtuele StorSimple-matrices gaat u naar [URL-patronen voor firewall regels](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).

Als de verificatie-URL niet is opgenomen in de firewall regels na de datum van afschaffing, zien de gebruikers een kritieke waarschuwing dat hun StorSimple-apparaat niet kan worden geverifieerd bij de service. De service kan niet communiceren met het apparaat. Als de gebruikers deze waarschuwing zien, moeten ze de nieuwe verificatie-URL toevoegen. Ga voor meer informatie over de waarschuwing naar [waarschuwingen gebruiken om uw StorSimple-apparaat te bewaken](storsimple-virtual-array-manage-alerts.md#networking-alerts).

## <a name="device-version-and-authentication-changes"></a>De versie van het apparaat en de verificatie worden gewijzigd

Als u een virtuele StorSimple-matrix gebruikt, gebruikt u de volgende tabel om te bepalen welke actie u moet uitvoeren op basis van de software versie van het apparaat dat u gebruikt.

| Als uw apparaat wordt uitgevoerd  | De volgende actie uitvoeren                                    |
|----------------------------|--------------------------------------------------------------|
| Update 1,0 of hoger en is offline. <br> U ziet een waarschuwing dat de URL niet allowlisted is.| 1. Wijzig de firewall regels zodat de verificatie-URL moet worden toegevoegd. Zie [verificatie-url's](#url-changes-for-aad-authentication). <br> 2. [Haal de Aad-registratie sleutel op uit de service](#aad-based-registration-keys). <br> 3. Voer de stappen 1-5 uit om [verbinding te maken met de Windows Power shell-interface van de virtuele matrix](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br> 4. Gebruik `Invoke-HcsReRegister` cmdlet om het apparaat te registreren via de Windows Power shell. Geef de sleutel op die u in de vorige stap hebt gekregen.|
| Update 1,0 of hoger en het apparaat is online.| Geen actie vereist.                                       |
| Update 0,6 of een eerdere versie en het apparaat is offline. | 1. [down load Update 1,0 via de catalogus server](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix).<br>2. [Pas Update 1,0 toe via de lokale web-UI](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix).<br>3. [Haal de Aad-registratie sleutel op uit de service](#aad-based-registration-keys). <br>4. Voer de stappen 1-5 uit om [verbinding te maken met de Windows Power shell-interface van de virtuele matrix](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br>5. Gebruik `Invoke-HcsReRegister` cmdlet om het apparaat te registreren via de Windows Power shell. Geef de sleutel op die u in de vorige stap hebt gekregen.|
| Update 0,6 of een eerdere versie en het apparaat is online | Wijzig de firewall regels zodanig dat ze de verificatie-URL bevatten.<br> Installeer update 1,0 via de Azure Portal. |

## <a name="aad-based-registration-keys"></a>Registratie sleutels op basis van AAD

Bij het starten van update 1,0 voor StorSimple virtuele matrices worden nieuwe op AAD gebaseerde registratie sleutels gebruikt. U gebruikt de registratie sleutels om uw StorSimple-Apparaatbeheer service met het apparaat te registreren.

U kunt de nieuwe registratie sleutels voor AAD-Services niet gebruiken als u een StorSimple-virtuele array gebruikt waarop update 0,6 of eerder wordt uitgevoerd. U moet de service registratie sleutel opnieuw genereren. Zodra u de sleutel opnieuw hebt gegenereerd, wordt de nieuwe sleutel gebruikt voor het registreren van alle volgende apparaten. De oude sleutel is niet meer geldig.

- De nieuwe AAD-registratie sleutel verloopt na drie dagen.
- De AAD-registratie sleutels werken alleen met virtuele StorSimple-matrices van de 1200-serie waarop update 1 of hoger wordt uitgevoerd. De AAD-registratie sleutel van een StorSimple 8000 Series-apparaat werkt niet.
- De AAD-registratie sleutels zijn langer dan de bijbehorende ACS-registratie sleutels.

Voer de volgende stappen uit om een AAD-service registratie sleutel te genereren.

#### <a name="to-generate-the-aad-service-registration-key"></a>De registratie sleutel voor AAD-Services genereren

1. Ga in **StorSimple Apparaatbeheer** naar **beheer &gt;** **sleutels**.
    
    ![Ga naar sleutels](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. Klik op **sleutel genereren**.

    ![Klik op opnieuw genereren](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. Kopieer de nieuwe sleutel. De oude sleutel werkt niet meer.

    ![Opnieuw genereren bevestigen](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het implementeren van [StorSimple Virtual array](storsimple-virtual-array-deploy1-portal-prep.md)

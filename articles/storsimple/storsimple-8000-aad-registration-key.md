---
title: Azure AD-verificatie voor StorSimple 8000 in Apparaatbeheer
description: Hierin wordt uitgelegd hoe u verificatie op basis van AAD gebruikt voor uw service, hoe u een nieuwe registratie sleutel genereert en hand matige registratie van de apparaten uitvoert.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: b09d68e7859a787c05a2fc62294f081c4345ae08
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805037"
---
# <a name="use-azure-active-directory-ad-authentication-for-your-storsimple"></a>Azure Active Directory-verificatie (AD) gebruiken voor uw StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Overzicht

De StorSimple-Apparaatbeheer-service wordt uitgevoerd in Microsoft Azure en er wordt verbinding gemaakt met meerdere StorSimple-apparaten. Tot nu toe heeft StorSimple-Apparaatbeheer service een Access Control service (ACS) gebruikt om de service te verifiëren op uw StorSimple-apparaat. Het ACS-mechanisme wordt binnenkort afgeschaft en vervangen door een Azure Active Directory (AAD)-verificatie. Ga voor meer informatie naar de volgende aankondigingen voor de ACS-afschaffing en het gebruik van AAD-verificatie.

- [De toekomst van Azure ACS is Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [Aanstaande wijzigingen in de micro soft-Access Control Service](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

In dit artikel worden de details van de AAD-verificatie en de bijbehorende nieuwe service registratie sleutel en wijzigingen aan de firewall regels beschreven die van toepassing zijn op de StorSimple-apparaten. De informatie in dit artikel is alleen van toepassing op apparaten uit de StorSimple-serie van 8000.

De AAD-verificatie vindt plaats in het StorSimple 8000 Series-apparaat met Update 5 of hoger. Als gevolg van de introductie van de AAD-verificatie, treden er wijzigingen op in:

- URL-patronen voor firewall regels.
- Service registratie sleutel.

Deze wijzigingen worden gedetailleerd beschreven in de volgende secties.

## <a name="url-changes-for-aad-authentication"></a>URL-wijzigingen voor AAD-verificatie

Om ervoor te zorgen dat de service gebruikmaakt van authenticatie op basis van AAD, moeten alle gebruikers de nieuwe verificatie-Url's opnemen in hun firewall regels.

Als u de StorSimple 8000-serie gebruikt, moet u ervoor zorgen dat de volgende URL is opgenomen in de firewall regels:

| URL-patroon                         | Cloud | Onderdeel/functionaliteit         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Openbare Azure-peering |AAD-verificatie service      |
| `https://login.microsoftonline.us` | Amerikaanse overheid |AAD-verificatie service      |

Voor een volledige lijst met URL-patronen voor StorSimple 8000 Series-apparaten gaat u naar [URL-patronen voor firewall regels](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules).

Als de verificatie-URL niet is opgenomen in de firewall regels na de datum van afschaffing en op het apparaat Update 5 wordt uitgevoerd, zien de gebruikers een URL-waarschuwing. De gebruikers moeten de nieuwe verificatie-URL toevoegen. Als op het apparaat een versie wordt uitgevoerd voordat 5 wordt bijgewerkt, zien de gebruikers een heartbeat-waarschuwing. In elk geval kan het StorSimple-apparaat niet worden geverifieerd bij de service en kan de service niet communiceren met het apparaat.

## <a name="device-version-and-authentication-changes"></a>De versie van het apparaat en de verificatie worden gewijzigd

Als u een StorSimple 8000-serie apparaat gebruikt, gebruikt u de volgende tabel om te bepalen welke actie u moet uitvoeren op basis van de software versie van het apparaat die u gebruikt.

| Als uw apparaat wordt uitgevoerd| De volgende actie uitvoeren                                    |
|--------------------------|------------------------|
| Update 5 of hoger en het apparaat is offline. <br> U ziet een waarschuwing dat de URL niet is goedgekeurd.|1. Wijzig de firewall regels zodat de verificatie-URL moet worden toegevoegd. Zie [verificatie-url's](#url-changes-for-aad-authentication).<br>2. [Haal de Aad-registratie sleutel op uit de service](#aad-based-registration-keys).<br>3. [Maak verbinding met de Windows Power shell-interface van het apparaat met de StorSimple 8000-serie](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).<br>4. Gebruik `Redo-DeviceRegistration` cmdlet om het apparaat te registreren via de Windows Power shell. Geef de sleutel op die u in de vorige stap hebt gekregen.|
| Update 5 of hoger en het apparaat online.| Geen actie vereist.                                       |
| Update 4 of lager en het apparaat is offline. |1. Wijzig de firewall regels zodat de verificatie-URL moet worden toegevoegd.<br>2. [down load Update 5 via de catalogus server](storsimple-8000-install-update-5.md#download-updates-for-your-device).<br>3. [Pas Update 5 toe via de hotfix-methode](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix).<br>4. [Haal de Aad-registratie sleutel op uit de service](#aad-based-registration-keys).<br>5. [Maak verbinding met de Windows Power shell-interface van het apparaat met de StorSimple 8000-serie](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>6. Gebruik `Redo-DeviceRegistration` cmdlet om het apparaat te registreren via de Windows Power shell. Geef de sleutel op die u in de vorige stap hebt gekregen.|
| Update 4 of lager en het apparaat is online. |Wijzig de firewall regels zodanig dat ze de verificatie-URL bevatten.<br> Installeer update 5 via de Azure Portal.              |
| Fabrieks instellingen terugzetten naar een versie vóór update 5.      |In de portal wordt een registratie sleutel op basis van AAD weer gegeven terwijl oudere software op het apparaat wordt uitgevoerd. Volg de stappen in het voor gaande scenario voor wanneer op het apparaat update 4 of eerder wordt uitgevoerd.              |

## <a name="aad-based-registration-keys"></a>Registratie sleutels op basis van AAD

Start update 5 voor de StorSimple-apparaten uit de 8000-serie, worden nieuwe registratie sleutels op basis van AAD gebruikt. U gebruikt de registratie sleutels om uw StorSimple-Apparaatbeheer service met het apparaat te registreren.

U kunt de nieuwe registratie sleutels voor AAD-Services niet gebruiken als u een StorSimple 8000 Series-apparaat gebruikt waarop update 4 of eerder wordt uitgevoerd (inclusief een ouder apparaat dat nu wordt geactiveerd).
In dit scenario moet u de service registratie sleutel opnieuw genereren. Zodra u de sleutel opnieuw hebt gegenereerd, wordt de nieuwe sleutel gebruikt voor het registreren van alle volgende apparaten. De oude sleutel is niet meer geldig.

- De nieuwe AAD-registratie sleutel verloopt na drie dagen.
- De AAD-registratie sleutels werken alleen met StorSimple 8000-serie apparaten met Update 5 of hoger.
- De AAD-registratie sleutels zijn langer dan de bijbehorende ACS-registratie sleutels.

Voer de volgende stappen uit om een AAD-service registratie sleutel te genereren.

#### <a name="to-generate-the-aad-service-registration-key"></a>De registratie sleutel voor AAD-Services genereren

1. Ga in **StorSimple Apparaatbeheer** naar **beheer &gt;** **sleutels**. U kunt ook de zoek balk gebruiken om te zoeken naar _sleutels_.
    
2. Klik op **sleutel genereren**.

    ![Klik op opnieuw genereren](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. Kopieer de nieuwe sleutel. De oudere sleutel werkt niet meer.

    ![Opnieuw genereren bevestigen](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > Als u een StorSimple Cloud Appliance maakt van de service die is geregistreerd bij uw StorSimple 8000 Series-apparaat, moet u geen registratie sleutel genereren terwijl het maken wordt uitgevoerd. Wacht totdat het maken is voltooid en genereer vervolgens de registratie sleutel.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het implementeren van een [StorSimple 8000 Series-apparaat](storsimple-8000-deployment-walkthrough-u2.md).

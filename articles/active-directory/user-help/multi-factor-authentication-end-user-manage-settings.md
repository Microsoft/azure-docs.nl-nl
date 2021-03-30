---
title: Wijzig de twee ledige verificatie methode en instellingen-Azure Active Directory
description: Meer informatie over het wijzigen van de beveiligings verificatie methode en instellingen voor uw werk-of school account, van de pagina aanvullende beveiligings verificatie.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 07/06/2020
ms.author: curtand
ms.openlocfilehash: e0a6c566e8e0dfb77b5899f735020d0f1facf3d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88798377"
---
# <a name="change-your-two-factor-verification-method-and-settings"></a>De twee ledige verificatie methode en-instellingen wijzigen

Nadat u de beveiligings verificatie methoden voor uw werk-of school account hebt ingesteld, kunt u de gerelateerde gegevens bijwerken, waaronder:

- Standaard methode voor beveiligings verificatie

- Details van de beveiligings verificatie methode, zoals uw telefoon nummer

- Verificator-app instellen of verwijderen van een apparaat vanuit de verificator-app

## <a name="using-the-additional-security-verification-page"></a>De extra beveiligings verificatie pagina gebruiken

Als uw organisatie u specifieke stappen heeft gegeven over het inschakelen en beheren van uw twee ledige verificatie, moet u eerst deze instructies volgen. Als dat niet het geval is, kunt u de instellingen voor de beveiligings verificatie methode van de [extra beveiligings verificatie](./multi-factor-authentication-end-user-first-time.md) pagina.

>[!Note]
>Als datgene wat u op uw scherm ziet, niet overeenkomt met wat er in dit artikel wordt behandeld, betekent dit dat de beheerder de **beveiligings gegevens (preview-versie)** heeft ingeschakeld of dat uw organisatie uw eigen aangepaste portal heeft opgegeven. Zie [overzicht van beveiligings informatie (preview)](./security-info-setup-signin.md)voor meer informatie over de nieuwe ervaring met beveiligings informatie. Neem contact op met de Help Desk van uw organisatie voor meer informatie over de aangepaste portal van uw organisatie.

### <a name="to-get-to-the-additional-security-verification-page"></a>De extra beveiligings verificatie pagina weer geven

U kunt deze koppeling volgen op de [pagina aanvullende beveiligings verificatie](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1).

![Aanvullende beveiligings verificatie pagina, met de beschik bare beveiligings verificatie methode Details](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

U kunt ook de volgende stappen uitvoeren om de **extra beveiligings verificatie** pagina te openen:

1. Meld u aan bij [https://myapps.microsoft.com](https://myapps.microsoft.com).

1. Selecteer uw account naam in de rechter bovenhoek en selecteer vervolgens **profiel**.

1. Selecteer **aanvullende beveiligings verificatie**.  

    ![Mijn apps koppeling naar de pagina aanvullende beveiligings verificatie](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>Zie [app-wacht woorden beheren voor twee ledige verificatie](multi-factor-authentication-end-user-app-passwords.md)voor meer informatie over het gebruik van de sectie **app-wacht woorden** van een **aanvullende beveiligings verificatie** pagina. App-wacht woorden mogen alleen worden gebruikt voor apps die geen ondersteuning bieden voor twee ledige verificatie.

## <a name="change-your-default-security-verification-method"></a>Uw standaard methode voor beveiligings verificatie wijzigen

Nadat u zich hebt aangemeld bij uw werk-of school account met uw gebruikers naam en wacht woord, wordt u automatisch weer gegeven met de gekozen methode voor beveiligings verificatie. Afhankelijk van de vereisten van uw organisatie, kan dit een melding of een verificatie code via een verificator-app, een tekst bericht of een telefoon gesprek zijn.

Als u besluit dat u de standaard beveiligings verificatie methode die u gebruikt, wilt wijzigen, kunt u dat hier doen.

### <a name="to-change-your-default-security-verification-method"></a>De standaard methode voor beveiligings verificatie wijzigen

1. Selecteer op de pagina **aanvullende beveiligings verificatie** de methode die u wilt gebruiken in de lijst met de **gewenste opties** . Alle opties worden weer gegeven, maar u kunt alleen de instellingen selecteren die voor u beschikbaar zijn gesteld door uw organisatie.

    - **Stuur mij een melding via de app**: u wordt via uw verificator-app op de hoogte gesteld dat u een wacht woord hebt gecontroleerd.

    - **Mijn telefoon nummer bellen**: u krijgt een telefoon gesprek op uw mobiele apparaat, waarin u wordt gevraagd uw gegevens te verifiëren.

    - **Tekst code naar mijn authenticatie telefoon**: u krijgt een verificatie code als onderdeel van een SMS-bericht op uw mobiele apparaat. U moet deze code invoeren in de verificatie prompt voor uw werk-of school account.

    - **Mijn zakelijke telefoon bellen**: u krijgt een telefoon gesprek op uw zakelijke telefoon en u wordt gevraagd uw gegevens te verifiëren.

    - **Verificatie code van app gebruiken**: u gebruikt uw verificator-app om een verificatie code op te geven die u typt in de prompt van uw werk-of school account.

2. Selecteer **Opslaan**.

## <a name="add-or-change-your-phone-number"></a>Uw telefoon nummer toevoegen of wijzigen

U kunt nieuwe telefoon nummers toevoegen of bestaande nummers bijwerken op de pagina **aanvullende beveiligings verificatie** .

>[!Important]
>We raden u ten zeerste aan om een tweede telefoon nummer toe te voegen om te voor komen dat uw account wordt vergrendeld als uw primaire telefoon is kwijt geraakt of gestolen, of als u een nieuwe telefoon hebt en u niet langer het oorspronkelijke, primaire telefoon nummer hebt.

### <a name="to-change-your-phone-numbers"></a>Telefoon nummers wijzigen

1. Werk in de sectie **Hoe wilt u reageren?** van de pagina **aanvullende beveiligings verificatie** de telefoon nummer gegevens voor uw **telefoon voor authenticatie** (uw primaire mobiele apparaat) en uw **zakelijke telefoon**.

1. Schakel het selectie vakje naast de optie **alternatieve telefoon voor authenticatie** in en typ in een secundair telefoon nummer waar u telefoon gesprekken kunt ontvangen als u geen toegang hebt tot uw primaire apparaat.

1. Selecteer **Opslaan**.

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>Een nieuw account toevoegen aan de micro soft Authenticator-app

U kunt uw werk-of school account instellen op de Microsoft Authenticator-app voor [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) of [IOS](https://apps.apple.com/app/microsoft-authenticator/id983156458).

Als u uw werk-of school account al hebt ingesteld in de app Microsoft Authenticator, hoeft u dit niet opnieuw uit te voeren.

1. Selecteer in de sectie **Hoe wilt u reageren?** op de pagina **aanvullende beveiligings verificatie** de optie **verificator-app instellen**.

    ![Uw werk-of school account instellen in de Microsoft Authenticator-app](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

1. Volg de instructies op het scherm, inclusief het gebruik van uw mobiele apparaat om de QR-code te scannen en selecteer vervolgens **volgende**.

    U wordt gevraagd een melding via de app Microsoft Authenticator goed te keuren om uw gegevens te verifiëren.

1. Selecteer **Opslaan**.

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>Uw account of apparaat uit de Microsoft Authenticator-app verwijderen

U kunt uw account uit de app Microsoft Authenticator verwijderen en u kunt uw apparaat verwijderen uit uw werk-of school account. Normaal gesp roken verwijdert u uw apparaat om een verloren, gestolen of oud apparaat permanent uit uw account te verwijderen en verwijdert u uw account om te proberen enkele verbindings problemen op te lossen of om een account wijziging aan te pakken, zoals een nieuwe gebruikers naam.

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>Uw apparaat verwijderen uit uw werk-of school account

1. Selecteer de knop **verificator-app instellen** in het gedeelte **Hoe wilt u reageren?** van de pagina **aanvullende beveiligings verificatie** .

1. Selecteer **Opslaan**.

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>Uw account verwijderen uit de Microsoft Authenticator-app

Selecteer in de Microsoft Authenticator-app de knop **verwijderen** naast het apparaat dat u wilt verwijderen.

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>Twee ledige verificatie prompts inschakelen op een vertrouwd apparaat

Afhankelijk van de instellingen van uw organisatie ziet u mogelijk een selectie vakje waarin wordt aangegeven dat u de **X dagen niet opnieuw moet vragen** wanneer u twee ledige verificatie uitvoert voor uw browser. Als u deze optie hebt geselecteerd om twee ledige verificatie prompts te stoppen en u het apparaat kwijtraakt of het apparaat mogelijk is aangetast, moet u twee ledige verificatie prompts weer inschakelen om uw account te beveiligen. U moet de prompts inschakelen voor al uw apparaten tegelijk. Helaas kunt u de prompts niet weer inschakelen voor alleen een specifiek apparaat.

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>Om twee ledige verificatie vragen weer in te scha kelen voor uw apparaten

Selecteer op de [pagina **aanvullende beveiligings verificatie**](#to-get-to-the-additional-security-verification-page)de optie **multi-factor Authentication herstellen op eerder vertrouwde apparaten**. De volgende keer dat u zich aanmeldt op een apparaat, wordt u gevraagd om twee ledige verificatie uit te voeren.

## <a name="next-steps"></a>Volgende stappen

Nadat u uw twee ledige verificatie-instellingen hebt toegevoegd of bijgewerkt, kunt u uw app-wacht woorden beheren, aanmelden of hulp krijgen bij een aantal veelvoorkomende problemen met twee factoren.

- [App-wacht woorden beheren voor twee ledige verificatie](multi-factor-authentication-end-user-app-passwords.md) voor alle apps die geen ondersteuning bieden voor twee ledige verificatie.

- [Aanmelden met twee ledige verificatie](multi-factor-authentication-end-user-signin.md)

- [Veelvoorkomende problemen met twee ledige verificatie oplossen](multi-factor-authentication-end-user-troubleshoot.md)
---
title: Niet-micro soft-accounts toevoegen aan de Microsoft Authenticator-app-Azure AD
description: Voeg niet-micro soft-accounts, zoals voor Google of Facebook, toe aan de Microsoft Authenticator-app om uw identiteit te verifiëren tijdens het gebruik van twee ledige verificatie.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 11/02/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 21c8e75ac81a443b1dd9d4a0f43263bbf40bee88
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359197"
---
# <a name="add-non-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Niet-micro soft-accounts toevoegen aan de app Microsoft Authenticator

Als u niet-micro soft-accounts hebt, zoals Google, Facebook of GitHub, kunt u deze voor twee ledige verificatie toevoegen aan de app Microsoft Authenticator. De Microsoft Authenticator-app werkt met elke app die gebruikmaakt van twee ledige verificatie en elk account dat de op tijd gebaseerde mobiele TOTP-standaarden (one-time password) ondersteunt.

>[!Important]
>Voordat u uw account kunt toevoegen, moet u de app Microsoft Authenticator downloaden en installeren. Als u dat nog niet hebt gedaan, volgt u de stappen in het artikel [De app downloaden en installeren](user-help-auth-app-download-install.md).

## <a name="add-personal-accounts"></a>Persoonlijke accounts toevoegen

Over het algemeen moet u voor al uw persoonlijke accounts het volgende doen:

1. Meld u aan bij uw account en schakel twee ledige verificatie in met behulp van het apparaat of de computer.

2. Voeg het account toe aan de app Microsoft Authenticator. U wordt mogelijk gevraagd om een QR-code te scannen als onderdeel van dit proces.

    >[!Note]
    >Als dit de eerste keer is dat u de Microsoft Authenticator-app instelt, wordt u mogelijk gevraagd of u de app toegang wilt geven tot uw camera (iOS) of de wilt toestaan foto's te maken en video op te nemen (Android). U moet **Toestaan** selecteren, zodat de Authenticator-app toegang heeft tot uw camera om in de volgende stap een foto te maken van de QR-code. Als u de app geen cameratoegang geeft, kunt u de Authenticator-app wel installeren, maar moet u de codegegevens handmatig toevoegen. Zie [Handmatig een account toevoegen aan de app](user-help-auth-app-add-account-manual.md) voor informatie over het handmatig toevoegen van de code.

We bieden hier het proces voor uw Facebook-, Google-, GitHub-en Amazon-accounts, maar het proces is hetzelfde voor andere apps, zoals Insta gram en Adobe.

## <a name="add-your-google-account"></a>Uw Google-account toevoegen

Voeg uw Google-account toe door twee ledige verificatie in te scha kelen en het account vervolgens toe te voegen aan de app.

### <a name="turn-on-two-factor-verification"></a>Twee ledige verificatie inschakelen

1. Ga op uw computer naar https://myaccount.google.com/signinoptions/two-step-verification/enroll-welcome , selecteer **aan de slag** en controleer uw identiteit.

2. Volg de stappen op de pagina om verificatie in twee stappen voor uw persoonlijke Google-account in te scha kelen.

### <a name="add-your-google-account-to-the-app"></a>Uw Google-account toevoegen aan de app

1. Ga op de pagina beveiliging Google-account op uw computer naar https://myaccount.google.com/security) het gedeelte **meer tweede stappen toevoegen om dit te controleren** en kies **instellen** in de sectie verificator- **app** .

2. Op de pagina **codes van de verificator-app ophalen** selecteert u **Android** of **iPhone** op basis van uw telefoon type en selecteert u vervolgens **volgende**.

    U krijgt een QR-code die u kunt gebruiken om uw account automatisch te koppelen aan de app Microsoft Authenticator. Sluit dit venster niet.

3. Open de Microsoft Authenticator-app, selecteer **account toevoegen** uit het pictogram **aanpassen en beheer** in de rechter bovenhoek en selecteer vervolgens **ander account (Google, Facebook, enzovoort)**.

4. Gebruik de camera van uw apparaat om de QR-code te scannen vanaf de pagina **verificator instellen** op de computer.

    >[!Note]
    >Als uw camera niet goed werkt, kunt u de QR-code en URL hand matig invoeren.

5. Bekijk de pagina **accounts** van de Microsoft Authenticator-app op uw apparaat om er zeker van te zijn dat uw account gegevens juist zijn en dat er een verificatie code van zes cijfers is.

    Voor extra beveiliging verandert de verificatie code elke 30 seconden om te voor komen dat iemand meerdere keren een code gebruikt.

6. Selecteer **volgende** op de pagina **verificator instellen** op uw computer, typ de verificatie code van zes cijfers in de app voor uw Google-account en selecteer vervolgens **controleren**.

7. Uw account is gecontroleerd en u kunt de optie **gereed** selecteren om de **verificator** -pagina instellen te sluiten.

    >[!NOTE]
    >Zie voor meer informatie over twee ledige verificatie en uw Google-account [2-stap verificatie inschakelen](https://support.google.com/accounts/answer/185839) en [meer informatie over verificatie](https://www.google.com/landing/2step/help.html)in twee stappen.

## <a name="add-your-facebook-account"></a>Uw Facebook-account toevoegen

Voeg uw Facebook-account toe door twee ledige verificatie in te scha kelen en het account vervolgens toe te voegen aan de app.

### <a name="turn-on-two-factor-verification"></a>Twee ledige verificatie inschakelen

1. Open Facebook op uw computer, selecteer de vervolg keuzelijst in de rechter bovenhoek en ga vervolgens naar **instellingen**  >  **beveiliging en aanmelding**.

    De pagina **beveiliging en aanmelden** wordt weer gegeven.

2. Ga naar de optie **twee ledige verificatie gebruiken** in het gedeelte met **twee ledige verificatie** en selecteer vervolgens **bewerken**.

    De pagina **twee ledige verificatie** wordt weer gegeven.

3. Selecteer **inschakelen**.

### <a name="add-your-facebook-account-to-the-app"></a>Uw Facebook-account toevoegen aan de app

1. Op de Facebook-pagina op uw computer, gaat u naar de sectie **een back-up toevoegen** en kiest u **Setup** in het gebied voor de **verificatie-app** .

    U krijgt een QR-code die u kunt gebruiken om uw account automatisch te koppelen aan de app Microsoft Authenticator. Sluit dit venster niet.

2. Open de Microsoft Authenticator-app, selecteer **account toevoegen** uit het pictogram **aanpassen en beheer** in de rechter bovenhoek en selecteer vervolgens **ander account (Google, Facebook, enzovoort)**.

3. Gebruik de camera van uw apparaat om de QR-code te scannen op de **twee Factor Authentication** -pagina op uw computer.

    >[!Note]
    >Als uw camera niet goed werkt, kunt u de QR-code en URL hand matig invoeren.

4. Bekijk de pagina **accounts** van de Microsoft Authenticator-app op uw apparaat om er zeker van te zijn dat uw account gegevens juist zijn en dat er een verificatie code van zes cijfers is.

    Voor extra beveiliging verandert de verificatie code elke 30 seconden om te voor komen dat iemand meerdere keren een code gebruikt.

5. Selecteer **volgende** op de pagina **twee ledige verificatie** op uw computer en typ vervolgens de 6-cijferige verificatie code in de app voor uw Facebook-account.

    Uw account is geverifieerd en u kunt nu de app gebruiken om uw account te verifiëren.

    >[!NOTE]
    >Zie [Wat is twee ledige verificatie en hoe werkt het?](https://www.facebook.com/help/148233965247823)voor meer informatie over twee ledige verificatie en uw Facebook-account.

## <a name="add-your-github-account"></a>Uw GitHub-account toevoegen

Voeg uw GitHub-account toe door twee ledige verificatie in te scha kelen en het account vervolgens toe te voegen aan de app.

### <a name="turn-on-two-factor-verification"></a>Twee ledige verificatie inschakelen

1. Open GitHub op uw computer, selecteer uw installatie kopie in de rechter bovenhoek en selecteer vervolgens **instellingen**.

    De pagina **twee ledige verificatie** wordt weer gegeven.

2. Selecteer **beveiliging** in de zijbalk **persoonlijke instellingen** en selecteer vervolgens **twee ledige verificatie inschakelen** in het gebied **met twee ledige verificatie** .

### <a name="add-your-github-account-to-the-app"></a>Uw GitHub-account toevoegen aan de app

1. Selecteer op de pagina **twee ledige verificatie** op uw computer **instellen met behulp van een app**.

2. Sla uw herstel codes op zodat u weer terug kunt gaan naar uw account als u geen toegang meer hebt en selecteer **volgende**.

    U kunt uw codes opslaan door ze te downloaden naar uw apparaat, door een harde kopie af te drukken of door ze te kopiëren naar een hulp programma voor wachtwoord beheer.

3. Selecteer op de pagina **twee ledige verificatie** de optie **instellen met behulp van een app**.

    De pagina wordt gewijzigd om een QR-code weer te geven. Sluit deze pagina niet.

4. Open de Microsoft Authenticator-app, selecteer **account toevoegen** uit het pictogram **aanpassen en beheer** in de rechter bovenhoek, selecteer een **ander account (Google, Facebook, enzovoort)** en selecteer vervolgens **deze tekst code invoeren** in de tekst boven aan de pagina.

    De Microsoft Authenticator-app kan de QR-code niet scannen, dus u moet de code hand matig invoeren.

5. Voer een **account naam** in (bijvoorbeeld github) en typ de **geheime sleutel** uit stap 4, en selecteer vervolgens **volt ooien**.

6. Typ op de pagina **met de twee ledige verificator** op uw computer de 6-cijferige verificatie code in de app voor uw github-account en selecteer **inschakelen**.

    Op de pagina **accounts** van de app wordt uw account naam en een 6-cijferige verificatie code weer gegeven. Voor extra beveiliging verandert de verificatie code elke 30 seconden om te voor komen dat iemand meerdere keren een code gebruikt.

    >[!NOTE]
    >Zie [about Two-Factor Authentication](https://help.github.com/articles/about-two-factor-authentication/)(Engelstalig) voor meer informatie over twee ledige verificatie en uw github-account.

## <a name="add-your-amazon-account"></a>Uw Amazon-account toevoegen

Voeg uw Amazon-account toe door twee ledige verificatie in te scha kelen en het account vervolgens toe te voegen aan de app.

### <a name="turn-on-two-factor-verification"></a>Twee ledige verificatie inschakelen

1. Open op uw computer Amazon, selecteer de vervolg keuzelijst **Account & lijsten** en selecteer vervolgens **uw account**.

2. Selecteer **aanmelden & beveiliging**, Meld u aan bij uw Amazon-account en selecteer vervolgens **bewerken** in het gebied **Geavanceerde beveiligings instellingen** .

    De pagina **Geavanceerde beveiligings instellingen** wordt weer gegeven.

3. Selecteer **aan de slag**.

4. Selecteer **verificator-app** in de pagina **Kies hoe u codes wilt ontvangen** .

    De pagina wordt gewijzigd om een QR-code weer te geven. Sluit deze pagina niet.

5. Open de Microsoft Authenticator-app, selecteer **account toevoegen** uit het pictogram **aanpassen en beheer** in de rechter bovenhoek en selecteer vervolgens **ander account (Google, Facebook, enzovoort)**.

6. Gebruik de camera van uw apparaat om de QR-code te scannen vanaf de pagina **kiezen hoe u codes** op uw computer wilt ontvangen.

    >[!Note]
    >Als uw camera niet goed werkt, kunt u de QR-code en URL hand matig invoeren.

7. Bekijk de pagina **accounts** van de Microsoft Authenticator-app op uw apparaat om er zeker van te zijn dat uw account gegevens juist zijn en dat er een verificatie code van zes cijfers is.

    Voor extra beveiliging verandert de verificatie code elke 30 seconden om te voor komen dat iemand meerdere keren een code gebruikt.

8. Typ op de pagina **Kies hoe u codes wilt ontvangen** op uw computer de 6-cijferige verificatie code in de app voor uw Amazon-account en selecteer vervolgens **code verifiëren en door gaan**.

9. Voltooi de rest van het aanmeldings proces, inclusief het toevoegen van een verificatie methode voor back-ups, zoals een tekst bericht, en selecteer vervolgens **code verzenden**.

10. Typ op de pagina **een verificatie methode voor back-ups toevoegen** op uw computer de 6-cijferige verificatie code van uw back-upverificatie methode voor uw Amazon-account en selecteer vervolgens **code verifiëren en door gaan**.

11. Bepaal op de pagina **bijna klaar** of u uw computer een vertrouwd apparaat moet maken en selecteer vervolgens **ontvangen. Schakel Two-Step verificatie in**.

    De pagina **Geavanceerde beveiligings instellingen** wordt weer gegeven, met daarin de bijgewerkte gegevens over twee ledige verificatie.

    >[!NOTE]
    >Zie [over Two-Step verificatie](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) en [Aanmelden met Two-Step-verificatie](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440)voor meer informatie over twee ledige verificatie en uw Amazon-account.

## <a name="next-steps"></a>Volgende stappen

- Nadat u uw accounts aan de app hebt toegevoegd, kunt u zich aanmelden met behulp van de verificator-app op uw apparaat. Zie [Aanmelden met de app](user-help-auth-app-sign-in.md)voor meer informatie.

- Voor apparaten met iOS kunt u ook een back-up maken van uw account referenties en de bijbehorende app-instellingen, zoals de volg orde van uw accounts, naar de Cloud. Zie [back-up en herstellen met Microsoft Authenticator-app](user-help-auth-app-backup-recovery.md)voor meer informatie.

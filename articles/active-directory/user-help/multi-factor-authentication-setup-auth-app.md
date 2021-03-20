---
title: Een verificator-app instellen als tweeledige verificatiemethode - Azure Active Directory | Microsoft Docs
description: Informatie over het instellen van de app Microsoft Authenticator als tweeledige verificatiemethode.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/12/2019
ms.author: curtand
ms.openlocfilehash: 10b6b788077eccda522c9e8c6e631939ca1819fc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91530337"
---
# <a name="set-up-an-authenticator-app-as-your-two-factor-verification-method"></a>Een verificator-app instellen als tweeledige verificatiemethode

U kunt een verificator-app instellen voor het verzenden van een melding naar uw mobiele apparaat of om u een verificatiecode te sturen als uw methode voor beveiligingsverificatie. U bent niet verplicht de app Microsoft Authenticator te gebruiken en u kunt een andere app selecteren tijdens het instellingsproces. In dit artikel wordt echter uitgegaan van de app Microsoft Authenticator.

>[!Important]
>Voordat u uw account kunt toevoegen, moet u de app Microsoft Authenticator downloaden en installeren. Als u dat nog niet hebt gedaan, volgt u de stappen in het artikel [De app downloaden en installeren](user-help-auth-app-download-install.md).

>[!Note]
> Als de optie Mobiele app grijs wordt weergegeven, is het mogelijk dat u van uw organisatie geen Authenticator-app voor verificatie mag gebruiken. In dat geval moet u een andere methode selecteren of contact opnemen met de beheerder voor meer hulp.

## <a name="set-up-the-microsoft-authenticator-app-to-send-notifications"></a>De app Microsoft Authenticator instellen voor het verzenden van meldingen

1. Selecteer op de pagina [Aanvullende beveiligingsverificatie](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1) de optie **Mobiele app** in het gedeelte **Stap 1: Hoe moet er contact met u worden opgenomen?**

2. Selecteer **Meldingen voor verificatie ontvangen** in het gedeelte **Hoe wilt u de mobiele app gebruiken?** en selecteer vervolgens **Instellen**.

    ![Scherm afbeelding waarin de pagina aanvullende beveiligings verificatie wordt weer gegeven, met "mobiele app" en "meldingen voor verificatie ontvangen" geselecteerd.](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification.png)

    De pagina **Mobiele app configureren** wordt weergegeven.

    ![Scherm met de QR-code](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Open de Microsoft Authenticator-app, selecteer **Account toevoegen** in het pictogram **Aanpassen en controle** in de rechterbovenhoek en selecteer vervolgens **Werk- of schoolaccount**.

    >[!Note]
    >Als dit de eerste keer is dat u de Microsoft Authenticator-app instelt, wordt u mogelijk gevraagd of u de app toegang wilt geven tot uw camera (iOS) of de app wilt toestaan foto's te maken en video op te nemen (Android). U moet **Toestaan** selecteren, zodat de Authenticator-app toegang heeft tot uw camera om in de volgende stap een foto te maken van de QR-code. Als u de app geen cameratoegang geeft, kunt u de Authenticator-app wel installeren, maar moet u de codegegevens handmatig toevoegen. Zie [Handmatig een account toevoegen aan de app](user-help-auth-app-add-account-manual.md) voor informatie over het handmatig toevoegen van de code.

4. Gebruik de camera van uw apparaat om de QR-code te scannen vanuit het scherm **Mobiele app configureren** op uw computer en kies vervolgens **Volgende**.

5. Ga op uw computer terug naar de pagina **Aanvullende beveiligingsverificatie**, controleer of u het bericht hebt gekregen dat uw configuratie is geslaagd en selecteer vervolgens **Volgende**.

    ![Scherm opname van de pagina aanvullende beveiligings verificatie, waarbij de mobiele app is geconfigureerd... het bericht geslaagd is gemarkeerd.](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification-confirm.png)

    De Authenticator-app stuurt als test een melding naar uw mobiele apparaat.

6. Selecteer **Goedkeuren** op uw mobiele apparaat.

7. Voeg op uw computer het telefoonnummer van uw mobiele apparaat toe in het gedeelte **Stap 3: Als u geen toegang meer hebt tot de mobiele app** en selecteer vervolgens **Volgende**.

    U wordt ten sterkste aangeraden het telefoonnummer van uw mobiele apparaat toe te voegen als back-up als u de mobiele app om welke reden dan ook niet kunt gebruiken.

8. Kopieer vanuit het gedeelte **Stap 4: Bestaande toepassingen behouden**, het opgegeven app-wachtwoord en plak het in een veilige plaats.

    ![Het gedeelte App-wachtwoorden op de pagina Aanvullende beveiligingsverificatie](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Zie [App-wachtwoorden beheren](multi-factor-authentication-end-user-app-passwords.md) voor informatie over het gebruik van het app-wachtwoord voor oudere apps. U hoeft alleen app-wachtwoorden te gebruiken als u oudere apps blijft gebruiken die geen ondersteuning bieden voor tweeledige verificatie.

9. Selecteer **Done**.

## <a name="set-up-the-microsoft-authenticator-app-to-use-verification-codes"></a>De app Microsoft Authenticator instellen voor het gebruik van verificatiecodes

1. Selecteer op de pagina **Aanvullende beveiligingsverificatie** de optie **Mobiele app** in het gedeelte **Stap 1: Hoe moet er contact met u worden opgenomen?**

2. Selecteer **Verificatiecode gebruiken** in het gedeelte **Hoe wilt u de mobiele app gebruiken?** en selecteer vervolgens **Instellen**.

    ![Pagina Aanvullende beveiligingsverificatie, met opties voor mobiele app en meldingen](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-code.png)

    De pagina **Mobiele app configureren** wordt weergegeven.

    ![Scherm met de QR-code](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Open de Microsoft Authenticator-app, selecteer **Account toevoegen** in het pictogram **Aanpassen en controle** in de rechterbovenhoek en selecteer vervolgens **Werk- of schoolaccount**.

    >[!Note]
    >Als dit de eerste keer is dat u de Microsoft Authenticator-app instelt, wordt u mogelijk gevraagd of u de app toegang wilt geven tot uw camera (iOS) of de app wilt toestaan foto's te maken en video op te nemen (Android). U moet **Toestaan** selecteren, zodat de Authenticator-app toegang heeft tot uw camera om in de volgende stap een foto te maken van de QR-code. Als u de app geen cameratoegang geeft, kunt u de Authenticator-app wel installeren, maar moet u de codegegevens handmatig toevoegen. Zie [Handmatig een account toevoegen aan de app](user-help-auth-app-add-account-manual.md) voor informatie over het handmatig toevoegen van de code.

4. Gebruik de camera van uw apparaat om de QR-code te scannen vanuit het scherm **Mobiele app configureren** op uw computer en kies vervolgens **Volgende**.

5. Ga op uw computer terug naar de pagina **Aanvullende beveiligingsverificatie**, controleer of u het bericht hebt gekregen dat uw configuratie is geslaagd en selecteer vervolgens **Volgende**.

    ![Pagina Aanvullende beveiligingsverificatie, met bericht dat de configuratie is geslaagd](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-confirm.png)

    De Authenticator-app vraagt als test om een verificatiecode.

6. Schuif in de Microsoft Authenticator-app omlaag naar uw werk- of schoolaccount, kopieer en plak de 6-cijferige code van de app in het vak **Stap 2: Verificatiecode van mobiele app invoeren** op de computer en selecteer **Verifiëren**.

    ![Pagina Aanvullende beveiligingsverificatie, met test van verificatiecode](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-test.png)

7. Voeg op uw computer het telefoonnummer van uw mobiele apparaat toe in het gedeelte **Stap 3: Als u geen toegang meer hebt tot de mobiele app** en selecteer vervolgens **Volgende**.

    U wordt ten sterkste aangeraden het telefoonnummer van uw mobiele apparaat toe te voegen als back-up als u de mobiele app om welke reden dan ook niet kunt gebruiken.

8. Kopieer vanuit het gedeelte **Stap 4: Bestaande toepassingen behouden**, het opgegeven app-wachtwoord en plak het in een veilige plaats.

    ![Het gedeelte App-wachtwoorden op de pagina Aanvullende beveiligingsverificatie](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Zie [App-wachtwoorden beheren](multi-factor-authentication-end-user-app-passwords.md) voor informatie over het gebruik van het app-wachtwoord voor oudere apps. U hoeft alleen app-wachtwoorden te gebruiken als u oudere apps blijft gebruiken die geen ondersteuning bieden voor tweeledige verificatie.

9. Selecteer **Done**.

## <a name="next-steps"></a>Volgende stappen

Nadat u de tweeledige verificatiemethode hebt ingesteld, kunt u extra methoden toevoegen, uw instellingen en app-wachtwoorden beheren, u aanmelden of hulp krijgen bij enkele veelvoorkomende problemen met tweeledige verificatie.

- [Instellingen voor tweeledige verificatiemethode beheren](multi-factor-authentication-end-user-manage-settings.md)

- [App-wachtwoorden beheren](multi-factor-authentication-end-user-app-passwords.md)

- [Aanmelden met behulp van tweeledige verificatie](multi-factor-authentication-end-user-signin.md)

- [Hulp krijgen bij tweeledige verificatie](multi-factor-authentication-end-user-troubleshoot.md)

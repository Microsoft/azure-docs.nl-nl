---
title: Verificatie gegevens registreren om uw eigen wacht woord opnieuw in te stellen-Azure AD
description: Registreer de gegevens van de verificatie methode voor de selfservice voor wachtwoord herstel van Azure AD, zodat u uw eigen wacht woord opnieuw kunt instellen zonder de hulp van de beheerder.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.openlocfilehash: e460f3fd383286b1e61a979b87a9dfa09f272313
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88799023"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>Uw informatie over de verificatie methode registreren om uw eigen wacht woord opnieuw in te stellen

Als u uw werk-of school Wachtwoord verg eten bent, nooit een wacht woord hebt ontvangen van uw organisatie of uw account hebt vergrendeld, kunt u uw beveiligings gegevens en uw mobiele apparaat gebruiken om uw werk-of school wachtwoord opnieuw in te stellen.

Uw beheerder moet deze functie inschakelen om uw gegevens te kunnen registreren en uw eigen wacht woord opnieuw in te stellen. Als u de optie **mijn wacht woord verg eten** niet ziet, betekent dit dat uw beheerder de functie voor uw organisatie niet heeft ingeschakeld. Als u denkt dat dit onjuist is, neemt u contact op met de Help Desk voor ondersteuning.

>[!Important]
>Dit artikel is bedoeld voor gebruikers die zich aanmelden voor selfservice voor wachtwoord herstel willen gebruiken. Dit betekent dat u uw eigen werk-of school wachtwoord opnieuw kunt instellen (zoals alain@contoso.com ), zonder dat u hiervoor de hulp van de beheerder hoeft te gebruiken. Als u een beheerder bent die op zoek is naar informatie over het inschakelen van de selfservice voor wachtwoord herstel voor uw werk nemers of andere gebruikers, raadpleegt u de [implementatie van Azure AD self-service voor wachtwoord herstel en andere artikelen](../authentication/howto-sspr-deployment.md).

## <a name="set-up-your-password-reset-verification-method"></a>De verificatie methode voor het opnieuw instellen van het wacht woord instellen

1. Open de webbrowser op het apparaat en ga naar de [pagina met beveiligings gegevens](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup).

2. Afhankelijk van hoe uw beheerder uw organisatie heeft ingesteld, zijn een of meer van de volgende opties beschikbaar voor het instellen van uw beveiligings verificatie methode. Als er meerdere opties beschikbaar zijn, raden we u ten zeerste aan om meer dan een te gebruiken als uw beveiligings verificatie methode als een van uw methoden niet beschikbaar is.

    - **Verificatie-app.** Kies ervoor om de Microsoft Authenticator-app of andere verificator-app als uw methode voor beveiligings verificatie te gebruiken. Zie [de Microsoft Authenticator-app instellen als verificatie methode](security-info-setup-auth-app.md)voor meer informatie over het instellen van de app.

    - **SMS-berichten.** Kies voor het verzenden van tekst berichten naar je mobiele apparaat. Voor meer informatie over het instellen van tekst berichten raadpleegt [u SMS-berichten instellen als verificatie methode](security-info-setup-text-msg.md).

    - **Telefoon gesprekken.** Kies ervoor om een telefoon gesprek te krijgen met je geregistreerde telefoon nummer. Zie voor meer informatie over het instellen van telefoon gesprekken [een telefoon nummer instellen als verificatie methode](security-info-setup-phone-number.md).

    - **Beveiligings sleutel.** Kies ervoor om een met micro soft compatibele beveiligings sleutel te gebruiken. Zie [een beveiligings sleutel instellen als verificatie methode](security-info-setup-security-key.md)voor meer informatie.

    - **E-mail adres.** Kies of u een alternatief e-mail adres wilt gebruiken dat kan worden gebruikt zonder dat u het wacht woord verg eten of ontbreekt. Dit werkt alleen voor het opnieuw instellen van wacht woorden, niet als een beveiligings verificatie methode. Zie [een e-mail adres instellen als verificatie methode](security-info-setup-email.md)voor meer informatie over het instellen van een e-mail adres.

    - **Beveiligings vragen.** U kunt vooraf gedefinieerde beveiligings vragen instellen en beantwoorden die door uw beheerder zijn ingesteld. Dit werkt alleen voor het opnieuw instellen van wacht woorden, niet als een beveiligings verificatie methode. Zie [beveiligings vragen instellen als verificatie methode](security-info-setup-questions.md)voor meer informatie over beveiligings vragen.

3. Nadat u uw methoden hebt geselecteerd en ingesteld, kiest u **volt ooien** om het proces te volt ooien.

    > [!Note]
    > Informatie die is toegevoegd voor uw telefoon nummer of e-mail adres wordt niet gedeeld met de globale map van uw organisatie. De enige personen die deze informatie kunnen zien, zijn u en uw beheerder. Alleen u kunt de antwoorden op uw beveiligings vragen zien.

## <a name="common-problems-and-their-solutions"></a>Veelvoorkomende problemen en oplossingen

 Hier volgen enkele veelvoorkomende fout gevallen en hun oplossingen:

| Foutbericht |  Mogelijke oplossing |
| --- | --- | --- |
| Neem contact op met uw beheerder.<br>Het wacht woord voor uw gebruikers account wordt niet beheerd door micro soft. Als gevolg hiervan kunnen we uw wacht woord niet automatisch opnieuw instellen.<br>Neem contact op met uw IT-mede werkers voor verdere hulp.| Als dit fout bericht wordt weer gegeven nadat u uw gebruikers-ID hebt ingevoerd, betekent dit dat uw organisatie uw wacht woord intern beheert en u niet wilt dat uw wacht woord opnieuw wordt ingesteld op de koppeling **geen toegang tot uw account** . Als u uw wacht woord in deze situatie opnieuw wilt instellen, neemt u contact op met de Help Desk van uw organisatie of met uw beheerder voor hulp. |
| Het account is niet ingeschakeld voor het opnieuw instellen van het wacht woord.<br>Uw IT-afdeling heeft uw account niet ingesteld voor gebruik met deze service.<br>Als u wilt, kunnen we contact opnemen met een beheerder in uw organisatie om uw wacht woord opnieuw in te stellen. | Als dit fout bericht wordt weer gegeven nadat u uw gebruikers-ID hebt ingevoerd, betekent dit dat uw organisatie de functie voor het opnieuw instellen van het wacht woord niet heeft ingeschakeld of dat u deze niet mag gebruiken. Als u uw wacht woord in deze situatie opnieuw wilt instellen, moet u de koppeling **contact opnemen met Administrator** selecteren. Nadat u op de koppeling hebt geklikt, wordt er een e-mail bericht verzonden naar de Help Desk of beheerder van uw organisatie, zodat u weet dat u uw wacht woord opnieuw wilt instellen. |
| Uw account kan niet worden geverifieerd.<br>Als u wilt, kunnen we contact opnemen met een beheerder in uw organisatie om uw wacht woord opnieuw in te stellen. | Als dit fout bericht wordt weer gegeven nadat u uw gebruikers-ID hebt ingevoerd, betekent dit dat uw organisatie het opnieuw instellen van het wacht woord heeft ingeschakeld en dat u deze kunt gebruiken, maar niet hebt geregistreerd voor de service. In dit geval moet u contact opnemen met de Help Desk of beheerder van uw organisatie om uw wacht woord opnieuw in te stellen. Zie het bovenstaande procedure in dit artikel voor informatie over het registreren van het opnieuw instellen van het wacht woord voor wachtwoord herstel wanneer u weer op uw apparaat bent. |

## <a name="next-steps"></a>Volgende stappen

- [Uw wacht woord wijzigen met selfservice voor wachtwoord herstel](active-directory-passwords-update-your-own-password.md)

- [Pagina met beveiligings gegevens](https://mysignins.microsoft.com/security-info)

- [Portal voor het opnieuw instellen van een wachtwoord](https://passwordreset.microsoftonline.com/)

- [Als u zich niet kunt aanmelden bij uw Microsoft-account](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
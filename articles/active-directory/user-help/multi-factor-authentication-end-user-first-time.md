---
title: Wat is de pagina Aanvullende verificatie? - Azure AD
description: De aanvullende beveiligings verificatie pagina voor twee ledige verificatie weer geven
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.openlocfilehash: e5b07f8f7ae766d110c87a495a3e1623b815e526
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88795980"
---
# <a name="what-is-the-additional-verification-page"></a>Wat is de pagina Aanvullende verificatie?

Uw organisatie neemt extra stappen uit om ervoor te zorgen dat u bent wie u zegt wanneer u zich aanmeldt. Deze extra beveiligings verificatie wordt ook wel twee ledige verificatie genoemd. Het bestaat uit een combi natie van uw gebruikers naam, wacht woord en een mobiel apparaat of telefoon nummer. Als u de twee ledige verificatie voor een Microsoft-account bijvoorbeeld wilt uitschakelen alain@outlook.com , gebruikt u de instructies bij het [inschakelen van twee ledige verificatie in-of uitschakelen voor uw Microsoft-account](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

<center>

![Afbeelding van de conceptuele verificatie methoden](../authentication/media/concept-mfa-howitworks/methods.png)</center>

Verificatie met twee factoren is veiliger dan alleen een wacht woord omdat het afhankelijk is van twee vormen van verificatie:

- Iets wat u weet, zoals uw wacht woord.
- Iets wat u hebt, zoals een telefoon of een ander apparaat dat u uitvoert.

Verificatie met twee factoren kan helpen voor komen dat kwaadwillende hackers zich voordoen. Zelfs als ze uw wacht woord hebben, zijn de conflicteert dat ze niet uw apparaat hebben.

>[!Important]
>Als u een beheerder bent die op zoek is naar informatie over het inschakelen van tweeledige verificatie voor uw werknemers of andere gebruikers, raadpleegt u de [documentatie voor Azure Active Directory-verificatie](../authentication/index.yml). Dit artikel is bedoeld voor gebruikers die proberen twee ledige verificatie te gebruiken met een werk-of school account (zoals alain@contoso.com ).

## <a name="who-decides-if-you-use-this-feature"></a>Wie besluit of u deze functie gebruikt?

Wie beslist of u twee ledige verificatie gebruikt, is afhankelijk van het type account dat u hebt:

- **Werk- of schoolaccount.** Als u een werk-of school account gebruikt (zoals alain@contoso.com ), is het aan uw organisatie gekoppeld, of u nu twee ledige verificatie gebruikt, samen met de specifieke verificatie methoden. Omdat uw organisatie u heeft besloten om deze functie te gebruiken, is het niet mogelijk om deze afzonderlijk in te scha kelen.

- **Persoonlijk Microsoft-account.** U kunt ervoor kiezen om twee ledige verificatie in te stellen voor uw persoonlijke micro soft-accounts (zoals alain@outlook.com ). U kunt deze functie op elk gewenst moment in-of uitschakelen met behulp van de eenvoudige instructies bij het [inschakelen van twee ledige verificatie in-of uitschakelen voor uw Microsoft-account](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

    >[!Note]
    >Als u andere problemen ondervindt met twee ledige verificatie en een van uw persoonlijke micro soft-accounts, zijn er meer suggesties in [het gebruik van verificatie in twee stappen met uw Microsoft-account](https://support.microsoft.com/help/12408/microsoft-account-how-to-use-two-step-verification).

## <a name="open-the-additional-security-verification-page"></a>De extra beveiligings verificatie pagina openen

Wanneer uw organisatie twee ledige verificatie heeft ingeschakeld, ontvangt u elke keer dat u zich aanmeldt u een prompt om u meer informatie te geven om uw account veilig te houden.

![Vraag om meer info te verstrekken](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>Toegang krijgen tot de pagina Aanvullende beveiligingsverificatie

1. Selecteer **Volgende** in de prompt **Meer informatie vereist**.

    De pagina **Aanvullende beveiligingsverificatie** wordt weergegeven.

2. Selecteer op de pagina **aanvullende beveiligings verificatie** de twee ledige verificatie methode die u wilt gebruiken om te controleren of u bent wie u zegt wanneer u zich aanmeldt bij uw werk-of school account. U kunt het volgende selecteren:

    | Contactmethode | Beschrijving |
    | --- | --- |
    | Mobiele app | <ul><li>**Meldingen ontvangen voor verificatie.** Met deze optie wordt een melding naar de verificator-app op uw smartphone of tablet gepusht. Bekijk de melding en, indien deze in orde is, selecteer **Verifiëren** in de app. Uw werk of onderwijsinstelling kan vereisen dat u een pincode invoert voordat u zich verifieert.</li><li>**Verificatiecode gebruiken.** In deze modus genereert de app een verificatie code die elke 30 seconden wordt bijgewerkt. Voer de meest actuele verificatiecode in het aanmeldingsscherm in.<br>De Microsoft Authenticator-app is beschikbaar voor [Android](https://go.microsoft.com/fwlink/?linkid=866594) en [iOS](https://go.microsoft.com/fwlink/?linkid=866594).</li></ul> |
    | Telefoonnummer voor verificatie | <ul><li>**Telefonische oproep** voert een automatische spraakoproep uit naar het door u verstrekte nummer. Beantwoord de oproep en druk op het hekje (#) op het toetsenblok om u te verifiëren.</li><li>**Sms-bericht** verzendt een sms-bericht met een verificatiecode. Na de prompt in de tekst antwoordt u op het sms-bericht of voert u de opgegeven verificatiecode in de aanmeldingsinterface in.</li></ul> |
    | Bureautelefoon | Voert een automatische spraakoproep uit naar het door u verstrekte nummer. Beantwoord de oproep en druk op het hekje (#) op het toetsenblok om u te verifiëren. |

## <a name="next-steps"></a>Volgende stappen

Nadat u een twee ledige verificatie methode op de pagina **aanvullende beveiligings verificatie** hebt geselecteerd, moet u deze instellen:

- [Uw mobiele apparaat instellen als uw verificatiemethode](multi-factor-authentication-setup-phone-number.md)

- [Uw bureautelefoon instellen als uw verificatiemethode](multi-factor-authentication-setup-office-phone.md)

- [De app Microsoft Authenticator instellen als uw verificatiemethode](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>Gerelateerde resources

- [Aanmelden met behulp van tweeledige verificatie](multi-factor-authentication-end-user-signin.md)

- [Hulp krijgen bij tweeledige verificatie](multi-factor-authentication-end-user-troubleshoot.md)
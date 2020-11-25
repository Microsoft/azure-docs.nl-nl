---
title: Aanmeld wachtwoord voor een beveiligings sleutel (preview)-Azure Active Directory
description: Aanmeldings wachtwoord zonder wacht woord inschakelen voor Azure AD met behulp van FIDO2-beveiligings sleutels (preview-versie)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 09/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d9c4dff1e4a3ba7c7a2b11311e97eb5e66a1585
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994244"
---
# <a name="enable-passwordless-security-key-sign-in-preview"></a>Aanmelden zonder wacht woord voor beveiligings sleutel inschakelen (preview)

Voor ondernemingen die tegenwoordig wacht woorden gebruiken en een gedeelde PC-omgeving hebben, bieden beveiligings sleutels een naadloze manier voor het verifiëren van werk nemers zonder een gebruikers naam of wacht woord in te voeren. Beveiligings sleutels bieden een verbeterde productiviteit voor werk nemers en hebben betere beveiliging.

Dit document is gericht op het inschakelen van op wacht woord gebaseerde verificatie op basis van een sleutel. Aan het einde van dit artikel kunt u zich met behulp van een FIDO2-beveiligings sleutel aanmelden bij webtoepassingen met uw Azure AD-account.

> [!NOTE]
> FIDO2-beveiligings sleutels zijn een open bare preview-functie van Azure Active Directory. Zie [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews) voor meer informatie.

## <a name="requirements"></a>Vereisten

- [Azure AD-Multi-Factor Authentication](howto-mfa-getstarted.md)
- [Voor beeld van registratie van gecombineerde beveiligings gegevens](concept-registration-mfa-sspr-combined.md) inschakelen
- Compatibele [FIDO2-beveiligings sleutels](concept-authentication-passwordless.md#fido2-security-keys)
- Webauthn vereist Windows 10 versie 1903 of hoger * *

Als u beveiligings sleutels wilt gebruiken om u aan te melden bij Web-apps en-services, moet u een browser hebben die het webauthn-protocol ondersteunt. Dit zijn onder andere micro soft Edge, Chrome, Firefox en Safari.

## <a name="prepare-devices-for-preview"></a>Apparaten voorbereiden voor de preview-versie

In azure AD gekoppelde apparaten waarvoor u een pilot uitvoert, moeten Windows 10 versie 1909 of hoger worden uitgevoerd. De beste ervaring is met Windows 10 versie 1903 of hoger.

Aan hybride Azure AD gekoppelde apparaten moet Windows 10 versie 2004 of nieuwer worden uitgevoerd.

## <a name="enable-passwordless-authentication-method"></a>Verificatie methode met wacht woord inschakelen

### <a name="enable-the-combined-registration-experience"></a>De gecombineerde registratie-ervaring inschakelen

Registratie functies voor verificatie methoden met een wacht woord zijn afhankelijk van de functie voor gecombineerde registratie. Volg de stappen in het artikel [registratie van gecombineerde beveiligings gegevens inschakelen (preview)](howto-registration-mfa-sspr-combined.md)om gecombineerde registratie in te scha kelen.

### <a name="enable-fido2-security-key-method"></a>FIDO2-beveiligings sleutel methode inschakelen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Blader naar **Azure Active Directory**  >  **Security**  >  **Authentication methods**  >  **beleid voor verificatie methode** voor beveiligings verificatie methoden (preview).
1. Kies onder de **beveiligings sleutel methode FIDO2** de volgende opties:
   1. **Inschakelen** -ja of Nee
   1. **Doel** -alle gebruikers of Selecteer gebruikers
1. **Sla** de configuratie op.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Gebruikers registratie en het beheer van FIDO2-beveiligings sleutels

1. Ga naar [https://myprofile.microsoft.com](https://myprofile.microsoft.com) .
1. Meld u aan als dat nog niet het geval is.
1. Klik op **beveiligings gegevens**.
   1. Als de gebruiker al ten minste één Azure AD Multi-Factor Authentication-methode heeft geregistreerd, kunnen ze onmiddellijk een FIDO2-beveiligings sleutel registreren.
   1. Als er niet ten minste één Azure AD Multi-Factor Authentication-methode is geregistreerd, moeten deze er een worden toegevoegd.
1. Voeg een FIDO2-beveiligings sleutel toe door te klikken op **methode toevoegen** en **beveiligings sleutel** te kiezen.
1. Kies een **USB-apparaat** of **NFC-apparaat**.
1. Laat uw sleutel gereed en kies **volgende**.
1. Er wordt een vak weer gegeven waarin de gebruiker wordt gevraagd een pincode voor uw beveiligings sleutel te maken of in te voeren. vervolgens moet u de vereiste penbeweging voor de sleutel, biometrisch of touch, uitvoeren.
1. De gebruiker wordt teruggekeerd naar de gecombineerde registratie-ervaring en er wordt gevraagd om een duidelijke naam op te geven voor de sleutel, zodat de gebruiker kan identificeren dat er meerdere zijn. Klik op **Volgende**.
1. Klik op **gereed** om het proces te volt ooien.

## <a name="sign-in-with-passwordless-credential"></a>Aanmelden met een wacht woord zonder referenties

In het voor beeld onder een gebruiker heeft de FIDO2-beveiligings sleutel al ingericht. De gebruiker kan ervoor kiezen om zich aan te melden op het web met de beveiligings sleutel FIDO2 in een ondersteunde browser in Windows 10 versie 1903 of hoger.

![Aanmelden voor beveiligings sleutel micro soft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Probleemoplossing en feedback

Als u feedback wilt delen of problemen ondervindt tijdens het vooraf bekijken van deze functie, kunt u met de volgende stappen delen via de Windows feedback hub-app:

1. Start de **feedback-hub** en zorg ervoor dat u bent aangemeld.
1. Feedback verzenden in de volgende categorisatie:
   - Categorie: beveiliging en privacy
   - Subcategorie: FIDO
1. Als u logboeken wilt vastleggen, gebruikt u de optie om **mijn probleem opnieuw te maken**

## <a name="known-issues"></a>Bekende problemen

### <a name="security-key-provisioning"></a>Inrichten van beveiligings sleutel

Het inrichten van de beheerder en het ongedaan maken van de inrichting van beveiligings sleutels is niet beschikbaar in de open bare preview.

### <a name="upn-changes"></a>UPN-wijzigingen

We werken aan het ondersteunen van een functie waarmee UPN kan worden gewijzigd op hybride Azure AD en aan Azure AD gekoppelde apparaten. Als de UPN van een gebruiker wordt gewijzigd, kunt u FIDO2-beveiligings sleutels niet meer wijzigen om de wijziging aan te brengen. De oplossing is het opnieuw instellen van het apparaat en de gebruiker moet zich opnieuw registreren.

## <a name="next-steps"></a>Volgende stappen

[FIDO2 beveiligings sleutel voor Windows 10 aanmelden](howto-authentication-passwordless-security-key-windows.md)

[FIDO2-verificatie inschakelen voor on-premises resources](howto-authentication-passwordless-security-key-on-premises.md)

[Meer informatie over apparaatregistratie](../devices/overview.md)

[Meer informatie over Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)

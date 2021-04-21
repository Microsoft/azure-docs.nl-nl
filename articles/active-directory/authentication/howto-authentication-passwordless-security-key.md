---
title: Aanmelden met een beveiligingssleutel zonder wachtwoord - Azure Active Directory
description: Aanmelden met een beveiligingssleutel zonder wachtwoord inschakelen bij Azure AD met behulp van FIDO2-beveiligingssleutels
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/21/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 436a972693aafd220d277d7411c0da12636e9cc6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829796"
---
# <a name="enable-passwordless-security-key-sign-in"></a>Aanmelden met een beveiligingssleutel zonder wachtwoord inschakelen 

Voor ondernemingen die tegenwoordig gebruikmaken van wachtwoorden en een gedeelde pc-omgeving hebben, bieden beveiligingssleutels een naadloze manier voor werknemers om zich te verifiëren zonder een gebruikersnaam of wachtwoord in te voeren. Beveiligingssleutels bieden verbeterde productiviteit voor werknemers en betere beveiliging.

Dit document is gericht op het inschakelen van verificatie zonder wachtwoord op basis van een beveiligingssleutel. Aan het einde van dit artikel kunt u zich met uw Azure AD-account aanmelden bij webtoepassingen met behulp van een FIDO2-beveiligingssleutel.

## <a name="requirements"></a>Vereisten

- [Azure AD Multi-Factor Authentication](howto-mfa-getstarted.md)
- Registratie [van gecombineerde beveiligingsgegevens inschakelen](concept-registration-mfa-sspr-combined.md)
- Compatibele [FIDO2-beveiligingssleutels](concept-authentication-passwordless.md#fido2-security-keys)
- WebAuthN vereist Windows 10 versie 1903 of hoger**

Als u beveiligingssleutels wilt gebruiken om u aan te melden bij web-apps en -services, moet u een browser hebben die ondersteuning biedt voor het WebAuthN-protocol. Dit zijn Microsoft Edge, Chrome, Firefox en Safari.


## <a name="prepare-devices"></a>Apparaten voorbereiden

Voor apparaten die zijn samengevoegd met Azure AD is de beste ervaring Windows 10 versie 1903 of hoger.

Op hybride Azure AD-apparaten moet Windows 10 versie 2004 of hoger worden uitgevoerd.

## <a name="enable-passwordless-authentication-method"></a>Verificatiemethode zonder wachtwoord inschakelen

### <a name="enable-the-combined-registration-experience"></a>De gecombineerde registratie-ervaring inschakelen

Registratiefuncties voor verificatiemethoden zonder wachtwoord zijn afhankelijk van de gecombineerde registratiefunctie. Volg de stappen in het artikel [Registratie van gecombineerde beveiligingsgegevens inschakelen](howto-registration-mfa-sspr-combined.md)om gecombineerde registratie in teschakelen.

### <a name="enable-fido2-security-key-method"></a>FiDO2-beveiligingssleutelmethode inschakelen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Blader naar **Azure Active Directory**  >  **beveiligingsverificatiemethoden**  >    >  **Verificatiemethodebeleid**.
1. Kies onder de **methode FIDO2-beveiligingssleutel** de volgende opties:
   1. **Inschakelen** : Ja of Nee
   1. **Doel:** alle gebruikers of Gebruikers selecteren
1. **Sla de** configuratie op.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Gebruikersregistratie en -beheer van FIDO2-beveiligingssleutels

1. Blader naar [https://myprofile.microsoft.com](https://myprofile.microsoft.com).
1. Meld u aan als u dat nog niet hebt gedaan.
1. Klik **op Beveiligingsgegevens.**
   1. Als de gebruiker al ten minste één Azure AD Multi-Factor Authentication-methode heeft geregistreerd, kan deze onmiddellijk een FIDO2-beveiligingssleutel registreren.
   1. Als ze niet ten minste één Azure AD Multi-Factor Authentication-methode hebben geregistreerd, moeten ze er een toevoegen.
1. Voeg een FIDO2-beveiligingssleutel toe door te klikken op **Methode toevoegen** en **Beveiligingssleutel te kiezen.**
1. Kies **EEN USB-apparaat** of **NFC-apparaat.**
1. Maak uw sleutel gereed en kies **Volgende.**
1. Er wordt een vak weergegeven en de gebruiker wordt gevraagd een pincode voor uw beveiligingssleutel te maken/invoeren en vervolgens de vereiste beweging voor de sleutel uit te voeren, ofwel biometrisch of aanraken.
1. De gebruiker keert terug naar de gecombineerde registratie-ervaring en wordt gevraagd om een betekenisvolle naam voor de sleutel op te geven, zodat de gebruiker kan identificeren welke er meerdere heeft. Klik op **Volgende**.
1. Klik **op Done** om het proces te voltooien.

## <a name="sign-in-with-passwordless-credential"></a>Aanmelden met referenties zonder wachtwoord

In het onderstaande voorbeeld heeft een gebruiker zijn FIDO2-beveiligingssleutel al ingericht. De gebruiker kan zich op internet aanmelden met de FIDO2-beveiligingssleutel in een ondersteunde browser op Windows 10 versie 1903 of hoger.

![Aanmeldingssleutel voor beveiligingssleutels Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Probleemoplossing en feedback

Als u feedback wilt delen of problemen ondervindt met deze functie, kunt u delen via de Windows Feedback-hub app met behulp van de volgende stappen:

1. Start **Feedback-hub** en zorg ervoor dat u bent aangemeld.
1. Feedback verzenden onder de volgende categorisatie:
   - Categorie: Beveiliging en privacy
   - Subcategorie: FIDO
1. Als u logboeken wilt vastleggen, gebruikt u de optie **Mijn probleem opnieuw maken.**

## <a name="known-issues"></a>Bekende problemen

### <a name="security-key-provisioning"></a>Inrichting van beveiligingssleutels

Het inrichten en de inrichting van beveiligingssleutels voor beheerders is niet beschikbaar.

### <a name="cached-logon-on-hybrid-azure-ad-joined-devices"></a>Aanmelding in cache op hybride Azure AD-apparaten

Aanmelding met FIDO2-sleutels in de cache mislukt op hybride Azure AD-apparaten op Windows 10 versie 20H2. Als gevolg hiervan kunnen gebruikers zich niet aanmelden wanneer de zichtlijn naar de on-premises domeincontroller niet beschikbaar is. Dit wordt momenteel onderzocht.

### <a name="upn-changes"></a>UPN-wijzigingen

We werken aan de ondersteuning van een functie waarmee UPN kan worden gewijzigd op hybride Azure AD-apparaten en apparaten die zijn toegevoegd aan Azure AD. Als de UPN van een gebruiker wordt gewijzigd, kunt u fido2-beveiligingssleutels niet langer wijzigen om rekening te houden met de wijziging. De oplossing is om het apparaat opnieuw in te stellen en de gebruiker moet zich opnieuw registreren.

## <a name="next-steps"></a>Volgende stappen

[FIDO2-beveiligingssleutel Windows 10 aanmelden](howto-authentication-passwordless-security-key-windows.md)

[FIDO2-verificatie voor on-premises resources inschakelen](howto-authentication-passwordless-security-key-on-premises.md)

[Meer informatie over apparaatregistratie](../devices/overview.md)

[Meer informatie over Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)

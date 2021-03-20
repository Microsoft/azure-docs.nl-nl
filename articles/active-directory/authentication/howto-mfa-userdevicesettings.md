---
title: Verificatie methoden beheren voor Azure AD-Multi-Factor Authentication-Azure Active Directory
description: Meer informatie over het configureren van Azure Active Directory gebruikers instellingen voor Azure AD Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/04/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc0be7e50c5cce511fafd7d8b407626bd57659bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98183129"
---
# <a name="manage-user-authentication-methods-for-azure-ad-multi-factor-authentication"></a>Gebruikers authenticatie methoden voor Azure AD Multi-Factor Authentication beheren

Gebruikers in azure AD hebben twee verschillende sets contact gegevens:  

- Contact gegevens van open bare profielen, die worden beheerd in het gebruikers profiel en die zichtbaar zijn voor leden van uw organisatie. Voor gebruikers die zijn gesynchroniseerd vanaf de on-premises Active Directory, wordt deze informatie beheerd in een on-premises Windows Server-Active Directory Domain Services.
- Verificatie methoden, die altijd privé blijven en alleen worden gebruikt voor verificatie, waaronder multi-factor Authentication (MFA). Beheerders kunnen deze methoden beheren in de Blade verificatie methode van een gebruiker en gebruikers kunnen hun methoden beheren op de pagina beveiligings gegevens van MyAccount.

Bij het beheren van Azure AD Multi-Factor Authentication-methoden voor uw gebruikers kunnen verificatie beheerders het volgende doen: 

1. Voeg verificatie methoden toe voor een specifieke gebruiker, met inbegrip van telefoon nummers die worden gebruikt voor MFA.
1. Het wacht woord van een gebruiker opnieuw instellen.
1. Vereisen dat een gebruiker zich opnieuw registreert voor MFA.
1. Bestaande MFA-sessies intrekken.
1. De bestaande app-wacht woorden van een gebruiker verwijderen  

## <a name="add-authentication-methods-for-a-user"></a>Verificatie methoden voor een gebruiker toevoegen 

U kunt verificatie methoden voor een gebruiker toevoegen via de Azure Portal of Microsoft Graph.  

> [!NOTE]
> Uit veiligheids overwegingen moet u contact gegevens van de open bare gebruiker niet gebruiken om MFA uit te voeren. In plaats daarvan moeten gebruikers hun verificatie methode nummers invullen om voor MFA te gebruiken.  

:::image type="content" source="media/howto-mfa-userdevicesettings/add-authentication-method-detail.png" alt-text="Verificatie methoden van de Azure Portal toevoegen":::

Verificatie methoden voor een gebruiker toevoegen via de Azure Portal:  

1. Meld u aan bij de **Azure Portal**. 
1. Blader naar **Azure Active Directory**  >  **gebruikers**  >  **alle gebruikers**. 
1. Kies de gebruiker voor wie u een verificatie methode wilt toevoegen en selecteer **verificatie methoden**.  
1. Selecteer boven aan het venster de optie **+ verificatie methode toevoegen**.
   1. Selecteer een methode (telefoon nummer of e-mail adres). E-mail adres kan worden gebruikt voor zelf-wachtwoord herstel, maar niet voor verificatie. Wanneer u een telefoon nummer toevoegt, selecteert u een type telefoon en voert u het telefoon nummer in met een geldige indeling (bijvoorbeeld + 1 4255551234).
   1. Selecteer **Toevoegen**.

> [!NOTE]
> Met de preview-ervaring kunnen beheerders beschik bare verificatie methoden voor gebruikers toevoegen, terwijl de oorspronkelijke ervaring alleen het bijwerken van telefoon-en alternatieve telefoon methoden toestaat.

### <a name="manage-methods-using-powershell"></a>Methoden beheren met Power shell:  

Installeer de Power shell-module micro soft. Graph. Identity. aanmeldingen met de volgende opdrachten. 

```powershell
Install-module Microsoft.Graph.Identity.Signins
Connect-MgGraph -Scopes UserAuthenticationMethod.ReadWrite.All
Select-MgProfile -Name beta
```

Op telefoon gebaseerde verificatie methoden vermelden voor een specifieke gebruiker.

```powershell
Get-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com
```

Maak een verificatie methode voor mobiele telefoons voor een specifieke gebruiker.

```powershell
New-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -phoneType "mobile" -phoneNumber "+1 7748933135"
```

Een specifieke telefoon methode voor een gebruiker verwijderen

```powershell
Remove-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -PhoneAuthenticationMethodId 3179e48a-750b-4051-897c-87b9720928f7
```

Verificatie methoden kunnen ook worden beheerd met behulp van Microsoft Graph-Api's. meer informatie vindt u in het document [Azure AD-verificatie methoden API-overzicht](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true)

## <a name="manage-user-authentication-options"></a>Opties voor gebruikers verificatie beheren

Als u de rol *authenticatie beheerder* hebt toegewezen, kunt u vereisen dat gebruikers hun wacht woord opnieuw instellen, zich opnieuw registreren voor MFA of de bestaande MFA-sessies van hun gebruikers object intrekken. Voer de volgende stappen uit om gebruikers instellingen te beheren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer aan de linkerkant **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.
1. Kies de gebruiker waarvoor u een actie wilt uitvoeren en selecteer **verificatie methoden**. Selecteer boven aan het venster een van de volgende opties voor de gebruiker:
   - **Wacht woord opnieuw instellen** stelt het wacht woord van de gebruiker opnieuw in en wijst een tijdelijk wacht woord toe dat moet worden gewijzigd bij de volgende aanmelding.
   - **Vereisen dat het opnieuw registreren van MFA vereist** is, zodat wanneer de gebruiker de volgende keer zich aanmeldt, wordt gevraagd om een nieuwe MFA-verificatie methode in te stellen.
   
      > [!NOTE]
      > De momenteel geregistreerde verificatie methoden van de gebruiker worden niet verwijderd wanneer een beheerder opnieuw moet worden geregistreerd voor MFA. Nadat een gebruiker zich opnieuw heeft geregistreerd voor MFA, raden we u aan hun beveiligings gegevens te controleren en eventuele eerder geregistreerde authenticatie methoden te verwijderen die niet meer bruikbaar zijn.
   
   - Met **MFA-sessies intrekken** worden de MFA-sessies van de gebruiker gewist en moeten ze MFA de volgende keer dat het beleid op het apparaat wordt vereist, worden uitgevoerd.
   
    :::image type="content" source="media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png" alt-text="Verificatie methoden beheren vanuit de Azure Portal":::

## <a name="delete-users-existing-app-passwords"></a>Bestaande app-wacht woorden van gebruikers verwijderen

Beheerders kunnen voor gebruikers die app-wacht woorden hebben gedefinieerd, ervoor kiezen deze wacht woorden te verwijderen, waardoor verouderde verificatie in deze toepassingen mislukt. Deze acties kunnen nodig zijn als u hulp moet bieden aan een gebruiker of als u de verificatie methoden opnieuw wilt instellen. Niet-browser-apps die zijn gekoppeld aan deze app-wacht woorden, werken niet totdat er een nieuw app-wacht woord wordt gemaakt. 

Voer de volgende stappen uit om de app-wacht woorden van een gebruiker te verwijderen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer aan de linkerkant **Azure Active Directory**  >  **gebruikers**  >  **alle gebruikers**.
1. Selecteer **Multi-Factor Authentication**. Mogelijk moet u naar rechts schuiven om deze menu optie weer te geven. Selecteer de onderstaande scherm afbeelding om het volledige Azure Portal venster en de menu locatie te bekijken: [ ![ Selecteer multi-factor Authentication in het venster gebruikers in azure AD.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Schakel het selectie vakje in naast de gebruiker of gebruikers die u wilt beheren. Er wordt aan de rechter kant een lijst met opties voor snelle stappen weer gegeven.
1. Selecteer **gebruikers instellingen beheren** en schakel het selectie vakje in om **alle bestaande app-wacht woorden te verwijderen die door de geselecteerde gebruikers zijn gegenereerd**, zoals wordt weer gegeven in het volgende voor beeld: ![ alle bestaande app-wacht woorden verwijderen](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Selecteer **Opslaan** en **sluiten**.

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt uitgelegd hoe u afzonderlijke gebruikers instellingen kunt configureren. Zie [instellingen van Azure AD-multi-factor Authentication configureren](howto-mfa-mfasettings.md)voor het configureren van algemene azure AD multi-factor Authentication-Service-instellingen.

Als uw gebruikers hulp nodig hebben, raadpleegt u de [Gebruikers handleiding voor Azure AD multi-factor Authentication](../user-help/multi-factor-authentication-end-user-first-time.md).

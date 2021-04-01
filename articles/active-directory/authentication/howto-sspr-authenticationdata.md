---
title: Contact gegevens vooraf invullen voor selfservice voor wachtwoord herstel-Azure Active Directory
description: Informatie over het vooraf invullen van contact gegevens voor gebruikers van Azure Active Directory self-service voor wachtwoord herstel (SSPR), zodat ze de functie kunnen gebruiken zonder een registratie proces te volt ooien.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 10/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ed93944c2a94370250fb75a679c1ac37a2a8418c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861150"
---
# <a name="pre-populate-user-authentication-contact-information-for-azure-active-directory-self-service-password-reset-sspr"></a>Contact gegevens van gebruikers verificatie vooraf invullen voor Azure Active Directory self-service voor wachtwoord herstel (SSPR)

Als u Azure Active Directory (Azure AD) self-service voor het opnieuw instellen van wacht woorden (SSPR) wilt gebruiken, moet u de contact gegevens van de verificatie voor een gebruiker bevinden. Sommige organisaties hebben gebruikers hun verificatie gegevens zelf registreren. Andere organisaties synchroniseren liever met verificatie gegevens die al bestaan in Active Directory Domain Services (AD DS). Deze gesynchroniseerde gegevens worden beschikbaar gesteld aan Azure AD en SSPR zonder tussen komst van de gebruiker. Wanneer gebruikers hun wacht woord moeten wijzigen of opnieuw moeten instellen, kunnen ze dit ook doen, zelfs als ze hun contact gegevens nog niet hebben geregistreerd.

U kunt de contact gegevens van de verificatie vooraf invullen als u aan de volgende vereisten voldoet:

* U hebt de gegevens in uw on-premises Directory juist opgemaakt.
* U hebt [Azure AD Connect](../hybrid/how-to-connect-install-express.md) geconfigureerd voor uw Azure AD-Tenant.

Telefoon nummers moeten de indeling *+ CountryCode phonenumber* hebben, zoals *+ 1 4251234567*.

> [!NOTE]
> Er moet een spatie zijn tussen de land code en het telefoon nummer.
>
> Het opnieuw instellen van wacht woorden biedt geen ondersteuning voor telefoon uitbreidingen. Zelfs in de indeling van de *+ 1-4251234567X12345* worden uitbrei dingen verwijderd voordat de oproep wordt geplaatst.

## <a name="fields-populated"></a>Velden ingevuld

Als u de standaard instellingen in Azure AD Connect gebruikt, worden de volgende toewijzingen gemaakt om de contact gegevens van de verificatie voor SSPR in te vullen:

| On-premises Active Directory | Azure AD     |
|------------------------------|--------------|
| telephoneNumber              | Zakelijke telefoon |
| mobiel                       | Mobiele telefoon |

Nadat een gebruiker zijn of haar mobiele telefoon nummer heeft geverifieerd, wordt het *telefoon* veld onder **verificatie contact gegevens** in azure AD ook ingevuld met dit nummer.

## <a name="authentication-contact-info"></a>Contact gegevens voor verificatie

Op de pagina **verificatie methoden** voor een Azure AD-gebruiker in de Azure Portal, kan een globale beheerder de contact gegevens voor de verificatie hand matig instellen. U kunt bestaande methoden bekijken onder het gedeelte *bruikbare verificatie methoden* of op **+ verificatie methoden toevoegen**, zoals wordt weer gegeven in de volgende voorbeeld scherm afbeelding:

:::image type="content" source="media/howto-sspr-authenticationdata/user-authentication-contact-info.png" alt-text="Verificatie methoden beheren vanuit de Azure Portal":::

De volgende overwegingen zijn van toepassing op deze contact gegevens voor verificatie:

* Als het veld *telefoon* is ingevuld en *mobiele telefoon* is ingeschakeld in het SSPR-beleid, ziet de gebruiker dat nummer op de registratie pagina voor het opnieuw instellen van het wacht woord en tijdens de werk stroom voor het opnieuw instellen van wacht woorden.
* Als het veld *e-mail* is ingevuld en *e-mail adres* is ingeschakeld in het SSPR-beleid, ziet de gebruiker dat e-mail bericht op de registratie pagina voor het opnieuw instellen van het wacht woord en tijdens de werk stroom voor wacht woord opnieuw instellen.

## <a name="security-questions-and-answers"></a>Beveiligings vragen en-antwoorden

De beveiligings vragen en-antwoorden worden veilig opgeslagen in uw Azure AD-Tenant en zijn alleen toegankelijk voor gebruikers via de [SSPR-registratie Portal](https://aka.ms/ssprsetup). Beheerders kunnen de inhoud van de vragen en antwoorden van een andere gebruiker niet weer geven, instellen of wijzigen.

## <a name="what-happens-when-a-user-registers"></a>Wat er gebeurt wanneer een gebruiker zich registreert

Wanneer een gebruiker zich registreert, stelt de registratie pagina de volgende velden in:

* **Telefoon voor authenticatie**
* **E-mail verificatie**
* **Beveiligings vragen en-antwoorden**

Als u een waarde voor *mobiele telefoon* of *alternatief e-mail adres* hebt gegeven, kunnen gebruikers deze waarden direct gebruiken om hun wacht woord opnieuw in te stellen, zelfs als ze niet zijn geregistreerd voor de service.

Gebruikers zien deze waarden ook wanneer ze zich voor de eerste keer registreren en kunnen ze desgewenst wijzigen. Nadat de registratie is geslaagd, worden deze waarden respectievelijk opgeslagen in de velden *verificatie telefoon* en *verificatie-e* .

## <a name="set-and-read-the-authentication-data-through-powershell"></a>De verificatie gegevens instellen en lezen via Power shell

De volgende velden kunnen worden ingesteld via Power shell:

* *Alternatief e-mailadres*
* *Mobiele telefoon*
* *Telefoon (werk)*
    * Kan alleen worden ingesteld als u niet synchroniseert met een on-premises Directory.

> [!IMPORTANT]
> Er is een bekend gebrek aan pariteit in opdracht functies tussen Power shell v1 en Power shell v2. De [Microsoft Graph rest API (bèta) voor verificatie methoden](/graph/api/resources/authenticationmethods-overview) is de huidige techniek voor het bieden van moderne interactie.

### <a name="use-powershell-version-1"></a>Power shell versie 1 gebruiken

[Down load en installeer de Azure AD Power shell-module](/previous-versions/azure/jj151815(v=azure.100)#bkmk_installmodule)om aan de slag te gaan. Nadat de app is geïnstalleerd, gebruikt u de volgende stappen om elk veld te configureren.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Verificatie gegevens instellen met Power shell versie 1

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 4251234567"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 4252345678"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 4251234567" -PhoneNumber "+1 4252345678"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>De verificatie gegevens lezen met Power shell versie 1

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>De e-mail opties voor verificatie via telefoon en verificatie lezen

Gebruik de volgende opdrachten om het **e-mail adres** voor verificatie van de **telefoon** en verificatie te lezen wanneer u Power shell versie 1 gebruikt:

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Power shell versie 2 gebruiken

[Down load en installeer de Azure AD-versie 2 Power shell-module](/powershell/module/azuread/)om aan de slag te gaan.

Voer de volgende opdrachten uit om snel te installeren vanuit recente versies van Power shell die ondersteuning bieden voor `Install-Module` . De eerste regel controleert of de module al is geïnstalleerd:

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

Nadat de module is geïnstalleerd, gebruikt u de volgende stappen om elk veld te configureren.

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Verificatie gegevens instellen met Power shell versie 2

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 4251234567"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 4252345678"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 4251234567" -TelephoneNumber "+1 4252345678"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>De verificatie gegevens lezen met Power shell versie 2

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Volgende stappen

Wanneer de contact gegevens van de verificatie vooraf zijn ingevuld voor gebruikers, voltooit u de volgende zelf studie om selfservice voor wachtwoord herstel in te scha kelen:

> [!div class="nextstepaction"]
> [Selfservice voor wachtwoord herstel van Azure AD inschakelen](tutorial-enable-sspr.md)

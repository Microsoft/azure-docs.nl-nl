---
title: Self-service voor wachtwoord herstel-Azure Active Directory
description: Meer informatie over de verschillende opties voor de Azure Active Directory selfservice voor wachtwoord herstel
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: a53b193d16a2cefbde7877fd930e5fa73b0c6a36
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861269"
---
# <a name="password-policies-and-account-restrictions-in-azure-active-directory"></a>Wachtwoord beleid en account beperkingen in Azure Active Directory

In Azure Active Directory (Azure AD) is er een wachtwoord beleid waarmee instellingen worden gedefinieerd, zoals de complexiteit, lengte of leeftijd van het wacht woord. Er is ook een beleid voor het definiëren van acceptabele tekens en lengte voor gebruikers namen.

Als self-service voor wachtwoord herstel (SSPR) wordt gebruikt om een wacht woord in azure AD te wijzigen of opnieuw in te stellen, wordt het wachtwoord beleid gecontroleerd. Als het wacht woord niet voldoet aan de beleids vereisten, wordt de gebruiker gevraagd om het opnieuw te proberen. Voor Azure-beheerders gelden enkele beperkingen voor het gebruik van SSPR die verschillen met gewone gebruikers accounts.

In dit artikel worden de wachtwoord beleids instellingen en complexiteits vereisten beschreven die zijn gekoppeld aan gebruikers accounts in uw Azure AD-Tenant, en hoe u Power shell kunt gebruiken om de instellingen voor het verlopen van wacht woorden te controleren of in te stellen.

## <a name="username-policies"></a><a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Gebruikers beleidsregels

Elk account waarmee u zich aanmeldt bij Azure AD moet een unieke user principal name-kenmerk waarde (UPN) hebben die is gekoppeld aan hun account. In hybride omgevingen met een AD DS-omgeving (on-premises Active Directory Domain Services) die is gesynchroniseerd met Azure AD met behulp van Azure AD Connect, wordt de Azure AD-UPN standaard ingesteld op de on-premises UPN.

De volgende tabel bevat een overzicht van de beleids regels voor de gebruikers naam die van toepassing zijn op zowel on-premises AD DS accounts die zijn gesynchroniseerd met Azure AD, en voor Cloud gebruikers accounts die rechtstreeks in azure AD zijn gemaakt:

| Eigenschap | UserPrincipalName-vereisten |
| --- | --- |
| Toegestane tekens |<ul> <li>A – Z</li> <li>a-z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Tekens niet toegestaan |<ul> <li>Een wille keurig \@ \" teken dat de gebruikers naam niet van het domein scheidt.</li> <li>Kan geen punt teken bevatten: "." direct voor het \@ \" symbool "</li></ul> |
| Lengte beperkingen |<ul> <li>De totale lengte mag niet langer zijn dan 113 tekens</li><li>Er mogen Maxi maal 64 tekens zijn vóór het \@ \" symbool</li><li>Er mogen Maxi maal 48 tekens na het \@ \" symbool</li></ul> |

## <a name="azure-ad-password-policies"></a><a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Azure AD-wachtwoord beleid

Een wachtwoord beleid wordt toegepast op alle gebruikers accounts die rechtstreeks in azure AD worden gemaakt en beheerd. Sommige van deze wachtwoord beleids instellingen kunnen niet worden gewijzigd, maar u kunt [aangepaste verboden wacht woorden configureren voor Azure AD-wachtwoord beveiliging](tutorial-configure-custom-password-protection.md) of account vergrendelings parameters.

Standaard wordt een account vergrendeld na 10 mislukte aanmeldings pogingen met het verkeerde wacht woord. De gebruiker is één minuut geblokkeerd. Bij verdere onjuiste aanmeldings pogingen wordt de gebruiker vergrendeld om de duur van de tijd te verg Roten. [Slimme vergren deling](howto-password-smart-lockout.md) houdt de laatste drie ongeldige hashes met een onjuist wacht woord bij om te voor komen dat de vergrendelings teller voor hetzelfde wacht woord wordt verhoogd. Als iemand hetzelfde onjuiste wacht woord meermaals invoert, wordt het account niet vergrendeld. U kunt de drempel en duur voor de slimme vergren deling definiëren.

Het Azure AD-wachtwoord beleid is niet van toepassing op gebruikers accounts die zijn gesynchroniseerd vanuit een on-premises AD DS omgeving met behulp van Azure AD Connect, tenzij u *EnforceCloudPasswordPolicyForPasswordSyncedUsers* inschakelt.

De volgende opties voor het Azure AD-wachtwoord beleid zijn gedefinieerd. Tenzij anders vermeld, kunt u deze instellingen niet wijzigen:

| Eigenschap | Vereisten |
| --- | --- |
| Toegestane tekens |<ul><li>A – Z</li><li>a-z</li><li>0 – 9</li> <li>@ # $% ^ & *-_! + = [] {} &#124; \: ',. ? / \` ~ " ( ) ;</li> <li>lege ruimte</li></ul> |
| Tekens niet toegestaan | Unicode-tekens. |
| Wachtwoord beperkingen |<ul><li>Mini maal acht tekens en Maxi maal 256 tekens.</li><li>Vereist drie van de volgende vier:<ul><li>Kleine letters.</li><li>Hoofd letters.</li><li>Getallen (0-9).</li><li>Symbolen (Zie de vorige wachtwoord beperkingen).</li></ul></li></ul> |
| Geldigheids duur van wacht woord (maximale wachtwoord duur) |<ul><li>Standaard waarde: **90** dagen.</li><li>De waarde kan worden geconfigureerd met behulp `Set-MsolPasswordPolicy` van de-cmdlet uit de Azure Active Directory-module voor Windows Power shell.</li></ul> |
| Melding over het verlopen van het wacht woord (wanneer gebruikers op de hoogte worden gesteld van het verlopen van wacht woorden) |<ul><li>Standaard waarde: **14** dagen (voordat wacht woord verloopt).</li><li>De waarde kan worden geconfigureerd met behulp van de- `Set-MsolPasswordPolicy` cmdlet.</li></ul> |
| Wacht woord verlopen (wacht woorden nooit verlopen) |<ul><li>Standaard waarde: **False** (geeft aan dat het wacht woord een verval datum heeft).</li><li>De waarde kan worden geconfigureerd voor afzonderlijke gebruikers accounts met behulp van de- `Set-MsolUser` cmdlet.</li></ul> |
| Geschiedenis van wachtwoord wijzigingen | Het laatste wacht woord *kan niet* opnieuw worden gebruikt wanneer de gebruiker een wacht woord wijzigt. |
| Geschiedenis van wacht woord opnieuw instellen | Het laatste wacht woord *kan* opnieuw worden gebruikt wanneer de gebruiker een verg eten wacht woord opnieuw instelt. |

## <a name="administrator-reset-policy-differences"></a>Administrator reset policy differences (Verschillen herstelbeleid beheerder)

Beheerders accounts zijn standaard ingeschakeld voor selfservice voor het opnieuw instellen van wacht woorden en een sterk standaard beleid voor het opnieuw instellen van *twee poorten* wordt afgedwongen. Dit beleid kan afwijken van de instelling die u voor uw gebruikers hebt gedefinieerd en dit beleid kan niet worden gewijzigd. U moet de functionaliteit voor het opnieuw instellen van wacht woorden altijd testen als een gebruiker zonder dat er Azure-beheerders rollen zijn toegewezen.

Beheerders kunnen met een twee poort beleid geen beveiligings vragen gebruiken.

Voor het twee-poort beleid zijn twee soorten verificatie gegevens vereist, zoals een e-mail adres, een verificator-app of een telefoon nummer. Een beleid met twee poorts is van toepassing op de volgende omstandigheden:

* Alle volgende Azure-beheerders rollen worden beïnvloed:
  * Helpdesk beheerder
  * Serviceondersteuningsbeheerder
  * Factureringsbeheerder
  * Laag1-ondersteuning voor partner
  * Laag2-ondersteuning voor partner
  * Exchange-beheerder
  * Skype voor Bedrijven-beheerder
  * Gebruikersbeheerder
  * Schrijvers van mappen
  * Globale beheerder of bedrijfs beheerder
  * SharePoint-beheerder
  * Beheerder voor naleving
  * Toepassings beheerder
  * Beveiligingsbeheerder
  * Beheerder voor bevoorrechte rollen
  * Intune-beheerder
  * Service beheerder van de toepassings proxy
  * Dynamics 365-beheerder
  * Power BI-servicebeheerder
  * Verificatie beheerder
  * Beheerder voor geprivilegieerde authenticatie

* Als er 30 dagen zijn verstreken in een proef abonnement; of
* Er is een aangepast domein geconfigureerd voor uw Azure AD-Tenant, zoals *contoso.com*. of
* De identiteiten van de on-premises Directory Azure AD Connect worden gesynchroniseerd

U kunt het gebruik van SSPR voor beheerders accounts uitschakelen met de Power shell [-cmdlet Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings) . De `-SelfServePasswordResetEnabled $False` para meter schakelt SSPR voor beheerders uit.

### <a name="exceptions"></a>Uitzonderingen

Voor een one-Gate-beleid is één verificatie gegevens vereist, zoals een e-mail adres of telefoon nummer. Een beleid met één poort is van toepassing op de volgende omstandigheden:

* Het is binnen de eerste 30 dagen van een proef abonnement. of
* Er is geen aangepast domein geconfigureerd voor uw Azure AD-Tenant. Daarom wordt de standaard waarde **. onmicrosoft.com* gebruikt. Het standaard domein **. onmicrosoft.com* wordt niet aanbevolen voor productie gebruik. maar
* De identiteiten van Azure AD Connect zijn niet gesynchroniseerd

## <a name="password-expiration-policies"></a><a name="set-password-expiration-policies-in-azure-ad"></a>Wachtwoord verloop beleid

Een *globale beheerder* of *gebruikers beheerder* kan de [Microsoft Azure ad-module voor Windows PowerShell](/powershell/module/Azuread/) gebruiken om gebruikers wachtwoorden in te stellen die niet verlopen.

U kunt ook Power shell-cmdlets gebruiken om de configuratie nooit-expires te verwijderen of om te zien welke gebruikers wachtwoorden zijn ingesteld op nooit verlopen.

Deze richt lijnen zijn van toepassing op andere providers, zoals intune en Microsoft 365, die ook afhankelijk zijn van Azure AD voor identiteits-en adreslijst Services. Verlopen van wacht woorden is het enige deel van het beleid dat kan worden gewijzigd.

> [!NOTE]
> Alleen wacht woorden voor gebruikers accounts die niet zijn gesynchroniseerd via Azure AD Connect, kunnen zodanig worden geconfigureerd dat ze niet verlopen. Zie [ad verbinden met Azure AD](../hybrid/whatis-hybrid-identity.md)voor meer informatie over Directory synchronisatie.

### <a name="set-or-check-the-password-policies-by-using-powershell"></a>Set or check the password policies by using PowerShell (Wachtwoordbeleid instellen of controleren met behulp van PowerShell)

Als u aan de slag wilt gaan, [downloadt en installeert u de Azure AD Power shell-module](/powershell/module/Azuread/) en [verbindt u deze met uw Azure AD-Tenant](/powershell/module/azuread/connect-azuread#examples).

Nadat de module is geïnstalleerd, voert u de volgende stappen uit om elke taak als nodig uit te voeren.

### <a name="check-the-expiration-policy-for-a-password"></a>Het verloop beleid voor een wacht woord controleren

1. Open een Power shell-prompt en [Maak verbinding met uw Azure AD-Tenant](/powershell/module/azuread/connect-azuread#examples) met behulp van een *globale beheerder* of een beheerders account voor de *gebruiker* .
1. Voer een van de volgende opdrachten uit voor een afzonderlijke gebruiker of voor alle gebruikers:

   * Voer de volgende cmdlet uit om te controleren of het wacht woord van een enkele gebruiker is ingesteld op nooit verlopen. Vervang door `<user ID>` de gebruikers-id van de gebruiker die u wilt controleren, bijvoorbeeld *driley \@ contoso.onmicrosoft.com*:

       ```powershell
       Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

   * Als u de instelling **wacht woord verloopt nooit** wilt zien voor alle gebruikers, voert u de volgende cmdlet uit:

       ```powershell
       Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

### <a name="set-a-password-to-expire"></a>Wacht woord instellen op verlopen

1. Open een Power shell-prompt en [Maak verbinding met uw Azure AD-Tenant](/powershell/module/azuread/connect-azuread#examples) met behulp van een *globale beheerder* of een beheerders account voor de *gebruiker* .
1. Voer een van de volgende opdrachten uit voor een afzonderlijke gebruiker of voor alle gebruikers:

   * Voer de volgende cmdlet uit om het wacht woord van een gebruiker in te stellen, zodat het wacht woord is verlopen. Vervang door `<user ID>` de gebruikers-id van de gebruiker die u wilt controleren, bijvoorbeeld *driley \@ contoso.onmicrosoft.com*

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
       ```

   * Als u de wacht woorden van alle gebruikers in de organisatie wilt instellen zodat deze verlopen, gebruikt u de volgende cmdlet:

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
       ```

### <a name="set-a-password-to-never-expire"></a>Stel een wacht woord in om nooit te verlopen

1. Open een Power shell-prompt en [Maak verbinding met uw Azure AD-Tenant](/powershell/module/azuread/connect-azuread#examples) met behulp van een *globale beheerder* of een beheerders account voor de *gebruiker* .
1. Voer een van de volgende opdrachten uit voor een afzonderlijke gebruiker of voor alle gebruikers:

   * Voer de volgende cmdlet uit om het wacht woord van een gebruiker in te stellen op nooit verlopen. Vervang door `<user ID>` de gebruikers-id van de gebruiker die u wilt controleren, bijvoorbeeld *driley \@ contoso.onmicrosoft.com*

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
       ```

   * Voer de volgende cmdlet uit om de wacht woorden in te stellen van alle gebruikers in een organisatie die nooit verlopen.

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
       ```

   > [!WARNING]
   > Wacht woorden zijn ingesteld op `-PasswordPolicies DisablePasswordExpiration` nog steeds leeftijd op basis van het `pwdLastSet` kenmerk. Als u op basis van het `pwdLastSet` kenmerk de verval datum wijzigt in `-PasswordPolicies None` , moeten alle wacht woorden die een `pwdLastSet` ouder zijn dan 90 dagen, worden gewijzigd wanneer de gebruiker zich de volgende keer aanmeldt. Deze wijziging kan van invloed zijn op een groot aantal gebruikers.

## <a name="next-steps"></a>Volgende stappen

Zie [zelf studie: gebruikers in staat stellen hun account te ontgrendelen of wacht woorden opnieuw in te stellen met Azure Active Directory self-service voor wachtwoord herstel](tutorial-enable-sspr.md)om aan de slag te gaan met SSPR.

Zie problemen met het [opnieuw instellen van wacht woorden oplossen](./troubleshoot-sspr.md) als u of gebruikers problemen hebben met SSPR

---
title: Gebruikers toegang intrekken in een nood geval in Azure Active Directory | Microsoft Docs
description: Alle toegang voor een gebruiker intrekken in Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
author: curtand
ms.author: curtand
manager: daveba
ms.reviewer: krbain
ms.date: 12/02/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d808b920ddc6ff6f1d44252c27d67edd9c0dc353
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575513"
---
# <a name="revoke-user-access-in-azure-active-directory"></a>Gebruikers toegang intrekken in Azure Active Directory

In de scenario's waarvoor een beheerder kan vereisen dat alle toegang voor een gebruiker wordt ingetrokken, neemt u accounts voor ontsluitingen van werk nemers en andere insider Threats op. Afhankelijk van de complexiteit van de omgeving, kunnen beheerders verschillende stappen uitvoeren om ervoor te zorgen dat de toegang wordt ingetrokken. In sommige gevallen kan er sprake zijn van een periode tussen het initiëren van toegang en wanneer de toegang effectief wordt ingetrokken.

Als u de Risico's wilt beperken, moet u weten hoe tokens werken. Er zijn veel soorten tokens. deze vallen in een van de patronen die in de onderstaande secties worden genoemd.

## <a name="access-tokens-and-refresh-tokens"></a>Toegangs tokens en vernieuwings tokens

Toegangs tokens en vernieuwings tokens worden vaak gebruikt met dikke client toepassingen en worden ook gebruikt in browser toepassingen zoals apps van één pagina.

- Wanneer gebruikers worden geverifieerd bij Azure AD, wordt het autorisatie beleid geëvalueerd om te bepalen of de gebruiker toegang kan krijgen tot een specifieke resource.  

- Als dit is toegestaan, geeft Azure AD een toegangs token en een vernieuwings token voor de resource.  

- Toegangs tokens die standaard door Azure AD worden uitgegeven, worden voor één uur in de afgelopen tijd. Als het verificatie protocol toestaat, kan de app de gebruiker op de achtergrond opnieuw verifiëren door het vernieuwings token door te geven aan Azure AD wanneer het toegangs token verloopt.

Azure AD evalueert vervolgens het autorisatie beleid opnieuw. Als de gebruiker nog gemachtigd is, geeft Azure AD een nieuw toegangs token en vernieuwings token uit.

Toegangs tokens kunnen een beveiligings probleem zijn als de toegang moet worden ingetrokken binnen een tijd die korter is dan de levens duur van het token, meestal ongeveer een uur. Daarom werkt micro soft actief om [doorlopende toegang](../conditional-access/concept-continuous-access-evaluation.md) te geven tot Microsoft 365 toepassingen, waardoor de validatie van toegangs tokens in bijna realtime wordt gegarandeerd.  

## <a name="session-tokens-cookies"></a>Sessie tokens (cookies)

De meeste toepassingen op basis van de browser gebruiken sessie tokens in plaats van toegangs-en vernieuwings tokens.  

- Wanneer een gebruiker een browser opent en zich verifieert bij een toepassing via Azure AD, ontvangt de gebruiker twee sessie tokens. Een van Azure AD en een andere van de toepassing.  

- Zodra een toepassing een eigen sessie token heeft uitgegeven, is de toepassings sessie van toepassing op de toegang tot de toepassing. Op dit moment is de gebruiker alleen van invloed op het autorisatie beleid waarvan de toepassing op de hoogte is.

- Het autorisatie beleid van Azure AD wordt opnieuw geëvalueerd, net zo vaak als de toepassing de gebruiker terugstuurt naar Azure AD. Opnieuw evalueren gebeurt doorgaans op de achtergrond, hoewel de frequentie afhankelijk is van de configuratie van de toepassing. Het is mogelijk dat de app de gebruiker nooit terug naar Azure AD stuurt, zolang het sessie token geldig is.

- Een sessie token kan alleen worden ingetrokken als de toepassing de toegang intrekt op basis van zijn eigen autorisatie beleid. Azure AD kan een sessie token dat is uitgegeven door een toepassing, niet rechtstreeks intrekken.  

## <a name="revoke-access-for-a-user-in-the-hybrid-environment"></a>Toegang intrekken voor een gebruiker in de hybride omgeving

Voor een hybride omgeving met on-premises Active Directory gesynchroniseerd met Azure Active Directory, raadt micro soft IT-beheerders aan om de volgende acties uit te voeren.  

### <a name="on-premises-active-directory-environment"></a>On-premises Active Directory omgeving

Als beheerder in de Active Directory maakt u verbinding met uw on-premises netwerk, opent u Power shell en voert u de volgende acties uit:

1. Schakel de gebruiker uit in Active Directory. Raadpleeg [Disable-ADAccount](/powershell/module/addsadministration/disable-adaccount?view=win10-ps).

    ```PowerShell
    Disable-ADAccount -Identity johndoe  
    ```

1. Het wacht woord van de gebruiker twee keer opnieuw instellen in de Active Directory. Raadpleeg [set-ADAccountPassword](/powershell/module/addsadministration/set-adaccountpassword?view=win10-ps).

    > [!NOTE]
    > De reden voor het twee keer wijzigen van het wacht woord van een gebruiker is het verminderen van het risico van Pass-the-hash, met name als er vertragingen optreden bij de on-premises wachtwoord replicatie. Als u veilig kunt aannemen dat dit account niet is aangetast, kunt u het wacht woord slechts eenmaal opnieuw instellen.

    > [!IMPORTANT] 
    > Gebruik niet de voorbeeld wachtwoorden in de volgende cmdlets. Zorg ervoor dat u de wacht woorden wijzigt in een wille keurige teken reeks.

    ```PowerShell
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd1" -Force)
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd2" -Force)
    ```

### <a name="azure-active-directory-environment"></a>Azure Active Directory omgeving

Als beheerder in Azure Active Directory opent u Power shell, voert u uit ``Connect-AzureAD`` en neemt u de volgende acties:

1. Schakel de gebruiker uit in azure AD. Raadpleeg [set-AzureADUser](/powershell/module/azuread/Set-AzureADUser?view=azureadps-2.0).

    ```PowerShell
    Set-AzureADUser -ObjectId johndoe@contoso.com -AccountEnabled $false
    ```
1. Trek de Azure AD-vernieuwings tokens van de gebruiker in. Raadpleeg [REVOKE-AzureADUserAllRefreshToken](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0).

    ```PowerShell
    Revoke-AzureADUserAllRefreshToken -ObjectId johndoe@contoso.com
    ```

1. Schakel de apparaten van de gebruiker uit. Raadpleeg [Get-AzureADUserRegisteredDevice](/powershell/module/azuread/get-azureaduserregistereddevice?view=azureadps-2.0).

    ```PowerShell
    Get-AzureADUserRegisteredDevice -ObjectId johndoe@contoso.com | Set-AzureADDevice -AccountEnabled $false
    ```

## <a name="optional-steps"></a>Optionele stappen

- [Bedrijfs gegevens wissen uit door intune beheerde toepassingen](/mem/intune/apps/apps-selective-wipe).

- [Apparaten in bedrijfs eigendom wissen het apparaat wordt opnieuw ingesteld op de standaard fabrieks instellingen](/mem/intune/remote-actions/devices-wipe).

> [!NOTE]
> Gegevens op het apparaat kunnen niet worden hersteld na een gewist.

## <a name="when-access-is-revoked"></a>Wanneer de toegang wordt ingetrokken

Zodra beheerders de bovenstaande stappen hebben uitgevoerd, kan de gebruiker geen nieuwe tokens meer verwerven voor een toepassing die is gekoppeld aan Azure Active Directory. De verstreken tijd tussen het intrekken en de gebruiker verliezen van hun toegang is afhankelijk van hoe de toepassing toegang verleent:

- Voor **toepassingen die toegangs tokens gebruiken**, verliest de gebruiker de toegang wanneer het toegangs token verloopt.

- Voor **toepassingen die gebruikmaken van sessie tokens**, worden de bestaande sessies beëindigd zodra het token verloopt. Als de uitgeschakelde status van de gebruiker is gesynchroniseerd met de toepassing, kan de toepassing de bestaande sessies van de gebruiker automatisch intrekken als deze hiervoor is geconfigureerd.  Hoe lang het duurt, is afhankelijk van de frequentie van de synchronisatie tussen de toepassing en Azure AD.

## <a name="next-steps"></a>Volgende stappen

- [Veilige toegangs procedures voor Azure AD-beheerders](../roles/security-planning.md)
- [Gebruikers profiel gegevens toevoegen of bijwerken](../fundamentals/active-directory-users-profile-azure-portal.md)
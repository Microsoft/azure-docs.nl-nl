---
title: Multi-Factor Authentication per gebruiker inschakelen-Azure Active Directory
description: Meer informatie over het inschakelen van Azure AD-Multi-Factor Authentication per gebruiker door het wijzigen van de gebruikers status
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 08/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 749829f641119273813d3c8ca826daf8b4dc4d11
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742660"
---
# <a name="enable-per-user-azure-ad-multi-factor-authentication-to-secure-sign-in-events"></a>Azure AD-Multi-Factor Authentication per gebruiker inschakelen om aanmeldings gebeurtenissen te beveiligen

Als u aanmeldings gebeurtenissen van gebruikers wilt beveiligen in azure AD, kunt u multi-factor Authentication (MFA) vereisen. Het inschakelen van Azure AD Multi-Factor Authentication met behulp van beleid voor voorwaardelijke toegang is de aanbevolen benadering om gebruikers te beschermen. Voorwaardelijke toegang is een Azure AD Premium P1 of P2-functie waarmee u regels kunt Toep assen zodat MFA zo nodig in bepaalde scenario's wordt vereist. Zie [zelf studie: aanmeldings gebeurtenissen voor beveiligde gebruikers met Azure AD multi-factor Authentication](tutorial-enable-azure-mfa.md)om aan de slag te gaan met voorwaardelijke toegang.

Voor Azure AD-gratis tenants zonder voorwaardelijke toegang kunt u de [standaard instellingen voor beveiliging gebruiken om gebruikers te beveiligen](../fundamentals/concept-fundamentals-security-defaults.md). Gebruikers wordt om een MFA gevraagd als dat nodig is, maar u kunt niet uw eigen regels definiëren om het gedrag te bepalen.

Indien nodig kunt u elk account inschakelen voor een Azure AD-Multi-Factor Authentication per gebruiker. Wanneer gebruikers afzonderlijk worden ingeschakeld, voeren ze multi-factor Authentication telkens uit wanneer ze zich aanmelden (met enkele uitzonde ringen, bijvoorbeeld wanneer ze zich aanmelden bij vertrouwde IP-adressen of als de functie _MFA op vertrouwde apparaten onthouden_ is ingeschakeld).

Het wijzigen van de gebruikers status wordt niet aanbevolen, tenzij uw Azure AD-licenties geen voorwaardelijke toegang bevatten en u geen standaard instellingen voor beveiliging wilt gebruiken. Zie [functies en licenties voor Azure AD multi-factor Authentication](concept-mfa-licensing.md)voor meer informatie over de verschillende manieren om MFA in te scha kelen.

> [!IMPORTANT]
>
> In dit artikel wordt beschreven hoe u de status van Azure AD-Multi-Factor Authentication per gebruiker kunt bekijken en wijzigen. Als u voorwaardelijke toegang of standaard instellingen voor beveiliging gebruikt, controleert of schakelt u geen gebruikers accounts in met behulp van deze stappen.
>
> Als Azure AD Multi-Factor Authentication wordt ingeschakeld via een beleid voor voorwaardelijke toegang, wordt de status van de gebruiker niet gewijzigd. Geen waarschuwing als gebruikers worden uitgeschakeld weer gegeven. De status wordt niet gewijzigd door voorwaardelijke toegang.
>
> **U kunt geen Azure AD-Multi-Factor Authentication per gebruiker inschakelen of afdwingen als u beleid voor voorwaardelijke toegang gebruikt.**

## <a name="azure-ad-multi-factor-authentication-user-states"></a>Gebruikers statussen van Azure AD Multi-Factor Authentication

De status van een gebruiker geeft aan of een beheerder deze heeft inge schreven bij Azure AD Multi-Factor Authentication per gebruiker. Gebruikers accounts in azure AD Multi-Factor Authentication hebben de volgende drie statussen:

| Staat | Beschrijving | Beïnvloede verouderde verificatie | Browser-apps die worden beïnvloed | Betrokken moderne verificatie |
|:---:| --- |:---:|:--:|:--:|
| Uitgeschakeld | De standaard status voor een gebruiker die niet is inge schreven bij Azure AD Multi-Factor Authentication per gebruiker. | Nee | Nee | Nee |
| Ingeschakeld | De gebruiker is inge schreven bij Azure AD Multi-Factor Authentication per gebruiker, maar kan nog steeds hun wacht woord gebruiken voor verouderde verificatie. Als de gebruiker nog geen MFA-verificatie methoden heeft geregistreerd, krijgen ze een prompt om de volgende keer dat ze zich aanmelden te registreren met moderne authenticatie (zoals via een webbrowser). | Nee. Verouderde verificatie blijft werken totdat het registratie proces is voltooid. | Ja. Nadat de sessie is verlopen, is de registratie van Azure AD Multi-Factor Authentication vereist.| Ja. Nadat het toegangs token is verlopen, is de registratie van Azure AD Multi-Factor Authentication vereist. |
| Afgedwongen | De gebruiker is inge schreven per gebruiker in azure AD Multi-Factor Authentication. Als de gebruiker nog geen verificatie methoden heeft geregistreerd, krijgen ze een prompt om de volgende keer dat ze zich aanmelden te registreren met moderne authenticatie (zoals via een webbrowser). Gebruikers die de registratie in de *ingeschakelde* status volt ooien, worden automatisch verplaatst naar de status *afgedwongen* . | Ja. Apps vereisen app-wacht woorden. | Ja. Azure AD Multi-Factor Authentication is vereist bij het aanmelden. | Ja. Azure AD Multi-Factor Authentication is vereist bij het aanmelden. |

Alle gebruikers worden *uitgeschakeld*. Wanneer u gebruikers inschrijft in een Azure AD-Multi-Factor Authentication per gebruiker, verandert de status in *ingeschakeld*. Wanneer ingeschakelde gebruikers zich aanmelden en het registratie proces volt ooien, wordt de status ervan gewijzigd in *afgedwongen*. Beheerders kunnen gebruikers verplaatsen tussen statussen, waaronder van *afgedwongen* in *ingeschakeld* of *uitgeschakeld*.

> [!NOTE]
> Als MFA per gebruiker opnieuw wordt ingeschakeld voor een gebruiker en de gebruiker zich niet opnieuw registreert, wordt de MFA-status niet overgezet van *ingeschakeld* om te worden *afgedwongen* in de gebruikers interface voor MFA-beheer. De beheerder moet de gebruiker rechtstreeks verplaatsen naar *afgedwongen*.

## <a name="view-the-status-for-a-user"></a>De status van een gebruiker weer geven

Als u de gebruikers status wilt weer geven en beheren, voert u de volgende stappen uit om toegang te krijgen tot de Azure Portal-pagina:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als beheerder.
1. Zoek en selecteer *Azure Active Directory* en selecteer vervolgens **gebruikers**  >  **alle gebruikers**.
1. Selecteer **Multi-Factor Authentication**. Mogelijk moet u naar rechts schuiven om deze menu optie weer te geven. Selecteer de onderstaande scherm afbeelding om het volledige Azure Portal venster en de menu locatie te bekijken: [ ![ Selecteer multi-factor Authentication in het venster gebruikers in azure AD.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Er wordt een nieuwe pagina geopend waarin de gebruikers status wordt weer gegeven, zoals in het volgende voor beeld wordt weer gegeven.
   ![Scherm afbeelding met voor beeld van informatie over de gebruikers status voor Azure AD Multi-Factor Authentication](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>De status voor een gebruiker wijzigen

Voer de volgende stappen uit om de Azure AD-Multi-Factor Authentication status per gebruiker voor een gebruiker te wijzigen:

1. Volg de vorige stappen om [de status van een gebruiker weer te geven](#view-the-status-for-a-user) om toegang te krijgen tot de pagina Azure AD multi-factor Authentication- **gebruikers** .
1. Zoek de gebruiker die u wilt inschakelen voor Azure AD-Multi-Factor Authentication per gebruiker. Mogelijk moet u de weer gave boven aan **gebruikers** wijzigen.
   ![Selecteer de gebruiker waarvan u de status wilt wijzigen van het tabblad gebruikers](./media/howto-mfa-userstates/enable1.png)
1. Schakel het selectie vakje in naast de naam (en) van de gebruiker (s) waarvan u de status wilt wijzigen.
1. Klik aan de rechter kant onder **snelle stappen** op **inschakelen** of **uitschakelen**. In het volgende voor beeld heeft de gebruiker *John Smith* een vinkje naast hun naam en wordt ingeschakeld voor gebruik: ![ Schakel geselecteerde gebruiker inschakelen door op inschakelen te klikken in het menu snelle stappen](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Ingeschakelde* gebruikers worden automatisch overgeschakeld naar *afgedwongen* wanneer ze zich registreren voor Azure AD multi-factor Authentication. Wijzig de gebruikers status niet hand matig in *afdwinging* tenzij de gebruiker al is geregistreerd of wanneer de gebruiker een onderbreking in de verbindingen met verouderde verificatie protocollen kan ondervinden.

1. Bevestig uw selectie in het pop-upvenster dat wordt geopend.

Nadat u gebruikers hebt ingeschakeld, stelt u deze via e-mail op de hoogte. Vertel de gebruikers dat een prompt wordt weer gegeven om te vragen de volgende keer dat ze zich aanmelden te registreren. Als uw organisatie niet-browser-apps gebruikt die geen ondersteuning bieden voor moderne verificatie, moeten ze ook app-wacht woorden maken. Zie voor meer informatie de [hand leiding voor Azure AD multi-factor Authentication-eind gebruiker](../user-help/multi-factor-authentication-end-user-first-time.md) om hen te helpen aan de slag te gaan.

## <a name="change-state-using-powershell"></a>Status wijzigen met behulp van Power shell

Als u de gebruikers status wilt wijzigen met behulp van [Azure AD Power shell](/powershell/azure/), wijzigt u de `$st.State` para meter voor een gebruikers account. Er zijn drie mogelijke statussen voor een gebruikers account:

* *Ingeschakeld*
* *Afgedwongen*
* *Uitgeschakeld*  

Over het algemeen moet u gebruikers niet rechtstreeks naar de status *afgedwongen* verplaatsen, tenzij ze al zijn geregistreerd voor MFA. Als u dit doet, werken verouderde verificatie-apps niet meer omdat de gebruiker geen Azure AD-Multi-Factor Authentication registratie heeft door lopen en een [app-wacht woord](howto-mfa-app-passwords.md)heeft verkregen. Dit kan in sommige gevallen gewenst zijn, maar heeft invloed op de gebruikers ervaring totdat de gebruiker zich registreert.

Als u aan de slag wilt gaan, installeert u de *MSOnline* -module met behulp van [install-module](/powershell/module/powershellget/install-module) als volgt:

```PowerShell
Install-Module MSOnline
```

Maak vervolgens verbinding via [Connect-MsolService](/powershell/module/msonline/connect-msolservice):

```PowerShell
Connect-MsolService
```

In het volgende voor beeld Power shell-script wordt MFA ingeschakeld voor een individuele gebruiker met de naam *bsimon@contoso.com* :

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

Het gebruik van Power shell is een goede optie wanneer u gebruikers bulksgewijs wilt kunnen inschakelen. Het volgende script herhaalt een lijst met gebruikers en schakelt MFA in voor hun accounts. Definieer de gebruikers accounts die op de eerste regel `$users` als volgt zijn ingesteld:

   ```PowerShell
   # Define your list of users to update state in bulk
   $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"

   foreach ($user in $users)
   {
       $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
       $st.RelyingParty = "*"
       $st.State = "Enabled"
       $sta = @($st)
       Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
   }
   ```

Als u MFA wilt uitschakelen, wordt in het volgende voor beeld een gebruiker met [Get-MsolUser](/powershell/module/msonline/get-msoluser)en vervolgens de *StrongAuthenticationRequirements* die voor de gedefinieerde gebruiker zijn ingesteld, verwijderd met [set-MsolUser](/powershell/module/msonline/set-msoluser):

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

U kunt MFA ook rechtstreeks uitschakelen voor een gebruiker met [set-MsolUser](/powershell/module/msonline/set-msoluser) als volgt:

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access"></a>Gebruikers converteren van MFA per gebruiker naar voorwaardelijke toegang

De volgende Power shell kan u helpen bij het converteren van de conversie naar voorwaardelijke toegang op basis van Azure AD Multi-Factor Authentication.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> Als MFA opnieuw wordt ingeschakeld voor een gebruiker en de gebruiker zich niet opnieuw registreert, wordt de MFA-status niet overgezet van *ingeschakeld* om te worden *afgedwongen* in de gebruikers interface van MFA-beheer. In dit geval moet de beheerder de gebruiker rechtstreeks verplaatsen naar *afgedwongen*.

## <a name="next-steps"></a>Volgende stappen

Zie  [instellingen van Azure AD-multi-factor Authentication configureren](howto-mfa-mfasettings.md)als u Azure ad-multi-factor Authentication instellingen wilt configureren.

Zie [Manage User Settings with Azure ad multi-factor Authentication](howto-mfa-userdevicesettings.md)(Engelstalig) als u de gebruikers instellingen voor azure AD multi-factor Authentication wilt beheren.

Zie [Azure AD multi-factor Authentication-rapporten](howto-mfa-reporting.md)als u wilt weten waarom een gebruiker is gevraagd of niet wordt gevraagd MFA uit te voeren.

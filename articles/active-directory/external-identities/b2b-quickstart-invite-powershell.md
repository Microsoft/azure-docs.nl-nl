---
title: 'Quickstart: Een gastgebruiker toevoegen met PowerShell - Azure AD'
description: In deze snelstartgids leert u hoe u PowerShell gebruikt voor het verzenden van een uitnodiging naar een externe gebruiker van de Azure AD B2B-samenwerking.
services: active-directory
author: msmimart
ms.author: mimart
manager: celestedg
ms.reviewer: mal
ms.date: 08/28/2018
ms.topic: quickstart
ms.service: active-directory
ms.subservice: B2B
ms.custom:
- it-pro
- seo-update-azuread-jan
- mode-api
ms.collection: M365-identity-device-management
ms.openlocfilehash: 765d8ea99167512e553cef9cc6f7fed583eff469
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529812"
---
# <a name="quickstart-add-a-guest-user-with-powershell"></a>Quickstart: Een gastgebruiker toevoegen met PowerShell

Er zijn veel manieren waarop u externe partners voor uw apps en services met Azure Active Directory B2B-samenwerking kunt uitnodigen. In de vorige snelstartgids hebt u gezien hoe u gastgebruikers rechtstreeks toevoegt in de Azure Active Directory-adminportal. U kunt ook PowerShell gebruiken om gastgebruikers individueel of bulksgewijs toe te voegen. In deze snelstartgids gebruikt u de opdracht New-AzureADMSInvitation om een gastgebruiker toe te voegen aan uw Azure-tenant.

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 

## <a name="prerequisites"></a>Vereisten

### <a name="install-the-latest-azureadpreview-module"></a>Installeer de nieuwste versie van de AzureADPreview-module
Zorg ervoor dat u de nieuwste versie van de Microsoft Azure AD PowerShell voor Graph-module (AzureADPreview) installeert. 

Controleer eerst welke modules die u hebt geïnstalleerd. Open PowerShell als een gebruiker met verhoogde bevoegdheid (Uitvoeren als administrator) en voer de volgende opdracht uit:
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

Als de AzureADPreview-module zonder bericht verschijnt dat aangeeft dat er een nieuwere versie bestaat, bent u klaar. Anders doet u op basis van de uitvoer, het volgende:

- Als er geen resultaten worden geretourneerd, voert u de volgende opdracht uit om de AzureADPreview-module te installeren:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Als alleen de AzureAD-module wordt weergegeven in de resultaten, voer de volgende opdrachten uit om de AzureADPreview-module installeren: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Als alleen de AzureADPreview-module wordt weergegeven in de resultaten, maar u een bericht ontvangt dat aangeeft dat er een latere versie, voer de volgende opdrachten uit om de module bij te werken: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
  ```

Mogelijk krijgt u een opdrachtprompt dat u de module vanuit een niet-vertrouwde opslagplaats installeert. Dit gebeurt als u de opslagplaats PSGallery eerder niet hebt ingesteld als een vertrouwde opslagplaats. Druk op **Y** om de module te installeren.

### <a name="get-a-test-email-account"></a>Ophalen van een test e-mailaccount

U moet een test e-mailaccount hebben waarnaar u de uitnodiging kunt verzenden. Het account moet van buiten uw organisatie zijn. U kunt elk type account, met inbegrip van een sociale account zoals een gmail.com of outlook.com-adres gebruiken.

## <a name="sign-in-to-your-tenant"></a>Aanmelden bij uw tenant

Voer de volgende opdracht uit om verbinding te maken met het tenantdomein:

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```
Bijvoorbeeld `Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`.

Voer uw referenties in wanneer dit wordt gevraagd.

## <a name="send-an-invitation"></a>Een uitnodiging verzenden

1. Voer de volgende PowerShell-opdracht uit om een uitnodiging voor uw test e-mailaccount te verzenden (vervang **'Sanda'** en **sanda\@fabrikam.com** met uw test-e-mailaccount en e-mailadres): 

   ```powershell
   New-AzureADMSInvitation -InvitedUserDisplayName "Sanda" -InvitedUserEmailAddress sanda@fabrikam.com -InviteRedirectURL https://myapps.microsoft.com -SendInvitationMessage $true
   ```
2. De opdracht verzendt een uitnodiging naar het opgegeven e-mailadres. Controleer de uitvoer, die er als volgt moet uitzien:

   ![PowerShell-uitvoer wordt weergegeven in afwachting van acceptatie van de gebruiker](media/quickstart-invite-powershell/powershell-azureadmsinvitation-result.png)

## <a name="verify-the-user-exists-in-the-directory"></a>Controleer of de gebruiker bestaat in de map

1. Om te controleren of de uitgenodigde gebruiker is toegevoegd aan Azure AD, moet u de volgende opdracht uitvoeren:
 
   ```powershell
   Get-AzureADUser -Filter "UserType eq 'Guest'"
   ```
3. Controleer de uitvoer om ervoor te zorgen dat de gebruiker die u hebt uitgenodigd, wordt weergegeven met een user principal name (UPN) in de indeling *e-mailadres*#EXT#\@*domein*. Bijvoorbeeld *sanda_fabrikam.com#EXT#\@contoso.onmicrosoft.com*, waarbij contoso.onmicrosoft.com staat voor de organisatie waaruit u de uitnodigingen hebt verzonden.

   ![PowerShell-uitvoer met weergave van gastgebruiker toegevoegd](media/quickstart-invite-powershell/powershell-guest-user-added.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de test-gebruikersaccount niet langer nodig hebt, kunt u deze uit de map verwijderen. Voer de volgende opdracht uit om een gebruikersaccount te verwijderen:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```
Bijvoorbeeld: `Remove-AzureADUser -ObjectId "sanda_fabrikam.com#EXT#@contoso.onmicrosoft.com"`


## <a name="next-steps"></a>Volgende stappen
In deze snelstartgids hebt u één gastgebruiker uitgenodigd en toegevoegd aan uw map met behulp van PowerShell. Hierna leert u hoe u gastgebruikers ook kunt uitnodigen in één bulkbewerking met behulp van PowerShell.

> [!div class="nextstepaction"]
> [Zelfstudie: Bulksgewijs gebruikers uitnodigen voor Azure AD B2B-samenwerking](tutorial-bulk-invite.md)

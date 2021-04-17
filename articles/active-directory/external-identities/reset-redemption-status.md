---
title: De inwisselstatus van een gastgebruiker opnieuw instellen - Azure AD
description: Meer informatie over het opnieuw instellen van de inwisselstatus van de uitnodiging voor Azure Active Directory B2B-gastgebruikers in Azure AD External Identities.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/06/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5bfce7ef2621cbe3bbbfdd95bf9a75e427c8cbd
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531873"
---
# <a name="reset-redemption-status-for-a-guest-user-preview"></a>De inwisselstatus voor een gastgebruiker opnieuw instellen (preview)

Nadat een gastgebruiker uw uitnodiging voor B2B-samenwerking heeft ingewisseld, kan het zijn dat u de aanmeldingsgegevens moet bijwerken, bijvoorbeeld wanneer:

- De gebruiker wil zich aanmelden met een andere e-mail- en id-provider
- Het account voor de gebruiker in de eigen tenant is verwijderd en opnieuw gemaakt
- De gebruiker is verplaatst naar een ander bedrijf, maar heeft nog steeds dezelfde toegang tot uw resources nodig
- De verantwoordelijkheden van de gebruiker zijn doorgegeven aan een andere gebruiker

Als u deze scenario's eerder wilt beheren, moest u het account van de gastgebruiker handmatig verwijderen uit uw directory en de gebruiker opnieuw uitnodigen. U kunt nu PowerShell of de Microsoft Graph-uitnodigings-API gebruiken om de inwisselstatus van de gebruiker opnieuw in te stellen en de gebruiker opnieuw te uitnodigen terwijl de object-id, groepslidmaatschap en app-toewijzingen van de gebruiker behouden blijven. Wanneer de gebruiker de nieuwe uitnodiging inwisselt, verandert de UPN van de gebruiker niet, maar wordt de aanmeldingsnaam van de gebruiker gewijzigd in het nieuwe e-mailbericht. De gebruiker kan zich vervolgens aanmelden met het nieuwe e-mailadres of een e-mailbericht dat u hebt toegevoegd aan de `otherMails` eigenschap van het gebruikersobject.

## <a name="reset-the-email-address-used-for-sign-in"></a>Het e-mailadres voor aanmelding opnieuw instellen

Als een gebruiker zich wil aanmelden via een ander e-mailadres:

1. Zorg ervoor dat het nieuwe e-mailadres is toegevoegd aan de `mail` eigenschap of van het `otherMails` gebruikersobject. 
2.  Vervang het e-mailadres in de `InvitedUserEmailAddress` eigenschap door het nieuwe e-mailadres.
3. Gebruik een van de onderstaande methoden om de inwisselstatus van de gebruiker opnieuw in te stellen.

> [!NOTE]
>Tijdens de openbare preview hebben we twee aanbevelingen:
>- Wanneer u het e-mailadres van de gebruiker opnieuw instelt op een nieuw adres, wordt u aangeraden de eigenschap in te `mail` stellen. Op deze manier kan de gebruiker de uitnodiging inwisselen door zich aan te melden bij uw directory en de inwisselkoppeling in de uitnodiging te gebruiken.
>- Wanneer u de status van een B2B-gastgebruiker opnieuw inwerkt, moet u dit doen in de gebruikerscontext. Alleen-app-aanroepen worden momenteel niet ondersteund.
>
## <a name="use-powershell-to-reset-redemption-status"></a>PowerShell gebruiken om de inwisselstatus opnieuw in te stellen

Installeer de nieuwste AzureADPreview PowerShell-module en maak een nieuwe uitnodiging met ingesteld op het nieuwe `InvitedUserEmailAddress` e-mailadres en stel in op `ResetRedemption` `true` .

```powershell  
Uninstall-Module AzureADPreview 
Install-Module AzureADPreview 
Connect-AzureAD 
$ADGraphUser = Get-AzureADUser -objectID "UPN of User to Reset"  
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId 
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser -ResetRedemption $True 
```

## <a name="use-microsoft-graph-api-to-reset-redemption-status"></a>Een Microsoft Graph-API gebruiken om de inwisselstatus opnieuw in te stellen

Stel met [Microsoft Graph uitnodigings-API](/graph/api/resources/invitation?view=graph-rest-beta&preserve-view=true)de eigenschap in `resetRedemption` op en geef het nieuwe `true` e-mailadres op in de `invitedUserEmailAddress` eigenschap .

```json
POST https://graph.microsoft.com/beta/invitations  
Authorization: Bearer eyJ0eX...  
ContentType: application/json  
{  
   "invitedUserEmailAddress": "<<external email>>",  
   "sendInvitationMessage": true,  
   "invitedUserMessageInfo": {  
      "messageLanguage": "en-US",  
      "ccRecipients": [  
         {  
            "emailAddress": {  
               "name": null,  
               "address": "<<optional additional notification email>>"  
            }  
         } 
      ],  
      "customizedMessageBody": "<<custom message>>"  
},  
"inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",  
"invitedUser": {  
   "id": "<<ID for the user you want to reset>>"  
}, 
"resetRedemption": true 
}
```

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers Azure Active Directory B2B-samenwerking toevoegen met behulp van PowerShell](customize-invitation-api.md#powershell)
- [Eigenschappen van een Azure AD B2B-gastgebruiker](user-properties.md)

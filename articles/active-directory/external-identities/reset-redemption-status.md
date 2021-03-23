---
title: De aflossings status van een gast gebruiker opnieuw instellen-Azure AD
description: Meer informatie over het opnieuw instellen van de uitstel status van de uitnodiging voor een Azure Active Directory B2B gast-gebruikers in externe Azure AD-identiteiten.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2998c3ea0d65bd3c96bd1ac5bdfa8ff148c6c4cc
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780426"
---
# <a name="reset-redemption-status-for-a-guest-user"></a>De status van de terugbetaling voor een gast gebruiker opnieuw instellen

Nadat een gast gebruiker de uitnodiging voor B2B-samen werking heeft ingewisseld, is het mogelijk dat u de aanmeldings gegevens moet bijwerken, bijvoorbeeld wanneer:

- De gebruiker wil zich aanmelden met een ander e-mail adres en ID-provider
- Het account voor de gebruiker in hun thuis Tenant is verwijderd en opnieuw gemaakt
- De gebruiker is verplaatst naar een ander bedrijf, maar er is nog steeds dezelfde toegang tot uw resources nodig
- De verantwoordelijkheden van de gebruiker zijn door gegeven aan een andere gebruiker

Als u deze scenario's eerder wilt beheren, moet u het gebruikers account van de gast hand matig verwijderen uit de map en de gebruiker opnieuw uitnodigen. U kunt nu Power shell of de API van Microsoft Graph-uitnodiging gebruiken om de uitstel status van de gebruiker opnieuw in te stellen en de gebruiker opnieuw aan te vragen met behoud van de object-ID, groepslid maatschappen en app-toewijzingen van de gebruiker. Wanneer de gebruiker de nieuwe uitnodiging inwisselt, wordt de UPN van de gebruiker niet gewijzigd, maar wordt de aanmeldings naam van de gebruiker gewijzigd in de nieuwe e-mail. De gebruiker kan zich vervolgens aanmelden met de nieuwe e-mail of een e-mail bericht dat u hebt toegevoegd aan de `otherMails` eigenschap van het gebruikers object.

## <a name="reset-the-email-address-used-for-sign-in"></a>Het e-mail adres dat wordt gebruikt voor aanmelding opnieuw instellen

Als een gebruiker zich wil aanmelden met een ander e-mail adres:

1. Zorg ervoor dat het nieuwe e-mail adres wordt toegevoegd aan de `mail` `otherMails` eigenschap of van het gebruikers object. 
2.  Vervang het e-mail adres in de `InvitedUserEmailAddress` eigenschap door het nieuwe e-mail adres.
3. Gebruik een van de onderstaande methoden om de status van de gebruikers wisseling opnieuw in te stellen.

> [!NOTE]
>Wanneer u tijdens de open bare Preview het e-mail adres van de gebruiker opnieuw instelt, wordt aangeraden de `mail` eigenschap in te stellen op het nieuwe e-mail adres. Op deze manier kan de gebruiker de uitnodiging inwisselen door zich aan te melden bij uw adres lijst naast het gebruik van de opname koppeling in de uitnodiging.
>
## <a name="use-powershell-to-reset-redemption-status"></a>Power shell gebruiken om de status van de terugbetaling opnieuw in te stellen

Installeer de meest recente AzureADPreview Power shell-module en maak een nieuwe uitnodiging met `InvitedUserEmailAddress` ingesteld op het nieuwe e-mail adres en `ResetRedemption` Stel in op `true` .

```powershell  
Uninstall-Module AzureADPreview 
Install-Module AzureADPreview 
Connect-AzureAD 
$ADGraphUser = Get-AzureADUser -objectID "UPN of User to Reset"  
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId 
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser -ResetRedemption $True 
```

## <a name="use-microsoft-graph-api-to-reset-redemption-status"></a>Microsoft Graph-API gebruiken om de status van de terugbetaling opnieuw in te stellen

Stel met behulp van de [API voor Microsoft Graph-uitnodiging](/graph/api/resources/invitation?view=graph-rest-1.0)de eigenschap in `resetRedemption` op `true` en geef het nieuwe e-mail adres op in de `invitedUserEmailAddress` eigenschap.

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

- [Azure Active Directory B2B-samenwerkings gebruikers toevoegen met behulp van Power shell](customize-invitation-api.md#powershell)
- [Eigenschappen van een Azure AD B2B-gast gebruiker](user-properties.md)

---
title: Interne gebruikers uitnodigen voor B2B-samen werking-Azure AD
description: Als u interne gebruikers accounts voor partners, distributeurs, leveranciers, leveranciers en andere gasten hebt, kunt u overschakelen naar Azure AD B2B-samen werking door ze uit te nodigen om zich aan te melden met hun eigen externe referenties of aanmelding. Gebruik Power shell of de Microsoft Graph uitnodiging-API.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 802307a21873d15242c2e387ec0defe35f50bb20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99576427"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>Interne gebruikers uitnodigen voor B2B-samen werking

Voor de beschik baarheid van Azure AD B2B-samen werking kunnen organisaties samen werken met distributeurs, leveranciers, leveranciers en andere gast gebruikers door interne referenties voor hen in te stellen. Als u over interne gast gebruikers beschikt, kunt u ze uitnodigen om B2B-samen werking te gebruiken. Deze B2B-gast gebruikers kunnen hun eigen identiteiten en referenties gebruiken om zich aan te melden en u hoeft geen wacht woorden te onderhouden of de levens cyclus van accounts te beheren.

Als u een uitnodiging naar een bestaand intern account verzendt, kunt u de object-ID, UPN, groepslid maatschappen en app-toewijzingen van die gebruiker behouden. U hoeft de gebruiker niet hand matig te verwijderen en opnieuw te uitnodigen of om resources opnieuw toe te wijzen. Als u de gebruiker wilt uitnodigen, gebruikt u de API voor uitnodigingen om het interne gebruikers object en het e-mail adres van de gast gebruiker samen met de uitnodiging door te geven. Wanneer de gebruiker de uitnodiging accepteert, wijzigt de B2B-service het bestaande interne gebruikers object in een B2B-gebruiker. De gebruiker moet zich met hun B2B-referenties aanmelden bij cloud resources Services.

## <a name="things-to-consider"></a>Aandachtspunten

- **Toegang tot on-premises resources**: nadat de gebruiker is uitgenodigd voor B2B-samen werking, kunnen ze nog steeds hun interne referenties gebruiken om toegang te krijgen tot on-premises resources. U kunt dit voor komen door het wacht woord voor het interne account opnieuw in te stellen of te wijzigen. De uitzonde ring is [e-mail met eenmalige verificatie van de wachtwoord code](one-time-passcode.md). Als de verificatie methode van de gebruiker is gewijzigd in eenmalige wachtwoord code, kunnen ze hun interne referenties niet meer gebruiken.

- **Facturering**: deze functie wijzigt de User type voor de gebruiker niet, zodat het facturerings model van de gebruiker niet automatisch wordt overgeschakeld naar de [prijzen voor maandelijks actieve gebruikers (Mau) voor externe id's](external-identities-pricing.md). Als u MAU-prijzen voor de gebruiker wilt activeren, wijzigt u de User type voor de gebruiker in `guest` . Houd er ook rekening mee dat uw Azure AD-Tenant moet worden [gekoppeld aan een Azure-abonnement](external-identities-pricing.md#link-your-azure-ad-tenant-to-a-subscription) om Mau-facturering te activeren.

- **Uitnodiging is één richting**: u kunt interne gebruikers uitnodigen om B2B-samen werking te gebruiken, maar de B2B-referenties kunnen niet worden verwijderd zodra ze zijn toegevoegd. Als u de gebruiker weer wilt wijzigen in een gebruiker met alleen interne gebruikers, moet u het gebruikers object verwijderen en een nieuw account maken.

- **Teams**: wanneer de gebruiker toegang heeft tot teams met hun externe referenties, is de Tenant niet in eerste instantie beschikbaar in de Tenant kiezer teams. De gebruiker heeft toegang tot teams met behulp van een URL die de context van de Tenant bevat, bijvoorbeeld: `https://team.microsoft.com/?tenantId=<TenantId>` . Daarna wordt de Tenant beschikbaar in de Tenant kiezer teams.

- **On-premises gesynchroniseerde gebruikers**: voor gebruikers accounts die zijn gesynchroniseerd tussen on-premises en de Cloud, blijft de on-premises Directory de bron van de autoriteit nadat ze zijn uitgenodigd om B2B-samen werking te gebruiken. Wijzigingen die u aanbrengt in het on-premises account, worden gesynchroniseerd met het Cloud account, met inbegrip van het uitschakelen of verwijderen van het account. Daarom is het niet mogelijk om te voor komen dat de gebruiker zich aanmeldt bij het on-premises account en hun Cloud account behoudt door simpelweg het lokale account te verwijderen. In plaats daarvan kunt u het wacht woord voor een on-premises account instellen op een wille keurige GUID of een andere onbekende waarde.

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>Interne gebruikers uitnodigen voor B2B-samen werking

U kunt Power shell of de API voor uitnodigingen gebruiken om een B2B-uitnodiging naar de interne gebruiker te verzenden. Zorg ervoor dat het e-mail adres dat u wilt gebruiken voor de uitnodiging is ingesteld als het externe e-mail adres van het interne gebruikers object.

- U moet het e-mail adres van de gebruiker. mail-eigenschap gebruiken voor de uitnodiging.
- Het domein in de eigenschap E-mail van de gebruiker moet overeenkomen met het account dat ze gebruiken om zich aan te melden. Anders kunnen sommige services, zoals teams, de gebruiker niet verifiëren.

Standaard stuurt de uitnodiging de gebruiker een e-mail bericht dat ze zijn uitgenodigd, maar u kunt deze e-mail onderdrukken en uw eigen e-mail bericht verzenden.

> [!NOTE]
> Als u uw eigen e-mail adres of andere communicatie wilt verzenden, kunt u `New-AzureADMSInvitation` met gebruiken met `-SendInvitationMessage:$false` om gebruikers op de achtergrond uit te nodigen en vervolgens uw eigen e-mail bericht naar de geconverteerde gebruiker te verzenden. Zie [Azure AD B2B-samenwerkings-API en-aanpassing](customize-invitation-api.md).

## <a name="use-powershell-to-send-a-b2b-invitation"></a>Power shell gebruiken om een B2B-uitnodiging te verzenden

U hebt Azure AD Power shell-module versie 2.0.2.130 of hoger nodig. Gebruik de volgende opdracht om bij te werken naar de meest recente AzureAD Power shell-module en de interne gebruiker voor B2B-samen werking uit te nodigen:

```powershell
Uninstall-Module AzureAD
Install-Module AzureAD
$ADGraphUser = Get-AzureADUser -objectID "UPN of Internal User"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>De API van de uitnodiging gebruiken om een B2B-uitnodiging te verzenden

In het onderstaande voor beeld ziet u hoe u de API voor uitnodigingen aanroept om een interne gebruiker als B2B-gebruiker uit te nodigen.

```json
POST https://graph.microsoft.com/v1.0/invitations
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
        "id": "<<ID for the user you want to convert>>"
    }
}
```

Het antwoord op de API is hetzelfde als het antwoord dat u ontvangt wanneer u een nieuwe gast gebruiker in de Directory uitnodigt.
## <a name="next-steps"></a>Volgende stappen

- [Inwisseling uitnodiging voor B2B-samen werking](redemption-experience.md)

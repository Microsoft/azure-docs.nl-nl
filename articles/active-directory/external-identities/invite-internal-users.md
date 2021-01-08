---
title: Interne gebruikers uitnodigen voor B2B-samen werking-Azure AD
description: Als u interne gebruikers accounts voor partners, distributeurs, leveranciers, leveranciers en andere gasten hebt, kunt u overschakelen naar Azure AD B2B-samen werking door ze uit te nodigen om zich aan te melden met hun eigen externe referenties of aanmelding. Gebruik Power shell of de Microsoft Graph uitnodiging-API.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8acc547552cecaebb60888bb7b9777f6279b9b7c
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015756"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>Interne gebruikers uitnodigen voor B2B-samen werking

> [!NOTE]
> Het uitnodigen van interne gebruikers voor het gebruik van B2B-samen werking is een open bare preview-functie van Azure Active Directory. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Voor de beschik baarheid van Azure AD B2B-samen werking kunnen organisaties samen werken met distributeurs, leveranciers, leveranciers en andere gast gebruikers door interne referenties voor hen in te stellen. Als u over interne gast gebruikers beschikt, kunt u ze uitnodigen voor B2B-samen werking, zodat u kunt profiteren van de voor delen van Azure AD B2B. Uw B2B-gast gebruikers kunnen hun eigen identiteiten en referenties gebruiken om zich aan te melden en u hoeft geen wacht woorden te onderhouden of de levens cyclus van accounts te beheren.

Als u een uitnodiging naar een bestaand intern account verzendt, kunt u de object-ID, UPN, groepslid maatschappen en app-toewijzingen van die gebruiker behouden. U hoeft de gebruiker niet hand matig te verwijderen en opnieuw te uitnodigen of om resources opnieuw toe te wijzen. Als u de gebruiker wilt uitnodigen, gebruikt u de API voor uitnodigingen om het interne gebruikers object en het e-mail adres van de gast gebruiker samen met de uitnodiging door te geven. Wanneer de gebruiker de uitnodiging accepteert, wijzigt de B2B-service het bestaande interne gebruikers object in een B2B-gebruiker. De gebruiker moet zich met hun B2B-referenties aanmelden bij cloud resources Services. Ze kunnen nog steeds hun interne referenties gebruiken om toegang te krijgen tot on-premises resources, maar u kunt dit voor komen door het wacht woord voor het interne account opnieuw in te stellen of te wijzigen.

> [!NOTE]
> De uitnodiging is in één richting. U kunt interne gebruikers uitnodigen om B2B-samen werking te gebruiken, maar de B2B-referenties kunnen niet worden verwijderd zodra ze zijn toegevoegd. Als u de gebruiker weer wilt wijzigen in een gebruiker met alleen interne gebruikers, moet u het gebruikers object verwijderen en een nieuw account maken.

In de open bare preview-versie kan de in dit artikel beschreven methode voor het uitnodigen van interne gebruikers voor B2B-samen werking niet worden gebruikt in deze gevallen:

- De interne gebruiker heeft een toegewezen Exchange-licentie.
- De gebruiker is afkomstig uit een domein dat is ingesteld voor directe Federatie in uw Directory.
- De interne gebruiker is een alleen-Cloud account en het hoofd account is niet in azure AD.

Als de interne gebruiker in deze gevallen moet worden gewijzigd in een B2B-gebruiker, moet u het interne account verwijderen en de gebruiker een uitnodiging sturen voor B2B-samen werking.

**On-premises gesynchroniseerde gebruikers**: voor gebruikers accounts die zijn gesynchroniseerd tussen on-premises en de Cloud, blijft de on-premises Directory de bron van de autoriteit nadat ze zijn uitgenodigd om B2B-samen werking te gebruiken. Wijzigingen die u aanbrengt in het on-premises account, worden gesynchroniseerd met het Cloud account, met inbegrip van het uitschakelen of verwijderen van het account. Daarom is het niet mogelijk om te voor komen dat de gebruiker zich aanmeldt bij het on-premises account en hun Cloud account behoudt door simpelweg het lokale account te verwijderen. In plaats daarvan kunt u het wacht woord voor een on-premises account instellen op een wille keurige GUID of een andere onbekende waarde.

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>Interne gebruikers uitnodigen voor B2B-samen werking

U kunt Power shell of de API voor uitnodigingen gebruiken om een B2B-uitnodiging naar de interne gebruiker te verzenden. Zorg ervoor dat het e-mail adres dat u wilt gebruiken voor de uitnodiging is ingesteld als het externe e-mail adres van het interne gebruikers object.

- U moet het e-mail adres van de gebruiker. mail-eigenschap gebruiken voor de uitnodiging.
- Het domein in de eigenschap E-mail van de gebruiker moet overeenkomen met het account dat ze gebruiken om zich aan te melden. Anders kunnen sommige services, zoals teams, de gebruiker niet verifiëren.

Standaard stuurt de uitnodiging de gebruiker een e-mail bericht dat ze zijn uitgenodigd, maar u kunt deze e-mail onderdrukken en uw eigen e-mail bericht verzenden.

> [!NOTE]
> Als u uw eigen e-mail of andere communicatie wilt verzenden, kunt u New-AzureADMSInvitation gebruiken met-SendInvitationMessage: $false om gebruikers op de achtergrond uit te nodigen en vervolgens uw eigen e-mail bericht naar de geconverteerde gebruiker te verzenden. Zie [Azure AD B2B-samenwerkings-API en-aanpassing](customize-invitation-api.md).

## <a name="use-powershell-to-send-a-b2b-invitation"></a>Power shell gebruiken om een B2B-uitnodiging te verzenden

Gebruik de volgende opdracht om de gebruiker uit te nodigen voor B2B-samen werking:

```powershell
Uninstall-Module AzureADPreview
Install-Module AzureADPreview
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

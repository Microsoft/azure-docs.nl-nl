---
title: Fouten en uitzonderingen verwerken in MSAL.NET
titleSuffix: Microsoft identity platform
description: Meer informatie over het afhandelen van fouten en uitzonde ringen, claim problemen voor voorwaardelijke toegang en nieuwe pogingen in MSAL.NET.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 565acd745ba5d7fdec71f306d3851e599838f7d9
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584041"
---
# <a name="handle-errors-and-exceptions-in-msalnet"></a>Fouten en uitzonderingen verwerken in MSAL.NET

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msalnet"></a>Fout afhandeling in MSAL.NET

Bij het verwerken van .NET-uitzonde ringen kunt u het uitzonderings type zelf en het `ErrorCode` lid gebruiken om onderscheid te maken tussen uitzonde ringen. `ErrorCode` waarden zijn constanten van het type [MsalError](/dotnet/api/microsoft.identity.client.msalerror).

U kunt ook een kijkje geven in de velden [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception)en [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception).

Als [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) wordt gegenereerd, kunt u de [fout codes voor verificatie en autorisatie](reference-aadsts-error-codes.md) proberen om te zien of de code daar wordt vermeld.

### <a name="common-net-exceptions"></a>Algemene .NET-uitzonde ringen

Hier volgen enkele veelvoorkomende uitzonde ringen die mogelijk worden gegenereerd en enkele mogelijke oplossingen:  

| Uitzondering | Foutcode | Oplossing|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS65001: de gebruiker of beheerder heeft niet ingestemd met het gebruik van de toepassing met de ID {appId} met de naam {appName}. Verzend een interactieve autorisatie aanvraag voor deze gebruiker en resource.| Vraag eerst toestemming van de gebruiker. Als u geen .NET Core gebruikt (die geen Web-UI heeft), roept u (eenmaal) aan `AcquireTokeninteractive` . Als u .NET Core gebruikt of als u geen gebruik wilt maken van een `AcquireTokenInteractive` , kan de gebruiker naar een URL navigeren om toestemming te geven: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read` . om aan te roepen `AcquireTokenInteractive` : `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS50079: de gebruiker is verplicht [multi-factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md)te gebruiken.| Er is geen beperking. Als MFA is geconfigureerd voor uw Tenant en Azure Active Directory (AAD) besluiten om deze af te dwingen, gaat u terug naar een interactieve stroom, zoals `AcquireTokenInteractive` of `AcquireTokenByDeviceCode` .|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) |AADSTS90010: het toekennings type wordt niet ondersteund via de */veelvoorkomende* -of */consumers* -eind punten. Gebruik het */organizations* -of Tenant-specifieke eind punt. U hebt */veelvoorkomende* gebruikt.| Zoals uitgelegd in het bericht van Azure AD, moet de instantie een Tenant of anderszins */organizations* hebben.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) | AADSTS70002: de hoofd tekst van de aanvraag moet de volgende para meter bevatten: `client_secret or client_assertion` .| Deze uitzonde ring kan worden gegenereerd als uw toepassing niet is geregistreerd als een open bare client toepassing in azure AD. Bewerk in het Azure Portal het manifest voor uw toepassing en stel in `allowPublicClient` op `true` . |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)| `unknown_user Message`: Kan aangemelde gebruiker niet identificeren| De bibliotheek kan geen query uitvoeren op de huidige aangemelde gebruiker van Windows of deze gebruiker is niet opgenomen in AD of Azure AD. Beperking 1: Controleer op UWP of de toepassing de volgende mogelijkheden heeft: ondernemings verificatie, particuliere netwerken (client en server), informatie over gebruikers accounts. Risico beperking 2: Implementeer uw eigen logica voor het ophalen van de gebruikers naam (bijvoorbeeld john@contoso.com ) en gebruik het `AcquireTokenByIntegratedWindowsAuth` formulier dat de gebruikers naam gebruikt.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)|integrated_windows_auth_not_supported_managed_user| Deze methode is afhankelijk van een protocol dat wordt weer gegeven door Active Directory (AD). Als een gebruiker is gemaakt in azure AD zonder AD-back-up (' beheerde ' gebruiker), mislukt deze methode. Gebruikers die zijn gemaakt in AD en die worden ondersteund door Azure AD (' federatieve ' gebruikers), kunnen profiteren van deze niet-interactieve verificatie methode. Risico beperking: interactieve verificatie gebruiken.|

### `MsalUiRequiredException`

Een van de algemene status codes die door MSAL.NET worden geretourneerd bij het aanroepen `AcquireTokenSilent()` is `MsalError.InvalidGrantError` . Deze status code houdt in dat de toepassing de verificatie bibliotheek opnieuw moet aanroepen, maar in de interactieve modus (AcquireTokenInteractive of AcquireTokenByDeviceCodeFlow voor open bare client toepassingen) hebt u een uitdaging in web apps. Dit komt doordat er aanvullende gebruikers interactie is vereist voordat het verificatie token kan worden uitgegeven.

De meeste tijd wanneer `AcquireTokenSilent` dit mislukt, komt doordat de token cache geen tokens bevat die overeenkomen met uw aanvraag. Toegangs tokens verlopen in één uur en `AcquireTokenSilent` Er wordt geprobeerd een nieuwe op te halen op basis van een vernieuwings token (in OAuth2-voor waarden is dit de stroom voor het vernieuwen van het token). Deze stroom kan om verschillende redenen ook mislukken, bijvoorbeeld als een Tenant beheerder strengere aanmeldings beleidsregels configureert. 

De interactie is gericht op het uitvoeren van een actie door de gebruiker. Sommige van deze voor waarden zijn gemakkelijk te oplossen (bijvoorbeeld het accepteren van gebruiks voorwaarden met één klik), en sommige kunnen niet worden omgezet met de huidige configuratie (de computer in kwestie moet bijvoorbeeld verbinding maken met een specifiek bedrijfs netwerk). Sommige gebruikers helpen bij het instellen van multi-factor Authentication of het installeren van Microsoft Authenticator op het apparaat.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException` inventarisatie van classificatie

MSAL toont een `Classification` veld dat u kunt lezen om een betere gebruikers ervaring te bieden. Bijvoorbeeld om de gebruiker te laten weten dat het wacht woord is verlopen of dat ze toestemming nodig hebben om een aantal resources te gebruiken. De ondersteunde waarden maken deel uit van de `UiRequiredExceptionClassification` Enum:

| Classificatie    | Betekenis           | Aanbevolen verwerking |
|-------------------|-------------------|----------------------|
| BasicAction | De voor waarde kan worden omgezet door de interactie van de gebruiker tijdens de interactieve verificatie stroom. | Roep AcquireTokenInteractively () aan. |
| AdditionalAction | Voor waarde kan worden opgelost door extra Remedia-interactie met het systeem, buiten de interactieve verificatie stroom. | Roep AcquireTokenInteractively () aan om een bericht weer te geven waarin de herstel actie wordt uitgelegd. De aanroepende toepassing kan ervoor kiezen om stromen te verbergen waarvoor additional_action nodig is als de gebruiker de herstel actie niet waarschijnlijk kan volt ooien. |
| MessageOnly      | De voor waarde kan op dit moment niet worden opgelost. Bij het starten van een interactieve verificatie stroom wordt een bericht weer gegeven waarin de voor waarde wordt uitgelegd. | Roep AcquireTokenInteractively () aan om een bericht weer te geven waarin de voor waarde wordt uitgelegd. AcquireTokenInteractively () retourneert een UserCanceled-fout nadat de gebruiker het bericht heeft gelezen en het venster sluit. De aanroepende toepassing kan ervoor kiezen om stromen te verbergen die leiden tot message_only als de gebruiker waarschijnlijk niet in aanmerking komt voor het bericht.|
| ConsentRequired  | Toestemming van de gebruiker ontbreekt of is ingetrokken. | Roep AcquireTokenInteractively () aan om de gebruiker toestemming te geven. |
| UserPasswordExpired | Het wacht woord van de gebruiker is verlopen. | Roep AcquireTokenInteractively () aan zodat gebruikers hun wacht woord opnieuw kunnen instellen. |
| PromptNeverFailed| Interactieve verificatie is aangeroepen met de parameter prompt = Never, het afdwingen van MSAL om te vertrouwen op browser cookies en niet om de browser weer te geven. Dit is mislukt. | Roep AcquireTokenInteractively () aan zonder prompt. none |
| AcquireTokenSilentFailed | De SDK voor MSAL heeft niet genoeg informatie om een token uit de cache op te halen. Dit kan zijn omdat er geen tokens in de cache staan of omdat er geen account is gevonden. Het fout bericht bevat meer details.  | Roep AcquireTokenInteractively () aan. |
| Geen    | Er worden geen verdere details gegeven. De voor waarde kan worden opgelost door de gebruikers interactie tijdens de interactieve verificatie stroom. | Roep AcquireTokenInteractively () aan. |

## <a name="net-code-example"></a>Voor beeld van .NET-code

```csharp
AuthenticationResult res;
try
{
 res = await application.AcquireTokenSilent(scopes, account)
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex) when (ex.ErrorCode == MsalError.InvalidGrantError)
{
 switch (ex.Classification)
 {
  case UiRequiredExceptionClassification.None:
   break;
  case UiRequiredExceptionClassification.MessageOnly:
  // You might want to call AcquireTokenInteractive(). Azure AD will show a message
  // that explains the condition. AcquireTokenInteractively() will return UserCanceled error
  // after the user reads the message and closes the window. The calling application may choose
  // to hide features or data that result in message_only if the user is unlikely to benefit 
  // from the message
  try
  {
      res = await application.AcquireTokenInteractive(scopes).ExecuteAsync();
  }
  catch (MsalClientException ex2) when (ex2.ErrorCode == MsalError.AuthenticationCanceledError)
  {
   // Do nothing. The user has seen the message
  }
  break;

  case UiRequiredExceptionClassification.BasicAction:
  // Call AcquireTokenInteractive() so that the user can, for instance accept terms
  // and conditions

  case UiRequiredExceptionClassification.AdditionalAction:
  // You might want to call AcquireTokenInteractive() to show a message that explains the remedial action. 
  // The calling application may choose to hide flows that require additional_action if the user 
  // is unlikely to complete the remedial action (even if this means a degraded experience)

  case UiRequiredExceptionClassification.ConsentRequired:
  // Call AcquireTokenInteractive() for user to give consent.
  
  case UiRequiredExceptionClassification.UserPasswordExpired:
  // Call AcquireTokenInteractive() so that user can reset their password
  
  case UiRequiredExceptionClassification.PromptNeverFailed:
  // You used WithPrompt(Prompt.Never) and this failed
  
  case UiRequiredExceptionClassification.AcquireTokenSilentFailed:
  default:
  // May be resolved by user interaction during the interactive authentication flow.
  res = await application.AcquireTokenInteractive(scopes)
                         .ExecuteAsync(); break;
 }
}
```
[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

Bij het aanroepen van een API waarvoor voorwaardelijke toegang is vereist vanuit MSAL.NET, moet uw toepassing de uitzonde ringen voor claim controle afhandelen. Dit wordt weer gegeven als een [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) waarbij de eigenschap [claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims) niet leeg is.

Als u de claim Challenge wilt afhandelen, moet u de `.WithClaim()` methode van de- `PublicClientApplicationBuilder` klasse gebruiken.

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

### <a name="http-error-codes-500-600"></a>HTTP-fout codes 500-600

MSAL.NET implementeert een eenvoudig mechanisme voor opnieuw proberen voor fouten met HTTP-fout codes 500-600.

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) -Opper vlakken `System.Net.Http.Headers.HttpResponseHeaders` als een-eigenschap `namedHeaders` . U kunt aanvullende informatie uit de fout code gebruiken om de betrouw baarheid van uw toepassingen te verbeteren. In het geval dat wordt beschreven, kunt u het `RetryAfterproperty` (type `RetryConditionHeaderValue` ) gebruiken en berekenen wanneer u het opnieuw wilt proberen.

Hier volgt een voor beeld van een daemon-toepassing met behulp van de client referenties flow. U kunt dit aanpassen aan een van de methoden voor het verkrijgen van een token.

```csharp

bool retry = false;
do
{
    TimeSpan? delay;
    try
    {
         result = await publicClientApplication.AcquireTokenForClient(scopes, account).ExecuteAsync();
    }
    catch (MsalServiceException serviceException)
    {
         if (serviceException.ErrorCode == "temporarily_unavailable")
         {
             RetryConditionHeaderValue retryAfter = serviceException.Headers.RetryAfter;
             if (retryAfter.Delta.HasValue)
             {
                 delay = retryAfter.Delta;
             }
             else if (retryAfter.Date.HasValue)
             {
                 delay = retryAfter.Date.Value.Offset;
             }
         }
    }
    // . . .
    if (delay.HasValue)
    {
        Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
        retry = true;
    }
} while (retry);
```

## <a name="next-steps"></a>Volgende stappen

Overweeg [logboek registratie in MSAL.net in](msal-logging-dotnet.md) te scha kelen om problemen op te sporen en op te lossen.

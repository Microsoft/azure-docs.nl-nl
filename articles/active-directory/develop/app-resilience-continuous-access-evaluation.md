---
title: Api's voor continue toegang voor evaluatie gebruiken in uw toepassingen | Azure
titleSuffix: Microsoft identity platform
description: Het verg Roten van de app-beveiliging en-tolerantie door ondersteuning toe te voegen voor de evaluatie van continue toegang, waardoor er lange toegangs tokens kunnen worden ingetrokken op basis van kritieke gebeurtenissen en beleids evaluatie.
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2020
ms.author: nichola
ms.reviewer: ''
ms.openlocfilehash: f6ce792b3db0100d7356884bbc6ee2696580df10
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97652055"
---
# <a name="how-to-use-continuous-access-evaluation-enabled-apis-in-your-applications"></a>Api's voor continue toegang voor evaluatie gebruiken in uw toepassingen

[Voortdurende toegang](../conditional-access/concept-continuous-access-evaluation.md) (CAE) is een nieuwe industrie standaard waarmee toegangs tokens kunnen worden ingetrokken op basis van [kritieke gebeurtenissen](../conditional-access/concept-continuous-access-evaluation.md#critical-event-evaluation) en [beleids evaluatie](../conditional-access/concept-continuous-access-evaluation.md#conditional-access-policy-evaluation-preview) , in plaats van het verstrijken van tokens op basis van de levens duur. Voor sommige resource-Api's, omdat het risico en het beleid in realtime worden geëvalueerd, kan dit de levens duur van het token tot 28 uur verhogen. Deze tokens met een lange levens duur worden proactief vernieuwd door de micro soft Authentication Library (MSAL), waardoor de tolerantie van uw toepassingen wordt verhoogd.

In dit artikel wordt beschreven hoe u met CAE ingeschakelde Api's in uw toepassingen kunt gebruiken.

## <a name="implementation-considerations"></a>Afwegingen bij de implementatie

Als u de evaluatie van continue toegang wilt gebruiken, moet uw app en de resource-API waartoe toegang wordt verkregen, zijn ingeschakeld voor CAE. Door de code voor te bereiden voor het gebruik van een gecaete resource, is het echter niet mogelijk om Api's te gebruiken waarvoor geen CAE is ingeschakeld.

Als een resource-API de CAE implementeert en uw toepassing declareert, kan de CAE worden afgehandeld. uw app haalt CAE-tokens voor die bron op. Daarom moet uw toepassing, als u uw app-CAE wilt declareren, de CAE-claim Challenge afhandelen voor alle resource-Api's die micro soft Identity Access tokens accepteren. Als u geen CAE-antwoorden in deze API-aanroepen afhandelt, kan uw app in een lus een API-aanroep opnieuw proberen met een token dat nog steeds in de geretourneerde levens duur van het token staat, maar is ingetrokken door de CAE-functie.

## <a name="the-code"></a>De code

De eerste stap is het toevoegen van code voor het verwerken van een reactie van de resource-API die de aanroep afwijst als gevolg van CAE. Met CAE retourneert Api's een status van 401 en een WWW-Authenticate-header wanneer het toegangs token is ingetrokken of de API detecteert dat er een wijziging in het IP-adres wordt gebruikt. De WWW-Authenticate header bevat een claim Challenge die de toepassing kan gebruiken om een nieuw toegangs token te verkrijgen.

Bijvoorbeeld:

```console
HTTP 401; Unauthorized
WWW-Authenticate=Bearer
  authorization_uri="https://login.windows.net/common/oauth2/authorize",
  error="insufficient_claims",
  claims="eyJhY2Nlc3NfdG9rZW4iOnsibmJmIjp7ImVzc2VudGlhbCI6dHJ1ZSwgInZhbHVlIjoiMTYwNDEwNjY1MSJ9fX0="
```

Uw app controleert op:

- de API-aanroep retourneert de 401-status
- het bestaan van een WWW-Authenticate header met:
  - een "Error"-para meter met de waarde "insufficient_claims"
  - de para meter ' claims '

Als aan deze voor waarden wordt voldaan, kan de app de claim Challenge extra heren en decoderen.

```csharp
if (APIresponse.IsSuccessStatusCode)
{
    // ...
}
else
{
    if (APIresponse.StatusCode == System.Net.HttpStatusCode.Unauthorized
        && APIresponse.Headers.WwwAuthenticate.Any())
    {
        AuthenticationHeaderValue bearer = APIresponse.Headers.WwwAuthenticate.First
            (v => v.Scheme == "Bearer");
        IEnumerable<string> parameters = bearer.Parameter.Split(',').Select(v => v.Trim()).ToList();
        var error = GetParameter(parameters, "error");

        if (null != error && "insufficient_claims" == error)
        {
            var claimChallengeParameter = GetParameter(parameters, "claims");
            if (null != claimChallengeParameter)
            {
                var claimChallengebase64Bytes = System.Convert.FromBase64String(claimChallengeParameter);
                var claimChallenge = System.Text.Encoding.UTF8.GetString(claimChallengebase64Bytes);
                var newAccessToken = await GetAccessTokenWithClaimChallenge(scopes, claimChallenge);
```

Uw app gebruikt vervolgens de claim Challenge om een nieuw toegangs token voor de resource te verkrijgen.

```csharp
try
{
    authResult = await _clientApp.AcquireTokenSilent(scopes, firstAccount)
        .WithClaims(claimChallenge)
        .ExecuteAsync()
        .ConfigureAwait(false);
}
catch (MsalUiRequiredException)
{
    try
    {
        authResult = await _clientApp.AcquireTokenInteractive(scopes)
            .WithClaims(claimChallenge)
            .WithAccount(firstAccount)
            .ExecuteAsync()
            .ConfigureAwait(false);
    }
    // ...
```

Zodra uw toepassing gereed is om de claim controle af te handelen die door een gestarte resource wordt geretourneerd, kunt u de micro soft-identiteit die uw app is ingesteld op CAE. Als u dit wilt doen in uw MSAL-toepassing, bouwt u uw open bare client met behulp van de client mogelijkheden van ' CP1 '.

```csharp
_clientApp = PublicClientApplicationBuilder.Create(App.ClientId)
    .WithDefaultRedirectUri()
    .WithAuthority(authority)
    .WithClientCapabilities(new [] {"cp1"})
    .Build();
```

U kunt uw toepassing testen door u aan te melden bij een gebruiker voor de toepassing. vervolgens gebruikt u de Azure Portal om de sessies van de gebruiker in te trekken. De volgende keer dat de app de CAE ingeschakelde API aanroept, wordt de gebruiker gevraagd om opnieuw te worden geverifieerd.

## <a name="next-steps"></a>Volgende stappen

Zie [evaluatie van continue toegang](../conditional-access/concept-continuous-access-evaluation.md)voor meer informatie.

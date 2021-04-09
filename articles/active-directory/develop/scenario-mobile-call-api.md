---
title: Een web-API aanroepen vanuit een mobiele app | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een mobiele app die web-Api's aanroept. (Een web-API aanroepen.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 656cb167fb47e44c28922afed75d8c46a460aaf1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99584394"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>Een web-API aanroepen vanuit een mobiele app

Nadat uw app een gebruiker heeft ondertekend en tokens ontvangt, wordt in de micro soft Authentication Library (MSAL) informatie weer gegeven over de gebruiker, de omgeving van de gebruiker en de uitgegeven tokens. Uw app kan deze waarden gebruiken om een web-API aan te roepen of een welkomst bericht voor de gebruiker weer te geven.

In dit artikel kijken we eerst naar het MSAL-resultaat. Vervolgens gaan we kijken hoe u een toegangs token van gebruikt `AuthenticationResult` of `result` een beveiligde web-API aanroept.

## <a name="msal-result"></a>MSAL resultaat
MSAL biedt de volgende waarden: 

- `AccessToken` Hiermee worden beveiligde web-Api's aangeroepen in een HTTP Bearer-aanvraag.
- `IdToken` bevat nuttige informatie over de aangemelde gebruiker. Deze informatie omvat de naam van de gebruiker, de thuis Tenant en een unieke id voor opslag.
- `ExpiresOn` is de verloop tijd van het token. MSAL verwerkt de automatische vernieuwing van een app.
- `TenantId` is de id van de Tenant waarbij de gebruiker zich heeft aangemeld. Voor gast gebruikers in Azure Active Directory (Azure AD) B2B, wordt met deze waarde de Tenant geïdentificeerd waar de gebruiker zich heeft aangemeld. Met de waarde wordt niet de thuis Tenant van de gebruiker geïdentificeerd.  
- `Scopes` Hiermee worden de bereiken aangegeven die met uw token zijn verleend. De toegekende bereiken kunnen een subset zijn van de bereiken die u hebt aangevraagd.

MSAL biedt ook een abstractie voor een `Account` waarde. Een `Account` waarde vertegenwoordigt het aangemelde account van de huidige gebruiker:

- `HomeAccountIdentifier` identificeert de thuis Tenant van de gebruiker.
- `UserName` de voorkeurs gebruikersnaam van de gebruiker. Deze waarde kan leeg zijn voor gebruikers van Azure AD B2C.
- `AccountIdentifier` Hiermee wordt de aangemelde gebruiker geïdentificeerd. In de meeste gevallen is deze waarde hetzelfde als de `HomeAccountIdentifier` waarde tenzij de gebruiker een gast in een andere Tenant is.

## <a name="call-an-api"></a>Een API aanroepen

Nadat u het toegangs token hebt, kunt u een web-API aanroepen. In uw app wordt het token gebruikt voor het bouwen van een HTTP-aanvraag en het uitvoeren van de aanvraag.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing.
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called Graph. Process data and send to UI.
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error.
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put access token in HTTP request.
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="msal-for-ios-and-macos"></a>MSAL voor iOS en macOS

De methoden voor het verkrijgen van tokens retour neren een `MSALResult` object. `MSALResult` beschrijft een `accessToken` eigenschap. U kunt gebruiken `accessToken` om een web-API aan te roepen. Voeg deze eigenschap toe aan de HTTP-autorisatie-header voordat u aanroept om toegang te krijgen tot de beveiligde web-API.

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };
        
NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

### <a name="xamarin"></a>Xamarin

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

## <a name="make-several-api-requests"></a>Meerdere API-aanvragen doen

Als u dezelfde API meerdere keren aanroept of meerdere Api's aanroept, moet u rekening houden met de volgende onderwerpen wanneer u uw app bouwt:

- **Incrementele toestemming**: met het micro soft Identity-platform kunnen Apps gebruikers toestemming krijgen wanneer er machtigingen zijn vereist in plaats van aan het begin. Telkens wanneer uw app klaar is om een API aan te roepen, moet deze alleen de bereiken aanvragen die het nodig heeft.

- **Voorwaardelijke toegang**: wanneer u meerdere API-aanvragen maakt, moet u mogelijk voldoen aan aanvullende vereisten voor voorwaardelijke toegang. De vereisten kunnen op deze manier toenemen als de eerste aanvraag geen beleids regels voor voorwaardelijke toegang heeft en uw app probeert op de achtergrond toegang te krijgen tot een nieuwe API waarvoor voorwaardelijke toegang is vereist. Als u dit probleem wilt verhelpen, moet u ervoor zorgen dat u fouten opvangt van Silent-aanvragen en een interactieve aanvraag maakt.  Zie [richt lijnen voor voorwaardelijke toegang](../azuread-dev/conditional-access-dev-guide.md)voor meer informatie.

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>Meerdere Api's aanroepen met behulp van incrementele toestemming en voorwaardelijke toegang

Als u meerdere Api's voor dezelfde gebruiker aanroept, kunt u, nadat u een token voor de gebruiker hebt verkregen, voor komen dat de gebruiker herhaaldelijk om referenties wordt gevraagd door u vervolgens `AcquireTokenSilent` aan te roepen om een token op te halen:

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Interactie is vereist wanneer:

- De gebruiker heeft toestemming gegeven voor de eerste API, maar nu moet worden gestemd op meer bereiken. In dit geval gebruikt u incrementele toestemming.
- De eerste API vereist geen [multi-factor Authentication](../authentication/concept-mfa-howitworks.md), maar de volgende API doet dat wel.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel in dit scenario en [Ga naar productie](scenario-mobile-production.md).

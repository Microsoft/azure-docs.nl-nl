---
title: Een Token ophalen voor een web-API die web-Api's aanroept | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een web-API voor het aanroepen van web-Api's die een token moeten verkrijgen voor de app.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 9f9758ec765ad34e5ef5d8b4d4e0a420a6701b6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98756395"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Een web-API die web-Api's aanroept: een Token ophalen voor de app

Nadat u een client toepassings object hebt gemaakt, gebruikt u dit om een token op te halen dat u kunt gebruiken om een web-API aan te roepen.

## <a name="code-in-the-controller"></a>Code in de controller

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

*Micro soft. Identity. Web* voegt extensie methoden toe die de mogelijkheid bieden om Microsoft Graph of een stroomafwaartse Web-API aan te roepen. Deze methoden worden gedetailleerd beschreven in [een web-API die web-api's aanroept: een API aanroepen](scenario-web-api-call-api-call-api.md). Met deze helper-methoden hoeft u geen token hand matig te verkrijgen.

Als u echter hand matig een token moet verkrijgen, ziet u in de volgende code een voor beeld van het gebruik van *micro soft. Identity. Web* om dit te doen in een API-controller. Er wordt een stroomafwaartse API met de naam *ToDoList* aangeroepen.
Als u een token wilt ophalen voor het aanroepen van de stroomafwaartse API, kunt u de service injecteren `ITokenAcquisition` door de afhankelijkheids injectie in de constructor van uw controller (of uw pagina-constructor als u een razendsnelle functie gebruikt) en u deze gebruiken in de controller acties, waarbij een token wordt opgehaald voor de gebruiker ( `GetAccessTokenForUserAsync` ) of voor de toepassing zelf ( `GetAccessTokenForAppAsync` ) in het geval van een daemon-scenario

```csharp
[Authorize]
public class MyApiController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

     static readonly string[] scopesToAccessDownstreamApi = new string[] { "api://MyTodolistService/access_as_user" };

    private readonly ITokenAcquisition _tokenAcquisition;

    public MyApiController(ITokenAcquisition tokenAcquisition)
    {
        _tokenAcquisition = tokenAcquisition;
    }

    public IActionResult Index()
    {
        HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

        string accessToken = _tokenAcquisition.GetAccessTokenForUserAsync(scopesToAccessDownstreamApi);
        return await callTodoListService(accessToken);
    }
}
```

Zie voor meer informatie over de `callTodoListService` -methode  [een web-API die web-api's aanroept: roep een API](scenario-web-api-call-api-call-api.md)aan.

### <a name="java"></a>[Java](#tab/java)

Hier volgt een voor beeld van code die wordt aangeroepen in de acties van de API-controllers. Het roept de downstream API-Microsoft Graph aan.

```java
@RestController
public class ApiController {

    @Autowired
    MsalAuthHelper msalAuthHelper;

    @RequestMapping("/graphMeApi")
    public String graphMeApi() throws MalformedURLException {

        String oboAccessToken = msalAuthHelper.getOboToken("https://graph.microsoft.com/.default");

        return callMicrosoftGraphMeEndpoint(oboAccessToken);
    }

}
```

### <a name="python"></a>[Python](#tab/python)
 
Een python-Web-API vereist het gebruik van middleware om het Bearer-token te valideren dat van de client is ontvangen. De Web-API kan vervolgens het toegangs token voor een downstream API verkrijgen met behulp van de MSAL python-bibliotheek door de methode aan te roepen [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) .
 
Hier volgt een voor beeld van code waarmee een toegangs token wordt verkregen met behulp van de `acquire_token_on_behalf_of` methode en het kolven-raam werk. Hiermee wordt de downstream API aangeroepen-het eind punt van Azure Management-abonnementen.
 
```python
def get(self):
 
        _scopes = ["https://management.azure.com/user_impersonation"]
        _azure_management_subscriptions_uri = "https://management.azure.com/subscriptions?api-version=2020-01-01"
 
        current_access_token = request.headers.get("Authorization", None)
        
        #This example only uses the default memory token cache and should not be used for production
        msal_client = msal.ConfidentialClientApplication(
                client_id=os.environ.get("CLIENT_ID"),
                authority=os.environ.get("AUTHORITY"),
                client_credential=os.environ.get("CLIENT_SECRET"))
 
        #acquire token on behalf of the user that called this API
        arm_resource_access_token = msal_client.acquire_token_on_behalf_of(
            user_assertion=current_access_token.split(' ')[1],
            scopes=_scopes
        )
 
        headers = {'Authorization': arm_resource_access_token['token_type'] + ' ' + arm_resource_access_token['access_token']}
 
        subscriptions_list = req.get(_azure_management_subscriptions_uri), headers=headers).json()
 
        return jsonify(subscriptions_list)
```

## <a name="advanced-accessing-the-signed-in-users-token-cache-from-background-apps-apis-and-services"></a>Gevanceerde De token cache van de aangemelde gebruiker openen vanaf de achtergrond-apps, Api's en services

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

---

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel in dit scenario en [roep een API](scenario-web-api-call-api-call-api.md)aan.

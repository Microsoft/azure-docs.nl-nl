---
title: Web-API voor het aanroepen van web-Api's | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een web-API voor het aanroepen van web-Api's.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: bbb42a4955ff0b4fbbac58830ec5c8aecf04915d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101686730"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Een web-API die web-Api's aanroept: een API aanroepen

Nadat u een token hebt, kunt u een beveiligde web-API aanroepen. Gewoonlijk roept u de downstream-Api's aan vanaf de controller of pagina's van uw web-API.

## <a name="controller-code"></a>Controller code

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Wanneer u *micro soft. Identity. Web* gebruikt, hebt u drie gebruiks scenario's:

- [Een web-API die web-Api's aanroept: een API aanroepen](#a-web-api-that-calls-web-apis-call-an-api)
  - [Controller code](#controller-code)
- [ASP.NET Core](#aspnet-core)
      - [Optie 1: Microsoft Graph aanroepen met de SDK](#option-1-call-microsoft-graph-with-the-sdk)
      - [Optie 2: een stroomafwaartse Web-API aanroepen met de helper-klasse](#option-2-call-a-downstream-web-api-with-the-helper-class)
      - [Optie 3: een stroomafwaartse Web-API aanroepen zonder de helper-klasse](#option-3-call-a-downstream-web-api-without-the-helper-class)
- [Java](#java)
- [Python](#python)
  - [Volgende stappen](#next-steps)

#### <a name="option-1-call-microsoft-graph-with-the-sdk"></a>Optie 1: Microsoft Graph aanroepen met de SDK

In dit scenario hebt u Startup.cs toegevoegd `.AddMicrosoftGraph()` zoals  opgegeven in [code configuratie](scenario-web-api-call-api-app-configuration.md#option-1-call-microsoft-graph), en kunt u het in de-controller of de pagina-constructor rechtstreeks injecteren `GraphServiceClient` voor gebruik in de acties. In het volgende voor beeld wordt een pagina weer gegeven met de foto van de aangemelde gebruiker.

```CSharp
 [Authorize]
 [AuthorizeForScopes(Scopes = new[] { "user.read" })]
 public class IndexModel : PageModel
 {
     private readonly GraphServiceClient _graphServiceClient;

     public IndexModel(GraphServiceClient graphServiceClient)
     {
         _graphServiceClient = graphServiceClient;
     }

     public async Task OnGet()
     {
         var user = await _graphServiceClient.Me.Request().GetAsync();
         try
         {
             using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
             {
                 byte[] photoByte = ((MemoryStream)photoStream).ToArray();
                 ViewData["photo"] = Convert.ToBase64String(photoByte);
             }
             ViewData["name"] = user.DisplayName;
         }
         catch (Exception)
         {
             ViewData["photo"] = null;
         }
     }
 }
```

#### <a name="option-2-call-a-downstream-web-api-with-the-helper-class"></a>Optie 2: een stroomafwaartse Web-API aanroepen met de helper-klasse

In dit scenario hebt u Startup.cs toegevoegd `.AddDownstreamWebApi()` zoals  opgegeven in [code configuratie](scenario-web-api-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph), en kunt u rechtstreeks een `IDownstreamWebApi` service in uw controller of pagina-constructor injecteren en gebruiken in de volgende acties:

```CSharp
 [Authorize]
 [AuthorizeForScopes(ScopeKeySection = "TodoList:Scopes")]
 public class TodoListController : Controller
 {
     private IDownstreamWebApi _downstreamWebApi;
     private const string ServiceName = "TodoList";

     public TodoListController(IDownstreamWebApi downstreamWebApi)
     {
         _downstreamWebApi = downstreamWebApi;
     }

     public async Task<ActionResult> Details(int id)
     {
         var value = await _downstreamWebApi.CallWebApiForUserAsync(
             ServiceName,
             options =>
             {
                 options.RelativePath = $"me";
             });
         return View(value);
     }
```

De- `CallWebApiForUserAsync` methode heeft ook sterk getypeerde generieke onderdrukkingen waarmee u direct een object kunt ontvangen. De volgende methode heeft bijvoorbeeld een exemplaar ontvangen `Todo` , een sterk getypeerde weer gave van de JSON die wordt geretourneerd door de Web-API.

```CSharp
 // GET: TodoList/Details/5
 public async Task<ActionResult> Details(int id)
 {
     var value = await _downstreamWebApi.CallWebApiForUserAsync<object, Todo>(
         ServiceName,
         null,
         options =>
         {
             options.HttpMethod = HttpMethod.Get;
             options.RelativePath = $"api/todolist/{id}";
         });
     return View(value);
 }
```

#### <a name="option-3-call-a-downstream-web-api-without-the-helper-class"></a>Optie 3: een stroomafwaartse Web-API aanroepen zonder de helper-klasse

Als u hebt besloten om een token hand matig te verkrijgen met behulp van de `ITokenAcquisition` -service, moet u het token nu gebruiken. In dat geval wordt de voorbeeld code die wordt weer gegeven in [een web-API die web-api's aanroept, voortgezet: Schaf een token voor de app aan](scenario-web-api-call-api-acquire-token.md). De code wordt aangeroepen in de acties van de API-controllers. Er wordt een stroomafwaartse API met de naam *ToDoList* aangeroepen.

 Nadat u het token hebt aangeschaft, kunt u dit als Bearer-token gebruiken om de downstream API aan te roepen.

```csharp
 private async Task CallTodoListService(string accessToken)
 {
  // After the token has been returned by Microsoft.Identity.Web, add it to the HTTP authorization header before making the call to access the todolist service.
 _httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

 // Call the todolist service.
 HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
 // ...
 }
 ```

# <a name="java"></a>[Java](#tab/java)

Met de volgende code wordt de voorbeeld code die wordt weer gegeven in [een web-API die web-api's aanroept, voortgezet: Schaf een token voor de app aan](scenario-web-api-call-api-acquire-token.md). De code wordt aangeroepen in de acties van de API-controllers. Het roept de downstream API MS Graph aan.

Nadat u het token hebt aangeschaft, kunt u dit als Bearer-token gebruiken om de downstream API aan te roepen.

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="python"></a>[Python](#tab/python)
Een voor beeld van het demonstreren van deze stroom met MSAL python is beschikbaar op [MS-Identity-python-](https://github.com/Azure-Samples/ms-identity-python-on-behalf-of)namens.

---

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel in dit scenario en [Ga naar productie](scenario-web-api-call-api-production.md).

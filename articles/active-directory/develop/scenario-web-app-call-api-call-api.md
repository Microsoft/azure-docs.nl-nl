---
title: Een web-API aanroepen vanuit een web-app | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een web-app die web-Api's aanroept (waarmee een beveiligde web-API wordt aangeroepen)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: b294a56a523adaa2629a5d1e72a7ccef532956e0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98753290"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>Een web-app die web-Api's aanroept: een web-API aanroepen

Nu u een token hebt, kunt u een beveiligde web-API aanroepen. Gewoonlijk roept u een downstream API aan vanaf de controller of pagina's van uw web-app.

## <a name="call-a-protected-web-api"></a>Een beveiligde web-API aanroepen

Het aanroepen van een beveiligde web-API is afhankelijk van uw taal en het gewenste Framework:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Wanneer u *micro soft. Identity. Web* gebruikt, hebt u drie gebruiks opties voor het aanroepen van een API:

- [Optie 1: Microsoft Graph aanroepen met de Microsoft Graph SDK](#option-1-call-microsoft-graph-with-the-sdk)
- [Optie 2: een stroomafwaartse Web-API aanroepen met de helper-klasse](#option-2-call-a-downstream-web-api-with-the-helper-class)
- [Optie 3: een stroomafwaartse Web-API aanroepen zonder de helper-klasse](#option-3-call-a-downstream-web-api-without-the-helper-class)

#### <a name="option-1-call-microsoft-graph-with-the-sdk"></a>Optie 1: Microsoft Graph aanroepen met de SDK

U wilt Microsoft Graph aanroepen. In dit scenario hebt u toegevoegd aan `AddMicrosoftGraph` het onderdeel *Startup. cs* zoals opgegeven in [code configuratie](scenario-web-app-call-api-app-configuration.md#option-1-call-microsoft-graph), en kunt u het in de-controller of de pagina-constructor rechtstreeks injecteren `GraphServiceClient` voor gebruik in de acties. In het volgende voor beeld wordt een pagina weer gegeven met de foto van de aangemelde gebruiker.

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

U wilt een andere web-API aanroepen dan Microsoft Graph. In dat geval hebt u het bestand `AddDownstreamWebApi` *Startup. cs* toegevoegd zoals opgegeven in [code configuratie](scenario-web-app-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph), en kunt u rechtstreeks een `IDownstreamWebApi` service in uw controller of pagina-constructor injecteren en gebruiken in de volgende acties:

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
}
```

Het `CallWebApiForUserAsync` heeft ook sterk getypeerde generieke onderdrukkingen waarmee u rechtstreeks een object kunt ontvangen. De volgende methode ontvangt bijvoorbeeld een `Todo` instantie, een sterk getypeerde weer gave van de JSON die wordt geretourneerd door de Web-API.

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

U hebt besloten om een token hand matig te verkrijgen met de `ITokenAcquisition` service, en u moet het token nu gebruiken. In dat geval wordt de voorbeeld code die wordt weer gegeven in [een web-app die web-api's aanroept, voortgezet: Schaf een token voor de app aan](scenario-web-app-call-api-acquire-token.md). De code wordt aangeroepen in de acties van de web-app-controllers.

Nadat u het token hebt aangeschaft, kunt u dit als Bearer-token gebruiken om de downstream API aan te roepen, in dit geval Microsoft Graph.

 ```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

  var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

  if (response.StatusCode == HttpStatusCode.OK)
  {
   var content = await response.Content.ReadAsStringAsync();

   dynamic me = JsonConvert.DeserializeObject(content);
   ViewData["Me"] = me;
  }

  return View();
}
```
> [!NOTE]
> U kunt dezelfde methode gebruiken om een web-API aan te roepen.
>
> De meeste Azure-Web-Api's bieden een SDK die het aanroepen van de API vereenvoudigt als het geval is voor Microsoft Graph. Zie bijvoorbeeld [een webtoepassing voor het maken van toegang tot Blob Storage met Azure AD](../../storage/common/storage-auth-aad-app.md?tabs=dotnet&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) voor een voor beeld van een web-app met behulp van micro soft. Identity. Web en met behulp van de Azure Storage SDK.

# <a name="java"></a>[Java](#tab/java)

```Java
private String getUserInfoFromGraph(String accessToken) throws Exception {
    // Microsoft Graph user endpoint
    URL url = new URL("https://graph.microsoft.com/v1.0/me");

    HttpURLConnection conn = (HttpURLConnection) url.openConnection();

    // Set the appropriate header fields in the request header.
    conn.setRequestProperty("Authorization", "Bearer " + accessToken);
    conn.setRequestProperty("Accept", "application/json");

    String response = HttpClientHelper.getResponseStringFromConn(conn);

    int responseCode = conn.getResponseCode();
    if(responseCode != HttpURLConnection.HTTP_OK) {
        throw new IOException(response);
    }

    JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
    return responseObject.toString();
}

```

# <a name="python"></a>[Python](#tab/python)

```Python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

---

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel in dit scenario en [Ga naar productie](scenario-web-app-call-api-production.md).
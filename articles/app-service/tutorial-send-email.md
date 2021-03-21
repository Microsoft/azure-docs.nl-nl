---
title: 'Zelfstudie: e-mail verzenden met Logic Apps'
description: Leer hoe u bedrijfsprocessen kunt aanroepen vanuit uw App Service-app. Verzend e-mailberichten, tweets en Facebook-berichten, voeg toe aan verzendlijsten en nog veel meer.
ms.topic: tutorial
ms.date: 04/08/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 8072a941cd89290af3e25cc63c4fccccce705df9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95014659"
---
# <a name="tutorial-send-email-and-invoke-other-business-processes-from-app-service"></a>Zelfstudie: E-mail verzenden en andere bedrijfsprocessen aanroepen vanuit App Service

In deze zelfstudie leert u hoe u uw App Service-app kunt integreren met uw bedrijfsprocessen. Dit is gebruikelijk voor web-app-scenario's, zoals:

- Bevestigings-e-mail voor een transactie verzenden
- Gebruiker toevoegen aan Facebook-groep
- Verbinding maken met systemen van derden als SAP, Salesforce, enzovoort.
- Standaard-B2B-berichten uitwisselen

In deze zelfstudie verzendt u e-mail berichten met Gmail vanuit uw App Service-app met behulp van [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Er zijn andere manieren om e-mail berichten te verzenden vanuit een web-app, zoals de SMTP-configuratie van uw taalframework. Logic Apps biedt uw App Service-app echter veel meer kracht, zonder complexiteit toe te voegen aan uw code. Logic Apps biedt een eenvoudige configuratie-interface voor de populairste bedrijfsintegraties, en uw app kan deze op elk gewenst moment aanroepen met een HTTP-aanvraag.

## <a name="prerequisite"></a>Vereiste

Implementeer een app naar App Service met het taalframework van uw keuze. Zie hieronder om een zelfstudie te volgen voor het implementeren van een voorbeeld-app:

### <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

[Zelfstudie: Een ASP.NET-app in Azure bouwen met behulp van SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

[Zelfstudie: Een ASP.NET Core- en SQL Database-app maken in Azure App Service](tutorial-dotnetcore-sqldb-app.md)

### <a name="nodejs"></a>[Node.js](#tab/node)

[Zelfstudie: Een Node.js- en MongoDB-app bouwen in Azure](tutorial-nodejs-mongodb-app.md)

### <a name="php"></a>[PHP](#tab/php)

[Zelfstudie: Een PHP- en MySQL-app bouwen in Azure](tutorial-php-mysql-app.md)

### <a name="python"></a>[Python](#tab/python)

[Zelfstudie: Een Python-web-app (Django) uitvoeren met PostgreSQL in Azure App Service](tutorial-python-postgresql-app.md)

### <a name="ruby"></a>[Ruby](#tab/ruby)

[Een Ruby- en Postgres-app maken in Azure App Service op Linux](tutorial-ruby-postgres-app.md)

---

## <a name="create-the-logic-app"></a>De logische app maken

1. Maak in de [Azure-portal](https://portal.azure.com)een lege logische app door de instructies in [Uw logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app) te volgen. Kom terug naar deze zelfstudie wanneer u de **Logic Apps Designer** ziet.
1. Selecteer op de welkomstpagina voor Logic Apps Designer **Wanneer een HTTP-aanvraag wordt ontvangen** onder **Beginnen met een algemene trigger**.

    ![Schermopname van de welkomstpagina voor de Logic Apps Designer met Wanneer een H T T P-aanvraag wordt ontvangen.](./media/tutorial-send-email/receive-http-request.png)
1. Selecteer in het dialoogvenster **Wanneer een HTTP-aanvraag wordt ontvangen** de optie **Voorbeeldpayload gebruiken om schema te genereren**.

    ![Schermopname van het dialoogvenster wanneer een H T T P-aanvraag wordt ontvangen waarin de optie Voorbeeld-payload gebruiken om schema te genereren is geselecteerd. ](./media/tutorial-send-email/generate-schema-with-payload.png)

1. Kopieer de volgende voorbeeld-JSON naar het tekstvak en selecteer **Gereed**.

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

    Het schema wordt nu gegenereerd voor de gewenste aanvraaggegevens. In de praktijk kunt u gewoon de werkelijke aanvraaggegevens opnemen die uw toepassingscode genereert, en Azure het JSON-schema voor u laten genereren. 
1. Kies bovenin de Logic Apps Designer **Opslaan**. 

    U kunt nu de URL van de HTTP-aanvraagtrigger zien. Selecteer het kopieerpictogram om deze te kopiëren voor later gebruik.

    ![Schermopname met daarin gemarkeerd het kopieerpictogram voor het kopiëren van de U R L van uw H T T P-aanvraagtrigger.](./media/tutorial-send-email/http-request-url.png)

    Deze HTTP-aanvraagdefinitie is een trigger voor alles wat u wilt doen in deze logische app, of het nu Gmail is of iets anders. Later roept u deze URL aan in uw App Service-app. Zie voor meer informatie over de aanvraagtrigger de [HTTP-aanvraag/antwoord-referentie](../connectors/connectors-native-reqres.md).

1. Klik onderaan de Designer op **Nieuwe stap**, typ **Gmail** in het zoekvak voor acties en selecteer **E-mail verzenden (v2)** .
    
    > [!TIP]
    > U kunt zoeken naar andere typen integraties, zoals SendGrid, MailChimp, Microsoft 365 en Sales Force. Zie de [Logic Apps-documentatie](../logic-apps/index.yml) voor meer informatie.

1. Selecteer **Aanmelden** in het dialoogvenster **Gmail** en meld u aan bij het Gmail-account van waaruit u het e-mailbericht wilt verzenden.

    ![Schermopname van het dialoogvenster Gmail dat u gebruikt om u aan te melden bij het Gmail-account van waaruit u e-mail wilt verzenden.](./media/tutorial-send-email/gmail-sign-in.png)

1. Wanneer u bent aangemeld, klikt u in het tekstvak **Aan** en wordt automatisch het dialoogvenster voor dynamische inhoud geopend.

1. Selecteer **Meer weergeven** naast **Wanneer een HTTP-aanvraag wordt ontvangen**.

    ![Schermopname die de knop Meer weergeven laat zien naast de actie Wanneer een H T T P-aanvraag wordt ontvangen.](./media/tutorial-send-email/expand-dynamic-content.png)

    U ziet nu de drie eigenschappen van uw voorbeeld-JSON-gegevens die u eerder hebt gebruikt. In deze stap gebruikt u deze eigenschappen van de HTTP-aanvraag om een e-mail bericht samen te stellen.
1. Omdat u de waarde voor het veld **Aan** selecteert, kiest u **e-mail**. Als u wilt, kunt u het dialoogvenster voor dynamische inhoud uitschakelen door op **Dynamische inhoud toevoegen** te klikken.

    ![Schermopname waarin de e-mailoptie en de optie Dynamische inhoud toevoegen zijn gemarkeerd.](./media/tutorial-send-email/hide-dynamic-content.png)

1. Selecteer **Onderwerp** en **Hoofdtekst** in de vervolgkeuzelijst **Nieuwe parameter toevoegen**.

1. Klik in het tekstvak **Onderwerp** en kies op dezelfde manier **taak**. Typ terwijl de cursor nog in het vak **Onderwerp** staat *gemaakt*. 

1. Klik in de **Hoofdtekst** en kies op dezelfde manier **einddatum**. Verplaats de cursor naar links van **einddatum** en typ *Dit werkitem moet gereed zijn op*.

    > [!TIP]
    > Als u HTML-inhoud rechtstreeks in de hoofdtekst van de e-mail wilt bewerken, selecteert u **Codeweergave** bovenin het venster Logic Apps Designer. Zorg er wel voor dat u de code voor de dynamische inhoud behoudt (bijvoorbeeld `@{triggerBody()?['due']}`)
    >
    > ![Schermopname van de codeweergave voor het weergeven van H T M L-inhoud rechtstreeks in de hoofdtekst van de e-mail.](./media/tutorial-send-email/edit-rich-html-email.png) 

1. Voeg vervolgens een asynchroon HTTP-antwoord toe aan de HTTP-trigger. Tussen de HTTP-trigger en de Gmail-actie klikt u op het **+** -teken en selecteert u **Een parallelle vertakking toevoegen**.

    ![Schermopname waarin het plusteken en de optie Een parallelle vertakking toevoegen zijn gemarkeerd.](./media/tutorial-send-email/add-http-response.png)

1. Zoek in het zoekvak naar **antwoord** en selecteer vervolgens de **Antwoord**-actie.

    ![Schermopname waarin de zoekbalk en de actie Antwoord zijn gemarkeerd.](./media/tutorial-send-email/choose-response-action.png)

    Standaard verzendt de antwoordactie een HTTP 200. Dat is voldoende voor deze zelfstudie. Zie voor meer informatie de [HTTP-aanvraag/antwoord-referentie](../connectors/connectors-native-reqres.md).

1. Kies nogmaals **Opslaan** bovenin de Logic Apps Designer. 

## <a name="add-http-request-code-to-app"></a>HTTP-aanvraagcode toevoegen aan de app

Zorg ervoor dat u de URL van de HTTP-aanvraagtrigger van eerder hebt gekopieerd. Omdat deze gevoelige informatie bevat, is de best practice deze niet rechtstreeks in de code te plaatsen. Met App Service kunt u er in plaats daarvan naar verwijzen als een omgevingsvariabele, met behulp van app-instellingen. 

Maak in de [Cloud Shell](https://shell.azure.com) de app-instelling met de volgende opdracht (vervang *\<app-name>* , *\<resource-group-name>* en *\<logic-app-url>* ):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings LOGIC_APP_URL="<your-logic-app-url>"
```

Maak in uw code een standaard HTTP-post naar de URL met behulp van een HTTP-clienttaal die beschikbaar is voor uw taalframework, met de volgende configuratie:

- De aanvraagtekst bevat dezelfde JSON-indeling die u hebt opgegeven voor uw logische app:

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

- De aanvraag bevat de kop `Content-Type: application/json`. 
- Verzend de aanvraag indien mogelijk asynchroon, om de prestaties te optimaliseren.

Klik op het tabblad van uw favoriete taal/framework hieronder om een voorbeeld te zien.

### <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

In ASP.NET kunt u het HTTP-bericht verzenden met de klasse [System.Net.Http.HttpClient](/dotnet/api/system.net.http.httpclient). Bijvoorbeeld:

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // requires using System.Configuration;
    ConfigurationManager.AppSettings["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

Als u deze code test op de voorbeeld-app voor [Zelfstudie: Een ASP.NET-app bouwen in Azure met SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md), kunt u deze gebruiken om een e-mailbevestiging te verzenden in de [actie Maken](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/blob/master/DotNetAppSqlDb/Controllers/TodosController.cs#L52-L63), nadat het `Todo`-item is toegevoegd. Als u de asynchrone code hierboven wilt gebruiken, moet u de actie Maken asynchroon maken.

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

In ASP.NET Core kunt u het HTTP-bericht verzenden met de klasse [System.Net.Http.HttpClient](/dotnet/api/system.net.http.httpclient). Bijvoorbeeld:

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // Requires DI configuration to access app settings. See https://docs.microsoft.com/azure/app-service/configure-language-dotnetcore#access-environment-variables
    _configuration["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

> [!NOTE]
> Deze code is geschreven voor een eenvoudige demonstratie. Instantieer in de praktijk geen `HttpClient`-object voor elke aanvraag. Volg de richtlijnen in [Use IHttpClientFactory to implement resilient HTTP requests](/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) (IHttpClientFactory gebruiken voor het implementeren van tolerante HTTP-aanvragen).

Als u deze code test op de voorbeeld-app voor [Zelfstudie: Een ASP.NET Core- en SQL Database-app bouwen in Azure App Service](tutorial-dotnetcore-sqldb-app.md), kunt u deze gebruiken om een e-mailbevestiging te verzenden in de [actie Maken](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial/blob/master/Controllers/TodosController.cs#L56-L65), nadat het `Todo`-item is toegevoegd.

### <a name="nodejs"></a>[Node.js](#tab/node)

In Node.js kunt u het HTTP-bericht eenvoudig verzenden met een NPM-pakket zoals [axios](https://www.npmjs.com/package/axios). Bijvoorbeeld:

```javascript
// Requires npm install --save axios
const axios = require('axios');

var jsonData = {
        email: "a-valid@emailaddress.com",
        due: "4/1/2020",
        task: "My new task!"
};

(async function(data) {
    try {
        const response = await axios.post(process.env.LOGIC_APP_URL, jsonData);
        console.log(response.status);
    } catch (error) {
        console.log(error);
    }
})(jsonData);

```

Als u deze code test op de voorbeeld-app voor [Zelfstudie: Een Node.js- en MongoDB-app bouwen in Azure](tutorial-nodejs-mongodb-app.md), kunt u deze gebruiken voor het verzenden van een e-mailbevestiging in de [functie Maken](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L14-L27), nadat [het artikel is opgeslagen](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L24).

### <a name="php"></a>[PHP](#tab/php)

In PHP kunt u het HTTP-bericht eenvoudig verzenden met [Guzzle](http://docs.guzzlephp.org/en/stable/index.html). Bijvoorbeeld:

```php
// Requires composer require guzzlehttp/guzzle:~6.0
use GuzzleHttp\Client;
...
$client = new Client();
$options = [
    'json' => [ 
        'email' => 'a-valid@emailaddress.com',
        'due' => '4/1/2020',
        'task' => "My new task!"
    ]
];

$promise = $client-> postAsync(getenv($LOGIC_APP_URL), $options)->then( 
    function ($response) {
        return $response->getStatusCode();
    }, function ($exception) {
        return $exception->getResponse();
    }
);

$response = $promise->wait();
// Requires Laravel to run Log::info(). Check the documentation of your preferred framework for logging instructions.
Log::info(print_r($response, TRUE));
```

Als u deze code test op de voorbeeld-app voor [Zelfstudie: Een PHP- en MySQL-app bouwen in Azure](tutorial-php-mysql-app.md), kunt u deze gebruiken om een e-mailbevestiging te verzenden in de [Route::post-functie](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48), net vóór de return-instructie.

### <a name="python"></a>[Python](#tab/python)

In Python kunt u het HTTP-bericht eenvoudig verzenden met [aanvragen](https://pypi.org/project/requests/). Bijvoorbeeld:

```python
# Requires pip install requests && pip freeze > requirements.txt
import requests
...
payload = {
    "email": "a-valid@emailaddress.com",
    "due": "4/1/2020",
    "task": "My new task!"
}
response = requests.post("https://prod-112.westeurope.logic.azure.com:443/workfl$
print(response.status_code)
```
<!-- ```python
# Requires pip install aiohttp && pip freeze > requirements.txt
import aiohttp
...
payload = {
        'email': 'a-valid@emailaddress.com',
        'due': '4/1/2020',
        'task': 'My new task!'
}
async with aiohttp.post('http://httpbin.org/post', data=json.dump(payload)) as resp:
    print(await resp.status())
``` -->

Als u deze code test op de voorbeeld-app voor [Zelfstudie: Een Python (Django) web-app uitvoeren met PostgreSQL in Azure App Service](tutorial-python-postgresql-app.md), kunt u deze gebruiken om een e-mailbevestiging te verzenden in de [Route::post-functie](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48), net vóór de return-instructie.

### <a name="ruby"></a>[Ruby](#tab/ruby)

In Ruby kunt u het HTTP-bericht eenvoudig verzenden met JSONClient. Bijvoorbeeld:

```ruby
clnt = JSONClient.new
body = { 
    'email' => 'a-valid@emailaddress.com',
    'due' => '4/1/2020',
    'task' => "My new task!"
}

connection = clnt.post_async(ENV['LOGIC_APP_URL'], body)
```

Als u deze code test in de voorbeeld-app voor [Een Ruby- en Postgres-app maken in Azure App Service op Linux](tutorial-ruby-postgres-app.md)bouwen, kunt u deze gebruiken om een e-mailbevestiging te verzenden in de actie [Maken](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L26-L38) [wanneer @task.save slaagt](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L30).

---

## <a name="more-resources"></a>Meer bronnen

[Zelfstudie: RESTful API hosten met CORS in Azure App Service](app-service-web-tutorial-rest-api.md)  
[HTTP-aanvraag/antwoord-referentie voor Logic Apps](../connectors/connectors-native-reqres.md)  
[Snelstart: Uw eerste werkstroom maken met Azure Logic Apps - Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)
---
title: Quickstart voor Bing Autosuggest-clientbibliotheek voor C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2020
ms.author: aahi
ms.openlocfilehash: 5bfe28a8111128ba7d8072c27d8647e6137620cd
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97844772"
---
Ga aan de slag met de Bing Autosuggest-clientbibliotheek voor .NET. Volg deze stappen om het pakket te installeren en de voorbeeldcode voor basistaken uit te proberen.

Gebruik de Bing Autosuggest-clientbibliotheek voor .NET om zoeksuggesties te krijgen op onvolledige queryreeksen.

[Referentiedocumentatie](/dotnet/api/overview/azure/cognitiveservices/bing-autosuggest-readme?view=azure-dotnet) | [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingAutoSuggest) | [Pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.AutoSuggest/) | [Voorbeeldcode](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/BingAutoSuggest/Program.cs)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [kunt u er gratis een aanmaken](https://azure.microsoft.com/free/cognitive-services).
* De huidige versie van [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](~/includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

## <a name="create-environment-variables"></a>Omgevingsvariabelen maken

>[!NOTE]
> De eindpunten voor resources die zijn gemaakt na 1 juli 2019, gebruiken de aangepaste indeling voor subdomeinen die hieronder wordt weergegeven. Zie [Aangepaste subdomeinnamen voor Cognitive Services](../../../cognitive-services-custom-subdomains.md) voor meer informatie en een volledige lijst met regionale eindpunten.

Maak met uw sleutel en eindpunt van de resource die u hebt gemaakt, twee omgevingsvariabelen voor verificatie:
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY`: De resourcesleutel voor het verifiëren van uw aanvragen.
* `AUTOSUGGEST_ENDPOINT`: Het resource-eindpunt voor het verzenden van API-aanvragen. Dit ziet er als volgt uit: `https://<your-custom-subdomain>.api.cognitive.microsoft.com`.

Volg de instructies voor uw besturingssysteem.
<!-- replace the below endpoint and key examples -->
### <a name="windows"></a>[Windows](#tab/windows)

```console
setx AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

Nadat u de omgevingsvariabele hebt toegevoegd, start u het consolevenster opnieuw.

### <a name="linux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source ~/.bashrc` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.

### <a name="macos"></a>[MacOS](#tab/unix)

Bewerk uw `.bash_profile` en voeg de omgevingsvariabele toe:

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source .bash_profile` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.
***

## <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

Maak een nieuwe .NET Core-toepassing in uw favoriete editor of IDE.

Gebruik in een consolevenster (zoals cmd, PowerShell of Bash) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam `bing-autosuggest-quickstart`. Met deze opdracht maakt u een eenvoudig Hallo wereld-C#-project met één bronbestand: *program.cs*.

```console
dotnet new console -n bing-autosuggest-quickstart
```

Wijzig uw map in de zojuist gemaakte app-map. U kunt de toepassing maken met:

```console
dotnet build
```

De build-uitvoer mag geen waarschuwingen of fouten bevatten.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

Open vanuit de projectmap het bestand *program.cs* in uw favoriete editor of IDE. Voeg de volgende `using`-instructies toe:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
```

Maak in de `Program`-klasse variabelen voor het Azure-eindpunt en de Azure-sleutel voor uw resource. Als u de omgevingsvariabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell waarmee deze wordt uitgevoerd, sluiten en opnieuw openen om toegang te krijgen tot de variabele.

```csharp
private const string key_var = "AUTOSUGGEST_SUBSCRIPTION_KEY";
private static readonly string subscription_key = Environment.GetEnvironmentVariable(key_var);

// Note you must use the same region as you used to get your subscription key.
private const string endpoint_var = "AUTOSUGGEST_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);
```

Voeg in de `Main`-methode van de toepassing de volgende methodeaanroepen toe, die u later gaat definiëren.

```csharp
static void Main(string[] args)
{
    Task.WaitAll(RunQuickstart());
    Console.WriteLine("Press any key to exit.");
    Console.Read();
}
```

## <a name="install-the-client-library"></a>De clientbibliotheek installeren

Installeer in de toepassingsmap de Bing Autosuggest-clientbibliotheek voor .NET met de volgende opdracht:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Search.AutoSuggest --version 2.0.0
```

Als u de Visual Studio-IDE, gebruikt, is de clientbibliotheek beschikbaar als downloadbaar NuGet-pakket.

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de clientbibliotheek van Bing Autosuggest voor .NET:

* [De client verifiëren](#authenticate-the-client)
* [Een Autosuggest-aanvraag verzenden](#send-an-autosuggest-request)

### <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> In deze quickstart wordt ervan uitgegaan dat u [een omgevingsvariabele hebt gemaakt](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) voor uw Bing Autosuggest-sleutel met de naam `AUTOSUGGEST_SUBSCRIPTION_KEY`, en een voor uw eindpunt met de naam `AUTOSUGGEST_ENDPOINT`.


Instantieer in een nieuwe asynchrone methode een client met uw eindpunt en sleutel. Maak een [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.apikeyserviceclientcredentials?view=azure-dotnet)-object met uw sleutel en gebruik het met uw eindpunt om een [AutosuggestClient-](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclient?view=azure-dotnet)-object te maken.

```csharp
async static Task RunQuickstart()
{
    // Generate the credentials and create the client.
    var credentials = new Microsoft.Azure.CognitiveServices.Search.AutoSuggest.ApiKeyServiceClientCredentials(subscription_key);
    var client = new AutoSuggestClient(credentials, new System.Net.Http.DelegatingHandler[] { })
    {
        Endpoint = endpoint
    };
}
```

### <a name="send-an-autosuggest-request"></a>Een Autosuggest-aanvraag verzenden

Gebruik in dezelfde methode de methode [AutoSuggestMethodAsync](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) van de client om een query te sturen naar Bing. Herhaal vervolgens het antwoord [Suggesties](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet) en druk de eerste suggestie af.

```csharp
var result = await client.AutoSuggestMethodAsync("xb");
var groups = result.SuggestionGroups;
if (groups.Count > 0) {
    var group = groups[0];
    Console.Write("First suggestion group: {0}\n", group.Name);
    var suggestions = group.SearchSuggestions;
    if (suggestions.Count > 0)
    {
        Console.WriteLine("First suggestion:");
        Console.WriteLine("Query: {0}", suggestions[0].Query);
        Console.WriteLine("Display text: {0}", suggestions[0].DisplayText);
    }
    else
    {
        Console.WriteLine("No suggestions found in this group.");
    }
}
else
{
    Console.WriteLine("No suggestions found.");
}
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing op vanuit uw toepassingsmap met de opdracht `dotnet run`.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd:

* [Een resourcegroep verwijderen in Azure Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources).
* [Een resourcegroep verwijderen in Azure Portal CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie over Bing Automatische suggesties](../../tutorials/autosuggest.md)

## <a name="see-also"></a>Zie ook

- [Wat is Bing Automatische suggesties?](../../get-suggested-search-terms.md)
- [Dotnet-referentie Bing Autosuggest](/dotnet/api/overview/azure/cognitiveservices/bing-autosuggest-readme?view=azure-dotnet)
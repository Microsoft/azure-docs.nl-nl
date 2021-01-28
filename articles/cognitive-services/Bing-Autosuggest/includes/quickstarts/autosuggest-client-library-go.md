---
title: Quickstart voor Bing Autosuggest-clientbibliotheek voor Go
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2020
ms.author: aahi
ms.openlocfilehash: 1c3ed6cc97c6fc77083baa915fac2f5530922eac
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948207"
---
Ga aan de slag met de Bing Autosuggest-clientbibliotheek voor Go. Volg deze stappen om de bibliotheek te installeren en onze voorbeelden voor basistaken uit te proberen.

Gebruik de Bing Autosuggest-clientbibliotheek voor Go om zoeksuggesties te krijgen op onvolledige queryreeksen.

[Referentiedocumentatie](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest) | [Broncode bibliotheek](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [Voorbeeldcode](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/BingAutoSuggest/BingAutoSuggestQuickstart.go)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [kunt u er gratis een aanmaken](https://azure.microsoft.com/free/cognitive-services).
* Nieuwste versie van [Go](https://golang.org/dl/).

Begin de Bing Autosuggest-clientbibliotheek te gebruiken door een Azure-resource te maken. Kies hieronder het resourcetype dat bij u past:

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](~/includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

## <a name="create-environment-variables"></a>Omgevingsvariabelen maken

>[!NOTE]
> De eindpunten voor resources die zijn gemaakt na 1 juli 2019, gebruiken de aangepaste indeling voor subdomeinen die hieronder wordt weergegeven. Zie [Aangepaste subdomeinnamen voor Cognitive Services](../../../cognitive-services-custom-subdomains.md) voor meer informatie en een volledige lijst met regionale eindpunten.

Maak met uw sleutel en eindpunt van de resource die u hebt gemaakt, twee omgevingsvariabelen voor verificatie:
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY`: De resourcesleutel voor het verifiëren van uw aanvragen.
* `AUTOSUGGEST_ENDPOINT`: Het resource-eindpunt voor het verzenden van API-aanvragen. Dit ziet er als volgt uit: `https://<your-custom-subdomain>.api.cognitive.microsoft.com`

Volg de instructies voor uw besturingssysteem.
<!-- replace the below endpoint and key examples -->
### <a name="windows"></a>[Windows](#tab/windows)

```console
setx BING_AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx BING_AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
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

## <a name="create-a-new-go-project"></a>Een nieuw Go-project maken

Maak in een consolevenster (cmd, PowerShell, Terminal, Bash) een nieuwe werkruimte voor uw Go-project en navigeer er naartoe. Uw werkruimte bevat drie mappen:

* **src**: Deze map bevat broncode en pakketten. Alle pakketten geïnstalleerd met de `go get`-opdracht bevinden zich hier.
* **pkg**: Deze map bevat de gecompileerde Go-pakketobjecten. Deze bestanden hebben allemaal een `.a`-extensie.
* **bin**: Deze map bevat de binaire uitvoerbare bestanden die worden gemaakt wanneer u `go install` uitvoert.

> [!TIP]
> Meer informatie over de structuur van een [Go-werkruimte](https://golang.org/doc/code.html#Workspaces). Deze handleiding bevat informatie om `$GOPATH` en `$GOROOT` in te stellen.

Laten we een werkruimte met de naam `my-app` en de vereiste submappen voor `src`, `pkg` en `bin` maken:

```
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

## <a name="install-the-client-library-for-go"></a>De clientbibliotheek installeren voor Go

Laten we nu de clientbibliotheek installeren voor Go:

```bash
$ go get -u <library-location-or-url>
```

of, als u dep gebruikt, binnen de uitvoer van de opslagplaats:

```bash
$ dep ensure -add <library-location-or-url>
```

## <a name="create-your-go-application"></a>Uw Go-toepassing maken

Vervolgens gaat u een bestand maken met de naam `src/sample-app.go`:

```bash
$ cd src
$ touch sample-app.go
```

Open `sample-app.go`, voeg de pakketnaam toe en importeer de volgende bibliotheken:

```Go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest"
    "github.com/Azure/go-autorest/autorest"
    "log"
    "os"
)
```

Maak een functie met de naam `main`. Maak vervolgens omgevingsvariabelen voor uw Bing Autosuggest-sleutel en -eindpunt:

```go
func main() {
    // Add your Bing Autosuggest subscription key to your environment variables.
    if "" == os.Getenv("BING_AUTOSUGGEST_SUBSCRIPTION_KEY") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_SUBSCRIPTION_KEY.")
    }
    // Add your Bing Autosuggest endpoint to your environment variables.
    var subscription_key string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
    if "" == os.Getenv("BING_AUTOSUGGEST_ENDPOINT") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_ENDPOINT.")
    }
    var endpoint string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
}
```

## <a name="code-examples"></a>Codevoorbeelden

Deze codevoorbeelden laten zien hoe u basistaken kunt uitvoeren met de Bing Autosuggest-clientbibliotheek voor Go:

* [De client verifiëren](#authenticate-the-client)
* [Een API-aanvraag verzenden](#send-an-api-request)

### <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> In deze quickstart wordt ervan uitgegaan dat u [een omgevingsvariabele hebt gemaakt](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) voor uw Bing Autosuggest-sleutel met de naam `BING_AUTOSUGGEST_SUBSCRIPTION_KEY`, en een voor uw eindpunt met de naam `BING_AUTOSUGGEST_ENDPOINT`.

Instantieer ub de `main()`-functie een client met uw eindpunt en sleutel.

```go
// Get the context, which is required by the SDK methods.
ctx := context.Background()

client := autosuggest.New()
// Set the subscription key on the client.
client.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscription_key)
client.Endpoint = endpoint
```

### <a name="send-an-api-request"></a>Een API-aanvraag verzenden

Gebruik in dezelfde methode de methode [AutoSuggestMethodAsync](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) van de client om een query te sturen naar Bing. Herhaal vervolgens het antwoord [Suggesties](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions) en druk de eerste suggestie af.

```Go
// This should return the query suggestion "xbox."
result, err := client.AutoSuggest (ctx, "xb", "", "", "", "", "", "", "", "", "", "", []autosuggest.ResponseFormat{"Json"})
if nil != err {
    log.Fatal(err)
}

groups := *result.SuggestionGroups
if len(groups) > 0 {
    group, _ := groups[0].AsSuggestionsSuggestionGroup()
    fmt.Printf ("First suggestion group: %s\n", (*group).Name)
    suggestions := *(*group).SearchSuggestions
    if len(suggestions) > 0 {
        fmt.Println("First suggestion:")
        fmt.Printf("Query: %s\n", *suggestions[0].Query)
        fmt.Printf("Display text: %s\n", *suggestions[0].DisplayText)
    } else {
        fmt.Println("No suggestions found in this group.")
    }
} else {
    fmt.Println("No suggestions found.")
}
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer uw Go-toepassing uit vanuit uw toepassingsmap met de opdracht `go run [arguments]`.

```Go
go run sample-app.go
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Een resourcegroep verwijderen in Azure Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources).
* [Een resourcegroep verwijderen in de Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie over Bing Automatische suggesties](../../tutorials/autosuggest.md)

## <a name="see-also"></a>Zie ook

- [Wat is Bing Automatische suggesties?](../../get-suggested-search-terms.md)
- [Naslaghandleiding Bing Automatische suggesties-API v7](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)

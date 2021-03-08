---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: cog-serv-seo-aug-2020
ms.date: 08/25/2020
ms.openlocfilehash: f98c34a53de60adc7866c1b57918232b966a0783
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444854"
---
[Referentiedocumentatie](/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer) | [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) | [Pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) | [Voorbeelden](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* De huidige versie van [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Zodra u een Azure-abonnement hebt, kunt u <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title="Een Personalizer-resource maken"  target="_blank">een Personalizer-resource maken </a> in de Azure-portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Personalizer-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

[!INCLUDE [Change model frequency](change-model-frequency.md)]

### <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

Maak een nieuwe .NET Core-toepassing in uw favoriete editor of IDE.

Gebruik in een consolevenster (zoals cmd, PowerShell of Bash) de opdracht dotnet `new` om een nieuwe console-app te maken met de naam `personalizer-quickstart`. Met deze opdracht maakt u een eenvoudig Hallo wereld-C#-project met één bronbestand: `Program.cs`.

```console
dotnet new console -n personalizer-quickstart
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

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Installeer in de toepassingsmap de Personalizer-clientbibliotheek voor .NET met de volgende opdracht:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0-preview
```

> [!TIP]
> Als u de Visual Studio-IDE, gebruikt, is de clientbibliotheek beschikbaar als downloadbaar NuGet-pakket.

Open vanuit de projectmap bestand `Program.cs` in uw favoriete editor of IDE. Voeg het volgende toe met behulp van de instructies:

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
using Microsoft.Azure.CognitiveServices.Personalizer.Models;
using System;
using System.Collections.Generic;
using System.Linq;
```

## <a name="object-model"></a>Objectmodel

De Personalizer-client is een [PersonalizerClient](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient)-object dat wordt geverifieerd bij Azure met behulp van Microsoft.Rest.ServiceClientCredentials, dat uw sleutel bevat.

Als u het beste item van de inhoud wilt hebben, maakt u een [RankRequest](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest) en geeft u dit door aan de methode [client.Rank](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank). Met de methode Rank wordt een RankResponse geretourneerd.

Als u een beloningsscore naar een Personalizer wilt verzenden, maakt u een [RewardRequest](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest) en geeft u dit door aan de methode [client.Reward](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward).

Het bepalen van de beloningsscore in deze quickstart is triviaal. In een productiesysteem kan de bepaling wat van invloed is op de [beloningsscore](../concept-rewards.md) en in welke mate, een complex proces zijn, dat u in de loop van de tijd zou kunnen wijzigen. Deze ontwerpbeslissing moet een van de belangrijkste beslissingen in uw Personalizer-architectuur zijn.

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Personalizer-clientbibliotheek voor .NET:

* [Personalizer-client maken](#authenticate-the-client)
* [Positie API](#request-the-best-action)
* [Beloning API](#send-a-reward)


## <a name="authenticate-the-client"></a>De client verifiëren

In deze sectie doet u twee dingen:
* Uw sleutel en eindpunt opgeven
* Personalizer-client maken

Voeg eerst de volgende regels toe aan de klasse Programma. Denk erom dat u de sleutel en het eindpunt van uw Personalizer-resource toevoegt.

[!INCLUDE [Personalizer find resource info](find-azure-resource-info.md)]

```csharp
private static readonly string ApiKey = "REPLACE-WITH-YOUR-PERSONALIZER-KEY";
private static readonly string ServiceEndpoint = "https://REPLACE-WITH-YOUR-PERSONALIZER-RESOURCE-NAME.cognitiveservices.azure.com";
```

Voeg vervolgens een methode aan uw programma toe om een nieuwe Personalizer-client te maken.

```csharp
static PersonalizerClient InitializePersonalizerClient(string url)
{
    PersonalizerClient client = new PersonalizerClient(
        new ApiKeyServiceClientCredentials(ApiKey)) { Endpoint = url };

    return client;
}
```

## <a name="get-food-items-as-rankable-actions"></a>Levensmiddelen ophalen als te rangschikken acties

Acties vertegenwoordigen de inhoudskeuzes waaruit Personalizer het beste inhoudsitem moet selecteren. Voeg de volgende methoden aan de klasse Programma toe om de reeks acties en hun functies te representeren. 

```csharp
static IList<RankableAction> GetActions()
{
    IList<RankableAction> actions = new List<RankableAction>
    {
        new RankableAction
        {
            Id = "pasta",
            Features =
            new List<object>() { new { taste = "salty", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
        },

        new RankableAction
        {
            Id = "ice cream",
            Features =
            new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionalLevel = 2 } }
        },

        new RankableAction
        {
            Id = "juice",
            Features =
            new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionLevel = 5 }, new { drink = true } }
        },

        new RankableAction
        {
            Id = "salad",
            Features =
            new List<object>() { new { taste = "salty", spiceLevel = "low" }, new { nutritionLevel = 8 } }
        }
    };

    return actions;
}
```

## <a name="get-user-preferences-for-context"></a>Gebruikersvoorkeuren voor context ophalen

Voeg de volgende methoden aan de klasse Programma toe om vanaf de opdrachtregel de invoer van een gebruiker op te halen voor het tijdstip van de dag en de huidige voedselvoorkeur. Deze worden als contextkenmerken gebruikt.

```csharp
static string GetUsersTimeOfDay()
{
    string[] timeOfDayFeatures = new string[] { "morning", "afternoon", "evening", "night" };

    Console.WriteLine("\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night");
    if (!int.TryParse(GetKey(), out int timeIndex) || timeIndex < 1 || timeIndex > timeOfDayFeatures.Length)
    {
        Console.WriteLine("\nEntered value is invalid. Setting feature value to " + timeOfDayFeatures[0] + ".");
        timeIndex = 1;
    }

    return timeOfDayFeatures[timeIndex - 1];
}
```

```csharp
static string GetUsersTastePreference()
{
    string[] tasteFeatures = new string[] { "salty", "sweet" };

    Console.WriteLine("\nWhat type of food would you prefer (enter number)? 1. salty 2. sweet");
    if (!int.TryParse(GetKey(), out int tasteIndex) || tasteIndex < 1 || tasteIndex > tasteFeatures.Length)
    {
        Console.WriteLine("\nEntered value is invalid. Setting feature value to " + tasteFeatures[0] + ".");
        tasteIndex = 1;
    }

    return tasteFeatures[tasteIndex - 1];
}
```

Beide methoden maken gebruik van de methode `GetKey` om de vanaf de opdrachtregel selectie van de gebruiker te lezen.

```csharp
private static string GetKey()
{
    return Console.ReadKey().Key.ToString().Last().ToString().ToUpper();
}
```

## <a name="create-the-learning-loop"></a>De leerlus maken

De Personalizer-leerlus is een cyclus van [Positie](#request-the-best-action)- en [Beloning](#send-a-reward)-aanroepen. In deze quickstart wordt elke Positie-oproep, voor het personaliseren van de inhoud, gevolgd door een Beloning-oproep om Personalizer te laten weten hoe goed de service is uitgevoerd.

Met de volgende code wordt herhaaldelijk vanaf de opdrachtregel de cyclus voor het vragen naar de voorkeuren van de gebruiker doorlopen. Deze gegevens worden naar Personalizer gestuurd om de beste actie te selecteren, waarbij de selectie aan de klant in een lijst wordt gepresenteerd om een keuze te kunnen maken. Vervolgens wordt er een beloningsscore naar Personalizer verzonden waarmee wordt aangegeven hoe goed de service de selectie heeft uitgevoerd.

```csharp
static void Main(string[] args)
{
    int iteration = 1;
    bool runLoop = true;

    IList<RankableAction> actions = GetActions();

    PersonalizerClient client = InitializePersonalizerClient(ServiceEndpoint);

    do
    {
        Console.WriteLine("\nIteration: " + iteration++);

        string timeOfDayFeature = GetUsersTimeOfDay();
        string tasteFeature = GetUsersTastePreference();

        IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature }
        };

        IList<string> excludeActions = new List<string> { "juice" };

        string eventId = Guid.NewGuid().ToString();

        var request = new RankRequest(actions, currentContext, excludeActions, eventId);
        RankResponse response = client.Rank(request);

        Console.WriteLine("\nPersonalizer service thinks you would like to have: " + response.RewardActionId + ". Is this correct? (y/n)");

        float reward = 0.0f;
        string answer = GetKey();

        if (answer == "Y")
        {
            reward = 1;
            Console.WriteLine("\nGreat! Enjoy your food.");
        }
        else if (answer == "N")
        {
            reward = 0;
            Console.WriteLine("\nYou didn't like the recommended food choice.");
        }
        else
        {
            Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended food choice.");
        }

        Console.WriteLine("\nPersonalizer service ranked the actions with the probabilities as below:");
        foreach (var rankedResponse in response.Ranking)
        {
            Console.WriteLine(rankedResponse.Id + " " + rankedResponse.Probability);
        }

        client.Reward(response.EventId, new RewardRequest(reward));

        Console.WriteLine("\nPress q to break, any other key to continue:");
        runLoop = !(GetKey() == "Q");

    } while (runLoop);
}
```

Voeg de volgende methoden toe, waarmee [de inhoudskeuzes worden opgehaald](#get-food-items-as-rankable-actions), voordat u het codebestand uitvoert:

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-the-best-action"></a>De beste actie aanvragen

Als u de Positie-aanvraag wilt voltooien, wordt naar de voorkeuren van de gebruiker gevraagd om een `currentContext` van de inhoudskeuzes te maken. Het proces kan inhoud maken die van de acties wordt uitgesloten, weergegeven als `excludeActions`. De Positie-aanvraag heeft de acties en hun kenmerken, currentcontext-kenmerken, excludeActions en een unieke gebeurtenis-id nodig om de reactie te kunnen ontvangen.

Deze quickstart bevat eenvoudige contextkenmerken over het tijdstip van de dag en de voedselvoorkeur van de gebruiker. In productiesystemen kan het bepalen en [evalueren](../concept-feature-evaluation.md) van [acties en kenmerken](../concepts-features.md) een ingewikkelde zaak zijn.

```csharp
string timeOfDayFeature = GetUsersTimeOfDay();
string tasteFeature = GetUsersTastePreference();

IList<object> currentContext = new List<object>() {
    new { time = timeOfDayFeature },
    new { taste = tasteFeature }
};

IList<string> excludeActions = new List<string> { "juice" };

string eventId = Guid.NewGuid().ToString();

var request = new RankRequest(actions, currentContext, excludeActions, eventId);
RankResponse response = client.Rank(request);
```

## <a name="send-a-reward"></a>Een beloning verzenden

Om ervoor te zorgen dat de beloningsscore de Beloning-aanvraag instuurt, haalt het programma de selectie van de gebruiker op vanaf de opdrachtregel, wijst een numerieke waarde toe aan de selectie en verzendt vervolgens de unieke gebeurtenis-id en de beloningsscore als de numerieke waarde naar de Beloning API.

In deze quickstart wordt een eenvoudig getal, een 0 of een 1, aan een beloningsscore toegewezen. In productiesystemen kan het bepalen van wanneer en wat er naar de [Beloning](../concept-rewards.md)-oproep moet worden gezonden een ingewikkelde zaak zijn, afhankelijk van uw specifieke behoeften.

```csharp
float reward = 0.0f;
string answer = GetKey();

if (answer == "Y")
{
    reward = 1;
    Console.WriteLine("\nGreat! Enjoy your food.");
}
else if (answer == "N")
{
    reward = 0;
    Console.WriteLine("\nYou didn't like the recommended food choice.");
}
else
{
    Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended food choice.");
}

Console.WriteLine("\nPersonalizer service ranked the actions with the probabilities as below:");
foreach (var rankedResponse in response.Ranking)
{
    Console.WriteLine(rankedResponse.Id + " " + rankedResponse.Probability);
}

// Send the reward for the action based on user response.
client.Reward(response.EventId, new RewardRequest(reward));
```

## <a name="run-the-program"></a>Het programma uitvoeren

Voer de toepassing uit vanuit uw toepassingsmap met de opdracht dotnet `run`.

```console
dotnet run
```

![Het quickstart-programma stelt een aantal vragen om gebruikersvoorkeuren te verzamelen, zogenaamde kenmerken. Vervolgens wordt de beste actie geboden.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

De [broncode voor deze quickstart](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer) is beschikbaar.

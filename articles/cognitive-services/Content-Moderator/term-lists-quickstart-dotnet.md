---
title: Tekst controleren aan de hand van een aangepaste termenlijst in C# - Content Moderator
titleSuffix: Azure Cognitive Services
description: Leer hoe u tekst kunt beheren met aangepaste termenlijsten met de Content Moderator-SDK voor C#.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 93d90232fb530a6c14c40558fc6a9974a1da42de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92900917"
---
# <a name="check-text-against-a-custom-term-list-in-c"></a>Tekst controleren aan de hand van een aangepaste termenlijst in C#

De standaardlijst met algemene termen van Azure Content Moderator is voldoende voor de meeste moderatietaken voor inhoudsbeheer. Het is echter mogelijk dat u inhoud moet controleren op termen die specifiek zijn voor uw organisatie. Zo is het bijvoorbeeld mogelijk dat u namen van concurrenten wilt taggen voor nader onderzoek. 

U kunt de [Content Moderator-SDK voor .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) gebruiken om aangepaste lijsten met termen te maken voor gebruik met de Teksttoezicht-API.

In dit artikel vindt u informatie en codevoorbeelden om aan de slag te gaan met de Content Moderator SDK voor .NET en het volgende te doen:
- Een lijst maken.
- Termen toevoegen aan een lijst.
- Termen vergelijken met de termen in een lijst.
- Termen verwijderen uit een lijst.
- Een lijst verwijderen.
- Lijstgegevens bewerken.
- De index vernieuwen zodat wijzigingen in de lijst worden gebruikt in een nieuwe scan.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/cognitive-services/) aan voordat u begint. 

## <a name="sign-up-for-content-moderator-services"></a>Registreren voor de Content Moderator-services

Om de Content Moderator-services via de REST-API of de SDK te kunnen gebruiken, hebt u een abonnementssleutel nodig. Abonneer u op de Content Moderator-service in de [Azure-portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) om ermee te kunnen werken.

## <a name="create-your-visual-studio-project"></a>Het Visual Studio-project maken

1. Voeg een nieuw project van het type **Console-app (.NET Framework)** toe aan uw oplossing.

1. Geef het project de naam **TermLists**. Selecteer dit project als het enige opstartproject voor de oplossing.

### <a name="install-required-packages"></a>De vereiste pakketten installeren

Installeer de volgende NuGet-pakketten voor het project TermLists:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>De using-instructies van het programma bijwerken

Voeg de volgende `using`-instructies toe.

```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>De Content Moderator-client maken

Voeg de volgende code toe om een Content Moderator-client voor uw abonnement te maken. Werk de `AzureEndpoint` `CMSubscriptionKey` velden en bij met de waarden van uw eind punt-URL en abonnements sleutel. U vindt deze in het Azure Portal op het tabblad **Quick Start** van uw resource.

```csharp
/// <summary>
/// Wraps the creation and configuration of a Content Moderator client.
/// </summary>
/// <remarks>This class library contains insecure code. If you adapt this 
/// code for use in production, use a secure method of storing and using
/// your Content Moderator subscription key.</remarks>
public static class Clients
{
    /// <summary>
    /// The base URL fragment for Content Moderator calls.
    /// </summary>
    private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

    /// <summary>
    /// Your Content Moderator subscription key.
    /// </summary>
    private static readonly string CMSubscriptionKey = "YOUR API KEY";

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        // Create and initialize an instance of the Content Moderator API wrapper.
        ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

        client.Endpoint = AzureEndpoint;
        return client;
    }
}
```

### <a name="add-private-properties"></a>Private-eigenschappen toevoegen

Voeg de volgende private-eigenschappen toe aan de naamruimte TermLists, klasse Program.

```csharp
/// <summary>
/// The language of the terms in the term lists.
/// </summary>
private const string lang = "eng";

/// <summary>
/// The minimum amount of time, in milliseconds, to wait between calls
/// to the Content Moderator APIs.
/// </summary>
private const int throttleRate = 3000;

/// <summary>
/// The number of minutes to delay after updating the search index before
/// performing image match operations against the list.
/// </summary>
private const double latencyDelay = 0.5;
```

## <a name="create-a-term-list"></a>Een termenlijst maken

U maakt een termenlijst met **ContentModeratorClient.ListManagementTermLists.Create**. De eerste parameter voor **Create** is een tekenreeks met een MIME-type, dat moet zijn ingesteld op 'application/json'. Zie voor meer informatie de [API-naslaghandleiding](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). De tweede parameter is een **Body**-object met een naam en beschrijving voor de nieuwe termenlijst.

> [!NOTE]
> Er is een maximumlimiet van **5 terminologielijsten** waarbij elke lijst **niet meer dan 10.000 termen mag bevatten**.

Voeg de volgende methodedefinitie toe aan de naamruimte TermLists, klasse Program.

> [!NOTE]
> De sleutel van uw Content Moderator-service heeft een limiet voor het aantal aanvragen per seconde (RPS). Als u die limiet overschrijdt, genereert de SDK een uitzondering met foutcode 429. Een sleutel voor de gratis laag heeft een limiet van één RPS.

```csharp
/// <summary>
/// Creates a new term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <returns>The term list ID.</returns>
static string CreateTermList (ContentModeratorClient client)
{
    Console.WriteLine("Creating term list.");

    Body body = new Body("Term list name", "Term list description");
    TermList list = client.ListManagementTermLists.Create("application/json", body);
    if (false == list.Id.HasValue)
    {
        throw new Exception("TermList.Id value missing.");
    }
    else
    {
        string list_id = list.Id.Value.ToString();
        Console.WriteLine("Term list created. ID: {0}.", list_id);
        Thread.Sleep(throttleRate);
        return list_id;
    }
}
```

## <a name="update-term-list-name-and-description"></a>Naam en beschrijving van termenlijst bijwerken

U kunt de gegevens van de termenlijst bijwerken met **ContentModeratorClient.ListManagementTermLists.Update**. De eerste parameter voor **Update** is de id van de termenlijst. De tweede parameter is een MIME-type, met de waarde 'application/json'. Zie voor meer informatie de [API-naslaghandleiding](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f685). De derde parameter is een **Body**-object, dat de nieuwe naam en beschrijving bevat.

Voeg de volgende methodedefinitie toe aan de naamruimte TermLists, klasse Program.

```csharp
/// <summary>
/// Update the information for the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to update.</param>
/// <param name="name">The new name for the term list.</param>
/// <param name="description">The new description for the term list.</param>
static void UpdateTermList (ContentModeratorClient client, string list_id, string name = null, string description = null)
{
    Console.WriteLine("Updating information for term list with ID {0}.", list_id);
    Body body = new Body(name, description);
    client.ListManagementTermLists.Update(list_id, "application/json", body);
    Thread.Sleep(throttleRate);
}
```

## <a name="add-a-term-to-a-term-list"></a>Een term toevoegen aan een termenlijst

Voeg de volgende methodedefinitie toe aan de naamruimte TermLists, klasse Program.

```csharp
/// <summary>
/// Add a term to the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to update.</param>
/// <param name="term">The term to add to the term list.</param>
static void AddTerm (ContentModeratorClient client, string list_id, string term)
{
    Console.WriteLine("Adding term \"{0}\" to term list with ID {1}.", term, list_id);
    client.ListManagementTerm.AddTerm(list_id, term, lang);
    Thread.Sleep(throttleRate);
}
```

## <a name="get-all-terms-in-a-term-list"></a>Alle termen in een termenlijst opvragen

Voeg de volgende methodedefinitie toe aan de naamruimte TermLists, klasse Program.

```csharp
/// <summary>
/// Get all terms in the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to get all terms.</param>
static void GetAllTerms(ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Getting terms in term list with ID {0}.", list_id);
    Terms terms = client.ListManagementTerm.GetAllTerms(list_id, lang);
    TermsData data = terms.Data;
    foreach (TermsInList term in data.Terms)
    {
        Console.WriteLine(term.Term);
    }
    Thread.Sleep(throttleRate);
}
```

## <a name="add-code-to-refresh-the-search-index"></a>Code toevoegen voor het vernieuwen van de zoekindex

Nadat u een termenlijst hebt gewijzigd, moet u de zoekindex van de lijst vernieuwen zodat de wijzigingen zijn verwerkt wanneer u de termenlijst de volgende keer gebruikt om tekst te screenen. Dit is vergelijkbaar met hoe een zoekmachine op het bureaublad (indien ingeschakeld) of een zoekmachine op internet voortdurend de eigen index vernieuwt om nieuwe bestanden of pagina's toe te voegen.

U vernieuwt de zoekindex van een termenlijst met **ContentModeratorClient.ListManagementTermLists.RefreshIndexMethod**.

Voeg de volgende methodedefinitie toe aan de naamruimte TermLists, klasse Program.

```csharp
/// <summary>
/// Refresh the search index for the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to refresh.</param>
static void RefreshSearchIndex (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Refreshing search index for term list with ID {0}.", list_id);
    client.ListManagementTermLists.RefreshIndexMethod(list_id, lang);
    Thread.Sleep((int)(latencyDelay * 60 * 1000));
}
```

## <a name="screen-text-using-a-term-list"></a>Tekst screenen met behulp van een termenlijst

U kunt tekst screenen met behulp van een termenlijst met **ContentModeratorClient.TextModeration.ScreenText**. Deze opdracht accepteert de volgende parameters.

- De taal van de termen in de termenlijst.
- Een MIME-type, te weten 'text/html', 'text/xml', 'text/markdown' of 'text/plain'.
- De tekst die moet worden gescreend.
- Een Booleaanse waarde Stel dit veld in op **true** om AutoCorrectie toe te passen op de tekst voordat deze wordt gescreend.
- Een Booleaanse waarde Stel dit veld in op **waar** om persoons gegevens in de tekst te detecteren.
- De id van de termenlijst.

Zie voor meer informatie de [API-naslaghandleiding](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f).

**ScreenText** retourneert een **Screen**-object, dat een eigenschap **Terms** heeft met daarin alle termen die Content Moderator tijdens het screenen heeft gedetecteerd. Als Content Moderator geen termen heeft gedetecteerd tijdens het screenen, heeft de eigenschap **Terms** de waarde **null**.

Voeg de volgende methodedefinitie toe aan de naamruimte TermLists, klasse Program.

```csharp
/// <summary>
/// Screen the indicated text for terms in the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to use to screen the text.</param>
/// <param name="text">The text to screen.</param>
static void ScreenText (ContentModeratorClient client, string list_id, string text)
{
    Console.WriteLine("Screening text: \"{0}\" using term list with ID {1}.", text, list_id);
    Screen screen = client.TextModeration.ScreenText(lang, "text/plain", text, false, false, list_id);
    if (null == screen.Terms)
    {
        Console.WriteLine("No terms from the term list were detected in the text.");
    }
    else
    {
        foreach (DetectedTerms term in screen.Terms)
        {
            Console.WriteLine(String.Format("Found term: \"{0}\" from list ID {1} at index {2}.", term.Term, term.ListId, term.Index));
        }
    }
    Thread.Sleep(throttleRate);
}
```

## <a name="delete-terms-and-lists"></a>Termen en lijsten verwijderen

U kunt eenvoudig term of lijsten verwijderen. Gebruik de SDK om de volgende taken uit te voeren:

- Een term verwijderen. (**ContentModeratorClient.ListManagementTerm.DeleteTerm**)
- Alle termen in een lijst verwijderen zonder de lijst te verwijderen. (**ContentModeratorClient.ListManagementTerm.DeleteAllTerms**)
- Een lijst en alle inhoud verwijderen. (**ContentModeratorClient.ListManagementTermLists.Delete**)

### <a name="delete-a-term"></a>Een term verwijderen

Voeg de volgende methodedefinitie toe aan de naamruimte TermLists, klasse Program.

```csharp
/// <summary>
/// Delete a term from the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to delete the term.</param>
/// <param name="term">The term to delete.</param>
static void DeleteTerm (ContentModeratorClient client, string list_id, string term)
{
    Console.WriteLine("Removed term \"{0}\" from term list with ID {1}.", term, list_id);
    client.ListManagementTerm.DeleteTerm(list_id, term, lang);
    Thread.Sleep(throttleRate);
}
```

### <a name="delete-all-terms-in-a-term-list"></a>Alle termen in een termenlijst verwijderen

Voeg de volgende methodedefinitie toe aan de naamruimte TermLists, klasse Program.

```csharp
/// <summary>
/// Delete all terms from the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to delete all terms.</param>
static void DeleteAllTerms (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Removing all terms from term list with ID {0}.", list_id);
    client.ListManagementTerm.DeleteAllTerms(list_id, lang);
    Thread.Sleep(throttleRate);
}
```

### <a name="delete-a-term-list"></a>Een termenlijst verwijderen

Voeg de volgende methodedefinitie toe aan de naamruimte TermLists, klasse Program.

```csharp
/// <summary>
/// Delete the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to delete.</param>
static void DeleteTermList (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Deleting term list with ID {0}.", list_id);
    client.ListManagementTermLists.Delete(list_id);
    Thread.Sleep(throttleRate);
}
```

## <a name="compose-the-main-method"></a>De methode Main opstellen

Voeg de definitie van de **hoofd** methode toe aan naam ruimte **TermLists**, Class- **programma**. Sluit ten slotte de **programma** klasse en de **TermLists** -naam ruimte.

```csharp
static void Main(string[] args)
{
    using (var client = Clients.NewClient())
    {
        string list_id = CreateTermList(client);

        UpdateTermList(client, list_id, "name", "description");
        AddTerm(client, list_id, "term1");
        AddTerm(client, list_id, "term2");

        GetAllTerms(client, list_id);

        // Always remember to refresh the search index of your list
        RefreshSearchIndex(client, list_id);

        string text = "This text contains the terms \"term1\" and \"term2\".";
        ScreenText(client, list_id, text);

        DeleteTerm(client, list_id, "term1");

        // Always remember to refresh the search index of your list
        RefreshSearchIndex(client, list_id);

        text = "This text contains the terms \"term1\" and \"term2\".";
        ScreenText(client, list_id, text);

        DeleteAllTerms(client, list_id);
        DeleteTermList(client, list_id);

        Console.WriteLine("Press ENTER to close the application.");
        Console.ReadLine();
    }
}
```

## <a name="run-the-application-to-see-the-output"></a>De toepassing uitvoeren om de uitvoer te bekijken

De uitvoer van de console ziet er als volgt uit:

```console
Creating term list.
Term list created. ID: 252.
Updating information for term list with ID 252.

Adding term "term1" to term list with ID 252.
Adding term "term2" to term list with ID 252.

Getting terms in term list with ID 252.
term1
term2

Refreshing search index for term list with ID 252.

Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
Found term: "term1" from list ID 252 at index 32.
Found term: "term2" from list ID 252 at index 46.

Removed term "term1" from term list with ID 252.

Refreshing search index for term list with ID 252.

Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
Found term: "term2" from list ID 252 at index 46.

Removing all terms from term list with ID 252.
Deleting term list with ID 252.
Press ENTER to close the application.
```

## <a name="next-steps"></a>Volgende stappen

Download de [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) en de [Visual Studio-oplossing](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) voor deze en andere snelstarts over Content Moderator voor .NET en begin met de integratie.

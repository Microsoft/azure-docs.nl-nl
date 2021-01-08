---
title: 'Zelfstudie: Een client-app coderen'
titleSuffix: Azure Digital Twins
description: Zelfstudie voor het schrijven van de minimale code voor een client-app, met behulp van de .NET ( C# ) SDK.
author: baanders
ms.author: baanders
ms.date: 11/02/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 96533c1742e8ad5fde9796ea2073f6e402ac8dcf
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97862384"
---
# <a name="tutorial-coding-with-the-azure-digital-twins-apis"></a>Zelfstudie: Coderen met de Azure Digital Twins-API's

Het is gebruikelijk dat ontwikkelaars die met Azure Digital Twins werken, een clienttoepassing schrijven voor interactie met hun exemplaar van de Azure Digital Twins-service. Deze zelfstudie voor ontwikkelaars biedt een inleiding op het programmeren van de Azure Digital Twins-service, met behulp van de [Azure Digital Twins-SDK voor .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). U wordt stap voor stap begeleid bij het schrijven van een C# console-client-app, vanaf het begin.

> [!div class="checklist"]
> * Project instellen
> * Aan de slag met projectcode   
> * Codevoorbeeld voltooien
> * Resources opschonen
> * Volgende stappen

## <a name="prerequisites"></a>Vereisten

In deze zelfstudie wordt de opdrachtregel gebruikt voor het instellen en projectwerk. Daarom kunt u elke code-editor gebruiken om de oefeningen door te lopen.

U gaat als volgt aan de slag:
* Elk soort code-editor
* **.NET Core 3.1** op uw ontwikkelcomputer. U kunt deze versie van .NET Core SDK voor meerdere platforms downloaden van [.NET Core 3.1. downloaden](https://dotnet.microsoft.com/download/dotnet-core/3.1).

### <a name="prepare-an-azure-digital-twins-instance"></a>Een Azure Digital Twins-exemplaar voorbereiden

[!INCLUDE [Azure Digital Twins: instance prereq](../../includes/digital-twins-prereq-instance.md)]

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

## <a name="set-up-project"></a>Project instellen

Wanneer u klaar bent om met uw Azure Digital Twins-exemplaar, moet u het client-app-project instellen. 

Open een opdrachtprompt of een ander consolevenster op uw computer en maak een lege projectmap waarin u uw werk wilt opslaan tijdens deze zelfstudie. Geef de map een willekeurige naam (bijvoorbeeld *DigitalTwinsCodeTutorial*).

Navigeer naar de nieuwe map.

**Maak een leeg .NET-console-app-project** in de projectmap. In het opdrachtvenster kunt u de volgende opdracht uitvoeren om een minimaal C#-project voor de console te maken:

```cmd/sh
dotnet new console
```

Hiermee maakt u een aantal bestanden in uw map, met daarin de naam *Program.cs* waarin u de meeste code schrijft.

Houd het opdrachtvenster geopend, omdat u het in de zelfstudie blijft gebruiken.

Vervolgens voegt u **twee afhankelijkheden toe aan uw project** die nodig zijn om te werken met Azure Digital Twins. U kunt de onderstaande koppelingen gebruiken om naar de pakketten op NuGet te navigeren. Hier vindt u de consoleopdrachten (inclusief voor .NET CLI) om elk pakket aan uw project toe te voegen.
* [**Azure.DigitalTwins.Core**](https://www.nuget.org/packages/Azure.DigitalTwins.Core). Dit is het pakket voor de [Azure Digital Twins SDK voor .NET](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). Voeg de nieuwste versie toe.
* [**Azure.Identity**](https://www.nuget.org/packages/Azure.Identity). Deze bibliotheek biedt hulpprogramma's voor de verificatie bij Azure. Voeg versie 1.2.2 toe.

>[!NOTE]
> Er is momenteel een [bekend probleem](troubleshoot-known-issues.md#issue-with-default-azure-credential-authentication-on-azureidentity-130) dat invloed heeft op de mogelijkheid om Azure.Identity-versie 1.3.0 te gebruiken met deze zelfstudie. Gebruik versie 1.2.2 zolang dit probleem zich blijft voordoen.

## <a name="get-started-with-project-code"></a>Aan de slag met projectcode

In deze sectie gaat u beginnen met het schrijven van de code voor uw nieuwe app-project om met Azure Digital Twins te werken. De behandelde acties zijn onder meer:
* Verificatie op basis van de service
* Een model uploaden
* Fouten waarnemen
* Digitale tweelingen maken
* Relaties maken
* Query uitvoeren voor digitale tweeling

Er is ook een sectie met de volledige code aan het einde van de zelfstudie. U kunt dit als referentie gebruiken om uw programma te controleren terwijl u dit doet.

Open het bestand *Program.cs* in een code-editor om te beginnen. Er wordt een sjabloon met minimale code weergegeven die er als volgt uitziet:

```csharp
using System;

namespace DigitalTwinsCodeTutorial
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}
```

Voeg eerst enkele `using` regels toe boven aan de code om de benodigde afhankelijkheden op te halen.

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
```

Vervolgens voegt u code toe aan dit bestand om een deel van de functionaliteit in te vullen. 

### <a name="authenticate-against-the-service"></a>Verificatie op basis van de service

Het eerste wat uw app moet doen, is verifiëren op basis van de Azure Digital Twins-service. Vervolgens kunt u een service-clientklasse maken voor toegang tot de SDK-functies.

Voor de verificatie hebt u de *hostName*-waarde van uw Azure Digital Twins-exemplaar nodig.

Plak de volgende code in *Program.cs* onder de regel "Hello World!" in de `Main` methode. Stel de waarde van `adtInstanceUrl` in op die van *hostName* van uw exemplaar van Azure Digital Twins.

```csharp
string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>"; 
var credential = new DefaultAzureCredential();
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
Console.WriteLine($"Service client created – ready to go");
```

Sla het bestand op. 

Voer in het opdrachtvenster de code uit met de volgende opdracht: 

```cmd/sh
dotnet run
```

Hiermee herstelt u de afhankelijkheden bij de eerste uitvoering en voert u het programma uit. 
* Als er geen fout optreedt, drukt het programma *Serviceclient gemaakt, klaar on te starten* af.
* Omdat er nog geen foutafhandeling in dit project is, ziet u een uitzondering die is gegenereerd door de code als er iets mis gaat.

### <a name="upload-a-model"></a>Een model uploaden

Azure Digital Twins heeft geen intrinsieke domeinwoordenlijst. De typen elementen in uw omgeving die u kunt vertegenwoordigen in Azure Digital Twins, worden door u gedefinieerd met behulp van **Modellen**. [Modellen](concepts-models.md) lijken op klassen in objectgeoriënteerde programmeertalen. Ze bieden door de gebruiker gedefinieerde sjablonen die [digitale tweelingen](concepts-twins-graph.md) later kunnen volgen en instantiëren. Ze worden geschreven in een JSON-achtige taal met de naam **Digital Twins Definition Language (DTDL)** .

De eerste stap bij het maken van een Azure Digital Twins-oplossing is het definiëren van ten minste een model in een DTDL-bestand.

Maak in de map waar u het project hebt gemaakt een nieuw *.json*-bestand met de naam *SampleModel.json*. Plak in de volgende hoofdtekst van het bestand: 

```json
{
  "@id": "dtmi:example:SampleModel;1",
  "@type": "Interface",
  "displayName": "SampleModel",
  "contents": [
    {
      "@type": "Relationship",
      "name": "contains"
    },
    {
      "@type": "Property",
      "name": "data",
      "schema": "string"
    }
  ],
  "@context": "dtmi:dtdl:context;2"
}
```

> [!TIP]
> Als u Visual Studio voor deze zelfstudie gebruikt, wilt u mogelijk het zojuist gemaakte JSON-bestand selecteren en de eigenschap *Kopiëren naar uitvoermap* instellen in de Eigenschappencontrole naar te *Kopiëren als nieuwer* of *Altijd kopiëren*. Hierdoor kan Visual Studio het JSON-bestand met het standaardpad vinden wanneer u het programma uitvoert met **F5** tijdens de rest van de zelfstudie.

> [!TIP] 
> Er is een taalagnostisch [DTDL-validatorvoorbeeld](/samples/azure-samples/dtdl-validator/dtdl-validator) dat u kunt gebruiken om modeldocumenten te controleren en u ervan te verzekeren dat de DTDL geldig is. Het voorbeeld is gemaakt op basis van de DTDL-parserbibliotheek, waarover u meer kunt lezen in [*Instructies: Modellen parseren en valideren*](how-to-parse-models.md).

Voeg vervolgens wat meer code toe aan *Program.cs* om het model dat u zojuist hebt gemaakt, te uploaden naar uw Azure Digital Twins-exemplaar.

Voer eerst boven aan het bestand de volgende `using`-instructies toe:

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
```

Vervolgens bereidt u het gebruik van de asynchrone methoden in de C# service-SDK voor door de handtekening van de `Main` methode te wijzigen zodat asynchrone uitvoering kan worden uitgevoerd. 

```csharp
static async Task Main(string[] args)
```

> [!NOTE]
> Het gebruik van `async` is niet strikt vereist, omdat de SDK ook synchrone versies van alle aanroepen biedt. In deze zelfstudie gebruikt `async`.

Vervolgens wordt de eerste bit van code geleverd die samenwerkt met de Azure Digital Twins-service. Met deze code wordt het DTDL-bestand dat u hebt gemaakt op basis van de schijf, geladen en vervolgens geüpload naar uw Azure Digital Twins-service-exemplaar. 

Plak de volgende code onder de autorisatiecode die u eerder hebt toegevoegd.

```csharp
Console.WriteLine();
Console.WriteLine($"Upload a model");
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
// Upload the model to the service
await client.CreateModelsAsync(typeList);
```

Voer in het opdrachtvenster de programma uit met de volgende opdracht: 

```cmd/sh
dotnet run
```
‘Een model uploaden’ wordt afgedrukt in de uitvoer, om aan te geven dat deze code is bereikt, maar er nog geen uitvoer is om aan te geven of het uploaden is geslaagd.

Als u een afdrukinstructie wilt toevoegen waarmee alle modellen worden weergegeven die zijn geüpload, voegt u de volgende code toe, direct na de vorige sectie:

```csharp
// Read a list of models back from the service
Console.WriteLine("Models uploaded to the instance:");
AsyncPageable<DigitalTwinsModelData> modelDataList = client.GetModelsAsync();
await foreach (DigitalTwinsModelData md in modelDataList)
{
    Console.WriteLine($"{md.Id}");
}
```

**Voordat u het programma opnieuw uitvoert om deze nieuwe code te testen**, moet u even terug denken aan de laatste keer dat u het programma hebt uitgevoerd. U hebt uw model toen al geüpload. Met Azure Digital Twins kunt u hetzelfde model niet twee keer uploaden, dus als u probeert hetzelfde model nogmaals te uploaden, moet het programma een uitzondering genereren.

Voer, met dit in gedachten, in het opdrachtvenster het programma uit met de volgende opdracht:

```cmd/sh
dotnet run
```

Het programma moet een uitzondering genereren. Wanneer u probeert een model te uploaden dat al is geüpload, retourneert de service de fout "ongeldige aanvraag" via de REST API. Als gevolg hiervan zal de Azure Digital Twins-client SDK een uitzondering genereren voor elke serviceretourcode die niet geslaagd is. 

In de volgende sectie vindt u informatie over uitzonderingen zoals deze en hoe u deze in uw code kunt verwerken.

### <a name="catch-errors"></a>Fouten detecteren

Als u wilt voorkomen dat het programma vastloopt, kunt u uitzonderingscode toevoegen rond de code voor het uploaden van het model. Verpak de bestaande clientaanroep `await client.CreateModelsAsync(typeList)` in een try/catch-handler, zoals:

```csharp
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
```

Als u het programma nu uitvoert met `dotnet run` in uw opdrachtvenster, ziet u dat er een foutcode wordt weergegeven. De uitvoer van de code voor het maken van het model laat deze fout zien:

:::image type="content" source= "media/tutorial-code/model-error.png" alt-text="Programma-uitvoer, waarin een bericht wordt weergegeven met de tekst '409:Serviceaanvraag is mislukt. Status: 409 (Conflict).', gevolgd door een weergave van de fout die aangeeft dat dtmi:example:SampleModel;1 al bestaat":::

Vanaf dit punt verstuurt de zelfstudie alle aanroepen naar servicemethoden in try/catch-handlers.

### <a name="create-digital-twins"></a>Digitale tweelingen maken

Nu u een model naar Azure Digital Twins hebt geüpload, kunt u deze modeldefinitie gebruiken voor het maken van **digitale tweelingen**. [Digitale tweelingen](concepts-twins-graph.md) zijn exemplaren van een model en vertegenwoordigen de entiteiten in uw bedrijfsomgeving: dingen als sensoren op een boerderij, ruimten in een gebouw, of lichten in een auto. In deze sectie wordt een aantal digitale tweelingen gemaakt op basis van het model dat u eerder hebt geüpload.

Voeg de volgende code toe aan het einde van de methode `Main` om drie digitale tweelingen te maken en initialiseren op basis van dit model.

```csharp
// Initialize twin data
BasicDigitalTwin twinData = new BasicDigitalTwin();
twinData.Metadata.ModelId = "dtmi:example:SampleModel;1";
twinData.Contents.Add("data", $"Hello World!");

string prefix="sampleTwin-";
for(int i=0; i<3; i++) {
    try {
        twinData.Id = $"{prefix}{i}";
        await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(twinData.Id, twinData);
        Console.WriteLine($"Created twin: {prefix}{i}");
    } catch(RequestFailedException rex) {
        Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
    }
}
```

Voer in het opdrachtvenster de programma uit met `dotnet run`. Zoek in de uitvoer naar de weergaveberichten die melden dat *sampleTwin-0*, *sampleTwin-1* en *sampleTwin-2* zijn gemaakt. 

Voer vervolgens het programma opnieuw uit. 

U ziet dat er geen fout wordt gegenereerd wanneer de tweelingen de tweede keer worden gemaakt, zelfs als de tweeling al bestaan na de eerste uitvoering. In tegenstelling tot het maken van een model, is het maken van tweelingen op het REST-niveau een *PUT*-aanroep met *upsert*-semantiek. Dit betekent dat als er al een tweeling bestaat, er door een poging om dezelfde tweeling nog een keer te maken, de oorspronkelijke tweeling gewoon wordt vervangen. Er wordt geen fout gegenereerd.

### <a name="create-relationships"></a>Relaties maken

Vervolgens kunt u **relaties** maken tussen deze gemaakte tweelingen, om ze te verbinden in een **tweelinggrafiek**. [Tweelinggrafieken](concepts-twins-graph.md) worden gebruikt om uw gehele omgeving voor te stellen.

Voeg vervolgens een **nieuwe statische methode** toe aan de `Program`-klasse, onder de `Main`-methode (de code bevat nu twee methodes):

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
{
    var relationship = new BasicRelationship
    {
        TargetId = targetId,
        Name = "contains"
    };

    try
    {
        string relId = $"{srcId}-contains->{targetId}";
        await client.CreateOrReplaceRelationshipAsync(srcId, relId, relationship);
        Console.WriteLine("Created relationship successfully");
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
    }
}
```

Voeg vervolgens de volgende code toe aan het einde van de `Main`-methode om de `CreateRelationship`-methode aan te roepen en gebruik de code die u zojuist hebt geschreven:

```csharp
// Connect the twins with relationships
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");
```

Voer in het opdrachtvenster de programma uit met `dotnet run`. Zoek in de uitvoer naar afdrukinstructies, waarin wordt gemeld dat de twee relaties zijn gemaakt.

Houd er rekening mee dat Azure Digital Twins niet in staat is om een relatie te maken als er al een andere relatie met dezelfde id bestaat, dus als u het programma meerdere keren uitvoert, worden er tijdens het maken van relaties uitzonderingen weergegeven. Met deze code worden de uitzonderingen onderschept en genegeerd. 

### <a name="list-relationships"></a>Lijst met relaties

Met de volgende code die u toevoegt, kunt u de lijst met relaties zien die u hebt gemaakt.

Voeg de volgende **nieuwe methode** toe aan de `Program`-klasse:

```csharp
public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
{
    try {
        AsyncPageable<BasicRelationship> results = client.GetRelationshipsAsync<BasicRelationship>(srcId);
        Console.WriteLine($"Twin {srcId} is connected to:");
        await foreach (BasicRelationship rel in results)
        {
            Console.WriteLine($" -{rel.Name}->{rel.TargetId}");
        }
    } catch (RequestFailedException rex) {
        Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
    }
}
```

Voeg vervolgens de volgende code toe aan het einde van de `Main`-methode om de `ListRelationships`-code aan te roepen:

```csharp
//List the relationships
await ListRelationships(client, "sampleTwin-0");
```

Voer in het opdrachtvenster de programma uit met `dotnet run`. Als het goed is, ziet u nu een lijst met alle relaties die u hebt gemaakt in een uitvoerinstructie die er als volgt uitziet:

:::image type="content" source= "media/tutorial-code/list-relationships.png" alt-text="Programma-uitvoer, waarin een bericht wordt weergegeven met de tekst 'Twin sampleTwin-0 is verbonden met: contains->sampleTwin-1, -contains->sampleTwin-2'":::

### <a name="query-digital-twins"></a>Query's uitvoeren op digitale tweelingen

Een hoofdfunctie van Azure Digital Twins is de mogelijkheid om gemakkelijk en efficiënt [query's](concepts-query-language.md) uit te voeren op uw tweelinggrafiek om vragen over uw omgeving te beantwoorden.

In de laatste sectie van de code die in deze zelfstudie moet worden toegevoegd, wordt een query uitgevoerd op het Azure Digital Twins-exemplaar. Met de query die in dit voorbeeld wordt gebruikt, worden alle digitale tweelingen in het exemplaar geretourneerd.

Voeg deze `using`-instructie toe om het gebruik van de `JsonSerializer`-klasse in te schakelen om de informatie over de digitale tweeling te presenteren:

```csharp
using System.Text.Json;
```

Voeg vervolgens de volgende code aan het einde van de `Main`-methode toe:

```csharp
// Run a query for all twins   
string query = "SELECT * FROM digitaltwins";
AsyncPageable<BasicDigitalTwin> result = client.QueryAsync<BasicDigitalTwin>(query);

await foreach (BasicDigitalTwin twin in result)
{
    Console.WriteLine(JsonSerializer.Serialize(twin));
    Console.WriteLine("---------------");
}
```

Voer in het opdrachtvenster de programma uit met `dotnet run`. In de uitvoer ziet u alle digitale tweelingen in dit exemplaar.

## <a name="complete-code-example"></a>Volledig voorbeeld van de code

Op dit punt in de zelfstudie hebt u een volledige client-app, waarmee u basisacties kunt uitvoeren op Azure Digital Twins. Ter referentie wordt de volledige code van het programma in *Program.cs* hieronder weergegeven:

```csharp
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using System.Text.Json;

namespace minimal
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");
            
            string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>"; 
            
            var credential = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
            Console.WriteLine($"Service client created – ready to go");

            Console.WriteLine();
            Console.WriteLine($"Upload a model");
            var typeList = new List<string>();
            string dtdl = File.ReadAllText("SampleModel.json");
            typeList.Add(dtdl);

            // Upload the model to the service
            try {
                await client.CreateModelsAsync(typeList);
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
            }
            // Read a list of models back from the service
            Console.WriteLine("Models uploaded to the instance:");
            AsyncPageable<DigitalTwinsModelData> modelDataList = client.GetModelsAsync();
            await foreach (DigitalTwinsModelData md in modelDataList)
            {
                Console.WriteLine($"{md.Id}");
            }

            // Initialize twin data
            BasicDigitalTwin twinData = new BasicDigitalTwin();
            twinData.Metadata.ModelId = "dtmi:example:SampleModel;1";
            twinData.Contents.Add("data", $"Hello World!");
            
            string prefix="sampleTwin-";
            for(int i=0; i<3; i++) {
                try {
                    twinData.Id = $"{prefix}{i}";
                    await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(twinData.Id, twinData);
                    Console.WriteLine($"Created twin: {prefix}{i}");
                } catch(RequestFailedException rex) {
                    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
                }
            }

            // Connect the twins with relationships
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");

            //List the relationships
            await ListRelationships(client, "sampleTwin-0");

            // Run a query for all twins   
            string query = "SELECT * FROM digitaltwins";
            AsyncPageable<BasicDigitalTwin> result = client.QueryAsync<BasicDigitalTwin>(query);
            
            await foreach (BasicDigitalTwin twin in result)
            {
                Console.WriteLine(JsonSerializer.Serialize(twin));
                Console.WriteLine("---------------");
            }
        }

        public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
        {
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = "contains"
            };
        
            try
            {
                string relId = $"{srcId}-contains->{targetId}";
                await client.CreateOrReplaceRelationshipAsync(srcId, relId, relationship);
                Console.WriteLine("Created relationship successfully");
            }
            catch (RequestFailedException rex) {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
        }
        
        public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
        {
            try {
                AsyncPageable<BasicRelationship> results = client.GetRelationshipsAsync<BasicRelationship>(srcId);
                Console.WriteLine($"Twin {srcId} is connected to:");
                await foreach (BasicRelationship rel in results)
                {
                    Console.WriteLine($" -{rel.Name}->{rel.TargetId}");
                }
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
            }
        }

    }
}
```
## <a name="clean-up-resources"></a>Resources opschonen
 
Het exemplaar dat in deze zelfstudie wordt gebruikt, kan opnieuw worden gebruikt in de volgende zelfstudie, [*Zelfstudie: De basisbeginselen verkennen met een voorbeeldclient-app*](tutorial-command-line-app.md). Als u van plan bent om door te gaan naar de volgende zelfstudie, kunt u het Azure Digital Twins-exemplaar dat u hier instelt, behouden.
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Verwijder ten slotte de projectmap die u hebt gemaakt op uw lokale machine.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een volledig nieuwe .NET-console-clienttoepassing gemaakt. U hebt code voor deze client-app geschreven om de basisacties uit te voeren op een Azure Digital Twins-exemplaar.

Ga verder met de volgende zelfstudie om de dingen te bekijken die u kunt doen met een dergelijke voorbeeld-client-app: 

> [!div class="nextstepaction"]
> [*Zelfstudie: De basisbeginselen verkennen met een voorbeeldclient-app*](tutorial-command-line-app.md)

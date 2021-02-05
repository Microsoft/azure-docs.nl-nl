---
title: 'Zelfstudie: Een client-app coderen'
titleSuffix: Azure Digital Twins
description: Zelfstudie voor het schrijven van de minimale code voor een client-app, met behulp van de .NET ( C# ) SDK.
author: baanders
ms.author: baanders
ms.date: 11/02/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 4851d06ffedaacb441d28cae24d7d32bfe1c611c
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576258"
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

Vervolgens voegt u **twee afhankelijkheden toe aan uw project** die nodig zijn om te werken met Azure Digital Twins. De eerste bestaat uit het pakket voor [Azure Digital Twins SDK voor .NET](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true), de tweede biedt hulpmiddelen voor de verificatie met Azure.

```cmd/sh
dotnet add package Azure.DigitalTwins.Core
dotnet add package Azure.Identity
```

## <a name="get-started-with-project-code"></a>Aan de slag met projectcode

In deze sectie gaat u beginnen met het schrijven van de code voor uw nieuwe app-project om met Azure Digital Twins te werken. De behandelde acties zijn onder meer:
* Verificatie op basis van de service
* Een model uploaden
* Fouten waarnemen
* Digitale tweelingen maken
* Relaties maken
* Query uitvoeren voor digitale tweeling

Er is ook een sectie met de volledige code aan het einde van de zelfstudie. U kunt dit als referentie gebruiken om uw programma te controleren terwijl u dit doet.

Open het bestand *Program.cs* in een code-editor om te beginnen. Er wordt een sjabloon met minimale code weergegeven die er ongeveer als volgt uitziet:

:::row:::
    :::column:::
        :::image type="content" source="media/tutorial-code/starter-template.png" alt-text="Een fragment met voorbeeldcode. Er is een ' using System; '-instructie, een naamruimte met de naam DigitalTwinsCodeTutorial; een klasse in de naamruimte met de naam Program en een hoofdmethode in de klasse met een standaard handtekening van 'static void Main(string[] args)'. De hoofdmethode bevat een Hallo wereld-printinstructie." lightbox="media/tutorial-code/starter-template.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Voeg eerst enkele `using` regels toe boven aan de code om de benodigde afhankelijkheden op te halen.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Azure_Digital_Twins_dependencies":::

Vervolgens voegt u code toe aan dit bestand om een deel van de functionaliteit in te vullen. 

### <a name="authenticate-against-the-service"></a>Verificatie op basis van de service

Het eerste wat uw app moet doen, is verifiëren op basis van de Azure Digital Twins-service. Vervolgens kunt u een service-clientklasse maken voor toegang tot de SDK-functies.

Voor de verificatie hebt u de *hostName*-waarde van uw Azure Digital Twins-exemplaar nodig.

Plak de volgende code in *Program.cs* onder de regel "Hello World!" in de `Main` methode. Stel de waarde van `adtInstanceUrl` in op die van *hostName* van uw exemplaar van Azure Digital Twins.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Authentication_code":::

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

:::code language="json" source="~/digital-twins-docs-samples/models/SampleModel.json":::

> [!TIP]
> Als u Visual Studio voor deze zelfstudie gebruikt, wilt u mogelijk het zojuist gemaakte JSON-bestand selecteren en de eigenschap *Kopiëren naar uitvoermap* instellen in de Eigenschappencontrole naar te *Kopiëren als nieuwer* of *Altijd kopiëren*. Hierdoor kan Visual Studio het JSON-bestand met het standaardpad vinden wanneer u het programma uitvoert met **F5** tijdens de rest van de zelfstudie.

> [!TIP] 
> Er is een taalagnostisch [DTDL-validatorvoorbeeld](/samples/azure-samples/dtdl-validator/dtdl-validator) dat u kunt gebruiken om modeldocumenten te controleren en u ervan te verzekeren dat de DTDL geldig is. Het voorbeeld is gemaakt op basis van de DTDL-parserbibliotheek, waarover u meer kunt lezen in [*Instructies: Modellen parseren en valideren*](how-to-parse-models.md).

Voeg vervolgens wat meer code toe aan *Program.cs* om het model dat u zojuist hebt gemaakt, te uploaden naar uw Azure Digital Twins-exemplaar.

Voer eerst boven aan het bestand de volgende `using`-instructies toe:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Model_dependencies":::

Vervolgens bereidt u het gebruik van de asynchrone methoden in de C# service-SDK voor door de handtekening van de `Main` methode te wijzigen zodat asynchrone uitvoering kan worden uitgevoerd. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Async_signature":::

> [!NOTE]
> Het gebruik van `async` is niet strikt vereist, omdat de SDK ook synchrone versies van alle aanroepen biedt. In deze zelfstudie gebruikt `async`.

Vervolgens wordt de eerste bit van code geleverd die samenwerkt met de Azure Digital Twins-service. Met deze code wordt het DTDL-bestand dat u hebt gemaakt op basis van de schijf, geladen en vervolgens geüpload naar uw Azure Digital Twins-service-exemplaar. 

Plak de volgende code onder de autorisatiecode die u eerder hebt toegevoegd.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp_excerpt_model.cs":::

Voer in het opdrachtvenster de programma uit met de volgende opdracht: 

```cmd/sh
dotnet run
```
‘Een model uploaden’ wordt afgedrukt in de uitvoer, om aan te geven dat deze code is bereikt, maar er nog geen uitvoer is om aan te geven of het uploaden is geslaagd.

Als u een afdrukinstructie wilt toevoegen waarmee alle modellen worden weergegeven die zijn geüpload, voegt u de volgende code toe, direct na de vorige sectie:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Print_model":::

**Voordat u het programma opnieuw uitvoert om deze nieuwe code te testen**, moet u even terug denken aan de laatste keer dat u het programma hebt uitgevoerd. U hebt uw model toen al geüpload. Met Azure Digital Twins kunt u hetzelfde model niet twee keer uploaden, dus als u probeert hetzelfde model nogmaals te uploaden, moet het programma een uitzondering genereren.

Voer, met dit in gedachten, in het opdrachtvenster het programma uit met de volgende opdracht:

```cmd/sh
dotnet run
```

Het programma moet een uitzondering genereren. Wanneer u probeert een model te uploaden dat al is geüpload, retourneert de service de fout "ongeldige aanvraag" via de REST API. Als gevolg hiervan zal de Azure Digital Twins-client SDK een uitzondering genereren voor elke serviceretourcode die niet geslaagd is. 

In de volgende sectie vindt u informatie over uitzonderingen zoals deze en hoe u deze in uw code kunt verwerken.

### <a name="catch-errors"></a>Fouten detecteren

Als u wilt voorkomen dat het programma vastloopt, kunt u uitzonderingscode toevoegen rond de code voor het uploaden van het model. Verpak de bestaande clientaanroep `await client.CreateModelsAsync(typeList)` in een try/catch-handler, zoals:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Model_try_catch":::

Als u het programma nu uitvoert met `dotnet run` in uw opdrachtvenster, ziet u dat er een foutcode wordt weergegeven. De uitvoer van de code voor het maken van het model laat deze fout zien:

:::image type="content" source= "media/tutorial-code/model-error.png" alt-text="Programma-uitvoer, waarin een bericht wordt weergegeven met de tekst '409:Serviceaanvraag is mislukt. Status: 409 (Conflict).', gevolgd door een weergave van de fout die aangeeft dat dtmi:example:SampleModel;1 al bestaat":::

Vanaf dit punt verstuurt de zelfstudie alle aanroepen naar servicemethoden in try/catch-handlers.

### <a name="create-digital-twins"></a>Digitale tweelingen maken

Nu u een model naar Azure Digital Twins hebt geüpload, kunt u deze modeldefinitie gebruiken voor het maken van **digitale tweelingen**. [Digitale tweelingen](concepts-twins-graph.md) zijn exemplaren van een model en vertegenwoordigen de entiteiten in uw bedrijfsomgeving: dingen als sensoren op een boerderij, ruimten in een gebouw, of lichten in een auto. In deze sectie wordt een aantal digitale tweelingen gemaakt op basis van het model dat u eerder hebt geüpload.

Voeg de volgende code toe aan het einde van de methode `Main` om drie digitale tweelingen te maken en initialiseren op basis van dit model.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Initialize_twins":::

Voer in het opdrachtvenster de programma uit met `dotnet run`. Zoek in de uitvoer naar de weergaveberichten die melden dat *sampleTwin-0*, *sampleTwin-1* en *sampleTwin-2* zijn gemaakt. 

Voer vervolgens het programma opnieuw uit. 

U ziet dat er geen fout wordt gegenereerd wanneer de tweelingen de tweede keer worden gemaakt, zelfs als tweeling al bestaan na de eerste uitvoering. In tegenstelling tot het maken van een model, is het maken van tweelingen op het REST-niveau een *PUT*-aanroep met *upsert*-semantiek. Dit betekent dat als er al een tweeling bestaat, er door een poging om dezelfde tweeling nog een keer te maken, de oorspronkelijke tweeling gewoon wordt vervangen. Er wordt geen fout gegenereerd.

### <a name="create-relationships"></a>Relaties maken

Vervolgens kunt u **relaties** maken tussen deze gemaakte tweelingen, om ze te verbinden in een **tweelinggrafiek**. [Tweelinggrafieken](concepts-twins-graph.md) worden gebruikt om uw gehele omgeving voor te stellen.

Voeg vervolgens een **nieuwe statische methode** toe aan de `Program`-klasse, onder de `Main`-methode (de code bevat nu twee methodes):

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Create_relationship":::

Voeg vervolgens de volgende code toe aan het einde van de `Main`-methode om de `CreateRelationship`-methode aan te roepen en gebruik de code die u zojuist hebt geschreven:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Use_create_relationship":::

Voer in het opdrachtvenster de programma uit met `dotnet run`. Zoek in de uitvoer naar afdrukinstructies, waarin wordt gemeld dat de twee relaties zijn gemaakt.

Houd er rekening mee dat Azure Digital Twins niet in staat is om een relatie te maken als er al een andere relatie met dezelfde id bestaat, dus als u het programma meerdere keren uitvoert, worden er tijdens het maken van relaties uitzonderingen weergegeven. Met deze code worden de uitzonderingen onderschept en genegeerd. 

### <a name="list-relationships"></a>Lijst met relaties

Met de volgende code die u toevoegt, kunt u de lijst met relaties zien die u hebt gemaakt.

Voeg de volgende **nieuwe methode** toe aan de `Program`-klasse:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="List_relationships":::

Voeg vervolgens de volgende code toe aan het einde van de `Main`-methode om de `ListRelationships`-code aan te roepen:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Use_list_relationships":::

Voer in het opdrachtvenster de programma uit met `dotnet run`. Als het goed is, ziet u nu een lijst met alle relaties die u hebt gemaakt in een uitvoerinstructie die er als volgt uitziet:

:::image type="content" source= "media/tutorial-code/list-relationships.png" alt-text="Programma-uitvoer, waarin een bericht wordt weergegeven met de tekst 'Twin sampleTwin-0 is verbonden met: contains->sampleTwin-1, -contains->sampleTwin-2'" lightbox="media/tutorial-code/list-relationships.png":::

### <a name="query-digital-twins"></a>Query's uitvoeren op digitale tweelingen

Een hoofdfunctie van Azure Digital Twins is de mogelijkheid om gemakkelijk en efficiënt [query's](concepts-query-language.md) uit te voeren op uw tweelinggrafiek om vragen over uw omgeving te beantwoorden.

In de laatste sectie van de code die in deze zelfstudie moet worden toegevoegd, wordt een query uitgevoerd op het Azure Digital Twins-exemplaar. Met de query die in dit voorbeeld wordt gebruikt, worden alle digitale tweelingen in het exemplaar geretourneerd.

Voeg deze `using`-instructie toe om het gebruik van de `JsonSerializer`-klasse in te schakelen om de informatie over de digitale tweeling te presenteren:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Query_dependencies":::

Voeg vervolgens de volgende code aan het einde van de `Main`-methode toe:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Query_twins":::

Voer in het opdrachtvenster de programma uit met `dotnet run`. In de uitvoer ziet u alle digitale tweelingen in dit exemplaar.

## <a name="complete-code-example"></a>Volledig voorbeeld van de code

Op dit punt in de zelfstudie hebt u een volledige client-app, waarmee u basisacties kunt uitvoeren op Azure Digital Twins. Ter referentie wordt de volledige code van het programma in *Program.cs* hieronder weergegeven:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs":::

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u deze zelf studie hebt voltooid, kunt u kiezen welke resources u wilt verwijderen, afhankelijk van wat u nu wilt doen.

* **Als u van plan bent om door te gaan naar de volgende zelf studie**, kan de instantie die in deze zelf studie wordt gebruikt, opnieuw worden gebruikt in de volgende. U kunt de Azure Digital Apparaatdubbels-resources die u hier instelt, houden en de rest van deze sectie overs Laan.

[!INCLUDE [digital-twins-cleanup-clear-instance.md](../../includes/digital-twins-cleanup-clear-instance.md)]
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Misschien wilt u ook de projectmap van uw lokale computer verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een volledig nieuwe .NET-console-clienttoepassing gemaakt. U hebt code voor deze client-app geschreven om de basisacties uit te voeren op een Azure Digital Twins-exemplaar.

Ga verder met de volgende zelfstudie om de dingen te bekijken die u kunt doen met een dergelijke voorbeeld-client-app: 

> [!div class="nextstepaction"]
> [*Zelfstudie: De basisbeginselen verkennen met een voorbeeldclient-app*](tutorial-command-line-app.md)

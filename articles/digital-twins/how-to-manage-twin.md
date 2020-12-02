---
title: Digitale tweelingen beheren
titleSuffix: Azure Digital Twins
description: Zie afzonderlijke apparaatdubbels en relaties ophalen, bijwerken en verwijderen.
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: ba444a497fa4fccab6b8dec1fadb3383420e4d49
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452968"
---
# <a name="manage-digital-twins"></a>Digitale tweelingen beheren

Entiteiten in uw omgeving worden vertegenwoordigd door [Digital apparaatdubbels](concepts-twins-graph.md). Het beheren van uw digitale apparaatdubbels kan het maken, wijzigen en verwijderen omvatten. Als u deze bewerkingen wilt uitvoeren, kunt u de [**DigitalTwins-api's**](/rest/api/digital-twins/dataplane/twins), de [.net (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)of de [Azure Digital apparaatdubbels cli](how-to-use-cli.md)gebruiken.

Dit artikel richt zich op het beheren van digitale apparaatdubbels; Zie [*How-to: manage the dubbele Graph with relationships*](how-to-manage-graph.md)als u wilt werken met relaties en de [dubbele grafiek](concepts-twins-graph.md) als geheel.

> [!TIP]
> Alle SDK-functies zijn beschikbaar in synchrone en asynchrone versies.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-twins"></a>Manieren om apparaatdubbels te beheren

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

## <a name="create-a-digital-twin"></a>Een digitale dubbele

Als u een dubbele wilt maken, gebruikt u de `CreateOrReplaceDigitalTwinAsync()` methode op de service-client als volgt:

```csharp
await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>("myTwinId", initData);
```

Als u een digitale dubbele wilt maken, moet u het volgende opgeven:
* De gewenste ID voor de digitale dubbele
* Het [model](concepts-models.md) dat u wilt gebruiken

U kunt desgewenst initiële waarden opgeven voor alle eigenschappen van de digitale twee. Eigenschappen worden beschouwd als optioneel en kunnen later worden ingesteld, maar **ze worden niet weer gegeven als onderdeel van een dubbele tot ze zijn ingesteld.**

>[!NOTE]
>Hoewel twee eigenschappen niet hoeven te worden geïnitialiseerd, moeten [onderdelen](concepts-models.md#elements-of-a-model) op de dubbele **taak** worden ingesteld wanneer het dubbele is gemaakt. Ze kunnen lege objecten zijn, maar de onderdelen zelf moeten aanwezig zijn.

Het model en alle initiële eigenschaps waarden worden gegeven via de `initData` para meter, een JSON-teken reeks met de relevante gegevens. Ga verder naar de volgende sectie voor meer informatie over het structureren van dit object.

> [!TIP]
> Na het maken of bijwerken van een dubbele, kan er een latentie van Maxi maal 10 seconden zijn voordat de wijzigingen in [query's](how-to-query-graph.md)worden weer gegeven. De `GetDigitalTwin` API ( [verderop in dit artikel](#get-data-for-a-digital-twin)) heeft deze vertraging niet, dus als u een direct antwoord nodig hebt, gebruikt u de API-aanroep in plaats van query's om de zojuist gemaakte apparaatdubbels te zien. 

### <a name="initialize-model-and-properties"></a>Model en eigenschappen initialiseren

U kunt de eigenschappen van een twee initialiseren op het moment dat de dubbele wordt gemaakt. 

De twee keer dat er een API wordt gemaakt, accepteert een object dat is geserialiseerd in een geldige JSON-beschrijving van de dubbele eigenschappen. Zie [*concepten: Digital apparaatdubbels en de dubbele grafiek*](concepts-twins-graph.md) voor een beschrijving van de JSON-indeling voor een dubbele. 

Eerst kunt u een gegevens object maken om de dubbele en de bijbehorende eigenschaps gegevens weer te geven. U kunt een parameter object hand matig of via een beschik bare helper-klasse maken. Hier volgt een voor beeld van elk.

#### <a name="create-twins-using-manually-created-data"></a>Apparaatdubbels maken met hand matig gemaakte gegevens

Als u geen aangepaste helper-klassen wilt gebruiken, kunt u de eigenschappen van een twee voor `Dictionary<string, object>` waarden in een, waarbij de de `string` naam van de eigenschap is en de `object` is een-object dat de eigenschap en de waarde vertegenwoordigt.

[!INCLUDE [Azure Digital Twins code: create twin](../../includes/digital-twins-code-create-twin.md)]

#### <a name="create-twins-with-the-helper-class"></a>Apparaatdubbels maken met de helper-klasse

Met de Help-klasse van `BasicDigitalTwin` kunt u eigenschaps velden rechtstreeks in een ' twee ' object opslaan. Het is mogelijk dat u de lijst met eigenschappen wilt maken met behulp van een `Dictionary<string, object>` , die vervolgens rechtstreeks aan het dubbele object kan worden toegevoegd `CustomProperties` .

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
props.Add("Humidity", 50.0);
twin.Contents = props;

client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>("myRoomId", twin);
Console.WriteLine("The twin is created successfully");
```

>[!NOTE]
> `BasicDigitalTwin` objecten worden geleverd met een `Id` veld. U kunt dit veld leeg laten, maar als u een ID-waarde toevoegt, moet deze overeenkomen met de ID-para meter die is door gegeven aan de `CreateOrReplaceDigitalTwinAsync()` aanroep. Bijvoorbeeld:
>
>```csharp
>twin.Id = "myRoomId";
>```

## <a name="get-data-for-a-digital-twin"></a>Gegevens ophalen voor een digitale dubbele

U kunt toegang krijgen tot de gegevens van een digitale dubbele manier door de methode als volgt aan te roepen `GetDigitalTwin()` :

```csharp
object result = await client.GetDigitalTwin(id);
```
Deze aanroep retourneert dubbele gegevens als een sterk getypeerd object type, zoals `BasicDigitalTwin` . `BasicDigitalTwin` is een serialisatiefunctie voor serialisatie die deel uitmaakt van de SDK, waardoor de belangrijkste dubbele meta gegevens en eigenschappen in een vooraf geparseerd formulier worden geretourneerd. Hier volgt een voor beeld van hoe u dit kunt gebruiken om dubbele details te bekijken:

```csharp
Response<BasicDigitalTwin> twin = client.GetDigitalTwin("myRoomId");
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.Contents.Keys)
{
  if (twin.Contents.TryGetValue(prop, out object value))
  Console.WriteLine($"Property '{prop}': {value}");
}
```
Alleen eigenschappen die ten minste eenmaal zijn ingesteld, worden geretourneerd wanneer u een dubbele waarde ophaalt met de- `GetDigitalTwin()` methode.

>[!TIP]
>De `displayName` for a-server maakt deel uit van de meta gegevens van het model en wordt dus niet weer gegeven bij het ophalen van gegevens voor het dubbele exemplaar. Als u deze waarde wilt zien, kunt u [deze uit het model ophalen](how-to-manage-model.md#retrieve-models).

Als u meerdere apparaatdubbels met één API-aanroep wilt ophalen, raadpleegt u de query-API-voor beelden in [*de instructies: Query's uitvoeren op de dubbele grafiek*](how-to-query-graph.md).

Bekijk het volgende model (geschreven in [Digital Apparaatdubbels Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL)) dat een *maan* definieert:

```json
{
    "@id": "dtmi:example:Moon;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "contents": [
        {
            "@type": "Property",
            "name": "radius",
            "schema": "double",
            "writable": true
        },
        {
            "@type": "Property",
            "name": "mass",
            "schema": "double",
            "writable": true
        }
    ]
}
```
Het resultaat van het aanroepen van `object result = await client.GetDigitalTwinAsync("my-moon");` een *maan*-type van twee kan er als volgt uitzien:

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:example:Moon;1",
    "radius": {
      "desiredValue": 1737.1,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "mass": {
      "desiredValue": 0.0734,
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

De gedefinieerde eigenschappen van de digitale twee worden geretourneerd als eigenschappen op het hoogste niveau van de digitale twee. Meta gegevens of systeem gegevens die geen deel uitmaken van de DTDL-definitie, worden geretourneerd met een `$` voor voegsel. Eigenschappen van meta gegevens zijn onder andere:
* De ID van de digitale dubbele in dit Azure Digital Apparaatdubbels-exemplaar, zoals `$dtId` .
* `$etag`, een standaard-HTTP-veld dat door de webserver wordt toegewezen.
* Andere eigenschappen in een `$metadata` sectie. Deze omvatten:
    - De DTMI van het model van de digitale twee.
    - Synchronisatie status voor elke Beschrijf bare eigenschap. Dit is vooral nuttig voor apparaten, waar het mogelijk is dat de service en het apparaat afwijkende statussen hebben (bijvoorbeeld wanneer een apparaat offline is). Deze eigenschap is op dit moment alleen van toepassing op fysieke apparaten die zijn verbonden met IoT Hub. Met de gegevens in de sectie meta gegevens is het mogelijk inzicht te krijgen in de volledige status van een eigenschap, evenals de laatste gewijzigde tijds tempels. Zie voor meer informatie over de synchronisatie status [deze IOT hub zelf studie over het](../iot-hub/tutorial-device-twins.md) synchroniseren van de Apparaatstatus.
    - Servicespecifieke meta gegevens, zoals van IoT Hub of Azure Digital Apparaatdubbels. 

Meer informatie over de serialisatie helper-klassen vindt u `BasicDigitalTwin` in de [*instructies: gebruik de Azure Digital apparaatdubbels Api's en sdk's*](how-to-use-apis-sdks.md).

## <a name="view-all-digital-twins"></a>Alle digitale apparaatdubbels weer geven

Als u alle digitale apparaatdubbels in uw exemplaar wilt weer geven, gebruikt u een [query](how-to-query-graph.md). U kunt een query uitvoeren met de [query-api's](/rest/api/digital-twins/dataplane/query) of de [cli-opdrachten](how-to-use-cli.md).

Dit is de hoofd tekst van de basis query waarmee een lijst met alle digitale apparaatdubbels in het exemplaar wordt geretourneerd:

```sql
SELECT *
FROM DIGITALTWINS
``` 

## <a name="update-a-digital-twin"></a>Een digital twin bijwerken

Als u de eigenschappen van een digitale dubbele gegevens wilt bijwerken, schrijft u de informatie die u wilt vervangen in de indeling van de [JSON-patch](http://jsonpatch.com/) . Op deze manier kunt u meerdere eigenschappen tegelijk vervangen. Vervolgens geeft u het JSON-patch document door aan een `UpdateDigitalTwin()` methode:

```csharp
await client.UpdateDigitalTwin(id, patch);
```

Een patch-oproep kan zoveel eigenschappen op een enkele dubbele manier bijwerken als u wilt (zelfs al deze). Als u eigenschappen voor meerdere apparaatdubbels wilt bijwerken, hebt u een afzonderlijke update aanroep voor elke dubbele taak nodig.

> [!TIP]
> Na het maken of bijwerken van een dubbele, kan er een latentie van Maxi maal 10 seconden zijn voordat de wijzigingen in [query's](how-to-query-graph.md)worden weer gegeven. De `GetDigitalTwin` API ( [eerder in dit artikel](#get-data-for-a-digital-twin)beschreven) heeft deze vertraging niet. Gebruik daarom de API-aanroep in plaats van een query uit te voeren om de zojuist bijgewerkte apparaatdubbels te zien als u een direct antwoord nodig hebt. 

Hier volgt een voor beeld van de JSON-patch code. Dit document vervangt de *massa* *-en RADIUS-* eigenschaps waarden van de digitale dubbele waarde die wordt toegepast op.

```json
[
  {
    "op": "replace",
    "path": "/mass",
    "value": 0.0799
  },
  {
    "op": "replace",
    "path": "/radius",
    "value": 0.800
  }
]
```
U kunt patches maken met behulp `JsonPatchDocument` van een in de [SDK](how-to-use-apis-sdks.md). Hier volgt een voorbeeld.

```csharp
var updateTwinData = new JsonPatchDocument();
updateTwinData.AppendAddOp("/Temperature", temperature.Value<double>());
await client.UpdateDigitalTwinAsync(twin_Id, updateTwinData);
```

### <a name="update-properties-in-digital-twin-components"></a>Eigenschappen in digitale dubbele onderdelen bijwerken

Intrekken dat een model onderdelen kan bevatten, zodat het kan bestaan uit andere modellen. 

Als u de eigenschappen van een digitale twee onderdelen wilt bijwerken, kunt u syntaxis voor paden gebruiken in JSON-patch:

```json
[
  {
    "op": "replace",
    "path": "/mycomponentname/mass",
    "value": 0.0799
  }
]
```

### <a name="update-a-digital-twins-model"></a>Een digitaal onderliggend model bijwerken

De `UpdateDigitalTwin()` functie kan ook worden gebruikt om een digitale dubbele naar een ander model te migreren. 

Bekijk bijvoorbeeld het volgende document van de JSON-patch dat het meta gegevens veld van de digitale twee vervangt `$model` :

```json
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:example:foo;1"
  }
]
```

Deze bewerking slaagt alleen als de digitale dubbele wijziging door de patch voldoet aan het nieuwe model. 

Kijk eens naar het volgende voorbeeld:
1. Stel dat een digitale twee maal een model van *foo_old*. *foo_old* definieert een vereiste eigenschaps *massa*.
2. Het nieuwe model *foo_new* definieert een eigenschaps massa en voegt een nieuwe vereiste *temperatuur* van de eigenschap toe.
3. Na de patch moet de digitale twee eigenschappen een massa-en temperatuur eigenschap hebben. 

Voor de patch voor deze situatie moet het model en de eigenschap Tempe ratuur van twee zaken worden bijgewerkt, zoals:

```json
[
  {
    "op": "replace",
    "path": "$metadata.$model",
    "value": "dtmi:example:foo_new"
  },
  {
    "op": "add",
    "path": "temperature",
    "value": 60
  }
]
```

### <a name="handle-conflicting-update-calls"></a>Conflicterende Update aanroepen verwerken

Azure Digital Apparaatdubbels zorgt ervoor dat alle inkomende aanvragen na elkaar worden verwerkt. Dit betekent dat zelfs als meerdere functies dezelfde eigenschap op een twee keer proberen bij te werken, u geen expliciete vergrendelings code **hoeft** te schrijven om het conflict af te handelen.

Dit gedrag bevindt zich op twee afzonderlijke basis. 

Stel dat er een scenario is waarin deze drie aanroepen gelijktijdig aankomen: 
*   Eigenschap A op *Twin1* schrijven
*   Schrijf eigenschap B op *Twin1*
*   Eigenschap A op *Twin2* schrijven

De twee aanroepen die *Twin1* wijzigen, worden na elkaar uitgevoerd en er worden wijzigings berichten voor elke wijziging gegenereerd. De aanroep om *Twin2* te wijzigen kan gelijktijdig zonder conflict worden uitgevoerd, zodra deze is binnengekomen.

## <a name="delete-a-digital-twin"></a>Een digitale dubbele

U kunt apparaatdubbels verwijderen met behulp van de- `DeleteDigitalTwin()` methode. U kunt echter alleen een dubbele waarde verwijderen als deze geen relaties meer heeft. Verwijder de dubbele en uitgaande relaties eerst.

Hier volgt een voor beeld van de code voor het verwijderen van apparaatdubbels en hun relaties:

```csharp
static async Task DeleteTwin(string id)
{
    await FindAndDeleteOutgoingRelationshipsAsync(id);
    await FindAndDeleteIncomingRelationshipsAsync(id);
    try
    {
        await client.DeleteDigitalTwin(id);
    } catch (RequestFailedException exc)
    {
        Console.WriteLine($"*** Error:{exc.Error}/{exc.Message}");
    }
}

public async Task FindAndDeleteOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);

        await foreach (BasicRelationship rel in rels)
        {
            await client.DeleteRelationshipAsync(dtId, rel.Id).ConfigureAwait(false);
            Log.Ok($"Deleted relationship {rel.Id} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
    }
}

async Task FindAndDeleteIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        await foreach (IncomingRelationship incomingRel in incomingRels)
        {
            await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
            Log.Ok($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"_*_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
    }
}
```
### <a name="delete-all-digital-twins"></a>Alle digitale apparaatdubbels verwijderen

Voor een voor beeld van het verwijderen van alle apparaatdubbels in een keer, downloadt u de voor beeld-app die wordt gebruikt in de [_Tutorial: Verken de basis principes met een voor beeld-client-app *](tutorial-command-line-app.md). Het *CommandLoop.cs* -bestand doet dit in een `CommandDeleteAllTwins()` functie.

## <a name="runnable-digital-twin-code-sample"></a>Voor beeld van uitvoer bare digitale dubbele code

U kunt het uitvoer bare-code voorbeeld hieronder gebruiken om een dubbele, bijwerkings gegevens te maken en de dubbele te verwijderen. 

### <a name="set-up-the-runnable-sample"></a>Het uitvoer bare-voor beeld instellen

Het fragment maakt gebruik van de [Room.jsop](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) model definitie uit [*zelf studie: Verken Azure Digital apparaatdubbels met een voor beeld-client-app*](tutorial-command-line-app.md). U kunt deze koppeling gebruiken om rechtstreeks naar het bestand te gaan of dit als onderdeel van het volledige end-to-end- [voorbeeld project te](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)downloaden.

Ga als volgt te werk voordat u het voor beeld uitvoert:
1. Down load het model bestand, plaats het in uw project en vervang de `<path-to>` tijdelijke aanduiding in de onderstaande code om uw programma te laten weten waar het zich bevindt.
2. Vervang de tijdelijke aanduiding door de `<your-instance-hostname>` hostnaam van uw Azure Digital apparaatdubbels-exemplaar.
3. Voeg deze pakketten toe aan uw project:
    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
    dotnet add package Azure.identity
    ```

U moet ook lokale referenties instellen als u het voor beeld rechtstreeks wilt uitvoeren. In de volgende sectie wordt dit uitgelegd.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Nadat u de bovenstaande stappen hebt voltooid, kunt u de volgende voorbeeld code rechtstreeks uitvoeren.

```csharp
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {

        public static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");

            //Create the Azure Digital Twins client for API calls
            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            Console.WriteLine($"Service client created – ready to go");
            Console.WriteLine();

            //Upload models
            Console.WriteLine($"Upload a model");
            Console.WriteLine();
            string dtdl = File.ReadAllText("<path-to>/Room.json");
            var typeList = new List<string>();
            typeList.Add(dtdl);
            // Upload the model to the service
            await client.CreateModelsAsync(typeList);

            //Create new digital twin
            BasicDigitalTwin twin = new BasicDigitalTwin();
            string twin_Id = "myRoomId";
            twin.Metadata = new DigitalTwinMetadata();
            twin.Metadata.ModelId = "dtmi:example:Room;1";
            // Initialize properties
            Dictionary<string, object> props = new Dictionary<string, object>();
            props.Add("Temperature", 35.0);
            props.Add("Humidity", 55.0);
            twin.Contents = props;
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(twin_Id, twin);
            Console.WriteLine("Twin created successfully");
            Console.WriteLine();

            //Print twin
            Console.WriteLine("--- Printing twin details:");
            twin = FetchAndPrintTwin(twin_Id, client);
            Console.WriteLine("--------");
            Console.WriteLine();

            //Update twin data
            var updateTwinData = new JsonPatchDocument();
            updateTwinData.AppendAdd("/Temperature", 25.0);
            await client.UpdateDigitalTwinAsync(twin_Id, updateTwinData);
            Console.WriteLine("Twin properties updated");
            Console.WriteLine();

            //Print twin again
            Console.WriteLine("--- Printing twin details (after update):");
            FetchAndPrintTwin(twin_Id, client);
            Console.WriteLine("--------");
            Console.WriteLine();

            //Delete twin
            await DeleteTwin(client, twin_Id);
        }

        private static BasicDigitalTwin FetchAndPrintTwin(string twin_Id, DigitalTwinsClient client)
        {
            BasicDigitalTwin twin;
            Response<BasicDigitalTwin> twin = client.GetDigitalTwin(twin_Id);
            Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
            foreach (string prop in twin.Contents.Keys)
            {
                if (twin.Contents.TryGetValue(prop, out object value))
                    Console.WriteLine($"Property '{prop}': {value}");
            }

            return twin;
        }
        private static async Task DeleteTwin(DigitalTwinsClient client, string id)
        {
            await FindAndDeleteOutgoingRelationshipsAsync(client, id);
            await FindAndDeleteIncomingRelationshipsAsync(client, id);
            try
            {
                await client.DeleteDigitalTwinAsync(id);
                Console.WriteLine("Twin deleted successfully");
            }
            catch (RequestFailedException exc)
            {
                Console.WriteLine($"*** Error:{exc.Message}");
            }
        }

        private static async Task FindAndDeleteOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin

            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);

                await foreach (BasicRelationship rel in rels)
                {
                    await client.DeleteRelationshipAsync(dtId, rel.Id).ConfigureAwait(false);
                    Console.WriteLine($"Deleted relationship {rel.Id} from {dtId}");
                }
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
            }
        }

       private static async Task FindAndDeleteIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin

            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
                    Console.WriteLine($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
                }
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"_*_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
            }
        }

    }
}

```
Hier volgt de console-uitvoer van het bovenstaande programma: 

:::image type="content" source="./media/how-to-manage-twin/console-output-manage-twins.png" alt-text="Console-uitvoer waarin wordt weer gegeven dat de dubbele is gemaakt, bijgewerkt en verwijderd" lightbox="./media/how-to-manage-twin/console-output-manage-twins.png":::

## <a name="next-steps"></a>Volgende stappen

Zie relaties tussen uw digitale apparaatdubbels maken en beheren: _ [ *instructies: de dubbele grafiek met relaties beheren*](how-to-manage-graph.md)
---
title: Uw gezichts gegevens migreren via abonnementen-gezicht
titleSuffix: Azure Cognitive Services
description: Deze hand leiding laat zien hoe u uw opgeslagen gezichts gegevens kunt migreren van het ene gezichts abonnement naar het andere.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: nitinme
ms.custom: devx-track-csharp
ms.openlocfilehash: ea2a127ffbf966b024322ab5a908466dec1cc8a9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935631"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Uw gezichts gegevens migreren naar een ander gezichts abonnement

In deze hand leiding wordt beschreven hoe u gezichts gegevens, zoals een opgeslagen PersonGroup-object met gezichten, kunt verplaatsen naar een ander Azure Cognitive Services Face-abonnement. Als u de gegevens wilt verplaatsen, gebruikt u de functie snap shot. Zo voor komt u dat u een PersonGroup-of FaceList-object herhaaldelijk kunt bouwen en trainen wanneer u uw bewerkingen verplaatst of uitbreidt. Mogelijk hebt u bijvoorbeeld een PersonGroup-object gemaakt met een gratis abonnement en wilt u dit nu migreren naar uw betaalde abonnement. Of u moet mogelijk gezichts gegevens synchroniseren tussen abonnementen in verschillende regio's voor een grote bedrijfs activiteit.

Deze migratie strategie is ook van toepassing op LargePersonGroup-en LargeFaceList-objecten. Als u niet bekend bent met de concepten in deze hand leiding, raadpleegt u de definities hiervan in de hand leiding voor de [gezichts herkennings concepten](../concepts/face-recognition.md) . In deze hand leiding wordt gebruikgemaakt van de face .NET-client bibliotheek met C#.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig:

- Twee gezichts abonnements sleutels, één met de bestaande gegevens en één om naar te migreren. Volg de instructies in [Create a cognitive Services account](../../cognitive-services-apis-create-account.md)om u te abonneren op de face-service en uw sleutel op te halen.
- De teken reeks voor het gezichts abonnement-ID die overeenkomt met het doel abonnement. Selecteer **overzicht** in het Azure Portal om het te vinden. 
- Een versie van [Visual Studio 2015 of 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Het Visual Studio-project maken

In deze hand leiding wordt gebruikgemaakt van een eenvoudige console-app voor het uitvoeren van de gezichts gegevens migratie. Zie voor een volledige implementatie het voor [beeld van het gezichts momentopname](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) op github.

1. Maak in Visual Studio een nieuwe console-app .NET Framework project. Noem deze **FaceApiSnapshotSample**.
1. Download de vereiste NuGet-pakketten. Klik met de rechter muisknop op het project in de Solution Explorer en selecteer **NuGet-pakketten beheren**. Selecteer het tabblad **Bladeren** en selecteer op **include Prerelease**. Het volgende pakket zoeken en installeren:
    - [Micro soft. Azure. CognitiveServices. Vision. Face 2.3.0-Preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>Face-clients maken

In de methode **Main** in *Program. cs* maakt u twee [FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) -instanties voor uw bron-en doel abonnementen. In dit voor beeld wordt een gezichts abonnement in de regio Azië-oost als bron en een West US-abonnement als doel gebruikt. In dit voor beeld ziet u hoe u gegevens migreert van de ene Azure-regio naar een andere. 

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

```csharp
var FaceClientEastAsia = new FaceClient(new ApiKeyServiceClientCredentials("<East Asia Subscription Key>"))
    {
        Endpoint = "https://southeastasia.api.cognitive.microsoft.com/>"
    };

var FaceClientWestUS = new FaceClient(new ApiKeyServiceClientCredentials("<West US Subscription Key>"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com/"
    };
```

Vul de abonnements sleutel waarden en eind punt-Url's in voor uw bron-en doel abonnementen.


## <a name="prepare-a-persongroup-for-migration"></a>Een PersonGroup voorbereiden voor migratie

U hebt de ID van de PersonGroup in uw bron abonnement nodig om deze te migreren naar het doel abonnement. Gebruik de methode [PersonGroupOperationsExtensions. ListAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync) om een lijst van uw PersonGroup-objecten op te halen. Haal vervolgens de eigenschap [PersonGroup. PersonGroupId](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) op. Dit proces ziet er anders uit op basis van de PersonGroup-objecten die u hebt. In deze hand leiding wordt de bron-PersonGroup-ID opgeslagen in `personGroupId` .

> [!NOTE]
> Met de [voorbeeld code](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) wordt een nieuwe PersonGroup gemaakt en getraind die kan worden gemigreerd. In de meeste gevallen moet u al een PersonGroup hebben om te gebruiken.

## <a name="take-a-snapshot-of-a-persongroup"></a>Een moment opname van een PersonGroup maken

Een moment opname is een tijdelijke externe opslag voor bepaalde gezichts gegevens typen. Het werkt als een soort klem bord om gegevens van het ene naar het andere abonnement te kopiëren. Eerst maakt u een moment opname van de gegevens in het bron abonnement. Vervolgens past u deze toe op een nieuw gegevens object in het doel abonnement.

Gebruik het FaceClient-exemplaar van het bron abonnement om een moment opname van de PersonGroup te maken. Gebruik [TakeAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync) met de PersonGroup-id en de id van het doel abonnement. Als u meerdere doel abonnementen hebt, voegt u deze toe als matrix vermeldingen in de derde para meter.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> Het proces van het nemen en Toep assen van moment opnamen verstoort geen normale aanroepen naar de bron-of doel-PersonGroups of-FaceLists. Maak geen gelijktijdige aanroepen waarmee het bron object wordt gewijzigd, zoals [FaceList-beheer aanroepen](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations) of de oproep van de [PersonGroup-training](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations) . De momentopname bewerking kan worden uitgevoerd vóór of na deze bewerkingen of kan fouten tegen komen.

## <a name="retrieve-the-snapshot-id"></a>De moment opname-ID ophalen

De methode die wordt gebruikt om moment opnamen te maken, is asynchroon, dus u moet wachten tot de bewerking is voltooid. Momentopname bewerkingen kunnen niet worden geannuleerd. In deze code bewaakt de `WaitForOperation` methode de asynchrone aanroep. Hiermee wordt de status elke 100 MS gecontroleerd. Wanneer de bewerking is voltooid, haalt u een bewerkings-ID op door het veld te parseren `OperationLocation` . 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Een typische `OperationLocation` waarde ziet er als volgt uit:

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

De `WaitForOperation` Help-methode is hier:

```csharp
/// <summary>
/// Waits for the take/apply operation to complete and returns the final operation status.
/// </summary>
/// <returns>The final operation status.</returns>
private static async Task<OperationStatus> WaitForOperation(IFaceClient client, Guid operationId)
{
    OperationStatus operationStatus = null;
    do
    {
        if (operationStatus != null)
        {
            Thread.Sleep(TimeSpan.FromMilliseconds(100));
        }

        // Get the status of the operation.
        operationStatus = await client.Snapshot.GetOperationStatusAsync(operationId);

        Console.WriteLine($"Operation Status: {operationStatus.Status}");
    }
    while (operationStatus.Status != OperationStatusType.Succeeded
            && operationStatus.Status != OperationStatusType.Failed);

    return operationStatus;
}
```

Na de bewerkings status wordt `Succeeded` de moment opname-id opgehaald door het `ResourceLocation` veld van het geretourneerde OperationStatus-exemplaar te parseren.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Een typische `resourceLocation` waarde ziet er als volgt uit:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>Een moment opname Toep assen op een doel abonnement

Maak vervolgens de nieuwe PersonGroup in het doel abonnement met behulp van een wille keurig gegenereerde ID. Gebruik vervolgens het FaceClient-exemplaar van het doel abonnement om de moment opname toe te passen op deze PersonGroup. Geef de moment opname-ID en de nieuwe PersonGroup-ID door.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Een momentopname object is slechts 48 uur geldig. Maak een moment opname alleen als u deze binnenkort wilt gebruiken voor gegevens migratie.

Een aanvraag voor het Toep assen van een moment opname retourneert een andere bewerking-ID. Als u deze ID wilt ophalen, parseert u het `OperationLocation` veld van het geretourneerde applySnapshotResult-exemplaar. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

Het momentopname toepassings proces is ook asynchroon, dus opnieuw gebruiken `WaitForOperation` om te wachten tot het is voltooid.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>De gegevens migratie testen

Nadat u de moment opname hebt toegepast, wordt de nieuwe PersonGroup in het doel abonnement gevuld met de oorspronkelijke gezichts gegevens. Standaard worden de resultaten van de training ook gekopieerd. De nieuwe PersonGroup is klaar voor het aanroepen van gezichts identificaties zonder opnieuw te hoeven trainen.

Als u de gegevens migratie wilt testen, voert u de volgende bewerkingen uit en vergelijkt u de resultaten die ze naar de console afdrukken:

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the PersonGroup before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

Gebruik de volgende Help-methoden:

```csharp
private static async Task DisplayPersonGroup(IFaceClient client, string personGroupId)
{
    var personGroup = await client.PersonGroup.GetAsync(personGroupId);
    Console.WriteLine("PersonGroup:");
    Console.WriteLine(JsonConvert.SerializeObject(personGroup));

    // List persons.
    var persons = await client.PersonGroupPerson.ListAsync(personGroupId);

    foreach (var person in persons)
    {
        Console.WriteLine(JsonConvert.SerializeObject(person));
    }

    Console.WriteLine();
}
```

```csharp
private static async Task IdentifyInPersonGroup(IFaceClient client, string personGroupId)
{
    using (var fileStream = new FileStream("data\\PersonGroup\\Daughter\\Daughter1.jpg", FileMode.Open, FileAccess.Read))
    {
        var detectedFaces = await client.Face.DetectWithStreamAsync(fileStream);

        var result = await client.Face.IdentifyAsync(detectedFaces.Select(face => face.FaceId.Value).ToList(), personGroupId);
        Console.WriteLine("Test identify against PersonGroup");
        Console.WriteLine(JsonConvert.SerializeObject(result));
        Console.WriteLine();
    }
}
```

U kunt nu de nieuwe PersonGroup gebruiken in het doel abonnement. 

Als u de doel-PersonGroup in de toekomst opnieuw wilt bijwerken, maakt u een nieuwe PersonGroup om de moment opname te ontvangen. Volg hiervoor de stappen in deze hand leiding. Een enkel PersonGroup-object kan een moment opname hebben die slechts één keer wordt toegepast.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het migreren van gezichts gegevens, moet u het momentopname object hand matig verwijderen.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>Volgende stappen

Bekijk vervolgens de relevante API-referentie documentatie, verken een voor beeld-app die gebruikmaakt van de snap shot-functie of volg een hand leiding om te beginnen met het gebruik van de andere API-bewerkingen die hier worden genoemd:

- [Naslag documentatie voor moment opnamen (.NET SDK)](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations)
- [Voor beeld van gezichts momentopname](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Gezichten toevoegen](how-to-add-faces.md)
- [Gezichten in een afbeelding detecteren](HowtoDetectFacesinImage.md)

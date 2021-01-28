---
title: Quickstart voor Anomaly Detector-clientbibliotheek voor .NET
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/22/2020
ms.author: mbullwin
ms.openlocfilehash: ee4dc926269d3ac66243a3953d7e41eb3a30198c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947425"
---
Ga aan de slag met de Anomaly Detector-clientbibliotheek voor .NET. Voer de volgende stappen uit om het pakket te installeren en de algoritmen te gaan gebruiken die door de service worden geleverd. Met de Anomaly Detector-service kunt u afwijkingen in uw tijdreeksgegevens vinden door hierop automatisch de best passende modellen uit te voeren, ongeacht de branche, het scenario of het gegevensvolume.

Gebruik de Anomaly Detector-clientbibliotheek voor .NET om:

* Anomalieën in uw tijdreeksgegevensset als een batchaanvraag te detecteren
* De anomaliestatus van het laatste gegevenspunt in uw tijdreeks te detecteren
* Trendwijzigingspunten in uw gegevensset te detecteren.

[Referentiedocumentatie voor bibliotheek](https://aka.ms/anomaly-detector-dotnet-ref) | [Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [Pakket (NuGet)](https://www.nuget.org/packages/Azure.AI.AnomalyDetector/3.0.0-preview.2) | [De code zoeken op GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* De huidige versie van [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* Zodra u een Azure-abonnement hebt, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Anomaly Detector-resource maken"  target="_blank">maakt u een Anomaly Detector-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in de Azure-portal om uw sleutel en eindpunt op te halen. Wacht tot deze is geïmplementeerd en klik op de knop **Naar de resource gaan**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt om de toepassing te verbinden met de Anomaly Detector-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-net-core-application"></a>Een nieuwe .NET Core-app maken

Gebruik in een consolevenster (zoals cmd, PowerShell of Bash) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam `anomaly-detector-quickstart`. Met deze opdracht maakt u een eenvoudig Hallo wereld-project met één C#-bronbestand: *Program.cs*.

```dotnetcli
dotnet new console -n anomaly-detector-quickstart
```

Wijzig uw map in de zojuist gemaakte app-map. U kunt de toepassing maken met:

```dotnetcli
dotnet build
```

De build-uitvoer mag geen waarschuwingen of fouten bevatten.

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Installeer in de toepassingsmap de Anomaly Detector-clientbibliotheek voor .NET met de volgende opdracht:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.AnomalyDetector
```

Open vanuit de projectmap het *program.cs*-bestand en voeg het volgende toe met behulp van `directives`:

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

Maak in de `main()`-methode van de toepassing variabelen voor de Azure-locatie van uw resource en uw sleutel als omgevingsvariabele. Als u de omgevingsvariabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell waarmee deze wordt uitgevoerd, sluiten en opnieuw openen om toegang te krijgen tot de variabele.

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

## <a name="object-model"></a>Objectmodel

De Anomaly Detector-client is een [AnomalyDetectorClient](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient)-object dat met behulp van [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials), dat uw sleutel bevat, wordt geverifieerd bij Azure. De client kan anomaliedetectie uitvoeren op een hele gegevensset met behulp van [EntireDetectAsync()](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync), of op het laatste gegevenspunt met behulp van [LastDetectAsync()](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync). De methode [ChangePointDetectAsync](https://aka.ms/anomaly-detector-dotnet-ref) detecteert punten die wijzigingen in een trend markeren.

Tijdreeksgegevens worden verzonden als een reeks [punten](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) in een [aanvraagobject](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request). Het `Request`-object bevat eigenschappen die de gegevens beschrijven ([granulariteit](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity) bijvoorbeeld) en parameters voor de anomaliedetectie.

Het antwoord van Anomaly Detector is een [EntireDetectResponse](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse)-, [LastDetectResponse](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse)- of [ChangePointDetectResponse](https://aka.ms/anomaly-detector-dotnet-ref)-object afhankelijk van de gebruikte methode.

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Anomaly Detector-clientbibliotheek voor .NET:

* [De client verifiëren](#authenticate-the-client)
* [Een tijdreeksgegevensset laden vanuit een bestand](#load-time-series-data-from-a-file)
* [Anomalieën in de volledige gegevensset detecteren](#detect-anomalies-in-the-entire-data-set)
* [De anomaliestatus van het laatste gegevenspunt detecteren](#detect-the-anomaly-status-of-the-latest-data-point)
* [De wijzigingspunten in de gegevensset detecteren](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>De client verifiëren

Instantieer in een nieuwe methode een client met uw eindpunt en sleutel. Maak een [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials)-object met uw sleutel en gebruik het met uw eindpunt om een [AnomalyDetectorClient-](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient)-object te maken.

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]

## <a name="load-time-series-data-from-a-file"></a>Tijdreeksgegevens uit een bestand laden

Download de voorbeeldgegevens voor deze quickstart van [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. Klik in uw browser met de rechtermuisknop op **Onbewerkt**.
2. Klik op **Koppeling opslaan als**.
3. Sla het bestand op in de map van de toepassing als CSV-bestand.

Deze tijdreeksgegevens worden opgemaakt als CSV-bestand en worden verzonden naar de Anomaly Detector-API.

Maak een nieuwe methode om de tijdreeksgegevens te lezen en voeg deze toe aan een [Aanvraag](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request)-object. Roep `File.ReadAllLines()` aan met het bestandspad en maak een lijst met [Punt](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point)-objecten en verwijder nieuwe regeltekens. Extraheer de waarden en scheid de tijdstempel van de numerieke waarde, en voeg ze toe aan een nieuw `Point`-object.

Maak een `Request`-object met de reeks punten en `Granularity.Daily` voor de [Granulariteit](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity) (of periodiciteit) van de gegevenspunten.

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Anomalieën in de volledige gegevensset detecteren

Maak een methode voor het aanroepen van de [EntireDetectAsync()](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_)-methode van de client met het `Request`-object en wacht op het antwoord als een [EntireDetectResponse](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse)-object. Als de tijdreeks afwijkende waarden bevat, herhaalt u de [IsAnomaly](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly)-waarden en drukt u de waarden af die `true` zijn. Deze waarden komen overeen met de index van afwijkende gegevenspunten, als die zijn gevonden.

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>De anomaliestatus van het laatste gegevenspunt detecteren

Maak een methode voor het aanroepen van de [LastDetectAsync()](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_)-methode van de client met het `Request`-object en wacht op het antwoord als een [LastDetectResponse](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse)-object. Controleer het [IsAnomaly](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly)-kenmerk van de reactie om te bepalen of het meest recente verzonden gegevenspunt wel of niet afwijkend is.

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="detect-change-points-in-the-data-set"></a>Wijzigingspunten in de gegevensset detecteren

Maak een methode voor het aanroepen van de [DetectChangePointAsync](https://aka.ms/anomaly-detector-dotnet-ref)-methode van de client met het `Request`-object en wacht op het antwoord als een [ChangePointDetectResponse](https://aka.ms/anomaly-detector-dotnet-ref)-object. Controleer de IsChangePoint-waarden van het antwoord en druk alle waarden af die `true` zijn. Deze waarden komen overeen met trendwijzigingspunten, als die zijn gevonden.

[!code-csharp[DetectChangePoint() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=changePointExample)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit vanuit uw toepassingsmap met de opdracht `dotnet run`.

```dotnetcli
dotnet run
```

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]

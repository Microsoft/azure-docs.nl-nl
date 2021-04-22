---
title: Anomaly Detector quickstart over multivariate Java-clientbibliotheek
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: e85f54beb9a3d4203e527ed9c8ce5582b6a2f5b0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879613"
---
Ga aan de slag met Anomaly Detector multivariate clientbibliotheek voor Java. Voer de volgende stappen uit om het pakket te installeren en de algoritmen te gaan gebruiken die door de service worden geleverd. Met de nieuwe API's voor anomaliedetectie met meerdere afwijkingen kunnen ontwikkelaars eenvoudig geavanceerde AI integreren voor het detecteren van afwijkingen uit groepen met metrische gegevens, zonder dat machine learning kennis of gelabelde gegevens nodig zijn. Afhankelijkheden en onderlinge correlaties tussen verschillende signalen worden automatisch geteld als belangrijke factoren. Dit helpt u om uw complexe systemen proactief te beschermen tegen storingen.

Gebruik de Anomaly Detector multivariate clientbibliotheek voor Java voor het volgende:

* Anomalieën op systeemniveau detecteren uit een groep tijdreeksen.
* Wanneer een afzonderlijke tijdreeks u niet veel vertelt en u alle signalen moet bekijken om een probleem te detecteren.
* Predicatief onderhoud van dure fysieke activa met tientallen tot honderden verschillende soorten sensoren die verschillende aspecten van de systeemtoestand meten.

[Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/anomalydetector/azure-ai-anomalydetector)  |  [Pakket (Maven)](https://repo1.maven.org/maven2/com/azure/azure-ai-anomalydetector/3.0.0-beta.2/)  |  [Voorbeeldcode](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/src/samples/java/com/azure/ai/anomalydetector/MultivariateSample.java)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* De huidige versie van de [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Het [hulpprogramma Gradle](https://gradle.org/install/) of een andere afhankelijkheidsbeheerder.
* Zodra u een Azure-abonnement hebt, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Anomaly Detector-resource maken"  target="_blank">maakt u een Anomaly Detector-resource </a> in Azure Portal om uw sleutel en eindpunt op te halen. Wacht tot deze is geïmplementeerd en klik op de knop **Naar de resource gaan**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt om de toepassing te verbinden met de Anomaly Detector-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-gradle-project"></a>Een nieuw Gradle-project maken

Deze quickstart maakt gebruik van de Gradle-afhankelijkheidsmanager. U vindt meer informatie over clientbibliotheken in de [centrale opslagplaats van Maven](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor).

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor de app, en navigeer naar deze map. 

```console
mkdir myapp && cd myapp
```

Voer de opdracht `gradle init` uit vanuit uw werkmap. Met deze opdracht maakt u essentiële buildbestanden voor Gradle, inclusief *build.gradle.kts*, dat tijdens runtime wordt gebruikt om de toepassing te maken en te configureren.

```console
gradle init --type basic
```

Wanneer u wordt gevraagd om een **DSL** te kiezen, selecteert u **Kotlin**.

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Zoek *build.gradle.kts* op en open het met uw favoriete IDE of teksteditor. Kopieer het vervolgens in deze buildconfiguratie. Zorg ervoor dat u de projectafhankelijkheden mee kopieert.

```kotlin
dependencies {
    compile("com.azure:azure-ai-anomalydetector")
}
```

### <a name="create-a-java-file"></a>Een Java-bestand maken

Maak een map voor de voorbeeld-app. Voer de volgende opdracht uit vanuit uw werkmap:

```console
mkdir -p src/main/java
```

Ga naar de nieuwe map en maak een bestand met de naam *MetricsAdvisorQuickstarts.java*. Open het bestand in uw voorkeurseditor of IDE en voeg de volgende `import`-instructies toe:

```java
package com.azure.ai.anomalydetector;

import com.azure.ai.anomalydetector.models.*;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.core.http.*;
import com.azure.core.http.policy.*;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.http.rest.PagedResponse;
import com.azure.core.http.rest.Response;
import com.azure.core.http.rest.StreamResponse;
import com.azure.core.util.Context;
import reactor.core.publisher.Flux;

import java.io.FileOutputStream;
import java.io.IOException;
import java.io.UncheckedIOException;
import java.nio.ByteBuffer;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.time.*;
import java.time.format.DateTimeFormatter;
import java.util.Iterator;
import java.util.List;
import java.util.UUID;
import java.util.stream.Collectors;
```

Maak variabelen voor het Azure-eindpunt en de Azure-sleutel voor uw resource. Maak nog een variabele voor het voorbeeldgegevensbestand.

```java
String key = "YOUR_API_KEY";
String endpoint = "YOUR_ENDPOINT";
```

 Als u de Anomaly Detector api's wilt gebruiken, moeten we ons eigen model trainen voordat u detectie gebruikt. Gegevens die worden gebruikt voor training zijn een batch tijdreeksen. Elke tijdreeks moet een CSV-indeling hebben met twee kolommen, tijdstempel en waarde. Alle tijdreeksen moeten worden ingepakt in één zip-bestand en worden geüpload naar [Azure Blob Storage.](../../../../storage/blobs/storage-blobs-introduction.md) Standaard wordt de bestandsnaam gebruikt om de variabele voor de tijdreeks weer te geven. U kunt ook een extra meta.jsin het zip-bestand als u wilt dat de naam van de variabele verschilt van de naam van het ZIP-bestand. Zodra we de [BLOB SAS-URL (Shared Access Signatures)](../../../../storage/common/storage-sas-overview.md)hebben gegenereerd, kunnen we de URL naar het ZIP-bestand gebruiken voor training.

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Anomaly Detector-clientbibliotheek voor Node.js:

* [De client verifiëren](#authenticate-the-client)
* [Een model trainen](#train-a-model)
* [Afwijkingen detecteren](#detect-anomalies)
* [Model exporteren](#export-model)
* [Model verwijderen](#delete-model)

## <a name="authenticate-the-client"></a>De client verifiëren

Instantieer een `anomalyDetectorClient` -object met uw eindpunt en referenties.

```java
HttpHeaders headers = new HttpHeaders()
    .put("Accept", ContentType.APPLICATION_JSON);

HttpPipelinePolicy authPolicy = new AzureKeyCredentialPolicy(key,
    new AzureKeyCredential(key));
AddHeadersPolicy addHeadersPolicy = new AddHeadersPolicy(headers);

HttpPipeline httpPipeline = new HttpPipelineBuilder().httpClient(HttpClient.createDefault())
    .policies(authPolicy, addHeadersPolicy).build();
// Instantiate a client that will be used to call the service.
HttpLogOptions httpLogOptions = new HttpLogOptions();
httpLogOptions.setLogLevel(HttpLogDetailLevel.BODY_AND_HEADERS);

AnomalyDetectorClient anomalyDetectorClient = new AnomalyDetectorClientBuilder()
    .pipeline(httpPipeline)
    .endpoint(endpoint)
    .httpLogOptions(httpLogOptions)
    .buildClient();
```

## <a name="train-a-model"></a>Een model trainen

### <a name="construct-a-model-result-and-train-model"></a>Een modelresultaat maken en het model trainen

Eerst moeten we een modelaanvraag maken. Zorg ervoor dat de begin- en eindtijd zijn afgestemd op uw gegevensbron.

 Als u de Anomaly Detector api's wilt gebruiken, moeten we ons eigen model trainen voordat u detectie gebruikt. Gegevens die worden gebruikt voor training zijn een batch tijdreeksen. Elke tijdreeks moet een CSV-indeling hebben met twee kolommen, een tijdstempel en een waarde. Alle tijdreeksen moeten worden ingepakt in één ZIP-bestand en worden geüpload naar [Azure Blob Storage.](../../../../storage/blobs/storage-blobs-introduction.md#blobs) Standaard wordt de bestandsnaam gebruikt om de variabele voor de tijdreeks weer te geven. U kunt ook een extra meta.jsin het zip-bestand als u wilt dat de naam van de variabele verschilt van de naam van het ZIP-bestand. Zodra we de [BLOB SAS-URL (Shared Access Signatures)](../../../../storage/common/storage-sas-overview.md)hebben gegenereerd, kunnen we de URL naar het ZIP-bestand gebruiken voor training.

```java
Path path = Paths.get("test-data.csv");
List<String> requestData = Files.readAllLines(path);
List<TimeSeriesPoint> series = requestData.stream()
    .map(line -> line.trim())
    .filter(line -> line.length() > 0)
    .map(line -> line.split(",", 2))
    .filter(splits -> splits.length == 2)
    .map(splits -> {
        TimeSeriesPoint timeSeriesPoint = new TimeSeriesPoint();
        timeSeriesPoint.setTimestamp(OffsetDateTime.parse(splits[0]));
        timeSeriesPoint.setValue(Float.parseFloat(splits[1]));
        return timeSeriesPoint;
    })
    .collect(Collectors.toList());

Integer window = 28;
AlignMode alignMode = AlignMode.OUTER;
FillNAMethod fillNAMethod = FillNAMethod.LINEAR;
Integer paddingValue = 0;
AlignPolicy alignPolicy = new AlignPolicy().setAlignMode(alignMode).setFillNAMethod(fillNAMethod).setPaddingValue(paddingValue);
String source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
OffsetDateTime startTime = OffsetDateTime.of(2021, 1, 2, 0, 0, 0, 0, ZoneOffset.UTC);
;
OffsetDateTime endTime = OffsetDateTime.of(2021, 1, 3, 0, 0, 0, 0, ZoneOffset.UTC);
;
String displayName = "Devops-MultiAD";

ModelInfo request = new ModelInfo().setSlidingWindow(window).setAlignPolicy(alignPolicy).setSource(source).setStartTime(startTime).setEndTime(endTime).setDisplayName(displayName);
TrainMultivariateModelResponse trainMultivariateModelResponse = anomalyDetectorClient.trainMultivariateModelWithResponse(request, Context.NONE);
String header = trainMultivariateModelResponse.getDeserializedHeaders().getLocation();
String[] model_ids = header.split("/");
UUID model_id = UUID.fromString(model_ids[model_ids.length - 1]);
System.out.println(model_id);

Integer skip = 0;
Integer top = 5;
PagedIterable<ModelSnapshot> response = anomalyDetectorClient.listMultivariateModel(skip, top);
Iterator<PagedResponse<ModelSnapshot>> ite = response.iterableByPage().iterator();

while (true) {
    Response<Model> response_model = anomalyDetectorClient.getMultivariateModelWithResponse(model_id, Context.NONE);
    UUID model = response_model.getValue().getModelId();
    System.out.println(response_model.getStatusCode());
    System.out.println(response_model.getValue().getModelInfo().getStatus());
    System.out.println(model);
    if (response_model.getValue().getModelInfo().getStatus() == ModelStatus.READY) {
        break;
    }
}
```

## <a name="detect-anomalies"></a>Afwijkingen detecteren

```java
DetectionRequest detectionRequest = new DetectionRequest().setSource(source).setStartTime(startTime).setEndTime(endTime);
DetectAnomalyResponse detectAnomalyResponse = anomalyDetectorClient.detectAnomalyWithResponse(model_id, detectionRequest, Context.NONE);
String result = detectAnomalyResponse.getDeserializedHeaders().getLocation();

String[] result_list = result.split("/");
UUID result_id = UUID.fromString(result_list[result_list.length - 1]);

while (true) {
    DetectionResult response_result = anomalyDetectorClient.getDetectionResult(result_id);
    if (response_result.getSummary().getStatus() == DetectionStatus.READY) {
        break;
    }
    else if(response_result.getSummary().getStatus() == DetectionStatus.FAILED){

    }
}
```

## <a name="export-model"></a>Model exporteren

Gebruik de om uw getrainde model te `exportModelWithResponse` exporteren.

```java
StreamResponse response_export = anomalyDetectorClient.exportModelWithResponse(model_id, Context.NONE);
Flux<ByteBuffer> value = response_export.getValue();
FileOutputStream bw = new FileOutputStream("result.zip");
value.subscribe(s -> write(bw, s), (e) -> close(bw), () -> close(bw));
```

## <a name="delete-model"></a>Model verwijderen

Als u een bestaand model wilt verwijderen dat beschikbaar is voor de huidige resource, gebruikt u de `deleteMultivariateModelWithResponse` functie .

```java
Response<Void> deleteMultivariateModelWithResponse = anomalyDetectorClient.deleteMultivariateModelWithResponse(model_id, Context.NONE);
```

## <a name="run-the-application"></a>De toepassing uitvoeren

U kunt de app maken met:

```console
gradle build
```
### <a name="run-the-application"></a>De toepassing uitvoeren

Voordat u gaat uitvoeren, kan het handig zijn om uw code te controleren op de [volledige voorbeeldcode](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/src/samples/java/com/azure/ai/anomalydetector/MultivariateSample.java).

De toepassing uitvoeren met het doel `run`:

```console
gradle run
```

## <a name="next-steps"></a>Volgende stappen

* [Anomaly Detector multivariate best practices (best practices voor meerdere varianten)](../../concepts/best-practices-multivariate.md)

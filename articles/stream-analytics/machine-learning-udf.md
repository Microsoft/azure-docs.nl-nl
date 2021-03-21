---
title: Azure Stream Analytics integreren met Azure Machine Learning
description: In dit artikel wordt beschreven hoe u een Azure Stream Analytics-taak integreert met Azure Machine Learning-modellen.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/21/2020
ms.custom: devx-track-js
ms.openlocfilehash: c35d5d2f63f4a7abe80a0ff19e5994013355c386
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020516"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>Azure Stream Analytics integreren met Azure Machine Learning (preview-versie)

U kunt machine learning modellen implementeren als een door de gebruiker gedefinieerde functie (UDF) in uw Azure Stream Analytics taken om in realtime scores en voor spellingen te doen op uw invoer gegevens voor streaming. Met [Azure machine learning](../machine-learning/overview-what-is-azure-ml.md) kunt u een veelgebruikt open source-hulp programma gebruiken, zoals tensor flow, scikit-Learn of PyTorch, voor het voorbereiden, trainen en implementeren van modellen.

## <a name="prerequisites"></a>Vereisten

Voer de volgende stappen uit voordat u een machine learning model toevoegt als een functie voor uw Stream Analytics taak:

1. Gebruik Azure Machine Learning om [uw model te implementeren als een webservice](../machine-learning/how-to-deploy-and-where.md).

2. Uw score script moet [voorbeeld invoer en uitvoer](../machine-learning/how-to-deploy-and-where.md) bevatten die door Azure machine learning worden gebruikt om een schema specificatie te genereren. Stream Analytics gebruikt het schema om inzicht te krijgen in de functie handtekening van uw webservice. U kunt deze voor [beeld-Swagger-definitie](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/AzureML/swagger-example.json) als referentie gebruiken om ervoor te zorgen dat u deze correct hebt ingesteld.

3. Zorg ervoor dat uw webservice een JSON-geserialiseerde gegevens accepteert en retourneert.

4. Implementeer uw model op de [Azure Kubernetes-service](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target) voor grootschalige productie-implementaties. Als de webservice niet in staat is om het aantal aanvragen af te handelen van uw taak, wordt de prestaties van uw Stream Analytics-taak verslechterd. Dit heeft gevolgen voor de latentie. Modellen die worden geïmplementeerd op Azure Container Instances worden alleen ondersteund wanneer u de Azure Portal gebruikt. Modellen die zijn gebouwd met behulp van [Azure machine learning Designer](../machine-learning/concept-designer.md) , worden nog niet ondersteund in stream Analytics.

## <a name="add-a-machine-learning-model-to-your-job"></a>Een machine learning model toevoegen aan uw taak

U kunt rechtstreeks vanuit de Azure Portal of Visual Studio code Azure Machine Learning functies aan uw Stream Analytics-taak toevoegen.

### <a name="azure-portal"></a>Azure Portal

1. Navigeer naar uw Stream Analytics-taak in de Azure Portal en selecteer **functies** onder **taak topologie**. Selecteer vervolgens **Azure machine learning-service** in de vervolg keuzelijst **+ toevoegen** .

   ![Azure Machine Learning UDF toevoegen](./media/machine-learning-udf/add-azure-machine-learning-udf.png)

2. Vul het formulier **Azure machine learning service functie** in met de volgende eigenschaps waarden:

   ![Azure Machine Learning UDF configureren](./media/machine-learning-udf/configure-azure-machine-learning-udf.png)

### <a name="visual-studio-code"></a>Visual Studio Code

1. Open uw Stream Analytics-project in Visual Studio code en klik met de rechter muisknop op de map **functions** . Kies vervolgens **functie toevoegen**. Selecteer **machine learning UDF** in de vervolg keuzelijst.

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-add-function.png" alt-text="UDF toevoegen in VS code":::

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-add-function-2.png" alt-text="Azure Machine Learning UDF toevoegen in VS code":::

2. Voer de naam van de functie in en vul de instellingen in het configuratie bestand in met behulp **van selecteren uit uw abonnementen** in code lens.

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-function-name.png" alt-text="Azure Machine Learning UDF selecteren in VS code":::

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-configure-settings.png" alt-text="Azure Machine Learning UDF configureren in VS code":::

In de volgende tabel worden de eigenschappen van Azure Machine Learning-service functies in Stream Analytics beschreven.

|Eigenschap|Beschrijving|
|--------|-----------|
|Functiealias|Voer een naam in om de functie in uw query aan te roepen.|
|Abonnement|Uw Azure-abonnement..|
|Azure Machine Learning-werkruimte|De Azure Machine Learning-werk ruimte die u hebt gebruikt voor het implementeren van uw model als een webservice.|
|Implementaties|De webservice die als host fungeert voor uw model.|
|Functie handtekening|De hand tekening van de webservice die is afgeleid van de schema specificatie van de API. Als uw hand tekening niet kan worden geladen, controleert u of u voorbeeld invoer en uitvoer hebt opgegeven in uw score script om het schema automatisch te genereren.|
|Aantal parallelle aanvragen per partitie|Dit is een geavanceerde configuratie voor het optimaliseren van een hoge schaal doorvoer. Dit nummer vertegenwoordigt de gelijktijdige aanvragen die vanuit elke partitie van uw taak naar de webservice worden verzonden. Taken met zes streaming-eenheden (SU) en lager hebben één partitie. Taken met 12 SUs hebben twee partities, 18 SUs heeft drie partities, enzovoort.<br><br> Als uw taak bijvoorbeeld twee partities heeft en u deze para meter instelt op vier, zijn er acht gelijktijdige aanvragen van uw taak naar uw webservice. Op dit moment van de open bare preview-versie is de standaard waarde 20 en kan niet worden bijgewerkt.|
|Maximum aantal batches|Dit is een geavanceerde configuratie voor het optimaliseren van een hoge schaal doorvoer. Dit nummer geeft het maximum aantal gebeurtenissen aan dat kan worden opgenomen in een enkele aanvraag die naar uw webservice wordt verzonden.|

## <a name="supported-input-parameters"></a>Ondersteunde invoer parameters

Wanneer uw Stream Analytics query een Azure Machine Learning UDF aanroept, maakt de taak een JSON-geserialiseerde aanvraag voor de webservice. De aanvraag is gebaseerd op een model-specifiek schema. U moet een voorbeeld invoer en uitvoer in uw score script opgeven om [automatisch een schema te genereren](../machine-learning/how-to-deploy-and-where.md). Met het schema kunnen Stream Analytics de JSON serialized-aanvraag maken voor een van de ondersteunde gegevens typen, zoals numpy, Pandas en PySpark. Meerdere invoer gebeurtenissen kunnen samen in één aanvraag in een batch worden opgenomen.

De volgende Stream Analytics query is een voor beeld van het aanroepen van een Azure Machine Learning UDF:

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

Stream Analytics biedt alleen ondersteuning voor het door geven van één para meter voor Azure Machine Learning-functies. Mogelijk moet u uw gegevens voorbereiden voordat deze worden door gegeven als invoer voor machine learning UDF. U moet ervoor zorgen dat de invoer naar een ML UDF niet null is als NULL-invoer zorgt ervoor dat de taak mislukt.

## <a name="pass-multiple-input-parameters-to-the-udf"></a>Meerdere invoer parameters door geven aan de UDF

De meest voorkomende voor beelden van invoer voor machine learning modellen zijn numpy-matrices en DataFrames. U kunt een matrix maken met behulp van een Java script UDF en een JSON-geserialiseerd data frame maken met behulp van de- `WITH` component.

### <a name="create-an-input-array"></a>Een invoer matrix maken

U kunt een Java script-UDF maken waarmee *N* het aantal invoer wordt geaccepteerd en een matrix wordt gemaakt die kan worden gebruikt als invoer voor uw Azure machine learning UDF.

```javascript
function createArray(vendorid, weekday, pickuphour, passenger, distance) {
    'use strict';
    var array = [vendorid, weekday, pickuphour, passenger, distance]
    return array;
}
```

Wanneer u de Java script UDF aan uw taak hebt toegevoegd, kunt u uw Azure Machine Learning UDF aanroepen met behulp van de volgende query:

```SQL
WITH 
ModelInput AS (
#use JavaScript UDF to construct array that will be used as input to ML UDF
SELECT udf.createArray(vendorid, weekday, pickuphour, passenger, distance) as inputArray
FROM input
)

SELECT udf.score(inputArray)
INTO output
FROM ModelInput
#validate inputArray is not null before passing it to ML UDF to prevent job from failing
WHERE inputArray is not null
```

De volgende JSON is een voorbeeld aanvraag:

```JSON
{
    "data": [
        ["1","Mon","12","1","5.8"],
        ["2","Wed","10","2","10"]
    ]
}
```

### <a name="create-a-pandas-or-pyspark-dataframe"></a>Een Panda-of PySpark-data frame maken

U kunt de- `WITH` component gebruiken om een JSON serialized data frame te maken die als invoer kan worden door gegeven aan uw Azure machine learning UDF, zoals hieronder wordt weer gegeven.

Met de volgende query maakt u een data frame door de gewenste velden te selecteren en de data frame als invoer te gebruiken voor de Azure Machine Learning UDF.

```SQL
WITH 
Dataframe AS (
SELECT vendorid, weekday, pickuphour, passenger, distance
FROM input
)

SELECT udf.score(Dataframe)
INTO output
FROM Dataframe
```

De volgende JSON is een voorbeeld aanvraag van de vorige query:

```JSON
{
    "data": [{
            "vendorid": "1",
            "weekday": "Mon",
            "pickuphour": "12",
            "passenger": "1",
            "distance": "5.8"
        }, {
            "vendorid": "2",
            "weekday": "Tue",
            "pickuphour": "10",
            "passenger": "2",
            "distance": "10"
        }
    ]
}
```

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>Optimaliseer de prestaties voor Azure Machine Learning Udf's

Wanneer u uw model implementeert in azure Kubernetes service, kunt u [uw model profileren om het resource gebruik te bepalen](../machine-learning/how-to-deploy-profile-model.md). U kunt ook [app Insights inschakelen voor uw implementaties om de](../machine-learning/how-to-enable-app-insights.md) aanvraag tarieven, reactie tijden en fout tarieven te begrijpen.

Als u een scenario met hoge gebeurtenis doorvoer hebt, moet u mogelijk de volgende para meters in Stream Analytics wijzigen om optimale prestaties te krijgen met een lage end-to-end latentie:

1. Maximum aantal batches.
2. Aantal parallelle aanvragen per partitie.

### <a name="determine-the-right-batch-size"></a>De juiste Batch grootte bepalen

Nadat u uw webservice hebt geïmplementeerd, verzendt u een voorbeeld aanvraag met verschillende batch grootten vanaf 50 en verhoogt u deze in de volg orde van honderden. Bijvoorbeeld 200, 500, 1000, 2000, enzovoort. Na een bepaalde Batch grootte neemt de latentie van de reactie toe. Het punt waarna de latentie van de reactie toeneemt, moet het maximum aantal batches voor uw taak zijn.

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>Het aantal parallelle aanvragen per partitie bepalen

Bij een optimale schaal baarheid moet uw Stream Analytics-taak meerdere parallelle aanvragen kunnen verzenden naar uw webservice en binnen enkele milliseconden een reactie krijgen. De latentie van de reactie van de webservice kan rechtstreeks van invloed zijn op de latentie en prestaties van uw Stream Analytics-taak. Als de oproep van uw taak voor de webservice lange tijd duurt, ziet u waarschijnlijk een toename van de vertraging van het water merk en ziet u mogelijk ook een toename van het aantal invoer gebeurtenissen met achterstand.

Als u een dergelijke latentie wilt voor komen, moet u ervoor zorgen dat uw Azure Kubernetes service-cluster (AKS) is ingericht met het [juiste aantal knoop punten en replica's](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli). Het is essentieel dat uw webservice Maxi maal beschikbaar is en dat er geslaagde reacties worden geretourneerd. Als uw taak een reactie van een service die niet beschikbaar is (503) van uw webservice ontvangt, wordt er voortdurend een nieuwe poging met exponentiële back-ups uitgevoerd. Een andere reactie dan geslaagd (200) en service niet beschikbaar (503) zorgt ervoor dat uw taak naar een mislukte status gaat.

## <a name="next-steps"></a>Volgende stappen

* [Zelfstudie: Door gebruiker gedefinieerde JavaScript-functies in Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Uw Stream Analytics-taak schalen met de functie Azure Machine Learning Studio (klassiek)](stream-analytics-scale-with-machine-learning-functions.md)

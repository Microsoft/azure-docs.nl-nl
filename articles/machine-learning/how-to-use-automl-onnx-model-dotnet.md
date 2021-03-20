---
title: Voor spellingen doen met het AutoML ONNX-model in .NET
description: Meer informatie over het maken van voor spellingen met behulp van een AutoML ONNX-model in .NET met ML.NET
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.date: 10/30/2020
ms.topic: conceptual
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.custom: how-to, automl
ms.openlocfilehash: 4fb147dc5c57c3a98607a025f566fa583bf87460
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93358810"
---
# <a name="make-predictions-with-an-automl-onnx-model-in-net"></a>Voor spellingen doen met een AutoML ONNX-model in .NET

In dit artikel leert u hoe u een Automated ML (AutoML) open Neural Network Exchange (ONNX)-model kunt gebruiken om voor spellingen te maken in een C# .NET core-console toepassing met ML.NET.

[Ml.net](/dotnet/machine-learning/) is een open-source, platformoverschrijdende machine learning Framework voor het .net-ecosysteem waarmee u aangepaste machine learning modellen kunt trainen en gebruiken met behulp van een code-First-benadering in C# of F #, en met weinig code, zoals [model Builder](/dotnet/machine-learning/automate-training-with-model-builder) en de [ml.net cli](/dotnet/machine-learning/automate-training-with-cli). Het Framework is ook uitbreidbaar en biedt u de mogelijkheid om andere populaire machine learning frameworks, zoals tensor flow en ONNX, te gebruiken.

ONNX is een open-source indeling voor AI-modellen. ONNX biedt ondersteuning voor interoperabiliteit tussen frameworks. Dit betekent dat u een model kunt trainen in een van de vele populaire machine learning frameworks zoals PyTorch, dit te converteren naar de ONNX-indeling en het ONNX-model in een ander Framework zoals ML.NET te gebruiken. Ga naar de [website van ONNX](https://onnx.ai/)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

- [.NET Core SDK 3,1 of hoger](https://dotnet.microsoft.com/download)
- Tekst editor of IDE (zoals [Visual Studio](https://visualstudio.microsoft.com/vs/) of [Visual Studio code](https://code.visualstudio.com/Download))
- ONNX-model. Voor meer informatie over hoe u een AutoML ONNX-model kunt trainen, raadpleegt u de volgende [notebook van Bank marketing-classificatie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb).
- [Netron](https://github.com/lutzroeder/netron) (optioneel)

## <a name="create-a-c-console-application"></a>Een C#-console toepassing maken

In dit voor beeld gebruikt u de .NET Core SLI om uw toepassing te bouwen, maar u kunt ook dezelfde taken uitvoeren met Visual Studio. Meer informatie over de [.net core SLI](/dotnet/core/tools/).

1. Open een Terminal en maak een nieuwe C# .NET core-console toepassing. In dit voor beeld is de naam van de toepassing `AutoMLONNXConsoleApp` . Er wordt een map met dezelfde naam gemaakt met de inhoud van uw toepassing.

    ```dotnetcli
    dotnet new console -o AutoMLONNXConsoleApp
    ```

1. Ga in de terminal naar de map *AutoMLONNXConsoleApp* .

    ```bash
    cd AutoMLONNXConsoleApp
    ```

## <a name="add-software-packages"></a>Software pakketten toevoegen

1. Installeer de **Microsoft.ml**-, **micro soft. ml. OnnxRuntime**-en **micro soft. ml. OnnxTransformer** NuGet-pakketten met behulp van de .net core SLI.

    ```dotnetcli
    dotnet add package Microsoft.ML
    dotnet add package Microsoft.ML.OnnxRuntime
    dotnet add package Microsoft.ML.OnnxTransformer
    ```

    Deze pakketten bevatten de afhankelijkheden die nodig zijn voor het gebruik van een ONNX-model in een .NET-toepassing. ML.NET biedt een API die gebruikmaakt van de [ONNX-runtime](https://github.com/Microsoft/onnxruntime) voor voor spellingen.

1. Open het bestand *Program. cs* en voeg `using` aan het begin de volgende instructies toe om naar de juiste pakketten te verwijzen.

    ```csharp
    using System.Linq;
    using Microsoft.ML;
    using Microsoft.ML.Data;
    using Microsoft.ML.Transforms.Onnx;
    ```

## <a name="add-a-reference-to-the-onnx-model"></a>Een verwijzing naar het ONNX-model toevoegen

Een manier om de console toepassing toegang te geven tot het ONNX-model is door het toe te voegen aan de map build uitvoermap.  Zie de [MSBuild-hand leiding](/visualstudio/msbuild/common-msbuild-project-items)voor meer informatie over algemene MSBuild-items.

Een verwijzing naar het ONNX-model bestand in uw toepassing toevoegen

1. Kopieer uw ONNX-model naar de basismap van de *AutoMLONNXConsoleApp* van uw toepassing.
1. Open het bestand *AutoMLONNXConsoleApp. csproj* en voeg de volgende inhoud toe aan het `Project` knoop punt.

    ```xml
    <ItemGroup>
        <None Include="automl-model.onnx">
            <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
        </None>
    </ItemGroup>
    ```

    In dit geval is de naam van het ONNX-model bestand *automl-model. ONNX*.

1. Open het bestand *Program. cs* en voeg de volgende regel toe aan de `Program` klasse.

    ```csharp
    static string ONNX_MODEL_PATH = "automl-model.onnx";
    ```

## <a name="initialize-mlcontext"></a>MLContext initialiseren

Maak in de `Main` methode van uw `Program` klasse een nieuwe instantie van [`MLContext`](xref:Microsoft.ML.MLContext) .

```csharp
MLContext mlContext = new MLContext();
```

De [`MLContext`](xref:Microsoft.ML.MLContext) klasse is een start punt voor alle ml.net-bewerkingen en bij het initialiseren `mlContext` wordt een nieuwe ml.NET-omgeving gemaakt die kan worden gedeeld in de levens cyclus van het model. Dit is vergelijkbaar met DbContext in Entity Framework.

## <a name="define-the-model-data-schema"></a>Het model gegevens schema definiëren

Uw model verwacht uw invoer-en uitvoer gegevens in een specifieke indeling. Met ML.NET kunt u de indeling van uw gegevens via klassen definiëren. Soms is het mogelijk dat u al weet wat die notatie lijkt te zijn. In gevallen waarin u de gegevens indeling niet weet, kunt u de hulpprogram ma's zoals Netron gebruiken om uw ONNX-model te controleren.

In het model dat in dit voor beeld wordt gebruikt, worden gegevens uit de NYC TLC-gegevensset voor de taxi gebruikt. Hieronder ziet u een voor beeld van de gegevens:

|vendor_id|rate_code|passenger_count|trip_time_in_secs|trip_distance|payment_type|fare_amount|
|---|---|---|---|---|---|---|
|VTS|1|1|1140|3,75|CRD|15.5|
|VTS|1|1|480|2,72|CRD|10.0|
|VTS|1|1|1680|7,8|CSH|26.5|

### <a name="inspect-the-onnx-model-optional"></a>Het ONNX-model controleren (optioneel)

Gebruik een hulp programma zoals Netron om de invoer en uitvoer van uw model te controleren.

1. Open Netron.
1. Selecteer in de bovenste menu balk **File > open** en gebruik de bestands browser om uw model te selecteren.
1. Het model wordt geopend. De structuur van het model *automl. onnx* ziet er bijvoorbeeld als volgt uit:

    :::image type="content" source="media/how-to-use-automl-onnx-model-dotnet/netron-automl-onnx-model.png" alt-text="Netron AutoML ONNX-model":::

1. Selecteer het laatste knoop punt onder aan de grafiek ( `variable_out1` in dit geval) om de meta gegevens van het model weer te geven. In de invoer en uitvoer van de zijbalk ziet u de verwachte invoer, uitvoer en gegevens typen van het model. Gebruik deze informatie om het invoer-en uitvoer schema van uw model te definiëren.

### <a name="define-model-input-schema"></a>Model invoer schema definiëren

Maak een nieuwe klasse `OnnxInput` met de naam met de volgende eigenschappen in het bestand *Program. cs* .

```csharp
public class OnnxInput
{
    [ColumnName("vendor_id")]
    public string VendorId { get; set; }

    [ColumnName("rate_code"),OnnxMapType(typeof(Int64),typeof(Single))]
    public Int64 RateCode { get; set; }

    [ColumnName("passenger_count"), OnnxMapType(typeof(Int64), typeof(Single))]
    public Int64 PassengerCount { get; set; }

    [ColumnName("trip_time_in_secs"), OnnxMapType(typeof(Int64), typeof(Single))]
    public Int64 TripTimeInSecs { get; set; }

    [ColumnName("trip_distance")]
    public float TripDistance { get; set; }

    [ColumnName("payment_type")]
    public string PaymentType { get; set; }
}
```

Elk van de eigenschappen wordt toegewezen aan een kolom in de gegevensset. De eigenschappen zijn verdere aantekeningen met kenmerken.

[`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute)Met het kenmerk kunt u opgeven hoe ml.net moet verwijzen naar de kolom wanneer deze op de gegevens worden uitgevoerd. Hoewel de `TripDistance` eigenschap de standaard .net-naamgevings conventies volgt, is het model bijvoorbeeld alleen van een kolom of onderdeel bekend als `trip_distance` . Om deze naamgevings discrepantie op te lossen, [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) wijst het kenmerk de `TripDistance` eigenschap toe aan een kolom of onderdeel met behulp van de naam `trip_distance` .
  
Voor numerieke waarden werkt ML.NET alleen op [`Single`](xref:System.Single) waardetypen. Het oorspronkelijke gegevens type van een aantal kolommen is echter een geheel getal. Het [`OnnxMapType`](xref:Microsoft.ML.Transforms.Onnx.OnnxMapTypeAttribute) kenmerk wijst typen toe tussen ONNX en ml.net.

Zie voor meer informatie over gegevens kenmerken de [hand leiding](/dotnet/machine-learning/how-to-guides/load-data-ml-net)voor het laden van ml.net-gegevens.

### <a name="define-model-output-schema"></a>Model-uitvoer schema definiëren

Zodra de gegevens zijn verwerkt, wordt een uitvoer van een bepaalde indeling gegenereerd. Definieer het schema voor gegevens uitvoer. Maak een nieuwe klasse `OnnxOutput` met de naam met de volgende eigenschappen in het bestand *Program. cs* .

```csharp
public class OnnxOutput
{
    [ColumnName("variable_out1")]
    public float[] PredictedFare { get; set; }
}
```

Vergelijkbaar met `OnnxInput` , gebruikt u het [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) kenmerk om de `variable_out1` uitvoer te koppelen aan een meer beschrijvende naam `PredictedFare` .

## <a name="define-a-prediction-pipeline"></a>Een Voorspellings pijplijn definiëren

Een pijp lijn in ML.NET is doorgaans een reeks gekoppelde trans formaties die op de invoer gegevens worden uitgevoerd om een uitvoer te produceren. Zie de [hand leiding voor ml.net-gegevens transformatie](/dotnet/machine-learning/resources/transforms)voor meer informatie over gegevens transformaties.

1. Een nieuwe methode met `GetPredictionPipeline` de naam in de `Program` klasse maken

    ```csharp
    static ITransformer GetPredictionPipeline(MLContext mlContext)
    {

    }
    ```

1. Definieer de naam van de kolommen invoer en uitvoer. Voeg de volgende code toe in de- `GetPredictionPipeline` methode.

    ```csharp
    var inputColumns = new string []
    {
        "vendor_id", "rate_code", "passenger_count", "trip_time_in_secs", "trip_distance", "payment_type"
    };

    var outputColumns = new string [] { "variable_out1" };
    ```

1. Definieer de pijp lijn. Een [`IEstimator`](xref:Microsoft.ML.IEstimator%601) geeft een blauw druk van de schema's voor bewerkingen, invoer en uitvoer van uw pijp lijn.

    ```csharp
    var onnxPredictionPipeline =
        mlContext
            .Transforms
            .ApplyOnnxModel(
                outputColumnNames: outputColumns,
                inputColumnNames: inputColumns,
                ONNX_MODEL_PATH);
    ```

    In dit geval [`ApplyOnnxModel`](xref:Microsoft.ML.OnnxCatalog.ApplyOnnxModel%2A) is de enige trans formatie in de pijp lijn, die in de namen van de invoer-en uitvoer kolommen en het pad naar het ONNX-model bestand neemt.

1. Een [`IEstimator`](xref:Microsoft.ML.IEstimator%601) definieert alleen de set bewerkingen die op uw gegevens moeten worden toegepast. Wat er op uw gegevens gebeurt, wordt aangeduid als een [`ITransformer`](xref:Microsoft.ML.ITransformer) . Gebruik de `Fit` methode om er een te maken `onnxPredictionPipeline` .

    ```csharp
    var emptyDv = mlContext.Data.LoadFromEnumerable(new OnnxInput[] {});

    return onnxPredictionPipeline.Fit(emptyDv);
    ```

    De [`Fit`](xref:Microsoft.ML.IEstimator%601.Fit%2A) methode verwacht een [`IDataView`](xref:Microsoft.ML.IDataView) als invoer voor het uitvoeren van de bewerkingen op. Een [`IDataView`](xref:Microsoft.ML.IDataView) manier om gegevens te vertegenwoordigen in ml.net met behulp van een tabellaire indeling. Omdat in dit geval de pijp lijn alleen voor voor spellingen wordt gebruikt, kunt u een lege waarde opgeven [`IDataView`](xref:Microsoft.ML.IDataView) om de [`ITransformer`](xref:Microsoft.ML.ITransformer) benodigde invoer-en uitvoer schema-informatie te geven. De aanwezige [`ITransformer`](xref:Microsoft.ML.ITransformer) wordt vervolgens geretourneerd voor verder gebruik in uw toepassing.

    > [!TIP]
    > In dit voor beeld wordt de pijp lijn gedefinieerd en gebruikt binnen dezelfde toepassing. Het is echter raadzaam om afzonderlijke toepassingen te gebruiken om de pijp lijn te definiëren en te gebruiken om voor spellingen te maken. In ML.NET kunnen uw pijp lijnen worden geserialiseerd en opgeslagen voor verder gebruik in andere .NET-toepassingen voor eind gebruikers. ML.NET ondersteunt verschillende implementatie doelen, zoals desktop toepassingen, webservices, webassemblage toepassingen * en nog veel meer. Zie de [hand leiding voor ml.net voor het opslaan en laden van getrainde modellen](/dotnet/machine-learning/how-to-guides/save-load-machine-learning-models-ml-net)voor meer informatie over het opslaan van pijp lijnen.
    >
    > * Webassembly wordt alleen ondersteund in .NET Core 5 of hoger

1. In de `Main` -methode roept u de- `GetPredictionPipeline` methode aan met de vereiste para meters.

    ```csharp
    var onnxPredictionPipeline = GetPredictionPipeline(mlContext);
    ```

## <a name="use-the-model-to-make-predictions"></a>Het model gebruiken om voor spellingen te maken

Nu u een pijp lijn hebt, is het tijd om deze te gebruiken voor het maken van voor spellingen. ML.NET biedt een gebruiks vriendelijke API voor het maken van voor spellingen bij het aanroepen van één gegevens exemplaar [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) .

1. Maak in de `Main` -methode een met [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) behulp van de- [`CreatePredictionEngine`](xref:Microsoft.ML.ModelOperationsCatalog.CreatePredictionEngine%2A) methode.

    ```csharp
    var onnxPredictionEngine = mlContext.Model.CreatePredictionEngine<OnnxInput, OnnxOutput>(onnxPredictionPipeline);
    ```

1. Maak een invoer voor de test gegevens.

    ```csharp
    var testInput = new OnnxInput
    {
        VendorId = "CMT",
        RateCode = 1,
        PassengerCount = 1,
        TripTimeInSecs = 1271,
        TripDistance = 3.8f,
        PaymentType = "CRD"
    };
    ```

1. Gebruik de `predictionEngine` om voor spellingen te maken op basis van de nieuwe `testInput` gegevens met behulp van de- [`Predict`](xref:Microsoft.ML.PredictionEngineBase%602.Predict%2A) methode.

    ```csharp
    var prediction = onnxPredictionEngine.Predict(testInput);
    ```

1. Uitvoer het resultaat van uw voor spelling naar de-console.

    ```csharp
    Console.WriteLine($"Predicted Fare: {prediction.PredictedFare.First()}");
    ```

1. Gebruik de .NET Core SLI om uw toepassing uit te voeren.

    ```dotnetcli
    dotnet run
    ```

    Het resultaat moet er ongeveer uitzien als in de volgende uitvoer:

    ```text
    Predicted Fare: 15.621523
    ```

Zie de [hand leiding een model gebruiken om voor spellingen te maken](/dotnet/machine-learning/how-to-guides/machine-learning-model-predictions-ml-net)voor meer informatie over het maken van voor spellingen in ml.net.

## <a name="next-steps"></a>Volgende stappen

- [Implementeer uw model als een ASP.NET Core Web-API](/dotnet/machine-learning/how-to-guides/serve-model-web-api-ml-net)
- [Implementeer uw model als een serverloze .NET Azure-functie](/dotnet/machine-learning/how-to-guides/serve-model-serverless-azure-functions-ml-net)
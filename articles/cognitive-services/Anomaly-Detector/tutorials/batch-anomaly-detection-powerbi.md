---
title: 'Zelfstudie: Afwijkingen visualiseren met batchgewijs detecteren en Power BI'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van de Anomaly Detector-API en Power BI voor het visualiseren van afwijkingen in uw tijdreeksgegevens.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: a17301e0807877662ae1bf34ade48e90a1d30c0c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006197"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>Zelfstudie: Afwijkingen visualiseren met batchgewijs detecteren en Power BI

Gebruik deze zelfstudie om afwijkingen in een tijdreeksgegevensverzameling batchgewijs te vinden. Met Power BI Desktop gaat u een Excel-bestand maken, de gegevens voorbereiden voor de Anomaly Detector-API en statistische afwijkingen visualiseren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Power BI Desktop gebruiken om een set tijdreeksgegevens te importeren en te transformeren
> * Power BI Desktop integreren met de Anomaly Detector-API voor het batchgewijs detecteren van afwijkingen
> * Gevonden afwijkingen in uw gegevens visualiseren, waaronder verwachte en aangetroffen waarden en grenzen voor afwijkingsdetectie.

## <a name="prerequisites"></a>Vereisten
* Een [Azure-abonnement](https://azure.microsoft.com/free/cognitive-services)
* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/get-started/), gratis beschikbaar.
* Een Excel-bestand (.xlsx) dat gegevenspunten van de tijdreeks bevat. De voorbeeldgegevens voor deze quickstart vindt u op [GitHub](https://go.microsoft.com/fwlink/?linkid=2090962)
* Zodra u een Azure-abonnement hebt, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Anomaly Detector-resource maken"  target="_blank">, maakt u een Anomaly Detector-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in de Azure-portal om uw sleutel en eindpunt op te halen.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Anomaly Detector-API. U doet dit later in de quickstart.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>De tijdreeksgegevens laden en opmaken

Als u aan de slag wilt gaan, opent u Power BI Desktop en laadt u de tijdreeksgegevens die u hebt gedownload zoals in de vereisten staat. Dit Excel-bestand bevat een reeks UTC-tijdstempels (Coordinated Universal Time) en waardeparen.  

> [!NOTE]
> Power BI kan gegevens gebruiken uit vele verschillende bronnen, zoals .csv-bestanden, SQL-databases en Azure Blob-opslag.  

Klik in het hoofdvenster van Power BI Desktop op het lint **Start**. Open in de groep **Externe gegevens** van het lint het vervolgkeuzemenu **Gegevens ophalen** en klik op **Excel**.

![Een afbeelding van de knop Gegevens ophalen in Power BI](../media/tutorials/power-bi-get-data-button.png)

Als het dialoogvenster wordt weergegeven, gaat u naar de map waarin u het Excel-voorbeeldbestand hebt gedownload en selecteert u het. Als het dialoogvenster **Navigator** wordt weergegeven, klikt u op **Sheet1** en vervolgens op **Bewerken**.

![Een afbeelding van het gegevensbronscherm van Navigator in Power BI](../media/tutorials/navigator-dialog-box.png)

Power BI converteert de tijdstempels in de eerste kolom naar het gegevenstype `Date/Time`. Deze tijdstempels moeten worden geconverteerd naar tekst om te worden verzonden naar de Anomaly Detector-API. Als de Power Query-editor niet automatisch wordt geopend, klikt u op het tabblad Start op **Query's bewerken**.

Klik in de Power Query-editor op het lint **Transformeren**. Open in de groep **Alle kolommen** het vervolgkeuzemenu **Gegevenstype:** en selecteer **Tekst**.

![Een afbeelding van de vervolgkeuzelijst Gegevenstype](../media/tutorials/data-type-drop-down.png)

Wanneer u een bericht krijgt over het wijzigen van het kolomtype, klikt u op **Huidige vervangen**. Klik daarna op **Sluiten en toepassen** of  **Toepassen** op het lint **Start**.

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>Een functie maken voor het verzenden van de gegevens en het opmaken van het antwoord

Als u het gegevensbestand wilt opmaken en verzenden naar de Anomaly Detector-API, kunt u een query aanroepen voor de tabel die hierboven is gemaakt. Open in de Power Query-editor vanuit het lint **Start** het vervolgkeuzemenu **Nieuwe bron** en klik op **Blanco query**.

Zorg ervoor dat de nieuwe query is geselecteerd en klik op **Geavanceerde editor**.

![Een afbeelding van het scherm Geavanceerde editor](../media/tutorials/advanced-editor-screen.png)

Gebruik in de Geavanceerde editor het volgende Power Query M-fragment om de kolommen op te halen uit de tabel en naar de API te verzenden. Daarna maakt de query een tabel op basis van het JSON-antwoord en retourneert deze. Vervang de variabele `apiKey` door de geldige Anomaly Detector-API-sleutel en `endpoint` door uw eindpunt. Nadat u de query hebt ingevoerd in de Geavanceerde editor, klikt u op **Gereed**.

```M
(table as table) => let

    apikey      = "[Placeholder: Your Anomaly Detector resource access key]",
    endpoint    = "[Placeholder: Your Anomaly Detector resource endpoint]/anomalydetector/v1.0/timeseries/entire/detect",
    inputTable = Table.TransformColumnTypes(table,{{"Timestamp", type text},{"Value", type number}}),
    jsontext    = Text.FromBinary(Json.FromValue(inputTable)),
    jsonbody    = "{ ""Granularity"": ""daily"", ""Sensitivity"": 95, ""Series"": "& jsontext &" }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Content-Type" = "application/json", #"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody, ManualStatusHandling={400}]),
    jsonresp    = Json.Document(bytesresp),

    respTable = Table.FromColumns({

                     Table.Column(inputTable, "Timestamp")
                     ,Table.Column(inputTable, "Value")
                     , Record.Field(jsonresp, "IsAnomaly") as list
                     , Record.Field(jsonresp, "ExpectedValues") as list
                     , Record.Field(jsonresp, "UpperMargins")as list
                     , Record.Field(jsonresp, "LowerMargins") as list
                     , Record.Field(jsonresp, "IsPositiveAnomaly") as list
                     , Record.Field(jsonresp, "IsNegativeAnomaly") as list

                  }, {"Timestamp", "Value", "IsAnomaly", "ExpectedValues", "UpperMargin", "LowerMargin", "IsPositiveAnomaly", "IsNegativeAnomaly"}
               ),

    respTable1 = Table.AddColumn(respTable , "UpperMargins", (row) => row[ExpectedValues] + row[UpperMargin]),
    respTable2 = Table.AddColumn(respTable1 , "LowerMargins", (row) => row[ExpectedValues] -  row[LowerMargin]),
    respTable3 = Table.RemoveColumns(respTable2, "UpperMargin"),
    respTable4 = Table.RemoveColumns(respTable3, "LowerMargin"),

    results = Table.TransformColumnTypes(

                respTable4,
                {{"Timestamp", type datetime}, {"Value", type number}, {"IsAnomaly", type logical}, {"IsPositiveAnomaly", type logical}, {"IsNegativeAnomaly", type logical},
                 {"ExpectedValues", type number}, {"UpperMargins", type number}, {"LowerMargins", type number}}
              )

 in results
```

Roep de query aan op uw gegevensblad door `Sheet1` onder **Parameter invoeren** te selecteren en op **Aanroepen** te klikken.

![Een afbeelding van de functie Aanroepen](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>Privacy en verificatie van gegevensbronnen

> [!NOTE]
> Houd rekening met het beleid van uw organisatie voor privacy en toegang tot gegevens. Zie [Power BI Desktop-privacyniveaus](/power-bi/desktop-privacy-levels) voor meer informatie.

Er wordt mogelijk een waarschuwing weergegeven wanneer u probeert de query uit te voeren, omdat deze een externe gegevensbron gebruikt.

![Een afbeelding met een waarschuwing van Power BI](../media/tutorials/blocked-function.png)

Klik op **Bestand** en **Opties en instellingen** om dit probleem op te lossen. Klik vervolgens op **Opties**. Selecteer onder **Huidig bestand** de optie **Privacy** en **De privacyniveaus en mogelijk verbeterde prestaties negeren**.

Daarnaast ontvangt u mogelijk een bericht waarin u wordt gevraagd hoe u verbinding wilt maken met de API.

![Een afbeelding met de vraag hoe u referenties wilt opgeven](../media/tutorials/edit-credentials-message.png)

Klik op **Referenties bewerken** in het bericht om dit probleem op te lossen. Als het dialoogvenster wordt weergegeven, selecteert u **Anoniem** om anoniem verbinding met de API te maken. Klik vervolgens op **Verbinden**.

Klik daarna op **Sluiten en toepassen** op het lint **Start**.

## <a name="visualize-the-anomaly-detector-api-response"></a>Het antwoord voor de Anomaly Detector-API visualiseren

Gebruik in het hoofdvenster van Power BI de eerder gemaakte query's om de gegevens te visualiseren. Selecteer eerst **Lijndiagram** in **Visualisaties**. Voeg vervolgens het tijdstempel van de aangeroepen functie toe aan de **As** van het lijndiagram. Klik er met de rechtermuisknop op en selecteer **Tijdstempel**.

![Met de rechtermuisknop op de waarde van de tijdstempel klikken](../media/tutorials/timestamp-right-click.png)

Voeg de volgende velden uit de **Aangeroepen functie** toe aan het veld **Waarden** van de grafiek. Gebruik de onderstaande schermopname om uw grafiek te maken.

* Waarde
* UpperMargins
* LowerMargins
* ExpectedValues

![Een afbeelding van de grafiekinstellingen](../media/tutorials/chart-settings.png)

Nadat u de velden hebt toegevoegd, klikt u op het diagram en past u het formaat aan om alle gegevenspunten weer te geven. Uw grafiek ziet er ongeveer uit als de volgende schermopname:

![Een afbeelding van de kaartvisualisatie](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>Afwijkende gegevenspunten weergeven

Klik aan de rechterkant van het Power BI-venster onder het deelvenster **VELDEN** met de rechtermuisknop op **Waarde** onder de **Aangeroepen functiequery** en klik op **Nieuwe snelle meting**.

![Een afbeelding van het scherm voor een nieuwe snelle meting](../media/tutorials/new-quick-measure.png)

Selecteer op het scherm dat wordt weergegeven **Gefilterde waarde** als de berekening. Stel **Basiswaarde** in op `Sum of Value`. Sleep `IsAnomaly` vervolgens van de velden **Aangeroepen functie** naar **Filter**. Selecteer `True` in het vervolgkeuzemenu **Filter**.

![Een tweede afbeelding van het scherm voor een nieuwe snelle meting](../media/tutorials/new-quick-measure-2.png)

Nadat u op **OK** hebt geklikt, hebt u het veld `Value for True` onder aan de lijst met uw velden. Klik er met de rechtermuisknop op en wijzig de naam ervan in **Afwijking**. Voeg het toe aan de **Waarden** van de grafiek. Selecteer vervolgens het hulpprogramma voor **Opmaak** en stel het type X-as in op **Categorisch**.

![Een afbeelding van de opmaak voor de X-as](../media/tutorials/format-x-axis.png)

Pas kleuren toe op uw grafiek door te klikken op het hulpprogramma voor **Opmaak** en **Gegevenskleuren**. Uw grafiek moet er ongeveer als volgt uitzien:

![Een afbeelding van de laatste grafiek](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Afwijkingsdetectie streamen met Azure Databricks](anomaly-detection-streaming-databricks.md)
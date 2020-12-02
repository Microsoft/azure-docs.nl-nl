---
title: Weers voorspelling met Azure Machine Learning Studio (klassiek) met IoT Hub gegevens
description: Gebruik Azure Machine Learning Studio (klassiek) om de kans op een regen te voors pellen op basis van de Tempe ratuur-en vochtigheids gegevens die uw IoT-hub verzamelt van een sensor.
author: robinsh
manager: philmea
keywords: machine learning weer voors pellen
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 09/16/2020
ms.author: robinsh
ms.openlocfilehash: ab9e122ba0b2b50203a2d66ae14f03f3b6300f96
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452340"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning-studio-classic"></a>Weers voorspelling met behulp van de sensor gegevens van uw IoT-hub in Azure Machine Learning Studio (klassiek)

![End-to-end-diagram](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Machine learning is een techniek van gegevens wetenschap die computers helpt bij het leren van bestaande gegevens om toekomstige gedragingen, resultaten en trends te voors pellen. Azure Machine Learning Studio (klassiek) is een Cloud predictive analytics service die het mogelijk maakt om snel voorspellende modellen te maken en te implementeren als analyse oplossingen.

## <a name="what-you-learn"></a>Wat u leert

U leert hoe u Azure Machine Learning Studio (klassiek) kunt gebruiken voor de weers voorspelling (kans op regen) met behulp van de Tempe ratuur en vochtigheids gegevens van uw Azure IoT hub. De kans op regen is de uitvoer van een voor bereid model voor voor spellingen. Het model is gebaseerd op historische gegevens om de kans op een regen op basis van de Tempe ratuur en vochtigheid te ramen.

## <a name="what-you-do"></a>Wat u doet

- Implementeer het weers voorspelling model als een webservice.
- Haal uw IoT-hub gereed voor gegevens toegang door een Consumer groep toe te voegen.
- Maak een Stream Analytics taak en stel de taak in op:
  - Lees de gegevens over Tempe ratuur en vochtigheid van uw IoT-hub.
  - Roep de webservice aan om de regen kans te halen.
  - Sla het resultaat op in een Azure Blob-opslag.
- Gebruik Microsoft Azure Storage Explorer om de weers voorspelling weer te geven.

## <a name="what-you-need"></a>Wat u nodig hebt

- Voltooi de zelf studie [Raspberry Pi online Simulator](iot-hub-raspberry-pi-web-simulator-get-started.md) of een van de zelf studies van het apparaat. bijvoorbeeld [Raspberry Pi met node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Deze voldoen aan de volgende vereisten:
  - Een actief Azure-abonnement.
  - Een Azure IoT hub onder uw abonnement.
  - Een client toepassing die berichten verzendt naar uw Azure IoT hub.
- Een [Azure machine learning Studio (klassiek)-](https://studio.azureml.net/) account.
- Een [Azure Storage-account](../storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-storage-accounts)is de voor keur voor een **v2-account voor algemeen gebruik** , maar alle Azure Storage-accounts die ondersteuning bieden voor Azure Blob Storage, werken ook.

> [!Note]
> In dit artikel worden Azure Stream Analytics en verschillende andere betaalde Services gebruikt. Er worden extra kosten in rekening gebracht in Azure Stream Analytics wanneer gegevens moeten worden overgebracht tussen Azure-regio's. Daarom is het raadzaam om ervoor te zorgen dat uw resource groep, IoT Hub, en Azure Storage account--en de Machine Learning Studio (klassieke) werk ruimte en Azure Stream Analytics taak later in deze zelf studie worden toegevoegd. Deze bevinden zich allemaal in dezelfde Azure-regio. U kunt de regionale ondersteuning voor Azure Machine Learning Studio (klassiek) en andere Azure-Services controleren op de [pagina Azure product beschik baarheid per regio](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-studio&regions=all).

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Het weers voorspelling model als een webservice implementeren

In deze sectie wordt het model voor de voor spelling van de Azure AI-bibliotheek weer geven. Vervolgens voegt u een R-script module toe aan het model om de gegevens van de Tempe ratuur en de vochtigheid op te schonen. Ten slotte implementeert u het model als voorspellende webservice.

### <a name="get-the-weather-prediction-model"></a>Het voors pellen model ophalen

In deze sectie wordt het model voor het voors pellen van de Azure AI Gallery weer geven en geopend in Azure Machine Learning Studio (klassiek).

1. Ga naar de pagina voor het voors [pellen model](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).

   ![Open de pagina weers voorspelling model in Azure AI Gallery](media/iot-hub-weather-forecast-machine-learning/weather-prediction-model-in-azure-ai-gallery.png)

1. Selecteer **openen in Studio (klassiek)** om het model in Microsoft Azure machine learning Studio (klassiek) te openen. Selecteer een regio in de buurt van uw IoT-hub en de juiste werk ruimte in het pop-upvenster **kopiëren van experiment vanuit galerie** .

   ![Open het model voor het voors pellen in Azure Machine Learning Studio (klassiek)](media/iot-hub-weather-forecast-machine-learning/open-ml-studio.png)

### <a name="add-an-r-script-module-to-clean-temperature-and-humidity-data"></a>Een R-script module toevoegen aan schone gegevens over Tempe ratuur en vochtigheid

Om het model goed te laten werken, moeten de gegevens over de Tempe ratuur en de vochtigheid worden omgezet in numerieke gegevens. In deze sectie voegt u een R-script module toe aan het weers Voorspellings model waarmee rijen met gegevens waarden voor de Tempe ratuur of vochtigheid worden verwijderd die niet kunnen worden geconverteerd naar numerieke waarden.

1. Selecteer aan de linkerkant van het venster Azure Machine Learning Studio (klassiek) de pijl om het deel venster extra uit te vouwen. Voer ' Execute ' in het zoekvak in. Selecteer de module voor het uitvoeren van een **R-script** .

   ![Selecteer de module R-script uitvoeren](media/iot-hub-weather-forecast-machine-learning/select-r-script-module.png)

1. Sleep de module voor het **uitvoeren van r-scripts** in de module **clean Missing Data** en de bestaande **Execute r-script** module in het diagram. Verwijder de verbinding tussen de **schone ontbrekende gegevens** en de **R-script** modules voor uitvoeren en verbind vervolgens de invoer en uitvoer van de nieuwe module, zoals wordt weer gegeven.

   ![De module voor het uitvoeren van een R-script toevoegen](media/iot-hub-weather-forecast-machine-learning/add-r-script-module.png)

1. Selecteer de nieuwe module voor het uitvoeren van een **R-script** om het eigenschappen venster te openen. Kopieer en plak de volgende code in het vak **R-script** .

   ```r
   # Map 1-based optional input ports to variables
   data <- maml.mapInputPort(1) # class: data.frame

   data$temperature <- as.numeric(as.character(data$temperature))
   data$humidity <- as.numeric(as.character(data$humidity))

   completedata <- data[complete.cases(data), ]

   maml.mapOutputPort('completedata')

   ```

   Wanneer u klaar bent, ziet het venster Eigenschappen er ongeveer als volgt uit:

   ![Code toevoegen voor het uitvoeren van de R-script module](media/iot-hub-weather-forecast-machine-learning/add-code-to-module.png)

### <a name="deploy-predictive-web-service"></a>Voorspellende webservice implementeren

In deze sectie valideert u het model, stelt u een voorspellende webservice in op basis van het model en implementeert u vervolgens de webservice.

1. Selecteer **uitvoeren** om de stappen in het model te valideren. Het kan enkele minuten duren voordat deze stap is voltooid.

   ![Voer het experiment uit om de stappen te valideren](media/iot-hub-weather-forecast-machine-learning/run-experiment.png)

1. Selecteer **Web Service**  >  **Predictive webservice** instellen. Het diagram voor het voorspellende experiment wordt geopend.

   ![Het weers voorspelling model in Azure Machine Learning Studio (klassiek) implementeren](media/iot-hub-weather-forecast-machine-learning/predictive-experiment.png)

1. In het diagram voorspellend experiment verwijdert u de verbinding tussen de module **Web Service-invoer** en de **kolommen in de gegevensset bovenaan selecteren** . Sleep de module **webservice-invoer** ergens in de buurt van de module **score model** en verbind deze zoals wordt weer gegeven:

   ![Twee modules in Azure Machine Learning Studio (klassiek) verbinden](media/iot-hub-weather-forecast-machine-learning/connect-modules-azure-machine-learning-studio.png)

1. Selecteer **uitvoeren** om de stappen in het model te valideren.

1. Selecteer **webservice implementeren** om het model als een webservice te implementeren.

1. Down load in het dash board van het model de **Excel 2010 of eerder-werkmap** voor **aanvraag/antwoord**.

   > [!Note]
   > Zorg ervoor dat u de **werkmap van excel 2010 of eerder** downloadt, zelfs als u een nieuwere versie van Excel op uw computer gebruikt.

   ![Down load het Excel-eind punt voor aanvraag antwoorden](media/iot-hub-weather-forecast-machine-learning/download-workbook.png)

1. Open de Excel-werkmap, noteer de URL van de **webservice** en de **toegangs sleutel**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Een Stream Analytics taak maken, configureren en uitvoeren

### <a name="create-a-stream-analytics-job"></a>Een Stream Analytics-taak maken

1. Selecteer in de [Azure Portal](https://portal.azure.com/) **een resource maken**. Typ ' stream Analytics job ' in het zoekvak en selecteer **Stream Analytics job** in de vervolg keuzelijst resultaten. Wanneer het deel venster **Stream Analytics taak** wordt geopend, selecteert u **maken**.
1. Voer de volgende informatie in voor de taak.

   **Taaknaam**: de naam van de taak. De naam moet wereldwijd uniek zijn.

   **Abonnement**: Selecteer uw abonnement als dit verschilt van de standaard waarde.

   **Resource groep**: gebruik dezelfde resource groep als uw IOT-hub.

   **Locatie**: gebruik dezelfde locatie als de resource groep.

   Wijzig de standaard waarden voor alle andere velden.

   ![Een Stream Analytics-taak maken in azure](media/iot-hub-weather-forecast-machine-learning/create-stream-analytics-job.png)

1. Selecteer **Maken**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Een invoer aan de Stream Analytics-taak toevoegen

1. Open de taak Stream Analytics.
1. Selecteer onder **Taaktopologie** de optie **Invoer**.
1. Selecteer in het deel venster **invoer** de optie **stroom invoer toevoegen** en selecteer vervolgens **IOT hub** in de vervolg keuzelijst. Kies in het deel venster **Nieuw invoer** de **optie IOT hub selecteren uit uw abonnementen** en voer de volgende gegevens in:

   **Invoer alias**: de unieke alias voor de invoer.

   **Abonnement**: Selecteer uw abonnement als dit verschilt van de standaard waarde.

   **IOT hub**: Selecteer de IOT-hub in uw abonnement.

   **Naam van beleid voor gedeelde toegang**: Selecteer  **service**. (U kunt ook **iothubowner** gebruiken.)

   **Consumenten groep**: Selecteer de Consumer groep die u hebt gemaakt.

   Wijzig de standaard waarden voor alle andere velden.

   ![Een invoer toevoegen aan de Stream Analytics-taak in azure](media/iot-hub-weather-forecast-machine-learning/add-input-stream-analytics-job.png)

1. Selecteer **Opslaan**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Een uitvoer aan de Stream Analytics-taak toevoegen

1. Selecteer onder **Taaktopologie** de optie **Uitvoer**.
1. Selecteer in het deel venster **outputs** de optie **toevoegen** en selecteer vervolgens **Blob Storage/Data Lake Storage** in de vervolg keuzelijst. Kies in het deel venster **nieuwe uitvoer** de optie **opslag selecteren bij uw abonnementen** en voer de volgende gegevens in:

   **Uitvoeralias**: de alias die uniek is voor de uitvoer.

   **Abonnement**: Selecteer uw abonnement als dit verschilt van de standaard waarde.

   **Opslag account**: het opslag account voor de Blob-opslag. U kunt een opslag account maken of een bestaande gebruiken.

   **Container**: de container waarin de blob is opgeslagen. U kunt een container maken of een bestaande gebruiken.

   **Indeling van de serialisatie van gebeurtenissen**: Selecteer **CSV**.

   ![Een uitvoer toevoegen aan de Stream Analytics-taak in azure](media/iot-hub-weather-forecast-machine-learning/add-output-stream-analytics-job.png)

1. Selecteer **Opslaan**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Een functie toevoegen aan de Stream Analytics-taak om de door u geïmplementeerde webservice aan te roepen

1. Selecteer **functies** onder **taak topologie**.
1. Selecteer in het deel venster **functies** de optie **toevoegen** en selecteer vervolgens **Azure-ml Studio** in de vervolg keuzelijst. (Zorg ervoor dat u **azure ml Studio** selecteert, niet de **Azure ml-service**.) Kies in het deel venster **Nieuw functie** de **instellingen voor Azure machine learning functie hand matig** en voer de volgende gegevens in:

   **Functie alias**: Enter `machinelearning` .

   **URL**: Voer de URL in van de webservice die u hebt genoteerd uit de Excel-werkmap.

   **Sleutel**: Voer de toegangs sleutel in die u hebt genoteerd uit de Excel-werkmap.

   ![Een functie toevoegen aan de Stream Analytics-taak in azure](media/iot-hub-weather-forecast-machine-learning/add-function-stream-analytics-job.png)

1. Selecteer **Opslaan**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>De query van de Stream Analytics-taak configureren

1. Selecteer **Query** onder **Taaktopologie**.
1. Vervang de huidige code door de volgende code:

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[scored probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   Vervang `[YourInputAlias]` door de invoeralias van de taak.

   Vervang `[YourOutputAlias]` door de uitvoeralias van de taak.

1. Selecteer **Query opslaan**.

> [!Note]
> Als u **test query** selecteert, wordt het volgende bericht weer gegeven: query's testen met machine learning-functies worden niet ondersteund. Wijzig de query en probeer het opnieuw. U kunt dit bericht negeren en **OK** selecteren om het bericht venster te sluiten. Zorg ervoor dat u de query opslaat voordat u doorgaat naar de volgende sectie.

### <a name="run-the-stream-analytics-job"></a>De Stream Analytics-taak uitvoeren

Selecteer in de taak Stream Analytics **overzicht** in het linkerdeel venster. **Selecteer vervolgens** start  >  **nu** starten  >  **Start**. Zodra de taak kan worden gestart, wordt de taakstatus veranderd van **Gestopt** naar **In uitvoering**.

![De Stream Analytics-taak uitvoeren](media/iot-hub-weather-forecast-machine-learning/run-stream-analytics-job.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Microsoft Azure Storage Explorer gebruiken om de weers voorspelling weer te geven

Voer de client toepassing uit om te beginnen met het verzamelen en verzenden van gegevens over de Tempe ratuur en vochtigheid naar uw IoT-hub. Voor elk bericht dat uw IoT-hub ontvangt, roept de Stream Analytics-taak de webservice voor de weers voorspelling aan om de kans op regen te produceren. Het resultaat wordt vervolgens opgeslagen in uw Azure Blob-opslag. Azure Storage Explorer is een hulp programma dat u kunt gebruiken om het resultaat weer te geven.

1. [Down load en installeer Microsoft Azure Storage Explorer](https://storageexplorer.com/).
1. Open Azure Storage Explorer.
1. Meld u aan bij uw Azure-account.
1. Selecteer uw abonnement.
1. Selecteer uw abonnement > **opslag Accounts** > uw opslag account > **Blob-containers** > uw container.
1. Down load een CSV-bestand om het resultaat te bekijken. In de laatste kolom wordt de kans op regen vastgelegd.

   ![Resultaat van de weers voorspelling ophalen met Azure Machine Learning Studio (klassiek)](media/iot-hub-weather-forecast-machine-learning/weather-forecast-result.png)

## <a name="summary"></a>Samenvatting

U hebt Azure Machine Learning Studio (klassiek) gebruikt voor het produceren van de kans op een regen op basis van de Tempe ratuur en de vochtigheids gegevens die uw IoT-hub ontvangt.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
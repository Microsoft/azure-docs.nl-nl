---
title: Weersvoorspelling met Azure Machine Learning Studio (klassiek) met IoT Hub gegevens
description: Gebruik Azure Machine Learning Studio (klassiek) om de kans op regen te voorspellen op basis van de temperatuur- en vochtigheidsgegevens die uw IoT-hub van een sensor verzamelt.
author: robinsh
manager: philmea
keywords: weersvoorspelling machine learning
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 09/16/2020
ms.author: robinsh
ms.openlocfilehash: 455d78ed21403952046448dd4447b5ec54f77c00
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566976"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning-studio-classic"></a>Weersvoorspelling met behulp van de sensorgegevens van uw IoT-hub in Azure Machine Learning Studio (klassiek)

![End-to-end-diagram](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Machine learning is een techniek van gegevenswetenschap waarmee computers kunnen leren van bestaande gegevens om toekomstig gedrag, resultaten en trends te voorspellen. Azure Machine Learning Studio (klassiek) is een cloudservice predictive analytics waarmee u snel voorspellende modellen kunt maken en implementeren als analyseoplossingen. In dit artikel leert u hoe u Azure Machine Learning Studio (klassiek) gebruikt voor het voorspellen van het weer (kans op regen) met behulp van de temperatuur- en vochtigheidsgegevens van uw Azure IoT-hub. De kans op regen is de uitvoer van een voorbereid weervoorspellingsmodel. Het model is gebaseerd op historische gegevens om de kans op regen te voorspellen op basis van temperatuur en vochtigheid.

## <a name="prerequisites"></a>Vereisten

- Voltooi de [onlinesimulator](iot-hub-raspberry-pi-web-simulator-get-started.md) van Raspberry Pi of een van de zelfstudies over apparaten. U kunt bijvoorbeeld naar [Raspberry Pi ](iot-hub-raspberry-pi-kit-node-get-started.md) gaan met node.jsof naar een van de [quickstarts Telemetrie](quickstart-send-telemetry-dotnet.md) verzenden. Deze artikelen hebben betrekking op de volgende vereisten:
  - Een actief Azure-abonnement.
  - Een Azure IoT-hub onder uw abonnement.
  - Een clienttoepassing die berichten naar uw Azure IoT-hub verzendt.
- Een [Azure Machine Learning Studio-account (klassiek).](https://studio.azureml.net/)
- Een [Azure Storage-account](../storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-storage-accounts)heeft de voorkeur voor een **v2-account** voor algemeen gebruik, maar elk Azure Storage-account dat Azure Blob Storage ondersteunt, werkt ook.

> [!Note]
> In dit artikel worden Azure Stream Analytics en verschillende andere betaalde services gebruikt. Er worden extra kosten in rekening gebracht Azure Stream Analytics gegevens moeten worden overgedragen tussen Azure-regio's. Daarom is het goed om ervoor te zorgen dat uw resourcegroep, IoT Hub en Azure Storage-account, evenals de Machine Learning Studio-werkruimte (klassiek) en de Azure Stream Analytics-taak die later in deze zelfstudie zijn toegevoegd, zich allemaal in dezelfde Azure-regio bevinden. U kunt regionale ondersteuning voor Azure Machine Learning Studio (klassiek) en andere Azure-services controleren op de pagina [Azure-productbeschikbaarheid per regio.](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-studio&regions=all)

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Het weervoorspellingsmodel implementeren als een webservice

In deze sectie krijgt u het weervoorspellingsmodel uit de Azure AI Bibliotheek. Vervolgens voegt u een R-scriptmodule toe aan het model om de temperatuur- en vochtigheidsgegevens op te schonen. Ten laatste implementeert u het model als een voorspellende webservice.

### <a name="get-the-weather-prediction-model"></a>Het weervoorspellingsmodel op halen

In deze sectie krijgt u het weervoorspellingsmodel van de Azure AI Gallery en opent u het in Azure Machine Learning Studio (klassiek).

1. Ga naar de [pagina met het weervoorspellingsmodel](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).

   ![Open de pagina weervoorspellingsmodel in Azure AI Gallery](media/iot-hub-weather-forecast-machine-learning/weather-prediction-model-in-azure-ai-gallery.png)

1. Selecteer **Openen in Studio (klassiek) om** het model te openen in Microsoft Azure Machine Learning Studio (klassiek). Selecteer een regio in de buurt van uw IoT-hub en de juiste werkruimte in het pop-uppop-upexperiment **Experiment kopiëren** uit galerie.

   ![Open het weervoorspellingsmodel in Azure Machine Learning Studio (klassiek)](media/iot-hub-weather-forecast-machine-learning/open-ml-studio.png)

### <a name="add-an-r-script-module-to-clean-temperature-and-humidity-data"></a>Een R-script-module toevoegen om temperatuur- en vochtigheidsgegevens op te schonen

Om het model correct te laten werken, moeten de temperatuur- en vochtigheidsgegevens worden gewisseld met numerieke gegevens. In deze sectie voegt u een R-script-module toe aan het weervoorspellingsmodel dat rijen verwijdert met gegevenswaarden voor temperatuur of vochtigheid die niet kunnen worden geconverteerd naar numerieke waarden.

1. Selecteer aan de linkerkant van het venster Azure Machine Learning Studio (klassiek) de pijl om het deelvenster Hulpprogramma's uit te vouwen. Voer 'Uitvoeren' in het zoekvak in. Selecteer de module **R-script** uitvoeren.

   ![De module R-script uitvoeren selecteren](media/iot-hub-weather-forecast-machine-learning/select-r-script-module.png)

1. Sleep de module **Execute R Script (R-script** uitvoeren) in de buurt van de module Clean Missing Data (Ontbrekende gegevens ops schonen) en de bestaande module Execute R Script **(R-script** uitvoeren) in het diagram.  Verwijder de verbinding tussen de modules **Clean Missing Data** en Execute R **Script** en verbind vervolgens de invoer en uitvoer van de nieuwe module, zoals weergegeven.

   ![De module Execute R Script toevoegen](media/iot-hub-weather-forecast-machine-learning/add-r-script-module.png)

1. Selecteer de nieuwe module **R-script uitvoeren** om het eigenschappenvenster te openen. Kopieer en plak de volgende code in het **vak R-script.**

   ```r
   # Map 1-based optional input ports to variables
   data <- maml.mapInputPort(1) # class: data.frame

   data$temperature <- as.numeric(as.character(data$temperature))
   data$humidity <- as.numeric(as.character(data$humidity))

   completedata <- data[complete.cases(data), ]

   maml.mapOutputPort('completedata')

   ```

   Wanneer u klaar bent, moet het eigenschappenvenster er ongeveer als volgt uitzien:

   ![Code toevoegen aan de module Execute R Script](media/iot-hub-weather-forecast-machine-learning/add-code-to-module.png)

### <a name="deploy-predictive-web-service"></a>Voorspellende webservice implementeren

In deze sectie valideert u het model, stelt u een voorspellende webservice in op basis van het model en implementeert u vervolgens de webservice.

1. Selecteer **Uitvoeren om** de stappen in het model te valideren. Deze stap kan enkele minuten duren.

   ![Het experiment uitvoeren om de stappen te valideren](media/iot-hub-weather-forecast-machine-learning/run-experiment.png)

1. Selecteer **SET UP WEB SERVICE**  >  **Predictive Web Service**. Het diagram van het voorspellende experiment wordt geopend.

   ![Het weervoorspellingsmodel implementeren in Azure Machine Learning Studio (klassiek)](media/iot-hub-weather-forecast-machine-learning/predictive-experiment.png)

1. Verwijder in het voorspellende experimentdiagram de verbinding tussen de module **Webservice-invoer** en de module **Kolommen in gegevensset** selecteren bovenaan. Sleep vervolgens de **invoermodule van de webservice** ergens in de buurt van de module **Score Model** en verbind deze zoals wordt weergegeven:

   ![Twee modules verbinden in Azure Machine Learning Studio (klassiek)](media/iot-hub-weather-forecast-machine-learning/connect-modules-azure-machine-learning-studio.png)

1. Selecteer **UITVOEREN om** de stappen in het model te valideren.

1. Selecteer **WEBSERVICE IMPLEMENTEREN om** het model te implementeren als een webservice.

1. Download op het dashboard van het model de **Excel 2010-** of eerdere werkmap voor **REQUEST/RESPONSE.**

   > [!Note]
   > Zorg ervoor dat u de **Excel 2010-** of eerdere werkmap downloadt, zelfs als u een latere versie van Excel op uw computer gebruikt.

   ![De Excel downloaden voor het eindpunt REQUEST RESPONSE](media/iot-hub-weather-forecast-machine-learning/download-workbook.png)

1. Open de Excel-werkmap, noteer de **WEBSERVICE-URL en** **de TOEGANGSSLEUTEL.**

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Een taak maken, configureren en uitvoeren Stream Analytics taak

### <a name="create-a-stream-analytics-job"></a>Een Stream Analytics-taak maken

1. Selecteer in [Azure Portal](https://portal.azure.com/)de **optie Een resource maken.** Typ 'Stream Analytics-taak' in het zoekvak en selecteer **Stream Analytics taak** in de vervolgkeuzekeuzekeuze. Wanneer het **Stream Analytics taak wordt** geopend, selecteert u **Maken.**
1. Voer de volgende informatie in voor de taak.

   **Taaknaam**: de naam van de taak. De naam moet wereldwijd uniek zijn.

   **Abonnement:** selecteer uw abonnement als dit anders is dan de standaardinstelling.

   **Resourcegroep:** gebruik dezelfde resourcegroep die uw IoT-hub gebruikt.

   **Locatie:** gebruik dezelfde locatie als uw resourcegroep.

   Laat alle andere velden op de standaardwaarde staan.

   ![Een Stream Analytics maken in Azure](media/iot-hub-weather-forecast-machine-learning/create-stream-analytics-job.png)

1. Selecteer **Maken**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Een invoer aan de Stream Analytics-taak toevoegen

1. Open de Stream Analytics taak.
1. Selecteer onder **Taaktopologie** de optie **Invoer**.
1. Selecteer in **het deelvenster Invoer** de optie **Stroominvoer toevoegen** en selecteer vervolgens **IoT Hub** in de vervolgkeuzeweergave. Kies in **het deelvenster** Nieuwe invoer de optie IoT Hub selecteren in **uw abonnementen** en voer de volgende gegevens in:

   **Invoeralias:** de unieke alias voor de invoer.

   **Abonnement:** selecteer uw abonnement als dit anders is dan de standaardinstelling.

   **IoT Hub:** selecteer de IoT-hub in uw abonnement.

   **Naam van beleid voor gedeelde toegang:** selecteer  **service**. (U kunt ook **iothubowner**.)

   **Consumentengroep:** selecteer de consumentengroep die u hebt gemaakt.

   Laat alle andere velden op de standaardwaarde staan.

   ![Invoer toevoegen aan de Stream Analytics-taak in Azure](media/iot-hub-weather-forecast-machine-learning/add-input-stream-analytics-job.png)

1. Selecteer **Opslaan**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Een uitvoer aan de Stream Analytics-taak toevoegen

1. Selecteer onder **Taaktopologie** de optie **Uitvoer**.
1. Selecteer in **het deelvenster Uitvoer** de optie **Toevoegen** en selecteer vervolgens **Blob Storage/Data Lake Storage** in de vervolgkeuzekeuze. Kies in **het deelvenster** Nieuwe uitvoer de optie Opslag selecteren in **uw abonnementen** en voer de volgende gegevens in:

   **Uitvoeralias**: de alias die uniek is voor de uitvoer.

   **Abonnement:** selecteer uw abonnement als dit anders is dan de standaardinstelling.

   **Opslagaccount:** het opslagaccount voor uw blobopslag. U kunt een opslagaccount maken of een bestaand account gebruiken.

   **Container:** de container waarin de blob wordt opgeslagen. U kunt een container maken of een bestaande container gebruiken.

   **Serialisatie-indeling voor gebeurtenissen:** selecteer **CSV.**

   ![Uitvoer toevoegen aan de Stream Analytics-taak in Azure](media/iot-hub-weather-forecast-machine-learning/add-output-stream-analytics-job.png)

1. Selecteer **Opslaan**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Een functie toevoegen aan de Stream Analytics om de webservice aan te roepen die u hebt geïmplementeerd

1. Selecteer **onder Taaktopologie** de optie **Functies**.
1. Selecteer in **het** deelvenster Functies de **optie Toevoegen** en selecteer vervolgens Azure **ML Studio** in de vervolgkeuzekeuze. (Zorg ervoor dat u **Azure ML Studio selecteert,** niet **Azure ML Service.)** Kies in **het deelvenster** Nieuwe functie handmatig de optie **Azure Machine Learning functie-instellingen** opgeven en voer de volgende gegevens in:

   **Functiealias:** voer `machinelearning` in.

   **URL:** voer de WEBSERVICE-URL in die u in de Excel-werkmap hebt genoteerd.

   **Sleutel:** voer de TOEGANGSSLEUTEL in die u in de Excel-werkmap hebt genoteerd.

   ![Een functie toevoegen aan de Stream Analytics-taak in Azure](media/iot-hub-weather-forecast-machine-learning/add-function-stream-analytics-job.png)

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
> Als u **Query testen selecteert,** wordt het volgende bericht weergegeven: Query's testen met Machine Learning functies wordt niet ondersteund. Wijzig de query en probeer het opnieuw. U kunt dit bericht veilig negeren en **OK selecteren om** het berichtvak te sluiten. Zorg ervoor dat u de query op slaan voordat u doorgaat met de volgende sectie.

### <a name="run-the-stream-analytics-job"></a>De Stream Analytics-taak uitvoeren

Selecteer in Stream Analytics taak **Overzicht** in het linkerdeelvenster. Selecteer vervolgens **Nu**  >  **starten**  >  **Start**. Zodra de taak kan worden gestart, wordt de taakstatus veranderd van **Gestopt** naar **In uitvoering**.

![De Stream Analytics-taak uitvoeren](media/iot-hub-weather-forecast-machine-learning/run-stream-analytics-job.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Gebruik Microsoft Azure Storage Explorer om de weersvoorspelling weer te bekijken

Voer de clienttoepassing uit om te beginnen met het verzamelen en verzenden van temperatuur- en vochtigheidsgegevens naar uw IoT-hub. Voor elk bericht dat uw IoT-hub ontvangt, roept de Stream Analytics de webservice voor weersvoorspelling aan om de kans op regen te produceren. Het resultaat wordt vervolgens opgeslagen in uw Azure Blob Storage. Azure Storage Explorer is een hulpprogramma dat u kunt gebruiken om het resultaat te bekijken.

1. [Download en installeer Microsoft Azure Storage Explorer](https://storageexplorer.com/).
1. Open Azure Storage Explorer.
1. Meld u aan bij uw Azure-account.
1. Selecteer uw abonnement.
1. Selecteer uw abonnement > **opslagaccounts >** uw opslagaccount > **blobcontainers >** uw container.
1. Download een CSV-bestand om het resultaat te bekijken. De laatste kolom registreert de kans op regen.

   ![Resultaat van weersvoorspelling met Azure Machine Learning Studio (klassiek)](media/iot-hub-weather-forecast-machine-learning/weather-forecast-result.png)

## <a name="summary"></a>Samenvatting

U hebt Azure Machine Learning Studio (klassiek) gebruikt om de kans op regen te produceren op basis van de temperatuur- en vochtigheidsgegevens die uw IoT-hub ontvangt.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
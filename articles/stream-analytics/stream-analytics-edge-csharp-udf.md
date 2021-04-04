---
title: 'Zelfstudie: door de gebruiker gedefinieerde C#-functies schrijven voor Azure Stream Analytics-taken in Visual Studio (preview)'
description: Deze zelfstudie laat zien hoe door de gebruiker gedefinieerde C#-functies voor Stream Analytics-taken in Visual Studio worden geschreven.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 851229e441aa2fbdf7b6eec05390c0ce2b149da2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020465"
---
# <a name="tutorial-write-a-c-user-defined-function-for-azure-stream-analytics-job-preview"></a>Zelfstudie: Een door de gebruiker gedefinieerde C#-functie schrijven voor een Azure Stream Analytics-taak (preview)

Met door de gebruiker gedefinieerde C#-functies (UDF’s) die zijn gemaakt in Visual Studio kunt u de Azure Stream Analytics-querytaal uitbreiden met uw eigen functies. U kunt bestaande code (inclusief DLL-bestanden) opnieuw gebruiken en wiskundige of complexe logica gebruiken met C#. Er zijn drie manieren waarop UDF's kunnen worden geïmplementeerd: CodeBehind-bestanden in een Stream Analytics-project, UDF's in een lokaal C#-project of UDF's van een bestaand pakket van een opslagaccount. In deze zelfstudie wordt de CodeBehind-methode gebruikt om een eenvoudige C#-functie te implementeren. De UDF-functie voor Stream Analytics-taken is momenteel in de previewfase en mag niet worden gebruikt voor productieworkloads.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een door de gebruiker gedefinieerde C#-functie maken met CodeBehind.
> * Uw Stream Analytics-taak lokaal testen.
> * Uw job naar Azure publiceren.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet aan de volgende vereisten zijn voldaan:

* Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan.
* Installeer [Stream Analytics-hulpprogramma’s voor Visual Studio](stream-analytics-tools-for-visual-studio-install.md) en de workload **Azure-ontwikkeling** of **Gegevensopslag en verwerking**.
* Bekijk de bestaande [handleiding voor het ontwikkelen van Stream Analytics Edge](stream-analytics-tools-for-visual-studio-edge-jobs.md) als u een IoT Edge-taak compileert.

## <a name="create-a-container-in-your-azure-storage-account"></a>Een container in uw Azure Storage-account maken

De container die u maakt, wordt gebruikt om het gecompileerde C#-pakket op te slaan. Als u een Edge-taak aanmaakt, wordt dit opslagaccount ook gebruikt voor het implementeren van het pakket op uw IoT Edge-apparaat. Gebruik een specifieke container voor elke Stream Analytics-taak. Hergebruik van dezelfde container voor meerdere Stream Analytics Edge-taken wordt niet ondersteund. Als u al een opslagaccount met bestaande containers hebt, kunt u die gebruiken. Zo niet, [maak dan een nieuwe container](../storage/blobs/storage-quickstart-blobs-portal.md). 

## <a name="create-a-stream-analytics-project-in-visual-studio"></a>Een Stream Analytics-project in Visual Studio aanmaken

1. Start Visual Studio.

2. Selecteer **Bestand > Nieuw > Project**.

3. Selecteer **Stream Analytics** in de sjablonenlijst aan de linkerkant en selecteer vervolgens **Azure Stream Analytics Edge Application** of **Azure Stream Analytics Application**.

4.  Voer de **Naam**, **Locatie** en **Oplossingsnaam** van het project in en selecteer **OK**.

    ![Een Azure Stream Analytics Edge-project in Visual Studio maken](./media/stream-analytics-edge-csharp-udf/stream-analytics-create-edge-app.png)

## <a name="configure-assembly-package-path"></a>Pad van assembly-pakket configureren

1. Open Visual Studio en ga naar de **Solution Explorer**.

2. Dubbelklik op het taakconfiguratiebestand `EdgeJobConfig.json`.

3. Vouw de sectie **Configuratie van de door de gebruiker gedefinieerde code** uit en vul de configuratie in met de volgende voorgestelde waarden:

   |**Instelling**|**Voorgestelde waarde**|
   |-------|---------------|
   |Resource globale opslaginstellingen|Kies gegevensbron van het huidige account|
   |Abonnement voor globale opslaginstellingen| < uw abonnement >|
   |Globale opslaginstellingen opslagaccount| < uw opslagaccount >|
   |Resource aangepaste code opslaginstellingen|Kies gegevensbron van het huidige account|
   |Aangepaste code opslaginstellingen opslagaccount|< uw opslagaccount >|
   |Container aangepaste code opslaginstellingen|< uw opslagcontainer >|


## <a name="write-a-c-udf-with-codebehind"></a>Een C#-UDF schrijven met CodeBehind
Een CodeBehind-bestand is een C#-bestand dat is gekoppeld aan een enkel ASA-queryscript. De Visual Studio-hulpprogramma's zippen het CodeBehind-bestand automatisch en uploaden het naar uw Azure-opslagaccount wanneer u het verzendt. Alle klassen moeten zijn gedefinieerd als *openbaar* en alle objecten moeten zijn gedefinieerd als *statisch openbaar*.

1. Vouw in **Solution Explorer** **Script.asql** uit om het CodeBehind-bestand **Script.asaql.cs** te zoeken.

2. Vervang de code door het volgende voorbeeld:

    ```csharp
        using System; 
        using System.Collections.Generic; 
        using System.IO; 
        using System.Linq; 
        using System.Text; 
    
        namespace ASAEdgeUDFDemo 
        { 
            public class Class1 
            { 
                // Public static function 
                public static Int64 SquareFunction(Int64 a) 
                { 
                    return a * a; 
                } 
            } 
        } 
    ```

## <a name="implement-the-udf"></a>De UDF implementeren

1. Open in **Solution Explorer** het bestand **Script.asaql**.

2. Vervang de bestaande query door de volgende:

    ```sql
        SELECT machine.temperature, udf.ASAEdgeUDFDemo_Class1_SquareFunction(try_cast(machine.temperature as bigint))
        INTO Output
        FROM Input 
    ```

## <a name="local-testing"></a>Lokaal testen

1. Download het [bestand met voorbeeldgegevens voor de temperatuursimulator](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Sample%20Data/TemperatureSampleData.json).

2. Vouw in **Solution Explorer** **Invoer** uit, klik met de rechtermuisknop op **Input.json** en selecteer **Lokale invoer toevoegen**.

   ![Lokale invoer toevoegen aan een Stream Analytics-taak in Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-add-local-input.png)

3. Geef het pad van het lokale invoerbestand op voor de voorbeeldgegevens die u hebt gedownload en selecteer **Opslaan**.

    ![Lokale invoer configureren voor een Stream Analytics-taak in Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-local-input-config.png)

4. Klik op **Lokaal uitvoeren** in de scripteditor. Nadat de resultaten van het uitvoeren zijn opgeslagen, drukt u op een willekeurige toets om de resultaten weer te geven in tabelindeling. 

    ![Azure Stream Analytics-taak lokaal uitvoeren met Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-run-locally.png)

5. U kunt ook **Map met resultaten openen** om de onbewerkte bestanden in JSON- en CSV-indeling weer te geven.

    ![Resultaten van lokale Azure Stream Analytics-taak weergeven met Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-view-local-results.png)

## <a name="debug-a-udf"></a>Fouten opsporen in een UDF
U kunt lokaal fouten opsporen in uw C# UDF, op dezelfde manier als dat u fouten opspoort in standaard C#-code. 

1. Onderbrekingspunten in uw C#-functie toevoegen.

    ![Onderbrekingspunten toevoegen aan een door de gebruiker gedefinieerde Stream Analytics-functie in Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-breakpoints.png)

2. Druk op **F5** om de foutopsporing te starten. Het programma stopt bij de onderbrekingspunten, zoals verwacht.

    ![De foutopsporingsresultaten van de door de gebruiker gedefinieerde Stream Analytics-functie weergeven](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-debug.png)

## <a name="publish-your-job-to-azure"></a>Uw taak publiceren naar Azure
Nadat u de query lokaal hebt getest, selecteert u **Verzenden naar Azure** in de scripteditor om de taak naar Azure te publiceren.

![Uw Stream Analytics Edge-taak verzenden naar Azure vanuit Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-submit-job.png)

## <a name="deploy-to-iot-edge-devices"></a>Implementeren naar IoT Edge-apparaten
Als u een Stream Analytics Edge-taak wilt compileren, kan dit nu worden geïmplementeerd als een IoT Edge-module. Volg de [IoT Edge-snelstart](../iot-edge/quickstart.md) om een IoT Hub te maken, een IoT Edge-apparaat te registeren en de IoT Edge-runtime op uw apparaat te installeren en starten. Volg vervolgens de zelfstudie [de taak implementeren](../iot-edge/tutorial-deploy-stream-analytics.md#deploy-the-job) om uw Stream Analytics-taak als een IoT Edge-module te implementeren. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een eenvoudige door de gebruiker gedefinieerde C#-functie aangemaakt met CodeBehind, uw taak gepubliceerd naar Azure en de taak geïmplementeerd op Azure of IoT Edge-apparaten. 

Als u meer wilt weten over de verschillende manieren waarop u door de gebruiker gedefinieerde C#-functies voor Stream Analytics-taken kunt gebruiken, gaat u verder met het volgende artikel:

> [!div class="nextstepaction"]
> [C#-functies schrijven in Azure Stream Analytics](stream-analytics-edge-csharp-udf-methods.md)
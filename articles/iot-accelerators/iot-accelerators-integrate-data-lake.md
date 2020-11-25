---
title: Gegevens streamen van externe bewaking naar Data Lake Store-Azure | Microsoft Docs
description: Meer informatie over het integreren van de oplossing voor externe controle met Azure Data Lake Store met behulp van een Azure Stream Analytics-taak.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: fce4da9cc9577bc9805289473d3df7647b1b0934
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000467"
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>De oplossing voor bewaking op afstand met Azure Data Lake Store integreren

Mogelijk beschikt u over geavanceerde analyse vereisten dan bij de oplossing voor externe controle. Azure Data Lake Store is ideaal voor deze toepassing omdat deze gegevens kan opslaan uit enorme en diverse gegevens sets, en kan worden geïntegreerd met Azure Data Lake Analytics om analyses op aanvraag mogelijk te maken.

In deze procedure gebruikt u een Azure Stream Analytics-taak voor het streamen van gegevens van de IoT-hub in uw oplossing voor externe controle naar een Azure Data Lake Store.

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze procedure hebt u het volgende nodig:

* [Implementeer de oplossings versneller voor externe controle](quickstart-remote-monitoring-deploy.md).
  * Met de oplossing voor controle op afstand wordt de IoT-hub en Azure Stream Analytics-taak die in dit artikel wordt gebruikt, geïmplementeerd in uw Azure-abonnement.
* [Een Azure Data Lake Store implementeren](../data-lake-store/data-lake-store-get-started-portal.md)
  * Uw Data Lake Store moet worden geïmplementeerd in dezelfde regio als uw oplossing voor externe controle.
  * [Maak een map](../data-lake-store/data-lake-store-get-started-portal.md#createfolder) met de naam ' streaming ' in uw account.

## <a name="create-a-consumer-group"></a>Een Consumer groep maken

Maak een speciale consumenten groep in de IoT-hub van uw oplossing voor externe controle. Dit wordt gebruikt door de Stream Analytics taak voor het streamen van gegevens naar uw Data Lake Store.

> [!NOTE]
> Consumenten groepen worden gebruikt door toepassingen voor het ophalen van gegevens uit Azure IoT Hub. U moet voor elke vijf uitvoer verbruiker een nieuwe consumenten groep maken. U kunt Maxi maal 32 consumenten groepen maken.

1. Meld u aan bij de Azure-portal.

1. Klik in het Azure Portal op de knop **Cloud shell** .

    ![Pictogram voor het starten van de portal](./media/iot-accelerators-integrate-data-lake/portal-launch-icon.png)

1. Voer deze opdracht uit om een nieuwe consumenten groep te maken:

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> Gebruik de naam van de resource groep en de IoT-hub van uw oplossing voor externe controle.

## <a name="create-stream-analytics-job"></a>Stream Analytics-taak maken

Maak een Azure Stream Analytics taak om de gegevens van uw IoT-hub naar uw Azure Data Lake Store te streamen.

1. Klik op **een resource maken**, selecteer Internet of things in Marketplace en klik op **Stream Analytics taak**.

    ![Nieuwe Stream Analytics taak](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. Voer de naam van een taak in en selecteer het juiste abonnement en de betreffende resource groep.

1. Selecteer een locatie in de buurt of in dezelfde regio als uw Data Lake Store. Hier gebruiken we VS-Oost.

1. Zorg ervoor dat u de hosting omgeving als de standaard- **Cloud** verlaat.

1. Klik op **Create**.

    ![Stream Analytics-taak maken](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>De Stream Analytics-taak configureren

1. Ga naar de **taak stream Analytics** in de resource groep voor de oplossing voor externe bewaking.

1. Klik op de pagina overzicht op **invoer**.

    ![Overzichts pagina](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. Klik op **stroom invoer toevoegen** en selecteer **IOT hub** in de vervolg keuzelijst.

    ![Invoer toevoegen](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. Voer op het tabblad Nieuw invoer een invoer alias van **IoTHub** in.

1. Selecteer de Consumer-groep die u eerder hebt gemaakt in de vervolg keuzelijst Consumer groep. Hier gebruiken we **streamanalyticsjob**.

    ![Invoer selecteren](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. Klik op **Opslaan**.

1. Klik op de pagina overzicht op **uitvoer**.

    ![Data Lake Store toevoegen](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. Klik op **toevoegen** en selecteer **Data Lake Store** in de vervolg keuzelijst.

    ![Uitvoer toevoegen](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. Voer op het tabblad nieuwe uitvoer een uitvoer alias van **Data Lake Store** in.

1. Selecteer het Data Lake Store account dat u in de vorige stappen hebt gemaakt en geef een mapstructuur op om gegevens naar de Store te streamen.

1. Voer **/streaming/{date}/{time}** in het veld datum notatie in. Behoud de standaard datum notatie JJJJ/MM/DD en tijd notatie uu.

    ![Mapstructuur opgeven](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. Klik op **autoriseren**.

    U moet autoriseren met Data Lake Store om de stream Analytics-taak schrijf toegang te geven tot het bestands systeem.

    ![Stream Analytics autoriseren voor Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    U ziet een pop-upvenster en wanneer het pop-upvenster wordt gesloten, wordt de knop voor autoriseren grijs weer gegeven nadat de autorisatie is voltooid.

    > [!NOTE]
    > Als er een fout in het pop-upvenster wordt weer geven, opent u een nieuw browser venster in de modus incognito en probeert u het opnieuw.

1. Klik op **Opslaan**.

## <a name="edit-the-stream-analytics-query"></a>De Stream Analytics query bewerken

Azure Stream Analytics maakt gebruik van een SQL-achtige query taal om een invoer bron op te geven waarmee gegevens worden gestreamd, de gegevens worden getransformeerd naar wens en uitvoer naar verschillende opslag-of verwerkings bestemmingen.

1. Klik op het tabblad Overzicht op **query bewerken**.

    ![Query bewerken](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. Vervang in de query-editor de tijdelijke aanduidingen [YourOutputAlias] en [YourInputAlias] door de waarden die u eerder hebt gedefinieerd.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Stream Analytics query](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. Klik op **Opslaan**.
1. Klik op **Ja** om de wijzigingen te accepteren.

## <a name="start-the-stream-analytics-job"></a>De Stream Analytics-taak starten

1. Klik op het tabblad Overzicht op **starten**.

    ![Stream Analytics taak starten](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. Klik op het tabblad Start taak op **aangepast**.

1. Stel aangepaste tijd in om een paar uur terug te gaan om gegevens op te halen wanneer het streamen van het apparaat is gestart.

1. Klik op **Start**.

    ![Aangepaste datum kiezen](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    Wacht totdat de taak wordt uitgevoerd. als er fouten worden weer geven, moet u controleren of de syntaxis juist is.

    ![Taak wordt uitgevoerd](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    De streaming-taak begint met het lezen van gegevens van uw IoT Hub en slaat de gegevens op in uw Data Lake Store. Het kan enkele minuten duren voordat de gegevens in uw Data Lake Store worden weer gegeven.

## <a name="explore-the-streaming-data"></a>De streaminggegevens verkennen

1. Ga naar uw Data Lake Store.

1. Klik op het tabblad Overzicht op **Data Explorer**.

1. Zoom in Data Explorer naar de map **/streaming** . U ziet mappen die zijn gemaakt met de indeling JJJJ/MM/DD.

    ![Scherm opname van het pad naar de map/streaming/YYYY/MM/DD/HH.](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    U ziet json-bestanden met één bestand per uur.

    ![Streaminggegevens verkennen](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>Volgende stappen

Azure Data Lake Analytics kunnen worden gebruikt om big data analyse uit te voeren op uw Data Lake Store gegevens sets. Meer informatie vindt u in de [Data Lake Analytics-documentatie](../data-lake-analytics/index.yml).
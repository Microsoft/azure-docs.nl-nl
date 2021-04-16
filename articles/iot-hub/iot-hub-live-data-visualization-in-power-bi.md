---
title: Realtime gegevensvisualisatie van gegevens uit Azure IoT Hub - Power BI
description: Gebruik Power BI om temperatuur- en vochtigheidsgegevens te visualiseren die van de sensor worden verzameld en naar uw Azure IoT-hub worden verzonden.
author: robinsh
keywords: realtime gegevensvisualisatie, live gegevensvisualisatie, visualisatie van sensorgegevens
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/08/2020
ms.author: robinsh
ms.openlocfilehash: 0b099f4ce91fd24e8d7baec054bcfc5a6cf0b032
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567107"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Realtime sensorgegevens van een Azure IoT Hub visualiseren met Power BI

![End-to-end-diagram](./media/iot-hub-live-data-visualization-in-power-bi/end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

In dit artikel leert u hoe u realtime sensorgegevens kunt visualiseren die uw Azure IoT-hub ontvangt met behulp van Power BI. Zie Use a web app to visualize real-time sensor data from Azure IoT Hub (Een web-app gebruiken om realtime sensorgegevens van uw IoT-hub te visualiseren [met een web-app) Azure IoT Hub.](iot-hub-live-data-visualization-in-web-apps.md)

## <a name="prerequisites"></a>Vereisten

* Voltooi de [onlinesimulator](iot-hub-raspberry-pi-web-simulator-get-started.md) van Raspberry Pi of een van de zelfstudies over apparaten. U kunt bijvoorbeeld naar [Raspberry Pi ](iot-hub-raspberry-pi-kit-node-get-started.md) gaan met node.jsof naar een van de [quickstarts Telemetrie](quickstart-send-telemetry-dotnet.md) verzenden. Deze artikelen hebben betrekking op de volgende vereisten:
  
  * Een actief Azure-abonnement.
  * Een Azure IoT-hub in uw abonnement.
  * Een clienttoepassing die berichten naar uw Azure IoT-hub verzendt.

* Een Power BI-account. ([Probeer Power BI gratis](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Een taak maken, configureren en uitvoeren Stream Analytics taak

Laten we beginnen met het maken van een Stream Analytics taak. Nadat u de taak hebt gemaakt, definieert u de invoer, uitvoer en de query die wordt gebruikt om de gegevens op te halen.

### <a name="create-a-stream-analytics-job"></a>Een Stream Analytics-taak maken

1. Selecteer in [Azure Portal](https://portal.azure.com) de optie **Een resource maken** > **Internet of Things** > **Stream Analytics-taak**.

2. Voer de volgende informatie in voor de taak.

   **Taaknaam**: de naam van de taak. De naam moet wereldwijd uniek zijn.

   **Resourcegroep:** gebruik dezelfde resourcegroep die uw IoT-hub gebruikt.

   **Locatie:** gebruik dezelfde locatie als uw resourcegroep.

   ![Een Stream Analytics maken in Azure](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job.png)

3. Selecteer **Maken**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Een invoer aan de Stream Analytics-taak toevoegen

1. Open de Stream Analytics taak.

2. Selecteer onder **Taaktopologie** de optie **Invoer**.

3. Selecteer in **het deelvenster Invoer** de optie **Stroominvoer toevoegen** en selecteer IoT Hub in de vervolgkeuzelijst.  Voer in het nieuwe invoerdeelvenster de volgende gegevens in:

   **Invoeralias:** voer een unieke alias in voor de invoer.

   **Selecteer IoT Hub uw abonnement:** selecteer dit keuzerondje.

   **Abonnement**: Selecteer het Azure-abonnement dat u gebruikt voor deze zelfstudie.

   **IoT Hub:** selecteer de IoT Hub u gebruikt voor deze zelfstudie.

   **Eindpunt**: selecteer **Berichten**.

   **Naam van het beleid voor gedeelde** toegang: selecteer de naam van het beleid voor gedeelde toegang dat u wilt gebruiken Stream Analytics taak voor uw IoT-hub. Voor deze zelfstudie kunt u *service selecteren.* Het *servicebeleid* wordt standaard gemaakt op nieuwe IoT-hubs en verleent machtigingen voor het verzenden en ontvangen van eindpunten in de cloud die worden weergegeven door de IoT-hub. Zie Toegangsbeheer en [machtigingen voor meer informatie.](iot-hub-devguide-security.md#access-control-and-permissions)

   **Beleidssleutel voor gedeelde toegang:** dit veld wordt automatisch ingevuld op basis van uw selectie voor de naam van het beleid voor gedeelde toegang.

   **Consumentengroep:** selecteer de consumentengroep die u eerder hebt gemaakt.

   Laat alle andere velden op de standaardwaarden staan.

   ![Invoer toevoegen aan een Stream Analytics-taak in Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job.png)

4. Selecteer **Opslaan**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Een uitvoer aan de Stream Analytics-taak toevoegen

1. Selecteer onder **Taaktopologie** de optie **Uitvoer**.

2. Selecteer in **het deelvenster Uitvoer** de optie **Toevoegen** **en Power BI.**

3. Selecteer in **het Power BI - Nieuwe**  uitvoer de optie Autor maken en volg de aanwijzingen om u aan te melden bij uw Power BI account.

4. Nadat u zich hebt aangemeld bij Power BI, voert u de volgende gegevens in:

   **Uitvoeralias:** een unieke alias voor de uitvoer.

   **Groepswerkruimte:** selecteer uw doelgroepwerkruimte.

   **Naam van gegevensset:** Voer een naam in voor de gegevensset.

   **Tabelnaam:** voer een tabelnaam in.

   **Verificatiemodus:** laat de standaardwaarde staan.

   ![Uitvoer toevoegen aan een Stream Analytics-taak in Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job.png)

5. Selecteer **Opslaan**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>De query van de Stream Analytics-taak configureren

1. Selecteer **Query** onder **Taaktopologie**.

2. Vervang `[YourInputAlias]` door de invoeralias van de taak.

3. Vervang `[YourOutputAlias]` door de uitvoeralias van de taak.

   ![Een query toevoegen aan een Stream Analytics-taak in Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-query-to-stream-analytics-job.png)

4. Selecteer **Query opslaan**.

### <a name="run-the-stream-analytics-job"></a>De Stream Analytics-taak uitvoeren

Selecteer in Stream Analytics taak Overzicht **en** selecteer **vervolgens Nu**  >    >  **starten.** Zodra de taak kan worden gestart, wordt de taakstatus veranderd van **Gestopt** naar **In uitvoering**.

![Een Stream Analytics uitvoeren in Azure](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Een rapport voor Power BI maken en publiceren om de gegevens te visualiseren

In de volgende stappen ziet u hoe u een rapport maakt en publiceert met behulp van de Power BI-service. U kunt deze stappen, met enige aanpassing, volgen als u het nieuwe uiterlijk wilt gebruiken in Power BI. Als u de verschillen wilt begrijpen en wilt navigeren in het nieuwe uiterlijk, gaat u naar Het nieuwe uiterlijk van [de Power BI-service](/power-bi/consumer/service-new-look).

1. Zorg ervoor dat de voorbeeldtoepassing wordt uitgevoerd op uw apparaat. Als dat niet het beste is, raadpleegt u de zelfstudies onder [Uw apparaat instellen.](./iot-hub-raspberry-pi-kit-node-get-started.md)

2. Meld u aan bij uw [Power BI](https://powerbi.microsoft.com/en-us/)-account.

3. Selecteer de werkruimte die u hebt gebruikt, **Mijn werkruimte.**

4. Selecteer **Gegevenssets**.

   U ziet de gegevensset die u hebt opgegeven tijdens het maken van de uitvoer voor de Stream Analytics taak.

5. Selecteer rapport toevoegen (het eerste pictogram rechts van de naam van de gegevensset) voor de gegevensset die u hebt gemaakt. 

   ![Een Microsoft Power BI maken](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-create-report.png)

6. Maak een lijndiagram om in realtime de temperatuur gedurende een bepaalde periode weer te geven.

   1. Selecteer in **het deelvenster Visualisaties** van de pagina voor het maken van het rapport het lijndiagrampictogram om een lijndiagram toe te voegen.

   2. Klap in het deelvenster **Velden** de tabel uit die u hebt opgegeven toen u de uitvoer voor de Stream Analytics-taak hebt gemaakt.

   3. Sleep **EventEnqueuedUtcTime** naar **As** in het deelvenster **Visualisaties**.

   4. Sleep **Temperatuur** naar **Waarden**.

      Er wordt een lijndiagram gemaakt. De x-as geeft de datum en tijd in UTC-tijdzone aan. De y-as geeft de temperatuur van de sensor aan.

      ![Een lijndiagram voor de temperatuur toevoegen aan een Microsoft Power BI rapport](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temperature.png)

7. Maak een ander lijndiagram om in realtime de vochtigheid gedurende een bepaalde periode weer te geven. Klik hiervoor op een leeg deel van het canvas en volg dezelfde stappen hierboven om **EventEnqueuedUtcTime** op de x-as en vochtigheid op de y-as te plaatsen. 

   ![Een lijndiagram voor vochtigheid toevoegen aan een Microsoft Power BI rapport](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. Selecteer **Opslaan om** het rapport op te slaan.

9. Selecteer **Rapporten** in het linkerdeelvenster en selecteer vervolgens het rapport dat u zojuist hebt gemaakt.

10. Selecteer **Bestand**  >  **Publiceren op internet.**

    ![Publiceren op internet selecteren voor het Microsoft Power BI rapport](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-select-publish-to-web.png)

    > [!NOTE]
    > Als u een melding ontvangt om contact op te nemen met uw beheerder om het maken van insluitcode in te kunnenschakelen, moet u mogelijk contact met de beheerder opnemen. Het maken van insluitcode moet zijn ingeschakeld voordat u deze stap kunt voltooien.
    >
    > ![Neem contact op met uw beheerdersmelding](./media/iot-hub-live-data-visualization-in-power-bi/contact-admin.png)

11. Selecteer **Insluitcode maken** en selecteer vervolgens **Publiceren.**

U krijgt de rapportkoppeling die u met iedereen kunt delen voor toegang tot het rapport en een codefragment dat u kunt gebruiken om het rapport te integreren in uw blog of website.

![Een Microsoft Power BI publiceren](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-web-output.png)

Microsoft biedt ook de [Power BI mobiele apps voor](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) weergave en interactie met uw Power BI dashboards en rapporten op uw mobiele apparaat.

## <a name="next-steps"></a>Volgende stappen

U hebt de Power BI om realtime sensorgegevens van uw Azure IoT-hub te visualiseren.

Zie Use a web app to [visualize real-time sensor data from](iot-hub-live-data-visualization-in-web-apps.md)Azure IoT Hub Azure IoT Hub (Een web-app gebruiken om realtime sensorgegevens van een Azure IoT Hub) voor een andere manier om gegevens uit uw Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

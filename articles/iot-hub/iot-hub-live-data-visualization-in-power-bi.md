---
title: Realtime gegevens visualisatie van gegevens van Azure IoT Hub – Power BI
description: Gebruik Power BI om de gegevens van de Tempe ratuur en de vochtigheid te visualiseren die van de sensor worden verzameld en naar uw Azure IoT hub te verzenden.
author: robinsh
keywords: realtime gegevens visualisatie, visualisatie van Live gegevens, sensor gegevens visualisatie
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/08/2020
ms.author: robinsh
ms.openlocfilehash: 82caf13618fe8483ab8d3a622c6c0d51ab05a206
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102177331"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Real-time sensor gegevens visualiseren vanuit Azure IoT Hub met behulp van Power BI

![End-to-end-diagram](./media/iot-hub-live-data-visualization-in-power-bi/end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Wat u leert

U leert hoe u real-time sensor gegevens visualiseren die uw Azure IoT hub ontvangt door gebruik te maken van Power BI. Als u de gegevens in uw IoT-hub wilt visualiseren met een web-app, raadpleegt u [een web-app gebruiken voor het visualiseren van real-time sensor gegevens uit Azure IOT hub](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>Wat u doet

* Haal uw IoT-hub gereed voor gegevens toegang door een Consumer groep toe te voegen.

* Een Stream Analytics taak maken, configureren en uitvoeren voor gegevens overdracht van uw IoT-hub naar uw Power BI-account.

* Een Power BI rapport maken en publiceren om de gegevens te visualiseren.

## <a name="what-you-need"></a>Wat u nodig hebt

* Voltooi de zelf studie [Raspberry Pi online Simulator](iot-hub-raspberry-pi-web-simulator-get-started.md) of een van de zelf studies van het apparaat. bijvoorbeeld [Raspberry Pi met node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Deze artikelen hebben betrekking op de volgende vereisten:
  
  * Een actief Azure-abonnement.
  * Een Azure IoT hub onder uw abonnement.
  * Een client toepassing die berichten verzendt naar uw Azure IoT hub.

* Een Power BI-account. ([Probeer Power bi gratis](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Een Stream Analytics taak maken, configureren en uitvoeren

Laten we beginnen met het maken van een Stream Analytics taak. Nadat u de taak hebt gemaakt, definieert u de invoer, uitvoer en de query die wordt gebruikt om de gegevens op te halen.

### <a name="create-a-stream-analytics-job"></a>Een Stream Analytics-taak maken

1. Selecteer in [Azure Portal](https://portal.azure.com) de optie **Een resource maken** > **Internet of Things** > **Stream Analytics-taak**.

2. Voer de volgende informatie in voor de taak.

   **Taaknaam**: de naam van de taak. De naam moet wereldwijd uniek zijn.

   **Resource groep**: gebruik dezelfde resource groep als uw IOT-hub.

   **Locatie**: gebruik dezelfde locatie als de resource groep.

   ![Een Stream Analytics-taak maken in azure](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job.png)

3. Selecteer **Maken**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Een invoer aan de Stream Analytics-taak toevoegen

1. Open de taak Stream Analytics.

2. Selecteer onder **Taaktopologie** de optie **Invoer**.

3. Selecteer in het deel venster **invoer** de optie **stroom invoer toevoegen** en selecteer vervolgens **IOT hub** in de vervolg keuzelijst. Voer in het deel venster Nieuw invoer de volgende gegevens in:

   **Invoer alias**: Voer een unieke alias in voor de invoer.

   **Selecteer IOT hub uit uw abonnement**: Selecteer dit keuze rondje.

   **Abonnement**: Selecteer het Azure-abonnement dat u gebruikt voor deze zelfstudie.

   **IOT hub**: selecteer de IOT hub die u voor deze zelf studie gebruikt.

   **Eindpunt**: selecteer **Berichten**.

   **Naam van beleid voor gedeelde toegang**: Selecteer de naam van het gedeelde toegangs beleid dat de stream Analytics taak moet gebruiken voor uw IOT-hub. Voor deze zelf studie kunt u *service* selecteren. Het *service* beleid wordt standaard gemaakt op nieuwe IOT-hubs en verleent machtigingen voor het verzenden en ontvangen van aan de Cloud zijde beschik bare eind punten van de IOT hub. Zie [toegangs beheer en machtigingen](iot-hub-devguide-security.md#access-control-and-permissions)voor meer informatie.

   **Sleutel voor gedeeld toegangs beleid**: dit veld wordt automatisch ingevuld op basis van uw selectie voor de naam van het gedeelde toegangs beleid.

   **Consumenten groep**: Selecteer de Consumer groep die u eerder hebt gemaakt.

   Vul alle andere velden in op de standaard waarden.

   ![Een invoer toevoegen aan een Stream Analytics-taak in azure](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job.png)

4. Selecteer **Opslaan**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Een uitvoer aan de Stream Analytics-taak toevoegen

1. Selecteer onder **Taaktopologie** de optie **Uitvoer**.

2. Selecteer in het deel venster **uitvoer** de optie **toevoegen** en **Power bi**.

3. Selecteer **machtigen** in het deel venster **Power bi-nieuwe uitvoer** en volg de prompts om u aan te melden bij uw Power bi-account.

4. Nadat u zich hebt aangemeld bij Power BI, voert u de volgende gegevens in:

   **Uitvoer alias**: een unieke alias voor de uitvoer.

   **Groeps werkruimte**: Selecteer de werk ruimte van uw doel groep.

   **Naam van gegevensset**: Voer een naam in voor de gegevensset.

   **Tabel naam**: Voer een tabel naam in.

   **Verificatie modus**: Vul de standaard waarde in.

   ![Een uitvoer toevoegen aan een Stream Analytics-taak in azure](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job.png)

5. Selecteer **Opslaan**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>De query van de Stream Analytics-taak configureren

1. Selecteer **Query** onder **Taaktopologie**.

2. Vervang `[YourInputAlias]` door de invoeralias van de taak.

3. Vervang `[YourOutputAlias]` door de uitvoeralias van de taak.

   ![Een query toevoegen aan een Stream Analytics-taak in azure](./media/iot-hub-live-data-visualization-in-power-bi/add-query-to-stream-analytics-job.png)

4. Selecteer **Query opslaan**.

### <a name="run-the-stream-analytics-job"></a>De Stream Analytics-taak uitvoeren

Selecteer in de taak stream Analytics **overzicht** **en selecteer**  >  **nu** starten  >  . Zodra de taak kan worden gestart, wordt de taakstatus veranderd van **Gestopt** naar **In uitvoering**.

![Een Stream Analytics-taak uitvoeren in azure](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Een Power BI rapport maken en publiceren om de gegevens te visualiseren

De volgende stappen laten zien hoe u een rapport maakt en publiceert met behulp van de Power BI-service. U kunt deze stappen met een bepaalde wijziging volgen, als u het ' nieuwe uiterlijk ' wilt gebruiken in Power BI. Zie [het nieuwe uiterlijk van de Power bi-service](/power-bi/consumer/service-new-look)als u meer wilt weten over de verschillen en hoe u in het nieuwe uiterlijk kunt navigeren.

1. Zorg ervoor dat de voorbeeld toepassing wordt uitgevoerd op het apparaat. Als dat niet het geval is, raadpleegt u de zelf studies onder [het instellen van uw apparaat](./iot-hub-raspberry-pi-kit-node-get-started.md).

2. Meld u aan bij uw [Power BI](https://powerbi.microsoft.com/en-us/)-account.

3. Selecteer de werk ruimte die u hebt gebruikt, **mijn werk ruimte**.

4. Selecteer **Gegevenssets**.

   U ziet de gegevensset die u hebt opgegeven tijdens het maken van de uitvoer voor de Stream Analytics taak.

5. Voor de gegevensset die u hebt gemaakt, selecteert u **rapport toevoegen** (het eerste pictogram rechts van de naam van de gegevensset).

   ![Een micro soft Power BI-rapport maken](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-create-report.png)

6. Maak een lijndiagram om in realtime de temperatuur gedurende een bepaalde periode weer te geven.

   1. Selecteer in het deel venster **Visualisaties** van de pagina rapport maken het pictogram lijn diagram om een lijn diagram toe te voegen.

   2. Klap in het deelvenster **Velden** de tabel uit die u hebt opgegeven toen u de uitvoer voor de Stream Analytics-taak hebt gemaakt.

   3. Sleep **EventEnqueuedUtcTime** naar **As** in het deelvenster **Visualisaties**.

   4. Sleep **Temperatuur** naar **Waarden**.

      Er wordt een lijndiagram gemaakt. De x-as geeft de datum en tijd in UTC-tijdzone aan. De y-as geeft de temperatuur van de sensor aan.

      ![Een lijn diagram voor de Tempe ratuur toevoegen aan een rapport van micro soft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temperature.png)

7. Maak een ander lijndiagram om in realtime de vochtigheid gedurende een bepaalde periode weer te geven. Als u dit wilt doen, klikt u op een leeg deel van het canvas en voert u dezelfde stappen uit om **EventEnqueuedUtcTime** op de x-as en **vochtigheid** op de y-as te plaatsen.

   ![Een lijn diagram voor de vochtigheid toevoegen aan een rapport van micro soft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. Selecteer **Opslaan** om het rapport op te slaan.

9. Selecteer **rapporten** in het linkerdeel venster en selecteer vervolgens het rapport dat u zojuist hebt gemaakt.

10. Selecteer **bestand**  >  **publiceren op Internet**.

    ![Selecteer publiceren op internet voor het micro soft Power BI-rapport](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-select-publish-to-web.png)

    > [!NOTE]
    > Als u een melding krijgt dat u contact opneemt met uw beheerder om het maken van code in te scha kelen, moet u mogelijk contact opnemen met de gebruikers. Het maken van code insluiten moet zijn ingeschakeld voordat u deze stap kunt volt ooien.
    >
    > ![Neem contact op met de beheerder](./media/iot-hub-live-data-visualization-in-power-bi/contact-admin.png)

11. Selecteer **invoeg code maken** en selecteer vervolgens **publiceren**.

U hebt de rapport koppeling die u kunt delen met iedereen voor toegang tot rapporten en een code fragment dat u kunt gebruiken om het rapport te integreren in uw blog of website.

![Een micro soft Power BI-rapport publiceren](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-web-output.png)

Micro soft biedt u ook de [Power bi mobiele apps](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) voor het weer geven en communiceren met uw Power bi Dash boards en rapporten op uw mobiele apparaat.

## <a name="next-steps"></a>Volgende stappen

U hebt Power BI gebruikt voor het visualiseren van real-time sensor gegevens van uw Azure IoT hub.

Zie [een web-app gebruiken voor het visualiseren van gegevens van de real-time-sensor vanuit azure IOT hub](iot-hub-live-data-visualization-in-web-apps.md)voor een andere methode voor het visualiseren van data van Azure IOT hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

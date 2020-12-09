---
title: Gesimuleerde telemetrie te visualiseren met Time Series Insights-Azure | Microsoft Docs
description: Meer informatie over het configureren van uw Time Series Insights omgeving voor het verkennen en analyseren van telemetrie die is gegenereerd door de Device simulatie Solution Accelerator.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: e1409b43f0ce1fc0d8c622dda79e857ac6abdd33
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854560"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Time Series Insights gebruiken om telemetrie te visualiseren die wordt verzonden vanuit de apparaat simulatie oplossings versneller

Met de Device simulatie Solution Accelerator kunt u telemetrie genereren op basis van gesimuleerde apparaten om uw IoT-oplossingen te testen. In deze hand leiding wordt uitgelegd hoe u de gesimuleerde telemetrie kunt visualiseren en analyseren met behulp van een Time Series Insights omgeving.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze hand leiding wilt volgen, hebt u een actief Azure-abonnement nodig. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

In de stappen in deze hand leiding wordt ervan uitgegaan dat u de apparaat simulatie oplossings versneller hebt geïmplementeerd in uw Azure-abonnement. Zie [device simulatie Deployment](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) op github als u nog geen apparaat simulatie hebt geïmplementeerd.

In dit artikel wordt ervan uitgegaan dat de naam van uw oplossings versneller is **Contoso-simulatie**. Vervang **Contoso-simulatie** door de naam van uw oplossings versneller als u de volgende stappen uitvoert.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>Een Consumer groep maken

U moet een speciale Consumer groep in uw IoT-hub maken om telemetrie te streamen naar Time Series Insights. Een gebeurtenis bron in Time Series Insights moet het exclusieve gebruik van een IoT Hub Consumer-groep hebben.

In de volgende stappen wordt de Azure CLI in de Azure Cloud Shell gebruikt voor het maken van de consumenten groep:

1. De IoT-hub is een van de verschillende bronnen die worden gemaakt bij het implementeren van de apparaat simulatie oplossings versneller. Voer de volgende opdracht uit om de naam van uw IoT-hub te zoeken. u moet de naam van uw oplossings versneller gebruiken:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    De IoT-hub is de resource van het type **micro soft. devices/IotHubs**.

1. Voeg een consumenten groep met de naam **devicesimulationtsi** toe aan de hub. Gebruik de volgende opdracht om de naam van uw hub en oplossings versneller te gebruiken:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    U kunt nu de Azure Cloud Shell sluiten.

## <a name="create-a-new-time-series-insights-environment"></a>Een nieuwe Time Series Insights omgeving maken

[Azure time series Insights](../../articles/time-series-insights/time-series-insights-overview.md) is een volledig beheerde analyse-, opslag-en visualisatie service voor het beheren van gegevens in de Cloud voor de tijd reeks van IOT-schaal. Een nieuwe Time Series Insights omgeving maken:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Selecteer **een resource maken**  >  **Internet of Things**  >  **Time Series Insights**:

    ![Nieuwe Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. Gebruik de waarden in de volgende tabel om uw Time Series Insights-omgeving te maken in dezelfde resource groep als uw oplossings versneller:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam van omgeving | De volgende scherm afbeelding maakt gebruik van de naam **Contoso-TSI**. Kies uw eigen unieke naam wanneer u deze stap uitvoert. |
    | Abonnement | Selecteer uw Azure-abonnement in de vervolgkeuzelijst. |
    | Resourcegroep | **Contoso-simulatie**. Gebruik de naam van uw oplossings versneller. |
    | Locatie | In dit voor beeld wordt **VS-Oost** gebruikt. Maak uw omgeving in dezelfde regio als de simulatie Accelerator van uw apparaat. |
    | Sku |**S1** |
    | Capaciteit | **1** |

    ![Time Series Insights maken](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > Als u de Time Series Insights-omgeving toevoegt aan dezelfde resource groep als de oplossings versneller, wordt deze verwijderd wanneer u de oplossings versneller verwijdert.

1. Klik op **Create**. Het kan enkele minuten duren voordat de omgeving is gemaakt.

## <a name="create-event-source"></a>Gebeurtenisbron maken

Maak een nieuwe gebeurtenis bron om verbinding te maken met uw IoT-hub. Gebruik de Consumer-groep die u in de vorige stappen hebt gemaakt. Een Time Series Insights gebeurtenis bron vereist dat een speciale Consumer-groep niet wordt gebruikt door een andere service.

1. Navigeer in het Azure Portal naar uw nieuwe tijdreeks omgeving.

1. Klik aan de linkerkant op **gebeurtenis bronnen**:

    ![Gebeurtenis bronnen weer geven](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. Klik op **toevoegen**:

    ![Gebeurtenis bron toevoegen](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. Als u uw IoT-hub als een nieuwe gebeurtenis bron wilt configureren, gebruikt u de waarden in de volgende tabel:

    | Instelling | Waarde |
    | ------- | ----- |
    | Bron naam van gebeurtenis | De volgende scherm afbeelding maakt gebruik van de naam **Contoso-IOT-hub**. Gebruik uw eigen unieke naam wanneer u deze stap uitvoert. |
    | Bron | **IoT Hub** |
    | Importoptie | **IoT Hub van beschik bare abonnementen gebruiken** |
    | Abonnements-id | Selecteer uw Azure-abonnement in de vervolgkeuzelijst. |
    | IOT-hub-naam | **Contoso-simulation7d894**. Gebruik de naam van uw IoT-hub via de computer simulatie oplossings versneller. |
    | Naam van het IoT-hub-beleid | **iothubowner** |
    | Beleids sleutel voor IOT hub | Dit veld wordt automatisch ingevuld. |
    | Klanten groep voor IOT hub | **devicesimulationtsi** |
    | Serialisatie-indeling voor gebeurtenissen | **JSON** |
    | Naam van de timestamp-eigenschap | Leeg laten |

    ![Gebeurtenis bron maken](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. Klik op **Create**.

> [!NOTE]
> U kunt [aanvullende gebruikers toegang verlenen](../time-series-insights/concepts-access-policies.md#grant-data-access) tot de time series Insights Explorer.

## <a name="start-a-simulation"></a>Een simulatie starten

Voordat u Time Series Insights Explorer gebruikt, configureert u de apparaat simulatie Solution Accelerator om een telemetrie te genereren. In de volgende scherm afbeelding ziet u een actieve simulatie met 10 Chiller-apparaten:

![Simulatie van apparaten uitvoeren](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Verkenner van Time Series Insights

De Time Series Insights Explorer is een web-app die u kunt gebruiken voor het visualiseren van uw telemetrie.

1. Selecteer in het Azure Portal het tabblad Time Series Insights **overzicht** .

1. Als u de Web-App van Time Series Insights Explorer wilt openen, klikt **u op Ga naar omgeving**:

    ![Verkenner van Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. Selecteer in het deel venster tijd selectie de **laatste 30 minuten** in het menu snelle tijden en klik op **zoeken**:

    ![Time Series Insights Explorer zoeken](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. Selecteer in het deel venster voor waarden aan de linkerkant de optie **Tempe ratuur** als **meting** en **iothub-verbinding-apparaat-id** als **splitsing op** waarde:

    ![Scherm opname van het Time Series Insights paneel ' termen ', met de waarden ' meten ' en ' splitsen op ' gemarkeerd.](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Klik met de rechter muisknop op de grafiek en selecteer **gebeurtenissen verkennen**:

    ![Time Series Insights Explorer-gebeurtenissen](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. De gebeurtenis gegevens worden weer gegeven in een raster:

    ![Time Series Insights Explorer-tabel](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. Klik op de knop perspectief weergave:

    ![Perspectief van Time Series Insights Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Klik **+** om een nieuwe query toe te voegen aan het perspectief:

    ![Time Series Insights Explorer-query toevoegen](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Selecteer de **laatste 30 minuten** als tijds Panne, **vochtigheids graad** als **meting** en **iothub-Connection-apparaat-id** als **splitsing op** waarde:

    ![Time Series Insights Explorer-query](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. Klik op de knop perspectief weergave om het telemetrie-dash board van uw apparaat weer te geven:

    ![Dash board van Time Series Insights Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om verder te gaan, moet u de oplossings versneller implementeren.

Als u de oplossings versneller niet meer nodig hebt, verwijdert u deze van de pagina [ingerichte oplossingen](https://www.azureiotsolutions.com/Accelerators#dashboard) door deze te selecteren en vervolgens op **oplossing verwijderen** te klikken.

Als u de Time Series Insights-omgeving aan de resource groep van de oplossings versneller hebt toegevoegd, wordt deze automatisch verwijderd wanneer u de oplossings versneller verwijdert. Anders moet u de Time Series Insights omgeving hand matig verwijderen uit de Azure Portal.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure time series Insights Explorer](../time-series-insights/time-series-insights-explorer.md)voor meer informatie over het verkennen en opvragen van gegevens in de time series Insights Explorer.
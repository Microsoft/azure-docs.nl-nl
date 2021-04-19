---
title: Gesimuleerde telemetrie visualiseren met Time Series Insights - Azure | Microsoft Docs
description: Meer informatie over het configureren van Time Series Insights om telemetrie te verkennen en te analyseren die is gegenereerd door de oplossingsversneller apparaatsimulatie.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 90a4b808daccc76e8cc9125973c69b13e8086fbf
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713962"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Gebruik Time Series Insights om telemetrie te visualiseren die is verzonden vanuit de oplossingsversneller voor apparaatsimulatie

Met de oplossingsversneller Apparaatsimulatie kunt u telemetrie genereren van gesimuleerde apparaten om uw IoT-oplossingen te testen. Deze handleiding laat zien hoe u de gesimuleerde telemetrie kunt visualiseren en analyseren met behulp van een Time Series Insights omgeving.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze handleiding wilt volgen, hebt u een actief Azure-abonnement nodig. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Bij de stappen in deze handleiding wordt ervan uitgenomen dat u de oplossingsversneller voor apparaatsimulatie hebt geïmplementeerd in uw Azure-abonnement. Als u nog geen apparaatsimulatie hebt geïmplementeerd, raadpleegt u [Apparaatsimulatie implementeren](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md) in GitHub.

In dit artikel wordt ervan uitgenomen dat de naam van uw oplossingsversneller **contoso-simulatie is.** Vervang **contoso-simulation** door de naam van uw oplossingsversneller terwijl u de volgende stappen voltooit.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>Een consumentengroep maken

U moet een specifieke consumentengroep maken in uw IoT-hub om telemetrie te streamen naar Time Series Insights. Een gebeurtenisbron in Time Series Insights moet het exclusieve gebruik hebben van een IoT Hub consumentengroep.

In de volgende stappen wordt de Azure CLI in de Azure Cloud Shell om de consumentengroep te maken:

1. De IoT-hub is een van de verschillende resources die zijn gemaakt toen u de oplossingsversneller voor apparaatsimulatie hebt geïmplementeerd. Voer de volgende opdracht uit om de naam van uw IoT-hub te vinden. Gebruik de naam van uw oplossingsversneller:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    De IoT-hub is de resource van het type **Microsoft.Devices/IotHubs.**

1. Voeg een consumentengroep met de **naam devicesimulationtsi toe** aan de hub. Gebruik in de volgende opdracht de naam van uw hub en oplossingsversneller:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    U kunt nu de Azure Cloud Shell.

## <a name="create-a-new-time-series-insights-environment"></a>Een nieuwe Time Series Insights maken

[Azure Time Series Insights](../../articles/time-series-insights/time-series-insights-overview.md) is een volledig beheerde service voor analyse, opslag en visualisatie voor het beheren van tijdreeksgegevens op IoT-schaal in de cloud. Een nieuwe omgeving Time Series Insights maken:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Selecteer **Een resource maken**  >  **Internet of Things**  >  **Time Series Insights**:

    ![Nieuwe Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. Als u uw Time Series Insights in dezelfde resourcegroep als uw oplossingsversneller wilt maken, gebruikt u de waarden in de volgende tabel:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam van omgeving | In de volgende schermopname wordt de **naam Contoso-TSI gebruikt.** Kies uw eigen unieke naam wanneer u deze stap voltooit. |
    | Abonnement | Selecteer uw Azure-abonnement in de vervolgkeuzelijst. |
    | Resourcegroep | **contoso-simulatie**. Gebruik de naam van uw oplossingsversneller. |
    | Locatie | In dit voorbeeld wordt VS **- oost gebruikt.** Maak uw omgeving in dezelfde regio als uw apparaatsimulatieversneller. |
    | Sku |**S1** |
    | Capaciteit | **1** |

    ![Time Series Insights maken](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > Als u Time Series Insights toevoegt aan dezelfde resourcegroep als de oplossingsversneller, betekent dit dat deze wordt verwijderd wanneer u de oplossingsversneller verwijdert.

1. Klik op **Create**. Het kan enkele minuten duren voordat de omgeving is gemaakt.

## <a name="create-event-source"></a>Gebeurtenisbron maken

Maak een nieuwe gebeurtenisbron om verbinding te maken met uw IoT-hub. Gebruik de consumentengroep die u in de vorige stappen hebt gemaakt. Een Time Series Insights gebeurtenisbron vereist een specifieke consumentengroep die niet wordt gebruikt door een andere service.

1. Navigeer in Azure Portal naar uw nieuwe Time Series Environment.

1. Klik aan de linkerkant **op Gebeurtenisbronnen:**

    ![Gebeurtenisbronnen weergeven](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. Klik **op Toevoegen:**

    ![Gebeurtenisbron toevoegen](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. Gebruik de waarden in de volgende tabel om uw IoT-hub te configureren als een nieuwe gebeurtenisbron:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam gebeurtenisbron | In de volgende schermopname wordt de **naam contoso-iot-hub gebruikt.** Gebruik uw eigen unieke naam wanneer u deze stap voltooit. |
    | Bron | **IoT Hub** |
    | Importoptie | **Gebruik IoT Hub van beschikbare abonnementen** |
    | Abonnements-id | Selecteer uw Azure-abonnement in de vervolgkeuzelijst. |
    | Naam van IoT-hub | **contoso-simulation7d894**. Gebruik de naam van uw IoT-hub van uw oplossingsversneller voor apparaatsimulatie. |
    | Naam van het IoT-hub-beleid | **iothubowner** |
    | Beleidssleutel voor IoT Hub | Dit veld wordt automatisch ingevuld. |
    | IoT Hub-consumentengroep | **devicesimulationtsi** |
    | Serialisatie-indeling van gebeurtenissen | **JSON** |
    | Naam van de timestamp-eigenschap | Leeg laten |

    ![Gebeurtenisbron maken](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. Klik op **Create**.

> [!NOTE]
> U kunt [extra gebruikers toegang verlenen tot](../time-series-insights/concepts-access-policies.md#grant-data-access) de Time Series Insights explorer.

## <a name="start-a-simulation"></a>Een simulatie starten

Voordat u Time Series Insights Explorer gebruikt, configureert u de oplossingsversneller apparaatsimulatie om telemetrie te genereren. In de volgende schermopname ziet u een simulatie met 10 koelapparaten:

![Apparaatsimulatie uitvoeren](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Verkenner van Time Series Insights

De Time Series Insights Explorer is een web-app die u kunt gebruiken om uw telemetrie te visualiseren.

1. Selecteer in Azure Portal het tabblad Time Series Insights **overzicht.**

1. Als u de web Time Series Insights Explorer-web-app wilt openen, klikt **u op Ga naar omgeving**:

    ![Verkenner van Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. Selecteer in het tijdselectievenster Afgelopen **30 minuten** in het menu Snelle tijden en klik op **Zoeken:**

    ![Time Series Insights explorer zoeken](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. Selecteer in het deelvenster met termen  aan de linkerkant temperatuur **als** De meting en **iothub-connection-device-id** als **de waarde Splitsen op:**

    ![Schermopname van het deelvenster Time Series Insights termen, met de waarden 'Meting' en 'Splitsen op' gemarkeerd.](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Klik met de rechtermuisknop op de grafiek en selecteer **Gebeurtenissen verkennen:**

    ![Time Series Insights explorer-gebeurtenissen](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. De gebeurtenisgegevens worden in een raster weer geven:

    ![Time Series Insights Explorer-tabel](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. Klik op de knop Perspectiefweergave:

    ![Time Series Insights Explorer-perspectief](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Klik **+** om een nieuwe query toe te voegen aan het perspectief:

    ![Time Series Insights explorer Query toevoegen](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Selecteer **Afgelopen 30 minuten** als tijdsspanne, **Vochtigheid** als meting **en** **iothub-connection-device-id** als **de waarde Splitsen op:**

    ![Time Series Insights Explorer-query](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. Klik op de perspectiefweergaveknop om uw apparaat-telemetriedashboard weer te geven:

    ![Time Series Insights Explorer-dashboard](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u verder wilt verkennen, laat u de oplossingsversneller geïmplementeerd.

Als u de oplossingsversneller niet [](https://www.azureiotsolutions.com/Accelerators#dashboard) meer nodig hebt, verwijdert u deze op de pagina Inrichtende oplossingen door deze te selecteren en vervolgens op **Oplossing verwijderen te klikken.**

Als u de Time Series Insights aan de resourcegroep van de oplossingsversneller hebt toegevoegd, wordt deze automatisch verwijderd wanneer u de oplossingsversneller verwijdert. Anders moet u de Time Series Insights handmatig verwijderen uit de Azure Portal.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het verkennen en opvragen van gegevens in Time Series Insights explorer [Azure Time Series Insights explorer.](../time-series-insights/time-series-insights-explorer.md)
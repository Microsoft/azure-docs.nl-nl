---
title: Implementaties IoT Edge bewaken - Azure IoT Edge
description: Bewaking op hoog niveau, inclusief edgeHub en edgeAgent gerapporteerde eigenschappen en metrische gegevens over automatische implementatie.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/21/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 39e7bb5c151d490e79ef111589f52f260c3e6c7a
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483160"
---
# <a name="monitor-iot-edge-deployments"></a>IoT Edge-implementaties bewaken

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge biedt rapportage waarmee u realtime informatie kunt bewaken over de modules die zijn geïmplementeerd op IoT Edge apparaten. De IoT Hub-service haalt de status van de apparaten op en maakt deze beschikbaar voor de operator. Bewaking is ook belangrijk voor [implementaties die op schaal worden gemaakt,](module-deployment-monitoring.md) waaronder automatische implementaties en gelaagde implementaties.

Zowel apparaten als modules hebben vergelijkbare gegevens, zoals connectiviteit, zodat waarden worden verkregen op basis van de apparaat-id of de module-id.

De IoT Hub-service verzamelt gegevens die worden gerapporteerd door apparaat- en module-tweelingen en geeft tellingen van de verschillende staten die apparaten mogelijk hebben. De IoT Hub-service organiseert deze gegevens in vier groepen met metrische gegevens:

| Type | Description |
| --- | ---|
| Gericht | Geeft de IoT Edge apparaten weer die overeenkomen met de doelvoorwaarde voor de implementatie. |
| Toegepast | Geeft de beoogde IoT Edge apparaten weer die niet het doel zijn van een andere implementatie met een hogere prioriteit. |
| Rapportage geslaagd | Toont de IoT Edge apparaten die hebben gerapporteerd dat de modules zijn geïmplementeerd. |
| Fout bij rapporteren | Toont de IoT Edge apparaten die hebben gerapporteerd dat een of meer modules niet zijn geïmplementeerd. Als u de fout verder wilt onderzoeken, maakt u extern verbinding met deze apparaten en bekijkt u de logboekbestanden. |

De IoT Hub-service maakt deze gegevens beschikbaar voor controle in de Azure Portal en in de Azure CLI.

## <a name="monitor-a-deployment-in-the-azure-portal"></a>Een implementatie in de Azure Portal

Gebruik de volgende stappen om de details van een implementatie weer te geven en de apparaten te bewaken die de implementatie uitvoeren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en navigeer naar uw IoT Hub.
1. Selecteer **IoT Edge** in het menu van het linkerdeelvenster.
1. Selecteer het **tabblad IoT Edge implementaties.**
1. Inspecteer de implementatielijst. Voor elke implementatie kunt u de volgende details bekijken:

    | Kolom | Beschrijving |
    | --- | --- |
    | Id | De naam van de implementatie. |
    | Type | Het type implementatie, implementatie **of** **gelaagde implementatie.** |
    | Doelvoorwaarde | De tag die wordt gebruikt voor het definiëren van doelapparaten. |
    | Prioriteit | Het prioriteitsnummer dat is toegewezen aan de implementatie. |
    | Metrische systeemgegevens | Het aantal apparaat twins in IoT Hub die overeenkomen met de doelvoorwaarde. **Toegepast** geeft het aantal apparaten aan waar de implementatie-inhoud is toegepast op hun module-tweelingen in IoT Hub. |
    | Metrische apparaatgegevens | Het aantal apparaten IoT Edge of fouten rapporteert van de IoT Edge clientruntime. |
    | Aangepaste metrische gegevens | Het aantal apparaten IoT Edge dat gegevens rapporteert voor metrische gegevens die u voor de implementatie hebt gedefinieerd. |
    | Aanmaaktijd | De tijdstempel vanaf het moment dat de implementatie is gemaakt. Deze tijdstempel wordt gebruikt om de bindingen te verbreken wanneer twee implementaties dezelfde prioriteit hebben. |

1. Selecteer de implementatie die u wilt bewaken.  
1. Schuif op **de pagina Implementatiedetails** omlaag naar de onderste sectie en selecteer het tabblad **Doelvoorwaarde.** Selecteer **Weergave om** de apparaten weer te maken die overeenkomen met de doelvoorwaarde. U kunt de voorwaarde en ook de Prioriteit **wijzigen.** Selecteer **Opslaan als** u wijzigingen hebt aangebracht.

   ![Doelapparaten voor een implementatie weergeven](./media/how-to-monitor-iot-edge-deployments/target-devices.png)

1. Selecteer het **tabblad Metrische** gegevens. Als u een metrische gegevens kiest in de  **vervolgkeuzekeuzeknop** Metrische gegevens selecteren, wordt er een knop Weergeven weergegeven om de resultaten weer te geven. U kunt ook Metrische **gegevens bewerken selecteren** om de criteria aan te passen voor aangepaste metrische gegevens die u hebt gedefinieerd. Selecteer **Opslaan als** u wijzigingen hebt aangebracht.

   ![Metrische gegevens voor een implementatie weergeven](./media/how-to-monitor-iot-edge-deployments/deployment-metrics-tab.png)

Zie Een implementatie wijzigen als u wijzigingen wilt [aanbrengen in uw implementatie.](how-to-deploy-at-scale.md#modify-a-deployment)

## <a name="monitor-a-deployment-with-azure-cli"></a>Een implementatie bewaken met Azure CLI

Gebruik de [opdracht az iot edge deployment show](/cli/azure/iot/edge/deployment) om de details van één implementatie weer te geven:

```azurecli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

De opdracht voor het tonen van de implementatie heeft de volgende parameters:

* **--deployment-id:** de naam van de implementatie die in de IoT-hub bestaat. Vereiste parameter.
* **--hub-name:** de naam van de IoT-hub waarin de implementatie bestaat. De hub moet zich in het huidige abonnement. Schakel over naar het gewenste abonnement met de opdracht `az account set -s [subscription name]`

Inspecteer de implementatie in het opdrachtvenster. De **eigenschap metrische** gegevens geeft een telling weer voor elke metrische gegevens die door elke hub worden geëvalueerd:

* **targetedCount:** een metrische systeemwaarde die het aantal apparaattweelingen in de IoT Hub die overeenkomen met de doelvoorwaarde.
* **appliedCount:** een metrische gegevens van het systeem geeft het aantal apparaten aan dat de implementatie-inhoud heeft toegepast op hun module-tweelingen in IoT Hub.
* **reportedSuccessfulCount:** een metrische apparaatmetrische gegevens die het aantal IoT Edge-apparaten in de implementatie aangeeft dat de implementatie is geslaagd IoT Edge clientruntime.
* **reportedFailedCount:** een metrische gegevens voor apparaten die het aantal IoT Edge-apparaten in de implementatie specificeren dat een fout rapporteert vanuit de runtime IoT Edge client.

U kunt een lijst met apparaat-ID's of objecten voor elk van de metrische gegevens tonen met de [opdracht az iot edge deployment show-ric:](/cli/azure/iot/edge/deployment)

```azurecli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

De opdracht show-ric voor de implementatie heeft de volgende parameters:

* **--deployment-id:** de naam van de implementatie die in de IoT-hub bestaat.
* **--metric-id:** de naam van de metrische gegevens waarvoor u de lijst met apparaat-id's wilt zien, bijvoorbeeld `reportedFailedCount` .
* **--hub-name:** de naam van de IoT-hub waarin de implementatie bestaat. De hub moet zich in het huidige abonnement. Schakel over naar het gewenste abonnement met de opdracht `az account set -s [subscription name]` .

Zie Een implementatie wijzigen als u wijzigingen wilt [aanbrengen in uw implementatie.](how-to-deploy-cli-at-scale.md#modify-a-deployment)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het bewaken van [module-tweelingen,](how-to-monitor-module-twins.md)voornamelijk de IoT Edge Agent- en IoT Edge Hub-runtimemodules, voor de connectiviteit en status van uw IoT Edge implementaties.

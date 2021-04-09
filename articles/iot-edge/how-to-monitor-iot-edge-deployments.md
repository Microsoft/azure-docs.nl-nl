---
title: IoT Edge implementaties bewaken-Azure IoT Edge
description: Bewaking op hoog niveau, waaronder edgeHub en edgeAgent gerapporteerde eigenschappen en metrische gegevens voor automatische implementatie.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/21/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: f4f2f8522f6d3d1039673803d946323deb485db9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200250"
---
# <a name="monitor-iot-edge-deployments"></a>IoT Edge-implementaties bewaken

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge biedt rapportage waarmee u real-time informatie kunt controleren over de modules die op uw IoT Edge apparaten zijn geïmplementeerd. De IoT Hub-service haalt de status op van de apparaten en maakt ze beschikbaar voor de operator. Bewaking is ook belang rijk voor [implementaties die op schaal zijn gemaakt](module-deployment-monitoring.md) en die automatische implementaties en gelaagde implementaties bevatten.

Zowel apparaten als modules hebben vergelijk bare gegevens, zoals connectiviteit, zodat waarden worden verkregen op basis van de apparaat-ID of de module-ID.

De IoT Hub-service verzamelt gegevens die worden gerapporteerd door apparaat-en module apparaatdubbels en biedt een aantal verschillende statussen die apparaten kunnen hebben. Met de IoT Hub-service worden deze gegevens ingedeeld in vier metrische groepen:

| Type | Description |
| --- | ---|
| Gericht | Toont de IoT Edge apparaten die overeenkomen met de voor waarde voor de implementatie doelstelling. |
| Toegepast | Geeft de doel IoT Edge apparaten weer die niet zijn gericht op een andere implementatie van een hogere prioriteit. |
| Geslaagde rapportage | Hier worden de IoT Edge-apparaten weer gegeven die hebben gerapporteerd dat de modules zijn geïmplementeerd. |
| Fout rapportage | Hier worden de IoT Edge-apparaten weer gegeven die hebben gerapporteerd dat een of meer modules niet zijn geïmplementeerd. Als u de fout nader wilt onderzoeken, maakt u extern verbinding met die apparaten en bekijkt u de logboek bestanden. |

De IoT Hub-service maakt het u mogelijk deze gegevens te bewaken in de Azure Portal en in de Azure CLI.

## <a name="monitor-a-deployment-in-the-azure-portal"></a>Een implementatie in de Azure Portal bewaken

Voer de volgende stappen uit om de details van een implementatie weer te geven en te controleren welke apparaten worden uitgevoerd:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar uw IOT hub.
1. Selecteer **IOT Edge** in het menu van het linkerdeel venster.
1. Selecteer het tabblad **IOT Edge implementaties** .
1. Inspecteer de implementatie lijst. Voor elke implementatie kunt u de volgende gegevens bekijken:

    | Kolom | Beschrijving |
    | --- | --- |
    | Id | De naam van de implementatie. |
    | Type | Het type implementatie, **implementatie** of **gelaagde implementatie**. |
    | Doel voorwaarde | De code die wordt gebruikt voor het definiëren van doel apparaten. |
    | Prioriteit | Het prioriteits nummer dat is toegewezen aan de implementatie. |
    | Systeem gegevens | Het aantal apparaatdubbels in IoT Hub dat overeenkomt met de doel voorwaarde. Met **toegepast** geeft u op hoeveel apparaten de implementatie-inhoud hebben toegepast op hun module apparaatdubbels in IOT hub. |
    | Metrische gegevens van apparaat | Het aantal IoT Edge apparaten geslaagde of mislukte rapporten van de IoT Edge-client runtime. |
    | Aangepaste metrische gegevens | Het aantal IoT Edge apparaten dat gegevens rapporteert voor alle gegevens die u voor de implementatie hebt gedefinieerd. |
    | Aanmaak tijd | Het tijds tempel van toen de implementatie werd gemaakt. Deze tijds tempel wordt gebruikt om bindingen te verstoren wanneer twee implementaties dezelfde prioriteit hebben. |

1. Selecteer de implementatie die u wilt bewaken.  
1. Blader op de pagina **Implementatie Details** naar de onderste sectie en selecteer het tabblad **doel voorwaarde** . Selecteer **weer gave** om de apparaten op te geven die overeenkomen met de doel voorwaarde. U kunt de voor waarde wijzigen en ook de **prioriteit**. Selecteer **Opslaan** als u wijzigingen hebt aangebracht.

   ![Doel apparaten voor een implementatie weer geven](./media/how-to-monitor-iot-edge-deployments/target-devices.png)

1. Selecteer het tabblad **metrische gegevens** . Als u een metriek kiest in de vervolg keuzelijst **metriek selecteren** , wordt een **weergave** knop weer gegeven om de resultaten weer te geven. U kunt ook **metrische gegevens bewerken** selecteren om de criteria voor aangepaste metrische gegevens die u hebt gedefinieerd, aan te passen. Selecteer **Opslaan** als u wijzigingen hebt aangebracht.

   ![Metrische gegevens weer geven voor een implementatie](./media/how-to-monitor-iot-edge-deployments/deployment-metrics-tab.png)

Zie [een implementatie wijzigen](how-to-deploy-at-scale.md#modify-a-deployment)als u wijzigingen wilt aanbrengen in uw implementatie.

## <a name="monitor-a-deployment-with-azure-cli"></a>Een implementatie bewaken met Azure CLI

Gebruik de opdracht [az IOT Edge Deployment show](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-show) om de details van één implementatie weer te geven:

```azurecli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

De opdracht show voor de implementatie heeft de volgende para meters:

* **--implementatie-id** : de naam van de implementatie die in de IOT-hub bestaat. Vereiste parameter.
* **--hub-name** -naam van de IOT-hub waarin de implementatie bestaat. De hub moet zich in het huidige abonnement benemen. Overschakelen naar het gewenste abonnement met de opdracht `az account set -s [subscription name]`

Inspecteer de implementatie in het opdracht venster. De eigenschap **Metrics** bevat een aantal voor elke metrische waarde die door elke hub wordt geëvalueerd:

* **targetedCount** : een systeem metriek waarmee het aantal apparaatdubbels van het apparaat in IOT hub wordt opgegeven dat overeenkomt met de doel voorwaarde.
* **appliedCount** : met een systeem metriek geeft u het aantal apparaten op waarop de implementatie-inhoud is toegepast op de bijbehorende module apparaatdubbels in IOT hub.
* **reportedSuccessfulCount** : een metrische waarde van het apparaat waarmee het aantal IOT edge-apparaten wordt opgegeven in het geslaagde implementatie rapport van de IOT Edge-client runtime.
* **reportedFailedCount** : een metrische waarde van het apparaat waarmee het aantal IOT edge-apparaten wordt opgegeven in de implementatie rapportage fout van de IOT Edge-client runtime.

U kunt een lijst met apparaat-Id's of objecten voor elk van de metrische gegevens weer geven met de opdracht [az IOT Edge Deployment show-metric](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-show-metric) :

```azurecli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

De opdracht show-meet waarde accepteert de volgende para meters:

* **--implementatie-id** : de naam van de implementatie die in de IOT-hub bestaat.
* **--metric-id** : de naam van de metriek waarvoor u de lijst met apparaat-id's wilt zien `reportedFailedCount` .
* **--hub-name** -naam van de IOT-hub waarin de implementatie bestaat. De hub moet zich in het huidige abonnement benemen. Schakel over naar het gewenste abonnement met de opdracht `az account set -s [subscription name]` .

Zie [een implementatie wijzigen](how-to-deploy-cli-at-scale.md#modify-a-deployment)als u wijzigingen wilt aanbrengen in uw implementatie.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [bewaken van module apparaatdubbels](how-to-monitor-module-twins.md), voornamelijk de IOT Edge Agent en IOT Edge hub runtime modules, voor de connectiviteit en status van uw IOT Edge-implementaties.

---
title: Meer informatie over het ingebouwde eind punt van Azure IoT Hub | Microsoft Docs
description: 'Ontwikkelaars handleiding: beschrijft hoe u het ingebouwde, Event hub-compatibele eind punt gebruikt om apparaat-naar-Cloud-berichten te lezen.'
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/01/2020
ms.custom:
- amqp
- 'Role: Cloud Development'
ms.openlocfilehash: 4bb33721625f4fc752745ce2b43051c90b3aaa74
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92147672"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Apparaat-naar-cloud-berichten lezen van het geïntegreerde eindpunt

Standaard worden berichten gerouteerd naar het ingebouwde service-Facing-eind punt (**berichten/gebeurtenissen**) die compatibel zijn met [Event hubs](https://azure.microsoft.com/documentation/services/event-hubs/). Dit eind punt is momenteel alleen beschikbaar via het [AMQP](https://www.amqp.org/) -protocol op poort 5671. Een IoT-hub geeft de volgende eigenschappen aan om u in staat te stellen de ingebouwde Event hub-compatibele **berichten/gebeurtenissen** voor berichten-endpoint te beheren.

| Eigenschap            | Beschrijving |
| ------------------- | ----------- |
| **Aantal partities** | Stel deze eigenschap in op maken om het aantal [partities](../event-hubs/event-hubs-features.md#partitions) voor apparaat-naar-Cloud-gebeurtenis opname te definiëren. |
| **Retentietijd**  | Met deze eigenschap geeft u op hoe lang in dagen berichten worden bewaard door IoT Hub. De standaard waarde is één dag, maar kan tot zeven dagen worden verhoogd. |

Met IoT Hub is het bewaren van gegevens in de ingebouwde Event Hubs een maximum van 7 dagen toegestaan. U kunt de Bewaar periode instellen tijdens het maken van uw IoT Hub. De Bewaar tijd voor gegevens in IoT Hub is afhankelijk van uw IoT hub-laag en eenheids type. In het geval van grootte kan de ingebouwde Event Hubs berichten van de maximale bericht grootte bewaren tot ten minste 24 uur aan quotum. Bijvoorbeeld: voor 1 S1-eenheid IoT Hub voldoende opslag ruimte om ten minste 400K berichten van elke 4.000 grootte te bewaren. Als uw apparaten kleinere berichten verzenden, kunnen ze langer worden bewaard (Maxi maal 7 dagen), afhankelijk van de hoeveelheid opslag ruimte die wordt verbruikt. We garanderen dat de gegevens voor de opgegeven Bewaar tijd mini maal worden bewaard. Berichten verlopen en zijn niet toegankelijk nadat de Bewaar periode is verstreken. 

Met IoT Hub kunt u ook consumenten groepen beheren op het ingebouwde apparaat-naar-Cloud-ontvangst-eind punt. U kunt Maxi maal 20 consumenten groepen voor elke IoT Hub hebben.

Als u [bericht routering](iot-hub-devguide-messages-d2c.md) gebruikt en de [terugval route](iot-hub-devguide-messages-d2c.md#fallback-route) is ingeschakeld, gaan alle berichten die niet overeenkomen met een query op een route naar het ingebouwde eind punt. Als u deze terugval route uitschakelt, worden berichten die niet overeenkomen met een query, verwijderd.

U kunt de retentie tijd wijzigen, hetzij programmatisch met behulp van de [IOT hub resource provider rest api's](/rest/api/iothub/iothubresource)of met de [Azure Portal](https://portal.azure.com).

IoT Hub stelt het ingebouwde eind punt voor **berichten/gebeurtenissen** voor uw back-end-services in om de apparaat-naar-Cloud-berichten te lezen die door uw hub worden ontvangen. Dit eind punt is Event hub-compatibel, waarmee u alle mechanismen kunt gebruiken die door de Event Hubs-service worden ondersteund voor het lezen van berichten.

## <a name="read-from-the-built-in-endpoint"></a>Lezen van het ingebouwde eind punt

Sommige product integraties en Event Hubs Sdk's zijn op de hoogte van IoT Hub en stellen u in staat om uw IoT hub-service connection string te gebruiken om verbinding te maken met het ingebouwde eind punt.

Wanneer u Event Hubs Sdk's of product integraties gebruikt die niet op de hoogte zijn van IoT Hub, hebt u een event hub-compatibel eind punt en Event hub-compatibele naam nodig. U kunt deze waarden als volgt ophalen uit de portal:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en ga naar uw IoT Hub.

2. Klik op **ingebouwde eind punten**.

3. De **sectie Events** bevat de volgende waarden: **partities**, **een event hub-compatibele naam**, een **Event hub-compatibel eind punt**, **retentie tijd** en **consumenten groepen**.

    ![Apparaat-naar-Cloud-instellingen](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

In de portal bevat het veld met de Event hub-compatibel eind punt een volledig Event Hubs connection string dat er als volgt uitziet: **eind punt = SB://abcd1234namespace.servicebus.Windows.net/; SharedAccessKeyName = iothubowner; SharedAccessKey = keykeykeykeykeykey =; EntityPath = iothub-eHub-ABCD-1234-123456**. Als de SDK die u gebruikt andere waarden vereist, dan zou het volgende zijn:

| Name | Waarde |
| ---- | ----- |
| Eindpunt | sb://abcd1234namespace.servicebus.windows.net/ |
| Hostnaam | abcd1234namespace.servicebus.windows.net |
| Naamruimte | abcd1234namespace |

U kunt vervolgens elk gedeeld toegangs beleid kiezen in de vervolg keuzelijst, zoals wordt weer gegeven in de bovenstaande scherm afbeelding. Er wordt alleen beleid weer gegeven met de **ServiceConnect** -machtigingen om verbinding te maken met de opgegeven Event hub.

De Sdk's die u kunt gebruiken om verbinding te maken met het ingebouwde Event hub-compatibele eind punt dat IoT Hub beschikbaar is, zijn:

| Taal | SDK | Voorbeeld |
| -------- | --- | ------ |
| .NET | https://www.nuget.org/packages/Azure.Messaging.EventHubs | [Snelstartgids](quickstart-send-telemetry-dotnet.md) |
| Java | https://mvnrepository.com/artifact/com.azure/azure-messaging-eventhubs | [Snelstartgids](quickstart-send-telemetry-java.md) |
| Node.js | https://www.npmjs.com/package/@azure/event-hubs | [Snelstartgids](quickstart-send-telemetry-node.md) |
| Python | https://pypi.org/project/azure-eventhub/ | [Snelstartgids](quickstart-send-telemetry-python.md) |

De product integraties die u kunt gebruiken met het ingebouwde Event hub-compatibele eind punt dat IoT Hub beschikbaar zijn, zijn:

* [Azure functions](../azure-functions/index.yml). Zie [gegevens verwerken van IOT hub met Azure functions](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/).
* [Azure stream Analytics](../stream-analytics/index.yml). Bekijk [gegevens als invoer in stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).
* [Time Series Insights](../time-series-insights/index.yml). Zie [een logboek bron van een IOT hub toevoegen aan uw time series Insights-omgeving](../time-series-insights/how-to-ingest-data-iot-hub.md).
* [Apache Storm Spout](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). U kunt de [Spout-bron](https://github.com/apache/storm/tree/master/external/storm-eventhubs) weer geven op github.
* [Integratie van Apache Spark](../hdinsight/spark/apache-spark-ipython-notebook-machine-learning.md).
* [Azure Databricks](/azure/azure-databricks/).

## <a name="next-steps"></a>Volgende stappen

* Zie [IOT hub-eind punten](iot-hub-devguide-endpoints.md)voor meer informatie over IOT hub-eind punten.

* In de [Quick](quickstart-send-telemetry-node.md) starts ziet u hoe u apparaat-naar-Cloud-berichten van gesimuleerde apparaten kunt verzenden en de berichten van het ingebouwde eind punt kunt lezen. 

Zie voor meer informatie de zelf studie [IOT hub apparaat-naar-Cloud-berichten verwerken met routes](tutorial-routing.md) .

* Zie [bericht routes en aangepaste eind punten voor apparaat-naar-Cloud-berichten gebruiken](iot-hub-devguide-messages-read-custom.md)als u uw apparaat-naar-Cloud-berichten wilt door sturen naar aangepaste eind punten.
---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 08/20/2019
ms.openlocfilehash: c412f7f3e20e4d04083e457bfb245b850b65e126
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "70050389"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

Apparaatdubbels zijn JSON-documenten waarin statusinformatie van een apparaat zijn opgeslagen, zoals metagegevens, configuraties en voorwaarden. IoT Hub persistent voor elk apparaat dat verbinding maakt met het apparaat.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Apparaat apparaatdubbels gebruiken voor het volgende:

* Meta gegevens van het apparaat opslaan vanuit de back-end van uw oplossing.

* Rapporteert de huidige status informatie, zoals de beschik bare mogelijkheden en voor waarden, bijvoorbeeld de gebruikte verbindings methode, van de apparaat-app.

* Synchroniseer de status van langlopende werk stromen, zoals firmware en configuratie-updates, tussen een apparaat-app en een back-end-app.

* Query's uitvoeren op de meta gegevens, configuratie of status van uw apparaat.

Apparaatdubbels zijn ontworpen voor synchronisatie en voor het uitvoeren van query's op de configuraties en voor waarden van apparaten. Meer informatie over het gebruik van apparaatdubbels vindt u in inzicht in de apparaatdubbels van het [apparaat](../articles/iot-hub/iot-hub-devguide-device-twins.md).

Apparaatdubbels van apparaten worden opgeslagen in een IoT-hub en bevatten de volgende elementen:

* **Tags**. Meta gegevens van apparaten die alleen toegankelijk zijn voor de back-end van de oplossing.

* **Gewenste eigenschappen**. JSON-objecten die worden gewijzigd door de back-end van de oplossing en kunnen worden weer gegeven door de apparaat-app.

* **Gerapporteerde eigenschappen**. JSON-objecten die worden gewijzigd door de apparaat-app en kunnen worden gelezen door de back-end van de oplossing.

Tags en eigenschappen kunnen geen matrices bevatten, maar objecten kunnen worden genest.

In de volgende afbeelding ziet u de dubbele organisatie van het apparaat:

![Dubbele afbeelding van apparaat met functionaliteit](./media/iot-hub-selector-twin-get-started/twin.png)

Daarnaast kan de back-end van de oplossing het apparaat apparaatdubbels opvragen op basis van alle bovenstaande gegevens.
Zie voor meer informatie over device apparaatdubbels inzicht in de [apparaatdubbels](../articles/iot-hub/iot-hub-devguide-device-twins.md)van het apparaat. Zie [IOT hub query language (Engelstalig](../articles/iot-hub/iot-hub-devguide-query-language.md)) voor meer informatie over het uitvoeren van query's.


In deze zelfstudie ontdekt u hoe u:

* Maak een back-end-app waarmee Tags worden toegevoegd aan een apparaat, en een gesimuleerde apparaat-app die het verbindings kanaal rapporteert als een gerapporteerde eigenschap op het apparaat dubbele.

* Zoek apparaten uit uw back-end-app met behulp van filters voor de tags en eigenschappen die eerder zijn gemaakt.

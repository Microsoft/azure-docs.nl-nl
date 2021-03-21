---
title: Wat is Azure IoT Edge | Microsoft Docs
description: Overzicht van de service Azure IoT Edge
author: kgremban
manager: philmea
ms.reviewer: chipalost
ms.service: iot-edge
services: iot-edge
ms.topic: overview
ms.date: 10/28/2019
ms.author: kgremban
ms.custom: mvc
ms.openlocfilehash: e6c8e83838240b989c8c782840d8c771a6f6b7db
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103487799"
---
# <a name="what-is-azure-iot-edge"></a>Wat is Azure IoT Edge?

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge verplaatst cloud-analyse en aangepaste bedrijfslogica naar apparaten, zodat uw organisatie zich kan richten op zakelijke inzichten in plaats van gegevensbeheer. U kunt uw IoT-oplossing uitbreiden door uw bedrijfslogica te verpakken in standaardcontainers. Vervolgens kunt u die containers implementeren op elk gewenst apparaat en alles bewaken vanuit de cloud.

Analyses stimuleren bedrijfswaarde in IoT-oplossingen, maar niet alle analyses hoeven zich in de cloud te bevinden. Als u zo snel mogelijk wilt reageren op noodsituaties reageert, kunt u anomaliedetectie uitvoeren aan de rand. Als u de kosten voor de bandbreedte wilt verlagen en wilt voorkomen dat terabytes aan onbewerkte gegevens worden overgebracht, kunt u de gegevens lokaal opschonen en aggregeren. Vervolgens verzendt u alleen de inzichten naar de cloud voor analyse.

Azure IoT Edge bestaat uit drie onderdelen:

* **IoT Edge-modules** zijn containers die Azure-services, services van derden of uw eigen code uitvoeren. De modules zijn geïmplementeerd op IoT Edge-apparaten en worden lokaal uitgevoerd op deze apparaten.
* De **IoT Edge-runtime** wordt op elk IoT Edge-apparaat uitgevoerd en beheert de modules die op elk apparaat zijn geïmplementeerd.
* Met een **cloudinterface** kunt u op afstand de IoT Edge-apparaten controleren en beheren.

>[!NOTE]
>Azure IoT Edge is alleen beschikbaar in de prijscategorieën gratis en standaard van IoT Hub. De gratis optie is alleen bedoeld voor testen en evalueren. Raadpleeg [How to choose the right IoT Hub tier](../iot-hub/iot-hub-scaling.md) (De juiste IoT Hub-prijscategorie kiezen) voor meer informatie over de Basic- en Standard-prijscategorieën.

## <a name="iot-edge-modules"></a>IoT Edge-modules

IoT Edge-modules zijn eenheden van uitvoerbewerking die als compatibele Docker-containers zijn geïmplementeerd, die uw bedrijfslogica aan de rand uitvoeren. Er kunnen meerdere modules worden geconfigureerd om met elkaar te communiceren, waardoor er een pijplijn van gegevensverwerking ontstaat. U kunt aangepaste modules ontwikkelen of bepaalde Azure-services ordenen in modules die offline en aan de rand inzichten geven.

### <a name="artificial-intelligence-at-the-edge"></a>Kunstmatige intelligentie aan de rand

Met Azure IoT Edge kunt u verwerking van complexe gebeurtenissen, machine learning, afbeeldingsherkenning en andere vormen van kunstmatige intelligentie (AI) van hoge waarde implementeren zonder deze intern te schrijven. Azure-services zoals Azure Functions, Azure Stream Analytics en Azure Machine Learning kunnen allemaal on-premises worden uitgevoerd via Azure IoT Edge. U bent echter niet beperkt tot Azure-services. Iedereen kan AI-modules maken en deze beschikbaar stellen aan de community voor gebruik via Microsoft Azure Marketplace.

### <a name="bring-your-own-code"></a>Uw eigen code gebruiken

Als u uw eigen code op uw apparaten wilt implementeren, ondersteunt Azure IoT Edge deze ook. Azure IoT Edge bevat hetzelfde programmeermodel als de andere Azure IoT-services. U kunt dezelfde code uitvoeren op een apparaat of in de cloud. Azure IoT Edge ondersteunt Linux en Windows, dus u kunt coderen voor het platform van uw keuze. Het programma biedt ondersteuning voor Java, .NET Core 2.0, Node.js, C en Python. Uw ontwikkelaars kunnen dus in een taal coderen die ze al kennen en bestaande bedrijfslogica gebruiken.

## <a name="iot-edge-runtime"></a>IoT Edge-runtime

De Azure IoT Edge-runtime maakt aangepaste en cloudlogica op IoT Edge-apparaten mogelijk. De runtime bevindt zich op het IoT Edge-apparaat en voert beheer- en communicatiebewerkingen uit. De runtime voert verschillende functies uit:

* Installeert workloads op het apparaat en werkt deze bij.
* Onderhoudt de Azure IoT Edge-beveiligingsstandaarden op het apparaat.
* Zorgt ervoor dat de IoT Edge-modules altijd worden uitgevoerd.
* Rapporteert de status van de module aan de cloud voor externe bewaking.
* Beheert de communicatie tussen downstream bladknooppuntapparaten en een IoT Edge-apparaat, tussen modules op een IoT Edge-apparaat en tussen een IoT Edge-apparaat en de cloud.

![IoT Edge-runtime verzendt inzichten en rapportages naar IoT Hub](./media/about-iot-edge/runtime.png)

Hoe u een Azure IoT Edge-apparaat gebruikt, is aan u. De runtime wordt vaak gebruikt voor de implementatie van AI in gatewayapparaten die gegevens van andere lokale apparaten aggregeren en verwerken, maar dit implementatiemodel is slechts één optie.

De Azure IoT Edge-runtime wordt uitgevoerd op een groot aantal IoT-apparaten, zodat u de runtime op tal van manieren kunt gebruiken. Deze biedt ondersteuning voor Linux- en Windows-besturingssystemen en abstraheert hardwaregegevens. Gebruik een apparaat dat kleiner is dan een Raspberry Pi 3 als u niet veel gegevens verwerkt of gebruik een industriële server om resource-intensieve workloads uit te voeren.

## <a name="iot-edge-cloud-interface"></a>Cloudinterface van IoT Edge

Het is moeilijk om de levenscyclus van de software te beheren voor miljoenen IoT-apparaten van diverse makelij en verschillende modellen, uit wijdverspreide geografische regio's. Workloads worden gemaakt en geconfigureerd voor een specifiek type apparaat, geïmplementeerd op al uw apparaten en bewaakt om apparaten te detecteren die zich niet normaal gedragen. Deze activiteiten kunnen niet per apparaat worden uitgevoerd, maar moeten op schaal worden uitgevoerd.

Azure IoT Edge integreert naadloos met Azure IoT-oplossingsversnellers en biedt één controlelaag voor de behoeften van uw oplossing. Met cloudservices kunt u het volgende doen:

* een workload maken en configureren die op een specifiek type apparaat wordt uitgevoerd;
* een workload verzenden naar een reeks apparaten;
* workloads bewaken die worden uitgevoerd op apparaten in het veld.

![Apparaattelemetrie en acties worden gecoördineerd in de cloud](./media/about-iot-edge/cloud-interface.png)

## <a name="next-steps"></a>Volgende stappen

Probeer deze concepten door [IoT Edge op een gesimuleerd apparaat te implementeren](quickstart.md).

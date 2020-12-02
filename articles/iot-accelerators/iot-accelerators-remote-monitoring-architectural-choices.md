---
title: Mogelijkheden van de architectuur van oplossingen voor externe bewaking-Azure | Microsoft Docs
description: In dit artikel worden de architectuur en technische keuzes beschreven die zijn aangebracht in externe controle
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: conceptual
ms.openlocfilehash: 27a65d8a499f6eba130dc9537de3cb2b3dc3abe8
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446773"
---
# <a name="remote-monitoring-architectural-choices"></a>Architectuurkeuzes voor Externe bewaking

De oplossings versneller voor externe controle van Azure IoT is een open-source, MIT-licentie, oplossings versneller. Om u te helpen uw IoT-ontwikkelings proces te versnellen, worden veelvoorkomende IoT-scenario's weer gegeven, zoals:

- Connectiviteit van apparaten
- Apparaatbeheer
- Streamverwerking

De oplossing voor externe controle volgt de aanbevolen [Azure IOT-referentie architectuur](/azure/architecture/reference-architectures/iot).

In dit artikel worden de belangrijkste architectuur en technische keuzes beschreven die in elk van de subsystemen voor externe bewaking zijn gemaakt. De technische keuzen die micro soft in de oplossing voor controle op afstand heeft gemaakt, zijn echter niet de enige manier om een IoT-oplossing voor externe bewaking te implementeren. U dient rekening te houden met de technische implementatie als basis lijn voor het bouwen van een succes volle toepassing en moet u deze wijzigen in:

- Pas aan de beschik bare vaardig heden en ervaring in uw organisatie.
- Ontmoet uw verticale toepassings behoeften.

## <a name="architectural-choices"></a>Architectuurkeuzes

De architectuur die micro soft adviseert voor een IoT-toepassing is de native Cloud, micro service en serverloze. U moet de verschillende subsystemen van een IoT-toepassing bouwen als discrete services die u onafhankelijk kunt implementeren en schalen. Deze kenmerken bieden een grotere schaal, meer flexibiliteit bij het bijwerken van afzonderlijke subsystemen en bieden de flexibiliteit om een geschikte technologie voor elk subsysteem te kiezen.

U kunt Micro services implementeren met meer dan één technologie. U kunt bijvoorbeeld een van de volgende opties kiezen voor het implementeren van een micro service:

- Gebruik een container technologie zoals docker met serverloze technologie, zoals Azure Functions.
- Host uw micro Services in PaaS services zoals Azure-app Services.

## <a name="technology-choices"></a>Technologieopties

In deze sectie vindt u een overzicht van de technologie opties die zijn gemaakt in de oplossing voor externe controle voor elk van de kern subsystemen.

![Kern diagram](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png)

### <a name="cloud-gateway"></a>Cloudgateway

Azure IoT Hub wordt gebruikt als de Cloud gateway voor de externe bewakings oplossing. [IOT hub](https://azure.microsoft.com/services/iot-hub/) biedt veilige, bidirectionele communicatie met apparaten.

Voor IoT-apparaten kunt u het volgende gebruiken:

- De [IOT hub apparaat-sdk's](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) voor het implementeren van een systeem eigen client toepassing voor uw apparaat. De Sdk's bieden wrappers rond de IoT Hub REST API en omgaan met scenario's zoals nieuwe pogingen.
- De integratie met Azure IoT Edge voor het implementeren en beheren van aangepaste modules die in containers op uw apparaten worden uitgevoerd.
- De integratie met automatisch Apparaatbeheer in IoT Hub om verbonden apparaten in bulk te beheren.

### <a name="stream-processing"></a>Streamverwerking

Voor de verwerking van streams maakt de oplossing voor controle op afstand gebruik van Azure Stream Analytics voor complexe regel verwerking. Als u eenvoudigere regels wilt gebruiken, is er een aangepaste micro service met ondersteuning voor eenvoudige regel verwerking, hoewel deze instelling geen deel uitmaakt van de out-of-the-box-implementatie. De referentie architectuur raadt Azure Functions aan voor eenvoudige regel verwerking en Azure Stream Analytics voor complexe regel verwerking.

### <a name="storage"></a>Storage

Voor opslag gebruikt de oplossings versneller voor externe controle zowel Azure Time Series Insights als Azure Cosmos DB. Azure Time Series Insights slaat de berichten op die via IoT Hub van uw verbonden apparaten worden verzonden. De oplossings versneller gebruikt Azure Cosmos DB voor alle andere opslag, zoals koude opslag, regel definities, waarschuwingen en configuratie-instellingen.

Azure Cosmos DB is de aanbevolen warme opslag oplossing voor algemeen gebruik voor IoT-toepassingen. Oplossingen zoals Azure Time Series Insights en Azure Data Lake zijn echter geschikt voor veel use cases. Met Azure Time Series Insights kunt u meer inzicht krijgen in uw tijdreeks sensor gegevens door herkennen trends en afwijkingen. Met deze functie kunt u hoofd oorzaken van analyses uitvoeren en kost bare downtime voor komen.

> [!NOTE]
> Time Series Insights is momenteel niet beschikbaar in de Azure China-Cloud. Nieuwe implementaties van oplossingen voor externe bewaking in de cloud van Azure China gebruik Cosmos DB voor alle opslag.

### <a name="business-integration"></a>Bedrijfs integratie

Bedrijfs integratie in de oplossing voor externe controle is beperkt tot het genereren van waarschuwingen, die in warme opslag worden geplaatst. Verbind de oplossing met Azure Logic Apps om diep gaande scenario's voor bedrijfs integratie te implementeren.

### <a name="user-interface"></a>Gebruikersinterface

De Web-UI is gebouwd met Java script reageert. Reageren biedt een veelgebruikte Web-UI-interface voor de branche en is vergelijkbaar met andere populaire frameworks, zoals hoek.

### <a name="runtime-and-orchestration"></a>Runtime en indeling

De oplossing voor externe controle maakt gebruik van docker-containers om de subsystemen met Kubernetes uit te voeren als Orchestrator voor horizontale schaal baarheid. Deze architectuur maakt afzonderlijke schaal definities mogelijk voor elk subsysteem. Deze architectuur maakt echter DevOps kosten om de virtuele machines en containers up-to-date te houden en te beveiligen.

Alternatieven voor docker zijn onder andere hosting van micro Services in PaaS services zoals Azure App Service. Alternatieven voor Kubernetes zijn Orchestrator zoals Service Fabric, DC/OS of Swarm.

## <a name="next-steps"></a>Volgende stappen

* Implementeer [hier](https://www.azureiotsolutions.com/)uw oplossing voor externe controle.
* Verken GitHub-code in [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) en [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* Meer informatie over de IoT-referentie architectuur [vindt u hier](/azure/architecture/reference-architectures/iot).
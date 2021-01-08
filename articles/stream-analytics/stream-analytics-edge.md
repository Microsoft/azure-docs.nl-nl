---
title: Azure Stream Analytics op IoT Edge
description: Maak Edge-taken in Azure Stream Analytics en implementeer ze op apparaten met Azure IoT Edge.
ms.service: stream-analytics
author: an-emma
ms.author: raan
ms.topic: conceptual
ms.date: 12/18/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: c2a062b75caa84a0e3c342ca1ce45ccce12f2bb7
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98012611"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics op IoT Edge
 
Azure Stream Analytics op IoT Edge biedt ontwikkel aars de mogelijkheid om in realtime analytische intelligentie dichter bij IoT-apparaten te implementeren, zodat ze de volledige waarde van door het apparaat gegenereerde gegevens kunnen ontgrendelen. Azure Stream Analytics biedt een lage latentie, tolerantie en efficiënt gebruik van bandbreedte en naleving. Ondernemingen kunnen beheer logica op de industriële bewerkingen implementeren en een aanvulling maken op Big data-analyses die in de cloud worden uitgevoerd.

Azure Stream Analytics op IoT Edge worden uitgevoerd binnen het [Azure IOT Edge](https://azure.microsoft.com/campaigns/iot-edge/) Framework. Zodra de taak is gemaakt in Stream Analytics, kunt u deze implementeren en beheren met behulp van IoT Hub.

## <a name="common-scenarios"></a>Algemene scenario's

In deze sectie worden de algemene scenario's voor het Stream Analytics op IoT Edge beschreven. In het volgende diagram ziet u de gegevens stroom tussen IoT-apparaten en de Azure-Cloud.

:::image type="content" source="media/stream-analytics-edge/edge-high-level-diagram.png" alt-text="Diagram van IoT Edge op hoog niveau":::

### <a name="low-latency-command-and-control"></a>Opdracht en controle met lage latentie

Veiligheids systemen voor productie moeten reageren op operationele gegevens met een extreem lage latentie. Met Stream Analytics op IoT Edge kunt u sensor gegevens in bijna realtime analyseren en opdrachten verlenen wanneer u afwijkingen detecteert om een computer te stoppen of waarschuwingen te activeren.

### <a name="limited-connectivity-to-the-cloud"></a>Beperkte connectiviteit met de Cloud

Essentiële systemen, zoals apparaten voor externe analyse, verbonden vaten of offshore-boren, moeten gegevens analyseren en reageren, zelfs wanneer de verbinding met de Cloud regel matig wordt uitgevoerd. Met Stream Analytics wordt uw streaming Logic onafhankelijk van de netwerk verbinding uitgevoerd en kunt u kiezen wat u naar de Cloud verzendt voor verdere verwerking of opslag.

### <a name="limited-bandwidth"></a>Beperkte band breedte

De hoeveelheid gegevens die wordt geproduceerd door Jet-engines of verbonden auto's kan zo groot zijn dat gegevens moeten worden gefilterd of vooraf moeten worden verwerkt voordat deze naar de cloud worden verzonden. Met Stream Analytics kunt u de gegevens die naar de Cloud moeten worden verzonden, filteren of samen voegen.

### <a name="compliance"></a>Naleving

De naleving van de regelgeving kan vereisen dat sommige gegevens lokaal worden geanonimiseerd of worden geaggregeerd voordat ze naar de cloud worden verzonden.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Edge-taken in Azure Stream Analytics

Stream Analytics Edge-taken worden uitgevoerd in containers die zijn geïmplementeerd op [Azure IOT edge apparaten](../iot-edge/about-iot-edge.md). Edge-taken bestaan uit twee delen:

* Een Cloud onderdeel dat verantwoordelijk is voor de taak definitie: gebruikers definiëren invoer, uitvoer, query en andere instellingen, zoals niet-bestel gebeurtenissen, in de Cloud.

* Een module die wordt uitgevoerd op uw IoT-apparaten. De module bevat de Stream Analytics-engine en ontvangt de taak definitie van de Cloud. 

Stream Analytics gebruikt IoT Hub om Edge-taken op een of meer apparaten te implementeren. Zie [IOT Edge-implementatie](../iot-edge/module-deployment-monitoring.md)voor meer informatie.

:::image type="content" source="media/stream-analytics-edge/stream-analytics-edge-job.png" alt-text="Taak voor Azure Stream Analytics Edge":::

## <a name="edge-job-limitations"></a>Beperkingen van Edge-taken

Het doel is om pariteit tussen IoT Edge taken en Cloud taken te hebben. De meeste SQL-query taal functies worden ondersteund voor zowel de rand als de Cloud. De volgende functies worden echter niet ondersteund voor Edge-taken:
* Door de gebruiker gedefinieerde functies (UDF) in Java script. UDF is beschikbaar in [C# voor IOT Edge-taken](./stream-analytics-edge-csharp-udf.md) (preview-versie).
* Door de gebruiker gedefinieerde aggregaties (UDA).
* Azure ML-functies.
* AVRO-indeling voor invoer/uitvoer. Op dit moment worden alleen CSV en JSON ondersteund.
* De volgende SQL-Opera tors:
    * PARTITIONEREN OP
    * GetMetadataPropertyValue
* Beleid voor late aankomst

### <a name="runtime-and-hardware-requirements"></a>Runtime-en hardwarevereisten
Als u Stream Analytics op IoT Edge wilt uitvoeren, hebt u apparaten nodig die [Azure IOT Edge](https://azure.microsoft.com/campaigns/iot-edge/)kunnen uitvoeren. 

Stream Analytics en Azure IoT Edge **docker** -containers gebruiken om een draag bare oplossing te bieden die op meerdere hostbesturingssysteem (Windows, Linux) wordt uitgevoerd.

Stream Analytics op IoT Edge wordt beschikbaar gesteld als Windows-en Linux-installatie kopieën die worden uitgevoerd op x86-64-of ARM-architecturen (Advanced RISC machines). 


## <a name="input-and-output"></a>Invoer en uitvoer

Stream Analytics Edge-taken kunnen invoer en uitvoer ophalen van andere modules die op IoT Edge apparaten worden uitgevoerd. Als u verbinding wilt maken vanuit en naar specifieke modules, kunt u de routerings configuratie instellen tijdens de implementatie. Meer informatie vindt u in [de documentatie van de IOT Edge-module compositie](../iot-edge/module-composition.md).

Voor zowel invoer als uitvoer worden CSV-en JSON-indelingen ondersteund.

Voor elke invoer-en uitvoer stroom die u in uw Stream Analytics-taak maakt, wordt een bijbehorend eind punt gemaakt op uw geïmplementeerde module. Deze eind punten kunnen worden gebruikt in de routes van uw implementatie.

De volgende invoer typen worden ondersteund:
* Edge hub
* Event Hub
* IoT Hub

Ondersteunde uitvoer typen voor stream zijn:
* Edge hub
* SQL Database
* Event Hub
* Blob Storage/ADLS Gen2

Verwijzings invoer ondersteunt verwijzings bestands type. Andere uitvoer kan worden bereikt met behulp van een Cloud taak downstream. Een Stream Analytics taak die in Edge wordt gehost, verzendt bijvoorbeeld uitvoer naar Edge hub, waardoor uitvoer naar IoT Hub kan worden verzonden. U kunt een tweede Azure Stream Analytics-taak in de Cloud met invoer van IoT Hub en uitvoer naar Power BI of een ander uitvoer type gebruiken.

## <a name="license-and-third-party-notices"></a>Licentie en kennisgevingen van derden
* [Azure stream Analytics op IOT Edge-licentie](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Kennisgeving van derden voor Azure stream Analytics op IOT Edge](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="azure-stream-analytics-module-image-information"></a>Informatie over de installatie kopie van Azure Stream Analytics module 

Deze versie-informatie is voor het laatst bijgewerkt op 2020-09-21:

- Afbeelding: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-amd64`
   - basis installatie kopie: mcr.microsoft.com/dotnet/core/runtime:2.1.13-alpine
   - onafhankelijk
      - architectuur: amd64
      - besturings systeem: Linux
 
- Afbeelding: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm32v7`
   - basis installatie kopie: mcr.microsoft.com/dotnet/core/runtime:2.1.13-bionic-arm32v7
   - onafhankelijk
      - architectuur: arm
      - besturings systeem: Linux
 
- Afbeelding: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm64`
   - basis installatie kopie: mcr.microsoft.com/dotnet/core/runtime:3.0-bionic-arm64v8
   - onafhankelijk
      - architectuur: arm64
      - besturings systeem: Linux
      
      
## <a name="get-help"></a>Help opvragen
Ga voor meer hulp naar de [pagina micro soft Q&een vraag voor Azure stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Azure IoT Edge](../iot-edge/about-iot-edge.md)
* [Zelf studie voor Stream Analytics op IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Stream Analytics Edge-taken ontwikkelen met behulp van Visual Studio Tools](./stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [CI/CD voor Stream Analytics implementeren met behulp van Api's](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/

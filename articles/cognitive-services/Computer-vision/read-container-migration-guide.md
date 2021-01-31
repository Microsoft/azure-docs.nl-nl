---
title: Migreren naar de Read v3.x OCR-containers
titleSuffix: Azure Cognitive Services
description: Meer informatie over het migreren naar de v3 Read OCR-containers
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 01/29/2021
ms.author: aahi
ms.openlocfilehash: baa91e21979022064aaf13aca6079f8d092d673e
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221150"
---
# <a name="migrate-to-the-read-v3x-ocr-containers"></a>Migreer naar de Read v3.x OCR-containers

Als u versie 2 van de Computer Vision Read OCR-container gebruikt, raadpleegt u dit artikel voor meer informatie over het upgraden van uw toepassing voor het gebruik van versie 3.x van de container. 


## <a name="configuration-changes"></a>Configuratiewijzigingen

* `ReadEngineConfig:ResultExpirationPeriod` wordt niet meer ondersteund. De Read-container heeft een ingebouwde cron-taak waarmee de resultaten en metagegevens die zijn gekoppeld aan een aanvraag na 48 uur worden verwijderd.
* `Cache:Redis:Configuration` wordt niet meer ondersteund. De cache wordt niet gebruikt in de v3.x-containers, dus u hoeft deze niet in te stellen.

## <a name="api-changes"></a>API-wijzigingen

De Read v3.2-container gebruikt versie 3 van de Computer Vision-API en heeft de volgende eindpunten:

* `/vision/v3.2-preview.1/read/analyzeResults/{operationId}`
* `/vision/v3.2-preview.1/read/analyze`
* `/vision/v3.2-preview.1/read/syncAnalyze`

Raadpleeg de [Migratiehandleiding van Computer Vision v3 REST API](./upgrade-api-versions.md) voor gedetailleerde informatie over het bijwerken van uw toepassingen voor het gebruik van versie 3 van op de Read-API in de cloud. Deze informatie is ook van toepassing op de container. Houd er rekening mee dat synchronisatiebewerkingen alleen worden ondersteund in containers.

## <a name="memory-requirements"></a>Geheugenvereisten

De vereisten en aanbevelingen zijn gebaseerd op benchmarks met één aanvraag per seconde, met behulp van een afbeelding van 8 MB van een gescande zakelijke brief met 29 regels en een totaal van 803 tekens. In de volgende tabel wordt de minimale en aanbevolen toewijzing van resources voor elke Read-container beschreven.

|Container  |Minimum | Aanbevolen  |
|---------|---------|------|
|Read 3.2-preview | 8 kernen, 16 GB geheugen         | 8 kernen, 24 GB geheugen |

Elke kern moet ten minste 2,6 gigahertz (GHz) of sneller zijn.

Kern en geheugen komen overeen met de instellingen voor `--cpus` en `--memory` die worden gebruikt als onderdeel van de opdracht docker uitvoeren.

## <a name="storage-implementations"></a>Opslagimplementaties

>[!NOTE]
> MongoDB wordt niet meer ondersteund in 3.x-versies van de container. In plaats daarvan ondersteunen de containers Azure Storage en offline bestandssystemen.

| Implementatie |    Vereiste argument(en) voor Common Language Runtime |
|---------|---------|
|Bestandsniveau (standaard)    | Er zijn geen argumenten voor Common Language Runtime vereist. `/share` de map wordt gebruikt. |
|Azure Blob    | `Storage:ObjectStore:AzureBlob:ConnectionString={AzureStorageConnectionString}` |

## <a name="queue-implementations"></a>Wachtrij-implementaties

In v3.x van de container wordt RabbitMQ momenteel niet ondersteund. De ondersteunde backing-implementaties zijn:

| Implementatie | Argument(en) voor Common Language Runtime | Beoogd gebruik |
|---------|---------|-------|
| In het geheugen (standaard) | Er zijn geen argumenten voor Common Language Runtime vereist. | Ontwikkelen en testen |
| Azure-wachtrijen | `Queue:Azure:ConnectionString={AzureStorageConnectionString}` | Productie |
| RabbitMQ    | Niet beschikbaar | Productie |

Voor extra redundantie gebruikt de Read v3.x-container een zichtbaarheidstimer om ervoor te zorgen dat aanvragen goed kunnen worden verwerkt in het geval van een crash, wanneer deze wordt uitgevoerd in een configuratie met meerdere containers. 

Stel de timer in met `Queue:Azure:QueueVisibilityTimeoutInMilliseconds`, waarmee de tijd voor een bericht wordt ingesteld op onzichtbaar wanneer een andere werknemer het verwerkt. Om te voorkomen dat pagina's redundant worden verwerkt, is het raadzaam om de time-outperiode in te stellen op 120 seconden. De standaardwaarde is 30 seconden.

| Standaardwaarde | Aanbevolen waarde |
|---------|---------|
| 30.000 |    120000 |


## <a name="next-steps"></a>Volgende stappen

* Controleer [Containers configureren](computer-vision-resource-container-config.md) voor configuratie-instellingen
* Bekijk [Overzicht van Computer Vision ](overview.md) voor meer informatie over het herkennen van gedrukte en handgeschreven tekst
* Raadpleeg de [Computer Vision-API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) voor meer informatie over de methoden die door de container worden ondersteund.
* Raadpleeg [Veelgestelde vragen (FAQ)](FAQ.md) voor het oplossen van problemen met betrekking tot de functionaliteit van Computer Vision.
* Gebruik meer [Cognitive Services-containers](../cognitive-services-container-support.md)
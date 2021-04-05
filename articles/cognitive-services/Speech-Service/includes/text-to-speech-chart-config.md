---
title: Spraak containers installeren
titleSuffix: Azure Cognitive Services
description: Details van de configuratie opties voor de tekst-naar-spraak helm-grafiek.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 22168974ab8b285413b4fa6e947c05f65a73ae12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96002266"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Tekst-naar-spraak (subdiagram: grafieken/textToSpeech)

Als u het schema "paraplu" wilt overschrijven, voegt u het voor voegsel toe aan `textToSpeech.` een para meter om het meer specifiek te maken. Hiermee wordt bijvoorbeeld de overeenkomstige para meter overschreven zoals `textToSpeech.numberOfConcurrentRequest` onderdrukkingen `numberOfConcurrentRequest` .

|Parameter|Beschrijving|Standaard|
| -- | -- | -- |
| `enabled` | Hiermee wordt aangegeven of de **tekst-naar-spraak** -service is ingeschakeld. | `false` |
| `numberOfConcurrentRequest` | Het aantal gelijktijdige aanvragen voor de service **tekst naar spraak** . In deze grafiek worden automatisch de CPU-en geheugen bronnen berekend op basis van deze waarde. | `2` |
| `optimizeForTurboMode`| Of de service moet worden geoptimaliseerd voor tekst invoer via tekst bestanden. Als `true` Deze grafiek meer CPU-resource wordt toegewezen aan de service. | `false` |
| `image.registry`| Het REGI ster **voor tekst naar spraak** docker-installatie kopie. | `containerpreview.azurecr.io` |
| `image.repository` | De **tekst-naar-spraak** docker-installatie kopie opslag. | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | De **tekst naar spraak** docker-afbeeldings code. | `latest` |
| `image.pullSecrets` | De afbeeldings geheimen voor het ophalen van de docker-afbeelding voor **tekst naar spraak** . | |
| `image.pullByHash`| Hiermee wordt aangegeven of de docker-installatie kopie wordt opgehaald door hash. Als `true` `image.hash` is vereist. | `false` |
| `image.hash`| De hash van de **tekst naar spraak-** docker-installatie kopie. Wordt alleen gebruikt wanneer `image.pullByHash: true` .  | |
| `image.args.eula` lang | Hiermee wordt aangegeven dat u de licentie hebt geaccepteerd. De enige geldige waarde is `accept` | |
| `image.args.billing` lang | De waarde voor de URL van het facturerings eindpunt is beschikbaar op de overzichts pagina van het Azure Portal. | |
| `image.args.apikey` lang | Wordt gebruikt om facturerings gegevens bij te houden. ||
| `service.type` | Het Service type Kubernetes van de **tekst-naar-spraak** -service. Zie de [instructies voor het Kubernetes-Service type](https://kubernetes.io/docs/concepts/services-networking/service/) voor meer informatie en controleer de ondersteuning van cloud providers. | `LoadBalancer` |
| `service.port`|  De poort van de service voor **tekst naar spraak** . | `80` |
| `service.annotations` | De **tekst-naar-spraak** -aantekeningen voor de meta gegevens van de service. Annotaties zijn sleutel waardeparen. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Hiermee wordt aangegeven of de horizontale pod-functie voor [automatisch schalen](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) is ingeschakeld. Als `true` , `text-to-speech-autoscaler` wordt de geïmplementeerd in het Kubernetes-cluster. | `true` |
| `service.podDisruption.enabled` | Of het [pod-Verstorings budget](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) is ingeschakeld. Als `true` , `text-to-speech-poddisruptionbudget` wordt de geïmplementeerd in het Kubernetes-cluster. | `true` |
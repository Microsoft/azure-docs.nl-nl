---
title: Spraak containers installeren
titleSuffix: Azure Cognitive Services
description: Details van de configuratie opties voor de helm voor spraak naar tekst.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/05/2020
ms.author: trbye
ms.openlocfilehash: 85c4e0641e1989ddea6c8aa8b8a8895a966a5ddb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96002265"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Spraak naar tekst (subdiagram: grafieken/speechToText)

Als u het schema "paraplu" wilt overschrijven, voegt u het voor voegsel toe aan `speechToText.` een para meter om het meer specifiek te maken. Hiermee wordt bijvoorbeeld de overeenkomstige para meter overschreven zoals `speechToText.numberOfConcurrentRequest` onderdrukkingen `numberOfConcurrentRequest` .

|Parameter|Beschrijving|Standaard|
| -- | -- | -- |
| `enabled` | Hiermee wordt aangegeven of de **spraak-naar-tekst-** service is ingeschakeld. | `false` |
| `numberOfConcurrentRequest` | Het aantal gelijktijdige aanvragen voor de service voor **spraak naar tekst** . In deze grafiek worden automatisch de CPU-en geheugen bronnen berekend op basis van deze waarde. | `2` |
| `optimizeForAudioFile`| Of de service moet worden geoptimaliseerd voor audio-invoer via audio bestanden. Als `true` Deze grafiek meer CPU-resource wordt toegewezen aan de service. | `false` |
| `image.registry`| Het REGI ster **met spraak-naar-tekst** docker-installatie kopieën. | `containerpreview.azurecr.io` |
| `image.repository` | De afbeeldings opslagplaats **voor spraak-naar-tekst-** docker. | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | De tag **voor spraak-naar-tekst-** docker-afbeelding. | `latest` |
| `image.pullSecrets` | De afbeeldings geheimen voor het ophalen van de **spraak-naar-tekst** docker-installatie kopie. | |
| `image.pullByHash`| Hiermee wordt aangegeven of de docker-installatie kopie wordt opgehaald door hash. Als `true` `image.hash` is vereist. | `false` |
| `image.hash`| De hash van de **spraak-naar-tekst-** docker-installatie kopie. Wordt alleen gebruikt wanneer `image.pullByHash: true` .  | |
| `image.args.eula` lang | Hiermee wordt aangegeven dat u de licentie hebt geaccepteerd. De enige geldige waarde is `accept` | |
| `image.args.billing` lang | De waarde voor de URL van het facturerings eindpunt is beschikbaar op de overzichts pagina van het Azure Portal. | |
| `image.args.apikey` lang | Wordt gebruikt om facturerings gegevens bij te houden. ||
| `service.type` | Het Service type Kubernetes van de service **voor spraak naar tekst** . Zie de [instructies voor het Kubernetes-Service type](https://kubernetes.io/docs/concepts/services-networking/service/) voor meer informatie en controleer de ondersteuning van cloud providers. | `LoadBalancer` |
| `service.port`|  De poort van de service voor **spraak naar tekst** . | `80` |
| `service.annotations` | De aantekeningen voor **spraak naar tekst** voor de meta gegevens van de service. Annotaties zijn sleutel waardeparen. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Hiermee wordt aangegeven of de horizontale pod-functie voor [automatisch schalen](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) is ingeschakeld. Als `true` , `speech-to-text-autoscaler` wordt de geïmplementeerd in het Kubernetes-cluster. | `true` |
| `service.podDisruption.enabled` | Of het [pod-Verstorings budget](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) is ingeschakeld. Als `true` , `speech-to-text-poddisruptionbudget` wordt de geïmplementeerd in het Kubernetes-cluster. | `true` |

#### <a name="sentiment-analysis-sub-chart-chartsspeechtotext"></a>Sentiment analyse (subdiagram: Charts/speechToText)

Vanaf v 2.2.0 van de 0.2.0 van het type spraak naar tekst, worden de volgende para meters gebruikt voor het analyseren van sentiment met behulp van de Text Analytics-API.

|Parameter|Beschrijving|Waarden|Standaard|
| --- | --- | --- | --- |
|`textanalytics.enabled`| Of de **tekst-Analytics-** service is ingeschakeld| waar/onwaar| `false`|
|`textanalytics.image.registry`| Het REGI ster van de **tekst analyse** van docker-installatie kopie| geldig REGI ster van docker-installatie kopie| |
|`textanalytics.image.repository`| De bibliotheek met installatie kopieën voor de **tekst analyse-** docker| geldige opslag plaats voor docker-installatie kopie| |
|`textanalytics.image.tag`| De code van de docker-afbeelding voor **tekst analyse**| geldige docker-afbeeldings code| |
|`textanalytics.image.pullSecrets`| De afbeeldings geheimen voor het ophalen van de docker-afbeelding voor **tekst analyse**| geldige geheimen naam| |
|`textanalytics.image.pullByHash`| Hiermee geeft u op of u een docker-installatie kopie via hash haalt.  Als `yes` `image.hash` is ook vereist. Als `no` u deze instelt op ' false '. De standaardinstelling is `false`.| waar/onwaar| `false`|
|`textanalytics.image.hash`| De hash van de docker-afbeelding voor **tekst analyse** . Gebruik deze alleen met `image.pullByHash:true` .| geldige hash voor docker-installatie kopie | |
|`textanalytics.image.args.eula`| Een van de vereiste argumenten per **tekst-Analytics-** container, waarmee wordt aangegeven dat u de licentie hebt geaccepteerd. De waarde van deze optie moet zijn: `accept` .| `accept`Als u de container wilt gebruiken | |
|`textanalytics.image.args.billing`| Een van de vereiste argumenten per **tekst-Analytics-** container, waarmee de URI van het facturerings eindpunt wordt opgegeven. De waarde voor de URL van het facturerings eindpunt is beschikbaar op de overzichts pagina van het Azure Portal.|geldige URI voor het facturerings eindpunt||
|`textanalytics.image.args.apikey`| Een van de vereiste argumenten voor de **tekst-Analytics-** container, die wordt gebruikt voor het bijhouden van facturerings gegevens.| geldige apikey||
|`textanalytics.cpuRequest`| De aangevraagde CPU voor de **tekst-Analytics** -container| int| `3000m`|
|`textanalytics.cpuLimit`| De beperkte CPU voor de container voor **tekst analyse**| | `8000m`|
|`textanalytics.memoryRequest`| Het aangevraagde geheugen voor de container voor **tekst analyse**| | `3Gi`|
|`textanalytics.memoryLimit`| Het beperkte geheugen voor de container voor **tekst analyse**| | `8Gi`|
|`textanalytics.service.sentimentURISuffix`| Het sentiment-analyse-URI-achtervoegsel, de volledige URI heeft de notatie ' http:// `<service>` : `<port>` / `<sentimentURISuffix>` '. | | `text/analytics/v3.0-preview/sentiment`|
|`textanalytics.service.type`| Het type **tekst analyse** service in Kubernetes. Zie [Kubernetes-service typen](https://kubernetes.io/docs/concepts/services-networking/service/) | geldig Kubernetes-Service type | `LoadBalancer` |
|`textanalytics.service.port`| De poort van de **tekst analyse** service| int| `50085`|
|`textanalytics.service.annotations`| De aantekeningen die gebruikers aan de meta gegevens van de **tekst analyse** service kunnen toevoegen. Bijvoorbeeld:<br/> **aantekeningen**<br/>`   `**enkele/annotation1: waarde1**<br/>`  `**sommige/annotation2: waarde2** | aantekeningen, één per regel| |
|`textanalytics.serivce.autoScaler.enabled`| Hiermee wordt aangegeven of [horizon taal pod automatisch schalen](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) is ingeschakeld. Indien ingeschakeld, `text-analytics-autoscaler` wordt geïmplementeerd in het Kubernetes-cluster | waar/onwaar| `true`|
|`textanalytics.service.podDisruption.enabled`| Of het [pod-Verstorings budget](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) is ingeschakeld. Indien ingeschakeld, `text-analytics-poddisruptionbudget` wordt geïmplementeerd in het Kubernetes-cluster| waar/onwaar| `true`|

---
title: Azure Event Grid IoT Edge voor de module dubbele Microsoft Docs
description: Configuratie via module twee.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: f39d22fe58d4375b3b68bacd237c1b200328c4b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171326"
---
# <a name="module-twin-json-schema-azure-event-grid"></a>Module dubbele JSON-schema (Azure Event Grid)

Event Grid op IoT Edge integreert met het IoT Edge ecosysteem en ondersteunt het maken van onderwerpen en abonnementen via de module twee. Ook wordt de huidige status van alle onderwerpen en gebeurtenis abonnementen gerapporteerd aan de gerapporteerde eigenschappen van de module dubbele.

> [!WARNING]
> Vanwege beperkingen in het IoT Edge ecosysteem zijn alle matrix elementen in het volgende JSON-voor beeld gecodeerd als JSON-teken reeksen. Zie `EventSubscription.Filter.EventTypes` en `EventSubscription.Filter.AdvancedFilters` sleutels in het volgende voor beeld.

## <a name="desired-properties-json"></a>Gewenste eigenschappen JSON

* De waarde van elk sleutel-waardepaar in het gedeelte topics heeft precies hetzelfde JSON-schema dat wordt gebruikt voor `Topic.Properties` de API bij het maken van onderwerpen.
* De waarde van elk sleutel-waardepaar in het gedeelte **EventSubscriptions** heeft precies hetzelfde JSON-schema dat wordt gebruikt voor `EventSubscription.Properties` de API bij het maken van onderwerpen.
* Als u een onderwerp wilt verwijderen, stelt u de waarde `null` in op in de gewenste eigenschappen.
* Het verwijderen van gebeurtenis abonnementen via gewenste eigenschappen wordt niet ondersteund.

```json
{
    "topics": {
        "twinTopic1": {},
        "twinTopic2": {
            "inputSchema": "customEventSchema"
        }
    },
    "eventSubscriptions": {
        "twinTopic1WebhookSub": {
            "topic": "twinTopic1",
            "retryPolicy": {
                "eventExpiryInMinutes": 120,
                "maxDeliveryAttempts": 30
            },
            "destination": {
                "endpointType": "WebHook",
                "properties": {
                    "endpointUrl": "https://localhost:4438"
                }
            },
            "filter": {
                "subjectBeginsWith": "^",
                "subjectEndsWith": "$",
                "isSubjectCaseSensitive": false,
                "includedEventTypes": "[\"et1\",\"et2\"]",
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"data.b\"},{\"values\":[\"\\\"\",\"c\"],    \"operatorType\":\"StringContains\",\"key\":\"data.s\"}]"
            }
        },
        "twinTopic2EdgeHubSub": {
            "topic": "twinTopic2",
            "deliveryPolicy": {
                "approxBatchSizeInBytes": 200000,
                "maxEventsPerBatch": 25
            },
            "destination": {
                "endpointType": "EdgeHub",
                "properties": {
                    "outputName": "twinTopic2EdgeHubSub"
                }
            },
            "filter": {
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"dAt\\\"A.a\"},{\"values\":[\"\\\"\",    \"c\"],\"operatorType\":\"StringContains\",\"key\":\"dAt\\\"A.a\"}]"
            }
        }
    }
}
```

## <a name="reported-properties-json"></a>Gerapporteerde eigenschappen JSON

De sectie gerapporteerde eigenschappen van de module dubbele bevat de volgende informatie:

* De set met onderwerpen en abonnementen die voor komen in de Store van de module
* Er zijn fouten opgetreden bij het maken van de gewenste onderwerpen/gebeurtenis abonnementen
* Eventuele opstart fouten (zoals de gewenste eigenschappen JSON parseren is mislukt)

```json
{
    "topics": {
        "twinTopic1": {},
        "twinTopic2": {
            "inputSchema": "customEventSchema"
        }
    },
    "eventSubscriptions": {
        "twinTopic1WebhookSub": {
            "topic": "twinTopic1",
            "retryPolicy": {
                "eventExpiryInMinutes": 120,
                "maxDeliveryAttempts": 30
            },
            "destination": {
                "endpointType": "WebHook",
                "properties": {
                    "endpointUrl": "https://localhost:4438"
                }
            },
            "filter": {
                "subjectBeginsWith": "^",
                "subjectEndsWith": "$",
                "isSubjectCaseSensitive": false,
                "includedEventTypes": "[\"et1\",\"et2\"]",
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"data.b\"},{\"values\":[\"\\\"\",\"c\"],    \"operatorType\":\"StringContains\",\"key\":\"data.s\"}]"
            }
        },
        "twinTopic2EdgeHubSub": {
            "topic": "twinTopic2",
            "deliveryPolicy": {
                "approxBatchSizeInBytes": 200000,
                "maxEventsPerBatch": 25
            },
            "destination": {
                "endpointType": "EdgeHub",
                "properties": {
                    "outputName": "twinTopic2EdgeHubSub"
                }
            },
            "filter": {
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"dAt\\\"A.a\"},{\"values\":[\"\\\"\",    \"c\"],\"operatorType\":\"StringContains\",\"key\":\"dAt\\\"A.a\"}]"
            }
        }
    },
    "errors": {
        "bootupMessage": "",
        "bootupException": "",
        "topics": {},
        "eventSubscriptions": {
            "twinTopic1EventGridUserTopicSub": "HttpStatusCode='BadRequest' ErrorCode='InvalidDestination' Message='EventSubscription.Properties.Destination failed validation. Reason: EndpointUrl must target the /api/events API of Azure Event Grid in the cloud..'"
        }
    }
}
```

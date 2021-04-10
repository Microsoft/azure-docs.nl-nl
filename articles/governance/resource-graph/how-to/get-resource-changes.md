---
title: Resourcewijzigingen ophalen
description: Meer informatie over hoe u kunt zoeken wanneer een resource is gewijzigd, een lijst met de gewijzigde eigenschappen ophalen en de verschillen evalueren.
ms.date: 01/27/2021
ms.topic: how-to
ms.openlocfilehash: 5ad86ec2598cd7f24b8e0cd2208889bb7a088568
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594641"
---
# <a name="get-resource-changes"></a>Resourcewijzigingen ophalen

Resources worden gewijzigd in de loop van dagelijks gebruik, herconfiguratie en zelfs opnieuw implementeren.
Wijzigingen kunnen afkomstig zijn van een individu of een geautomatiseerd proces. De meeste wijziging is standaard, maar soms niet. Met de laatste 14 dagen van de wijzigings geschiedenis kunt u met Azure resource Graph het volgende doen:

- Zoeken wanneer er wijzigingen zijn gedetecteerd voor een Azure Resource Manager eigenschap
- Zie voor elke resource wijziging Details van eigenschaps wijziging
- Bekijk een volledige vergelijking van de resource vóór en na de gedetecteerde wijziging

Wijzigings detectie en Details zijn waardevol voor de volgende voorbeeld scenario's:

- Tijdens incident beheer om inzicht te krijgen in _mogelijk_ gerelateerde wijzigingen. Query's voor wijzigings gebeurtenissen tijdens een bepaald tijd venster en de wijzigings Details evalueren.
- Een configuratie beheer database, ook wel een CMDB, up-to-date houden. In plaats van alle resources en de volledige eigenschappen sets op een geplande frequentie te vernieuwen, krijgt u alleen de gewijzigde gegevens te zien.
- Meer informatie over welke andere eigenschappen mogelijk zijn gewijzigd wanneer de nalevings status van een resource is gewijzigd. Evaluatie van deze aanvullende eigenschappen kan inzicht bieden in andere eigenschappen die mogelijk moeten worden beheerd via een Azure Policy definitie.

In dit artikel wordt uitgelegd hoe u deze informatie kunt verzamelen via de SDK van resource Graph. Zie de wijzigings [geschiedenis](../../policy/how-to/determine-non-compliance.md#change-history) van Azure Policy of de [wijzigings geschiedenis](../../../azure-monitor/essentials/activity-log.md#view-the-activity-log)van het Azure-activiteiten logboek om deze informatie te bekijken in de Azure Portal. Zie voor meer informatie over wijzigingen in uw toepassingen van de laag van de infra structuur de implementatie van toepassingen [(preview-versie) gebruiken](../../../azure-monitor/app/change-analysis.md) in azure monitor.

> [!NOTE]
> Details wijzigen in resource grafiek zijn voor eigenschappen van Resource Manager. Zie voor het bijhouden van wijzigingen in een virtuele machine de Azure Automation [Wijzigingen bijhouden](../../../automation/change-tracking/overview.md) of de [gast configuratie](../../policy/concepts/guest-configuration.md)van de Azure Policy voor vm's.

> [!IMPORTANT]
> De wijzigings geschiedenis in azure resource Graph bevindt zich in de open bare preview.

## <a name="find-detected-change-events-and-view-change-details"></a>Gedetecteerde wijzigings gebeurtenissen zoeken en Details van wijziging weer geven

De eerste stap in het weer geven van wat er is gewijzigd in een resource is het vinden van de wijzigings gebeurtenissen die betrekking hebben op die resource binnen een tijd venster. Elke wijzigings gebeurtenis bevat ook informatie over wat er is gewijzigd voor de resource. Deze stap wordt uitgevoerd via het **resourceChanges** rest-eind punt.

Het **resourceChanges** -eind punt accepteert de volgende para meters in de hoofd tekst van de aanvraag:

- **resourceId** \[ vereist \] : de Azure-resource voor het zoeken naar wijzigingen.
- **interval** \[ vereist \] : een eigenschap met _begin_ -en _eind_ datums voor wanneer moet worden gecontroleerd op een wijzigings gebeurtenis met behulp van de **Zulu-tijd zone (Z)**.
- **fetchPropertyChanges** (optioneel): een Booleaanse eigenschap die instelt of het antwoord object eigenschaps wijzigingen bevat.

Voor beeld van aanvraag tekst:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-09-28T00:00:00.000Z",
        "end": "2019-09-29T00:00:00.000Z"
    },
    "fetchPropertyChanges": true
}
```

Met de bovenstaande aanvraag tekst is de REST API URI voor **resourceChanges** :

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

Het antwoord ziet er ongeveer uit als in dit voor beeld:

```json
{
    "changes": [
        {
            "changeId": "{\"beforeId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"beforeTime\":\"2019-09-28T00:45:35.012Z\",\"afterId\":\"6178968e-981e-4dac-ac37-340ee73eb577\",\"afterTime\":\"2019-09-28T00:52:53.371Z\"}",
            "beforeSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "afterSnapshot": {
                "snapshotId": "6178968e-981e-4dac-ac37-340ee73eb577",
                "timestamp": "2019-09-28T00:52:53.371Z"
            },
            "changeType": "Create"
        },
        {
            "changeId": "{\"beforeId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"beforeTime\":\"2019-09-28T00:43:38.366Z\",\"afterId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"afterTime\":\"2019-09-28T00:45:35.012Z\"}",
            "beforeSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "afterSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "changeType": "Delete"
        },
        {
            "changeId": "{\"beforeId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"beforeTime\":\"2019-09-28T00:43:15.518Z\",\"afterId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"afterTime\":\"2019-09-28T00:43:38.366Z\"}",
            "beforeSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "afterSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "propertyChanges": [
                {
                    "propertyName": "tags.org",
                    "afterValue": "compute",
                    "changeCategory": "User",
                    "changeType": "Insert"
                },
                {
                    "propertyName": "tags.team",
                    "afterValue": "ARG",
                    "changeCategory": "User",
                    "changeType": "Insert"
                }
            ],
            "changeType": "Update"
        },
        {
            "changeId": "{\"beforeId\":\"19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268\",\"beforeTime\":\"2019-09-28T00:42:46.839Z\",\"afterId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"afterTime\":\"2019-09-28T00:43:15.518Z\"}",
            "beforeSnapshot": {
                "snapshotId": "19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268",
                "timestamp": "2019-09-28T00:42:46.839Z"
            },
            "afterSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "propertyChanges": [{
                "propertyName": "tags.cgtest",
                "afterValue": "hello",
                "changeCategory": "User",
                "changeType": "Insert"
            }],
            "changeType": "Update"
        }
    ]
}
```

Elke gedetecteerde wijzigings gebeurtenis voor de **resourceId** heeft de volgende eigenschappen:

- **changeId** : deze waarde is uniek voor die bron. Hoewel de **changeId** -teken reeks soms andere eigenschappen kan bevatten, is het alleen gegarandeerd uniek.
- **beforeSnapshot** : bevat de **snapshotId** en de **tijds tempel** van de resource momentopname die is gemaakt voordat een wijziging is gedetecteerd.
- **afterSnapshot** : bevat de **snapshotId** en de **tijds tempel** van de resource momentopname die is gemaakt nadat een wijziging is gedetecteerd.
- **Change type** : beschrijft het type wijziging dat is gedetecteerd voor de hele wijzigings record tussen de **beforeSnapshot** en **afterSnapshot**. Waarden zijn: _maken_, _bijwerken_ en _verwijderen_. De **propertyChanges** -eigenschaps matrix wordt alleen opgenomen als **Change type** is _bijgewerkt_.
- **propertyChanges** -deze matrix met eigenschappen bevat alle bron eigenschappen die zijn bijgewerkt tussen de **BeforeSnapshot** en de **afterSnapshot**:
  - **PropertyName** : de naam van de resource-eigenschap die is gewijzigd.
  - **changeCategory** -Hiermee wordt beschreven wat de wijziging heeft aangebracht. Waarden zijn: _systeem_ en _gebruiker_.
  - **Change type** -Hiermee wordt het type wijziging dat is gedetecteerd voor de afzonderlijke bron eigenschap beschreven.
    Waarden zijn: _Invoegen_, _bijwerken_, _verwijderen_.
  - **beforeValue** : de waarde van de eigenschap resource in de **beforeSnapshot**. Wordt niet weer gegeven wanneer **Change type** is _ingevoegd_.
  - **afterValue** : de waarde van de eigenschap resource in de **afterSnapshot**. Wordt niet weer gegeven wanneer **Change type** wordt _verwijderd_.

## <a name="compare-resource-changes"></a>Resource wijzigingen vergelijken

Met de **changeId** van het **resourceChanges** -eind punt wordt vervolgens het **resourceChangeDetails** rest-eind punt gebruikt om de moment opnamen vóór en na te halen van de resource die is gewijzigd.

Het **resourceChangeDetails** -eind punt vereist twee para meters in de aanvraag tekst:

- **resourceId**: de Azure-resource waarmee wijzigingen worden vergeleken.
- **changeId**: de unieke wijzigings gebeurtenis voor de **resourceId** die is verzameld van **resourceChanges**.

Voor beeld van aanvraag tekst:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"afterTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

Met de bovenstaande aanvraag tekst is de REST API URI voor **resourceChangeDetails** :

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

Het antwoord ziet er ongeveer uit als in dit voor beeld:

```json
{
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
    "beforeSnapshot": {
        "timestamp": "2019-03-29T01:32:05.993Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": false,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    },
    "afterSnapshot": {
        "timestamp": "2019-03-29T01:54:24.42Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": true,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    }
}
```

**beforeSnapshot** en **afterSnapshot** geven elk het tijdstip waarop de moment opname is gemaakt en de eigenschappen op dat moment. De wijziging is op een bepaald moment tussen deze moment opnamen. In het vorige voor beeld zien we dat de gewijzigde eigenschap is **supportsHttpsTrafficOnly**.

Als u de resultaten wilt vergelijken, gebruikt u de eigenschap **Changes** in **resourceChanges** of evalueert u het **inhouds** gedeelte van elke moment opname in **resourceChangeDetails** om het verschil te bepalen. Als u de moment opnamen vergelijkt, wordt de **tijds tempel** altijd als een verschil weer gegeven ondanks de verwachte tijd.

## <a name="next-steps"></a>Volgende stappen

- Zie de taal die wordt gebruikt in [Starter-query's](../samples/starter.md).
- Zie [Geavanceerde query's](../samples/advanced.md) voor geavanceerde gebruikswijzen.
- Meer informatie over het [verkennen van resources](../concepts/explore-resources.md).
- Zie [richt lijnen voor beperkte aanvragen](../concepts/guidance-for-throttled-requests.md)voor meer informatie over het werken met query's met een hoge frequentie.

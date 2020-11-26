---
title: Azure Monitor-API buiten gebruik stellen
description: Beschrijft de buiten gebruiks telling van oudere versies van de OperationalInsights-resource provider-API.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/29/2020
ms.openlocfilehash: e2b12d7a2206ab369328563af438c6ef1ea39327
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184107"
---
# <a name="operationalinsights-api-version-retirement"></a>OperationalInsights API-versie buiten gebruik stellen
Micro soft biedt een kennisgeving van ten minste twaalf maanden voor het buiten gebruik stellen van een API om de overgang naar een nieuwere/ondersteunde versie te versoepelen. We hebben een nieuwe versie (2020-08-01) uitgebracht voor de Api's van de **OperationalInsights** -resource provider en zullen eerdere API-versies op 29 februari 2024 buiten gebruik stellen.

We raden u aan om versie 2020-08-01 nu te gaan gebruiken om te profiteren van de voor delen van nieuwe functionaliteit, zoals een [toegewezen cluster](../log-query/logs-dedicated-clusters.md), door de [klant beheerde sleutels](./customer-managed-keys.md), [persoonlijke koppeling](./private-link-security.md) en [gegevens export](./logs-data-export.md). Daarnaast worden nieuwe functies en functionaliteit en optimalisaties alleen toegevoegd aan de huidige API.

Na 29 februari 2024 biedt Azure Monitor geen ondersteuning meer voor eerdere versies van Api's dan 2020-08-01. Als u liever geen upgrade uitvoert, worden aanvragen die zijn verzonden vanuit eerdere versies, nog steeds verwerkt door de Azure Monitor-service tot en met 29 februari 2024.

## <a name="migration-steps"></a>Migratiestappen
Afhankelijk van de configuratie methode die u gebruikt, moet u de nieuwe versie bijwerken in **rest** -aanvragen en **Resource Manager-sjablonen**. Volg de onderstaande voor beelden om de API-versie bij te werken:

1. REST API aanvragen gebruiken de API-versie in de URL van de aanvraag. Vervang die versie door de nieuwste versie (2020-08-01), zoals wordt weer gegeven in het volgende voor beeld.

    ```rest
    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}?api-version=2020-08-01
    ```

2. Azure Resource Manager sjablonen gebruiken de API-versie in de eigenschap **apiVersion** van de resource. Vervang die versie door de nieuwste versie (2020-08-01), zoals wordt weer gegeven in het volgende voor beeld.


    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string",
                "metadata": {
                "description": "Name of the workspace."
                }
            },
            "resources": [
            {
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('workspaceName')]",
                "apiVersion": "2020-08-01",
                "location": "westus",
                "properties": {
                    "sku": {
                        "name": "pergb2018"
                    },
                    "retentionInDays": 30,
                    "features": {
                        "searchVersion": 1,
                        "legacy": 0,
                        "enableLogAccessUsingOnlyResourcePermissions": true
                    }
                }
            }
        ]
    }
    }
    ```


## <a name="next-steps"></a>Volgende stappen

- Zie de [Naslag informatie voor de OperationalInsights-werkruimte-API](/rest/api/loganalytics/workspaces).
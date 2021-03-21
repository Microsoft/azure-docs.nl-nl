---
title: Jacht-en Livestream-query's beheren in azure Sentinel met behulp van REST API | Microsoft Docs
description: In dit artikel wordt beschreven hoe u met de functies van Azure Sentinel-jacht kunt profiteren van Log Analytics REST API voor het beheren van jacht-en Livestream-query's.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2020
ms.author: yelevin
ms.openlocfilehash: 64f05e18ff757d9f086cf06d74109bf64e32a05c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98795682"
---
# <a name="manage-hunting-and-livestream-queries-in-azure-sentinel-using-rest-api"></a>Opsporings- en livestreamquery's beheren in Azure Sentinel met behulp van REST API

Met Azure Sentinel, die deel uitmaakt van Azure Monitor Log Analytics, kunt u Log Analytics REST API gebruiken voor het beheren van jacht-en Livestream-query's. Dit document bevat informatie over het maken en beheren van jacht-query's met behulp van de REST API.  Query's die op deze manier zijn gemaakt, worden weer gegeven in de Azure Sentinel-gebruikers interface.

Raadpleeg de definitieve REST API-referentie voor meer informatie over de [opgeslagen Zoek opdrachten-API](/rest/api/loganalytics/savedsearches).

## <a name="api-examples"></a>API-voorbeelden

Vervang in de volgende voor beelden deze tijdelijke aanduidingen door de vereiste vervanging in de volgende tabel:

| Tijdelijke aanduiding | Vervangen door |
|-|-|
| **Abonnements** | de naam van het abonnement waarop u de jacht-of Livestream-query toepast. |
| **ResourceGroupName** | de naam van de resource groep waarop u de jacht-of Livestream-query toepast. |
| **{savedSearchId}** | een unieke id (GUID) voor elke jacht-query. |
| **WorkspaceName** | de naam van de Log Analytics werk ruimte die het doel van de query is. |
| **DisplayName** | een weergave naam van uw keuze voor de query. |
| **Beschrijvingen** | een beschrijving van de jacht-of Livestream-query. |
| **Strategieën** | de relevante MITRE ATT&verzonken-tactieken die van toepassing zijn op de query. |
| **Ophalen** | de query-expressie voor de query. |
|  

### <a name="example-1"></a>Voorbeeld 1

Dit voor beeld laat zien hoe u een zoek opdracht voor een bepaalde Azure Sentinel-werk ruimte maakt of bijwerkt.  Voor een livestream-query vervangt u *' Category ': ' jagers query's '* door *' Category ': ' Livestream query's '* in de **hoofd tekst** van de aanvraag: 

#### <a name="request-header"></a>Aanvraagheader

```http
PUT https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

#### <a name="request-body"></a>Aanvraagbody

```json
{
"properties": {
    “Category”: “Hunting Queries”,
    "DisplayName": "HuntingRule02",
    "Query": "SecurityEvent | where EventID == \"4688\" | where CommandLine contains \"-noni -ep bypass $\"",
    “Tags”: [
        { 
        “Name”: “Description”,
        “Value”: “Test Hunting Query”
        },
        { 
        “Name”: “Tactics”,
        “Value”: “Execution, Discovery”
        }
        ]        
    }
}
```

### <a name="example-2"></a>Voorbeeld 2

In dit voor beeld ziet u hoe u een query voor een jacht-of Livestream verwijdert voor een bepaalde Azure Sentinel-werk ruimte:

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId} _
       /resourcegroups/{resourceGroupName} _
       /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
       /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

### <a name="example-3"></a>Voorbeeld 3

Dit voor beeld laat zien hoe u een query voor een jacht-of Livestream ophaalt voor een bepaalde werk ruimte:

```http
GET https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u de opdrachten voor jacht en Livestream in azure Sentinel beheert met behulp van de Log Analytics-API. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- [Proactief zoeken naar bedreigingen](hunting.md)
- [Notitie blokken gebruiken voor het uitvoeren van geautomatiseerde jacht-campagnes](notebooks.md)
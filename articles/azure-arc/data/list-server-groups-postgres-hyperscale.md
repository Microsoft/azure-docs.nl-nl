---
title: Een lijst weer geven van de Azure-PostgreSQL grootschalige-Server groepen die zijn gemaakt in een Azure-Arc-gegevens controller
description: Een lijst weer geven van de Azure-PostgreSQL grootschalige-Server groepen die zijn gemaakt in een Azure-Arc-gegevens controller
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7577ca4b8a1d7db7ea99aadfef4fd2a445b66425
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90936800"
---
# <a name="list-the-azure-arc-enabled-postgresql-hyperscale-server-groups-created-in-an-azure-arc-data-controller"></a>Een lijst weer geven van de Azure-PostgreSQL grootschalige-Server groepen die zijn gemaakt in een Azure-Arc-gegevens controller

In dit artikel wordt uitgelegd hoe u de lijst met Server groepen die zijn gemaakt in uw Arc-gegevens controller kunt ophalen.

Als u deze lijst wilt ophalen, gebruikt u een van de volgende methoden zodra u verbinding hebt met de Arc-gegevens controller:

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-cli-with-azdata"></a>Van CLI met azdata
De algemene indeling van de opdracht is:
```console
azdata arc postgres server list
```

Er wordt een uitvoer geretourneerd zoals:
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    2
postgres02  Ready    2
```
Voer het volgende uit voor meer informatie over de beschik bare para meters voor deze opdracht:
```console
azdata arc postgres server list --help
```

## <a name="from-cli-with-kubectl"></a>Van CLI met kubectl
Voer een van de volgende opdrachten uit.

**Als u de Server groepen wilt weer geven onafhankelijk van de versie van post gres, voert u de volgende handelingen uit:**
```console
kubectl get postgresqls
```
Er wordt een uitvoer geretourneerd zoals:
```console
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      51s
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d
```

**Als u de Server groepen van een specifieke versie van post gres wilt weer geven, voert u de volgende handelingen uit:**
```console
kubectl get postgresql-12
```

Als u een lijst wilt weer geven van de Server groepen met versie 11 van post gres, vervangt u _postgresql-12_ door _postgresql-11_.

## <a name="next-steps"></a>Volgende stappen:

* [Lees het artikel over het ophalen van de verbindings eindpunten en de verbindings reeksen te vormen om verbinding te maken met uw server groep](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)
* [Lees het artikel over het weer geven van de configuratie van een Azure Arc enabled PostgreSQL grootschalige-Server groep](show-configuration-postgresql-hyperscale-server-group.md)

---
title: De PostgreSQL-poort wijzigen
description: Wijzig de poort waarop de Azure-PostgreSQL grootschalige-Server groep luistert.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/02/2020
ms.topic: how-to
ms.openlocfilehash: 45424408c790e4921be48464818c55fe74313fd3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93328716"
---
# <a name="change-the-port-on-which-the-server-group-is-listening"></a>De poort wijzigen waarop de Server groep luistert 

Het wijzigen van de poort is een standaard bewerking voor het bewerken van de Server groep. Voer de volgende opdracht uit om de poort te wijzigen:
```console
 azdata arc postgres server edit -n <server group name> --port <desired port number>
```

Laten we bijvoorbeeld aannemen dat de naam van uw server groep _postgres01_ is en dat deze luistert op poort _866_. Voer de volgende opdracht uit:
```console
 azdata arc postgres server edit -n postgres01 --port 866
```

## <a name="verify-that-the-port-was-changed"></a>Controleren of de poort is gewijzigd

Als u wilt controleren of de poort is gewijzigd, voert u de volgende opdracht uit om de configuratie van de Server groep weer te geven:
```console
azdata arc postgres server show -n <server group name>
```

In de uitvoer van die opdracht, kijkt u naar het poort nummer dat wordt weer gegeven voor het item ' poort ' in de sectie ' service ' van de specificaties van de Server groep.
U kunt ook in het gedeelte item externalEndpoint van de sectie status van de specificaties van uw server groep controleren of het IP-adres wordt gevolgd door het poort nummer dat u hebt geconfigureerd.

Als een afbeelding, als we het bovenstaande voor beeld voortzetten, voert u de volgende opdracht uit:
```console
azdata arc postgres server show -n postgres01
```

en hier ziet u poort 866 waarnaar wordt verwezen:

```console
"service": {
      "port": 866,
      "type": "LoadBalancer"
    },
```
Kijk ook eens hier

```console
"status": {
    "externalEndpoint": "12.678.345.09:866",
    "logSearchDashboard": "https://12.345.678.90:30777/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))",
    "metricsDashboard": "https://12.345.678.90:30777/grafana/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01",
    "readyPods": "3/3",
    "state": "Ready"
  }
```
## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [hoe u verbinding maakt met uw server groep](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md).
- Meer informatie over het configureren van andere aspecten van uw server groep vindt u in de sectie How-to\Manage\Configure & scale van de documentatie.

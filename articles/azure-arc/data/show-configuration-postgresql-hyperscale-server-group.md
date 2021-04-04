---
title: De configuratie van een PostgreSQL grootschalige-Server groep met Arc-functionaliteit weer geven
titleSuffix: Azure Arc enabled data services
description: De configuratie van een PostgreSQL grootschalige-Server groep met Arc-functionaliteit weer geven
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a268cd6b2fa3da6846554e3d1b170298abec7f18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93279398"
---
# <a name="show-the-configuration-of-an-arc-enabled-postgresql-hyperscale-server-group"></a>De configuratie van een PostgreSQL grootschalige-Server groep met Arc-functionaliteit weer geven

In dit artikel wordt uitgelegd hoe u de configuratie van uw server groep (en) kunt weer geven. Dit doet u door te anticiperen op enkele vragen die u naar zichzelf vraagt en deze antwoorden. Het kan voor komen dat er enkele geldige antwoorden zijn. In dit artikel worden de meest voorkomende of nuttige voor waarden in de meeste gevallen verduidelijkt. Deze vragen worden gegroepeerd op basis van het thema:

- vanuit een Kubernetes-weergave punt
- vanuit een Azure Arc enabled Data Services-oogpunt van weer gave

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-a-kubernetes-point-of-view"></a>Vanuit een Kubernetes-weergave punt

### <a name="how-many-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale"></a>Hoeveel peulen worden er gebruikt door Azure Arc enabled PostgreSQL grootschalige?

Geef een lijst van de Kubernetes-resources van het type post gres. Voer de opdracht uit:

```console
kubectl get postgresqls [-n <namespace name>]
```

De uitvoer van deze opdracht geeft de lijst weer van gemaakte Server groepen. Voor elke wordt het aantal peulen aangegeven. Bijvoorbeeld:

```output
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      6h34m
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
```

Dit voor beeld laat zien dat twee Server groepen worden gemaakt en elk worden uitgevoerd op 3 of meer werk nemers (1 coördinator + 2). Dit betekent dat de Server groepen die zijn gemaakt in deze Azure Arc data controller 6 peul gebruiken.

### <a name="what-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Welke peulen worden gebruikt door Azure Arc ingeschakelde PostgreSQL grootschalige-Server groepen?

Voer het volgende uit:

```console
kubectl get pods [-n <namespace name>]
```

Hiermee wordt de lijst met peulen geretourneerd. U ziet dat het Peul wordt gebruikt door uw server groepen op basis van de namen die u hebt opgegeven voor deze server groepen. Bijvoorbeeld:

```console 
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-vdltm   1/1     Running   0          6d8h
control-h6kc9        2/2     Running   0          6d8h
controldb-0          2/2     Running   0          6d8h
controlwd-96sbn      1/1     Running   0          6d8h
logsdb-0             1/1     Running   0          6d8h
logsui-7wkg2         1/1     Running   0          6d8h
metricsdb-0          1/1     Running   0          6d8h
metricsdc-28ffl      1/1     Running   0          6d8h
metricsui-k7qsh      1/1     Running   0          6d8h
mgmtproxy-gd84z      2/2     Running   0          6d8h
postgres01-0         3/3     Running   0          6h50m
postgres01-1         3/3     Running   0          6h50m
postgres01-2         3/3     Running   0          6h50m
postgres02-0         3/3     Running   0          22h
postgres02-1         3/3     Running   0          22h
postgres02-2         3/3     Running   0          22h
```

In dit voor beeld is de zes peulen die als host fungeren voor de twee Server groepen die worden gemaakt:
- `postgres01-0`
- `postgres01-1`
- `postgres01-2`
- `postgres02-0`
- `postgres02-1`
- `postgres02-2`  

### <a name="what-server-group-pod-is-used-for-what-role-the-server-group"></a>Welke server groep pod wordt gebruikt voor de rol van de Server groep?

Elk pod-naam achtervoegsel dat `-0` een coördinator knooppunt vertegenwoordigt. De naam van een knoop punt met een waarde van `-x` 1 of hoger is het worker-knoop punt. In het bovenstaande voorbeeld:
- De coördinatoren zijn: `postgres01-0` , `postgres02-0`
- De werk nemers zijn: `postgres01-2` , `postgres01-2` , `postgres02-1` , `postgres02-2`

### <a name="what-is-the-status-of-the-pods"></a>Wat is de status van het Peul?

Voer uit `kubectl get pods` en Bekijk de kolom `STATUS`

### <a name="what-persistent-volume-claims-pvcs-are-being-used"></a>Welke Pvc's (permanente volume claims) worden gebruikt? 

Als u wilt weten welke Pvc's er worden gebruikt, en wat wordt gebruikt voor gegevens, logboeken en back-ups, voert u de volgende handelingen uit: 

```console
kubectl get pvc [-n <namespace name>]
```

Standaard geeft het voor voegsel van de naam van een PVC het gebruik aan:

- `backups-`...: is een PVC dat wordt gebruikt voor back-ups
- `data-`...: een PVC dat wordt gebruikt voor gegevens bestanden
- `logs-`...: is een PVC dat wordt gebruikt voor transactie logboeken/WAL-bestanden

Bijvoorbeeld:

```output
NAME                                            STATUS   VOLUME              CAPACITY   ACCESS MODES   STORAGECLASS    AGE
backups-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    local-pv-485e37db   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    local-pv-9d3d4a15   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    local-pv-7b8dd819   1938Gi     RWO            local-storage   6d6h
...
data-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-3c1a8cc5   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-8303ab19   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-55572fe6   1938Gi     RWO            local-storage   6d6h
...
logs-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-5e852b76   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-55d309a7   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-5ccd02e6   1938Gi     RWO            local-storage   6d6h
...
```


## <a name="from-an-azure-arc-enabled-data-services-point-of-view"></a>Vanuit een weer gave met Azure Arc enabled Data Services:

* Hoeveel Server groepen worden er gemaakt in een Arc-gegevens controller?
* Wat zijn de namen?
* Hoeveel worker-knoop punten worden er gebruikt?
* Welke versie van post gres worden uitgevoerd?

Gebruik een van de volgende opdrachten.
- **Met kubectl:**

   ```console
   kubectl get postgresqls [-n <namespace name>]
   ``` 

   Bijvoorbeeld, wordt de onderstaande uitvoer gegenereerd waarbij elke regel een is `servergroup` . De structuur van de naam in de onderstaande weer gave wordt gevormd als:

   `<Name-Of-Custom-Resource-Definition>`/`<Server-Group-Name>`

   ```output
   NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
   postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      7h15m
   postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
   ```

   In de bovenstaande uitvoer ziet u twee Server groepen van post gres versie 12. Als de versie post gres 11 is, wordt in plaats daarvan de naam van de CRD postgresql-11.arcdata.microsoft.com.

   Elk van deze wordt uitgevoerd op 3 knoop punten/peul: 1 coördinator en 2 werk nemers.

- **Met azdata:**

Voer de volgende opdracht uit. In de uitvoer ziet u vergelijk bare informatie over kubectl.

   ```console
   azdata arc postgres server list

   `output
   Name        State    Workers
   ----------  -------  ---------
   postgres01  Ready    2
   postgres02  Ready    2
   ```


### <a name="how-much-memory-and-vcores-are-being-used-and-what-extensions-were-created-for-a-group"></a>Hoeveel geheugen en vCores worden gebruikt en welke uitbrei dingen voor een groep zijn gemaakt?

Voer een van de volgende opdrachten uit

**Met Kubectl:**

Gebruik kubectl om een post gres-resources te beschrijven. Hiervoor hebt u het type (naam van de Kubernetes-bron (CRD) voor de bijbehorende post gres versie zoals hierboven weer gegeven) en de naam van de Server groep nodig.
De algemene indeling van deze opdracht is:

```console
kubectl describe <CRD name>/<server group name> [-n <namespace name>]
```

Bijvoorbeeld:

```console
kubectl describe postgresql-12/postgres02
```

Met deze opdrachten wordt de configuratie van de Server groep weer gegeven:

```output
Name:         postgres02
Namespace:    arc
Labels:       <none>
Annotations:  <none>
API Version:  arcdata.microsoft.com/v1alpha1
Kind:         postgresql-12
Metadata:
  Creation Timestamp:  2020-08-31T21:01:07Z
  Generation:          10
  Resource Version:    569516
  Self Link:           /apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02
  UID:                 8a9cd118-361b-4a2e-8a9d-5f9257bf6abb
Spec:
  Engine:
    Extensions:
      Name:  citus
      Name:  pg_stat_statements
  Scale:
    Workers:  2
  Scheduling:
    Default:
      Resources:
        Limits:
          Cpu:     4
          Memory:  1024Mi
        Requests:
          Cpu:     1
          Memory:  512Mi
  Service:
    Type:  NodePort
  Storage:
    Data:
      Class Name:  local-storage
      Size:        5Gi
    Logs:
      Class Name:  local-storage
      Size:        5Gi
Status:
  External Endpoint:  10.0.0.4:31066
  Ready Pods:         3/3
  State:              Ready
Events:               <none>
```

>[!NOTE]
>Vóór de release van oktober 2020 `Workers` was `Shards` het vorige voor beeld. Zie [release opmerkingen-Azure Arc enabled Data Services (preview)](release-notes.md) voor meer informatie.

Laten we een aantal specifieke aandachtspunten aanroepen in de beschrijving van de `servergroup` hierboven weer gegeven. Wat is er aan deze server groep door ons te vertellen?

- Het is van versie 12 van post gres: 
   > ```json
   > Kind:         `postgresql-12`
   > ```
- Het is gemaakt in de maand augustus 2020:
   > ```json
   > Creation Timestamp:  `2020-08-31T21:01:07Z`
   > ```
- Er zijn twee post gres-extensies gemaakt in deze server groep: `citus` en `pg_stat_statements`
   > ```json
   > Engine:
   >    Extensions:
   >      Name:  `citus`
   >      Name:  `pg_stat_statements`
   > ```
- Er worden twee worker-knoop punten gebruikt
   > ```json
   > Scale:
   >    Workers:  `2`
   > ```
- Het is gegarandeerd dat er 1 CPU/vCore en 512 MB RAM per knoop punt worden gebruikt. Er worden meer dan vier CPU-vCores en 1024 MB geheugen gebruikt:
   > ```json
   > Scheduling:
   >    Default: 
   >      Resources:
   >        Limits:
   >          Cpu:     4
   >          Memory:  1024Mi
   >        Requests:
   >          Cpu:     1
   >          Memory:  512Mi
   > ```
 - Het is beschikbaar voor query's en heeft geen probleem. Alle knoop punten zijn actief:
   > ```json
   > Status:
   >  ...
   >  Ready Pods:         3/3
   >  State:              Ready
   > ```

**Met azdata:**

De algemene indeling van de opdracht is:

```console
azdata arc postgres server show -n <server group name>
```

Bijvoorbeeld:

```console
azdata arc postgres server show -n postgres02
```

Retourneert de onderstaande uitvoer in een indeling en inhoud die veel lijkt op het item dat door kubectl wordt geretourneerd.

```console
{
  "apiVersion": "arcdata.microsoft.com/v1alpha1",
  "kind": "postgresql-12",
  "metadata": {
    "creationTimestamp": "2020-08-31T21:01:07Z",
    "generation": 10,
    "name": "postgres02",
    "namespace": "arc",
    "resourceVersion": "569516",
    "selfLink": "/apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02",
    "uid": "8a9cd118-361b-4a2e-8a9d-5f9257bf6abb"
  },
  "spec": {
    "engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_stat_statements"
        }
      ]
    },
    "scale": {
      "workers": 2
    },
    "scheduling": {
      "default": {
        "resources": {
          "limits": {
            "cpu": "4",
            "memory": "1024Mi"
          },
          "requests": {
            "cpu": "1",
            "memory": "512Mi"
          }
        }
      }
    },
    "service": {
      "type": "NodePort"
    },
    "storage": {
      "data": {
        "className": "local-storage",
        "size": "5Gi"
      },
      "logs": {
        "className": "local-storage",
        "size": "5Gi"
      }
    }
  },
  "status": {
    "externalEndpoint": "10.0.0.4:31066",
    "readyPods": "3/3",
    "state": "Ready"
  }
}
```

## <a name="next-steps"></a>Volgende stappen
- [Meer informatie over de concepten van Azure Arc enabled PostgreSQL grootschalige](concepts-distributed-postgres-hyperscale.md)
- [Meer informatie over uitschalen (werk knooppunten toevoegen) een server groep](scale-out-postgresql-hyperscale-server-group.md)
- [Meer informatie over het omhoog/omlaag schalen van een server groep (verg Roten of verkleinen van geheugen en/of vCores)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- [Meer informatie over opslag configuratie](storage-configuration.md)
- [Meer informatie over het bewaken van een Data Base-exemplaar](monitor-grafana-kibana.md)
- [PostgreSQL-extensies gebruiken in uw Azure-PostgreSQL grootschalige-Server groep](using-extensions-in-postgresql-hyperscale-server-group.md)
- [Beveiliging configureren voor uw PostgreSQL Hyperscale-servergroep met Azure Arc](configure-security-postgres-hyperscale.md)

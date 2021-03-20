---
title: Plaatsing van een PostgreSQL grootschalige-Server groep op de Kubernetes-cluster knooppunten
description: Hierin wordt uitgelegd hoe PostgreSQL-instanties die een PostgreSQL grootschalige-Server groep vormen, worden geplaatst op de Kubernetes-cluster knooppunten
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: b88b36ba8ec1d2d612adbbf19a6cf1e91fbb2cfd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100377751"
---
# <a name="azure-arc-enabled-postgresql-hyperscale-server-group-placement"></a>Plaatsing van Azure-PostgreSQL grootschalige-Server groep

In dit artikel nemen we een voor beeld om te laten zien hoe de PostgreSQL-exemplaren van de Azure-PostgreSQL grootschalige-Server groep worden geplaatst op de fysieke knoop punten van het Kubernetes-cluster waarop ze worden gehost. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configuration"></a>Configuratie

In dit voor beeld gebruiken we een Azure Kubernetes service-cluster (AKS) met vier fysieke knoop punten. 

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/1_cluster_portal.png" alt-text="AKS cluster met 4 knoop punten in Azure Portal":::

De fysieke knoop punten van het Kubernetes-cluster weer geven. Voer de opdracht uit:

```console
kubectl get nodes
```

`kubectl` retourneert vier fysieke knoop punten in het Kubernetes-cluster:

```output
NAME                                STATUS   ROLES   AGE   VERSION
aks-agentpool-42715708-vmss000000   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000001   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000002   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000003   Ready    agent   11h   v1.17.9
```

De architectuur kan worden weer gegeven als:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/2_logical_cluster.png" alt-text="Logische weer gave van vier knoop punten gegroepeerd in een Kubernetes-cluster":::

Het Kubernetes-cluster fungeert als host voor een Azure-Arc-gegevens controller en één grootschalige-Server groep voor Azure-Arc ingeschakelde PostgreSQL. Deze server groep is samengesteld uit drie PostgreSQL-instanties: één coördinator en twee werk nemers.

Geef het Peul weer met de volgende opdracht:

```console
kubectl get pods -n arc3
```
`kubectl` retourneert

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01c-0         3/3     Running   0          9h
postgres01w-0         3/3     Running   0          9h
postgres01w-1         3/3     Running   0          9h
```
Elk van deze objecten die een PostgreSQL-exemplaar hosten. Samen vormen de PostgreSQL-Server groep Azure-Arc ingeschakelde grootschalige:

```output
Pod name        Role in the server group
postgres01c-0 Coordinator
postgres01w-0   Worker
postgres01w-1   Worker
```

## <a name="placement"></a>Plaatsing
Laten we eens kijken hoe Kubernetes het Peul van de Server groep plaatst. Beschrijf elke pod en bepaal op welk fysiek knoop punt van het Kubernetes-cluster ze worden geplaatst. Voor de coördinator voert u bijvoorbeeld de volgende opdracht uit:

```console
kubectl describe pod postgres01c-0 -n arc3
```

`kubectl` retourneert

```output
Name:         postgres01c-0
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
Start Time:   Thu, 17 Sep 2020 00:40:33 -0700
…
```

Als we deze opdracht voor elk van de peulen uitvoeren, wordt de huidige plaatsing samenvatten als:

| Functie Server groep|Pod Server groep|Fysiek Kubernetes-knoop punt dat als host fungeert voor de pod |
|--|--|--|
| Werk|postgres01-1|AKS-agent pool-42715708-vmss000002 |
| Werk|postgres01-2|AKS-agent pool-42715708-vmss000003 |

En Let er ook op dat in de beschrijving van het Peul de namen van de containers die elke pod-host. Voor de tweede werk nemer voert u bijvoorbeeld de volgende opdracht uit:

```console
kubectl describe pod postgres01w-1 -n arc3
```

`kubectl` retourneert

```output
…
Node:         aks-agentpool-42715708-vmss000003/10.240.0.7
..
Containers:
  Fluentbit:
…
  Postgres:
…
  Telegraf:
…
```

Elke pod die deel uitmaakt van de PostgreSQL grootschalige-Server groep voor Azure-Arc, host de volgende drie containers:

|Containers|Beschrijving
|----|----|
|`Fluentbit` |Gegevens * logboek verzamelaar: https://fluentbit.io/
|`Postgres`|PostgreSQL-exemplaar onderdeel van de Azure-PosgreSQL grootschalige-Server groep ingeschakeld
|`Telegraf` |Collector van metrische gegevens: https://www.influxdata.com/time-series-platform/telegraf/

De architectuur ziet er als volgt uit:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/3_pod_placement.png" alt-text="3 elke plaats op afzonderlijke knoop punten":::

Dit betekent dat op dit moment elk PostgreSQL-exemplaar dat de Azure-PostgreSQL grootschalige-Server groep vormt, wordt gehost op een specifieke fysieke host in de Kubernetes-container. Deze configuratie biedt de meeste prestaties van de Azure-PostgreSQL grootschalige-Server groep, aangezien elke rol (coördinator en werk nemers) de bronnen van elk fysiek knoop punt gebruikt. Deze resources worden niet gedeeld tussen verschillende PostgreSQL-rollen.

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale"></a>Uitschalen Azure Arc enabled PostgreSQL grootschalige

Nu gaan we uitbreiden om een derde werk knooppunt toe te voegen aan de Server groep en te kijken wat er gebeurt. Er wordt een vierde PostgreSQL-instantie gemaakt die wordt gehost in een vierde pod.
Voer de volgende opdracht uit om uit te schalen:

```console
azdata arc postgres server edit --name postgres01 --workers 3
```

Dit resulteert in de volgende uitvoer:

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

Geef een lijst weer van de Server groepen die zijn geïmplementeerd in de Azure Arc-gegevens controller en controleer of de Server groep nu wordt uitgevoerd met drie werk rollen. Voer de opdracht uit:

```console
azdata arc postgres server list
```

En houd er rekening mee dat het uitschalen van twee werk rollen naar drie werk nemers is uitgevoerd:

```output
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

Zoals we eerder hebben gedaan, zien we dat de Server groep nu een totaal van vier peulen gebruikt:

```console
kubectl get pods -n arc3
```

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01c-0         3/3     Running   0          11h
postgres01w-0         3/3     Running   0          11h
postgres01w-1         3/3     Running   0          11h
postgres01w-2         3/3     Running   0          5m2s
```

En beschrijf de nieuwe pod om te identificeren op welke van de fysieke knoop punten van het Kubernetes-cluster het wordt gehost.
Voer de opdracht uit:

```console
kubectl describe pod postgres01w-2 -n arc3
```

De naam van het hosting knooppunt identificeren:

```output
Name:         postgres01w-2
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
```

De plaatsing van de PostgreSQL-exemplaren op de fysieke knoop punten van het cluster is nu:

|Functie Server groep|Pod Server groep|Fysiek Kubernetes-knoop punt dat als host fungeert voor de pod
|-----|-----|-----
|Action|postgres01-0|AKS-agent pool-42715708-vmss000000
|Werk|postgres01-1|AKS-agent pool-42715708-vmss000002
|Werk|postgres01-2|AKS-agent pool-42715708-vmss000003
|Werk|postgres01-3|AKS-agent pool-42715708-vmss000000

U ziet dat de pod van de nieuwe werk nemer (postgres01w-2) is geplaatst op hetzelfde knoop punt als de coördinator. 

De architectuur ziet er als volgt uit:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/4_pod_placement_.png" alt-text="Vierde pod op hetzelfde knoop punt als coördinator":::

Waarom is de nieuwe werk nemer-pod niet geplaatst op het resterende fysieke knoop punt van het Kubernetes-cluster AKS-agent pool-42715708-vmss000003?

De reden hiervoor is dat het laatste fysieke knoop punt van het Kubernetes-cluster in werkelijkheid een aantal extra onderdelen host die vereist zijn voor het uitvoeren van Azure Arc ingeschakelde gegevens Services. Kubernetes heeft beoordeeld dat de beste kandidaat – op het moment van de planning – als u de extra werk nemer wilt hosten als AKS-agent pool-42715708-vmss000000 fysiek knoop punt. 

Dezelfde opdrachten gebruiken als hierboven. We zien wat elk fysiek knoop punt host:

|Andere namen van peulen\* |Gebruik|Fysiek Kubernetes-knoop punt dat als host fungeert voor het Peul
|----|----|----
|Boots Trapper-jh48b|Een service die inkomende aanvragen verwerkt voor het maken, bewerken en verwijderen van aangepaste resources, zoals SQL Managed instances, PostgreSQL grootschalige server groups en gegevens controllers|AKS-agent pool-42715708-vmss000003
|Control-gwmbs||AKS-agent pool-42715708-vmss000002
|controldb-0|De controller gegevens opslag die wordt gebruikt voor het opslaan van de configuratie en status voor de gegevens controller.|AKS-agent pool-42715708-vmss000001
|controlwd-zzjp7|De controller ' Watch honden ' die een oog houdt over de beschik baarheid van de gegevens controller.|AKS-agent pool-42715708-vmss000000
|logsdb-0|Een elastisch Zoek exemplaar dat wordt gebruikt voor het opslaan van alle logboeken die in alle gegevens services van de boog worden verzameld. Elasticsearch, ontvangt gegevens van `Fluentbit` de container van elke pod|AKS-agent pool-42715708-vmss000003
|logsui-5fzv5|Een Kibana-exemplaar dat zich boven op de Elastic Search-Data Base bevindt om een log Analytics-gebruikers interface te presen teren.|AKS-agent pool-42715708-vmss000003
|metricsdb-0|Een InfluxDB-exemplaar dat wordt gebruikt voor het opslaan van alle metrische gegevens die worden verzameld over alle Arc Data Services-peulen. InfluxDB, ontvangt gegevens van de `Telegraf` container van elke pod|AKS-agent pool-42715708-vmss000000
|metricsdc-47d47|Een daemon-set die is geïmplementeerd op alle Kubernetes-knoop punten in het cluster voor het verzamelen van metrische gegevens op knooppunt niveau over de knoop punten.|AKS-agent pool-42715708-vmss000002
|metricsdc-864kj|Een daemon-set die is geïmplementeerd op alle Kubernetes-knoop punten in het cluster voor het verzamelen van metrische gegevens op knooppunt niveau over de knoop punten.|AKS-agent pool-42715708-vmss000001
|metricsdc-l8jkf|Een daemon-set die is geïmplementeerd op alle Kubernetes-knoop punten in het cluster voor het verzamelen van metrische gegevens op knooppunt niveau over de knoop punten.|AKS-agent pool-42715708-vmss000003
|metricsdc-nxm4l|Een daemon-set die is geïmplementeerd op alle Kubernetes-knoop punten in het cluster voor het verzamelen van metrische gegevens op knooppunt niveau over de knoop punten.|AKS-agent pool-42715708-vmss000000
|metricsui-4fb7l|Een Grafana-exemplaar dat zich boven op de InfluxDB-Data Base bevindt, waarmee een bewakings dashboard kan worden weer gegeven.|AKS-agent pool-42715708-vmss000003
|mgmtproxy-4qppp|Een Web Application proxy-laag die zich vóór de Grafana-en Kibana-instanties bevindt.|AKS-agent pool-42715708-vmss000002

> \* Het achtervoegsel voor pod namen is afhankelijk van andere implementaties. We bieden hier alleen de peulen die worden gehost in de Kubernetes-naam ruimte van de Azure Arc data controller.

De architectuur ziet er als volgt uit:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/5_full_list_of_pods.png" alt-text="Alle peulen in de naam ruimte op verschillende knoop punten":::

Zoals hierboven beschreven, delen de coördinator knooppunten (pod 1) van de Azure-post gres grootschalige-Server groep dezelfde fysieke resources als het derde worker-knoop punt (pod 4) van de Server groep. Dit is acceptabel omdat het knoop punt van de coördinator doorgaans weinig bronnen gebruikt in vergelijking met wat een worker-knoop punt kan gebruiken. Daarom moet u zorgvuldig de volgende opties kiezen:
- de grootte van het Kubernetes-cluster en de kenmerken van elk van de fysieke knoop punten (geheugen, vCore)
- het aantal fysieke knoop punten in het Kubernetes-cluster
- de toepassingen of workloads die u host op het Kubernetes-cluster.

De implicatie van het hosten van te veel werk belastingen op het Kubernetes-cluster kan worden beperkt voor de Azure-PostgreSQL grootschalige-Server groep. Als dat het geval is, profiteert u niet zoveel van de mogelijkheid om horizon taal te schalen. De prestaties die u uit het systeem haalt, zijn niet alleen te weten over de plaatsing of de fysieke kenmerken van de fysieke knoop punten of het opslag systeem. De prestaties die u krijgt, zijn ook informatie over de configuratie van elk van de resources die in het Kubernetes-cluster worden uitgevoerd (inclusief Azure Arc enabled PostgreSQL grootschalige), bijvoorbeeld de aanvragen en limieten die u hebt ingesteld voor geheugen en vCore. De hoeveelheid werk belasting die u kunt hosten op een bepaald Kubernetes-cluster is relatief ten opzichte van de kenmerken van het Kubernetes-cluster, de aard van de werk belastingen, het aantal gebruikers, hoe de bewerkingen van het Kubernetes-cluster worden uitgevoerd...

## <a name="scale-out-aks"></a>Uitschalen AKS

Laten we zien dat het Horizon taal schalen van zowel het AKS-cluster als de PostgreSQL grootschalige-server met Azure Arc is een manier om optimaal te profiteren van de hoge prestaties van Azure Arc ingeschakelde PostgreSQL grootschalige.
We gaan een vijfde knoop punt toevoegen aan het AKS-cluster:

:::row:::
    :::column:::
        Voor
    :::column-end:::
    :::column:::
        Na
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/6_layout_before.png" alt-text="Azure Portal indeling vóór":::
    :::column-end:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/7_layout_after.png" alt-text="Azure Portal indeling na":::
    :::column-end:::
:::row-end:::

De architectuur ziet er als volgt uit:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/8_logical_layout_after.png" alt-text="Logische indeling op Kubernetes-cluster na update":::

Laten we eens kijken naar welk doel van de Arc data controller-naam ruimte wordt gehost op het nieuwe fysieke AKS-knoop punt door de opdracht uit te voeren:

```console
kubectl describe node aks-agentpool-42715708-vmss000004
```

En laten we de weer gave van de architectuur van ons systeem bijwerken:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/9_updated_list_of_pods.png" alt-text="Alle peulen in het logische diagram van het cluster":::

We kunnen zien dat het nieuwe fysieke knoop punt van het Kubernetes-cluster alleen de metrische pod-gegevens host die nodig zijn voor Azure Arc Data Services. Houd er rekening mee dat in dit voor beeld alleen aandacht wordt besteed aan de naam ruimte van de Arc-gegevens controller, maar niet voor het andere peul.

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale-again"></a>Azure Arc enabled PostgreSQL grootschalige opnieuw uitschalen

Er wordt nog geen werk belasting gehost op het vijfde fysieke knoop punt. Terwijl we de Azure-PostgreSQL-grootschalige uitschalen, wordt de plaatsing van de nieuwe PostgreSQL pod door Kubernetes geoptimaliseerd en mag deze niet worden termijnen op fysieke knoop punten die al meer werk belastingen hosten. Voer de volgende opdracht uit om de PostgreSQL-grootschalige van Azure-arctangens te schalen van 3 naar 4 werk nemers. Aan het einde van de bewerking wordt de Server groep gevormd en gedistribueerd over vijf PostgreSQL-instanties, een coördinator en vier werk nemers.

```console
azdata arc postgres server edit --name postgres01 --workers 4
```

Dit resulteert in de volgende uitvoer:

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

Geef een lijst weer van de Server groepen die in de gegevens controller zijn geïmplementeerd en controleer of de Server groep nu wordt uitgevoerd met vier werk rollen:

```console
azdata arc postgres server list
```

En houd er rekening mee dat deze uit drie tot vier werk rollen is geschaald. 

```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

Zoals we eerder hebben gedaan, wordt in de Server groep nu vier peulen gebruikt:

```output
kubectl get pods -n arc3

NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01c-0         3/3     Running   0          13h
postgres01w-0         3/3     Running   0          13h
postgres01w-1         3/3     Running   0          13h
postgres01w-2         3/3     Running   0          179m
postgres01w-3         3/3     Running   0          3m13s
```

De vorm van de Server groep is nu:

|Functie Server groep|Pod Server groep
|----|-----
|Action|postgres01c-0
|Werk|postgres01w-0
|Werk|postgres01w-1
|Werk|postgres01w-2
|Werk|postgres01w-3

We beschrijven de postgres01w-3-pod om te identificeren in welk fysiek knoop punt deze wordt gehost:

```console
kubectl describe pod postgres01w-3 -n arc3
```

En kijk op welke peul het draait:

|Functie Server groep|Pod Server groep| Pod
|----|-----|------
|Action|postgres01c-0|AKS-agent pool-42715708-vmss000000
|Werk|postgres01w-0|AKS-agent pool-42715708-vmss000002
|Werk|postgres01w-1|AKS-agent pool-42715708-vmss000003
|Werk|postgres01w-2|AKS-agent pool-42715708-vmss000000
|Werk|postgres01w-3|AKS-agent pool-42715708-vmss000004

En de architectuur ziet er als volgt uit:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/10_kubernetes_schedules_newest_pod.png" alt-text="Kubernetes plant de nieuwste pod in het knoop punt met het laagste gebruik":::

Kubernetes heeft de nieuwe PostgreSQL-pod gepland in het minst geladen fysieke knoop punt van het Kubernetes-cluster.

## <a name="summary"></a>Samenvatting

Om het beste te profiteren van de schaal baarheid en de prestaties van het Horizon taal schalen van Azure Arc ingeschakelde Server groep, moet u bron conflicten in het Kubernetes-cluster vermijden:
- tussen de Azure Arc enabled PostgreSQL grootschalige-Server groep en andere workloads die worden gehost op hetzelfde Kubernetes-cluster
- tussen alle PostgreSQL-instanties die de PostgreSQL grootschalige-Server groep voor Azure-Arc vormen

U kunt dit op verschillende manieren doen:
- Kubernetes en Azure Arc ingeschakeld post gres grootschalige: overweeg om horizon taal het Kubernetes-cluster op dezelfde manier te schalen als de postgresql-server groep voor Azure-Arc ingeschakeld. Voeg een fysiek knoop punt toe aan het cluster voor elke werk nemer die u aan de Server groep toevoegt.
- Azure Arc enabled post gres grootschalige uitschalen zonder Kubernetes te schalen: door de juiste resource beperkingen (aanvraag en limieten op geheugen en vCore) in te stellen voor de werk belastingen die worden gehost in Kubernetes (Azure Arc enabled PostgreSQL grootschalige inbegrepen), schakelt u de werk belasting op Kubernetes in en vermindert u het risico van bron conflicten. U moet ervoor zorgen dat de fysieke kenmerken van de fysieke knoop punten van het Kubernetes-cluster kunnen voldoen aan de beperkingen van de resources die u definieert. U moet er ook voor zorgen dat de werk belasting in de loop van de tijd blijft, of dat er meer werk belastingen worden toegevoegd aan het Kubernetes-cluster.
- Gebruik de Kubernetes-mechanismen (pod selector, affiniteit, anti-affiniteit) om de plaatsing van het peul te beïnvloeden.

## <a name="next-steps"></a>Volgende stappen

[Uw Azure-PostgreSQL grootschalige-Server groep uitschalen door meer worker-knoop punten toe te voegen](scale-out-postgresql-hyperscale-server-group.md)

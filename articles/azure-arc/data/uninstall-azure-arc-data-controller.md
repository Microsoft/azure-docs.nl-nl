---
title: Azure Arc-gegevens controller verwijderen
description: Azure Arc-gegevens controller verwijderen
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a040200c5746defcaee84a951521d5919c0c4d28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91660673"
---
# <a name="delete-azure-arc-data-controller"></a>Azure Arc-gegevens controller verwijderen

In het volgende artikel wordt beschreven hoe u een Azure Arc-gegevens controller verwijdert.

Voordat u doorgaat, moet u ervoor zorgen dat alle gegevens services die zijn gemaakt op de gegevens controller, als volgt worden verwijderd:

## <a name="log-in-to-the-data-controller"></a>Meld u aan bij de gegevens controller

Meld u aan bij de gegevens controller die u wilt verwijderen:

```
azdata login
```

## <a name="list--delete-existing-data-services"></a>Bestaande gegevens services weer geven & verwijderen

Voer de volgende opdracht uit om te controleren of er SQL Managed instances zijn gemaakt:

```
azdata arc sql mi list
```

Voer voor elk door SQL beheerd exemplaar uit de bovenstaande lijst de opdracht verwijderen als volgt uit:

```
azdata arc sql mi delete -n <name>
# for example: azdata arc sql mi delete -n sqlinstance1
```

Als u wilt controleren op PostgreSQL grootschalige-instanties, voert u de volgende handelingen uit:

```
azdata arc postgres server list
```

En voer voor elk exemplaar van PostgreSQL grootschalige de opdracht verwijderen als volgt uit:
```
azdata arc postgres server delete -n <name>
# for example: azdata arc postgres server delete -n pg1
```

## <a name="delete-controller"></a>Controller verwijderen

Nadat alle door SQL beheerde instanties en PostgreSQL grootschalige-instanties zijn verwijderd, kan de gegevens controller als volgt worden verwijderd:

```
azdata arc dc delete -n <name> -ns <namespace>
# for example: azdata arc dc delete -ns arc -n arcdc
```

### <a name="remove-sccs-red-hat-openshift-only"></a>SCCs verwijderen (alleen Red Hat open Shift)

```console
oc adm policy remove-scc-from-user privileged -z default -n arc
oc adm policy remove-scc-from-user anyuid     -z default -n arc
```

### <a name="delete-cluster-level-objects"></a>Objecten op cluster niveau verwijderen

Als u ook de objecten op cluster niveau wilt verwijderen, zoals de CRDs, `clusterroles` en u `clusterrolebindings` de volgende opdrachten uit te voeren, moet u naast de naam ruimte objecten in bereik

```console
# Cleanup azure arc data service artifacts
#Delete CRDs
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete crd postgresql-11s.arcdata.microsoft.com 
kubectl delete crd postgresql-12s.arcdata.microsoft.com
#Delete clusterrole
kubectl delete clusterrole <namespace>:cr-arc-metricsdc-reader
#For example: kubectl delete clusterrole arc:cr-arc-metricsdc-reader
#Delete rolebinding
kubectl delete clusterrolebinding <namespace>:crb-arc-metricsdc-reader
#For example: kubectl delete clusterrolebinding arc:crb-arc-metricsdc-reader
```

### <a name="optionally-delete-the-azure-arc-data-controller-namespace"></a>U kunt ook de Azure Arc data controller-naam ruimte verwijderen


```console
kubectl delete ns <nameSpecifiedDuringCreation>
# for example: kubectl delete ns arc
```

## <a name="next-steps"></a>Volgende stappen

[Wat zijn gegevensservices met Azure Arc?](overview.md)

---
title: Door Azure Arc ingeschakeld SQL Managed instance verwijderen
description: Door Azure Arc ingeschakeld SQL Managed instance verwijderen
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e531349e8f404380d9f0601caa3b66557c297062
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90936830"
---
# <a name="delete-azure-arc-enabled-sql-managed-instance"></a>Door Azure Arc ingeschakeld SQL Managed instance verwijderen
In dit artikel wordt beschreven hoe u een door Azure Arc ingeschakeld SQL Managed Instance kunt verwijderen.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="view-existing-azure-arc-enabled-sql-managed-instances"></a>Bestaande Azure Arc enabled SQL Managed instances weer geven
Als u SQL Managed instances wilt weer geven, voert u de volgende opdracht uit:

```console
azdata arc sql mi list
```

De uitvoer ziet er ongeveer als volgt uit:

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
demo-mi 1/1         10.240.0.4:32023  Ready
```

## <a name="delete-a-azure-arc-enabled-sql-managed-instance"></a>Een door Azure Arc ingeschakeld SQL Managed instance verwijderen
Als u een SQL Managed instance wilt verwijderen, voert u de volgende opdracht uit:

```console
azdata arc sql mi delete -n <NAME_OF_INSTANCE>
```

De uitvoer ziet er ongeveer als volgt uit:

```console
# azdata arc sql mi delete -n demo-mi
Deleted demo-mi from namespace arc
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>De Kubernetes permanente volume claims (Pvc's) opnieuw claimen

Als u een SQL Managed instance verwijdert, worden de bijbehorende [pvc's](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)niet verwijderd. Dit is standaard. De bedoeling is de gebruiker te helpen bij het openen van de databasebestanden voor het geval het verwijderen van het exemplaar onbedoeld is. Het verwijderen van PVC's is niet verplicht. Het wordt echter wel aanbevolen. Als u deze Pvc's niet vrijmaakt, wordt er uiteindelijk een fout opgetreden terwijl uw Kubernetes-cluster geen schijf ruimte meer heeft. Voer de volgende stappen uit om de PVC's vrij te maken:

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. de Pvc's weer geven voor de Server groep die u hebt verwijderd
Voer de volgende opdracht uit om de Pvc's weer te geven:
```console
kubectl get pvc
```

In het onderstaande voor beeld ziet u de Pvc's voor de SQL Managed instances die u hebt verwijderd.
```console
# kubectl get pvc -n arc

NAME                    STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
data-demo-mi-0        Bound     pvc-1030df34-4b0d-4148-8986-4e4c20660cc4   5Gi        RWO            managed-premium   13h
logs-demo-mi-0        Bound     pvc-11836e5e-63e5-4620-a6ba-d74f7a916db4   5Gi        RWO            managed-premium   13h
```

### <a name="2-delete-each-of-the-pvcs"></a>2. alle Pvc's verwijderen
Verwijder de gegevens en logboek-Pvc's voor elk van de door u verwijderde SQL Managed instances.
De algemene indeling van deze opdracht is: 
```console
kubectl delete pvc <name of pvc>
```

Bijvoorbeeld:
```console
kubectl delete pvc data-demo-mi-0 -n arc
kubectl delete pvc logs-demo-mi-0 -n arc
```

Met elk van deze kubectl-opdrachten wordt bevestigd dat het verwijderen van het PVC is geslaagd. Bijvoorbeeld:
```console
persistentvolumeclaim "data-demo-mi-0" deleted
persistentvolumeclaim "logs-demo-mi-0" deleted
```
  

> [!NOTE]
> Zoals aangegeven kan het verwijderen van de Pvc's ertoe leiden dat uw Kubernetes-cluster uiteindelijk wordt opgehaald in een situatie waarin fouten optreden. Bij sommige van deze fouten kan het voor komen dat u zich niet kunt aanmelden bij uw Kubernetes-cluster met azdata, aangezien het peul kan worden verwijderd vanwege dit opslag probleem (normaal Kubernetes gedrag).
>
> Er kunnen bijvoorbeeld berichten in de logboeken worden weer gegeven die vergelijkbaar zijn met:  
> - Annotaties: microsoft.com/ignore-pod-health: True  
> - Status: mislukt  
> - Reden: verwijderd  
> - Bericht: het knoop punt heeft weinig resources: kortstondige opslag. Container controller gebruikt 16372Ki, die de aanvraag van 0 overschrijdt.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [functies en mogelijkheden van SQL Managed instance met Azure Arc](managed-instance-features.md)

[Begin door een gegevenscontroller aan te maken](create-data-controller.md)

Hebt u al een gegevenscontroller aangemaakt? [Een SQL Managed Instance met Azure Arc maken](create-sql-managed-instance.md)
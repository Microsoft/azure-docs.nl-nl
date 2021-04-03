---
title: Hoge Beschik baarheid voor Azure SQL Edge-containers-Azure SQL Edge
description: Meer informatie over maximale Beschik baarheid voor Azure SQL Edge-containers
keywords: SQL-rand, containers, hoge Beschik baarheid
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 56b9f06547f737bc05d573f98ce1dbac2ba48758
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90935221"
---
# <a name="high-availability-for-azure-sql-edge-containers"></a>Hoge Beschik baarheid voor Azure SQL Edge-containers

Uw Azure SQL Edge-instanties systeem eigen maken en beheren in Kubernetes. Implementeer Azure SQL Edge op docker-containers die worden beheerd door [Kubernetes](https://kubernetes.io/). In Kubernetes kan een container met een Azure SQL Edge-instantie automatisch worden hersteld voor het geval een cluster knooppunt mislukt. U kunt de SQL Edge-container installatie kopie configureren met een Kubernetes permanente volume claim (PVC). Kubernetes bewaakt het proces van Azure SQL Edge in de container. Als het proces, de Pod, de container of het knoop punt uitvalt, Kubernetes automatisch een andere instantie Boots trapt en opnieuw verbinding maken met de opslag.

## <a name="azure-sql-edge-containers-on-kubernetes"></a>Azure SQL Edge-containers op Kubernetes

Kubernetes 1,6 en hoger biedt ondersteuning voor [*opslag klassen*](https://kubernetes.io/docs/concepts/storage/storage-classes/), [*permanente volume claims*](https://kubernetes.io/docs/concepts/storage/storage-classes/#persistentvolumeclaims).

In deze configuratie speelt Kubernetes de rol van de container Orchestrator af. 

![Diagram van Azure SQL Edge in een Kubernetes-cluster](media/deploy-kubernetes/kubernetes-sql-edge.png)

In het voorgaande diagram is `azure-sql-edge` een container in een [pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/). Kubernetes organiseert de resources in het cluster. Een [replicaset](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) ervoor zorgt dat de pod automatisch wordt hersteld na een storing in een knooppunt. Toepassingen maken verbinding met de service. In dit geval vertegenwoordigt de service een load balancer die als host fungeert voor een IP-adres dat niet hetzelfde is na het mislukken van de `azure-sql-edge`.

De `azure-sql-edge`-container is mislukt in het volgende diagram. Als orchestrator garandeert Kubernetes het juiste aantal gezonde instanties in de replicaset en wordt een nieuwe container gestart op basis van de configuratie. De orchestrator start een nieuwe pod op hetzelfde knooppunt en `azure-sql-edge` maakt opnieuw verbinding met dezelfde permanente opslag. De service maakt verbinding met de opnieuw gemaakte `azure-sql-edge`.

![Azure SQL Edge in een Kubernetes-cluster nadat de pod is mislukt](media/deploy-kubernetes/kubernetes-sql-edge-after-pod-fail.png)

In het volgende diagram is het knooppunt dat als host fungeert voor de `azure-sql-edge`-container mislukt. De orchestrator start de nieuwe pod op een ander knooppunt en `azure-sql-edge` maakt opnieuw verbinding met dezelfde permanente opslag. De service maakt verbinding met de opnieuw gemaakte `azure-sql-edge`.

![Azure SQL Edge in een Kubernetes-cluster nadat het knooppunt is mislukt](media/deploy-kubernetes/kubernetes-sql-edge-after-node-fail.png).

Als u een container wilt maken in Kubernetes, raadpleegt u [een Azure SQL Edge-container implementeren in Kubernetes](deploy-Kubernetes.md)

## <a name="next-steps"></a>Volgende stappen

Als u Azure SQL Edge-containers wilt implementeren in azure Kubernetes service (AKS), raadpleegt u de volgende artikelen:
- [Een Azure SQL Edge-container implementeren in Kubernetes](deploy-Kubernetes.md)
- [Machine Learning en kunstmatige intelligentie met ONNX in SQL Edge](onnx-overview.md).
- [Een end-to-end IoT-oplossing bouwen met SQL Edge met behulp van IoT Edge](tutorial-deploy-azure-resources.md).
- [Gegevensstreaming in Azure SQL Edge](stream-data.md)
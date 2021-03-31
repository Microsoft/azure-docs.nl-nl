---
title: Verbinding maken met de Azure Kubernetes-service-Azure Database for PostgreSQL-één server
description: Meer informatie over het verbinden van Azure Kubernetes service (AKS) met Azure Database for PostgreSQL-één server
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.date: 07/14/2020
ms.topic: conceptual
ms.openlocfilehash: 9b7da2fcc1310f03f894e048089658f25be3a149
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91708846"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-postgresql---single-server"></a>Verbinding maken met de Azure Kubernetes-service en Azure Database for PostgreSQL-één server

Azure Kubernetes service (AKS) biedt een beheerd Kubernetes-cluster dat u kunt gebruiken in Azure. Hieronder vindt u enkele opties waarmee u rekening moet houden wanneer u AKS en Azure Database for PostgreSQL samen gebruikt om een toepassing te maken.


## <a name="accelerated-networking"></a>Versneld netwerken
Gebruik versneld netwerken met onderliggende virtuele machines in uw AKS-cluster. Wanneer versneld netwerken op een virtuele machine is ingeschakeld, is er sprake van een lagere latentie, verminderde jitter en minder CPU-gebruik op de VM. Meer informatie over de werking van versneld netwerken, de ondersteunde versies van besturings systemen en ondersteunde VM-instanties voor [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

Vanaf november 2018 ondersteunt AKS versneld netwerken op die ondersteunde VM-exemplaren. Versneld netwerken zijn standaard ingeschakeld voor nieuwe AKS-clusters die gebruikmaken van deze Vm's.

U kunt controleren of uw AKS-cluster versneld netwerken heeft:
1. Ga naar de Azure Portal en selecteer uw AKS-cluster.
2. Selecteer het tabblad Properties.
3. Kopieer de naam van de **infrastructuur resource groep**.
4. Gebruik de zoek balk van de portal om de resource groep voor de infra structuur te zoeken en te openen.
5. Selecteer een virtuele machine in die resource groep.
6. Ga naar het tabblad **netwerken** van de VM.
7. Controleer of **versneld netwerken** zijn ingeschakeld.

Of via de Azure CLI met behulp van de volgende twee opdrachten:
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
De uitvoer is de gegenereerde resource groep die AKS maakt die de netwerk interface bevat. Voer de naam ' nodeResourceGroup ' in en gebruik deze in de volgende opdracht. **EnableAcceleratedNetworking** heeft de waarde True of False:
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```


## <a name="connection-pooling"></a>Groepsgewijze verbinding
Een verbindings groep minimaliseert de kosten en tijd die zijn gekoppeld aan het maken en sluiten van nieuwe verbindingen met de data base. De pool is een verzameling verbindingen die opnieuw kunnen worden gebruikt. 

Er zijn meerdere groepsgewijze verbindingen die u kunt gebruiken met PostgreSQL. Een van deze is [PgBouncer](https://pgbouncer.github.io/). In de micro soft-Container Registry bieden we een licht gewicht container PgBouncer die in een zijspan wagen kan worden gebruikt om verbindingen van AKS te maken naar Azure Database for PostgreSQL. Ga naar de [docker hub-pagina](https://hub.docker.com/r/microsoft/azureossdb-tools-pgbouncer/) voor meer informatie over het openen en gebruiken van deze installatie kopie. 


## <a name="next-steps"></a>Volgende stappen
-  [Een Azure Kubernetes Service-cluster maken](../aks/kubernetes-walkthrough.md)

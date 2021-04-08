---
title: Verbinding maken met de Azure Kubernetes-service-Azure Database for MySQL
description: Meer informatie over het verbinden van Azure Kubernetes service met Azure Database for MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: 5c4a5f5d792a60ed3fef07797fdbdfa0c9cfb8fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94534327"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-mysql"></a>Verbinding maken met Azure Kubernetes service en Azure Database for MySQL

Azure Kubernetes service (AKS) biedt een beheerd Kubernetes-cluster dat u kunt gebruiken in Azure. Hieronder vindt u enkele opties waarmee u rekening moet houden wanneer u AKS en Azure Database for MySQL samen gebruikt om een toepassing te maken.


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


## <a name="next-steps"></a>Volgende stappen
- [Een Azure Kubernetes Service-cluster maken](../aks/kubernetes-walkthrough.md)
- Meer informatie over het [installeren van WordPress vanuit een helm-diagram met OSBA en Azure database for MySQL](../aks/index.yml)
---
title: Opslag plaatsen weer geven in Portal
description: Gebruik de Azure Portal om Azure Container Registry opslag plaatsen weer te geven, die fungeren als host voor docker-container installatie kopieën en andere ondersteunde artefacten.
ms.topic: article
ms.date: 01/05/2018
ms.openlocfilehash: 1da72706d2554610a685f71199ab14af5e30ce1a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74456292"
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Container register opslagplaatsen weer geven in de Azure Portal

Met Azure Container Registry kunt u docker-container installatie kopieën opslaan in opslag plaatsen. Door afbeeldingen in opslag plaatsen op te slaan, kunt u groepen installatie kopieën (of versies van installatie kopieën) in geïsoleerde omgevingen opslaan. U kunt deze opslag plaatsen opgeven wanneer u installatie kopieën naar uw REGI ster pusht en de inhoud ervan weergeeft in de Azure Portal.

## <a name="prerequisites"></a>Vereisten

* **Container register**: een container register maken in uw Azure-abonnement. Gebruik bijvoorbeeld de [Azure Portal](container-registry-get-started-portal.md) of de [Azure cli](container-registry-get-started-azure-cli.md).
* **Docker cli**: Installeer [docker][docker-install] op uw lokale machine, waarmee u de opdracht regel interface van docker kunt installeren.
* **Container installatie kopie**: push een installatie kopie naar het container register. Zie [een installatie kopie pushen en ophalen](container-registry-get-started-docker-cli.md)voor meer informatie over het pushen en ophalen van installatie kopieën.

## <a name="view-repositories-in-azure-portal"></a>Opslag plaatsen in Azure Portal weer geven

U kunt een lijst weer geven met de opslag plaatsen die als host fungeren voor uw installatie kopieën, evenals de afbeeldings Tags in de Azure Portal.

Als u de stappen in [push en pull a-installatie kopie](container-registry-get-started-docker-cli.md) hebt uitgevoerd (en de installatie kopie niet meer hebt verwijderd), moet u een nginx-installatie kopie hebben in het container register. De instructies in dat artikel hebben opgegeven dat u de installatie kopie labelt met een naam ruimte, de "voor beelden" in `/samples/nginx` . Als refresher is de opdracht [docker push][docker-push] opgegeven in dat artikel:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Omdat Azure Container Registry dergelijke opslag ruimten met meerdere niveaus ondersteunt, kunt u verzamelingen van installatie kopieën die betrekking hebben op een specifieke app of een verzameling apps, beperken tot verschillende ontwikkel-of operationele teams. Zie [persoonlijke docker-container registers in azure](container-registry-intro.md)voor meer informatie over opslag plaatsen in container registers.

Een opslag plaats weer geven:

1. Meld u aan bij [Azure Portal][portal]
1. Selecteer de **Azure container Registry** waarnaar u de nginx-afbeelding hebt gepusht
1. Selecteer **opslag** plaatsen om een lijst weer te geven met de opslag plaatsen die de installatie kopieën in het REGI ster bevatten
1. Selecteer een opslag plaats om de afbeeldings codes in die opslag plaats weer te geven

Als u bijvoorbeeld de nginx-installatie kopie hebt gepusht, zoals in [een push-en pull-installatie kopie](container-registry-get-started-docker-cli.md), ziet u iets dat lijkt op:

![Opslag plaatsen in de portal](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>Volgende stappen

Nu u de basis principes kent van het weer geven en werken met opslag plaatsen in de portal, kunt u Azure Container Registry gebruiken met een [Azure Kubernetes service (AKS)-](../aks/tutorial-kubernetes-prepare-app.md) cluster.

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com

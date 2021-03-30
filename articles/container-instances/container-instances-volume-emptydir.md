---
title: EmptyDir volume koppelen aan container groep
description: Meer informatie over het koppelen van een emptyDir-volume voor het delen van gegevens tussen de containers in een container groep in Azure Container Instances
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 64a3c83008f163167528a5e5987fe2316942d5bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "77117749"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Een emptyDir-volume koppelen in Azure Container Instances

Meer informatie over het koppelen van een *emptyDir* -volume voor het delen van gegevens tussen de containers in een container groep in azure container instances. Gebruik *emptyDir* -volumes als tijdelijke caches voor uw door de container geplaatste workloads.

> [!NOTE]
> Het koppelen van een *emptyDir* -volume is momenteel beperkt tot Linux-containers. Terwijl we aan de slag gaan met het toevoegen van alle functies aan Windows-containers, kunt u de huidige platform verschillen vinden in het [overzicht](container-instances-overview.md#linux-and-windows-containers).

## <a name="emptydir-volume"></a>emptyDir volume

Het *emptyDir* -volume biedt een Beschrijf bare Directory die toegankelijk is voor elke container in een container groep. Containers in de groep kunnen dezelfde bestanden in het volume lezen en schrijven en kunnen worden gekoppeld met dezelfde of verschillende paden in elke container.

Een voor beeld van een *emptyDir* -volume:

* Scratch ruimte
* Controle punten uitvoeren tijdens langlopende taken
* Gegevens opslaan die zijn opgehaald door een zijspan wagen en worden bediend door een toepassings container

Gegevens in een *emptyDir* -volume blijven behouden via container crashes. Containers die opnieuw worden opgestart, zijn echter niet gegarandeerd de gegevens in een *emptyDir* -volume op te slaan. Als u een container groep stopt, wordt het *emptyDir* -volume niet persistent gemaakt.

De maximale grootte van een Linux *emptyDir* -volume is 50 GB.

## <a name="mount-an-emptydir-volume"></a>Een emptyDir-volume koppelen

Als u een emptyDir-volume in een container exemplaar wilt koppelen, kunt u implementeren met behulp van een [Azure Resource Manager-sjabloon](/azure/templates/microsoft.containerinstance/containergroups), een [yaml-bestand](container-instances-reference-yaml.md)of andere programmatische methoden voor het implementeren van een container groep.

Vul eerst de `volumes` matrix in het gedeelte container Group `properties` van het bestand in. Voor elke container in de container groep waarin u het *emptyDir* -volume wilt koppelen, vult u de `volumeMounts` matrix in het `properties` gedeelte van de container definitie.

De volgende Resource Manager-sjabloon maakt bijvoorbeeld een container groep die bestaat uit twee containers, die elk het *emptyDir* -volume koppelen:

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Zie [een groep met meerdere containers implementeren met behulp van een resource manager-sjabloon](container-instances-multi-container-group.md) en [een groep met meerdere containers implementeren met behulp van een yaml-bestand](container-instances-multi-container-yaml.md)om voor beelden te bekijken van implementatie van container groep.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het koppelen van andere volume typen in Azure Container Instances:

* [Een Azure-bestandsshare koppelen in Azure Container Instances](container-instances-volume-azure-files.md)
* [Een gitRepo-volume koppelen in Azure Container Instances](container-instances-volume-gitrepo.md)
* [Een geheim volume koppelen in Azure Container Instances](container-instances-volume-secret.md)
---
title: Ondersteunde inhouds indelingen
description: Meer informatie over inhouds indelingen die worden ondersteund door Azure Container Registry, waaronder docker-compatibele container installatie kopieën, helm-grafieken, OCI-afbeeldingen en OCI-artefacten.
ms.topic: article
ms.date: 03/02/2021
ms.openlocfilehash: 218d98f3f16e8d0ca76a24692afbb2b69606564b
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223061"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Inhouds indelingen die worden ondersteund in Azure Container Registry

Gebruik een privé-opslag plaats in Azure Container Registry om een van de volgende inhouds indelingen te beheren. 

## <a name="docker-compatible-container-images"></a>Docker-compatibele container installatie kopieën

De volgende indelingen van de docker-container installatie kopieën worden ondersteund:

* [Manifest voor docker-installatie kopie v2, schema 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker-installatie kopie manifest v2, schema 2](https://docs.docker.com/registry/spec/manifest-v2-2/) : bevat manifest lijsten waarmee registers [installatie kopieën met meerdere architecturen](push-multi-architecture-images.md) kunnen opslaan onder één `image:tag` Referentie

## <a name="oci-images"></a>OCI-afbeeldingen

Azure Container Registry ondersteunt installatie kopieën die voldoen aan de specificatie van de [indeling open container Initiative (OCI)](https://github.com/opencontainers/image-spec/blob/master/spec.md), met inbegrip van de optionele definitie van de [installatie kopie-index](https://github.com/opencontainers/image-spec/blob/master/image-index.md) . Pakket indelingen zijn onder andere de indeling van de [enkelvouds afbeelding](https://github.com/sylabs/sif).

## <a name="oci-artifacts"></a>OCI-artefacten

Azure Container Registry ondersteunt de [OCI-distributie specificatie](https://github.com/opencontainers/distribution-spec), een leverancier-neutraal, Cloud-neutraal specificatie voor het opslaan, delen, beveiligen en implementeren van container installatie kopieën en andere inhouds typen (artefacten). Met de specificatie kan een REGI ster een breed scala aan artefacten opslaan naast container installatie kopieën. U gebruikt hulp middelen die geschikt zijn voor het artefacten om artefacten te pushen en te pullen. Zie [een OCI-artefact pushen en trekken met behulp van een Azure container Registry](container-registry-oci-artifacts.md)voor een voor beeld.

Zie voor meer informatie over OCI-artefacten het [OCI-REGI ster als Storage (Oras)](https://github.com/deislabs/oras) opslag plaats en de [OCI-artefacten](https://github.com/opencontainers/artifacts) opslag plaats op github.

## <a name="helm-charts"></a>Helm-grafieken

Azure Container Registry kunt opslag plaatsen hosten voor [helm-grafieken](https://helm.sh/), een verpakkings indeling die wordt gebruikt voor het snel beheren en implementeren van toepassingen voor Kubernetes. [Helm-client](https://docs.helm.sh/using_helm/#installing-helm) versie 3 wordt aanbevolen. Zie [push-en pull-helm-diagrammen naar een Azure container Registry](container-registry-helm-repos.md).

## <a name="next-steps"></a>Volgende stappen

* Zie installatie kopieën [pushen en pullen](container-registry-get-started-docker-cli.md) met Azure container Registry.

* Gebruik [ACR-taken](container-registry-tasks-overview.md) om container installatie kopieën te bouwen en te testen. 

* Gebruik de [Moby-BuildKit](https://github.com/moby/buildkit) voor het maken en inpakken van containers in OCI-indeling.

* Stel een [helm-opslag plaats](container-registry-helm-repos.md) in die wordt gehost in azure container Registry. 



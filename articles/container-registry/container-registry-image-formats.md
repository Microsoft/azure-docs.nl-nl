---
title: Ondersteunde inhouds indelingen
description: Meer informatie over inhouds indelingen die worden ondersteund door Azure Container Registry, waaronder docker-compatibele container installatie kopieën, helm-grafieken, OCI-afbeeldingen en OCI-artefacten.
ms.topic: article
ms.date: 08/30/2019
ms.openlocfilehash: b2a54c65d149a27ed9eae85c3308d657ed3471a3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100008329"
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

Azure Container Registry kunt opslag plaatsen hosten voor [helm-grafieken](https://helm.sh/), een verpakkings indeling die wordt gebruikt voor het snel beheren en implementeren van toepassingen voor Kubernetes. [Helm-client](https://docs.helm.sh/using_helm/#installing-helm) versie 2 (2.11.0 of hoger) wordt ondersteund.

## <a name="next-steps"></a>Volgende stappen

* Zie installatie kopieën [pushen en pullen](container-registry-get-started-docker-cli.md) met Azure container Registry.

* Gebruik [ACR-taken](container-registry-tasks-overview.md) om container installatie kopieën te bouwen en te testen. 

* Gebruik de [Moby-BuildKit](https://github.com/moby/buildkit) voor het maken en inpakken van containers in OCI-indeling.

* Stel een [helm-opslag plaats](container-registry-helm-repos.md) in die wordt gehost in azure container Registry. 



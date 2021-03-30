---
title: Registers van beheerde containers
description: Kennismaking met de Azure Container Registry-service, waarmee u cloudgebaseerde, beheerde en persoonlijke Docker-registers kunt maken.
author: stevelas
ms.topic: overview
ms.date: 02/10/2020
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: b5c81d8b2e6d7eac2dcd9070bf1f448340ea1a18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96341262"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Inleiding tot privé-Docker-containerregisters in Azure

Azure Container Registry is een beheerde service voor privé-Docker-registers gebaseerd op de open-source Docker Registry 2.0. Maak en onderhoud Azure-containerregisters om uw persoonlijke installatiekopieën en bijbehorende artefacten voor Docker-containers op te slaan en te beheren.

Gebruik Azure-containerregisters met uw bestaande pijplijnen voor containerontwikkeling en -implementatie of gebruik Azure Container Registry-taken om containerinstallatiekopieën in Azure te bouwen. Bouw op aanvraag of automatiseer builds volledig met triggers door het vastleggen van broncode en updates van basisinstallatiekopieën.

Zie [Docker-overzicht](https://docs.docker.com/engine/docker-overview/) en [Registers, opslagplaatsen en installatiekopieën](container-registry-concepts.md) voor meer informatie over Docker en registerconcepten.

## <a name="use-cases"></a>Gebruiksvoorbeelden

Haal installatiekopieën op vanuit een Azure-containerregister en push ze naar verschillende implementatiedoelen:

* **Schaalbare indelingssystemen** die toepassingen in een container beheren in hostclusters, waaronder [Kubernetes](https://kubernetes.io/docs/), [DC/OS](https://docs.mesosphere.com/) en [Docker Swarm](https://docs.docker.com/get-started/swarm-deploy/).
* **Azure-services** die het bouwen en uitvoeren van toepassingen op schaal ondersteunen, waaronder [Azure Kubernetes Service (AKS)](../aks/index.yml), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](../service-fabric/index.yml) en andere.

Ontwikkelaars kunnen ook naar een containerregister pushen als onderdeel van een ontwikkelingswerkstroom met containers. Bijvoorbeeld naar een containerregister vanuit doorlopende integratie- leveringsprogramma's als [Azure-pijplijnen ](/azure/devops/pipelines/ecosystems/containers/acr-template) of [Jenkins](https://jenkins.io/).

Configureer ACR-taken om automatisch installatiekopieën van toepassingen te maken wanneer hun basisinstallatiekopie wordt bijgewerkt of automatiseer de bouw van installatiekopieën wanneer uw team code vastlegt in een Git-opslagplaats. Maak taken van meerdere stappen om het bouwen, testen en patchen van meerdere containerinstallatiekopieën parallel in de cloud te automatiseren.

Azure biedt hulpprogramma's met inbegrip van Azure-opdrachtregelinterface, Azure-portal en API-ondersteuning om uw Azure-containerregisters te beheren. Installeer eventueel de [Docker-extensie voor Visual Studio-code](https://code.visualstudio.com/docs/azure/docker) en de [Azure-account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)extensie om te werken met uw Azure-containerregisters. Pull en push installatiekopieën naar een Azure-containerregister of voer ACR-taken uit, allemaal in Visual Studio-code.

## <a name="key-features"></a>Belangrijke functies

* **Registerservicelaag**: maak een of meerdere containerregisters in uw Azure-abonnement. Registers zijn beschikbaar in drie lagen: [Basic, Standard en Premium](container-registry-skus.md), die alle ondersteuning bieden voor integratie van webhooks, registerverificatie met Azure Active Directory en functionaliteit voor verwijderen. Maak een register op dezelfde Azure-locatie als uw implementaties om te profiteren van lokale opslag dichtbij in het netwerk van uw containerinstallatiekopieën. Gebruik de [geo-replicatie](container-registry-geo-replication.md)functie van Premium-registers voor geavanceerde replicatie- en distributiescenario's voor containerinstallatiekopieën. 

* **Beveiliging en toegang**: u meldt u aan bij een register met de Azure CLI- of de standaard `docker login`-opdracht. Azure Container Registry draagt installatiekopieën over via HTTPS en ondersteunt TLS om clientverbindingen te beveiligen. 

  > [!IMPORTANT]
  > Vanaf 13 januari 2020 moeten voor Azure Container Registry alle beveiligde verbindingen van servers en toepassingen TLS 1.2 gebruiken. Schakel TLS 1.2 in met een recente docker-client (versie 18.03.0 of hoger). Ondersteuning voor TLS 1.0 en 1.1 wordt buiten gebruik gesteld. 

  U kunt [toegang beheren](container-registry-authentication.md) tot een containerregister met behulp van een Azure-identiteit, een door Azure Active Directory ondersteunde [service-principal](../active-directory/develop/app-objects-and-service-principals.md) of een opgegeven beheeraccount. Gebruik op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) om gebruikers of systemen nauwkeurige machtigingen toe te kennen voor een register.

  Beveiligingsfuncties van de Premium-servicelaag omvatten onder meer [inhoudsvertrouwen](container-registry-content-trust.md) voor het ondertekenen van installatiekopietags en [firewalls en virtuele netwerken (preview)](container-registry-vnet.md) om toegang tot het register te beperken. Azure Security Center kan eventueel worden geïntegreerd met Azure Container Registry om [installatiekopieën te scannen](../security-center/defender-for-container-registries-introduction.md?bc=%2fazure%2fcontainer-registry%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcontainer-registry%2ftoc.json) wanneer een installatiekopie naar een register wordt gepusht.

* **Ondersteunde installatiekopieën en artefacten**: elke installatiekopie die is opgeslagen in een opslagplaats is een alleen-lezenmomentopname van een met Docker compatibele container. Azure-containerregisters kunnen zowel Windows- als Linux-installatiekopieën bevatten. U beheert de namen van de installatiekopieën voor al uw containerimplementaties. Gebruik standaard-[Docker-opdrachten](https://docs.docker.com/engine/reference/commandline/) om installatiekopieën naar een opslagplaats te pushen of een installatiekopie uit een opslagplaats op te halen. Naast Docker-containerinstallatiekopieën slaat Azure Container Registry [gerelateerde inhoudsindelingen](container-registry-image-formats.md) op, zoals [Helm-grafieken](container-registry-helm-repos.md) en installatiekopieën gebouwd volgens de [Specificatie voor de installatiekopie-indeling Open Container Initiative (OCI)](https://github.com/opencontainers/image-spec/blob/master/spec.md).

* **Installatiekopieën automatisch bouwen**: gebruik [Azure Container Registry-taken](container-registry-tasks-overview.md) (ACR-taken) om het ontwikkelen, testen, pushen en implementeren van installatiekopieën in Azure te stroomlijnen. Gebruik bijvoorbeeld ACR-taken om uw interne ontwikkelingsactiviteiten uit te breiden naar de cloud door `docker build`-bewerkingen te offloaden naar Azure. Configureer buildtaken om uw container-OS- en frameworkpatchingpijplijn te automatiseren en automatisch installatiekopieën te maken wanneer uw team code met bronbeheer doorvoert.

  [Taken met meerdere stappen](container-registry-tasks-overview.md#multi-step-tasks) biedt een stapsgewijze taakdefinitie en -uitvoering voor het ontwikkelen, testen en patchen van containerinstallatiekopieën in de cloud. Taakstappen definiëren afzonderlijke ontwikkel- en pushbewerkingen voor containerinstallatiekopieën. Ze kunnen ook de uitvoering definiëren van een of meer containers, waarbij elke stap de container als uitvoeringsomgeving gebruikt.

## <a name="next-steps"></a>Volgende stappen

* [Een containerregister maken met Azure Portal](container-registry-get-started-portal.md)
* [Een containerregister maken met de Azure-CLI](container-registry-get-started-azure-cli.md)
* [Ontwikkeling en onderhoud van container-builds met ACR-taken](container-registry-tasks-overview.md)
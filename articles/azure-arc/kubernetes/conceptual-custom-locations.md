---
title: 'Aangepaste locaties: Azure Arc enabled Kubernetes'
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Dit artikel bevat een conceptueel overzicht van aangepaste locatie mogelijkheden van Azure Arc enabled Kubernetes
ms.openlocfilehash: 1235c6adfe97a943ef77584a6a0c8683d691be91
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451027"
---
# <a name="custom-locations-on-top-of-azure-arc-enabled-kubernetes"></a>Aangepaste locaties boven op Azure-Arc ingeschakeld Kubernetes

Als een uitbrei ding van de *Azure-locatie constructie kunnen Tenant* beheerders hun Azure Arc-Kubernetes-clusters gebruiken als doel locaties voor het implementeren van Azure Services-exemplaren. Azure-resources-voor beelden zijn onder meer Azure Arc enabled SQL Managed instance en Azure Arc enabled PostgreSQL grootschalige.

Net als bij Azure-locaties kunnen eind gebruikers in de Tenant met toegang tot aangepaste locaties resources implementeren met behulp van de persoonlijke Compute van hun bedrijf.

[![Arc-platform lagen ](./media/conceptual-arc-platform-layers.png)](./media/conceptual-arc-platform-layers.png#lightbox)

U kunt aangepaste locaties visualiseren als een abstractie laag boven op Azure Arc enabled Kubernetes cluster, cluster Connect en cluster Extensions. Aangepaste locaties maken de gedetailleerde [RoleBindings en ClusterRoleBindings](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) die nodig zijn voor andere Azure-Services voor toegang tot het cluster. Voor deze andere Azure-Services is toegang tot het cluster vereist voor het beheren van resources die de klant wil implementeren op hun clusters.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Architectuur

Wanneer de beheerder de functie voor aangepaste locaties op het cluster inschakelt, wordt er een ClusterRoleBinding op het cluster gemaakt, waarbij de Azure AD-toepassing die wordt gebruikt door de Custom locations resource provider (RP) wordt geautoriseerd. Na toestemming kunnen aangepaste locaties RP ClusterRoleBindings of RoleBindings maken die nodig zijn voor andere Azure RPs om aangepaste resources te maken op dit cluster. De cluster uitbreidingen die op het cluster zijn geïnstalleerd, bepalen de lijst met RPs die moeten worden geautoriseerd.

[![Aangepaste locaties ](./media/conceptual-custom-locations-usage.png) gebruiken](./media/conceptual-custom-locations-usage.png#lightbox)

Wanneer de gebruiker een exemplaar van de gegevens service op het cluster maakt: 
1. De PUT-aanvraag wordt verzonden naar Azure Resource Manager.
1. De PUT-aanvraag wordt doorgestuurd naar de Azure-Arc ingeschakeld Data Services RP. 
1. De RP haalt het `kubeconfig` bestand op dat is gekoppeld aan het Azure Arc enabled Kubernetes-cluster, waarop de aangepaste locatie zich bevindt. 
   * Naar een aangepaste locatie wordt verwezen als `extendedLocation` in de oorspronkelijke put-aanvraag. 
1. Azure Arc enabled Data Services RP gebruikt de `kubeconfig` om te communiceren met het cluster om een aangepaste resource te maken van het type Azure-Arc ingeschakeld Data Services op de naam ruimte die is toegewezen aan de aangepaste locatie. 
   * De Azure Arc enabled Data Services-operator is geïmplementeerd via het maken van een cluster uitbreiding voordat de aangepaste locatie aanwezig was. 
1. Met de Azure Arc enabled Data Services-operator wordt de nieuwe aangepaste resource die is gemaakt op het cluster gelezen en wordt de gegevens controller gemaakt en wordt deze omgezet in realisatie van de gewenste status in het cluster. 

De volg orde van de stappen voor het maken van het exemplaar van SQL Managed instance en PostgreSQL zijn identiek aan de volg orde van de stappen die hierboven worden beschreven.

## <a name="next-steps"></a>Volgende stappen

* Gebruik onze Snelstartgids om [een Kubernetes-cluster te verbinden met Azure Arc](./quickstart-connect-cluster.md).
* [Maak een aangepaste locatie](./custom-locations.md) op uw Azure Arc enabled Kubernetes-cluster.
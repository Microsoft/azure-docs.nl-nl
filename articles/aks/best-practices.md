---
title: Aanbevolen procedures voor Azure Kubernetes service (AKS)
description: Verzameling van de best practices voor cluster operators en ontwikkel aars voor het maken en beheren van toepassingen in azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 12/07/2018
ms.openlocfilehash: e8f9c1946567ac3c3ea1045a216d6b0486fd959a
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686256"
---
# <a name="cluster-operator-and-developer-best-practices-to-build-and-manage-applications-on-azure-kubernetes-service-aks"></a>Aanbevolen procedures voor cluster operators en ontwikkel aars voor het maken en beheren van toepassingen in azure Kubernetes service (AKS)

Als u toepassingen wilt bouwen en uitvoeren in azure Kubernetes service (AKS), zijn er enkele belang rijke aandachtspunten om te begrijpen en implementeren. Dit zijn onder andere multitenancy-en scheduler-functies, cluster-en pod-beveiliging, of bedrijfs continuïteit en herstel na nood gevallen. De volgende aanbevolen procedures zijn gegroepeerd om cluster operators en ontwikkel aars inzicht te geven in de overwegingen voor elk van deze gebieden en de juiste functies uit te voeren.

Deze aanbevolen procedures en conceptuele artikelen zijn geschreven in combi natie met de AKS-product groep, technische teams en veld teams, met inbegrip van wereld wijde zwarte gordels (GBBs).

## <a name="cluster-operator-best-practices"></a>Aanbevolen procedures voor cluster operators

Als cluster operator kunt u samen werken met eigen aren van toepassingen en ontwikkel aars om inzicht te krijgen in hun behoeften. U kunt vervolgens de volgende aanbevolen procedures gebruiken om uw AKS-clusters naar behoefte te configureren.

**Multitenancy**

* [Aanbevolen procedures voor clusterisolatie](operator-best-practices-cluster-isolation.md)
    * Inclusief multitenancy-kern onderdelen en logische isolatie met naam ruimten.
* [Aanbevolen procedures voor standaard Scheduler-functies](operator-best-practices-scheduler.md)
    * Omvat het gebruik van resource quota's en pod-verstoringen budgetten.
* [Aanbevolen procedures voor geavanceerde Scheduler-functies](operator-best-practices-advanced-scheduler.md)
    * Omvat het gebruik van taints en verdragen, knooppunt selecties en affiniteit en de Inter-pod-affiniteit en anti-affiniteit.
* [Aanbevolen procedures voor verificatie en autorisatie](operator-best-practices-identity.md)
    * Omvat de integratie met Azure Active Directory, met behulp van Kubernetes op rollen gebaseerd toegangs beheer (Kubernetes RBAC), met behulp van Azure RBAC en pod-identiteiten.

**Beveiliging**

* [Aanbevolen procedures voor beveiliging en upgrades van clusters](operator-best-practices-cluster-security.md)
    * Omvat het beveiligen van toegang tot de API-server, het beperken van toegang tot containers en het beheren van upgrades en het opnieuw opstarten van knoop punten.
* [Aanbevolen procedures voor het beheer en de beveiliging van container installatie kopieën](operator-best-practices-container-image-management.md)
    * Omvat het beveiligen van de installatie kopie en runtime en geautomatiseerde builds voor updates van basis installatie kopieën.
* [Aanbevolen procedures voor pod-beveiliging](developer-best-practices-pod-security.md)
    * Omvat het beveiligen van de toegang tot bronnen, het beperken van de referentie blootstelling en het gebruik van pod-identiteiten en digitale sleutel kluizen.

**Netwerk en opslag**

* [Aanbevolen procedures voor netwerk connectiviteit](operator-best-practices-network.md)
    * Bevat verschillende netwerk modellen, met behulp van binnenkomend en firewalls voor webtoepassingen (WAF) en het beveiligen van het knoop punt SSH-toegang.
* [Best practices voor opslag en back-ups](operator-best-practices-storage.md)
    * Omvat het kiezen van het juiste opslag type en de gewenste knooppunt grootte, het dynamisch inrichten van volumes en gegevens back-ups.

**Bedrijfs klare workloads uitvoeren**

* [Aanbevolen procedures voor bedrijfs continuïteit en herstel na nood gevallen](operator-best-practices-multi-region.md)
    * Omvat het gebruik van regio paren, meerdere clusters met Azure Traffic Manager en geo-replicatie van container installatie kopieën.

## <a name="developer-best-practices"></a>Best practices voor ontwikkel aars

Als ontwikkelaar of toepassings eigenaar kunt u uw ontwikkelings ervaring vereenvoudigen en de behoeften van de toepassings prestaties bepalen.

* [Aanbevolen procedures voor toepassingsontwikkelaars om resources te beheren](developer-best-practices-resource-management.md)
    * Omvat het definiëren van Pod en limieten voor de resource, het configureren van ontwikkel hulpprogramma's en het controleren op toepassings problemen.
* [Aanbevolen procedures voor pod-beveiliging](developer-best-practices-pod-security.md)
    * Omvat het beveiligen van de toegang tot bronnen, het beperken van de referentie blootstelling en het gebruik van pod-identiteiten en digitale sleutel kluizen.

## <a name="kubernetes--aks-concepts"></a>Kubernetes/AKS-concepten

Als u wilt weten wat de functies en onderdelen van deze best practices zijn, kunt u ook de volgende conceptuele artikelen zien voor clusters in azure Kubernetes service (AKS):

* [Basis concepten voor Kubernetes](concepts-clusters-workloads.md)
* [Toegang en identiteit](concepts-identity.md)
* [Beveiligingsconcepten](concepts-security.md)
* [Netwerk concepten](concepts-network.md)
* [Opslagopties](concepts-storage.md)
* [Schaal opties](concepts-scale.md)

## <a name="next-steps"></a>Volgende stappen

Als u aan de slag wilt met AKS, volgt u een van de Quick starts voor het implementeren van een Azure Kubernetes service (AKS)-cluster met behulp van [Azure cli](kubernetes-walkthrough.md) of [Azure Portal](kubernetes-walkthrough-portal.md).

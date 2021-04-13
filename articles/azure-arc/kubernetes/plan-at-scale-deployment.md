---
title: Azure Arc enabled Kubernetes plannen en implementeren
services: azure-arc
ms.service: azure-arc
ms.date: 04/12/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Onboarding van een groot aantal clusters naar Azure Arc enabled Kubernetes voor configuratie beheer
ms.openlocfilehash: 4b34cee08db508728f01d262ae4b1ee4ed4754e1
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315426"
---
# <a name="plan-and-deploy-azure-arc-enabled-kubernetes"></a>Azure Arc enabled Kubernetes plannen en implementeren

De implementatie van een IT-infrastructuur service of bedrijfs toepassing is een uitdaging voor elk bedrijf. Als u onwelkome of ongeplande kosten wilt voor komen, moet u er rekening mee houden om ervoor te zorgen dat u zo klaar mogelijk bent. In een dergelijk plan moeten ontwerp-en implementatie criteria worden geïdentificeerd waaraan moet worden voldaan om de taken uit te voeren.

Als u de implementatie soepel wilt voortzetten, moet uw plan een duidelijk inzicht hebben in:

* Rollen en verantwoordelijkheden.
* Inventarisatie van alle Kubernetes-clusters
* Voldoen aan de netwerk vereisten.
* De vaardig heden en training die vereist zijn om de implementatie en het continue beheer mogelijk te maken.
* Acceptatie criteria en hoe u het kunt volgen.
* Hulpprogram ma's of methoden die moeten worden gebruikt voor het automatiseren van de implementaties.
* Er zijn Risico's en beperkende plannen gevonden om vertragingen en onderbrekingen te voor komen.
* Hoe voorkom ik onderbrekingen tijdens de implementatie.
* Wat is het escalatie traject wanneer er een significant probleem optreedt?

Het doel van dit artikel is om ervoor te zorgen dat u bent voor bereid op een succes volle implementatie van Azure Arc enabled Kubernetes over meerdere productie clusters in uw omgeving.

## <a name="prerequisites"></a>Vereisten

* Een bestaand Kubernetes-cluster. Als u er geen hebt, kunt u een cluster maken met een van de volgende opties:
    - [Kubernetes in docker (KIND)](https://kind.sigs.k8s.io/)
    - Een Kubernetes-cluster maken met behulp van docker voor [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) of [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)
    - Zelf-beheerde Kubernetes-cluster met behulp van [cluster-API](https://cluster-api.sigs.k8s.io/user/quick-start.html)

* Uw computers hebben verbinding met uw on-premises netwerk of een andere cloud omgeving, hetzij rechtstreeks, hetzij via een proxy server. Meer informatie vindt u onder [netwerk vereisten](quickstart-connect-cluster.md#meet-network-requirements).

* Een `kubeconfig` bestand dat verwijst naar het cluster dat u wilt verbinden met Azure Arc.
* Lees-en schrijf machtigingen voor de gebruiker of Service-Principal waarbij het Kubernetes-resource type van Azure Arc is ingeschakeld `Microsoft.Kubernetes/connectedClusters` .

## <a name="pilot"></a>Test

Voordat u naar alle productie clusters implementeert, moet u eerst het implementatie proces evalueren voordat u het globaal in uw omgeving aanneemt. Voor een pilot moet u een representatieve steek proef van clusters identificeren die niet essentieel zijn voor uw bedrijf om zaken te doen. Zorg ervoor dat u voldoende tijd hebt om de pilot uit te voeren en het effect ervan te beoordelen: we raden u ongeveer 30 dagen aan.

Stel een formeel plan op waarin het bereik en de details van de pilot worden beschreven. Aan de hand van het volgende voorbeeld schema kunt u aan de slag.

* **Doel stellingen** : beschrijft de zakelijke en technische Stuur Programma's die hebben geleid tot de beslissing dat een pilot nood zakelijk is.
* **Selectie criteria** : Hiermee geeft u de criteria op die worden gebruikt om te selecteren welke aspecten van de oplossing worden gedemonstreerd via een pilot.
* **Bereik** : Hiermee worden oplossings onderdelen, verwachte planning, duur van de pilot en het aantal clusters met doel beschreven.
* **Criteria voor succes en metrische gegevens** : Definieer de succes criteria van de pilot en specifieke metingen die worden gebruikt om het niveau van het succes te bepalen.
* **Trainings plan** : hierin wordt het plan voor het trainen van systeem technici, beheerders, enzovoort, beschreven die tijdens de pilot geen ervaring hebben met Azure en IT-Services.
* **Overgangs plan** : beschrijft de strategie en de criteria die worden gebruikt om de overgang van de pilot naar de productie handleiding te begeleiden.
* **Rollback** : hierin worden de procedures beschreven voor het terugdraaien van een pilot naar de status vóór de implementatie.
* **Risico's** : lijst met alle geïdentificeerde Risico's voor het uitvoeren van de pilot en het koppelen van de productie-implementatie.

## <a name="phase-1-build-a-foundation"></a>Fase 1: een basis versie bouwen

In deze fase voeren systeem engineers of beheerders de kern activiteiten uit die zijn gemaakt voor het maken van resource groepen, tags, roltoewijzingen, zodat de Azure-Kubernetes-resources kunnen worden gemaakt en beheerd.

|Taak |Detail |Duur |
|-----|-------|---------|
| [Een resourcegroep maken](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | Een speciale resource groep die alleen Kubernetes-resources van Azure Arc kan bevatten en het beheer en de bewaking van deze resources kan centraliseren. | Een uur |
| [Tags](../../azure-resource-manager/management/tag-resources.md) Toep assen om machines te organiseren. | Evalueer en ontwikkel een IT-uitgelijnde [coderings strategie](/azure/cloud-adoption-framework/decision-guides/resource-tagging/). Dit kan helpen de complexiteit te verminderen van het beheren van uw Azure Arc-Kubernetes-resources en het vereenvoudigen van beheer beslissingen. | Eén dag |
| [Configuraties](tutorial-use-gitops-connected-cluster.md) voor GitOps identificeren | Bepaal de toepassing of basislijn configuraties zoals `PodSecurityPolicy` , `NetworkPolicy` die u wilt implementeren in uw clusters | Eén dag |
| [Een Azure Policy](../../governance/policy/overview.md) governance-abonnement ontwikkelen | Bepaal hoe u governance van Azure Arc enabled Kubernetes-clusters op het abonnement of het bereik van de resource groep met Azure Policy implementeert. | Eén dag |
| [Op rollen gebaseerd toegangs beheer](../../role-based-access-control/overview.md) (RBAC) configureren | Een toegangs plan ontwikkelen om te bepalen wie de machtigingen lezen/schrijven/alle heeft voor uw clusters | Eén dag |

## <a name="phase-2-deploy-azure-arc-enabled-kubernetes"></a>Fase 2: Azure Arc enabled Kubernetes implementeren

In deze fase verbinden we uw Kubernetes-clusters met Azure:

|Taak |Detail |Duur |
|-----|-------|---------|
| [Uw eerste Kubernetes-cluster verbinden met Azure Arc](quickstart-connect-cluster.md) | Als onderdeel van het aansluiten van uw eerste cluster op Azure Arc, stelt u uw onboarding-omgeving in met alle vereiste hulpprogram ma's, zoals Azure CLI, helm en `connectedk8s` Extension voor Azure cli. | 15 minuten |
| [Een service-principal maken](create-onboarding-service-principal.md) | Maak een Service-Principal om Kubernetes clusters niet-interactief te koppelen met behulp van Azure CLI of Power shell. | Een uur |


## <a name="phase-3-manage-and-operate"></a>Fase 3: beheren en uitvoeren

In deze fase implementeren we toepassingen en basislijn configuraties voor uw Kubernetes-clusters.

|Taak |Detail |Duur |
|-----|-------|---------|
|[Configuraties maken](tutorial-use-gitops-connected-cluster.md) in uw clusters | Maak configuraties voor het implementeren van uw toepassingen op uw Azure-Kubernetes-resource. | 15 minuten |
|[Azure Policy gebruiken](use-azure-policy.md) voor het op schaal afdwingen van configuraties | Maak beleids toewijzingen voor het automatiseren van de implementatie van basislijn configuraties in al uw clusters onder een abonnement of een resource groeps bereik. | 15 minuten |
| [Azure Arc-agents bijwerken](agent-upgrade.md) | Als u automatisch bijwerken van agents op uw clusters hebt uitgeschakeld, werkt u uw agents hand matig bij naar de nieuwste versie om ervoor te zorgen dat u de meest recente beveiligings-en fout oplossingen hebt. | 15 minuten |

## <a name="next-steps"></a>Volgende stappen

* Gebruik onze Snelstartgids om [een Kubernetes-cluster te verbinden met Azure Arc](./quickstart-connect-cluster.md).
* [Maak configuraties](./tutorial-use-gitops-connected-cluster.md) op uw Azure Arc enabled Kubernetes-cluster.
* [Gebruik Azure Policy om configuraties op schaal toe te passen](./use-azure-policy.md).
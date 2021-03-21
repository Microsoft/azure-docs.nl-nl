---
title: Hybride infra structuur op schaal beheren met Azure Arc
description: Meer informatie over het effectief beheren van de computers en Kubernetes-clusters van uw klanten buiten Azure.
ms.date: 03/12/2021
ms.topic: how-to
ms.openlocfilehash: ec32389cd7444405580530a00c8b7c5bc48bcd56
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419317"
---
# <a name="manage-hybrid-infrastructure-at-scale-with-azure-arc"></a>Hybride infra structuur op schaal beheren met Azure Arc

Als service provider hebt u mogelijk meerdere tenants voor klanten in [Azure Lighthouse](../overview.md). Met Azure Lighthouse kunnen service providers bewerkingen tegelijk uitvoeren in meerdere Azure Active Directory (Azure AD)-tenants, waardoor beheer taken efficiënter worden.

[Azure Arc](../../azure-arc/overview.md) helpt bij het vereenvoudigen van complexe en gedistribueerde omgevingen over on-premises, Edge en multicloud, waardoor Azure-Services overal kunnen worden geïmplementeerd en Azure-beheer kan worden uitgebreid naar elke infra structuur.

Met [Azure Arc-servers](../../azure-arc/servers/overview.md)kunnen klanten alle Windows-en Linux-machines beheren die buiten Azure worden gehost op hun bedrijfs netwerk, op dezelfde manier als waarop ze systeem eigen virtuele machines van Azure beheren. Door een hybride computer aan Azure te koppelen, wordt deze verbonden en wordt deze behandeld als een resource in Azure. Service providers kunnen vervolgens deze niet-Azure-machines beheren samen met de Azure-resources van hun klanten.

Met [Azure Arc enabled Kubernetes](../../azure-arc/kubernetes/overview.md) kunnen klanten Kubernetes-clusters binnen of buiten Azure aansluiten en configureren. Wanneer een Kubernetes-cluster is gekoppeld aan Azure-Arc, wordt het weer gegeven in de Azure Portal, met een Azure Resource Manager-ID en een beheerde identiteit. Clusters zijn gekoppeld aan standaardabonnementen in Azure, bevinden zich in een resourcegroep, en kunnen labels ontvangen zoals elke andere Azure-resource.

In dit onderwerp vindt u een overzicht van hoe service providers Azure Arc-servers en Azure Arc-Kubernetes kunnen gebruiken op een schaal bare manier om de hybride omgeving van hun klanten te beheren, met zicht baarheid in alle beheerde tenants van de klant.

> [!TIP]
> Hoewel we in dit onderwerp naar service providers en klanten verwijzen, is deze richt lijn ook van toepassing op [ondernemingen die Azure Lighthouse gebruiken om meerdere tenants te beheren](../concepts/enterprise.md).

## <a name="manage-hybrid-servers-at-scale-with-azure-arc-enabled-servers"></a>Hybride servers op schaal beheren met servers die geschikt zijn voor Azure-Arc

Als service provider kunt u on-premises Windows Server-of Linux-computers buiten Azure beheren die uw klanten hebben verbonden met hun abonnement met behulp van de [Azure Connected machine agent](../../azure-arc/servers/agent-overview.md).

Wanneer u resources weergeeft voor een gedelegeerd abonnement in de Azure Portal, ziet u deze verbonden computers met het label **Azure Arc**. U kunt deze verbonden machines met behulp van Azure-constructs, zoals Azure Policy en tagging, op dezelfde manier beheren als de Azure-resources van de klant. U kunt ook op de tenants van de klant werken om alle aangesloten hybride machines samen te beheren.

U kunt er bijvoorbeeld voor [zorgen dat dezelfde set beleids regels wordt toegepast op hybride computers van klanten](../../azure-arc/servers/learn/tutorial-assign-policy-portal.md). U kunt Azure Security Center ook gebruiken om de naleving van alle hybride omgevingen van uw klanten te bewaken of [Azure monitor gebruiken om gegevens rechtstreeks van uw hybride computers te verzamelen](../../azure-arc/servers/learn/tutorial-enable-vm-insights.md) in een log Analytics-werk ruimte. [Extensies voor virtuele machines](../../azure-arc/servers/manage-vm-extensions.md) kunnen worden geïmplementeerd op niet-Azure Windows-en Linux-vm's, waardoor het beheer van de hybride machines van de klant wordt vereenvoudigd.

## <a name="manage-hybrid-kubernetes-clusters-at-scale-with-azure-arc-enabled-kubernetes"></a>Hybride Kubernetes-clusters op schaal beheren met Azure-Arc ingeschakeld Kubernetes

U kunt Kubernetes-clusters beheren die zijn [verbonden met het abonnement van een klant met Azure Arc](../../azure-arc/kubernetes/connect-cluster.md), net alsof ze in azure werden uitgevoerd.

Als uw klant een service- [Principal-account heeft gemaakt voor het voorbereiden van Kubernetes-clusters naar Azure Arc](../../azure-arc/kubernetes/create-onboarding-service-principal.md), hebt u toegang tot dit Service-Principal-account voor het onboarden en beheren van clusters. Dit kan worden gedaan door gebruikers in de Tenant voor het beheren van de ' Kubernetes-cluster: Azure Arc-onboarding '. de ingebouwde rol Azure-hand leiding wanneer het abonnement met het Service-Principal-account is geklaard [voor Azure Lighthouse](onboard-customer.md).

U kunt [configuraties](../../azure-arc/kubernetes/use-gitops-connected-cluster.md) en [helm-grafieken](../../azure-arc/kubernetes/use-gitops-with-helm.md) implementeren met behulp van GitOps voor verbonden clusters.

U kunt ook verbonden clusters bewaken met Azure Monitor en [Azure Policy gebruiken om cluster configuraties op schaal toe te passen](../../azure-arc/kubernetes/use-azure-policy.md).

## <a name="next-steps"></a>Volgende stappen

- Bekijk de gestarte en voor beelden in de [Azure Arc github-opslag plaats](https://github.com/microsoft/azure_arc).
- Meer informatie over [ondersteunde scenario's voor Azure Arc-servers](../../azure-arc/servers/overview.md#supported-scenarios).
- Meer informatie over [Kubernetes-distributies die worden ondersteund door Azure Arc](../../azure-arc/kubernetes/overview.md#supported-kubernetes-distributions).
- Meer informatie over het [implementeren van een beleid op schaal](policy-at-scale.md).
- Meer informatie over het [gebruik van Azure monitor-logboeken op schaal](monitor-at-scale.md).

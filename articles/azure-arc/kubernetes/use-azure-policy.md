---
title: Azure Policy gebruiken om clusterconfiguraties op schaal toe te passen (preview)
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure Policy gebruiken om cluster configuraties op schaal toe te passen
keywords: Kubernetes, Arc, azure, K8s, containers
ms.openlocfilehash: 23cd42458c396afd31741c648d713934250a4112
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587804"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Azure Policy gebruiken om clusterconfiguraties op schaal toe te passen (preview)

## <a name="overview"></a>Overzicht

U kunt Azure Policy gebruiken om een van de volgende resources af te dwingen voor specifieke `Microsoft.KubernetesConfiguration/sourceControlConfigurations` toegepaste:
*  `Microsoft.Kubernetes/connectedclusters` resource.
* Resource met GitOps-functionaliteit `Microsoft.ContainerService/managedClusters` . 

Als u Azure Policy wilt gebruiken, selecteert u een bestaande beleids definitie en maakt u een beleids toewijzing. Bij het maken van de beleids toewijzing:
1. Stel het bereik voor de toewijzing in.
    * Het bereik is een Azure-resource groep of-abonnement. 
2. Stel de para meters in voor de `sourceControlConfiguration` die worden gemaakt. 

Zodra de toewijzing is gemaakt, identificeert de Azure Policy engine `connectedCluster` alle `managedCluster` resources of bronnen die zich binnen het bereik bevinden en past deze `sourceControlConfiguration` toe op elke.

U kunt meerdere Git-opslag plaatsen inschakelen als de bronnen van waarheid voor elk cluster door gebruik te maken van meerdere beleids toewijzingen. Elke beleids toewijzing zou worden geconfigureerd voor het gebruik van een andere Git-opslag plaats. een voor beeld: een opslag plaats voor de centrale IT/cluster-operator en andere opslag plaatsen voor toepassings teams.

## <a name="prerequisite"></a>Vereiste

Controleer of u `Microsoft.Authorization/policyAssignments/write` machtigingen hebt voor het bereik (abonnement of resource groep) waar u deze beleids toewijzing gaat maken.

## <a name="create-a-policy-assignment"></a>Een beleidstoewijzing maken

1. Ga in het Azure Portal naar **beleid**.
1. Selecteer in het gedeelte **ontwerpen** van de zijbalk **definities**.
1. Kies in de categorie ' Kubernetes ' het ingebouwde beleid ' GitOps to Kubernetes cluster implementeren '. 
1. Klik op **toewijzen**.
1. Stel het **bereik** in op de beheer groep, het abonnement of de resource groep waarop de beleids toewijzing van toepassing is.
    * Als u resources uit het beleids bereik wilt uitsluiten, stelt u **uitsluitingen** in.
1. Geef de beleids toewijzing een eenvoudig herken bare **naam** en **Beschrijving**.
1. Zorg ervoor dat het **afdwingen van beleid** is ingesteld op **ingeschakeld**.
1. Selecteer **Next**.
1. Stel de parameter waarden in die moeten worden gebruikt bij het maken van de `sourceControlConfiguration` .
1. Selecteer **Next**.
1. Schakel **een herstel taak maken** in.
1. Controleer of **er een beheerde identiteit maken** is ingeschakeld en of de identiteit **Inzender** machtigingen heeft. 
    * Zie de [Snelstartgids een beleids toewijzing maken](../../governance/policy/assign-policy-portal.md) en de [niet-compatibele resources herstellen met Azure Policy artikel](../../governance/policy/how-to/remediate-resources.md)voor meer informatie.
1. Selecteer **Controleren + maken**.

Nadat u de beleids toewijzing hebt gemaakt, `sourceControlConfiguration` wordt deze toegepast voor een van de volgende resources die zich binnen het bereik van de toewijzing bevinden:
* Nieuwe `connectedCluster` resources.
* Nieuwe `managedCluster` resources waarop de GitOps-agents zijn geïnstalleerd. 

Voor bestaande clusters moet u een herstel taak hand matig uitvoeren. Deze taak duurt doorgaans 10 tot 20 minuten voordat de beleids toewijzing van kracht wordt.

## <a name="verify-a-policy-assignment"></a>Een beleids toewijzing controleren

1. Navigeer in het Azure Portal naar een van uw `connectedCluster` resources.
1. Selecteer in de sectie **instellingen** van de zijbalk **beleids regels**. 
    * De AKS-cluster UX is nog niet geïmplementeerd.
    * In de lijst met beleids regels ziet u de beleids toewijzing die u eerder hebt gemaakt met de **nalevings status** ingesteld op *compatibel*.
1. Selecteer in de sectie **instellingen** van de zijbalk **configuraties**.
    * In de lijst configuraties ziet u `sourceControlConfiguration` dat de beleids toewijzing is gemaakt.
1. Gebruiken `kubectl` voor het opvragen van het cluster. 
    * U ziet de naam ruimte en artefacten die zijn gemaakt door `sourceControlConfiguration` .
    * Binnen vijf minuten ziet u in de cluster de artefacten die worden beschreven in de manifesten in het geconfigureerde Git-opslag plaats.

## <a name="next-steps"></a>Volgende stappen

* [Azure Monitor instellen voor containers met Kubernetes-clusters die zijn ingeschakeld voor Arc](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md)

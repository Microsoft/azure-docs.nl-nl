---
title: Azure Policy gebruiken om cluster configuraties op schaal toe te passen
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure Policy gebruiken om cluster configuraties op schaal toe te passen
keywords: Kubernetes, Arc, azure, K8s, containers
ms.openlocfilehash: 05a6665a985ef8b229ee58082dc9b2c10cdcece3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121453"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale"></a>Azure Policy gebruiken om cluster configuraties op schaal toe te passen

U kunt Azure Policy gebruiken om configuraties ( `Microsoft.KubernetesConfiguration/sourceControlConfigurations` resource type) op schaal toe te passen op de Kubernetes-clusters van Azure Arc enabled ( `Microsoft.Kubernetes/connectedclusters` ).

Als u Azure Policy wilt gebruiken, selecteert u een bestaande beleids definitie en maakt u een beleids toewijzing. Bij het maken van de beleids toewijzing:
1. Stel het bereik voor de toewijzing in.
    * Het bereik is een Azure-resource groep of-abonnement. 
2. Stel de para meters in voor de configuratie die wordt gemaakt. 

Zodra de toewijzing is gemaakt, identificeert Azure Policy engine alle Azure-Kubernetes-clusters die zich binnen het bereik bevinden en past de configuratie toe op elk cluster.

U kunt meerdere configuraties maken, die elk verwijzen naar een andere Git-opslag plaats, met behulp van meerdere beleids toewijzingen. Een voor beeld: een opslag plaats voor de centrale IT/cluster-operator en andere opslag plaatsen voor toepassings teams.

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

Na het maken van de beleids toewijzing wordt de configuratie toegepast op nieuwe Azure Arc-Kubernetes-clusters die zijn gemaakt binnen het bereik van beleids toewijzing.

Voor bestaande clusters moet u hand matig een herstel taak uitvoeren. Deze taak duurt doorgaans 10 tot 20 minuten voordat de beleids toewijzing van kracht wordt.

## <a name="verify-a-policy-assignment"></a>Een beleids toewijzing controleren

1. Navigeer in het Azure Portal naar een van uw Azure Arc-Kubernetes-clusters.
1. Selecteer in de sectie **instellingen** van de zijbalk **beleids regels**. 
    * In de lijst met beleids regels ziet u de beleids toewijzing die u eerder hebt gemaakt met de **nalevings status** ingesteld op *compatibel*.
1. Selecteer in de sectie **instellingen** van de zijbalk **configuraties**.
    * In de lijst configuraties ziet u de configuratie die is gemaakt door de beleids toewijzing.
1. Gebruiken `kubectl` voor het opvragen van het cluster. 
    * De naam ruimte en artefacten die zijn gemaakt door de configuratie bronnen worden weer geven.
    * Binnen vijf minuten (uitgaande van het cluster heeft een netwerk verbinding met Azure), ziet u de objecten die worden beschreven door de manifesten in Git opslag plaats, die worden gemaakt op het cluster.

## <a name="next-steps"></a>Volgende stappen

[Azure monitor instellen voor containers met Azure Arc enabled Kubernetes-clusters](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md).

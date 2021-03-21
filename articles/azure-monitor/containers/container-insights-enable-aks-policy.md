---
title: Invoeg toepassing voor AKS-bewaking inschakelen met behulp van Azure Policy
description: Hierin wordt beschreven hoe u AKS monitoring-invoeg toepassing inschakelt met aangepast Azure-beleid.
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 2163527cc83e70913e9a6e11bf2e22f9ed9c6690
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713895"
---
# <a name="enable-aks-monitoring-addon-using-azure-policy"></a>Invoeg toepassing voor AKS-bewaking inschakelen met behulp van Azure Policy
In dit artikel wordt beschreven hoe u AKS monitoring-invoeg toepassing inschakelt met aangepast Azure-beleid. Het aangepaste beleid voor het bewaken van invoeg toepassingen kan worden toegewezen aan het abonnement of het bereik van de resource groep. Als de Azure Log Analytics-werk ruimte en het AKS-cluster zich in verschillende abonnementen bevinden, moet de beheerde identiteit die wordt gebruikt door de beleids toewijzing, beschikken over de vereiste rolmachtigingen op beide abonnementen of op de bron van de Log Analytics-werk ruimte. Als het beleid is toegewezen aan de resource groep, moet de beheerde identiteit de vereiste rolmachtigingen hebben op de Log Analytics-werk ruimte als de werk ruimte niet in het geselecteerde bereik van de resource groep is.

Voor het controleren van addon zijn de volgende rollen vereist voor de beheerde identiteit die wordt gebruikt door Azure Policy:

 - [Azure-kubernetes-service-Inzender-rol](../../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role)
 - [Log-Analytics-Inzender](../../role-based-access-control/built-in-roles.md#log-analytics-contributor)

## <a name="create-and-assign-policy-definition-using-azure-portal"></a>Beleids definitie maken en toewijzen met behulp van Azure Portal

### <a name="create-policy-definition"></a>Beleids definitie maken

1. Down load de definitie van het aangepaste Azure-beleid om AKS-bewaking in te scha kelen.
 
    ``` sh
    curl -o azurepolicy.json -L https://aka.ms/aks-enable-monitoring-custom-policy
    ```

3. Navigeer naar https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions en maak beleids definitie met de volgende details in het dialoog venster beleids definitie maken.
 
    - **Definitie locatie**: Kies het Azure-abonnement waarin de beleids definitie moet worden opgeslagen.
    - **Naam**: *(preview) AKS-invoeg toepassing*
    - **Beschrijving**: *aangepast Azure-beleid voor het inschakelen van invoeg toepassing op Azure Kubernetes-cluster (s) in het opgegeven bereik*
    - **Categorie**: Kies *bestaande gebruiken* en kies *Kubernetes* in de vervolg keuzelijst.
    - **Beleids regel**: Verwijder de bestaande voorbeeld regels en kopieer de inhoud van *azurepolicy.js* in stap #1 hierboven.

### <a name="assign-policy-definition-to-specified-scope"></a>Beleids definitie toewijzen aan het opgegeven bereik

> [!NOTE]
>  De beheerde identiteit wordt automatisch gemaakt en opgegeven rollen worden toegewezen in de beleids definitie.

1. Selecteer de beleids definitie *(preview) AKS-invoeg* toepassing die u zojuist hebt gemaakt.
4. Klik op *toewijzen** * en geef een **bereik** op van waar het beleid moet worden toegewezen. 
5. Klik op **volgende** en geef de resource-id op van de Azure log Analytics-werk ruimte. De resource-ID moet de volgende indeling hebben `/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>` .
6. Maak een herstel taak als u op beleid wilt Toep assen op bestaande AKS-clusters in het geselecteerde bereik.
7. Klik op **beoordeling + optie maken** om de beleids toewijzing te maken.
   
## <a name="create-and-assign-policy-definition-using-azure-cli"></a>Beleids definitie maken en toewijzen met behulp van Azure CLI

### <a name="create-policy-definition"></a>Beleids definitie maken

1. Down load de regels voor het aangepaste Azure-beleid en de parameter bestanden met de volgende opdrachten:

    ``` sh
    curl -o azurepolicy.rules.json -L https://aka.ms/aks-enable-monitoring-custom-policy-rules
    curl -o azurepolicy.parameters.json -L https://aka.ms/aks-enable-monitoring-custom-policy-parameters
    ```

2. Maak de beleids definitie met de volgende opdracht:

    ``` sh
    az cloud set -n <AzureCloud | AzureChinaCloud | AzureUSGovernment> # set the Azure cloud
    az login # login to cloud environment 
    az account set -s <subscriptionId>
    az policy definition create --name "(Preview)AKS-Monitoring-Addon" --display-name "(Preview)AKS-Monitoring-Addon" --mode Indexed --metadata version=1.0.0 category=Kubernetes --rules azurepolicy.rules.json --params azurepolicy.parameters.json
    ```

### <a name="assign-policy-definition-to-specified-scope"></a>Beleids definitie toewijzen aan het opgegeven bereik

- Maak de toewijzing van het beleid met de volgende opdracht:

    ``` sh
    az policy assignment create --name aks-monitoring-addon --policy "(Preview)AKS-Monitoring-Addon" --assign-identity --identity-scope /subscriptions/<subscriptionId> --role Contributor --scope /subscriptions/<subscriptionId> --location <locatio> --role Contributor --scope /subscriptions/<subscriptionId> -p "{ \"workspaceResourceId\": { \"value\":  \"/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>\" } }"
    ```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure Policy](../../governance/policy/overview.md).
- Meer informatie over hoe [herstel beveiliging werkt](../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works).
- Meer informatie over [container Insights](./container-insights-overview.md).
- Installeer de [Azure CLI](/cli/azure/install-azure-cli).
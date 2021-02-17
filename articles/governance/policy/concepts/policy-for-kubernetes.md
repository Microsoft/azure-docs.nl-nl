---
title: Azure Policy leren voor Kubernetes
description: Lees hoe Azure Policy Rego gebruikt en beleids agent opent voor het beheren van clusters met Kubernetes in azure of on-premises.
ms.date: 12/01/2020
ms.topic: conceptual
ms.openlocfilehash: 0aaf610cd5712ee195ed2a4108cf9e5ca9c65183
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577098"
---
# <a name="understand-azure-policy-for-kubernetes-clusters"></a>Azure Policy voor Kubernetes-clusters

Azure Policy breidt [gate keeper](https://github.com/open-policy-agent/gatekeeper) v3, een _toegangs controller-Webhook_ voor [Open Policy Agent](https://www.openpolicyagent.org/) (opa), uit om afdwingingen en beveiligingen op uw clusters op een gecentraliseerde, consistente manier toe te passen. Azure Policy maakt het mogelijk om de compatibiliteits status van uw Kubernetes-clusters vanaf één locatie te beheren en te rapporteren. Met de invoeg toepassing worden de volgende functies aangenomen:

- Hiermee wordt met Azure Policy-service gecontroleerd of beleids toewijzingen aan het cluster worden toegewezen.
- Hiermee worden beleids definities geïmplementeerd in het cluster als [beperkings sjabloon](https://github.com/open-policy-agent/gatekeeper#constraint-templates) en aangepaste resources voor [beperkingen](https://github.com/open-policy-agent/gatekeeper#constraints) .
- Rapporteert controle-en nalevings details terug naar Azure Policy service.

Azure Policy voor Kubernetes ondersteunt de volgende cluster omgevingen:

- [Azure Kubernetes Service (AKS)](../../../aks/intro-kubernetes.md)
- [Kubernetes met Azure Arc](../../../azure-arc/kubernetes/overview.md)
- [AKS-engine](https://github.com/Azure/aks-engine/blob/master/docs/README.md)

> [!IMPORTANT]
> De **Preview-versie** van de AKS-engine en de Arc ingeschakelde Kubernetes zijn beschikbaar. Azure Policy voor Kubernetes biedt alleen ondersteuning voor Linux-knooppunt Pools en ingebouwde beleids definities. Ingebouwde beleids definities bevinden zich in de categorie **Kubernetes** . De beperkte preview-beleids definities met **EnforceOPAConstraint** -en **EnforceRegoPolicy** -effect en de gerelateerde **service categorie Kubernetes** zijn _afgeschaft_. Gebruik in plaats daarvan de effecten _controleren_ en _weigeren_ met de resource provider modus `Microsoft.Kubernetes.Data` .

## <a name="overview"></a>Overzicht

Als u Azure Policy met uw Kubernetes-cluster wilt inschakelen en gebruiken, voert u de volgende acties uit:

1. Configureer uw Kubernetes-cluster en installeer de invoeg toepassing:
   - [Azure Kubernetes Service (AKS)](#install-azure-policy-add-on-for-aks)
   - [Kubernetes met Azure Arc](#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)
   - [AKS-engine](#install-azure-policy-add-on-for-aks-engine)

   > [!NOTE]
   > Zie [probleem oplossing-Azure Policy-invoeg toepassing](../troubleshoot/general.md#add-on-for-kubernetes-installation-errors)voor veelvoorkomende problemen met de installatie.

1. [Inzicht in de Azure Policy taal voor Kubernetes](#policy-language)

1. [Een ingebouwde definitie toewijzen aan uw Kubernetes-cluster](#assign-a-built-in-policy-definition)

1. [Wachten op validatie](#policy-evaluation)

## <a name="limitations"></a>Beperkingen

De volgende algemene beperkingen zijn van toepassing op de Azure Policy-invoeg toepassing voor Kubernetes-clusters:

- Azure Policy-invoeg toepassing voor Kubernetes wordt ondersteund op Kubernetes versie **1,14** of hoger.
- Azure Policy-invoeg toepassing voor Kubernetes kan alleen worden geïmplementeerd voor Linux-knooppunt Pools
- Alleen ingebouwde beleids definities worden ondersteund
- Maximum aantal niet-compatibele records per beleid per cluster: **500**
- Maximum aantal niet-compatibele records per abonnement: **1.000.000**
- Installatie van gate keeper buiten de Azure Policy-invoeg toepassing wordt niet ondersteund. Verwijder onderdelen die door een eerdere gate keeper-installatie zijn geïnstalleerd voordat u de invoeg toepassing Azure Policy inschakelt.
- [Redenen voor niet-naleving](../how-to/determine-non-compliance.md#compliance-reasons) zijn niet beschikbaar voor de modus van de `Microsoft.Kubernetes.Data` 
   [resource provider](./definition-structure.md#resource-provider-modes). [Onderdeel gegevens](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes)gebruiken.
- [Uitzonde ringen](./exemption-structure.md) worden niet ondersteund voor [resource provider modi](./definition-structure.md#resource-provider-modes).

De volgende beperkingen gelden alleen voor de Azure Policy-invoeg toepassing voor AKS:

- [AKS pod-beveiligings beleid](../../../aks/use-pod-security-policies.md) en de Azure Policy-invoeg toepassing voor AKS kunnen niet beide worden ingeschakeld. Zie [AKS pod Security beperking](../../../aks/use-pod-security-on-azure-policy.md#limitations)(Engelstalig) voor meer informatie.
- Naam ruimten automatisch uitgesloten door Azure Policy invoeg toepassing voor evaluatie: _uitvoeren-System_, _gate keeper-System_ en _AKS-Peri Scope_.

## <a name="recommendations"></a>Aanbevelingen

Hieronder vindt u algemene aanbevelingen voor het gebruik van de Azure Policy-invoeg toepassing:

- Voor de invoeg toepassing Azure Policy zijn 3 gate keeper-onderdelen vereist om uit te voeren: 1 audit pod en 2 webhook pod-replica's. Deze onderdelen gebruiken meer bronnen als het aantal Kubernetes-resources en beleids toewijzingen toenames in het cluster waarvoor controle-en afdwingings bewerkingen vereist zijn.

  - Voor minder dan 500 peul in één cluster met een maximum van 20 beperkingen: 2 Vcpu's en 350 MB geheugen per onderdeel.
  - Meer dan 500 peul in één cluster met een maximum van 40 beperkingen: 3 Vcpu's en 600 MB geheugen per onderdeel.

- Windows-peul [biedt geen ondersteuning voor beveiligings contexten](https://kubernetes.io/docs/concepts/security/pod-security-standards/#what-profiles-should-i-apply-to-my-windows-pods).
  Sommige van de Azure Policy definities, zoals het niet toestaan van basis rechten, kunnen niet worden geëscaleerd in Windows-peul en zijn alleen van toepassing op Linux peul.

De volgende aanbeveling geldt alleen voor AKS en de invoeg toepassing Azure Policy:

- Gebruik de systeem knooppunt groep met `CriticalAddonsOnly` Taint om de taak van de gate keeper te plannen. Zie het [gebruik van systeem knooppunt groepen](../../../aks/use-system-pools.md#system-and-user-node-pools)voor meer informatie.
- Beveiligde uitgaand verkeer van uw AKS-clusters. Zie voor meer informatie controle van uitgaand [verkeer voor cluster knooppunten](../../../aks/limit-egress-traffic.md).
- Als het cluster is `aad-pod-identity` ingeschakeld, wijzigt NMI (node Managed Identity) het knoop punt ' iptables ' om aanroepen naar het eind punt van de meta gegevens van het Azure-exemplaar te onderscheppen. Deze configuratie houdt in dat elke aanvraag voor het eind punt van de meta gegevens wordt onderschept door NMI, zelfs als de pod niet wordt gebruikt `aad-pod-identity` . AzurePodIdentityException CRD kan zodanig worden geconfigureerd `aad-pod-identity` dat alle aanvragen voor het eind punt van de meta gegevens die afkomstig zijn van een pod die overeenkomt met de labels die zijn gedefinieerd in CRD, via een proxy moeten worden gewaarschuwd zonder enige verwerking in NMI. Het systeem van het `kubernetes.azure.com/managedby: aks` label in de naam ruimte _uitvoeren_ moet worden uitgesloten in `aad-pod-identity` door de AzurePodIdentityException CRD te configureren. Zie [Aad-pod-Identity voor een specifieke Pod of toepassing uitschakelen](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)voor meer informatie.
  Als u een uitzonde ring wilt configureren, installeert u de YAML van de [Mic-uitzonde ring](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml).

## <a name="install-azure-policy-add-on-for-aks"></a>Azure Policy-invoeg toepassing voor AKS installeren

Voordat u de Azure Policy invoeg toepassing installeert of een van de service functies inschakelt, moet uw abonnement de resource providers **micro soft. container service** en **micro soft. PolicyInsights** inschakelen.

1. U moet de Azure CLI-versie 2.12.0 of hoger hebben geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

1. Registreer de resource providers en preview-functies.

   - Azure Portal:

     Registreer de resource providers **micro soft. container service** en **micro soft. PolicyInsights** . Zie [resource providers en-typen](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)voor instructies.

   - Azure CLI:

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Kubernetes Service provider
     az provider register --namespace Microsoft.ContainerService

     # Provider register: Register the Azure Policy provider
     az provider register --namespace Microsoft.PolicyInsights
     ```

1. Als er beperkte preview-beleids definities zijn geïnstalleerd, verwijdert u de invoeg toepassing met de knop **uitschakelen** in uw AKS-cluster op de pagina **beleid** .

1. Het AKS-cluster moet versie _1,14_ of hoger zijn. Gebruik het volgende script om uw AKS-cluster versie te valideren:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Installeer versie _2.12.0_ of hoger van de Azure cli. Zie [De Azure CLI installeren](/cli/azure/install-azure-cli) voor meer informatie.

Zodra de bovenstaande vereiste stappen zijn voltooid, installeert u de Azure Policy-invoeg toepassing in het AKS-cluster dat u wilt beheren.

- Azure Portal

  1. Start de AKS-service in de Azure Portal door **alle services** te selecteren en vervolgens op **Kubernetes-Services** te zoeken en te selecteren.

  1. Selecteer een van uw AKS-clusters.

  1. Selecteer **beleid** aan de linkerkant van de Kubernetes-service pagina.

  1. Selecteer op de hoofd pagina de knop **invoeg toepassing inschakelen** .

     <a name="migrate-from-v1"></a>
     > [!NOTE]
     > Als de knop **invoeg toepassing uitschakelen** is ingeschakeld en er een bericht over een migratie waarschuwing v2 wordt weer gegeven, wordt v1 add-on geïnstalleerd en moet het worden verwijderd voordat u v2-beleids definities toewijst. De _afgeschafte_ v1-invoeg toepassing wordt automatisch vervangen door de v2-invoeg toepassing vanaf 24 augustus
     > 2020. Nieuwe v2-versies van de beleids definities moeten vervolgens worden toegewezen. Voer de volgende stappen uit om nu een upgrade uit te voeren:
     >
     > 1. Voor het valideren van uw AKS-cluster is de V1-invoeg toepassing geïnstalleerd door de pagina **beleids regels** op uw AKS-cluster te bezoeken. het huidige cluster maakt gebruik van Azure Policy add-on v1... Bericht.
     > 1. [Verwijder de invoeg toepassing](#remove-the-add-on-from-aks).
     > 1. Selecteer de knop **invoeg toepassing inschakelen** om de v2-versie van de invoeg toepassing te installeren.
     > 1. [V2-versies van uw v1 ingebouwde beleids definities toewijzen](#assign-a-built-in-policy-definition)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

Voer de volgende opdracht uit om te controleren of de installatie van de invoeg toepassing is geslaagd en het _Azure-beleid_ en de _gate keeper_ peul worden uitgevoerd:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

Controleer ten slotte of de meest recente invoeg toepassing is geïnstalleerd door deze Azure CLI-opdracht uit te voeren, vervangen door `<rg>` de naam van de resource groep en `<cluster-name>` de naam van uw AKS-cluster: `az aks show --query addonProfiles.azurepolicy -g <rg> -n <cluster-name>` . Het resultaat moet er ongeveer uitzien als de volgende uitvoer en **configuratie. versie** moet zijn `v2` :

```output
"addonProfiles": {
    "azurepolicy": {
        "config": {
            "version": "v2"
        },
        "enabled": true,
        "identity": null
    },
}
```

## <a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes-preview"></a><a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes"></a>Azure Policy-invoeg toepassing installeren voor Azure Arc enabled Kubernetes (preview)

Voordat u de Azure Policy invoeg toepassing installeert of een van de service functies inschakelt, moet uw abonnement de resource provider **micro soft. PolicyInsights** inschakelen en een roltoewijzing maken voor de Cluster-service-principal.

1. U moet de Azure CLI-versie 2.12.0 of hoger hebben geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

1. Als u de resource provider wilt inschakelen, volgt u de stappen in [resource providers en typen](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) of voert u de Azure CLI-of Azure PowerShell opdracht uit:

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Het Kubernetes-cluster moet versie _1,14_ of hoger zijn.

1. Installeer [helm 3](https://v3.helm.sh/docs/intro/install/).

1. Uw Kubernetes-cluster is ingeschakeld voor Azure Arc. Zie voor meer informatie [een onboarding van een Kubernetes-cluster naar Azure Arc](../../../azure-arc/kubernetes/connect-cluster.md).

1. Hebben de volledig gekwalificeerde Azure-Resource-ID van het Kubernetes-cluster met Azure Arc enabled.

1. Open poorten voor de invoeg toepassing. De invoeg toepassing Azure Policy gebruikt deze domeinen en poorten om beleids definities en toewijzingen op te halen en de compatibiliteit van het cluster weer te geven aan Azure Policy.

   |Domain |Poort |
   |---|---|
   |`gov-prod-policy-data.trafficmanager.net` |`443` |
   |`raw.githubusercontent.com` |`443` |
   |`login.windows.net` |`443` |
   |`dc.services.visualstudio.com` |`443` |

1. Wijs de roltoewijzing ' policy Insights Data Writer (preview) ' toe aan het Azure Arc enabled Kubernetes-cluster. Vervang door `<subscriptionId>` uw abonnements-id, `<rg>` met de resource groep Azure Arc enabled Kubernetes-cluster en `<clusterName>` met de naam van het Azure-Arc-Kubernetes-cluster. Houd bij de installatie stappen de geretourneerde waarden bij voor _AppID_, _wacht woord_ en _Tenant_ .

   - Azure CLI

     ```azurecli-interactive
     az ad sp create-for-rbac --role "Policy Insights Data Writer (Preview)" --scopes "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     $sp = New-AzADServicePrincipal -Role "Policy Insights Data Writer (Preview)" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"

     @{ appId=$sp.ApplicationId;password=[System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret));tenant=(Get-AzContext).Tenant.Id } | ConvertTo-Json
     ```

   Voorbeeld uitvoer van de bovenstaande opdrachten:

   ```json
   {
       "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
       "password": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
       "tenant": "cccccccc-cccc-cccc-cccc-cccccccccccc"
   }
   ```

Zodra de bovenstaande vereiste stappen zijn voltooid, installeert u de Azure Policy-invoeg toepassing in uw Azure Arc enabled Kubernetes-cluster:

1. Voeg de Azure Policy add-on opslag plaats toe aan helm:

   ```bash
   helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
   ```

1. Installeer de Azure Policy-invoeg toepassing met behulp van de helm-grafiek:

   ```bash
   # In below command, replace the following values with those gathered above.
   #    <AzureArcClusterResourceId> with your Azure Arc enabled Kubernetes cluster resource Id. For example: /subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>
   #    <ServicePrincipalAppId> with app Id of the service principal created during prerequisites.
   #    <ServicePrincipalPassword> with password of the service principal created during prerequisites.
   #    <ServicePrincipalTenantId> with tenant of the service principal created during prerequisites.
   helm install azure-policy-addon azure-policy/azure-policy-addon-arc-clusters \
       --set azurepolicy.env.resourceid=<AzureArcClusterResourceId> \
       --set azurepolicy.env.clientid=<ServicePrincipalAppId> \
       --set azurepolicy.env.clientsecret=<ServicePrincipalPassword> \
       --set azurepolicy.env.tenantid=<ServicePrincipalTenantId>
   ```

   Voor meer informatie over de installatie van de add-on helm-grafiek, zie de [grafiek definitie van de helm-invoeg toepassing Azure Policy](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-arc-clusters) op github.

Voer de volgende opdracht uit om te controleren of de installatie van de invoeg toepassing is geslaagd en het _Azure-beleid_ en de _gate keeper_ peul worden uitgevoerd:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="install-azure-policy-add-on-for-aks-engine-preview"></a><a name="install-azure-policy-add-on-for-aks-engine"></a>Azure Policy-invoeg toepassing installeren voor AKS Engine (preview-versie)

Voordat u de Azure Policy invoeg toepassing installeert of een van de service functies inschakelt, moet uw abonnement de resource provider **micro soft. PolicyInsights** inschakelen en een roltoewijzing maken voor de Cluster-service-principal.

1. U moet de Azure CLI-versie 2.0.62 of hoger hebben geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

1. Als u de resource provider wilt inschakelen, volgt u de stappen in [resource providers en typen](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) of voert u de Azure CLI-of Azure PowerShell opdracht uit:

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Maak een roltoewijzing voor de service-principal van het cluster.

   - Als u de App-ID van de Cluster-service-principal niet weet, kunt u deze bekijken met de volgende opdracht.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system

     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Wijs de roltoewijzing ' policy Insights Data Writer (preview) ' toe aan de App-ID van de Cluster-service-principal (waarde _aadClientID_ uit de vorige stap) met Azure cli. Vervang door `<subscriptionId>` uw abonnements-id en `<aks engine cluster resource group>` de resource groep waarin het zelf beheerde Kubernetes-cluster van de AKS-engine zich bevindt.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

Nadat de bovenstaande vereiste stappen zijn voltooid, installeert u de Azure Policy-invoeg toepassing. De installatie kan worden uitgevoerd tijdens het maken of bijwerken van een AKS-Engine of als onafhankelijke actie op een bestaand cluster.

- Installeren tijdens maken of bijwerken van cyclus

  Als u de invoeg toepassing Azure Policy wilt inschakelen tijdens het maken van een nieuw zelf beheerd cluster of als een update van een bestaand cluster, neemt u de eigenschap [Addons](https://github.com/Azure/aks-engine/tree/master/examples/addons/azure-policy) cluster definition voor de AKS-engine op.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true
  }]
  ```

  Zie voor meer informatie over de externe hand leiding [AKS engine-cluster definitie](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).

- Installeren in een bestaand cluster met helm-grafieken

  Gebruik de volgende stappen om het cluster voor te bereiden en de invoeg toepassing te installeren:

  1. Installeer [helm 3](https://v3.helm.sh/docs/intro/install/).

  1. Voeg de Azure Policy opslag plaats toe aan helm.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Zie [helm Chart-Quick Start Guide (Engelstalig](https://helm.sh/docs/using_helm/#quickstart-guide)) voor meer informatie.

  1. Installeer de invoeg toepassing met een helm-grafiek. Vervang door `<subscriptionId>` uw abonnements-id en `<aks engine cluster resource group>` de resource groep waarin het zelf beheerde Kubernetes-cluster van de AKS-engine zich bevindt.

     ```bash
     helm install azure-policy-addon azure-policy/azure-policy-addon-aks-engine --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Voor meer informatie over de installatie van de add-on helm-grafiek, zie de [grafiek definitie van de helm-invoeg toepassing Azure Policy](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-aks-engine) op github.

     > [!NOTE]
     > Als gevolg van de relatie tussen Azure Policy invoeg toepassing en de resource groep-ID Azure Policy ondersteunt slechts één AKS-engine cluster voor elke resource groep.

Voer de volgende opdracht uit om te controleren of de installatie van de invoeg toepassing is geslaagd en het _Azure-beleid_ en de _gate keeper_ peul worden uitgevoerd:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="policy-language"></a>Beleids taal

De Azure Policy taal structuur voor het beheren van Kubernetes volgt de bestaande beleids definities. Met de [modus van de resource provider](./definition-structure.md#resource-provider-modes) van `Microsoft.Kubernetes.Data` worden de effecten [controleren](./effects.md#audit) en [weigeren](./effects.md#deny) gebruikt voor het beheren van uw Kubernetes-clusters. _Controleren_ en _weigeren_ moeten **gedetailleerde** eigenschappen bieden die specifiek zijn voor het werken met [opa CONSTRAINT Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint) en gate keeper v3.

Als onderdeel van de eigenschappen _Details. constraintTemplate_ en _Details. CONSTRAINT_ in de beleids definitie Azure Policy door gegeven de uri's van deze [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) naar de invoeg toepassing. Rego is de taal die OPA en gate keeper ondersteunen voor het valideren van een aanvraag naar het Kubernetes-cluster. Dankzij de ondersteuning van een bestaande standaard voor Kubernetes-beheer, maakt Azure Policy het mogelijk om bestaande regels opnieuw te gebruiken en deze te koppelen aan Azure Policy voor een uniforme rapportage van de naleving van de Cloud. Zie [Wat is Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego)voor meer informatie.

## <a name="assign-a-built-in-policy-definition"></a>Een ingebouwde beleidsdefinitie toewijzen

Als u een beleids definitie wilt toewijzen aan uw Kubernetes-cluster, moet u de juiste bewerkingen voor beleids toewijzing van Azure RBAC (Azure op rollen gebaseerd toegangs beheer) krijgen. De ingebouwde functies van het **resource beleid** van de functie voor Azure zijn Inzender en de **eigenaar** hebben deze bewerkingen. Zie [Azure RBAC-machtigingen in azure Policy](../overview.md#azure-rbac-permissions-in-azure-policy)voor meer informatie.

Zoek de ingebouwde beleids definities voor het beheren van uw cluster met behulp van de Azure Portal door de volgende stappen uit te voeren:

1. Start de Azure Policy-service in de Azure Portal. Selecteer **alle services** in het linkerdeel venster en zoek en selecteer vervolgens **beleid**.

1. Selecteer in het linkerdeel venster van de pagina Azure Policy **definities**.

1. Gebruik in de vervolg keuzelijst Categorie de **optie Alles selecteren** om het filter te wissen en selecteer vervolgens **Kubernetes**.

1. Selecteer de beleids definitie en selecteer vervolgens de knop **toewijzen** .

1. Stel het **bereik** in op de beheer groep, het abonnement of de resource groep van het Kubernetes-cluster waarop de beleids toewijzing van toepassing is.

   > [!NOTE]
   > Bij het toewijzen van de Azure Policy voor Kubernetes definitie moet het **bereik** de cluster bron bevatten. De **Scope** moet de resource groep van het cluster zijn voor een AKS engine-cluster.

1. Geef de beleids toewijzing een **naam** en **Beschrijving** die u kunt gebruiken om deze gemakkelijk te identificeren.

1. Stel het [afdwingen](./assignment-structure.md#enforcement-mode) van het beleid in op een van de onderstaande waarden.

   - **Ingeschakeld** : dwing het beleid af op het cluster. Kubernetes-toegangs aanvragen met schendingen worden geweigerd.

   - **Uitgeschakeld** : dwing het beleid niet af op het cluster. Kubernetes-toegangs aanvragen met schendingen worden niet geweigerd. De resultaten van de nalevings beoordeling zijn nog steeds beschikbaar. Bij het implementeren van nieuwe beleids definities voor het uitvoeren van clusters, is de optie _uitgeschakeld_ handig voor het testen van de beleids definitie als toegangs aanvragen met schendingen niet worden geweigerd.

1. Selecteer **Next**.

1. **Parameter waarden** instellen

   - Als u Kubernetes-naam ruimten van beleids evaluatie wilt uitsluiten, geeft u de lijst met naam ruimten in de para meters van de **naam ruimte** op. Het wordt aanbevolen om: _uitvoeren-System_, _gate keeper-System_ en _Azure-Arc_ uit te sluiten.

1. Selecteer **Controleren + maken**.

U kunt ook de Snelstartgids [een beleid toewijzen-Portal](../assign-policy-portal.md) gebruiken om een Kubernetes-beleid te zoeken en toe te wijzen. Zoek in plaats van het voor beeld ' vm's controleren ' naar een Kubernetes-beleids definitie.

> [!IMPORTANT]
> Ingebouwde beleids definities zijn beschikbaar voor Kubernetes-clusters in categorie **Kubernetes**. Zie Kubernetes-voor [beelden](../samples/built-in-policies.md#kubernetes)voor een lijst met ingebouwde beleids definities.

## <a name="policy-evaluation"></a>Beleids evaluatie

De invoeg toepassing controleert met Azure Policy service of er elke 15 minuten wijzigingen in beleids toewijzingen zijn aangebracht.
Tijdens deze vernieuwings cyclus controleert de invoeg toepassing op wijzigingen. Met deze wijzigingen trigger worden de beperkings sjablonen en-beperkingen gemaakt, bijgewerkt of verwijderd.

Als een naam ruimte in een Kubernetes-cluster een van de volgende labels heeft, worden de toegangs aanvragen met schendingen niet geweigerd. De resultaten van de nalevings beoordeling zijn nog steeds beschikbaar.

- `control-plane`
- `admission.policy.azure.com/ignore`

> [!NOTE]
> Hoewel een cluster beheerder gemachtigd is om beperkings sjablonen en Azure Policy beperkingen te maken en bij te werken, worden deze niet ondersteund als hand matige updates worden overschreven. Gate keeper blijft beleid evalueren dat bestond vóór de installatie van de invoeg toepassing en het toewijzen van Azure Policy-beleids definities.

Elke 15 minuten wordt de invoeg toepassing aangeroepen voor een volledige scan van het cluster. Na het verzamelen van Details van de volledige scan en eventuele realtime-evaluaties door gate keeper van pogingen om wijzigingen aan te brengen in het cluster, worden de resultaten weer gegeven in Azure Policy om te worden opgenomen in [compatibiliteits Details](../how-to/get-compliance-data.md) zoals een Azure Policy toewijzing. Er worden alleen resultaten voor actieve beleids toewijzingen geretourneerd tijdens de controle cyclus. Controle resultaten kunnen ook worden gezien als [schendingen](https://github.com/open-policy-agent/gatekeeper#audit) die worden vermeld in het veld status van de beperking mislukt. Zie voor meer informatie over _niet-compatibele_ resources [onderdeel Details voor de resource provider modi](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes).

> [!NOTE]
> Elk nalevings rapport in Azure Policy voor uw Kubernetes-clusters bevatten alle schendingen in de afgelopen 45 minuten. De tijds tempel geeft aan wanneer een schending is opgetreden.

Enkele andere overwegingen:

- Als het cluster abonnement is geregistreerd bij Azure Security Center, wordt Azure Security Center Kubernetes-beleid automatisch toegepast op het cluster.

- Wanneer een beleid voor weigeren wordt toegepast op een cluster met bestaande Kubernetes-resources, wordt een bestaande resource die niet voldoet aan het nieuwe beleid, nog steeds uitgevoerd. Wanneer de niet-compatibele resource opnieuw wordt gepland op een ander knoop punt, blokkeert de gate keeper het maken van de resource.

- Wanneer een cluster beleid voor weigeren heeft waarmee resources worden gevalideerd, wordt door de gebruiker geen afwijzings bericht weer gegeven bij het maken van een implementatie. Denk bijvoorbeeld aan een Kubernetes-implementatie die replicasets en peul bevat. Wanneer een gebruiker wordt uitgevoerd `kubectl describe deployment $MY_DEPLOYMENT` , wordt er geen afwijzings bericht geretourneerd als onderdeel van gebeurtenissen. Retourneert echter `kubectl describe replicasets.apps $MY_DEPLOYMENT` de gebeurtenissen die aan de weigering zijn gekoppeld.

## <a name="logging"></a>Logboekregistratie

Als Kubernetes-controller/-container moeten zowel de _Azure-beleids-_ als de _gate keeper_ -Logboeken in het Kubernetes-cluster worden bewaard. De logboeken kunnen worden weer gegeven op de pagina **inzichten** van het Kubernetes-cluster.
Zie voor meer informatie [uw Kubernetes-cluster prestaties bewaken met Azure monitor voor containers](../../../azure-monitor/containers/container-insights-analyze.md).

Als u de logboeken wilt weer geven, gebruikt u `kubectl` :

```bash
# Get the azure-policy pod name installed in kube-system namespace
kubectl logs <azure-policy pod name> -n kube-system

# Get the gatekeeper pod name installed in gatekeeper-system namespace
kubectl logs <gatekeeper pod name> -n gatekeeper-system
```

Zie [debug Gate](https://github.com/open-policy-agent/gatekeeper#debugging) in de gate keeper Documentation (Engelstalig) voor meer informatie.

## <a name="troubleshooting-the-add-on"></a>Problemen met de invoeg toepassing oplossen

Zie de [sectie Kubernetes](../troubleshoot/general.md#add-on-for-kubernetes-general-errors) van het artikel Azure Policy Troubleshooting (Engelstalig) voor meer informatie over het oplossen van problemen met de invoeg toepassing voor Kubernetes.

## <a name="remove-the-add-on"></a>De invoeg toepassing verwijderen

### <a name="remove-the-add-on-from-aks"></a>De invoeg toepassing verwijderen uit AKS

Als u de invoeg toepassing Azure Policy wilt verwijderen uit uw AKS-cluster, gebruikt u de Azure Portal of Azure CLI:

- Azure Portal

  1. Start de AKS-service in de Azure Portal door **alle services** te selecteren en vervolgens op **Kubernetes-Services** te zoeken en te selecteren.

  1. Selecteer uw AKS-cluster waarvoor u de invoeg toepassing Azure Policy wilt uitschakelen.

  1. Selecteer **beleid** aan de linkerkant van de Kubernetes-service pagina.

  1. Op de hoofd pagina selecteert u de knop **invoeg toepassing uitschakelen** .

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="remove-the-add-on-from-azure-arc-enabled-kubernetes"></a>De invoeg toepassing verwijderen uit Azure Arc enabled Kubernetes

Als u Azure Policy de invoeg toepassing en gate keeper van uw Azure-Kubernetes-cluster wilt verwijderen, voert u de volgende helm-opdracht uit:

```bash
helm uninstall azure-policy-addon
```

### <a name="remove-the-add-on-from-aks-engine"></a>De invoeg toepassing verwijderen uit de AKS-engine

Als u Azure Policy de invoeg toepassing en gate keeper van uw AKS-Engine wilt verwijderen, gebruikt u de methode die wordt afgestemd op de manier waarop de invoeg toepassing is geïnstalleerd:

- Indien geïnstalleerd door de eigenschap **Addons** in de cluster definitie voor de AKS-engine in te stellen:

  Implementeer de cluster definitie opnieuw in de AKS-Engine nadat u de eigenschap **Addons** voor _Azure-Policy_ hebt gewijzigd in False:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

  Zie de [AKS-engine uitschakelen Azure Policy-invoeg toepassing](https://github.com/Azure/aks-engine/blob/master/examples/addons/azure-policy/README.md#disable-azure-policy-add-on)voor meer informatie.

- Als dit is geïnstalleerd met helm-grafieken, voert u de volgende helm-opdracht uit:

  ```bash
  helm uninstall azure-policy-addon
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Diagnostische gegevens die door Azure Policy invoeg toepassing zijn verzameld

De Azure Policy-invoeg toepassing voor Kubernetes verzamelt beperkte diagnostische gegevens van het cluster. Deze diagnostische gegevens zijn vitale technische gegevens met betrekking tot software en prestaties. Deze wordt op de volgende manieren gebruikt:

- Azure Policy invoeg toepassing up-to-date houden
- Bewaar Azure Policy extra beveiliging, betrouw bare, krachtige prestaties
- De statistische analyse van het gebruik van de invoeg toepassing verbeteren Azure Policy

De gegevens die door de invoeg toepassing worden verzameld, zijn geen persoonlijke gegevens. De volgende details worden momenteel verzameld:

- Azure Policy-Agent versie van invoeg toepassing
- Clustertype
- Cluster regio
- Cluster resource groep
- Cluster bron-ID
- Abonnements-ID van cluster
- Cluster besturingssysteem (voor beeld: Linux)
- Cluster plaats (voor beeld: Seattle)
- Status of provincie van cluster (voor beeld: Washington)
- Land of regio van het cluster (voor beeld: Verenigde Staten)
- Uitzonde ringen/fouten aangetroffen door Azure Policy-invoeg toepassing tijdens de installatie van de agent op beleids evaluatie
- Het aantal gate keeper-beleids definities dat niet is geïnstalleerd door Azure Policy invoeg toepassing

## <a name="next-steps"></a>Volgende stappen

- Bekijk voor beelden op [Azure Policy voor beelden](../samples/index.md).
- Controleer de [structuur van de beleids definitie](definition-structure.md).
- Lees [Informatie over de effecten van het beleid](effects.md).
- Meer informatie over het [programmatisch maken van beleids regels](../how-to/programmatically-create.md).
- Meer informatie over het [ophalen van compatibiliteits gegevens](../how-to/get-compliance-data.md).
- Ontdek hoe u [niet-compatibele resources kunt herstellen](../how-to/remediate-resources.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/overview.md).
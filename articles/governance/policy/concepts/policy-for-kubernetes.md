---
title: Meer Azure Policy kubernetes
description: Ontdek hoe Azure Policy Rego en Open Policy Agent gebruikt voor het beheren van clusters met Kubernetes in Azure of on-premises.
ms.date: 03/22/2021
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9ca33c3a937b0a155928f20469830388a95a08e3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107506020"
---
# <a name="understand-azure-policy-for-kubernetes-clusters"></a>Azure Policy voor Kubernetes-clusters

Azure Policy is een uitbreiding van [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3, een _webhook_ voor toegangscontrollers voor [Open Policy Agent](https://www.openpolicyagent.org/) (OPA), om afdwinging en beveiliging op schaal op uw clusters op een gecentraliseerde, consistente manier toe te passen. Azure Policy kunt u de nalevingstoestand van uw Kubernetes-clusters vanaf één plek beheren en rapporteren. De invoeg-on heeft de volgende functies:

- Controleert Azure Policy service op beleidstoewijzingen aan het cluster.
- Implementeert beleidsdefinities in het cluster als sjabloon [voor beperkingen](https://github.com/open-policy-agent/gatekeeper#constraint-templates) en aangepaste resources [voor](https://github.com/open-policy-agent/gatekeeper#constraints) beperkingen.
- Rapporteert controle- en nalevingsdetails aan Azure Policy service.

Azure Policy voor Kubernetes ondersteunt de volgende clusteromgevingen:

- [Azure Kubernetes Service (AKS)](../../../aks/intro-kubernetes.md)
- [Kubernetes met Azure Arc](../../../azure-arc/kubernetes/overview.md)
- [AKS-engine](https://github.com/Azure/aks-engine/blob/master/docs/README.md)

> [!IMPORTANT]
> De invoegtoepassingen voor AKS Engine en Kubernetes met Arc zijn in **preview.** Azure Policy kubernetes ondersteunt alleen Linux-knooppuntgroepen en ingebouwde beleidsdefinities. Ingebouwde beleidsdefinities vallen in de **categorie Kubernetes.** De beperkte preview-beleidsdefinities met het effect **EnforceOPAConstraint** en **EnforceRegoPolicy** en de gerelateerde **Kubernetes Service-categorie** _zijn afgeschaft._ Gebruik in plaats daarvan de _effectencontrole en_ _weigeren met_ de resourceprovidermodus `Microsoft.Kubernetes.Data` .

## <a name="overview"></a>Overzicht

Als u een Azure Policy kubernetes-cluster wilt inschakelen en gebruiken, moet u de volgende acties uitvoeren:

1. Configureer uw Kubernetes-cluster en installeer de invoegsel:
   - [Azure Kubernetes Service (AKS)](#install-azure-policy-add-on-for-aks)
   - [Kubernetes met Azure Arc](#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)
   - [AKS-engine](#install-azure-policy-add-on-for-aks-engine)

   > [!NOTE]
   > Zie Troubleshoot [- Azure Policy Add-on voor veelvoorkomende problemen met de installatie.](../troubleshoot/general.md#add-on-for-kubernetes-installation-errors)

1. [Inzicht in Azure Policy taal voor Kubernetes](#policy-language)

1. [Een ingebouwde definitie toewijzen aan uw Kubernetes-cluster](#assign-a-built-in-policy-definition)

1. [Wachten op validatie](#policy-evaluation)

## <a name="limitations"></a>Beperkingen

De volgende algemene beperkingen gelden voor de Azure Policy-invoegsel voor Kubernetes-clusters:

- Azure Policy-invoegsel voor Kubernetes wordt ondersteund in Kubernetes versie **1.14** of hoger.
- Azure Policy-invoegsel voor Kubernetes kan alleen worden geïmplementeerd in Linux-knooppuntgroepen
- Alleen ingebouwde beleidsdefinities worden ondersteund
- Maximum aantal niet-compatibele records per beleid per cluster: **500**
- Maximum aantal niet-compatibele records per abonnement: **1 miljoen**
- Installaties van Gatekeeper buiten de Azure Policy-invoeginstallatie worden niet ondersteund. Verwijder alle onderdelen die zijn geïnstalleerd door een eerdere Gatekeeper-installatie voordat u de Azure Policy inschakelen.
- [Redenen voor niet-naleving](../how-to/determine-non-compliance.md#compliance-reasons) zijn niet beschikbaar voor de `Microsoft.Kubernetes.Data` 
   [resourceprovidermodus.](./definition-structure.md#resource-provider-modes) Gebruik [Onderdeeldetails.](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes)
- [Uitzonderingen worden](./exemption-structure.md) niet ondersteund voor [resourceprovidermodi.](./definition-structure.md#resource-provider-modes)

De volgende beperkingen gelden alleen voor de Azure Policy-invoeg-on voor AKS:

- [Beveiligingsbeleid voor AKS-pods](../../../aks/use-pod-security-policies.md) en Azure Policy-invoeg-invoeging voor AKS kunnen niet beide worden ingeschakeld. Zie Beveiligingsbeperking voor [AKS-pods voor meer informatie.](../../../aks/use-azure-policy.md)
- Naamruimten die automatisch worden uitgesloten door Azure Policy-invoegserver voor evaluatie: _kube-system,_ _gatekeeper-system_ en _aks-periscope_.

## <a name="recommendations"></a>Aanbevelingen

Hier volgen algemene aanbevelingen voor het gebruik van de Azure Policy-invoeg-on:

- Voor Azure Policy-invoeging zijn drie Gatekeeper-onderdelen vereist: 1 controlepod en 2 replica's van webhookpods. Deze onderdelen verbruiken meer resources naarmate het aantal Kubernetes-resources en beleidstoewijzingen in het cluster toeneemt. Hiervoor zijn controle- en afdwingingsbewerkingen vereist.

  - Voor minder dan 500 pods in één cluster met een maximum van 20 beperkingen: 2 vCCPUs en 350 MB geheugen per onderdeel.
  - Voor meer dan 500 pods in één cluster met een maximum van 40 beperkingen: 3 vCCPUs en 600 MB geheugen per onderdeel.

- Windows-pods [bieden geen ondersteuning voor beveiligingscontexten.](https://kubernetes.io/docs/concepts/security/pod-security-standards/#what-profiles-should-i-apply-to-my-windows-pods)
  Daarom kunnen sommige van de Azure Policy-definities, zoals het niet toepassen van hoofdbevoegdheden, niet worden geëscaleerd in Windows-pods en alleen van toepassing op Linux-pods.

De volgende aanbeveling is alleen van toepassing op AKS en de Azure Policy-invoeg-on:

- Gebruik een systeem-knooppuntgroep met `CriticalAddonsOnly` taint om Gatekeeper-pods te plannen. Zie Systeem-knooppuntgroepen [gebruiken voor meer informatie.](../../../aks/use-system-pools.md#system-and-user-node-pools)
- Beveilig uitgaand verkeer van uw AKS-clusters. Zie Control [egress traffic for cluster nodes (Het verkeer voor het verkeer naar het cluster controleren) voor meer informatie.](../../../aks/limit-egress-traffic.md)
- Als het cluster is ingeschakeld, Node Managed Identity (NMI)-pods de iptables van de knooppunten wijzigen om aanroepen naar het `aad-pod-identity` Azure Instance Metadata-eindpunt te onderscheppen. Deze configuratie betekent dat elke aanvraag bij het eindpunt voor metagegevens wordt onderschept door NMI, zelfs als de pod geen `aad-pod-identity` gebruikt. AzurePodIdentityException CRD kan worden geconfigureerd om te informeren dat aanvragen naar het eindpunt metagegevens afkomstig van een pod die overeenkomt met labels die zijn gedefinieerd in CRD moeten worden geproxied zonder verwerking `aad-pod-identity` in NMI. De systeempods met `kubernetes.azure.com/managedby: aks` een label in de _kube-system-naamruimte_ moeten worden uitgesloten in door de `aad-pod-identity` CRD AzurePodIdentityException te configureren. Zie [Aad-pod-identity](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)uitschakelen voor een specifieke pod of toepassing voor meer informatie.
  Als u een uitzondering wilt configureren, installeert [u de YAML mic-exception](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml).

## <a name="install-azure-policy-add-on-for-aks"></a>Een Azure Policy voor AKS installeren

Voordat u de Azure Policy of inschakelen van een van de servicefuncties, moet uw abonnement de **resourceproviders Microsoft.PolicyInsights** inschakelen.

1. Azure CLI versie 2.12.0 of hoger moet zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

1. Registreer de resourceproviders en preview-functies.

   - Azure Portal:

     Registreer de **Microsoft.PolicyInsights-resourceproviders.** Zie Resourceproviders en [-typen voor de stappen.](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)

   - Azure CLI:

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace Microsoft.PolicyInsights
     ```

1. Als er beperkte preview-beleidsdefinities zijn geïnstalleerd, verwijdert u de invoegversie met **de** knop Uitschakelen op uw AKS-cluster op de **pagina** Beleid.

1. Het AKS-cluster moet versie _1.14_ of hoger zijn. Gebruik het volgende script om de versie van uw AKS-cluster te valideren:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Installeer versie _2.12.0_ of hoger van de Azure CLI. Zie [De Azure CLI installeren](/cli/azure/install-azure-cli) voor meer informatie.

Nadat de bovenstaande vereiste stappen zijn voltooid, installeert u de Azure Policy-invoegversie in het AKS-cluster dat u wilt beheren.

- Azure Portal

  1. Start de AKS-service in Azure Portal door Alle services te **selecteren** en vervolgens **Kubernetes-services** te zoeken en te selecteren.

  1. Selecteer een van uw AKS-clusters.

  1. Selecteer **Beleidsregels** aan de linkerkant van de kubernetes-servicepagina.

  1. Selecteer op de hoofdpagina de **knop Invoeg toevoegen inschakelen.**

     <a name="migrate-from-v1"></a>
     > [!NOTE]
     > Als de knop Invoeg toevoegen uitschakelen is ingeschakeld en er een **v2-bericht** voor migratiewaarschuwingen wordt weergegeven, wordt de v1-invoeg-on geïnstalleerd en moet deze worden verwijderd voordat v2-beleidsdefinities worden toegewezen. De _afgeschafte_ v1-invoeg-on wordt vanaf 24 augustus automatisch vervangen door de v2-invoegvoeging.
     > 2020. Nieuwe v2-versies van de beleidsdefinities moeten vervolgens worden toegewezen. Volg deze stappen om nu een upgrade uit te voeren:
     >
     > 1. Controleer of in uw AKS-cluster de v1-invoegversie is geïnstalleerd door naar de pagina Beleid op uw AKS-cluster te gaan en controleer of 'Het huidige cluster gebruikmaakt van Azure Policy-invoegversie v1...'  Bericht.
     > 1. [Verwijder de invoeg-app](#remove-the-add-on-from-aks).
     > 1. Selecteer de **knop Invoeg toevoegen inschakelen** om de v2-versie van de invoegversie te installeren.
     > 1. [v2-versies van uw ingebouwde v1-beleidsdefinities toewijzen](#assign-a-built-in-policy-definition)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

Voer de volgende opdracht uit om te controleren of de invoeg-installatie is geslaagd en of de _pods azure-policy_ en _gatekeeper_ worden uitgevoerd:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

Controleer ten laatste of de meest recente invoegversie is geïnstalleerd door deze Azure CLI-opdracht uit te voeren, te vervangen door de naam van uw resourcegroep en door de naam van uw `<rg>` `<cluster-name>` AKS-cluster: `az aks show --query addonProfiles.azurepolicy -g <rg> -n <cluster-name>` . Het resultaat moet er ongeveer uitzien als in de volgende uitvoer en **config.version** moet `v2` zijn:

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

## <a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes-preview"></a><a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes"></a>Een Azure Policy installeren voor kubernetes Azure Arc kubernetes (preview)

Voordat u de Azure Policy installeert of een van de servicefuncties inschakelen, moet uw abonnement de resourceprovider **Microsoft.PolicyInsights** inschakelen en een roltoewijzing maken voor de clusterservice-principal.

1. Azure CLI versie 2.12.0 of hoger moet zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

1. Als u de resourceprovider wilt inschakelen, volgt u de stappen in [Resourceproviders en](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) -typen of gebruikt u de Azure CLI of Azure PowerShell opdracht:

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

1. Het Kubernetes-cluster moet versie _1.14_ of hoger zijn.

1. Installeer [Helm 3.](https://v3.helm.sh/docs/intro/install/)

1. Uw Kubernetes-cluster is ingeschakeld voor Azure Arc. Zie [Onboarding a Kubernetes cluster to Azure Arc (Onboarding van een Kubernetes-cluster voor meer Azure Arc.](../../../azure-arc/kubernetes/quickstart-connect-cluster.md)

1. Zorg ervoor dat de volledig gekwalificeerde Azure-resource-id van Azure Arc Kubernetes-cluster is ingeschakeld.

1. Open poorten voor de invoeg-on. De Azure Policy maakt gebruik van deze domeinen en poorten om beleidsdefinities en -toewijzingen op te halen en de naleving van het cluster weer te Azure Policy.

   |Domain |Poort |
   |---|---|
   |`gov-prod-policy-data.trafficmanager.net` |`443` |
   |`raw.githubusercontent.com` |`443` |
   |`login.windows.net` |`443` |
   |`dc.services.visualstudio.com` |`443` |

1. Wijs de roltoewijzing 'Policy Insights Data Writer (Preview)' toe aan het kubernetes-cluster Azure Arc kubernetes-cluster. Vervang door uw abonnements-id, door de resourcegroep Azure Arc Kubernetes-cluster ingeschakeld en door de naam van Azure Arc `<subscriptionId>` `<rg>` `<clusterName>` Kubernetes-cluster. Houd de geretourneerde waarden voor _appId,_ _password_ en _tenant bij_ voor de installatiestappen.

   - Azure CLI

     ```azurecli-interactive
     az ad sp create-for-rbac --role "Policy Insights Data Writer (Preview)" --scopes "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     $sp = New-AzADServicePrincipal -Role "Policy Insights Data Writer (Preview)" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"

     @{ appId=$sp.ApplicationId;password=[System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret));tenant=(Get-AzContext).Tenant.Id } | ConvertTo-Json
     ```

   Voorbeelduitvoer van de bovenstaande opdrachten:

   ```json
   {
       "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
       "password": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
       "tenant": "cccccccc-cccc-cccc-cccc-cccccccccccc"
   }
   ```

Nadat de bovenstaande vereiste stappen zijn voltooid, installeert u de Azure Policy-invoegsel in Azure Arc Kubernetes-cluster:

1. Voeg de Azure Policy-on-repo toe aan Helm:

   ```bash
   helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
   ```

1. Installeer de Azure Policy met behulp van Helm-grafiek:

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

   Zie de definitie van de Helm-grafiek voor [](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-arc-clusters) de invoeg-app op GitHub Azure Policy informatie over de installatie van de helm-invoeg-app.

Voer de volgende opdracht uit om te controleren of de installatie van de invoeg-on is geslaagd en of de pods _azure-policy_ en _gatekeeper_ worden uitgevoerd:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="install-azure-policy-add-on-for-aks-engine-preview"></a><a name="install-azure-policy-add-on-for-aks-engine"></a>Een Azure Policy installeren voor de AKS-engine (preview)

Voordat u de Azure Policy toevoegen of inschakelen van een van de servicefuncties, moet uw abonnement de resourceprovider **Microsoft.PolicyInsights** inschakelen en een roltoewijzing maken voor de clusterservice-principal.

1. Azure CLI versie 2.0.62 of hoger moet zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

1. Als u de resourceprovider wilt inschakelen, volgt u de stappen in [Resourceproviders en](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) -typen of gebruikt u de Azure CLI of Azure PowerShell opdracht:

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

1. Maak een roltoewijzing voor de clusterservice-principal.

   - Als u de app-id van de clusterservice-principal niet weet, zoek deze dan op met de volgende opdracht.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system

     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Wijs de roltoewijzing 'Policy Insights Data Writer (Preview)' toe aan de app-id van de clusterservice-principal (waarde _aadClientID_ uit de vorige stap) met Azure CLI. Vervang `<subscriptionId>` door uw abonnements-id en door de resourcegroep waarin het `<aks engine cluster resource group>` zelfbeheerde Kubernetes-cluster van de AKS-engine zich in zich heeft.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

Nadat de bovenstaande vereiste stappen zijn voltooid, installeert u de Azure Policy-invoeg-on. De installatie kan zijn tijdens het maken of bijwerken van een AKS-engine of als een onafhankelijke actie op een bestaand cluster.

- Installeren tijdens het maken of bijwerken

  Als u de Azure Policy wilt inschakelen tijdens het maken van een nieuw zelf beheerd cluster of als een update van een bestaand cluster, voegt u de eigenschapsclusterdefinitie [addons](https://github.com/Azure/aks-engine/tree/master/examples/addons/azure-policy) voor de AKS-engine toe.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true
  }]
  ```

  Zie de externe handleiding [AKS Engine-clusterdefinitie voor meer informatie over](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).

- Installeren in bestaand cluster met Helm-grafieken

  Gebruik de volgende stappen om het cluster voor te bereiden en de invoeg-on te installeren:

  1. Installeer [Helm 3](https://v3.helm.sh/docs/intro/install/).

  1. Voeg de Azure Policy-repo toe aan Helm.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Zie Helm-grafiek [- Snelstartgids voor meer informatie.](https://helm.sh/docs/using_helm/#quickstart-guide)

  1. Installeer de invoeg-app met een Helm-grafiek. Vervang `<subscriptionId>` door uw abonnements-id en door de resourcegroep waarin het `<aks engine cluster resource group>` zelfbeheerde Kubernetes-cluster van de AKS-engine zich in zich heeft.

     ```bash
     helm install azure-policy-addon azure-policy/azure-policy-addon-aks-engine --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Zie de definitie van de Helm-grafiek voor [](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-aks-engine) de invoeg-app op GitHub Azure Policy informatie over de installatie van de helm-invoeg-app.

     > [!NOTE]
     > Vanwege de relatie tussen Azure Policy-invoeggebruik en de resourcegroep-id, ondersteunt Azure Policy slechts één AKS Engine-cluster voor elke resourcegroep.

Voer de volgende opdracht uit om te controleren of de installatie van de invoeg-on is geslaagd en of de pods _azure-policy_ en _gatekeeper_ worden uitgevoerd:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="policy-language"></a>Beleidstaal

De Azure Policy taalstructuur voor het beheren van Kubernetes volgt die van bestaande beleidsdefinities. Met de [resourceprovidermodus](./definition-structure.md#resource-provider-modes) `Microsoft.Kubernetes.Data` van worden de [effectencontrole](./effects.md#audit) en [-weigeren](./effects.md#deny) gebruikt voor het beheren van uw Kubernetes-clusters. _Controle_ en _weigeren moeten_ details **geven** die specifiek zijn voor het werken [OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint) en Gatekeeper v3.

Als onderdeel van de eigenschappen _details.constraintTemplate_ en _details.constraint_ in de beleidsdefinitie, geeft Azure Policy de URI's van deze [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) door aan de invoegvoeging. Rego is de taal die door OPA en Gatekeeper wordt ondersteund om een aanvraag voor het Kubernetes-cluster te valideren. Door een bestaande standaard voor Kubernetes-beheer te ondersteunen, maakt Azure Policy het mogelijk om bestaande regels opnieuw te gebruiken en deze te koppelen aan Azure Policy voor een uniforme rapportage-ervaring voor cloudcomputing. Zie Wat [is Rego? voor meer informatie.](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego)

## <a name="assign-a-built-in-policy-definition"></a>Een ingebouwde beleidsdefinitie toewijzen

Als u een beleidsdefinitie wilt toewijzen aan uw Kubernetes-cluster, moet aan u de juiste toewijzingsbewerkingen voor op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) zijn toegewezen. De ingebouwde Azure-rollen **Inzender voor resourcebeleid** **en Eigenaar** hebben deze bewerkingen. Zie Azure [RBAC-machtigingen in](../overview.md#azure-rbac-permissions-in-azure-policy)Azure Policy.

Zoek de ingebouwde beleidsdefinities voor het beheren van uw cluster met behulp Azure Portal volgende stappen:

1. Start de Azure Policy-service in de Azure Portal. Selecteer **Alle services** in het linkerdeelvenster en zoek en selecteer vervolgens **Beleid.**

1. Selecteer definities in Azure Policy linkerdeelvenster van **de pagina**.

1. Gebruik in de vervolgkeuzelijst Categorie de optie **Alles selecteren om** het filter te verwijderen en selecteer vervolgens **Kubernetes.**

1. Selecteer de beleidsdefinitie en selecteer vervolgens de **knop** Toewijzen.

1. Stel Bereik **in** op de beheergroep, het abonnement of de resourcegroep van het Kubernetes-cluster waarop de beleidstoewijzing van toepassing is.

   > [!NOTE]
   > Bij het toewijzen van Azure Policy kubernetes-definitie moet **het bereik** de clusterresource bevatten. Voor een AKS Engine-cluster moet **het bereik** de resourcegroep van het cluster zijn.

1. Geef de beleidstoewijzing **een naam** **en beschrijving die** u kunt gebruiken om deze gemakkelijk te identificeren.

1. Stel [beleidshandhaving](./assignment-structure.md#enforcement-mode) in op een van de onderstaande waarden.

   - **Ingeschakeld:** het beleid op het cluster afdwingen. Kubernetes-toegangsaanvragen met schendingen worden geweigerd.

   - **Uitgeschakeld:** dwing het beleid niet af op het cluster. Kubernetes-toegangsaanvragen met schendingen worden niet geweigerd. De resultaten van de nalevingsevaluatie zijn nog steeds beschikbaar. Bij het uitrollen van nieuwe beleidsdefinities voor het uitvoeren van clusters _is_ de optie Uitgeschakeld handig voor het testen van de beleidsdefinitie, omdat toegangsaanvragen met schendingen niet worden geweigerd.

1. Selecteer **Next**.

1. Parameterwaarden **instellen**

   - Als u Kubernetes-naamruimten wilt uitsluiten van beleidsevaluatie, geeft u de lijst met naamruimten op in de parameter **Naamruimteuitsluitingen.** Het is raadzaam om het volgende uit te sluiten: _kube-system,_ _gatekeeper-system_ en _azure-arc._

1. Selecteer **Controleren + maken**.

U kunt ook de quickstart [Een beleid toewijzen - Portal](../assign-policy-portal.md) gebruiken om een Kubernetes-beleid te zoeken en toe te wijzen. Zoek naar een Kubernetes-beleidsdefinitie in plaats van het voorbeeld 'VM's controleren'.

> [!IMPORTANT]
> Ingebouwde beleidsdefinities zijn beschikbaar voor Kubernetes-clusters in de **categorie Kubernetes.** Zie Kubernetes-voorbeelden voor een lijst met ingebouwde [beleidsdefinities.](../samples/built-in-policies.md#kubernetes)

## <a name="policy-evaluation"></a>Beleidsevaluatie

Met de invoeg-invoeging wordt Azure Policy elke 15 minuten op wijzigingen in beleidstoewijzingen.
Tijdens deze vernieuwingscyclus controleert de invoeg-on op wijzigingen. Deze wijzigingen activeren het maken, bijwerken of verwijderen van de sjablonen en beperkingen voor beperkingen.

Als een naamruimte in een Kubernetes-cluster een van de volgende labels heeft, worden de toegangsaanvragen met schendingen niet geweigerd. De resultaten van de nalevingsevaluatie zijn nog steeds beschikbaar.

- `control-plane`
- `admission.policy.azure.com/ignore`

> [!NOTE]
> Hoewel een clusterbeheerder mogelijk machtigingen heeft om beperkingssjablonen en beperkingen voor resources te maken en bij te werken die door de Azure Policy-invoegversie worden geïnstalleerd, zijn dit geen ondersteunde scenario's omdat handmatige updates worden overschreven. Gatekeeper blijft beleidsregels evalueren die bestonden vóór de installatie van de invoeg-on en het toewijzen Azure Policy beleidsdefinities.

Om de 15 minuten vraagt de invoeg-on om een volledige scan van het cluster. Na het verzamelen van details van de volledige scan en eventuele realtime-evaluaties door Gatekeeper van geprobeerde wijzigingen in het cluster, rapporteert de invoeg-on de resultaten terug naar Azure Policy voor opname [in](../how-to/get-compliance-data.md) nalevingsdetails zoals elke Azure Policy-toewijzing. Alleen resultaten voor actieve beleidstoewijzingen worden geretourneerd tijdens de controlecyclus. Controleresultaten kunnen ook worden gezien als [schendingen](https://github.com/open-policy-agent/gatekeeper#audit) die worden vermeld in het statusveld van de mislukte beperking. Zie Onderdeeldetails voor resourceprovidermodi voor meer informatie over _niet-compatibele_ [resources.](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes)

> [!NOTE]
> Elk nalevingsrapport in Azure Policy voor uw Kubernetes-clusters bevat alle schendingen in de afgelopen 45 minuten. De tijdstempel geeft aan wanneer een schending is opgetreden.

Enkele andere overwegingen:

- Als het clusterabonnement is geregistreerd bij Azure Security Center, Azure Security Center Kubernetes-beleid automatisch toegepast op het cluster.

- Wanneer een beleid voor weigeren wordt toegepast op een cluster met bestaande Kubernetes-resources, blijft elke bestaande resource die niet compatibel is met het nieuwe beleid, worden uitgevoerd. Wanneer de niet-compatibele resource opnieuw wordt gepland op een ander knooppunt, blokkeert de Gatekeeper het maken van de resource.

- Wanneer een cluster een beleid voor weigeren heeft dat resources valideert, ziet de gebruiker geen afwijzingsbericht bij het maken van een implementatie. Denk bijvoorbeeld aan een Kubernetes-implementatie die replicasets en pods bevat. Wanneer een gebruiker `kubectl describe deployment $MY_DEPLOYMENT` uitvoert, retourneert deze geen afwijzingsbericht als onderdeel van gebeurtenissen. `kubectl describe replicasets.apps $MY_DEPLOYMENT`Retourneert echter de gebeurtenissen die zijn gekoppeld aan afwijzing.

## <a name="logging"></a>Logboekregistratie

Als Kubernetes-controller/-container bewaren zowel _de pods azure-policy_ als _gatekeeper_ logboeken in het Kubernetes-cluster. De logboeken kunnen worden weergegeven op **de pagina Inzichten** van het Kubernetes-cluster. Zie Prestaties van uw [Kubernetes-cluster](../../../azure-monitor/containers/container-insights-analyze.md)bewaken met Azure Monitor voor containers voor meer informatie.

Als u de invoeglogboeken wilt weergeven, gebruikt u `kubectl` :

```bash
# Get the azure-policy pod name installed in kube-system namespace
kubectl logs <azure-policy pod name> -n kube-system

# Get the gatekeeper pod name installed in gatekeeper-system namespace
kubectl logs <gatekeeper pod name> -n gatekeeper-system
```

Zie Voor meer informatie [Gatekeeper debuggen](https://github.com/open-policy-agent/gatekeeper#debugging) in de Gatekeeper-documentatie.

## <a name="troubleshooting-the-add-on"></a>Problemen met de invoegoplossing oplossen

Zie de sectie Kubernetes van het artikel Azure Policy problemen oplossen voor meer informatie over het oplossen van problemen met de invoegoplossing voor [Kubernetes.](../troubleshoot/general.md#add-on-for-kubernetes-general-errors)

## <a name="remove-the-add-on"></a>De invoeg-app verwijderen

### <a name="remove-the-add-on-from-aks"></a>De invoeg-app uit AKS verwijderen

Als u de Azure Policy wilt verwijderen uit uw AKS-cluster, gebruikt u de Azure Portal of Azure CLI:

- Azure Portal

  1. Start de AKS-service in Azure Portal door Alle services te **selecteren** en vervolgens **Kubernetes-services** te zoeken en te selecteren.

  1. Selecteer uw AKS-cluster waar u de invoeg-Azure Policy wilt uitschakelen.

  1. Selecteer **Beleidsregels** aan de linkerkant van de kubernetes-servicepagina.

  1. Selecteer op de hoofdpagina de **knop Invoeg toevoegen uitschakelen.**

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="remove-the-add-on-from-azure-arc-enabled-kubernetes"></a>De invoeg-app verwijderen Azure Arc Kubernetes

Als u de Azure Policy-invoegsel en Gatekeeper wilt verwijderen uit uw kubernetes-cluster Azure Arc kubernetes-cluster, moet u de volgende Helm-opdracht uitvoeren:

```bash
helm uninstall azure-policy-addon
```

### <a name="remove-the-add-on-from-aks-engine"></a>De invoeg-app verwijderen uit de AKS-engine

Als u de Azure Policy Add-on en Gatekeeper wilt verwijderen uit uw AKS Engine-cluster, gebruikt u de methode die is afgestemd op de installatie van de invoeggebruiker:

- Indien geïnstalleerd door de eigenschap **addons** in te stellen in de clusterdefinitie voor de AKS-engine:

  De clusterdefinitie opnieuw implementeren naar de AKS-engine nadat u de **eigenschap addons** voor _azure-policy_ hebt veranderd in false:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

  Zie [AKS Engine - Disable Azure Policy Add-on voor meer informatie.](https://github.com/Azure/aks-engine/blob/master/examples/addons/azure-policy/README.md#disable-azure-policy-add-on)

- Als u helm-grafieken hebt geïnstalleerd, moet u de volgende Helm-opdracht uitvoeren:

  ```bash
  helm uninstall azure-policy-addon
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Diagnostische gegevens verzameld door Azure Policy-invoeg-on

De Azure Policy-invoegsel voor Kubernetes verzamelt beperkte diagnostische gegevens van clusters. Deze diagnostische gegevens zijn essentiële technische gegevens met betrekking tot software en prestaties. Deze wordt op de volgende manieren gebruikt:

- De Azure Policy up-to-date houden
- Houd Azure Policy-on veilig, betrouwbaar en presterend
- De Azure Policy verbeteren : via de geaggregeerde analyse van het gebruik van de invoeg-on

De informatie die door de invoeg-invoegvoeging wordt verzameld, zijn geen persoonlijke gegevens. De volgende gegevens worden momenteel verzameld:

- Azure Policy versie van de invoegversie van de agent
- Clustertype
- Clusterregio
- Clusterresourcegroep
- Clusterresource-id
- Clusterabonnements-id
- Cluster os (voorbeeld: Linux)
- Cluster stad (voorbeeld: Seattle)
- Clustertoestand of -provincie (voorbeeld: Washington)
- Clusterland of -regio (voorbeeld: Verenigde Staten)
- Uitzonderingen/fouten die optreden tijdens de Azure Policy tijdens de installatie van de agent bij de beleidsevaluatie
- Aantal Gatekeeper-beleidsdefinities dat niet is geïnstalleerd door Azure Policy-invoeg-on

## <a name="next-steps"></a>Volgende stappen

- Bekijk voorbeelden op [Azure Policy voorbeelden.](../samples/index.md)
- Controleer de [structuur van beleidsdefinitie.](definition-structure.md)
- Lees [Informatie over de effecten van het beleid](effects.md).
- Begrijpen hoe u [programmatisch beleid kunt maken.](../how-to/programmatically-create.md)
- Meer informatie over het [op halen van nalevingsgegevens.](../how-to/get-compliance-data.md)
- Ontdek hoe u [niet-compatibele resources kunt herstellen](../how-to/remediate-resources.md).
- Bekijk wat een beheergroep is met [Uw resources organiseren met Azure-beheergroepen.](../../management-groups/overview.md)

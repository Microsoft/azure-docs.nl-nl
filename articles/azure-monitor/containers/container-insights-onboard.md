---
title: Container Insights-| Microsoft Docs
description: In dit artikel wordt beschreven hoe u Container Insights kunt inschakelen en configureren, zodat u begrijpt hoe uw container presteert en welke prestatieproblemen zijn geïdentificeerd.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: e0544232f40e93cce0705fff6814d29697a96218
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782113"
---
# <a name="enable-container-insights"></a>Container insights inschakelen

In dit artikel vindt u een overzicht van de opties die beschikbaar zijn voor het instellen van Container Insights om de prestaties te bewaken van workloads die zijn geïmplementeerd in Kubernetes-omgevingen en worden gehost op:

- [Azure Kubernetes Service (AKS)](../../aks/index.yml)  
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) versies 3.x en 4.x  
- [Red Hat OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) versie 4.x  
- Een [Kubernetes-cluster met Arc](../../azure-arc/kubernetes/overview.md)

U kunt ook de prestaties bewaken van workloads die worden geïmplementeerd op zelf-beheerde Kubernetes-clusters die worden gehost op:
- Azure, met behulp van de [AKS-engine](https://github.com/Azure/aks-engine)
- [Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) of on-premises, met behulp van de AKS-engine.

U kunt Container Insights inschakelen voor een nieuwe implementatie of voor een of meer bestaande implementaties van Kubernetes met behulp van een van de volgende ondersteunde methoden:

- Azure Portal
- Azure PowerShell
- De Azure CLI
- [Terraform en AKS](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u aan de volgende vereisten voldoet:

> [!IMPORTANT]
> Met Log Analytics in een container geplaatste Linux-agent (replicasetpod) worden API-aanroepen naar alle Windows-knooppunten op kubelet Secure Port (10250) binnen het cluster gemaakt om metrische gegevens met betrekking tot knooppunt- en containerprestaties te verzamelen. Kubelet secure port (:10250) should be opened in the cluster's virtual network for both inbound and outbound for Windows Node and container performance related metrics collection to work.
>
> Als u een Kubernetes-cluster met Windows-knooppunten hebt, controleert en configureert u de netwerkbeveiligingsgroep en het netwerkbeleid om ervoor te zorgen dat de beveiligde Kubelet-poort (:10250) is geopend voor zowel binnenkomende als uitgaande poort in het virtuele netwerk van het cluster.


- U hebt een Log Analytics-werkruimte.

   Container Insights ondersteunt een Log Analytics-werkruimte in de regio's die worden vermeld in [Beschikbare producten per regio.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)

   U kunt een werkruimte maken wanneer u bewaking inschakelen voor uw nieuwe AKS-cluster of u kunt de onboarding-ervaring een standaardwerkruimte laten maken in de standaardresourcegroep van het AKS-clusterabonnement. 
   
   Als u ervoor kiest om de werkruimte zelf te maken, kunt u deze maken via: 
   - [Azure Resource Manager](../logs/resource-manager-workspace.md)
   - [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)
   - [Azure Portal](../logs/quick-create-workspace.md) 
   
   Zie Regiotoewijzing voor Container insights voor een lijst met de ondersteunde toewijzingsparen die moeten worden gebruikt voor de [standaardwerkruimte.](container-insights-region-mapping.md)

- U bent lid van de log *analytics-inzendergroep* voor het inschakelen van containerbewaking. Zie Werkruimten beheren voor meer informatie over het beheren van toegang tot een Log [Analytics-werkruimte.](../logs/manage-access.md)

- U bent lid van de groep [ *Eigenaar van* de](../../role-based-access-control/built-in-roles.md#owner) AKS-clusterresource.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

- Als u de bewakingsgegevens wilt weergeven, moet u de [*rol Van Log Analytics-lezer*](../logs/manage-access.md#manage-access-using-azure-permissions) hebben in de Log Analytics-werkruimte, geconfigureerd met Container Insights.

- Prometheus-metrische gegevens worden niet standaard verzameld. Voordat u [de agent](container-insights-prometheus-integration.md) configureert voor het verzamelen van de metrische gegevens, is het belangrijk dat u de [Prometheus-documentatie](https://prometheus.io/) bekijkt om te begrijpen welke gegevens kunnen worden verzameld en welke methoden worden ondersteund.
- Een AKS-cluster kan worden gekoppeld aan een Log Analytics-werkruimte in een ander Azure-abonnement in dezelfde Azure AD-tenant. Dit kan momenteel niet worden gedaan met Azure Portal, maar kan worden gedaan met Azure CLI of Resource Manager sjabloon.

## <a name="supported-configurations"></a>Ondersteunde configuraties

Container Insights ondersteunt officieel de volgende configuraties:

- Omgevingen: Azure Red Hat OpenShift, Kubernetes on-premises en de AKS-engine in Azure en Azure Stack. Zie de [AKS-engine op Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)voor meer Azure Stack.
- De versies van Kubernetes en ondersteuningsbeleid zijn hetzelfde als de versies die worden ondersteund [in Azure Kubernetes Service (AKS).](../../aks/supported-kubernetes-versions.md) 

## <a name="network-firewall-requirements"></a>Vereisten voor netwerkfirewalls

De volgende tabel bevat de proxy- en firewallconfiguratiegegevens die nodig zijn om de containeragent te laten communiceren met Container Insights. Al het netwerkverkeer van de agent is uitgaand naar Azure Monitor.

|Agentresource|Poort |
|--------------|------|
| `*.ods.opinsights.azure.com` | 443 |
| `*.oms.opinsights.azure.com` | 443 |
| `dc.services.visualstudio.com` | 443 |
| `*.monitoring.azure.com` | 443 |
| `login.microsoftonline.com` | 443 |

De volgende tabel bevat de proxy- en firewallconfiguratiegegevens voor Azure China 21Vianet:

|Agentresource|Poort |Beschrijving | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.cn` | 443 | Gegevensopname |
| `*.oms.opinsights.azure.cn` | 443 | OMS-onboarding |
| `dc.services.visualstudio.com` | 443 | Voor agent-telemetrie die gebruikmaakt van Azure Public Cloud Application Insights |

De volgende tabel bevat de proxy- en firewallconfiguratiegegevens voor Azure US Government:

|Agentresource|Poort |Beschrijving | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.us` | 443 | Gegevensopname |
| `*.oms.opinsights.azure.us` | 443 | OMS-onboarding |
| `dc.services.visualstudio.com` | 443 | Voor agent-telemetrie die gebruikmaakt van Azure Public Cloud Application Insights |

## <a name="components"></a>Onderdelen

De mogelijkheid om de prestaties te bewaken is afhankelijk van een in een container geplaatste Log Analytics-agent voor Linux die speciaal is ontwikkeld voor Container Insights. Deze gespecialiseerde agent verzamelt prestatie- en gebeurtenisgegevens van alle knooppunten in het cluster en de agent wordt tijdens de implementatie automatisch geïmplementeerd en geregistreerd bij de opgegeven Log Analytics-werkruimte. 

De versie van de agent is microsoft/oms:ciprod04202018 of hoger en wordt vertegenwoordigd door een datum in de volgende indeling: *mmddyyyy*.

>[!NOTE]
>Met de algemene beschikbaarheid van Windows Server-ondersteuning voor AKS, heeft een AKS-cluster met Windows Server-knooppunten een preview-agent geïnstalleerd als een daemonset-pod op elk afzonderlijk Windows-serverknooppunt om logboeken te verzamelen en door te geven aan Log Analytics. Voor metrische prestatiegegevens verzamelt en doorsturen een Linux-knooppunt dat automatisch in het cluster wordt geïmplementeerd als onderdeel van de standaardimplementatie, de gegevens namens alle Windows-knooppunten in het cluster naar Azure Monitor.

Wanneer er een nieuwe versie van de agent wordt uitgebracht, wordt deze automatisch bijgewerkt op uw beheerde Kubernetes-clusters die worden gehost op Azure Kubernetes Service (AKS). Zie aankondigingen van de [agentre](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)release om bij te houden welke versies worden uitgebracht.

> [!NOTE]
> Als u al een AKS-cluster hebt geïmplementeerd, hebt u bewaking ingeschakeld met behulp van de Azure CLI of een opgegeven Azure Resource Manager-sjabloon, zoals verderop in dit artikel wordt beschreven. U kunt de agent niet upgraden, verwijderen, opnieuw implementeren `kubectl` of implementeren.
>
> De sjabloon moet worden geïmplementeerd in dezelfde resourcegroep als het cluster.

Als u Container Insights wilt inschakelen, gebruikt u een van de methoden die in de volgende tabel worden beschreven:

| Implementatietoestand | Methode | Beschrijving |
|------------------|--------|-------------|
| Nieuw Kubernetes-cluster | [Een AKS-cluster maken met behulp van de Azure CLI](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| U kunt bewaking inschakelen voor een nieuw AKS-cluster dat u maakt met behulp van de Azure CLI. |
| | [Een AKS-cluster maken met behulp van Terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| U kunt bewaking inschakelen voor een nieuw AKS-cluster dat u maakt met behulp van het opensource-hulpprogramma Terraform. |
| | [Een OpenShift-cluster maken met behulp van een Azure Resource Manager sjabloon](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | U kunt bewaking inschakelen voor een nieuw OpenShift-cluster dat u maakt met behulp van een vooraf geconfigureerde Azure Resource Manager sjabloon. |
| | [Een OpenShift-cluster maken met behulp van de Azure CLI](/cli/azure/openshift#az_openshift_create) | U kunt bewaking inschakelen wanneer u een nieuw OpenShift-cluster implementeert met behulp van de Azure CLI. |
| Bestaand Kubernetes-cluster | [Bewaking van een AKS-cluster inschakelen met behulp van de Azure CLI](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | U kunt bewaking inschakelen voor een AKS-cluster dat al is geïmplementeerd met behulp van de Azure CLI. |
| |[Inschakelen voor AKS-cluster met behulp van Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | U kunt bewaking inschakelen voor een AKS-cluster dat al is geïmplementeerd met behulp van het opensource-hulpprogramma Terraform. |
| | [Inschakelen voor AKS-cluster vanuit Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| U kunt bewaking inschakelen voor een of meer AKS-clusters die al zijn geïmplementeerd vanaf de pagina met meerdere clusters in Azure Monitor. |
| | [Inschakelen vanuit AKS-cluster](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| U kunt bewaking rechtstreeks vanuit een AKS-cluster inschakelen in Azure Portal. |
| | [Inschakelen voor AKS-cluster met behulp van een Azure Resource Manager sjabloon](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| U kunt bewaking voor een AKS-cluster inschakelen met behulp van een vooraf geconfigureerde Azure Resource Manager sjabloon. |
| | [Inschakelen voor hybride Kubernetes-cluster](container-insights-hybrid-setup.md) | U kunt bewaking inschakelen voor de AKS-engine die wordt gehost op Azure Stack of voor een Kubernetes-cluster dat on-premises wordt gehost. |
| | [Inschakelen voor Kubernetes-cluster](container-insights-enable-arc-enabled-clusters.md)met Arc. | U kunt bewaking inschakelen voor uw Kubernetes-clusters die buiten Azure worden gehost en zijn ingeschakeld met Azure Arc. |
| | [OpenShift-cluster inschakelen met behulp van een Azure Resource Manager sjabloon](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | U kunt bewaking voor een bestaand OpenShift-cluster inschakelen met behulp van een vooraf geconfigureerde Azure Resource Manager sjabloon. |
| | [OpenShift-cluster inschakelen vanuit Azure Monitor](container-insights-azure-redhat-setup.md#from-the-azure-portal) | U kunt bewaking inschakelen voor een of meer OpenShift-clusters die al zijn geïmplementeerd vanaf de pagina met meerdere clusters in Azure Monitor. |

## <a name="next-steps"></a>Volgende stappen

Nu u bewaking hebt ingeschakeld, kunt u beginnen met het analyseren van de prestaties van uw Kubernetes-clusters die worden gehost op Azure Kubernetes Service (AKS), Azure Stack of een andere omgeving. Zie Prestaties van [Kubernetes-clusters](container-insights-analyze.md)weergeven voor meer informatie over het gebruik van Container Insights.

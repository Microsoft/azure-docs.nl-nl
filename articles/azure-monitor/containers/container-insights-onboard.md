---
title: Container Insights inschakelen | Microsoft Docs
description: In dit artikel wordt beschreven hoe u container Insights inschakelt en configureert, zodat u kunt begrijpen hoe uw container presteert en welke prestatie problemen zijn geïdentificeerd.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 58797221fa3380e4f7533a710e2f8dc658cb676c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708353"
---
# <a name="enable-container-insights"></a>Container Insights inschakelen

Dit artikel bevat een overzicht van de opties die beschikbaar zijn voor het instellen van container Insights voor het bewaken van de prestaties van werk belastingen die zijn geïmplementeerd in Kubernetes omgevingen en die worden gehost op:

- [Azure Kubernetes Service (AKS)](../../aks/index.yml)  
- [Azure Red Hat open Shift](../../openshift/intro-openshift.md) versie 3. x en 4. x  
- [Red Hat open Shift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) versie 4. x  
- Een [Kubernetes-cluster met Arc-functionaliteit](../../azure-arc/kubernetes/overview.md)

U kunt ook de prestaties bewaken van werk belastingen die zijn geïmplementeerd op zelf beheerde Kubernetes-clusters die worden gehost op:
- Azure, met behulp van de [AKS-engine](https://github.com/Azure/aks-engine)
- [Azure stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) of on-premises, met behulp van de AKS-engine.

U kunt container Insights inschakelen voor een nieuwe implementatie of voor een of meer bestaande implementaties van Kubernetes door gebruik te maken van een van de volgende ondersteunde methoden:

- Azure Portal
- Azure PowerShell
- De Azure CLI
- [Terraform en AKS](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u aan de volgende vereisten voldoet:

> [!IMPORTANT]
> Log Analytics in containers geplaatste Linux-agent (POD) maakt API-aanroepen naar alle Windows-knoop punten op Kubelet beveiligde poort (10250) in het cluster voor het verzamelen van metrische gegevens over het knoop punt en de container prestaties. Kubelet beveiligde poort (: 10250) moet worden geopend in het virtuele netwerk van het cluster voor zowel binnenkomend als uitgaand verkeer voor Windows-knoop punten en de verzameling metrische prestatie gegevens over de container.
>
> Als u een Kubernetes-cluster met Windows-knoop punten hebt, kunt u de netwerk beveiligings groep en het netwerk beleid controleren en configureren om ervoor te zorgen dat de Kubelet beveiligde poort (: 10250) wordt geopend voor zowel binnenkomend als uitgaand verkeer in het virtuele netwerk van het cluster.


- U hebt een Log Analytics-werk ruimte.

   Container Insights biedt ondersteuning voor een Log Analytics-werk ruimte in de regio's die worden vermeld in [beschik bare producten per regio](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor).

   U kunt een werk ruimte maken wanneer u controle inschakelt voor uw nieuwe AKS-cluster, of u kunt de voorbereidings ervaring laten maken van een standaardwerk ruimte in de standaard resource groep van het AKS-cluster abonnement. 
   
   Als u ervoor kiest om de werk ruimte zelf te maken, kunt u deze maken via: 
   - [Azure Resource Manager](../logs/resource-manager-workspace.md)
   - [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)
   - [Azure Portal](../logs/quick-create-workspace.md) 
   
   Zie [regio toewijzing voor container Insights](container-insights-region-mapping.md)voor een lijst met de ondersteunde toewijzings paren die voor de standaardwerk ruimte moeten worden gebruikt.

- U bent lid van de groep *log Analytics Inzender* voor het inschakelen van container bewaking. Zie [werk ruimten beheren](../logs/manage-access.md)voor meer informatie over het controleren van de toegang tot een log Analytics-werk ruimte.

- U bent lid van de groep [ *eigenaar*](../../role-based-access-control/built-in-roles.md#owner) op de AKS-cluster bron.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

- Als u de bewakings gegevens wilt bekijken, moet u de rol van [*log Analytics lezer*](../logs/manage-access.md#manage-access-using-azure-permissions) hebben in de log Analytics-werk ruimte, geconfigureerd met container Insights.

- Prometheus-metrische gegevens worden niet standaard verzameld. Voordat u [de agent configureert](container-insights-prometheus-integration.md) voor het verzamelen van de metrische gegevens, is het belang rijk om de [Prometheus-documentatie](https://prometheus.io/) te controleren om te begrijpen welke informatie kan worden verzameld en welke methoden worden ondersteund.

## <a name="supported-configurations"></a>Ondersteunde configuraties

Container Insights officieel ondersteunt de volgende configuraties:

- Omgevingen: Azure Red Hat open Shift, Kubernetes on-premises en de AKS-engine op Azure en Azure Stack. Zie [de AKS-engine op Azure stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)voor meer informatie.
- De versies van Kubernetes en het ondersteunings beleid zijn hetzelfde als die welke worden [ondersteund in de Azure Kubernetes-service (AKS)](../../aks/supported-kubernetes-versions.md). 

## <a name="network-firewall-requirements"></a>Netwerk firewall vereisten

De volgende tabel geeft een lijst van de proxy-en firewall configuratie-informatie die is vereist voor de container agent om te communiceren met container Insights. Al het netwerk verkeer van de agent is uitgaand naar Azure Monitor.

|Agentresource|Poort |
|--------------|------|
| `*.ods.opinsights.azure.com` | 443 |
| `*.oms.opinsights.azure.com` | 443 |
| `dc.services.visualstudio.com` | 443 |
| `*.monitoring.azure.com` | 443 |
| `login.microsoftonline.com` | 443 |

De volgende tabel geeft een overzicht van de proxy-en firewall configuratie-informatie voor Azure China 21Vianet:

|Agentresource|Poort |Beschrijving | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.cn` | 443 | Gegevensopname |
| `*.oms.opinsights.azure.cn` | 443 | OMS-onboarding |
| `dc.services.visualstudio.com` | 443 | Voor telemetrie van agents die gebruikmaken van open bare Azure-Cloud Application Insights |

De volgende tabel geeft een overzicht van de proxy-en firewall configuratie-informatie voor de Amerikaanse overheid van Azure:

|Agentresource|Poort |Beschrijving | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.us` | 443 | Gegevensopname |
| `*.oms.opinsights.azure.us` | 443 | OMS-onboarding |
| `dc.services.visualstudio.com` | 443 | Voor telemetrie van agents die gebruikmaken van open bare Azure-Cloud Application Insights |

## <a name="components"></a>Onderdelen

De mogelijkheid om de prestaties te bewaken is afhankelijk van een container Log Analytics-agent voor Linux die specifiek is ontwikkeld voor container Insights. Deze gespecialiseerde agent verzamelt prestatie-en gebeurtenis gegevens van alle knoop punten in het cluster en de agent wordt automatisch geïmplementeerd en geregistreerd bij de opgegeven Log Analytics-werk ruimte tijdens de implementatie. 

De agent versie is micro soft/OMS: ciprod04202018 of hoger en wordt weer gegeven met een datum in de volgende notatie: *mmddyyyy*.

>[!NOTE]
>Met de algemene Beschik baarheid van Windows Server-ondersteuning voor AKS, heeft een AKS-cluster met Windows Server-knoop punten een preview-agent geïnstalleerd als een daemonset pod op elk afzonderlijk Windows Server-knoop punt om logboeken te verzamelen en door te sturen naar Log Analytics. Voor metrische gegevens over prestaties wordt een Linux-knoop punt dat automatisch in het cluster wordt geïmplementeerd als onderdeel van de standaard implementatie, de gegevens verzameld en doorgestuurd naar Azure Monitor voor alle Windows-knoop punten in het cluster.

Wanneer een nieuwe versie van de agent wordt uitgebracht, wordt deze automatisch geüpgraded op uw beheerde Kubernetes-clusters die worden gehost op Azure Kubernetes service (AKS). Zie [Release aankondigingen](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)van de agent als u wilt bijhouden welke versies er worden uitgebracht.

> [!NOTE]
> Als u al een AKS-cluster hebt geïmplementeerd, hebt u bewaking ingeschakeld met behulp van de Azure CLI of een gegeven Azure Resource Manager sjabloon, zoals verderop in dit artikel wordt uitgelegd. U kunt niet gebruiken `kubectl` om de agent bij te werken, te verwijderen, opnieuw te implementeren of te implementeren.
>
> De sjabloon moet worden geïmplementeerd in dezelfde resource groep als het cluster.

Als u container Insights wilt inschakelen, gebruikt u een van de methoden die worden beschreven in de volgende tabel:

| Implementatie status | Methode | Beschrijving |
|------------------|--------|-------------|
| Nieuw Kubernetes-cluster | [Een AKS-cluster maken met behulp van de Azure CLI](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| U kunt bewaking inschakelen voor een nieuw AKS-cluster dat u maakt met behulp van de Azure CLI. |
| | [Een AKS-cluster maken met behulp van terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| U kunt bewaking inschakelen voor een nieuw AKS-cluster dat u maakt met behulp van de open source-terraform. |
| | [Een open Shift-cluster maken met behulp van een Azure Resource Manager sjabloon](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | U kunt de bewaking inschakelen voor een nieuw open Shift-cluster dat u maakt met behulp van een vooraf geconfigureerde Azure Resource Manager sjabloon. |
| | [Een open Shift-cluster maken met behulp van de Azure CLI](/cli/azure/openshift#az-openshift-create) | U kunt bewaking inschakelen wanneer u een nieuw open Shift-cluster implementeert met behulp van de Azure CLI. |
| Bestaand Kubernetes-cluster | [Bewaking van een AKS-cluster inschakelen met behulp van de Azure CLI](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | U kunt bewaking inschakelen voor een AKS-cluster dat al is geïmplementeerd met behulp van de Azure CLI. |
| |[Inschakelen voor AKS-cluster met behulp van terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | U kunt bewaking inschakelen voor een AKS-cluster dat al is geïmplementeerd met behulp van de open source-terraform. |
| | [Inschakelen voor AKS-cluster van Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| U kunt bewaking inschakelen voor een of meer AKS-clusters die al zijn geïmplementeerd op de pagina met meerdere clusters in Azure Monitor. |
| | [Inschakelen vanuit AKS-cluster](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| U kunt de bewaking rechtstreeks vanuit een AKS-cluster inschakelen in de Azure Portal. |
| | [Inschakelen voor AKS-cluster met behulp van een Azure Resource Manager sjabloon](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| U kunt bewaking inschakelen voor een AKS-cluster met behulp van een vooraf geconfigureerde Azure Resource Manager sjabloon. |
| | [Inschakelen voor hybride Kubernetes-cluster](container-insights-hybrid-setup.md) | U kunt bewaking inschakelen voor de AKS-engine die wordt gehost op Azure Stack of voor een Kubernetes-cluster dat on-premises wordt gehost. |
| | [Inschakelen voor Kubernetes-cluster met Arc-functionaliteit](container-insights-enable-arc-enabled-clusters.md). | U kunt bewaking inschakelen voor uw Kubernetes-clusters die buiten Azure worden gehost en zijn ingeschakeld met Azure Arc. |
| | [Inschakelen voor open Shift-cluster met behulp van een Azure Resource Manager sjabloon](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | U kunt controle inschakelen voor een bestaand openstaand cluster met behulp van een vooraf geconfigureerde Azure Resource Manager sjabloon. |
| | [Inschakelen voor open Shift-cluster van Azure Monitor](container-insights-azure-redhat-setup.md#from-the-azure-portal) | U kunt bewaking inschakelen voor een of meer openstaande clusters die al zijn geïmplementeerd vanaf de pagina multi cluster in Azure Monitor. |

## <a name="next-steps"></a>Volgende stappen

Nu u bewaking hebt ingeschakeld, kunt u beginnen met het analyseren van de prestaties van uw Kubernetes-clusters die worden gehost op Azure Kubernetes service (AKS), Azure Stack of een andere omgeving. Zie [Kubernetes-cluster prestaties weer geven](container-insights-analyze.md)voor meer informatie over het gebruik van container Insights.
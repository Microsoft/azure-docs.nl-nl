---
title: Een privé-Azure Kubernetes Service maken
description: Meer informatie over het maken van een AKS Azure Kubernetes Service cluster (Private Azure Kubernetes Service)
services: container-service
ms.topic: article
ms.date: 3/31/2021
ms.openlocfilehash: 339bb41aed5ead3d7ee7d1217bfbc771cf068832
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719111"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Een privé-Azure Kubernetes Service maken

In een privécluster heeft het besturingsvlak of de API-server interne IP-adressen die zijn gedefinieerd in het document [RFC1918 - Adrestoewijzing voor privé-internet.](https://tools.ietf.org/html/rfc1918) Door een privécluster te gebruiken, kunt u ervoor zorgen dat netwerkverkeer tussen uw API-server en uw knooppuntgroepen alleen in het particuliere netwerk blijft.

Het besturingsvlak of de API-server maakt Azure Kubernetes Service beheerd Azure-abonnement (AKS). Het cluster of de knooppuntgroep van een klant is in het abonnement van de klant. De server en de cluster- of knooppuntgroep kunnen met elkaar communiceren via de [Azure Private Link-service][private-link-service] in het virtuele netwerk van de API-server en een privé-eindpunt dat beschikbaar is in het subnet van het AKS-cluster van de klant.

## <a name="region-availability"></a>Beschikbaarheid in regio’s

Een privécluster is beschikbaar in openbare regio's, Azure Government en Azure China 21Vianet regio's waar [AKS wordt ondersteund.](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)

> [!NOTE]
> Azure Government-sites worden ondersteund, maar US Gov - Texas wordt momenteel niet ondersteund vanwege ontbrekende Private Link ondersteuning.

## <a name="prerequisites"></a>Vereisten

* Azure CLI versie 2.2.0 of hoger
* De Private Link-service wordt alleen ondersteund Azure Load Balancer Standard-service. Basic Azure Load Balancer wordt niet ondersteund.  
* Als u een aangepaste DNS-server wilt gebruiken, voegt u Azure DNS IP 168.63.129.16 toe als upstream-DNS-server in de aangepaste DNS-server.

## <a name="create-a-private-aks-cluster"></a>Een privé-AKS-cluster maken

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep of gebruik een bestaande resourcegroep voor uw AKS-cluster.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Standaard basisnetwerken 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Waarbij `--enable-private-cluster` een verplichte vlag is voor een privécluster. 

### <a name="advanced-networking"></a>Geavanceerde netwerken  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
Waarbij `--enable-private-cluster` een verplichte vlag is voor een privécluster. 

> [!NOTE]
> Als het adres van de Docker-brug CIDR (172.17.0.1/16) conflicteert met de CIDR van het subnet, wijzigt u het adres van de Docker-brug op de juiste wijze.

## <a name="configure-private-dns-zone"></a>Een Privé-DNS configureren 

De volgende parameters kunnen worden gebruikt voor het configureren Privé-DNS Zone.

- 'Systeem' is de standaardwaarde. Als het argument --private-dns-zone wordt weggelaten, maakt AKS een Privé-DNS zone in de knooppuntresourcegroep.
- 'Geen' betekent dat AKS geen nieuwe Privé-DNS maken.  Hiervoor moet u Bring Your Own DNS Server gebruiken en de DNS-resolutie voor de privé-FQDN configureren.  Als u geen DNS-resolutie configureert, kan DNS alleen worden opgelost binnen de agentknooppunten en worden clusterproblemen veroorzaakt na de implementatie. 
- 'CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID' vereist dat u een Privé-DNS Zone in deze indeling maakt voor azure global cloud: `privatelink.<region>.azmk8s.io` . U hebt de resource-id van die Privé-DNS zone nodig.  Daarnaast hebt u een door de gebruiker toegewezen identiteit of service-principal met ten minste de `private dns zone contributor`  rollen `vnet contributor` en nodig.
- 'fqdn-subdomain' kan alleen worden gebruikt met 'CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID' om subdomeinmogelijkheden te bieden aan `privatelink.<region>.azmk8s.io`

### <a name="prerequisites"></a>Vereisten

* De AKS Preview-versie 0.5.7 of hoger
* De API-versie 2020-11-01 of hoger

### <a name="create-a-private-aks-cluster-with-private-dns-zone"></a>Een privé-AKS-cluster maken met Privé-DNS Zone

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone [system|none]
```

### <a name="create-a-private-aks-cluster-with-a-custom-private-dns-zone"></a>Een privé-AKS-cluster maken met een aangepaste Privé-DNS zone

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone <custom private dns zone ResourceId> --fqdn-subdomain <subdomain-name>
```

## <a name="options-for-connecting-to-the-private-cluster"></a>Opties voor het maken van verbinding met het privécluster

Het eindpunt van de API-server heeft geen openbaar IP-adres. Als u de API-server wilt beheren, moet u een VM gebruiken die toegang heeft tot de Azure Virtual Network (VNet) van het AKS-cluster. Er zijn verschillende opties voor het tot stand komen van netwerkconnectiviteit met het privécluster.

* Maak een VM in hetzelfde Azure Virtual Network (VNet) als het AKS-cluster.
* Gebruik een virtuele machine in een afzonderlijk netwerk en stel [peering voor virtuele netwerken in.][virtual-network-peering]  Zie de sectie hieronder voor meer informatie over deze optie.
* Gebruik een [ExpressRoute- of VPN-verbinding.][express-route-or-VPN]
* Gebruik de [AKS Uitvoeropdracht functie](#aks-run-command-preview).

Het maken van een VM in hetzelfde VNET als het AKS-cluster is de eenvoudigste optie.  Express Route en VPN's voegen kosten toe en vereisen extra netwerkcomplexiteit.  Voor peering voor virtuele netwerken moet u de CIDR-adresbereiken van uw netwerk plannen om ervoor te zorgen dat er geen overlappende adresbereiken zijn.

### <a name="aks-run-command-preview"></a>AKS Uitvoeropdracht (preview)

Wanneer u toegang nodig hebt tot een privécluster, moet u dit doen binnen het virtuele clusternetwerk of een peered netwerk of clientmachine. Hiervoor moet uw computer meestal via VPN of Express Route zijn verbonden met het virtuele clusternetwerk of een jumpbox die in het virtuele netwerk van het cluster moet worden gemaakt. Met de AKS-opdracht uitvoeren kunt u opdrachten in een AKS-cluster op afstand aanroepen via de AKS-API. Deze functie biedt een API waarmee u bijvoorbeeld Just-In-Time-opdrachten kunt uitvoeren vanaf een externe laptop voor een privécluster. Dit kan enorm helpen bij snelle Just-In-Time-toegang tot een privécluster wanneer de clientmachine zich niet in het privénetwerk van het cluster behoudt en dezelfde RBAC-besturingselementen en persoonlijke API-server afdwingt.

### <a name="register-the-runcommandpreview-preview-feature"></a>De `RunCommandPreview` preview-functie registreren

Als u de nieuwe UITVOEROPDRACHT-API wilt gebruiken, moet u de `RunCommandPreview` functievlag inschakelen voor uw abonnement.

Registreer de `RunCommandPreview` functievlag met behulp van de opdracht [az feature register][az-feature-register], zoals wordt weergegeven in het volgende voorbeeld:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "RunCommandPreview"
```

Het duurt enkele minuten voordat de status Geregistreerd *we weergeven.* Controleer de registratiestatus met behulp van [de opdracht az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/RunCommandPreview')].{Name:name,State:properties.state}"
```

Wanneer u klaar bent, vernieuwt u de registratie van de resourceprovider *Microsoft.ContainerService* met behulp van de [opdracht az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="use-aks-run-command"></a>AKS-Uitvoeropdracht

Eenvoudige opdracht

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl get pods -n kube-system"
```

Een manifest implementeren door het specifieke bestand bij te koppelen

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl apply -f deployment.yaml -n default" -f deployment.yaml
```

Een manifest implementeren door een hele map te koppelen

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl apply -f deployment.yaml -n default" -f .
```

Een Helm-installatie uitvoeren en het manifest met specifieke waarden doorgeven

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "helm repo add bitnami https://charts.bitnami.com/bitnami && helm repo update && helm install my-release -f values.yaml bitnami/nginx" -f values.yaml
```

## <a name="virtual-network-peering"></a>Peering op virtueel netwerk

Zoals vermeld, is peering voor virtuele netwerken een manier om toegang te krijgen tot uw privécluster. Als u peering voor virtuele netwerken wilt gebruiken, moet u een koppeling instellen tussen het virtuele netwerk en de privé-DNS-zone.
    
1. Ga naar de knooppuntresourcegroep in de Azure Portal.  
2. Selecteer de privé-DNS-zone.   
3. Selecteer in het linkerdeelvenster de **koppeling Virtueel** netwerk.  
4. Maak een nieuwe koppeling om het virtuele netwerk van de virtuele machine toe te voegen aan de privé-DNS-zone. Het duurt enkele minuten voordat de DNS-zonekoppeling beschikbaar is.  
5. Navigeer Azure Portal de resourcegroep die het virtuele netwerk van uw cluster bevat.  
6. Selecteer het virtuele netwerk in het rechterdeelvenster. De naam van het virtuele netwerk heeft de vorm *aks-vnet- \**.  
7. Selecteer **peerings in het linkerdeelvenster.**  
8. Selecteer **Toevoegen,** voeg het virtuele netwerk van de virtuele machine toe en maak vervolgens de peering.  
9. Ga naar het virtuele netwerk waar u de virtuele machine hebt, selecteer **Peerings,** selecteer het virtuele AKS-netwerk en maak vervolgens de peering. Als het adresbereik in het virtuele AKS-netwerk en het virtuele netwerk van de virtuele machine conflicteren, mislukt peering. Zie Peering voor virtuele [netwerken voor meer informatie.][virtual-network-peering]

## <a name="hub-and-spoke-with-custom-dns"></a>Hub en spoke met aangepaste DNS

[Hub- en spoke-architecturen](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) worden vaak gebruikt voor het implementeren van netwerken in Azure. In veel van deze implementaties zijn DNS-instellingen in de spoke-VNets geconfigureerd om te verwijzen naar een centrale DNS-doorsturende server om on-premises en op Azure gebaseerde DNS-resolutie toe te staan. Wanneer u een AKS-cluster implementeert in een dergelijke netwerkomgeving, moet u rekening houden met enkele speciale overwegingen.

![Hub en spoke van privécluster](media/private-clusters/aks-private-hub-spoke.png)

1. Wanneer een privécluster wordt ingericht, worden standaard een privé-eindpunt (1) en een privé-DNS-zone (2) gemaakt in de door het cluster beheerde resourcegroep. Het cluster gebruikt een A-record in de privézone om het IP-adres van het privé-eindpunt om te zetten voor communicatie met de API-server.

2. De privé-DNS-zone is alleen gekoppeld aan het VNet waar de clusterknooppunten aan zijn gekoppeld (3). Dit betekent dat het privé-eindpunt alleen kan worden opgelost door hosts in dat gekoppelde VNet. In scenario's waarin er geen aangepaste DNS is geconfigureerd op het VNet (standaard), werkt dit zonder problemen als hosts punt op 168.63.129.16 voor DNS die records in de privé-DNS-zone kunnen oplossen vanwege de koppeling.

3. In scenario's waarin het VNet met uw cluster aangepaste DNS-instellingen heeft (4), mislukt de clusterimplementatie, tenzij de privé-DNS-zone is gekoppeld aan het VNet dat de aangepaste DNS-resolvers bevat (5). Deze koppeling kan handmatig worden gemaakt nadat de privézone is gemaakt tijdens het inrichten van het cluster of via automatisering bij het detecteren van het maken van de zone met behulp van implementatiemechanismen op basis van gebeurtenissen (bijvoorbeeld Azure Event Grid en Azure Functions).

> [!NOTE]
> Als u Bring Your Own Route Table met [kubenet](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) en Bring Your Own DNS met privécluster gebruikt, mislukt het maken van het cluster. U moet de [RouteTable](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) in de knooppuntresourcegroep koppelen aan het subnet nadat het maken van het cluster is mislukt, om het maken te laten slagen.

## <a name="limitations"></a>Beperkingen 
* Toegestane IP-adresbereiken kunnen niet worden toegepast op het privé-API-server-eindpunt. Ze zijn alleen van toepassing op de openbare API-server
* [Azure Private Link servicebeperkingen zijn][private-link-service] van toepassing op privéclusters.
* Er is geen ondersteuning voor door Microsoft gehoste Azure DevOps-agents met privéclusters. Overweeg om [zelf-hostende agents te gebruiken.](/azure/devops/pipelines/agents/agents?tabs=browser) 
* Voor klanten die de Azure Container Registry met privé-AKS moeten kunnen werken, moet het virtuele netwerk Container Registry peering hebben met het virtuele netwerk van het agentcluster.
* Geen ondersteuning voor het converteren van bestaande AKS-clusters naar privéclusters
* Als u het privé-eindpunt in het klantsubnet wilt verwijderen of wijzigen, werkt het cluster niet meer. 
* Nadat klanten de A-record op hun eigen DNS-servers hebben bijgewerkt, zouden die pods na de migratie nog steeds de FQDN apiserver oplossen naar het oudere IP-adres totdat ze opnieuw worden opgestart. Klanten moeten hostNetwork Pods en standaard-DNSPolicy Pods opnieuw starten na de migratie van het besturingsvlak.
* In het geval van onderhoud op het besturingsvlak kan het [IP-adres van uw AKS](./limit-egress-traffic.md) worden gewijzigd. In dit geval moet u de A-record bijwerken die verwijst naar het privé-IP-adres van de API-server op uw aangepaste DNS-server en aangepaste pods of implementaties opnieuw starten met hostNetwork.

<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: ../private-link/private-link-service-overview.md#limitations
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md
[devops-agents]: /azure/devops/pipelines/agents/agents
[availability-zones]: availability-zones.md

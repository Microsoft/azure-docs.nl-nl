---
title: Een Azure CNI configureren in Azure Kubernetes Service (AKS)
description: Meer informatie over het configureren Azure CNI (geavanceerde) netwerken in Azure Kubernetes Service (AKS), waaronder het implementeren van een AKS-cluster in een bestaand virtueel netwerk en subnet.
services: container-service
ms.topic: article
ms.date: 06/03/2019
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 62885a4695e7b061a5e7f0e70496cde4663c943d
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478927"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Een Azure CNI configureren in Azure Kubernetes Service (AKS)

AKS-clusters maken standaard gebruik [van kubenet][kubenet]en er worden een virtueel netwerk en subnet voor u gemaakt. Met *kubenet krijgen* knooppunten een IP-adres uit een subnet van een virtueel netwerk. Network Address Translation (NAT) wordt vervolgens geconfigureerd op de knooppunten en pods ontvangen een IP-adres dat 'verborgen' is achter het knooppunt-IP. Deze aanpak vermindert het aantal IP-adressen dat u in uw netwerkruimte moet reserveren voor gebruik door pods.

Met [Azure Container Networking Interface (CNI)][cni-networking]krijgt elke pod een IP-adres uit het subnet en is deze rechtstreeks toegankelijk. Deze IP-adressen moeten uniek zijn binnen uw netwerkruimte en moeten van tevoren worden gepland. Elk knooppunt heeft een configuratieparameter voor het maximum aantal pods dat wordt ondersteund. Het equivalente aantal IP-adressen per knooppunt wordt vervolgens vooraf gereserveerd voor dat knooppunt. Deze aanpak vereist meer planning en leidt vaak tot uitputting van IP-adressen of de noodzaak om clusters opnieuw te bouwen in een groter subnet naarmate de vraag van uw toepassing toe groeit.

In dit artikel wordt beschreven hoe u *Azure CNI* gebruikt om een subnet van een virtueel netwerk te maken en te gebruiken voor een AKS-cluster. Zie Netwerkconcepten voor Kubernetes en AKS voor meer informatie over [netwerkopties en overwegingen.][aks-network-concepts]

## <a name="prerequisites"></a>Vereisten

* Het virtuele netwerk voor het AKS-cluster moet uitgaande internetverbinding toestaan.
* AKS-clusters gebruiken mogelijk niet , , of voor het adresbereik van de `169.254.0.0/16` `172.30.0.0/16` `172.31.0.0/16` Kubernetes-service, het adresbereik van pods of het adresbereik `192.0.2.0/24` van het virtuele netwerk van het cluster.
* De clusteridentiteit die door het AKS-cluster wordt gebruikt, moet ten minste machtigingen voor Netwerkbijdrager hebben voor het subnet in uw virtuele netwerk. [](../role-based-access-control/built-in-roles.md#network-contributor) Als u een aangepaste rol [wilt](../role-based-access-control/custom-roles.md) definiëren in plaats van de ingebouwde rol Inzender voor netwerken te gebruiken, zijn de volgende machtigingen vereist:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`
* Het subnet dat is toegewezen aan de AKS-knooppuntgroep kan geen [gedelegeerd subnet zijn.](../virtual-network/subnet-delegation-overview.md)

## <a name="plan-ip-addressing-for-your-cluster"></a>IP-adressering voor uw cluster plannen

Clusters die zijn geconfigureerd met Azure CNI netwerk, vereisen aanvullende planning. De grootte van uw virtuele netwerk en het subnet moeten geschikt zijn voor het aantal pods dat u wilt uitvoeren en het aantal knooppunten voor het cluster.

IP-adressen voor de pods en de knooppunten van het cluster worden toegewezen vanuit het opgegeven subnet binnen het virtuele netwerk. Elk knooppunt is geconfigureerd met een primair IP-adres. Standaard worden 30 extra IP-adressen vooraf geconfigureerd door Azure CNI die zijn toegewezen aan pods die zijn gepland op het knooppunt. Wanneer u uw cluster uitschaalt, wordt elk knooppunt op dezelfde manier geconfigureerd met IP-adressen uit het subnet. U kunt ook het [maximumaantal pods per knooppunt weergeven.](#maximum-pods-per-node)

> [!IMPORTANT]
> Het aantal vereiste IP-adressen moet overwegingen bevatten voor upgrade- en schaalbewerkingen. Als u het IP-adresbereik zo instelt dat alleen een vast aantal knooppunten wordt ondersteund, kunt u uw cluster niet upgraden of schalen.
>
> * Wanneer u **uw** AKS-cluster upgradet, wordt er een nieuw knooppunt geïmplementeerd in het cluster. Services en workloads worden op het nieuwe knooppunt uitgevoerd en een ouder knooppunt wordt verwijderd uit het cluster. Dit rolling upgrade vereist dat er minimaal één extra blok IP-adressen beschikbaar is. Uw aantal knooppunt is dan `n + 1` .
>   * Deze overweging is met name belangrijk wanneer u Windows Server-knooppuntgroepen gebruikt. Windows Server-knooppunten in AKS passen niet automatisch Windows-updates toe, maar u voert een upgrade uit op de knooppuntgroep. Met deze upgrade worden nieuwe knooppunten geïmplementeerd met de meest recente basisknooppuntafbeelding en beveiligingspatches voor Window Server 2019. Zie Een knooppuntgroep upgraden in AKS voor meer informatie over het upgraden van een Windows [Server-knooppuntgroep.][nodepool-upgrade]
>
> * Wanneer u **een** AKS-cluster schaalt, wordt er een nieuw knooppunt geïmplementeerd in het cluster. Services en workloads worden op het nieuwe knooppunt uitgevoerd. Uw IP-adresbereik moet rekening houden met de manier waarop u het aantal knooppunten en pods dat door uw cluster kan worden ondersteund, omhoog wilt schalen. Er moet ook een extra knooppunt voor upgradebewerkingen worden opgenomen. Uw aantal knooppunt is dan `n + number-of-additional-scaled-nodes-you-anticipate + 1` .

Als u verwacht dat uw knooppunten het maximum aantal pods uitvoeren en regelmatig pods vernietigen en implementeren, moet u ook rekening houden met een aantal extra IP-adressen per knooppunt. Deze extra IP-adressen houden er rekening mee dat het enkele seconden kan duren voordat een service wordt verwijderd en het IP-adres wordt vrijgegeven voor de nieuwe service en het adres wordt verkregen.

Het IP-adresplan voor een AKS-cluster bestaat uit een virtueel netwerk, ten minste één subnet voor knooppunten en pods en een Kubernetes-serviceadresbereik.

| Adresbereik / Azure-resource | Limieten en formaat |
| --------- | ------------- |
| Virtueel netwerk | Het virtuele Azure-netwerk kan zo groot zijn als /8, maar is beperkt tot 65.536 geconfigureerde IP-adressen. Houd rekening met al uw netwerkbehoeften, inclusief de communicatie met services in andere virtuele netwerken, voordat u uw adresruimte configureert. Als u bijvoorbeeld een te grote adresruimte configureert, kunt u problemen krijgen met overlappende andere adresruimten in uw netwerk.|
| Subnet | Moet groot genoeg zijn voor de knooppunten, pods en alle Kubernetes- en Azure-resources die in uw cluster kunnen worden ingericht. Als u bijvoorbeeld een interne Azure Load Balancer implementeert, worden de front-end-IP's toegewezen vanuit het clustersubnet, niet via openbare IP's. Bij de subnetgrootte moet ook rekening worden gehouden met upgradebewerkingen of toekomstige schaalbehoeften.<p />De minimale *subnetgrootte* berekenen, inclusief een extra knooppunt voor upgradebewerkingen: `(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>Voorbeeld voor een cluster met 50 knooppunt: `(51) + (51  * 30 (default)) = 1,581` (/21 of groter)<p/>Voorbeeld voor een cluster met 50 knooppunten dat ook is ingericht voor het omhoog schalen van 10 extra knooppunten: `(61) + (61 * 30 (default)) = 1,891` (/21 of groter)<p>Als u bij het maken van uw cluster geen maximum aantal pods per knooppunt opgeeft, wordt het maximum aantal pods per knooppunt ingesteld *op 30*. Het minimale aantal vereiste IP-adressen is gebaseerd op die waarde. Als u de minimale IP-adresvereisten voor een andere maximumwaarde berekent, bekijkt u hoe u het maximum aantal [pods per](#configure-maximum---new-clusters) knooppunt configureert om deze waarde in te stellen wanneer u uw cluster implementeert. |
| Adresbereik van Kubernetes Service | Dit bereik mag niet worden gebruikt door een netwerkelement in of verbonden met dit virtuele netwerk. Serviceadres-CIDR moet kleiner zijn dan /12. U kunt dit bereik opnieuw gebruiken voor verschillende AKS-clusters. |
| IP-adres van DNS-service van Kubernetes | IP-adres binnen het adresbereik van de Kubernetes-service dat wordt gebruikt door clusterservicedetectie. Gebruik niet het eerste IP-adres in uw adresbereik, zoals .1. Het eerste adres in uw subnetbereik wordt gebruikt voor het adres *kubernetes.default.svc.cluster.local.* |
| Adres van Docker Bridge | Het netwerkadres van Docker Bridge vormt het standaardadres van het *docker0* bridge-netwerk dat in alle Docker-installaties aanwezig is. Hoewel *docker0* Bridge niet wordt gebruikt door AKS-clusters of de pods zelf, moet u dit adres instellen om scenario's zoals *docker-build* in het AKS-cluster te blijven ondersteunen. U moet een CIDR selecteren voor het netwerkadres van de Docker-brug, omdat Docker anders automatisch een subnet kiest, wat een conflict kan veroorzaakt met andere CIDR's. U moet een adresruimte kiezen die niet in contact komt met de rest van de CIDR's in uw netwerken, met inbegrip van de CIDR van de service van het cluster en de CIDR voor pods. De standaardwaarde is 172.17.0.1/16. U kunt dit bereik opnieuw gebruiken voor verschillende AKS-clusters. |

## <a name="maximum-pods-per-node"></a>Maximum aantal pods per knooppunt

Het maximum aantal pods per knooppunt in een AKS-cluster is 250. Het *standaard* maximumaantal pods per knooppunt varieert tussen *kubenet* en *Azure CNI* en de methode van clusterimplementatie.

| Implementatiemethode | Standaard Kubenet | Azure CNI standaard | Configureerbaar tijdens implementatie |
| -- | :--: | :--: | -- |
| Azure CLI | 110 | 30 | Ja (maximaal 250) |
| Resource Manager-sjabloon | 110 | 30 | Ja (maximaal 250) |
| Portal | 110 | 110 (geconfigureerd op het tabblad Knooppuntgroepen) | Nee |

### <a name="configure-maximum---new-clusters"></a>Maximum configureren - nieuwe clusters

U kunt het maximum aantal pods per knooppunt configureren tijdens de implementatie van het cluster of wanneer u nieuwe knooppuntgroepen toevoegt. Als u implementeert met de Azure CLI of met een Resource Manager-sjabloon, kunt u het maximum aantal pods per knooppuntwaarde instellen op 250.

Als u geen maxPods opgeeft bij het maken van nieuwe knooppuntgroepen, ontvangt u een standaardwaarde van 30 voor Azure CNI.

Er wordt een minimumwaarde afgedwongen voor het maximum aantal pods per knooppunt om ruimte te garanderen voor systeempods die essentieel zijn voor de clustertoestand. De minimumwaarde die kan worden ingesteld voor het maximum aantal pods per knooppunt is 10 als en alleen als de configuratie van elke knooppuntgroep ruimte heeft voor minimaal 30 pods. Als u bijvoorbeeld het maximum aantal pods per knooppunt in stelt op het minimum van 10, moet elke afzonderlijke knooppuntgroep minimaal drie knooppunten hebben. Deze vereiste geldt ook voor elke nieuwe knooppuntgroep die wordt gemaakt, dus als 10 is gedefinieerd als maximum aantal pods per knooppunt, moet elke volgende toegevoegde knooppuntgroep ten minste 3 knooppunten hebben.

| Netwerken | Minimum | Maximum |
| -- | :--: | :--: |
| Azure CNI | 10 | 250 |
| Kubenet | 10 | 110 |

> [!NOTE]
> De minimumwaarde in de bovenstaande tabel wordt strikt afgedwongen door de AKS-service. U kunt geen maxPods-waarde lager instellen dan het minimum dat wordt weergegeven, zodat het cluster niet kan worden starten.

* **Azure CLI:** geef het `--max-pods` argument op wanneer u een cluster implementeert met de opdracht az [aks create.][az-aks-create] De maximumwaarde is 250.
* **Resource Manager sjabloon:** geef de eigenschap op in het `maxPods` object [ManagedClusterAgentPoolProfile] wanneer u een cluster met een Resource Manager implementeert. De maximumwaarde is 250.
* **Azure Portal:** u kunt het maximum aantal pods per knooppunt niet wijzigen wanneer u een cluster met de Azure Portal. Azure CNI-netwerkclusters zijn beperkt tot 30 pods per knooppunt wanneer u implementeert met behulp van de Azure Portal.

### <a name="configure-maximum---existing-clusters"></a>Maximum configureren - bestaande clusters

De instelling maxPod per knooppunt kan worden gedefinieerd wanneer u een nieuwe knooppuntgroep maakt. Als u de instelling maxPod per knooppunt op een bestaand cluster wilt verhogen, voegt u een nieuwe knooppuntgroep toe met het nieuwe gewenste maximum aantal maxPods. Nadat u uw pods naar de nieuwe pool hebt gemigreert, verwijdert u de oudere groep. Als u een oudere pool in een cluster wilt verwijderen, moet u knooppuntgroepmodi instellen zoals gedefinieerd in het [document systeemknooppuntgroepen.][system-node-pools]

## <a name="deployment-parameters"></a>Implementatieparameters

Wanneer u een AKS-cluster maakt, kunnen de volgende parameters worden geconfigureerd voor Azure CNI netwerken:

**Virtueel netwerk:** het virtuele netwerk waarin u het Kubernetes-cluster wilt implementeren. Als u een nieuw virtueel netwerk voor uw cluster wilt maken, selecteert u *Nieuw* maken en volgt u de stappen in de sectie *Virtueel netwerk* maken. Zie Azure subscription [and service limits, quotas, and constraints (Limieten,](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)quota en beperkingen voor Azure-abonnementen en -service) voor meer informatie over de limieten en quota voor een virtueel Azure-netwerk.

**Subnet:** het subnet binnen het virtuele netwerk waar u het cluster wilt implementeren. Als u een nieuw subnet wilt maken in het  virtuele netwerk voor uw cluster, selecteert u Nieuwe maken en volgt u de stappen in de *sectie Subnet* maken. Voor hybride connectiviteit mag het adresbereik niet overlappen met andere virtuele netwerken in uw omgeving.

**Azure Network** Plugin: wanneer de Azure-netwerkinvoegvoeging wordt gebruikt, is de interne LoadBalancer-service met externalTrafficPolicy=Local niet toegankelijk vanaf VM's met een IP in clusterCIDR die niet tot het AKS-cluster behoort.

**Adresbereik van Kubernetes-service:** deze parameter is de set virtuele IP-adressen die Kubernetes toewijst aan interne [services][services] in uw cluster. U kunt elk privéadresbereik gebruiken dat voldoet aan de volgende vereisten:

* Mag zich niet binnen het IP-adresbereik van het virtuele netwerk van uw cluster
* Mag niet overlappen met andere virtuele netwerken waarmee het virtuele netwerk van het cluster peers is
* Mag niet overlappen met on-premises IP's
* Mag zich niet binnen de `169.254.0.0/16` bereiks , `172.30.0.0/16` , `172.31.0.0/16` of `192.0.2.0/24`

Hoewel het technisch mogelijk is om een serviceadresbereik op te geven binnen hetzelfde virtuele netwerk als uw cluster, wordt dit niet aanbevolen. Onvoorspelbaar gedrag kan het gevolg zijn als overlappende IP-bereiken worden gebruikt. Zie de sectie Veelgestelde [vragen](#frequently-asked-questions) van dit artikel voor meer informatie. Zie Services [in][services] de Kubernetes-documentatie voor meer informatie over Kubernetes-services.

**IP-adres van De DNS-service van Kubernetes:** het IP-adres voor de DNS-service van het cluster. Dit adres moet binnen het *adresbereik van Kubernetes Service* liggen. Gebruik niet het eerste IP-adres in uw adresbereik, zoals .1. Het eerste adres in uw subnetbereik wordt gebruikt voor het *adres kubernetes.default.svc.cluster.local.*

**Docker Bridge-adres:** het netwerkadres van Docker Bridge vertegenwoordigt het standaardnetwerkadres *docker0* bridge dat aanwezig is in alle Docker-installaties. Hoewel *docker0* Bridge niet wordt gebruikt door AKS-clusters of de pods zelf, moet u dit adres instellen om scenario's zoals *docker-build* binnen het AKS-cluster te blijven ondersteunen. U moet een CIDR selecteren voor het netwerkadres van de Docker-brug, omdat Docker anders automatisch een subnet kiest dat conflict kan veroorzaakt met andere CIDR's. U moet een adresruimte kiezen die niet in contact komt met de rest van de CIDR's in uw netwerken, met inbegrip van de CIDR van de service van het cluster en de CIDR voor pods.

## <a name="configure-networking---cli"></a>Netwerken configureren - CLI

Wanneer u een AKS-cluster maakt met de Azure CLI, kunt u ook een Azure CNI configureren. Gebruik de volgende opdrachten om een nieuw AKS-cluster te maken met Azure CNI ingeschakeld.

Haal eerst de resource-id van het subnet op voor het bestaande subnet waaraan het AKS-cluster wordt samengevoegd:

```azurecli-interactive
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query "[0].id" --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Gebruik de [opdracht az aks create][az-aks-create] met het argument om een cluster met geavanceerde netwerken te `--network-plugin azure` maken. Werk de `--vnet-subnet-id` waarde bij met de subnet-id die in de vorige stap is verzameld:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --generate-ssh-keys
```

## <a name="configure-networking---portal"></a>Netwerken configureren - portal

In de volgende schermafbeelding van Azure Portal ziet u een voorbeeld van het configureren van deze instellingen tijdens het maken van het AKS-cluster:

! [Geavanceerde netwerkconfiguratie in de Azure Portal] [portal-01-networking-advanced]

## <a name="dynamic-allocation-of-ips-and-enhanced-subnet-support-preview"></a>Dynamische toewijzing van IP's en verbeterde ondersteuning voor subnetten (preview)

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

> [!NOTE] 
> Deze preview-functie is momenteel beschikbaar in de volgende regio's:
>
> * VS - west-centraal

Een nadeel van de traditionele CNI is de uitputting van pod-IP-adressen naarmate het AKS-cluster groeit, waardoor het hele cluster opnieuw moet worden opgebouwd in een groter subnet. De nieuwe functie voor dynamische IP-toewijzing in Azure CNI dit probleem op door pod-IP-adressen toe te staan vanuit een subnet dat is gescheiden van het subnet dat als host voor het AKS-cluster wordt gebruikt.  Het biedt de volgende voordelen:

* **Beter IP-gebruik:** IP-adressen worden dynamisch toegewezen aan clusterpods uit het podsubnet. Dit leidt tot een beter gebruik van IP's in het cluster in vergelijking met de traditionele CNI-oplossing, die statische toewijzing van IP's voor elk knooppunt doet.  

* **Schaalbaar en flexibel:** knooppunt- en podsubnetten kunnen onafhankelijk van elkaar worden geschaald. Eén podsubnet kan worden gedeeld tussen meerdere knooppuntgroepen van een cluster of tussen meerdere AKS-clusters die in hetzelfde VNet zijn geïmplementeerd. U kunt ook een afzonderlijk podsubnet configureren voor een knooppuntgroep.  

* **Hoge prestaties:** omdat aan pods VNet-IP's zijn toegewezen, hebben ze directe verbinding met andere clusterpods en resources in het VNet. De oplossing ondersteunt zeer grote clusters zonder prestatievermindering.

* **Afzonderlijke VNet-beleidsregels voor pods:** aangezien pods een afzonderlijk subnet hebben, kunt u afzonderlijke VNet-beleidsregels voor pods configureren die verschillen van het knooppuntbeleid. Dit maakt veel nuttige scenario's mogelijk, zoals het toestaan van alleen internetverbinding voor pods en niet voor knooppunten, het herstellen van het bron-IP-adres voor pods in een knooppuntgroep met behulp van een VNet Network NAT en het gebruik van NSG's om verkeer tussen knooppuntgroepen te filteren.  

* **Kubernetes-netwerkbeleid:** zowel het Azure-netwerkbeleid als Het Bedrijfsbeleid van Nuco werken met deze nieuwe oplossing.  

### <a name="install-the-aks-preview-azure-cli"></a>De `aks-preview` Azure CLI installeren

U hebt de Azure *CLI-extensie aks-preview* nodig. Installeer de *Azure CLI-extensie aks-preview* met behulp van [de opdracht az extension add.][az-extension-add] U kunt ook beschikbare updates installeren met behulp van [de opdracht az extension update.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-podsubnetpreview-preview-feature"></a>De `PodSubnetPreview` preview-functie registreren

Als u de functie wilt gebruiken, moet u ook de `PodSubnetPreview` functievlag inschakelen voor uw abonnement.

Registreer de `PodSubnetPreview` functievlag met behulp van [de opdracht az feature register,][az-feature-register] zoals wordt weergegeven in het volgende voorbeeld:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "PodSubnetPreview"
```

Het duurt enkele minuten voordat de status Geregistreerd *we weergeven.* Controleer de registratiestatus met behulp van [de opdracht az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSubnetPreview')].{Name:name,State:properties.state}"
```

Wanneer u klaar bent, vernieuwt u de registratie van de resourceprovider *Microsoft.ContainerService* met behulp van de [opdracht az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="additional-prerequisites"></a>Aanvullende vereisten

De vereisten die al worden vermeld voor Azure CNI zijn nog steeds van toepassing, maar er zijn enkele extra beperkingen:

* Alleen Linux-knooppuntclusters en knooppuntgroepen worden ondersteund.
* AKS-engine- en -CLUSTERS WORDEN niet ondersteund.

### <a name="planning-ip-addressing"></a>IP-adressering plannen

Wanneer u deze functie gebruikt, is het plannen veel eenvoudiger. Omdat de knooppunten en pods onafhankelijk worden geschaald, kunnen hun adresruimten ook afzonderlijk worden gepland. Omdat podsubnetten kunnen worden geconfigureerd voor de granulariteit van een knooppuntgroep, kunnen klanten altijd een nieuw subnet toevoegen wanneer ze een knooppuntgroep toevoegen. De systeempods in een cluster-/knooppuntgroep ontvangen ook DEP's van het podsubnet. Dit gedrag moet dus worden vereend.

De planning van DEP's voor K8S-services en Docker Bridge blijft ongewijzigd.

### <a name="maximum-pods-per-node-in-a-cluster-with-dynamic-allocation-of-ips-and-enhanced-subnet-support"></a>Maximum aantal pods per knooppunt in een cluster met dynamische toewijzing van IPs en verbeterde subnetondersteuning

De waarden voor pods per knooppunt bij gebruik Azure CNI dynamische toewijzing van DEP's zijn enigszins gewijzigd van het traditionele CNI-gedrag:

|Cni|Implementatiemethode|Standaard|Configureerbaar tijdens implementatie|
|--|--| :--: |--|
|Traditionele Azure CNI|Azure CLI|30|Ja (maximaal 250)|
|Azure CNI met dynamische toewijzing van IP's|Azure CLI|250|Ja (maximaal 250)|

Alle andere richtlijnen met betrekking tot het configureren van het maximum aantal knooppunten per pod blijven hetzelfde.

### <a name="additional-deployment-parameters"></a>Aanvullende implementatieparameters

De hierboven beschreven implementatieparameters zijn allemaal nog geldig, met één uitzondering:

* De **subnetparameter** verwijst nu naar het subnet dat is gerelateerd aan de knooppunten van het cluster.
* Er wordt een **extra parameterpodsubnet** gebruikt om het subnet op te geven waarvan de IP-adressen dynamisch worden toegewezen aan pods.

### <a name="configure-networking---cli-with-dynamic-allocation-of-ips-and-enhanced-subnet-support"></a>Netwerken configureren - CLI met dynamische toewijzing van IP's en verbeterde subnetondersteuning

Het gebruik van dynamische toewijzing van IP's en verbeterde subnetondersteuning in uw cluster is vergelijkbaar met de standaardmethode voor het configureren van een cluster Azure CNI. Het volgende voorbeeld laat zien hoe u een nieuw virtueel netwerk maakt met een subnet voor knooppunten en een subnet voor pods, en hoe u een cluster maakt dat gebruikmaakt van Azure CNI met dynamische toewijzing van IP's en verbeterde subnetondersteuning. Zorg ervoor dat u variabelen zoals vervangt `$subscription` door uw eigen waarden:

Maak eerst het virtuele netwerk met twee subnetten:

```azurecli-interactive
$resourceGroup="myResourceGroup"
$vnet="myVirtualNetwork"

# Create our two subnet network 
az network vnet create -g $rg --name $vnet --address-prefixes 10.0.0.0/8 -o none 
az network vnet subnet create -g $rg --vnet-name $vnet --name nodesubnet --address-prefixes 10.240.0.0/16 -o none 
az network vnet subnet create -g $rg --vnet-name $vnet --name podsubnet --address-prefixes 10.241.0.0/16 -o none 
```

Maak vervolgens het cluster en verwijs naar het knooppuntsubnet met en `--vnet-subnet-id` het podsubnet met behulp van `--pod-subnet-id` :

```azurecli-interactive
$clusterName="myAKSCluster"
$location="eastus"
$subscription="aaaaaaa-aaaaa-aaaaaa-aaaa"

az aks create -n $clusterName -g $resourceGroup -l $location --max-pods 250 --node-count 2 --network-plugin azure --vnet-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/nodesubnet --pod-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/podsubnet  
```

#### <a name="adding-node-pool"></a>Knooppuntgroep toevoegen

Wanneer u een knooppuntgroep toevoegt, verwijst u naar het knooppuntsubnet met `--vnet-subnet-id` en het podsubnet met behulp van `--pod-subnet-id` . In het volgende voorbeeld worden twee nieuwe subnetten gemaakt waarnaar wordt verwezen bij het maken van een nieuwe knooppuntgroep:

```azurecli-interactive
az network vnet subnet create -g $resourceGroup --vnet-name $vnet --name node2subnet --address-prefixes 10.242.0.0/16 -o none 
az network vnet subnet create -g $resourceGroup --vnet-name $vnet --name pod2subnet --address-prefixes 10.243.0.0/16 -o none 

az aks nodepool add --cluster-name $clusterName -g $resourceGroup  -n newNodepool --max-pods 250 --node-count 2 --vnet-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/node2subnet  --pod-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/pod2subnet --no-wait 
```

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

De volgende vragen en antwoorden zijn van toepassing op de **Azure CNI** netwerkconfiguratie.

* *Kan ik VM's implementeren in mijn clustersubnet?*

  Ja.

* *Welk bron-IP-adres zien externe systemen voor verkeer dat afkomstig is van Azure CNI pod met Azure CNI-functie?*

  Systemen in hetzelfde virtuele netwerk als het AKS-cluster zien het IP-adres van de pod als het bronadres voor verkeer van de pod. Systemen buiten het virtuele AKS-clusternetwerk zien het knooppunt-IP als het bronadres voor verkeer van de pod.

* *Kan ik netwerkbeleid per pod configureren?*

  Ja, Kubernetes-netwerkbeleid is beschikbaar in AKS. Zie Verkeer tussen [pods beveiligen][network-policy]met behulp van netwerkbeleid in AKS om aan de slag te gaan.

* *Kan het maximum aantal pods dat in een knooppunt kan worden geïmplementeerd, worden geconfigureerd?*

  Ja, wanneer u een cluster implementeert met de Azure CLI of een Resource Manager sjabloon. Zie [Maximum aantal pods per knooppunt](#maximum-pods-per-node).

  U kunt het maximum aantal pods per knooppunt op een bestaand cluster niet wijzigen.

* *Hoe kan ik aanvullende eigenschappen configureren voor het subnet dat ik heb gemaakt tijdens het maken van het AKS-cluster? Bijvoorbeeld service-eindpunten.*

  De volledige lijst met eigenschappen voor het virtuele netwerk en de subnetten die u maakt tijdens het maken van het AKS-cluster, kan worden geconfigureerd op de standaardpagina voor de configuratie van het virtuele netwerk in de Azure Portal.

* *Kan ik een ander subnet binnen mijn virtuele clusternetwerk gebruiken voor het* adresbereik van de **Kubernetes-service?**

  Dit wordt niet aanbevolen, maar deze configuratie is mogelijk. Het serviceadresbereik is een set virtuele IP-adressen (VIP's) die Kubernetes toewijst aan interne services in uw cluster. Azure-netwerken heeft geen inzicht in het IP-adresbereik van de service van het Kubernetes-cluster. Vanwege het gebrek aan zichtbaarheid in het adresbereik van de service van het cluster, is het mogelijk om later een nieuw subnet te maken in het virtuele clusternetwerk dat overlapt met het serviceadresbereik. Als een dergelijke overlapping optreedt, kan Kubernetes een IP-adres toewijzen aan een service die al wordt gebruikt door een andere resource in het subnet, waardoor onvoorspelbaar gedrag of fouten optreden. Door ervoor te zorgen dat u een adresbereik buiten het virtuele netwerk van het cluster gebruikt, kunt u dit overlappingsrisico voorkomen.

### <a name="dynamic-allocation-of-ip-addresses-and-enhanced-subnet-support-faqs"></a>Veelgestelde vragen over dynamische toewijzing van IP-adressen en uitgebreid subnet

De volgende vragen en antwoorden zijn van toepassing op de Azure CNI netwerkconfiguratie bij gebruik van dynamische toewijzing van IP-adressen en verbeterde **subnetondersteuning.**

* *Kan ik meerdere podsubnetten toewijzen aan een cluster-/knooppuntgroep?*

  Er kan slechts één subnet worden toegewezen aan een cluster of knooppuntgroep. Meerdere clusters of knooppuntgroepen kunnen echter één subnet delen.

* *Kan ik podsubnetten van een ander VNet helemaal toewijzen?*

  Het podsubnet moet afkomstig zijn van hetzelfde VNet als het cluster.  

* *Kunnen sommige knooppuntgroepen in een cluster de traditionele CNI gebruiken, terwijl andere de nieuwe CNI gebruiken?*

  Het hele cluster mag slechts één type CNI gebruiken.

## <a name="aks-engine"></a>AKS-engine

[Azure Kubernetes Service Engine (AKS Engine)][aks-engine] is een opensource-project dat Azure Resource Manager-sjablonen genereert die u kunt gebruiken voor het implementeren van Kubernetes-clusters in Azure.

Kubernetes-clusters die zijn gemaakt met de AKS-engine ondersteunen zowel de [kubenet-][kubenet] als Azure CNI-invoeghulp. [][cni-networking] Als zodanig worden beide netwerkscenario's ondersteund door de AKS-engine.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over netwerken in AKS in de volgende artikelen:

* [Gebruik een statisch IP-adres met de Azure Kubernetes Service (AKS) load balancer](static-ip.md)
* [Een interne load balancer met Azure Container Service (AKS)](internal-lb.md)

* [Een eenvoudige controller voor ingress maken met externe netwerkconnectiviteit][aks-ingress-basic]
* [De invoegtoepassing http-toepassingsroutering inschakelen][aks-http-app-routing]
* [Een controller voor ingress maken die gebruikmaakt van een intern, privénetwerk en IP-adres][aks-ingress-internal]
* [Maak een controller voor verkeer met een dynamisch openbaar IP-adres en configureer Let's Encrypt om automatisch TLS-certificaten te genereren][aks-ingress-tls]
* [Maak een controller voor verkeer met een statisch openbaar IP-adres en configureer Let's Encrypt om automatisch TLS-certificaten te genereren][aks-ingress-static-tls]
<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png [portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[network-policy]: use-network-policies.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[network-comparisons]: concepts-network.md#compare-network-models
[system-node-pools]: use-system-pools.md

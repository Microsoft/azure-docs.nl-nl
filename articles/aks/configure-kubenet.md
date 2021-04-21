---
title: Kubenet-netwerken configureren in Azure Kubernetes Service (AKS)
description: Meer informatie over het configureren van een kubenet-netwerk (basic) in Azure Kubernetes Service (AKS) om een AKS-cluster te implementeren in een bestaand virtueel netwerk en subnet.
services: container-service
ms.topic: article
ms.date: 06/02/2020
ms.reviewer: nieberts, jomore
ms.openlocfilehash: c373e45c8607f10c36f40a23c776bd081bf13207
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789515"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Kubenet-netwerken gebruiken met uw eigen IP-adresbereiken in Azure Kubernetes Service (AKS)

AKS-clusters maken standaard gebruik [van kubenet][kubenet]en er worden een virtueel Azure-netwerk en -subnet voor u gemaakt. Met *kubenet krijgen* knooppunten een IP-adres uit het subnet van het virtuele Azure-netwerk. Pods krijgen een IP-adres van een logisch verschillende adresruimte van het subnet van het virtuele Azure-netwerk van de knooppunten. NAT (Network Address Translation) wordt vervolgens zo geconfigureerd dat de pods resources kunnen bereiken in het virtuele Azure-netwerk. Het bron-IP-adres van het verkeer is NAT'd naar het primaire IP-adres van het knooppunt. Deze aanpak vermindert het aantal IP-adressen dat u in uw netwerkruimte moet reserveren voor gebruik door pods.

Met [Azure Container Networking Interface (CNI)][cni-networking]krijgt elke pod een IP-adres uit het subnet en is deze rechtstreeks toegankelijk. Deze IP-adressen moeten uniek zijn binnen uw netwerkruimte en moeten van tevoren worden gepland. Elk knooppunt heeft een configuratieparameter voor het maximum aantal pods dat wordt ondersteund. Het equivalente aantal IP-adressen per knooppunt wordt vervolgens vooraf gereserveerd voor dat knooppunt. Deze aanpak vereist meer planning en leidt vaak tot uitputting van IP-adressen of de noodzaak om clusters opnieuw te bouwen in een groter subnet naarmate de vraag van uw toepassing toe groeit. U kunt het maximum aantal pods configureren dat in een knooppunt kan worden geïmplementeerd tijdens het maken van het cluster of bij het maken van nieuwe knooppuntgroepen. Als u geen maxPods opgeeft bij het maken van nieuwe knooppuntgroepen, ontvangt u de standaardwaarde 110 voor kubenet.

In dit artikel wordt beschreven hoe u *kubenet-netwerken* gebruikt om een subnet van een virtueel netwerk te maken en te gebruiken voor een AKS-cluster. Zie Netwerkconcepten voor Kubernetes en AKS voor meer informatie over [netwerkopties en overwegingen.][aks-network-concepts]

## <a name="prerequisites"></a>Vereisten

* Het virtuele netwerk voor het AKS-cluster moet uitgaande internetverbinding toestaan.
* Maak niet meer dan één AKS-cluster in hetzelfde subnet.
* AKS-clusters maken mogelijk geen gebruik van , , of voor het adresbereik van de `169.254.0.0/16` `172.30.0.0/16` `172.31.0.0/16` Kubernetes-service, het adresbereik van pods of het adresbereik van `192.0.2.0/24` het virtuele netwerk van het cluster.
* De clusteridentiteit die door het AKS-cluster wordt gebruikt, moet ten minste de rol Netwerkbijdrager hebben in het subnet in uw virtuele netwerk. [](../role-based-access-control/built-in-roles.md#network-contributor) U moet ook de juiste machtigingen hebben, zoals de eigenaar van het abonnement, om een clusteridentiteit te maken en deze machtigingen toe te wijzen. Als u een aangepaste rol [wilt](../role-based-access-control/custom-roles.md) definiëren in plaats van de ingebouwde rol Inzender voor netwerken te gebruiken, zijn de volgende machtigingen vereist:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

> [!WARNING]
> Als u Windows Server-knooppuntgroepen wilt gebruiken, moet u Azure CNI. Het gebruik van kubenet als netwerkmodel is niet beschikbaar voor Windows Server-containers.

## <a name="before-you-begin"></a>Voordat u begint

Azure CLI versie 2.0.65 of hoger moet zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>Overzicht van kubenet-netwerken met uw eigen subnet

In veel omgevingen hebt u virtuele netwerken en subnetten gedefinieerd met toegewezen IP-adresbereiken. Deze virtuele netwerkbronnen worden gebruikt ter ondersteuning van meerdere services en toepassingen. Voor netwerkconnectiviteit kunnen AKS-clusters gebruikmaken van *kubenet* (basisnetwerken) of Azure CNI (*geavanceerde netwerken).*

Met *kubenet* ontvangen alleen de knooppunten een IP-adres in het subnet van het virtuele netwerk. Pods kunnen niet rechtstreeks met elkaar communiceren. In plaats daarvan worden door de gebruiker gedefinieerde routering (UDR) en doorsturen via IP gebruikt voor connectiviteit tussen pods tussen knooppunten. Standaard worden UDR's en configuratie voor doorsturen via IP gemaakt en onderhouden door de AKS-service, maar u moet de optie hebben om uw eigen routetabel te gebruiken voor aangepast [routebeheer.][byo-subnet-route-table] U kunt ook pods achter een service implementeren die een toegewezen IP-adres ontvangt en het verkeer voor de toepassing taakverdeelt. In het volgende diagram ziet u hoe de AKS-knooppunten een IP-adres ontvangen in het subnet van het virtuele netwerk, maar niet in de pods:

![Kubenet-netwerkmodel met een AKS-cluster](media/use-kubenet/kubenet-overview.png)

Azure ondersteunt maximaal 400 routes in een UDR, zodat u geen AKS-cluster met meer dan 400 knooppunten kunt hebben. Virtuele [AKS-knooppunten][virtual-nodes] en Azure-netwerkbeleid worden niet ondersteund met *kubenet*.  U kunt [Network Policies gebruiken,][calico-network-policies]omdat deze worden ondersteund met kubenet.

Met *Azure CNI* ontvangt elke pod een IP-adres in het IP-subnet en kan deze rechtstreeks communiceren met andere pods en services. Uw clusters kunnen zo groot zijn als het IP-adresbereik dat u opgeeft. Het IP-adresbereik moet echter van tevoren worden gepland en alle IP-adressen worden gebruikt door de AKS-knooppunten op basis van het maximum aantal pods dat ze kunnen ondersteunen. Geavanceerde netwerkfuncties en [][virtual-nodes] -scenario's, zoals virtuele knooppunten of netwerkbeleidsregels (Azure of Lapco), worden ondersteund *met Azure CNI.*

### <a name="limitations--considerations-for-kubenet"></a>Beperkingen & overwegingen voor kubenet

* Er is een extra hop vereist in het ontwerp van kubenet, waarmee kleine latentie wordt toegevoegd aan podcommunicatie.
* Routetabellen en door de gebruiker gedefinieerde routes zijn vereist voor het gebruik van kubenet, waardoor bewerkingen complexer worden.
* Directe pod-adressering wordt niet ondersteund voor kubenet vanwege het kubenet-ontwerp.
* In tegenstelling Azure CNI clusters kunnen meerdere kubenet-clusters geen subnet delen.
* Functies **die niet worden ondersteund op kubenet zijn** onder andere:
   * [Azure-netwerkbeleid](use-network-policies.md#create-an-aks-cluster-and-enable-network-policy), maar Het Netwerkbeleid van Deco wordt ondersteund op kubenet
   * [Windows-knooppuntgroepen](./windows-faq.md)
   * [Invoeg-on voor virtuele knooppunten](virtual-nodes.md#network-requirements)

### <a name="ip-address-availability-and-exhaustion"></a>Beschikbaarheid en uitputting van IP-adressen

Met Azure CNI is een veelvoorkomende probleem dat het toegewezen IP-adresbereik te klein is om vervolgens extra *knooppunten* toe te voegen wanneer u een cluster schaalt of upgradet. Het netwerkteam kan mogelijk ook geen groot genoeg IP-adresbereik uitgeven ter ondersteuning van de verwachte eisen van uw toepassing.

Als compromis kunt u een AKS-cluster maken dat gebruikmaakt van *kubenet* en verbinding maken met een bestaand subnet van een virtueel netwerk. Met deze methode kunnen de knooppunten gedefinieerde IP-adressen ontvangen, zonder dat ze vooraf een groot aantal IP-adressen hoeven te reserveren voor alle potentiële pods die in het cluster kunnen worden uitgevoerd.

Met *kubenet* kunt u een veel kleiner IP-adresbereik gebruiken en grote clusters en toepassingseisen ondersteunen. Zelfs met een */27* IP-adresbereik in uw subnet kunt u bijvoorbeeld een cluster met 20-25 knooppunt uitvoeren met voldoende ruimte om te schalen of upgraden. Deze clustergrootte ondersteunt maximaal *2200-2750* pods (met een standaard maximum van 110 pods per knooppunt). Het maximum aantal pods per knooppunt dat u met *kubenet* in AKS kunt configureren, is 110.

In de volgende basisberekeningen wordt het verschil in netwerkmodellen vergeleken:

- **kubenet:** een eenvoudig */24* IP-adresbereik biedt ondersteuning voor *maximaal 251* knooppunten in het cluster (elk subnet van een virtueel Azure-netwerk reserveert de eerste drie IP-adressen voor beheerbewerkingen)
  - Dit aantal knooppunt kan maximaal *27.610* pods ondersteunen (met een standaard maximum van 110 pods per knooppunt met *kubenet*)
- **Azure CNI:** hetzelfde *basisbereik van /24* subnets kan maximaal *8* knooppunten in het cluster ondersteunen
  - Dit aantal knooppunt kan maximaal *240* pods ondersteunen (met een standaard maximum van 30 pods per knooppunt *met Azure CNI*)

> [!NOTE]
> Bij deze maximumen wordt geen rekening gehouden met upgrade- of schaalbewerkingen. In de praktijk kunt u niet het maximum aantal knooppunten uitvoeren dat door het IP-adresbereik van het subnet wordt ondersteund. U moet een aantal IP-adressen beschikbaar laten voor gebruik tijdens de schaal van upgradebewerkingen.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Peering van virtuele netwerken en ExpressRoute-verbindingen

Om on-premises connectiviteit te bieden, kunnen zowel *kubenet-* als *Azure-CNI-netwerkmethoden* peering van [virtuele Azure-netwerken][vnet-peering] of [ExpressRoute-verbindingen gebruiken.][express-route] Plan uw IP-adresbereiken zorgvuldig om overlapping en onjuiste verkeersroutering te voorkomen. Veel on-premises netwerken gebruiken bijvoorbeeld een *adresbereik van 10.0.0.0/8* dat wordt geadverteerd via de ExpressRoute-verbinding. Het is raadzaam om uw AKS-clusters te maken in subnetten van virtuele Azure-netwerken buiten dit adresbereik, zoals *172.16.0.0/16.*

### <a name="choose-a-network-model-to-use"></a>Kies een netwerkmodel dat u wilt gebruiken

De keuze van de netwerkinvoeging die u voor uw AKS-cluster wilt gebruiken, is meestal een balans tussen flexibiliteit en geavanceerde configuratiebehoeften. De volgende overwegingen geven een overzicht van wanneer elk netwerkmodel het meest geschikt is.

Gebruik *kubenet wanneer:*

- U hebt beperkte IP-adresruimte.
- Het grootste deel van de podcommunicatie is binnen het cluster.
- U hebt geen geavanceerde AKS-functies nodig, zoals virtuele knooppunten of Azure Network Policy.  Gebruik [Het netwerkbeleid van Kalico.][calico-network-policies]

Gebruik *Azure CNI* wanneer:

- U hebt beschikbare IP-adresruimte.
- Het grootste deel van de podcommunicatie is naar resources buiten het cluster.
- U wilt geen door de gebruiker gedefinieerde routes voor podconnectiviteit beheren.
- U hebt geavanceerde AKS-functies nodig, zoals virtuele knooppunten of Azure Network Policy.  Gebruik [Het netwerkbeleid van Kalico.][calico-network-policies]

Zie Netwerkmodellen en hun ondersteuningsbereik vergelijken voor meer informatie om u te helpen bepalen welk netwerkmodel u [wilt gebruiken.][network-comparisons]

## <a name="create-a-virtual-network-and-subnet"></a>Een virtueel netwerk en een subnet maken

Als u aan de slag wilt met het gebruik van *kubenet* en uw eigen subnet voor een virtueel netwerk, maakt u eerst een resourcegroep met de [opdracht az group create.][az-group-create] In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Als u geen bestaand virtueel netwerk en subnet hebt om te gebruiken, maakt u deze netwerkbronnen met de [opdracht az network vnet create.][az-network-vnet-create] In het volgende voorbeeld heeft het virtuele netwerk de naam *myVnet* met het adres voorvoegsel *192.168.0.0/16.* Er wordt een subnet gemaakt met de naam *myAKSSubnet* met het adres voorvoegsel *192.168.1.0/24.*

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 192.168.0.0/16 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Een service-principal maken en machtigingen toewijzen

Een AKS-cluster heeft een service-principal van Azure Active Directory nodig om met andere Azure-resources te kunnen communiceren. De service-principal moet machtigingen hebben voor het beheren van het virtuele netwerk en subnet dat de AKS-knooppunten gebruiken. Als u een service-principal wilt maken, gebruikt [u de opdracht az ad sp create-for-rbac:][az-ad-sp-create-for-rbac]

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

In de volgende voorbeelduitvoer ziet u de toepassings-id en het wachtwoord voor uw service-principal. Deze waarden worden in extra stappen gebruikt om een rol toe te wijzen aan de service-principal en vervolgens het AKS-cluster te maken:

```output
{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

Als u de juiste delegaties wilt toewijzen in de resterende stappen, gebruikt u de opdrachten [az network vnet show][az-network-vnet-show] en az network [vnet subnet show om][az-network-vnet-subnet-show] de vereiste resource-ID's op te halen. Deze resource-ID's worden opgeslagen als variabelen en er wordt in de resterende stappen naar verwezen:

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Wijs nu de service-principal  toe voor de inzendersmachtigingen voor het netwerk van uw AKS-cluster op het virtuele netwerk met behulp van [de opdracht az role assignment create.][az-role-assignment-create] Geef uw eigen *\<appId>* op, zoals wordt weergegeven in de uitvoer van de vorige opdracht om de service-principal te maken:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role "Network Contributor"
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Een AKS-cluster maken in het virtuele netwerk

U hebt nu een virtueel netwerk en subnet gemaakt en machtigingen gemaakt en toegewezen voor een service-principal om deze netwerkbronnen te gebruiken. Maak nu een AKS-cluster in uw virtuele netwerk en subnet met behulp van [de opdracht az aks create.][az-aks-create] Definieer uw eigen service-principal en , zoals wordt weergegeven in de uitvoer van de vorige *\<appId>* *\<password>* opdracht om de service-principal te maken.

De volgende IP-adresbereiken worden ook gedefinieerd als onderdeel van het proces voor het maken van het cluster:

* De *--service-cidr wordt gebruikt om* interne services in het AKS-cluster een IP-adres toe te wijzen. Dit IP-adresbereik moet een adresruimte zijn die elders in uw netwerkomgeving niet wordt gebruikt, met inbegrip van on-premises netwerkbereiken als u verbinding maakt of verbinding wilt maken met uw virtuele Azure-netwerken met behulp van Express Route of een site-naar-site-VPN-verbinding.

* Het *--dns-service-ip-adres* moet het *.10-adres* van het IP-adresbereik van uw service zijn.

* De *--pod-cidr moet* een grote adresruimte zijn die elders in uw netwerkomgeving niet wordt gebruikt. Dit bereik omvat alle on-premises netwerkbereiken als u verbinding maakt of verbinding wilt maken met uw virtuele Azure-netwerken met behulp van Express Route of een site-naar-site-VPN-verbinding.
    * Dit adresbereik moet groot genoeg zijn om ruimte te bieden aan het aantal knooppunten waar u naar verwachting naar opschaalt. U kunt dit adresbereik niet wijzigen zodra het cluster is geïmplementeerd als u meer adressen nodig hebt voor extra knooppunten.
    * Het IP-adresbereik van de pod wordt gebruikt om *een /24-adresruimte* toe te wijzen aan elk knooppunt in het cluster. In het volgende voorbeeld wijst *de --pod-cidr* *van 10.244.0.0/16* het eerste knooppunt *10.244.0.0/24* toe, het tweede knooppunt *10.244.1.0/24* en het derde knooppunt *10.244.2.0/24*.
    * Wanneer het cluster wordt geschaald of geupgraded, blijft het Azure-platform een IP-adresbereik voor pods toewijzen aan elk nieuw knooppunt.
    
* Met *--docker-bridge-address kunnen* de AKS-knooppunten communiceren met het onderliggende beheerplatform. Dit IP-adres mag niet binnen het IP-adresbereik van het virtuele netwerk van uw cluster liggen en mag niet overlappen met andere adresbereiken die in uw netwerk worden gebruikt.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

> [!Note]
> Als u een AKS-cluster wilt inschakelen om een [Kalico-netwerkbeleid][calico-network-policies] op te nemen, kunt u de volgende opdracht gebruiken.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet --network-policy calico \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

Wanneer u een AKS-cluster maakt, worden er automatisch een netwerkbeveiligingsgroep en routetabel gemaakt. Deze netwerkbronnen worden beheerd door het AKS-besturingsvlak. De netwerkbeveiligingsgroep wordt automatisch gekoppeld aan de virtuele NIC's op uw knooppunten. De routetabel wordt automatisch gekoppeld aan het subnet van het virtuele netwerk. Regels voor netwerkbeveiligingsgroep en routetabellen worden automatisch bijgewerkt wanneer u services maakt en wee geeft.

## <a name="bring-your-own-subnet-and-route-table-with-kubenet"></a>Uw eigen subnet en routetabel meenemen met kubenet

Met kubenet moet er een routetabel bestaan op uw clustersubnetten. AKS ondersteunt het gebruik van uw eigen bestaande subnet en routetabel.

Als uw aangepaste subnet geen routetabel bevat, maakt AKS er een voor u en worden er regels aan toegevoegd gedurende de levenscyclus van het cluster. Als uw aangepaste subnet een routetabel bevat wanneer u uw cluster maakt, bevestigt AKS de bestaande routetabel tijdens clusterbewerkingen en worden regels dienovereenkomstig toegevoegd/bijgewerkt voor bewerkingen van cloudproviders.

> [!WARNING]
> Aangepaste regels kunnen worden toegevoegd aan de aangepaste routetabel en worden bijgewerkt. Er worden echter regels toegevoegd door de Kubernetes-cloudprovider die niet mogen worden bijgewerkt of verwijderd. Regels zoals 0.0.0.0/0 moeten altijd aanwezig zijn in een bepaalde routetabel en zijn toe te staan aan het doel van uw internetgateway, zoals een NVA of een andere gateway voor een ingang. Wees voorzichtig bij het bijwerken van regels die alleen uw aangepaste regels wijzigen.

Meer informatie over het instellen van een [aangepaste routetabel.][custom-route-table]

Voor Kubenet-netwerken zijn geordende regels voor routetabel vereist om aanvragen te kunnen doorrouten. Vanwege dit ontwerp moeten routetabellen zorgvuldig worden onderhouden voor elk cluster dat ervan afhankelijk is. Meerdere clusters kunnen een routetabel niet delen omdat pod-CIDR's uit verschillende clusters elkaar kunnen overlappen, wat onverwachte en verbroken routering veroorzaakt. Wanneer u meerdere clusters in hetzelfde virtuele netwerk configureert of een virtueel netwerk toekent aan elk cluster, moet u rekening houden met de volgende beperkingen.

Beperkingen:

* Machtigingen moeten worden toegewezen voordat het cluster wordt gemaakt. Zorg ervoor dat u een service-principal gebruikt met schrijfmachtigingen voor uw aangepaste subnet en aangepaste routetabel.
* Er moet een aangepaste routetabel aan het subnet worden gekoppeld voordat u het AKS-cluster maakt.
* De gekoppelde routetabelresource kan niet worden bijgewerkt nadat het cluster is gemaakt. Hoewel de resource van de routetabel niet kan worden bijgewerkt, kunnen aangepaste regels worden gewijzigd in de routetabel.
* Elk AKS-cluster moet één unieke routetabel gebruiken voor alle subnetten die aan het cluster zijn gekoppeld. U kunt een routetabel met meerdere clusters niet opnieuw gebruiken vanwege de kans op overlappende POD-CIDR's en conflicterende routeringsregels.

Nadat u een aangepaste routetabel hebt gemaakt en deze hebt koppelen aan uw subnet in uw virtuele netwerk, kunt u een nieuw AKS-cluster maken dat gebruikmaakt van uw routetabel.
U moet de subnet-id gebruiken voor de plaats waar u uw AKS-cluster wilt implementeren. Dit subnet moet ook worden gekoppeld aan uw aangepaste routetabel.

```azurecli-interactive
# Find your subnet ID
az network vnet subnet list --resource-group
                            --vnet-name
                            [--subscription]
```

```azurecli-interactive
# Create a kubernetes cluster with with a custom subnet preconfigured with a route table
az aks create -g MyResourceGroup -n MyManagedCluster --vnet-subnet-id MySubnetID
```

## <a name="next-steps"></a>Volgende stappen

Nu er een AKS-cluster is geïmplementeerd in uw bestaande subnet van het virtuele netwerk, kunt u het cluster nu op de normale manier gebruiken. Ga aan de slag [met het maken van nieuwe apps met Helm][develop-helm] of het implementeren van bestaande apps met [Helm.][use-helm]

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[Calico-network-policies]: https://docs.projectcalico.org/v3.9/security/calico-network-policy

<!-- LINKS - Internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-network-concepts]: concepts-network.md
[az-group-create]: /cli/azure/group#az_group_create
[az-network-vnet-create]: /cli/azure/network/vnet#az_network_vnet_create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az_network_vnet_show
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[byo-subnet-route-table]: #bring-your-own-subnet-and-route-table-with-kubenet
[develop-helm]: quickstart-helm.md
[use-helm]: kubernetes-helm.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
[network-comparisons]: concepts-network.md#compare-network-models
[custom-route-table]: ../virtual-network/manage-route-table.md

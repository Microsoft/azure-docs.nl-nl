---
title: Azure CNI-netwerken configureren in azure Kubernetes service (AKS)
description: Meer informatie over het configureren van Azure CNI (Advanced)-netwerken in azure Kubernetes service (AKS), waaronder het implementeren van een AKS-cluster in een bestaand virtueel netwerk en subnet.
services: container-service
ms.topic: article
ms.date: 06/03/2019
ms.openlocfilehash: 58c2c597c7a75c801af91cd735561071250bda2c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000569"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Azure CNI-netwerken configureren in azure Kubernetes service (AKS)

AKS-clusters gebruiken standaard [kubenet][kubenet]en er worden voor u een virtueel netwerk en subnet gemaakt. Met *kubenet* krijgen knoop punten een IP-adres uit een subnet van een virtueel netwerk. NAT (Network Address Translation) wordt vervolgens geconfigureerd op de knoop punten en er wordt een IP-adres "verborgen" achter het knoop punt-IP ontvangen. Deze aanpak vermindert het aantal IP-adressen dat u in uw netwerk ruimte moet reserveren voor gebruik.

Met [Azure container Networking interface (cni)][cni-networking]haalt elke pod een IP-adres uit het subnet en kan het rechtstreeks worden geopend. Deze IP-adressen moeten uniek zijn binnen uw netwerk ruimte en moeten vooraf worden gepland. Elk knoop punt heeft een configuratie parameter voor het maximum aantal peulen dat wordt ondersteund. Het equivalente aantal IP-adressen per knoop punt wordt vervolgens vóór dat knoop punt gereserveerd. Deze aanpak vereist meer planning en leidt vaak tot een aflopende IP-adres afvoer of de nood zaak om clusters opnieuw te bouwen in een groter subnet naarmate uw toepassings vereisten groeien.

Dit artikel laat u zien hoe u met *Azure cni* Networking een subnet voor een virtueel netwerk maakt en gebruikt voor een AKS-cluster. Zie [netwerk concepten voor Kubernetes en AKS][aks-network-concepts]voor meer informatie over netwerk opties en overwegingen.

## <a name="prerequisites"></a>Vereisten

* Het virtuele netwerk voor het AKS-cluster moet uitgaande Internet verbinding toestaan.
* AKS-clusters mogen niet worden gebruikt `169.254.0.0/16` , `172.30.0.0/16` , `172.31.0.0/16` , of `192.0.2.0/24` voor het adres bereik van de Kubernetes-service, het Pod-adres bereik of het virtuele netwerk bereik van het cluster. 
* De service-principal die wordt gebruikt door het AKS-cluster moet ten minste [netwerkinzender](../role-based-access-control/built-in-roles.md#network-contributor) machtigingen hebben voor het subnet binnen het virtuele netwerk. Als u een [aangepaste rol](../role-based-access-control/custom-roles.md) wilt definiëren in plaats van de ingebouwde rol netwerk bijdrager te gebruiken, zijn de volgende machtigingen vereist:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`
* In plaats van een Service-Principal kunt u de door het systeem toegewezen beheerde identiteit voor machtigingen gebruiken. Zie [Beheerde identiteiten gebruiken](use-managed-identity.md) voor meer informatie.
* Het subnet dat is toegewezen aan de AKS-knooppunt groep mag geen [gedelegeerd subnet](../virtual-network/subnet-delegation-overview.md)zijn.

## <a name="plan-ip-addressing-for-your-cluster"></a>IP-adres sering voor uw cluster plannen

Voor clusters die zijn geconfigureerd met Azure CNI-netwerken is aanvullende planning vereist. De grootte van uw virtuele netwerk en het bijbehorende subnet moet het aantal te gebruiken plannen en het aantal knoop punten voor het cluster bevatten.

IP-adressen voor de peul en de knoop punten van het cluster worden toegewezen vanuit het opgegeven subnet binnen het virtuele netwerk. Elk knoop punt is geconfigureerd met een primair IP-adres. Standaard worden 30 extra IP-adressen vooraf geconfigureerd door Azure-CNI die worden toegewezen aan een Peul schema op het knoop punt. Wanneer u uw cluster uitschaalt, wordt elk knoop punt op dezelfde manier geconfigureerd met IP-adressen uit het subnet. U kunt ook het [maximum aantal per knoop punt](#maximum-pods-per-node)weer geven.

> [!IMPORTANT]
> Het aantal vereiste IP-adressen moet overwegingen bevatten voor upgrade-en schaal bewerkingen. Als u het IP-adres bereik zo instelt dat alleen een vast aantal knoop punten wordt ondersteund, kunt u het cluster niet upgraden of schalen.
>
> - Wanneer u uw AKS-cluster **bijwerkt** , wordt er een nieuw knoop punt in het cluster geïmplementeerd. Services en workloads worden gestart op het nieuwe knoop punt en een ouder knoop punt wordt uit het cluster verwijderd. Voor dit rolling upgrade proces moet mini maal één extra IP-adres blok beschikbaar zijn. Het aantal knoop punten is dan `n + 1` .
>   - Deze overweging is vooral belang rijk wanneer u Windows Server-knooppunt groepen gebruikt. Windows Server-knoop punten in AKS worden niet automatisch Windows-updates toegepast, in plaats daarvan een upgrade uit te voeren voor de knooppunt groep. Deze upgrade implementeert nieuwe knoop punten met de meest recente Window server 2019 base-basis knooppunt installatie kopie en beveiligings patches. Zie [een knooppunt groep bijwerken in AKS][nodepool-upgrade]voor meer informatie over het upgraden van een Windows Server-knooppunt groep.
>
> - Wanneer u een AKS-cluster **schaalt** , wordt er een nieuw knoop punt in het cluster geïmplementeerd. Services en workloads worden gestart op het nieuwe knoop punt. In uw IP-adres bereik moet u rekening houden met overwegingen voor het opschalen van het aantal knoop punten en de samen werking van het cluster. Ook moet een extra knoop punt voor upgrade bewerkingen worden opgenomen. Het aantal knoop punten is dan `n + number-of-additional-scaled-nodes-you-anticipate + 1` .

Als u verwacht dat uw knoop punten het maximum aantal en regel matig vernietigen en implementeren, moet u ook rekening houden met een aantal extra IP-adressen per knoop punt. Deze extra IP-adressen worden in overweging genomen. het kan een paar seconden duren voordat een service wordt verwijderd en het IP-adres dat voor een nieuwe service is vrijgegeven, moet worden geïmplementeerd en het adres kan worden verkregen.

Het IP-adres schema voor een AKS-cluster bestaat uit een virtueel netwerk, ten minste één subnet voor knoop punten en peulen, en een adres bereik van de Kubernetes-service.

| Adres bereik/Azure-resource | Limieten en grootte |
| --------- | ------------- |
| Virtueel netwerk | Het virtuele netwerk van Azure kan zo groot zijn als/8, maar is beperkt tot 65.536 geconfigureerde IP-adressen. Houd rekening met al uw netwerk behoeften, inclusief communicatie met Services in andere virtuele netwerken voordat u uw adres ruimte configureert. Als u bijvoorbeeld te veel adres ruimte configureert, kunt u problemen ondervinden met overlappende andere adres ruimten in uw netwerk.|
| Subnet | Moet groot genoeg zijn voor de knoop punten, peulen en alle Kubernetes en Azure-resources die in uw cluster kunnen worden ingericht. Als u bijvoorbeeld een interne Azure Load Balancer implementeert, worden de front-end Ip's toegewezen vanuit het subnet van het cluster, niet open bare Ip's. De grootte van het subnet moet ook worden toegepast op upgrades van het account of voor toekomstige schaal behoeften.<p />De *minimale* subnet grootte berekenen, inclusief een extra knoop punt voor upgrade bewerkingen: `(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>Voor beeld voor een 50-knooppunt cluster: `(51) + (51  * 30 (default)) = 1,581` (/21 of groter)<p/>Voor beeld voor een cluster met een 50-knoop punt dat ook voorziet in het opschalen van een extra 10 knoop punten: `(61) + (61 * 30 (default)) = 1,891` (/21 of groter)<p>Als u tijdens het maken van het cluster niet het maximum aantal peulen per knoop punt opgeeft, wordt het maximum aantal per knoop punt ingesteld op *30*. Het minimum aantal vereiste IP-adressen is gebaseerd op die waarde. Als u de minimum vereisten voor IP-adressen op een andere maximum waarde berekent, raadpleegt u [het maximum aantal per knoop punt configureren](#configure-maximum---new-clusters) om deze waarde in te stellen wanneer u uw cluster implementeert. |
| Adresbereik van Kubernetes Service | Dit bereik mag niet worden gebruikt door elk netwerk element op of er is geen verbinding met dit virtuele netwerk. Het service adres CIDR moet kleiner zijn dan/12. U kunt dit bereik opnieuw gebruiken in verschillende AKS-clusters. |
| IP-adres van DNS-service van Kubernetes | IP-adres binnen het adres bereik van de Kubernetes-service dat wordt gebruikt door de detectie van de Cluster service. Gebruik niet het eerste IP-adres in uw adres bereik, zoals 1. Het eerste adres in het bereik van uw subnet wordt gebruikt voor het adres *kubernetes. default. SVC. cluster. local* . |
| Docker Bridge-adres | Het netwerkadres van Docker Bridge vormt het standaardadres van het *docker0* bridge-netwerk dat in alle Docker-installaties aanwezig is. Hoewel de *docker0* -brug niet wordt gebruikt door aks-clusters of de peul zelf, moet u dit adres instellen om te blijven ondersteuning bieden voor scenario's zoals *docker build* in het AKS-cluster. U moet een CIDR voor het netwerk adres van de docker-brug selecteren, omdat in een andere docker automatisch een subnet wordt gekozen dat strijdig zou kunnen zijn met andere CIDR. U moet een adres ruimte kiezen die niet in conflict is met de rest van de CIDR-Services in uw netwerken, met inbegrip van de service-CIDR van het cluster en pod CIDR. Standaard waarde van 172.17.0.1/16. U kunt dit bereik opnieuw gebruiken in verschillende AKS-clusters. |

## <a name="maximum-pods-per-node"></a>Maxi maal aantal peulen per knoop punt

Het maximum aantal peulen per knoop punt in een AKS-cluster is 250. Het *maximum aantal* peulen per knoop punt is afhankelijk van *Kubenet* -en *Azure cni* -netwerken en de methode voor cluster implementatie.

| Implementatie methode | Kubenet standaard | Standaard instelling van Azure CNI | Configureerbaar tijdens implementatie |
| -- | :--: | :--: | -- |
| Azure CLI | 110 | 30 | Ja (Maxi maal 250) |
| Resource Manager-sjabloon | 110 | 30 | Ja (Maxi maal 250) |
| Portal | 110 | 30 | No |

### <a name="configure-maximum---new-clusters"></a>Maximum aantal nieuwe clusters configureren

U kunt het maximum aantal peulen per knoop punt op de implementatie tijd van het cluster configureren of als u nieuwe knooppunt groepen toevoegt. Als u implementeert met de Azure CLI of met een resource manager-sjabloon, kunt u het maximum aantal per knooppunt waarde instellen als 250.

Als u maxPods niet opgeeft bij het maken van nieuwe knooppunt groepen, ontvangt u een standaard waarde van 30 voor Azure CNI.

Een minimum waarde voor het maximale aantal peulen per knoop punt wordt afgedwongen om ruimte te garanderen voor het systeem van cruciaal belang voor de cluster status. De minimum waarde die kan worden ingesteld voor een maximum van peulen per knoop punt is 10 als en alleen als de configuratie van elke knooppunt groep ruimte heeft voor mini maal 30%. Voor het instellen van het maximale aantal peulen per knoop punt op het minimum van 10 moet elke afzonderlijke knooppunt groep mini maal 3 knoop punten hebben. Deze vereiste geldt ook voor elke nieuwe knooppunt groep die wordt gemaakt, dus als 10 is gedefinieerd als het maximum aantal peulen per knoop punt elke volgende knooppunt groep die wordt toegevoegd, moet ten minste drie knoop punten hebben.

| Netwerken | Minimum | Maximum |
| -- | :--: | :--: |
| Azure-CNI | 10 | 250 |
| Kubenet | 10 | 110 |

> [!NOTE]
> De minimum waarde in de bovenstaande tabel wordt strikt afgedwongen door de AKS-service. U kunt geen lagere maxPods instellen dan de minimale waarde die wordt weer gegeven, waardoor het cluster niet kan worden gestart.

* **Azure cli**: Geef het `--max-pods` argument op wanneer u een cluster implementeert met de opdracht [AZ AKS Create][az-aks-create] . De maximum waarde is 250.
* **Resource Manager-sjabloon**: Geef de `maxPods` eigenschap in het [ManagedClusterAgentPoolProfile] -object op wanneer u een cluster implementeert met een resource manager-sjabloon. De maximum waarde is 250.
* **Azure Portal**: u kunt het maximum aantal peulen per knoop punt niet wijzigen wanneer u een cluster implementeert met de Azure Portal. Azure CNI-netwerk clusters zijn beperkt tot 30 peulen per knoop punt wanneer u implementeert met behulp van de Azure Portal.

### <a name="configure-maximum---existing-clusters"></a>Maxi maal bestaande clusters configureren

De instelling maxPod per knoop punt kan worden gedefinieerd wanneer u een nieuwe knooppunt groep maakt. Als u de maxPod per knooppunt instelling wilt verhogen op een bestaand cluster, voegt u een nieuwe knooppunt groep toe met het nieuwe gewenste maxPod aantal. Nadat u uw peul hebt gemigreerd naar de nieuwe groep, verwijdert u de oudere groep. Als u een oudere groep in een cluster wilt verwijderen, moet u ervoor zorgen dat u de modi van de groep knoop punten instelt zoals gedefinieerd in het [document met groeps beleidspunten][system-node-pools].

## <a name="deployment-parameters"></a>Implementatieparameters

Wanneer u een AKS-cluster maakt, kunnen de volgende para meters worden geconfigureerd voor Azure CNI-netwerken:

**Virtueel netwerk**: het virtuele netwerk waarin u het Kubernetes-cluster wilt implementeren. Als u een nieuw virtueel netwerk voor uw cluster wilt maken, selecteert u *nieuwe maken* en volgt u de stappen in de sectie *virtueel netwerk maken* . Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)voor meer informatie over de limieten en quota's voor een virtueel Azure-netwerk.

**Subnet**: het subnet binnen het virtuele netwerk waar u het cluster wilt implementeren. Als u een nieuw subnet in het virtuele netwerk voor uw cluster wilt maken, selecteert u *nieuwe maken* en volgt u de stappen in de sectie *subnet maken* . Het adres bereik voor hybride connectiviteit mag niet overlappen met andere virtuele netwerken in uw omgeving.

**Adres bereik** van de Kubernetes-service: dit is de set virtuele IP-adressen die Kubernetes toewijst aan interne [Services][services] in uw cluster. U kunt elk persoonlijk adres bereik gebruiken dat voldoet aan de volgende vereisten:

* Mag zich niet binnen het IP-adres bereik van het virtuele netwerk van uw cluster bevallen
* Mag niet overlappen met andere virtuele netwerken waarmee de peers van het virtuele cluster netwerk
* Mag niet overlappen met on-premises Ip's
* Mag niet binnen het bereik `169.254.0.0/16` ,, `172.30.0.0/16` `172.31.0.0/16` , of `192.0.2.0/24`

Hoewel het technisch mogelijk is om een service adres bereik op te geven binnen hetzelfde virtuele netwerk als uw cluster, wordt dit niet aanbevolen. Onvoorspelbaar gedrag kan resulteren in het gebruik van overlappende IP-bereiken. Zie de sectie [Veelgestelde vragen](#frequently-asked-questions) van dit artikel voor meer informatie. Zie [Services][services] in de Kubernetes-documentatie voor meer informatie over Kubernetes Services.

**IP-adres van de DNS-service Kubernetes**: het IP-adres voor de DNS-service van het cluster. Dit adres moet binnen het *adresbereik van Kubernetes Service* liggen. Gebruik niet het eerste IP-adres in uw adres bereik, zoals 1. Het eerste adres in het bereik van uw subnet wordt gebruikt voor het adres *kubernetes. default. SVC. cluster. local* .

**Docker Bridge-adres**: het netwerk adres van docker-brug vertegenwoordigt het standaard *docker0* -brug netwerk adres dat aanwezig is in alle docker-installaties. Hoewel de *docker0* -brug niet wordt gebruikt door aks-clusters of de peul zelf, moet u dit adres instellen om te blijven ondersteuning bieden voor scenario's zoals *docker build* in het AKS-cluster. U moet een CIDR voor het netwerk adres van de docker-brug selecteren, omdat in een andere docker automatisch een subnet wordt gekozen dat strijdig zou kunnen zijn met andere CIDR. U moet een adres ruimte kiezen die niet in conflict is met de rest van de CIDR-Services in uw netwerken, met inbegrip van de service-CIDR van het cluster en pod CIDR.

## <a name="configure-networking---cli"></a>Netwerken configureren-CLI

Wanneer u een AKS-cluster maakt met de Azure CLI, kunt u ook Azure CNI-netwerken configureren. Gebruik de volgende opdrachten voor het maken van een nieuw AKS-cluster waarvoor Azure CNI-netwerken zijn ingeschakeld.

Haal eerst de bron-ID van het subnet op voor het bestaande subnet waarin het AKS-cluster wordt toegevoegd:

```azurecli-interactive
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query "[0].id" --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Gebruik de opdracht [AZ AKS Create][az-aks-create] met het `--network-plugin azure` argument om een cluster met geavanceerde netwerken te maken. Werk de `--vnet-subnet-id` waarde bij met de subnet-id die u in de vorige stap hebt verzameld:

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

## <a name="configure-networking---portal"></a>Netwerken configureren-Portal

In de volgende scherm afbeelding van de Azure Portal ziet u een voor beeld van het configureren van deze instellingen tijdens het maken van een AKS-cluster:

![Geavanceerde netwerk configuratie in de Azure Portal][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

De volgende vragen en antwoorden zijn van toepassing op de **Azure cni** -netwerk configuratie.

* *Kan ik Vm's in mijn cluster subnet implementeren?*

  Ja.

* *Wat is het bron-IP-adres dat externe systemen zien voor verkeer dat afkomstig is van een pod met Azure CNI-functionaliteit?*

  Systemen in hetzelfde virtuele netwerk als het AKS-cluster zien de pod-IP als bron adres voor elk verkeer van de pod. Systemen buiten het virtuele netwerk van het AKS-cluster zien het knoop punt-IP als het bron adres voor elk verkeer van de pod. 

* *Kan ik een per-pod-netwerk beleid configureren?*

  Ja, Kubernetes netwerk beleid is beschikbaar in AKS. Als u aan de slag wilt gaan, kunt u het [beveiligde verkeer tussen de twee gebruiken met behulp van netwerk beleid in AKS][network-policy].

* *Is het maximum aantal dat kan worden geïmplementeerd voor een knoop punt dat kan worden geconfigureerd?*

  Ja, wanneer u een cluster implementeert met de Azure CLI of een resource manager-sjabloon. Bekijk het [maximum aantal per knoop punt](#maximum-pods-per-node).

  U kunt het maximum aantal peulen per knoop punt op een bestaand cluster niet wijzigen.

* *Hoe kan ik extra eigenschappen configureren voor het subnet dat ik heb gemaakt tijdens het maken van het AKS-cluster? Bijvoorbeeld service-eind punten.*

  De volledige lijst met eigenschappen voor het virtuele netwerk en de subnetten die u maakt tijdens het maken van het AKS-cluster kan worden geconfigureerd op de pagina standaard virtuele netwerk configuratie in de Azure Portal.

* *Kan ik een ander subnet gebruiken in mijn cluster-virtueel netwerk voor het* **adres bereik** van de Kubernetes-service?

  Het wordt niet aanbevolen, maar deze configuratie is wel mogelijk. Het adres bereik van de service is een set virtuele IP-adressen (Vip's) die Kubernetes toewijst aan interne services in uw cluster. Azure Networking heeft geen zicht baarheid van het service-IP-bereik van het Kubernetes-cluster. Vanwege het ontbreken van zicht baarheid in het service adres bereik van het cluster is het mogelijk om later een nieuw subnet in het virtuele cluster netwerk te maken dat overlapt met het service adres bereik. Als er sprake is van een dergelijke overlap ping, kan Kubernetes een service toewijzen die al wordt gebruikt door een andere bron in het subnet, waardoor onvoorspelbaar gedrag of fouten optreden. Door ervoor te zorgen dat u een adres bereik gebruikt buiten het virtuele netwerk van het cluster, kunt u dit overlappings risico vermijden.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over netwerken in AKS vindt u in de volgende artikelen:

- [Gebruik een statisch IP-adres met de Azure Kubernetes-service (AKS) load balancer](static-ip.md)
- [Een interne load balancer gebruiken met Azure Container Service (AKS)](internal-lb.md)

- [Een eenvoudige ingangs controller met externe netwerk verbinding maken][aks-ingress-basic]
- [De invoeg toepassing voor het routeren van HTTP-toepassingen inschakelen][aks-http-app-routing]
- [Een ingangs controller maken die gebruikmaakt van een intern, privé netwerk en IP-adres][aks-ingress-internal]
- [Een ingangs controller maken met een dynamisch openbaar IP-adres en configureren laten versleutelen om automatisch TLS-certificaten te genereren][aks-ingress-tls]
- [Een ingangs controller met een statisch openbaar IP-adres maken en configureren laten versleutelen om automatisch TLS-certificaten te genereren][aks-ingress-static-tls]

### <a name="aks-engine"></a>AKS-engine

De [Azure Kubernetes service Engine (AKS-Engine)][aks-engine] is een open-source project dat Azure Resource Manager sjablonen genereert die u kunt gebruiken voor het implementeren van Kubernetes-clusters in Azure.

Kubernetes-clusters die zijn gemaakt met AKS-engine ondersteunen zowel de [kubenet][kubenet] -als [Azure cni][cni-networking] -invoeg toepassingen. Als zodanig worden beide netwerk scenario's ondersteund door de AKS-engine.

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
[network-policy]: use-network-policies.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[network-comparisons]: concepts-network.md#compare-network-models
[system-node-pools]: use-system-pools.md

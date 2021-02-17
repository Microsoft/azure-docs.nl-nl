---
title: Veelgestelde vragen over Windows Server-knooppunt groepen
titleSuffix: Azure Kubernetes Service
description: Raadpleeg de veelgestelde vragen over het uitvoeren van Windows Server-knooppunt groepen en-werk belastingen in azure Kubernetes service (AKS).
services: container-service
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: b20ebe82556bb4db6844511ec0953f4d4e75f383
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100574735"
---
# <a name="frequently-asked-questions-for-windows-server-node-pools-in-aks"></a>Veelgestelde vragen over Windows Server-knooppunt groepen in AKS

In azure Kubernetes service (AKS) kunt u een knooppunt groep maken waarop Windows Server als het gast besturingssysteem op de knoop punten wordt uitgevoerd. Deze knoop punten kunnen systeem eigen Windows-container toepassingen uitvoeren, zoals die zijn gebaseerd op de .NET Framework. Er zijn verschillen in de manier waarop het Linux-en Windows-besturings systeem ondersteuning bieden voor containers. Sommige algemene Linux Kubernetes-en pod-gerelateerde functies zijn momenteel niet beschikbaar voor Windows-knooppunt groepen.

In dit artikel vindt u een overzicht van een aantal veelgestelde vragen en besturingssysteem concepten voor Windows Server-knoop punten in AKS.

## <a name="which-windows-operating-systems-are-supported"></a>Welke Windows-besturings systemen worden ondersteund?

AKS maakt gebruik van Windows Server 2019 als de versie van het besturings systeem van de host en ondersteunt alleen isolatie van processen. Container installatie kopieën die zijn gemaakt met andere versies van Windows Server, worden niet ondersteund. Zie compatibiliteit met Windows- [container versie][windows-container-compat]voor meer informatie.

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Is Kubernetes anders in Windows en Linux?

Ondersteuning voor de venster Server knooppunt groep bevat enkele beperkingen die deel uitmaken van de upstream-Windows-Server in Kubernetes-project. Deze beperkingen zijn niet specifiek voor AKS. Zie de sectie [ondersteunde functionaliteit en beperkingen][upstream-limitations] van het project [Inleiding tot Windows-ondersteuning in Kubernetes][intro-windows] -document in het Kubernetes-bestand voor meer informatie over deze ondersteuning voor upstreams voor Windows Server in Kubernetes.

Kubernetes is historisch gericht op Linux. Veel voor beelden die worden gebruikt in de upstream [Kubernetes.io][kubernetes] -website zijn bedoeld voor gebruik op Linux-knoop punten. Wanneer u implementaties maakt die gebruikmaken van Windows Server-containers, gelden de volgende overwegingen op het niveau van het besturings systeem:

- **Identiteit** -Linux identificeert een gebruiker op basis van een gebruikers-id (UID) van een geheel getal. Een gebruiker heeft ook een alfanumerieke gebruikers naam voor aanmelding, die Linux vertaalt naar de UID van de gebruiker. Net als Linux wordt een gebruikers groep aangeduid met een groeps-id (GID) en wordt een groeps naam omgezet in de bijbehorende GID.
    - Windows Server gebruikt een grotere binaire beveiligings-id (SID) die is opgeslagen in de SAM-data base (Windows Security Access Manager). Deze data base wordt niet gedeeld tussen de host en containers of tussen containers.
- **Bestands machtigingen** : Windows Server gebruikt een toegangs beheer lijst op basis van sid's, in plaats van een bitmasker van machtigingen en UID + GID
- **Bestands paden** : de Conventie voor Windows Server is door gebruik te gebruiken in plaats van/.
    - In pod-specificaties die volumes koppelen, geeft u het pad correct voor Windows Server-containers op. Geef bijvoorbeeld in plaats van een koppel punt van */mnt/volume* in een Linux-container een stationsletter en locatie op, zoals */K/volume* , die moeten worden gekoppeld als het station *K:* .

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Wat voor soort schijven worden ondersteund voor Windows?

Azure-schijven en-Azure Files zijn de ondersteunde volume typen. Deze zijn toegankelijk als NTFS-volumes in de Windows Server-container.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>Kan ik alleen Windows-clusters in AKS uitvoeren?

De hoofd knooppunten (het besturings vlak) in een AKS-cluster worden gehost door AKS de service. u wordt niet blootgesteld aan het besturings systeem van de knoop punten die de hoofd onderdelen hosten. Alle AKS-clusters worden gemaakt met een standaard eerste knooppunt groep, die is gebaseerd op Linux. Deze knooppunt groep bevat systeem services, die nodig zijn voor het functioneren van het cluster. Het is raadzaam ten minste twee knoop punten in de eerste knooppunt groep uit te voeren om de betrouw baarheid van uw cluster en de mogelijkheid om cluster bewerkingen te kunnen garanderen. De eerste knooppunt groep op basis van Linux kan alleen worden verwijderd als het AKS-cluster zelf is verwijderd.

## <a name="how-do-i-patch-my-windows-nodes"></a>Hoe kan ik patch mijn Windows-knoop punten?

Als u de nieuwste patches voor Windows-knoop punten wilt downloaden, kunt u [de knooppunt groep upgraden][nodepool-upgrade] of [de knooppunt installatie kopie bijwerken][upgrade-node-image]. Windows-updates zijn niet ingeschakeld op knoop punten in AKS. AKS geeft een nieuwe installatie kopie van de knooppunt groep vrij zodra er patches beschikbaar zijn. het is de verantwoordelijkheid van de gebruiker om de knooppunt groepen bij te werken om op de hoogte te blijven van patches en hotfixes. Dit geldt ook voor het gebruik van de Kubernetes-versie. [Release opmerkingen bij AKS][aks-release-notes] geven aan wanneer er nieuwe versies beschikbaar zijn. Zie [een knooppunt groep bijwerken in AKS][nodepool-upgrade]voor meer informatie over het upgraden van de hele Windows Server-knooppunt groep. Zie [AKS-knooppunt installatie kopieën][upgrade-node-image]als u alleen de installatie kopie van het knoop punt wilt bijwerken.

> [!NOTE]
> De bijgewerkte installatie kopie van Windows Server wordt alleen gebruikt als er een upgrade van een cluster is uitgevoerd voordat de knooppunt groep werd geüpgraded.
>

## <a name="what-network-plug-ins-are-supported"></a>Welke netwerk-invoeg toepassingen worden ondersteund?

AKS-clusters met Windows-knooppunt Pools moeten het netwerk model van Azure CNI (Geavanceerd) gebruiken. Kubenet (Basic)-netwerken worden niet ondersteund. Zie [Network concepten for Applications in AKS][azure-network-models](Engelstalig) voor meer informatie over de verschillen in netwerk modellen. Het Azure CNI-netwerk model vereist aanvullende planning en overwegingen voor het beheer van IP-adressen. Zie [Azure cni-netwerken configureren in AKS][configure-azure-cni]voor meer informatie over het plannen en implementeren van Azure cni.

## <a name="is-preserving-the-client-source-ip-supported"></a>Wordt het IP-adres van de client ondersteund?

Op dit moment wordt het [IP-behoud van client bronnen][client-source-ip] niet ondersteund met Windows-knoop punten.

## <a name="can-i-change-the-max--of-pods-per-node"></a>Kan ik het maximum aantal van het aantal peulen per knoop punt wijzigen?

Ja. Zie [maximum aantal peulen][maximum-number-of-pods]voor de implicaties en opties die beschikbaar zijn.

## <a name="why-am-i-seeing-an-error-when-i-try-to-create-a-new-windows-agent-pool"></a>Waarom wordt er een fout bericht weer gegeven wanneer ik een nieuwe groep van Windows agent probeer te maken?

Als u uw cluster hebt gemaakt vóór 2020 februari en nog nooit cluster upgrade bewerkingen hebt uitgevoerd, maakt het cluster toch gebruik van een oude Windows-installatie kopie. Mogelijk is er een fout melding weer gegeven die er ongeveer als volgt uitziet:

De volgende lijst met installatie kopieën waarnaar wordt verwezen vanuit de implementatie sjabloon, is niet gevonden: Publisher: MicrosoftWindowsServer, offer: WindowsServer, SKU: 2019-Data Center-core-smalldisk-2004, version: Latest. Raadpleeg https://docs.microsoft.com/azure/virtual-machines/windows/cli-ps-findimage voor instructies voor het zoeken naar beschik bare installatie kopieën.

U kunt deze fout als volgt oplossen:

1. Werk het [cluster besturings vlak][upgrade-cluster-cp] bij om de afbeeldings aanbieding en de uitgever bij te werken.
1. Nieuwe Windows-agent groepen maken.
1. Verplaats Windows-Peul van bestaande Windows-agent groepen naar nieuwe Windows-agent groepen.
1. Oude groepen van Windows-agents verwijderen.

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>Hoe kan ik de service-principal voor mijn Windows-knooppunt groep draaien?

Windows-knooppunt groepen bieden geen ondersteuning voor het draaien van service-principals. Als u de Service-Principal wilt bijwerken, maakt u een nieuwe Windows-knooppunt groep en migreert u uw Peul van de oudere groep naar het nieuwe. Wanneer dit is voltooid, verwijdert u de oude knooppunt groep.

Gebruik in plaats daarvan beheerde identiteiten, die in feite wrappers rond service-principals zijn. Zie [beheerde identiteiten gebruiken in azure Kubernetes service][managed-identity]voor meer informatie.

## <a name="how-many-node-pools-can-i-create"></a>Hoeveel knooppunt groepen kan ik maken?

Het AKS-cluster kan Maxi maal 10 knooppunt groepen bevatten. U kunt Maxi maal 1000 knoop punten in deze knooppunt groepen hebben. [Beperkingen van de knooppunt groep][nodepool-limitations].

## <a name="what-can-i-name-my-windows-node-pools"></a>Wat kan ik mijn Windows-knooppunt groepen noemen?

U moet de naam Maxi maal 6 (zes) tekens gebruiken. Dit is een huidige beperking van AKS.

## <a name="are-all-features-supported-with-windows-nodes"></a>Worden alle functies ondersteund met Windows-knoop punten?

Netwerk beleid en kubenet worden momenteel niet ondersteund met Windows-knoop punten.

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Kan ik ingangs controllers uitvoeren op Windows-knoop punten?

Ja, een ingangs-controller die Windows Server-containers ondersteunt, kan worden uitgevoerd op Windows-knoop punten in AKS.

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>Kan ik Azure dev Spaces gebruiken met Windows-knoop punten?

Azure dev Spaces is momenteel alleen beschikbaar voor knooppunt groepen op basis van Linux.

## <a name="can-my-windows-server-containers-use-gmsa"></a>Kan mijn Windows Server-containers gMSA gebruiken?

GMSA-ondersteuning (Group managed service accounts) is momenteel niet beschikbaar in AKS.

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>Kan ik Azure Monitor gebruiken voor containers met Windows-knoop punten en containers?

Ja, u kunt Azure Monitor echter een open bare preview hebben voor het verzamelen van Logboeken (stdout, stderr) en metrische gegevens van Windows-containers. U kunt ook koppelen aan de live stream van stdout-logboeken vanuit een Windows-container.

## <a name="are-there-any-limitations-on-the-number-of-services-on-a-cluster-with-windows-nodes"></a>Zijn er beperkingen voor het aantal services op een cluster met Windows-knoop punten?

Een cluster met Windows-knoop punten kan ongeveer 500 Services hebben voordat de poort wordt uitgeput.

## <a name="can-i-use-azure-hybrid-benefit-with-windows-nodes"></a>Kan ik Azure Hybrid Benefit gebruiken met Windows-knoop punten?

Ja. Azure Hybrid Benefit voor Windows Server reduceert de exploitatie kosten door uw on-premises Windows Server-licentie naar AKS Windows-knoop punten te brengen.

Azure Hybrid Benefit kunnen worden gebruikt in uw hele AKS-cluster of op afzonderlijke knoop punten. Voor afzonderlijke knoop punten moet u naar de [resource groep knoop punt][resource-groups] navigeren en de Azure Hybrid Benefit rechtstreeks op de knoop punten Toep assen. Zie [Azure Hybrid Benefit voor Windows Server][hybrid-vms]voor meer informatie over het Toep assen van Azure Hybrid Benefit op afzonderlijke knoop punten. 

Gebruik het argument om Azure Hybrid Benefit te gebruiken op een nieuw AKS-cluster `--enable-ahub` .

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku Standard \
    --windows-admin-password 'Password1234$' \
    --windows-admin-username azure \
    --network-plugin azure
    --enable-ahub
```

Als u Azure Hybrid Benefit wilt gebruiken op een bestaand AKS-cluster, moet u het cluster bijwerken met behulp van het `--enable-ahub` argument.

```azurecli
az aks update \
    --resource-group myResourceGroup
    --name myAKSCluster
    --enable-ahub
```

Als u wilt controleren of Azure Hybrid Benefit is ingesteld op het cluster, gebruikt u de volgende opdracht:

```azurecli
az vmss show --name myAKSCluster --resource-group MC_CLUSTERNAME
```

Als het cluster Azure Hybrid Benefit ingeschakeld, ziet de uitvoer van `az vmss show` er ongeveer als volgt uit:

```console
"platformFaultDomainCount": 1,
  "provisioningState": "Succeeded",
  "proximityPlacementGroup": null,
  "resourceGroup": "MC_CLUSTERNAME"
```

## <a name="can-i-use-the-kubernetes-web-dashboard-with-windows-containers"></a>Kan ik het Kubernetes-webdashboard met Windows-containers gebruiken?

Ja, u kunt het [Kubernetes-Webdash Board][kubernetes-dashboard] gebruiken om toegang te krijgen tot informatie over Windows-containers, maar op dit moment kunt u *kubectl exec* niet rechtstreeks vanuit het Kubernetes Web dash board uitvoeren in een actieve Windows-container. Zie [verbinding maken met RDP naar Azure Kubernetes service (AKS) cluster Windows Server-knoop punten voor onderhoud of probleem oplossing][windows-rdp]voor meer informatie over het maken van een verbinding met uw actieve Windows-container.

## <a name="what-if-i-need-a-feature-thats-not-supported"></a>Wat moet ik doen als ik een functie nodig die niet wordt ondersteund?

We werken hard om alle functies te gebruiken die u nodig hebt voor Windows in AKS, maar als er hiaten optreden, biedt het open source-upstream [-AKS-engine-][aks-engine] project een eenvoudige en volledig aanpas bare manier om Kubernetes in azure uit te voeren, waaronder Windows-ondersteuning. Bekijk ons overzicht van de functies van het AKS- [schema][aks-roadmap].

## <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan met Windows Server-containers in AKS, [maakt u een knooppunt groep die Windows Server in AKS uitvoert][windows-node-cli].

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1
[aks-release-notes]: https://github.com/Azure/AKS/releases

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[upgrade-cluster]: upgrade-cluster.md
[upgrade-cluster-cp]: use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
[maximum-number-of-pods]: configure-azure-cni.md#maximum-pods-per-node
[azure-monitor]: ../azure-monitor/containers/container-insights-overview.md#what-does-azure-monitor-for-containers-provide
[client-source-ip]: concepts-network.md#ingress-controllers
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-rdp]: rdp.md
[upgrade-node-image]: node-image-upgrade.md
[managed-identity]: use-managed-identity.md
[hybrid-vms]: ../virtual-machines/windows/hybrid-use-benefit-licensing.md
[resource-groups]: faq.md#why-are-two-resource-groups-created-with-aks

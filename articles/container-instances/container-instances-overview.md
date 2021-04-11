---
title: Serverloze containers in Azure
description: De Azure Container Instances-service is de snelste en eenvoudigste methode voor het uitvoeren van geïsoleerde containers in Azure. Hierbij hoeft u geen virtuele machines te beheren of een orchestrator op een hoger niveau te gebruiken.
ms.topic: overview
ms.date: 03/22/2021
ms.custom: seodec18, mvc
ms.openlocfilehash: c445687db7a154b6fc86e962d2c2340ad6297431
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104799068"
---
# <a name="what-is-azure-container-instances"></a>Wat is Azure Container Instances?

Containers worden de voorkeursmethode voor het verpakken, implementeren en beheren van cloudtoepassingen. Azure Container Instances is de snelste en meest eenvoudige methode voor het uitvoeren van een container in Azure, waarbij het niet nodig is om virtuele machines te beheren of het serviceniveau te verhogen.

Azure Container Instances is een ideale oplossing voor elk scenario dat kan werken in geïsoleerde containers, met inbegrip van eenvoudige toepassingen, taakautomatisering en het bouwen van taken. Voor scenario's waarin u een volledige indeling met containers nodig hebt, waaronder servicedetectie in meerdere containers, automatisch schalen en gecoördineerde toepassingsupgrades, raden wij [Azure Kubernetes Service (AKS)](../aks/index.yml) aan.

## <a name="fast-startup-times"></a>Snel opstarten

Containers bieden aanzienlijke opstartvoordelen ten opzichte van virtuele machines (VM’s). Met Azure Container Instances kunnen containers in Azure in enkele seconden worden gestart, zonder dat u VM’s hoeft in te richten en te beheren.

U kunt Linux- of Windows-containerinstallatiekopieën halen uit Docker Hub, een privé [Azure-containerregister](../container-registry/index.yml), of een ander clouddockerregister. Ga naar de [Veelgestelde vragen](container-instances-faq.md) voor meer informatie over welke registers door ACI worden ondersteund. Azure Container Instances slaat verschillende veelgebruikte basisbesturingssysteeminstallatiekopieën op in het cachegeheugen, waardoor de implementatie van uw aangepaste app-installatiekopieën sneller verloopt.

## <a name="container-access"></a>Toegang tot container

Met Azure Container Instances kunt u uw containergroepen direct weergeven op internet met een IP-adres en een Fully Qualified Domain Name. Wanneer u een containerexemplaar maakt, kunt u een aangepast DNS-naamlabel opgeven, zodat uw toepassing bereikbaar is via *aangepastlabel*.*azureregio*.azurecontainer.io.

Azure Container Instances biedt ook ondersteuning voor het uitvoeren van een opdracht in een actieve container via een interactieve shell die u helpt bij het ontwikkelen en oplossen van toepassingen. Toegang vindt plaats via HTTPS, met behulp van TLS om clientverbindingen te beveiligen.

> [!IMPORTANT]
> Vanaf 13 januari 2020 moeten voor Azure Container Instances alle beveiligde verbindingen van servers en toepassingen TLS 1.2 gebruiken. Ondersteuning voor TLS 1.0 en 1.1 wordt buiten gebruik gesteld.

## <a name="compliant-deployments"></a>Compatibele implementaties

### <a name="hypervisor-level-security"></a>Beveiliging op hypervisorniveau

In het verleden boden containers toepassingsafhankelijke isolatie en resourcebesturing, maar werden ze niet voldoende veilig beschouwd voor onveilig multitenant gebruik. Met Azure Container Instances is uw toepassing gegarandeerd even geïsoleerd in een container als deze op een VM zou zijn.

### <a name="customer-data"></a>Klantgegevens

De ACI-service slaat de klantgegevens op die minimaal nodig zijn om ervoor te zorgen dat de containergroepen zoals verwacht worden uitgevoerd. Het opslaan van klant gegevens in één regio is momenteel alleen beschikbaar in de regio Zuid-Azië (Singapore) van de Azië en Stille Oceaan geo-en Brazilië-zuid (Sao Paulo-staat) van Brazilië geo. Voor alle andere regio's worden klantgegevens opgeslagen in [Geo](https://azure.microsoft.com/global-infrastructure/geographies/). Neem contact op met de ondersteuning voor Azure voor meer informatie.

## <a name="custom-sizes"></a>Aangepaste grootten

Containers zijn meestal geoptimaliseerd voor het uitvoeren van één toepassing, maar de exacte behoeften van deze toepassingen kunnen aanzienlijk verschillen. Azure Container Instances biedt een optimaal gebruik door exacte specificaties van CPU-kernen en -geheugen toe te staan. U betaalt op basis van wat u nodig hebt en wordt per seconde gefactureerd, zodat u uw uitgaven kunt verfijnen op basis van werkelijke behoefte.

Voor rekenintensieve taken zoals machine learning kunnen met Azure Container Instances Linux-containers worden ingesteld om [GPU-resources](container-instances-gpu.md) (preview) van NVIDIA Tesla te gebruiken.

## <a name="persistent-storage"></a>Permanente opslag

Voor het ophalen en persisteren van statuswaarden met exemplaren van Azure Container Instances, biedt Microsoft direct [koppelen van Azure Files-shares](./container-instances-volume-azure-files.md), ondersteund door Azure Storage.

## <a name="linux-and-windows-containers"></a>Linux- en Windows-containers

Met Azure Container Instances kunt u Windows- en Linux-containers met dezelfde API plannen. Geef gewoon het type besturingssysteem op wanneer u de [containergroepen](container-instances-container-groups.md) maakt.

Sommige functies zijn momenteel beperkt tot Linux-containers:

* Meerdere containers per containergroep
* Volume koppelen ([Azure Files](container-instances-volume-azure-files.md), [emptyDir](container-instances-volume-emptydir.md), [GitRepo](container-instances-volume-gitrepo.md),[ geheim](container-instances-volume-secret.md))
* [Metrische gebruiksgegevens voor resources](container-instances-monitor.md) met Azure Monitor
* [Implementatie van virtuele netwerken](container-instances-vnet.md)
* [GPU-resources](container-instances-gpu.md) (preview-versie)

Gebruik voor implementaties van Windows-containers installatiekopieën op basis van algemene [Windows-installatiekopieën](container-instances-faq.md#what-windows-base-os-images-are-supported).

## <a name="co-scheduled-groups"></a>Samen geplande groepen

Azure Container Instances biedt ondersteuning voor planning van [meerdere containergroepen](container-instances-container-groups.md) die een hostmachine, lokaal netwerk, opslag en levenscyclus delen. Hierdoor kunt u uw hoofdtoepassingscontainer combineren met andere ondersteunende rolcontainers, zoals het in een logboek vastleggen van sidecars.

## <a name="virtual-network-deployment"></a>Implementatie van virtuele netwerken

Azure Container Instances maakt [de implementatie van containerinstanties in een virtueel netwerk in Azure](container-instances-vnet.md) mogelijk. Als containerinstanties zijn geïmplementeerd in een subnet in uw virtuele netwerk, kunnen ze veilig communiceren met andere resources in het virtuele netwerk, waaronder on-premises resources (via [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) of [ ExpressRoute](../expressroute/expressroute-introduction.md)).

## <a name="next-steps"></a>Volgende stappen

Probeer met behulp van onze snelstart een container te implementeren in Azure met een enkele opdracht:

> [!div class="nextstepaction"]
> [Snelstart voor Azure Container Instances](container-instances-quickstart.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

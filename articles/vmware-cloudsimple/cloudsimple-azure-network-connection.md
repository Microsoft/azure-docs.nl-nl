---
title: VMware-oplossing door CloudSimple-Azure-netwerk verbindingen
description: Meer informatie over het verbinden van uw virtuele Azure-netwerk met uw CloudSimple-regio netwerk
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cfd4d65b07cf255ac2b60d6bf8376723a997374e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "77025075"
---
# <a name="azure-network-connections-overview"></a>Overzicht van Azure-netwerk verbindingen

Wanneer u een CloudSimple-service in een regio maakt en knoop punten maakt, kunt u het volgende doen:

* Vraag een Azure ExpressRoute-circuit aan en koppel deze aan het CloudSimple-netwerk in die regio.
* Verbind uw CloudSimple Region-netwerk met uw virtuele Azure-netwerk of uw on-premises netwerk met behulp van Azure ExpressRoute.
* Toegang bieden tot services die worden uitgevoerd in uw Azure-abonnement of uw on-premises netwerk vanuit uw Privécloud.

De ExpressRoute-verbinding is een hoge band breedte met een lage latentie.

## <a name="benefits"></a>Voordelen

Met Azure-netwerk verbinding kunt u:

* Gebruik Azure als back-updoel voor virtuele machines in uw Privécloud.
* Implementeer KMS-servers in uw Azure-abonnement om uw vSAN-gegevens opslag in de Privécloud te versleutelen.
* Gebruik hybride toepassingen waarbij de weblaag van de toepassing wordt uitgevoerd in de open bare Cloud terwijl de lagen van de toepassing en de data base in uw Privécloud worden uitgevoerd.

## <a name="azure-virtual-network-connection"></a>Virtuele Azure-netwerk verbinding

Persoonlijke Clouds kunnen worden verbonden met uw Azure-resources met behulp van ExpressRoute.  Met de ExpressRoute-verbinding kunt u vanuit uw Privécloud toegang krijgen tot resources die worden uitgevoerd in uw Azure-abonnement.  Met deze verbinding kunt u uw particuliere cloud netwerk uitbreiden naar uw virtuele Azure-netwerk.  Routes van het CloudSimple-netwerk worden via BGP uitgewisseld met uw virtuele Azure-netwerk.  Als u virtuele netwerk peering hebt geconfigureerd, zijn alle gekoppelde virtuele netwerken toegankelijk vanuit uw CloudSimple-netwerk.

![Azure ExpressRoute-verbinding met virtueel netwerk](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute-verbinding met een on-premises netwerk

U kunt uw bestaande Azure ExpressRoute-circuit aansluiten op uw CloudSimple-regio. De functie ExpressRoute Global Reach wordt gebruikt om de twee circuits met elkaar te verbinden.  Er wordt een verbinding tot stand gebracht tussen de on-premises en CloudSimple ExpressRoute-circuits.  Deze verbinding stelt u in staat om uw on-premises netwerken uit te breiden naar een particulier Cloud netwerk. Routes van uw CloudSimple-netwerk worden uitgewisseld via BGP met uw on-premises netwerk.

![On-premises ExpressRoute-verbinding-Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>Verbinding met een on-premises netwerk en een virtueel Azure-netwerk

Verbindingen met een on-premises netwerk en een virtueel Azure-netwerk kunnen worden gecombineerd met uw CloudSimple-netwerk.  De verbinding maakt gebruik van BGP voor het uitwisselen van routes tussen on-premises netwerk, het virtuele netwerk van Azure en het CloudSimple-netwerk.  Wanneer u uw CloudSimple-netwerk verbindt met uw virtuele Azure-netwerk in aanwezigheid van Global Reach verbinding, worden de routes van het virtuele Azure-netwerk weer gegeven in uw on-premises netwerk.  Route uitwisseling vindt plaats tussen de Edge-routers in Azure.

![On-premises ExpressRoute-verbinding met Azure Virtual Network-verbinding](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>Belangrijke overwegingen

Als u verbinding maakt met een CloudSimple-netwerk vanuit een on-premises netwerk en vanuit een virtueel Azure-netwerk, kan route uitwisseling tussen alle netwerken worden ingeschakeld.

* Het virtuele netwerk van Azure is zichtbaar voor zowel on-premises netwerk als CloudSimple-netwerk.
* Als u verbinding hebt gemaakt met uw virtuele Azure-netwerk vanuit een on-premises netwerk, is verbinding met CloudSimple-netwerk via Global Reach toegang tot virtuele netwerken vanuit CloudSimple-netwerk mogelijk.
* Subnetten **mogen niet** overlappen tussen de netwerken die zijn verbonden.
* CloudSimple adverteert **geen** standaard route naar de ExpressRoute-verbindingen
* Als uw on-premises router de standaard route adverteert, zal het verkeer van het CloudSimple-netwerk en het virtuele netwerk van Azure gebruikmaken van de geadverteerde standaard route.  Als gevolg hiervan is het niet mogelijk om met open bare IP-adressen toegang te krijgen tot virtuele machines in Azure.

## <a name="next-steps"></a>Volgende stappen

* [Virtueel Azure-netwerk verbinden met CloudSimple met behulp van ExpressRoute](virtual-network-connection.md)
* [Verbinding maken tussen on-premises en CloudSimple met behulp van ExpressRoute](on-premises-connection.md)

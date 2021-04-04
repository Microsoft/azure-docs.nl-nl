---
title: Verbinding maken tussen Azure en open bare Clouds | Microsoft Docs
description: Verschillende manieren voor het verbinden van Azure met andere open bare Clouds beschrijven
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 07/24/2019
ms.author: duau
ms.openlocfilehash: fb4c34345ab902e243f22eae06fd5ea174fd2992
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92202188"
---
# <a name="connecting-azure-with-public-clouds"></a>Azure verbinden met open bare Clouds

Veel ondernemingen beleven een strategie voor meerdere clouds vanwege zakelijke en technische doelen. Dit zijn onder andere kosten, flexibiliteit, Beschik baarheid van functies, redundantie, data soevereiniteit, enzovoort. Deze strategie helpt hen het beste van beide Clouds te benutten. 

Deze benadering vormt ook uitdagingen voor de onderneming in termen van netwerk-en toepassings architectuur. Enkele van deze uitdagingen zijn latentie en gegevens doorvoer. Om deze uitdagingen te verhelpen, is het aan te raden om rechtstreeks verbinding te maken met meerdere clouds. Sommige service providers bieden een oplossing voor het verbinden van meerdere cloud providers voor de klanten. In andere gevallen kan de klant hun eigen router implementeren om meerdere open bare Clouds te verbinden.
## <a name="connectivity-via-expressroute"></a>Connectiviteit via ExpressRoute
Met ExpressRoute kunnen klanten hun on-premises netwerken uitbreiden naar de micro soft-Cloud via een persoonlijke verbinding die wordt vereenvoudigd door een connectiviteits provider. Met ExpressRoute kunnen klanten verbindingen tot stand brengen met micro soft-Cloud Services.

Er zijn drie manieren om verbinding te maken via ExpressRoute.

1. Layer3-provider
2. Layer2-provider
3. Directe verbinding

### <a name="layer3-provider"></a>Layer3-provider

Layer3-providers worden meestal IP VPN-of MPLS-VPN-providers genoemd. Klanten maken gebruik van deze providers voor multi point connectiviteit tussen hun data centers, vertakkingen en de Cloud. Klanten maken verbinding met de L3-provider via BGP of via statische standaard route. Service provider adverteert routes tussen de sites van de klant, data centers en open bare Cloud. 
 
Wanneer u verbinding maakt via een Layer3-provider, adverteert micro soft klant VNET-routes naar de service provider via BGP. De provider kan twee verschillende implementaties hebben.

![Diagram waarin een Layer3-provider wordt weer gegeven.](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l3.png)

Provider kan elke Cloud provider in een afzonderlijke VRF uitbrengen als verkeer van alle cloud providers op de klant router wordt bereikt. Als de klant BGP met service provider uitvoert, worden deze routes standaard opnieuw aangekondigd aan andere cloud providers. 

Als de service provider alle cloud providers in hetzelfde VRF afgeeft, worden routes rechtstreeks naar andere cloud providers van de service provider geadverteerd. Hierbij wordt uitgegaan van een standaard BGP-bewerking waarbij eBGP-routes standaard worden geadverteerd naar andere eBGP-neighbors.

Elke open bare Cloud heeft een andere voorvoegsel limiet, dus bij het distribueren van de routes service provider moet er rekening mee worden gehouden bij het distribueren van de routes.

### <a name="layer2-provider-and-direct-connection"></a>Layer2-provider en directe verbinding

Hoewel fysieke connectiviteit in beide modellen anders is, maar op Layer3 BGP direct wordt ingesteld tussen MSEE en de klant router. Voor ExpressRoute direct-klanten maakt rechtstreeks verbinding met MSEE. In het geval van Layer2 breidt de service provider VLAN uit van de lokale locatie van de klant naar de Cloud. Klanten voeren BGP uit op het Layer2-netwerk om hun Dc's te verbinden met de Cloud.
![Diagram waarin een Layer2-provider en directe verbinding worden weer gegeven.](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l2.png)
In beide gevallen heeft de klant Point-to-point-verbindingen met elk van de open bare Clouds. De klant brengt een afzonderlijke BGP-verbinding tot stand met elke open bare Cloud. Routes die door een Cloud provider worden ontvangen, worden standaard naar een andere Cloud provider geadverteerd. Elke Cloud provider heeft een andere voorvoegsel limiet, zodat de routes die de klant aankondigen, deze limieten moeten overnemen. Klanten kunnen gebruikelijke BGP-knoppen met micro soft gebruiken tijdens het adverteren van routes van andere open bare Clouds.

## <a name="direct-connection-with-expressroute"></a>Directe verbinding met ExpressRoute

Klanten kunnen ervoor kiezen om ExpressRoute rechtstreeks te verbinden met de directe connectiviteits aanbieding van de Cloud provider. Er worden twee cloud providers verbonden met de achtergrond en BGP wordt direct tot stand gebracht tussen hun routers. Dit type verbinding is vandaag beschikbaar met Oracle.

## <a name="site-to-site-vpn"></a>Site-naar-site-VPN

Klanten kunnen gebruikmaken van Internet om hun instanties in azure te verbinden met andere open bare Clouds. Bijna alle cloud providers bieden mogelijkheden voor site-naar-site-VPN. Er kunnen echter incompatibiliteits problemen zijn door bepaalde varianten te missen. Sommige cloud providers ondersteunen bijvoorbeeld alleen IKEv1, dus er is een VPN-eind punt vereist in die Cloud. Voor cloud providers die IKEv2 ondersteunen, kan verbinding worden gemaakt tussen VPN-gateways bij beide cloud providers.

Site-naar-site-VPN wordt niet beschouwd als een oplossing met hoge door Voer en lage latentie. Het kan echter worden gebruikt als back-up naar fysieke connectiviteit.

## <a name="next-steps"></a>Volgende stappen
Zie [Veelgestelde vragen over ExpressRoute][ER-FAQ] voor meer vragen over de connectiviteit van ExpressRoute en het virtuele netwerk.

Zie [een rechtstreekse verbinding tussen Azure en Oracle-Cloud instellen][ER-OCI] voor connectiviteit tussen Azure en Oracle

<!--Link References-->
[ER-FAQ]: ./expressroute-faqs.md
[ER-OCI]: ../virtual-machines/workloads/oracle/configure-azure-oci-networking.md
---
title: Zakelijke continuïteit van virtueel netwerk | Microsoft Docs
description: Meer informatie over wat u moet doen in het geval van een Azure-service die invloed heeft op Azure Virtual Networks.
services: virtual-network
documentationcenter: ''
author: NarayanAnnamalai
manager: jefco
editor: ''
ms.assetid: ad260ab9-d873-43b3-8896-f9a1db9858a5
ms.service: virtual-network
ms.workload: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: narayan
ms.reviewer: aglick
ms.openlocfilehash: 3f91d24bff0bec540ff0e7964f21c2f47c03638c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "67876176"
---
# <a name="virtual-network--business-continuity"></a>Virtual Network: bedrijfs continuïteit

## <a name="overview"></a>Overzicht
Een Virtual Network (VNet) is een logische weer gave van uw netwerk in de Cloud. Hiermee kunt u uw eigen privé-IP-adres ruimte definiëren en het netwerk in subnetten segmenteren. VNets fungeert als een grens van een vertrouwens relatie om uw reken resources te hosten, zoals Azure Virtual Machines en Cloud Services (Web/Worker-rollen). Met een VNet kunt u direct particuliere IP-communicatie tussen de resources die worden gehost. U kunt een virtueel netwerk koppelen aan een on-premises netwerk via een VPN Gateway of ExpressRoute.

Een VNet wordt binnen het bereik van een regio gemaakt. U kunt VNets *maken* met dezelfde adres ruimte in twee verschillende regio's (bijvoorbeeld VS-Oost en VS-West), maar omdat ze dezelfde adres ruimte hebben, kunt u ze niet samen verbinden. 

## <a name="business-continuity"></a>Bedrijfscontinuïteit

Er zijn verschillende manieren waarop uw toepassing kan worden onderbroken. Een regio kan volledig worden afgekapt als gevolg van een natuur ramp of een gedeeltelijk nood geval vanwege een fout in meerdere apparaten of services. De gevolgen voor de VNet-service verschillen in elk van deze situaties.

**V: wat moet ik doen als er een storing optreedt in een hele regio? Bijvoorbeeld, als een regio volledig is afgebroken vanwege een natuur ramp? Wat gebeurt er met de virtuele netwerken die in de regio worden gehost?**

A: het virtuele netwerk en de resources in het betrokken gebied blijven ontoegankelijk tijdens de onderbreking van de service.

![Eenvoudig Virtual Network diagram](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**V: wat kan ik doen om hetzelfde virtuele netwerk opnieuw te maken in een andere regio?**

A: virtuele netwerken zijn tamelijk licht gewicht bronnen. U kunt Azure-Api's aanroepen om een VNet te maken met dezelfde adres ruimte in een andere regio. Als u dezelfde omgeving die aanwezig was in de betrokken regio opnieuw wilt maken, maakt u API-aanroepen om de Cloud Services Web-en werk rollen en de virtuele machines die u had, opnieuw te implementeren. Als u een on-premises verbinding hebt, zoals in een hybride implementatie, moet u een nieuwe VPN Gateway implementeren en verbinding maken met uw on-premises netwerk.

Zie [een virtueel netwerk maken](manage-virtual-network.md#create-a-virtual-network)voor meer informatie over het maken van een virtueel netwerk.

**V: kan een replica van een VNet in een bepaalde regio van tevoren opnieuw worden gemaakt in een andere regio?**

A: Ja, u kunt twee VNets maken met behulp van dezelfde persoonlijke IP-adres ruimte en bronnen in twee verschillende regio's van tevoren. Als u op Internet gerichte Services in het VNet host, kunt u Traffic Manager instellen op geo-route verkeer naar de actieve regio. U kunt echter niet twee VNets met dezelfde adres ruimte verbinden met uw on-premises netwerk, omdat dit routerings problemen zou veroorzaken. Op het moment van een nood geval en verlies van een VNet in één regio, kunt u het andere VNet in de beschik bare regio verbinden met de overeenkomstige adres ruimte op uw on-premises netwerk.

Zie [een virtueel netwerk maken](manage-virtual-network.md#create-a-virtual-network)voor meer informatie over het maken van een virtueel netwerk.


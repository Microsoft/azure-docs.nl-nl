---
title: Netwerken en connectiviteit met Azure VMWare Solution
description: Beschrijving van netwerken en connectiviteit met Azure VMWare Solution.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 96dd93f1db5dc3ddcbb883313e19c6aed8a256da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462576"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware-oplossing biedt een persoonlijke cloud omgeving die toegankelijk is via on-premises en Azure-resources. Services zoals Azure ExpressRoute, VPN-verbindingen of Azure Virtual WAN bieden de connectiviteit. Voor het inschakelen van deze services zijn specifieke bereiken van netwerkadressen en firewallpoorten vereist.

Wanneer u een privécloud implementeert, worden er privénetwerken gemaakt voor beheer, inrichting en vMotion. Gebruik deze privénetwerken om toegang te krijgen tot vCenter en NSX-T Manager en vMotion of implementatie op virtuele machines.  

ExpressRoute Global Reach wordt gebruikt om privéclouds te verbinden met on-premises omgevingen. Voor de verbinding is een virtueel netwerk met een ExpressRoute-circuit naar on-premises in uw abonnement vereist.

Virtuele machines (Vm's) die in de privécloud zijn geïmplementeerd, zijn toegankelijk voor Internet via de open bare IP-functionaliteit van Azure Virtual WAN.  Internettoegang is standaard uitgeschakeld voor nieuwe privéclouds. Zie [Hoe gebruikt u de openbare IP-functionaliteit in Azure VMware Solution](../public-ip-usage.md) voor meer informatie.


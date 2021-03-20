---
title: Subnet-extensie in azure | Microsoft Docs
description: Meer informatie over de subnet-extensie in Azure.
services: virtual-network
documentationcenter: na
author: anupam-p
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2019
ms.author: anupand
ms.openlocfilehash: 555c903dc3375080a8b0d006d2aac7216854c939
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99539295"
---
# <a name="subnet-extension"></a>Subnetextensie
De migratie van werk belastingen naar de open bare Cloud vereist een zorgvuldige planning en coördinatie. Een van de belangrijkste overwegingen is de mogelijkheid om uw IP-adressen te bewaren. Dit kan bijzonder belang rijk zijn als uw toepassingen afhankelijk zijn van IP-adressen of als u nalevings vereisten hebt voor het gebruik van specifieke IP-adressen. Met Azure Virtual Network wordt dit probleem voor u opgelost, zodat u VNet en subnetten kunt maken met behulp van een IP-adres bereik van uw keuze.

Migraties kunnen een beetje lastig zijn wanneer de bovenstaande vereiste is gekoppeld aan een extra vereiste om een aantal toepassingen on-premises te hand haven. In een situatie moet u de toepassingen tussen Azure en on-premises splitsen zonder de IP-adressen aan beide zijden opnieuw te nummeren. Daarnaast moet u toestaan dat de toepassingen communiceren alsof ze zich in hetzelfde netwerk bevinden.

Een oplossing voor het bovenstaande probleem is een subnet-extensie. Door een netwerk uit te breiden, kunnen toepassingen via hetzelfde broadcast-domein communiceren wanneer ze op verschillende fysieke locaties bestaan, waardoor het niet nodig is om de netwerk topologie opnieuw te ontwerpen. 

Het uitbreiden van uw netwerk is niet een goede gewoonte in het algemeen, onder use-cases kan het nodig zijn.

- **Gefaseerde migratie**: het meest voorkomende scenario is dat u uw migratie wilt faseren. U wilt eerst een aantal toepassingen maken en de rest van de toepassingen naar Azure migreren.
- **Latentie**: vereisten voor lage latentie kunnen een andere reden zijn voor u om sommige toepassingen on-premises te houden om ervoor te zorgen dat ze zo dicht mogelijk bij uw Data Center zijn.
- **Naleving**: een ander gebruiks voorbeeld is dat u nalevings vereisten kunt hebben om een aantal van uw toepassingen on-premises te houden.
 
> [!NOTE] 
> U moet uw subnetten alleen uitbreiden als dat nodig is. In de gevallen waarin u de subnetten uitbreidt, moet u proberen om het een tussen stap te maken. Met tijd kunt u de toepassingen in uw on-premises netwerk opnieuw nummeren en deze migreren naar Azure.

In de volgende sectie wordt uitgelegd hoe u uw subnets kunt uitbreiden naar Azure.


## <a name="extend-your-subnet-to-azure"></a>Uw subnet uitbreiden naar Azure
 U kunt uw on-premises subnetten uitbreiden naar Azure met behulp van een laag-3-overlay-netwerk oplossing. De meeste oplossingen maken gebruik van een bedekkings technologie zoals VXLAN om het laag-2-netwerk uit te breiden met behulp van een laag-3-overlay-netwerk. In het onderstaande diagram ziet u een algemene oplossing. In deze oplossing bestaat hetzelfde subnet aan beide zijden van Azure en on-premises. 

![Voor beeld van een subnet-extensie](./media/subnet-extension/subnet-extension.png)

De IP-adressen van het subnet worden toegewezen aan Vm's op Azure en on-premises. Azure en on-premises hebben een NVA in hun netwerken. Wanneer een virtuele machine in azure probeert te communiceren met een virtuele machine in een on-premises netwerk, wordt het pakket door Azure NVA vastgelegd, ingekapseld en verzonden via VPN/Express-route naar het on-premises netwerk. De on-premises NVA ontvangt het pakket decapsulates het en stuurt het door naar de bedoelde ontvanger in het netwerk. Het retour verkeer maakt gebruik van een vergelijk bare pad en logica.

In het bovenstaande voor beeld communiceren de Azure-NVA en de on-premises NVA en meer informatie over IP-adressen achter elkaar. Complexere netwerken kunnen ook een toewijzings service hebben, die de toewijzing bewaart tussen de Nva's en de IP-adressen die zich achter elkaar bevinden. Wanneer een NVA een pakket ontvangt, wordt een query uitgevoerd op de toewijzings service om het adres te vinden van de NVA die het bestemmings-IP-adres heeft.

In de volgende sectie vindt u informatie over de oplossingen voor subnet uitbreidingen die we hebben getest op Azure.

## <a name="next-steps"></a>Volgende stappen 
[Breid uw subnet uit naar Azure met behulp van oplossingen van leveranciers.](https://github.com/microsoft/Azure-LISP)
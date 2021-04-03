---
title: bestand opnemen
description: bestand opnemen
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 11/05/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 57b0bb9ab8ceb34021a38db0d0abf2c9c919e808
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94356643"
---
### <a name="can-i-still-deploy-multiple-bastion-hosts-across-peered-virtual-networks"></a>Kan ik nog steeds meerdere Bastion-hosts implementeren in gekoppelde virtuele netwerken?

Ja. Standaard ziet een gebruiker de bastion-host die is geïmplementeerd in hetzelfde virtuele netwerk waarin de VM zich bevindt. In het menu **verbinden** kunnen gebruikers echter meerdere Bastion-hosts zien die zijn gedetecteerd in peered netwerken. Ze kunnen de bastion-host selecteren die ze liever gebruiken om verbinding te maken met de virtuele machine die in het virtuele netwerk is geïmplementeerd.

### <a name="if-my-peered-vnets-are-deployed-in-different-subscriptions-will-connectivity-via-bastion-work"></a>Als mijn gepeerde VNets in verschillende abonnementen worden geïmplementeerd, zal connectiviteit via Bastion werken?

Ja, de connectiviteit via Bastion blijft werken voor VNets in een ander abonnement voor één Tenant. Abonnementen over twee verschillende tenants worden niet ondersteund. Als u wilt zien bastion in de vervolg keuzelijst **verbinding maken** , moet de gebruiker de optie sluiten selecteren die toegang heeft tot **> globale abonnement**.

:::image type="content" source="./media/bastion-faq-peering-include/global-subscriptions.png" alt-text="Filter voor globale abonnementen" lightbox="./media/bastion-faq-peering-include/global-subscriptions.png":::

### <a name="i-have-access-to-the-peered-vnet-but-i-cant-see-the-vm-deployed-there"></a>Ik heb toegang tot het gepeerde VNet, maar ik zie de VM die daar niet is geïmplementeerd.

Zorg ervoor dat de gebruiker **Lees** toegang heeft tot zowel de virtuele machine als de peered VNet. Controleer bovendien onder IAM dat de gebruiker **Lees** toegang heeft tot de volgende resources:

* De lezerrol op de virtuele machine.
* De lezerrol op de NIC met het privé-IP-adres van de virtuele machine.
* De rol van Lezer in de Azure Bastion-resource.
* De rol van lezer op de Virtual Network (niet nodig als er geen gekoppeld virtueel netwerk is).

|Machtigingen|Description|Machtigings type|
|---|---| ---|
|Micro soft. Network/bastionHosts/lezen |Hiermee wordt een bastion-host opgehaald|Bewerking|
|Micro soft. Network/virtualNetworks/BastionHosts/Action |Hiermee worden Bastion opgehaald in een Virtual Network.|Bewerking|
|Micro soft. Network/virtualNetworks/bastionHosts/default/action|Hiermee worden Bastion opgehaald in een Virtual Network.|Bewerking|
|Micro soft. Network/networkInterfaces/lezen|Hiermee haalt u een definitie van een netwerk interface.|Bewerking|
|Micro soft. Network/networkInterfaces/ipconfigurations/lezen|Hiermee wordt een IP-configuratie definitie van de netwerk interface opgehaald.|Bewerking|
|Micro soft. Network/virtualNetworks/lezen|De virtuele-netwerk definitie ophalen|Bewerking|
|Micro soft. Network/virtualNetworks/subnetten/informatie/lezen|Hiermee worden verwijzingen naar alle virtuele machines in een subnet van een virtueel netwerk opgehaald|Bewerking|
|Micro soft. Network/virtualNetworks/informatie/lezen|Hiermee worden verwijzingen naar alle virtuele machines in een virtueel netwerk opgehaald|Bewerking|
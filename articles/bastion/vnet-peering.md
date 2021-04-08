---
title: VNet-peering en Azure Bastion-architectuur
description: In dit artikel leest u hoe VNet-peering en Azure Bastion samen kunnen worden gebruikt om verbinding te maken met virtuele machines.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 12/09/2020
ms.author: cherylmc
ms.openlocfilehash: f72a3739fac1e7d6afdafd2676ea6fcefe847b2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101710580"
---
# <a name="vnet-peering-and-azure-bastion-preview"></a>VNet-peering en Azure Bastion (preview-versie)

Azure Bastion en VNet-peering kunnen samen worden gebruikt. Wanneer VNet-peering is geconfigureerd, hoeft u geen Azure Bastion te implementeren in elk peered VNet. Dit betekent dat als u een Azure bastion-host in één virtueel netwerk (VNet) hebt geconfigureerd, deze kan worden gebruikt om verbinding te maken met virtuele machines die zijn geïmplementeerd in een gepeerd VNet zonder een extra bastion-host te implementeren. Zie [over de peering van het virtuele netwerk](../virtual-network/virtual-network-peering-overview.md)voor meer informatie over VNet-peering.

Azure Bastion werkt met de volgende typen peering:

* **Peering op virtueel netwerk:** Verbind virtuele netwerken binnen dezelfde Azure-regio.
* **Globale peering van virtueel netwerk:** Verbinding maken met virtuele netwerken tussen Azure-regio's.

## <a name="architecture"></a>Architectuur

Wanneer VNet-peering is geconfigureerd, kan Azure bastion in hub-en-spoke-of full-mesh-topologieën worden geïmplementeerd. De Azure Bastion-implementatie vindt plaats per virtueel netwerk, niet per abonnement/account of virtuele machine.

Wanneer u de Azure Bastion-service in uw virtuele netwerk hebt ingericht, is de RDP/SSH-ervaring beschikbaar voor alle virtuele machines in hetzelfde VNet, evenals de peered VNets. Dit betekent dat u Bastion-implementaties kunt consolideren naar één VNet en nog steeds Vm's hebt geïmplementeerd in een gepeerd VNet, waardoor de algehele implementatie wordt gecentraliseerd.

:::image type="content" source="./media/vnet-peering/design.png" alt-text="Ontwerp-en architectuur diagram":::

In deze afbeelding ziet u de architectuur van een Azure Bastion-implementatie in een hub-en-spoke-model. In dit diagram ziet u de volgende configuratie:

* De bastion-host wordt geïmplementeerd in het gecentraliseerde hub-netwerk.
* Gecentraliseerde netwerk beveiligings groep (NSG) is geïmplementeerd.
* Een openbaar IP-adres is niet vereist op de Azure-VM.

**Stappen**

1. Maak verbinding met de Azure Portal met behulp van een HTML5-browser.
2. Zorg ervoor dat u **Lees** toegang hebt tot zowel de doel-VM als de peered VNet. Controleer bovendien onder IAM dat u lees toegang hebt tot de volgende bronnen:
   * De lezerrol op de virtuele machine.
   * De lezerrol op de NIC met het privé-IP-adres van de virtuele machine.
   * De rol van Lezer in de Azure Bastion-resource.
   * De rol van lezer op de Virtual Network (niet nodig als er geen gekoppeld virtueel netwerk is).
3. Als u de Bastion in de vervolg keuzelijst **verbinding** wilt zien, moet u het abonnement dat u hebt geopend, selecteren **> wereld wijde abonnement**.
4. Selecteer de virtuele machine waarmee u verbinding wilt maken.
5. Azure Bastion wordt naadloos gedetecteerd in het gekoppelde VNet.
6. Wordt de RDP-/SSH-sessie met één klik geopend in de browser. Zie [RDP-en SSH-sessies](bastion-faq.md#limits)voor limieten voor gelijktijdige sessies met RDP en SSH.

  :::image type="content" source="../../includes/media/bastion-vm-rdp/connect-vm.png" alt-text="Verbinding maken":::

   Voor meer informatie over verbinding maken met een virtuele machine via Azure Bastion raadpleegt u:

   * [Verbinding maken met een VM-RDP](bastion-connect-vm-rdp.md).
   * [Verbinding maken met een VM-SSH](bastion-connect-vm-ssh.md).

## <a name="faq"></a>Veelgestelde vragen

[!INCLUDE [FAQ for VNet peering](../../includes/bastion-faq-peering-include.md)]

## <a name="next-steps"></a>Volgende stappen

Lees de [Veelgestelde vragen over Bastion](bastion-faq.md).

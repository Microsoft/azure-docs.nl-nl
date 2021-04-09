---
title: Verbinding maken met een virtuele Windows-machine met behulp van Azure Bastion
description: In dit artikel leert u hoe u verbinding kunt maken met een virtuele Azure-machine met Windows met behulp van Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/21/2020
ms.author: cherylmc
ms.openlocfilehash: 708bd1f61da2f3973333f8e68cabdceee0717bee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521528"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Verbinding maken met een virtuele Windows-machine met behulp van Azure Bastion

Met Azure Bastion kunt u veilig en naadloos verbinding maken met uw virtuele machines via SSL rechtstreeks in de Azure Portal. Wanneer u Azure Bastion gebruikt, is voor uw Vm's geen client, agent of extra software vereist. In dit artikel wordt beschreven hoe u verbinding maakt met uw Windows-Vm's. Zie [verbinding maken met een virtuele Linux-machine](bastion-connect-vm-ssh.md)voor meer informatie over verbinding maken met een virtuele Linux-machine.

Azure Bastion biedt een beveiligde connectiviteit voor alle virtuele machines in een virtueel netwerk waarin deze is ingericht. Azure Bastion beschermt uw virtuele machines tegen blootstelling van RDP-/SSH-poorten aan de buitenwereld, terwijl u toch beveiligde toegang krijgt geboden met behulp van RDP/SSH. Zie [Wat is Azure Bastion?](bastion-overview.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Controleer voordat u begint of u aan de volgende criteria hebt voldaan:

* Er is al een VNet met de bastion-host geïnstalleerd.

   Zorg ervoor dat u een Azure bastion-host hebt ingesteld voor het virtuele netwerk waarin de virtuele machine zich bevindt. Zodra de Bastion-service is ingericht en geïmplementeerd in uw virtuele netwerk, kunt u deze gebruiken om verbinding te maken met elke virtuele machine in het virtuele netwerk. Zie [een bastion-host maken](tutorial-create-host-portal.md#createhost)voor het instellen van een Azure bastion-host.
* Een virtuele Windows-machine in een virtueel netwerk.
* De volgende vereiste rollen:
  * De lezerrol op de virtuele machine.
  * De lezerrol op de NIC met het privé-IP-adres van de virtuele machine.
  * De rol van Lezer in de Azure Bastion-resource.
* Poorten: Als u verbinding wilt maken met de Windows-VM, moeten de volgende poorten zijn geopend op uw Windows-VM:
  * Poorten voor inkomend verkeer: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>Verbinding maken

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]
 
## <a name="next-steps"></a>Volgende stappen

Lees de [Veelgestelde vragen over Bastion](bastion-faq.md).
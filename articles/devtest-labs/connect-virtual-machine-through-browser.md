---
title: Verbinding maken met uw virtuele machines via een browser-Azure | Microsoft Docs
description: Meer informatie over hoe u via een browser verbinding kunt maken met uw virtuele machines.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 352fc5fd3ff53a00d9f62966ecf21417ad898706
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87288067"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Verbinding maken met uw virtuele machines via een browser 

DevTest Labs kan worden geïntegreerd met [Azure Bastion](../bastion/index.yml), waarmee u via een browser verbinding kunt maken met uw virtuele machines. Zie [browser verbinding inschakelen op Lab virtual machines](enable-browser-connection-lab-virtual-machines.md)voor meer informatie over het inschakelen van deze functie in DevTest Labs.

Zodra de *browser verbinding maken* is ingeschakeld, hebben Lab-gebruikers via een browser toegang tot virtuele machines.  

## <a name="create-a-lab-virtual-machine"></a>Een virtuele machine voor het lab maken

U moet eerst de virtuele machine van het lab maken binnen een virtueel netwerk waarop Bastion is geconfigureerd. Selecteer het tweede **subnet** dat u hebt gemaakt, niet de AzureBastionSubnet. U kunt een virtueel netwerk selecteren tijdens het maken van de virtuele machine door naar het tabblad **Geavanceerde instellingen** te gaan.

![Virtuele machine maken](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Virtuele machine in een browser starten

Zodra de virtuele machine is gemaakt, kunt u deze in een browser openen door te klikken op de knop *verbinding maken* en uw gebruikers naam en wacht woord voor de machine in te voeren.  

![Starten in een browser](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Volgende stappen

[Een VM toevoegen aan een lab in Azure DevTest Labs](devtest-lab-add-vm.md)

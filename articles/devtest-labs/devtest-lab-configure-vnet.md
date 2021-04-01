---
title: Een virtueel netwerk configureren in Azure DevTest Labs | Microsoft Docs
description: Meer informatie over het configureren van een bestaand virtueel netwerk en subnet, en het gebruik ervan in een VM met Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 21fe23b6be6fdc924c5502ff97f9c03a3f3e759d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87283551"
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Een virtueel netwerk configureren in Azure DevTest Labs
Zoals beschreven in het artikel [een VM toevoegen aan een Lab](devtest-lab-add-vm.md)wanneer u een virtuele machine in een Lab maakt, kunt u een geconfigureerd virtueel netwerk opgeven. U moet bijvoorbeeld toegang hebben tot uw Corpnet-resources van uw virtuele machines met behulp van het virtuele netwerk dat is geconfigureerd met ExpressRoute of site-naar-site-VPN.

In dit artikel wordt uitgelegd hoe u uw bestaande virtuele netwerk kunt toevoegen aan de Virtual Network-instellingen van een lab, zodat deze beschikbaar is om te kiezen bij het maken van Vm's.

> [!NOTE]
> Zie [prijzen voor azure Virtual Network](../virtual-network/virtual-networks-overview.md#pricing)als u meer wilt weten over de kosten die zijn gekoppeld aan de Azure Virtual Network-Service.

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Een virtueel netwerk voor een lab configureren met behulp van de Azure Portal
De volgende stappen helpen u bij het toevoegen van een bestaand virtueel netwerk (en subnet) aan een Lab zodat het kan worden gebruikt bij het maken van een virtuele machine in hetzelfde lab. 

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecteer **alle services** en selecteer vervolgens **DevTest Labs** in de lijst.
1. Selecteer in de lijst met Labs het gewenste Lab. 
1. Selecteer **configuratie en beleid** in het hoofd venster van het lab.

    ![De configuratie en het beleid van het lab openen](./media/devtest-lab-configure-vnet/policies-menu.png)
1. Selecteer in de sectie **externe resources** **virtuele netwerken**. Er wordt een lijst weer gegeven met virtuele netwerken die zijn geconfigureerd voor het huidige Lab en het virtuele standaard netwerk dat voor uw Lab is gemaakt. 
1. Selecteer **+ Toevoegen**.
   
    ![Een bestaand virtueel netwerk aan uw Lab toevoegen](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. Selecteer in het deel venster **virtueel netwerk** de optie **[virtueel netwerk selecteren]**.
   
    ![Selecteer een bestaand virtueel netwerk](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. Selecteer in het deel venster **virtueel netwerk kiezen** het gewenste virtuele netwerk. Er wordt een lijst weer gegeven met alle virtuele netwerken die zich in dezelfde regio in het abonnement bevinden als het lab.
1. Nadat u een virtueel netwerk hebt geselecteerd, keert u terug naar het deel venster **virtueel netwerk** . Selecteer in de lijst onderaan het subnet.

    ![Lijst met subnetten](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    Het deel venster Lab-subnet wordt weer gegeven.

    ![Deel venster Lab-subnet](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - Geef een **naam** op voor het subnet van het lab.
   - Selecteer gebruiken bij het maken van een **virtuele machine** om toe te staan dat een subnet kan worden gebruikt bij het maken van een Lab VM.
   - Als u een [gedeeld openbaar IP-adres](devtest-lab-shared-ip.md)wilt inschakelen, selecteert u **gedeelde open bare IP inschakelen**.
   - Als u open bare IP-adressen in een subnet wilt toestaan, selecteert u **openbaar IP-adres maken toestaan**.
   - Geef in het veld **maximum aantal virtuele machines per gebruiker** het maximum aantal vm's per gebruiker op voor elk subnet. Als u een onbeperkt aantal Vm's wilt, laat u dit veld leeg.
1. Selecteer **OK** om het deel venster Lab-subnet te sluiten.
1. Selecteer **Opslaan** om het deel venster virtueel netwerk te sluiten.

Nu het virtuele netwerk is geconfigureerd, kan het worden geselecteerd bij het maken van een virtuele machine. Als u wilt zien hoe u een virtuele machine maakt en een virtueel netwerk opgeeft, raadpleegt u het artikel [een VM toevoegen aan een Lab](devtest-lab-add-vm.md). 

De [Virtual Network documentatie](../virtual-network/index.yml) van Azure biedt meer informatie over het gebruik van VNets, met inbegrip van het instellen en beheren van een VNet en verbinding maken met uw on-premises netwerk.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Volgende stappen
Zodra u het gewenste virtuele netwerk aan uw Lab hebt toegevoegd, is de volgende stap het [toevoegen van een virtuele machine aan uw Lab](devtest-lab-add-vm.md).

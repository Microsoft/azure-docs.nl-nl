---
title: Netwerk interfaces van Vm's beheren op uw Azure Stack Edge Pro via de Azure Portal
description: Meer informatie over het beheren van netwerk interfaces op virtuele machines die zijn geïmplementeerd op uw Azure Stack Edge Pro GPU via de Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
ms.openlocfilehash: 3e709b04b4eac60e6cc0ba3e53eb77583162dfef
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078890"
---
# <a name="use-the-azure-portal-to-manage-network-interfaces-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Gebruik de Azure Portal om netwerk interfaces op de virtuele machines op uw Azure Stack Edge Pro GPU te beheren

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

U kunt virtuele machines (Vm's) maken en beheren op een Azure Stack edge-apparaat met behulp van Azure Portal, sjablonen, Azure PowerShell-cmdlets en via Azure CLI/python-scripts. In dit artikel wordt beschreven hoe u de netwerk interfaces beheert op een virtuele machine die wordt uitgevoerd op uw Azure Stack edge-apparaat met behulp van de Azure Portal. 

Wanneer u een VM maakt, geeft u één virtuele netwerk interface op die moet worden gemaakt. Mogelijk wilt u een of meer netwerk interfaces toevoegen aan de virtuele machine nadat deze is gemaakt. Mogelijk wilt u ook de standaard instellingen van de netwerk interface voor een bestaande netwerk interface wijzigen.

In dit artikel wordt uitgelegd hoe u een netwerk interface toevoegt aan een bestaande virtuele machine, bestaande instellingen wijzigt, zoals IP-type (statisch versus dynamisch), en tenslotte een bestaande interface verwijderen of loskoppelen. 

        
## <a name="about-network-interfaces-on-vms"></a>Over netwerk interfaces op Vm's

Met een netwerk interface kan een virtuele machine (VM) worden uitgevoerd op uw Azure Stack Edge Pro-apparaat om te communiceren met Azure en on-premises resources. Wanneer u een poort voor het berekenings netwerk op uw apparaat inschakelt, wordt er een virtuele switch op die netwerk interface gemaakt. Deze virtuele switch wordt vervolgens gebruikt voor het implementeren van reken werkbelastingen, zoals Vm's of container toepassingen op uw apparaat. 

Uw apparaat ondersteunt slechts één virtuele switch maar meerdere virtuele netwerk interfaces. Aan elke netwerk interface op uw virtuele machine is een statisch of dynamisch IP-adres toegewezen. Met IP-adressen die zijn toegewezen aan meerdere netwerk interfaces op uw virtuele machine, worden bepaalde mogelijkheden ingeschakeld op de virtuele machine. Uw virtuele machine kan bijvoorbeeld meerdere websites of services hosten met verschillende IP-adressen en SSL-certificaten op één server. Een virtuele machine op uw apparaat kan fungeren als een virtueel netwerk apparaat, zoals een firewall of een load balancer. <!--Is it possible to do that on ASE?-->

<!--There is a limit to how many virtual network interfaces can be created on the virtual switch on your device. See the Azure Stack Edge Pro limits article for details.--> 


## <a name="prerequisites"></a>Vereisten

Voordat u begint met het beheren van virtuele machines op uw apparaat via de Azure Portal, moet u ervoor zorgen dat:

1. U hebt toegang tot een geactiveerd Azure Stack Edge Pro GPU-apparaat. U hebt een netwerk interface ingeschakeld voor Compute op het apparaat. Met deze actie maakt u een virtuele switch op de netwerk interface van de virtuele machine. 
    1. Ga in de lokale gebruikers interface van uw apparaat naar **Compute**. Selecteer de netwerkinterface die u wilt gebruiken om een virtuele switch te maken.

        > [!IMPORTANT] 
        > U kunt slechts één poort configureren voor berekeningen.

    1. Schakel Compute in op de netwerkinterface. Met Azure Stack Edge Pro GPU wordt een virtuele switch gemaakt en beheerd die overeenkomt met die netwerk interface.

1. U hebt ten minste één VM geïmplementeerd op uw apparaat. Als u deze virtuele machine wilt maken, raadpleegt u de instructies in [virtuele machine implementeren op uw Azure stack Edge Pro via de Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).

1. De VM moet de status **gestopt** hebben. Als u uw virtuele machine wilt stoppen, gaat u naar **virtuele machines > overzicht** en selecteert u de virtuele machine die u wilt stoppen. Selecteer op de pagina eigenschappen van virtuele machine **stoppen** en selecteer vervolgens **Ja** wanneer u wordt gevraagd om bevestiging. Voordat u netwerk interfaces toevoegt, bewerkt of verwijdert, moet u de virtuele machine stoppen.

    ![VM stoppen vanaf pagina met VM-eigenschappen](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="add-a-network-interface"></a>Een netwerk interface toevoegen

Volg deze stappen om een netwerk interface toe te voegen aan een virtuele machine die op het apparaat is geïmplementeerd. 

1. Ga naar de virtuele machine die u hebt gestopt en ga vervolgens naar de pagina met **VM-eigenschappen** . Selecteer **Netwerken**.
    
    ![Eigenschappen pagina netwerken op VM selecteren](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-1.png)

2. Selecteer op de Blade **netwerken** op de opdracht balk **+ netwerk interface toevoegen**.

    ![Selecteer netwerk interface toevoegen](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-2.png)

3. Voer op de Blade **netwerk interface toevoegen** de volgende para meters in:

    
    |Kolom 1  |Kolom 2  |
    |---------|---------|
    |Name     | Een unieke naam binnen de resource groep. De naam kan niet worden gewijzigd nadat de netwerk interface is gemaakt. Als u meerdere netwerk interfaces eenvoudig wilt beheren, gebruikt u de suggesties in de [naamgevings conventies](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming).     |
    |Virtueel netwerk| Het virtuele netwerk dat is gekoppeld aan de virtuele switch die is gemaakt op uw apparaat wanneer u Compute op de netwerk interface inschakelt. Er is slechts één virtueel netwerk gekoppeld aan uw apparaat.         |         
    |Subnet     | Een subnet in het geselecteerde virtuele netwerk. Dit veld wordt automatisch ingevuld met het subnet dat is gekoppeld aan de netwerk interface waarvoor u Compute hebt ingeschakeld.         |       
    |IP-toewijzing   | Een statisch of dynamisch IP-adres voor uw netwerk interface. Het statische IP-adres moet een beschikbaar, vrij IP-adres zijn van het opgegeven subnet-bereik. Kies dynamisch als er een DHCP-server in de omgeving bestaat.        | 

    ![Een Blade voor een netwerk interface toevoegen](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-3.png)

4. U ziet een melding dat het maken van de netwerk interface wordt uitgevoerd.

    ![Melding wanneer de netwerk interface wordt gemaakt](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-4.png)

5.  Wanneer de netwerk interface is gemaakt, wordt de lijst met netwerk interfaces vernieuwd om de zojuist gemaakte interface weer te geven.

    ![Bijgewerkte lijst met netwerk interfaces](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-5.png)


## <a name="edit-a-network-interface"></a>Een netwerk interface bewerken

Volg deze stappen voor het bewerken van een netwerk interface die is gekoppeld aan een virtuele machine die op het apparaat is geïmplementeerd.

1. Ga naar de virtuele machine die u hebt gestopt en ga naar de pagina met **VM-eigenschappen** . Selecteer **Netwerken**.

1. Selecteer in de lijst met netwerk interfaces de interface die u wilt bewerken. Selecteer in de rechter kant van de geselecteerde netwerk interface het bewerkings pictogram (potlood).  

    ![Selecteer een netwerk interface om te bewerken](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-1.png)

1. Op de Blade **netwerk interface bewerken** kunt u alleen de IP-toewijzing van de netwerk interface wijzigen. De naam, het virtuele netwerk en het subnet die aan de netwerk interface zijn gekoppeld, kunnen niet meer worden gewijzigd nadat deze is gemaakt. Wijzig de **IP-toewijzing** in statisch en sla de wijzigingen op.

    ![IP-toewijzing voor de netwerk interface wijzigen](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-2.png)

1. De lijst met netwerk interface vernieuwt om de bijgewerkte netwerk interface weer te geven.


## <a name="detach-a-network-interface"></a>Een netwerk interface ontkoppelen

Volg deze stappen voor het ontkoppelen of verwijderen van een netwerk interface die is gekoppeld aan een virtuele machine die op het apparaat is geïmplementeerd.

1. Ga naar de virtuele machine die u hebt gestopt en ga naar de pagina met **VM-eigenschappen** . Selecteer **Netwerken**.

1. Selecteer in de lijst met netwerk interfaces de interface die u wilt bewerken. Selecteer helemaal rechts van de geselecteerde netwerk interface het pictogram loskoppelen (ontkoppelen).  

    ![Selecteer een netwerk interface om te ontkoppelen](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/detach-nic-1.png)

1. Nadat de interface volledig is losgekoppeld, wordt de lijst met netwerk interfaces vernieuwd om de resterende interfaces weer te geven.

## <a name="next-steps"></a>Volgende stappen

Zie [virtuele machines implementeren via de Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)voor meer informatie over het implementeren van virtuele machines op uw Azure stack Edge Pro-apparaat.

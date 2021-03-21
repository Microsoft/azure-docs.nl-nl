---
title: 'Quickstart: Een schaalset voor virtuele machines maken in de Azure-portal'
description: Ga aan de slag met uw implementaties door te leren hoe u snel een schaal voor virtuele machines kunt maken met de Azure-portal.
author: ju-shim
ms.author: jushiman
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.subservice: ''
ms.date: 06/30/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 0408341eff483de71d76a24a933ece4cbdc2b947
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100535277"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-in-the-azure-portal"></a>Quickstart: Een schaalset voor virtuele machines maken in Azure Portal

Met een virtuele-machineschaalset kunt u een reeks automatisch schalende virtuele machines implementeren en beheren. U kunt het aantal VM’s in de schaalset handmatig schalen of regels voor automatisch schalen definiëren op basis van resourcegebruik zoals CPU, vraag naar geheugen, of netwerkverkeer. Een Azure load balancer verdeelt het verkeer vervolgens naar de VM-exemplaren in de schaalset. In deze snelstartgids gaat u in Azure Portal een schaalset voor virtuele machines maken.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.


## <a name="log-in-to-azure"></a>Meld u aan bij Azure.
Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-load-balancer"></a>Een load balancer maken

Azure b[load balancer](../load-balancer/load-balancer-overview.md) verdeelt binnenkomend verkeer over geschikte VM-exemplaren. 

Maak eerst een openbare Standard Load Balancer met behulp van de portal. De naam en het openbare IP-adres die u maakt, worden automatisch geconfigureerd als de front-end van de load balancer.

1. Typ **load balancer** in het zoekvak. Onder **Marketplace** selecteert u **Load Balancer** in de zoekresultaten.
1. Typ of selecteer de volgende informatie op het tabblad **Basisbeginselen** van de pagina **Load balancer maken**:

    | Instelling                 | Waarde   |
    | ---| ---|
    | Abonnement  | Selecteer uw abonnement.    |    
    | Resourcegroep | Selecteer **Nieuwe maken** en typ *myVMSSResourceGroup* in het tekstvak.|
    | Naam           | *myLoadBalancer*         |
    | Regio         | Selecteer **VS - oost**.       |
    | Type          | Selecteer **Openbaar**.       |
    | SKU           | selecteer **Standaard**.       |
    | Openbaar IP-adres | Selecteer **Nieuw maken**. |
    | Naam openbaar IP-adres  | *myPip*   |
    | Toewijzing| Statisch |
    | Beschikbaarheidszone | selecteer **Zone-redundant**. |

1. Als u klaar bent, selecteert u **Beoordelen en maken**. 
1. Nadat de validatie is gelukt, selecteert u **Maken**. 

![Een load balancer maken](./media/virtual-machine-scale-sets-create-portal/load-balancer.png)

## <a name="create-virtual-machine-scale-set"></a>Schaalset voor virtuele machines maken
U kunt een schaalset implementeren met een installatiekopie van Windows Server of Linux, zoals RHEL, CentOS, Ubuntu of SLES.

1. Typ **Schaalset** in het zoekvak. In de resultaten onder **Marketplace** selecteert u **Schaalsets voor virtuele machines**. Selecteer **Maken** op de pagina **Virtuele-machineschaalsets**, waarna de pagina **Een virtuele-machineschaalset maken** wordt geopend. 
1. Controleer op het tabblad **basis beginselen** onder **Project Details** of het juiste abonnement is geselecteerd en selecteer *myVMSSResourceGroup* uit de lijst met resource groepen. 
1. Typ *myScaleSet* als de naam van de schaalset.
1. Selecteer bij **Regio** een regio die zich dichtbij uw gebied bevindt.
1. Selecteer een Marketplace-installatiekopie bij **Installatiekopie**. In dit voorbeeld hebben we gekozen voor *Ubuntu Server 18.04 LTS*.
1. Voer de gewenste gebruikersnaam in en selecteer het verificatietype dat u wilt gebruiken.
   - Een **wachtwoord** moet ten minste 12 tekens lang zijn en aan drie van de vier volgende complexiteitsvereisten voldoen: ten minste één kleine letter, één hoofdletter, één cijfer en één speciaal teken. Zie [Wat zijn de wachtwoordvereisten bij het maken van een virtuele machine?](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) voor meer informatie.
   - Als u een installatiekopie voor een Linux-besturingssysteem selecteert, kunt u ook **Openbare SSH-sleutel** kiezen. Geef alleen de openbare sleutel op, zoals *~/.ssh/id_rsa.pub*. U kunt vanuit de portal de Azure Cloud-Shell gebruiken om [SSH-sleutels te maken en gebruiken](../virtual-machines/linux/mac-create-ssh-keys.md).
   
    :::image type="content" source="./media/virtual-machine-scale-sets-create-portal/quick-create-scale-set.png" alt-text="Afbeelding van de opties voor het maken van schaalsets in de Azure-portal.":::

1. Selecteer **Volgende** om de andere pagina's te verplaatsen. 
1. Accepteer de standaardwaarden voor de pagina's **Instantie** en **Schijven**.
1. Op de pagina **Netwerk** selecteert u onder **Taakverdeling** de optie **Ja** om de instanties van de schaalset achter een load balancer te plaatsen. 
1. Selecteer bij **Opties voor taakverdeling** de optie **Azure Load Balancer**.
1. Bij **Een load balancer selecteren** selecteert u de *myLoadBalancer* die u eerder hebt gemaakt.
1. Selecteer onder **Een back-endadresgroep selecteren** de optie **Nieuwe maken**, typ vervolgens *myBackendPool* en selecteer **Maken**.
1. Als u klaar bent, selecteert u **Beoordelen en maken**. 
1. Nadat de validatie is gelukt, selecteert u **Maken** om de schaalset te implementeren.


## <a name="clean-up-resources"></a>Resources opschonen
De resourcegroep, de schaalset en alle gerelateerde resources kunt u verwijderen wanneer u deze niet meer nodig hebt. Hiervoor selecteert u de resourcegroep voor de schaalset en selecteert u **Verwijderen**.


## <a name="next-steps"></a>Volgende stappen
In deze snelstartgids hebt u via Azure Portal een eenvoudige schaalset voor virtuele machines gemaakt. Als u meer wilt weten over schaalsets, gaat u verder met de zelfstudie voor het maken en beheren van schaalsets voor virtuele Azure-machines.

> [!div class="nextstepaction"]
> [Schaalsets voor virtuele Azure-machines maken en beheren](tutorial-create-and-manage-powershell.md)

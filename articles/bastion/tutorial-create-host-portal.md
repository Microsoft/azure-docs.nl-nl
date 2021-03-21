---
title: 'Zelfstudie: Een Azure Bastion-host maken: Windows-VM: portal'
description: In dit artikel leert u hoe u een Azure Bastion-host kunt maken en verbinding maken met een Windows-VM.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: tutorial
ms.date: 02/12/2021
ms.author: cherylmc
ms.openlocfilehash: 60b49e5b6e103a85d79cf8495f2743b22e434c96
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100586791"
---
# <a name="tutorial-configure-bastion-and-connect-to-a-windows-vm-through-a-browser"></a>Zelfstudie: Een Azure Bastion configureren en verbinding maken met een Windows-VM via een browser

In deze zelfstudie leert u hoe u via uw browser verbinding kunt maken met een virtuele machine met behulp van Azure Bastion en de Azure-portal. In de Azure-portal implementeert u Bastion in uw virtuele netwerk. Na de implementatie van Bastion kunt u met behulp van de Azure-portal verbinding maken met een VM via het privé-IP-adres ervan. Er is voor uw VM geen openbaar IP-adres of speciale software nodig. Zodra de service is ingericht, is de RDP/SSH-ervaring beschikbaar voor alle virtuele machines in hetzelfde virtuele netwerk. Zie [Wat is Azure Bastion?](bastion-overview.md) voor meer informatie over Azure Bastion.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Bastion-host maken voor uw VNet
> * Verbinding maken met een virtuele Windows-machine

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een virtueel netwerk.
* Een virtuele Windows-machine in een virtueel netwerk.
* De volgende vereiste rollen:
  * De lezerrol op de virtuele machine.
  * De lezerrol op de NIC met het privé-IP-adres van de virtuele machine.
  * De rol van Lezer in de Azure Bastion-resource.

* Poorten: Als u verbinding wilt maken met de Windows-VM, moeten de volgende poorten zijn geopend op uw Windows-VM:
  * Poorten voor inkomend verkeer: RDP (3389)

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Een Bastion-host maken

Deze sectie helpt u bij het maken van het Bastion-object in uw VNet. Dit is vereist om een beveiligde verbinding te maken met een VM in het VNet.

1. Selecteer op uw **Start** pagina de optie **+ Een resource maken**.
1. Typ op de pagina **Nieuw** de tekst **Bastion** in het zoekvak en selecteer **Enter** om naar de zoekresultaten te gaan. Controleer in het resultaat voor **Bastion** of de uitgever Microsoft is.
1. Selecteer **Maken**.
1. Configureer op de pagina **Een bastion maken** een nieuwe Bastion-resource.

   :::image type="content" source="./media/tutorial-create-host-portal/bastion-basics.png" alt-text="Een Bastion-host maken" lightbox="./media/tutorial-create-host-portal/bastion-basics.png":::

    * **Abonnement**: Het Azure-abonnement dat u wilt gebruiken voor het maken van een nieuwe Bastion-resource.
    * **Resourcegroep:** De Azure-resourcegroep waarin de nieuwe Bastion-resource wordt gemaakt. Als u geen bestaande resourcegroep hebt, kunt u een nieuwe maken.
    * **Naam**: De naam van de nieuwe Bastion-resource.
    * **Regio**: De openbare Azure-regio waarin de resource wordt gemaakt.
    * **Virtueel netwerk**: Het virtuele netwerk waarin de Bastion-resource wordt gemaakt. U kunt tijdens dit proces een nieuw virtueel netwerk maken in de portal of een bestaand virtueel netwerk gebruiken. Als u een bestaand virtueel netwerk gebruikt, zorg er dan voor dat het bestaande virtuele netwerk voldoende vrije adresruimte heeft om te voldoen aan de vereisten van het Bastion-subnet. Als uw virtuele netwerk niet in de vervolgkeuzelijst wordt weergegeven, controleer dan of u de juiste resourcegroep hebt geselecteerd.
    * **Subnet**: Wanneer u een virtueel netwerk hebt gemaakt of geselecteerd, wordt het subnetveld weergegeven. Het subnet in het virtuele netwerk waar de nieuwe Bastion-host wordt geïmplementeerd. Het subnet wordt toegewezen aan de Bastion-host. Selecteer **Subnetconfiguratie beheren** en maak het Azure Bastion-subnet. Selecteer **+Subnet** en maak een subnet met behulp van de volgende richtlijnen:

         * Het subnet moet **AzureBastionSubnet** heten.
         * Het subnet moet minstens /27 of groter zijn.

      U hoeft geen aanvullende velden in te vullen. Selecteer **OK** en selecteer aan de bovenkant van de pagina **Een bastion maken** om terug te gaan naar de configuratiepagina van Bastion.
    * **Openbaar IP-adres**: Het openbare IP-adres van de Bastion-resource waarop RDP/SSH wordt geopend (via poort 443). Maak een nieuw openbaar IP-adres. Het openbare IP-adres moet zich in dezelfde regio bevinden als de Bastion-resource die u aan het maken bent. Dit IP-adres heeft niets om te doen met een van de virtuele machines waarmee u verbinding wilt maken. Het is het openbare IP-adres voor de Bastion-hostresource.
    * **Openbare IP-adresnaam**: De naam van de resource voor het openbare IP-adres. Voor deze zelfstudie kunt u de standaardwaarden laten staan.
    * **Openbaar IP-adres SKU**: Deze instelling wordt standaard vooraf ingevuld met **Standaard**. Azure Bastion gebruikt/ondersteunt alleen de Standard Public IP SKU.
    * **Toewijzing**: Deze instelling wordt standaard vooraf ingevuld met **Statisch**.

1. Wanneer u klaar bent met het opgeven van de instellingen, klikt u op **Bekijken + maken**. Hierdoor worden de waarden gevalideerd. Zodra de validatie is geslaagd, kunt u de Bastion-resource maken.
1. Selecteer **Maken**.
1. Er wordt een bericht weergegeven met de melding dat uw implementatie aan de gang is. De status wordt op deze pagina weergegeven terwijl de resources worden gemaakt. Het maken en implementeren van de Bastion-resource duurt ongeveer 5 minuten.

## <a name="connect-to-a-vm"></a>Verbinding maken met een VM

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, verwijdert u de resources met behulp van de volgende stappen:

1. Typ de naam van uw resourcegroep in het **zoekvak** bovenaan de portal. Wanneer u uw resourcegroep in de zoekresultaten ziet, selecteert u deze.
1. Selecteer **Resourcegroep verwijderen**.
1. Voer de naam van uw resourcegroep in voor **TYP DE NAAM VAN DE RESOURCEGROEP:** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Bastion-host gemaakt en gekoppeld aan een virtueel netwerk, en vervolgens verbonden met een Windows-VM. U kunt ervoor kiezen om netwerkbeveiligingsgroepen te gebruiken met uw Azure Bastion-subnet. Zie hiervoor:

> [!div class="nextstepaction"]
> [Werken met NSG's](bastion-nsg.md)

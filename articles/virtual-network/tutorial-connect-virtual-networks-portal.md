---
title: Virtuele netwerken verbinden met VNet-peering - zelfstudie - Azure Portal
description: In deze zelfstudie leert u hoe u virtuele netwerken kunt verbinden met virtueel-netwerkpeering met behulp van Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/22/2020
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 71ba82cfd5f0f4166d25983f3f80530da45bdeac
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932944"
---
# <a name="tutorial-connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Zelfstudie: virtuele netwerken verbinden met virtueel-netwerkpeering met behulp van Azure Portal

U kunt virtuele netwerken met elkaar verbinden met virtueel-netwerk peering. Deze virtuele netwerken kunnen zich in de dezelfde regio of in andere regio's bevinden (ook wel bekend als Wereldwijd VNET-peering). Wanneer virtuele netwerken als peers zijn gekoppeld, kunnen resources in beide virtuele netwerken met elkaar communiceren met dezelfde latentie en bandbreedte als wanneer de resources zich in hetzelfde virtuele netwerk zouden bevinden. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Twee virtuele netwerken maken
> * Twee virtuele netwerken koppelen met virtueel-netwerkpeering
> * Een virtuele machine (VM) implementeren op elk van de virtuele netwerken
> * Communiceren tussen VM's

U kunt deze zelfstudie desgewenst volgen met behulp van de [Azure CLI](tutorial-connect-virtual-networks-cli.md) of [Azure PowerShell](tutorial-connect-virtual-networks-powershell.md).

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u een Azure-account met een actief abonnement hebben. Als u er nog geen hebt, kunt u [gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-virtual-networks"></a>Virtuele netwerken maken

1. Selecteer **Een resource maken** in de Azure-portal.
2. Selecteer **Netwerken** en selecteer vervolgens **Virtueel netwerk**.
3. Voer op het tabblad **Algemeen** de volgende informatie in of selecteer deze, en accepteer de standaardwaarden voor de overige instellingen:

    |Instelling|Waarde|
    |---|---|
    |Abonnement| Selecteer uw abonnement.|
    |Resourcegroep| Selecteer **Nieuwe maken** en voer *myResourceGroup* in.|
    |Regio| Selecteer **VS - oost**.|
    |Name|myVirtualNetwork1|

4. Voer op het tabblad **IP-adressen** 10.0.0.0/16 in voor het veld **Adresruimte**. Klik hieronder op de knop **Subnet toevoegen** en voer *Subnet1* voor **Subnetnaam** in en 10.0.0.0/24 voor **Subnetadresbereik**.
5. Selecteer **Controleren en maken** en selecteer vervolgens **Maken**.
   
5. Volg de stappen 1-5 nogmaals, maar dan met de volgende wijzigingen:

    |Instelling|Waarde|
    |---|---|
    |Naam|myVirtualNetwork2|
    |Adresruimte|10.1.0.0/16|
    |Resourcegroep| Selecteer **Bestaande gebruiken** en selecteer **myResourceGroup**.|
    |Subnetnaam | Subnet2|
    |Subnetadresbereik|10.1.0.0/24|

## <a name="peer-virtual-networks"></a>Peering van virtuele netwerken

1. Begin in het zoekvak bovenaan Azure Portal *MyVirtualNetwork1* te typen. Wanneer **myVirtualNetwork1** wordt weergegeven in de zoekresultaten, selecteert u dit.
2. Selecteer **Peerings** onder **instellingen** en selecteer **Toevoegen**, zoals in de volgende afbeelding:

    ![Peering maken](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. Voer de volgende informatie in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer **OK**.

    |Instelling|Waarde|
    |---|---|
    |Naam van het peeringnetwerk tussen myVirtualNetwork1 en het externe virtuele netwerk|myVirtualNetwork1-myVirtualNetwork2: wanneer de pagina voor het eerst wordt geladen, ziet u hier de tekst 'remote virtual network'. Nadat u het externe virtuele netwerk hebt gekozen, wordt de tekst 'remote virtual network' vervangen door de naam van het externe virtuele netwerk.|
    |Abonnement| Selecteer uw abonnement.|
    |Virtueel netwerk|myVirtualNetwork2: u selecteert het virtuele netwerk *myVirtualNetwork2* door **Virtueel netwerk** en vervolgens **myVirtualNetwork2 (myResourceGroup)** te selecteren. U kunt een virtueel netwerk in dezelfde regio of in een andere regio selecteren.|
    |Naam van het peeringnetwerk tussen myVirtualNetwork2 en myVirtualNetwork1|myVirtualNetwork2-myVirtualNetwork1|

    ![Peering-instellingen](./media/tutorial-connect-virtual-networks-portal/peering-settings-bidirectional.png)

    De **PEERINGSTATUS** is *Verbonden*, zoals wordt weergegeven in de volgende afbeelding:

    ![Peeringstatus](./media/tutorial-connect-virtual-networks-portal/peering-status-connected.png)

    Als u de status niet ziet, vernieuwt u de browser.

## <a name="create-virtual-machines"></a>Virtuele machines maken

Maak een VM in elk virtueel netwerk, zodat u er in een latere stap tussen kunt communiceren.

### <a name="create-the-first-vm"></a>De eerste VM maken

1. Selecteer **Een resource maken** in de Azure-portal.
2. Selecteer **Compute** en vervolgens **Windows Server 2016 Datacenter**. U kunt een ander besturingssysteem selecteren, maar in de overige stappen wordt ervan uitgegaan dat u **Windows Server 2016 Datacenter** hebt geselecteerd. 
3. Voer de volgende informatie voor **Basisinformatie** in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer vervolgens **Maken**:

    |Instelling|Waarde|
    |---|---|
    |Resourcegroep| Selecteer **Bestaande gebruiken** en selecteer **myResourceGroup**.|
    |Name|myVm1|
    |Locatie| Selecteer **VS - oost**.|
    |Gebruikersnaam| Voer een gebruikersnaam naar keuze in.|
    |Wachtwoord| Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
   
4. Selecteer een VM-grootte voor de optie **Grootte**.
5. Selecteer de volgende waarden onder **Netwerken**:

    |Instelling|Waarde|
    |---|---|
    |Virtueel netwerk| myVirtualNetwork1: als dit nog niet is geselecteerd, selecteert u **Virtueel netwerk** en vervolgens **myVirtualNetwork1**.|
    |Subnet| Subnet1: als dit nog niet is geselecteerd, selecteert u **Subnet** en vervolgens **Subnet1**.|
   
6. Selecteer **Netwerken**. Kies **Geselecteerde poorten toestaan** voor de optie **Openbare inkomende poorten**. Kies **RDP** voor de optie **Binnenkomende poorten selecteren** hieronder. 

7. Selecteer de knop **Beoordelen en maken** linksonder om de VM-implementatie te starten.

### <a name="create-the-second-vm"></a>De tweede VM maken

Volg de stappen 1-6 nogmaals, met de volgende wijzigingen:

|Instelling|Waarde|
|---|---|
|Naam | myVm2|
|Virtueel netwerk | myVirtualNetwork2|

Het maken van de VM's duurt enkele minuten. Ga niet verder met de overige stappen voordat beide VM's zijn gemaakt.

## <a name="communicate-between-vms"></a>Communiceren tussen VM's

1. Begin in het vak *Zoeken* bovenaan de portal *myVm1* te typen. Wanneer **myVm1** wordt weergegeven in de zoekresultaten, selecteert u deze.
2. Maak een externe bureaubladverbinding met *myVm1* door **Verbinding maken** te kiezen, zoals in de volgende afbeelding:

    ![Verbinding maken met de virtuele machine](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. Open het gedownloade RDP-bestand om verbinding te maken met de VM. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.
4. Voer de gebruikersnaam en het wachtwoord in die u bij het maken van de VM hebt opgegeven (mogelijk moet u **Meer opties** en **Een ander account gebruiken** selecteren om de aanmeldingsgegevens op te geven die u hebt ingevoerd tijdens het maken van de virtuele machine) en selecteer **OK**.
5. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Selecteer **Ja** om door te gaan met de verbinding.
6. In een latere stap wordt ping gebruikt om te communiceren met *myVm2* vanaf *myVm1*. Ping maakt gebruik van ICMP (Internet Control Message Protocol), wat standaard door Windows Firewall wordt geweigerd. Schakel op *myVm1* ICMP via Windows Firewall in, zodat u deze VM in een latere stap kunt pingen vanaf *myVm2* met behulp van PowerShell:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```
    
    Hoewel ping wordt gebruikt voor communicatie tussen VM's in deze zelfstudie, wordt niet aanbevolen ICMP via Windows Firewall toe te staan voor productie-implementaties.

7. Maak verbinding met *myVm2* door op *myVm1* de volgende opdracht in te voeren vanaf een opdrachtprompt:

    ```
    mstsc /v:10.1.0.4
    ```
    
8. Aangezien u ping hebt ingeschakeld op *myVm1*, kunt u deze nu pingen met het IP-adres:

    ```
    ping 10.0.0.4
    ```
    
9. Verbreek de RDP-sessies met zowel *myVm1* als *myVm2*.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet langer nodig hebt, verwijdert u de resourcegroep en alle resources die deze bevat: 

1. Voer *myResourceGroup* in het vak **Zoeken** bovenaan de portal in. Wanneer u **myResourceGroup** ziet in de zoekresultaten, selecteert u deze.
2. Selecteer **Resourcegroep verwijderen**.
3. Voer *myResourceGroup* in voor **TYP DE RESOURCEGROEPNAAM:** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over peering van virtuele netwerken](virtual-network-peering-overview.md)



---
title: Privé IP-adressen voor Vm's configureren-Azure Portal
description: Meer informatie over het configureren van privé-IP-adressen voor virtuele machines met behulp van de Azure Portal.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: kumud
ms.openlocfilehash: c8fdba59a8d31c064745c7a1904204359b386a7f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015917"
---
# <a name="configure-a-private-ip-address-for-a-vm-using-the-azure-portal"></a>Een privé-IP-adres configureren voor een virtuele machine met behulp van de Azure Portal

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

In de volgende voorbeeld stappen wordt ervan uitgegaan dat er al een eenvoudige omgeving is gemaakt. Als u de stappen wilt uitvoeren zoals ze worden weer gegeven in dit document, moet u eerst [een virtueel netwerk maken](quick-create-portal.md#create-a-virtual-network). In stap 3 gebruikt u deze waarden echter in plaats daarvan:

| Instelling | Waarde |
| ------- | ----- |
| Naam | *TestVNet* |
| Adresruimte | *192.168.0.0/16* |
| Resourcegroep | **TestRG** (Selecteer zo nodig **nieuwe maken** om deze te maken) |
| Subnet - Naam | *FrontEnd* |
| Subnet - adresbereik | *192.168.1.0/24* |

## <a name="create-a-vm-for-testing-static-private-ip-addresses"></a>Een virtuele machine maken voor het testen van statische privé-IP-adressen
Wanneer u een virtuele machine in de Resource Manager-implementatie modus maakt, kunt u geen statisch privé IP-adres instellen met behulp van de Azure Portal. In plaats daarvan maakt u eerst de virtuele machine. Vervolgens kunt u het privé-IP-adres instellen op statisch.

Voer de volgende stappen uit om een virtuele machine met de naam *DNS01* in het *frontend* -subnet van een virtueel netwerk met de naam *TestVNet* te maken:

1. Selecteer **Een resource maken** in het menu van de [Azure-portal](https://portal.azure.com).

    ![Een resource maken, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-resource.png)
2. Selecteer   >  **virtuele machine** berekenen.

    ![VM maken, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/compute-virtual-machine.png)
3. Geef in **basis principes** waarden op voor items zoals beschreven in de volgende tabel. Selecteer vervolgens **volgende &nbsp; : &nbsp; schijven** en vervolgens **volgende &nbsp; : &nbsp; netwerken**.

    | Item | Waarde |
    | --- | --- |
    | **Abonnement** | Uw huidige abonnement |
    | **Resourcegroep** | **TestRG** (selecteren in vervolg keuzelijst) |
    | **Naam van virtuele machine** | *DNS01* |
    | **Regio** | **(VS) VS - oost** |
    | **Afbeelding** | **Windows Server 2019 Datacenter** |
    | **Grootte** | **VM-grootte** van **B1ls**, **aanbieding** van **Standard** |
    | **Gebruikersnaam** | De gebruikers naam van uw beheerders account |
    | **Wachtwoord** | Het wacht woord voor de gebruikers naam van uw beheerders account |
    | **Wachtwoord bevestigen** | Het wacht woord opnieuw |

    ![Maak een virtuele machine op het tabblad basis beginselen, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-basics.png)
4. Geef in **netwerken** waarden op voor items, zoals wordt beschreven in de volgende tabel, en selecteer vervolgens **volgende**.

    | Item | Waarde |
    | --- | --- |
    | **Virtueel netwerk** | **TestVNet** |
    | **Subnet** | **FrontEnd** |

    ![Maak een virtuele machine op het tabblad netwerken, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-networking.png)
5. Kies in **beheer** onder **Diagnostische opslag account** de optie **vnetstorage**. Als dat opslag account niet wordt weer gegeven in de lijst, selecteert u **nieuwe maken**, geeft u een **naam** op voor *vnetstorage* en selecteert u **OK**. Selecteer ten slotte **controle &nbsp; + &nbsp; maken**.

    ![Maak een virtuele machine op het tabblad beheer, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-management.png)
6. Controleer de overzichts informatie in **bekijken en maken** en selecteer vervolgens **maken**.

    ![Bekijk + tabblad maken, maak een virtuele machine Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-review-create.png)

Het volgende bericht wordt weer gegeven wanneer de virtuele machine is gemaakt.

![Een virtuele machine maken Azure Portal](./media/virtual-networks-static-ip-arm-pportal/deployment-is-complete.png)

## <a name="retrieve-private-ip-address-information-for-a-vm"></a>Privé-IP-adres gegevens ophalen voor een virtuele machine
De privé-IP-adres gegevens voor uw nieuwe virtuele machine weer geven:

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw virtuele machine te vinden. Zoek en selecteer **virtuele machines**.

    ![Virtuele machines, zoekvak, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/search-box-virtual-machines.png)

2. Selecteer de naam van de nieuwe virtuele machine (**DNS01**).

    ![Lijst met virtuele machines, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/virtual-machine-list.png)

3. Kies **netwerken** en selecteer de exclusieve netwerk interface die wordt weer gegeven.

    ![Netwerk interface, netwerken, virtuele machine, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/networking-network-interface.png)

4. Kies **IP-configuraties** en selecteer de IP-configuratie die in de tabel wordt weer gegeven.

    ![IP-configuratie, netwerk interface, netwerken, virtuele machine, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/network-interface-ip-configurations.png)

5. In **particuliere IP-adres instellingen**, onder het **TestVNet/frontend** virtuele netwerk/subnet, ziet u de **toewijzings** waarde (**dynamisch** of **statisch**) en het **IP-adres**.

    ![Dynamische of statische toewijzing, oude instellingen voor persoonlijk IP-adres, IP-configuratie, netwerk interface, netwerken, virtuele machine, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-old.png)

## <a name="add-a-static-private-ip-address-to-an-existing-vm"></a>Een statisch privé-IP-adres toevoegen aan een bestaande virtuele machine
Een statisch privé-IP-adres toevoegen aan uw nieuwe VM:

1. Stel op de pagina IP-configuratie de toewijzing voor uw privé-IP-adres in op **statisch**.
2. Wijzig uw privé **-IP-adres** in *192.168.1.101* en selecteer vervolgens **Opslaan**.
   
    ![Dynamische of statische toewijzing, nieuwe privé IP-adres instellingen, IP-configuratie, netwerk interface, netwerken, virtuele machine, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-new.png)

> [!NOTE]
> Als u **merkt dat de** toewijzing nog steeds op **dynamisch** is ingesteld, wordt het IP-adres dat u hebt getypt, al gebruikt. Probeer een ander IP-adres.

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Een statisch privé-IP-adres uit een virtuele machine verwijderen
Het statische privé-IP-adres uit de virtuele machine verwijderen:

Stel op de pagina IP-configuratie de toewijzing voor uw privé-IP-adres in op **dynamisch** en selecteer vervolgens **Opslaan**.

## <a name="set-ip-addresses-within-the-operating-system"></a>IP-adressen in het besturings systeem instellen

Vanuit het besturings systeem van een virtuele machine mag u het *privé* -IP-adres dat is toegewezen aan de Azure-VM, niet statisch toewijzen. Voer een statische toewijzing van een privé-IP-adres uit wanneer dit nodig is, bijvoorbeeld wanneer [u een groot aantal IP-adressen toewijst aan vm's](virtual-network-multiple-ip-addresses-portal.md). Als u het privé-IP-adres hand matig instelt in het besturings systeem, moet u ervoor zorgen dat het overeenkomt met het privé-IP-adres dat is toegewezen aan de Azure- [netwerk interface](virtual-network-network-interface-addresses.md#change-ip-address-settings). Anders kunt u de verbinding met de VM verliezen. Meer informatie over instellingen voor [privé-IP-adressen](virtual-network-network-interface-addresses.md#private).

U moet ook het *open bare* IP-adres dat is toegewezen aan een virtuele machine van Azure niet hand matig toewijzen binnen het besturings systeem van de virtuele machine.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het beheren van [IP-adres instellingen](virtual-network-network-interface-addresses.md).

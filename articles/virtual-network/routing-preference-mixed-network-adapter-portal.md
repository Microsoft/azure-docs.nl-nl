---
title: Beide routerings voorkeurs opties configureren voor een virtuele machine-Azure Portal
description: Meer informatie over het inschakelen van beide routerings voorkeurs opties
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: d9bb00077b1d96d13e15861cac477f913a002030
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679658"
---
# <a name="configure-both-routing-preference-options-for-a-virtual-machine"></a>Beide routerings voorkeurs opties voor een virtuele machine configureren

In dit artikel wordt beschreven hoe u beide [routerings voorkeuren](./routing-preference-overview.md) configureert (Internet en micro soft Global Network) voor een virtuele machine (VM). Dit wordt bereikt met behulp van twee virtuele netwerk interfaces, één netwerk interface met een openbaar IP-adres dat via het globale netwerk van micro soft wordt gerouteerd en de andere met een openbaar IP-adres dat via een ISP-netwerk wordt gerouteerd.

## <a name="prerequisites"></a>Vereisten

Maak een virtuele machine met een openbaar IP-adres volgens de instructies die worden beschreven in [een virtuele machine maken met een statisch openbaar IP-adres](./virtual-network-deploy-static-pip-arm-portal.md) met behulp van de Azure Portal Azure Portal. De standaard keuze voor routerings voorkeur voor het open bare IP-adres is via het **wereld wijde netwerk van micro soft**. Wanneer u een virtuele machine hebt gemaakt met een openbaar IP-adres, voegt u een tweede open bare IP-adres toe aan de VM die verkeer via een transit ISP-netwerk stuurt met de routerings voorkeur die is geconfigureerd als **Internet**.

## <a name="create-a-public-ip-address-with-a-routing-preference-choice-internet"></a>Een openbaar IP-adres maken met een voorkeurs instelling voor het routeren van Internet
1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer **Een resource maken**. 
3. Typ *openbaar IP-adres* in het zoekvak.
4. Selecteer **openbaar IP-adres** in de zoek resultaten. Selecteer vervolgens op de pagina **openbaar IP-adres** **maken**.
5. Selecteer **Internet** in de **Voorkeurs instellingen voor route ring** .

      ![Een openbaar IP-adres maken](./media/routing-preference-portal/public-ip-new.png)

    > [!NOTE]
    > Openbare IP-adressen worden gemaakt met een IPv4- of IPv6-adres. Routerings voorkeur ondersteunt momenteel echter alleen IPV4.

## <a name="create-a-network-interface-and-associate-the-public-ip"></a>Een netwerk interface maken en het open bare IP-adres koppelen

1. Maak een [netwerk interface](./routing-preference-overview.md) met behulp van de Azure Portal met de standaard instellingen. 
1. [Koppel het open bare IP-adres aan de netwerk interface](./associate-public-ip-address-vm.md) die in de vorige sectie is gemaakt. Het kan een paar seconden duren voordat een IP-adres wordt weer gegeven. Bekijk het open bare IP-adres dat is toegewezen aan de IP-configuratie, zoals wordt weer gegeven:

    ![Het open bare IP-adres koppelen](./media/routing-preference-mixed-network-adapter-portal/public-ip.png)

## <a name="attach-network-interface-to-the-vm"></a>Netwerk interface koppelen aan de virtuele machine

1. [Koppel de netwerk interface die in de vorige sectie is gemaakt aan de virtuele machine](./virtual-network-network-interface-vm.md).
2. U kunt nu twee virtuele netwerk interfaces weer geven die zijn gekoppeld aan de virtuele machine, een met een openbaar IP-adres dat via het wereld wijde micro soft-netwerk wordt gerouteerd en de andere gerouteerd via een ISP-netwerk zoals wordt weer gegeven:  ![ een netwerk interface koppelen aan een VM](./media/routing-preference-mixed-network-adapter-portal/mixed-network-adapter.png) 

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [open bare IP met routerings voorkeur](routing-preference-overview.md).
- [Routerings voorkeur configureren voor een virtuele machine](tutorial-routing-preference-virtual-machine-portal.md).
- [Configureer routerings voorkeur voor een openbaar IP-adres met behulp van de Power shell](routing-preference-powershell.md).
- Meer informatie over [open bare IP-adressen](./public-ip-addresses.md#public-ip-addresses) in Azure.
- Meer informatie over alle [open bare IP-adres instellingen](virtual-network-public-ip-address.md#create-a-public-ip-address).
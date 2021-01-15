---
title: Routerings voorkeur configureren voor een virtuele machine-Azure Portal
description: Meer informatie over het maken van een virtuele machine met een openbaar IP-adres met een routerings voorkeurs keuze met behulp van de Azure Portal.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/02/2020
ms.author: mnayak
ms.openlocfilehash: beb670106690645a80aa12b930e3a005efb4282a
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98221643"
---
# <a name="configure-routing-preference-for-a-vm-using-the-azure-portal"></a>Routerings voorkeur configureren voor een virtuele machine met behulp van de Azure Portal

In dit artikel wordt beschreven hoe u routerings voorkeur configureert voor een virtuele machine. Het Internet gebonden verkeer van de virtuele machine wordt doorgestuurd via het ISP-netwerk wanneer u kiest voor **Internet** als de voorkeurs methode voor route ring. De standaard routering is via het wereld wijde netwerk van micro soft.

In dit artikel wordt beschreven hoe u een virtuele machine maakt met een openbaar IP-adres dat is ingesteld om verkeer via het open bare Internet te routeren met behulp van de Azure Portal.

> [!IMPORTANT]
> De voor keuren voor route ring is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://preview.portal.azure.com/).

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Selecteer **+ Een resource maken** in de linkerbovenhoek van Azure Portal.
2. Selecteer **Compute** en selecteer vervolgens **Windows Server 2016 VM** of een ander besturings systeem van uw keuze.
3. Voer de volgende informatie in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer **OK**:

    |Instelling|Waarde|
    |---|---|
    |Naam|myVM|
    |Gebruikersnaam| Voer een gebruikersnaam naar keuze in.|
    |Wachtwoord| Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Abonnement| Selecteer uw abonnement.|
    |Resourcegroep| Selecteer **Bestaande gebruiken** en selecteer **myResourceGroup**.|
    |Locatie| Selecteer **VS - oost**|

4. Selecteer een grootte voor de virtuele machine en selecteer **Selecteren**.
5. Klik op het tabblad **netwerken** op **Nieuw maken** voor **openbaar IP-adres**.
6. Voer *myPublicIpAddress* in, selecteer SKU als **standaard** en selecteer vervolgens routerings voorkeur **Internet** en klik vervolgens op **OK**, zoals wordt weer gegeven in de volgende afbeelding:

   ![Statisch selecteren](./media/tutorial-routing-preference-virtual-machine-portal/routing-preference-internet-new.png)

6. Selecteer een poort of geen poorten onder **Selecteer open bare binnenkomende poorten**. Portal 3389 is geselecteerd om externe toegang tot de virtuele Windows Server-machine via internet in te scha kelen. Het openen van poort 3389 van Internet wordt niet aanbevolen voor productie werkbelastingen.

   ![Poort selecteren](./media/tutorial-routing-preference-virtual-machine-portal/pip-ports-new.png)

7. Accepteer de overige standaard instellingen en selecteer **OK**.
8. Op de pagina **Overzicht** selecteert u **Maken**. De implementatie van de virtuele machine duurt een paar minuten.
9. Wanneer de virtuele machine is geïmplementeerd, voert u *myPublicIpAddress* in het zoekvak boven aan de portal in. Wanneer **myPublicIpAddress** wordt weer gegeven in de zoek resultaten, selecteert u dit.
10. U kunt het open bare IP-adres weer geven dat is toegewezen en dat het adres is toegewezen aan de virtuele **myVM** -machine, zoals wordt weer gegeven in de volgende afbeelding:

    ![Scherm afbeelding toont de open bare NIC-mynic voor de netwerk interface.](./media/tutorial-routing-preference-virtual-machine-portal/pip-properties-new.png)

11. Selecteer **netwerken**, klik op NIC- **mynic** en selecteer vervolgens het open bare IP-adres om te bevestigen dat de routerings voorkeur is toegewezen als **Internet**.

    ![Scherm afbeelding toont de voor keur voor het adres en de route ring van een openbaar I P-adres.](./media/tutorial-routing-preference-virtual-machine-portal/pip-routing-internet-new.png)

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resourcegroep en alle gerelateerde resources die deze bevat verwijderen wanneer u deze niet meer nodig hebt:

1. Voer *myResourceGroup* in het vak **Zoeken** bovenaan de portal in. Wanneer u **myResourceGroup** ziet in de zoekresultaten, selecteert u deze.
2. Selecteer **Resourcegroep verwijderen**.
3. Voer *myResourceGroup* in voor **TYP DE RESOURCEGROEPNAAM:** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [open bare IP met routerings voorkeur](routing-preference-overview.md).
- Meer informatie over [open bare IP-adressen](./public-ip-addresses.md#public-ip-addresses) in Azure.
- Meer informatie over alle [open bare IP-adres instellingen](virtual-network-public-ip-address.md#create-a-public-ip-address).
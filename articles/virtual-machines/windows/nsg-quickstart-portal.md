---
title: Poorten openen voor een virtuele machine met behulp van de Azure Portal
description: Meer informatie over het openen van een poort/het maken van een eind punt voor uw VM met behulp van de Azure Portal
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/27/2020
ms.author: cynthn
ms.openlocfilehash: 28b3ee98d44e1567d9e25cf9237015396975ab01
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550447"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Poorten openen voor een virtuele machine met de Azure Portal
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Aanmelden bij Azure
Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

1. Zoek en selecteer de resource groep voor de virtuele machine, klik op **toevoegen**, zoek naar en selecteer **netwerk beveiligings groep**.

1. Selecteer **Maken**.

    Het venster **netwerk beveiligings groep maken** wordt geopend.

    ![Een netwerkbeveiligingsgroep maken](./media/nsg-quickstart-portal/create-nsg.png)

1. Voer een naam in voor de netwerk beveiligings groep. 

1. Selecteer of maak een resource groep en selecteer vervolgens een locatie.

1. Selecteer **maken** om de netwerk beveiligings groep te maken.

## <a name="create-an-inbound-security-rule"></a>Een regel voor binnenkomende beveiliging maken

1. Selecteer de nieuwe netwerk beveiligings groep. 

1. Selecteer **binnenkomende beveiligings regels** in het menu links en selecteer vervolgens **toevoegen**.

    ![Scha kelen naar de pagina Geavanceerd](./media/nsg-quickstart-portal/advanced.png)

1. Schakel op de pagina **een regel voor binnenkomende beveiliging toevoegen** de optie **Geavanceerd** van de **basis** aan de bovenkant van de pagina in. 

1. Kies een algemene **service** in de vervolg keuzelijst, zoals **http**. U kunt ook **aangepast** selecteren als u een specifieke poort wilt opgeven. 

1. Desgewenst kunt u de **prioriteit** of **naam** wijzigen. De prioriteit is van invloed op de volg orde waarin regels worden toegepast: de lagere numerieke waarde, de eerdere regel wordt toegepast.

1. Selecteer **toevoegen** om de regel te maken.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Uw netwerk beveiligings groep koppelen aan een subnet

De laatste stap bestaat uit het koppelen van uw netwerkbeveiligingsgroep aan een subnet of een specifieke netwerkinterface. In dit voor beeld koppelen we de netwerk beveiligings groep aan een subnet. 

1. Selecteer **subnetten** in het menu links en selecteer vervolgens **koppelen**.

1. Selecteer het virtuele netwerk en selecteer vervolgens het juiste subnet.

    ![Een netwerk beveiligings groep koppelen aan een virtueel netwerk](./media/nsg-quickstart-portal/select-vnet-subnet.png)

1. Selecteer **OK** wanneer u klaar bent.

## <a name="additional-information"></a>Aanvullende informatie

U kunt [de stappen in dit artikel ook uitvoeren met behulp van Azure PowerShell](nsg-quickstart-powershell.md).

Met de opdrachten die in dit artikel worden beschreven, kunt u snel verkeer ontvangen dat naar uw virtuele machine stroomt. Netwerk beveiligings groepen bieden veel fantastische functies en granulatie voor het beheren van de toegang tot uw resources. Zie [netwerk verkeer filteren met een netwerk beveiligings groep](../../virtual-network/tutorial-filter-network-traffic.md)voor meer informatie.

Voor Maxi maal beschik bare webtoepassingen kunt u overwegen uw Vm's achter een Azure-load balancer te plaatsen. De load balancer distribueert verkeer naar Vm's, met een netwerk beveiligings groep die verkeer filtering biedt. Zie [taak verdeling van virtuele Windows-machines in azure om een Maxi maal beschik bare toepassing te maken](tutorial-load-balancer.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u een netwerk beveiligings groep gemaakt, een regel voor binnenkomende verbindingen gemaakt waarmee HTTP-verkeer wordt toegestaan op poort 80, en die regel vervolgens aan een subnet is gekoppeld. 

In de volgende artikelen vindt u informatie over het maken van meer gedetailleerde omgevingen:
- [Overzicht van Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Beveiligingsgroepen](../../virtual-network/network-security-groups-overview.md)

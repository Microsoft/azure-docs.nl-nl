---
title: Een openbaar uitgaand IP-adres instellen voor ISE's
description: Meer informatie over het instellen van één openbaar uitgaand IP-adres voor ISE's (Integration Service Environments) in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: 7ec495dd52607f2f65e0bef50489dd182c2a3253
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874185"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>Eén IP-adres instellen voor een of meer integratieserviceomgevingen in Azure Logic Apps

Wanneer u met Azure Logic Apps werkt, kunt u een [ISE *(Integration Service Environment)*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) instellen voor het hosten van logische apps die toegang nodig hebben tot resources in een [virtueel Azure-netwerk.](../virtual-network/virtual-networks-overview.md) Wanneer u meerdere ISE-exemplaren hebt die toegang nodig hebben tot andere eindpunten met [](../virtual-network/virtual-networks-overview.md#filter-network-traffic) IP-beperkingen, implementeert u een [Azure Firewall of](../firewall/overview.md) een virtueel netwerkapparaat in uw virtuele netwerk en routeert u uitgaand verkeer via die firewall of het virtuele netwerkapparaat. Vervolgens kunt u ervoor zorgen dat alle ISE-exemplaren in uw virtuele netwerk één openbaar, statisch en voorspelbaar IP-adres gebruiken om te communiceren met de doelsystemen die u wilt gebruiken. Op die manier hoeft u geen extra firewall-openingen op uw doelsystemen in te stellen voor elke ISE.

In dit onderwerp ziet u hoe u uitgaand verkeer routeert via een Azure Firewall, maar u kunt vergelijkbare concepten toepassen op een virtueel netwerkapparaat, zoals een firewall van derden vanuit de Azure Marketplace. Hoewel dit onderwerp gericht is op het instellen van meerdere ISE-exemplaren, kunt u deze methode ook gebruiken voor één ISE wanneer uw scenario het aantal IP-adressen moet beperken dat toegang nodig heeft. Overweeg of de extra kosten voor de firewall of het virtuele netwerkapparaat zinvol zijn voor uw scenario. Meer informatie over [Azure Firewall prijzen.](https://azure.microsoft.com/pricing/details/azure-firewall/)

## <a name="prerequisites"></a>Vereisten

* Een Azure-firewall die wordt uitgevoerd in hetzelfde virtuele netwerk als uw ISE. Als u geen firewall hebt, voegt u eerst [een subnet](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) met de naam toe `AzureFirewallSubnet` aan uw virtuele netwerk. Vervolgens kunt u [een firewall in uw](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) virtuele netwerk maken en implementeren.

* Een [Azure-routetabel.](../virtual-network/manage-route-table.md) Als u er nog geen hebt, maakt u [eerst een routetabel.](../virtual-network/manage-route-table.md#create-a-route-table) Zie Routering van virtueel netwerkverkeer voor meer informatie [over routering.](../virtual-network/virtual-networks-udr-overview.md)

## <a name="set-up-route-table"></a>Routetabel instellen

1. Selecteer in [Azure Portal](https://portal.azure.com)de routetabel, bijvoorbeeld:

   ![Routetabel selecteren met regel voor het om leiden van uitgaand verkeer](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. Als [u een nieuwe route wilt toevoegen,](../virtual-network/manage-route-table.md#create-a-route)selecteert u routes toevoegen in het menu   >  **routetabel.**

   ![Route toevoegen om uitgaand verkeer om te leiden](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. Stel in **het deelvenster Route** toevoegen de nieuwe [route](../virtual-network/manage-route-table.md#create-a-route) in met een regel die aangeeft dat al het uitgaande verkeer naar het doelsysteem dit gedrag volgt:

   * Maakt gebruik [**van het virtuele apparaat**](../virtual-network/virtual-networks-udr-overview.md#user-defined) als het volgende hoptype.

   * Gaat naar het privé-IP-adres voor het firewall-exemplaar als het adres van de volgende hop.

     Als u dit IP-adres wilt vinden, selecteert u in het firewallmenu **Overzicht,** gaat u naar het adres onder **Privé-IP-adres,** bijvoorbeeld:

     ![Privé-IP-adres van firewall zoeken](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Hier is een voorbeeld dat laat zien hoe een dergelijke regel eruit kan zien:

   ![Regel instellen voor het om leiden van uitgaand verkeer](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Routenaam** | <*unique-route-name*> | Een unieke naam voor de route in de routetabel |
   | **Adresvoorvoegsel** | <*doeladres*> | Het adresprefix voor het doelsysteem waar u uitgaand verkeer naartoe wilt laten gaan. Zorg ervoor dat u [classless Inter-Domain Routing (CIDR)-notatie](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) voor dit adres gebruikt. In dit voorbeeld is dit adres voorvoegsel voor een SFTP-server, die wordt beschreven in de sectie [Netwerkregel instellen.](#set-up-network-rule) |
   | **Volgend hoptype** | **Virtueel apparaat** | Het [hoptype](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) dat wordt gebruikt door uitgaand verkeer |
   | **Adres van de volgende hop** | <*firewall-privé-IP-adres*> | Het privé-IP-adres voor uw firewall |
   |||

<a name="set-up-network-rule"></a>

## <a name="set-up-network-rule"></a>Netwerkregel instellen

1. Zoek en Azure Portal firewall in het dialoogvenster. Selecteer in het firewallmenu onder **Instellingen** de optie **Regels.** Selecteer in het deelvenster Regels de optie **Netwerkregelverzameling**  >  **Netwerkregelverzameling toevoegen.**

   ![Verzameling netwerkregel toevoegen aan firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. Voeg in de verzameling een regel toe die verkeer naar het doelsysteem toestaat.

   Stel bijvoorbeeld dat u een logische app hebt die wordt uitgevoerd in een ISE en moet communiceren met een SFTP-server. U maakt een netwerkregelverzameling met de naam . Deze bevat `LogicApp_ISE_SFTP_Outbound` een netwerkregel met de naam `ISE_SFTP_Outbound` . Deze regel staat verkeer toe van het IP-adres van elk subnet waarop uw ISE wordt uitgevoerd in uw virtuele netwerk naar de SFTP-doelserver met behulp van het privé-IP-adres van uw firewall.

   ![Netwerkregel instellen voor firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Eigenschappen van netwerkregelverzameling**

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Naam** | <*network-rule-collection-name*> | De naam voor uw netwerkregelverzameling |
   | **Priority** | <*prioriteitsniveau*> | De volgorde van de prioriteit die moet worden gebruikt voor het uitvoeren van de regelverzameling. Zie Wat zijn enkele Azure Firewall [concepten? voor meer informatie.](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts) |
   | **Actie** | **Toestaan** | Het actietype dat voor deze regel moet worden uitvoeren |
   |||

   **Eigenschappen van netwerkregel**

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Naam** | <*network-rule-name*> | De naam voor uw netwerkregel |
   | **Protocol** | <*verbindingsprotocollen*> | De verbindingsprotocollen die moeten worden gebruikt. Als u bijvoorbeeld NSG-regels gebruikt, selecteert u **zowel TCP** als **UDP,** niet alleen **TCP.** |
   | **Bronadressen** | <*ISE-subnetadressen*> | De IP-adressen van het subnet waar uw ISE wordt uitgevoerd en waar het verkeer van uw logische app vandaan komt |
   | **Doeladressen** | <*destination-IP-address*> | Het IP-adres voor het doelsysteem waar u uitgaand verkeer naartoe wilt laten gaan. In dit voorbeeld is dit IP-adres voor de SFTP-server. |
   | **Doelpoorten** | <*doelpoorten*> | Poorten die uw doelsysteem gebruikt voor binnenkomende communicatie |
   |||

   Zie de volgende artikelen voor meer informatie over netwerkregels:

   * [Een netwerkregel configureren](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Regels voor de logicaverwerking in Azure Firewall](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Veelgestelde vragen over Azure Firewall](../firewall/firewall-faq.yml)
   * [Azure PowerShell: New-AzFirewallNetworkRule](/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI: az network firewall network-rule](/cli/azure/network/firewall/network-rule#az_network_firewall_network_rule_create)

## <a name="next-steps"></a>Volgende stappen

* [Verbinding maken met virtuele Azure-netwerken vanuit Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

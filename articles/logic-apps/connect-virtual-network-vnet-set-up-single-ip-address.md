---
title: Een openbaar uitgaand IP-adres instellen voor ISEs
description: Meer informatie over het instellen van één openbaar uitgaand IP-adres voor integratie service omgevingen (ISEs) in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: e88c4bf05d88007a6e19b568f1bc1085e24b0325
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211053"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>Stel één IP-adres in voor een of meer integratie service omgevingen in Azure Logic Apps

Wanneer u met Azure Logic Apps werkt, kunt u een [ISE ( *Integration service Environment* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) instellen voor het hosten van logische apps die toegang nodig hebben tot bronnen in een [virtueel Azure-netwerk](../virtual-network/virtual-networks-overview.md). Wanneer u meerdere ISE-exemplaren hebt die toegang nodig hebben tot andere eind punten met IP-beperkingen, implementeert u een [Azure firewall](../firewall/overview.md) of een [virtueel netwerk apparaat](../virtual-network/virtual-networks-overview.md#filter-network-traffic) in uw virtuele netwerk en stuurt u uitgaand verkeer via die firewall of het virtuele netwerk apparaat. U kunt de ISE-instanties in uw virtuele netwerk vervolgens gebruiken om één openbaar, statisch en voorspelbaar IP-adres te communiceren met de gewenste doel systemen. Op die manier hoeft u geen aanvullende firewall-openingen op uw doel systemen in te stellen voor elke ISE.

In dit onderwerp wordt beschreven hoe u uitgaand verkeer via een Azure Firewall stuurt, maar u kunt vergelijk bare concepten Toep assen op een virtueel netwerk apparaat, zoals een firewall van derden vanuit Azure Marketplace. Dit onderwerp richt zich op het instellen van meerdere ISE-instanties, maar u kunt deze methode ook gebruiken voor één ISE wanneer uw scenario het aantal IP-adressen dat toegang nodig heeft beperken. Bepaal of de extra kosten voor de firewall of het virtuele netwerk apparaat logisch kunnen zijn voor uw scenario. Meer informatie over [Azure firewall prijzen](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="prerequisites"></a>Vereisten

* Een Azure-firewall die wordt uitgevoerd in hetzelfde virtuele netwerk als uw ISE. Als u geen firewall hebt, moet u eerst [een subnet](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) met de naam `AzureFirewallSubnet` aan uw virtuele netwerk toevoegen. U kunt vervolgens [een firewall maken en implementeren](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) in uw virtuele netwerk.

* Een Azure- [route tabel](../virtual-network/manage-route-table.md). Als u er nog geen hebt, moet u eerst [een route tabel maken](../virtual-network/manage-route-table.md#create-a-route-table). Zie [virtueel netwerk verkeer routeren](../virtual-network/virtual-networks-udr-overview.md)voor meer informatie over route ring.

## <a name="set-up-route-table"></a>Route tabel instellen

1. Selecteer in de [Azure Portal](https://portal.azure.com)de route tabel, bijvoorbeeld:

   ![Route tabel selecteren met regel voor het omleiden van uitgaand verkeer](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. Als u [een nieuwe route wilt toevoegen](../virtual-network/manage-route-table.md#create-a-route), selecteert u in het menu route tabel **routes**  >  **toevoegen**.

   ![Route toevoegen voor het omleiden van uitgaand verkeer](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. Stel in het deel venster **route toevoegen** [de nieuwe route](../virtual-network/manage-route-table.md#create-a-route) in met een regel waarmee wordt aangegeven dat het uitgaande verkeer naar het doel systeem het volgende gedrag volgt:

   * Maakt gebruik van het [**virtuele apparaat**](../virtual-network/virtual-networks-udr-overview.md#user-defined) als het type van de volgende hop.

   * Gaat naar het privé-IP-adres voor de firewall instantie als het adres van de volgende hop.

     Als u dit IP-adres wilt zoeken, selecteert u in het menu Firewall de optie **overzicht** en zoekt u het adres onder **persoonlijk IP-adres**, bijvoorbeeld:

     ![Privé-IP-adres van firewall zoeken](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Hier volgt een voor beeld waarin wordt getoond hoe een dergelijke regel eruit kan zien:

   ![Regel voor het omleiden van uitgaand verkeer instellen](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Routenaam** | <*unieke route naam*> | Een unieke naam voor de route in de route tabel |
   | **Adresvoorvoegsel** | <*doel adres*> | Het adres voorvoegsel voor het doel systeem waar u uitgaand verkeer wilt doen. Zorg ervoor dat u de [CIDR-notatie (klasseloze Inter-Domain route ring)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) gebruikt voor dit adres. In dit voor beeld is dit adres voorvoegsel voor een SFTP-server, die wordt beschreven in de sectie [netwerk regel instellen](#set-up-network-rule). |
   | **Volgend hoptype** | **Virtueel apparaat** | Het [type hop](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) dat wordt gebruikt door uitgaand verkeer |
   | **Adres van de volgende hop** | <*Firewall-persoonlijk IP-adres*> | Het privé-IP-adres voor uw firewall |
   |||

<a name="set-up-network-rule"></a>

## <a name="set-up-network-rule"></a>Netwerk regel instellen

1. Zoek en selecteer uw firewall in de Azure Portal. Selecteer in het menu Firewall onder **instellingen** de optie **regels**. Selecteer in het deel venster regels **netwerk regel verzameling**  >  **netwerk regel verzameling toevoegen**.

   ![Netwerk regel verzameling toevoegen aan firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. Voeg in de verzameling een regel toe die verkeer naar het doel systeem toestaat.

   Stel bijvoorbeeld dat u een logische app hebt die in een ISE wordt uitgevoerd en moet communiceren met een SFTP-server. U maakt een netwerk regel verzameling met de naam `LogicApp_ISE_SFTP_Outbound` , die een netwerk regel bevat met de naam `ISE_SFTP_Outbound` . Deze regel staat verkeer toe van het IP-adres van elk subnet waar uw ISE wordt uitgevoerd in uw virtuele netwerk naar de doel-SFTP-server met behulp van het privé-IP-adres van uw firewall.

   ![Netwerk regel voor Firewall instellen](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Eigenschappen van de verzameling netwerk regels**

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Naam** | <*netwerk-regel-verzameling-naam*> | De naam voor uw netwerk regel verzameling |
   | **Priority** | <*prioriteits niveau*> | De volg orde van prioriteit die moet worden gebruikt voor het uitvoeren van de regel verzameling. Zie [Wat zijn enkele Azure firewall concepten](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts)? voor meer informatie. |
   | **Actie** | **Toestaan** | Het actie type dat moet worden uitgevoerd voor deze regel |
   |||

   **Eigenschappen van netwerk regel**

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Naam** | <*netwerk-regel naam*> | De naam voor uw netwerk regel |
   | **Protocol** | <*verbinding-protocollen*> | De verbindings protocollen die moeten worden gebruikt. Als u bijvoorbeeld NSG-regels gebruikt, selecteert u zowel **TCP** als **UDP**, niet alleen **TCP**. |
   | **Bron adressen** | <*ISE-subnet-adressen*> | Het subnet-IP-adres waar uw ISE wordt uitgevoerd en waar verkeer van uw logische app afkomstig is |
   | **Doel adressen** | <*doel-IP-adres*> | Het IP-adres van het doel systeem waar u uitgaand verkeer wilt. In dit voor beeld is dit IP-adres voor de SFTP-server. |
   | **Doelpoorten** | <*doel poorten*> | Poorten die het doel systeem gebruikt voor binnenkomende communicatie |
   |||

   Zie de volgende artikelen voor meer informatie over netwerk regels:

   * [Een netwerkregel configureren](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Regels voor de logicaverwerking in Azure Firewall](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Veelgestelde vragen over Azure Firewall](../firewall/firewall-faq.yml)
   * [Azure PowerShell: New-AzFirewallNetworkRule](/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI: AZ Network Firewall Network-Rule](/cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>Volgende stappen

* [Verbinding maken met virtuele Azure-netwerken vanuit Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

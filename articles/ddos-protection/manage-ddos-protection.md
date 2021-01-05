---
title: Azure DDoS Protection Standard beheren met de Azure Portal
description: Meer informatie over het gebruik van Azure DDoS Protection Standard om een aanval te verhelpen.
services: ddos-protection
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: ddos-protection
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: fd154ec40ed4d1650e44a67fd5d362a2bc0c519d
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97813935"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard"></a>Snelstartgids: Azure DDoS Protection standaard maken en configureren

Ga aan de slag met Azure DDoS Protection Standard met behulp van de Azure Portal. 

In een DDoS-beschermings plan wordt een set virtuele netwerken gedefinieerd waarvoor DDoS-beschermings standaard is ingeschakeld, via abonnementen. U kunt één DDoS-beveiligings plan configureren voor uw organisatie en virtuele netwerken vanuit meerdere abonnementen koppelen aan hetzelfde abonnement. 

In deze Quick Start maakt u een DDoS-beschermings plan en koppelt u het aan een virtueel netwerk. 

## <a name="prerequisites"></a>Vereisten

- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
- Meld u aan bij Azure Portal op https://portal.azure.com. Zorg ervoor dat uw account is toegewezen aan de rol [netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste acties zijn toegewezen die worden vermeld in de hand leiding voor [machtigingen](manage-permissions.md).

## <a name="create-a-ddos-protection-plan"></a>Een DDoS-beschermings plan maken

1. Selecteer in de linkerbovenhoek van het Azure Portal **een resource maken** .
2. Zoek naar de term *DDoS*. Wanneer **DDoS-beschermings plan** wordt weer gegeven in de zoek resultaten, selecteert u dit.
3. Selecteer **Maken**.
4. Voer de volgende waarden in of Selecteer deze, en selecteer vervolgens **maken**:

    |Instelling        |Waarde                                              |
    |---------      |---------                                          |
    |Naam           | Voer _MyDdosProtectionPlan_ in.                     |
    |Abonnement   | Selecteer uw abonnement.                         |
    |Resourcegroep | Selecteer **nieuwe maken** en voer _MyResourceGroup_ in.|
    |Locatie       | Voer _VS-Oost_ in.                                  |

## <a name="enable-ddos-protection-for-a-virtual-network"></a>DDoS-beveiliging inschakelen voor een virtueel netwerk

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>DDoS-beveiliging inschakelen voor een nieuw virtueel netwerk

1. Selecteer in de linkerbovenhoek van het Azure Portal **een resource maken** .
2. Selecteer **Netwerken** en selecteer vervolgens **Virtueel netwerk**.
3. Voer de volgende waarden in of Selecteer deze, accepteer de resterende standaard instellingen en selecteer vervolgens **maken**:

    | Instelling         | Waarde                                           |
    | ---------       | ---------                                       |
    | Naam            | Voer _MyVnet_ in.                                 |
    | Abonnement    | Selecteer uw abonnement.                                    |
    | Resourcegroep  | Selecteer **bestaande gebruiken** en selecteer vervolgens **MyResourceGroup** |
    | Locatie        | _VS-Oost_ invoeren                                                    |
    | DDoS Protection Standard | Selecteer **Inschakelen**. Het plan dat u selecteert, kan zich in hetzelfde of een ander abonnement bevindt dan het virtuele netwerk, maar beide abonnementen moeten aan dezelfde Azure Active Directory-Tenant zijn gekoppeld.|

U kunt een virtueel netwerk niet verplaatsen naar een andere resource groep of een ander abonnement wanneer DDoS standaard is ingeschakeld voor het virtuele netwerk. Als u een virtueel netwerk wilt verplaatsen waarbij DDoS Standard is ingeschakeld, moet u eerst DDoS-standaard uitschakelen, het virtuele netwerk verplaatsen en vervolgens DDoS Standard inschakelen. Na de verplaatsing worden de automatisch afgestemde beleids drempels voor alle beveiligde open bare IP-adressen in het virtuele netwerk opnieuw ingesteld.

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>DDoS-beveiliging inschakelen voor een bestaand virtueel netwerk

1. Maak een DDoS-beschermings plan door de stappen in [een DDoS-beveiligings plan maken](#create-a-ddos-protection-plan)uit te voeren, als u geen bestaand DDoS-beveiligings abonnement hebt.
2. Selecteer in de linkerbovenhoek van het Azure Portal **een resource maken** .
3. Voer de naam in van het virtuele netwerk waarvoor u DDoS Protection standaard wilt inschakelen in het **vak resources, services en documenten zoeken** bovenaan de portal. Wanneer de naam van het virtuele netwerk wordt weer gegeven in de zoek resultaten, selecteert u dit.
4. Selecteer **DDoS Protection** onder **instellingen**.
5. selecteer **Standaard**. Selecteer onder **DDoS-beschermings plan** een bestaand DDoS-beveiligings plan of het plan dat u hebt gemaakt in stap 1 en selecteer vervolgens **Opslaan**. Het plan dat u selecteert, kan zich in hetzelfde of een ander abonnement bevindt dan het virtuele netwerk, maar beide abonnementen moeten aan dezelfde Azure Active Directory-Tenant zijn gekoppeld.

### <a name="enable-ddos-protection-for-all-virtual-networks"></a>DDoS-beveiliging inschakelen voor alle virtuele netwerken

Met dit [beleid](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Policy%20-%20Virtual%20Networks%20should%20be%20associated%20with%20an%20Azure%20DDoS%20Protection%20Standard%20plan) worden alle virtuele netwerken gedetecteerd in een gedefinieerd bereik waarvoor geen DDoS Protection standaard is ingeschakeld. vervolgens kunt u desgewenst een herstel taak maken waarmee de koppeling wordt gemaakt om het VNet te beveiligen. Zie voor gedetailleerde stapsgewijze instructies voor het implementeren van dit beleid https://aka.ms/ddosvnetpolicy-techcommunity .

## <a name="validate-and-test"></a>Valideren en testen

Controleer eerst de details van uw DDoS-beveiligings plan:

1. Selecteer **alle services** bovenaan, links van de portal.
2. Geef *DDoS* op in het vak **filter** . Wanneer **DDoS-beveiligings plannen** worden weer gegeven in de resultaten, selecteert u deze.
3. Selecteer uw DDoS-beveiligings plan in de lijst.

Het virtuele netwerk van _MyVnet_ moet worden weer gegeven. 

## <a name="clean-up-resources"></a>Resources opschonen

U kunt uw resources voor de volgende zelf studie blijven gebruiken. Als u deze niet meer nodig hebt, verwijdert u de resource groep _MyResourceGroup_ . Wanneer u de resource groep verwijdert, verwijdert u ook het DDoS-beschermings plan en alle gerelateerde resources. Als u dit DDoS-beschermings plan niet wilt gebruiken, moet u resources verwijderen om onnodige kosten te voor komen.

   >[!WARNING]
   >Deze actie kan niet ongedaan worden gemaakt.

1. Zoek en selecteer in Azure Portal de optie **Resourcegroepen** of selecteer **Resourcegroepen** vanuit het menu in Azure Portal.

2. Filter of schuif omlaag om de resource groep _MyResourceGroup_ te zoeken.

3. Selecteer de resourcegroep en selecteer **Resourcegroep verwijderen**.

4. Typ de naam van de resourcegroep die u wilt controleren en selecteer vervolgens **Verwijderen**.

DDoS-beveiliging uitschakelen voor een virtueel netwerk: 

1. Voer de naam in van het virtuele netwerk waarvoor u de DDoS-beveiligings standaard wilt uitschakelen in het **vak resources, services en documenten zoeken** bovenaan de portal. Wanneer de naam van het virtuele netwerk wordt weer gegeven in de zoek resultaten, selecteert u dit.
2. Selecteer **onder DDoS Protection standaard** de optie **uitschakelen**.

Als u een DDoS-beschermings plan wilt verwijderen, moet u eerst alle virtuele netwerken loskoppelen. 

## <a name="next-steps"></a>Volgende stappen

Ga door naar de zelf studies voor meer informatie over het weer geven en configureren van telemetrie voor uw DDoS-beveiligings plan.

> [!div class="nextstepaction"]
> [DDoS-beschermingstelemetrie bekijken en configureren](telemetry.md)

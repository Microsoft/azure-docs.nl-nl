---
title: IP-firewallregels configureren
description: Een artikel waarin u leert hoe u IP-firewallregels configureert in Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: b937dad6c3c8f5a5773ca7779493b41c905307b1
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226502"
---
# <a name="azure-synapse-analytics-ip-firewall-rules"></a>IP-firewallregels voor Azure Synapse Analytics

In dit artikel worden de IP-firewallregels uitgelegd en leert u hoe u deze regels configureert in Azure Synapse Analytics.

## <a name="ip-firewall-rules"></a>IP-firewallregels

IP-firewallregels verlenen of blokkeren toegang tot uw Synapse-werkruimte op basis van het oorspronkelijke IP-adres van elke aanvraag. U kunt IP-firewallregels instellen voor uw werkruimte. De IP-firewallregels die op het niveau van de werkruimte zijn ingesteld, zijn van toepassing op alle openbare eindpunten van de werkruimte (toegewezen SQL-pools, serverloze SQL-pools, en ontwikkeling).

## <a name="create-and-manage-ip-firewall-rules"></a>IP-firewallregels maken en beheren

Er zijn twee manieren om IP-firewallregels toe te voegen aan een Synapse-werkruimte. Als u een IP-firewall wilt toevoegen aan uw werkruimte, selecteert u **Beveiliging en netwerk** en selecteert u **Verbindingen vanaf alle IP-adressen toestaan** tijdens het maken van de werkruimte.

![Schermopname waarin de knop Beveiliging en netwerken is gemarkeerd.](./media/synpase-workspace-ip-firewall/ip-firewall-1.png)

![IP-configuratie van Synapse-werkruimte in de Azure-portal.](./media/synpase-workspace-ip-firewall/ip-firewall-2.png)

U kunt ook IP-firewallregels toevoegen aan een Synapse-werkruimte nadat de werkruimte is gemaakt. Selecteer hiervoor **Firewalls** onder **Beveiliging** in de Azure-portal. Als u een nieuwe IP-firewallregel wilt toevoegen, geeft u deze een naam, een begin-IP-adres en een eind-IP-adres. Selecteer **Opslaan** wanneer u klaar bent.

![IP-configuratie van Synapse-werkruimte in de Azure-portal.](./media/synpase-workspace-ip-firewall/ip-firewall-3.png)

## <a name="connect-to-synapse-from-your-own-network"></a>Verbinding maken met Synapse via uw eigen netwerk

U kunt verbinding maken met uw Synapse-werkruimte met behulp van Synapse Studio. U kunt ook SSMS (SQL Server Management Studio) gebruiken om verbinding te maken met de SQL-resources (toegewezen SQL-pools en serverloze SQL-pools) in uw werkruimte.

Zorg ervoor dat de firewall in uw netwerk en op uw lokale computer uitgaande communicatie via TCP-poorten 80, 443 en 1443 toestaan voor Synapse Studio.

U moet ook uitgaande communicatie toestaan op UDP-poort 53 voor Synapse Studio. Als u verbinding wilt maken met via hulpprogramma's zoals SSMS en Power BI, moet u uitgaande communicatie toestaan op TCP-poort 1433.

Het SQL-verbindings beleid is ingesteld op de *standaard waarde* voor de werk ruimte. U vindt [hier](../../azure-sql/database/connectivity-architecture.md#connection-policy)meer informatie over de IP-adressen en poorten waarmee clients uitgaande communicatie kunnen toestaan.




## <a name="next-steps"></a>Volgende stappen

Een [Azure Synapse-werkruimte](../quickstart-create-workspace.md) maken

Een Azure Synapse-werkruimte maken met een [Virtueel netwerk van een beheerde werkruimte](./synapse-workspace-managed-vnet.md)
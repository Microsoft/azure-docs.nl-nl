---
title: Verbinding maken met een beveiligd opslag account vanuit uw Azure Synapse-werk ruimte
description: In dit artikel leert u hoe u verbinding kunt maken met een beveiligd opslag account vanuit uw Azure Synapse-werk ruimte
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 02/10/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5d43d6f56b48a34fa34baf727508ad8f1c151aa7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674322"
---
# <a name="connect-to-a-secure-azure-storage-account-from-your-synapse-workspace"></a>Verbinding maken met een beveiligd Azure Storage-account vanuit uw Synapse-werk ruimte

In dit artikel leert u hoe u verbinding kunt maken met een beveiligd Azure Storage-account vanuit uw Azure Synapse-werk ruimte. U kunt een Azure Storage-account koppelen aan uw Synapse-werk ruimte wanneer u uw werk ruimte maakt. U kunt meer opslag accounts koppelen nadat u uw werk ruimte hebt gemaakt.


## <a name="secured-azure-storage-accounts"></a>Beveiligde Azure Storage-accounts
Azure Storage biedt een gelaagd beveiligings model waarmee u de toegang tot uw opslag accounts kunt beveiligen en beheren. U kunt IP-firewall regels configureren om verkeer toe te kennen van geselecteerde open bare IP-adresbereiken toegang tot uw opslag account. U kunt ook netwerk regels configureren om verkeer van geselecteerde virtuele netwerken toegang tot uw opslag account te verlenen. U kunt IP-firewall regels combi neren die toegang toestaan vanaf geselecteerde IP-adresbereiken en netwerk regels die toegang verlenen vanuit geselecteerde virtuele netwerken op hetzelfde opslag account. Deze regels zijn van toepassing op het open bare eind punt van een opslag account. U hebt geen toegangs regels nodig om verkeer toe te staan van beheerde privé-eind punten die in uw werk ruimte zijn gemaakt in een opslag account. Opslag firewall regels kunnen worden toegepast op bestaande opslag accounts of op nieuwe opslag accounts wanneer u deze maakt. U vindt [hier](../../storage/common/storage-network-security.md)meer informatie over het beveiligen van uw opslag account.

## <a name="synapse-workspaces-and-virtual-networks"></a>Synapse-werk ruimten en virtuele netwerken
Wanneer u een Synapse-werk ruimte maakt, kunt u ervoor kiezen om een beheerd virtueel netwerk in te scha kelen om eraan te koppelen. Als u beheerd virtueel netwerk niet inschakelt voor uw werk ruimte wanneer u deze maakt, bevindt uw werk ruimte zich in een gedeeld virtueel netwerk, samen met andere Synapse-werk ruimten waaraan geen beheerd virtueel netwerk is gekoppeld. Als u beheerd virtueel netwerk hebt ingeschakeld tijdens het maken van de werk ruimte, wordt uw werk ruimte gekoppeld aan een toegewezen virtueel netwerk dat wordt beheerd door Azure Synapse. Deze virtuele netwerken worden niet gemaakt in uw klant abonnement. Daarom is het niet mogelijk om verkeer van deze virtuele netwerken toegang te verlenen tot uw beveiligde opslag account met behulp van de hierboven beschreven netwerk regels.  

## <a name="access-a-secured-storage-account"></a>Toegang tot een beveiligd opslag account
Synapse werkt van netwerken die niet in uw netwerk regels kunnen worden opgenomen. U moet het volgende doen om toegang vanuit uw werk ruimte met uw beveiligde-opslag account in te scha kelen.

* Een Azure Synapse-werk ruimte maken met een beheerd virtueel netwerk dat hieraan is gekoppeld en beheerde persoonlijke eind punten maken van het account
* Verleen uw Azure Synapse-werk ruimte toegang tot uw beveiligde-opslag account als een vertrouwde Azure-service. Als een vertrouwde service gebruikt Azure Synapse vervolgens sterke verificatie om veilig verbinding te maken met uw opslag account.   

### <a name="create-a-synapse-workspace-with-a-managed-virtual-network-and-create-managed-private-endpoints-to-your-storage-account"></a>Een Synapse-werk ruimte maken met een beheerd virtueel netwerk en beheerde privé-eind punten maken voor uw opslag account
U kunt [deze stappen](./synapse-workspace-managed-vnet.md) volgen om een Synapse-werk ruimte te maken waaraan een beheerd virtueel netwerk is gekoppeld. Nadat de werk ruimte met een gekoppeld virtueel netwerk is gemaakt, kunt u een beheerd privé-eind punt maken voor uw beveiligde-opslag account. Volg hiervoor de stappen die [hier](./how-to-create-managed-private-endpoints.md)worden beschreven. 

### <a name="grant-your-azure-synapse-workspace-access-to-your-secure-storage-account-as-a-trusted-azure-service"></a>Uw Azure Synapse-werk ruimte toegang verlenen tot uw beveiligde-opslag account als een vertrouwde Azure-service
Analytische mogelijkheden zoals een toegewezen SQL-groep en een Serverloze SQL-pool gebruiken multi-tenant infrastructuur die niet is geïmplementeerd in het beheerde virtuele netwerk. Als u verkeer van deze mogelijkheden voor toegang tot het beveiligde opslag account wilt gebruiken, moet u de toegang tot uw opslag account configureren op basis van de door het systeem toegewezen beheerde identiteit van de werk ruimte door de volgende stappen uit te voeren.

Ga in Azure Portal naar uw account voor beveiligde opslag. Selecteer **netwerken** in het navigatie deel venster aan de linkerkant. Selecteer in de sectie **bron exemplaren** *micro soft. Synapse/werk ruimten* als het **resource type** en voer de naam van uw werk ruimte in voor de naam van het **exemplaar**. Selecteer **Opslaan**.

![Netwerk configuratie van het opslag account.](./media/connect-to-a-secure-storage-account/secured-storage-access.png)

U hebt nu toegang tot uw beveiligde opslag account vanuit de werk ruimte.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [virtuele netwerk van de beheerde werk ruimte](./synapse-workspace-managed-vnet.md).

Meer informatie over [beheerde privé-eind punten](./synapse-workspace-managed-private-endpoints.md).
---
title: Verbinding maken met een Synapse Studio met behulp van persoonlijke koppelingen
description: In dit artikel leert u hoe u verbinding kunt maken met uw Azure Synapse Studio met behulp van persoonlijke koppelingen
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: d39beca60264023c8eb7c1bc78cd1ac15c3b45dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104586621"
---
# <a name="connect-to-azure-synapse-studio-using-azure-private-link-hubs"></a>Verbinding maken met Azure Synapse Studio met behulp van Azure private link hubs 

In dit artikel wordt uitgelegd hoe persoonlijke koppelingen van Azure Synapse Analytics persoonlijke koppeling hubs worden gebruikt om veilig verbinding te maken met Synapse Studio. 

## <a name="azure-private-link-hubs"></a>Azure private link-hubs 
U kunt veilig verbinding maken met Azure Synapse Studio via uw virtuele Azure-netwerk met behulp van persoonlijke koppelingen. Azure Synapse Analytics-hubs voor persoonlijke koppelingen zijn Azure-resources die fungeren als Connect oren tussen uw beveiligde netwerk en de Synapse Studio-webervaring. 

Er zijn twee stappen om verbinding te maken met Synapse Studio met behulp van persoonlijke koppelingen. Eerst moet u een bron van een persoonlijke koppelings hubs maken. Ten tweede moet u een persoonlijk eind punt maken op basis van uw virtuele Azure-netwerk naar deze persoonlijke koppelings hub. U kunt vervolgens privé-eind punten gebruiken om veilig te communiceren met Synapse Studio. U moet de persoonlijke eind punten integreren met uw DNS-oplossing, uw on-premises oplossing of Azure Privé-DNS. 

## <a name="azure-private-links-hubs-and-azure-synapse-studio"></a>Azure persoonlijke koppelingen hubs en Azure Synapse Studio
U kunt één Azure Synapse private link hub-Resource gebruiken om op een particuliere plek verbinding te maken met al uw Azure Synapse Analytics-werk ruimten met behulp van Azure Synapse Studio. De werk ruimten hoeven zich niet in dezelfde regio te bevinden als de Azure Synapse private link-hub. De Azure Synapse private link hub-resource kan ook worden gebruikt voor verbindingen met Synapse-werk ruimten in verschillende abonnementen of Azure AD-tenants.

U kunt de hub van uw persoonlijke koppeling maken door te zoeken naar *Synapse-hubs* in de Azure Portal en **Azure Synapse Analytics (persoonlijke koppelings hubs)** te selecteren bij Services. Volg de stappen in de hand leiding voor informatie over het [maken van verbinding met werkruimte bronnen vanuit een beperkt netwerk](./how-to-connect-to-workspace-from-restricted-network.md) .

>[!NOTE]
>De persoonlijke koppelings hubs zijn bedoeld voor het veilig laden van de statische inhoud Synapse Studio via persoonlijke koppelingen. U moet **afzonderlijke, persoonlijke eind punten** maken voor de resources waarmee u verbinding wilt maken in de werk ruimte, zoals ingerichte/toegewezen SQL-groepen of mousserende Pools. 

## <a name="azure-private-links-hubs-and-azure-virtual-network"></a>Azure persoonlijke koppelingen hubs en Azure Virtual Network
U moet uw virtuele Azure-netwerk verbinden met de Synapse private link hub-resource om de end-to-end-verbinding met Synapse Studio te beveiligen. Hiervoor moet u een persoonlijk eind punt van uw virtuele netwerk maken naar de hub van de persoonlijke koppeling die u hebt gemaakt. U kunt de Azure Portal voor uw persoonlijke link-hub gebruiken en naar de sectie persoonlijk eind punt gaan. Selecteer ' + persoonlijk eind punt ' om een nieuw persoonlijk eind punt te maken dat verbinding maakt met de hub van uw persoonlijke koppeling.

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-private-endpoint.png" alt-text="Scherm opname van de pagina verbindingen met privé-eind punten.":::

Zorg ervoor dat u het resource type ' micro soft. Synapse/privateLinkHubs ' kiest op het tabblad Resource.

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-resource-type.png" alt-text="Scherm opname van de pagina ' Maak een persoonlijk eind punt ' met ' resource type ' gemarkeerd.":::

Op het tabblad ' configuratie ' selecteert u ' privatelink.azuresynapse.net ' voor Privé-DNS zones bij het integreren met uw virtuele netwerk en particuliere DNS-zone.

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-dns-zones.png" alt-text="Een persoonlijk eind punt maken in een persoonlijke koppelings hub":::

## <a name="next-steps"></a>Volgende stappen

[Verbinding maken met werkruimte bronnen vanuit een beperkt netwerk](./how-to-connect-to-workspace-from-restricted-network.md)

Meer informatie over [Managed workspace Virtual Network](./synapse-workspace-managed-vnet.md)

Meer informatie over [beheerde privé-eindpunten](./synapse-workspace-managed-private-endpoints.md)

[Create Managed private endpoint to your data sources](./how-to-create-managed-private-endpoints.md) (Beheerde privé-eindpunten naar uw gegevensbronnen maken)

[Verbinding maken met de Synapse-werk ruimte met behulp van privé-eind punten](./how-to-connect-to-workspace-with-private-links.md)


---
title: Virtuele netwerken beheren-Azure Portal-Azure Database for PostgreSQL-flexibele server
description: Maak en beheer virtuele netwerken voor Azure Database for PostgreSQL-flexibele server met behulp van de Azure Portal
author: rothja
ms.author: jroth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 8a3c983a60dc542cf83f9e818b7f9c1f20265b49
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552356"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Maak en beheer virtuele netwerken voor Azure Database for PostgreSQL-flexibele server met behulp van de Azure Portal

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is als preview-versie beschikbaar

Azure Database for PostgreSQL-flexibele server ondersteunt twee typen elkaar wederzijds exclusieve netwerk connectiviteits methoden om verbinding te maken met uw flexibele server. De twee opties zijn:

* Openbare toegang (toegestane IP-adressen)
* Privétoegang (VNet-integratie)

In dit artikel wordt de nadruk gelegd op het maken van een PostgreSQL **-server met persoonlijke toegang (VNet-integratie)** met behulp van Azure Portal. Met persoonlijke toegang (VNet-integratie) kunt u uw flexibele server implementeren in uw eigen [Azure-Virtual Network](../../virtual-network/virtual-networks-overview.md). Virtuele Azure-netwerken bieden particuliere en beveiligde netwerk communicatie. Met persoonlijke toegang worden de verbindingen met de PostgreSQL-server beperkt tot het virtuele netwerk. Raadpleeg voor meer informatie over [persoonlijke toegang (VNet-integratie)](./concepts-networking.md#private-access-vnet-integration).

U kunt uw flexibele server tijdens het maken van de server implementeren in een virtueel netwerk en een subnet. Nadat de flexibele server is geïmplementeerd, kunt u deze niet verplaatsen naar een ander virtueel netwerk, subnet of naar *openbare toegang (toegestane IP-adressen)* .

## <a name="prerequisites"></a>Vereisten
Als u een flexibele server in een virtueel netwerk wilt maken, hebt u het volgende nodig:
- Een [virtueel netwerk](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > Het virtuele netwerk en het subnet moeten zich in dezelfde regio en hetzelfde abonnement bevinden als uw flexibele server.

-  [Een subnet delegeren](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service) naar **micro soft. DBforPostgreSQL/flexibleServers**. Deze overdracht houdt in dat alleen Azure Database for PostgreSQL flexibele servers dat subnet kunnen gebruiken. Er kunnen zich geen andere Azure-resourcetypen in het gedelegeerde subnet bevinden.
-  Voeg toe `Microsoft.Storage` aan het service-eind punt voor het subnet dat is overgedragen aan flexibele servers. Dit doet u door de volgende stappen uit te voeren:
     1. Ga naar de pagina van uw virtuele netwerk.
     2. Selecteer het VNET waarin u de flexibele server wilt implementeren.
     3. Kies het subnet dat wordt gedelegeerd voor flexibele server.
     4. Kies in het scherm voor het uitpakken onder **service-eind punt** `Microsoft.storage` de optie in de vervolg keuzelijst.
     5. Sla de wijzigingen op.


## <a name="create-azure-database-for-postgresql---flexible-server-in-an-already-existing-virtual-network"></a>Azure Database for PostgreSQL-flexibele server maken in een bestaand virtueel netwerk

1. Selecteer in de linkerbovenhoek van de portal **Een resource maken** (+).
2. Selecteer **Databases** > **Azure Database for PostgreSQL**. U kunt ook **postgresql** in het zoekvak typen om de service te vinden.
3. Selecteer **Flexibele server** als implementatieoptie.
4. Vul het formulier **basis principes** in.
5. Ga naar het tabblad **netwerken** om te configureren hoe u verbinding wilt maken met uw server.
6. Selecteer in de **verbindings methode** **persoonlijke toegang (VNet-integratie)**. Ga naar **Virtual Network** en selecteer het bestaande *virtuele netwerk* en het *subnet* dat u hebt gemaakt als onderdeel van de bovenstaande vereisten.
7. Selecteer **Beoordelen + maken** om uw flexibele serverconfiguratie te controleren.
8. Selecteer **Maken** om de server in te richten. Het inrichten kan enkele minuten duren.

>[!Note]
> Nadat de flexibele server op een virtueel netwerk en subnet is geïmplementeerd, kunt u deze niet naar open bare toegang (toegestane IP-adressen) verplaatsen.
## <a name="next-steps"></a>Volgende stappen
- [Maak en beheer Azure database for PostgreSQL flexibel Virtueel Server netwerk met behulp van Azure cli](./how-to-manage-virtual-network-cli.md).
- Meer informatie over [netwerken in azure database for PostgreSQL-flexibele server](./concepts-networking.md)
- Meer informatie over [Azure database for PostgreSQL-flexibel server virtueel netwerk](./concepts-networking.md#private-access-vnet-integration).
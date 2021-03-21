---
title: VNet-eind punten beheren-Azure Portal-Azure Database for MariaDB
description: Azure Database for MariaDB VNet-service-eind punten en-regels maken en beheren met behulp van de Azure Portal
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 5eaa7821c61010b322d8f9032c439df28c297f3d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98665018"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Azure Database for MariaDB VNet-service-eind punten en VNet-regels maken en beheren met behulp van de Azure Portal

Service-eindpunten en -regels voor virtuele netwerken (VNets) breiden de privé-adresruimte van een virtueel netwerk uit naar uw Azure Database for MariaDB-server. Zie [Azure database for MariaDB server VNet-service-eind punten](concepts-data-access-security-vnet.md)voor een overzicht van Azure database for MariaDB VNet-service-eind punten, met inbegrip van beperkingen. VNet-service-eind punten zijn beschikbaar in alle ondersteunde regio's voor Azure Database for MariaDB.

> [!NOTE]
> Ondersteuning voor VNet-service-eind punten is alleen voor servers met Algemeen en geoptimaliseerd voor geheugen.

## <a name="create-a-vnet-rule-and-enable-service-endpoints"></a>Een VNet-regel maken en service-eind punten inschakelen

1. Klik op de pagina MariaDB-server, onder de kop instellingen, op **verbindings beveiliging** om het deel venster verbindings beveiliging te openen voor Azure database for MariaDB.

2. Zorg ervoor dat het besturings element toegang tot Azure-Services toestaan is ingesteld op **uit**.

> [!Important]
> Als u deze instelt op aan, accepteert uw Azure MariaDB-database server communicatie vanuit elk subnet. Het is mogelijk dat het besturings element dat is ingesteld op aan, overmatig toegankelijk is vanuit het beveiligings oogpunt van de weer gave. Met de functie Microsoft Azure Virtual Network Service-eind punt, in combi natie met de regel functie voor virtuele netwerken van Azure Database for MariaDB, kan uw beveiligings surface area worden verminderd.

3. Klik vervolgens op **+ bestaand virtueel netwerk toevoegen**. Als u geen bestaand VNet hebt, kunt u op **+ nieuw virtueel netwerk maken** klikken om er een te maken. Zie [Quick Start: een virtueel netwerk maken met behulp van de Azure Portal](../virtual-network/quick-create-portal.md)

   ![Azure Portal-Klik op verbindings beveiliging](./media/howto-manage-vnet-portal/1-connection-security.png)

4. Voer een naam in voor de VNet-regel, selecteer de naam van het abonnement, het virtuele netwerk en het subnet en klik vervolgens op **inschakelen**. Hiermee worden de VNet-service-eind punten in het subnet automatisch ingeschakeld met behulp van het label **micro soft. SQL** -service.

   ![Azure Portal-VNet configureren](./media/howto-manage-vnet-portal/2-configure-vnet.png)

   Het account moet beschikken over de benodigde machtigingen voor het maken van een virtueel netwerk en een service-eindpunt.

   Service-eind punten kunnen afzonderlijk op virtuele netwerken worden geconfigureerd door een gebruiker met schrijf toegang tot het virtuele netwerk.
    
   Als u Azure-service resources wilt beveiligen met een VNet, moet de gebruiker over de machtiging ' micro soft. Network/virtualNetworks/subnets/joinViaServiceEndpoint/' beschikken voor de subnetten die worden toegevoegd. Deze machtiging is standaard opgenomen in de ingebouwde service-beheerdersrollen en kan worden gewijzigd door aangepaste rollen te maken.
    
   Meer informatie over [ingebouwde rollen](../role-based-access-control/built-in-roles.md) en het toewijzen van specifieke machtigingen voor [aangepaste rollen](../role-based-access-control/custom-roles.md).
    
   VNets en Azure-serviceresources kunnen in hetzelfde abonnement of in verschillende abonnementen zitten. Als de VNet-en Azure-service resources zich in verschillende abonnementen bevinden, moeten de resources onder dezelfde Active Directory (AD)-Tenant vallen. Zorg ervoor dat de **micro soft. SQL** -resource provider is geregistreerd voor beide abonnementen. Raadpleeg [Resource-Manager-registratie][resource-manager-portal] voor meer informatie

   > [!IMPORTANT]
   > Het is raadzaam om dit artikel te lezen over service-eindpunt configuraties en overwegingen voordat u service-eind punten configureert. **Service-eind punt Virtual Network:** Een [Virtual Network Service-eind punt](../virtual-network/virtual-network-service-endpoints-overview.md) is een subnet waarvan de eigenschaps waarden een of meer formele namen van Azure-service typen bevatten. VNet-service-eind punten gebruiken de service type naam **micro soft. SQL**, die verwijst naar de Azure-service met de naam SQL database. Deze servicetag is ook van toepassing op de Azure SQL Database-, Azure Database for MariaDB-, PostgreSQL-en MySQL-Services. Het is belang rijk te weten wanneer u de code van het **micro soft. SQL** -service toepast op een VNet-service-eind punt, waarbij service-eindpunt verkeer wordt geconfigureerd voor alle Azure Data Base-Services, waaronder Azure SQL Database, Azure Database for PostgreSQL, Azure Database for MariaDB en Azure database for MySQL servers in het subnet.
   > 

5. Als deze functie is ingeschakeld, klikt u op **OK** . u ziet dat de vnet-service-eind punten zijn ingeschakeld in combi natie met een VNet-regel.

   ![De VNet-service-eind punten zijn ingeschakeld en de VNet-regel is gemaakt](./media/howto-manage-vnet-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [configureren van SSL op Azure database for MariaDB](howto-configure-ssl.md)
- Op dezelfde manier kunt u scripts [gebruiken om vnet-service-eind punten in te scha kelen en een VNet-regel voor Azure database for MariaDB te maken met behulp van Azure cli](howto-manage-vnet-cli.md).

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
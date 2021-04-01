---
title: Regels voor virtueel netwerk-Azure Portal-Azure Database for PostgreSQL-één-server gebruiken
description: VNet-service-eind punten en-regels Azure Database for PostgreSQL-één server maken en beheren met behulp van de Azure Portal
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 377f8eba179253ca6187b10a22970d0eadcda2f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92489826"
---
# <a name="create-and-manage-vnet-service-endpoints-and-vnet-rules-in-azure-database-for-postgresql---single-server-by-using-the-azure-portal"></a>VNet-service-eind punten en VNet-regels maken en beheren in Azure Database for PostgreSQL-één server met behulp van de Azure Portal
Virtual Network-Services (VNet)-eind punten en-regels breiden de privé-adres ruimte van een Virtual Network naar uw Azure Database for PostgreSQL-server uit. Zie [Azure database for postgresql server VNet-service-eind punten](concepts-data-access-and-security-vnet.md)voor een overzicht van Azure database for PostgreSQL VNet-service-eind punten, met inbegrip van beperkingen. VNet-service-eind punten zijn beschikbaar in alle ondersteunde regio's voor Azure Database for PostgreSQL.

> [!NOTE]
> Ondersteuning voor VNet-service-eind punten is alleen voor servers met Algemeen en geoptimaliseerd voor geheugen.
> In het geval van VNet-peering, als verkeer via een gemeen schappelijke VNet-gateway met Service-eind punten stroomt en naar de peer moet stromen, moet u een ACL/VNet-regel maken om Azure Virtual Machines in de gateway-VNet toegang te geven tot de Azure Database for PostgreSQL-server.


## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Een VNet-regel maken en service-eind punten inschakelen in de Azure Portal

1. Klik op de pagina PostgreSQL-server, onder de kop instellingen, op **verbindings beveiliging** om het deel venster verbindings beveiliging te openen voor Azure database for PostgreSQL. 

2. Zorg ervoor dat het besturings element toegang tot Azure-Services toestaan is ingesteld op **uit**.

> [!Important]
> Als u het besturings element instelt op aan, accepteert uw Azure PostgreSQL-database server communicatie vanuit elk subnet. Het is mogelijk dat het besturings element dat is ingesteld op aan, overmatig toegankelijk is vanuit het beveiligings oogpunt van de weer gave. Met de functie Microsoft Azure Virtual Network Service-eind punt, in combi natie met de regel functie voor virtuele netwerken van Azure Database for PostgreSQL, kan uw beveiligings surface area worden verminderd.

3. Klik vervolgens op **+ bestaand virtueel netwerk toevoegen**. Als u geen bestaand VNet hebt, kunt u op **+ nieuw virtueel netwerk maken** klikken om er een te maken. Zie [Quick Start: een virtueel netwerk maken met behulp van de Azure Portal](../virtual-network/quick-create-portal.md)

   :::image type="content" source="./media/howto-manage-vnet-using-portal/1-connection-security.png" alt-text="Azure Portal-Klik op verbindings beveiliging":::

4. Voer een naam in voor de VNet-regel, selecteer de naam van het abonnement, het virtuele netwerk en het subnet en klik vervolgens op **inschakelen**. Hiermee worden de VNet-service-eind punten in het subnet automatisch ingeschakeld met behulp van het label **micro soft. SQL** -service.

   :::image type="content" source="./media/howto-manage-vnet-using-portal/2-configure-vnet.png" alt-text="Azure Portal-VNet configureren":::

    Het account moet beschikken over de benodigde machtigingen voor het maken van een virtueel netwerk en een service-eindpunt.

    Service-eind punten kunnen afzonderlijk op virtuele netwerken worden geconfigureerd door een gebruiker met schrijf toegang tot het virtuele netwerk.
    
    Als u Azure-service resources wilt beveiligen met een VNet, moet de gebruiker over de machtiging ' micro soft. Network/virtualNetworks/subnets/joinViaServiceEndpoint/' beschikken voor de subnetten die worden toegevoegd. Deze machtiging is standaard opgenomen in de ingebouwde service-beheerdersrollen en kan worden gewijzigd door aangepaste rollen te maken.
    
    Meer informatie over [ingebouwde rollen](../role-based-access-control/built-in-roles.md) en het toewijzen van specifieke machtigingen voor [aangepaste rollen](../role-based-access-control/custom-roles.md).
    
    VNets en Azure-serviceresources kunnen in hetzelfde abonnement of in verschillende abonnementen zitten. Als de VNet-en Azure-service resources zich in verschillende abonnementen bevinden, moeten de resources onder dezelfde Active Directory (AD)-Tenant vallen. Zorg ervoor dat de **micro soft. SQL** -resource provider is geregistreerd voor beide abonnementen. Raadpleeg [Resource-Manager-registratie][resource-manager-portal] voor meer informatie

   > [!IMPORTANT]
   > Het is raadzaam om dit artikel te lezen over service-eindpunt configuraties en overwegingen voordat u service-eind punten configureert. **Service-eind punt Virtual Network:** Een [Virtual Network Service-eind punt](../virtual-network/virtual-network-service-endpoints-overview.md) is een subnet waarvan de eigenschaps waarden een of meer formele namen van Azure-service typen bevatten. VNet-service-eind punten gebruiken de service type naam **micro soft. SQL**, die verwijst naar de Azure-service met de naam SQL database. Deze servicetag is ook van toepassing op de Azure SQL Database, Azure Database for PostgreSQL en MySQL-Services. Het is belang rijk te weten wanneer u de code van het **micro soft. SQL** -service toepast op een VNet-service-eind punt Hiermee wordt service-eindpunt verkeer geconfigureerd voor alle Azure Data Base-Services, waaronder Azure SQL Database, Azure Database for PostgreSQL en Azure database for MySQL servers in het subnet. 
   > 

5. Als deze functie is ingeschakeld, klikt u op **OK** . u ziet dat de vnet-service-eind punten zijn ingeschakeld in combi natie met een VNet-regel.

   :::image type="content" source="./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png" alt-text="De VNet-service-eind punten zijn ingeschakeld en de VNet-regel is gemaakt":::

## <a name="next-steps"></a>Volgende stappen
- Op dezelfde manier kunt u scripts [gebruiken om vnet-service-eind punten in te scha kelen en een VNet-regel voor Azure database for PostgreSQL te maken met behulp van Azure cli](howto-manage-vnet-using-cli.md).
- Zie voor hulp bij het maken van verbinding met een Azure Database for PostgreSQL-server [verbindings bibliotheken voor Azure database for PostgreSQL](./concepts-connection-libraries.md)

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
---
title: Netwerktopologieën voor migraties van SQL-beheerde exemplaren
titleSuffix: Azure Database Migration Service
description: Meer informatie over de bron-en doel configuraties voor migraties van Azure SQL Managed instances met behulp van de Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: reference
ms.date: 01/08/2020
ms.openlocfilehash: 0799e8c76bc5d3969943d766aa83de40659a236a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093307"
---
# <a name="network-topologies-for-azure-sql-managed-instance-migrations-using-azure-database-migration-service"></a>Netwerk topologieën voor Azure SQL Managed instance-migraties met Azure Database Migration Service

In dit artikel worden verschillende netwerktopologieën beschreven waarmee Azure Database Migration Service kunnen werken om een uitgebreide migratie ervaring te bieden van SQL-servers naar Azure SQL Managed instance.

## <a name="azure-sql-managed-instance-configured-for-hybrid-workloads"></a>Azure SQL Managed instance geconfigureerd voor hybride werk belastingen 

Gebruik deze topologie als uw Azure SQL Managed instance is verbonden met uw on-premises netwerk. Deze aanpak biedt de meest vereenvoudigde netwerk Routering en levert de maximale gegevens doorvoer tijdens de migratie.

![Netwerk topologie voor hybride werk belastingen](media/resource-network-topologies/hybrid-workloads.png)

**Vereisten**

- In dit scenario worden de SQL Managed instance en het Azure Database Migration Service-exemplaar in dezelfde Microsoft Azure Virtual Network gemaakt, maar ze gebruiken verschillende subnetten.  
- Het virtuele netwerk dat in dit scenario wordt gebruikt, is ook verbonden met het on-premises netwerk met behulp van [ExpressRoute](../expressroute/expressroute-introduction.md) of [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

## <a name="sql-managed-instance-isolated-from-the-on-premises-network"></a>SQL Managed instance geïsoleerd van het on-premises netwerk

Gebruik deze netwerk topologie als uw omgeving een of meer van de volgende scenario's vereist:

- Het beheerde exemplaar van SQL wordt geïsoleerd van on-premises connectiviteit, maar uw Azure Database Migration Service-exemplaar is verbonden met het on-premises netwerk.
- Als er op Azure-beleid voor op rollen gebaseerd toegangs beheer (Azure RBAC) is ingesteld en u de gebruikers wilt beperken tot hetzelfde abonnement dat als host fungeert voor het SQL Managed instance.
- De virtuele netwerken die worden gebruikt voor het beheerde exemplaar van SQL en Azure Database Migration Service, bevinden zich in verschillende abonnementen.

![Netwerk topologie voor beheerde instantie die is geïsoleerd van het on-premises netwerk](media/resource-network-topologies/mi-isolated-workload.png)

**Vereisten**

- Het virtuele netwerk dat Azure Database Migration Service voor dit scenario gebruikt, moet ook worden verbonden met het on-premises netwerk met behulp van ofwel https://docs.microsoft.com/azure/expressroute/expressroute-introduction) [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- Stel [VNet-netwerk peering](../virtual-network/virtual-network-peering-overview.md) in tussen het virtuele netwerk dat wordt gebruikt voor SQL Managed Instance en Azure database Migration service.

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>Cloud-naar-Cloud-migraties: gedeeld virtueel netwerk

Gebruik deze topologie als de bron SQL Server wordt gehost in een Azure-VM en hetzelfde virtuele netwerk deelt met SQL Managed instance en Azure Database Migration Service.

![Netwerk topologie voor Cloud-naar-Cloud migraties met een gedeeld VNet](media/resource-network-topologies/cloud-to-cloud.png)

**Vereisten**

- Geen aanvullende vereisten.

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>Cloud migraties: geïsoleerd virtueel netwerk

Gebruik deze netwerk topologie als uw omgeving een of meer van de volgende scenario's vereist:

- Het beheerde exemplaar van SQL wordt ingericht in een geïsoleerd virtueel netwerk.
- Als er op Azure-beleid voor op rollen gebaseerd toegangs beheer (Azure RBAC) is ingesteld, moet u de gebruikers beperken tot hetzelfde abonnement dat wordt gehost op een SQL-beheerd exemplaar.
- De virtuele netwerken die worden gebruikt voor SQL Managed instance en Azure Database Migration Service bevinden zich in verschillende abonnementen.

![Netwerk topologie voor Cloud-naar-Cloud migraties met een geïsoleerd VNet](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Vereisten**

- Stel [VNet-netwerk peering](../virtual-network/virtual-network-peering-overview.md) in tussen het virtuele netwerk dat wordt gebruikt voor SQL Managed Instance en Azure database Migration service.

## <a name="inbound-security-rules"></a>Inkomende beveiligingsregels

| **NAAM**   | **IMPORTEER** | **PROTOCOLSUBSTATUS** | **Bron** | **BEOOGDE** | **OPTREDEN** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Alle      | Alle          | DMS-SUBNET | Alle             | Toestaan      |

## <a name="outbound-security-rules"></a>Uitgaande beveiligingsregels

| **NAAM**                  | **IMPORTEER**                                              | **PROTOCOLSUBSTATUS** | **Bron** | **BEOOGDE**           | **OPTREDEN** | **Reden voor regel**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ServiceBus                | 443, ServiceTag: ServiceBus                           | TCP          | Alle        | Alle                       | Toestaan      | Communicatie van beheer vlak via Service Bus. <br/>(Als micro soft-peering is ingeschakeld, hebt u deze regel mogelijk niet nodig.)                                                             |
| Storage                   | 443, ServiceTag: opslag                              | TCP          | Alle        | Alle                       | Toestaan      | Beheer vlak met Azure Blob Storage. <br/>(Als micro soft-peering is ingeschakeld, hebt u deze regel mogelijk niet nodig.)                                                             |
| Diagnostiek               | 443, ServiceTag: AzureMonitor                         | TCP          | Alle        | Alle                       | Toestaan      | DMS gebruikt deze regel voor het verzamelen van diagnostische gegevens voor het oplossen van problemen. <br/>(Als micro soft-peering is ingeschakeld, hebt u deze regel mogelijk niet nodig.)                                                  |
| SQL-bron server         | 1433 (of TCP IP-poort waarnaar SQL Server luistert) | TCP          | Alle        | On-premises adresruimte | Toestaan      | SQL Server bron connectiviteit vanuit DMS <br/>(Als u site-naar-site-connectiviteit hebt, hebt u deze regel mogelijk niet nodig.)                                                                                       |
| SQL Server benoemd exemplaar | 1434                                                  | UDP          | Alle        | On-premises adresruimte | Toestaan      | SQL Server bron connectiviteit van een benoemde instantie van DMS <br/>(Als u site-naar-site-connectiviteit hebt, hebt u deze regel mogelijk niet nodig.)                                                                        |
| SMB-share                 | 445 (als scenario neeeds)                             | TCP          | Alle        | On-premises adresruimte | Toestaan      | SMB-netwerk share voor DMS voor het opslaan van back-upbestanden van data bases voor migraties naar Azure SQL Database MI-en SQL-servers op Azure VM <br/>(Als u site-naar-site-connectiviteit hebt, hebt u deze regel mogelijk niet nodig). |
| DMS_subnet                | Alle                                                   | Alle          | Alle        | DMS_Subnet                | Toestaan      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Zie ook

- [SQL Server migreren naar een beheerd exemplaar van SQL](./tutorial-sql-server-to-managed-instance.md)
- [Overzicht van vereisten voor het gebruik van Azure Database Migration Service](./pre-reqs.md)
- [Een virtueel netwerk maken met Azure Portal](../virtual-network/quick-create-portal.md)

## <a name="next-steps"></a>Volgende stappen

- Zie het artikel [Wat is er Azure database Migration service?](dms-overview.md)voor een overzicht van Azure database Migration service.
- Zie de pagina [beschik bare producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration) voor actuele informatie over de regionale Beschik baarheid van Azure database Migration service.
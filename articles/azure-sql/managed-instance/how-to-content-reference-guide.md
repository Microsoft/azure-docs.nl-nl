---
title: Inhouds referentie configureren & beheren
titleSuffix: Azure SQL Managed Instance
description: Een naslag handleiding voor inhoud die u leert over het configureren en beheren van Azure SQL Managed instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 04/16/2019
ms.openlocfilehash: b7f2f060f32cf3bf92660a2e8b75a45b7ec76b82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92779761"
---
# <a name="azure-sql-managed-instance-content-reference"></a>Naslag informatie voor Azure SQL Managed instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In dit artikel vindt u een inhouds verwijzing naar verschillende hand leidingen, scripts en uitleg die u helpen bij het beheren en configureren van Azure SQL Managed instance.

## <a name="load-data"></a>Gegevens laden

- [Migreren naar Azure SQL Managed instance](migrate-to-instance-from-sql-server.md): meer informatie over het aanbevolen migratie proces en hulpprogram ma's voor migratie naar Azure SQL Managed instance.
- [TDe-certificaat migreren naar een beheerd exemplaar van Azure SQL](tde-certificate-migrate.md): als uw SQL Server-Data Base wordt beveiligd met transparent Data Encryption (TDE), moet u het certificaat migreren dat door SQL Managed instance kan worden gebruikt voor het ontsleutelen van de back-up die u wilt herstellen in Azure.
- [Een DB uit een BACPAC importeren](../database/database-import.md)
- [Een DB naar een BACPAC exporteren](../database/database-export.md)
- [Gegevens laden met BCP](../load-from-csv-with-bcp.md)
- [Gegevens laden met Azure Data Factory](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="network-configuration"></a>Netwerkconfiguratie

- De grootte van het [subnet bepalen](vnet-subnet-determine-size.md): omdat de grootte van het subnet niet kan worden gewijzigd nadat het SQL-beheerde exemplaar is geïmplementeerd, moet u berekenen welk IP-adres bereik is vereist voor het aantal en de typen beheerde exemplaren die u wilt implementeren in het subnet. 
- [Maak een nieuw VNet en subnet](virtual-network-subnet-create-arm-template.md): Configureer het virtuele netwerk en subnet volgens de [netwerk vereisten](connectivity-architecture-overview.md#network-requirements). 
- [Een bestaand VNet en subnet configureren](vnet-existing-add-subnet.md): Controleer de netwerk vereisten en configureer uw bestaande virtuele netwerk en subnet om het SQL-beheerde exemplaar te implementeren. 
- [Aangepaste DNS configureren](custom-dns-configure.md): aangepaste DNS configureren om toegang van externe bronnen te verlenen aan aangepaste domeinen van een SQL-beheerd exemplaar via een gekoppelde server van DB-mail profielen. 
- [Netwerk configuratie synchroniseren](azure-app-sync-network-configuration.md): Vernieuw het netwerk configuratie plan als u geen verbinding kunt maken nadat u [uw app hebt geïntegreerd met een virtueel Azure-netwerk](../../app-service/web-sites-integrate-with-vnet.md).
- [Het IP-adres van het beheer eindpunt zoeken](management-endpoint-find-ip-address.md): het open bare eind punt bepalen dat door SQL beheerd exemplaar wordt gebruikt voor beheer doeleinden. 
- [Ingebouwde firewall beveiliging controleren](management-endpoint-verify-built-in-firewall.md): Controleer of het SQL Managed instance verkeer alleen toestaat op de benodigde poorten en andere ingebouwde firewall regels. 
- [Connecting Applications](connect-application-instance.md): meer informatie over verschillende patronen voor het verbinden van de toepassingen met een SQL Managed instance.

## <a name="feature-configuration"></a>Configuratie van onderdelen

- [Azure AD-verificatie configureren](../database/authentication-aad-configure.md)
- [Voorwaardelijke toegang configureren](../database/conditional-access-configure.md)
- [Meervoudige Azure AD-verificatie](../database/authentication-mfa-ssms-overview.md)
- [Multi-Factor Authentication instellen](../database/authentication-mfa-ssms-configure.md)
- [Een tijdelijk Bewaar beleid configureren](../database/temporal-tables-retention-policy.md)
- [TDE met BYOK (Bring Your Own Key) configureren](../database/transparent-data-encryption-byok-configure.md)
- [TDE BYOK-sleutels roteren](../database/transparent-data-encryption-byok-key-rotation.md)
- [Een TDE-Protector verwijderen](../database/transparent-data-encryption-byok-remove-tde-protector.md)
- [OLTP in het geheugen configureren](../in-memory-oltp-configure.md)
- [Azure Automation configureren](../database/automation-manage.md)
- Met [transactionele replicatie](replication-between-two-instances-configure-tutorial.md) kunt u uw gegevens repliceren tussen beheerde exemplaren of van SQL Server on-premises naar een beheerd exemplaar van SQL, en omgekeerd.
- [Detectie van bedreigingen configureren](threat-detection-configure.md) : [bedreigingen detectie](../database/threat-detection-overview.md) is een ingebouwde Azure SQL Managed instance-functie die verschillende mogelijke aanvallen detecteert zoals SQL-injectie of toegang vanaf verdachte locaties. 
- Door [waarschuwingen te maken](alerts-create.md) , kunt u waarschuwingen instellen voor gecontroleerde metrische gegevens, zoals CPU-gebruik, het verbruik van opslag ruimte, IOPS en andere voor een SQL-beheerd exemplaar. 

## <a name="monitoring-and-tuning"></a>Bewaking en afstemming

- [Handmatig instellen](../database/performance-guidance.md)
- [DMV's gebruiken voor het bewaken van prestaties](../database/monitoring-with-dmvs.md)
- [Query opslag gebruiken om de prestaties te bewaken](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Prestatieproblemen oplossen met Intelligent Insights](../database/intelligent-insights-troubleshoot-performance.md)
- [Het Intelligent Insights diagnostische logboek gebruiken](../database/intelligent-insights-use-diagnostics-log.md)
- [In-Memory OLTP-ruimte bewaken](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>Uitgebreide gebeurtenissen

- [Uitgebreide gebeurtenissen](../database/xevent-db-diff-from-svr.md)
- [Uitgebreide gebeurtenissen opslaan in een gebeurtenis bestand](../database/xevent-code-event-file.md)
- [Uitgebreide gebeurtenissen opslaan in een ring buffer](../database/xevent-code-ring-buffer.md)

### <a name="alerting"></a>Waarschuwingen

- [Waarschuwingen in beheerd exemplaar maken](alerts-create.md)

## <a name="operations"></a>Operations

- [User-initiated manual failover on SQL Managed Instance](user-initiated-failover.md) (Door gebruiker geïnitieerde handmatige failover op een SQL Managed Instance)

## <a name="develop-applications"></a>Toepassingen ontwikkelen

- [Connectiviteit](../database/connect-query-content-reference-guide.md#libraries)
- [Spark-connector gebruiken](../../cosmos-db/spark-connector.md)
- [Een app verifiëren](../database/application-authentication-get-client-id-keys.md)
- [Batch verwerking gebruiken voor betere prestaties](../performance-improve-use-batching.md)
- [Connectiviteitsrichtlijnen](../database/troubleshoot-common-connectivity-issues.md)
- [DNS-aliassen](../database/dns-alias-overview.md)
- [Een DNS-alias instellen met behulp van Power shell](../database/dns-alias-powershell-create.md)
- [Poorten - ADO.NET](../database/adonet-v12-develop-direct-route-ports.md)
- [C en C ++](../database/develop-cplusplus-simple.md)
- [Excel](../database/connect-excel.md)

## <a name="design-applications"></a>Toepassingen ontwerpen

- [Ontwerpen voor herstel na noodgeval](../database/designing-cloud-solutions-for-disaster-recovery.md)
- [Ontwerpen voor elastische pools](../database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Ontwerpen voor app-upgrades](../database/manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Multi tenant SaaS-toepassingen ontwerpen

- [SaaS-ontwerppatronen](../database/saas-tenancy-app-design-patterns.md)
- [Indexeerfunctie voor SaaS-video](../database/saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Beveiliging van SaaS-app](../database/saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag door [SQL Managed instance te implementeren](instance-create-quickstart.md).
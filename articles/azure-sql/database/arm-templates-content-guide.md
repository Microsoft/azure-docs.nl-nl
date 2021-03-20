---
title: Azure Resource Manager-sjablonen - Azure SQL Database en een met Azure SQL beheerd exemplaar
description: Gebruik sjablonen van Azure Resource Manager voor het maken en configureren van Azure SQL Database en een met Azure SQL beheerd exemplaar.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: overview-samples sqldbrb=2
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: 5202145af6f60f34bdce57aa94f4fec92a8b227f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92677266"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database--sql-managed-instance"></a>Azure Resource Manager-sjablonen voor Azure SQL Database en een met Azure SQL beheerd exemplaar
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure Resource Manager-sjablonen maken het mogelijk om uw infrastructuur als code te definiëren en uw oplossingen naar de Azure-cloud te implementeren voor uw Azure SQL Database en een met Azure SQL beheerd exemplaar.

## <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

De volgende tabel bevat koppelingen naar Azure Resource Manager-sjablonen voor Azure SQL Database.

|Koppeling |Beschrijving|
|---|---|
| [SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Met deze Azure Resource Manager-sjabloon maakt u één database in Azure SQL Database en configureert u de firewallregels op serverniveau. |
| [Server](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Met deze Azure Resource Manager-sjabloon maakt u een server voor Azure SQL Database. |
| [Elastische pool](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Met deze sjabloon kunt u een elastische pool implementeren en databases eraan toewijzen. |
| [Failover-groepen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Met deze sjabloon maakt u twee servers, één database en een failover-groep in Azure SQL Database.|
| [Detectie van bedreigingen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Met deze sjabloon kunt u een server en een set databases implementeren met detectie van bedreigingen ingeschakeld, met een e-mailadres voor waarschuwingen voor elke database. Detectie van bedreigingen is een onderdeel van de oplossing SQL Advanced Threat Protection (ATP) en biedt een beveiligingslaag die reageert op mogelijke bedreigingen op servers en databases.|
| [Controle naar Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Met deze sjabloon kunt u een server implementeren met controle ingeschakeld voor het wegschrijven van auditlogboeken naar een blobopslag. De controlefunctie voor Azure SQL Database houdt in dat er databasegebeurtenissen worden bijgehouden en weggeschreven naar een auditlogboek in uw Azure-opslagaccount, OMS-werkruimte of Event Hubs.|
| [Controle naar Azure Event Hub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Met deze sjabloon kunt u een server implementeren met controle ingeschakeld voor het wegschrijven van auditlogboeken naar een bestaande Event Hub. Om controlegebeurtenissen te verzenden naar Event Hubs, moet u controle-instellingen instellen met `Enabled` `State` en `IsAzureMonitorTargetEnabled` instellen op `true`. Daarnaast moet u de diagnostische instellingen configureren met de categorie `SQLSecurityAuditEvents` voor logboeken op de `master`-database (voor controle op serverniveau). De controlefunctie houdt in dat er databasegebeurtenissen worden bijgehouden en weggeschreven naar een auditlogboek in uw Azure-opslagaccount, OMS-werkruimte of Event Hubs.|
| [Azure-web-app met SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Hiermee kunt u een gratis Azure-web-app en een database in Azure SQL Database maken op het serviceniveau Basic.|
| [Azure-web-app en Redis Cache met SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Met deze sjabloon maakt u een web-app, Redis-cache en database in dezelfde resourcegroep, en maakt u twee verbindingsreeksen in de web-app voor de database en Redis-cache.|
| [Gegevens importeren uit blob-opslag met ADF V2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Met deze Azure Resource Manager-sjabloon maakt u een exemplaar van Azure Data Factory V2 waarmee gegevens uit Azure Blob Storage naar SQL Database worden gekopieerd.|
| [HDInsight-cluster met een database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Met deze sjabloon kunt u een HDInsight-cluster, een logische SQL-server, een database en twee tabellen maken. Deze sjabloon wordt gebruikt in het [artikel over het gebruik van Sqoop met Hadoop in HDInsight](../../hdinsight/hadoop/hdinsight-use-sqoop.md). |
| [Logische Azure-app die een in SQL opgeslagen procedure volgens een schema uitvoert](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Met deze sjabloon kunt u een logische app maken die een in SQL opgeslagen procedure volgens een schema uitvoert. Eventuele argumenten voor de procedure kunnen in de hoofdsectie van de sjabloon worden geplaatst.|

## <a name="azure-sql-managed-instance"></a>[Azure SQL Managed Instance](#tab/managed-instance)

De volgende tabel bevat koppelingen naar Azure Resource Manager-sjablonen voor een met Azure SQL beheerd exemplaar.

|Koppeling|Beschrijving|
|---|---|
| [Met SQL beheerd exemplaar in een nieuw VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Met deze Azure Resource Manager-sjabloon maakt u een nieuw geconfigureerd virtueel Azure-netwerk en een beheerd exemplaar in het virtuele netwerk. |
| [Netwerkomgeving voor een met SQL beheerd exemplaar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Met deze implementatie maakt u een geconfigureerd virtueel Azure-netwerk met twee subnetten: één subnet dat is gereserveerd voor uw beheerde exemplaren en een ander subnet voor het opslaan van andere resources (zoals VM's, App Service-omgevingen, enzovoort). Met deze sjabloon maakt u een correct geconfigureerde netwerkomgeving waarin u beheerde exemplaren kunt implementeren. |
| [Met SQL beheerd exemplaar met P2S-verbinding](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Met deze implementatie maakt u een virtueel Azure-netwerk met twee subnetten: `ManagedInstance` en `GatewaySubnet`. Het met SQL beheerde exemplaar wordt geïmplementeerd in het subnet ManagedInstance. Een virtuele netwerkgateway wordt gemaakt in het subnet `GatewaySubnet` en geconfigureerd voor een punt-naar-site VPN-verbinding. |
| [Met SQL beheerd exemplaar met virtuele machine](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Met deze implementatie maakt u een virtueel Azure-netwerk met twee subnetten: `ManagedInstance` en `Management`. Het met SQL beheerde exemplaar wordt geïmplementeerd in het subnet `ManagedInstance`. Een virtuele machine met de meest recente versie van SQL Server Management Studio (SSMS) wordt geïmplementeerd in het subnet `Management`. |

---
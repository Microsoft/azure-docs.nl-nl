---
title: Gegevens synchroniseren vanuit Azure SQL Edge met behulp van SQL Data Sync
description: Meer informatie over het synchroniseren van gegevens van Azure SQL Edge met behulp van Azure SQL Data Sync
keywords: SQL Edge, gegevens synchroniseren vanaf SQL Edge, SQL Edge-gegevenssynchronisatie
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5659ce5fa6f99463f58a33662563d768248fd8cb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93394897"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Zelfstudie: Gegevens synchroniseren vanuit Azure SQL Edge naar Azure SQL Database met behulp van SQL Data Sync

In deze zelfstudie leert u hoe u een Azure SQL Data Sync-*synchronisatiegroep* kunt gebruiken om gegevens van de Azure SQL Edge incrementeel te synchroniseren naar Azure SQL Database. SQL Data Sync is een service die is gebouwd op een Azure SQL Database waarmee u de gegevens die u selecteert in twee richtingen kunt synchroniseren over meerdere Microsoft Azure SQL-databases en SQL server-exemplaren. Zie [Azure SQL Data Sync](../azure-sql/database/sql-data-sync-data-sql-server-sql-database.md) voor meer informatie over SQL Data Sync.

Omdat SQL Edge is gebaseerd op de nieuwste versies van de [SQL Server Database-engine](/sql/sql-server/sql-server-technical-documentation/), kunnen alle gegevenssynchronisatiemechanismen die van toepassing zijn op een SQL Server-exemplaar ook worden gebruikt voor het synchroniseren van gegevens van of naar een SQL Edge-exemplaar dat wordt uitgevoerd op een edge-apparaat.

## <a name="prerequisites"></a>Vereisten

Bij deze zelfstudie is een Windows-computer vereist die is geconfigureerd met de [Data Sync-agent voor Azure SQL Data Sync](../azure-sql/database/sql-data-sync-agent-overview.md).

## <a name="before-you-begin"></a>Voordat u begint

* Een database maken in Azure SQL Database. Zie [Een afzonderlijke database maken in Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal) voor meer informatie over het maken van een database met behulp van de Azure-portal.

* De tabellen en andere benodigde objecten in uw Azure SQL Database-implementatie maken.

* De benodigde tabellen en objecten in uw Azure SQL Edge-implementatie maken. Zie [SQL Database DAC-pakketten gebruiken met SQL Edge](deploy-dacpac.md) voor meer informatie.

* De Azure SQL Edge-instantie registreren met de Data Sync-agent voor Azure SQL Data Sync. Zie [Een SQL Server-database toevoegen](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-on-prem) voor meer informatie.

## <a name="sync-data-between-a-database-in-azure-sql-database-and-sql-edge"></a>Gegevens synchroniseren tussen een database in Azure SQL Database en SQL Edge

Het instellen van synchronisatie tussen een database in Azure SQL Database en een SQL Edge-exemplaar met behulp van SQL Data Sync omvat drie belangrijke stappen:  


1. Gebruik de Azure-portal om een synchronisatiegroep te maken. Zie [Een synchronisatiegroep maken](../azure-sql/database/sql-data-sync-sql-server-configure.md#create-sync-group) voor meer informatie. U kunt één *hub*-database gebruiken om meerdere synchronisatiegroepen te maken om gegevens van verschillende SQL Edge-exemplaren te synchroniseren met een of meer databases in Microsoft Azure SQL Database. 

2. Voeg synchronisatieleden toe aan de synchronisatiegroep. Zie [Synchronisatieleden toevoegen](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-sync-members) voor meer informatie.

3. Stel de synchronisatiegroep in om de tabellen te selecteren die deel zullen uitmaken van de synchronisatie. Zie [Een synchronisatiegroep configureren](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-sync-members) voor meer informatie.

Nadat u de voorgaande stappen hebt voltooid, hebt u een synchronisatiegroep die een database in Azure SQL Database en een SQL Edge-exemplaar bevat.

Zie de volgende artikelen voor meer informatie over SQL Data Sync:

* [Data Sync-agent voor Azure SQL Data Sync](../azure-sql/database/sql-data-sync-agent-overview.md)

* [Best practices](../azure-sql/database/sql-data-sync-best-practices.md) en [Problemen met Azure SQL Data Sync oplossen](../azure-sql/database/sql-data-sync-troubleshoot.md)

* [SQL Data Sync bewaken met Azure Monitor-logboeken](../azure-sql/database/monitor-tune-overview.md)

* [Het synchronisatieschema bijwerken met Transact-SQL](../azure-sql/database/sql-data-sync-update-sync-schema.md) of [PowerShell](../azure-sql/database/scripts/update-sync-schema-in-sync-group.md)

## <a name="next-steps"></a>Volgende stappen


* [PowerShell gebruiken om tussen Azure SQL-database en Azure SQL Edge te synchroniseren](../azure-sql/database/scripts/sql-data-sync-sync-data-between-azure-onprem.md). In deze zelfstudie vervangt u de `OnPremiseServer` databasedetails door de details van Azure SQL Edge.
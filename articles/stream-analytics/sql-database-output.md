---
title: Azure SQL Database uitvoer van Azure Stream Analytics
description: In dit artikel wordt Azure SQL Database als uitvoer voor Azure Stream Analytics beschreven.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 631fdba451f69e44a675d396a42e1cddaea50a3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98013954"
---
# <a name="azure-sql-database-output-from-azure-stream-analytics"></a>Azure SQL Database uitvoer van Azure Stream Analytics

U kunt [Azure SQL database](https://azure.microsoft.com/services/sql-database/) gebruiken als uitvoer voor gegevens die relationeel zijn of voor toepassingen die afhankelijk zijn van inhoud die wordt gehost in een relationele data base. Azure Stream Analytics-taken worden geschreven naar een bestaande tabel in SQL Database. Het tabel schema moet exact overeenkomen met de velden en de bijbehorende typen in de uitvoer van uw taak. U kunt ook [Azure Synapse Analytics](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) opgeven als uitvoer via de optie SQL database uitvoer. Zie het artikel [Stream Analytics met Azure SQL database als uitvoer als](stream-analytics-sql-output-perf.md) u meer wilt weten over manieren om de schrijf doorvoer te verbeteren.

U kunt ook [Azure SQL Managed instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) als uitvoer gebruiken. U moet een [openbaar eind punt configureren in het SQL Managed instance](../azure-sql/managed-instance/public-endpoint-configure.md) en vervolgens hand matig de volgende instellingen configureren in azure stream Analytics. Een virtuele Azure-machine met SQL Server met een gekoppelde data base wordt ook ondersteund door de onderstaande instellingen hand matig te configureren.

## <a name="output-configuration"></a>Uitvoer configuratie

De volgende tabel bevat de namen van de eigenschappen en de bijbehorende beschrijvingen voor het maken van een SQL Database uitvoer.

| Naam van eigenschap | Description |
| --- | --- |
| Uitvoeralias |Een beschrijvende naam die wordt gebruikt in query's om de uitvoer van de query naar deze data base te sturen. |
| Database | De naam van de Data Base waarin u uw uitvoer wilt verzenden. |
| Servernaam | De logische SQL-Server naam of de naam van het beheerde exemplaar. Voor het beheerde exemplaar van SQL moet de poort 3342 worden opgegeven. Bijvoorbeeld *sampleserver. public. data base. Windows. net, 3342* |
| Gebruikersnaam | De gebruikers naam die schrijf toegang tot de data base heeft. Stream Analytics ondersteunt alleen SQL-verificatie. |
| Wachtwoord | Het wacht woord om verbinding te maken met de data base. |
| Tabel | De tabel naam waarin de uitvoer wordt geschreven. De tabel naam is hoofdletter gevoelig. Het schema van deze tabel moet exact overeenkomen met het aantal velden en de bijbehorende typen die uw taak uitvoer genereert. |
|Partitie schema overnemen| Een optie voor het overnemen van het partitie schema van uw vorige query stap, om een volledig parallelle topologie met meerdere schrijvers voor de tabel in te scha kelen. Zie [Azure stream Analytics uitvoer naar Azure SQL database](stream-analytics-sql-output-perf.md)voor meer informatie.|
|Maximum aantal batches| De aanbevolen bovengrens voor het aantal records dat wordt verzonden met elke bulksgewijze insert-trans actie.|

Er zijn twee adapters die uitvoer van Azure Stream Analytics naar Azure Synapse Analytics: SQL Database en Azure Synapse. Als een van de volgende voor waarden van toepassing is, kunt u het beste de Azure Synapse Analytics-adapter kiezen in plaats van de SQL Database adapter:

* **Door Voer**: als de verwachte door Voer nu of in de toekomst groter is dan 10 MB per seconde, gebruikt u de Azure Synapse-uitvoer optie voor betere prestaties.

* **Invoer partities**: als u acht of meer invoer partities hebt, gebruikt u de Azure Synapse-uitvoer optie voor beter uitschalen.

## <a name="partitioning"></a>Partitionering

Partitioneren moet zijn ingeschakeld en is gebaseerd op de component PARTITION BY in de query. Wanneer de optie partitioneren overnemen is ingeschakeld, volgt de invoer partitie voor [volledige kan worden opgestart-query's](stream-analytics-scale-jobs.md). Zie [Azure stream Analytics output to Azure SQL database](stream-analytics-sql-output-perf.md)voor meer informatie over het verbeteren van de prestaties van schrijf doorvoer tijdens het laden van gegevens in Azure SQL database.

## <a name="output-batch-size"></a>Grootte van uitvoer batch

U kunt de maximale bericht grootte configureren met behulp van **maximum aantal batches**. De standaard waarde is 10.000 en de minimale standaard waarde is 100 rijen per afzonderlijke bulk invoer. Zie [Azure SQL-limieten](../azure-sql/database/resource-limits-logical-server.md)voor meer informatie. Elke batch wordt in eerste instantie bulksgewijs ingevoegd met een maximum aantal batches. Batch is gesplitst in tweeën (totdat het minimale aantal batches is) op basis van Herhaal bare fouten van SQL.

## <a name="next-steps"></a>Volgende stappen

* [Beheerde identiteiten gebruiken om toegang te krijgen tot Azure SQL Database of Azure Synapse Analytics vanuit een Azure Stream Analytics-taak (preview)](sql-database-output-managed-identity.md)
* [Snelstart: Een Stream Analytics-taak maken via Azure Portal](stream-analytics-quick-create-portal.md)
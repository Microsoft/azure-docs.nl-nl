---
title: Ondersteunde functies van Azure Synapse Link voor Azure Cosmos DB
description: Krijg meer informatie over de huidige lijst met acties die worden ondersteund in Azure Synapse Link voor Azure Cosmos DB
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: synapse-link
ms.date: 03/02/2021
ms.author: rosouz
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: cdc9f344e108fc58399f9bcb2a9f02a4659ecfe1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627673"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-supported-features"></a>Ondersteunde functies van Azure Synapse Link voor Azure Cosmos DB

In dit artikel worden de functies beschreven die momenteel worden ondersteund in Azure Synapse Link voor Azure Cosmos DB.

## <a name="azure-synapse-support"></a>Ondersteuning in Azure Synapse

Er zijn twee typen containers in Azure Cosmos DB:
* HTAP-container: een container waarvoor Synapse Link is ingeschakeld. Deze container heeft zowel een transactionele opslag als een analytische opslag. 
* OLTP-container-een container met de Synaspe-koppeling is niet ingeschakeld. Deze container heeft alleen transactionele Store en geen analytische opslag.

> [!IMPORTANT]
> De koppeling van Azure Synapse voor Azure Cosmos DB wordt momenteel ondersteund in Synapse-werk ruimten waarvoor geen beheerd virtueel netwerk is ingeschakeld. 

U kunt verbinding maken met een Azure Cosmos DB-container zonder de Synapse-koppeling in te scha kelen. In dit scenario kunt u alleen lezen/schrijven naar de transactionele Store. Hier volgt een lijst met de momenteel ondersteunde functies in de Synapse-koppeling voor Azure Cosmos DB. 

| Categorie              | Beschrijving |[Apache Spark-pool](../sql/on-demand-workspace-overview.md) | [Serverloze SQL-pool](../sql/on-demand-workspace-overview.md) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- |
| **Ondersteuning tijdens uitvoeringen** |Ondersteunde Azure Synapse runtime voor toegang tot Azure Cosmos DB| ✓ | ✓ |
| **API-ondersteuning voor Azure Cosmos DB** | Ondersteund Azure Cosmos DB-API-soort | SQL / MongoDB | SQL / MongoDB |
| **Object**  |Objecten, zoals een tabel, die kunnen worden gemaakt en rechtstreeks naar de Azure Cosmos DB-container verwijzen| Data frame, weer gave, tabel | Weergave |
| **Lezen**    | Type Azure Cosmos DB container dat kan worden gelezen | OLTP / HTAP | HTAP  |
| **Schrijven**   | Kan Azure Synapse runtime worden gebruikt voor het schrijven van gegevens naar een Azure Cosmos DB-container | Ja | Nee |

* Als u gegevens in een Azure Cosmos DB container schrijft vanuit Spark, wordt dit proces uitgevoerd via het transactionele archief van Azure Cosmos DB. Dit heeft invloed op de transactionele prestaties van Azure Cosmos DB door aanvraag eenheden te gebruiken.
* Integratie van exclusieve SQL-groep via externe tabellen wordt momenteel niet ondersteund.
 
## <a name="supported-code-generated-actions-for-spark"></a>Ondersteunde met code gegenereerde acties voor Spark

| Bewegen              | Beschrijving |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Laden in Dataframe** |Gegevens laden en lezen in een Spark-dataframe |✓| ✓ |
| **Spark-tabel maken** |Een tabel maken die verwijst naar een Azure Cosmos DB-container|✓| ✓ |
| **Dataframe naar een container schrijven** |Gegevens naar een container schrijven|✓| ✓ |
| **Streaming-dataframe laden vanuit een container** |Gegevens streamen met behulp van de wijzigingenfeed in Azure Cosmos DB|✓| ✓ |
| **Streaming-dataframe naar een container schrijven** |Gegevens streamen met behulp van de wijzigingenfeed in Azure Cosmos DB|✓| ✓ |

## <a name="supported-code-generated-actions-for-serverless-sql-pool"></a>Ondersteunde door code gegenereerde acties voor een serverloze SQL-groep

| Bewegen              | Beschrijving |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Gegevens verkennen** |Gegevens verkennen uit een container met bekende T-SQL-syntaxis en automatische schema-deinterferentie|X| ✓ |
| **Weer gaven maken en BI-rapporten bouwen** |Een SQL-weer gave maken voor directe toegang tot een container voor BI via serverloze SQL-groep |X| ✓ |
| **Samen voegen met ongelijksoortige gegevens bronnen en Cosmos DB gegevens** | Sla de resultaten op van het lezen van gegevens uit Cosmos DB containers samen met gegevens in Azure Blob Storage of Azure Data Lake Storage met behulp van CETAS |X| ✓ |

## <a name="next-steps"></a>Volgende stappen

* [Verbinding maken met Synapse Link voor Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
* [Meer informatie over het uitvoeren van een query op het Cosmos DB-analytische archief met Spark](how-to-query-analytical-store-spark.md)
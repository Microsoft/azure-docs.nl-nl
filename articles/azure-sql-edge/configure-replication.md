---
title: Replicatie naar Azure SQL Edge configureren
description: Meer informatie over het configureren van replicatie naar Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 3b424ece9207328d87068160f78ebc78a3bd1a8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395220"
---
# <a name="configure-replication-to-azure-sql-edge"></a>Replicatie naar Azure SQL Edge configureren 

U kunt een exemplaar van de Azure SQL-rand configureren als de push-abonnee voor transactionele replicatie of momentopname replicatie op eenrichtings wijze. Dit exemplaar kan niet fungeren als de uitgever of de Distributor voor een transactionele replicatie Configuratie. Houd er rekening mee dat Azure SQL Edge geen ondersteuning biedt voor samenvoeg replicatie, peer-to-peer-replicatie of Oracle-publicatie.

## <a name="supported-configurations"></a>Ondersteunde configuraties
  
- Het exemplaar van de Azure SQL-Edge moet een push-abonnee zijn voor een uitgever.
- De uitgever en de Distributor kunnen een van de volgende zijn:
   - Een exemplaar van SQL Server dat on-premises wordt uitgevoerd of een exemplaar van SQL Server dat wordt uitgevoerd op een virtuele machine van Azure. Zie [SQL Server op Azure virtual machines Overview](../azure-sql/virtual-machines/index.yml)voor meer informatie. SQL Server exemplaren moeten een versie hebben die hoger is dan SQL Server 2016.
   - Een exemplaar van een door Azure SQL beheerd exemplaar. SQL Managed instance kan Uitgever, distributeur en abonnee database hosten. Zie [replicatie met SQL database Managed instance](/azure/sql-database/replication-with-sql-database-managed-instance/)voor meer informatie.

- De distributie database en de replicatie agenten kunnen niet worden geplaatst op een exemplaar van de Azure SQL-rand.  

> [!NOTE]
> Als u probeert replicatie te configureren met behulp van een niet-ondersteunde versie, kunnen de volgende twee fouten optreden: MSSQL_REPL20084 (' het proces kan geen verbinding maken met de abonnee ') en MSSQL_REPL40532 (' kan de server \<name> die door de aanmelding is aangevraagd, niet openen. De aanmelding is mislukt. ').  

## <a name="remarks"></a>Opmerkingen

De volgende vereisten en aanbevolen procedures zijn belang rijk om te begrijpen wanneer u replicatie configureert:

- U kunt replicatie configureren met behulp van [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). U kunt dit ook doen door Transact-SQL-instructies uit te voeren op de Publisher met behulp van SQL Server Management Studio of [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio).
- Als u wilt repliceren naar een exemplaar van Azure SQL Edge, moet u SQL Server-verificatie gebruiken om u aan te melden.
- Gerepliceerde tabellen moeten een primaire sleutel hebben.
- Eén publicatie op SQL Server kan zowel Azure SQL-Edge als SQL Server (on-premises en SQL Server in een virtuele machine van Azure) ondersteunen.  
- Replicatie beheer, bewaking en probleem oplossing moeten worden uitgevoerd vanuit het SQL Server-exemplaar.  
- Alleen push-abonnementen naar Azure SQL Edge worden ondersteund.  
- `@subscriber_type = 0`Wordt alleen ondersteund in de opgeslagen procedure `sp_addsubscription` voor Azure SQL Edge.  
- Azure SQL Edge biedt geen ondersteuning voor bi-directionele, onmiddellijke, bijwerk bare of peer-to-peer-replicatie.
- Azure SQL Edge ondersteunt alleen een subset van functies die beschikbaar zijn in SQL Server of een door SQL beheerd exemplaar. Als u probeert een Data Base (of objecten in de data base) te repliceren die een of meer niet-ondersteunde functies bevat, mislukt de poging. Als u bijvoorbeeld een Data Base probeert te repliceren die objecten met ruimtelijke gegevens typen bevat, wordt er een fout bericht weer gegeven. Zie [ondersteunde functies van Azure SQL Edge](features.md)voor meer informatie.

## <a name="initialize-reference-data-on-an-instance-of-azure-sql-edge"></a>Referentie gegevens initialiseren op een exemplaar van Azure SQL Edge

Mogelijk wilt u uw exemplaar initialiseren met referentie gegevens die na verloop van tijd veranderen. U kunt bijvoorbeeld machine learning modellen bijwerken op uw exemplaar van Azure SQL Edge, nadat deze zijn getraind op een SQL Server-exemplaar. U kunt als volgt uw exemplaar in een dergelijk scenario initialiseren:

1. Een transactionele replicatie publicatie maken op een SQL Server Data Base.  
2. Gebruik op het SQL Server-exemplaar de **wizard Nieuw abonnement** of de Transact-SQL-instructies om een push te maken voor een abonnement op Azure SQL Edge.  
3. U kunt de gerepliceerde data base initialiseren op Azure SQL Edge met behulp van een moment opname die is gegenereerd door de snap shot agent en die wordt gedistribueerd en geleverd door de distributie agent. U kunt ook initialiseren met behulp van een back-up van de data base van de uitgever. Houd er rekening mee dat als de back-up van de data base objecten of functies bevat die niet worden ondersteund door Azure SQL Edge, de herstel bewerking mislukt.

## <a name="limitations"></a>Beperkingen

De volgende opties worden niet ondersteund voor Azure SQL Edge-abonnementen:

- Koppeling bestands groepen kopiëren  
- Schema's voor het partitioneren van tabellen kopiëren  
- Schema's voor het partitioneren van indexen kopiëren  
- Door de gebruiker gedefinieerde statistieken kopiëren  
- Standaard bindingen kopiëren  
- Regel bindingen kopiëren  
- Volledige-tekst indexen kopiëren  
- XSD van kopiëren XML  
- XML-indexen kopiëren  
- Machtigingen kopiëren  
- Ruimtelijke indexen kopiëren  
- Gefilterde indexen kopiëren  
- Gegevens compressie kenmerk kopiëren  
- Sparse kolom kenmerk kopiëren  
- FileStream-, `hierarchyid` of ruimtelijke gegevens typen kopiëren
- Converteren `hierarchyid` naar maximum aantal gegevens typen  
- Ruimtelijke naar maximum aantal gegevens typen converteren  
- Uitgebreide eigenschappen kopiëren  
- Machtigingen kopiëren  

## <a name="examples"></a>Voorbeelden

Maak een publicatie en een push-abonnement. Zie voor meer informatie:
  
- [Een publicatie maken](/sql/relational-databases/replication/publish/create-a-publication)
- [Maak een push-abonnement](/sql/relational-databases/replication/create-a-push-subscription/) met behulp van de naam en het IP-adres van de Azure SQL Edge-Server (bijvoorbeeld **myEdgeinstance, 1433**) en een database naam op het exemplaar van de Azure SQL-Edge als de doel database (bijvoorbeeld **AdventureWorks**).  

## <a name="next-steps"></a>Volgende stappen  

- [Een publicatie maken](/sql/relational-databases/replication/publish/create-a-publication)
- [Een push-abonnement maken](/sql/relational-databases/replication/create-a-push-subscription/)
- [Typen replicatie](/sql/relational-databases/replication/types-of-replication)
- [Bewaking (replicatie)](/sql/relational-databases/replication/monitor/monitoring-replication)
- [Een abonnement initialiseren](/sql/relational-databases/replication/initialize-a-subscription)
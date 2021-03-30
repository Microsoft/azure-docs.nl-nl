---
title: Bulksgewijs invoegen optimaliseren-Azure Database for PostgreSQL-één server
description: In dit artikel wordt beschreven hoe u Bulk Insert-bewerkingen kunt optimaliseren op een Azure Database for PostgreSQL-één server.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 4d10f06577738364e3f4a0ea40221d37ebfb31c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86116281"
---
# <a name="optimize-bulk-inserts-and-use-transient-data-on-an-azure-database-for-postgresql---single-server"></a>Bulk toevoegingen optimaliseren en tijdelijke gegevens gebruiken op een Azure Database for PostgreSQL-één server 
In dit artikel wordt beschreven hoe u bewerkingen voor bulksgewijs invoegen optimaliseert en tijdelijke gegevens op een Azure Database for PostgreSQL server gebruikt.

## <a name="use-unlogged-tables"></a>Niet-geregistreerde tabellen gebruiken
Als u werk belasting bewerkingen hebt waarbij tijdelijke gegevens worden betrokken of als u grote data sets tegelijk invoegt, kunt u gebruikmaken van niet-geregistreerde tabellen.

Niet-geregistreerde tabellen is een PostgreSQL-functie die effectief kan worden gebruikt om bulk toevoegingen te optimaliseren. PostgreSQL maakt gebruik van Write-Ahead logboek registratie (WAL). Het biedt standaard atomische en duurzaamheid. Atomiciteit, consistentie, isolatie en duurzaamheid vormen de ACID-eigenschappen. 

Als u invoegt in een niet-geregistreerde tabel, betekent dit dat PostgreSQL wordt ingevoegd zonder te worden geschreven naar het transactie logboek. Dit is een I/O-bewerking. Als gevolg hiervan zijn deze tabellen aanzienlijk sneller dan gewone tabellen.

Gebruik de volgende opties om een niet-geregistreerde tabel te maken:
- Een nieuwe, niet-geregistreerde tabel maken met behulp van de syntaxis `CREATE UNLOGGED TABLE <tableName>` .
- Een bestaande, geregistreerde tabel naar een niet-geregistreerde tabel converteren met behulp van de syntaxis `ALTER TABLE <tableName> SET UNLOGGED` .  

Gebruik de syntaxis om het proces om te keren `ALTER TABLE <tableName> SET LOGGED` .

## <a name="unlogged-table-tradeoff"></a>Niet-geregistreerde tabel balans
Niet-geregistreerde tabellen zijn niet crash-veilig. Een niet-geregistreerde tabel wordt automatisch afgekapt na een crash of een onvolledige afsluiting. De inhoud van een niet-geregistreerde tabel wordt ook niet gerepliceerd naar stand-by-servers. Indexen die zijn gemaakt in een niet-geregistreerde tabel, worden ook automatisch niet geregistreerd. Nadat de bewerking is voltooid, converteert u de tabel naar vastgelegd zodat de invoeging duurzaam is.

Sommige werk belastingen van klanten hebben ongeveer een prestatie verbetering van 15 tot 20% ondervonden bij het gebruik van niet-geregistreerde tabellen.

## <a name="next-steps"></a>Volgende stappen
Controleer uw werk belasting op het gebruik van tijdelijke gegevens en grote bulk toevoegingen. Raadpleeg de volgende PostgreSQL-documentatie:
 
- [SQL-opdrachten voor tabel maken](https://www.postgresql.org/docs/current/static/sql-createtable.html)

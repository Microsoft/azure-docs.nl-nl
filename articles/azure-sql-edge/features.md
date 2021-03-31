---
title: Ondersteunde functies van Azure SQL Edge
description: Meer informatie over de functies die door Azure SQL Edge worden ondersteund.
keywords: Inleiding tot SQL Edge, wat is SQL Edge, overzicht van SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 19dcbbf102a1d8d21f1b14780ea33816a1677c55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392024"
---
# <a name="supported-features-of-azure-sql-edge"></a>Ondersteunde functies van Azure SQL Edge 

Azure SQL Edge is gebaseerd op de nieuwste versie van de SQL Database-Engine. Het ondersteunt een subset van de functies die worden ondersteund in SQL Server 2019 op Linux, naast sommige functies die momenteel niet worden ondersteund of beschikbaar zijn in SQL Server 2019 op Linux (of in SQL Server in Windows).

Zie voor een volledige lijst van de functies die worden ondersteund in SQL Server on Linux [edities en ondersteunde functies van SQL Server 2019 op Linux](/sql/linux/sql-server-linux-editions-and-components-2019). Zie [edities en ondersteunde functies van SQL Server 2019 (15. x)](/sql/sql-server/editions-and-components-of-sql-server-version-15)voor edities en ondersteunde functies van SQL Server in Windows.

## <a name="azure-sql-edge-editions"></a>Azure SQL Edge-edities

Azure SQL Edge is beschikbaar in twee verschillende edities of software plannen. Deze edities hebben identieke functie sets en verschillen alleen met de gebruiks rechten en de hoeveelheid geheugen en kernen die ze op het hostsysteem kunnen benaderen.

   |**Plannen**  |**Beschrijving**  |
   |---------|---------|
   |Ontwikkel aars van Azure SQL Edge  |  Alleen voor ontwikkeling. Elke Azure SQL Edge-ontwikkelaars container is beperkt tot Maxi maal 4 kernen en 32 GB geheugen.  |
   |Azure SQL Edge    |  Voor productie. Elke Azure SQL Edge-container is beperkt tot 8 kernen en 64 GB geheugen.  |

## <a name="operating-system"></a>Besturingssysteem

Azure SQL Edge-containers zijn gebaseerd op Ubuntu 18,04 en worden alleen ondersteund om te worden uitgevoerd op docker-hosts met Ubuntu 18,04 LTS (aanbevolen) of Ubuntu 20,04 LTS. Het is mogelijk om Azure SQL Edge-containers op andere hosts van het besturings systeem uit te voeren. het kan bijvoorbeeld worden uitgevoerd op andere distributies van Linux of op Windows (met behulp van docker CE of docker EE), maar micro soft raadt u echter niet aan om dit te doen, omdat deze configuratie mogelijk niet uitgebreid is getest.

De aanbevolen configuratie voor het uitvoeren van Azure SQL Edge in Windows is het configureren van een Ubuntu VM op de Windows-host en het uitvoeren van Azure SQL Edge in de Linux-VM.

Het aanbevolen en ondersteunde bestands systeem voor Azure SQL Edge is EXT4 en XFS. Als permanente volumes worden gebruikt om een back-up te maken van de Azure SQL Edge-database opslag, moet het onderliggende bestands systeem van de host EXT4 en XFS zijn.

## <a name="hardware-support"></a>Hardwareondersteuning

Voor Azure SQL Edge is een 64-bits processor vereist (x64 of ARM64), met mini maal één processor en één GB RAM-geheugen op de host. Hoewel het geheugen voor het opstarten van de 450MB van Azure SQL Edge bijna is, is het extra geheugen nodig voor andere IoT Edge modules of processen die op het apparaat Edge worden uitgevoerd. De werkelijke geheugen-en CPU-vereisten voor Azure SQL Edge variëren, afhankelijk van de complexiteit van de werk belasting en het volume van de gegevens die worden verwerkt. Wanneer u een hardware voor uw oplossing kiest, raadt micro soft u aan uitgebreide prestatie tests uit te voeren om ervoor te zorgen dat aan de vereiste prestatie kenmerken voor uw oplossing wordt voldaan.  

## <a name="azure-sql-edge-components"></a>Azure SQL Edge-onderdelen

Azure SQL Edge ondersteunt alleen de data base-engine. Het bevat geen ondersteuning voor andere onderdelen die beschikbaar zijn met SQL Server 2019 in Windows of met SQL Server 2019 in Linux. Met name Azure SQL Edge biedt geen ondersteuning voor SQL Server onderdelen zoals Analysis Services, Reporting Services, Integration Services, Master Data Services, Machine Learning Services (in-data base) en Machine Learning Server (zelfstandige versie).

## <a name="supported-features"></a>Ondersteunde functies

Naast het ondersteunen van een subset van de functies van SQL Server on Linux biedt Azure SQL EDGE ondersteuning voor de volgende nieuwe functies: 

- SQL-streaming, dat is gebaseerd op dezelfde engine die Azure Stream Analytics, biedt mogelijkheden voor realtime gegevens stromen in Azure SQL Edge. 
- De T-SQL-functie aanroep `Date_Bucket` voor Time-Series gegevens analyse.
- Machine learning-mogelijkheden via de ONNX-runtime, opgenomen in de SQL-engine.

## <a name="unsupported-features"></a>Niet-ondersteunde functies

De volgende lijst bevat de SQL Server 2019 in Linux-functies die momenteel niet worden ondersteund in Azure SQL Edge.

| Gebied | Niet-ondersteunde functie of service |
|-----|-----|
| **Database ontwerp** | In-Memory OLTP, en gerelateerde DDL-opdrachten en Transact-SQL-functies, catalogus weergaven en dynamische beheer weergaven. |
| &nbsp; | `HierarchyID` gegevens type en gerelateerde DDL-opdrachten en Transact-SQL-functies, catalogus weergaven en dynamische beheer weergaven. |
| &nbsp; | `Spatial` gegevens type en gerelateerde DDL-opdrachten en Transact-SQL-functies, catalogus weergaven en dynamische beheer weergaven. |
| &nbsp; | Stretch DB en gerelateerde DDL-opdrachten en Transact-SQL-functies, catalogus weergaven en dynamische beheer weergaven. |
| &nbsp; | Volledige-tekst indexen en zoek acties, en gerelateerde DDL-opdrachten en Transact-SQL-functies, catalogus weergaven en dynamische beheer weergaven.|
| &nbsp; | `FileTable`, `FILESTREAM` en gerelateerde DDL-opdrachten en Transact-SQL-functies, catalogus weergaven en dynamische beheer weergaven.|
| **Database Engine** | Replicat. Houd er rekening mee dat u Azure SQL Edge kunt configureren als een push-abonnee van een replicatie topologie. |
| &nbsp; | Poly base. Houd er rekening mee dat u Azure SQL Edge kunt configureren als een doel voor externe tabellen in poly base. |
| &nbsp; | Taal uitbreid baarheid via Java en Spark. |
| &nbsp; | Integratie van Active Directory. |
| &nbsp; | Data base automatisch verkleinen. De eigenschap automatisch verkleinen voor een Data Base kan worden ingesteld met behulp van de `ALTER DATABASE <database_name> SET AUTO_SHRINK ON` opdracht, maar deze wijziging heeft geen effect. De automatische verkleinings taak wordt niet uitgevoerd voor de data base. Gebruikers kunnen de database bestanden nog steeds verkleinen met behulp van de opdracht ' DBCC '. |
| &nbsp; | Database momentopnamen. |
| &nbsp; | Ondersteuning voor permanent geheugen. |
| &nbsp; | Micro soft gedistribueerde transactie. |
| &nbsp; | Resource regeling en i/o-resource beheer. |
| &nbsp; | Extensie van de buffer groep. |
| &nbsp; | Gedistribueerde query's met verbindingen van derden. |
| &nbsp; | Gekoppelde servers. |
| &nbsp; | Door systeem uitgebreide opgeslagen procedures (zoals `XP_CMDSHELL` ). |
| &nbsp; | CLR-assembly's en gerelateerde DDL-opdrachten en Transact-SQL-functies, catalogus weergaven en dynamische beheer weergaven. |
| &nbsp; | CLR: afhankelijke T-SQL-functies, zoals `ASSEMBLYPROPERTY` , `FORMAT` , `PARSE` en `TRY_PARSE` . |
| &nbsp; | CLR: afhankelijke datum-en tijd catalogus weergaven, functies en query componenten. |
| &nbsp; | Extensie van de buffer groep. |
| &nbsp; | Data base mail. |
| &nbsp; | Service Broker |
| &nbsp; | Beheer op basis van beleid |
| &nbsp; | Management-Data Warehouse |
| &nbsp; | Inge sloten data bases |
| **SQL Server Agent** |  Subsystemen: CmdExec, Power shell, Queue Reader, SSIS, SSAS en SSRS. |
| &nbsp; | Berichten. |
| &nbsp; | Beheerde back-up. |
| **Hoge beschikbaarheid** | AlwaysOn-beschikbaarheids groepen.  |
| &nbsp; | Basis beschikbaarheids groepen. |
| &nbsp; | Altijd op het failover-cluster exemplaar. |
| &nbsp; | Database spiegeling. |
| &nbsp; | Hot toevoegen van geheugen en CPU. |
| **Beveiliging** | Uitbreidbaar sleutel beheer. |
| &nbsp; | Integratie van Active Directory.|
| &nbsp; | Ondersteuning voor beveiligde enclaves.|
| **Services** | SQL Server Browser. |
| &nbsp; | Machine Learning tot en met R en python. |
| &nbsp; | StreamInsight. |
| &nbsp; | Analysis Services. |
| &nbsp; | Reporting Services. |
| &nbsp; | Data Quality Services. |
| &nbsp; | Master Data Services. |
| &nbsp; | Distributed Replay. |
| **Beheer baarheid** | SQL Server Utility controle punt. |

## <a name="next-steps"></a>Volgende stappen

- [Azure SQL Edge implementeren](deploy-portal.md)
- [Azure SQL Edge configureren](configure.md)
- [Verbinding maken met een exemplaar van Azure SQL Edge met SQL Server-client hulpprogramma's](connect.md)
- [Back-up en herstel van data bases in Azure SQL Edge](backup-restore.md)
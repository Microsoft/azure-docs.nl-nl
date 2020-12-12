---
title: Azure Storage gebruiken voor SQL Server back-up en herstellen | Microsoft Docs
description: Meer informatie over het maken van een back-up van SQL Server naar Azure Storage. Hierin worden de voor delen van het maken van back-ups van SQL-data bases in Azure Storage uitgelegd
services: virtual-machines-windows
documentationcenter: ''
author: MikeRayMSFT
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.subservice: backup
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mathoma
ms.openlocfilehash: b4100800385792557358d3fb6438f52650483f89
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359786"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Azure Storage gebruiken voor SQL Server back-up en herstel
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Vanaf SQL Server 2012 SP1 CU2 kunt u nu rechtstreeks SQL Server back-ups naar Azure Blob-opslag schrijven. U kunt deze functie gebruiken om een back-up te maken van en te herstellen vanuit Azure Blob-opslag en een SQL Server-Data Base. De back-up naar de Cloud biedt voor delen van Beschik baarheid, onbeperkte geo-gerepliceerde opslag op locatie en gemakkelijke migratie van gegevens van en naar de Cloud. U kunt een back-up-of Restore-instructie verzenden met behulp van Transact-SQL of SMO.

## <a name="overview"></a>Overzicht
SQL Server 2016 introduceert nieuwe mogelijkheden. u kunt [back-ups van bestands momentopnamen](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) gebruiken om bijna momentane back-ups en zeer snelle herstel bewerkingen uit te voeren.

In dit onderwerp wordt uitgelegd waarom u Azure Storage kunt gebruiken voor SQL Server back-ups en worden vervolgens de betrokken onderdelen beschreven. U kunt de bronnen aan het einde van het artikel gebruiken om toegang te krijgen tot instructies en aanvullende informatie om deze service te gaan gebruiken met uw SQL Server back-ups.

## <a name="benefits-of-using-azure-blob-storage-for-sql-server-backups"></a>Voor delen van het gebruik van Azure Blob Storage voor SQL Server back-ups
Er zijn verschillende uitdagingen waarbij u een back-up maakt van SQL Server. Deze uitdagingen zijn onder andere opslag beheer, risico van opslag storingen, toegang tot externe opslag en hardwareconfiguratie. Veel van deze uitdagingen worden verholpen met Azure Blob Storage voor SQL Server back-ups. Houd rekening met de volgende voor delen:

* **Gebruiks gemak**: het opslaan van uw back-ups in azure-blobs kan een handige, flexibele en eenvoudig te gebruiken optie zijn. Het maken van een off-site opslag voor uw SQL Server back-ups kan net zo eenvoudig zijn als het wijzigen van uw bestaande scripts/taken voor het gebruik van de **back-up-URL-** syntaxis. Buiten-site opslag is doorgaans voldoende van de locatie van de productie database om één nood geval te voor komen die van invloed kan zijn op de locaties van de buiten-site-en productie database. Als u ervoor kiest om [uw Azure-blobs op geografische](../../../storage/common/storage-redundancy.md)locatie te repliceren, hebt u een extra beveiligingslaag in het geval van een nood situatie die van invloed kan zijn op de hele regio.
* **Back-uparchief**: Azure Blob-opslag biedt een beter alternatief voor de vaak gebruikte tape optie voor het archiveren van back-ups. Tape opslag vereist mogelijk fysiek Trans Port naar een off-site faciliteit en metingen om de media te beveiligen. Het opslaan van uw back-ups in Azure Blob-opslag biedt een onmiddellijke, Maxi maal beschik bare en duurzame archiverings optie.
* **Beheerde hardware**: er is geen overhead van hardwarematig beheer met Azure-Services. Azure-Services beheren de hardware en bieden geo-replicatie voor redundantie en bescherming tegen hardwarestoringen.
* **Onbeperkte opslag**: door een directe back-up naar Azure-blobs in te scha kelen, hebt u toegang tot vrijwel onbeperkte opslag. Als u een back-up wilt maken op een virtuele machine van Azure, zijn er beperkingen ingesteld op basis van de computer grootte. Er is een limiet voor het aantal schijven dat u kunt koppelen aan een virtuele machine van Azure voor back-ups. Deze limiet is 16 schijven voor een extra grote instantie en minder voor kleinere exemplaren.
* **Beschik baarheid van back-ups**: back-ups die zijn opgeslagen in azure-blobs, zijn overal en op elk gewenst moment beschikbaar en kunnen eenvoudig worden geopend voor herstel naar een SQL Server-exemplaar, zonder dat de data base moet worden gekoppeld/losgekoppeld of gedownload en de VHD wordt gekoppeld.
* **Kosten**: Betaal alleen voor de service die wordt gebruikt. Kan rendabel zijn als een off-site-en back-uparchief optie. Zie de [Azure-prijs calculator](https://go.microsoft.com/fwlink/?LinkId=277060 "Prijscalculator")en het [artikel over Azure-prijzen](https://go.microsoft.com/fwlink/?LinkId=277059 "Prijs artikel") voor meer informatie.
* **Opslag momentopnamen**: wanneer database bestanden worden opgeslagen in een Azure-Blob en u SQL Server 2016 gebruikt, kunt u [back-ups van bestands momentopnamen](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) gebruiken om bijna momentane back-ups en zeer snelle herstel bewerkingen uit te voeren.

Zie [SQL Server Backup en Restore with Azure Blob Storage](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)(Engelstalig) voor meer informatie.

In de volgende twee secties wordt Azure Blob-opslag geïntroduceerd, met inbegrip van de vereiste SQL Server onderdelen. Het is belang rijk om inzicht te krijgen in de onderdelen en hun interactie om back-ups te maken en te herstellen vanuit Azure Blob-opslag.

## <a name="azure-blob-storage-components"></a>Azure Blob-opslag onderdelen
De volgende Azure-onderdelen worden gebruikt bij het maken van een back-up naar Azure Blob Storage.

| Onderdeel | Beschrijving |
| --- | --- |
| **Opslagaccount** |Het opslag account is het begin punt voor alle opslag Services. Als u toegang wilt krijgen tot Azure Blob-opslag, moet u eerst een Azure Storage-account maken. Zie [Azure Blob Storage gebruiken](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/)voor meer informatie over Azure Blob-opslag. |
| **Container** |Een container biedt een groepering van een set blobs en kan een onbeperkt aantal blobs bevatten. Als u een SQL Server back-up wilt schrijven naar Azure Blob-opslag, moet u ten minste de basis container hebben gemaakt. |
| **Blob** |Een bestand van elk type en elke grootte. Blobs zijn adresseerbaar met behulp van de volgende URL-indeling: **https://[Storage account]. blob. core. Windows. net/[container]/[BLOB]**. Zie [Wat zijn blok-en pagina-blobs](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) ? voor meer informatie over pagina-blobs. |

## <a name="sql-server-components"></a>SQL Server onderdelen
De volgende SQL Server onderdelen worden gebruikt bij het maken van een back-up naar Azure Blob Storage.

| Onderdeel | Beschrijving |
| --- | --- |
| **URL** |Met een URL wordt een Uniform Resource Identifier (URI) naar een uniek back-upbestand opgegeven. De URL wordt gebruikt om de locatie en naam van het back-upbestand van SQL Server op te geven. De URL moet verwijzen naar een echte blob, niet alleen een container. Als de BLOB niet bestaat, wordt deze gemaakt. Als een bestaande blob is opgegeven, mislukt de back-up, tenzij de > met de optie FORMAT is opgegeven. Hier volgt een voor beeld van de URL die u in de back-upopdracht zou opgeven: **http [s]://[Storage account]. blob. core. Windows. net/[container]/[filename. bak]**. HTTPS wordt aanbevolen, maar is niet vereist. |
| **Referentie** |De gegevens die nodig zijn om verbinding te maken en te verifiëren met Azure Blob Storage, worden opgeslagen als referentie. Als SQL Server back-ups naar een Azure-Blob wilt schrijven of van deze wilt herstellen, moet u een SQL Server referentie maken. Zie [SQL Server referentie](/sql/t-sql/statements/create-credential-transact-sql)voor meer informatie. |

> [!NOTE]
> SQL Server 2016 is bijgewerkt ter ondersteuning van blok-blobs. Raadpleeg de [zelf studie: Microsoft Azure Blob-opslag gebruiken met SQL Server 2016-data bases](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016) voor meer informatie.
> 
> 

## <a name="next-steps"></a>Volgende stappen
1. Maak een Azure-account als u er nog geen hebt. Als u Azure evalueert, moet u rekening houden met de [gratis proef versie](https://azure.microsoft.com/free/).
2. Ga vervolgens door met een van de volgende zelf studies, waarmee u een opslag account maakt en een herstel bewerking uitvoert.
   
   * **SQL Server 2014**: [zelf studie: SQL Server 2014 back-up en herstel naar Microsoft Azure Blob-opslag](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
   * **SQL Server 2016**: [zelf studie: de Microsoft Azure Blob storage gebruiken met SQL Server 2016-data bases](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016)
3. Bekijk de aanvullende documentatie vanaf [SQL Server back-up en herstel met Microsoft Azure Blob-opslag](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service).

Als u problemen ondervindt, raadpleegt u het onderwerp [SQL Server back-up naar aanbevolen procedures voor URL en probleem oplossing](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting).

Zie [back-up en herstel voor SQL Server op Azure virtual machines](backup-restore.md)voor andere SQL Server opties voor back-up en herstel.
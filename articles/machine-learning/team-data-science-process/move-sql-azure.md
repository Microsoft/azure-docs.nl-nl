---
title: Gegevens verplaatsen naar een Azure SQL Database-team data Science process
description: Gegevens verplaatsen van platte bestanden (CSV-of TSV-indeling) of van gegevens die zijn opgeslagen in een SQL Server naar een Azure SQL Database.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 42bac2df7abe00be8c0e6ffddcc9bef7ef28ba9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93309516"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Gegevens voor Azure Machine Learning verplaatsen naar een Azure SQL-database

In dit artikel vindt u een overzicht van de opties voor het verplaatsen van gegevens uit platte bestanden (CSV-of TSV-indeling) of van gegevens die zijn opgeslagen in SQL Server naar een Azure SQL Database. Deze taken voor het verplaatsen van gegevens naar de Cloud maken deel uit van het team data Science process.

Zie [gegevens verplaatsen naar SQL Server op een virtuele machine van Azure](move-sql-server-virtual-machine.md)voor een onderwerp waarin de opties worden beschreven voor het verplaatsen van gegevens naar SQL Server voor machine learning.

De volgende tabel bevat een overzicht van de opties voor het verplaatsen van gegevens naar een Azure SQL Database.

| <b>Bron</b> | <b>DOEL: Azure SQL Database</b> |
| --- | --- |
| <b>Plat bestand (CSV of TSV Format teren)</b> |[SQL-query bulksgewijs invoegen](#bulk-insert-sql-query) |
| <b>On-premises SQL Server</b> |1.[exporteren naar een plat bestand](#export-flat-file)<br> 2. [SQL database wizard Migratie](#insert-tables-bcp)<br> 3. [back-up en herstel van data base](#db-migration)<br> 4. [Azure Data Factory](#adf) |

## <a name="prerequisites"></a><a name="prereqs"></a>Vereisten
Voor de procedures die hier worden beschreven, is het volgende vereist:

* Een **Azure-abonnement**. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/pricing/free-trial/).
* Een **Azure-opslag account**. U gebruikt een Azure-opslag account voor het opslaan van de gegevens in deze zelf studie. Zie het artikel [Een opslagaccount maken](../../storage/common/storage-account-create.md) als u geen account Azure-opslagaccount hebt. Nadat u het opslagaccount hebt gemaakt, moet u de accountsleutel ophalen die wordt gebruikt voor toegang tot de opslag. Zie [toegangs sleutels voor opslag accounts beheren](../../storage/common/storage-account-keys-manage.md).
* Toegang tot een **Azure SQL database**. Als u een Azure SQL Database moet instellen, kunt u aan de [slag met Microsoft Azure SQL database](../../azure-sql/database/single-database-create-quickstart.md) informatie over het inrichten van een nieuw exemplaar van een Azure SQL database.
* **Azure PowerShell** lokaal geïnstalleerd en geconfigureerd. Zie [Azure PowerShell installeren en configureren](/powershell/azure/)voor instructies.

**Gegevens**: de migratie processen worden gedemonstreerd met behulp van de [NYC taxi-gegevensset](https://chriswhong.com/open-data/foil_nyc_taxi/). De NYC taxi-gegevensset bevat informatie over reis gegevens en beurzen en is beschikbaar in Azure Blob-opslag: [NYC taxi-gegevens](https://www.andresmh.com/nyctaxitrips/). Een voor beeld en een beschrijving van deze bestanden zijn te vinden in de beschrijving van de [NYC taxi trips](sql-walkthrough.md#dataset)van de verzameling.

U kunt de procedures die hier worden beschreven, aanpassen aan een set met uw eigen gegevens of de stappen volgen die worden beschreven met behulp van de NYC taxi-gegevensset. Als u de NYC taxi-gegevensset wilt uploaden naar uw SQL Server-Data Base, volgt u de procedure die wordt beschreven in [gegevens bulksgewijs importeren in SQL Server-Data Base](sql-walkthrough.md#dbload).

## <a name="moving-data-from-a-flat-file-source-to-an-azure-sql-database"></a><a name="file-to-azure-sql-database"></a> Gegevens verplaatsen van een bron met een plat bestand naar een Azure SQL Database
Gegevens in platte bestanden (CSV of TSV) kunnen worden verplaatst naar een Azure SQL Database met behulp van een Bulk Insert SQL-query.

### <a name="bulk-insert-sql-query"></a><a name="bulk-insert-sql-query"></a> SQL-query bulksgewijs invoegen
De stappen voor de procedure met behulp van de bulksgewijze insert SQL-query zijn vergelijkbaar met de instructies voor het verplaatsen van gegevens van een platte bestands bron naar SQL Server op een virtuele machine van Azure. Zie [BULKSGEWIJZE SQL-query invoegen](move-sql-server-virtual-machine.md#insert-tables-bulkquery)voor meer informatie.

## <a name="moving-data-from-sql-server-to-an-azure-sql-database"></a><a name="sql-on-prem-to-sazure-sql-database"></a> Gegevens verplaatsen van SQL Server naar een Azure SQL Database
Als de bron gegevens zijn opgeslagen in SQL Server, zijn er verschillende mogelijkheden om de gegevens naar een Azure SQL Database te verplaatsen:

1. [Exporteren naar plat bestand](#export-flat-file)
2. [Wizard Migratie SQL Database](#insert-tables-bcp)
3. [Back-up en herstel van data base](#db-migration)
4. [Azure Data Factory](#adf)

De stappen voor de eerste drie zijn vergelijkbaar met die secties in [gegevens verplaatsen naar SQL Server op een virtuele machine van Azure](move-sql-server-virtual-machine.md) die dezelfde procedures beslaan. Koppelingen naar de relevante secties in dit onderwerp vindt u in de volgende instructies.

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Exporteren naar plat bestand
De stappen voor het exporteren naar een plat bestand zijn vergelijkbaar met die voor het [exporteren naar een plat bestand](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="sql-database-migration-wizard"></a><a name="insert-tables-bcp"></a>Wizard Migratie SQL Database
De stappen voor het gebruik van de wizard SQL Database Migration zijn vergelijkbaar met die van de instructies in de [wizard SQL database migratie](move-sql-server-virtual-machine.md#sql-migration).

### <a name="database-back-up-and-restore"></a><a name="db-migration"></a>Back-up en herstel van data base
De stappen voor het gebruik van back-up en herstel van de Data Base zijn vergelijkbaar met die in de [back-up en herstel bewerking van de data base](move-sql-server-virtual-machine.md#sql-backup).

### <a name="azure-data-factory"></a><a name="adf"></a>Azure Data Factory
Informatie over het verplaatsen van gegevens naar een Azure SQL Database met Azure Data Factory (ADF) in dit onderwerp kunt u [gegevens verplaatsen van een SQL Server naar SQL Azure met Azure Data Factory](move-sql-azure-adf.md). In dit onderwerp wordt beschreven hoe u ADF gebruikt om gegevens van een SQL Server Data Base te verplaatsen naar een Azure SQL Database via Azure Blob Storage.

U kunt ADF gebruiken wanneer gegevens voortdurend moeten worden gemigreerd met hybride on-premises en Cloud bronnen.  ADF helpt ook wanneer de gegevens trans formaties nodig hebben of nieuwe bedrijfs logica nodig heeft tijdens de migratie. Met ADF kunnen taken worden gepland en gecontroleerd met behulp van eenvoudige JSON-scripts waarmee de verplaatsing van gegevens periodiek wordt beheerd. ADF heeft ook andere mogelijkheden, zoals ondersteuning voor complexe bewerkingen.
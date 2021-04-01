---
title: Gegevens laden in Azure Storage omgevingen-team data Science process
description: Meer informatie over het opnemen van gegevens in verschillende doel omgevingen waarin de gegevens worden opgeslagen en verwerkt.
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
ms.openlocfilehash: 46e911ad6bdf5a478d46e425f8700740ece56c6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96452656"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Gegevens voor analysedoeleinden in opslagomgevingen laden

Voor het proces van de team data Science moeten gegevens in elke fase op de meest geschikte manier worden opgenomen of geladen. Gegevens bestemmingen kunnen Azure Blob Storage, SQL Azure-data bases, SQL Server op Azure VM, HDInsight (Hadoop), Azure Synapse Analytics en Azure Machine Learning zijn. 

In de volgende artikelen wordt beschreven hoe u gegevens opneemt in verschillende doel omgevingen waarin de gegevens worden opgeslagen en verwerkt.

* Naar/van [Azure Blob Storage](move-azure-blob.md)
* [SQL Server op Azure VM](move-sql-server-virtual-machine.md)
* [Azure SQL database](move-sql-azure.md)
* In [Hive-tabellen](move-hive-tables.md)
* Naar [SQL-gepartitioneerde tabellen](parallel-load-sql-partitioned-tables.md)
* Van [on-premises SQL Server](move-sql-azure-adf.md)

Technische en bedrijfs behoeften, evenals de oorspronkelijke locatie, indeling en grootte van uw gegevens bepalen het beste plan voor gegevens opname. Het is niet ongebruikelijk dat een beste plan is om verschillende stappen uit te voeren. Deze reeks taken kan bijvoorbeeld betrekking hebben op het verkennen van gegevens, het vooraf verwerken, schoonmaken, bemonsteren en model trainingen.  Azure Data Factory is een aanbevolen Azure-resource voor het organiseren van gegevens verplaatsing en-trans formatie.

---
title: Blob Storage-gegevens verplaatsen met SSIS-connectors-team data Science process
description: Meer informatie over het verplaatsen van gegevens naar of van Azure Blob Storage met SQL Server Integration Services Feature Pack voor Azure.
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
ms.openlocfilehash: 9572d612e7ee8e2fd72850ba14447e8449f0f371
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321999"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Gegevens verplaatsen van of naar Azure Blob Storage met SSIS-connectors
Het [SQL Server Integration Services Feature Pack voor Azure](/sql/integration-services/azure-feature-pack-for-integration-services-ssis) biedt onderdelen om verbinding te maken met Azure, gegevens over te dragen tussen Azure-en on-premises gegevens bronnen en gegevens die zijn opgeslagen in azure te verwerken.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Zodra klanten on-premises gegevens naar de Cloud hebben verplaatst, hebben ze toegang tot hun gegevens vanuit elke Azure-service om gebruik te kunnen maken van de volledige kracht van de Azure-technologieën. De gegevens kunnen vervolgens worden gebruikt, bijvoorbeeld in Azure Machine Learning of op een HDInsight-cluster.

Voor beelden van het gebruik van deze Azure-resources zijn te vinden in de [SQL](sql-walkthrough.md) -en [HDInsight](hive-walkthrough.md) -scenario's.

Zie [meer met SQL Server Integration Services Feature Pack voor Azure](https://techcommunity.microsoft.com/t5/sql-server-integration-services/doing-more-with-sql-server-integration-services-feature-pack-for/ba-p/388238) blog voor een bespreking van canonieke scenario's die gebruikmaken van SSIS om bedrijfs behoeften te bereiken die gemeen schappelijk zijn voor hybride gegevens integratie scenario's.

> [!NOTE]
> Raadpleeg Azure Blob- [basis beginselen](../../storage/blobs/storage-quickstart-blobs-dotnet.md) en [Azure Blob service](/rest/api/storageservices/Blob-Service-Concepts)voor een volledige inleiding tot Azure Blob-opslag.
> 
> 

## <a name="prerequisites"></a>Vereisten
Als u de taken wilt uitvoeren die in dit artikel worden beschreven, moet u een Azure-abonnement en een Azure Storage-account instellen. U hebt de Azure Storage-account naam en de account sleutel nodig om gegevens te uploaden of te downloaden.

* Zie [een gratis proef versie van één maand](https://azure.microsoft.com/pricing/free-trial/)voor meer informatie over het instellen van een **Azure-abonnement**.
* Zie [over Azure Storage-accounts](../../storage/common/storage-account-create.md)voor instructies over het maken van een **opslag account** en voor het ophalen van account-en sleutel gegevens.

Als u de **SSIS-connectors** wilt gebruiken, moet u het volgende downloaden:

* **SQL Server 2014 of 2016 Standard (of hoger)**: de installatie bevat SQL Server Integration Services.
* **Microsoft SQL Server 2014 of 2016 Integration Services Feature Pack voor Azure**: deze Connectors kunnen respectievelijk worden gedownload via de pagina's [SQL Server 2014 integration services](https://www.microsoft.com/download/details.aspx?id=47366) en [SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492) .

> [!NOTE]
> SSIS wordt geïnstalleerd met SQL Server, maar is niet opgenomen in de snelle versie. Zie [SQL Server-edities](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/) voor meer informatie over welke toepassingen zijn opgenomen in de verschillende edities van SQL Server.
> 
> 

Voor trainings materiaal op SSIS, Zie [handen voor training voor SSIS](https://www.microsoft.com/sql-server/training-certification)

Zie [SSIS-zelf studie: een eenvoudig ETL-pakket maken](/sql/integration-services/ssis-how-to-create-an-etl-package)voor informatie over het ophalen van en het uitvoeren van SISS voor het bouwen van eenvoudige pakketten voor het uitpakken, transformeren en laden (ETL).

## <a name="download-nyc-taxi-dataset"></a>NYC taxi-gegevensset downloaden
In het voor beeld dat hier wordt beschreven, wordt een openbaar beschik bare gegevensset gebruikt: de NYC van de [taxi trips](https://www.andresmh.com/nyctaxitrips/) . De gegevensset bestaat uit ongeveer 173.000.000 taxi-onderdrukkingen in NYC van het jaar 2013. Er zijn twee soorten gegevens: reis gegevens en gegevens over ritbedrag. Omdat er voor elke maand een bestand is, zijn er 24 bestanden, die elk ongeveer 2 GB ongecomprimeerd zijn.

## <a name="upload-data-to-azure-blob-storage"></a>Gegevens uploaden naar Azure Blob Storage
Als u gegevens wilt verplaatsen met behulp van het SSIS-functie pakket van on-premises naar Azure Blob-opslag, wordt een exemplaar van de [**Azure Blob upload-taak**](/sql/integration-services/control-flow/azure-blob-upload-task)gebruikt, zoals hier wordt weer gegeven:

![configure-data-Science-VM](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

De para meters die de taak gebruikt, worden hier beschreven:

| Veld | Description |
| --- | --- |
| **AzureStorageConnection** |Hiermee geeft u een bestaande Azure Storage verbindings beheer of maakt u een nieuwe die verwijst naar een Azure Storage-account waarnaar wordt verwezen naar de host van de BLOB-bestanden. |
| **Zijn** |Hiermee geeft u de naam op van de BLOB-container die de geüploade bestanden als blobs bevat. |
| **BlobDirectory** |Hiermee geeft u de BLOB-map op waarin het geüploade bestand wordt opgeslagen als een blok-blob. De BLOB-map is een virtuele hiërarchische structuur. Als de BLOB al bestaat, wordt de IA vervangen. |
| **LocalDirectory** |Hiermee geeft u de lokale map met de bestanden die moeten worden geüpload. |
| **Bestands** |Hiermee geeft u een naam filter op waarmee bestanden met het opgegeven naam patroon worden geselecteerd. MySheet \* . xls \* bevat bijvoorbeeld bestanden zoals MySheet001.xls en MySheetABC.xlsx |
| **TimeRangeFrom/TimeRangeTo** |Hiermee geeft u een tijds bereik filter op. Bestanden die zijn gewijzigd na *TimeRangeFrom* en voordat *TimeRangeTo* zijn opgenomen. |

> [!NOTE]
> De **AzureStorageConnection** -referenties moeten juist zijn en de **zijn** moeten bestaan voordat de overdracht wordt uitgevoerd.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Gegevens downloaden van Azure Blob-opslag
Als u gegevens van Azure Blob-opslag naar on-premises opslag met SSIS wilt downloaden, gebruikt u een exemplaar van de [Azure Blob-Download taak](/sql/integration-services/control-flow/azure-blob-download-task).

## <a name="more-advanced-ssis-azure-scenarios"></a>Meer geavanceerde SSIS-Azure scenario's
Met het SSIS Feature Pack kunnen complexere stromen worden verwerkt door taken samen te pakken. De BLOB-gegevens kunnen bijvoorbeeld rechtstreeks in een HDInsight-cluster worden gefeedd waarvan de uitvoer kan worden gedownload naar een BLOB en vervolgens naar de on-premises opslag. SSIS kan Hive-en Pig-taken uitvoeren op een HDInsight-cluster met extra SSIS-connectors:

* Als u een Hive-script op een Azure HDInsight-cluster met SSIS wilt uitvoeren, gebruikt u de [component taak van Azure hdinsight](/sql/integration-services/control-flow/azure-hdinsight-hive-task).
* Als u een Pig-script wilt uitvoeren op een Azure HDInsight-cluster met SSIS, gebruikt u de [Azure Hdinsight Pig-taak](/sql/integration-services/control-flow/azure-hdinsight-pig-task).
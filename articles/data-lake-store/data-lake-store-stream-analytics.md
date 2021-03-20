---
title: Gegevens streamen van Stream Analytics naar Data Lake Storage Gen1-Azure
description: Informatie over het gebruik van Azure Data Lake Storage Gen1 als uitvoer voor een Azure Stream Analytics-taak, met een eenvoudig scenario waarmee gegevens uit een Azure Storage-BLOB worden gelezen.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/30/2018
ms.author: twooley
ms.openlocfilehash: 4c289ecb1d8471a7b99f1d4c85a0163de4d0c593
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91576214"
---
# <a name="stream-data-from-azure-storage-blob-into-azure-data-lake-storage-gen1-using-azure-stream-analytics"></a>Gegevens streamen van Azure Storage Blob naar Azure Data Lake Storage Gen1 met behulp van Azure Stream Analytics
In dit artikel leert u hoe u Azure Data Lake Storage Gen1 kunt gebruiken als uitvoer voor een Azure Stream Analytics taak. In dit artikel wordt een eenvoudig scenario gedemonstreerd waarmee gegevens uit een Azure Storage BLOB (invoer) worden gelezen en de gegevens naar Data Lake Storage Gen1 worden geschreven (uitvoer).

## <a name="prerequisites"></a>Vereisten
Voordat u met deze zelfstudie begint, moet u het volgende hebben of hebben gedaan:

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* **Een Azure Storage-account**. U gebruikt een BLOB-container van dit account om gegevens in te voeren voor een Stream Analytics taak. Voor deze zelf studie wordt ervan uitgegaan dat u een opslag account hebt met de naam **storageforasa** en een container in het account met de naam **storageforasacontainer**. Wanneer u de container hebt gemaakt, uploadt u een voorbeeld gegevensbestand. 
  
* **Een Data Lake Storage gen1-account**. Volg de instructies in aan [de slag met Azure data Lake Storage gen1 met behulp van de Azure Portal](data-lake-store-get-started-portal.md). We gaan ervan uit dat u een Data Lake Storage Gen1 account hebt met de naam **myadlsg1**. 

## <a name="create-a-stream-analytics-job"></a>Een Stream Analytics-taak maken
U begint met het maken van een Stream Analytics-taak die een invoer bron en een uitvoer bestemming bevat. Voor deze zelf studie is de bron een Azure Blob-container en is de bestemming Data Lake Storage Gen1.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Klik in het linkerdeel venster op **Stream Analytics taken** en klik vervolgens op **toevoegen**.

    ![Een Stream Analytics-taak maken](./media/data-lake-store-stream-analytics/create.job.png "Een Stream Analytics-taak maken")

    > [!NOTE]
    > Zorg ervoor dat u een taak maakt in dezelfde regio als het opslag account of dat er extra kosten in rekening worden gebracht voor het verplaatsen van gegevens tussen regio's.
    >

## <a name="create-a-blob-input-for-the-job"></a>Een BLOB-invoer voor de taak maken

1. Open de pagina voor de Stream Analytics taak, klik in het linkerdeel venster op het tabblad **invoer** en klik vervolgens op **toevoegen**.

    ![Scherm opname van de Blade Stream Analytics taak met de optie invoer en de optie stream-invoer toevoegen.](./media/data-lake-store-stream-analytics/create.input.1.png "Een invoer toevoegen aan uw taak")

2. Geef op de Blade **nieuwe invoer** de volgende waarden op.

    ![Scherm afbeelding van de Blade Blob Storage-nieuwe invoer.](./media/data-lake-store-stream-analytics/create.input.2.png "Een invoer toevoegen aan uw taak")

   * Voer bij **invoer alias** een unieke naam in voor de taak invoer.
   * Selecteer **gegevens stroom** bij **bron type**.
   * Voor **bron**, selecteer **Blob Storage**.
   * Selecteer voor **abonnement** de optie **Blob-opslag gebruiken uit het huidige abonnement**.
   * Selecteer bij **opslag account** het opslag account dat u hebt gemaakt als onderdeel van de vereisten. 
   * Selecteer voor **container** de container die u hebt gemaakt in het geselecteerde opslag account.
   * Selecteer **CSV** voor **gebeurtenis serialisatie-indeling**.
   * Selecteer **Tab** voor **scheidings teken**.
   * Voor **code ring** selecteert u **UTF-8**.

     Klik op **Create**. De Portal voegt nu de invoer toe en test de verbinding.


## <a name="create-a-data-lake-storage-gen1-output-for-the-job"></a>Een Data Lake Storage Gen1 uitvoer voor de taak maken

1. Open de pagina voor de taak Stream Analytics, klik op het tabblad **uitvoer** , klik op **toevoegen** en selecteer **Data Lake Storage gen1**.

    ![Scherm opname van de Blade Stream Analytics taak met de optie outputs, de optie toevoegen en de optie Data Lake Storage gen 1 genoemd.](./media/data-lake-store-stream-analytics/create.output.1.png "Een uitvoer toevoegen aan uw taak")

2. Geef de volgende waarden op de Blade **nieuwe uitvoer** op.

    ![Scherm afbeelding van de Blade Data Lake Storage gen 1-nieuwe uitvoer met de optie voor autoriseren.](./media/data-lake-store-stream-analytics/create.output.2.png "Een uitvoer toevoegen aan uw taak")

    * Voer voor **uitvoer alias** een unieke naam in voor de taak uitvoer. Dit is een beschrijvende naam die wordt gebruikt in query's om de uitvoer van de query naar dit Data Lake Storage Gen1-account te sturen.
    * U wordt gevraagd toegang tot het Data Lake Storage Gen1-account te verlenen. Klik op **autoriseren**.

3. Ga naar de Blade **nieuwe uitvoer** en voer de volgende waarden in.

    ![Scherm afbeelding van de Blade Data Lake Storage gen 1-nieuwe uitvoer.](./media/data-lake-store-stream-analytics/create.output.3.png "Een uitvoer toevoegen aan uw taak")

   * Selecteer bij **account naam** het data Lake Storage gen1 account dat u al hebt gemaakt op de locatie waar u de taak uitvoer naar wilt verzenden.
   * Voer voor **patroon voor voegsel van pad** een bestandspad in dat wordt gebruikt voor het schrijven van bestanden binnen het opgegeven Data Lake Storage gen1-account.
   * Als u in het pad voor het voor voegsel een datum token hebt gebruikt, kunt u voor **datum notatie** de datum notatie selecteren waarin uw bestanden zijn geordend.
   * Als u voor **tijd notatie** een tijd token in het pad voor voegsel hebt gebruikt, geeft u de tijd notatie op waarin uw bestanden zijn geordend.
   * Selecteer **CSV** voor **gebeurtenis serialisatie-indeling**.
   * Selecteer **Tab** voor **scheidings teken**.
   * Voor **code ring** selecteert u **UTF-8**.
    
     Klik op **Create**. De Portal voegt nu de uitvoer toe en test de verbinding.
    
## <a name="run-the-stream-analytics-job"></a>De Stream Analytics-taak uitvoeren

1. Als u een Stream Analytics-taak wilt uitvoeren, moet u een query uitvoeren op het tabblad **query** . Voor deze zelf studie kunt u de voorbeeld query uitvoeren door de tijdelijke aanduidingen te vervangen door de invoer-en uitvoer aliassen van de taak, zoals wordt weer gegeven in de scherm opname hieronder.

    ![Query uitvoeren](./media/data-lake-store-stream-analytics/run.query.png "Query uitvoeren")

2. Klik op **Opslaan** boven aan het scherm en klik vervolgens op het tabblad **overzicht** op **starten**. Selecteer in het dialoog venster **aangepaste tijd** en stel vervolgens de huidige datum en tijd in.

    ![Taak tijd instellen](./media/data-lake-store-stream-analytics/run.query.2.png "Taak tijd instellen")

    Klik op **Start** om de taak te starten. Het kan enkele minuten duren om de taak te starten.

3. Als u de taak wilt activeren om de gegevens uit de BLOB te selecteren, kopieert u een voorbeeld gegevensbestand naar de BLOB-container. U kunt een voor beeld van een gegevens bestand ophalen uit de [Azure data Lake Git-opslag plaats](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). Voor deze zelf studie gaan we het bestand kopiëren **vehicle1_09142014.csv**. U kunt verschillende clients, zoals [Azure Storage Explorer](https://storageexplorer.com/), gebruiken om gegevens te uploaden naar een BLOB-container.

4. Ga naar het tabblad **overzicht** en Bekijk hoe de gegevens zijn verwerkt, onder **bewaking**.

    ![Taak controleren](./media/data-lake-store-stream-analytics/run.query.3.png "Taak controleren")

5. Ten slotte kunt u controleren of de taak uitvoer gegevens beschikbaar zijn in het Data Lake Storage Gen1-account. 

    ![Uitvoer controleren](./media/data-lake-store-stream-analytics/run.query.4.png "Uitvoer controleren")

    In het deel venster Data Explorer ziet u dat de uitvoer wordt geschreven naar een mappad zoals opgegeven in de Data Lake Storage Gen1 Output Settings ( `streamanalytics/job/output/{date}/{time}` ).  

## <a name="see-also"></a>Zie ook
* [Een HDInsight-cluster maken voor het gebruik van Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

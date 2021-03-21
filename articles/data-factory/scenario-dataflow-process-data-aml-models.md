---
title: Gegevens stromen gebruiken om gegevens van modellen van geautomatiseerde machine learning (AutoML) te verwerken
description: Meer informatie over het gebruik van Azure Data Factory gegevens stromen voor het verwerken van gegevens uit geautomatiseerd machine learning modellen (AutoML).
services: data-factory
author: amberz
co-author: ATLArcht
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 1/31/2021
ms.author: amberz
ms.co-author: Donnana
ms.openlocfilehash: 45cd44cc0678b7f3a006a88bf66be2bca091af76
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595376"
---
# <a name="process-data-from-automated-machine-learning-models-by-using-data-flows"></a>Gegevens verwerken van geautomatiseerde machine learning modellen met behulp van gegevens stromen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Automatische machine learning (AutoML) wordt door machine learning projecten aangenomen om de beste modellen automatisch te trainen, af te stemmen en te krijgen met behulp van doel metrieken die u opgeeft voor classificatie, regressie en prognose van time-series.

Een uitdaging voor AutoML is dat onbewerkte gegevens uit een Data Warehouse of een transactionele Data Base een enorme gegevensset zijn, mogelijk 10 GB. Een grote gegevensset vereist een langere periode voor het trainen van modellen, daarom raden wij aan dat u de gegevens verwerking optimaliseert voordat u Azure Machine Learning modellen traint. Deze zelf studie gaat over het gebruik van Azure Data Factory om een gegevensset te partitioneren in AutoML-bestanden voor een Machine Learning-gegevensset.

Het AutoML-project bevat de volgende drie scenario's voor het verwerken van gegevens:

* Partitioneer grote gegevens naar AutoML-bestanden voordat u modellen traint.

     Het [gegevens frame Panda](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) wordt vaak gebruikt voor het verwerken van gegevens voordat u modellen traint. Het gegevens frame van Pandas werkt goed voor gegevens groottes die kleiner zijn dan 1 GB, maar als de gegevens groter zijn dan 1 GB, vertraagt een Panda data-frame het proces van gegevens. Soms kan zelfs een fout bericht over onvoldoende geheugen worden weer gegeven. U kunt het beste een [Parquet-bestands](https://parquet.apache.org/) indeling gebruiken voor machine learning, omdat het een binaire kolom indeling is.
    
     Data Factory gegevens stromen zijn visueel ontworpen gegevens transformaties waarmee gegevens engineers geen code kunnen schrijven. Het toewijzen van gegevens stromen is een krachtige manier om grote gegevens te verwerken omdat de pijp lijn uitgebreide Spark-clusters gebruikt.

* Splits de trainings gegevensset en de test gegevensset.
    
    De trainings gegevensset wordt gebruikt voor een trainings model. De test-gegevensset wordt gebruikt om modellen in een machine learning project te evalueren. Met de voorwaardelijke Splits activiteit voor het toewijzen van gegevens stromen worden trainings gegevens en test gegevens gesplitst.

* Niet-gekwalificeerde gegevens verwijderen.

    Mogelijk wilt u niet-gekwalificeerde gegevens verwijderen, zoals een Parquet-bestand met nul rijen. In deze zelf studie gebruiken we de cumulatieve activiteit om een telling van het aantal rijen op te halen. Het aantal rijen is een voor waarde voor het verwijderen van niet-gekwalificeerde gegevens.

## <a name="preparation"></a>Voorbereiding

Gebruik de volgende Azure SQL Database-tabel.

```
CREATE TABLE [dbo].[MyProducts](
    [ID] [int] NULL,
    [Col1] [char](124) NULL,
    [Col2] [char](124) NULL,
    [Col3] datetime NULL,
    [Col4] int NULL

) 

```

## <a name="convert-data-format-to-parquet"></a>Gegevens indeling converteren naar Parquet

Met de volgende gegevens stroom wordt een SQL Database tabel geconverteerd naar een Parquet-bestands indeling:

- **Bron-gegevensset**: transactie tabel van SQL database.
- **Sink-gegevensset**: Blob Storage met Parquet-indeling.

## <a name="remove-unqualified-data-based-on-row-count"></a>Niet-gekwalificeerde gegevens verwijderen op basis van aantal rijen

Stel dat er een aantal rijen moet worden verwijderd dat kleiner is dan twee.

1. Gebruik de cumulatieve activiteit om een telling van het aantal rijen op te halen. Gebruik **gegroepeerd** op op basis van col2 en **aggregaties** met `count(1)` voor het aantal rijen.

    ![Scherm opname van de configuratie van de cumulatieve activiteit om een telling van het aantal rijen op te halen.](./media/scenario-dataflow-process-data-aml-models/aggregate-activity-addrowcount.png)

1. Gebruik de Sink-activiteit om het **sink-type** als **cache** op het tabblad **sink** te selecteren. Selecteer vervolgens de gewenste kolom in de vervolg keuzelijst **sleutel kolommen** op het tabblad **instellingen** .

    ![Scherm opname van de configuratie van de CacheSink-activiteit om een telling van het aantal rijen in een in cache gefilterde Sink op te halen.](./media/scenario-dataflow-process-data-aml-models/cachesink-activity-addrowcount.png)

1. Gebruik de afgeleide kolom activiteit om een kolom met het aantal rijen in de bron stroom toe te voegen. Op het tabblad instellingen van de **afgeleide kolom** gebruikt `CacheSink#lookup` u de expressie om een aantal rijen op te halen uit CacheSink.

    ![Scherm opname van de configuratie van de afgeleide kolom activiteit om een telling van het aantal rijen in source1 toe te voegen.](./media/scenario-dataflow-process-data-aml-models/derived-column-activity-rowcount-source-1.png)

1. Gebruik de activiteit voorwaardelijke splitsing om niet-gekwalificeerde gegevens te verwijderen. In dit voor beeld is het aantal rijen gebaseerd op de kolom col2. De voor waarde is om een aantal rijen kleiner dan twee te verwijderen, zodat er twee (ID = 2 en ID = 7) worden verwijderd. U kunt niet-gekwalificeerde gegevens opslaan in Blob Storage voor gegevens beheer.

    ![Scherm opname van de configuratie van de voorwaardelijke Splits activiteit voor het ophalen van gegevens die groter zijn dan of gelijk zijn aan twee.](./media/scenario-dataflow-process-data-aml-models/conditionalsplit-greater-or-equal-than-2.png)

> [!NOTE]
>    * Maak een nieuwe bron voor het ophalen van een telling van het aantal rijen dat in latere stappen wordt gebruikt in de oorspronkelijke bron.
>    * Gebruik CacheSink uit het oogpunt van prestaties.

## <a name="split-training-data-and-test-data"></a>Trainings gegevens en test gegevens splitsen

We willen de trainings gegevens en test gegevens voor elke partitie splitsen. In dit voor beeld worden voor dezelfde waarde van col2 de bovenste twee rijen als test gegevens en de rest van de rijen als trainings gegevens opgehaald.

1. Gebruik de activiteit venster om één kolom rijnummer voor elke partitie toe te voegen. Selecteer op het tabblad **over** een kolom voor de partitie. In deze zelf studie wordt gepartitioneerd voor col2. Geef een volg orde op het tabblad **sorteren** , die in deze zelf studie wordt gebaseerd op de id. Geef een volg orde op het tabblad **venster kolommen** om één kolom toe te voegen als rijnummer voor elke partitie.

    ![Scherm afbeelding van de configuratie van de venster activiteit om één nieuwe kolom toe te voegen aan het rijnummer.](./media/scenario-dataflow-process-data-aml-models/window-activity-add-row-number.png)

1. Gebruik de activiteit voorwaardelijke splitsing om de bovenste twee rijen van elke partitie te splitsen in de gegevensset voor de test en de rest van de rijen in de gegevensset van de training. Gebruik op het tabblad **instellingen voor voorwaardelijke splitsing** de expressie `lesserOrEqual(RowNum,2)` als voor waarde.

    ![Scherm opname van de configuratie van de voorwaardelijke Splits activiteit voor het splitsen van de huidige gegevensset in de gegevensset van de training en de test gegevensset.](./media/scenario-dataflow-process-data-aml-models/split-training-dataset-test-dataset.png)

## <a name="partition-the-training-and-test-datasets-with-parquet-format"></a>De trainings-en test gegevens sets partitioneren met de Parquet-indeling

Gebruik de Sink-activiteit, op het tabblad **Optimize** , de **unieke waarde per partitie** om een kolom in te stellen als een kolom sleutel voor de partitie.

![Scherm opname van de configuratie van de Sink-activiteit om de partitie van de trainings gegevensset in te stellen.](./media/scenario-dataflow-process-data-aml-models/partition-training-dataset-sink.png)

Laten we eens kijken naar de volledige pijp lijn logica.

![Scherm opname van de logica van de volledige pijp lijn.](./media/scenario-dataflow-process-data-aml-models/entire-pipeline.png)

## <a name="next-steps"></a>Volgende stappen

Bouw de rest van uw gegevensstroom logica door [trans formaties](concepts-data-flow-overview.md)voor gegevens stromen toe te wijzen.

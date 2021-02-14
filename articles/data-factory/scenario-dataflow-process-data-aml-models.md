---
title: Gegevensfeed gebruiken voor het verwerken van gegevens uit geautomatiseerde machine learning modellen (AutoML)
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
ms.openlocfilehash: 494fc2c227b6fe855e96a780d43cc6702722fa94
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520929"
---
# <a name="process-data-from-automated-machine-learningautoml-models-using-data-flow"></a>Gegevens verwerken uit geautomatiseerde machine learning modellen (AutoML) met behulp van de gegevens stroom

Automatische machine learning (AutoML) wordt door machine learning projecten aangenomen om automatisch het beste model te trainen, af te stemmen en te krijgen met behulp van de doel metriek die u opgeeft voor classificatie, regressie en prognose van time-series. 

Eén uitdaging is onbewerkte gegevens uit data warehouse of transactionele data base zou een enorme gegevensset zijn, zoals: 10 GB. de grote gegevensset vereist meer tijd om modellen te trainen. optimalisatie van gegevens wordt daarom aanbevolen voordat Azure Machine Learning modellen worden getraind. In deze zelf studie leert u hoe u ADF kunt gebruiken om gegevensset te partitioneren naar Parquet-bestanden voor Azure Machine Learning-gegevensset. 

In AutoML-project (Automated machine learning) worden de volgende drie scenario's voor het verwerken van gegevens toegepast:

* Partitioneer grote gegevens naar Parquet-bestanden vóór trainings modellen. 

     [Panda data frame](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) wordt vaak gebruikt voor het verwerken van gegevens vóór trainings modellen. Het gegevens frame van Pandas werkt goed voor gegevens groottes die kleiner zijn dan 1 GB, maar als de gegevens groot zijn dan 1 GB, wordt het gegevens frame door Panda, zelfs als er geen geheugen meer beschikbaar is voor het verwerken van gegevens. [Parquet-bestands](https://parquet.apache.org/) indelingen worden aanbevolen voor machine learning omdat het een binaire kolom indeling is.
    
    Azure data Factorys waarmee gegevens stromen worden toegewezen, zijn visueel ontworpen gegevens transformaties met gratis code voor data engineers. Het is krachtig om grote gegevens te verwerken omdat de pijp lijn uitgebreide Spark-clusters gebruikt.

* Splits dataset en test gegevensset.
    
    De trainings gegevensset wordt gebruikt voor het trainings model. de test gegevensset wordt gebruikt voor het evalueren van modellen in machine learning project. Bij het toewijzen van gegevens stromen Conditional Split activiteit worden opleidings gegevens en test gegevens gesplitst. 

* Niet-gekwalificeerde gegevens verwijderen.

    Mogelijk wilt u niet-gekwalificeerde gegevens verwijderen, zoals: Parquet-bestand met nul-rij. In deze zelf studie gebruiken we geaggregeerde activiteit voor het tellen van het aantal rijen. het rijnummer is een voor waarde voor het verwijderen van niet-gekwalificeerde gegevens. 


## <a name="preparation"></a>Voorbereiding
Gebruik de volgende tabel met Azure SQL Database. 
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

Met de gegevens stroom wordt een tabel met Azure SQL Database geconverteerd naar een Parquet-bestands indeling. 

**Bron gegevensset**: transactie tabel van Azure SQL database

**Sink-gegevensset**: Blob Storage met Parquet-indeling


## <a name="remove-unqualified-data-based-on-row-count"></a>Niet-gekwalificeerde gegevens verwijderen op basis van aantal rijen

Stel dat het aantal rijen kleiner dan 2 moet worden verwijderd. 

1. Statistische activiteit gebruiken om het totale aantal rijen op te halen: **groeperen** op basis van col2 en **statistische functies** met aantal (1) voor aantal rijen. 

    ![Statistische activiteit configureren om het totale aantal rijen op te halen](./media/scenario-dataflow-process-data-aml-models/aggregate-activity-addrowcount.png)

1. Gebruik Sink-activiteit, kies **sink-type** als cache op **sink** -tabblad en kies vervolgens gewenste kolom uit de vervolg keuzelijst met **sleutel kolommen** op het tabblad **instellingen** . 

    ![CacheSink-activiteit configureren om het aantal rijen in de gecachete Sink op te halen](./media/scenario-dataflow-process-data-aml-models/cachesink-activity-addrowcount.png)

1. Gebruik een afgeleide kolom activiteit om een kolom in het aantal rijen in de bron stroom toe te voegen. In **het tabblad instellingen van de afgeleide kolom** , gebruikt u de opzoek expressie CacheSink # om het aantal rijen op te halen uit SinkCache.
    ![een afgeleide kolom activiteit configureren om het aantal rijen in de bron 1 toe te voegen](./media/scenario-dataflow-process-data-aml-models/derived-column-activity-rowcount-source-1.png)

1. Gebruik voorwaardelijke Splits activiteit om niet-gekwalificeerde gegevens te verwijderen. In dit voor beeld wordt het aantal rijen op basis van de kolom col2, en de voor waarde om het aantal rijen kleiner dan 2 te verwijderen, zodat er twee (ID = 2 en ID = 7) worden verwijderd. U slaat niet-gekwalificeerde gegevens op in een Blob-opslag voor gegevens beheer. 

    ![Voorwaardelijke Splits activiteit configureren om gegevens op te halen die groter zijn dan of gelijk zijn aan 2](./media/scenario-dataflow-process-data-aml-models/conditionalsplit-greater-or-equal-than-2.png)

> [!NOTE]
>    *    Maak een nieuwe bron voor het ophalen van het aantal rijen dat in latere stappen in de oorspronkelijke bron zal worden gebruikt. 
>    *    Gebruik CacheSink uit het oogpunt van prestaties. 

## <a name="split-training-data-and-test-data"></a>Trainings gegevens en test gegevens splitsen 

1. We willen trainings gegevens en test gegevens voor elke partitie splitsen. In dit voor beeld worden voor dezelfde waarde van col2 de bovenste 2 rijen als test gegevens en de rest rijen als trainings gegevens opgehaald. 

    Gebruik venster activiteit om één kolom rijnummer voor elke partitie toe te voegen. Kies op **het tabblad kolom** voor partitie kiezen (in deze zelf studie wordt gepartitioneerd voor col2), geef een volg orde op het tabblad **sorteren** (in deze zelf studie, op basis van de te best Ellen id) en op het tabblad **venster kolommen** om één kolom als rijnummer voor elke partitie toe te voegen. 
    ![Venster activiteit configureren om een nieuwe kolom toe te voegen aan een rijnummer](./media/scenario-dataflow-process-data-aml-models/window-activity-add-row-number.png)

1. Gebruik Conditional Split activiteit om elke partitie bovenste 2 rijen te splitsen om de gegevensset te testen en de rest rijen om de gegevensset te trainen. Gebruik in het tabblad **instellingen voor voorwaardelijke splitsing** de expressie LesserOrEqual (RowNum, 2) als voor waarde. 

    ![Conditional Split activiteit configureren om de huidige gegevensset te splitsen in trainings gegevensset en test gegevensset](./media/scenario-dataflow-process-data-aml-models/split-training-dataset-test-dataset.png)

## <a name="partition-training-dataset-and-test-dataset-with-parquet-format"></a>Trainings-en test gegevensset met Parquet-indeling partitioneren

1. Gebruik Sink-activiteit, in het tabblad **Optimize** , met behulp van **unieke waarde per partitie** om een kolom in te stellen als kolom sleutel voor partitie. 
    ![Sink-activiteit configureren voor het instellen van de partitie van de trainings gegevensset](./media/scenario-dataflow-process-data-aml-models/partition-training-dataset-sink.png)

    We gaan de volledige pijplijn logica bekijken.
    ![De logica van de volledige pijp lijn](./media/scenario-dataflow-process-data-aml-models/entire-pipeline.png)


## <a name="next-steps"></a>Volgende stappen

* Bouw de rest van uw gegevensstroom logica met behulp van trans [formaties](concepts-data-flow-overview.md)voor gegevens stromen.

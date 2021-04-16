---
title: 'Quickstart: Gegevens transformeren met behulp van een toewijzingsgegevensstroom'
description: Deze zelfstudie bevat stapsgewijs instructies voor het gebruik van Azure Synapse Analytics gegevens te transformeren met toewijzingsgegevensstroom.
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/03/2020
ms.openlocfilehash: 37696d2f4054e46125b39f3d5efa794ce54f94b5
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567720"
---
# <a name="quickstart-transform-data-using-mapping-data-flows"></a>Quickstart: Gegevens transformeren met behulp van toewijzingsgegevensstromen

In deze quickstart gebruikt u Azure Synapse Analytics om een pijplijn te maken die gegevens transformeert van een Azure Data Lake Storage Gen2-bron (ADLS Gen2) naar een ADLS Gen2-sink met behulp van toewijzingsgegevensstroom. Het configuratiepatroon in deze quickstart kan worden uitgebreid bij het transformeren van gegevens met behulp van toewijzingsgegevensstroom

In deze snelstart gaat u de volgende stappen volgen:

> [!div class="checklist"]
> * Maak een pijplijn met een Gegevensstroom-activiteit in Azure Synapse Analytics.
> * Bouw een toewijzingsgegevensstroom met vier transformaties.
> * De uitvoering van de pijplijn testen.
> * Een Gegevensstroom bewaken

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* **Azure Synapse:** maak een Synapse-werkruimte met behulp van de Azure Portal de instructies in [Quickstart: Een Synapse-werkruimte maken.](quickstart-create-workspace.md)
* **Azure-opslagaccount:** u gebruikt ADLS-opslag als *bron-* en *sinkgegevensopslag.* Als u geen opslagaccount hebt, raadpleegt u het artikel [Een opslagaccount maken](../storage/common/storage-account-create.md) om een account te maken.

    Het bestand dat we in deze zelfstudie transformeren, is MoviesDB.csv, dat hier te [vinden is.](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv) Als u het bestand wilt ophalen uit GitHub, kopieert u de inhoud naar een teksteditor van uw keuze om het lokaal op te slaan als csv-bestand. Zie Blobs uploaden met de Azure Portal om het bestand naar uw [opslagaccount te Azure Portal.](../storage/blobs/storage-quickstart-blobs-portal.md) De voorbeelden verwijzen naar een container met de naam 'sample-data'.

### <a name="navigate-to-the-synapse-studio"></a>Ga naar Synapse Studio

Wanneer uw Azure Synapse-werkruimte is gemaakt, kunt u Synapse Studio op twee manieren openen:

* Open de Synapse-werkruimte in de [Azure-portal](https://ms.portal.azure.com/#home). Selecteer **Openen** op de kaart 'Open Synapse Studio' onder 'Aan de slag'.
* Open [Azure Synapse Analytics](https://web.azuresynapse.net/) en meld u aan bij uw werkruimte.

In deze quickstart wordt de werkruimte met de naam 'adftest2020' als voorbeeld gebruikt. Er wordt automatisch naar de startpagina van Synapse Studio genavigeerd.

![Startpagina van Synapse Studio](media/doc-common-process/synapse-studio-home.png)

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Een pijplijn maken met een Gegevensstroom activiteit

Een pijplijn bevat de logische stroom voor het uitvoeren van een reeks activiteiten. In deze sectie maakt u een pijplijn die een Gegevensstroom bevat.

1. Ga naar het tabblad **Integreren**. Klik op het pluspictogram naast de kop Pijplijnen en selecteer Pijplijn.

   ![Een nieuwe pijplijn maken](media/doc-common-process/new-pipeline.png)

1. Voer op **de** pagina Eigenschappeninstellingen van de pijplijn **TransformMovies in** als **Naam.**

1. Sleep *gegevensstroom onder Verplaatsen en* transformeren in *het* deelvenster Activiteiten **naar** het pijplijn-canvas.

1. Selecteer in **het pop-uppaginapagina** Gegevensstroom toevoegen de optie **Nieuwe gegevensstroom maken**  ->  **Gegevensstroom.** Klik **op OK** wanneer u klaar bent.

   ![Een gegevensstroom maken](media/quickstart-data-flow/new-data-flow.png)

1. Noem de gegevensstroom **TransformMovies** op de **pagina** Eigenschappen.

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Transformatielogica bouwen in het gegevensstroom-canvas

Zodra u uw Gegevensstroom, wordt u automatisch naar het gegevensstroomvas verzonden. In deze stap bouwt u een gegevensstroom die de MoviesDB.csv in ADLS-opslag gebruikt en de gemiddelde classificatie van comedies van 1910 tot 2000 aggregeert. Vervolgens schrijft u dit bestand terug naar de ADLS-opslag.

1. Schuif boven het canvas van de gegevensstroom de schuifregelaar **Gegevensstroom voor foutopsporing** aan. Met de foutopsporingsmodus kunt u transformatielogica interactief testen op een live Spark-cluster. Gegevensstroom-clusters duurt 5-7 minuten om op te warmen en gebruikers wordt aangeraden om eerst foutopsporing in te schakelen als ze van plan zijn om Gegevensstroom ontwikkelen. Zie Foutopsporingsmodus [voor meer informatie.](../data-factory/concepts-data-flow-debug-mode.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)

    ![Schuif de foutopsporing aan](media/quickstart-data-flow/debug-on.png)

1. Voeg in het canvas van de gegevensstroom een bron toe door op het vak **Bron toevoegen te** klikken.

1. Noem uw bron **MoviesDB.** Klik op **Nieuw om** een nieuwe bronset te maken.

    ![Een nieuwe bronset maken](media/quickstart-data-flow/new-source-dataset.png)

1. Kies **Azure Data Lake Storage Gen2**. Klik op Doorgaan.

    ![Kies Azure Data Lake Storage Gen2](media/quickstart-data-flow/select-source-dataset.png)

1. Kies **DelimitedText.** Klik op Doorgaan.

1. Noem uw **gegevensset MoviesDB.** Kies Nieuw in de **vervolgkeuzekeuze voor de gekoppelde service.**

1. Geef in het scherm voor het maken van de gekoppelde service ADLS Gen2 gekoppelde service **ADLSGen2** op en geef uw verificatiemethode op. Voer vervolgens uw verbindingsreferenties in. In deze quickstart gebruiken we Accountsleutel om verbinding te maken met ons opslagaccount. U kunt op **Verbinding testen klikken** om te controleren of uw referenties correct zijn ingevoerd. Klik op **Maken** als u klaar bent.

    ![Een gekoppelde bronservice maken](media/quickstart-data-flow/adls-gen2-linked-service.png)

1. Wanneer u terug bent in het scherm voor het maken van de gegevensset, voert u **onder** het veld Bestandspad in waar het bestand zich bevindt. In deze quickstart bevindt het bestand 'MoviesDB.csv' zich in de container 'sample-data'. Als het bestand headers bevat, controleert u **Eerste rij als header.** Selecteer **Uit verbinding/archief om** het headerschema rechtstreeks vanuit het bestand in de opslag te importeren. Klik **op OK** wanneer u klaar bent.

    ![Instellingen voor de brongegevensset](media/quickstart-data-flow/source-dataset-properties.png)

1. Als uw foutopsporingscluster is gestart, gaat u naar het tabblad **Gegevensvoorbeeld** van de brontransformatie en klikt u op Vernieuwen **om** een momentopname van de gegevens op te halen. U kunt voorbeeld van gegevens gebruiken om te controleren of uw transformatie correct is geconfigureerd.

    ![Voorbeeld van gegevens](media/quickstart-data-flow/data-preview.png)

1. Klik naast het bron-knooppunt op het gegevensstroomvas op het pluspictogram om een nieuwe transformatie toe te voegen. De eerste transformatie die u toevoegt, is **filter**.

    ![Een filter toevoegen](media/quickstart-data-flow/add-filter.png)

1. Noem de filtertransformatie **FilterYears.** Klik op het expressievak naast **Filteren op om** de opbouwer van de expressie te openen. Hier geeft u de filtervoorwaarde op.

1. Met de opbouwer van gegevensstroomexpressie kunt u interactief expressies bouwen voor gebruik in verschillende transformaties. Expressies kunnen ingebouwde functies, kolommen uit het invoerschema en door de gebruiker gedefinieerde parameters bevatten. Zie voor meer informatie over het bouwen van expressies [Gegevensstroom opbouwer van expressies.](../data-factory/concepts-data-flow-expression-builder.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)

    In deze snelstart wilt u films van genregenres filteren die tussen de jaren 1910 en 2000 zijn uit gekomen. Omdat jaar momenteel een tekenreeks is, moet u deze converteren naar een geheel getal met behulp van de ```toInteger()``` functie . Gebruik de operatoren groter dan of gelijk aan (>=) en kleiner dan of gelijk aan (<=) om de letterlijke jaarwaarden 1910 en 200-te vergelijken. Deze expressies samenbrengen met de operator en (&&). De expressie komt als volgt uit:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Als u wilt weten welke films comedies zijn, kunt u de functie gebruiken om het patroon ```rlike()``` 'Tje' in de kolomkolom te zoeken. Maak de rlike-expressie samen met de jaarvergelijking om het volgende te krijgen:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    ![Filtervoorwaarde opgeven](media/quickstart-data-flow/visual-expression-builder.png)

    Als u een actief foutopsporingscluster hebt, kunt u uw logica controleren door te klikken op Vernieuwen om de uitvoer van de expressie weer te geven in vergelijking met de gebruikte invoer.  Er is meer dan één juist antwoord op hoe u deze logica kunt uitvoeren met behulp van de expressietaal van de gegevensstroom.

    Klik **op Opslaan en voltooien** wanneer u klaar bent met uw expressie.

1. Haal een **voorbeeld van gegevens op** om te controleren of het filter correct werkt.

1. De volgende transformatie die u toevoegt, is een **Aggregatietransformatie** onder **Schema-modifier**.

    ![Een aggregatie toevoegen](media/quickstart-data-flow/add-aggregate.png)

1. Noem de **aggregatietransformatie AggregateComedyRatings.** Selecteer op **het tabblad** Groeperen op de **optie Jaar** in de vervolgkeuze lijst om de aggregaties te groeperen op het jaar dat de film is uit gekomen.

    ![Aggregatie-instellingen 1](media/quickstart-data-flow/aggregate-settings.png)

1. Ga naar het **tabblad Aggregates.** Noem in het linkertekstvak de aggregatiekolom **AverageComedyRating.** Klik op het vak met de rechterexpressie om de samengetagde expressie in te voeren via de opbouwer van de expressie.

    ![Aggregatie-instellingen 2](media/quickstart-data-flow/aggregate-settings-2.png)

1. Gebruik de statistische functie om het gemiddelde **van kolom Waardering** op te ```avg()``` halen. Omdat **Waardering** een tekenreeks is en numerieke invoer op zich neemt, moeten we de waarde converteren ```avg()``` naar een getal via de functie ```toInteger()``` . Deze expressie ziet er als volgende uit:

    ```avg(toInteger(Rating))```

    Klik **op Opslaan en voltooien wanneer** u klaar bent.

    ![Gemiddelde waardering voor de waardering van een waardering voor de waardering](media/quickstart-data-flow/average-comedy-rating.png)

1. Ga naar het **tabblad Gegevensvoorbeeld** om de transformatie-uitvoer weer te geven. U ziet dat er slechts twee kolommen zijn: **year** en **AverageComedyRating.**

    ![Preview van cumulatief gegevens](media/quickstart-data-flow/transformation-output.png)

1. Vervolgens wilt u een **Sink-transformatie** toevoegen onder **Destination**.

    ![Een sink toevoegen](media/quickstart-data-flow/add-sink.png)

1. Noem uw sink **Sink.** Klik **op Nieuw om** uw sink-gegevensset te maken.

1. Kies **Azure Data Lake Storage Gen2**. Klik op Doorgaan.

1. Kies **DelimitedText.** Klik op Doorgaan.

1. Noem uw **sink-gegevensset MoviesSink.** Kies voor gekoppelde service de ADLS Gen2 service die u in stap 7 hebt gemaakt. Voer een uitvoermap in om uw gegevens naar te schrijven. In deze quickstart schrijven we naar de map 'output' in container 'sample-data'. De map hoeft niet vooraf te bestaan en kan dynamisch worden gemaakt. Stel **Eerste rij in als header** als true en selecteer Geen **bij** **Schema importeren.** Klik **op OK** wanneer u klaar bent.

    ![Eigenschappen van sink-gegevensset](media/quickstart-data-flow/sink-dataset-properties.png)

U bent nu klaar met het bouwen van uw gegevensstroom. U bent klaar om deze in uw pijplijn uit te voeren.

## <a name="running-and-monitoring-the-data-flow"></a>De Gegevensstroom

U kunt fouten opsporen in een pijplijn voordat u deze publiceert. In deze stap activeert u een foutopsporingsrun van de gegevensstroompijplijn. Hoewel er bij het voorbeeld van gegevens geen gegevens worden geschreven, worden bij een foutopsporingsrun gegevens naar uw sinkbestemming geschreven.

1. Ga naar het pijplijn-canvas. Klik **op Fouten opsporen** om een foutopsporingsrun te activeren.

    ![Foutopsporingspijplijn](media/quickstart-data-flow/debug-pipeline.png)

1. Pijplijnbuggen van Gegevensstroom maakt gebruik van het actieve foutopsporingscluster, maar het initialiseren duurt nog minstens een minuut. U kunt de voortgang volgen via het **tabblad** Uitvoer. Zodra de run is geslaagd, klikt u op het pictogram van een bril om het bewakingsvenster te openen.

    ![Uitvoer van debuggen](media/quickstart-data-flow/debugging-output.png)

1. In het deelvenster Bewaking ziet u het aantal rijen en de tijd die in elke transformatiestap is besteed.

    ![Transformatiebewaking](media/quickstart-data-flow/4-transformations.png)

1. Klik op een transformatie voor gedetailleerde informatie over de kolommen en partitionering van de gegevens.

    ![Transformatiedetails](media/quickstart-data-flow/transformation-details.png)

Als u deze quickstart correct hebt gevolgd, moet u 83 rijen en 2 kolommen in uw sinkmap hebben geschreven. U kunt de gegevens controleren door uw blobopslag te controleren.


## <a name="next-steps"></a>Volgende stappen

Lees de volgende artikelen voor meer informatie over Azure Synapse Analytics ondersteuning:

> [!div class="nextstepaction"]
> [Pijplijn en activiteiten](../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [Overzicht van toewijzingsgegevensstromen](../data-factory/concepts-data-flow-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [Taal van gegevensstroomexpressie](../data-factory/data-flow-expression-functions.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
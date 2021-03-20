---
title: 'Snelstartgids: gegevens transformeren met behulp van een toewijzings gegevens stroom'
description: Deze zelf studie bevat stapsgewijze instructies voor het gebruik van Azure Synapse Analytics om gegevens te transformeren met toewijzings gegevens stroom.
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/03/2020
ms.openlocfilehash: cb0fd5464f7025d71dd384c56233aefa6a6cd364
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98218719"
---
# <a name="quickstart-transform-data-using-mapping-data-flows"></a>Snelstartgids: gegevens transformeren met behulp van gegevens stromen toewijzen

In deze Quick Start gebruikt u Azure Synapse Analytics om een pijp lijn te maken waarmee gegevens worden getransformeerd van een Azure Data Lake Storage Gen2 bron (ADLS Gen2) naar een ADLS Gen2 Sink met behulp van de toewijzing van de gegevens stroom. Het configuratie patroon in deze Quick start kan worden uitgebreid bij het transformeren van gegevens met behulp van de toewijzings gegevens stroom

In deze Quick Start voert u de volgende stappen uit:

> [!div class="checklist"]
> * Maak een pijp lijn met een gegevens stroom activiteit in azure Synapse Analytics.
> * Bouw een toewijzings gegevens stroom met vier trans formaties.
> * De uitvoering van de pijplijn testen.
> * Een gegevens stroom activiteit bewaken

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* **Azure Synapse-werk ruimte**: Maak een Synapse-werk ruimte met behulp van de Azure Portal Volg de instructies in [Quick Start: een Synapse-werk ruimte maken](quickstart-create-workspace.md).
* **Azure-opslag account**: u kunt ADLS Storage gebruiken als *bron* -en *sink* -gegevens opslag. Als u geen opslagaccount hebt, raadpleegt u het artikel [Een opslagaccount maken](../storage/common/storage-account-create.md) om een account te maken.

    Het bestand dat u in deze zelf studie transformeert, is MoviesDB.csv, dat u [hier](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)kunt vinden. Als u het bestand wilt ophalen uit GitHub, kopieert u de inhoud naar een tekst editor van uw keuze om lokaal op te slaan als een CSV-bestand. Als u het bestand naar uw opslag account wilt uploaden, raadpleegt u [blobs uploaden met de Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). De voor beelden verwijzen naar een container met de naam ' Sample-Data '.

### <a name="navigate-to-the-synapse-studio"></a>Ga naar Synapse Studio

Wanneer uw Azure Synapse-werkruimte is gemaakt, kunt u Synapse Studio op twee manieren openen:

* Open de Synapse-werkruimte in de [Azure-portal](https://ms.portal.azure.com/#home). Selecteer **Openen** op de kaart 'Open Synapse Studio' onder 'Aan de slag'.
* Open [Azure Synapse Analytics](https://web.azuresynapse.net/) en meld u aan bij uw werkruimte.

In deze quickstart wordt de werkruimte met de naam 'adftest2020' als voorbeeld gebruikt. Er wordt automatisch naar de startpagina van Synapse Studio genavigeerd.

![Startpagina van Synapse Studio](media/doc-common-process/synapse-studio-home.png)

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Een pijp lijn maken met een activiteit voor gegevens stromen

Een pijplijn bevat de logische stroom voor het uitvoeren van een reeks activiteiten. In deze sectie maakt u een pijp lijn die een gegevens stroom activiteit bevat.

1. Ga naar het tabblad **Integreren**. Klik op het pluspictogram naast de kop Pijplijnen en selecteer Pijplijn.

   ![Een nieuwe pijplijn maken](media/doc-common-process/new-pipeline.png)

1. Voer op de pagina **Eigenschappen** instellingen van de pijp lijn **TransformMovies** in als **naam**.

1. Sleep onder *verplaatsen en transformeren* in het deel venster *activiteiten* de **gegevens stroom** naar het pijplijn doek.

1. Selecteer in het pop-upvenster **gegevens stroom pagina toevoegen** de optie **nieuwe gegevens stroom maken**  ->  **gegevens** stroom. Klik op **OK** wanneer u klaar bent.

   ![Een gegevens stroom maken](media/quickstart-data-flow/new-data-flow.png)

1. Geef uw gegevensstroom **TransformMovies** een naam op de pagina **Eigenschappen** .

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Transformatie logica bouwen in het canvas voor gegevens stromen

Wanneer u de gegevens stroom hebt gemaakt, wordt u automatisch naar het canvas voor gegevens stromen verzonden. In deze stap bouwt u een gegevens stroom die de MoviesDB.csv in ADLS-opslag neemt en de gemiddelde beoordeling van comedies van 1910 tot 2000 samenvoegt. Vervolgens schrijft u dit bestand terug naar de ADLS-opslag.

1. Schuif boven het canva voor gegevens stroom de schuif regelaar voor **fout opsporing van gegevens stromen** in. In de modus voor fout opsporing kunt u de transformatie logica interactief testen op een live Spark-cluster. Gegevens stroom clusters nemen 5-7 minuten in beslag en gebruikers worden aanbevolen om debug eerst in te scha kelen als de ontwikkeling van gegevens stromen wordt gepland. Zie [debug mode (foutopsporingsmodus](../data-factory/concepts-data-flow-debug-mode.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)) voor meer informatie.

    ![De fout opsporing op de dia](media/quickstart-data-flow/debug-on.png)

1. Voeg in het canvas gegevens stroom een bron toe door te klikken op het vak **bron toevoegen** .

1. Geef uw bron **MoviesDB** een naam. Klik op **Nieuw** om een nieuwe bron gegevensset te maken.

    ![Een nieuwe bron-gegevensset maken](media/quickstart-data-flow/new-source-dataset.png)

1. Kies **Azure data Lake Storage Gen2**. Klik op door gaan.

    ![Azure Data Lake Storage Gen2 kiezen](media/quickstart-data-flow/select-source-dataset.png)

1. Kies **DelimitedText**. Klik op door gaan.

1. Geef uw gegevensset een naam **MoviesDB**. Kies in de vervolg keuzelijst gekoppelde service de optie **Nieuw**.

1. Geef in het scherm gekoppelde service maken de naam op van de ADLS Gen2 gekoppelde service **ADLSGen2** en de verificatie methode op. Voer vervolgens uw verbindings referenties in. In deze Snelstartgids gebruiken we de account sleutel om verbinding te maken met het opslag account. U kunt klikken op **verbinding testen** om te controleren of uw referenties correct zijn ingevoerd. Klik op **Maken** als u klaar bent.

    ![Een gekoppelde bron service maken](media/quickstart-data-flow/adls-gen2-linked-service.png)

1. Wanneer u weer op het scherm gegevensset maken hebt geklikt, voert u in **het veld bestandspad** de locatie in waar uw bestand zich bevindt. In deze Quick start is het bestand ' MoviesDB.csv ' opgeslagen in de container ' voor beeld-gegevens '. Controleer de **eerste rij als koptekst**, terwijl het bestand kopteksten bevat. Selecteer **in verbinding/archief** om het header-schema rechtstreeks te importeren uit het bestand in de opslag. Klik op **OK** wanneer u klaar bent.

    ![Instellingen voor de brongegevensset](media/quickstart-data-flow/source-dataset-properties.png)

1. Als het cluster voor fout opsporing is gestart, gaat u naar het tabblad **voor beeld van gegevens** van de bron transformatie en klikt u op **vernieuwen** om een moment opname van de gegevens op te halen. U kunt gegevens voorbeeld gebruiken om te controleren of uw trans formatie correct is geconfigureerd.

    ![Voorbeeld van gegevens](media/quickstart-data-flow/data-preview.png)

1. Klik naast het bron knooppunt op het canvas voor gegevens stromen op het plus pictogram om een nieuwe trans formatie toe te voegen. De eerste trans formatie die u toevoegt, is een **filter**.

    ![Een filter toevoegen](media/quickstart-data-flow/add-filter.png)

1. Geef een naam op voor de filter transformatie **FilterYears**. Klik op het expressievak naast **filteren op** om de opbouw functie voor expressies te openen. Hier geeft u uw filter voorwaarde op.

1. Met de opbouw functie voor de data flow-expressie kunt u interactief expressies bouwen voor gebruik in verschillende trans formaties. Expressies kunnen ingebouwde functies, kolommen uit het invoer schema en door de gebruiker gedefinieerde para meters bevatten. Zie de [opbouw functie voor data flow](../data-factory/concepts-data-flow-expression-builder.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)-expressies voor meer informatie over het maken van expressies.

    In deze Snelstartgids wilt u de films van genre Comedy filteren die zijn uitgekomen tussen de jaren 1910 en 2000. Als jaar momenteel een teken reeks is, moet u deze converteren naar een geheel getal met behulp van de ```toInteger()``` functie. Gebruik de Opera tors groter dan of gelijk aan (>=) en kleiner dan of gelijk aan (<=) om te vergelijken met de letterlijke jaar waarden 1910 en 200-. Voeg deze expressies samen met de operator and (&&). De expressie wordt als volgt opgehaald:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Als u wilt weten welke films comedies zijn, kunt u de ```rlike()``` functie gebruiken om het patroon ' comedy ' te vinden in de kolom genres. De rlike-expressie samen voegen met de jaar vergelijking om het volgende te krijgen:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    ![Filter voorwaarde opgeven](media/quickstart-data-flow/visual-expression-builder.png)

    Als u een debug-cluster hebt geactiveerd, kunt u uw logica controleren door op **vernieuwen** te klikken om de expressie-uitvoer te bekijken in vergelijking met de gebruikte invoer. Er is meer dan één recht antwoord op hoe u deze logica kunt uitvoeren met behulp van de data flow-expressie taal.

    Klik op **opslaan en volt ooien** zodra u klaar bent met de expressie.

1. Een **voor beeld** van een gegevens ophalen om te controleren of het filter goed werkt.

1. De volgende trans formatie die u toevoegt, is een **statistische** trans formatie onder **schema wijzigings functie**.

    ![Een aggregatie toevoegen](media/quickstart-data-flow/add-aggregate.png)

1. Geef uw aggregatie transformatie **AggregateComedyRatings** een naam. Selecteer in het tabblad **groeperen op** **jaar** in de vervolg keuzelijst om de aggregaties te groeperen op het jaar dat de film is uitkwam.

    ![Cumulatieve instellingen 1](media/quickstart-data-flow/aggregate-settings.png)

1. Ga naar het tabblad **aggregaties** . Geef in het vak links de cumulatieve kolom **AverageComedyRating**. Klik op het vak rechter expressie om de statistische expressie op te geven via de opbouw functie voor expressies.

    ![Cumulatieve instellingen 2](media/quickstart-data-flow/aggregate-settings-2.png)

1. Gebruik de functie aggregate om het gemiddelde van de kolom **classificatie** te berekenen ```avg()``` . Als **classificatie** is een teken reeks die ```avg()``` in een numerieke invoer neemt, moeten we de waarde converteren naar een getal via de ```toInteger()``` functie. Deze expressie ziet er als volgt uit:

    ```avg(toInteger(Rating))```

    Klik op **opslaan en volt ooien** wanneer u klaar bent.

    ![Gemiddelde Comedy-classificatie](media/quickstart-data-flow/average-comedy-rating.png)

1. Ga naar het tabblad voor **beeld van gegevens** om de trans formatie-uitvoer weer te geven. U ziet slechts twee kolommen, **jaar** en **AverageComedyRating**.

    ![Preview van gecombineerde gegevens](media/quickstart-data-flow/transformation-output.png)

1. Vervolgens wilt u een **sink** -trans formatie toevoegen onder **bestemming**.

    ![Een Sink toevoegen](media/quickstart-data-flow/add-sink.png)

1. Geef **uw Sink een naam.** Klik op **Nieuw** om uw Sink-gegevensset te maken.

1. Kies **Azure data Lake Storage Gen2**. Klik op door gaan.

1. Kies **DelimitedText**. Klik op door gaan.

1. Geef uw Sink-gegevensset een naam **MoviesSink**. Kies voor gekoppelde service de ADLS Gen2 gekoppelde service die u in stap 7 hebt gemaakt. Voer een uitvoermap in om uw gegevens naar te schrijven. In deze Snelstartgids schrijven we naar de map ' output ' in container ' voor beeld-gegevens '. De map hoeft niet vooraf te bestaan en kan dynamisch worden gemaakt. Stel de **eerste rij als koptekst** in op True en selecteer **geen** voor het **import schema**. Klik op **OK** wanneer u klaar bent.

    ![Eigenschappen van Sink-gegevensset](media/quickstart-data-flow/sink-dataset-properties.png)

Nu bent u klaar met het bouwen van uw gegevens stroom. U kunt deze nu uitvoeren in de pijp lijn.

## <a name="running-and-monitoring-the-data-flow"></a>De gegevens stroom wordt uitgevoerd en bewaakt

U kunt fouten opsporen in een pijp lijn voordat u deze publiceert. In deze stap gaat u een debug-uitvoering van de gegevensstroom pijplijn activeren. Terwijl voor beeld van de gegevens geen gegevens worden geschreven, worden gegevens naar de Sink-bestemming geschreven.

1. Ga naar het pijp lijn-canvas. Klik op **fout opsporing** om een uitvoering van de fout opsporing te activeren.

    ![Debug-pijp lijn](media/quickstart-data-flow/debug-pipeline.png)

1. Bij het opsporen van pijp lijn voor gegevens stroom activiteiten wordt het actieve debug-cluster gebruikt, maar het kan wel ten minste een minuut duren om te initialiseren. U kunt de voortgang volgen via het tabblad **uitvoer** . Zodra de uitvoering is geslaagd, klikt u op het pictogram bril om het deel venster bewaking te openen.

    ![Uitvoer fout opsporing](media/quickstart-data-flow/debugging-output.png)

1. In het deel venster bewaking ziet u het aantal rijen en tijd dat aan elke transformatie stap is besteed.

    ![Trans formatie bewaken](media/quickstart-data-flow/4-transformations.png)

1. Klik op een trans formatie om gedetailleerde informatie over de kolommen en het partitioneren van de gegevens op te halen.

    ![Transformatie Details](media/quickstart-data-flow/transformation-details.png)

Als u deze Quick Start op de juiste wijze hebt uitgevoerd, moet u 83 rijen en twee kolommen in uw map Sink hebben geschreven. U kunt de gegevens controleren door uw Blob-opslag te controleren.


## <a name="next-steps"></a>Volgende stappen

Ga naar de volgende artikelen voor meer informatie over ondersteuning voor Azure Synapse Analytics:

> [!div class="nextstepaction"]
> [Pijp lijn en activiteiten](../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  Overzicht van toewijzings [gegevens stroom](../data-factory/concepts-data-flow-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [Data flow-expressie taal](../data-factory/data-flow-expression-functions.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
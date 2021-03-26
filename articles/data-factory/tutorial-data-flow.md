---
title: Gegevens transformeren met behulp van een toewijzings gegevens stroom
description: Deze zelf studie bevat stapsgewijze instructies voor het gebruik van Azure Data Factory om gegevens te transformeren met toewijzings gegevens stroom
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/11/2021
ms.openlocfilehash: 2363afc4c84ee7606410ceecd6819c12d0333dbb
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563290"
---
# <a name="transform-data-using-mapping-data-flows"></a>Gegevens transformeren met toewijzingsgegevensstromen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Zie [Inleiding tot Azure Data Factory](introduction.md) als u niet bekend bent met Azure Data Factory.

In deze zelf studie gebruikt u de Azure Data Factory gebruikers interface (UX) om een pijp lijn te maken waarmee gegevens worden gekopieerd en getransformeerd van een Azure Data Lake Storage (ADLS) Gen2-bron naar een ADLS Gen2-Sink met toewijzings gegevens stroom. Het configuratie patroon in deze zelf studie kan worden uitgebreid bij het transformeren van gegevens met behulp van de toewijzings gegevens stroom

 >[!NOTE]
   >Deze zelf studie is bedoeld voor het toewijzen van gegevens stromen in het algemeen. Gegevens stromen zijn beschikbaar in Azure Data Factory-en Synapse-pijp lijnen. Als u geen ervaring hebt met gegevens stromen in azure Synapse-pijp lijnen, kunt u de [gegevens stroom volgen met behulp van Azure Synapse-pijp lijnen](../synapse-analytics/concepts-data-flow-overview.md) 
   
In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een data factory maken.
> * Maak een pijp lijn met een gegevens stroom activiteit.
> * Bouw een toewijzings gegevens stroom met vier trans formaties.
> * De uitvoering van de pijplijn testen.
> * Een gegevens stroom activiteit bewaken

## <a name="prerequisites"></a>Vereisten
* **Azure-abonnement**. Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* **Azure-opslagaccount**. U gebruikt ADLS-opslag als *bron* -en *sink* -gegevens opslag. Als u geen opslagaccount hebt, raadpleegt u het artikel [Een opslagaccount maken](../storage/common/storage-account-create.md) om een account te maken.

Het bestand dat u in deze zelf studie transformeert, is MoviesDB.csv, dat u [hier](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)kunt vinden. Als u het bestand wilt ophalen uit GitHub, kopieert u de inhoud naar een tekst editor van uw keuze om lokaal op te slaan als een CSV-bestand. Als u het bestand naar uw opslag account wilt uploaden, raadpleegt u [blobs uploaden met de Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). De voor beelden verwijzen naar een container met de naam ' Sample-Data '.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

In deze stap maakt u een data factory en opent u de Data Factory UX om een pijp lijn te maken in de data factory.

1. Open **Microsoft Edge** of **Google Chrome**. Momenteel wordt Data Factory gebruikers interface alleen ondersteund in de micro soft Edge-en Google Chrome-webbrowsers.
2. Selecteer in het linkermenu **Een resource maken** > **Integratie** > **Data Factory**:

   ![Selectie van Data Factory in het deelvenster Nieuw](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Voer op de pagina **Nieuwe data factory** **ADFTutorialDataFactory** in bij **Naam**.

   De naam van de Azure-gegevensfactory moet *wereldwijd uniek* zijn. Als u een foutbericht ontvangt dat betrekking heeft op de waarde die bij de naam is ingevuld, voert u een andere naam in voor de data factory. (Gebruik dan bijvoorbeeld uwnaamADFTutorialDataFactory). Zie [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Nieuw data factory fout bericht voor dubbele naam.":::
4. Selecteer het Azure-**abonnement** waarin u de data factory wilt maken.
5. Voer een van de volgende stappen uit voor **Resourcegroep**:

    a. Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst.

    b. Selecteer **Nieuwe maken** en voer de naam van een resourcegroep in. 
         
    Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie. 
6. Selecteer **V2** onder **Versie**.
7. Selecteer onder **Locatie** een locatie voor de data factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. Gegevens archieven (bijvoorbeeld Azure Storage en SQL Database) en berekeningen (zoals Azure HDInsight) die door de data factory worden gebruikt, kunnen zich in andere regio's bevindt.
8. Selecteer **Maken**.
9. Als het maken is voltooid, ziet u de melding in het meldingencentrum. Selecteer **Naar resource gaan** om naar de pagina Data factory te gaan.
10. Selecteer de tegel **Maken en controleren** om de Data Factory-gebruikersinterface te openen op een afzonderlijk tabblad.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Een pijp lijn maken met een activiteit voor gegevens stromen

In deze stap maakt u een pijp lijn die een gegevens stroom activiteit bevat.

1. Selecteer op de pagina **Aan de slag** de optie **Pijplijn maken**.

   ![Pijplijn maken](./media/doc-common-process/get-started-page.png)

1. Voer op het tabblad **Algemeen** voor de pijp lijn **TransformMovies** in als **naam** van de pijp lijn.
1. Schuif in de bovenste balk van de fabriek de schuif regelaar voor **fout opsporing van gegevens stromen** aan. In de modus voor fout opsporing kunt u de transformatie logica interactief testen op een live Spark-cluster. Gegevens stroom clusters nemen 5-7 minuten in beslag en gebruikers worden aanbevolen om debug eerst in te scha kelen als de ontwikkeling van gegevens stromen wordt gepland. Zie [debug mode (foutopsporingsmodus](concepts-data-flow-debug-mode.md)) voor meer informatie.

    ![Activiteit gegevens stroom](media/tutorial-data-flow/dataflow1.png)
1. Vouw in het deel venster **activiteiten** de accordeon **verplaatsen en transformeren** uit. Sleep de activiteit **gegevens stroom** van het deel venster naar het pijp lijn-canvas.

    ![Scherm opname van het pijplijn doek waar u de activiteit van de gegevens stroom kunt verwijderen.](media/tutorial-data-flow/activity1.png)
1. Selecteer in het pop-upvenster **gegevens stroom toevoegen** de optie **nieuwe gegevens stroom maken** en geef vervolgens een naam op voor de gegevens stroom **TransformMovies**. Klik op volt ooien wanneer u klaar bent.

    ![Scherm opname van de naam van de gegevens stroom wanneer u een nieuwe gegevens stroom maakt.](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Transformatie logica bouwen in het canvas voor gegevens stromen

Wanneer u de gegevens stroom hebt gemaakt, wordt u automatisch naar het canvas voor gegevens stromen verzonden. In deze stap bouwt u een gegevens stroom die de moviesDB.csv in ADLS-opslag neemt en de gemiddelde beoordeling van comedies van 1910 tot 2000 samenvoegt. Vervolgens schrijft u dit bestand terug naar de ADLS-opslag.

1. Voeg in het canvas gegevens stroom een bron toe door te klikken op het vak **bron toevoegen** .

    ![Scherm opname van het vak Bron toevoegen.](media/tutorial-data-flow/dataflow2.png)
1. Geef uw bron **MoviesDB** een naam. Klik op **Nieuw** om een nieuwe bron gegevensset te maken.

    ![Scherm afbeelding die laat zien waar u nieuw selecteert nadat u de naam van uw bron hebt gewijzigd.](media/tutorial-data-flow/dataflow3.png)
1. Kies **Azure data Lake Storage Gen2**. Klik op door gaan.

    ![Scherm opname van de Azure Data Lake Storage Gen2 tegel.](media/tutorial-data-flow/dataset1.png)
1. Kies **DelimitedText**. Klik op door gaan.

    ![Scherm afbeelding waarin de tegel DelimitedText wordt weer gegeven.](media/tutorial-data-flow/dataset2.png)
1. Geef uw gegevensset een naam **MoviesDB**. Kies in de vervolg keuzelijst gekoppelde service de optie **Nieuw**.

    ![Scherm opname van de lijst met gekoppelde service vervolg keuzelijst.](media/tutorial-data-flow/dataset3.png)
1. Typ in het scherm gekoppelde service maken de naam van uw ADLS Gen2-gekoppelde service **ADLSGen2** en geef uw verificatie methode op. Voer vervolgens uw verbindings referenties in. In deze zelf studie gebruiken we de account sleutel om verbinding te maken met het opslag account. U kunt klikken op **verbinding testen** om te controleren of uw referenties correct zijn ingevoerd. Klik op Maken als u klaar bent.

    ![Gekoppelde service](media/tutorial-data-flow/ls1.png)
1. Wanneer u weer op het scherm gegevensset maken hebt geklikt, voert u in waar het bestand zich bevindt in het veld **bestandspad** . In deze zelf studie wordt het bestand moviesDB.csv bevindt zich in container-voorbeeld gegevens. Controleer de **eerste rij als koptekst**, terwijl het bestand kopteksten bevat. Selecteer **in verbinding/archief** om het header-schema rechtstreeks te importeren uit het bestand in de opslag. Klik op OK wanneer u klaar bent.

    ![Gegevenssets](media/tutorial-data-flow/dataset4.png)
1. Als het cluster voor fout opsporing is gestart, gaat u naar het tabblad **voor beeld van gegevens** van de bron transformatie en klikt u op **vernieuwen** om een moment opname van de gegevens op te halen. U kunt gegevens voorbeeld gebruiken om te controleren of uw trans formatie correct is geconfigureerd.

    ![Scherm afbeelding die laat zien waar u een voor beeld van uw gegevens kunt bekijken om te controleren of uw trans formatie correct is geconfigureerd.](media/tutorial-data-flow/dataflow4.png)
1. Klik naast het bron knooppunt op het canvas voor gegevens stromen op het plus pictogram om een nieuwe trans formatie toe te voegen. De eerste trans formatie die u toevoegt, is een **filter**.

    ![Canvas voor gegevens stroom](media/tutorial-data-flow/dataflow5.png)
1. Geef een naam op voor de filter transformatie **FilterYears**. Klik op het expressievak naast **filteren op** om de opbouw functie voor expressies te openen. Hier geeft u uw filter voorwaarde op.

    ![Scherm opname van het vak filteren op expressie.](media/tutorial-data-flow/filter1.png)
1. Met de opbouw functie voor de data flow-expressie kunt u interactief expressies bouwen voor gebruik in verschillende trans formaties. Expressies kunnen ingebouwde functies, kolommen uit het invoer schema en door de gebruiker gedefinieerde para meters bevatten. Zie de [opbouw functie voor data flow](concepts-data-flow-expression-builder.md)-expressies voor meer informatie over het maken van expressies.

    In deze zelf studie wilt u de films van genre Comedy filteren die zijn uitgekomen tussen de jaren 1910 en 2000. Als jaar momenteel een teken reeks is, moet u deze converteren naar een geheel getal met behulp van de ```toInteger()``` functie. Gebruik de Opera tors groter dan of gelijk aan (>=) en kleiner dan of gelijk aan (<=) om te vergelijken met de letterlijke jaar waarden 1910 en 200-. Voeg deze expressies samen met de operator and (&&). De expressie wordt als volgt opgehaald:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Als u wilt weten welke films comedies zijn, kunt u de ```rlike()``` functie gebruiken om het patroon ' comedy ' te vinden in de kolom genres. De rlike-expressie samen voegen met de jaar vergelijking om het volgende te krijgen:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    Als u een debug-cluster hebt geactiveerd, kunt u uw logica controleren door op **vernieuwen** te klikken om de expressie-uitvoer te bekijken in vergelijking met de gebruikte invoer. Er is meer dan één recht antwoord op hoe u deze logica kunt uitvoeren met behulp van de data flow-expressie taal.

    ![Filter](media/tutorial-data-flow/filter2.png)

    Klik op **opslaan en volt ooien** zodra u klaar bent met de expressie.

1. Een **voor beeld** van een gegevens ophalen om te controleren of het filter goed werkt.

    ![Scherm opname van de voor beeld van de gegevens die u hebt opgehaald.](media/tutorial-data-flow/filter3.png)
1. De volgende trans formatie die u toevoegt, is een **statistische** trans formatie onder **schema wijzigings functie**.

    ![Scherm opname van de aggregatie van het cumulatieve schema.](media/tutorial-data-flow/agg1.png)
1. Geef uw aggregatie transformatie **AggregateComedyRatings** een naam. Selecteer in het tabblad **groeperen op** **jaar** in de vervolg keuzelijst om de aggregaties te groeperen op het jaar dat de film is uitkwam.

    ![Scherm afbeelding met de optie Year op het tabblad Group by onder statistische instellingen.](media/tutorial-data-flow/agg2.png)
1. Ga naar het tabblad **aggregaties** . Geef in het vak links de cumulatieve kolom **AverageComedyRating**. Klik op het vak rechter expressie om de statistische expressie op te geven via de opbouw functie voor expressies.

    ![Scherm afbeelding met de optie Year op het tabblad aggregaties onder aggregatie instellingen.](media/tutorial-data-flow/agg3.png)
1. Gebruik de functie aggregate om het gemiddelde van de kolom **classificatie** te berekenen ```avg()``` . Als **classificatie** is een teken reeks die ```avg()``` in een numerieke invoer neemt, moeten we de waarde converteren naar een getal via de ```toInteger()``` functie. De expressie ziet er als volgt uit:

    ```avg(toInteger(Rating))```

    Klik op **opslaan en volt ooien** wanneer u klaar bent.

    ![Scherm opname waarin de opgeslagen expressie wordt weer gegeven.](media/tutorial-data-flow/agg4.png)
1. Ga naar het tabblad voor **beeld van gegevens** om de trans formatie-uitvoer weer te geven. U ziet slechts twee kolommen, **jaar** en **AverageComedyRating**.

    ![Samenvoegen](media/tutorial-data-flow/agg3.png)
1. Vervolgens wilt u een **sink** -trans formatie toevoegen onder **bestemming**.

    ![Scherm afbeelding die laat zien waar een Sink-trans formatie onder bestemming moet worden toegevoegd.](media/tutorial-data-flow/sink1.png)
1. Geef **uw Sink een naam.** Klik op **Nieuw** om uw Sink-gegevensset te maken.

    ![Scherm afbeelding die laat zien waar u uw Sink kunt benoemen en een nieuwe Sink-gegevensset maakt.](media/tutorial-data-flow/sink2.png)
1. Kies **Azure data Lake Storage Gen2**. Klik op door gaan.

    ![Scherm opname van de Azure Data Lake Storage Gen2 tegel die u kunt kiezen.](media/tutorial-data-flow/dataset1.png)
1. Kies **DelimitedText**. Klik op door gaan.

    ![Gegevensset](media/tutorial-data-flow/dataset2.png)
1. Geef uw Sink-gegevensset een naam **MoviesSink**. Voor gekoppelde service kiest u de gekoppelde ADLS Gen2-service die u in stap 6 hebt gemaakt. Voer een uitvoermap in om uw gegevens naar te schrijven. In deze zelf studie schrijven we naar map ' uitvoer ' in container ' voor beeld-gegevens '. De map hoeft niet vooraf te bestaan en kan dynamisch worden gemaakt. Stel de **eerste rij als koptekst** in op True en selecteer **geen** voor het **import schema**. Klik op Voltooien.

    ![Sink](media/tutorial-data-flow/sink3.png)

Nu bent u klaar met het bouwen van uw gegevens stroom. U kunt deze nu uitvoeren in de pijp lijn.

## <a name="running-and-monitoring-the-data-flow"></a>De gegevens stroom wordt uitgevoerd en bewaakt

U kunt fouten opsporen in een pijp lijn voordat u deze publiceert. In deze stap gaat u een debug-uitvoering van de gegevensstroom pijplijn activeren. Terwijl voor beeld van de gegevens geen gegevens worden geschreven, worden gegevens naar de Sink-bestemming geschreven.

1. Ga naar het pijp lijn-canvas. Klik op **fout opsporing** om een uitvoering van de fout opsporing te activeren.

    ![Scherm opname van het pijplijn doek met de markering debug.](media/tutorial-data-flow/pipeline1.png)
1. Bij het opsporen van pijp lijn voor gegevens stroom activiteiten wordt het actieve debug-cluster gebruikt, maar het kan wel ten minste een minuut duren om te initialiseren. U kunt de voortgang volgen via het tabblad **uitvoer** . Zodra de uitvoering is geslaagd, klikt u op het pictogram bril om het deel venster bewaking te openen.

    ![Pijplijn](media/tutorial-data-flow/pipeline2.png)
1. In het deel venster bewaking ziet u het aantal rijen en tijd dat aan elke transformatie stap is besteed.

    ![Scherm opname van het deel venster bewaking waarin u het aantal rijen en tijd kunt zien dat elke transformatie stap is doorstaan.](media/tutorial-data-flow/pipeline3.png)
1. Klik op een trans formatie om gedetailleerde informatie over de kolommen en het partitioneren van de gegevens op te halen.

    ![Bewaking](media/tutorial-data-flow/pipeline4.png)

Als u deze zelf studie correct hebt gevolgd, moet u 83 rijen en 2 kolommen in uw map Sink hebben geschreven. U kunt controleren of de gegevens correct zijn door uw Blob-opslag te controleren.

## <a name="next-steps"></a>Volgende stappen

De pijp lijn in deze zelf studie voert een gegevens stroom uit die de gemiddelde waardering van comedies van 1910 tot 2000 samenvoegt en schrijft de gegevens naar ADLS. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een data factory maken.
> * Maak een pijp lijn met een gegevens stroom activiteit.
> * Bouw een toewijzings gegevens stroom met vier trans formaties.
> * De uitvoering van de pijplijn testen.
> * Een gegevens stroom activiteit bewaken

Meer informatie over de [taal van de data flow-expressie](data-flow-expression-functions.md).
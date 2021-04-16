---
title: Gegevens transformeren met behulp van een toewijzingsgegevensstroom
description: Deze zelfstudie bevat stapsgewijs instructies voor het gebruik van Azure Data Factory om gegevens te transformeren met toewijzingsgegevensstroom
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2021
ms.openlocfilehash: 0842dad0e0ea6f9987727e8abf3d0eaf8a59e821
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517509"
---
# <a name="transform-data-using-mapping-data-flows"></a>Gegevens transformeren met toewijzingsgegevensstromen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Zie [Inleiding tot Azure Data Factory](introduction.md) als u niet bekend bent met Azure Data Factory.

In deze zelfstudie gebruikt u de Azure Data Factory-gebruikersinterface (UX) om een pijplijn te maken die gegevens kopieert en transformeert van een Azure Data Lake Storage Gen2-bron (ADLS) naar een ADLS Gen2-sink met behulp van toewijzingsgegevensstroom. Het configuratiepatroon in deze zelfstudie kan worden uitgebreid bij het transformeren van gegevens met behulp van toewijzingsgegevensstroom

 >[!NOTE]
   >Deze zelfstudie is bedoeld voor het toewijzen van gegevensstromen in het algemeen. Gegevensstromen zijn beschikbaar in Azure Data Factory en Synapse-pijplijnen. Als u geen tijd hebt voor gegevensstromen in Azure Synapse Pipelines, volgt u de instructies Gegevensstroom [pijplijnen Azure Synapse pijplijnen](../synapse-analytics/concepts-data-flow-overview.md) 
   
In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een data factory maken.
> * Maak een pijplijn met een Gegevensstroom activiteit.
> * Bouw een toewijzingsgegevensstroom met vier transformaties.
> * De uitvoering van de pijplijn testen.
> * Een Gegevensstroom bewaken

## <a name="prerequisites"></a>Vereisten
* **Azure-abonnement**. Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* **Azure-opslagaccount**. U gebruikt ADLS-opslag als *bron-* en *sinkgegevensopslag.* Als u geen opslagaccount hebt, raadpleegt u het artikel [Een opslagaccount maken](../storage/common/storage-account-create.md) om een account te maken.

Het bestand dat we in deze zelfstudie transformeren, is MoviesDB.csv, dat hier te [vinden is.](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv) Als u het bestand wilt ophalen uit GitHub, kopieert u de inhoud naar een teksteditor van uw keuze om het lokaal op te slaan als een CSV-bestand. Zie Blobs uploaden met de Azure Portal om het bestand naar uw [opslagaccount te Azure Portal.](../storage/blobs/storage-quickstart-blobs-portal.md) De voorbeelden verwijzen naar een container met de naam 'sample-data'.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

In deze stap maakt u een data factory en opent u de Data Factory UX om een pijplijn te maken in de data factory.

1. Open **Microsoft Edge** of **Google Chrome**. Momenteel wordt Data Factory-gebruikersinterface alleen ondersteund in de webbrowsers Microsoft Edge en Google Chrome.
2. Selecteer in het linkermenu **Een resource maken** > **Integratie** > **Data Factory**:

   ![Selectie van Data Factory in het deelvenster Nieuw](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Voer op de pagina **Nieuwe data factory** **ADFTutorialDataFactory** in bij **Naam**.

   De naam van de Azure-gegevensfactory moet *wereldwijd uniek* zijn. Als u een foutbericht ontvangt dat betrekking heeft op de waarde die bij de naam is ingevuld, voert u een andere naam in voor de data factory. (Gebruik dan bijvoorbeeld uwnaamADFTutorialDataFactory). Zie [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Nieuw data factory foutbericht voor dubbele naam.":::
4. Selecteer het Azure-**abonnement** waarin u de data factory wilt maken.
5. Voer een van de volgende stappen uit voor **Resourcegroep**:

    a. Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst.

    b. Selecteer **Nieuwe maken** en voer de naam van een resourcegroep in. 
         
    Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie. 
6. Selecteer **V2** onder **Versie**.
7. Selecteer onder **Locatie** een locatie voor de data factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. Gegevensopslag (bijvoorbeeld Azure Storage en SQL Database) en berekeningen (bijvoorbeeld Azure HDInsight) die door de data factory worden gebruikt, kunnen zich in andere regio's.
8. Selecteer **Maken**.
9. Als het maken is voltooid, ziet u de melding in het meldingencentrum. Selecteer **Naar resource gaan** om naar de pagina Data factory te gaan.
10. Selecteer de tegel **Maken en controleren** om de Data Factory-gebruikersinterface te openen op een afzonderlijk tabblad.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Een pijplijn maken met een Gegevensstroom activiteit

In deze stap maakt u een pijplijn die een Gegevensstroom bevat.

1. Selecteer op de pagina **Aan de slag** de optie **Pijplijn maken**.

   ![Pijplijn maken](./media/doc-common-process/get-started-page.png)

1. Voer op **het** tabblad Algemeen voor de pijplijn **TransformMovies in** **als Naam** van de pijplijn.
1. Vouw in **het** deelvenster Activiteiten de overeenkomst **Verplaatsen en transformeren** uit. Sleep de activiteit **Gegevensstroom** van het deelvenster naar het pijplijn-canvas.

    ![Schermopname van het pijplijn-canvas waar u de activiteit Gegevensstroom neerzetten.](media/tutorial-data-flow/activity1.png)
1. Selecteer in **het** pop-up Gegevensstroom toevoegen de optie Nieuwe Gegevensstroom **en** noem vervolgens de **gegevensstroom TransformMovies.** Klik op Voltooien wanneer u klaar bent.

    ![Schermopname die laat zien waar u uw gegevensstroom een naam geeft wanneer u een nieuwe gegevensstroom maakt.](media/tutorial-data-flow/activity2.png)
1. Schuif in de bovenste balk van het pijplijn-canvas de **Gegevensstroom schuifregelaar voor foutopsporing** in. Met de foutopsporingsmodus kunt u transformatielogica interactief testen op een live Spark-cluster. Gegevensstroom-clusters duurt 5-7 minuten om op te warmen en gebruikers wordt aangeraden om eerst foutopsporing in te schakelen als ze van plan zijn om Gegevensstroom ontwikkelen. Zie Foutopsporingsmodus [voor meer informatie.](concepts-data-flow-debug-mode.md)

    ![Gegevensstroom activiteit](media/tutorial-data-flow/dataflow1.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Transformatielogica bouwen in het gegevensstroom-canvas

Zodra u uw Gegevensstroom, wordt u automatisch naar het gegevensstroomvas verzonden. In deze stap bouwt u een gegevensstroom die de moviesDB.csv in ADLS-opslag gebruikt en de gemiddelde classificatie van comedies van 1910 tot 2000 aggregeert. Vervolgens schrijft u dit bestand terug naar de ADLS-opslag.

1. Voeg in het canvas van de gegevensstroom een bron toe door op het vak **Bron toevoegen te** klikken.

    ![Schermopname van het vak Bron toevoegen.](media/tutorial-data-flow/dataflow2.png)
1. Noem uw bron **MoviesDB.** Klik op **Nieuw om** een nieuwe bronset te maken.

    ![Schermopname die laat zien waar u Nieuw selecteert nadat u de bron een naam hebt gegeven.](media/tutorial-data-flow/dataflow3.png)
1. Kies **Azure Data Lake Storage Gen2**. Klik op Doorgaan.

    ![Schermopname van de Azure Data Lake Storage Gen2 tegel.](media/tutorial-data-flow/dataset1.png)
1. Kies **DelimitedText.** Klik op Doorgaan.

    ![Schermopname van de tegel DelimitedText.](media/tutorial-data-flow/dataset2.png)
1. Noem uw **gegevensset MoviesDB.** Kies Nieuw in de **vervolgkeuzekeuze voor de gekoppelde service.**

    ![Schermopname van de vervolgkeuzelijst Gekoppelde service.](media/tutorial-data-flow/dataset3.png)
1. Geef in het scherm voor het maken van de gekoppelde service uw gekoppelde ADLS Gen2-service **ADLSGen2** een naam en geef uw verificatiemethode op. Voer vervolgens uw verbindingsreferenties in. In deze zelfstudie gebruiken we Accountsleutel om verbinding te maken met ons opslagaccount. U kunt op **Verbinding testen klikken** om te controleren of uw referenties correct zijn ingevoerd. Klik op Maken als u klaar bent.

    ![Gekoppelde service](media/tutorial-data-flow/ls1.png)
1. Wanneer u terug bent in het scherm voor het maken van de gegevensset, voert u in waar het bestand zich bevindt onder het **veld Bestandspad.** In deze zelfstudie bevindt het bestand moviesDB.csv zich in container sample-data. Als het bestand headers bevat, controleert u **Eerste rij als header.** Selecteer **Uit verbinding/archief om** het headerschema rechtstreeks vanuit het bestand in de opslag te importeren. Klik op OK wanneer u klaar bent.

    ![Gegevenssets](media/tutorial-data-flow/dataset4.png)
1. Als uw foutopsporingscluster is gestart, gaat u naar het tabblad **Gegevensvoorbeeld** van de brontransformatie en klikt u op Vernieuwen **om** een momentopname van de gegevens op te halen. U kunt het voorbeeld van gegevens gebruiken om te controleren of uw transformatie correct is geconfigureerd.

    ![Schermopname die laat zien waar u een voorbeeld van uw gegevens kunt bekijken om te controleren of uw transformatie correct is geconfigureerd.](media/tutorial-data-flow/dataflow4.png)
1. Klik naast het bron-knooppunt op het canvas van de gegevensstroom op het pluspictogram om een nieuwe transformatie toe te voegen. De eerste transformatie die u toevoegt, is **filter**.

    ![Gegevensstroom canvas](media/tutorial-data-flow/dataflow5.png)
1. Noem de filtertransformatie **FilterYears.** Klik op het expressievak naast **Filteren op om** de opbouwer voor expressies te openen. Hier geeft u de filtervoorwaarde op.

    ![Schermopname van het vak Filteren op expressie.](media/tutorial-data-flow/filter1.png)
1. Met de opbouwer van gegevensstroomexpressie kunt u interactief expressies bouwen voor gebruik in verschillende transformaties. Expressies kunnen ingebouwde functies, kolommen uit het invoerschema en door de gebruiker gedefinieerde parameters bevatten. Zie voor meer informatie over het bouwen van expressies [Gegevensstroom builder voor expressies.](concepts-data-flow-expression-builder.md)

    In deze zelfstudie wilt u films van genregenres filteren die tussen 1910 en 2000 zijn uit gekomen. Omdat jaar momenteel een tekenreeks is, moet u deze converteren naar een geheel getal met behulp van de ```toInteger()``` functie . Gebruik de operatoren groter dan of gelijk aan (>=) en kleiner dan of gelijk aan (<=) om de letterlijke jaarwaarden 1910 en 200-te vergelijken. Deze expressies samenbrengen met de operator en (&&). De expressie komt als volgt uit:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Als u wilt weten welke films comedies zijn, kunt u de functie gebruiken om patroon ```rlike()``` 'Tje' te vinden in de kolomkolom. Maak de rlike-expressie samen met de jaarvergelijking om het volgende te krijgen:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    Als u een actief foutopsporingscluster hebt, kunt u uw logica controleren door te klikken op Vernieuwen om de expressie-uitvoer weer te geven in vergelijking met de gebruikte invoer.  Er is meer dan één juist antwoord op hoe u deze logica kunt uitvoeren met behulp van de taal van de gegevensstroomexpressie.

    ![Filter](media/tutorial-data-flow/filter2.png)

    Klik **op Opslaan en voltooien** wanneer u klaar bent met uw expressie.

1. Haal een **voorbeeld van gegevens** op om te controleren of het filter correct werkt.

    ![Schermopname van het gegevensvoorbeeld dat u hebt opgehaald.](media/tutorial-data-flow/filter3.png)
1. De volgende transformatie die u toevoegt, is een **Aggregatietransformatie** onder **Schema-modifier**.

    ![Schermopname van de aggregatieschema-modifier.](media/tutorial-data-flow/agg1.png)
1. Noem de **aggregatietransformatie AggregateComedyRatings.** Selecteer op **het tabblad** Groeperen op de **optie Jaar** in de vervolgkeuze lijst om de aggregaties te groeperen op het jaar dat de film uit is gekomen.

    ![Schermopname van de optie jaar op het tabblad Groeperen op onder Aggregatie-instellingen.](media/tutorial-data-flow/agg2.png)
1. Ga naar het **tabblad Statistische** gegevens. Noem in het linkertekstvak de aggregatiekolom **AverageComedyRating.** Klik op het vak met de rechterexpressie om de samengetagde expressie in te voeren via de opbouwer van de expressie.

    ![Schermopname van de optie Jaar op het tabblad Aggregaten onder Aggregatie-instellingen.](media/tutorial-data-flow/agg3.png)
1. Gebruik de statistische functie om het gemiddelde **van de kolom Waardering** op te ```avg()``` halen. Omdat **Waardering** een tekenreeks is en numerieke invoer op zich neemt, moeten we de waarde converteren ```avg()``` naar een getal via de functie ```toInteger()``` . Dit is de expressie die er als volgende uitziet:

    ```avg(toInteger(Rating))```

    Klik **op Opslaan en voltooien wanneer** u klaar bent.

    ![Schermopname van de opgeslagen expressie.](media/tutorial-data-flow/agg4.png)
1. Ga naar het **tabblad Gegevensvoorbeeld** om de transformatie-uitvoer weer te geven. U ziet dat er slechts twee kolommen zijn: **year** en **AverageComedyRating.**

    ![Samenvoegen](media/tutorial-data-flow/agg3.png)
1. Vervolgens wilt u een **Sink-transformatie toevoegen** onder **Doel**.

    ![Schermopname die laat zien waar u een sinktransformatie kunt toevoegen onder Doel.](media/tutorial-data-flow/sink1.png)
1. Noem uw sink **Sink**. Klik **op Nieuw om** uw sink-gegevensset te maken.

    ![Schermopname die laat zien waar u de sink een naam kunt geven en een nieuwe sink-gegevensset kunt maken.](media/tutorial-data-flow/sink2.png)
1. Kies **Azure Data Lake Storage Gen2**. Klik op Doorgaan.

    ![Schermopname van de Azure Data Lake Storage Gen2 tegel die u kunt kiezen.](media/tutorial-data-flow/dataset1.png)
1. Kies **DelimitedText.** Klik op Doorgaan.

    ![Gegevensset](media/tutorial-data-flow/dataset2.png)
1. Noem uw **sink-gegevensset MoviesSink.** Kies voor gekoppelde service de gekoppelde ADLS Gen2-service die u in stap 6 hebt gemaakt. Voer een uitvoermap in om uw gegevens naar te schrijven. In deze zelfstudie schrijven we naar de map 'output' in container 'sample-data'. De map hoeft niet vooraf te bestaan en kan dynamisch worden gemaakt. Stel **Eerste rij in als header** als true en selecteer Geen **bij** **Schema importeren.** Klik op Voltooien.

    ![Sink](media/tutorial-data-flow/sink3.png)

U bent nu klaar met het bouwen van uw gegevensstroom. U bent klaar om deze in uw pijplijn uit te voeren.

## <a name="running-and-monitoring-the-data-flow"></a>De Gegevensstroom

U kunt fouten opsporen in een pijplijn voordat u deze publiceert. In deze stap activeert u een foutopsporingsrun van de gegevensstroompijplijn. Hoewel de voorbeeldgegevens geen gegevens schrijven, schrijft een foutopsporingsrun gegevens naar uw sinkbestemming.

1. Ga naar het pijplijn-canvas. Klik **op Fouten opsporen** om een foutopsporingsrun te activeren.

    ![Schermopname van het pijplijn-canvas met Foutopsporing gemarkeerd.](media/tutorial-data-flow/pipeline1.png)
1. Pijplijnbuggen van Gegevensstroom maakt gebruik van het actieve foutopsporingscluster, maar het duurt nog minstens een minuut om te initialiseren. U kunt de voortgang volgen via het **tabblad** Uitvoer. Zodra de run is geslaagd, klikt u op het pictogram van een bril om het bewakingsvenster te openen.

    ![Pijplijn](media/tutorial-data-flow/pipeline2.png)
1. In het deelvenster Bewaking ziet u het aantal rijen en de tijd die in elke transformatiestap is besteed.

    ![Schermopname van het bewakingsvenster waarin u het aantal rijen en de tijd kunt zien die in elke transformatiestap zijn besteed.](media/tutorial-data-flow/pipeline3.png)
1. Klik op een transformatie voor gedetailleerde informatie over de kolommen en partitionering van de gegevens.

    ![Bewaking](media/tutorial-data-flow/pipeline4.png)

Als u deze zelfstudie correct hebt gevolgd, moet u 83 rijen en 2 kolommen in uw sinkmap hebben geschreven. U kunt controleren of de gegevens juist zijn door uw blobopslag te controleren.

## <a name="next-steps"></a>Volgende stappen

Met de pijplijn in deze zelfstudie wordt een gegevensstroom uitgevoerd die de gemiddelde classificatie van comedies van 1910 tot 2000 samenvoegt en de gegevens naar ADLS schrijft. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een data factory maken.
> * Maak een pijplijn met een Gegevensstroom activiteit.
> * Bouw een toewijzingsgegevensstroom met vier transformaties.
> * De uitvoering van de pijplijn testen.
> * Een Gegevensstroom bewaken

Meer informatie over de [expressietaal van de gegevensstroom.](data-flow-expression-functions.md)
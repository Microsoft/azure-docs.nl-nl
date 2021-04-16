---
title: Gegevens transformeren met een Azure Data Factory beheerde gegevensstroom voor toewijzing van virtuele netwerken
description: Deze zelfstudie bevat stapsgewijs instructies voor het gebruik van Azure Data Factory gegevens te transformeren met toewijzingsgegevensstromen.
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2021
ms.openlocfilehash: ad101bee84256662d1436ba8d8a49304aecb9129
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518271"
---
# <a name="transform-data-securely-by-using-mapping-data-flow"></a>Gegevens veilig transformeren met behulp van toewijzingsgegevensstroom

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Zie [Inleiding tot Azure Data Factory](./introduction.md) als u niet bekend bent met Azure Data Factory.

In deze zelfstudie gebruikt u de gebruikersinterface (UI) van Data Factory om een pijplijn te maken waarmee gegevens worden gekopieerd en getransformeerd van een Azure Data Lake Storage Gen2-bron naar een *Data Lake Storage Gen2-sink (beide* voor toegang tot alleen geselecteerde netwerken) met behulp van toewijzingsgegevensstroom in [Data Factory Managed Virtual Network](managed-virtual-network-private-endpoint.md). U kunt het configuratiepatroon in deze zelfstudie uitbreiden wanneer u gegevens transformeert met behulp van een toewijzingsgegevensstroom.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
>
> * Een data factory maken.
> * Maak een pijplijn met een gegevensstroomactiviteit.
> * Bouw een toewijzingsgegevensstroom met vier transformaties.
> * De uitvoering van de pijplijn testen.
> * Een gegevensstroomactiviteit bewaken.

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**. Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* **Azure-opslagaccount**. U gebruikt Data Lake Storage als *bron-* en *sinkgegevensopslag.* Als u geen opslagaccount hebt, raadpleegt u het artikel [Een opslagaccount maken](../storage/common/storage-account-create.md?tabs=azure-portal) om een account te maken. *Zorg ervoor dat het opslagaccount alleen toegang toestaat vanuit geselecteerde netwerken.* 

Het bestand dat we in deze zelfstudie transformeren, is moviesDB.csv, dat u kunt vinden op deze [GitHub-inhoudssite.](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv) Als u het bestand wilt ophalen uit GitHub, kopieert u de inhoud naar een teksteditor van uw keuze om het lokaal op te slaan als een CSV-bestand. Zie Blobs uploaden met de Azure Portal om het bestand naar uw [opslagaccount te Azure Portal.](../storage/blobs/storage-quickstart-blobs-portal.md) De voorbeelden verwijzen naar een container met de **naam sample-data.**

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

In deze stap maakt u een data factory en opent u de Data Factory ui om een pijplijn te maken in de data factory.

1. Open Microsoft Edge of Google Chrome. Op dit moment wordt de Data Factory-gebruikersinterface alleen ondersteund in de webbrowsers Microsoft Edge en Google Chrome.
1. Selecteer in het linkermenu **Een resource maken** > **Analyse** > **Data Factory**.
1. Voer op de pagina **Nieuwe data factory** **ADFTutorialDataFactory** in bij **Naam**.

   De naam van de data factory moet *wereldwijd uniek* zijn. Als u een foutbericht ontvangt over de naamwaarde, voert u een andere naam in voor de data factory (bijvoorbeeld uwnaamADFTutorialDataFactory). Zie [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.

1. Selecteer het Azure-**abonnement** waarin u de data factory wilt maken.
1. Voer een van de volgende stappen uit voor **Resourcegroep**:

    * Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst.
    * Selecteer **Nieuwe maken** en voer de naam van een resourcegroep in. 
         
    Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie. 
1. Selecteer **V2** onder **Versie**.
1. Selecteer onder **Locatie** een locatie voor de data factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. Gegevensopslag (bijvoorbeeld Azure Storage en Azure SQL Database) en berekeningen (bijvoorbeeld Azure HDInsight) die door de data factory worden gebruikt, kunnen zich in andere regio's.

1. Selecteer **Maken**.
1. Als het maken is voltooid, ziet u de melding in het meldingencentrum. Selecteer **Naar de resource gaan** om naar de pagina **Data Factory** te gaan.
1. Selecteer de tegel **Maken en controleren** om de Data Factory-gebruikersinterface te openen op een afzonderlijk tabblad.

## <a name="create-an-azure-ir-in-data-factory-managed-virtual-network"></a>Een Azure IR maken in Data Factory Managed Virtual Network

In deze stap maakt u een Azure IR en Data Factory Beheerde Virtual Network.

1. Ga in Data Factory portal naar **Beheren** en selecteer **Nieuw om** een nieuw Azure IR.

   ![Schermopname van het maken van een nieuw Azure IR.](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. Kies op **de pagina Integratieruntime** instellen welke integratieruntime moet worden gemaakt op basis van de vereiste mogelijkheden. In deze zelfstudie selecteert **u Azure, zelf-hostend** en klikt u vervolgens op **Doorgaan.** 
1. Selecteer **Azure en** klik vervolgens op Doorgaan **om** een Azure Integration-runtime te maken.

   ![Schermopname van een nieuw Azure IR.](./media/tutorial-copy-data-portal-private/azure-ir.png)

1. Selecteer onder **Configuratie van virtueel netwerk (preview)** de optie **Inschakelen**.

   ![Schermopname van het inschakelen van een Azure IR.](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)

1. Selecteer **Maken**.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Een pijplijn maken met een gegevensstroomactiviteit

In deze stap maakt u een pijplijn die een gegevensstroomactiviteit bevat.

1. Selecteer op de pagina **Aan de slag** de optie **Pijplijn maken**.

   ![Schermopname waarin het maken van een pijplijn wordt weergegeven.](./media/doc-common-process/get-started-page.png)

1. Voer in het deelvenster Eigenschappen voor de pijplijn **TransformMovies in** als naam van de pijplijn.
1. Vouw verplaatsen **en transformeren** uit in het **deelvenster Activiteiten.** Sleep de **Gegevensstroom** van het deelvenster naar het pijplijn-canvas.

1. Selecteer in de pop-up **Gegevensstroom** toevoegen de optie **Nieuwe gegevensstroom maken** en selecteer vervolgens **Toewijzing Gegevensstroom**. Selecteer **OK** wanneer u klaar bent.

    ![Schermopname van Toewijzingstoewijzing Gegevensstroom.](media/tutorial-data-flow-private/mapping-dataflow.png)

1. Noem de gegevensstroom **TransformMovies** in het deelvenster Eigenschappen.
1. Schuif in de bovenste balk van het pijplijnvas de **schuifregelaar Gegevensstroom foutopsporing** in. Met de foutopsporingsmodus kunt u transformatielogica interactief testen op een live Spark-cluster. Gegevensstroom het 5-7 minuten duren voordat clusters zijn opgewarmd en gebruikers wordt aangeraden om eerst foutopsporing in te schakelen als ze van plan zijn om Gegevensstroom ontwikkelen. Zie Foutopsporingsmodus [voor meer informatie.](concepts-data-flow-debug-mode.md)

    ![Schermopname van de schuifregelaar Gegevensstroom voor foutopsporing.](media/tutorial-data-flow-private/dataflow-debug.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Transformatielogica bouwen in het gegevensstroom-canvas

Nadat u de gegevensstroom hebt gemaakt, wordt u automatisch naar het gegevensstroomvas verzonden. In deze stap bouwt u een gegevensstroom die het moviesDB.csv-bestand in Data Lake Storage gebruikt en de gemiddelde classificatie van comedies van 1910 tot 2000 aggregeert. Vervolgens schrijft u dit bestand terug naar Data Lake Storage.

### <a name="add-the-source-transformation"></a>De brontransformatie toevoegen

In deze stap stelt u een Data Lake Storage Gen2 als bron.

1. Voeg in het canvas van de gegevensstroom een bron toe door het **vak Bron toevoegen te** selecteren.

1. Noem uw bron **MoviesDB.** Selecteer **Nieuw om** een nieuwe bronset te maken.

1. Selecteer **Azure Data Lake Storage Gen2** en selecteer vervolgens **Doorgaan.**

1. Selecteer **DelimitedText** en selecteer vervolgens **Doorgaan.**

1. Noem uw **gegevensset MoviesDB.** Selecteer Nieuw in de vervolgkeuzekeuzeop voor **de gekoppelde service.**

1. Geef in het scherm voor het maken van de gekoppelde service uw Data Lake Storage Gen2 gekoppelde service **ADLSGen2** op en geef uw verificatiemethode op. Voer vervolgens uw verbindingsreferenties in. In deze zelfstudie gebruiken we accountsleutel **om verbinding** te maken met ons opslagaccount. 

1. Zorg ervoor dat u **Interactieve creatie** inschakelt. Het kan een minuut duren om deze functie in te stellen.

    ![Schermopname waarin interactieve creatie wordt weergegeven.](./media/tutorial-data-flow-private/interactive-authoring.png)

1. Selecteer **Verbinding testen**. Dit moet mislukken omdat het opslagaccount geen toegang tot het account inschakelen zonder het maken en goedkeuren van een privé-eindpunt. In het foutbericht wordt een koppeling weergegeven om een privé-eindpunt te maken dat u kunt volgen om een beheerd privé-eindpunt te maken. U kunt ook rechtstreeks naar het tabblad Beheren gaan **en** de instructies in deze sectie [volgen](#create-a-managed-private-endpoint) om een beheerd privé-eindpunt te maken.

1. Houd het dialoogvenster geopend en ga vervolgens naar uw opslagaccount.

1. Volg de instructies in [deze sectie](#approval-of-a-private-link-in-a-storage-account) om de persoonlijke koppeling goed te keuren.

1. Ga terug naar het dialoogvenster. Selecteer **Test de verbinding** opnieuw en selecteer vervolgens **Maken** om de gekoppelde service te implementeren.

1. Voer in het scherm voor het maken van de gegevensset in waar uw bestand zich bevindt onder het **veld Bestandspad.** In deze zelfstudie bevindt het bestand moviesDB.csv zich in de container **sample-data**. Omdat het bestand headers bevat, selecteert u het selectievakje **Eerste rij als koptekst.** Selecteer **Uit verbinding/archief om** het headerschema rechtstreeks vanuit het bestand in de opslag te importeren. Selecteer **OK** wanneer u klaar bent.

    ![Schermopname van het bronpad.](media/tutorial-data-flow-private/source-file-path.png)

1. Als uw foutopsporingscluster is gestart, gaat  u naar het tabblad **Gegevensvoorbeeld** van de brontransformatie en selecteert u Vernieuwen om een momentopname van de gegevens op te halen. U kunt het voorbeeld van gegevens gebruiken om te controleren of uw transformatie correct is geconfigureerd.

    ![Schermopname van het tabblad Gegevensvoorbeeld.](media/tutorial-data-flow-private/data-preview.png)

#### <a name="create-a-managed-private-endpoint"></a>Een beheerd privé-eindpunt maken

Als u de hyperlink niet hebt gebruikt tijdens het testen van de voorgaande verbinding, volgt u het pad. Nu moet u een beheerd privé-eindpunt maken dat u verbindt met de gekoppelde service die u hebt gemaakt.

1. Ga naar het tabblad **Beheren**.

   > [!NOTE]
   > Het tabblad **Beheren** is mogelijk niet beschikbaar voor alle exemplaren van Data Factory. Als u het niet ziet, kunt u toegang krijgen tot privé-eindpunten door **Auteur** > **Verbindingen** > **Privé-eindpunt** te selecteren.

1. Ga naar het gedeelte **Beheerde privé-eindpunten**.
1. Selecteer **+ Nieuwe** onder **Beheerde privé-eindpunten**.

    ![Schermafbeelding met de knoppen Beheerde privé-eindpunten en Nieuw.](./media/tutorial-data-flow-private/new-managed-private-endpoint.png) 

1. Selecteer de **Azure Data Lake Storage Gen2** in de lijst en selecteer **Doorgaan.**
1. Voer de naam in van het opslagaccount dat u hebt gemaakt.
1. Selecteer **Maken**.
1. Na enkele seconden wordt voor de privékoppeling een goedkeuring vereist.
1. Selecteer het privé-eindpunt dat u hebt gemaakt. U ziet een hyperlink waarmee u het privé-eindpunt kunt goedkeuren op het niveau van het opslagaccount.

    ![Schermopname van het deelvenster Privé-eindpunt beheren.](./media/tutorial-data-flow-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>Goedkeuring van een privékoppeling in een opslagaccount

1. Ga in het opslagaccount naar **Privé-eindpuntverbindingen** in het gedeelte **Instellingen**.

1. Schakel het selectievakje in bij het privé-eindpunt dat u hebt gemaakt en selecteer **Goedkeuren.**

    ![Schermopname van de knop Goedkeuren van het privé-eindpunt.](./media/tutorial-data-flow-private/approve-private-endpoint.png)

1. Voeg een beschrijving toe en selecteer **ja**.
1. Ga terug naar het gedeelte **Beheerde privé-eindpunten** van het tabblad **Beheren** in Data Factory.
1. Na ongeveer een minuut wordt de goedkeuring voor uw privé-eindpunt weergegeven.

### <a name="add-the-filter-transformation"></a>De filtertransformatie toevoegen

1. Selecteer naast het bron-knooppunt op het gegevensstroomvas het pluspictogram om een nieuwe transformatie toe te voegen. De eerste transformatie die u toevoegt, is **filter**.

    ![Schermopname van het toevoegen van een filter.](media/tutorial-data-flow-private/add-filter.png)
1. Noem de filtertransformatie **FilterYears.** Selecteer het expressievak naast **Filteren op om** de opbouwer van de expressie te openen. Hier geeft u de filtervoorwaarde op.

    ![Schermopname van FilterYears.](media/tutorial-data-flow-private/filter-years.png)
1. Met de opbouwer van gegevensstroomexpressie kunt u interactief expressies bouwen voor gebruik in verschillende transformaties. Expressies kunnen ingebouwde functies, kolommen uit het invoerschema en door de gebruiker gedefinieerde parameters bevatten. Zie Opbouw van gegevensstroomexpressie voor meer informatie over het bouwen [van expressies.](./concepts-data-flow-expression-builder.md)

    * In deze zelfstudie wilt u films filteren in het genre uit de jaren 1910 en 2000. Omdat het jaar momenteel een tekenreeks is, moet u deze converteren naar een geheel getal met behulp van de ```toInteger()``` functie . Gebruik de operatoren groter dan of gelijk aan (>=) en kleiner dan of gelijk aan (<=) om te vergelijken met de letterlijke jaarwaarden 1910 en 2000. Deze expressies samenbrengen met de operator en (&&). De expressie komt als volgt uit:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    * Als u wilt weten welke films comedies zijn, kunt u de functie gebruiken om het patroon ```rlike()``` 'Ën' in de kolomkolom te vinden. Maak de rlike-expressie samen met de jaarvergelijking om het volgende te krijgen:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    * Als u een actief foutopsporingscluster  hebt, kunt u uw logica controleren door Vernieuwen te selecteren om de uitvoer van de expressie weer te geven in vergelijking met de gebruikte invoer. Er is meer dan één juist antwoord op hoe u deze logica kunt uitvoeren met behulp van de expressietaal van de gegevensstroom.

        ![Schermopname van de filterexpressie.](media/tutorial-data-flow-private/filter-expression.png)

    * Selecteer **Opslaan en voltooien** nadat u klaar bent met uw expressie.

1. Haal een **voorbeeld van gegevens op** om te controleren of het filter correct werkt.

    ![Schermopname van het gefilterde voorbeeld van gegevens.](media/tutorial-data-flow-private/filter-data.png)

### <a name="add-the-aggregate-transformation"></a>De aggregatietransformatie toevoegen

1. De volgende transformatie die u toevoegt, is een **Aggregatietransformatie** onder **Schema-modifier**.

    ![Schermopname van het toevoegen van de aggregatie.](media/tutorial-data-flow-private/add-aggregate.png)
1. Noem de aggregatietransformatie **AggregateComedyRating.** Selecteer op **het tabblad** Groeperen op de optie **Jaar** in de vervolgkeuzevak om de aggregaties te groeperen op het jaar dat de film uit is gekomen.

    ![Schermopname van de aggregatiegroep.](media/tutorial-data-flow-private/group-by-year.png)
1. Ga naar het **tabblad Statistische** gegevens. Noem in het linkertekstvak de aggregatiekolom **AverageComedyRating.** Selecteer het vak met de juiste expressie om de samengetagde expressie in te voeren via de opbouwer van de expressie.

    ![Schermopname van de naam van de samengevoegde kolom.](media/tutorial-data-flow-private/name-column.png)
1. Gebruik de statistische functie om het gemiddelde **van de kolom Waardering** op te ```avg()``` halen. Omdat **Waardering** een tekenreeks is en numerieke invoer op zich neemt, moeten we de waarde converteren ```avg()``` naar een getal via de functie ```toInteger()``` . Deze expressie ziet er als volgende uit:

    ```avg(toInteger(Rating))```

1. Selecteer **Opslaan en voltooien** nadat u klaar bent.

    ![Schermopname van het opslaan van de aggregatie.](media/tutorial-data-flow-private/save-aggregate.png)
1. Ga naar het **tabblad Gegevensvoorbeeld** om de transformatie-uitvoer weer te geven. U ziet dat er slechts twee kolommen zijn: **year** en **AverageComedyRating.**

### <a name="add-the-sink-transformation"></a>De sinktransformatie toevoegen

1. Vervolgens wilt u een **Sink-transformatie** toevoegen onder **Destination**.

    ![Schermopname van het toevoegen van een sink.](media/tutorial-data-flow-private/add-sink.png)
1. Noem uw sink **Sink**. Selecteer **Nieuw om** uw sink-gegevensset te maken.

    ![Schermopname van het maken van een sink.](media/tutorial-data-flow-private/create-sink.png)
1. Selecteer op **de pagina** Nieuwe gegevensset **Azure Data Lake Storage Gen2** selecteer vervolgens **Doorgaan.**

1. Selecteer op **de pagina Indeling** selecteren de optie **DelimitedText** en selecteer vervolgens **Doorgaan.**

1. Noem uw **sink-gegevensset MoviesSink.** Kies voor gekoppelde service dezelfde gekoppelde **ADLSGen2-service** die u hebt gemaakt voor brontransformatie. Voer een uitvoermap in om uw gegevens naar te schrijven. In deze zelfstudie schrijven we naar de **mapuitvoer** in de container **sample-data**. De map hoeft niet vooraf te bestaan en kan dynamisch worden gemaakt. Schakel het **selectievakje Eerste rij als koptekst** in en selecteer Geen **bij** **Schema importeren.** Selecteer **OK**.

    ![Schermopname van het sinkpad.](media/tutorial-data-flow-private/sink-file-path.png)

U bent nu klaar met het bouwen van uw gegevensstroom. U bent klaar om deze in uw pijplijn uit te voeren.

## <a name="run-and-monitor-the-data-flow"></a>De gegevensstroom uitvoeren en bewaken

U kunt fouten opsporen in een pijplijn voordat u deze publiceert. In deze stap activeert u een foutopsporingsrun van de gegevensstroompijplijn. Hoewel in het voorbeeld van gegevens geen gegevens worden geschreven, worden bij een foutopsporingsrun gegevens naar uw sinkbestemming geschreven.

1. Ga naar het pijplijn-canvas. Selecteer **Fouten opsporen om** een foutopsporingsrun te activeren.

1. Pijplijnopsporing van gegevensstroomactiviteiten maakt gebruik van het actieve foutopsporingscluster, maar het initialiseren duurt nog minstens een minuut. U kunt de voortgang volgen via het **tabblad** Uitvoer. Nadat de run is geslaagd, selecteert u het pictogram van een bril voor details van de run.

1. Op de detailpagina ziet u het aantal rijen en de tijd die is besteed aan elke transformatiestap.

    ![Schermopname van een controle-run.](media/tutorial-data-flow-private/run-details.png)
1. Selecteer een transformatie voor gedetailleerde informatie over de kolommen en partitionering van de gegevens.

Als u deze zelfstudie correct hebt gevolgd, moet u 83 rijen en 2 kolommen in uw sinkmap hebben geschreven. U kunt controleren of de gegevens juist zijn door uw blobopslag te controleren.

## <a name="summary"></a>Samenvatting

In deze zelfstudie hebt u de Data Factory-gebruikersinterface gebruikt om een pijplijn te maken waarmee gegevens worden gekopieerd en getransformeerd van een Data Lake Storage Gen2-bron naar een Data Lake Storage Gen2-sink (beide bieden toegang tot alleen geselecteerde netwerken) met behulp van toewijzingsgegevensstroom in [Data Factory Managed Virtual Network](managed-virtual-network-private-endpoint.md).
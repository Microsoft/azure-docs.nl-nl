---
title: Gegevens transformeren met een Azure Data Factory stroom voor het toewijzen van beheerde virtuele netwerk gegevens
description: In deze zelf studie vindt u stapsgewijze instructies voor het gebruik van Azure Data Factory om gegevens te transformeren met toewijzing van gegevens stromen.
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/15/2021
ms.openlocfilehash: a5c93244862d72f9c8ea2928c41e699302b1752b
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98249432"
---
# <a name="transform-data-securely-by-using-mapping-data-flow"></a>Gegevens veilig transformeren met behulp van gegevens stroom toewijzen

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Zie [Inleiding tot Azure Data Factory](./introduction.md) als u niet bekend bent met Azure Data Factory.

In deze zelf studie gebruikt u de Data Factory gebruikers interface (UI) om een pijp lijn te maken waarmee gegevens *van een Azure data Lake Storage Gen2 bron worden gekopieerd en getransformeerd naar een Data Lake Storage Gen2 Sink (zowel toegang verlenen aan geselecteerde netwerken)* via toewijzing van gegevens stroom in [Data Factory beheerde Virtual Network](managed-virtual-network-private-endpoint.md). U kunt in deze zelf studie het configuratie patroon uitbreiden wanneer u gegevens transformeert met behulp van gegevens stroom toewijzen.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
>
> * Een data factory maken.
> * Maak een pijp lijn met een gegevens stroom activiteit.
> * Bouw een toewijzings gegevens stroom met vier trans formaties.
> * De uitvoering van de pijplijn testen.
> * Een gegevens stroom activiteit bewaken.

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**. Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* **Azure-opslagaccount**. U gebruikt Data Lake Storage als *bron* -en *sink* -gegevens opslag. Als u geen opslagaccount hebt, raadpleegt u het artikel [Een opslagaccount maken](../storage/common/storage-account-create.md?tabs=azure-portal) om een account te maken. *Zorg ervoor dat het opslagaccount alleen toegang toestaat vanuit geselecteerde netwerken.* 

Het bestand dat in deze zelf studie wordt getransformeerd, is moviesDB.csv, dat u kunt vinden op deze [github-inhouds site](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Als u het bestand wilt ophalen uit GitHub, kopieert u de inhoud naar een tekst editor van uw keuze om deze lokaal op te slaan als een CSV-bestand. Als u het bestand naar uw opslag account wilt uploaden, raadpleegt u [blobs uploaden met de Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). In de voor beelden wordt verwezen naar een container met de naam **Sample-Data**.

## <a name="create-a-data-factory"></a>Een data factory maken

In deze stap maakt u een data factory en opent u de Data Factory-gebruikers interface om een pijp lijn te maken in de data factory.

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
1. Selecteer onder **Locatie** een locatie voor de data factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. Gegevens archieven (bijvoorbeeld Azure Storage en Azure SQL Database) en berekeningen (zoals Azure HDInsight) die door de data factory worden gebruikt, kunnen zich in andere regio's bevindt.

1. Selecteer **Maken**.
1. Als het maken is voltooid, ziet u de melding in het meldingencentrum. Selecteer **Naar de resource gaan** om naar de pagina **Data Factory** te gaan.
1. Selecteer de tegel **Maken en controleren** om de Data Factory-gebruikersinterface te openen op een afzonderlijk tabblad.

## <a name="create-an-azure-ir-in-data-factory-managed-virtual-network"></a>Een Azure IR maken in Data Factory beheerde Virtual Network

In deze stap maakt u een Azure IR en schakelt u Data Factory beheerde Virtual Network in.

1. Ga in de Data Factory Portal naar **beheren** en selecteer **Nieuw** om een nieuwe Azure IR te maken.

   ![Scherm opname van het maken van een nieuwe Azure IR.](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. Kies op de pagina **Integration runtime Setup** welke Integration runtime u wilt maken op basis van de vereiste mogelijkheden. In deze zelf studie selecteert u **Azure, zelf-hostend** en klikt u vervolgens op **door gaan**. 
1. Selecteer **Azure** en klik vervolgens op **door gaan** om een Azure Integration runtime te maken.

   ![Scherm opname waarin een nieuwe Azure IR wordt weer gegeven.](./media/tutorial-copy-data-portal-private/azure-ir.png)

1. Selecteer onder **Configuratie van virtueel netwerk (preview)** de optie **Inschakelen**.

   ![Scherm opname van het inschakelen van een nieuwe Azure IR.](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)

1. Selecteer **Maken**.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Een pijp lijn maken met een activiteit voor gegevens stromen

In deze stap maakt u een pijp lijn die een gegevens stroom activiteit bevat.

1. Selecteer op de pagina **Aan de slag** de optie **Pijplijn maken**.

   ![Schermopname waarin het maken van een pijplijn wordt weergegeven.](./media/doc-common-process/get-started-page.png)

1. Voer in het deel venster Eigenschappen voor de pijp lijn **TransformMovies** in voor de naam van de pijp lijn.
1. Schuif in de bovenste balk van de fabriek de schuif regelaar voor **fout opsporing van gegevens stromen** aan. In de modus voor fout opsporing kunt u de transformatie logica interactief testen op een live Spark-cluster. Gegevens stroom clusters nemen vijf tot zeven minuten in beslag. Schakel de **fout opsporing van gegevens stromen** eerst in als u de ontwikkeling van gegevens stromen wilt plannen. Zie [debug mode (foutopsporingsmodus](./concepts-data-flow-debug-mode.md)) voor meer informatie.

    ![Scherm opname van de schuif regelaar voor fout opsporing van gegevens stromen.](media/tutorial-data-flow-private/dataflow-debug.png)
1. Vouw in het deel venster **activiteiten** het item **verplaatsen en transformeren** uit. Sleep de activiteit **gegevens stroom** van het deel venster naar het pijp lijn-canvas.

1. Selecteer in het pop-upvenster **gegevens stroom toevoegen** de optie **nieuwe gegevens stroom maken** en selecteer vervolgens **gegevens stroom toewijzen**. Selecteer **OK** wanneer u klaar bent.

    ![Scherm opname van de toewijzings gegevens stroom.](media/tutorial-data-flow-private/mapping-dataflow.png)

1. Geef uw gegevensstroom **TransformMovies** een naam in het deel venster Eigenschappen.

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Transformatie logica bouwen in het canvas voor gegevens stromen

Nadat u de gegevens stroom hebt gemaakt, wordt u automatisch naar het canvas voor gegevens stromen verzonden. In deze stap maakt u een gegevens stroom die het moviesDB.csv bestand in Data Lake Storage neemt en de gemiddelde waardering van comedies van 1910 tot 2000 samenvoegt. Vervolgens schrijft u dit bestand terug naar Data Lake Storage.

### <a name="add-the-source-transformation"></a>De bron transformatie toevoegen

In deze stap stelt u Data Lake Storage Gen2 in als een bron.

1. Voeg in het canvas voor gegevens stroom een bron toe door het vak **bron toevoegen** te selecteren.

1. Geef uw bron **MoviesDB** een naam. Selecteer **Nieuw** om een nieuwe bron-gegevensset te maken.

1. Selecteer **Azure data Lake Storage Gen2** en selecteer vervolgens **door gaan**.

1. Selecteer **DelimitedText** en selecteer vervolgens **door gaan**.

1. Geef uw gegevensset een naam **MoviesDB**. Selecteer in de vervolg keuzelijst gekoppelde service de optie **Nieuw**.

1. Geef in het scherm gekoppelde service maken de naam op van de Data Lake Storage Gen2 gekoppelde service **ADLSGen2** en de verificatie methode op. Voer vervolgens uw verbindings referenties in. In deze zelf studie gebruiken we de **account sleutel** om verbinding te maken met het opslag account. 

1. Zorg ervoor dat u **Interactieve creatie** inschakelt. Het kan een minuut duren voordat deze is ingeschakeld.

    ![Schermopname waarin interactieve creatie wordt weergegeven.](./media/tutorial-data-flow-private/interactive-authoring.png)

1. Selecteer **Verbinding testen**. Het moet mislukken omdat het opslag account geen toegang tot de server inschakelt zonder een persoonlijk eind punt te maken en goed te keuren. In het foutbericht wordt een koppeling weergegeven om een privé-eindpunt te maken dat u kunt volgen om een beheerd privé-eindpunt te maken. U kunt ook rechtstreeks naar het tabblad **beheren** gaan en de instructies in [deze sectie](#create-a-managed-private-endpoint) volgen om een beheerd privé-eind punt te maken.

1. Houd het dialoogvenster geopend en ga vervolgens naar uw opslagaccount.

1. Volg de instructies in [deze sectie](#approval-of-a-private-link-in-a-storage-account) om de persoonlijke koppeling goed te keuren.

1. Ga terug naar het dialoogvenster. Selecteer **Test de verbinding** opnieuw en selecteer vervolgens **Maken** om de gekoppelde service te implementeren.

1. Voer in het scherm gegevensset maken de locatie in waar uw bestand zich bevindt in het veld **bestandspad** . In deze zelf studie wordt het bestand moviesDB.csv bevindt zich in de container **-voorbeeld gegevens**. Schakel het selectie vakje **eerste rij als koptekst** in, omdat het bestand kopteksten bevat. Selecteer **in verbinding/archief** om het header-schema rechtstreeks te importeren uit het bestand in de opslag. Selecteer **OK** wanneer u klaar bent.

    ![Scherm afbeelding waarin het bronpad wordt weer gegeven.](media/tutorial-data-flow-private/source-file-path.png)

1. Als het cluster voor fout opsporing is gestart, gaat u naar het tabblad **voor beeld van gegevens** van de bron transformatie en selecteert u **vernieuwen** om een moment opname van de gegevens op te halen. U kunt het voor beeld van de gegevens gebruiken om te controleren of uw trans formatie correct is geconfigureerd.

    ![Scherm opname van het tabblad voor het voor beeld van de gegevens.](media/tutorial-data-flow-private/data-preview.png)

#### <a name="create-a-managed-private-endpoint"></a>Een beheerd privé-eindpunt maken

Als u de Hyper link niet hebt gebruikt tijdens het testen van de voor gaande verbinding, volgt u het pad. Nu moet u een beheerd privé-eindpunt maken dat u verbindt met de gekoppelde service die u hebt gemaakt.

1. Ga naar het tabblad **Beheren**.

   > [!NOTE]
   > Het tabblad **Beheren** is mogelijk niet beschikbaar voor alle exemplaren van Data Factory. Als u het niet ziet, kunt u toegang krijgen tot privé-eindpunten door **Auteur** > **Verbindingen** > **Privé-eindpunt** te selecteren.

1. Ga naar het gedeelte **Beheerde privé-eindpunten**.
1. Selecteer **+ Nieuwe** onder **Beheerde privé-eindpunten**.

    ![Schermafbeelding met de knoppen Beheerde privé-eindpunten en Nieuw.](./media/tutorial-data-flow-private/new-managed-private-endpoint.png) 

1. Selecteer de tegel **Azure data Lake Storage Gen2** in de lijst en selecteer **door gaan**.
1. Voer de naam in van het opslagaccount dat u hebt gemaakt.
1. Selecteer **Maken**.
1. Na enkele seconden wordt voor de privékoppeling een goedkeuring vereist.
1. Selecteer het privé-eindpunt dat u hebt gemaakt. U ziet een hyperlink waarmee u het privé-eindpunt kunt goedkeuren op het niveau van het opslagaccount.

    ![Scherm opname van het deel venster privé-eind punt beheren.](./media/tutorial-data-flow-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>Goedkeuring van een privékoppeling in een opslagaccount

1. Ga in het opslagaccount naar **Privé-eindpuntverbindingen** in het gedeelte **Instellingen**.

1. Schakel het selectie vakje in op het persoonlijke eind punt dat u hebt gemaakt en selecteer **goed keuren**.

    ![Scherm opname van de knop voor goed keuring van privé-eind punten.](./media/tutorial-data-flow-private/approve-private-endpoint.png)

1. Voeg een beschrijving toe en selecteer **ja**.
1. Ga terug naar het gedeelte **Beheerde privé-eindpunten** van het tabblad **Beheren** in Data Factory.
1. Na ongeveer een minuut ziet u dat de goed keuring voor uw persoonlijke eind punt wordt weer gegeven.

### <a name="add-the-filter-transformation"></a>De filter transformatie toevoegen

1. Selecteer het plus pictogram naast het bron knooppunt op het canvas voor gegevens stromen om een nieuwe trans formatie toe te voegen. De eerste trans formatie die u toevoegt, is een **filter**.

    ![Scherm opname van het toevoegen van een filter.](media/tutorial-data-flow-private/add-filter.png)
1. Geef een naam op voor de filter transformatie **FilterYears**. Selecteer het expressievak naast **filteren op** om de opbouw functie voor expressies te openen. Hier geeft u uw filter voorwaarde op.

    ![Scherm opname van FilterYears.](media/tutorial-data-flow-private/filter-years.png)
1. Met de opbouw functie voor de data flow-expressie kunt u interactief expressies bouwen voor gebruik in verschillende trans formaties. Expressies kunnen ingebouwde functies, kolommen uit het invoer schema en door de gebruiker gedefinieerde para meters bevatten. Zie de [opbouw functie voor data flow](./concepts-data-flow-expression-builder.md)-expressies voor meer informatie over het maken van expressies.

    * In deze zelf studie wilt u films filteren in het Comedy-genre dat is opgetreden tussen de jaren 1910 en 2000. Omdat het jaar momenteel een teken reeks is, moet u deze converteren naar een geheel getal met behulp van de ```toInteger()``` functie. Gebruik de Opera tors groter dan of gelijk aan (>=) en kleiner dan of gelijk aan (<=) om te vergelijken met de letterlijke jaar waarden 1910 en 2000. Voeg deze expressies samen met de operator and (&&). De expressie wordt als volgt opgehaald:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    * Als u wilt weten welke films comedies zijn, kunt u de ```rlike()``` functie gebruiken om het patroon ' comedy ' te vinden in de kolom genres. De rlike-expressie samen voegen met de jaar vergelijking om het volgende te krijgen:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    * Als u een debug-cluster hebt geactiveerd, kunt u uw logica controleren door **vernieuwen** te selecteren om de expressie-uitvoer te bekijken vergeleken met de gebruikte invoer. Er is meer dan één recht antwoord op hoe u deze logica kunt uitvoeren met behulp van de data flow-expressie taal.

        ![Scherm opname waarin de filter expressie wordt weer gegeven.](media/tutorial-data-flow-private/filter-expression.png)

    * Selecteer **opslaan en volt ooien** nadat u klaar bent met uw expressie.

1. Een **voor beeld** van een gegevens ophalen om te controleren of het filter goed werkt.

    ![Scherm opname van de gefilterde voorbeeld gegevens.](media/tutorial-data-flow-private/filter-data.png)

### <a name="add-the-aggregate-transformation"></a>De cumulatieve trans formatie toevoegen

1. De volgende trans formatie die u toevoegt, is een **statistische** trans formatie onder **schema wijzigings functie**.

    ![Scherm opname van het toevoegen van de samen voeging.](media/tutorial-data-flow-private/add-aggregate.png)
1. Geef uw aggregatie transformatie **AggregateComedyRating** een naam. Selecteer op het tabblad **groeperen op** **jaar** in de vervolg keuzelijst om de aggregaties te groeperen op het jaar dat de film is binnengekomen.

    ![Scherm opname van de groep samen voegen.](media/tutorial-data-flow-private/group-by-year.png)
1. Ga naar het tabblad **aggregaties** . Geef in het vak links de cumulatieve kolom **AverageComedyRating**. Selecteer het vak juiste expressie om de statistische expressie via de opbouw functie voor expressies in te voeren.

    ![Scherm afbeelding met de naam van de cumulatieve kolom.](media/tutorial-data-flow-private/name-column.png)
1. Gebruik de functie aggregate om het gemiddelde van de kolom **classificatie** te berekenen ```avg()``` . Omdat **classificatie** een teken reeks is en ```avg()``` een numerieke invoer opneemt, moeten we de waarde converteren naar een getal via de ```toInteger()``` functie. Deze expressie ziet er als volgt uit:

    ```avg(toInteger(Rating))```

1. Selecteer **opslaan en volt ooien** nadat u klaar bent.

    ![Scherm opname van het opslaan van de samen voeging.](media/tutorial-data-flow-private/save-aggregate.png)
1. Ga naar het tabblad voor **beeld van gegevens** om de trans formatie-uitvoer weer te geven. U ziet slechts twee kolommen, **jaar** en **AverageComedyRating**.

### <a name="add-the-sink-transformation"></a>De Sink-trans formatie toevoegen

1. Vervolgens wilt u een **sink** -trans formatie toevoegen onder **bestemming**.

    ![Scherm opname van het toevoegen van een sink.](media/tutorial-data-flow-private/add-sink.png)
1. Geef **uw Sink een naam.** Selecteer **Nieuw** om uw Sink-gegevensset te maken.

    ![Scherm opname van het maken van een sink.](media/tutorial-data-flow-private/create-sink.png)
1. Selecteer op de pagina **nieuwe gegevensset** de optie **Azure data Lake Storage Gen2** en selecteer vervolgens **door gaan**.

1. Selecteer op de pagina **indeling selecteren** de optie **DelimitedText** en selecteer vervolgens **door gaan**.

1. Geef uw Sink-gegevensset een naam **MoviesSink**. Kies voor gekoppelde service dezelfde **ADLSGen2** gekoppelde service die u hebt gemaakt voor de bron transformatie. Voer een uitvoermap in om uw gegevens naar te schrijven. In deze zelf studie schrijven we naar de **uitvoer** van de map in de container **-voorbeeld gegevens**. De map hoeft niet vooraf te bestaan en kan dynamisch worden gemaakt. Schakel het selectie vakje **eerste rij als koptekst** in en selecteer **geen** voor **import schema**. Selecteer **OK**.

    ![Scherm opname die het sink-pad weergeeft.](media/tutorial-data-flow-private/sink-file-path.png)

Nu bent u klaar met het bouwen van uw gegevens stroom. U kunt deze nu uitvoeren in de pijp lijn.

## <a name="run-and-monitor-the-data-flow"></a>De gegevens stroom uitvoeren en controleren

U kunt fouten opsporen in een pijp lijn voordat u deze publiceert. In deze stap gaat u een debug-uitvoering van de gegevensstroom pijplijn activeren. Terwijl het voor beeld van de gegevens geen gegevens schrijft, worden er door een debug run gegevens naar uw Sink-bestemming geschreven.

1. Ga naar het pijp lijn-canvas. Selecteer **fout opsporing** om een uitvoering van de fout opsporing te activeren.

1. Bij het opsporen van pijp lijn voor gegevens stroom activiteiten wordt het actieve debug-cluster gebruikt, maar het kan wel ten minste een minuut duren. U kunt de voortgang volgen via het tabblad **uitvoer** . Nadat de uitvoering is voltooid, selecteert u het bril pictogram voor details van de uitvoering.

1. Op de pagina Details ziet u het aantal rijen en de tijd die aan elke transformatie stap is besteed.

    ![Scherm afbeelding waarin een bewakings uitvoering wordt weer gegeven.](media/tutorial-data-flow-private/run-details.png)
1. Selecteer een trans formatie om gedetailleerde informatie over de kolommen en het partitioneren van de gegevens op te halen.

Als u deze zelf studie correct hebt gevolgd, moet u 83 rijen en 2 kolommen in uw map Sink hebben geschreven. U kunt controleren of de gegevens correct zijn door uw Blob-opslag te controleren.

## <a name="summary"></a>Samenvatting

In deze zelf studie hebt u de Data Factory-gebruikers interface gebruikt om een pijp lijn te maken waarmee gegevens van een Data Lake Storage Gen2 bron worden gekopieerd en getransformeerd naar een Data Lake Storage Gen2 Sink (zowel toegang tot geselecteerde netwerken toestaan) met behulp van toewijzing van gegevens stroom in [Data Factory beheerde Virtual Network](managed-virtual-network-private-endpoint.md).
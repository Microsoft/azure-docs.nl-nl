---
title: Delta Lake ETL met gegevens stromen
description: Deze zelf studie bevat stapsgewijze instructies voor het gebruik van gegevens stromen voor het transformeren en analyseren van gegevens in Delta Lake
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 02/09/2021
ms.openlocfilehash: cb8d44353e826df14ed3baab2c4ca66ffed4a569
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417285"
---
# <a name="transform-data-in-delta-lake-using-mapping-data-flows"></a>Gegevens transformeren in Delta Lake met toewijzing van gegevens stromen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Zie [Inleiding tot Azure Data Factory](introduction.md) als u niet bekend bent met Azure Data Factory.

In deze zelf studie gebruikt u het canvas voor gegevens stromen om gegevens stromen te maken waarmee u gegevens kunt analyseren en transformeren in Azure Data Lake Storage (ADLS) Gen2 en opslaan in Delta Lake.

## <a name="prerequisites"></a>Vereisten
* **Azure-abonnement**. Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* **Azure-opslagaccount**. U gebruikt ADLS-opslag als *bron* -en *sink* -gegevens opslag. Als u geen opslagaccount hebt, raadpleegt u het artikel [Een opslagaccount maken](../storage/common/storage-account-create.md) om een account te maken.

Het bestand dat u in deze zelf studie transformeert, is MoviesDB.csv, dat u [hier](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/moviesDB2.csv)kunt vinden. Als u het bestand wilt ophalen uit GitHub, kopieert u de inhoud naar een tekst editor van uw keuze om lokaal op te slaan als een CSV-bestand. Als u het bestand naar uw opslag account wilt uploaden, raadpleegt u [blobs uploaden met de Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). De voor beelden verwijzen naar een container met de naam ' Sample-Data '.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

In deze stap maakt u een data factory en opent u de Data Factory UX om een pijp lijn te maken in de data factory.

1. Open **Microsoft Edge** of **Google Chrome**. Momenteel wordt Data Factory gebruikers interface alleen ondersteund in de micro soft Edge-en Google Chrome-webbrowsers.
1. Selecteer in het menu links **een resource**-  >  **integratie** maken  >  **Data Factory**
1. Voer op de pagina **nieuw Data Factory** onder **naam** **ADFTutorialDataFactory** in
1. Selecteer het Azure-**abonnement** waarin u de data factory wilt maken.
1. Voer een van de volgende stappen uit voor **Resourcegroep**:

    a. Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst.

    b. Selecteer **Nieuwe maken** en voer de naam van een resourcegroep in. 
         
    Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie. 
1. Selecteer **V2** onder **Versie**.
1. Selecteer onder **Locatie** een locatie voor de data factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. Gegevens archieven (bijvoorbeeld Azure Storage en SQL Database) en berekeningen (zoals Azure HDInsight) die door de data factory worden gebruikt, kunnen zich in andere regio's bevindt.
1. Selecteer **Maken**.
1. Als het maken is voltooid, ziet u de melding in het meldingencentrum. Selecteer **Naar resource gaan** om naar de pagina Data factory te gaan.
1. Selecteer de tegel **Maken en controleren** om de Data Factory-gebruikersinterface te openen op een afzonderlijk tabblad.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Een pijp lijn maken met een activiteit voor gegevens stromen

In deze stap maakt u een pijp lijn die een gegevens stroom activiteit bevat.

1. Selecteer op de pagina **Aan de slag** de optie **Pijplijn maken**.

   ![Pijplijn maken](./media/doc-common-process/get-started-page.png)

1. Voer op het tabblad **Algemeen** voor de pijp lijn **DeltaLake** in als **naam** van de pijp lijn.
1. Schuif in de bovenste balk van de fabriek de schuif regelaar voor **fout opsporing van gegevens stromen** aan. In de modus voor fout opsporing kunt u de transformatie logica interactief testen op een live Spark-cluster. Gegevens stroom clusters nemen 5-7 minuten in beslag en gebruikers worden aanbevolen om debug eerst in te scha kelen als de ontwikkeling van gegevens stromen wordt gepland. Zie [debug mode (foutopsporingsmodus](concepts-data-flow-debug-mode.md)) voor meer informatie.

    ![Activiteit gegevens stroom](media/tutorial-data-flow/dataflow1.png)
1. Vouw in het deel venster **activiteiten** de accordeon **verplaatsen en transformeren** uit. Sleep de activiteit **gegevens stroom** van het deel venster naar het pijp lijn-canvas.

    ![Scherm opname van het pijplijn doek waar u de activiteit van de gegevens stroom kunt verwijderen.](media/tutorial-data-flow/activity1.png)
1. Selecteer in het pop-upvenster **gegevens stroom toevoegen** de optie **nieuwe gegevens stroom maken** en geef vervolgens een naam op voor de gegevens stroom **DeltaLake**. Klik op volt ooien wanneer u klaar bent.

    ![Scherm opname van de naam van de gegevens stroom wanneer u een nieuwe gegevens stroom maakt.](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Transformatie logica bouwen in het canvas voor gegevens stromen

In deze zelf studie worden twee gegevens stromen gegenereerd. De eerste gegevens stroom is een eenvoudige bron om te sinken om een nieuwe Delta Lake te genereren op basis van het CSV-bestand van de film. Tot slot maakt u dit stroom ontwerp hieronder om gegevens in Delta Lake bij te werken.

![Laatste stroom](media/data-flow/data-flow-tutorial-6.png "Laatste stroom")

### <a name="tutorial-objectives"></a>Zelf studie doelstellingen

1. Zet de bron van de MoviesCSV-gegevensset van boven, en maak een nieuwe Delta Lake
1. De logica voor het bijwerken van de classificaties voor 1988-films op 1 bouwen
1. Alle films verwijderen van 1950
1. Nieuwe films invoegen voor 2021 door de films te dupliceren van 1960

### <a name="start-from-a-blank-data-flow-canvas"></a>Beginnen met een leeg canvas voor gegevens stromen

1. Klik op de bron transformatie
1. Klik op nieuw naast gegevensset in het onderste deel venster 1 een nieuwe gekoppelde service maken voor ADLS Gen2
1. Tekst met scheidings tekens voor het type gegevensset kiezen
1. Naam van de gegevensset "MoviesCSV" 
1. Ga naar het MoviesCSV-bestand dat u hebt geüpload naar de bovenstaande opslag
1. Stel deze in op door komma's gescheiden en koptekst op de eerste rij bevatten 
1. Ga naar het tabblad Bron projectie en klik op gegevens typen detecteren
1. Zodra u de projectieset hebt ingesteld, kunt u door gaan 
1. Een Sink-trans formatie toevoegen
1. Delta is een inline-type gegevensset. U moet uw ADLS Gen2-opslag account aanwijzen.
   
   ![Inline gegevensset](media/data-flow/data-flow-tutorial-5.png "Inline gegevensset")

1. Kies in uw opslag container een mapnaam waar u wilt dat ADF de Delta Lake maakt
1. Ga terug naar de ontwerp functie voor pijp lijnen en klik op fouten opsporen om de pijp lijn uit te voeren in de foutopsporingsmodus met alleen deze gegevens stroom activiteit op het canvas. Hiermee wordt uw nieuwe Delta Lake in ADLS Gen2 gegenereerd.
1. Klik vanuit de fabrieks resources op nieuw > gegevens stroom 
1. Gebruik de MoviesCSV opnieuw als bron en klik nogmaals op gegevens typen detecteren
1. Een filter transformatie toevoegen aan de bron transformatie in de grafiek
1. Sta alleen film rijen toe die overeenkomen met de drie jaren die u gaat gebruiken. Dit is 1950, 1988 en 1960
1. Classificaties voor elke 1988-film bijwerken naar 1 door nu een afgeleide kolom transformatie toe te voegen aan uw filter transformatie
1. In diezelfde afgeleide kolom maakt u films voor 2021 door een bestaand jaar te nemen en het jaar te wijzigen in 2021. Laten we 1960 kiezen.
1. Zo ziet u dat uw drie afgeleide kolommen er als volgt uitzien

   ![Afgeleide kolom](media/data-flow/data-flow-tutorial-2.png "Afgeleide kolom")
   
1. ```Update, insert, delete, and upsert``` het beleid wordt gemaakt in de trans formatie Alter Row. Een alter Row trans formatie toevoegen na de afgeleide kolom.
1. Uw beleid voor het wijzigen van rijen moet er als volgt uitzien.

   ![Rij wijzigen](media/data-flow/data-flow-tutorial-3.png "Rij wijzigen")
   
1. Nu u het juiste beleid hebt ingesteld voor elk type Alter Row, controleert u of de juiste update regels zijn ingesteld voor de Sink-trans formatie

   ![Sink](media/data-flow/data-flow-tutorial-4.png "Sink")
   
1. Hier gebruiken we de Delta Lake Sink voor uw ADLS Gen2 data Lake en kunnen invoeg-, update-, verwijderingen worden toegestaan. 
1. Houd er rekening mee dat de sleutel kolommen bestaan uit een samengestelde sleutel die bestaat uit de kolom primaire en jaar kolom van de film. Dit is omdat we valse 2021-films hebben gemaakt door de 1960-rijen te dupliceren. Dit voor komt conflicten bij het opzoeken van de bestaande rijen door uniekheid op te geven.

### <a name="download-completed-sample"></a>Voor beeld downloaden voltooid
[Hier volgt een voor beeld van een oplossing voor de Delta pijplijn met een gegevens stroom voor het bijwerken/verwijderen van rijen in het Lake:](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/DeltaPipeline.zip)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [taal van de data flow-expressie](data-flow-expression-functions.md).

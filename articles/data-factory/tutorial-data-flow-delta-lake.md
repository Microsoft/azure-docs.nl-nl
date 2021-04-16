---
title: Delta Lake ETL met gegevensstromen
description: Deze zelfstudie bevat stapsgewijs instructies voor het gebruik van gegevensstromen voor het transformeren en analyseren van gegevens in Delta Lake
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 04/16/2021
ms.openlocfilehash: 4a88ed2df74d3eebb96c42e2cdc87b14153419cd
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565369"
---
# <a name="transform-data-in-delta-lake-using-mapping-data-flows"></a>Gegevens transformeren in Delta Lake met behulp van toewijzingsgegevensstromen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Zie [Inleiding tot Azure Data Factory](introduction.md) als u niet bekend bent met Azure Data Factory.

In deze zelfstudie gebruikt u het canvas voor gegevensstromen om gegevensstromen te maken waarmee u gegevens kunt analyseren en transformeren in Azure Data Lake Storage (ADLS) Gen2 en deze kunt opslaan in Delta Lake.

## <a name="prerequisites"></a>Vereisten
* **Azure-abonnement**. Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* **Azure-opslagaccount**. U gebruikt ADLS-opslag als *bron-* en *sinkgegevensopslag.* Als u geen opslagaccount hebt, raadpleegt u het artikel [Een opslagaccount maken](../storage/common/storage-account-create.md) om een account te maken.

Het bestand dat we in deze zelfstudie transformeren, is MoviesDB.csv, dat u hier [kunt vinden.](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/moviesDB2.csv) Als u het bestand wilt ophalen uit GitHub, kopieert u de inhoud naar een teksteditor van uw keuze om het lokaal op te slaan als een CSV-bestand. Zie Blobs uploaden met de Azure Portal om het bestand naar uw [opslagaccount te Azure Portal.](../storage/blobs/storage-quickstart-blobs-portal.md) De voorbeelden verwijzen naar een container met de naam 'sample-data'.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

In deze stap maakt u een data factory en opent u de Data Factory UX om een pijplijn te maken in de data factory.

1. Open **Microsoft Edge** of **Google Chrome**. Momenteel wordt Data Factory alleen ondersteund in de webbrowsers Microsoft Edge Google Chrome.
1. Selecteer in het menu links De **integratie van een resource**  >    >  **Data Factory**
1. Voer op **de pagina Data factory,** onder **Naam,** **ADFTutorialDataFactory in**
1. Selecteer het Azure-**abonnement** waarin u de data factory wilt maken.
1. Voer een van de volgende stappen uit voor **Resourcegroep**:

    a. Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst.

    b. Selecteer **Nieuwe maken** en voer de naam van een resourcegroep in. 
         
    Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie. 
1. Selecteer **V2** onder **Versie**.
1. Selecteer onder **Locatie** een locatie voor de data factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. Gegevensopslag (bijvoorbeeld Azure Storage en SQL Database) en berekeningen (bijvoorbeeld Azure HDInsight) die door de data factory worden gebruikt, kunnen zich in andere regio's.
1. Selecteer **Maken**.
1. Als het maken is voltooid, ziet u de melding in het meldingencentrum. Selecteer **Naar resource gaan** om naar de pagina Data factory te gaan.
1. Selecteer de tegel **Maken en controleren** om de Data Factory-gebruikersinterface te openen op een afzonderlijk tabblad.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Een pijplijn maken met een gegevensstroomactiviteit

In deze stap maakt u een pijplijn die een gegevensstroomactiviteit bevat.

1. Selecteer op de pagina **Aan de slag** de optie **Pijplijn maken**.

   ![Pijplijn maken](./media/doc-common-process/get-started-page.png)

1. Voer op **het** tabblad Algemeen voor de pijplijn **DeltaLake in** **bij Naam** van de pijplijn.
1. Vouw in **het** deelvenster Activiteiten de overeenkomst **Verplaatsen en transformeren** uit. Sleep de activiteit **Gegevensstroom** van het deelvenster naar het pijplijn-canvas.

    ![Schermopname van het pijplijn-canvas waar u de activiteit Gegevensstroom neerzetten.](media/tutorial-data-flow/activity1.png)
1. Selecteer in **het pop-up** Gegevensstroom toevoegen de optie Nieuwe Gegevensstroom **en** noem uw gegevensstroom **DeltaLake.** Klik op Voltooien wanneer u klaar bent.

    ![Schermopname die laat zien waar u uw gegevensstroom een naam geeft wanneer u een nieuwe gegevensstroom maakt.](media/tutorial-data-flow/activity2.png)
1. Schuif in de bovenste balk van het pijplijnvas de **schuifregelaar Gegevensstroom foutopsporing** in. Met de foutopsporingsmodus kunt u transformatielogica interactief testen op een live Spark-cluster. Gegevensstroom het 5-7 minuten duren voordat clusters zijn opgewarmd en gebruikers wordt aangeraden om eerst foutopsporing in te schakelen als ze van plan zijn om Gegevensstroom ontwikkelen. Zie Foutopsporingsmodus [voor meer informatie.](concepts-data-flow-debug-mode.md)

    ![Schermopname die laat zien waar de schuifregelaar Foutopsporing gegevensstroom is.](media/tutorial-data-flow/dataflow1.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas&quot;></a>Transformatielogica bouwen in het gegevensstroom-canvas

In deze zelfstudie genereert u twee gegevensstromen. De gegevensstroom van de film is een eenvoudige bron om een nieuwe Delta Lake te genereren op basis van het CSV-bestand met de films hierboven. Ten laatste maakt u dit stroomontwerp hieronder om gegevens in Delta Lake bij te werken.

![Laatste stroom](media/data-flow/data-flow-tutorial-6.png &quot;Laatste stroom")

### <a name="tutorial-objectives"></a>Doelstellingen van de zelfstudie

1. Neem de gegevenssetbron MoviesCSV van hierboven en maak er een nieuwe Delta Lake van
1. De logica voor het updaten van beoordelingen voor 1988-films naar '1' bouwen
1. Alle films uit 1950 verwijderen
1. Nieuwe films voor 2021 invoegen door de films uit 1960 te dupliceren

### <a name="start-from-a-blank-data-flow-canvas"></a>Beginnen met een leeg canvas voor gegevensstromen

1. Klik op de brontransformatie
1. Klik op Nieuw naast de gegevensset in het onderste deelvenster 1 Een nieuwe gekoppelde service maken voor ADLS Gen2
1. Tekst met scheidingstekens kiezen voor het type gegevensset
1. Noem de gegevensset 'MoviesCSV' 
1. Wijs het MoviesCSV-bestand aan dat u hierboven naar de opslag hebt geüpload
1. Stel deze in op door komma's scheidingstekens en neem koptekst op in de eerste rij 
1. Ga naar het tabblad voor bronprojectie en klik op Gegevenstypen detecteren
1. Zodra u de projectieset hebt ingesteld, kunt u doorgaan 
1. Een sinktransformatie toevoegen
1. Delta is een inline gegevenssettype. U moet naar uw opslagaccount ADLS Gen2 wijzen.
   
   ![Inline-gegevensset](media/data-flow/data-flow-tutorial-5.png "Inline-gegevensset")

1. Kies een mapnaam in de opslagcontainer waarin u wilt dat ADF de Delta Lake maakt
1. Terug naar de pijplijnontwerper en klik op Fouten opsporen om de pijplijn uit te voeren in de foutopsporingsmodus met alleen deze gegevensstroomactiviteit op het canvas. Hiermee wordt uw nieuwe Delta Lake in ADLS Gen2.
1. Klik vanuit Factory-resources op nieuwe > Gegevensstroom 
1. Gebruik de MoviesCSV opnieuw als bron en klik opnieuw op Gegevenstypen detecteren
1. Een filtertransformatie toevoegen aan uw brontransformatie in de grafiek
1. Alleen filmrijen toestaan die overeenkomen met de drie jaar waarmee u gaat werken, zoals 1950, 1988 en 1960
1. Werk classificaties voor elke 1988-film bij naar '1' door nu een afgeleide kolomtransformatie toe te voegen aan uw filtertransformatie
1. Maak in dezelfde afgeleide kolom films voor 2021 door een bestaand jaar te nemen en het jaar te wijzigen in 2021. Laten we 1960 kiezen.
1. Zo ziet uw drie afgeleide kolommen eruit

   ![Afgeleide kolom](media/data-flow/data-flow-tutorial-2.png "Afgeleide kolom")
   
1. ```Update, insert, delete, and upsert``` beleidsregels worden gemaakt in de transformatie Rij wijzigen. Voeg een transformatie voor rij wijzigen toe na uw afgeleide kolom.
1. Uw beleidsregels voor het wijzigen van rijen moeten er als dit uitzien.

   ![Rij wijzigen](media/data-flow/data-flow-tutorial-3.png "Rij wijzigen")
   
1. Nu u het juiste beleid hebt ingesteld voor elk rijtype wijzigen, controleert u of de juiste updateregels zijn ingesteld voor de sinktransformatie

   ![Sink](media/data-flow/data-flow-tutorial-4.png "Sink")
   
1. Hier gebruiken we de Delta Lake-sink naar uw ADLS Gen2 data lake en kunnen we invoegen, updates en verwijderen. 
1. Houd er rekening mee dat de sleutelkolommen een samengestelde sleutel zijn die bestaat uit de kolom primaire sleutel en de kolom Jaar van de film. Dit komt doordat we valse 2021-films hebben gemaakt door de rijen van 1960 te dupliceren. Dit voorkomt problemen bij het opzoek van de bestaande rijen door uniekheid te bieden.

### <a name="download-completed-sample"></a>Voltooid voorbeeld downloaden
[Hier is een voorbeeldoplossing voor de Delta-pijplijn met een gegevensstroom voor het bijwerken/verwijderen van rijen in de lake:](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/DeltaPipeline.zip)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [expressietaal van de gegevensstroom.](data-flow-expression-functions.md)

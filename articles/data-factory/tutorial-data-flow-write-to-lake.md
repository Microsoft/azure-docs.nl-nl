---
title: Best practices voor het schrijven naar bestanden naar Data Lake met gegevens stromen
description: In deze zelf studie worden aanbevolen procedures beschreven voor het schrijven naar bestanden naar Data Lake met gegevens stromen
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 04/01/2021
ms.openlocfilehash: 8010f3f95c9358714b659df5821a375bd8488ad8
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582212"
---
# <a name="best-practices-for-writing-to-files-to-data-lake-with-data-flows"></a>Best practices voor het schrijven naar bestanden naar Data Lake met gegevens stromen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Zie [Inleiding tot Azure Data Factory](introduction.md) als u niet bekend bent met Azure Data Factory.

In deze zelf studie leert u aanbevolen procedures die kunnen worden toegepast wanneer u bestanden naar ADLS Gen2 of Azure-Blob Storage schrijft met behulp van gegevens stromen. U hebt toegang tot een Azure Blob Storage-account of Azure Data Lake Store Gen2-account nodig om een Parquet-bestand te lezen en vervolgens de resultaten in mappen op te slaan.

## <a name="prerequisites"></a>Vereisten
* **Azure-abonnement**. Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* **Azure-opslagaccount**. U gebruikt ADLS-opslag als *bron* -en *sink* -gegevens opslag. Als u geen opslagaccount hebt, raadpleegt u het artikel [Een opslagaccount maken](../storage/common/storage-account-create.md) om een account te maken.

In de stappen in deze zelf studie wordt ervan uitgegaan dat u 

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

In deze stap maakt u een data factory en opent u de Data Factory UX om een pijp lijn te maken in de data factory.

1. Open **Microsoft Edge** of **Google Chrome**. Momenteel wordt Data Factory gebruikers interface alleen ondersteund in de micro soft Edge-en Google Chrome-webbrowsers.
1. Selecteer in het menu links **een resource**-  >  **integratie** maken  >  **Data Factory**
1. Voer op de pagina **nieuw Data Factory** onder **naam** **ADFTutorialDataFactory** in
1. Selecteer het Azure-**abonnement** waarin u de data factory wilt maken.
1. Voer een van de volgende stappen uit voor **Resourcegroep**:

    a. Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst.
    
    b. Selecteer **nieuwe maken** en voer de naam van een resource groep in. Zie [resource groepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md)voor meer informatie over resource groepen.
    
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

U neemt alle bron gegevens op (in deze zelf studie gebruiken we een Parquet-bestands bron) en gebruiken ze een Sink-trans formatie voor het binnenhalen van de gegevens in Parquet-indeling met behulp van de meest efficiënte mechanismen voor data Lake ETL.

![Laatste stroom](media/data-flow/parts-final.png "Laatste stroom")

### <a name="tutorial-objectives"></a>Zelf studie doelstellingen

1. Kies een van de bron gegevens sets in een nieuwe gegevens stroom
1. Gegevens stromen gebruiken om uw Sink-gegevensset effectief te partitioneren
1. Uw gepartitioneerde gegevens in ADLS Gen2 Lake folders belandten

### <a name="start-from-a-blank-data-flow-canvas"></a>Beginnen met een leeg canvas voor gegevens stromen

Eerst gaan we de gegevens stroom omgeving instellen voor elk van de hieronder beschreven mechanismen voor de aanvoer van gegevens in ADLS Gen2

1. Klik op de bron transformatie.
1. Klik op de knop Nieuw naast gegevensset in het onderste deel venster.
1. Kies een gegevensset of maak een nieuwe. Voor deze demo gebruiken we een Parquet-gegevensset met de naam gebruikers gegevens.
1. Voeg een afgeleide kolom transformatie toe. We gebruiken deze methode om de gewenste mapnamen dynamisch in te stellen.
1. Een Sink-trans formatie toevoegen.
   
### <a name="hierarchical-folder-output"></a>Hiërarchische map-uitvoer

Het is gebruikelijk om unieke waarden in uw gegevens te gebruiken om maphiërarchieën te maken om uw gegevens in het Lake te partitioneren. Dit is een zeer optimale manier om gegevens in het Lake en in Spark te organiseren en verwerken (de compute-engine achter gegevens stromen). Er zijn echter een kleine prestatie kosten om uw uitvoer op deze manier te organiseren. Bekijk een kleine afname van de totale pijplijn prestaties met behulp van dit mechanisme in de sink.

1. Ga terug naar de ontwerp functie voor gegevens stromen en bewerk de hierboven gemaakte gegevens stroom. Klik op de Sink-trans formatie.
1. Klik op optimalisatie > set partitioneren > sleutel
1. Selecteer de kolommen die u wilt gebruiken om de structuur van uw hiërarchische map in te stellen.
1. Opmerking in het onderstaande voor beeld gebruikt u jaar en maand als kolom voor de naam van de map. De resultaten zijn mappen van het formulier ```releaseyear=1990/month=8``` .
1. Wanneer u toegang hebt tot de gegevens partities in een gegevens stroom bron, gaat u naar de map op het hoogste niveau hierboven ```releaseyear``` en gebruikt u een Joker teken patroon voor elke volgende map, bijvoorbeeld: ```**/**/*.parquet```
1. Als u de gegevens waarden wilt bewerken, of zelfs als u synthetische waarden wilt genereren voor mapnamen, gebruikt u de afgeleide kolom transformatie om de waarden te maken die u in de mapnamen wilt gebruiken.

![Sleutel partitioneren](media/data-flow/key-parts.png "Sleutel partitioneren")
   
### <a name="name-folder-as-data-values"></a>Map een naam gegeven als gegevens waarden

Een iets betere manier om de Sink-techniek voor Lake data te gebruiken met behulp van ADLS Gen2 dat niet hetzelfde voor deel biedt als het partitioneren van sleutels en waarden, is ```Name folder as column data``` . Terwijl u met de belangrijkste stijl van een hiërarchische structuur gegevens segmenten gemakkelijker kunt verwerken, is deze techniek een platte mappen structuur waarmee gegevens sneller kunnen worden geschreven.

1. Ga terug naar de ontwerp functie voor gegevens stromen en bewerk de hierboven gemaakte gegevens stroom. Klik op de Sink-trans formatie.
1. Klik op optimalisatie > set partitioneren > huidige partitionering gebruiken.
1. Klik op instellingen > map naam als kolom gegevens.
1. Selecteer de kolom die u wilt gebruiken voor het genereren van mapnamen.
1. Als u de gegevens waarden wilt bewerken, of zelfs als u synthetische waarden wilt genereren voor mapnamen, gebruikt u de afgeleide kolom transformatie om de waarden te maken die u in de mapnamen wilt gebruiken.

![Optie voor map](media/data-flow/folders.png "Mappen")

### <a name="name-file-as-data-values"></a>Naam bestand als gegevens waarden

De technieken die in de bovenstaande zelf studies worden vermeld, zijn goede use cases voor het maken van apparaatcategorieën in uw data Lake. Het standaard bestandsnaam schema dat door deze technieken wordt gebruikt, is de taak-ID van de Spark-uitvoerder te gebruiken. Soms wilt u de naam van het uitvoer bestand in een tekst filter voor de gegevens stroom instellen. Deze techniek wordt alleen aanbevolen voor gebruik met kleine bestanden. Het proces van het samen voegen van partitie bestanden in één uitvoer bestand is een langlopend proces.

1. Ga terug naar de ontwerp functie voor gegevens stromen en bewerk de hierboven gemaakte gegevens stroom. Klik op de Sink-trans formatie.
1. Klik op optimalisatie > set partitioneren > enkele partitie. Dit is een vereiste voor één partitie die een knel punt tijdens het uitvoerings proces maakt wanneer bestanden worden samengevoegd. Deze optie wordt alleen aanbevolen voor kleine bestanden.
1. Klik op instellingen > naam bestand als kolom gegevens.
1. Selecteer de kolom die u wilt gebruiken voor het genereren van bestands namen.
1. Als u de gegevens waarden wilt bewerken, of zelfs als u synthetische waarden voor bestands namen wilt genereren, gebruikt u de afgeleide kolom transformatie om de waarden te maken die u in de bestands namen wilt gebruiken.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [gegevens stroom-sinks](data-flow-sink.md).

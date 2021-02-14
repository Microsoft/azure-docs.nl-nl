---
title: Incrementeel kopiëren van nieuwe bestanden op basis van een gepartitioneerde bestands naam
description: Maak een Azure-data factory en gebruik vervolgens het hulp programma Gegevens kopiëren om nieuwe bestanden op basis van een gepartitioneerde bestands naam stapsgewijs te laden.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/09/2020
ms.openlocfilehash: 8e68852434a4a8bea43b575523a60c9346b2a569
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384772"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Kopieer incrementeel nieuwe bestanden op basis van een gepartitioneerde bestands naam met behulp van het Gegevens kopiëren-hulp programma

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In deze zelfstudie gebruikt u Azure Portal om een gegevensfactory te maken. Vervolgens gebruikt u het hulp programma Gegevens kopiëren om een pijp lijn te maken waarmee nieuwe bestanden incrementeel worden gekopieerd op basis van een gepartitioneerde bestands naam van Azure Blob-opslag naar Azure Blob-opslag.

> [!NOTE]
> Zie [Inleiding tot Azure Data Factory](introduction.md) als u niet bekend bent met Azure Data Factory.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een data factory maken.
> * Het hulpprogramma Copy Data gebruiken om een pijplijn te maken.
> * De uitvoering van de pijplijn en van de activiteit controleren.

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* **Azure-opslag account**: Blob Storage gebruiken als _bron_  -en _sink_ -gegevens archief. Als u geen Azure-opslagaccount hebt, raadpleegt u de instructies in [Een opslagaccount maken](../storage/common/storage-account-create.md).

### <a name="create-two-containers-in-blob-storage"></a>Twee containers maken in Blob Storage

Bereid de Blob-opslag voor op de zelf studie door de volgende stappen uit te voeren.

1. Maak een container met de naam **Source**.  Maak een mappad als **2020/03/17/03** in uw container. Maak een leeg tekst bestand en geef het de naam als **file1.txt**. Upload het file1.txt naar de map bronpad **/2020/03/17/03** in uw opslag account.  U kunt verschillende hulpprogramma's gebruiken om deze taken uit te voeren, zoals [Azure Storage Explorer](https://storageexplorer.com/).

    ![bestanden uploaden](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)

    > [!NOTE]
    > Wijzig de mapnaam met uw UTC-tijd.  Als de huidige UTC-tijd bijvoorbeeld 3:38 uur is op 17 maart 2020, kunt u het mappad maken als **bron/2020/03/17/03/** door de regel van **bron/{jaar}/{maand}/{dag}/{uur}/**.

2. Maak een container met de naam **Destination**. U kunt verschillende hulpprogramma's gebruiken om deze taken uit te voeren, zoals [Azure Storage Explorer](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Selecteer in het linkermenu **Een resource maken** > **Integratie** > **Data Factory**:

   ![Selectie van Data Factory in het deelvenster Nieuw](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Voer op de pagina **Nieuwe data factory** **ADFTutorialDataFactory** in bij **Naam**.

    De naam van de data factory moet _wereldwijd uniek_ zijn. Mogelijk wordt het volgende foutbericht weergegeven:

   ![Foutbericht nieuwe data factory](./media/doc-common-process/name-not-available-error.png)

   Als u een foutbericht ontvangt dat betrekking heeft op de waarde die bij de naam is ingevuld, voert u een andere naam in voor de data factory. Gebruik bijvoorbeeld de naam _**uwnaam**_**ADFTutorialDataFactory**. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
3. Selecteer het Azure-**abonnement** waarin u de nieuwe data factory wilt maken.
4. Voer een van de volgende stappen uit voor **Resourcegroep**:

    a. Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst.

    b. Selecteer **Nieuwe maken** en voer de naam van een resourcegroep in. 
         
    Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.

5. Selecteer bij **Versie** de optie **V2** als de versie.
6. Selecteer bij **Locatie** de locatie voor de data factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevensarchieven (bijvoorbeeld Azure Storage en SQL Database) en -berekeningen (bijvoorbeeld Azure HDInsight) die door uw data factory worden gebruikt, kunnen zich in andere locaties of regio's bevinden.
7. Selecteer **Maken**.
8. Nadat de data factory is gemaakt, wordt de startpagina **Data Factory** weergegeven.
9. Selecteer de tegel **Author & Monitor** om de gebruikersinterface (UI) van Azure Data Factory te openen in een afzonderlijk tabblad.

    ![Startpagina van de gegevensfactory](./media/doc-common-process/data-factory-home-page.png)


## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Het hulpprogramma Copy Data gebruiken om een pijplijn te maken

1. Selecteer op de pagina **aan de slag** de **gegevens kopiëren** titel om het gegevens kopiëren-hulp programma te starten.

   ![De tegel Copy Data-hulpprogramma](./media/doc-common-process/get-started-page.png)

2. Voer de volgende stappen uit op de pagina **Eigenschappen** :

    a. Voer onder **taak naam** **DeltaCopyFromBlobPipeline** in.

    b. Selecteer onder **taak uitgebracht of taak planning** **regel matig uitvoeren volgens schema**.

    c. Onder **trigger type** selecteert u **tumblingvenstertriggers-venster**.

    d. Voer **1 uur** in onder **terugkeer patroon**.

    e. Selecteer **Next**.

    De gebruikersinterface van Data Factory maakt een pijplijn met de opgegeven taaknaam.

    ![De pagina Eigenschappen](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. Voltooi op de pagina **Brongegevensarchief** de volgende stappen:

    a. Klik op **+ Nieuwe verbinding maken** om een verbinding toe te voegen
    
    b. Selecteer in de galerie de optie Azure Blob Storage. Selecteer vervolgens Doorgaan.
    
    c. Voer op de pagina **nieuwe gekoppelde service (Azure Blob Storage)** een naam in voor de gekoppelde service. Selecteer uw Azure-abonnement en selecteer uw opslag account in de lijst naam van het **opslag account** . Test de verbinding en selecteer vervolgens **Maken**.

    ![De pagina Brongegevensarchief](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)

    d. Selecteer de zojuist gemaakte gekoppelde service op de pagina **brongegevens archief** en klik vervolgens op **volgende**.

4. Voer op de pagina **Het invoerbestand of de invoermap kiezen** de volgende stappen uit:

    a. Blader en selecteer de **bron** container en selecteer vervolgens **kiezen**.

    ![Scherm afbeelding toont het dialoog venster invoer bestand of-map kiezen.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)

    b. Selecteer bij **gedrag bij het laden van bestanden** de optie **Incrementeel laden: gepartitioneerde map/bestands namen**.

    c. Schrijf het pad naar de dynamische map als **bron/{Year}/{maand}/{Day}/{Hour}/** en wijzig de notatie zoals weer gegeven in de volgende scherm afbeelding. Controleer de **binaire kopie** en klik op **volgende**.

    ![Scherm afbeelding toont het dialoog venster invoer bestand of-map kiezen met een geselecteerde map.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     

5. Selecteer op de pagina **doel gegevens archief** de **AzureBlobStorage**, die hetzelfde opslag account als gegevens bron archief is, en klik vervolgens op **volgende**.

    ![De pagina Doelgegevensarchief](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png)
6. Voer op de pagina **het uitvoer bestand of de map kiezen** de volgende stappen uit:

    a. Blader en selecteer de **doelmap** en klik vervolgens op **kiezen**.

    b. Schrijf het pad naar de dynamische map als **doel/{Year}/{maand}/{Day}/{Hour}/** en wijzig de notatie als volgt:

    ![Scherm afbeelding toont het dialoog venster uitvoer bestand of-map kiezen.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/output-file-name.png)

    c. Klik op **Volgende**.

    ![Scherm afbeelding toont het dialoog venster uitvoer bestand of-map kiezen met de volgende selectie.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)
7. Selecteer op de pagina **Instellingen** de optie **Volgende**.

8. Bekijk op de **Overzichtspagina** de waarden voor alle instellingen en selecteer vervolgens **Volgende**.

    ![Overzichtspagina](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)

9. Selecteer op de pagina **Implementatie** de optie **Controleren** om de pijplijn of taak te controleren.
    ![De pagina Implementatie](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)

10. U ziet dat het tabblad **Controleren** aan de linkerkant automatisch wordt geselecteerd.  U moet wachten op de uitvoering van de pijp lijn wanneer deze automatisch wordt geactiveerd (ongeveer na één uur). Wanneer deze wordt uitgevoerd, klikt u op de pijplijn naam koppeling **DeltaCopyFromBlobPipeline** om de details van de activiteit weer te geven of de pijp lijn opnieuw uit te voeren. Selecteer **Vernieuwen** om de lijst te vernieuwen.

    ![Scherm afbeelding toont het deel venster pijplijn uitvoeringen.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs-1.png)
11. Omdat er slechts één activiteit (kopieeractiviteit) in de pijplijn is, ziet u slechts één vermelding in de lijst. De kolom breedte van de **bron** -en **doel** kolommen aanpassen (indien nodig) als u meer informatie wilt weer geven, kunt u zien dat het bron bestand (file1.txt) is gekopieerd van  *bron/2020/03/17/03/* naar *doel/2020/03/17/03* /met dezelfde bestands naam. 

    ![Scherm opname toont details van de pijplijn uitvoering.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)

    U kunt hetzelfde ook controleren met behulp van Azure Storage Explorer ( https://storageexplorer.com/) om de bestanden te scannen.

    ![Scherm afbeelding toont details van de pijplijn uitvoering voor het doel.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)

12. Maak nog een leeg tekst bestand met de nieuwe naam als **file2.txt**. Upload het file2.txt bestand naar het pad naar de map **Source/2020/03/17/04** in uw opslag account. U kunt verschillende hulpprogramma's gebruiken om deze taken uit te voeren, zoals [Azure Storage Explorer](https://storageexplorer.com/).

    > [!NOTE]
    > Mogelijk weet u dat er een nieuw mappad moet worden gemaakt. Wijzig de mapnaam met uw UTC-tijd.  Als de huidige UTC-tijd bijvoorbeeld 4:20 uur is op mrt. 17, 2020, kunt u het mappad maken als **bron/2020/03/17/04/** door de regel **{year}/{month}/{Day}/{Hour}/**.

13. Als u wilt terugkeren naar de weer gave **pijplijn uitvoeringen** , selecteert u **alle pijp lijnen worden uitgevoerd** en wacht u tot dezelfde pijp lijn opnieuw wordt geactiveerd na een uur.  

    ![Scherm afbeelding toont de koppeling alle pijplijn uitvoeringen om terug te gaan naar die pagina.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Selecteer de nieuwe **DeltaCopyFromBlobPipeline** -koppeling voor de tweede pijplijn uitvoering wanneer deze wordt geleverd en doe hetzelfde om de details te bekijken. U ziet dat het bron bestand (file2.txt) is gekopieerd van  **bron/2020/03/17/04/**  naar **doel/2020/03/17/04** /met dezelfde bestands naam. U kunt hetzelfde ook controleren met behulp van Azure Storage Explorer ( https://storageexplorer.com/) de bestanden in de **doel** container scannen.


## <a name="next-steps"></a>Volgende stappen
Ga naar de volgende zelfstudie voor meer informatie over het transformeren van gegevens met behulp van een Spark-cluster in Azure:

> [!div class="nextstepaction"]
>[Gegevens transformeren met behulp van een Spark-cluster in de cloud](tutorial-transform-data-spark-portal.md)

---
title: Hulp programma voor gegevens om nieuwe en bijgewerkte bestanden stapsgewijs te kopiëren
description: Maak een Azure-data factory en gebruik vervolgens het hulp programma Gegevens kopiëren om nieuwe bestanden op basis van LastModifiedDate stapsgewijs te laden.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: fda76fd16787c01b9fc35ef63473be215a2c126d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740049"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Incrementeel nieuwe en gewijzigde bestanden op basis van LastModifiedDate kopiëren met behulp van het hulp programma Gegevens kopiëren

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In deze zelf studie gebruikt u de Azure Portal om een data factory te maken. Vervolgens gebruikt u het hulp programma Gegevens kopiëren om een pijp lijn te maken die alleen nieuwe en gewijzigde bestanden incrementeel kopieert van Azure Blob-opslag naar Azure Blob-opslag. Hiermee wordt `LastModifiedDate` bepaald welke bestanden moeten worden gekopieerd.

Nadat u de stappen hier hebt voltooid, worden in Azure Data Factory alle bestanden in het bron archief gescand, wordt het bestands filter toegepast `LastModifiedDate` en wordt alleen naar de doel opslag gekopieerd bestanden die sinds de laatste keer zijn bijgewerkt of gewijzigd. Houd er rekening mee dat als Data Factory een groot aantal bestanden scant, er nog steeds een lange duur moet worden verwacht. Het scannen van bestanden is tijdrovend, zelfs wanneer de hoeveelheid gekopieerde gegevens wordt gereduceerd.

> [!NOTE]
> Zie [Inleiding tot Azure Data Factory](introduction.md) als u niet bekend bent met Azure Data Factory.

In deze zelfstudie voert u de volgende taken uit:

> [!div class="checklist"]
> * Een data factory maken.
> * Het hulpprogramma Copy Data gebruiken om een pijplijn te maken.
> * De uitvoering van de pijplijn en van de activiteit controleren.

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* **Azure Storage account**: gebruik Blob Storage voor de bron-en Sink-gegevens opslag. Als u geen Azure Storage account hebt, volgt u de instructies in [een opslag account maken](../storage/common/storage-account-create.md).

## <a name="create-two-containers-in-blob-storage"></a>Twee containers maken in Blob Storage

Bereid de Blob-opslag voor op de zelf studie door de volgende stappen uit te voeren:

1. Maak een container met de naam **Source**. U kunt verschillende hulp middelen gebruiken om deze taak uit te voeren, zoals [Azure Storage Explorer](https://storageexplorer.com/).

2. Maak een container met de naam **Destination**.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Selecteer **Een resource maken** in het linkerdeelvenster. Selecteer **integratie**  >  **Data Factory**:

   ![Data Factory selecteren](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Voer op de pagina **Nieuwe data factory** **ADFTutorialDataFactory** in bij **Naam**.

   De naam van de data factory moet wereldwijd uniek zijn. Dit fout bericht kan worden weer gegeven:

   ![Fout bericht de naam is niet beschikbaar](./media/doc-common-process/name-not-available-error.png)

   Als u een foutbericht ontvangt dat betrekking heeft op de waarde die bij de naam is ingevuld, voert u een andere naam in voor de data factory. Gebruik bijvoorbeeld de naam _**uwnaam**_**ADFTutorialDataFactory**. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
3. Selecteer bij **abonnement** het Azure-abonnement waarin u de nieuwe Data Factory wilt maken.
4. Voer een van de volgende stappen uit onder **resource groep**:

    * Selecteer **bestaande gebruiken** en selecteer een bestaande resource groep in de lijst.

    * Selecteer **nieuwe maken** en voer een naam in voor de resource groep.
         
    Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.

5. Selecteer **V2** onder **Versie**.
6. Selecteer bij **Locatie** de locatie voor de data factory. Alleen ondersteunde locaties worden weer gegeven in de lijst. De gegevens archieven (bijvoorbeeld Azure Storage en Azure SQL Database) en berekeningen (bijvoorbeeld Azure HDInsight) die door uw data factory worden gebruikt, kunnen zich op andere locaties en regio's bevinden.
8. Selecteer **Maken**.
9. Nadat de data factory is gemaakt, wordt de start pagina van data factory weer gegeven.
10. Als u de Azure Data Factory gebruikers interface (UI) wilt openen op een afzonderlijk tabblad, selecteert u de tegel **auteur & monitor** :

    ![Startpagina van de gegevensfactory](./media/doc-common-process/data-factory-home-page.png)

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Het hulpprogramma Copy Data gebruiken om een pijplijn te maken

1. Selecteer op de pagina **aan de slag** de **gegevens kopiëren** tegel om het gegevens kopiëren-hulp programma te openen:

   ![Gegevens kopiëren tegel](./media/doc-common-process/get-started-page.png)

2. Voer de volgende stappen uit op de pagina **Eigenschappen** :

    a. Voer onder **taak naam** **DeltaCopyFromBlobPipeline** in.

    b. Selecteer onder **taak uitgebracht of taak planning** **regel matig uitvoeren volgens schema**.

    c. Onder **trigger type** selecteert u **tumblingvenstertriggers-venster**.

    d. Voer onder **terugkeer patroon** **15 minuut/minuten** in.

    e. Selecteer **Next**.

    Data Factory maakt een pijp lijn met de opgegeven taak naam.

    ![Pagina gegevens eigenschappen kopiëren](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. Voer de volgende stappen uit op de pagina **brongegevens archief** :

    a. Selecteer  **nieuwe verbinding maken** om een verbinding toe te voegen.

    b. Selecteer **Azure Blob Storage** in de galerie en selecteer vervolgens **door gaan**:

    ![Azure-blog opslag selecteren](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Selecteer op de pagina **nieuwe gekoppelde service (Azure Blob Storage)** uw opslag account in de lijst **naam van het opslag account** . Test de verbinding en selecteer vervolgens **maken**.

    d. Selecteer de nieuwe gekoppelde service en selecteer **volgende**:

   ![De nieuwe gekoppelde service selecteren](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Voltooi op de pagina **Invoerbestand of invoermap kiezen** de volgende stappen:

    a. Blader naar de **bronmap** en selecteer deze en selecteer vervolgens **kiezen**.

    ![Kies het invoerbestand of invoermap.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. Selecteer bij **gedrag bij het laden van bestanden** de optie **Incrementeel laden: LastModifiedDate**.

    c. Selecteer **binaire kopie** en selecteer **volgende**:

     ![De pagina voor het invoer bestand of de map kiezen](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. Selecteer op de pagina **doel gegevens archief** de **AzureBlobStorage** -service die u hebt gemaakt. Dit is hetzelfde opslag account als het gegevens archief van de bron. Selecteer vervolgens **Volgende**.

6. Voltooi op de pagina **Uitvoerbestand of uitvoermap kiezen** de volgende stappen:

    a. Blader naar de **doelmap** en selecteer deze en selecteer vervolgens **kiezen**:

    ![De pagina voor het uitvoer bestand of de map kiezen](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. Selecteer **Next**.

7. Selecteer op de pagina **Instellingen** de optie **Volgende**.

8. Controleer de instellingen op de pagina **samen vatting** en selecteer **volgende**.

    ![Overzichtspagina](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. Selecteer op de pagina **Implementatie** de optie **Controleren** om de pijplijn of taak te controleren.

    ![De pagina Implementatie](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. U ziet dat het tabblad **Controleren** aan de linkerkant automatisch wordt geselecteerd. De toepassing wordt overgeschakeld naar het tabblad **monitor** . U ziet de status van de pijp lijn. Selecteer **Vernieuwen** om de lijst te vernieuwen. Selecteer de koppeling onder **PIJPLIJN naam** om details van de activiteit weer te geven of de pijp lijn opnieuw uit te voeren.

    ![De lijst vernieuwen en Details van de uitvoering van de activiteit weer geven](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs-1.png)

11. Er is slechts één activiteit (de Kopieer activiteit) in de pijp lijn, zodat er slechts één vermelding wordt weer gegeven. Selecteer de koppeling **Details** (het bril-pictogram) in de kolom **naam van activiteit** voor meer informatie over de Kopieer bewerking. Zie [overzicht van Kopieer activiteiten](copy-activity-overview.md)voor meer informatie over de eigenschappen.

    ![Kopieer activiteit in de pijp lijn](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Omdat er geen bestanden aanwezig zijn in de bron container in uw Blob Storage-account, ziet u geen bestanden die naar de doel container worden gekopieerd in het account:

    ![Geen bestanden in de bron container of doel container](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. Maak een leeg tekst bestand en geef het de naam **file1.txt**. Upload dit tekst bestand naar de bron container in uw opslag account. U kunt verschillende hulpprogram ma's gebruiken om deze taken uit te voeren, zoals [Azure Storage Explorer](https://storageexplorer.com/).

    ![file1.txt maken en uploaden naar de bron container](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. Als u wilt terugkeren naar de weer gave **pijplijn uitvoeringen** , selecteert u **alle pijplijn uitvoeringen** en wacht u totdat dezelfde pijp lijn automatisch opnieuw wordt geactiveerd.  

14. Wanneer de tweede pijp lijn is uitgevoerd, voert u dezelfde stappen uit om de details van de uitvoering van de activiteit te controleren.  

    U ziet dat één bestand (file1.txt) is gekopieerd van de bron container naar de doel container van uw Blob Storage-account:

    ![file1.txt is gekopieerd van de bron container naar de doel container](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. Maak nog een leeg tekst bestand en noem het **file2.txt**. Upload dit tekst bestand naar de bron container in uw Blob Storage-account.

16. Herhaal stap 13 en 14 voor het tweede tekst bestand. U ziet dat alleen het nieuwe bestand (file2.txt) is gekopieerd van de bron container naar de doel container van uw opslag account tijdens de uitvoering van deze pijp lijn.  

    U kunt ook controleren of er slechts één bestand is gekopieerd met behulp van [Azure Storage Explorer](https://storageexplorer.com/) om de bestanden te scannen:

    ![Bestanden scannen met behulp van Azure Storage Explorer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>Volgende stappen
Ga naar de volgende zelf studie voor meer informatie over het transformeren van gegevens met behulp van een Apache Spark cluster in Azure:

> [!div class="nextstepaction"]
>[Gegevens in de Cloud transformeren met behulp van een Apache Spark cluster](tutorial-transform-data-spark-portal.md)

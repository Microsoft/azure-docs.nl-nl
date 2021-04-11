---
title: Gegevens kopiëren vanuit Azure Blob-opslag naar SQL met het hulpprogramma Gegevens kopiëren
description: Maak een Azure-gegevensfactory. Gebruik vervolgens het hulpprogramma Gegevens kopiëren om gegevens uit Azure Blob Storage te kopiëren naar een SQL-database.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 20db863f1e2ae66acada928687b0bcd572f729f9
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103535"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Gegevens kopiëren vanuit Azure Blob-opslag naar een SQL Database met het hulpprogramma Gegevens kopiëren

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Huidige versie](tutorial-copy-data-tool.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In deze zelfstudie gebruikt u Azure Portal om een gegevensfactory te maken. Vervolgens gebruikt u het hulpprogramma Gegevens kopiëren om een pijplijn te maken waarmee gegevens uit Azure Blob-opslag worden gekopieerd naar een SQL Database.

> [!NOTE]
> Zie [Inleiding tot Azure Data Factory](introduction.md) als u niet bekend bent met Azure Data Factory.

In deze zelfstudie voert u de volgende stappen uit:
> [!div class="checklist"]
> * Een data factory maken.
> * Het hulpprogramma Copy Data gebruiken om een pijplijn te maken.
> * De uitvoering van de pijplijn en van de activiteit controleren.

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* **Azure-opslagaccount**: Gebruik blobopslag als de _bron_-gegevensopslag. Als u geen Azure-opslagaccount hebt, raadpleegt u de instructies in [Een opslagaccount maken](../storage/common/storage-account-create.md).
* **Azure SQL Database**: Gebruik een SQL Database als de _sink_-gegevensopslag. Als u geen SQL Database hebt, raadpleegt u de instructies in [Een SQL Database maken](../azure-sql/database/single-database-create-quickstart.md).

### <a name="create-a-blob-and-a-sql-table"></a>Een blob en een SQL-tabel maken

Bereid uw Blob-opslag en de SQL Database voor voor gebruik tijdens de zelfstudie, door de volgende stappen uit te voeren.

#### <a name="create-a-source-blob"></a>Een bron-blob maken

1. Start **Kladblok**. Kopieer de volgende tekst en sla deze op schijf op in een bestand met de naam **inputEmp.txt**:

   ```text
   FirstName|LastName
   John|Doe
   Jane|Doe
   ```

1. Maak een container met de naam **adfv2tutorial** en upload het bestand inputEmp.txt naar de container. U kunt de Azure Portal of verschillende hulpprogramma's gebruiken zoals [Azure Storage Explorer](https://storageexplorer.com/) om deze taken uit te voeren.

#### <a name="create-a-sink-sql-table"></a>Een SQL-sink-tabel maken

1. Gebruik het volgende SQL-script om een tabel met de naam **dbo.emp** te maken in uw SQL Database:

   ```sql
   CREATE TABLE dbo.emp
   (
       ID int IDENTITY(1,1) NOT NULL,
       FirstName varchar(50),
       LastName varchar(50)
   )
   GO
   CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
   ```

2. Geef Azure-services toegang tot de SQL-server. Controleer of de instelling **Azure-services en -resources toegang geven tot deze server**  is ingeschakeld voor uw server waarop SQL Database wordt uitgevoerd. Met deze instelling kan Data Factory gegevens naar uw database-instantie schrijven. Om deze instelling te controleren en in te schakelen, gaat u naar logische SQL-server > Beveiliging > Firewalls en virtuele netwerken > zet de optie **Toegang tot Azure-services toestaan** op **AAN**.

   > [!NOTE]
   > Met de optie om **Azure-Services en-resources toegang te geven tot deze server** , is toegang tot uw SQL Server mogelijk vanaf elke Azure-resource, niet alleen die in uw abonnement. Zie de [firewall regels voor Azure SQL Server](../azure-sql/database/firewall-configure.md)voor meer informatie. In plaats daarvan kunt u [privé-eind punten](../private-link/private-endpoint-overview.md) gebruiken om verbinding te maken met Azure PaaS-Services zonder open bare ip's te gebruiken.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Selecteer in het linkermenu **Een resource maken** > **Integratie** > **Data Factory**:

   ![Nieuwe data factory maken](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Voer op de pagina **Nieuwe data factory** **ADFTutorialDataFactory** in bij **Naam**.

   De naam van de data factory moet _wereldwijd uniek_ zijn. Mogelijk wordt het volgende foutbericht weergegeven:

   :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Nieuw data factory fout bericht voor dubbele naam.":::

   Als u een foutbericht ontvangt dat betrekking heeft op de waarde die bij de naam is ingevuld, voert u een andere naam in voor de data factory. Gebruik bijvoorbeeld de naam _**uwnaam**_**ADFTutorialDataFactory**. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.

1. Selecteer het Azure-**abonnement** waarin u de nieuwe data factory wilt maken.

1. Voer een van de volgende stappen uit voor **Resourcegroep**:

   a. Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst.

   b. Selecteer **Nieuwe maken** en voer de naam van een resourcegroep in.

   Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.

1. Selecteer bij **Versie** de optie **V2** als de versie.

1. Selecteer bij **Locatie** de locatie voor de data factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevensarchieven (bijvoorbeeld Azure Storage en SQL Database) en -berekeningen (bijvoorbeeld Azure HDInsight) die door uw data factory worden gebruikt, kunnen zich in andere locaties of regio's bevinden.

1. Selecteer **Maken**.

1. Nadat de data factory is gemaakt, wordt de startpagina **Data Factory** weergegeven.

   :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Start pagina voor de Azure Data Factory, met de tegel Author & monitor.":::

1. Selecteer de tegel **Author & Monitor** om de gebruikersinterface (UI) van Azure Data Factory te openen in een afzonderlijk tabblad.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Het hulpprogramma Copy Data gebruiken om een pijplijn te maken

1. Selecteer op de pagina **Aan de slag** de tegel **Copy Data** om het hulpprogramma Copy Data te starten.

   ![De tegel Copy Data-hulpprogramma](./media/doc-common-process/get-started-page.png)

1. Op de pagina **Eigenschappen** bij **Taaknaam**, voert u **CopyFromBlobToSqlPipeline** in. Selecteer vervolgens **Volgende**. De gebruikersinterface van Data Factory maakt een pijplijn met de opgegeven taaknaam.

   ![Een pijplijn maken](./media/tutorial-copy-data-tool/create-pipeline.png)

1. Voltooi op de pagina **Brongegevensarchief** de volgende stappen:

   a. Selecteer **+ nieuwe verbinding maken** om een verbinding toe te voegen

   b. Selecteer in de galerie de optie **Azure Blob Storage**. Selecteer vervolgens **Doorgaan**.

   c. Selecteer op de pagina **Nieuwe gekoppelde service** uw Azure-abonnement en selecteer uw opslagaccount in de lijst **Naam van opslagaccount**. Test de verbinding en selecteer vervolgens **Maken**.

   d. Selecteer de zojuist gemaakte gekoppelde service als bron en selecteer **volgende**.

   ![Aan de bron gekoppelde service selecteren](./media/tutorial-copy-data-tool/select-source-linked-service.png)

1. Voltooi op de pagina **Invoerbestand of invoermap kiezen** de volgende stappen:

   a. Selecteer **Bladeren** om naar de map **adfv2tutorial/input** te gaan, selecteer het **inputEmp.txt** bestand en selecteer vervolgens **kiezen**.

   b. Selecteer **volgende** om naar de volgende stap te gaan.

1. Schakel op de pagina **Instellingen bestandsindeling** het selectievakje voor *Eerste rij als header* in. Het hulpprogramma detecteert automatisch de scheidingstekens voor kolommen en rijen. Selecteer **Next**. U kunt ook een voorbeeld van gegevens en het schema van de ingevoerde gegevens op deze pagina bekijken.

   ![Pagina Instellingen bestandsindelingen](./media/tutorial-copy-data-tool/file-format-settings-page.png)

1. Voltooi op de pagina **Doelgegevensarchief** de volgende stappen:

   a. Selecteer **+ nieuwe verbinding maken** om een verbinding toe te voegen

   b. Selecteer in de galerie de optie **Azure SQL Database**. Selecteer vervolgens **Doorgaan**.

   c. Selecteer op de pagina **Nieuwe gekoppelde service** uw servernaam en databasenaam in de vervolgkeuzelijst en geef de gebruikersnaam en het wachtwoord op. Selecteer vervolgens **Maken**.

      ![Azure SQL DB configureren](./media/tutorial-copy-data-tool/config-azure-sql-db.png)

   d. Selecteer de zojuist gemaakte gekoppelde service als Sink en selecteer **volgende**.

1. Selecteer op de pagina **Tabeltoewijzing** de tabel **[dbo].[emp]** en selecteer vervolgens **Volgende**.

1. Op de pagina **Kolomtoewijzing** ziet u dat de tweede en derde kolom in het invoerbestand zijn toegewezen aan de kolommen **FirstName** en **LastName** van de tabel **emp**. Pas de toewijzing aan om ervoor te zorgen dat er geen fout is en selecteer vervolgens **Volgende**.

   ![Pagina kolomtoewijzing](./media/tutorial-copy-data-tool/column-mapping.png)

1. Selecteer op de pagina **Instellingen** de optie **Volgende**.

1. Bekijk op de **Overzichtspagina** de waarden voor alle instellingen en selecteer vervolgens **Volgende**.

1. Selecteer op de pagina **Implementatie** de optie **Controleren** om de pijplijn of taak te controleren.

   ![De pijplijn bewaken](./media/tutorial-copy-data-tool/monitor-pipeline.png)

1. Selecteer op de pagina Pijplijnuitvoeringen **Vernieuwen** om de lijst te vernieuwen. Selecteer de koppeling onder **PIJPLIJN naam** om details van de activiteit weer te geven of de pijp lijn opnieuw uit te voeren. 

   ![Pijplijnuitvoering](./media/tutorial-copy-data-tool/pipeline-run.png)

1. Selecteer op de pagina Activiteitsuitvoeringen de link **Details** (pictogram van een bril) in de kolom **NAAM ACTIVITEIT** voor meer informatie over de kopieerbewerking. Als u wilt terugkeren naar de weergave Pijplijnuitvoeringen, klikt u op de link **ALLE pijplijnuitvoeringen** bovenaan het koppelingsmenu. Selecteer **Vernieuwen** om de weergave te vernieuwen.

   ![Uitvoering van activiteiten controleren](./media/tutorial-copy-data-tool/activity-monitoring.png)

1. Controleer of de gegevens zijn opgenomen in de tabel **dbo.emp** in uw SQL Database.

1. Selecteer het tabblad **Auteur** aan de linkerkant om over te schakelen naar de bewerkingsmodus. U kunt de gekoppelde services, gegevenssets en pijplijnen die zijn gemaakt met het hulpprogramma, bijwerken met behulp van de editor. Bekijk [de Azure Portal-versie van deze tutorial](tutorial-copy-data-portal.md) voor details over het bewerken van entiteiten in de Data Factory-UI.

   ![Tabblad Auteur selecteren](./media/tutorial-copy-data-tool/author-tab.png)

## <a name="next-steps"></a>Volgende stappen
In dit voorbeeld kopieert de pijplijn gegevens vanuit Blob Storage naar een SQL Database. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een data factory maken.
> * Het hulpprogramma Copy Data gebruiken om een pijplijn te maken.
> * De uitvoering van de pijplijn en van de activiteit controleren.

Ga verder met de volgende zelfstudie als u wilt weten hoe u on-premises gegevens kopieert naar de cloud:

>[!div class="nextstepaction"]
>[On-premises gegevens kopiëren naar de cloud](tutorial-hybrid-copy-data-tool.md)

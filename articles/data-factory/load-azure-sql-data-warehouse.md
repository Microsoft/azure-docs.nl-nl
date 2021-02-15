---
title: Gegevens laden in Azure Synapse Analytics
description: Azure Data Factory gebruiken om gegevens te kopiëren naar Azure Synapse Analytics
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/29/2020
ms.openlocfilehash: 94f5884cb9d1886a9e7aa6b09817b26bba6d47f7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370713"
---
# <a name="load-data-into-azure-synapse-analytics-by-using-azure-data-factory"></a>Gegevens laden in azure Synapse Analytics met behulp van Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) is een scale-out-data base op basis van de cloud die kan verwerken van enorme hoeveel heden gegevens, zowel relationele als niet-relationeel. Azure Synapse Analytics is gebaseerd op de MPP-architectuur (enorm parallelle verwerking) die is geoptimaliseerd voor bedrijfsdata Warehouse-workloads. De service biedt de flexibiliteit van de cloud om opslag te schalen en onafhankelijk te berekenen.

Het is nu eenvoudiger dan ooit om aan de slag te gaan met Azure Synapse Analytics wanneer u Azure Data Factory gebruikt. Azure Data Factory is een volledig beheerde service voor gegevens integratie in de Cloud. U kunt de service gebruiken om een Azure Synapse-analyse te vullen met gegevens van uw bestaande systeem en tijd te besparen bij het bouwen van uw analyse oplossingen.

Azure Data Factory biedt de volgende voor delen voor het laden van gegevens in azure Synapse Analytics:

* **Eenvoudig in te stellen**: een intuïtieve 5-stap wizard zonder scripting vereist.
* **Ondersteuning voor uitgebreide gegevens opslag**: ingebouwde ondersteuning voor een uitgebreide set on-premises en cloud-gebaseerde gegevens archieven. Zie de tabel met [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een gedetailleerde lijst.
* **Beveiligd en compatibel**: gegevens worden overgebracht via https of ExpressRoute. De wereld wijde service aanwezigheid zorgt ervoor dat uw gegevens de geografische grens nooit verlaten.
* **Ongeëvenaarde prestaties met poly base**: poly Base is de meest efficiënte manier om gegevens te verplaatsen naar Azure Synapse Analytics. Gebruik de functie voor het maken van een staging-blob om hoge belasting snelheden te realiseren van alle typen gegevens archieven, waaronder Azure Blob-opslag en Data Lake Store. (Poly Base biedt standaard ondersteuning voor Azure Blob Storage en Azure Data Lake Store.) Zie [prestaties van Kopieer activiteit](copy-activity-performance.md)voor meer informatie.

Dit artikel laat u zien hoe u met het hulp programma Data Factory Gegevens kopiëren _gegevens van Azure SQL database in azure Synapse Analytics kunt laden_. U kunt vergelijkbare stappen volgen om gegevens te kopiëren vanuit andere typen gegevensarchieven.

> [!NOTE]
> Zie [gegevens kopiëren naar of van Azure Synapse Analytics met behulp van Azure Data Factory](connector-azure-sql-data-warehouse.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* Azure Synapse Analytics: het Data Warehouse bevat de gegevens die worden gekopieerd van de SQL database. Als u geen Azure Synapse Analytics hebt, raadpleegt u de instructies in [Create a Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md).
* Azure SQL Database: In deze zelfstudie worden gegevens uit de voorbeeldgegevensset Adventure Works LT gekopieerd naar Azure SQL Database. U kunt deze voorbeelddatabase maken in SQL Database door de instructies in [Een voorbeelddatabase maken in Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md) te volgen.
* Azure-opslag account: Azure Storage wordt gebruikt als de _faserings_ -Blob in de bulksgewijze Kopieer bewerking. Als u geen Azure-opslagaccount hebt, raadpleegt u de instructies in [Een opslagaccount maken](../storage/common/storage-account-create.md).

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Selecteer in het linkermenu **Een resource maken** > **Gegevens en analyses** > **Data factory**:

2. Geef op de pagina **nieuw Data Factory** waarden op voor de volgende items:

    * **Naam**: Voer *LoadSQLDWDemo* in als naam. De naam van uw data factory moet * wereld wijd uniek zijn. Als het fout bericht ' Data Factory-naam ' LoadSQLDWDemo ' is niet beschikbaar ' wordt weer gegeven, voert u een andere naam in voor de data factory. U kunt bijvoorbeeld _**de naam**_**ADFTutorialDataFactory**. Probeer de data factory opnieuw te maken. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
    * **Abonnement**: Selecteer het Azure-abonnement waarin u de Data Factory wilt maken. 
    * **Resource groep**: Selecteer een bestaande resource groep in de vervolg keuzelijst of selecteer de optie **nieuwe maken** en voer de naam van een resource groep in. Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.  
    * **Versie**: Selecteer **V2**.
    * **Locatie**: Selecteer de locatie voor de Data Factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevens archieven die door data factory worden gebruikt, kunnen zich op andere locaties en regio's bevinden. Deze gegevens archieven bevatten Azure Data Lake Store, Azure Storage, Azure SQL Database, enzovoort.

3. Selecteer **Maken**.
4. Nadat het maken is voltooid, gaat u naar uw data factory. U ziet de **Data Factory** start pagina zoals wordt weer gegeven in de volgende afbeelding:

   ![Startpagina van de gegevensfactory](./media/doc-common-process/data-factory-home-page.png)

   Klik op de tegel **Maken en bewaken** om de toepassing Gegevensintegratie in een afzonderlijk tabblad te openen.

## <a name="load-data-into-azure-synapse-analytics"></a>Gegevens laden in Azure Synapse Analytics

1. Selecteer op de pagina **Aan de slag** de tegel **Gegevens kopiëren** om het hulpprogramma Gegevens kopiëren te openen.

2. Geef op de pagina **Eigenschappen** **CopyFromSQLToSQLDW** op voor het veld **taak naam** en selecteer **volgende**.

    ![De pagina Eigenschappen](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

3. Voer de volgende stappen uit op de pagina **brongegevens archief** :
    >[!TIP]
    >In deze zelf studie gebruikt u *SQL-verificatie* als verificatie type voor uw bron gegevensopslag, maar u kunt andere ondersteunde verificatie methoden kiezen:*Service-Principal* en *beheerde identiteit* , indien nodig. Raadpleeg de bijbehorende secties in [dit artikel](./connector-azure-sql-database.md#linked-service-properties) voor meer informatie.
    >Het ook raadzaam om een Azure Key Vault te gebruiken om geheimen voor gegevensarchieven veilig op te slaan. Raadpleeg [dit artikel](./store-credentials-in-key-vault.md) voor gedetailleerde illustraties.

    a. Klik op **+ nieuwe verbinding maken**.

    b. Selecteer **Azure SQL database** in de galerie en selecteer **door gaan**. U kunt ' SQL ' in het zoekvak typen om de connectors te filteren.

    ![Azure SQL DB selecteren](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. Selecteer op de pagina **nieuwe gekoppelde service** de naam van de server en de data base in de vervolg keuzelijst en geef de gebruikers naam en het wacht woord op. Klik op **Verbinding testen** om de instellingen te valideren en selecteer vervolgens **Maken**.

    ![Azure SQL DB configureren](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Selecteer de zojuist gemaakte gekoppelde service als bron. Klik vervolgens op **Volgende**.

4. Voer in de **tabellen selecteren waaruit de gegevens moeten worden gekopieerd of een aangepaste query** pagina, **tabel saleslt** in om de tabellen te filteren. Kies het vak **(Alles selecteren)** om alle tabellen voor de kopie te gebruiken en selecteer vervolgens **volgende**.

    ![Bron tabellen selecteren](./media/load-azure-sql-data-warehouse/select-source-tables.png)

5. Geef op de pagina **filter Toep assen** uw instellingen op of selecteer **volgende**.

6. Voer op de pagina **doel gegevens archief** de volgende stappen uit:
    >[!TIP]
    >In deze zelf studie gebruikt u *SQL-verificatie* als verificatie type voor uw doel gegevens opslag, maar u kunt andere ondersteunde verificatie methoden kiezen:*Service-Principal* en *beheerde identiteit* , indien nodig. Raadpleeg de bijbehorende secties in [dit artikel](./connector-azure-sql-data-warehouse.md#linked-service-properties) voor meer informatie.
    >Het ook raadzaam om een Azure Key Vault te gebruiken om geheimen voor gegevensarchieven veilig op te slaan. Raadpleeg [dit artikel](./store-credentials-in-key-vault.md) voor gedetailleerde illustraties.

    a. Klik op **+ Nieuwe verbinding maken** om een verbinding toe te voegen

    b. Selecteer **Azure Synapse Analytics** in de galerie en selecteer **door gaan**.

    ![Azure Synapse Analytics selecteren](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. Selecteer op de pagina **nieuwe gekoppelde service** de naam van de server en de data base in de vervolg keuzelijst en geef de gebruikers naam en het wacht woord op. Klik op **Verbinding testen** om de instellingen te valideren en selecteer vervolgens **Maken**.

    ![Azure Synapse Analytics configureren](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Selecteer de zojuist gemaakte gekoppelde service als sink. Klik vervolgens op **Volgende**.

7. Bekijk de inhoud op de pagina **tabel toewijzing** en selecteer **volgende**. Een intelligente tabel toewijzing wordt weer gegeven. De bron tabellen worden toegewezen aan de doel tabellen op basis van de tabel namen. Als een bron tabel niet bestaat op de doel locatie, maakt Azure Data Factory standaard een doel tabel met dezelfde naam. U kunt ook een bron tabel toewijzen aan een bestaande doel tabel.

   ![De pagina Tabeltoewijzing](./media/load-azure-sql-data-warehouse/table-mapping.png)

8. Bekijk de inhoud op de pagina **kolom toewijzing** en selecteer **volgende**. De intelligente tabel toewijzing is gebaseerd op de naam van de kolom. Als u Data Factory automatisch tabellen maakt, kan het gegevens type worden geconverteerd als er incompatibiliteits problemen zijn tussen de bron-en doel opslag. Als er een niet-ondersteund gegevens type conversie is tussen de kolom Bron en doel, wordt er een fout bericht weer gegeven naast de overeenkomende tabel.

    ![Pagina kolomtoewijzing](./media/load-azure-sql-data-warehouse/schema-mapping.png)

9. Voer op de pagina **instellingen** de volgende stappen uit:

    a. Klik in de sectie **faserings instellingen** op **+ Nieuw** voor een nieuwe staging-opslag. Het archief wordt gebruikt voor het faseren van de gegevens voordat deze in Azure Synapse Analytics worden geladen met PolyBase. Nadat de kopie is voltooid, worden de tussenliggende gegevens in Azure Blob Storage automatisch opgeschoond.

    b. Selecteer uw opslag account op de pagina **nieuwe gekoppelde service** en selecteer **maken** om de gekoppelde service te implementeren.

    c. Schakel de optie **type standaard gebruiken** uit en selecteer **volgende**.

    ![Poly base configureren](./media/load-azure-sql-data-warehouse/configure-polybase.png)

10. Controleer de instellingen op de pagina **samen vatting** en selecteer **volgende**.

    ![Overzichtspagina](./media/load-azure-sql-data-warehouse/summary-page.png)

11. Selecteer op de pagina **Implementatie** de optie **Controleren** om de pijplijn of taak te controleren. 
 
12. U ziet dat het tabblad **Controleren** aan de linkerkant automatisch wordt geselecteerd. Wanneer de uitvoering van de pijp lijn is voltooid, selecteert u de koppeling **CopyFromSQLToSQLDW** onder de kolom **pijplijn naam** om details van de uitvoering van de activiteit weer te geven of om de pijp lijn opnieuw uit te voeren.

    [![Pijplijnuitvoeringen](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png#lightbox) controleren

13. Als u wilt terugkeren naar de weergave met de pijplijnuitvoeringen, selecteert u de koppeling **Alle pijplijnuitvoeringen** bovenaan. Selecteer **Vernieuwen** om de lijst te vernieuwen.

    ![Uitvoering van activiteiten controleren](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

14. Als u de uitvoerings Details voor elke Kopieer activiteit wilt bewaken, selecteert u de koppeling **Details** (bril pictogram) onder **activiteit naam** in de weer gave uitvoeringen van activiteit. U kunt details bekijken, zoals het volume van de gegevens die uit de bron zijn gekopieerd naar de sink, de gegevensdoorvoer, de uitvoeringsstappen met de overeenkomstige duur en de gebruikte configuraties.
    ![Details van de uitvoering van de activiteit eerst controleren](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-1.png)

    ![Details van uitvoering van activiteit bewaken seconde](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-2.png)

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over ondersteuning voor Azure Synapse Analytics:

> [!div class="nextstepaction"]
>[Azure Synapse Analytics-connector](connector-azure-sql-data-warehouse.md)
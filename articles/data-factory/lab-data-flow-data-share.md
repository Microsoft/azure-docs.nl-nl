---
title: Gegevensintegratie met behulp van Azure Data Factory en Azure Data Share
description: Gegevens kopiëren, transformeren en delen met behulp van Azure Data Factory en Azure Data Share
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: fa424f7e1f5e1f885dd433b8abc8aae1dc1bc206
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97006154"
---
# <a name="data-integration-using-azure-data-factory-and-azure-data-share"></a>Gegevensintegratie met behulp van Azure Data Factory en Azure Data Share

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Als klanten hun moderne datawarehouse- en analyseprojecten starten, hebben ze niet alleen meer gegevens nodig, maar ook meer inzicht in hun gegevens. Deze workshop gaat dieper in op hoe verbeteringen aan Azure Data Factory en Azure Data Share gegevensintegratie en -beheer in Azure vereenvoudigen. 

Van het inschakelen van codevrije ETL/ELT tot het maken van een uitgebreide weergave van uw gegevens, met verbeteringen in Azure Data Factory krijgen uw gegevensengineers de mogelijkheid om meer gegevens, en dus meer waarde, aan uw onderneming toe te voegen. Met Azure Data Share kunt u op een beheerste manier gegevens tussen bedrijven delen.

In deze workshop gebruikt u Azure Data Factory (ADF) om gegevens van Azure SQL Database op te nemen in Azure Data Lake Storage Gen2 (ADLS Gen2). Zodra u de gegevens in het lake hebt opgenomen, kunt u deze transformeren via toewijzingsgegevensstroom, de systeemeigen transformatieservice van Data Dactory, en deze opvangen in Azure Synapse Analytics. Vervolgens deelt u de tabel met getransformeerde gegevens, samen met enkele aanvullende gegevens met behulp van de Azure Data Share. 

De gegevens die in dit lab worden gebruikt, zijn gegevens van New York City-taxi. Als u deze wilt importeren in uw database in SQL Database, moet u het [taxi-data bacpac-bestand](https://github.com/djpmsft/ADF_Labs/blob/master/sample-data/taxi-data.bacpac) downloaden.

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

* **Azure SQL Database**: Als u geen SQL DB hebt, leert u hoe u [een SQL DB-account maakt](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal)

* **Azure Data Lake Storage Gen2-opslagaccount**: Als u geen ADLS Gen2-opslagaccount hebt, leert u hoe u [een ADLS Gen2-opslagaccount kunt maken](../storage/common/storage-account-create.md).

* **Azure Synapse Analytics**: Als u geen Azure Synapse Analytics hebt, leert u hoe u [een Azure Synapse Analytics-instantie kunt maken](../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md).

* **Azure Data Factory**: Als u nog geen data factory hebt gemaakt, ziet u hoe u [een data factory kunt maken](./quickstart-create-data-factory-portal.md).

* **Azure Data Share**: Als u nog geen gegevensshare hebt gemaakt, ziet u hoe u [een gegevensshare kunt maken](../data-share/share-your-data.md#create-a-data-share-account).

## <a name="set-up-your-azure-data-factory-environment"></a>Uw Azure Data Factory-omgeving instellen

In dit gedeelte leert u hoe u de Azure Data Factory-gebruikerservaring (ADF UX) kunt openen vanuit de Azure-portal. In de ADF UX configureert u drie gekoppelde services voor elk van de gegevensarchieven die we gebruiken: Azure SQL DB, ADLS Gen2 en Azure Synapse Analytics.

Met gekoppelde services van Azure Data Factory wordt de verbindingsinformatie naar externe resources gedefinieerd. Azure Data Factory ondersteunt momenteel meer dan 85 connectors.

### <a name="open-the-azure-data-factory-ux"></a>Open de Azure Data Factory UX

1. Open de [Azure-portal](https://portal.azure.com) in Microsoft Edge of Google Chrome.
1. Zoek op 'Data Factories' in de zoekbalk bovenaan de pagina

    ![Portal 1](media/lab-data-flow-data-share/portal1.png)
1. Klik op uw data factory-resource om de resourceblade te openen.

    ![Portal 2](media/lab-data-flow-data-share/portal2.png)
1. Klik op **Ontwerpen en controleren** om de ADF UX te openen. De ADF UX kan ook worden geopend op adf.azure.com.

    ![Portal 3](media/lab-data-flow-data-share/portal3.png)
1. U wordt omgeleid naar de startpagina van de ADF UX. Deze pagina bevat quickstarts, instructievideo's en koppelingen naar zelfstudies voor meer informatie over data factory-concepten. Klik op het potloodpictogram in de linkerbalk om te beginnen met ontwerpen.

    ![Portal configureren](media/lab-data-flow-data-share/configure1.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Een gekoppelde Azure SQL Database-service maken

1. Op de ontwerppagina kunt u data factory-resources maken, zoals pijplijnen, gegevenssets, gegevensstromen, triggers en gekoppelde services. Als u een gekoppelde service wilt maken, klikt u op de knop **Verbindingen** in de rechterbenedenhoek.

    ![Portal configureren 2](media/lab-data-flow-data-share/configure2.png)
1. Klik op het tabblad verbindingen op **Nieuw** om een nieuwe gekoppelde service toe te voegen.

    ![Portal configureren 3](media/lab-data-flow-data-share/configure3.png)
1. De eerste gekoppelde service die u configureert, is een Azure SQL-database. U kunt de zoekbalk gebruiken om de lijst met gegevensarchieven te filteren. Klik op de tegel **Azure SQL Database** en klik op doorgaan.

    ![Portal configureren 4](media/lab-data-flow-data-share/configure-4.png)
1. Voer in het deelvenster SQL DB-configuratie 'SQLDB' in als de naam van de gekoppelde service. Voer uw aanmeldingsgegevens in om data factory verbinding te laten maken met uw database. Als u SQL-verificatie gebruikt, voert u de servernaam, de database, uw gebruikersnaam en wachtwoord in. U kunt controleren of de verbindingsgegevens juist zijn door te klikken op **Verbinding testen**. Klik op **Maken** als u klaar bent.

    ![Portal configureren 5](media/lab-data-flow-data-share/configure5.png)

### <a name="create-an-azure-synapse-analytics-linked-service"></a>Een gekoppelde Azure Synapse Analytics-service maken

1. Herhaal hetzelfde proces om een gekoppelde Azure Synapse Analytics-service toe te voegen. Klik op het tabblad verbindingen op **Nieuw**. Selecteer de tegel **Azure Synapse Analytics** en klik op doorgaan.

    ![Portal configureren 6](media/lab-data-flow-data-share/configure-6.png)
1. Voer in het deelvenster Configuratie van gekoppelde service 'SQLDW' in als de naam van de gekoppelde service. Voer uw aanmeldingsgegevens in om data factory verbinding te laten maken met uw database. Als u SQL-verificatie gebruikt, voert u de servernaam, de database, uw gebruikersnaam en wachtwoord in. U kunt controleren of de verbindingsgegevens juist zijn door te klikken op **Verbinding testen**. Klik op **Maken** als u klaar bent.

    ![Portal configureren 7](media/lab-data-flow-data-share/configure-7.png)

### <a name="create-an-azure-data-lake-storage-gen2-linked-service"></a>Een gekoppelde Azure Data Lake Storage Gen2-service maken

1. De laatste gekoppelde service die nodig is voor dit lab is een Azure Data Lake Storage Gen2.  Klik op het tabblad verbindingen op **Nieuw**. Selecteer de tegel **Azure Data Lake Storage Gen2** en klik op doorgaan.

    ![Portal configureren 8](media/lab-data-flow-data-share/configure8.png)
1. Voer in het deelvenster Configuratie van gekoppelde service 'ADLSGen2' in als de naam van de gekoppelde service. Als u accountsleutelverificatie gebruikt, selecteert u uw ADLS Gen2-opslagaccount in de vervolgkeuzelijst **Naam van het opslagaccount**. U kunt controleren of de verbindingsgegevens juist zijn door te klikken op **Verbinding testen**. Klik op **Maken** als u klaar bent.

    ![Portal configureren 9](media/lab-data-flow-data-share/configure9.png)

### <a name="turn-on-data-flow-debug-mode"></a>Foutopsporingsmodus voor gegevensstromen inschakelen

In het gedeelte *Gegevens transformeren met behulp van toewijzingsgegevensstroom* gaat uw toewijzingsgegevensstromen bouwen. Een best practice voordat u toewijzingsgegevensstromen bouwt, is het inschakelen van de foutopsporingsmodus, waarmee u de transformatielogica in een paar seconden kunt testen op een actief Spark-cluster.

Als u foutopsporing wilt inschakelen, klikt u op de schuifregelaar **Foutopsporing gegevensstroom** in de bovenste balk van de factory. Klik op OK als het bevestigingsvenster wordt weergegeven. Het duurt ongeveer 5-7 minuten om het cluster op te starten. Ga door met *Gegevens opnemen vanuit Azure SQL DB in ADLS Gen2 met behulp van de kopieeractiviteit* tijdens de initialisatie.

![Portal configureren 10](media/lab-data-flow-data-share/configure10.png)

## <a name="ingest-data-using-the-copy-activity"></a>Gegevens opnemen met behulp van de kopieeractiviteit

In deze sectie maakt u een pijplijn met een kopieeractiviteit die een tabel van een Azure SQL-database opneemt in een ADLS Gen2-opslagaccount. U leert hoe u een pijplijn toevoegt, een gegevensset configureert en fouten in een pijplijn opspoort via de ADF UX. Het configuratiepatroon dat wordt gebruikt in deze zelfstudie, kan worden toegepast op het kopiëren van een relationeel gegevensarchief naar een gegevensarchief op basis van bestanden.

In Azure Data Factory is een pijplijn een logische groep activiteiten die samen een taak uitvoeren. Een activiteit definieert een bewerking die op uw gegevens moet worden uitgevoerd. Een gegevensset wijst naar de gegevens die u wilt gebruiken in een gekoppelde service.

### <a name="create-a-pipeline-with-a-copy-activity"></a>Een pijplijn met kopieeractiviteit maken

1. Klik in het deelvenster Factory-resources op het pluspictogram om het menu Nieuwe resource te openen. Selecteer **Pijplijn**.

    ![Portal kopiëren 1](media/lab-data-flow-data-share/copy1.png)
1. Geef op het tabblad **Algemeen** van het pijplijncanvas een beschrijvende naam op voor de pijplijn, zoals 'IngestAndTransformTaxiData'.

    ![Portal kopiëren 2](media/lab-data-flow-data-share/copy2.png)
1. Open in het deelvenster Activiteiten van het pijplijncanvas de accordion **Verplaatsen en transformeren** en sleep de activiteit **Gegevens kopiëren** naar het canvas. Geef de kopieeractiviteit een beschrijvende naam zoals 'IngestIntoADLS'.

    ![Portal kopiëren 3](media/lab-data-flow-data-share/copy3.png)

### <a name="configure-azure-sql-db-source-dataset"></a>Azure SQL DB-brongegevensset configureren

1. Klik op het tabblad **Bron** van de kopieeractiviteit. Klik op **Nieuw** om een nieuwe gegevensset te maken. De bron is de tabel 'dbo.TripData' die zich bevindt in de eerder geconfigureerde gekoppelde service 'SQLDB'.

    ![Portal kopiëren 4](media/lab-data-flow-data-share/copy4.png)
1. Zoek naar **Azure SQL Database** en klik op doorgaan.

    ![Portal kopiëren 5](media/lab-data-flow-data-share/copy-5.png)
1. Roep de gegevensset 'TripData' aan. Selecteer 'SQLDB' als uw gekoppelde service. Selecteer tabelnaam 'dbo.TripData' uit de vervolgkeuzelijst voor de tabelnaam. Importeer het schema **Uit verbinding/archief**. Als u klaar bent, klikt u op OK.

    ![Portal kopiëren 6](media/lab-data-flow-data-share/copy6.png)

U hebt uw eerste brongegevensset gemaakt. Zorg ervoor dat in de broninstellingen de standaardwaarde **Tabel** is geselecteerd in het veld Query gebruiken.

### <a name="configure-adls-gen2-sink-dataset"></a>ADLS Gen 2-sinkgegevensset configureren

1. Klik op het tabblad **Sink** van de kopieeractiviteit. Klik op **Nieuw** om een nieuwe gegevensset te maken.

    ![Portal kopiëren 7](media/lab-data-flow-data-share/copy7.png)
1. Zoek naar **Azure Data Lake Storage Gen2** en klik op doorgaan.

    ![Portal kopiëren 8](media/lab-data-flow-data-share/copy8.png)
1. Selecteer in het deelvenster Opmaak selecteren de optie **DelimitedText** terwijl u naar een CSV-bestand schrijft. Klik op Doorgaan.

    ![Portal kopiëren 9](media/lab-data-flow-data-share/copy9.png)
1. Noem uw sinkgegevensset 'TripDataCSV'. Selecteer 'ADLSGen2' als uw gekoppelde service. Voer in waar u uw CSV-bestand wilt schrijven. U kunt bijvoorbeeld uw gegevens schrijven naar het bestand `trip-data.csv` in container `staging-container`. Stel **Eerste rij als header** in op waar als u wilt dat uw uitvoergegevens headers bevatten. Omdat er nog geen bestand in de bestemming bestaat, stelt u **Importschema** in op **Geen**. Als u klaar bent, klikt u op OK.

    ![Portal kopiëren 10](media/lab-data-flow-data-share/copy10.png)

### <a name="test-the-copy-activity-with-a-pipeline-debug-run"></a>De kopieeractiviteit testen met het uitvoeren van een pijplijnfoutopsporing

1. Als u wilt controleren of uw kopieeractiviteit correct werkt, klikt u op **Fouten opsporen** bovenaan het pijplijncanvas om een uitvoering van de foutopsporing uit te voeren. Met een foutopsporingsuitvoering kunt u de pijplijn end-to-end testen of tot een onderbrekingspunt voordat u deze naar de data factory-service publiceert.

    ![Portal kopiëren 11](media/lab-data-flow-data-share/copy11.png)
1. Als u de foutopsporingsuitvoering wilt controleren, gaat u naar het tabblad **Uitvoer** van het pijplijncanvas. Het controlescherm wordt automatisch elke 20 seconden vernieuwd of wanneer u handmatig op de knop Vernieuwen klikt. De kopieeractiviteit heeft een speciale controleweergave die u kunt openen door op het brilpictogram te klikken in de kolom **Acties**.

    ![Portal kopiëren 12](media/lab-data-flow-data-share/copy12.png)
1. De controleweergave voor kopiëren bevat de uitvoeringsdetails en prestatiekenmerken van de activiteit. U kunt informatie bekijken zoals gelezen/geschreven gegevens, gelezen/geschreven rijen, gelezen/geschreven bestanden en doorvoer. Als u alles op de juiste wijze hebt geconfigureerd, ziet u dat er 49.999 rijen zijn geschreven naar één bestand in uw ADLS-sink.

    ![Portal kopiëren 13](media/lab-data-flow-data-share/copy13.png)
1. Voordat u doorgaat naar het volgende gedeelte, wordt u geadviseerd om uw wijzigingen te publiceren naar de data factory-service door te klikken op **Alles publiceren** in de bovenste balk van de factory. Hoewel dit niet wordt besproken in dit lab, ondersteunt Azure Data Factory volledige git-integratie. Git-integratie biedt versiebeheer, iteratief opslaan in een opslagplaats en samenwerking op een data factory. Zie voor meer informatie [broncodebeheer in Azure Data Factory](./source-control.md#troubleshooting-git-integration).

    ![Portal publiceren 1](media/lab-data-flow-data-share/publish1.png)

## <a name="transform-data-using-mapping-data-flow"></a>Gegevens transformeren met toewijzingsgegevensstroom

Nu u gegevens naar Azure Data Lake Storage hebt gekopieerd, is het tijd om die gegevens samen te voegen en te aggregeren in een datawarehouse. We gebruiken de toewijzingsgegevensstroom, de visueel ontworpen transformatieservice van Azure Data Factory. Met toewijzingsgegevensstromen kunnen gebruikers transformatielogica codevrij ontwikkelen en uitvoeren op Spark-clusters die worden beheerd door de ADF-service.

De gegevensstroom die in deze stap wordt gemaakt, wordt gekoppeld aan de gegevensset 'TripDataCSV' die in het vorige gedeelte is gemaakt met een tabel 'dbo.TripFares' die is opgeslagen in 'SQLDB' op basis van vier sleutelkolommen. Vervolgens worden de gegevens geaggregeerd op basis van kolom `payment_type` om het gemiddelde van bepaalde velden te berekenen en worden ze geschreven naar een Azure Synapse Analytics-tabel.

### <a name="add-a-data-flow-activity-to-your-pipeline"></a>Een gegevensstroomactiviteit toevoegen aan uw pijplijn

1. Open in het deelvenster Activiteiten van het pijplijncanvas de accordion **Verplaatsen en transformeren** en sleep de activiteit **Gegevensstroom** naar het canvas.

    ![Gegevensstroom portal 1](media/lab-data-flow-data-share/dataflow1.png)
1. Selecteer in het zijdeelvenster dat wordt geopend **Nieuwe gegevensstroom maken** en kies **Toewijzingsgegevensstroom**. Klik op **OK**.

    ![Gegevensstroom portal 2](media/lab-data-flow-data-share/dataflow2.png)
1. U wordt omgeleid naar het gegevensstroomcanvas waar u uw transformatielogica gaat bouwen. Geef op het tabblad Algemeen uw gegevensstroom de naam 'JoinAndAggregateData'.

    ![Gegevensstroom portal 3](media/lab-data-flow-data-share/dataflow3.png)

### <a name="configure-your-trip-data-csv-source"></a>De CSV-bron van uw reisgegevens configureren

1. Het eerste wat u moet doen is uw twee brontransformaties configureren. De eerste bron wijst naar de DelimitedText-gegevensset 'TripDataCSV'. Als u een brontransformatie wilt toevoegen, klikt u op het vak **Bron toevoegen** op het canvas.

    ![Gegevensstroom portal 4](media/lab-data-flow-data-share/dataflow4.png)
1. Geef de bron de naam 'TripDataCSV' en selecteer de gegevensset 'TripDataCSV' in de vervolgkeuzelijst Bron. U hebt in eerste instantie geen schema geïmporteerd bij het maken van deze gegevensset omdat er geen gegevens waren. Omdat `trip-data.csv` nu bestaat, klikt u op **Bewerken** om naar het tabblad Instellingen van gegevensset te gaan.

    ![Gegevensstroom portal 5](media/lab-data-flow-data-share/dataflow5.png)
1. Ga naar het tabblad **Schema** en klik op **Schema importeren**. Selecteer **Uit verbinding/archief** om rechtstreeks vanuit het bestandsarchief te importeren. Er moeten 14 kolommen van het type tekenreeks worden weergegeven.

    ![Gegevensstroom portal 6](media/lab-data-flow-data-share/dataflow6.png)
1. Ga terug naar de gegevensstroom 'JoinAndAggregateData'. Als uw cluster voor foutopsporing is gestart (aangegeven door een groene cirkel naast de schuifregelaar voor foutopsporing), kunt u een momentopname van de gegevens ophalen op het tabblad **Gegevenspreview**. Klik op **Vernieuwen** om een voorbeeld van de gegevens op te halen.

    ![Gegevensstroom portal 7](media/lab-data-flow-data-share/dataflow7.png)

> [!Note]
> De gegevenspreview schrijft geen gegevens.

### <a name="configure-your-trip-fares-sql-db-source"></a>Uw reistarieven configureren SQL DB-bron

1. De tweede bron die u gaat toevoegen, verwijst naar de SQL DB-tabel 'dbo.TripFares'. Onder de bron 'TripDataCSV' bevindt zich nog een vak **Bron toevoegen**. Klik hierop om een nieuwe brontransformatie toe te voegen.

    ![Gegevensstroom portal 8](media/lab-data-flow-data-share/dataflow8.png)
1. Geef deze bron de naam 'TripFaresSQL'. Klik op **Nieuw** naast het veld Brongegevensset om een nieuwe SQL DB-gegevensset te maken.

    ![Gegevensstroom portal 9](media/lab-data-flow-data-share/dataflow9.png)
1. Selecteer de tegel **Azure SQL Database** en klik op doorgaan. *Opmerking: Het kan zijn dat veel van de connectors in data factory niet worden ondersteund in de toewijzingsgegevensstroom. Als u gegevens uit een van deze bronnen wilt transformeren, neemt u deze op in een ondersteunde bron met behulp van de kopieeractiviteit*.

    ![Gegevensstroom portal 10](media/lab-data-flow-data-share/dataflow-10.png)
1. Roep de gegevensset 'TripFares' aan. Selecteer 'SQLDB' als uw gekoppelde service. Selecteer tabelnaam 'dbo.TripFares' uit de vervolgkeuzelijst voor de tabelnaam. Importeer het schema **Uit verbinding/archief**. Als u klaar bent, klikt u op OK.

    ![Gegevensstroom portal 11](media/lab-data-flow-data-share/dataflow11.png)
1. Als u uw gegevens wilt controleren, haalt u een gegevenspreview op in het tabblad **Gegevenspreview**.

    ![Gegevensstroom portal 12](media/lab-data-flow-data-share/dataflow12.png)

### <a name="inner-join-tripdatacsv-and-tripfaressql"></a>Inner join TripDataCSV en TripFaresSQL

1. Als u een nieuwe transformatie wilt toevoegen, klikt u op het pluspictogram in de rechterbenedenhoek van 'TripDataCSV'. Onder **Meerdere invoeren/uitvoeren** selecteert u **Samenvoegen**.

    ![Deelnemen aan portal 1](media/lab-data-flow-data-share/join1.png)
1. Geef uw join-transformatie de naam 'InnerJoinWithTripFares'. Selecteer 'TripFaresSQL' in de vervolgkeuzelijst voor de stroom aan de rechterkant. Selecteer **Inner** als join-type. Raadpleegt [Join-typen](./data-flow-join.md#join-types) voor meer informatie over de verschillende join-typen in toewijzingsgegevensstroom.

    Selecteer de kolommen die u wilt vergelijken vanuit elke stroom via de vervolgkeuzelijst **Join-voorwaarden**. Als u een extra join-voorwaarde wilt toevoegen, klikt u op het pluspictogram naast een bestaande voorwaarde. Standaard worden alle join-voorwaarden gecombineerd met een AND-operator, wat betekent dat aan alle voorwaarden moet worden voldaan voor een overeenkomst. In dit lab willen we overeenkomsten met kolommen `medallion`, `hack_license`, `vendor_id` en `pickup_datetime`

    ![Deelnemen aan portal 2](media/lab-data-flow-data-share/join2.png)
1. Controleer of u 25 kolommen hebt samengevoegd met een gegevenspreview.

    ![Deelnemen aan portal 3](media/lab-data-flow-data-share/join3.png)

### <a name="aggregate-by-payment_type"></a>Aggregatie per payment_type

1. Nadat u de join-transformatie hebt voltooid, voegt u een geaggregeerde transformatie toe door te klikken op het pluspictogram naast 'InnerJoinWithTripFares'. Kies **Aggregeren** onder **Schemawijzigingsfunctie**.

    ![Portal aggregeren 1](media/lab-data-flow-data-share/agg1.png)
1. Geef uw geaggregeerde transformatie de naam 'AggregateByPaymentType'. Selecteer `payment_type` als de kolom Groeperen op.

    ![Portal aggregeren 2](media/lab-data-flow-data-share/agg2.png)
1. Ga naar het tabblad **Aggregaties**. Hier geeft u twee aggregaties op:
    * De gemiddelde tarief gegroepeerd op betalingstype
    * De totale reisafstand gegroepeerd op betalingstype

    Eerst maakt u de expressie voor gemiddeld tarief. Voer 'average_fare' in het tekstvak **Een kolom toevoegen of selecteren** in.

    ![Portal aggregeren 3](media/lab-data-flow-data-share/agg3.png)
1. Als u een expressie voor aggregatie wilt invoeren, klikt u op het blauwe vak **Expressie invoeren**. Hiermee opent u de opbouwfunctie voor de gegevensstroomexpressie, een hulpprogramma dat wordt gebruikt om gegevensstroomexpressies visueel te maken met behulp van invoerschema's, ingebouwde functies en bewerkingen, en door de gebruiker gedefinieerde parameters. Zie de [Documentatie voor opbouwfunctie van expressies](./concepts-data-flow-expression-builder.md) voor meer informatie over de mogelijkheden van de opbouwfunctie voor expressies.

    Als u het gemiddelde tarief wilt ophalen, gebruikt u de `avg()` aggregatiefunctie om de kolom `total_amount` te aggregeren met een geheel getal met `toInteger()`. In de taal van de gegevensstroomexpressie wordt dit gedefinieerd als `avg(toInteger(total_amount))`. Klik op **Opslaan en voltooien** wanneer u klaar bent.

    ![Portal aggregeren 4](media/lab-data-flow-data-share/agg4.png)
1. Klik op het plus pictogram naast `average_fare` om een extra aggregatie-expressie toe te voegen. Selecteer **Kolom toevoegen**.

    ![Portal aggregeren 5](media/lab-data-flow-data-share/agg5.png)
1. Voer 'total_trip_distance' in het tekstvak **Een kolom toevoegen of selecteren** in. Open zoals in de laatste stap de opbouwfunctie voor expressies om de expressie in te voeren.

    Als u de totale reisafstand wilt ophalen, gebruikt u de `sum()` aggregatiefunctie om de kolom `trip_distance` te aggregeren met een geheel getal met `toInteger()`. In de taal van de gegevensstroomexpressie wordt dit gedefinieerd als `sum(toInteger(trip_distance))`. Klik op **Opslaan en voltooien** wanneer u klaar bent.

    ![Portal aggregeren 6](media/lab-data-flow-data-share/agg6.png)
1. Test uw transformatielogica op het tabblad **Gegevenspreview**. Zoals u kunt zien, zijn er aanzienlijk minder rijen en kolommen dan eerder. Alleen de drie kolommen voor groeperen op en aggregatie die in deze transformatie zijn gedefinieerd, gaan verder. Aangezien het voorbeeld slechts vijf groepen betalingstypen bevat, worden er slechts vijf rijen gegenereerd.

    ![Portal aggregeren 7](media/lab-data-flow-data-share/agg7.png)

### <a name="configure-you-azure-synapse-analytics-sink"></a>Uw Azure Synapse Analytics-sink configureren

1. Nu we onze transformatielogica hebben voltooid, kunnen we onze gegevens in een Azure Synapse Analytics-tabel opvangen. Voeg een sink-transformatie toe in het gedeelte **Bestemming**.

    ![Portalsink 1](media/lab-data-flow-data-share/sink1.png)
1. Geef uw sink de naam 'SQLDWSink'. Klik op **Nieuw** naast het veld Sink-gegevensset om een nieuwe Azure Synapse Analytics-gegevensset te maken.

    ![Portalsink 2](media/lab-data-flow-data-share/sink2.png)

1. Selecteer de tegel **Azure Synapse Analytics** en klik op doorgaan.

    ![Portalsink 3](media/lab-data-flow-data-share/sink-3.png)
1. Roep de gegevensset 'AggregatedTaxiData' aan. Selecteer 'SQLDW' als uw gekoppelde service. Selecteer **Nieuwe tabel maken** en geef de nieuwe tabel de naam dbo.AggregateTaxiData. Als u klaar bent, klikt u op OK

    ![Portalsink 4](media/lab-data-flow-data-share/sink4.png)
1. Ga naar het tabblad **Instellingen** van de sink. Omdat we een nieuwe tabel maken, moeten we **Tabel opnieuw maken** selecteren onder tabelactie. Schakel het selectievakje **Fasering inschakelen** uit om te schakelen tussen rij per rij of in batch invoeren.

    ![Portalsink 5](media/lab-data-flow-data-share/sink5.png)

U hebt uw gegevensstroom gemaakt. Nu is het tijd om deze uit te voeren in een pijplijnactiviteit.

### <a name="debug-your-pipeline-end-to-end"></a>End-to-end-foutopsporing voor uw pijplijn

1. Ga terug naar het tabblad voor de pijplijn **IngestAndTransformData**. U ziet een groen vakje op de kopieeractiviteit 'IngestIntoADLS'. Sleep het naar de gegevensstroomactiviteit 'JoinAndAggregateData'. Hiermee maakt u een 'bij gelukt', waardoor de gegevensstroomactiviteit alleen wordt uitgevoerd als de kopie is geslaagd.

    ![Portalpijplijn 1](media/lab-data-flow-data-share/pipeline1.png)
1. Klik op **Foutopsporing** om foutopsporing uit te voeren, net zoals we voor de kopieeractiviteit hebben gedaan. Voor de uitvoering van foutopsporing gebruikt de gegevensstroomactiviteit het actieve foutopsporingscluster in plaats van een nieuw cluster te ontwikkelen. Het uitvoeren van deze pijplijn duurt iets langer dan een minuut.

    ![Portalpijplijn 2](media/lab-data-flow-data-share/pipeline2.png)
1. Net als bij de kopieeractiviteit heeft de gegevensstroom een speciale controleweergave die na voltooiing van de activiteit via het brilpictogram kan worden geopend.

    ![Portalpijplijn 3](media/lab-data-flow-data-share/pipeline3.png)
1. In de controleweergave kunt u een vereenvoudigde gegevensstroomgrafiek bekijken, samen met de uitvoeringstijden en rijen bij elke uitvoeringsfase. Als alles goed is uitgevoerd, hebt u 49.999 rijen samengevoegd tot vijf rijen in deze activiteit.

    ![Portalpijplijn 4](media/lab-data-flow-data-share/pipeline4.png)
1. U kunt op een transformatie klikken om aanvullende informatie te bekijken over de uitvoering, zoals het partitioneren van gegevens en nieuwe/bijgewerkte/verwijderde kolommen.

    ![Portalpijplijn 5](media/lab-data-flow-data-share/pipeline5.png)

U hebt nu het gedeelte data factory van dit lab voltooid. Publiceer uw resources als u deze wilt uitvoeren met triggers. U hebt een pijplijn uitgevoerd die gegevens van Azure SQL Database heeft opgenomen naar Azure Data Lake Storage met behulp van de kopieeractiviteit en vervolgens hebt u deze gegevens samengevoegd in een Azure Synapse Analytics. U kunt controleren of de gegevens zijn geschreven door de SQL Server zelf te bekijken.

## <a name="share-data-using-azure-data-share"></a>Gegevens delen met Azure Data Share

In dit gedeelte leert u hoe u een nieuwe gegevensshare instelt met behulp van de Azure-portal. Hiervoor moet u een nieuwe gegevensshare maken die gegevenssets bevat van Azure Data Lake Store Gen2 en Azure Synapse Analytics. Vervolgens configureert u een schema voor momentopnamen, zodat de gegevensgebruikers een optie hebben om de gegevens die met hen worden gedeeld automatisch te vernieuwen. Vervolgens nodigt u ontvangers uit voor uw gegevensshare. 

Zodra u een gegevensshare hebt gemaakt, schakelt u over van rol en wordt u de *gegevensgebruiker*. Als gegevensgebruiker loopt u door de stroom van het accepteren van een gegevensshare-uitnodiging, het configureren van de locatie waar de gegevens moeten worden ontvangen en het toewijzen van gegevenssets aan verschillende opslaglocaties. Vervolgens wordt een momentopname geactiveerd, waarmee de met u gedeelde gegevens worden gekopieerd naar de opgegeven bestemming. 

### <a name="sharing-data-data-provider-flow"></a>Gegevens delen (stroom van gegevensprovider)

1. Open de Azure-portal in Microsoft Edge of Google Chrome.

1. Zoek op **Gegevensshares** in de zoekbalk bovenaan de pagina

    ![Portaladvertenties](media/lab-data-flow-data-share/portal-ads.png)

1. Selecteer het gegevensshare-account met 'Provider' in de naam. Bijvoorbeeld **DataProvider0102**. 

1. Selecteer **Beginnen met het delen van uw gegevens**

    ![Beginnen met delen](media/lab-data-flow-data-share/ads-start-sharing.png)

1. Selecteer **+Maken** om te beginnen met het configureren van de nieuwe gegevensshare. 

1. Geef een gewenste naam op onder **Sharenaam**. Dit is de sharenaam die wordt weergegeven aan uw gegevensgebruiker. Zorg er dus voor dat u een beschrijvende naam opgeeft, zoals TaxiData.

1. Onder **Beschrijving** voert u een zin in waarmee de inhoud van de gegevensshare wordt beschreven. De gegevensshare bevat gegevens van wereldwijde taxiritjes die zijn opgeslagen in een aantal archieven, waaronder Azure Synapse Analytics en Azure Data Lake Store. 

1. Geef onder **Gebruiksvoorwaarden** een set voorwaarden op waaraan uw gegevensgebruiker moet voldoen. Enkele voorbeelden zijn 'Deze gegevens niet distribueren buiten uw organisatie' of 'Raadpleeg de juridische overeenkomst'. 

    ![Details delen](media/lab-data-flow-data-share/ads-details.png)

1. Selecteer **Doorgaan**. 

1. Selecteer **Gegevenssets toevoegen** 

    ![Gegevensset 1 toevoegen](media/lab-data-flow-data-share/add-dataset.png)

1. Selecteer **Azure Synapse Analytics** om een tabel te selecteren uit Azure Synapse Analytics waarin uw ADF-transformaties terecht zijn gekomen.

    ![Gegevensset sql toevoegen](media/lab-data-flow-data-share/add-dataset-sql.png)


1. U krijgt een script dat u moet uitvoeren voordat u kunt doorgaan. Met het geboden script wordt een gebruiker gemaakt in de SQL-database zodat de Azure Data Share MSI kan verifiëren namens de gebruiker. 

> [!IMPORTANT]
> Voordat u het script uitvoert, moet u zichzelf instellen als de Active Directory-beheerder voor de SQL Server. 

1. Open een nieuw tabblad en ga naar de Azure-portal. Kopieer het geleverde script om een gebruiker te maken in de database waarvan u gegevens wilt delen. Meld u hiervoor aan bij de EDW-database met behulp van Query Explorer (preview) met behulp van AAD-verificatie. 

    U moet het script wijzigen zodat de gemaakte gebruiker tussen vierkante haken staat. Bijvoorbeeld:
    
    maak gebruiker [dataprovider-xxxx] van externe aanmelding;  exec sp_addrolemember db_owner, [dataprovider-xxxx];
    
1. Ga terug naar de Azure Data Share waar u gegevenssets aan uw gegevensshare hebt toegevoegd. 

1. Selecteer **EDW** en selecteer vervolgens **AggregatedTaxiData** voor de tabel. 

1. Selecteer **Gegevensset toevoegen**

    We hebben nu een SQL-tabel die deel uitmaakt van de gegevensset. Vervolgens voegen we extra gegevenssets toe van Azure Data Lake Store. 

1. Selecteer **Gegevensset toevoegen** en selecteer **Azure Data Lake Store Gen2**

    ![Gegevensset adls toevoegen](media/lab-data-flow-data-share/add-dataset-adls.png)

1. Selecteer **Volgende**

1. Vouw *wwtaxidata* uit. Vouw *Boston-taxigegevens* uit. U ziet dat u tot het bestandsniveau kunt delen. 

1. Selecteer de map *Boston-taxigegevens* om de volledige map toe te voegen aan uw gegevensshare. 

1. Selecteer **Gegevenssets toevoegen**

1. Controleer de gegevenssets die zijn toegevoegd. U moet een SQL-tabel en een ADLS Gen2-map aan uw gegevensshare hebben toegevoegd. 

1. Selecteer **Doorgaan**

1. In dit scherm kunt u ontvangers toevoegen aan uw gegevensshare. De ontvangers die u toevoegt, ontvangen uitnodigingen voor uw gegevensshare. Voor het doel van dit lab moet u twee e-mailadressen toevoegen:

    1. Het e-mailadres van het Azure-abonnement dat u gebruikt. 

        ![Ontvangers toevoegen](media/lab-data-flow-data-share/add-recipients.png)

    1. Voeg de fictieve gegevensgebruiker met de naam *janedoe@fabrikam.com* toe.

1. In dit scherm kunt u een momentopname-instelling voor uw gegevensgebruiker configureren. Zo krijgen ze regelmatig updates van uw gegevens met een interval dat door u is opgegeven. 

1. Controleer **Schema voor momentopnamen** en stel in dat uw gegevens elk uur worden vernieuwd met behulp van de vervolgkeuzelijst *Terugkeerpatroon*.  

1. Selecteer **Maken**.

    U hebt nu een actieve gegevensshare. Hiermee kunt u zien wat u ziet als een gegevensprovider wanneer u een gegevensshare maakt. 

1. Selecteer de gegevensshare die u hebt gemaakt met de naam **DataProvider**. U kunt hiernaar navigeren door **Verzonden shares** te selecteren in **Gegevensshare**. 

1. Klik op Schema voor momentopnamen. U kunt indien gewenst het schema voor momentopnamen uitschakelen. 

1. Selecteer vervolgens het tabblad **Gegevenssets**. U kunt extra gegevenssets toevoegen aan deze gegevensshare nadat deze is gemaakt. 

1. Selecteer het tabblad **Shareabonnementen**. Er zijn nog geen shareabonnementen omdat uw gegevensgebruiker de uitnodiging nog niet heeft geaccepteerd.

1. Ga naar het tabblad **Uitnodigingen**. Hier ziet u een lijst met openstaande uitnodiging(en). 

    ![Openstaande uitnodigingen](media/lab-data-flow-data-share/pending-invites.png)

1. Selecteer de uitnodiging voor *janedoe@fabrikam.com* . Selecteer Verwijderen. Als uw ontvanger de uitnodiging nog niet heeft geaccepteerd, kan hij of zij dat ook niet meer doen. 

1. Selecteer het tabblad **Geschiedenis**. Er wordt nog niets weergegeven, omdat uw gegevensgebruiker de uitnodiging nog niet heeft geaccepteerd en een momentopname heeft geactiveerd. 

### <a name="receiving-data-data-consumer-flow"></a>Gegevens ontvangen (gegevensgebruikersstroom)

Nu we onze gegevensshare hebben bekeken, zijn we klaar voor een andere context en te schakelen naar de rol van gegevensgebruiker. 

U hebt nu een uitnodiging voor een Azure Data Share in uw postvak in ontvangen van Microsoft Azure. Start Outlook Web Access (outlook.com) en meld u aan met de aanmeldingsgegevens die zijn opgegeven voor uw Azure-abonnement.

Klik in het e-mailbericht dat u hebt ontvangen op 'Uitnodiging weergeven >'. Op dit moment gaat u de ervaring van de gegevensgebruiker simuleren wanneer u een uitnodiging van gegevensprovider voor zijn of haar gegevensshare accepteert. 

![E-mailuitnodiging](media/lab-data-flow-data-share/email-invite.png)

U wordt mogelijk gevraagd om een abonnement te selecteren. Zorg ervoor dat u het abonnement selecteert waarmee u werkt voor dit lab. 

1. Klik op de uitnodiging met de naam *DataProvider*. 

1. In dit scherm Uitnodiging ziet u verschillende details over de gegevensshare die u eerder als gegevensprovider hebt geconfigureerd. Bekijk de details en accepteer de gebruiksrechtsvoorwaarden indien van toepassing.

1. Selecteer het Abonnement en de Resourcegroep die al bestaan voor uw lab. 

1. Voor **Gegevensshare-account** selecteert u **DataConsumer**. U kunt ook een nieuwe gegevensshare-account maken. 

1. Naast **Ontvangen sharenaam** ziet u dat de standaard sharenaam de naam is die is opgegeven door de gegevensprovider. Geef de share een beschrijvende naam die de gegevens beschrijft die u op het punt staat te ontvangen, bijvoorbeeld **TaxiDataShare**.

    ![Uitnodiging accepteren](media/lab-data-flow-data-share/consumer-accept.png)

1. U kunt kiezen voor **Nu accepteren en configureren** of **Later accepteren en configureren**. Als u ervoor kiest om nu te accepteren en te configureren, geeft u een opslagaccount op waarnaar alle gegevens moeten worden gekopieerd. Als u ervoor kiest om later te accepteren en te configureren, worden de gegevenssets in de share niet toegewezen en moet u ze handmatig toewijzen. We zullen hier later voor kiezen. 

1. Selecteer **Later accepteren en configureren**. 

    Bij het configureren van deze optie wordt een shareabonnement gemaakt, maar kunnen de gegevens nergens worden neergezet omdat er geen bestemming is toegewezen. 

    Daarna gaan we gegevenssettoewijzingen voor de gegevensshare configureren. 

1. Selecteer de Ontvangen share (de naam die u in stap 5 hebt opgegeven).

    **Trigger voor momentopname** wordt grijs weergegeven, maar de share is actief. 

1. Selecteer het tabblad **Gegevenssets**. U ziet dat elke gegevensset Niet-toegewezen is, wat betekent dat er geen bestemming is om gegevens naar te kopiëren. 

    ![niet-toegewezen gegevenssets](media/lab-data-flow-data-share/unmapped.png)

1. Selecteer de Azure Synapse Analytics-tabel en selecteer vervolgens **+Toewijzen aan doel**.

1. Selecteer aan de rechterkant van het scherm de vervolgkeuzelijst **Doelgegevenstype**. 

    U kunt de SQL-gegevens toewijzen aan een breed scala aan gegevensarchieven. In dit geval wijzen we toe aan een Azure SQL Database.

    ![toewijzing](media/lab-data-flow-data-share/mapping-options.png)
    
    (Optioneel) Selecteer **Azure Data Lake Store Gen2** als het doelgegevenstype. 
    
    (Optioneel) Selecteer het abonnement, de resourcegroep en het opslagaccount waarin u werkt. 
    
    (Optioneel) U kunt ervoor kiezen om de gegevens in uw data lake te ontvangen in de CSV- of Parquet-indeling. 

1. Selecteer Azure SQL Database naast **Doelgegevenstype**. 

1. Selecteer het abonnement, de resourcegroep en het opslagaccount waarin u werkt. 

    ![toewijzen aan sql](media/lab-data-flow-data-share/map-to-sqldb.png)

1. Voordat u kunt doorgaan, moet u een nieuwe gebruiker maken in de SQL Server door het geleverde script uit te voeren. Kopieer eerst het geleverde script naar het klembord. 

1. Open een nieuw tabblad in de Azure-portal. Sluit uw bestaande tabbladen niet, omdat u er zo meteen weer naar moet terugkeren. 

1. Ga in het nieuwe tabblad dat u hebt geopend naar **SQL-databases**.

1. Selecteer de SQL-database (er mag er zich slechts één in uw abonnement bevinden). Pas op dat u de datawarehouse niet selecteert. 

1. Selecteer **Query-editor (preview)**

1. Gebruik AAD-verificatie om u aan te melden bij de Query-editor. 

1. Voer de query uit die wordt geboden in de gegevensshare (gekopieerd naar het klembord in stap 14). 

    Met deze opdracht geeft u de Azure Data Share-service de mogelijkheid om beheerde identiteiten te gebruiken voor Azure-Services om te verifiëren of de SQL Server gegevens kan kopiëren. 

1. Ga terug naar het oorspronkelijke tabblad en selecteer **Toewijzen aan doel**.

1. Selecteer vervolgens de map Azure Data Lake Gen2 die deel uitmaakt van de gegevensset en wijs deze toe aan een Azure Blob Storage-account. 

    ![opslag](media/lab-data-flow-data-share/storage-map.png)

    Als alle gegevenssets zijn toegewezen, bent u er klaar voor om gegevens te ontvangen van de gegevensprovider. 

    ![toegewezen](media/lab-data-flow-data-share/all-mapped.png)
    
1. Selecteer **Details**. 

    U ziet dat **Momentopname activeren** niet langer grijs wordt weergegeven, omdat de gegevensshare nu bestemmingen heeft waarnaar moet worden gekopieerd.

1. Selecteer Momentopname activeren -> Volledige kopie. 

    ![activeren](media/lab-data-flow-data-share/trigger-full.png)

    Hiermee wordt begonnen met het kopiëren van gegevens naar het nieuwe gegevensshare-account. In een praktijkscenario zijn deze gegevens afkomstig van derden. 

    Het duurt ongeveer 3-5 minuten voordat de gegevens worden overgedragen. U kunt de voortgang controleren door te klikken op het tabblad **Geschiedenis**. 

    Terwijl u wacht, gaat u naar de oorspronkelijke gegevensshare (DataProvider) en bekijkt u de status van de tabbladen **Shareabonnementen** en **Geschiedenis**. U ziet dat er nu een actief abonnement is en als gegevensprovider kunt u ook controleren wanneer de gegevensgebruiker de gegevens heeft ontvangen die met hem of haar zijn gedeeld. 

1. Ga terug naar de gegevensshare van de gegevensgebruiker. Zodra de status van de trigger is geslaagd, gaat u naar de doel-SQL-database en data lake om te zien dat de gegevens in de respectieve archieven zijn terechtgekomen. 

Gefeliciteerd, u hebt het lab voltooid.
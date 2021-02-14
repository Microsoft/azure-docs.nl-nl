---
title: Functies voor het optimaliseren van de activiteit prestaties
description: Meer informatie over de belangrijkste functies waarmee u de prestaties van de Kopieer activiteit kunt optimaliseren in Azure Data Factory Marketplace.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/24/2020
ms.openlocfilehash: ecb4550b218b069273cba2e3d70a9510c1cc74ca
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387798"
---
# <a name="copy-activity-performance-optimization-features"></a>Functies voor het optimaliseren van de activiteit prestaties

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel vindt u een overzicht van de functies voor het optimaliseren van de Kopieer activiteit, die u kunt gebruiken in Azure Data Factory.

## <a name="data-integration-units"></a>Eenheden voor gegevensintegratie

Een gegevens integratie-eenheid is een meting die de kracht vertegenwoordigt (een combi natie van CPU, geheugen en netwerk bron toewijzing) van één eenheid in Azure Data Factory. De gegevens integratie-eenheid is alleen van toepassing op [Azure Integration runtime](concepts-integration-runtime.md#azure-integration-runtime), maar niet [zelf-hostende Integration runtime](concepts-integration-runtime.md#self-hosted-integration-runtime).

De toegestane DIUs om de uitvoering van een Kopieer activiteit te stimuleren, ligt **tussen 2 en 256**. Als u de gebruikers interface niet opgeeft of ' automatisch ' kiest, wordt de optimale DIU-instelling door Data Factory dynamisch toegepast op basis van uw combi natie van bron-Sink en gegevens patroon. De volgende tabel geeft een overzicht van de ondersteunde DIU-bereiken en het standaard gedrag in verschillende Kopieer scenario's:

| Scenario kopiëren | Ondersteund DIU-bereik | Standaard DIUs bepaald door service |
|:--- |:--- |---- |
| Tussen bestands archieven |- **Kopiëren van of naar één bestand**: 2-4 <br>- **Kopiëren van en naar meerdere bestanden**: 2-256, afhankelijk van het aantal en de grootte van de bestanden <br><br>Als u bijvoorbeeld gegevens kopieert vanuit een map met vier grote bestanden en ervoor kiest om de hiërarchie te behouden, is de maximale effectief DIU 16; Wanneer u ervoor kiest om het bestand samen te voegen, is de Maxi maal bedoel bare DIU 4. |Tussen 4 en 32, afhankelijk van het aantal en de grootte van de bestanden |
| Uit bestands archief naar niet-bestands archief |- **Kopiëren uit één bestand**: 2-4 <br/>- **Kopiëren uit meerdere bestanden**: 2-256, afhankelijk van het aantal en de grootte van de bestanden <br/><br/>Als u bijvoorbeeld gegevens kopieert vanuit een map met vier grote bestanden, is de maximale ingangs DIU 16. |- **Kopieer naar Azure SQL database of Azure Cosmos DB**: tussen 4 en 16, afhankelijk van de Sink-laag (Dtu's/RUs) en het bron bestands patroon<br>- **Kopieer naar Azure Synapse Analytics** met de poly base-of kopieer instructie: 2<br>-Ander scenario: 4 |
| Van niet-bestands opslag naar File Store |- **Kopiëren van gegevens archieven** die zijn ingeschakeld voor partities (inclusief [Azure SQL database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL Managed instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)en [Teradata](connector-teradata.md#teradata-as-source)): 2-256 wanneer u naar een map schrijft en 2-4 wanneer u naar één bestand schrijft. Opmerking per bron gegevens partitie kan Maxi maal 4 DIUs gebruiken.<br>- **Andere scenario's**: 2-4 |- **Kopiëren van rest of http**: 1<br/>- **Kopiëren van Amazon Redshift** met behulp van verwijderen: 2<br>- **Ander scenario**: 4 |
| Tussen niet-bestands archieven |- **Kopiëren van gegevens archieven** die zijn ingeschakeld voor partities (inclusief [Azure SQL database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL Managed instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)en [Teradata](connector-teradata.md#teradata-as-source)): 2-256 wanneer u naar een map schrijft en 2-4 wanneer u naar één bestand schrijft. Opmerking per bron gegevens partitie kan Maxi maal 4 DIUs gebruiken.<br/>- **Andere scenario's**: 2-4 |- **Kopiëren van rest of http**: 1<br>- **Ander scenario**: 4 |

U kunt de DIUs die voor elke Kopieer bewerking wordt gebruikt, bekijken in de weer gave controle activiteit controleren of activiteiten uitvoer. Zie voor meer informatie [controle activiteit kopiëren](copy-activity-monitoring.md). Als u deze standaard instelling wilt overschrijven, geeft u als volgt een waarde op voor de `dataIntegrationUnits` eigenschap. Het *werkelijke aantal DIUs* dat de Kopieer bewerking tijdens runtime gebruikt, is gelijk aan of kleiner dan de geconfigureerde waarde, afhankelijk van het gegevens patroon.

Er wordt een bedrag in rekening gebracht **met de gebruikte DIUs \* kopie duur \* eenheids prijs/DIU-uur**. Bekijk [hier](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)de huidige prijzen. Lokale valuta en afzonderlijke korting kunnen per abonnements type worden toegepast.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "dataIntegrationUnits": 128
        }
    }
]
```

## <a name="self-hosted-integration-runtime-scalability"></a>Schaal baarheid van zelf-hostende Integration runtime

Als u een hogere door voer wilt doen, kunt u de zelf-hostende IR schalen of uitschalen:

- Als de CPU en het beschik bare geheugen op het zelf-hostende IR-knoop punt niet volledig worden gebruikt, maar de uitvoering van gelijktijdige taken de limiet bereikt, moet u omhoog schalen door het aantal gelijktijdige taken dat kan worden uitgevoerd op een knoop punt te verhogen.  Zie [hier](create-self-hosted-integration-runtime.md#scale-up) voor instructies.
- Als de CPU daarentegen hoog is op het zelf-hostende IR-knoop punt of het beschik bare geheugen is laag, kunt u een nieuw knoop punt toevoegen om de belasting over meerdere knoop punten te verg Roten.  Zie [hier](create-self-hosted-integration-runtime.md#high-availability-and-scalability) voor instructies.

Opmerking in de volgende scenario's kan de uitvoering van één Kopieer activiteit gebruikmaken van meerdere zelf-hostende IR-knoop punten:

- Gegevens kopiëren van archieven op basis van bestanden, afhankelijk van het aantal en de grootte van de bestanden.
- Kopieer gegevens uit het gegevens archief met partitie opties (inclusief [Azure SQL database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL Managed instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SAP Hana](connector-sap-hana.md#sap-hana-as-source), [SAP open hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), [SAP-tabel](connector-sap-table.md#sap-table-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)en [Teradata](connector-teradata.md#teradata-as-source)), afhankelijk van het aantal gegevens partities.

## <a name="parallel-copy"></a>Parallelle kopie

U kunt een parallelle kopie ( `parallelCopies` eigenschap) instellen op Kopieer activiteit om aan te geven in welke parallellisme u de Kopieer activiteit wilt gebruiken. U kunt deze eigenschap beschouwen als het maximum aantal threads in de Kopieer activiteit dat vanuit uw bron wordt gelezen of op parallelle gegevens opslag wordt geschreven.

De parallelle kopie is een rechthoek met [gegevens integratie-eenheden](#data-integration-units) of [zelf-hostende IR-knoop punten](#self-hosted-integration-runtime-scalability). De waarde wordt geteld op alle DIUs of zelf-hostende IR-knoop punten.

Voor elke uitvoering van de Kopieer activiteit wordt standaard Azure Data Factory dynamisch de instelling voor de optimale parallelle kopie toegepast op basis van uw bron-Sink en gegevens patroon. 

> [!TIP]
> Het standaard gedrag van parallelle kopieën biedt doorgaans de beste door Voer, die automatisch wordt bepaald door ADF op basis van uw source-Sink-paar, het gegevens patroon en het aantal DIUs of de zelf-hostende IR/het CPU/geheugen/het aantal knoop punten. Raadpleeg de [prestaties van de Kopieer activiteit oplossen](copy-activity-performance-troubleshooting.md) wanneer u parallelle kopieën afstemt.

De volgende tabel geeft een overzicht van het gedrag van parallelle kopieën:

| Scenario kopiëren | Gedrag van parallelle Kopieer bewerking |
| --- | --- |
| Tussen bestands archieven | `parallelCopies` bepaalt de parallellisme **op bestands niveau**. De Chunking binnen elk bestand vindt onder automatisch en transparant plaats. Het is ontworpen om de beste juiste segment grootte te gebruiken voor een gegeven type gegevens opslag om gegevens parallel te laden. <br/><br/>Het werkelijke aantal Kopieer activiteiten voor parallelle kopieën dat tijdens de uitvoerings tijd wordt gebruikt, is niet groter dan het aantal bestanden dat u hebt. Als het Kopieer gedrag wordt **mergeFile** in de bestands sink, kan de Kopieer activiteit niet profiteren van parallellisme op bestands niveau. |
| Uit bestands archief naar niet-bestands archief | -Bij het kopiëren van gegevens naar Azure SQL Database of Azure Cosmos DB is de standaard parallelle kopie ook afhankelijk van de Sink-laag (aantal Dtu's/RUs).<br>-Bij het kopiëren van gegevens naar een Azure-tabel is de standaard parallelle kopie 4. |
| Van niet-bestands opslag naar File Store | -Bij het kopiëren van gegevens uit een gegevens archief met partitie opties ( [inclusief Azure SQL database](connector-azure-sql-database.md#azure-sql-database-as-the-source), Azure [SQL Managed instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SAP Hana](connector-sap-hana.md#sap-hana-as-source), [SAP open hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), [SAP-tabel](connector-sap-table.md#sap-table-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)en [Teradata](connector-teradata.md#teradata-as-source)), is de standaard parallelle kopie 4. Het werkelijke aantal kopieën van de parallelle Kopieer activiteit wordt gebruikt tijdens de uitvoering van het aantal gegevens partities dat u hebt. Wanneer u zelf-hostende Integration Runtime gebruikt en naar Azure Blob/ADLS Gen2 kopieert, noteert u de maximale efficiënte parallelle kopie 4 of 5 per IR-knoop punt.<br>-Voor andere scenario's worden parallelle kopieën niet van kracht. Zelfs als parallelisme is opgegeven, wordt het niet toegepast. |
| Tussen niet-bestands archieven | -Bij het kopiëren van gegevens naar Azure SQL Database of Azure Cosmos DB is de standaard parallelle kopie ook afhankelijk van de Sink-laag (aantal Dtu's/RUs).<br/>-Bij het kopiëren van gegevens uit een gegevens archief met partitie opties ( [inclusief Azure SQL database](connector-azure-sql-database.md#azure-sql-database-as-the-source), Azure [SQL Managed instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SAP Hana](connector-sap-hana.md#sap-hana-as-source), [SAP open hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), [SAP-tabel](connector-sap-table.md#sap-table-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)en [Teradata](connector-teradata.md#teradata-as-source)), is de standaard parallelle kopie 4.<br>-Bij het kopiëren van gegevens naar een Azure-tabel is de standaard parallelle kopie 4. |

Als u de belasting wilt beheren op machines die uw gegevens archieven hosten, of als u de Kopieer prestaties wilt afstemmen, kunt u de standaard waarde overschrijven en een waarde voor de `parallelCopies` eigenschap opgeven. De waarde moet een geheel getal zijn dat groter is dan of gelijk is aan 1. Tijdens runtime gebruikt de Kopieer activiteit een waarde die kleiner is dan of gelijk is aan de waarde die u hebt ingesteld.

Wanneer u een waarde voor de `parallelCopies` eigenschap opgeeft, neemt u de belasting verhoging op uw bron-en Sink-gegevens op in het account. Denk ook na over de belasting verhoging voor de zelf-hostende Integration runtime als de Kopieer activiteit hiervoor is gemachtigd. Deze belasting toename treedt vooral op wanneer u meerdere activiteiten of gelijktijdige uitvoeringen hebt van dezelfde activiteiten die worden uitgevoerd op hetzelfde gegevens archief. Als u merkt dat het gegevens archief of de zelf-hostende Integration runtime wordt overspoeld met de belasting, vermindert u de `parallelCopies` waarde om de belasting te ontlasten.

**Voorbeeld:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

## <a name="staged-copy"></a>Gefaseerde kopie

Wanneer u gegevens uit een brongegevens archief naar een Sink-gegevens archief kopieert, kunt u ervoor kiezen om Azure Blob Storage of Azure Data Lake Storage Gen2 te gebruiken als een tijdelijke faserings opslag. Fase ring is met name handig in de volgende gevallen:

- **U wilt gegevens uit verschillende gegevens archieven opnemen in azure Synapse Analytics via Poly Base, gegevens kopiëren van/naar sneeuw of gegevens opnemen vanuit Amazon Redshift/HDFS performantly.** Meer informatie over:
  - [Gebruik poly Base om gegevens te laden in azure Synapse Analytics](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-synapse-analytics).
  - [Sneeuw-connector](connector-snowflake.md)
  - [Amazon Redshift-connector](connector-amazon-redshift.md)
  - [HDFS-connector](connector-hdfs.md)
- **U wilt geen andere poorten dan poort 80 en poort 443 openen in uw firewall vanwege het IT-beleid van het bedrijf.** Wanneer u bijvoorbeeld gegevens van een on-premises gegevens archief naar een Azure SQL Database of een Azure Synapse-analyse kopieert, moet u uitgaande TCP-communicatie activeren op poort 1433 voor zowel Windows Firewall als uw bedrijfs firewall. In dit scenario kan de gefaseerde kopie gebruikmaken van de zelf-hostende Integration runtime om eerst gegevens te kopiëren naar een staging-opslag via HTTP of HTTPS op poort 443, en vervolgens de gegevens van staging te laden in SQL Database of Azure Synapse Analytics. In deze stroom hoeft u poort 1433 niet in te scha kelen.
- **Soms duurt het even om een hybride gegevens verplaatsing (dat wil zeggen, kopiëren van een on-premises gegevens archief naar een gegevens archief in de Cloud) uit te voeren via een trage netwerk verbinding.** Om de prestaties te verbeteren, kunt u gefaseerde kopie gebruiken om de gegevens on-premises te comprimeren, zodat het minder tijd kost om gegevens te verplaatsen naar de faserings gegevens opslag in de Cloud. Vervolgens kunt u de gegevens in het faserings archief decomprimeren voordat u ze laadt in de doel gegevens opslag.

### <a name="how-staged-copy-works"></a>Hoe gefaseerd kopiëren werkt

Wanneer u de faserings functie activeert, worden de gegevens eerst uit de brongegevens opslag gekopieerd naar de staging-opslag (neem uw eigen Azure Blob of Azure Data Lake Storage Gen2). Vervolgens worden de gegevens uit de fase ring naar het gegevens archief van de Sink gekopieerd. Azure Data Factory Kopieer activiteit beheert automatisch de tweestage stroom voor u en worden er ook tijdelijke gegevens uit de staging-opslag opgeschoond nadat de gegevens verplaatsing is voltooid.

![Gefaseerde kopie](media/copy-activity-performance/staged-copy.png)

Wanneer u gegevens verplaatsing activeert met behulp van een faserings opslag, kunt u opgeven of u wilt dat de gegevens worden gecomprimeerd voordat u gegevens van de brongegevens opslag naar de faserings opslag verplaatst en vervolgens gedecomprimeerd voordat u gegevens verplaatst van een tussentijds-of faserings gegevens archief naar de Sink-gegevens opslag.

Op dit moment kunt u geen gegevens kopiëren tussen twee gegevens archieven die zijn verbonden via een andere zelf-hostende IRs, noch zonder een gefaseerde kopie. Voor dit scenario kunt u twee expliciet gekoppelde Kopieer activiteiten configureren om te kopiëren van bron naar fase ring en vervolgens van fase ring naar sink.

### <a name="configuration"></a>Configuratie

Configureer de instelling **enableStaging** in de Kopieer activiteit om op te geven of u wilt dat de gegevens in de opslag worden klaargezet voordat u deze in een doel gegevens archief laadt. Wanneer u **enableStaging** instelt op `TRUE` , geeft u de aanvullende eigenschappen op die in de volgende tabel worden weer gegeven. 

| Eigenschap | Beschrijving | Standaardwaarde | Vereist |
| --- | --- | --- | --- |
| enableStaging |Geef op of u gegevens wilt kopiëren via een tijdelijke faserings opslag. |Niet waar |No |
| linkedServiceName |Geef de naam op van een [Azure Blob-opslag](connector-azure-blob-storage.md#linked-service-properties) of [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) gekoppelde service, die verwijst naar het exemplaar van de opslag die u gebruikt als een tijdelijke faserings opslag. |N.v.t. |Ja, wanneer **enableStaging** is ingesteld op True |
| leertraject |Geef het pad op dat u wilt dat de gefaseerde gegevens bevat. Als u geen pad opgeeft, maakt de service een container om tijdelijke gegevens op te slaan. |N.v.t. |No |
| enableCompression |Hiermee geeft u op of gegevens moeten worden gecomprimeerd voordat ze naar het doel worden gekopieerd. Deze instelling vermindert het volume van de gegevens die worden overgedragen. |Niet waar |No |

>[!NOTE]
> Als u een gefaseerde kopie gebruikt terwijl compressie is ingeschakeld, wordt de service-principal of MSI-verificatie voor de gekoppelde BLOB-hostservice niet ondersteund.

Hier volgt een voor beeld van een Kopieer activiteit met de eigenschappen die in de voor gaande tabel worden beschreven:

```json
"activities":[
    {
        "name": "CopyActivityWithStaging",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "OracleSource",
            },
            "sink": {
                "type": "SqlDWSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path"
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>Facturerings impact voor gefaseerde kopie

Er worden kosten in rekening gebracht op basis van twee stappen: de duur en het Kopieer type kopiëren.

* Wanneer u fase ring gebruikt tijdens een Cloud kopie, die gegevens uit een gegevens archief in de cloud kopieert naar een ander gegevens archief in de Cloud, wordt de [som van de Kopieer duur voor stap 1 en stap 2] x [eenheids prijs voor de Cloud kopie] in rekening gebracht.
* Wanneer u fase ring gebruikt tijdens een hybride kopie, waarmee gegevens worden gekopieerd van een on-premises gegevens opslag naar een gegevens archief in de Cloud, wordt één fase van een zelf-hostende Integration runtime in rekening gebracht voor [Hybrid Copy duration] x [hybride kopie van de eenheids prijs] + [duur van de Cloud kopie] x [eenheids prijs van de Cloud].

## <a name="next-steps"></a>Volgende stappen
Zie de andere artikelen over Kopieer activiteiten:

- [Overzicht van de Kopieer activiteit](copy-activity-overview.md)
- [Handleiding voor prestaties en schaalbaarheid van kopieeractiviteit](copy-activity-performance.md)
- [Prestaties van de Kopieer activiteit oplossen](copy-activity-performance-troubleshooting.md)
- [Azure Data Factory gebruiken om gegevens van uw data Lake of Data Warehouse te migreren naar Azure](data-migration-guidance-overview.md)
- [Gegevens migreren van Amazon S3 naar Azure Storage](data-migration-guidance-s3-azure-storage.md)
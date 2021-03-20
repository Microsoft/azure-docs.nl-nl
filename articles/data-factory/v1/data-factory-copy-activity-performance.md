---
title: Copy Activity performance and tuning guide (Gids voor prestaties en configuratie van Activiteit kopiëren)
description: Meer informatie over de belangrijkste factoren die van invloed zijn op de prestaties van het verplaatsen van gegevens in Azure Data Factory wanneer u de Kopieer activiteit gebruikt.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 9a890719de39a71d8336d39f9932e73f7baccf87
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100377207"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Copy Activity performance and tuning guide (Gids voor prestaties en configuratie van Activiteit kopiëren)

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-copy-activity-performance.md)
> * [Versie 2 (huidige versie)](../copy-activity-performance.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u de [hand leiding Copy activity Performance and Tuning for Data Factory](../copy-activity-performance.md).

Azure Data Factory Copy-activiteit levert een eersteklas, veilige, betrouw bare en hoogwaardige oplossing voor het laden van gegevens. Zo kunt u tien tallen terabytes aan gegevens elke dag kopiëren over een groot aantal verschillende Cloud-en on-premises gegevens opslag. Ultra snelle prestaties voor het laden van gegevens is essentieel om ervoor te zorgen dat u zich kunt richten op het belangrijkste probleem met de big data: het bouwen van geavanceerde analyse oplossingen en het verkrijgen van grondige inzichten van al deze gegevens.

Azure biedt een reeks hoogwaardige oplossingen voor gegevens opslag en Data Warehouse, en kopieer activiteiten bieden een zeer geoptimaliseerde ervaring voor het laden van gegevens die eenvoudig te configureren en instellen is. Met slechts één Kopieer activiteit kunt u het volgende doen:

* Gegevens laden in **Azure Synapse Analytics** op **1,2 Gbps**. Zie voor een overzicht met een use-case [1 TB in azure Synapse Analytics onder 15 minuten laden met Azure Data Factory](data-factory-load-sql-data-warehouse.md).
* Gegevens laden in **Azure Blob-opslag** op **1,0 Gbps**
* Gegevens laden in **Azure data Lake Store** op **1,0 Gbps**

In dit artikel wordt het volgende beschreven:

* [Prestatie referentie nummers](#performance-reference) voor ondersteunde bron-en Sink-gegevens opslag om u te helpen bij het plannen van uw project.
* Functies waarmee de Kopieer doorvoer kan worden verhoogd in verschillende scenario's, waaronder [gegevens verplaatsings eenheden](#cloud-data-movement-units)voor de Cloud, [parallelle kopie](#parallel-copy)en [gefaseerde kopie](#staged-copy);
* [Richt lijnen voor het afstemmen](#performance-tuning-steps) van de prestaties van de prestaties en de belangrijkste factoren die van invloed kunnen zijn op de Kopieer prestaties.

> [!NOTE]
> Als u niet bekend bent met de Kopieer activiteit in het algemeen, raadpleegt u [gegevens verplaatsen met behulp van Kopieer activiteit voordat u](data-factory-data-movement-activities.md) dit artikel leest.
>

## <a name="performance-reference"></a>Naslag informatie over prestaties

Als referentie wordt onder tabel het Kopieer doorvoer nummer in MBps weer gegeven voor de opgegeven bron-en Sink-paren op basis van interne tests. Ter vergelijking ziet u ook hoe verschillende instellingen van de [verplaatsings eenheden voor Cloud gegevens](#cloud-data-movement-units) of [Data Management Gateway schaal baarheid](data-factory-data-management-gateway-high-availability-scalability.md) (meerdere gateway knooppunten) kunnen helpen bij het kopiëren van prestaties.

![Prestatie matrix](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>In Azure Data Factory versie 1 zijn de minimale eenheden voor het verplaatsen van de Cloud gegevens voor Cloud-naar-Cloud kopie twee. Als u dit niet opgeeft, ziet u standaard eenheden voor gegevens verplaatsing die worden gebruikt in [eenheden voor gegevens verplaatsing](#cloud-data-movement-units)in de Cloud.

**Punten om te noteren:**
* De door Voer wordt berekend met behulp van de volgende formule: [grootte van gegevens die uit de bron zijn gelezen]/[duur van de Kopieer activiteit].
* De prestatie referentie nummers in de tabel zijn gemeten met behulp van de [TPC-H-](http://www.tpc.org/tpch/) gegevensset in één exemplaar van de Kopieer activiteit.
* In azure-gegevens archieven bevinden de bron en Sink zich in dezelfde Azure-regio.
* Voor hybride kopieën tussen on-premises en gegevens opslag in de Cloud, werd elk gateway knooppunt uitgevoerd op een computer die is gescheiden van de on-premises gegevens opslag met onderstaande specificatie. Als er één activiteit op de gateway werd uitgevoerd, gebruikt de Kopieer bewerking slechts een klein deel van de CPU, het geheugen of de netwerk bandbreedte van de test machine. Meer informatie vindt u in [overweging voor Data Management Gateway](#considerations-for-data-management-gateway).
    <table>
    <tr>
        <td>CPU</td>
        <td>32 kernen 2,20 GHz Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>Geheugen</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>Netwerk</td>
        <td>Internet-interface: 10 Gbps; intranet Interface: 40 Gbps</td>
    </tr>
    </table>


> [!TIP]
> U kunt een hogere door Voer gebruiken door gebruik te maken van meer DMUs (Data units) dan de standaard maximale DMUs. Dit is 32 voor het uitvoeren van een Cloud-naar-Cloud-Kopieer activiteit. Met 100 DMUs kunt u bijvoorbeeld gegevens van Azure-Blob kopiëren naar Azure Data Lake Store op **1.0 Gbps**. Zie de sectie [eenheden voor gegevens verplaatsing](#cloud-data-movement-units) in de Cloud voor meer informatie over deze functie en het Ondersteunde scenario. Neem contact op met de [ondersteuning van Azure](https://azure.microsoft.com/support/) om meer DMUs aan te vragen.

## <a name="parallel-copy"></a>Parallelle kopie
U kunt gegevens uit de bron lezen of gegevens naar het doel schrijven, **parallel binnen het uitvoeren van een Kopieer activiteit**. Deze functie verbetert de door Voer van een Kopieer bewerking en vermindert de tijd die nodig is om gegevens te verplaatsen.

Deze instelling wijkt af van de **gelijktijdigheids** eigenschap in de definitie van de activiteit. De **gelijktijdigheids** eigenschap bepaalt het aantal **uitvoeringen van de gelijktijdige Kopieer activiteit** voor het verwerken van gegevens uit verschillende activiteiten Vensters (1 tot 2 uur, 2 uur tot 3 uur, 3 tot 4 uur, enzovoort). Deze functie is handig wanneer u een historische belasting uitvoert. De functionaliteit voor parallel kopiëren is van toepassing op **één uitvoering** van de activiteit.

Laten we eens kijken naar een voorbeeld scenario. In het volgende voor beeld moeten meerdere segmenten van het verleden worden verwerkt. Data Factory voert een exemplaar van de Kopieer activiteit (een uitvoering van de activiteit) uit voor elk segment:

* Het gegevens segment van het eerste activiteiten venster (1 uur tot 2 uur) = => activiteit uitvoeren 1
* Het gegevens segment van het tweede activiteiten venster (2 tot 3 uur) = => activiteit 2
* Het gegevens segment van het tweede activiteiten venster (3 tot 4 uur) = => activiteit uitvoeren 3

Enzovoort.

In dit voor beeld, wanneer de **gelijktijdigheids** waarde is ingesteld op 2, **activiteit uitvoeren 1** en **activiteit 2** gegevens gelijktijdig kopiëren van twee activiteits **Vensters,** om de prestaties van de gegevens verplaatsing te verbeteren. Als er echter meerdere bestanden zijn gekoppeld aan de uitvoering van de activiteit 1, kopieert de gegevens verplaatsings service bestanden van de bron naar de bestemming één bestand per keer.

### <a name="cloud-data-movement-units"></a>Eenheden voor gegevens verplaatsing in de Cloud
Een **DMU (Cloud gegevens verplaatsings eenheid)** is een meting die de kracht vertegenwoordigt (een combi natie van CPU, geheugen en netwerk bron toewijzing) van één eenheid in Data Factory. DMU is van toepassing op Kopieer bewerkingen in de Cloud, maar niet in een hybride kopie.

**De minimale eenheden voor gegevens verplaatsing in de cloud om de uitvoering van de Kopieer activiteit te stimuleren, is twee.** Als dat niet is opgegeven, wordt in de volgende tabel de standaard DMUs weer gegeven die worden gebruikt in verschillende Kopieer scenario's:

| Scenario kopiëren | Standaard DMUs bepaald door service |
|:--- |:--- |
| Gegevens kopiëren tussen archieven op basis van bestanden | Tussen 4 en 16, afhankelijk van het aantal en de grootte van de bestanden. |
| Alle andere Kopieer scenario's | 4 |

Als u deze standaard instelling wilt overschrijven, geeft u als volgt een waarde op voor de eigenschap **cloudDataMovementUnits** . De **toegestane waarden** voor de eigenschap **cloudDataMovementUnits** zijn 2, 4, 8, 16, 32. Het **werkelijke aantal Cloud DMUs** dat de Kopieer bewerking wordt gebruikt tijdens runtime, is gelijk aan of kleiner dan de geconfigureerde waarde, afhankelijk van het gegevens patroon. Voor informatie over het prestatie niveau dat u kunt krijgen wanneer u meer eenheden voor een specifieke Kopieer bron en Sink configureert, raadpleegt u de [prestatie verwijzing](#performance-reference).

```json
"activities":[
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "cloudDataMovementUnits": 32
        }
    }
]
```

> [!NOTE]
> Neem contact op met de [ondersteuning van Azure](https://azure.microsoft.com/support/)als u meer Cloud DMUs nodig hebt voor een hogere door voer. De instelling van 8 en hoger werkt momenteel alleen wanneer u **meerdere bestanden kopieert van Blob Storage/Data Lake Store/Amazon S3/Cloud FTP/Cloud SFTP naar Blob Storage/Data Lake Store/Azure SQL database**.
>

### <a name="parallelcopies"></a>parallelCopies
U kunt de eigenschap **parallelCopies** gebruiken om aan te geven welke parallellisme u wilt gebruiken om te kopiëren. U kunt deze eigenschap beschouwen als het maximum aantal threads in de Kopieer activiteit dat kan worden gelezen vanuit uw bron of naar uw Sink-gegevens opslag kunt schrijven, parallel.

Voor elke uitvoering van de Kopieer activiteit bepaalt Data Factory het aantal parallelle kopieën dat moet worden gebruikt voor het kopiëren van gegevens uit de brongegevens opslag en naar het doel gegevens archief. Het standaard aantal parallelle kopieën dat wordt gebruikt, is afhankelijk van het type bron en Sink dat u gebruikt.

| Bron en sink | Standaard aantal parallelle kopieën bepaald door service |
| --- | --- |
| Gegevens kopiëren tussen archieven op basis van bestanden (Blob Storage; Data Lake Store; Amazon S3; een on-premises bestands systeem; een on-premises HDFS) |Tussen 1 en 32. Is afhankelijk van de grootte van de bestanden en het aantal DMUs (Cloud data units) dat wordt gebruikt voor het kopiëren van gegevens tussen twee gegevens archieven in de Cloud of de fysieke configuratie van de gateway computer die wordt gebruikt voor een hybride kopie (om gegevens naar of van een on-premises gegevens opslag te kopiëren). |
| Gegevens uit **een brongegevens archief kopiëren naar Azure-tabel opslag** |4 |
| Alle andere bron-en Sink-paren |1 |

Normaal gesp roken moet het standaard gedrag u de beste door voer geven. Als u echter de belasting wilt beheren op machines die uw gegevens archieven hosten, of als u de Kopieer prestaties wilt afstemmen, kunt u ervoor kiezen de standaard waarde te overschrijven en een waarde voor de eigenschap **parallelCopies** op te geven. De waarde moet tussen 1 en 32 (inclusief) zijn. Tijdens runtime gebruikt de Kopieer activiteit een waarde die kleiner is dan of gelijk is aan de waarde die u hebt ingesteld.

```json
"activities":[
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 8
        }
    }
]
```
Punten om te noteren:

* Wanneer u gegevens kopieert tussen archieven op basis van bestanden, bepaalt de **parallelCopies** de parallelle factor op bestands niveau. De Chunking binnen één bestand gebeurt onder automatisch en transparant, en is ontworpen om de beste juiste segment grootte te gebruiken voor een bepaald type brongegevens opslag om gegevens parallel en rechthoekig naar parallelCopies te laden. Het werkelijke aantal parallelle kopieën dat de gegevens verplaatsings service gebruikt voor de Kopieer bewerking tijdens runtime is niet groter dan het aantal bestanden dat u hebt. Als het Kopieer gedrag **mergeFile** is, kan de Kopieer activiteit geen gebruik maken van parallellisme op bestands niveau.
* Wanneer u een waarde voor de eigenschap **parallelCopies** opgeeft, moet u rekening houden met de toename van de belasting van uw bron-en Sink-gegevens opslag, en naar de gateway als dit een hybride kopie is. Dit gebeurt met name wanneer u meerdere activiteiten of gelijktijdige uitvoeringen hebt van dezelfde activiteiten die worden uitgevoerd op dezelfde gegevens opslag. Als u merkt dat het gegevens archief of de gateway is overbelast met de belasting, verlaagt u de **parallelCopies** -waarde om de belasting te ontlasten.
* Wanneer u gegevens kopieert uit winkels die niet op bestanden zijn gebaseerd, wordt de eigenschap **parallelCopies** genegeerd door de service voor gegevens verplaatsing. Zelfs als parallelisme is opgegeven, wordt dit in dit geval niet toegepast.

> [!NOTE]
> U moet Data Management Gateway versie 1,11 of hoger gebruiken om de functie **parallelCopies** te gebruiken wanneer u een hybride kopie gaat maken.
>
>

Als u deze twee eigenschappen beter wilt gebruiken en de door Voer van gegevens verplaatsing wilt uitbreiden, raadpleegt u de voor beelden van use cases. U hoeft **parallelCopies** niet te configureren om te profiteren van het standaard gedrag. Als u configureert en **parallelCopies** te klein is, is het mogelijk dat meerdere Cloud-DMUs niet volledig worden gebruikt.

### <a name="billing-impact"></a>Facturerings impact
Het is **belang rijk** te weten dat u wordt gefactureerd op basis van de totale tijd van de Kopieer bewerking. Als een Kopieer taak wordt gebruikt om één uur te maken met één Cloud eenheid en nu 15 minuten duurt met vier Cloud eenheden, blijft de volledige factuur bijna hetzelfde. U kunt bijvoorbeeld vier Cloud eenheden gebruiken. De eerste cloud eenheid besteedt tien minuten, de tweede, 10 minuten, de derde, 5 minuten en de vierde, 5 minuten, alle in één Kopieer activiteit uit. Er worden kosten in rekening gebracht voor de totale Kopieer tijd (gegevens verplaatsing), die 10 + 10 + 5 + 5 = 30 minuten is. Het gebruik van **parallelCopies** heeft geen invloed op facturering.

## <a name="staged-copy"></a>Gefaseerde kopie
Wanneer u gegevens uit een brongegevens archief naar een Sink-gegevens archief kopieert, kunt u ervoor kiezen om Blob-opslag te gebruiken als een tijdelijke faserings opslag. Fase ring is met name handig in de volgende gevallen:

1. **U wilt gegevens uit verschillende gegevens archieven opnemen in azure Synapse Analytics via Poly base**. Azure Synapse Analytics maakt gebruik van poly Base als mechanisme voor hoge door Voer om een grote hoeveelheid gegevens in azure Synapse Analytics te laden. De bron gegevens moeten echter in Blob Storage zijn en moeten aan aanvullende criteria voldoen. Wanneer u gegevens laadt vanuit een ander gegevens archief dan Blob-opslag, kunt u het kopiëren van gegevens met behulp van tussenliggende staging-Blobopslag activeren. In dat geval voert Data Factory de vereiste gegevens transformaties uit om ervoor te zorgen dat het voldoet aan de vereisten van poly base. Vervolgens wordt poly base gebruikt voor het laden van gegevens in azure Synapse Analytics. Zie [poly Base gebruiken om gegevens te laden in azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-synapse-analytics)voor meer informatie. Zie voor een overzicht met een use-case [1 TB in azure Synapse Analytics onder 15 minuten laden met Azure Data Factory](data-factory-load-sql-data-warehouse.md).
2. **Soms duurt het even om een hybride gegevens verplaatsing uit te voeren (dat wil zeggen, kopiëren tussen een on-premises gegevens opslag en een gegevens archief in de Cloud) via een trage netwerk verbinding**. U kunt de prestaties verbeteren door de gegevens on-premises te comprimeren, zodat het minder tijd kost om gegevens te verplaatsen naar de faserings gegevens opslag in de Cloud. Vervolgens kunt u de gegevens in het faserings archief decomprimeren voordat u deze laadt in de doel gegevens opslag.
3. **U wilt geen andere poorten dan poort 80 en poort 443 in uw firewall openen vanwege een bedrijfs beleid**. Wanneer u bijvoorbeeld gegevens kopieert van een on-premises gegevens archief naar een Azure SQL Database sink of een Azure Synapse Analytics-sink, moet u uitgaande TCP-communicatie activeren op poort 1433 voor zowel Windows Firewall als uw bedrijfs firewall. In dit scenario maakt u gebruik van de gateway om eerst gegevens te kopiëren naar een staging-opslag kopie van een Blob Storage via HTTP of HTTPS op poort 443. Vervolgens laadt u de gegevens in SQL Database of Azure Synapse Analytics vanuit de fase van het maken van Blob-opslag. In deze stroom hoeft u poort 1433 niet in te scha kelen.

### <a name="how-staged-copy-works"></a>Hoe gefaseerd kopiëren werkt
Wanneer u de faserings functie activeert, worden de gegevens eerst van de brongegevens opslag gekopieerd naar de staging-gegevens opslag (neem uw eigen op). Vervolgens worden de gegevens uit de staging-gegevens opslag naar de Sink-gegevens opslag gekopieerd. Data Factory beheert automatisch de twee fase stroom voor u. Data Factory verwijdert ook tijdelijke gegevens uit de staging-opslag nadat de gegevens verplaatsing is voltooid.

In het scenario voor het kopiëren van de Cloud (zowel de bron-als de Sink-gegevens opslag bevinden zich in de Cloud), wordt de gateway niet gebruikt. De Data Factory-service voert de Kopieer bewerkingen uit.

![Gefaseerde kopie: Cloud scenario](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

In het scenario voor hybride kopieën (bron is on-premises en Sink bevindt zich in de Cloud), verplaatst de gateway gegevens van het brongegevens archief naar een staging-gegevens opslag. Data Factory-service verplaatst gegevens van de staging-gegevens opslag naar de Sink-gegevens opslag. Het kopiëren van gegevens uit een gegevens archief in de Cloud naar een on-premises gegevens opslag via staging wordt ook ondersteund met de omgekeerde stroom.

![Gefaseerde kopie: hybride scenario](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Wanneer u gegevens verplaatsing activeert met behulp van een faserings opslag, kunt u opgeven of u wilt dat de gegevens worden gecomprimeerd voordat u gegevens van de brongegevens opslag naar een tussentijds-of faserings archief verplaatst en vervolgens comprimeert voordat u gegevens verplaatst van een tussentijds-of faserings gegevens archief naar de Sink-gegevens opslag.

Op dit moment kunt u geen gegevens tussen twee on-premises gegevens archieven kopiëren met behulp van een staging Store. Deze optie is binnenkort beschikbaar.

### <a name="configuration"></a>Configuratie
Configureer de instelling **enableStaging** in de Kopieer activiteit om op te geven of u wilt dat de gegevens in Blob Storage worden klaargezet voordat u deze in een doel gegevens archief laadt. Wanneer u **enableStaging** INSTELT op True, geeft u de aanvullende eigenschappen op die in de volgende tabel worden weer gegeven. Als u er nog geen hebt, moet u ook een gekoppelde service voor het maken van een hand tekening voor gedeelde toegang met een Azure Storage of opslag voor de staging gebruiken.

| Eigenschap | Beschrijving | Standaardwaarde | Vereist |
| --- | --- | --- | --- |
| **enableStaging** |Geef op of u gegevens wilt kopiëren via een tijdelijke faserings opslag. |Niet waar |Nee |
| **linkedServiceName** |Geef de naam op van een gekoppelde [opslag](data-factory-azure-blob-connector.md#azure-storage-linked-service) -of [azurestoragesas zijn](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) -service, die verwijst naar het exemplaar van de opslag die u gebruikt als een tijdelijke faserings opslag. <br/><br/> U kunt geen opslag gebruiken met een Shared Access Signature om gegevens te laden in azure Synapse Analytics via Poly base. U kunt deze gebruiken in alle andere scenario's. |N.v.t. |Ja, wanneer **enableStaging** is ingesteld op True |
| **path** |Geef het pad op van de Blob-opslag waarvoor u de gefaseerde gegevens wilt opnemen. Als u geen pad opgeeft, maakt de service een container om tijdelijke gegevens op te slaan. <br/><br/> Geef alleen een pad op als u opslag gebruikt met een hand tekening voor gedeelde toegang of als u wilt dat tijdelijke gegevens zich op een specifieke locatie bevinden. |N.v.t. |Nee |
| **enableCompression** |Hiermee geeft u op of gegevens moeten worden gecomprimeerd voordat ze naar het doel worden gekopieerd. Deze instelling vermindert het volume van de gegevens die worden overgedragen. |Niet waar |Nee |

Hier volgt een voor beeld van de definitie van de Kopieer activiteit met de eigenschappen die in de voor gaande tabel worden beschreven:

```json
"activities":[
{
    "name": "Sample copy activity",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDBOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlSink"
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob",
            "path": "stagingcontainer/path",
            "enableCompression": true
        }
    }
}
]
```

### <a name="billing-impact"></a>Facturerings impact
Er worden kosten in rekening gebracht op basis van twee stappen: de duur en het Kopieer type kopiëren.

* Wanneer u fase ring gebruikt tijdens een Cloud kopie (gegevens uit een gegevens archief in de Cloud kopiëren naar een ander gegevens archief in de Cloud), wordt de [som van de Kopieer duur voor stap 1 en stap 2] x [eenheids prijs van de Cloud] in rekening gebracht.
* Wanneer u fase ring gebruikt tijdens een hybride kopie (het kopiëren van gegevens van een on-premises gegevens archief naar een gegevens archief in de Cloud), worden er kosten in rekening gebracht voor [Hybrid Copy duration] x [hybride kopie van de eenheids prijs] + [duur van de Cloud kopie] x [eenheids prijs voor de Cloud].

## <a name="performance-tuning-steps"></a>Stappen voor het afstemmen van prestaties
U wordt aangeraden deze stappen uit te voeren om de prestaties van uw Data Factory service af te stemmen met de Kopieer activiteit:

1. **Een basis lijn vaststellen**. Test uw pijp lijn tijdens de ontwikkelings fase met behulp van Kopieer activiteiten op basis van een representatief gegevens voorbeeld. U kunt het Data Factory [segment model](data-factory-scheduling-and-execution.md) gebruiken om de hoeveelheid gegevens te beperken waarmee u samenwerkt.

   De uitvoerings tijd en prestatie kenmerken verzamelen met behulp van de **app voor bewaking en beheer**. Kies **controleren & beheren** op de start pagina van Data Factory. Kies de **uitvoer gegevensset** in de structuur weergave. Kies in de lijst **activiteit Windows** de uitvoering van de Kopieer activiteit. **Activiteit Windows** vermeldt de duur van de Kopieer activiteit en de grootte van de gekopieerde gegevens. De door Voer wordt weer gegeven in het **activiteiten venster Verkenner**. Zie [Azure Data Factory pijp lijnen bewaken en beheren met behulp van de app voor bewaking en beheer](data-factory-monitor-manage-app.md)voor meer informatie over de app.

   ![Details uitvoering van activiteit](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   Verderop in dit artikel kunt u de prestaties en configuratie van uw scenario vergelijken om de [prestatie referentie](#performance-reference) van de activiteit van onze tests te kopiëren.
2. **Prestaties diagnosticeren en optimaliseren**. Als de prestaties die u ziet niet voldoen aan uw verwachtingen, moet u prestatie knelpunten identificeren. Optimaliseer vervolgens de prestaties om het effect van knel punten te verwijderen of te verminderen. Een volledige beschrijving van de prestatie diagnose valt buiten het bereik van dit artikel, maar hier volgen enkele algemene overwegingen:

   * Prestatie functies:
     * [Parallelle kopie](#parallel-copy)
     * [Eenheden voor gegevens verplaatsing in de Cloud](#cloud-data-movement-units)
     * [Gefaseerde kopie](#staged-copy)
     * [Schaal baarheid Data Management Gateway](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Gegevensbeheergateway](#considerations-for-data-management-gateway)
   * [Bron](#considerations-for-the-source)
   * [Sink](#considerations-for-the-sink)
   * [Serialisatie en deserialisatie](#considerations-for-serialization-and-deserialization)
   * [Compressie](#considerations-for-compression)
   * [Toewijzen van kolommen](#considerations-for-column-mapping)
   * [Andere overwegingen](#other-considerations)
3. **Breid de configuratie uit naar uw volledige gegevensset**. Wanneer u tevreden bent met de resultaten en prestaties van de uitvoering, kunt u de actieve periode van de definitie en pijp lijn uitvouwen om uw hele gegevensset te behandelen.

## <a name="considerations-for-data-management-gateway"></a>Overwegingen voor Data Management Gateway
**Gateway-installatie**: we raden u aan om een toegewezen computer te gebruiken om data management gateway te hosten. Zie [overwegingen voor het gebruik van Data Management Gateway](data-factory-data-management-gateway.md#considerations-for-using-gateway).

**Gateway bewaking en-omhoog/uitschalen**: Eén logische gateway met een of meer gateway knooppunten kan tegelijkertijd meerdere exemplaren van de Kopieer activiteit uitvoeren. U kunt bijna realtime moment opnamen van resource gebruik (CPU, geheugen, netwerk (in/uit), enzovoort) weer geven op een gateway computer, evenals het aantal gelijktijdige taken dat tegenover de limiet in het Azure Portal wordt uitgevoerd, Zie [gateway in de portal controleren](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal). Als u veel behoefte hebt aan hybride gegevens verplaatsing, met een groot aantal gelijktijdig uitgevoerde Kopieer activiteiten of met grote hoeveel heden gegevens die moeten worden gekopieerd, kunt u overwegen om de [gateway te verg Roten of te schalen](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) , zodat u uw resource beter kunt gebruiken of meer resources kunt inrichten.

## <a name="considerations-for-the-source"></a>Overwegingen voor de bron
### <a name="general"></a>Algemeen
Zorg ervoor dat de onderliggende gegevens opslag niet wordt overspoeld door andere workloads die op of worden uitgevoerd.

Zie voor micro soft-gegevens archieven [onderwerpen bewaken en afstemmen](#performance-reference) die specifiek zijn voor gegevens opslag en Help u inzicht krijgen in de prestatie kenmerken van het gegevens archief, de reactie tijden minimaliseren en de door Voer maximaliseren.

Als u gegevens kopieert van Blob-opslag naar Azure Synapse Analytics, kunt u gebruikmaken van **poly base** om de prestaties te verbeteren. Zie [poly Base gebruiken om gegevens te laden in azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-synapse-analytics) voor meer informatie. Zie voor een overzicht met een use-case [1 TB in azure Synapse Analytics onder 15 minuten laden met Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Gegevens archieven op basis van bestanden
*(Inclusief Blob Storage, Data Lake Store, Amazon S3, on-premises bestands systemen en on-premises HDFS)*

* De **gemiddelde bestands grootte en het aantal bestanden**: de Kopieer activiteit brengt gegevens één bestand per keer over. Met dezelfde hoeveelheid gegevens die moeten worden verplaatst, is de algehele door Voer lager als de gegevens uit een groot aantal kleine bestanden bestaan in plaats van een paar grote bestanden als gevolg van de Boots trap-fase voor elk bestand. Combi neer zo mogelijk kleine bestanden in grotere bestanden om een hogere door voer te krijgen.
* **Bestands indeling en-compressie**: voor meer manieren om de prestaties te verbeteren, zie de [overwegingen voor serialisatie en deserialisatie](#considerations-for-serialization-and-deserialization) en [overwegingen voor compressie](#considerations-for-compression) secties.
* Zie de sectie [overwegingen voor Data Management Gateway](#considerations-for-data-management-gateway) voor het scenario voor een **on-premises bestands systeem** , waarin **Data Management Gateway** is vereist.

### <a name="relational-data-stores"></a>Relationele gegevens archieven
*(Bevat SQL Database; Azure Synapse Analytics; Amazon RedShift; SQL Server data bases; en Oracle-, MySQL-, DB2-, Teradata-, Sybase-en PostgreSQL-data bases, enzovoort.)*

* **Gegevens patroon**: uw tabel schema is van invloed op de door Voer van de Kopieer activiteit. Een grote Rijgrootte biedt betere prestaties dan een kleine Rijgrootte, om dezelfde hoeveelheid gegevens te kopiëren. De reden hiervoor is dat de data base efficiënter minder batches met gegevens kan ophalen die minder rijen bevatten.
* **Query of opgeslagen procedure**: Optimaliseer de logica van de query of opgeslagen procedure die u opgeeft in de bron van de Kopieer activiteit om gegevens efficiënter op te halen.
* Voor **on-premises relationele data bases**, zoals SQL Server en Oracle, waarvoor het gebruik van **Data Management Gateway** is vereist, raadpleegt u de sectie overwegingen voor Data Management Gateway.

## <a name="considerations-for-the-sink"></a>Overwegingen voor de Sink
### <a name="general"></a>Algemeen
Zorg ervoor dat de onderliggende gegevens opslag niet wordt overspoeld door andere workloads die op of worden uitgevoerd.

Raadpleeg voor micro soft-gegevens archieven [onderwerpen over het bewaken en afstemmen](#performance-reference) die specifiek zijn voor gegevens archieven. Deze onderwerpen kunnen u helpen bij het begrijpen van de prestatie kenmerken van het gegevens archief en het minimaliseren van de reactie tijden en het maximaliseren van de door voer.

Als u gegevens kopieert van **Blob-opslag** naar **Azure Synapse Analytics**, kunt u gebruikmaken van **poly base** om de prestaties te verbeteren. Zie [poly Base gebruiken om gegevens te laden in azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-synapse-analytics) voor meer informatie. Zie voor een overzicht met een use-case [1 TB in azure Synapse Analytics onder 15 minuten laden met Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Gegevens archieven op basis van bestanden
*(Inclusief Blob Storage, Data Lake Store, Amazon S3, on-premises bestands systemen en on-premises HDFS)*

* **Gedrag bij kopiëren**: als u gegevens kopieert vanuit een ander gegevens archief op basis van een bestand, heeft Kopieer activiteit drie opties via de eigenschap **copyBehavior** . Hiermee behoudt u de hiërarchie, worden hiërarchieën afgevlakt of bestanden samengevoegd. Het behouden of afvlakken van hiërarchie heeft weinig of geen prestatie overhead, maar het samen voegen van bestanden leidt ertoe dat de prestatie overhead toeneemt.
* **Bestands indeling en-compressie**: Zie [aandachtspunten voor serialisatie en deserialisatie](#considerations-for-serialization-and-deserialization) en [overwegingen voor compressie](#considerations-for-compression) secties voor meer manieren om de prestaties te verbeteren.
* **Blob-opslag**: momenteel ondersteunt Blob Storage alleen blok-blobs voor geoptimaliseerde gegevens overdracht en door voer.
* Zie de sectie [overwegingen voor Data Management Gateway](#considerations-for-data-management-gateway) voor **on-premises scenario's voor bestands systemen** waarvoor het gebruik van **Data Management Gateway** is vereist.

### <a name="relational-data-stores"></a>Relationele gegevens archieven
*(Omvat SQL Database, Azure Synapse Analytics, SQL Server data bases en Oracle-data bases)*

* **Kopieer gedrag**: afhankelijk van de eigenschappen die u hebt ingesteld voor **sqlSink**, schrijven de Kopieer activiteit gegevens op verschillende manieren naar de doel database.
  * De service voor gegevens verplaatsing maakt standaard gebruik van de API voor bulksgewijs kopiëren om gegevens in de toevoeg modus toe te voegen. Dit biedt de beste prestaties.
  * Als u een opgeslagen procedure in de Sink configureert, past de data base de gegevens één rij tegelijk toe in plaats van bulksgewijs laden. Prestaties gaan aanzienlijk verloren. Als uw gegevensset groot is, kunt u, indien van toepassing, overwegen om over te scha kelen naar het gebruik van de eigenschap **sqlWriterCleanupScript** .
  * Als u de eigenschap **sqlWriterCleanupScript** configureert voor elke uitvoering van de Kopieer activiteit, wordt het script door de service geactiveerd en vervolgens gebruikt u de API voor bulk kopieën om de gegevens in te voegen. Als u bijvoorbeeld de volledige tabel wilt overschrijven met de meest recente gegevens, kunt u een script opgeven om eerst alle records te verwijderen voordat de nieuwe gegevens uit de bron worden geladen.
* **Gegevens patroon en Batch grootte**:
  * Het tabel schema is van invloed op de Kopieer doorvoer. Als u dezelfde hoeveelheid gegevens wilt kopiëren, geeft een grote Rijgrootte u betere prestaties dan een kleine Rijgrootte, omdat de data base minder batches gegevens efficiënter kan door voeren.
  * Met de Kopieer activiteit worden gegevens ingevoegd in een reeks batches. U kunt het aantal rijen in een batch instellen met behulp van de eigenschap **writeBatchSize** . Als uw gegevens kleine rijen hebben, kunt u de eigenschap **writeBatchSize** instellen op een hogere waarde om te profiteren van lagere batch overhead en een hogere door voer. Als de Rijgrootte van uw gegevens groot is, moet u voorzichtig zijn wanneer u **writeBatchSize** verhoogt. Een hoge waarde kan leiden tot een Kopieer fout die wordt veroorzaakt door het overbelasten van de data base.
* Zie de sectie [overwegingen voor Data Management Gateway](#considerations-for-data-management-gateway) voor **on-premises relationele data bases** , zoals SQL Server en Oracle, waarvoor het gebruik van **Data Management Gateway** is vereist.

### <a name="nosql-stores"></a>NoSQL-winkels
*(Bevat tabel opslag en Azure Cosmos DB)*

* Voor **tabel opslag**:
  * **Partition**: het schrijven van gegevens naar Interleaved partities vermindert de prestaties aanzienlijk. Sorteer de bron gegevens op partitie sleutel zodat de gegevens efficiënt worden ingevoegd in één partitie na een andere, of pas de logica aan om de gegevens naar één partitie te schrijven.
* Voor **Azure Cosmos DB**:
  * **Batch grootte**: de eigenschap **writeBatchSize** stelt het aantal parallelle aanvragen voor de Azure Cosmos DB-service in om documenten te maken. U kunt betere prestaties verwachten wanneer u **writeBatchSize** verhoogt, omdat er meer parallelle aanvragen naar Azure Cosmos DB worden verzonden. U kunt echter wel controleren op beperking wanneer u naar Azure Cosmos DB schrijft (het fout bericht is ' de aanvraag snelheid is groot '). Verschillende factoren kunnen leiden tot een beperking, zoals de document grootte, het aantal voor waarden in de documenten en het indexerings beleid van de doel verzameling. Voor een hogere Kopieer doorvoer kunt u overwegen om een betere verzameling te gebruiken, bijvoorbeeld S3.

## <a name="considerations-for-serialization-and-deserialization"></a>Overwegingen voor serialisatie en deserialisatie
Serialisatie en deserialisatie kunnen optreden wanneer uw gegevensset of uitvoer gegevensset is ingesteld op een bestand. Zie [ondersteunde bestands-en compressie-indelingen](data-factory-supported-file-and-compression-formats.md) met details over ondersteunde bestands indelingen op basis van de Kopieer activiteit.

**Kopieer gedrag**:

* Kopiëren van bestanden tussen gegevens archieven op basis van bestanden:
  * Als invoer-en uitvoer gegevens sets dezelfde instellingen voor de bestands indeling hebben, voert de service voor gegevens verplaatsing een binaire kopie uit zonder serialisatie of deserialisatie. U ziet een hogere door Voer vergeleken met het scenario, waarbij de instellingen voor de bron-en Sink-indeling verschillen van elkaar.
  * Wanneer de invoer-en uitvoer gegevens sets beide in tekst indeling zijn en alleen het coderings type verschilt, wordt de service voor gegevens verplaatsing alleen geconverteerd naar een code ring. Er worden geen serialisatie en deserialisatie uitgevoerd, waardoor er een aantal prestatie overhead wordt veroorzaakt ten opzichte van een binaire kopie.
  * Als invoer-en uitvoer gegevens sets verschillende bestands indelingen of verschillende configuraties hebben, zoals scheidings tekens, deserialiseren de gegevens verplaatsings service bron gegevens naar streamen, transformeren en vervolgens serialiseren naar de uitvoer indeling die u hebt opgegeven. Deze bewerking resulteert in een veel meer aanzienlijke prestatie overhead ten opzichte van andere scenario's.
* Wanneer u bestanden kopieert naar/van een gegevens archief dat niet op bestanden is gebaseerd (bijvoorbeeld van een archief op basis van een bestand naar een relationeel archief), is de stap serialisatie of deserialisatie vereist. Deze stap resulteert in aanzienlijke prestatie overhead.

**Bestands indeling**: de bestands indeling die u kiest, kan de prestaties van het kopiëren beïnvloeden. Avro is bijvoorbeeld een Compact binaire indeling waarin meta gegevens worden opgeslagen in de data. Het biedt brede ondersteuning in het Hadoop-ecosysteem voor verwerking en query's. Avro is echter duurder voor serialisatie en deserialisatie, wat leidt tot een lagere Kopieer doorvoer ten opzichte van de tekst indeling. Maak uw keuze van de bestands indeling voor de hele verwerkings stroom op een holistische manier. Begin met het formulier waarin de gegevens zijn opgeslagen, opslag van bron gegevens of uit externe systemen worden opgehaald. de beste indeling voor opslag, analytische verwerking en query's. en in welke indeling de gegevens moeten worden geëxporteerd naar Data Marts voor rapportage-en visualisatie hulpprogramma's. Het is ook mogelijk dat een bestands indeling die voor lees-en schrijf prestaties wordt geoptimaliseerd, een goede keuze is wanneer u het algehele analytische proces overweegt.

## <a name="considerations-for-compression"></a>Overwegingen voor compressie
Wanneer de invoer-of uitvoer gegevensset een bestand is, kunt u de Kopieer activiteit instellen om compressie of decompressie uit te voeren terwijl de gegevens naar de bestemming worden geschreven. Wanneer u compressie kiest, maakt u een verhouding tussen invoer/uitvoer (I/O) en CPU. Comprimeren van de gegevens kosten in reken resources. Maar als resultaat wordt de netwerk-I/O-en opslag capaciteit verminderd. Afhankelijk van uw gegevens ziet u mogelijk een boost in de totale Kopieer doorvoer.

**Codec**: Kopieer activiteit biedt ondersteuning voor gzip-, bzip2-en deflater compressie typen. Azure HDInsight kan alle drie de typen voor verwerking in beslag nemen. Elke compressie-codec heeft voor delen. Bzip2 heeft bijvoorbeeld de laagste kopie doorvoer, maar u krijgt de beste query prestaties met bzip2, omdat u deze kunt splitsen voor verwerking. Gzip is de meest evenwichtige optie en wordt het meest gebruikt. Kies de codec die het beste bij uw end-to-end-scenario past.

**Niveau**: u kunt kiezen uit twee opties voor elke compressie-codec: snelst gecomprimeerd en optimaal gecomprimeerd. Met de snelste compressie optie worden de gegevens zo snel mogelijk gecomprimeerd, zelfs als het resulterende bestand niet optimaal is gecomprimeerd. De optimale compressie optie brengt meer tijd in behoeven en levert een minimale hoeveelheid gegevens op. U kunt beide opties testen om te zien wat een betere algehele prestaties in uw geval biedt.

**Overweging**: als u een grote hoeveelheid gegevens wilt kopiëren tussen een on-premises opslag en de Cloud, kunt u overwegen om tussenliggende Blob-opslag te gebruiken met compressie. Het gebruik van tijdelijke opslag is handig wanneer de band breedte van uw bedrijfs netwerk en uw Azure-Services de beperkende factor zijn, en u wilt dat de invoer-en uitvoer gegevens worden ingesteld op niet-gecomprimeerde vorm. Meer specifiek kunt u een enkele Kopieer activiteit in twee Kopieer activiteiten afsplitsen. De eerste Kopieer activiteit kopieert van de bron naar een tijdelijke of tijdelijke Blob in gecomprimeerde vorm. De tweede Kopieer activiteit kopieert de gecomprimeerde gegevens uit de fase ring en vervolgens gedecomprimeerd tijdens het schrijven naar de sink.

## <a name="considerations-for-column-mapping"></a>Overwegingen voor kolom toewijzing
U kunt de eigenschap **columnMappings** in de Kopieer activiteit instellen om alle of een subset van de invoer kolommen toe te wijzen aan de uitvoer kolommen. Nadat de gegevens verplaatsings service de gegevens van de bron heeft gelezen, moet deze kolom toewijzing uitvoeren op de gegevens voordat de gegevens naar de Sink worden geschreven. Deze extra verwerking vermindert de door Voer van de Kopieer bewerking.

Als uw brongegevens archief query's kan uitvoeren, bijvoorbeeld als het een relationele archief is zoals SQL Database of SQL Server, of als het een NoSQL-archief is, zoals tabel opslag of Azure Cosmos DB, kunt u overwegen om de kolom filtering te pushen **en de Volg** orde van de logica te wijzigen in plaats van kolom toewijzing. Op deze manier treedt de projectie op terwijl de gegevens verplaatsings service gegevens uit de brongegevens opslag leest, waar deze veel efficiënter zijn.

## <a name="other-considerations"></a>Andere overwegingen
Als de grootte van de gegevens die u wilt kopiëren groot is, kunt u uw bedrijfs logica aanpassen om de gegevens verder te partitioneren met behulp van het segmenteer mechanisme in Data Factory. Plan vervolgens de Kopieer activiteit om de gegevens grootte voor elke uitvoering van de Kopieer activiteit vaker te laten uitvoeren.

Wees voorzichtig met het aantal gegevens sets en kopieer activiteiten die tegelijk Data Factory naar de connector naar hetzelfde gegevens archief moeten worden gestuurd. Veel gelijktijdige Kopieer taken kunnen een gegevens opslag beperken en leiden tot gedegradeerde prestaties, het kopiëren van een taak in een interne nieuwe poging, en in sommige gevallen, fouten bij de uitvoering.

## <a name="sample-scenario-copy-from-a-sql-server-database-to-blob-storage"></a>Voorbeeld scenario: kopiëren van een SQL Server Data Base naar Blob Storage
**Scenario**: een pijp lijn is gebouwd om gegevens van een SQL Server Data Base naar Blob Storage te kopiëren in CSV-indeling. Als u de Kopieer taak sneller wilt uitvoeren, moeten de CSV-bestanden worden gecomprimeerd in de bzip2-indeling.

**Testen en analyse**: de door Voer van de Kopieer activiteit is minder dan 2 Mbps, wat langzamer is dan de benchmark waarde voor prestaties.

**Prestatie analyse en-afstemming**: Bekijk hoe de gegevens worden verwerkt en verplaatst om het prestatie probleem op te lossen.

1. **Gegevens lezen**: de gateway opent een verbinding met SQL Server en verzendt de query. SQL Server reageert door de gegevens stroom via het intranet naar de gateway te verzenden.
2. **Gegevens serialiseren en comprimeren**: de gateway serialeert de gegevens stroom naar de CSV-indeling en comprimeert de gegevens naar een bzip2-stroom.
3. **Gegevens schrijven**: gateway uploadt de bzip2-stroom naar Blob-opslag via internet.

Zoals u kunt zien, worden de gegevens verwerkt en verplaatst op een opeenvolgende manier: SQL Server > LAN > gateway > WAN > Blob Storage. **De algehele prestaties worden gegated door de minimale door voer over de pijp lijn**.

![Gegevensstroom](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Een of meer van de volgende factoren kunnen de prestatie knelpunt veroorzaken:

* **Bron**: SQL Server zichzelf heeft een lage door Voer vanwege zware belasting.
* **Data Management Gateway**:
  * **LAN**: de gateway bevindt zich ver van de SQL Server computer en heeft een verbinding met een lage band breedte.
  * **Gateway**: de beperkingen voor het laden van de gateway zijn bereikt om de volgende bewerkingen uit te voeren:
    * **Serialisatie**: het serialiseren van de gegevens stroom naar de CSV-indeling heeft een trage door voer.
    * **Compressie**: u hebt een langzame compressie-codec gekozen (bijvoorbeeld bzip2, 2,8 Mbps met Kern i7).
  * **WAN**: de band breedte tussen het bedrijfs netwerk en uw Azure-Services is laag (bijvoorbeeld T1 = 1.544 kbps; T2 = 6.312 kbps).
* **Sink**: Blob Storage heeft lage door voer. (Dit scenario is niet waarschijnlijk omdat de SLA mini maal 60 MBps waarborgt.)

In dit geval kan bzip2 gegevens compressie de volledige pijp lijn vertragen. Als u overschakelt naar een gzip-compressie-codec, kan dit knel punt worden vereenvoudigd.

## <a name="sample-scenarios-use-parallel-copy"></a>Voorbeeld scenario's: parallelle kopie gebruiken
**Scenario I:** Kopieer 1.000 1-MB bestanden van het on-premises bestands systeem naar Blob Storage.

**Analyse en prestaties afstemmen**: als u een gateway op een quad core-computer hebt geïnstalleerd, maakt Data Factory gebruik van 16 parallelle kopieën om bestanden van het bestands systeem naar de Blob-opslag gelijktijdig te verplaatsen. Deze parallelle uitvoering moet resulteren in een hoge door voer. U kunt ook expliciet het aantal parallelle kopieën opgeven. Wanneer u veel kleine bestanden kopieert, wordt de door Voer van parallelle kopieën aanzienlijk geholpen door resources effectiever te gebruiken.

![Scenario 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Scenario II**: Kopieer 20 blobs van 500 MB van de Blob-opslag naar Data Lake Store Analytics en stel vervolgens de prestaties af.

**Analyse en prestaties afstemmen**: in dit scenario Data Factory kopieert de gegevens uit de Blob-opslag naar Data Lake Store met behulp van één kopie (**parallelCopies** ingesteld op 1) en gegevens verplaatsings eenheden met één Cloud. De door u geobserveerde door Voer is bijna hetzelfde als de gegevens die worden beschreven in de [sectie prestatie verwijzing](#performance-reference).

![Scenario 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**Scenario III**: de afzonderlijke bestands grootte is groter dan tien tallen MBS en het totale volume is groot.

**Analyse en prestaties inschakelen**: het verhogen van **parallelCopies** leidt niet tot betere Kopieer prestaties vanwege de resource beperkingen van een DMU met één Cloud. In plaats daarvan moet u meer Cloud DMUs opgeven om meer bronnen te krijgen om de gegevens verplaatsing uit te voeren. Geef geen waarde op voor de eigenschap **parallelCopies** . Data Factory de parallelle uitvoering voor u afhandelt. Als u in dit geval **cloudDataMovementUnits** instelt op 4, treedt er een door Voer van ongeveer vier keer op.

![Scenario 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Naslaginformatie
Hier volgen de prestaties en het afstemmen van verwijzingen voor een aantal ondersteunde gegevens archieven:

* Azure Blob-opslag: [schaal baarheid en prestatie doelen voor Blob Storage](../../storage/blobs/scalability-targets.md) en de [controle lijst voor prestaties en schaal baarheid voor Blob Storage](../../storage/blobs/storage-performance-checklist.md).
* Azure-tabel opslag: [schaal baarheid en prestatie doelen voor](../../storage/tables/scalability-targets.md) de [controle lijst voor tabel opslag en prestaties en schaal baarheid voor tabel opslag](../../storage/tables/storage-performance-checklist.md).
* Azure SQL Database: u kunt [de prestaties bewaken](../../azure-sql/database/monitor-tune-overview.md) en het DTU-percentage (data base Trans Action Unit) controleren
* Azure Synapse Analytics: de mogelijkheid ervan wordt gemeten in data warehouse units (Dwu's); Zie [reken kracht beheren in azure Synapse Analytics (overzicht)](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [prestatie niveaus in azure Cosmos DB](../../cosmos-db/performance-levels.md)
* On-premises SQL Server: [prestaties bewaken en afstemmen](/sql/relational-databases/performance/monitor-and-tune-for-performance)
* On-premises Bestands server: [prestaties afstemmen voor bestands servers](/previous-versions//dn567661(v=vs.85))
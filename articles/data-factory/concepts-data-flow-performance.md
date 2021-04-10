---
title: Prestaties en afstemmings handleiding voor gegevens stromen toewijzen
description: Meer informatie over de belangrijkste factoren die van invloed zijn op de prestaties van het toewijzen van gegevens stromen in Azure Data Factory.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 03/15/2021
ms.openlocfilehash: dd5b857c274e757f70920f244786df61c2770085
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103561682"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Gegevens stromen toewijzen prestaties en afstemmings handleiding

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Het toewijzen van gegevens stromen in Azure Data Factory bieden een code-Free interface voor het ontwerpen en uitvoeren van gegevens transformaties op schaal. Als u niet bekend bent met het toewijzen van gegevens stromen, raadpleegt u het [overzicht gegevens stroom toewijzen](concepts-data-flow-overview.md). In dit artikel worden verschillende manieren beschreven om uw gegevens stromen af te stemmen en te optimaliseren, zodat ze voldoen aan uw prestatie benchmarks.

Bekijk de onderstaande video om te zien toont enkele voor beelden van tijds instellingen waarmee gegevens worden getransformeerd met gegevens stromen.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="testing-data-flow-logic"></a>Logica voor gegevens stroom testen

Bij het ontwerpen en testen van gegevens stromen vanuit de ADF UX, kunt u met de modus fout opsporing interactief testen op een live Spark-cluster. Zo kunt u een voor beeld van gegevens bekijken en uw gegevens stromen uitvoeren zonder te hoeven wachten tot een cluster is opgewarmd. Zie [debug mode (foutopsporingsmodus](concepts-data-flow-debug-mode.md)) voor meer informatie.

## <a name="monitoring-data-flow-performance"></a>Prestaties van de gegevens stroom bewaken

Nadat u uw transformatie logica hebt gecontroleerd met de foutopsporingsmodus, voert u end-to-end van uw gegevens stroom uit als een activiteit in een pijp lijn. Gegevens stromen worden in een pijp lijn operationeel met behulp van de [activiteit gegevens stroom uitvoeren](control-flow-execute-data-flow-activity.md). De activiteit gegevens stroom heeft een unieke bewakings ervaring vergeleken met andere Azure Data Factory activiteiten die een gedetailleerd uitvoerings plan en prestatie profiel van de transformatie logica weergeeft. Als u gedetailleerde bewakings gegevens van een gegevens stroom wilt weer geven, klikt u op het pictogram bril in de uitvoer van een pijp lijn uitvoering van activiteit. Zie [toewijzing van gegevens stromen controleren](concepts-data-flow-monitoring.md)voor meer informatie.

![Monitor voor gegevens stroom](media/data-flow/monitoring-details.png "Monitor voor gegevens stroom 2")

Bij het bewaken van de prestaties van de gegevens stroom zijn er vier mogelijke knel punten die moeten worden weer gegeven voor:

* Opstart tijd van het cluster
* Lezen van een bron
* Transformatie tijd
* Schrijven naar een Sink 

![Gegevensstroom controleren](media/data-flow/monitoring-performance.png "Monitor voor gegevens stroom 3")

De opstart tijd van het cluster is de tijd die nodig is om een Apache Spark cluster in te zetten. Deze waarde bevindt zich in de rechter bovenhoek van het scherm voor controle. Gegevens stromen worden uitgevoerd op een just-in-time-model, waarbij elke taak gebruikmaakt van een geïsoleerd cluster. Deze start tijd duurt doorgaans 3-5 minuten. Voor sequentiële taken kan dit worden verminderd door een TTL-waarde (time to Live) in te scha kelen. Zie [optimalisatie van de Azure Integration runtime](#ir)voor meer informatie.

Gegevens stromen maken gebruik van een Spark-Optimizer waarmee uw bedrijfs logica in fasen opnieuw kan worden geordend en uitgevoerd om zo snel mogelijk uit te voeren. Voor elke sink die door uw gegevens stroom wordt geschreven, wordt in de bewakings uitvoer de duur van elke transformatie fase vermeld, samen met de tijd die nodig is voor het schrijven van gegevens naar de sink. De tijd die het grootst is, is waarschijnlijk het knel punt van uw gegevens stroom. Als de transformatie fase die de grootste bevat een bron heeft, kunt u het beste uw Lees tijd verder optimaliseren. Als een trans formatie veel tijd in beslag neemt, moet u mogelijk de grootte van de Integration runtime opnieuw partitioneren of verg Roten. Als de verwerkings tijd van de Sink groot is, moet u mogelijk uw data base schalen of controleren of u geen enkel bestand hebt.

Wanneer u de knel punten van uw gegevens stroom hebt geïdentificeerd, gebruikt u de onderstaande strategieën voor optimalisatie om de prestaties te verbeteren.

## <a name="optimize-tab"></a>Tabblad optimaliseren

Het tabblad **Optimize** bevat instellingen voor het configureren van het partitie schema van het Spark-cluster. Dit tabblad bevindt zich in elke trans formatie van de gegevens stroom en geeft aan of u de gegevens opnieuw wilt partitioneren **nadat** de trans formatie is voltooid. Het aanpassen van de partitionering biedt controle over de distributie van uw gegevens over reken knooppunten en gegevens lokale optimalisaties die zowel positieve als negatieve effecten kunnen hebben op de prestaties van uw algemene gegevens stroom.

![Scherm afbeelding toont het tabblad Optimize, inclusief partitie optie, partitie type en aantal partities.](media/data-flow/optimize.png)

Standaard is het *gebruik van huidige partitionering* geselecteerd, wat aangeeft dat Azure Data Factory de huidige uitvoer partitionering van de trans formatie wilt blijven gebruiken. Omdat het opnieuw partitioneren van gegevens tijd kost, is het *gebruik van huidige partitionering* aanbevolen in de meeste scenario's. Scenario's waarin het mogelijk is om uw gegevens opnieuw te partitioneren, zijn na aggregaties en samen voegingen waarmee u uw gegevens aanzienlijk kunt hellen of wanneer u bron partities gebruikt voor een SQL-data base.

Als u het partitioneren van een trans formatie wilt wijzigen, selecteert u het tabblad **optimaliseren** en selecteert u het keuze rondje **partitioneren instellen** . U krijgt een reeks opties voor partitioneren. De beste methode voor partitionering verschilt op basis van uw gegevens volumes, kandidaat-sleutels, null-waarden en kardinaliteit. 

> [!IMPORTANT]
> Met één partitie worden alle gedistribueerde gegevens gecombineerd tot één partitie. Dit is een zeer langzame bewerking die ook van invloed is op alle downstream trans formatie en schrijf bewerkingen. De Azure Data Factory wordt ten zeerste aanbevolen om deze optie te gebruiken tenzij er een expliciete zakelijke reden hiervoor is.

De volgende partitionatie opties zijn beschikbaar in elke trans formatie:

### <a name="round-robin"></a>Round Robin 

Met Round Robin worden gegevens gelijkmatig verdeeld over partities. Gebruik Round Robin wanneer u niet over de juiste belangrijkste kandidaten beschikt voor het implementeren van een solide strategie voor slimme partitionering. U kunt het aantal fysieke partities instellen.

### <a name="hash"></a>Hash

Azure Data Factory produceert een hash van kolommen om uniforme partities te maken, zodat rijen met vergelijk bare waarden in dezelfde partitie vallen. Wanneer u de hash-optie gebruikt, moet u testen op mogelijke partitie scheefheid. U kunt het aantal fysieke partities instellen.

### <a name="dynamic-range"></a>Dynamisch bereik

Het dynamische bereik maakt gebruik van Spark dynamische bereiken op basis van de kolommen of expressies die u opgeeft. U kunt het aantal fysieke partities instellen. 

### <a name="fixed-range"></a>Vast bereik

Bouw een expressie die een vast bereik voor waarden in de gepartitioneerde gegevens kolommen levert. U moet een goed idee hebben van uw gegevens voordat u deze optie gebruikt om het hellen van partities te voor komen. De waarden die u voor de expressie invoert, worden gebruikt als onderdeel van een partitie functie. U kunt het aantal fysieke partities instellen.

### <a name="key"></a>Sleutel

Als u een goed beeld hebt van de kardinaliteit van uw gegevens, is het mogelijk dat sleutel partities een goede strategie zijn. Met sleutel partities maakt u partities voor elke unieke waarde in uw kolom. U kunt het aantal partities niet instellen omdat het nummer is gebaseerd op unieke waarden in de gegevens.

> [!TIP]
> Als u het partitie schema hand matig instelt, worden de gegevens in een andere volg orde gezet en kunnen de voor delen van de Spark-Optimizer worden verrekend. Een best practice moet de partitie pas hand matig instellen, tenzij u dat nodig hebt.

## <a name="logging-level"></a>Logboek registratie niveau

Als u niet elke pijplijn uitvoering van uw gegevens stroom activiteiten nodig hebt om alle uitgebreide telemetriegegevens logboeken volledig te registreren, kunt u desgewenst uw logboek registratie niveau instellen op basis of geen. Bij het uitvoeren van uw gegevens stromen in de modus ' uitgebreid ' (standaard), vraagt u om de automatische logboek activiteit voor elk afzonderlijke partitie niveau bij de gegevens transformatie. Dit kan een dure bewerking zijn, zodat u alleen uitgebreide informatie kunt inschakelen als u problemen met het oplossen van gegevens stroom en de prestaties van de pijp lijn verbetert. In de modus standaard worden alleen transformatie duur vastgelegd terwijl ' geen ' een samen vatting van de duur geeft.

![Logboek registratie niveau](media/data-flow/logging.png "Niveau van logboek registratie instellen")

## <a name="optimizing-the-azure-integration-runtime"></a><a name="ir"></a> De Azure Integration Runtime optimaliseren

Gegevens stromen worden uitgevoerd op Spark-clusters die tijdens runtime actief zijn. De configuratie voor het gebruikte cluster wordt gedefinieerd in de Integration runtime (IR) van de activiteit. Er zijn drie prestatie overwegingen die u moet aanbrengen bij het definiëren van de Integration runtime: cluster type, cluster grootte en time to Live.

Zie [Integration runtime in azure Data Factory](concepts-integration-runtime.md)voor meer informatie over het maken van een Integration runtime.

### <a name="cluster-type"></a>Clustertype

Er zijn drie beschik bare opties voor het type Spark-cluster: algemeen gebruik, geoptimaliseerd voor geheugen en berekenings optimalisatie.

Clusters voor **algemeen gebruik** zijn de standaard selectie en zijn ideaal voor de meeste werk belastingen van de gegevens stroom. Dit zijn meestal het beste saldo van prestaties en kosten.

Als uw gegevens stroom veel koppelingen en zoek acties heeft, kunt u een cluster met **geoptimaliseerd geheugen** gebruiken. Clusters die zijn geoptimaliseerd voor geheugen kunnen meer gegevens in het geheugen opslaan en kunnen geheugen fouten beperken die u kunt krijgen. Het geheugen dat is geoptimaliseerd, heeft het hoogste prijs punt per kern, maar is ook vaak succes volle pijp lijnen. Als er geheugen fouten optreden tijdens het uitvoeren van gegevens stromen, schakelt u over naar een geoptimaliseerd voor geheugen Azure IR-configuratie. 

**Compute Optimized** is niet ideaal voor etl-werk stromen en wordt niet aanbevolen door het Azure Data Factory-team voor de meeste productie werkbelastingen. Voor eenvoudigere, niet-geheugen intensieve gegevens transformaties, zoals het filteren van gegevens of het toevoegen van afgeleide kolommen, kunnen Compute-geoptimaliseerde clusters worden gebruikt tegen een goedkopere prijs per kern.

### <a name="cluster-size"></a>Grootte van cluster

Gegevens stromen distribueren de gegevens verwerking over verschillende knoop punten in een Spark-cluster om bewerkingen parallel uit te voeren. Een Spark-cluster met meer kern geheugens verhoogt het aantal knoop punten in de reken omgeving. Meer knoop punten verg Roten de verwerkings kracht van de gegevens stroom. Het verg Roten van het cluster is vaak een eenvoudige manier om de verwerkings tijd te verminderen.

De standaard cluster grootte is vier Stuur knooppunten en vier werk knooppunten.  Bij het verwerken van meer gegevens worden grotere clusters aanbevolen. Hieronder ziet u de mogelijke opties voor de grootte:

| Kernen van werk nemers | Kern geheugens van Stuur Programma's | Totaal aantal cores | Notities |
| ------------ | ------------ | ----------- | ----- |
| 4 | 4 | 8 | Niet beschikbaar voor berekenings optimalisatie |
| 8 | 8 | 16 | |
| 16 | 16 | 32 | |
| 32 | 16 | 48 | |
| 64 | 16 | 80 | |
| 128 | 16 | 144 | |
| 256 | 16 | 272 | |

Gegevens stromen zijn geprijsd op VCore. Dit betekent dat zowel de cluster grootte als de uitvoerings tijd factor in dit. Wanneer u omhoog schaalt, worden de kosten voor het cluster per minuut verhoogd, maar wordt de totale tijd afgenomen.

> [!TIP]
> Er is een plafond voor het effect van de grootte van een cluster op de prestaties van een gegevens stroom. Afhankelijk van de grootte van uw gegevens is er een punt waar het verg Roten van de grootte van een cluster de prestaties niet meer verbetert. Als u bijvoorbeeld meer knoop punten hebt dan gegevens partities, kunnen extra knoop punten niet worden toegevoegd. Een best practice is het starten van klein en schalen om te voldoen aan de prestatie behoeften. 

### <a name="time-to-live"></a>Time To Live

Elke gegevens stroom activiteit draait standaard naar een nieuw cluster op basis van de IR-configuratie. De opstart tijd van het cluster duurt enkele minuten en de gegevens verwerking kan pas worden gestart als deze is voltooid. Als uw pijp lijnen meerdere **sequentiële** gegevens stromen bevatten, kunt u een TTL-waarde (time to Live) inschakelen. Als u een time to Live-waarde opgeeft, blijft een cluster actief gedurende een bepaalde periode nadat de uitvoering is voltooid. Als een nieuwe taak begint met het gebruik van de IR tijdens de TTL-tijd, wordt het bestaande cluster opnieuw gebruikt en wordt de opstart tijd aanzienlijk verminderd. Nadat de tweede taak is voltooid, blijft het cluster weer actief gedurende de TTL-tijd.

Er kan slechts één taak tegelijk worden uitgevoerd op één cluster. Als er een cluster beschikbaar is, maar twee gegevens stromen start, wordt er slechts één gebruikt voor het Live cluster. Met de tweede taak wordt een eigen geïsoleerd cluster gedraaid.

Als de meeste gegevens stromen parallel worden uitgevoerd, is het niet raadzaam om TTL in te scha kelen. 

> [!NOTE]
> Time to Live is niet beschikbaar wanneer de Integration runtime automatisch oplossen wordt gebruikt

## <a name="optimizing-sources"></a>Bronnen optimaliseren

Voor elke bron, met uitzonde ring van Azure SQL Database, is het raadzaam om **huidige partitionering** te blijven gebruiken als de geselecteerde waarde. Bij het lezen van alle andere bron systemen, worden gegevens stromen automatisch gepartitioneerd op basis van de grootte van de gegevens. Voor elke 128 MB aan gegevens wordt een nieuwe partitie gemaakt. Naarmate de omvang van de gegevens toeneemt, neemt het aantal partities toe.

Aangepaste partitionering vindt plaats *nadat* Spark in de gegevens is gelezen en negatieve invloed heeft op de prestaties van uw gegevens stroom. Omdat de gegevens gelijkmatig zijn gepartitioneerd bij het lezen, wordt dit niet aanbevolen. 

> [!NOTE]
> Lees snelheden kunnen worden beperkt door de door Voer van uw bron systeem.

### <a name="azure-sql-database-sources"></a>Azure SQL Database bronnen

Azure SQL Database heeft een unieke partitionering-optie met de naam bron partitioneren. Het inschakelen van bron partitionering kan uw Lees tijden van Azure SQL DB verbeteren door parallelle verbindingen op het bron systeem in te scha kelen. Geef het aantal partities en het partitioneren van uw gegevens op. Gebruik een partitie kolom met een hoge kardinaliteit. U kunt ook een query invoeren die overeenkomt met het partitie schema van de bron tabel.

> [!TIP]
> Voor het partitioneren van de bron is de I/O van de SQL Server het knel punt. Als u te veel partities toevoegt, kan de bron database onverzadigd worden. Doorgaans vier of vijf partities is ideaal wanneer u deze optie gebruikt.

![Bron partities](media/data-flow/sourcepart3.png "Bron partities")

#### <a name="isolation-level"></a>Isolatie niveau

Het isolatie niveau van de Lees bewerking op een Azure SQL-bron systeem heeft invloed op de prestaties. Als u ' niet doorgevoerd ' kiest, worden de snelste prestaties geboden en kunnen er geen database vergrendelingen worden uitgevoerd. Zie informatie over [isolatie niveaus](/sql/connect/jdbc/understanding-isolation-levels)voor meer informatie over SQL-isolatie niveaus.

#### <a name="read-using-query"></a>Lezen met behulp van query

U kunt lezen van Azure SQL Database met behulp van een tabel of een SQL-query. Als u een SQL-query uitvoert, moet de query worden voltooid voordat de trans formatie kan worden gestart. SQL-Query's kunnen nuttig zijn om bewerkingen te pushen die sneller kunnen worden uitgevoerd en de hoeveelheid gegevens te verminderen die uit een SQL Server, zoals SELECT-, WHERE-en samen voegings instructies, worden gelezen. Bij het pushen van bewerkingen verliest u de mogelijkheid om de afkomst en prestaties van de trans formaties bij te houden voordat de gegevens in de gegevens stroom worden geplaatst.

### <a name="azure-synapse-analytics-sources"></a>Azure Synapse Analytics-bronnen

Wanneer u Azure Synapse Analytics gebruikt, is een instelling met de naam **fase ring inschakelen** aanwezig in de bron opties. Dit maakt het mogelijk om ADF te lezen van Synapse met ```Staging``` , waardoor de Lees prestaties aanzienlijk worden verbeterd. ```Staging```Als u inschakelen hebt, moet u een Azure Blob Storage of Azure data Lake Storage Gen2 tijdelijke locatie opgeven in de instellingen voor de gegevens stroom activiteit.

![Faseringsmodus inschakelen](media/data-flow/enable-staging.png "Faseringsmodus inschakelen")

### <a name="file-based-sources"></a>Op bestanden gebaseerde bronnen

Hoewel gegevens stromen ondersteuning bieden voor verschillende bestands typen, raadt het Azure Data Factory aan om de Spark-systeem eigen Parquet-indeling te gebruiken voor optimale lees-en schrijf tijden.

Als u dezelfde gegevens stroom uitvoert voor een set bestanden, raden we u aan een map te lezen met behulp van joker tekens of het lezen van een lijst met bestanden. Met een activiteit voor het uitvoeren van één gegevens stroom kan al uw bestanden in batch worden verwerkt. Meer informatie over het instellen van deze instellingen vindt u in de documentatie van de connector, zoals [Azure Blob Storage](connector-azure-blob-storage.md#source-transformation).

Vermijd het gebruik van de For-Each activiteit voor het uitvoeren van gegevens stromen over een set bestanden, indien mogelijk. Dit zorgt ervoor dat elke herhaling van de voor-elk als een eigen Spark-cluster draait, wat vaak niet nodig is en duur kan zijn. 

## <a name="optimizing-sinks"></a>Sinks optimaliseren

Wanneer gegevens worden wegge schreven naar sinks, worden aangepaste partities direct vóór de schrijf bewerking uitgevoerd. Net als bij de bron, wordt het aanbevolen dat u **huidige partitionering** als de geselecteerde partitie optie gebruikt. Gepartitioneerde gegevens worden aanzienlijk sneller geschreven dan niet-gepartitioneerde gegevens, zelfs als uw bestemming niet is gepartitioneerd. Hieronder vindt u de afzonderlijke overwegingen voor verschillende Sink-typen. 

### <a name="azure-sql-database-sinks"></a>Azure SQL Database-sinks

Met Azure SQL Database zou de standaard partitionering in de meeste gevallen moeten werken. Er is een kans dat uw Sink te veel partities heeft om uw SQL database te kunnen verwerken. Als u hier werkt, vermindert u het aantal partities dat wordt gegenereerd door uw SQL Database sink.

#### <a name="impact-of-error-row-handling-to-performance"></a>Impact van de verwerking van de fout rijen op de prestaties

Wanneer u het verwerken van de fout rijen (' door gaan bij fout ') inschakelt in de Sink-trans formatie, neemt de ADF een extra stap voordat de compatibele rijen naar de doel tabel worden geschreven. Deze extra stap heeft een geringe prestatie vermindering die binnen het bereik van 5% is toegevoegd voor deze stap, met een extra kleine prestatie treffer die ook wordt toegevoegd als u de optie instelt op ook met de niet-compatibele rijen naar een logboek bestand.

#### <a name="disabling-indexes-using-a-sql-script"></a>Indexen uitschakelen met behulp van een SQL-script

Als u indexen uitschakelt voordat een belasting in een SQL database, kan de prestaties van het schrijven naar de tabel aanzienlijk worden verbeterd. Voer de onderstaande opdracht uit voordat u naar de SQL-Sink schrijft.

`ALTER INDEX ALL ON dbo.[Table Name] DISABLE`

Nadat de schrijf bewerking is voltooid, bouwt u de indexen opnieuw met behulp van de volgende opdracht:

`ALTER INDEX ALL ON dbo.[Table Name] REBUILD`

Deze kunnen zowel als systeem eigen worden uitgevoerd met scripts van vóór en na SQL binnen een Azure SQL DB-of Synapse-Sink bij het toewijzen van gegevens stromen.

![Indexen uitschakelen](media/data-flow/disable-indexes-sql.png "Indexen uitschakelen")

> [!WARNING]
> Wanneer u indexen uitschakelt, wordt de controle over een data base in feite uitgevoerd en zijn query's waarschijnlijk op dit moment niet mogelijk. Als gevolg hiervan worden veel ETL-taken in het midden van de nacht geactiveerd om dit conflict te voor komen. Meer informatie over de beperkingen voor het [uitschakelen van indexen](/sql/relational-databases/indexes/disable-indexes-and-constraints)

#### <a name="scaling-up-your-database"></a>Uw data base omhoog schalen

Plan een grootte van uw bron en Sink Azure SQL DB en DW vóór de uitvoering van de pijp lijn om de door voer te verhogen en Azure-beperking te minimaliseren zodra u DTU-limieten bereikt. Nadat de uitvoering van de pijp lijn is voltooid, kunt u het formaat van uw data bases herstellen naar hun normale uitvoerings frequentie.

### <a name="azure-synapse-analytics-sinks"></a>Azure Synapse Analytics-sinks

Wanneer u naar Azure Synapse Analytics schrijft, moet u ervoor zorgen dat het **inschakelen van staging** is ingesteld op waar. Dit maakt het mogelijk om ADF te schrijven met behulp van de [SQL copy-opdracht](/sql/t-sql/statements/copy-into-transact-sql) , die de gegevens effectief laadt. U moet verwijzen naar een Azure Data Lake Storage Gen2-of Azure Blob Storage-account voor het faseren van de gegevens bij gebruik van staging.

Met uitzonde ring van fase ring, zijn dezelfde aanbevolen procedures van toepassing op Azure Synapse Analytics als Azure SQL Database.

### <a name="file-based-sinks"></a>Op bestanden gebaseerde sinks 

Hoewel gegevens stromen ondersteuning bieden voor verschillende bestands typen, raadt het Azure Data Factory aan om de Spark-systeem eigen Parquet-indeling te gebruiken voor optimale lees-en schrijf tijden.

Als de gegevens gelijkmatig worden gedistribueerd, is het **gebruik van huidige partitionering** de snelste partitie optie voor het schrijven van bestanden.

#### <a name="file-name-options"></a>Opties voor bestands namen

Bij het schrijven van bestanden hebt u een keuze uit de naamgevings opties die elk een invloed hebben op de prestaties.

![Sink-opties](media/data-flow/file-sink-settings.png "Sink-opties")

Als u de **standaard** optie selecteert, wordt het snelst geschreven. Elke partitie wordt vergeleken met een bestand met de standaard naam Spark. Dit is handig als u alleen in de map met gegevens leest.

Als u een naamgevings **patroon** instelt, wordt de naam van elk partitie bestand gewijzigd in een gebruiks vriendelijke, beschrijvende namen. Deze bewerking treedt op na schrijven en is iets langzamer dan de standaard waarde kiezen. Per partitie kunt u elke afzonderlijke partitie hand matig benoemen.

Als een kolom overeenkomt met de manier waarop u de gegevens wilt uitvoeren, kunt u **als gegevens selecteren in kolom**. Dit heeft gevolgen voor de gegevens en kan invloed hebben op de prestaties als de kolommen niet gelijkmatig worden gedistribueerd.

**Met uitvoer naar één bestand worden** alle gegevens gecombineerd tot één partitie. Dit leidt tot lange schrijf tijden, met name voor grote gegevens sets. Het Azure Data Factory Team raadt met klem aan deze optie **niet** te kiezen, tenzij er een expliciete zakelijke reden is om dit te doen.

### <a name="cosmosdb-sinks"></a>CosmosDB-sinks

Wanneer u naar CosmosDB schrijft, kunt u de prestaties verbeteren door de door Voer en de grootte van de batch tijdens de uitvoering van de gegevens stroom te wijzigen. Deze wijzigingen worden pas van kracht tijdens de uitvoering van de gegevens stroom activiteit en terugkeren naar de oorspronkelijke verzamelings instellingen na de conclusie. 

**Batch grootte:** Normaal gesp roken is het te lang om te beginnen met de standaard Batch grootte. Als u deze waarde verder wilt afstemmen, berekent u de ruwe object grootte van uw gegevens en zorgt u ervoor dat de object grootte * Batch grootte kleiner is dan 2 MB. Als dat het geval is, kunt u de Batch grootte verg Roten om een betere door voer te krijgen.

**Door Voer:** Stel hier een hogere doorvoer instelling in zodat documenten sneller naar CosmosDB kunnen schrijven. Houd de hogere RU-kosten in acht op basis van een instelling voor hoge door voer.

**Budget voor schrijf doorvoer:** Gebruik een waarde die kleiner is dan het totaal van RUs per minuut. Als u een gegevens stroom hebt met een groot aantal Spark-partities, is het instellen van een budget doorvoer meer evenwicht over die partities.

## <a name="optimizing-transformations"></a>Transformaties optimaliseren

### <a name="optimizing-joins-exists-and-lookups"></a>Samen voegingen, bestaan en lookups optimaliseren

#### <a name="broadcasting"></a>Verzenden

In samen voegingen, zoek acties en bestaande trans formaties, als een of beide gegevens stromen klein genoeg zijn voor het geheugen van het worker-knoop punt, kunt u de prestaties optimaliseren door **broadcast** in te scha kelen. Broadcasten is wanneer u kleine gegevens frames naar alle knoop punten in het cluster verzendt. Hierdoor kan de Spark-engine een koppeling uitvoeren zonder dat de gegevens in de grote stream opnieuw worden gevolgd. Standaard wordt door de Spark-Engine automatisch bepaald of één zijde van een koppeling moet worden uitgezonden. Als u bekend bent met uw inkomende gegevens en weet dat de ene stroom aanzienlijk kleiner is dan de andere, kunt u een **vaste** uitzending selecteren. Met de vaste uitzending wordt Spark geforceerd de geselecteerde stroom uitgezonden. 

Als de verzonden gegevens te groot zijn voor het Spark-knoop punt, wordt er mogelijk een geheugen fout weer gegeven. Gebruik **geoptimaliseerde geheugen** clusters om geheugen fouten te voor komen. Als u uitzend-time-outs ondervindt tijdens de uitvoering van gegevens stromen, kunt u de uitschakeling van de uitzending uitschakelen. Dit leidt er echter toe dat gegevens stromen langzamer worden uitgevoerd.

Wanneer u werkt met gegevens bronnen die meer query's kunnen uitvoeren, zoals grote database query's, is het raadzaam om de broadcast uit te scha kelen voor samen voegen. Bron met lange query tijden kan Spark-time-outs veroorzaken wanneer het cluster naar reken knooppunten probeert te broadcasten. Een andere goede keus voor het uitschakelen van de uitzending is wanneer u een stroom in uw gegevens stroom hebt die waarden samenvoegt voor gebruik in een opzoek transformatie. Dit patroon kan de Spark-Optimizer verwarren en time-outs veroorzaken.

![Trans formatie optimaliseren](media/data-flow/joinoptimize.png "Deelname aan optimalisatie")

#### <a name="cross-joins"></a>Cross-samen voegingen

Als u letterlijke waarden in uw samenvoegings voorwaarden gebruikt of meerdere overeenkomsten aan beide zijden van een koppeling hebt, wordt de koppeling als een cross-koppeling door Spark uitgevoerd. Een cross join is een volledig Cartesisch product dat vervolgens de samengevoegde waarden filtert. Dit is aanzienlijk trager dan andere jointypen. Zorg ervoor dat u kolom verwijzingen aan beide zijden van uw samenvoegings voorwaarden hebt om de invloed op de prestaties te voor komen.

#### <a name="sorting-before-joins"></a>Sorteren voor samen voegingen

In tegens telling tot merge-koppeling in hulpprogram ma's als SSIS is de koppelings transformatie geen verplichte samenvoeg bewerking samen voegen. De koppelings sleutels hoeven niet te worden gesorteerd vóór de trans formatie. Het Azure Data Factory team wordt niet aanbevolen om sorteer bewerkingen te gebruiken voor het toewijzen van gegevens stromen.

### <a name="window-transformation-performance"></a>Prestaties van de venster transformatie

Met de [venster transformatie](data-flow-window.md) worden uw gegevens gepartitioneerd op waarde in kolommen die u als onderdeel van de ```over()``` component in de trans formatie-instellingen selecteert. Er zijn een aantal populaire statistische en analytische functies die worden weer gegeven in de Windows-trans formatie. Als uw use-case echter een venster voor uw hele gegevensset moet genereren voor het doel van de rang schikking ```rank()``` of het rijnummer ```rowNumber()``` , is het raadzaam om in plaats daarvan de [rang transformatie](data-flow-rank.md) en de [surrogaat sleutel transformatie](data-flow-surrogate-key.md)te gebruiken. Met deze trans formatie worden de volledige gegevensset-bewerkingen sneller uitgevoerd met behulp van deze functies.

### <a name="repartitioning-skewed-data"></a>Gescheefe gegevens opnieuw partitioneren

Bepaalde trans formaties, zoals samen voegingen en aggregaties, stellen uw gegevens partities in een andere volg orde en kunnen af en toe leiden tot gescheefe gegevens. Scheefgetrokken gegevens betekenen dat gegevens niet gelijkmatig over de partities worden verdeeld. Sterk scheefgetrokken gegevens kunnen leiden tot tragere downstream-trans formaties en Sink-schrijf bewerkingen. U kunt de scheefheid van uw gegevens op elk gewenst moment in een gegevens stroom controleren door te klikken op de trans formatie in het controle weergave.

![Scheefheid en kurtosis](media/data-flow/skewness-kurtosis.png "Scheefheid en kurtosis")

In het bewakings scherm ziet u hoe de gegevens worden verdeeld over elke partitie, samen met twee metrieken, scheefheid en kurtosis. **Scheefheid** is een meting van hoe asymmetrisch de gegevens zijn en kunnen een positieve, nul, negatieve of niet-gedefinieerde waarde hebben. Negatieve scheefheid betekent dat de linkerhelft langer is dan de rechter kant. **Kurtosis** is de meting of de gegevens met een hevige staart of met een lichtstaart worden gemeten. Hoge kurtosis-waarden zijn niet gewenst. De ideale bereiken van scheefheid liggen tussen-3 en 3 en het bereik van de kurtosis minder dan 10. Een eenvoudige manier om deze getallen te interpreteren is het partitie diagram te bekijken en te zien of 1 balk significant groter is dan de rest.

Als uw gegevens na een trans formatie niet gelijkmatig zijn gepartitioneerd, kunt u het [tabblad optimaliseren](#optimize-tab) gebruiken om opnieuw te partitioneren. Het opnieuw afnemen van gegevens neemt enige tijd in beslag en verbetert de prestaties van uw gegevens stroom mogelijk niet.

> [!TIP]
> Als u uw gegevens opnieuw partitioneert, maar u downstream-trans formaties hebt waarmee uw gegevens in een andere volg orde worden ingedeeld, gebruikt u hash-partitionering op een kolom die wordt gebruikt als een koppelings sleutel.

## <a name="using-data-flows-in-pipelines"></a>Gegevens stromen gebruiken in pijp lijnen 

Wanneer u complexe pijp lijnen met meerdere gegevens stromen bouwt, kan uw logische stroom een grote invloed hebben op de timing en de kosten. In deze sectie wordt de impact van verschillende architectuur strategieën besproken.

### <a name="executing-data-flows-in-parallel"></a>Gegevens stromen parallel uitvoeren

Als u meerdere gegevens stromen parallel uitvoert, draait ADF om afzonderlijke Spark-clusters voor elke activiteit. Hierdoor kan elke taak worden geïsoleerd en parallel worden uitgevoerd, maar zal er meerdere clusters tegelijk worden uitgevoerd.

Als uw gegevens stromen parallel worden uitgevoerd, wordt het aanbevolen om de eigenschap Azure IR time to Live niet in te scha kelen omdat deze wordt gebruikt om meerdere niet-gebruikte warme Pools te maken.

> [!TIP]
> In plaats van dezelfde gegevens stroom meerdere keren in een voor elke activiteit uit te voeren, moet u de gegevens in een Data Lake stage en Joker teken paden gebruiken om de gegevens in één gegevens stroom te verwerken.

### <a name="execute-data-flows-sequentially"></a>Gegevens stromen sequentieel uitvoeren

Als u de gegevens stroom activiteiten op volg orde uitvoert, is het raadzaam een TTL in te stellen in de Azure IR configuratie. De reken resources worden door ADF opnieuw gebruikt, wat resulteert in een snellere start tijd van het cluster. Elke activiteit zal nog steeds geïsoleerd een nieuwe Spark-context ontvangen voor elke uitvoering.

Het uitvoeren van taken zal waarschijnlijk de langste tijd duren om end-to-end uit te voeren, maar biedt een schone schei ding van logische bewerkingen.

### <a name="overloading-a-single-data-flow"></a>Eén gegevens stroom overbelasten

Als u al uw logica in één gegevens stroom plaatst, wordt de volledige taak door ADF uitgevoerd op één Spark-exemplaar. Hoewel dit een manier is om de kosten te reduceren, worden verschillende logische stromen gecombineerd en kunnen ze moeilijk worden bewaakt en worden fouten opgespoord. Als een onderdeel mislukt, zullen alle andere delen van de taak ook mislukken. Het Azure Data Factory Team raadt aan om gegevens stromen te organiseren door onafhankelijke stromen van bedrijfs logica. Als uw gegevens stroom te groot wordt, kunt u deze opsplitsen in gescheiden onderdelen, waardoor bewaking en fout opsporing eenvoudiger zijn. Hoewel er geen vaste limiet is voor het aantal trans formaties in een gegevens stroom, wordt de taak met te veel complex gemaakt.

### <a name="execute-sinks-in-parallel"></a>Sinks parallel uitvoeren

Het standaard gedrag van gegevens stroom-sinks is om elke Sink sequentieel, op een seriële manier uit te voeren en om te voor komen dat de gegevens stroom wordt uitgevoerd wanneer er een fout optreedt in de sink. Daarnaast worden alle sinks standaard ingesteld op dezelfde groep, tenzij u naar de eigenschappen van de gegevens stroom gaat en verschillende prioriteiten instelt voor de sinks.

Met gegevens stromen kunt u sinks groeperen in groepen op het tabblad Eigenschappen van gegevens stroom in de gebruikers interface van UI Designer. U kunt de volg orde van de uitvoering van uw sinks instellen en de combi natie van wastafels groeperen met hetzelfde groeps nummer. Als u groepen wilt beheren, kunt u een ADF vragen om sinks in dezelfde groep uit te voeren, zodat deze parallel worden uitgevoerd.

In de activiteit gegevens stroom uitvoeren met pijp lijn onder de sectie eigenschappen van ' sink ' is een optie om het laden van parallelle sinks in te scha kelen. Wanneer u ' parallel uitvoeren ' inschakelt, geeft u aan dat gegevens stromen op hetzelfde moment worden geschreven naar verbonden sinks, in plaats van op een opeenvolgende manier. Als u de parallelle optie wilt gebruiken, moeten de sinks zijn gegroepeerd en met dezelfde stroom worden verbonden via een nieuwe vertakking of voorwaardelijke splitsing.

## <a name="next-steps"></a>Volgende stappen

Zie andere artikelen over gegevens stromen met betrekking tot prestaties:

- [Activiteit gegevens stroom](control-flow-execute-data-flow-activity.md)
- [Prestaties van de gegevens stroom bewaken](concepts-data-flow-monitoring.md)

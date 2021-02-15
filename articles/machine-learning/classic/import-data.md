---
title: 'ML Studio (klassiek): trainings gegevens importeren-Azure'
description: Informatie over het importeren van uw gegevens in Azure Machine Learning Studio (klassiek) van verschillende gegevens bronnen. Meer informatie over welke gegevens typen en gegevens indelingen worden ondersteund.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/01/2019
ms.openlocfilehash: db5f3cc4b9530c4aeac40786756b36cc0ac98728
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520371"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-classic-from-various-data-sources"></a>Importeer uw trainings gegevens in Azure Machine Learning Studio (klassiek) van verschillende gegevens bronnen

**VAN TOEPASSING OP:**  ![Van toepassing op.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek) ![Niet van toepassing op.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Als u uw eigen gegevens in Machine Learning Studio (klassiek) wilt gebruiken voor het ontwikkelen en trainen van een predictive analytics oplossing, kunt u gegevens gebruiken van: 

* **Lokaal bestand** : lokale gegevens vooraf laden van de vaste schijf voor het maken van een gegevensset module in uw werk ruimte
* **Online gegevens bronnen** : gebruik de module [gegevens importeren][import-data] om toegang te krijgen tot gegevens van een van de verschillende online bronnen terwijl uw experiment wordt uitgevoerd
* **Machine learning Studio (klassiek) experiment** : gegevens gebruiken die zijn opgeslagen als een gegevensset in machine learning Studio (klassiek)
* [**SQL Server Data Base**](use-data-from-an-on-premises-sql-server.md) -gegevens uit een SQL Server-Data Base gebruiken zonder gegevens hand matig te hoeven kopiëren

> [!NOTE]
> Er zijn een aantal voorbeeld gegevens sets beschikbaar in Machine Learning Studio (klassiek) die u voor trainings gegevens kunt gebruiken. Zie [de voorbeeld gegevens sets gebruiken in azure machine learning Studio (klassiek)](use-sample-datasets.md)voor meer informatie.

## <a name="prepare-data"></a>Gegevens voorbereiden

Machine Learning Studio (klassiek) is ontworpen om te werken met rechthoekige of tabellaire gegevens, zoals tekst gegevens die worden gescheiden of gestructureerd van gegevens uit een Data Base, maar in sommige gevallen kunnen niet-rechthoekige gegevens worden gebruikt.

Het is het beste als uw gegevens relatief schoon zijn voordat u deze in Studio (klassiek) importeert. Stel dat u problemen ondervindt zoals teken reeksen die geen aanhalings tekens zijn.

Er zijn echter modules beschikbaar in Studio (klassiek) waarmee u gegevens in uw experiment kunt bewerken nadat u uw gegevens hebt geïmporteerd. Afhankelijk van de machine learning algoritmen die u gebruikt, moet u mogelijk bepalen hoe u gegevens structurele kwesties, zoals ontbrekende waarden en sparse gegevens, kunt afhandelen. er zijn modules die u kunnen helpen. Zoek in de sectie **gegevens transformatie** van het module palet naar modules die deze functies uitvoeren.

U kunt op elk gewenst moment in uw experiment de gegevens weer geven of downloaden die door een module worden geproduceerd door te klikken op de uitvoer poort. Afhankelijk van de module zijn er mogelijk verschillende download opties beschikbaar of kunt u de gegevens in de webbrowser in Studio (klassiek) visualiseren.

## <a name="supported-data-formats-and-data-types"></a>Ondersteunde gegevens indelingen en gegevens typen

U kunt een aantal gegevens typen importeren in uw experiment, afhankelijk van het mechanisme dat u gebruikt voor het importeren van gegevens en de herkomst van:

* Tekst zonder opmaak (. txt)
* Door komma's gescheiden waarden (CSV) met een header (. CSV) of zonder (.nh.csv)
* Door tabs gescheiden waarden (TSV) met een header (. tsv) of zonder (. NH. tsv)
* Excel-bestand
* Azure Table
* Hive-tabel
* SQL database tabel
* OData-waarden
* SVMLight-gegevens (. SVMLight) (Zie de [SVMLight-definitie](http://svmlight.joachims.org/) voor indelings informatie)
* Kenmerk relatie bestands indeling (ARFF) gegevens (. ARFF) (Zie de [ARFF-definitie](https://www.cs.waikato.ac.nz/ml/weka/arff.html) voor indelings informatie)
* Zip-bestand (. zip)
* R-object of werkruimte bestand (. RData

Als u gegevens importeert in een indeling zoals ARFF die meta gegevens bevat, gebruikt Studio (klassiek) deze meta gegevens om de kop en het gegevens type van elke kolom te definiëren.

Als u gegevens importeert zoals een TSV-of CSV-indeling zonder deze meta gegevens, wordt door Studio (klassiek) het gegevens type voor elke kolom afgenomen door de gegevens te bemonsteren. Als de gegevens ook geen kolom koppen bevatten, biedt studio (klassiek) standaard namen.

U kunt de koppen en gegevens typen voor kolommen expliciet opgeven of wijzigen met behulp van de module [meta gegevens bewerken][edit-metadata] .

De volgende gegevens typen worden herkend door Studio (klassiek):

* Tekenreeks
* Geheel getal
* Dubbel
* Boolean
* DateTime
* TimeSpan

Studio gebruikt een intern gegevens type met de naam ***Data Table*** voor het door geven van gegevens tussen modules. U kunt uw gegevens in de indeling van de gegevens tabel expliciet converteren met behulp van de module [converteren naar dataset][convert-to-dataset] .

Alle modules die andere indelingen dan gegevens tabel accepteren, converteren de gegevens naar de gegevens tabel op de achtergrond voordat deze aan de volgende module worden door gegeven.

Indien nodig kunt u de indeling van de gegevens tabel weer omzetten in de CSV-, TSV-, ARFF-of SVMLight-indeling met behulp van andere conversie modules.
Zoek in de sectie **gegevens indeling conversies** van het palet module voor modules die deze functies uitvoeren.

## <a name="data-capacities"></a>Gegevens capaciteit

Modules in Machine Learning Studio (klassiek) ondersteunen gegevens sets van Maxi maal 10 GB voor veelvoorkomende, gebruiks voorbeelden. Als een module meer dan één invoer heeft, is de waarde 10 GB de totale invoergrootte. U kunt een voor beeld van grotere gegevens sets maken met behulp van query's in Hive of Azure SQL Database, of u kunt de voor bereiding voor Learning by Count gebruiken voordat u de gegevens importeert.  

U kunt de volgende typen gegevens in grotere gegevenssets opnemen tijdens het normaliseren van kenmerken, tot maximaal 10 GB:

* Sparse
* Categorische gegevens
* Tekenreeksen
* Binaire gegevens

De volgende modules zijn beperkt tot gegevenssets die kleiner zijn dan 10 GB:

* Aanbevelingsmodules
* De module Synthetic Minority Oversampling Technique (SMOTE)
* Scriptmodules: R, Python, SQL
* Modules waarbij de grootte van de uitvoer groter is dan invoergegevens, zoals Join- of hash-functies
* Kruisvalidatie, Tune Model Hyperparameters, ordinale regressie en One-vs-All-multiklasse, wanneer het aantal herhalingen groot is

Voor gegevens sets die groter zijn dan een paar GB, moet u de gegevens uploaden naar Azure Storage of Azure SQL Database of gebruikmaken van Azure HDInsight, in plaats van rechtstreeks vanuit een lokaal bestand te uploaden.

Meer informatie over afbeeldings gegevens vindt u in de module [afbeeldingen importeren](/azure/machine-learning/studio-module-reference/import-images#bkmk_Notes) .

## <a name="import-from-a-local-file"></a>Importeren uit een lokaal bestand

U kunt een gegevens bestand van uw harde schijf uploaden om te gebruiken als trainings gegevens in Studio (klassiek). Wanneer u een gegevens bestand importeert, maakt u een gegevensset-module die gereed is voor gebruik in experimenten in uw werk ruimte.

Ga als volgt te werk om gegevens te importeren vanaf een lokale harde schijf:

1. Klik op **+ Nieuw** onder aan het venster Studio (klassiek).
2. Selecteer **gegevensset** en **van het lokale bestand**.
3. Blader in het dialoog venster **een nieuwe gegevensset uploaden** naar het bestand dat u wilt uploaden.
4. Voer een naam in, Identificeer het gegevens type en voer eventueel een beschrijving in. Een beschrijving wordt aanbevolen-Hiermee kunt u de kenmerken vastleggen van de gegevens die u wilt onthouden wanneer u de gegevens in de toekomst gebruikt.
5. Met het selectie vakje **Dit is de nieuwe versie van een bestaande gegevensset,** waarmee u een bestaande gegevensset kunt bijwerken met nieuwe gegevens. Als u dit wilt doen, klikt u op dit selectie vakje en voert u de naam van een bestaande gegevensset in.

![Een nieuwe gegevensset uploaden](./media/import-data/upload-dataset-from-local-file.png)

De upload tijd is afhankelijk van de grootte van uw gegevens en de snelheid van de verbinding met de service. Als u weet dat het bestand lang duurt, kunt u andere dingen doen binnen Studio (klassiek) terwijl u wacht. Als de browser wordt gesloten voordat het uploaden van gegevens is voltooid, mislukt de upload echter.

Zodra de gegevens zijn geüpload, wordt deze opgeslagen in een gegevensset-module en is deze beschikbaar voor elk experiment in uw werk ruimte.

Wanneer u een experiment bewerkt, kunt u de gegevens sets die u hebt geüpload, vinden in de lijst **mijn gegevens sets** onder de lijst **opgeslagen gegevens sets** in het module palet. U kunt de gegevensset slepen en neerzetten op het canvas als u de gegevensset voor verdere analyse en machine learning wilt gebruiken.

## <a name="import-from-online-data-sources"></a>Importeren uit online gegevens bronnen

Met de module [gegevens importeren][import-data] kan uw experiment gegevens importeren uit verschillende online gegevens bronnen terwijl het experiment wordt uitgevoerd.

> [!NOTE]
> Dit artikel bevat algemene informatie over de module [gegevens importeren][import-data] . Zie het onderwerp module verwijzing voor de module [gegevens importeren][import-data] voor meer informatie over de typen gegevens die u kunt openen, opmaken, para meters en antwoorden op veelgestelde vragen.

U kunt met behulp van de module [gegevens importeren][import-data] toegang krijgen tot gegevens van een van de verschillende online gegevens bronnen terwijl uw experiment wordt uitgevoerd:

* Een web-URL met HTTP
* Hadoop met behulp van HiveQL
* Azure Blob Storage
* Azure Table
* Azure SQL Database. SQL Managed instance of SQL Server
* Er is momenteel een gegevensfeeds provider, OData
* Azure Cosmos DB

Omdat deze trainings gegevens worden geopend terwijl uw experiment actief is, is het alleen beschikbaar in dat experiment. In vergelijking worden gegevens die zijn opgeslagen in een gegevensset-module, beschikbaar voor elk experiment in uw werk ruimte.

Als u toegang wilt krijgen tot online gegevens bronnen in uw studio-experiment (klassiek), voegt u de module [gegevens importeren][import-data] toe aan uw experiment. Selecteer vervolgens **wizard Import gegevens starten** onder **Eigenschappen** voor stapsgewijze instructies om de gegevens bron te selecteren en configureren. U kunt ook hand matig **gegevens bron** selecteren onder **Eigenschappen** en de para meters opgeven die nodig zijn om toegang te krijgen tot de gegevens.

De online gegevens bronnen die worden ondersteund, worden in de onderstaande tabel gespecificeerd. In deze tabel vindt u ook een overzicht van de bestands indelingen die worden ondersteund en de para meters die worden gebruikt voor toegang tot de gegevens.

> [!IMPORTANT]
> Op dit moment kunnen de modules [gegevens importeren][import-data] en [exporteren][export-data] gegevens alleen lezen en schrijven vanuit Azure Storage die is gemaakt met het klassieke implementatie model. Met andere woorden, het nieuwe Azure Blob Storage-account type dat een hot Storage-toegangs laag of een cool Storage-toegangs laag biedt, wordt nog niet ondersteund.
>
> Over het algemeen zijn alle Azure Storage-accounts die u mogelijk hebt gemaakt voordat deze service optie beschikbaar werd, niet van invloed.
> Als u een nieuw account wilt maken, selecteert u **klassiek** voor het implementatie model of gebruikt u Resource Manager en selecteert u **Algemeen** gebruik in plaats van **Blob Storage** voor het **soort account**.
>
> Zie [Azure Blob Storage: warme en cool Storage-lagen](../../storage/blobs/storage-blob-storage-tiers.md)voor meer informatie.

### <a name="supported-online-data-sources"></a>Ondersteunde online gegevens bronnen
De module **import data** van Azure machine learning Studio (Classic) ondersteunt de volgende gegevens bronnen:

| Gegevensbron | Description | Parameters |
| --- | --- | --- |
| Web-URL via HTTP |Hiermee worden gegevens gelezen in CSV-indeling (door komma's gescheiden waarden), door tabs gescheiden waarden (TSV), kenmerk-relation File Format (ARFF) en support vector machines (SVM-licht), van een web-URL die gebruikmaakt van HTTP |<b>URL</b>: Hiermee geeft u de volledige naam van het bestand, met inbegrip van de site-URL en de bestands naam, op met een wille keurige extensie. <br/><br/><b>Gegevens indeling</b>: Hiermee geeft u een van de ondersteunde gegevens indelingen op: CSV, tsv, ARFF of SVM-Light. Als de gegevens een veldnamenrij hebben, wordt deze gebruikt om kolom namen toe te wijzen. |
| Hadoop/HDFS |Hiermee worden gegevens uit de gedistribueerde opslag in Hadoop gelezen. U geeft de gewenste gegevens op met behulp van HiveQL, een SQL-achtige query taal. HiveQL kan ook worden gebruikt om gegevens samen te voegen en gegevens te filteren voordat u de gegevens toevoegt aan Studio (klassiek). |<b>Hive-database query</b>: Hiermee geeft u de Hive-query op die wordt gebruikt voor het genereren van de gegevens.<br/><br/><b>URI</b> van de HCatalog-server: Geef de naam van uw cluster op met de notatie *&lt; uw cluster naam &gt; . azurehdinsight.net.*<br/><br/><b>Hadoop-gebruikers accountnaam</b>: Hiermee geeft u de naam van het Hadoop-gebruikers account op dat is gebruikt om het cluster in te richten.<br/><br/><b>Hadoop-wacht woord voor gebruikers account</b> : Hiermee geeft u de referenties op die worden gebruikt bij het inrichten van het cluster. Zie [Hadoop-clusters maken in HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)voor meer informatie.<br/><br/><b>Locatie van uitvoer gegevens</b>: Hiermee geeft u op of de gegevens worden opgeslagen in een Hadoop Distributed File System (HDFS) of in Azure. <br/><ul>Als u uitvoer gegevens opslaat in HDFS, geeft u de URI van de HDFS-server op. (Vergeet niet om de naam van het HDInsight-cluster te gebruiken zonder het HTTPS://-voor voegsel). <br/><br/>Als u de uitvoer gegevens in azure opslaat, moet u de naam van het Azure Storage-account, de toegangs sleutel voor de opslag en de opslag container naam opgeven.</ul> |
| SQL-database |Hiermee worden gegevens gelezen die zijn opgeslagen in Azure SQL Database, een beheerd exemplaar van SQL of een SQL Server-Data Base die wordt uitgevoerd op een virtuele machine van Azure. |<b>Database server naam</b>: Hiermee geeft u de naam op van de server waarop de data base wordt uitgevoerd.<br/><ul>In het geval van Azure SQL Database voert u de server naam in die wordt gegenereerd. Normaal gesp roken heeft het de notatie *&lt; generated_identifier &gt; . database.Windows.net.* <br/><br/>In het geval van een SQL-Server die op een virtuele Azure-machine wordt gehost, voert u *TCP: &lt; DNS-naam van de virtuele machine in &gt; , 1433*</ul><br/><b>Database naam </b>: Hiermee geeft u de naam op van de Data Base op de server. <br/><br/><b>Naam van Server gebruikers account</b>: Hiermee geeft u een gebruikers naam op voor een account met toegangs machtigingen voor de data base. <br/><br/><b>Wacht woord Server gebruikers account</b>: Hiermee geeft u het wacht woord voor het gebruikers account op.<br/><br/><b>Database query</b>: Voer een SQL-instructie in met een beschrijving van de gegevens die u wilt lezen. |
| On-premises SQL database |Hiermee worden gegevens gelezen die zijn opgeslagen in een SQL database. |<b>Gegevens gateway</b>: Hiermee geeft u de naam op van de Data Management gateway die is geïnstalleerd op een computer waarop de SQL Server-Data Base kan worden geopend. Zie voor meer informatie over het instellen van de gateway [geavanceerde analyses uitvoeren met Azure machine learning Studio (klassiek) met behulp van gegevens van een SQL-Server](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Database server naam</b>: Hiermee geeft u de naam op van de server waarop de data base wordt uitgevoerd.<br/><br/><b>Database naam </b>: Hiermee geeft u de naam op van de Data Base op de server. <br/><br/><b>Naam van Server gebruikers account</b>: Hiermee geeft u een gebruikers naam op voor een account met toegangs machtigingen voor de data base. <br/><br/><b>Gebruikers naam en wacht woord</b>: Klik op <b>waarden opgeven</b> om uw database referenties in te voeren. U kunt geïntegreerde Windows-verificatie of SQL Server verificatie gebruiken, afhankelijk van hoe uw SQL Server is geconfigureerd.<br/><br/><b>Database query</b>: Voer een SQL-instructie in met een beschrijving van de gegevens die u wilt lezen. |
| Azure Table |Hiermee worden gegevens uit de Table service in Azure Storage gelezen.<br/><br/>Als u grote hoeveel heden gegevens zelden leest, moet u de Azure Table-service gebruiken. Het biedt een flexibele, niet-relationele (NoSQL), uiterst schaal bare, goedkope en Maxi maal beschik bare opslag oplossing. |De opties in de **import gegevens** veranderen, afhankelijk van of u toegang hebt tot open bare gegevens of een privé-opslag account waarvoor aanmeldings referenties zijn vereist. Dit wordt bepaald door het <b>verificatie type</b> dat de waarde ' PublicOrSAS ' of ' account ' kan hebben, waarvan elk een eigen set para meters heeft. <br/><br/><b>Open bare of Shared Access Signature SAS-URI</b>: de para meters zijn:<br/><br/><ul><b>Tabel-URI</b>: Hiermee geeft u de open bare of SAS-URL voor de tabel op.<br/><br/><b>Hiermee geeft u de rijen op die moeten worden gescand op eigenschapnamen</b>: de waarden zijn <i>TopN</i> om het opgegeven aantal rijen te scannen of <i>ScanAll</i> om alle rijen in de tabel op te halen. <br/><br/>Als de gegevens homo geen zijn en voorspelbaar zijn, is het raadzaam om *TopN* te selecteren en een getal in te voeren voor N. Voor grote tabellen kan dit leiden tot snellere lees tijden.<br/><br/>Als de gegevens zijn gestructureerd met sets eigenschappen die variëren op basis van de diepte en positie van de tabel, kiest u de optie *ScanAll* om alle rijen te scannen. Zo zorgt u ervoor dat de integriteit van de resulterende eigenschappen en meta gegevens worden geconverteerd.<br/><br/></ul><b>Privé-opslag account</b>: de para meters zijn: <br/><br/><ul><b>Account naam</b>: Hiermee geeft u de naam op van het account dat de te lezen tabel bevat.<br/><br/><b>Account sleutel</b>: Hiermee geeft u de opslag sleutel op die aan het account is gekoppeld.<br/><br/><b>Tabel naam</b> : Hiermee geeft u de naam op van de tabel die de gegevens bevat die moeten worden gelezen.<br/><br/><b>Rijen om te scannen op eigenschapnamen</b>: de waarden zijn <i>TopN</i> om het opgegeven aantal rijen te scannen of <i>ScanAll</i> om alle rijen in de tabel op te halen.<br/><br/>Als de gegevens homo geen zijn en voorspelbaar zijn, raden we u aan *TopN* te selecteren en een getal in te voeren voor N. Voor grote tabellen kan dit leiden tot snellere lees tijden.<br/><br/>Als de gegevens zijn gestructureerd met sets eigenschappen die variëren op basis van de diepte en positie van de tabel, kiest u de optie *ScanAll* om alle rijen te scannen. Zo zorgt u ervoor dat de integriteit van de resulterende eigenschappen en meta gegevens worden geconverteerd.<br/><br/> |
| Azure Blob Storage |Hiermee worden gegevens gelezen die zijn opgeslagen in de Blob service in Azure Storage, inclusief afbeeldingen, ongestructureerde tekst of binaire gegevens.<br/><br/>U kunt de Blob service gebruiken om gegevens openbaar weer te geven of om toepassings gegevens privé op te slaan. U hebt vanaf elke locatie toegang tot uw gegevens door HTTP-of HTTPS-verbindingen te gebruiken. |De opties in de module **gegevens importeren** wijzigen, afhankelijk van of u toegang hebt tot open bare gegevens of een privé-opslag account waarvoor aanmeldings referenties zijn vereist. Dit wordt bepaald door het <b>verificatie type</b> dat de waarde ' PublicOrSAS ' of ' account ' kan hebben.<br/><br/><b>Open bare of Shared Access Signature SAS-URI</b>: de para meters zijn:<br/><br/><ul><b>URI</b>: Hiermee geeft u de open bare of SAS-URL op voor de opslag-blob.<br/><br/><b>Bestands indeling</b>: Hiermee geeft u de indeling van de gegevens in de BLOB service. De ondersteunde indelingen zijn CSV, TSV en ARFF.<br/><br/></ul><b>Privé-opslag account</b>: de para meters zijn: <br/><br/><ul><b>Account naam</b>: Hiermee geeft u de naam op van het account dat de BLOB bevat die u wilt lezen.<br/><br/><b>Account sleutel</b>: Hiermee geeft u de opslag sleutel op die aan het account is gekoppeld.<br/><br/><b>Pad naar container, map of BLOB </b> : Hiermee geeft u de naam van de BLOB op die de te lezen gegevens bevat.<br/><br/><b>Indeling van BLOB-bestand</b>: Hiermee geeft u de indeling van de gegevens in de BLOB-service op. De ondersteunde gegevens indelingen zijn CSV, TSV, ARFF, CSV met een opgegeven code ring en Excel. <br/><br/><ul>Als de notatie CSV of TSV is, moet u aangeven of het bestand een veldnamenrij bevat.<br/><br/>U kunt de Excel-optie gebruiken om gegevens uit Excel-werkmappen te lezen. Geef in de optie <i>Excel-gegevens indeling</i> aan of de gegevens zich in een Excel-werkblad bereik of in een Excel-tabel bevinden. Geef in het <i>Excel-werk blad of de Inge sloten tabel </i>optie de naam op van het blad of de tabel waarvan u wilt lezen.</ul><br/> |
| Data feed-provider |Hiermee worden gegevens van een ondersteunde feed-provider gelezen. Momenteel wordt alleen de OData-indeling (Open Data Protocol) ondersteund. |<b>Type gegevens inhoud</b>: Hiermee geeft u de OData-indeling op.<br/><br/><b>Bron-URL</b>: Hiermee geeft u de volledige URL voor de gegevensfeed op. <br/>Bijvoorbeeld, de volgende URL-Lees bewerkingen van de voorbeeld database noorden wind: https://services.odata.org/northwind/northwind.svc/ |

## <a name="import-from-another-experiment"></a>Importeren uit een ander experiment

Wanneer u een tussentijds resultaat van een experiment wilt maken en dit als onderdeel van een ander experiment wilt gebruiken. Als u dit wilt doen, slaat u de module op als een gegevensset:

1. Klik op de uitvoer van de module die u wilt opslaan als een gegevensset.
2. Klik op **Opslaan als gegevensset**.
3. Wanneer u hierom wordt gevraagd, voert u een naam en een beschrijving in waarmee u de gegevensset eenvoudig kunt identificeren.
4. Klik op het selectie vakje **OK** .

Wanneer het opslaan is voltooid, is de gegevensset beschikbaar voor gebruik binnen een experiment in uw werk ruimte. U vindt deze in de lijst **opgeslagen gegevens sets** in het module palet.

## <a name="next-steps"></a>Volgende stappen

[Azure Machine Learning Studio-webservices implementeren die gebruikmaken van modules voor het importeren en exporteren van gegevens](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data
[export-data]: /azure/machine-learning/studio-module-reference/export-data


<!-- Module References -->
[convert-to-dataset]: /azure/machine-learning/studio-module-reference/convert-to-dataset
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[import-data]: /azure/machine-learning/studio-module-reference/import-data
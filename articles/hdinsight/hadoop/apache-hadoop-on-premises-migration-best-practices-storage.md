---
title: 'Opslag: on-premises Apache Hadoop migreren naar Azure HDInsight'
description: Meer informatie over best practices voor opslag voor het migreren van on-premises Hadoop-clusters naar Azure HDInsight.
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/10/2019
ms.openlocfilehash: 8d87d2164a5131b71a2000243c37553610497750
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944850"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight"></a>On-premises Apache Hadoop clusters migreren naar Azure HDInsight

Dit artikel bevat aanbevelingen voor gegevens opslag in azure HDInsight-systemen. Het is onderdeel van een serie die aanbevolen procedures biedt voor het migreren van on-premises Apache Hadoop systemen naar Azure HDInsight.

## <a name="choose-right-storage-system-for-hdinsight-clusters"></a>Kies het juiste opslag systeem voor HDInsight-clusters

De mapstructuur van het on-premises Apache Hadoop bestands systeem (HDFS) kan opnieuw worden gemaakt in Azure Blob-opslag of Azure Data Lake Storage. U kunt vervolgens HDInsight-clusters die worden gebruikt voor berekeningen, veilig verwijderen zonder dat er gebruikers gegevens verloren gaan. Beide services kunnen worden gebruikt als het standaard bestandssysteem en een extra bestands systeem voor een HDInsight-cluster. Het HDInsight-cluster en het opslag account moeten worden gehost in dezelfde regio.

### <a name="azure-storage"></a>Azure Storage

HDInsight-clusters kunnen de BLOB-container in Azure Storage gebruiken als het standaard bestandssysteem of een extra bestands systeem. Het opslag account voor de standaard laag wordt ondersteund voor gebruik met HDInsight-clusters. De premier-laag wordt niet ondersteund. In de standaard blobcontainer worden clusterspecifieke gegevens opgeslagen, zoals taakgeschiedenis en logboeken. Het delen van één BLOB-container als het standaard bestandssysteem voor meerdere clusters wordt niet ondersteund.

De opslag accounts die zijn gedefinieerd in het aanmaak proces en de bijbehorende sleutels worden opgeslagen `%HADOOP_HOME%/conf/core-site.xml` op de cluster knooppunten. Ze zijn ook toegankelijk via de sectie ' aangepaste kern site ' in de Ambari-gebruikers interface. De sleutel van het opslag account wordt standaard versleuteld en er wordt een aangepast ontsleutelings script gebruikt om de sleutels te ontsleutelen voordat deze wordt door gegeven aan Hadoop-daemons. De taken, waaronder Hive, MapReduce, Hadoop streaming en Pig, bevatten een beschrijving van opslag accounts en meta gegevens.

Azure Storage kunnen geo-replicatie zijn. Hoewel geo-replicatie geografisch herstel en gegevens redundantie biedt, heeft een failover naar de geo-gerepliceerde locatie ernstige gevolgen voor de prestaties en kunnen er extra kosten in rekening worden gebracht. Het is raadzaam om de geo-replicatie goed en alleen te kiezen als de waarde van de gegevens de extra kosten waard is.

Een van de volgende indelingen kan worden gebruikt om toegang te krijgen tot gegevens die zijn opgeslagen in Azure Storage:

|Data Access-indeling |Beschrijving |
|---|---|
|`wasb:///`|Toegang tot standaard opslag met niet-versleutelde communicatie.|
|`wasbs:///`|Toegang tot de standaard opslag met behulp van gecodeerde communicatie.|
|`wasb://<container-name>@<account-name>.blob.core.windows.net/`|Wordt gebruikt bij de communicatie met een niet-standaard-opslag account. |

Met [schaalbaarheids doelen voor standaard opslag accounts](../../storage/common/scalability-targets-standard-account.md) worden de huidige limieten voor Azure Storage accounts vermeld. Als de behoeften van de toepassing de schaalbaarheids doelen van één opslag account overschrijden, kan de toepassing worden gebouwd voor het gebruik van meerdere opslag accounts en vervolgens gegevens objecten in die opslag accounts partitioneren.

[Azure Opslaganalyse](../../storage/common/storage-analytics.md) biedt metrische gegevens voor alle opslag services en Azure portal kunnen worden geconfigureerd om metrische gegevens over verzamelen te visualiseren via grafieken. Waarschuwingen kunnen worden gemaakt om te melden wanneer de drempel waarden voor de metrische gegevens van de opslag resource zijn bereikt.

Azure Storage biedt een [zacht verwijderen voor blob-objecten](../../storage/blobs/soft-delete-blob-overview.md) om gegevens te herstellen wanneer het per ongeluk is gewijzigd of verwijderd door een toepassing of een andere gebruiker van het opslag account.

U kunt [BLOB-moment opnamen](/rest/api/storageservices/creating-a-snapshot-of-a-blob)maken. Een moment opname is een alleen-lezen versie van een blob die op een bepaald moment wordt gemaakt en biedt een manier om een back-up van een BLOB te maken. Zodra een moment opname is gemaakt, kan deze worden gelezen, gekopieerd of verwijderd, maar niet worden gewijzigd.

> [!Note]
> Voor oudere versies van on-premises Hadoop-distributies die geen ' wasbs-certificaat hebben, moeten ze worden geïmporteerd in het Archief voor Java-vertrouwens relaties.

De volgende methoden kunnen worden gebruikt voor het importeren van certificaten in het archief Java-vertrouwens relaties:

Het TLS/SSL-certificaat van Azure Blob naar een bestand downloaden

```bash
echo -n | openssl s_client -connect <storage-account>.blob.core.windows.net:443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > Azure_Storage.cer
```

Importeer het bovenstaande bestand naar het archief met de Java-vertrouwens relatie op alle knoop punten

```bash
keytool -import -trustcacerts -keystore /path/to/jre/lib/security/cacerts -storepass changeit -noprompt -alias blobtrust -file Azure_Storage.cer
```

Controleer of het toegevoegde certificaat zich in het vertrouwens archief bevindt

```bash
keytool -list -v -keystore /path/to/jre/lib/security/cacerts
```

Raadpleeg voor meer informatie de volgende artikelen:

- [Azure Storage gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-blob-storage.md)
- [Schaalbaarheids doelen voor standaard opslag accounts](../../storage/common/scalability-targets-standard-account.md)
- [Schaalbaarheids- en prestatiedoelen voor Blob-opslag](../../storage/blobs/scalability-targets.md)
- [Controlelijst voor prestaties en schaalbaarheid van Microsoft Azure Storage](../../storage/blobs/storage-performance-checklist.md)
- [Microsoft Azure Storage bewaken, problemen opsporen en oplossen](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)
- [Een Storage-account bewaken in de Azure-portal](../../storage/common/storage-monitor-storage-account.md)

### <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 implementeert de Access Control model van HDFS en POSIX-stijl. Het biedt de eerste klasse-integratie met Azure AD voor verfijnd toegangs beheer. Er zijn geen limieten voor de grootte van gegevens die kunnen worden opgeslagen of de mogelijkheid om een enorm parallelle analyse uit te voeren.

Raadpleeg voor meer informatie de volgende artikelen:

- [Maak HDInsight-clusters met Data Lake Storage Gen1 met behulp van de Azure Portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
- [Data Lake Storage Gen1 gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-data-lake-storage-gen1.md)

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 is de meest recente opslag aanbieding. Het combineert de kern mogelijkheden van de eerste generatie Azure Data Lake Storage Gen1 met een Hadoop-compatibel bestandssysteem dat rechtstreeks is geïntegreerd in Azure Blob Storage. Deze uitbrei ding combineert de schaal en kosten voor delen van object opslag met de betrouw baarheid en prestaties die doorgaans alleen worden gekoppeld aan on-premises bestands systemen.

Azure Data Lake Storage gen 2 is gebaseerd op [Azure Blob-opslag](../../storage/blobs/storage-blobs-introduction.md) en biedt u de mogelijkheid om met gegevens te werken met behulp van zowel bestands systeem-als object opslag-modellen. Functies van [Azure data Lake Storage gen1](../../data-lake-store/index.yml), zoals bestandssysteem semantiek, beveiliging op bestands niveau en schaal, worden gecombineerd met goedkope, gelaagde opslag, hoge Beschik baarheid/herstel na nood gevallen en een groot hulp programma voor het maken van SDK/hulpprogram Ma's vanuit [Azure Blob-opslag](../../storage/blobs/storage-blobs-introduction.md). In Data Lake Storage Gen2 blijven alle kwaliteiten van object opslag aanwezig terwijl de voor delen van een bestandssysteem interface die is geoptimaliseerd voor analysewerk belastingen worden toegevoegd.

Een fundamenteel onderdeel van Data Lake Storage Gen2 is het toevoegen van een [hiërarchische naam ruimte](../../storage/blobs/data-lake-storage-namespace.md) aan de Blob Storage-service, waarmee objecten/bestanden worden ingedeeld in een hiërarchie van mappen voor uitvoering van gegevens toegang. De hiërarchische structuur maakt bewerkingen, zoals het wijzigen van de naam of het verwijderen van een map voor één atomische meta gegevens, in de Directory in plaats van het inventariseren en verwerken van alle objecten die het voor voegsel van de directory delen.

In het verleden moesten cloud-gebaseerde analyses in het gedrang komen op het gebied van prestaties, beheer en beveiliging. De belangrijkste functies van Azure Data Lake Storage (ADLS) Gen2 zijn als volgt:

- **Hadoop-compatibele toegang**: met Azure data Lake Storage Gen2 kunt u gegevens beheren en openen, net zoals u dat zou doen met een [Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Het nieuwe [ABFS-stuur programma](../../storage/blobs/data-lake-storage-abfs-driver.md) is beschikbaar in alle Apache Hadoop omgevingen die zijn opgenomen in [Azure HDInsight](../index.yml). Met dit stuur programma kunt u toegang krijgen tot gegevens die zijn opgeslagen in Data Lake Storage Gen2.

- **Een superset van POSIX-machtigingen**: het beveiligings model voor data Lake Gen2 biedt volledige ondersteuning van ACL-en POSIX-machtigingen samen met een extra granulatie specifiek voor data Lake Storage Gen2. Instellingen kunnen worden geconfigureerd via beheer hulpprogramma's of via Frameworks als Hive en Spark.

- **Rendabel: data Lake Storage Gen2** biedt goedkope opslag capaciteit en-trans acties. Naarmate de volledige levens cyclus van gegevens overgaat, worden de facturerings tarieven gewijzigd om de kosten te minimaliseren via ingebouwde functies, zoals [Azure Blob Storage-levens cyclus](../../storage/blobs/storage-lifecycle-management-concepts.md).

- **Werkt met hulpprogram ma's voor Blob-opslag, frameworks en apps**: data Lake Storage Gen2 blijft werken met een breed scala aan hulpprogram ma's, frameworks en toepassingen die momenteel bestaan voor Blob Storage.

- **Geoptimaliseerd stuur programma**: het Azure Blob-bestandssysteem stuur programma (ABFS) is speciaal voor Big Data Analytics [geoptimaliseerd](../../storage/blobs/data-lake-storage-abfs-driver.md) . De bijbehorende REST Api's worden opgehaald via het DFS-eind punt, dfs.core.windows.net.

Een van de volgende indelingen kan worden gebruikt om toegang te krijgen tot gegevens die zijn opgeslagen in ADLS Gen2:
- `abfs:///`: Toegang tot de standaard Data Lake Storage voor het cluster.
- `abfs://file_system@account_name.dfs.core.windows.net`: Wordt gebruikt bij de communicatie met een niet-standaard Data Lake Storage.

Raadpleeg voor meer informatie de volgende artikelen:

- [Inleiding tot Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md)
- [Het Azure Blob-bestandssysteem stuur programma (ABFS.md)](../../storage/blobs/data-lake-storage-abfs-driver.md)
- [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="secure-azure-storage-keys-within-on-premises-hadoop-cluster-configuration"></a>Azure Storage sleutels beveiligen in de configuratie van een on-premises Hadoop-cluster

De Azure Storage sleutels die worden toegevoegd aan de Hadoop-configuratie bestanden, maken van connectiviteit tussen on-premises HDFS en Azure Blob-opslag. Deze sleutels kunnen worden beveiligd door ze te versleutelen met het Hadoop-referentie provider Framework. Nadat deze zijn versleuteld, kunnen ze veilig worden opgeslagen en geopend.

**De referenties inrichten:**

```bash
hadoop credential create fs.azure.account.key.account.blob.core.windows.net -value <storage key> -provider jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks/file
```

**Om het bovenstaande pad naar de provider toe te voegen aan de core-site.xml of aan de Ambari-configuratie onder aangepaste kern-site:**

```xml
<property>
    <name>hadoop.security.credential.provider.path</name>
        <value>
        jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks
        </value>
    <description>Path to interrogate for protected credentials.</description>
</property>
```

> [!Note]
> De eigenschap van het provider pad kan ook worden toegevoegd aan de distcp-opdracht regel in plaats van de sleutel op het cluster niveau op core-site.xml als volgt op te slaan:

```bash
hadoop distcp -D hadoop.security.credential.provider.path=jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks /user/user1/ wasb:<//yourcontainer@youraccount.blob.core.windows.net/>user1
```

## <a name="restrict-azure-storage-data-access-using-sas"></a>Toegang tot Azure Storage gegevens beperken met behulp van SAS

HDInsight heeft standaard volledige toegang tot de gegevens in de Azure Storage accounts die aan het cluster zijn gekoppeld. Shared Access signatures (SAS) op de BLOB-container kunnen worden gebruikt om de toegang tot de gegevens te beperken, zoals gebruikers met alleen-lezen toegang tot de gegevens geven.

### <a name="using-the-sas-token-created-with-python"></a>Het SAS-token gebruiken dat is gemaakt met python

1. Open het  [SASToken.py](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature/blob/master/Python/SASToken.py) -bestand en wijzig de volgende waarden:

    |Token eigenschap|Beschrijving|
    |---|---|
    |policy_name|De naam die moet worden gebruikt om het opgeslagen beleid te maken.|
    |storage_account_name|De naam van uw opslagaccount.|
    |storage_account_key|De sleutel voor het opslag account.|
    |storage_container_name|De container in het opslag account waartoe u de toegang wilt beperken.|
    |example_file_path|Het pad naar een bestand dat naar de container wordt geüpload.|

2. Het SASToken.py-bestand wordt geleverd met de `ContainerPermissions.READ + ContainerPermissions.LIST` machtigingen en kan worden aangepast op basis van de use-case.

3. Voer het script als volgt uit: `python SASToken.py`

4. Hier wordt de SAS-token weer gegeven die vergelijkbaar is met de volgende tekst wanneer het script is voltooid: `sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14`

5. Als u de toegang tot een container met Shared Access Signature wilt beperken, voegt u een aangepaste vermelding toe aan de configuratie van de kern site voor het cluster onder Ambari HDFS configs geavanceerde aangepaste kern-site toevoegen eigenschap.

6. Gebruik de volgende waarden voor de velden **sleutel** en **waarde** :

    **Sleutel**: `fs.azure.sas.YOURCONTAINER.YOURACCOUNT.blob.core.windows.net` **waarde**: de SAS-sleutel die wordt geretourneerd door de python-toepassing uit stap 4 hierboven.

7. Klik op de knop **toevoegen** om deze sleutel en waarde op te slaan en klik vervolgens op de knop **Opslaan** om de configuratie wijzigingen op te slaan. Als u hierom wordt gevraagd, voegt u een beschrijving van de wijziging toe (bijvoorbeeld het toevoegen van SAS-opslag toegang) en klikt u vervolgens op **Opslaan**.

8. Selecteer in de Ambari-webgebruikersinterface de optie HDFS in de lijst aan de linkerkant en selecteer vervolgens **alle betrokken onderdelen opnieuw opstarten** in de vervolg keuzelijst service acties aan de rechter kant. Selecteer **Bevestig opnieuw opstarten** als dit wordt gevraagd.

9. Herhaal dit proces voor MapReduce2 en GARENs.

Er zijn drie belang rijke dingen die u moet weten over het gebruik van SAS-tokens in Azure:

1. Wanneer SAS-tokens worden gemaakt met de machtigingen lezen en lijst, kunnen gebruikers die toegang hebben tot de BLOB-container met die SAS-token geen gegevens schrijven en verwijderen. Gebruikers die toegang hebben tot de BLOB-container met die SAS-token en een schrijf-of verwijder bewerking proberen, ontvangen een bericht zoals `"This request is not authorized to perform this operation"` .

2. Wanneer de SAS-tokens worden gegenereerd met `READ + LIST + WRITE` machtigingen (alleen om te beperken `DELETE` ), opdrachten als `hadoop fs -put` eerste schrijven naar een `\_COPYING\_` bestand en vervolgens proberen de naam van het bestand te wijzigen. Deze HDFS-bewerking is gekoppeld aan een `copy+delete` for WASB. Omdat de `DELETE` machtiging niet is opgegeven, mislukt de "put". De `\_COPYING\_` bewerking is een Hadoop-functie die is bedoeld om een gelijktijdigheids beheer te bieden. Op dit moment is er geen manier om alleen de bewerking ' DELETE ' te beperken zonder dat dit gevolgen heeft voor schrijf bewerkingen.

3. Helaas werken de Hadoop-referentie provider en de provider voor ontsleutelings sleutel (ShellDecryptionKeyProvider) momenteel niet met de SAS-tokens, zodat deze momenteel niet kan worden beveiligd tegen zicht baarheid.

Zie [Azure Storage-hand tekeningen voor gedeelde toegang gebruiken om de toegang tot gegevens in HDInsight te beperken](../hdinsight-storage-sharedaccesssignature-permissions.md)voor meer informatie.

## <a name="use-data-encryption-and-replication"></a>Gegevens versleuteling en-replicatie gebruiken

Alle gegevens die naar Azure Storage worden geschreven, worden automatisch versleuteld met behulp van [Storage service Encryption (SSE)](../../storage/common/storage-service-encryption.md). De gegevens in het Azure Storage-account worden altijd gerepliceerd voor hoge Beschik baarheid. Wanneer u een opslag account maakt, kunt u een van de volgende replicatie opties kiezen:

- [Lokaal redundante opslag (LRS)](../../storage/common/storage-redundancy.md#locally-redundant-storage)
- [Zone-redundante opslag (ZRS)](../../storage/common/storage-redundancy.md#zone-redundant-storage)
- [Geografisch redundante opslag (GRS)](../../storage/common/storage-redundancy.md#geo-redundant-storage)
- [Geografisch redundante opslag met leestoegang (RA-GRS)](../../storage/common/storage-redundancy.md)

Azure Storage biedt lokaal redundante opslag (LRS), maar u moet ook essentiële gegevens kopiëren naar een andere Azure Storage account in een andere regio met een frequentie die is afgestemd op de behoeften van het nood herstel plan. Er zijn verschillende methoden om gegevens te kopiëren, waaronder [ADLCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md), [DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html), [Azure PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md)of [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md). Het wordt ook aanbevolen om toegangs beleid af te dwingen voor Azure Storage account om onbedoelde verwijdering te voor komen.

Raadpleeg voor meer informatie de volgende artikelen:

- [Replicatie Azure Storage](../../storage/common/storage-redundancy.md)
- [Hulp bij nood gevallen voor Azure Data Lake Storage Gen1 (ADLS)](../../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

## <a name="attach-additional-azure-storage-accounts-to-cluster"></a>Extra Azure Storage-accounts toevoegen aan cluster

Tijdens het maken van het HDInsight-proces wordt een Azure Storage account, Azure Data Lake Storage Gen1 of Azure Data Lake Storage Gen2 gekozen als het standaard bestandssysteem. Naast dit standaard opslag account kunnen extra opslag accounts worden toegevoegd uit hetzelfde Azure-abonnement of verschillende Azure-abonnementen tijdens het proces voor het maken van het cluster of nadat een cluster is gemaakt.

U kunt op de volgende manieren een extra opslag account toevoegen:
- Ambari HDFS config geavanceerde aangepaste kern-site de naam en sleutel van het opslag account toevoegen de services opnieuw starten
- [Script actie](../hdinsight-hadoop-add-storage.md) gebruiken door de naam en sleutel van het opslag account door te geven

> [!Note]
> In geldige gebruiks gevallen kunnen de limieten voor de Azure-opslag worden verhoogd via een aanvraag voor [ondersteuning van Azure](https://azure.microsoft.com/support/faq/).

Zie [Extra opslagaccounts toevoegen aan HDInsight](../hdinsight-hadoop-add-storage.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Lees het volgende artikel in deze reeks: [Aanbevolen procedures voor het migreren van gegevens voor on-premises om de migratie te Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-data-migration.md).

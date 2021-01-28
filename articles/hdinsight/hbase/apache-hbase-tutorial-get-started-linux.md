---
title: 'Zelfstudie: Apache HBase gebruiken in Azure HDInsight'
description: Volg deze zelfstudie voor Apache HBase om met Hadoop aan de slag te gaan in HDInsight. Maak tabellen vanuit de HBase-shell en gebruik Hive om query's uit te voeren op de tabellen.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/22/2021
ms.openlocfilehash: 05e40dd38fc7111521b600908cda38084249e4de
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936052"
---
# <a name="tutorial-use-apache-hbase-in-azure-hdinsight"></a>Zelfstudie: Apache HBase gebruiken in Azure HDInsight

In deze zelfstudie ziet u hoe u een Apache HBase-cluster maakt in Azure HDInsight, HBase-tabellen maakt en query’s uitvoert op tabellen met behulp van Apache Hive.  Zie [Overzicht van HDInsight HBase](./apache-hbase-overview.md) voor algemene informatie over HBase.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Apache HBase-cluster maken
> * HBase-tabellen maken en gegevens invoegen
> * Query’s uitvoeren op Apache HBase met Apache Hive
> * HBase REST API's gebruiken met Curl
> * De clusterstatus controleren

## <a name="prerequisites"></a>Vereisten

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Bash. In de voorbeelden in dit artikel wordt gebruikgemaakt van de Bash-shell in Windows 10 voor de cURL-opdrachten. Zie [Installatiehandleiding voor Windows 10 voor Windows-subsysteem voor Linux](/windows/wsl/install-win10) voor installatiestappen.  Andere [UNIX-shells](https://www.gnu.org/software/bash/) kunnen ook worden gebruikt.  De cURL-voorbeelden kunnen, met enige aanpassingen, worden gebruikt in een Windows-opdrachtprompt.  U kunt ook de Windows PowerShell-cmdlet [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) gebruiken.

## <a name="create-apache-hbase-cluster"></a>Een Apache HBase-cluster maken

Voor de volgende procedure wordt een Azure Resource Manager-sjabloon gebruikt om een HBase-cluster te maken. Met de sjabloon wordt ook het afhankelijke standaard Azure Storage-account gemaakt. Zie [Op Linux gebaseerde Hadoop-clusters maken in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) voor meer inzicht in de parameters die voor deze procedure worden gebruikt en andere methoden voor het maken van clusters.

1. Selecteer de volgende afbeelding om de sjabloon in Azure Portal te openen. De sjabloon bevindt zich in [Azure-quickstart-sjablonen](https://azure.microsoft.com/resources/templates/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Voer in het dialoogvenster **Aangepaste implementatie** de volgende waarden in:

    |Eigenschap |Beschrijving |
    |---|---|
    |Abonnement|Selecteer het Azure-abonnement dat wordt gebruikt om het cluster te maken.|
    |Resourcegroep|Maak een Azure-resourcebeheergroep of gebruik een bestaande.|
    |Locatie|Geef de locatie van de resourcegroep op. |
    |Clusternaam|Voer een naam in voor het HBase-cluster.|
    |Gebruikersnaam en wachtwoord voor aanmelding bij cluster|De standaardaanmeldingsnaam is **admin**.|
    |SSH-gebruikersnaam en SSH-wachtwoord|De standaardgebruikersnaam is **sshuser**.|

    Andere parameters zijn optioneel.  

    Elk cluster is afhankelijk van een Azure Storage-account. Nadat u een cluster hebt verwijderd, blijven de gegevens in het opslagaccount. De naam van het standaardopslagaccount voor het cluster is de naam waaraan 'store' is toegevoegd. Deze is vastgelegd in de sectie met sjabloonvariabelen.

3. Selecteer **Ik ga akkoord met de bovenstaande voorwaarden** en selecteer vervolgens **Kopen**. Het duurt ongeveer 20 minuten om een cluster te maken.

Nadat een HBase-cluster is verwijderd, kunt u een ander HBase-cluster maken met de dezelfde standaard blob-container. Het nieuwe cluster haalt de HBase-tabellen op die u hebt gemaakt in het oorspronkelijke cluster. Om inconsistenties te voorkomen, wordt u aangeraden de HBase-tabellen uit te schakelen voordat u het cluster verwijdert.

## <a name="create-tables-and-insert-data"></a>Tabellen maken en gegevens invoegen

U kunt SSH gebruiken om verbinding te maken met HBase-clusters en vervolgens [Apache HBase Shell](https://hbase.apache.org/0.94/book/shell.html) gebruiken om HBase-tabellen te maken, gegevens in te voegen, en gegevens te doorzoeken.

Voor de meeste mensen worden de gegevens weergegeven in een tabelindeling:

![Tabelgegevens in HDInsight Apache HBase](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png)

In HBase (een implementatie van [Cloud BigTable](https://cloud.google.com/bigtable/)) zien dezelfde gegevens er als volgt uit:

![BigTable-gegevens in HDInsight Apache HBase](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png)

**De HBase-shell gebruiken**

1. Gebruik de opdracht `ssh` om verbinding te maken met uw HBase-cluster. Bewerk de onderstaande opdracht door `CLUSTERNAME` te vervangen door de naam van uw cluster.Voer vervolgens deze opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Gebruik de opdracht `hbase shell` om de interactieve HBase-shell te starten. Voer de volgende opdracht in uw SSH-verbinding in:

    ```bash
    hbase shell
    ```

1. Gebruik de opdracht `create` om een HBase-tabel met twee kolomfamilies te maken. De tabel- en kolomnamen zijn hoofdlettergevoelig. Voer de volgende opdracht in:

    ```hbaseshell
    create 'Contacts', 'Personal', 'Office'
    ```

1. Gebruik de opdracht `list` om alle tabellen in HBase weer te geven. Voer de volgende opdracht in:

    ```hbase
    list
    ```

1. Gebruik de opdracht `put` om in een opgegeven rij in een bepaalde tabel waarden in te voegen voor een opgegeven kolom. Voer de volgende opdrachten in:

    ```hbaseshell
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

1. Gebruik de opdracht `scan` om de `Contacts`-tabelgegevens te scannen en te retourneren. Voer de volgende opdracht in:

    ```hbase
    scan 'Contacts'
    ```

    ![HDInsight Apache Hadoop HBase-shell](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png)

1. Gebruik de opdracht `get` om de inhoud van een rij op te halen. Voer de volgende opdracht in:

    ```hbaseshell
    get 'Contacts', '1000'
    ```

    U ziet soortgelijke resultaten als bij de opdracht `scan`, omdat er maar één rij is.

    Zie [Inleiding tot het Apache HBase-schemaontwerp](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf) voor meer informatie over het HBase-tabelschema. Raadpleeg de [Snelzoekgids voor Apache HBase](https://hbase.apache.org/book.html#quickstart) voor meer HBase-opdrachten.

1. Gebruik de opdracht `exit` om de interactieve HBase-shell te stoppen. Voer de volgende opdracht in:

    ```hbaseshell
    exit
    ```

**Gegevens bulksgewijs laden in de HBase-tabel met contacten**

U kunt in HBase verschillende methoden gebruiken om gegevens in tabellen te laden.  Zie [Bulk loading](https://hbase.apache.org/book.html#arch.bulk.load) (Bulkgsgewijs laden) voor meer informatie.

Een voorbeeld van een gegevensbestand is te vinden in een openbare Blob-container, `wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt`.  De inhoud van het gegevensbestand is:

`8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.`

`16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz`

`4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta`

`16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.`

`3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive`

`3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle`

`10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street`

`4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street`

`4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.`

`16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive`

U kunt een tekstbestand maken en het bestand desgewenst uploaden naar uw eigen opslagaccount. Zie [Gegevens voor Apache Hadoop-taken in HDInsight uploaden](../hdinsight-upload-data.md) voor instructies.

In deze procedure wordt gebruikgemaakt van de HBase-tabel `Contacts` die u in de laatste procedure hebt gemaakt.

1. Voer vanuit uw open SSH-verbinding de volgende opdracht uit om het gegevensbestand te transformeren naar StoreFiles en op te slaan naar een relatief pad dat is opgegeven door `Dimporttsv.bulk.output`.

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Voer de volgende opdracht uit om de gegevens uit `/example/data/storeDataFileOutput` naar de HBase-tabel te uploaden:

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. U kunt de HBase-shell openen en de opdracht `scan` gebruiken om de tabelinhoud weer te geven.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Query’s uitvoeren op Apache HBase met Apache Hive

Met [Apache Hive](https://hive.apache.org/) kunt u een query uitvoeren op de gegevens in HBase-tabellen. In dit gedeelte maakt u een Hive-tabel die is toegewezen aan de HBase-tabel en deze gebruikt om een query voor de gegevens in uw HBase-tabel uit te voeren.

1. Gebruik vanuit uw open SSH-verbinding de volgende opdracht om Beeline te starten:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Zie voor meer informatie over Beeline [Hive gebruiken met Hadoop in HDInsight met Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).

1. Voer het volgende [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)-script uit om een Hive-tabel te maken die is toegewezen aan de HBase-tabel. Zorg ervoor dat u met de HBase-shell de voorbeeldtabel hebt gemaakt waarnaar eerder in dit artikel is verwezen voordat u deze instructie uitvoert.

    ```hiveql
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

1. Voer het volgende HiveQL-script uit om een query uit te voeren voor de gegevens in de HBase-tabel:

    ```hiveql
    SELECT count(rowkey) AS rk_count FROM hbasecontacts;
    ```

1. Gebruik `!exit` om Beeline af te sluiten.

1. Gebruik `exit` om uw SSH-verbinding af te sluiten.

### <a name="separate-hive-and-hbase-clusters"></a>Afzonderlijke Hive- en Hbase-clusters

De Hive-query voor toegang tot HBase-gegevens hoeft niet te worden uitgevoerd vanuit het HBase-cluster. Elk cluster dat deel uitmaakt van Hive (inclusief Spark, Hadoop, HBase of Interactive Query) kan worden gebruikt voor het opvragen van HBase-gegevens, mits de volgende stappen worden uitgevoerd:

1. Beide clusters moeten aan hetzelfde virtuele netwerk en subnet zijn gekoppeld
2. `/usr/hdp/$(hdp-select --version)/hbase/conf/hbase-site.xml` kopiëren van de HBase-clusterhoofdknooppunten naar de Hive-clusterhoofdknooppunten

### <a name="secure-clusters"></a>Beveiligde clusters

HBase-gegevens kunnen ook worden opgevraagd uit Hive met behulp van HBase met ESP: 

1. Bij het volgen van een patroon met meerdere clusters moet op beide clusters ESP zijn ingeschakeld. 
2. Als u wilt dat Hive query's kan uitvoeren op HBase-gegevens, moet u ervoor zorgen dat de `hive`-gebruiker machtigingen krijgt voor toegang tot de HBase-gegevens via de Hbase Apache Ranger-invoeg toepassing
3. Bij het gebruik van afzonderlijke ESP-clusters moet de inhoud van `/etc/hosts` van de HBase-cluster hoofdknooppunten worden toegevoegd aan `/etc/hosts` van de Hive-clusterhoofd knooppunten. 
> [!NOTE]
> Nadat u clusters hebt geschaald, moet `/etc/hosts` opnieuw worden toegevoegd

## <a name="use-hbase-rest-apis-using-curl"></a>HBase REST API's gebruiken met Curl

De REST API is beveiligd via [basisverificatie](https://en.wikipedia.org/wiki/Basic_access_authentication). U moet aanvragen altijd uitvoeren via een beveiligde HTTP-verbinding (HTTPS). Zo zorgt u ervoor dat uw referenties veilig worden verzonden naar de server.

1. Voeg het volgende aangepaste opstartscript tot aan de sectie **Script Action** om HBase REST-API’s in te schakelen in het HDInsight-cluster. U kunt het opstartscript toevoegen tijdens het maken van het cluster of nadat het cluster is gemaakt. Selecteer **Regioservers** bij **Knooppunttype** om ervoor te zorgen dat het script alleen op HBase-regioservers wordt uitgevoerd.


    ```bash
    #! /bin/bash

    THIS_MACHINE=`hostname`

    if [[ $THIS_MACHINE != wn* ]]
    then
        printf 'Script to be executed only on worker nodes'
        exit 0
    fi

    RESULT=`pgrep -f RESTServer`
    if [[ -z $RESULT ]]
    then
        echo "Applying mitigation; starting REST Server"
        sudo python /usr/lib/python2.7/dist-packages/hdinsight_hbrest/HbaseRestAgent.py
    else
        echo "Rest server already running"
        exit 0
    fi
    ```

1. Stel voor het gemak de omgevingsvariabele in. Bewerk de onderstaande opdrachten door `MYPASSWORD` te vervangen door het wachtwoord voor aanmelding bij het cluster. Vervang `MYCLUSTERNAME` door de naam van uw HBase-cluster. Voer daarna de opdrachten in.

    ```bash
    export password='MYPASSWORD'
    export clustername=MYCLUSTERNAME
    ```

1. Gebruik de volgende opdracht om een lijst met bestaande HBase-tabellen weer te geven:

    ```bash
    curl -u admin:$password \
    -G https://$clustername.azurehdinsight.net/hbaserest/
    ```

1. Gebruik de volgende opdracht om een nieuwe HBase-tabel met twee kolomfamilies te maken:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    Het schema wordt opgegeven in de JSON-indeling.
1. Gebruik de volgende opdracht om enkele gegevens in te voegen:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
    -v
    ```

    Codeer de waarden die in de schakeloptie -d zijn opgegeven met Base64. In het voorbeeld:

   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ==: Persoonlijk: Naam
   * Sm9obiBEb2xl: John Dole

     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) maakt het mogelijk om meerdere waarden (in batch) in te voegen.

1. Gebruik de volgende opdracht om een rij te verkrijgen:

    ```bash
    curl -u admin:$password \
    GET "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/1000" \
    -H "Accept: application/json" \
    -v
    ```

Zie [Apache HBase Reference Guide](https://hbase.apache.org/book.html#_rest) (Snelzoekgids voor Apache HBase) voor meer informatie over HBase REST.

> [!NOTE]  
> Thrift wordt niet ondersteund door HBase in HDInsight.
>
> Wanneer u Curl of een andere REST-communicatie gebruikt met WebHCat, moet u de aanvragen verifiëren door de gebruikersnaam en het wachtwoord voor de beheerder van het HDInsight-cluster op te geven. U moet ook de clusternaam gebruiken als onderdeel van de URI (Uniform Resource Identifier) die wordt gebruikt om de aanvragen naar de server te verzenden:
>
> `curl -u <UserName>:<Password> \`
>
> `-G https://<ClusterName>.azurehdinsight.net/templeton/v1/status`
>
> Het antwoord dat u ontvangt, is vergelijkbaar met het volgende antwoord:
>
> `{"status":"ok","version":"v1"}`

## <a name="check-cluster-status"></a>De clusterstatus controleren

HBase in HDInsight wordt geleverd met een webgebruikersinterface voor het bewaken van clusters. Met de webgebruikersinterface kunt u statistieken of informatie over regio's aanvragen.

**De HBase-hoofdinterface openen**

1. Meld u aan bij de Ambari-webgebruikersinterface op `https://CLUSTERNAME.azurehdinsight.net`, waarbij `CLUSTERNAME` de naam van uw HBase-cluster is.

1. Selecteer **HBase** in het menu links.

1. Selecteer **Snelkoppelingen** boven aan de pagina, wijs de actieve Zookeeper-knooppuntkoppeling aan en selecteer vervolgens **HBase Master UI**.  De interface wordt in een nieuw browsertabblad geopend:

   ![HDInsight Apache HBase HMaster-interface](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   De HBase-hoofdinterface bevat de volgende onderdelen:

   - regioservers
   - back-upmasters
   - tabellen
   - taken
   - softwarekenmerken

## <a name="cluster-recreation"></a>Cluster recreatie

Nadat een HBase-cluster is verwijderd, kunt u een ander HBase-cluster maken met de dezelfde standaard blob-container. Het nieuwe cluster haalt de HBase-tabellen op die u hebt gemaakt in het oorspronkelijke cluster. Om inconsistenties te voor komen, wordt u echter aangeraden de HBase-tabellen uit te scha kelen voordat u het cluster verwijdert. 

U kunt de HBase-opdracht `disable 'Contacts'` gebruiken. 

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet meer gebruikt, verwijdert u het HBase-cluster dat u hebt gemaakt, via de volgende stappen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Typ **HDInsight** in het **Zoekvak** bovenaan.
1. Selecteer onder **Services** de optie **HDInsight-clusters**.
1. Klik in de lijst met HDInsight-clusters die wordt weergegeven, op de **...** naast het cluster dat u voor deze zelfstudie hebt gemaakt.
1. Klik op **Verwijderen**. Klik op **Ja**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een Apache HBase-cluster maakt. En hoe u tabellen maakt en gegevens in deze tabellen weergeeft vanuit de HBase-shell. U hebt ook geleerd hoe u een Hive-query op gegevens in HBase-tabellen uitvoert. En hoe u de HBase C# REST API's gebruikt om een HBase-tabel te maken en gegevens op te halen uit de tabel. Voor meer informatie zie:

> [!div class="nextstepaction"]
> [Overzicht van HDInsight HBase](./apache-hbase-overview.md)

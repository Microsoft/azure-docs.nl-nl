---
title: Apache Hive beleid in Apache zwerver-Azure HDInsight
description: Meer informatie over het configureren van Apache zwerver-beleids regels voor Hive in een Azure HDInsight-service met Enterprise Security Package.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: fbe4361033321f2ba81478c41a138937f70b5498
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101705701"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>Apache Hive-beleidsregels configureren in HDInsight met Enterprise Security Package

Meer informatie over het configureren van Apache zwerver-beleids regels voor Apache Hive. In dit artikel maakt u twee Ranger-beleidsregels om toegang tot de hivesampletable te beperken. De hivesampletable wordt geleverd met HDInsight-clusters. Nadat u het beleid hebt geconfigureerd, gebruikt u Excel en het ODBC-stuur programma om verbinding te maken met hive-tabellen in HDInsight.

## <a name="prerequisites"></a>Vereisten

* Een HDInsight-cluster met Enterprise Security Package. Zie [HDInsight-clusters configureren met ESP](./apache-domain-joined-configure-using-azure-adds.md).
* Een werk station met Microsoft 365-apps voor Enter prise, Office 2016, Office 2013 Professional Plus, Excel 2013 standalone of Office 2010 Professional Plus.

## <a name="connect-to-apache-ranger-admin-ui"></a>Verbinding maken met de beheerinterface van Apache Ranger
**Verbinding maken met de beheerinterface van Ranger**

1. Ga vanuit een browser naar de gebruikers interface van zwerver, `https://CLUSTERNAME.azurehdinsight.net/Ranger/` waarbij clustername de naam van uw cluster is.

   > [!NOTE]  
   > Zwerver gebruikt andere referenties dan Apache Hadoop cluster. Als u browsers wilt voor komen dat Hadoop-referenties in de cache worden gebruikt, gebruikt u het nieuwe InPrivate-browser venster om verbinding te maken met de gebruikers interface van zwerver.

2. Meld u aan met de gebruikersnaam en het wachtwoord van het clusterbeheerdomein:

    ![Start pagina van HDInsight ESP zwerver](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    Op dit moment werkt Ranger alleen met Yarn en Hive.

## <a name="create-domain-users"></a>Domeingebruikers maken

Zie [een HDInsight-cluster met ESP maken](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)voor informatie over het maken van hiveruser1 en hiveuser2. U gebruikt de twee gebruikers accounts in dit artikel.

## <a name="create-ranger-policies"></a>Ranger-beleidsregels maken

In deze sectie maakt u twee zwerver-beleids regels voor toegang tot hivesampletable. U geeft de machtiging SELECT op voor verschillende sets kolommen. Beide gebruikers zijn gemaakt met behulp [van een HDInsight-cluster met ESP maken](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp). In de volgende sectie test u de twee beleids regels in Excel.

**Ranger-beleidsregels maken**

1. Open de beheerinterface van Ranger. Zie Verbinding maken met de beheerinterface van Apache Ranger.
2. Selecteer **CLUSTERNAME_Hive** onder **Hive**. Er worden twee vooraf geconfigureerde beleidsregels weergegeven.
3. Selecteer **Nieuw beleid toevoegen** en voer de volgende waarden in:

    |Eigenschap |Waarde |
    |---|---|
    |Policy Name|lezen-hivesampletable-alle|
    |Hive-data base|standaardinstelling|
    |tabel|hivesampletable|
    |Hive-kolom|*|
    |Select User|hiveuser1|
    |Machtigingen|Selecteer|

    ![HDInsight ESP zwerver Hive-beleid configureren](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

    > [!NOTE]  
    > Als een domeingebruiker niet is ingevuld in Gebruiker selecteren, wacht u even, zodat Ranger met AAD kan synchroniseren.

4. Selecteer **Toevoegen** om het beleid op te slaan.

5. Herhaal de laatste twee stappen, zodat u een ander beleid kunt maken met de volgende eigenschappen:

    |Eigenschap |Waarde |
    |---|---|
    |Policy Name|lezen-hivesampletable-devicemake|
    |Hive-data base|standaardinstelling|
    |tabel|hivesampletable|
    |Hive-kolom|ClientID, devicemake|
    |Select User|hiveuser2|
    |Machtigingen|Selecteer|

## <a name="create-hive-odbc-data-source"></a>Hive ODBC-gegevensbron maken

De instructies vindt u in [Hive ODBC-gegevensbron maken](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).  

 | Eigenschap  |Beschrijving |
 | --- | --- |
 | Naam van de gegevensbron | Geef uw gegevensbron een naam |
 | Host | Voer CLUSTERNAME.azurehdinsight.net in. Bijvoorbeeld: myHDICluster.azurehdinsight.net |
 | Poort | Gebruik **443**. (Deze poort is gewijzigd van 563 in 443.) |
 | Database | **Standaard instelling** gebruiken. |
 | Type Hive-server | Selecteer **Hive Server 2** |
 | Mechanisme | Selecteer **Azure HDInsight Service** |
 | HTTP-pad | Laat dit leeg. |
 | Gebruikersnaam | Voer hiveuser1@contoso158.onmicrosoft.com in. Werk de domein naam bij als deze niet overeenkomt. |
 | Wachtwoord | Voer het wachtwoord van hiveuser1 in. |

Zorg ervoor dat u op **Test** klikt voordat u de gegevensbron opslaat.

## <a name="import-data-into-excel-from-hdinsight"></a>Gegevens in Excel importeren vanuit HDInsight

In de laatste sectie hebt u twee beleids regels geconfigureerd.  hiveuser1 heeft de machtiging SELECT voor alle kolommen en hiveuser2 heeft de machtiging SELECT voor twee kolommen. In deze sectie imiteert u de twee gebruikers, zodat u gegevens kunt importeren in Excel.

1. Open een nieuwe of bestaande werkmap in Excel.

1. Ga op het tabblad **gegevens** naar **gegevens ophalen**  >  **uit andere bronnen**  >  **van ODBC** om het venster **van ODBC** te starten.

    ![Wizard gegevens verbinding openen](./media/apache-domain-joined-run-hive/simbahiveodbc-excel-dataconnection1.png)

1. Selecteer in de vervolg keuzelijst de naam van de gegevens bron die u in de laatste sectie hebt gemaakt en selecteer vervolgens **OK**.

1. Voor het eerste gebruik wordt het dialoog venster **ODBC-stuur programma** geopend. Selecteer **Windows** in het menu links. Selecteer vervolgens **verbinding maken** om het **Navigator** venster te openen.

1. Wacht totdat het dialoogvenster **Database en tabel selecteren** wordt geopend. Dit kan een paar seconden duren.

1. Selecteer **hivesampletable** en selecteer **volgende**.

1. Selecteer **Finish**.

1. In het dialoogvenster **Gegevens importeren** kunt u de query wijzigen of opgeven. Als u dit wilt doen, selecteert u **Eigenschappen**. Dit kan een paar seconden duren.

1. Selecteer het tabblad **definitie** . De opdracht tekst is:

    ```sql
    SELECT * FROM "HIVE"."default"."hivesampletable"`
    ```

   Bij de Ranger-beleidsregels hebt u gedefinieerd dat hiveuser1 de machtiging SELECT heeft voor alle kolommen.  Deze query werkt dus met hiveuser1-referenties, maar deze query werkt niet met hiveuser2-referenties.

1. Selecteer **OK** om het dialoog venster verbindings eigenschappen te sluiten.

1. Selecteer **OK** om het dialoog venster **gegevens importeren** te sluiten.  

1. Voer het wachtwoord van hiveuser1 opnieuw in en klik op **OK**. Het duurt een paar seconden voordat de gegevens naar Excel worden geïmporteerd. Wanneer u klaar bent, ziet u elf kolommen met gegevens.

Als u het tweede beleid wilt testen (Read-hivesampletable-devicemake), hebt u in de laatste sectie gemaakt

1. Voeg een nieuw blad in Excel toe.
2. Voer de vorige procedure uit om de gegevens te importeren.  De enige wijziging die u aanbrengt, is het gebruik van hiveuser2-referenties in plaats van hiveuser1. Dit mislukt omdat hiveuser2 alleen machtigingen heeft om twee kolommen weer te geven. De volgende fout wordt weergegeven:

    ```output
    [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
    ```

3. Voer dezelfde procedure uit om gegevens te importeren. Gebruik deze keer de referenties van hiveuser2 en wijzig ook de SELECT-instructie van:

    ```sql
    SELECT * FROM "HIVE"."default"."hivesampletable"
    ```

    in:

    ```sql
    SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"
    ```

    Wanneer u klaar bent, ziet u twee kolommen met geïmporteerde gegevens.

## <a name="next-steps"></a>Volgende stappen

* Zie [hdinsight-clusters met ESP configureren](./apache-domain-joined-configure-using-azure-adds.md)voor meer informatie over het configureren van een hdinsight-cluster met Enterprise Security Package.
* Zie [hdinsight-clusters beheren met ESP](apache-domain-joined-manage.md)voor meer informatie over het beheren van een hdinsight-cluster met ESP.
* Zie [SSH gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight)voor het uitvoeren van Hive-QUERY'S met SSH op HDInsight-clusters met ESP.
* Zie [verbinding maken met Apache Hive in azure HDInsight met behulp van het Hive JDBC-stuur programma voor het](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md) verbinden van de component met hive JDBC.
* Zie [Excel verbinden met Apache Hadoop met het micro soft Hive ODBC-station voor het](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md) verbinden van Excel met Hadoop met behulp van Hive ODBC.
* Zie [Excel verbinden met Apache Hadoop met behulp van Power query](../hadoop/apache-hadoop-connect-excel-power-query.md) om Excel te verbinden met Hadoop met behulp van Power query
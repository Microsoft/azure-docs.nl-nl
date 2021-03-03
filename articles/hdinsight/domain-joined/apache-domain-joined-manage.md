---
title: Enterprise Security Package clusters beheren-Azure HDInsight
description: Meer informatie over het beheren van Azure HDInsight-clusters met Enterprise Security Package.
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/04/2019
ms.openlocfilehash: b0f8ba4adfa4b08c23d3f69fa4e2b01f4580bb19
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742208"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>HDInsight-clusters beheren met Enterprise Security Package

Meer informatie over de gebruikers en de rollen in HDInsight Enterprise Security Package (ESP) en hoe u ESP-clusters beheert.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>Gebruik VSCode om een koppeling te maken met een cluster dat aan een domein is toegevoegd

U kunt een normaal cluster koppelen met behulp van Apache Ambari Managed username, ook een beveiligings Apache Hadoop cluster koppelen met behulp van domein gebruikers naam (zoals: `user1@contoso.com` ).

1. Open [Visual Studio code](https://code.visualstudio.com/). Zorg ervoor dat de extensie van de [Spark-& Hive-Hulpprogram ma's](../hdinsight-for-vscode.md) is geïnstalleerd.

1. Volg de stappen voor het [koppelen van een cluster](../hdinsight-for-vscode.md#link-a-cluster) voor Visual Studio code.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>Gebruik IntelliJ om een koppeling te maken met een cluster dat aan een domein is toegevoegd

U kunt een normaal cluster koppelen met behulp van Ambari Managed username, ook een beveiligings-Hadoop-cluster koppelen met behulp van domein gebruikers naam (zoals: `user1@contoso.com` ).

1. Open IntelliJ IDEA. Zorg ervoor dat aan alle [vereisten](../spark/apache-spark-intellij-tool-plugin.md#prerequisites) wordt voldaan.

1. Volg de stappen in [een cluster koppelen](../spark/apache-spark-intellij-tool-plugin.md#link-a-cluster) voor IntelliJ.

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Gebruik Eclipse om een koppeling te maken met een cluster dat aan een domein is toegevoegd

U kunt een normaal cluster koppelen met behulp van Ambari Managed username, ook een beveiligings-Hadoop-cluster koppelen met behulp van domein gebruikers naam (zoals: `user1@contoso.com` ).

1. Open Eclipse. Zorg ervoor dat aan alle [vereisten](../spark/apache-spark-eclipse-tool-plugin.md#prerequisites) wordt voldaan.

1. Volg de stappen van [een cluster koppelen](../spark/apache-spark-eclipse-tool-plugin.md#link-a-cluster) voor eclips.

## <a name="access-the-clusters-with-enterprise-security-package"></a>Toegang tot de clusters met Enterprise Security Package

Enterprise Security Package (voorheen bekend als HDInsight Premium) biedt toegang van meerdere gebruikers tot het cluster, waarbij de verificatie wordt uitgevoerd door Active Directory en autorisatie door Apache zwerver en opslag-Acl's (ADLS Acl's). Autorisatie biedt een veilige grenzen tussen meerdere gebruikers en geeft alleen bevoegde gebruikers toegang tot de gegevens op basis van het autorisatie beleid.

Beveiliging en gebruikers isolatie zijn belang rijk voor een HDInsight-cluster met Enterprise Security Package. Om aan deze vereisten te voldoen, wordt SSH-toegang tot het cluster met Enterprise Security Package ondersteund voor de lokale gebruiker die is geselecteerd op het moment dat het cluster wordt gemaakt, evenals de gebruikers die beschikbaar zijn in AAD-DS (d.w.z. Kerberos). De volgende tabel bevat de aanbevolen toegangs methoden voor elk cluster type:

|Workload|Scenario|Toegangs methode|
|--------|--------|-------------|
|Apache Hadoop|Hive-interactieve taken/Query's    |<ul><li>[Beeline](#beeline)</li><li>[Hive-weergave](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC-Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio-Hulpprogram Ma's](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Interactieve taken/Query's, PySpark Interactive|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin met livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Hive-weergave](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC-Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio-Hulpprogram Ma's](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|Batch-Scenario's – Spark-verzen ding, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Interactieve query (LLAP)|Interactief|<ul><li>[Beeline](#beeline)</li><li>[Hive-weergave](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC-Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio-Hulpprogram Ma's](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Alle|Aangepaste toepassing installeren|<ul><li>[Scriptacties](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > Jupyter is niet geïnstalleerd/ondersteund in Enterprise Security Package.

Het gebruik van de standaard-Api's helpt bij het beveiligen van de beveiliging. U krijgt ook de volgende voor delen:

- **Beheer** : u kunt uw code beheren en taken automatiseren met standaard-Api's – LIVY, HS2 enzovoort.
- **Audit** : met SSH is er geen manier om te controleren of de gebruikers SSHen naar het cluster. Dit is niet het geval wanneer taken zijn gemaakt via standaard-eind punten, aangezien ze worden uitgevoerd in de context van de gebruiker.

### <a name="use-beeline"></a><a name="beeline"></a>Beeline gebruiken

Beeline op uw computer installeren en verbinding maken via het open bare Internet, gebruik de volgende para meters:

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Als Beeline lokaal is geïnstalleerd en verbinding maakt via een Azure Virtual Network, gebruikt u de volgende para meters:

```
Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Als u wilt zoeken naar de Fully Qualified Domain Name van een hoofd knooppunt, gebruikt u de informatie in het REST API-document HDInsight beheren met behulp van de gegevens in de Ambari.

## <a name="users-of-hdinsight-clusters-with-esp"></a>Gebruikers van HDInsight-clusters met ESP

Een niet-ESP HDInsight-cluster heeft twee gebruikers accounts die worden gemaakt tijdens het maken van het cluster:

- **Ambari beheerder**: dit account wordt ook een *Hadoop-gebruiker* of *http-gebruiker* genoemd. Dit account kan worden gebruikt om u aan te melden bij Ambari op `https://CLUSTERNAME.azurehdinsight.net` . Het kan ook worden gebruikt om query's uit te voeren op Ambari-weer gaven, taken uit te voeren via externe hulpprogram ma's (bijvoorbeeld Power shell, Templeton, Visual Studio) en te verifiëren met het Hive ODBC-stuur programma en BI-hulpprogram ma's (bijvoorbeeld Excel, Power BI of tableau).

Een HDInsight-cluster met ESP heeft naast Ambari-beheerder drie nieuwe gebruikers.

- **Zwerver-beheerder**: dit account is het lokale Apache zwerver-beheerders account. Het is geen Active Directory-domein gebruiker. Dit account kan worden gebruikt voor het instellen van beleid en het maken van andere gebruikers beheerders of gedelegeerde beheerders (zodat deze gebruikers beleid kunnen beheren). De gebruikers naam is standaard *beheerder* en het wacht woord is hetzelfde als het Ambari beheerders wachtwoord. Het wacht woord kan worden bijgewerkt vanaf de pagina instellingen in zwerver.

- **Domein gebruiker cluster beheerder**: dit account is een Active Directory-domein gebruiker die is aangewezen als Hadoop-Cluster beheer, waaronder Ambari en zwerver. U moet de referenties van deze gebruiker opgeven tijdens het maken van het cluster. Deze gebruiker heeft de volgende bevoegdheden:
    - Voeg computers toe aan het domein en plaats deze binnen de OE die u opgeeft tijdens het maken van het cluster.
    - Service-principals maken binnen de organisatie-eenheid die u opgeeft tijdens het maken van het cluster.
    - Omgekeerde DNS-vermeldingen maken.

    Houd er rekening mee dat de andere AD-gebruikers ook over deze bevoegdheden beschikken.

    Er zijn een aantal eind punten in het cluster (bijvoorbeeld Templeton) die niet worden beheerd door zwerver en daarom niet zijn beveiligd. Deze eind punten zijn voor alle gebruikers vergrendeld, met uitzonde ring van de domein gebruiker cluster beheerder.

- **Normaal**: tijdens het maken van een cluster kunt u meerdere Active Directory-groepen opgeven. De gebruikers in deze groepen worden gesynchroniseerd met zwerver en Ambari. Deze gebruikers zijn domein gebruikers en hebben toegang tot alleen zwerver-beheerde eind punten (bijvoorbeeld Hiveserver2). Alle RBAC-beleids regels en-controles zijn van toepassing op deze gebruikers.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>Rollen van HDInsight-clusters met ESP

HDInsight Enterprise Security Package heeft de volgende rollen:

- Cluster beheerder
- Cluster operator
- Servicebeheerder
- Service operator
- Cluster gebruiker

**De machtigingen van deze rollen weer geven**

1. Open de gebruikers interface van Ambari-beheer.  Zie [de gebruikers interface van Ambari Management openen](#open-the-ambari-management-ui).
2. Selecteer in het menu links de optie **rollen**.
3. Selecteer het blauwe vraag teken om de machtigingen te bekijken:

    ![Machtigingen voor ESP HDInsight-rollen](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>De gebruikers interface van Ambari Management openen

1. Navigeer naar `https://CLUSTERNAME.azurehdinsight.net/` de naam van het cluster.
1. Meld u aan bij Ambari met behulp van de domein gebruikers naam en het wacht woord van de Cluster beheerder.
1. Selecteer de vervolg keuzelijst **beheerder** in de rechter bovenhoek en selecteer vervolgens **Ambari beheren**.

    ![ESP HDInsight Manage Apache Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    De gebruikers interface ziet er als volgt uit:

    ![Gebruikers interface voor ESP HDInsight Apache Ambari Management](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>De domein gebruikers weer geven die zijn gesynchroniseerd vanuit uw Active Directory

1. Open de gebruikers interface van Ambari-beheer.  Zie [de gebruikers interface van Ambari Management openen](#open-the-ambari-management-ui).
2. Selecteer in het menu links de optie **gebruikers**. U ziet alle gebruikers die zijn gesynchroniseerd van uw Active Directory naar het HDInsight-cluster.

    ![Gebruikers interface lijst van ESP HDInsight Ambari-beheer](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>De domein groepen weer geven die zijn gesynchroniseerd vanuit uw Active Directory

1. Open de gebruikers interface van Ambari-beheer.  Zie [de gebruikers interface van Ambari Management openen](#open-the-ambari-management-ui).
2. Selecteer in het menu links **groepen**. U ziet alle groepen die zijn gesynchroniseerd van uw Active Directory naar het HDInsight-cluster.

    ![Lijst groepen van de gebruikers interface van ESP HDInsight Ambari Management](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Machtigingen voor Hive-weer gaven configureren

1. Open de gebruikers interface van Ambari-beheer.  Zie [de gebruikers interface van Ambari Management openen](#open-the-ambari-management-ui).
2. Selecteer in het menu links de optie **weer gaven**.
3. Selecteer **Hive** om de details weer te geven.

    ![UI-Hive-weer gaven ESP HDInsight Ambari Management](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)

4. Selecteer de koppeling van de **Hive-weer gave** om Hive-weer gaven te configureren.
5. Schuif omlaag naar de sectie **machtigingen** .

    ![Gebruikers interface van ESP HDInsight Ambari Management-Hive machtigingen configureren](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)

6. Selecteer **gebruiker toevoegen** of **groep toevoegen** en geef vervolgens de gebruikers of groepen op die Hive-weer gaven kunnen gebruiken.

## <a name="configure-users-for-the-roles"></a>Gebruikers configureren voor de rollen

 Zie rollen van HDInsight-clusters met ESP voor een overzicht van de rollen en de bijbehorende machtigingen.

1. Open de gebruikers interface van Ambari-beheer.  Zie [de gebruikers interface van Ambari Management openen](#open-the-ambari-management-ui).
2. Selecteer in het menu links de optie **rollen**.
3. Selecteer **gebruiker toevoegen** of **groep toevoegen** om gebruikers en groepen toe te wijzen aan verschillende rollen.

## <a name="next-steps"></a>Volgende stappen

- Zie [hdinsight-clusters met ESP configureren](./apache-domain-joined-configure-using-azure-adds.md)voor meer informatie over het configureren van een hdinsight-cluster met Enterprise Security Package.
- Zie [configure Apache Hive policies for HDInsight clusters with ESP](apache-domain-joined-run-hive.md)(Engelstalig) voor het configureren van Hive-beleid en het uitvoeren van Hive-query's.
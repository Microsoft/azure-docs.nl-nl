---
title: 'Zelfstudie: Apache Kafka & Enterprise Security - Azure HDInsight'
description: 'Zelfstudie: leer hoe u Apache Ranger-beleidsregels voor Kafka configureert in Azure HDInsight met Enterprise Security Package.'
ms.service: hdinsight
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: bab3df857dfdac3ca3b9193bda1caea0040a4cbb
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866978"
---
# <a name="tutorial-configure-apache-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Zelfstudie: Apache Kafka-beleidsregels configureren in HDInsight met Enterprise Security Package (preview)

Leer hoe u Apache Ranger-beleidsregels configureert voor Apache Kafka-clusters met Enterprise Security Package (ESP). ESP-clusters worden verbonden met een domein zodat gebruikers zich kunnen verifiëren met domeinreferenties. In deze zelfstudie maakt u twee Ranger-beleidsregels om de toegang tot de onderwerpen `sales` en `marketingspend` te beperken.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Domeingebruikers maken
> * Ranger-beleidsregels maken
> * Onderwerpen in een Kafka-cluster maken
> * Ranger-beleidsregels testen

## <a name="prerequisite"></a>Vereiste

Een [HDInsight Kafka-cluster met Enterprise Security Package](./apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Verbinding maken met de beheerinterface van Apache Ranger

1. Maak vanuit een browser verbinding met de beheerinterface van Ranger met behulp van de URL `https://ClusterName.azurehdinsight.net/Ranger/`. Vergeet niet om `ClusterName` te wijzigen in de naam van uw Kafka-cluster. Ranger-referenties zijn niet hetzelfde als referenties van een Hadoop-cluster. Gebruik een nieuw InPrivate-browservenster om verbinding te maken met de beheerinterface van Ranger om te voorkomen dat browsers Hadoop-referenties in de cache gebruiken.

2. Meld u aan met uw beheerdersreferenties voor Azure Active Directory (AD). Deze referenties zijn niet hetzelfde als de referenties voor het HDInsight-cluster of de SSH-referenties voor het Linux HDInsight knooppunt.

   :::image type="content" source="./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png" alt-text="Beheerinterface voor HDInsight Apache Ranger" border="true":::

## <a name="create-domain-users"></a>Domeingebruikers maken

Ga naar [Een HDInsight-cluster maken met Enterprise Security Package](./apache-domain-joined-configure-using-azure-adds.md) voor informatie over het maken van de domeingebruikers **sales_user** en **marketing_user**. In een productiescenario zijn de domeingebruikers afkomstig uit uw Active Directory-tenant.

## <a name="create-ranger-policy"></a>Ranger-beleid maken

Maak een Ranger-beleid voor **sales_user** en **marketing_user**.

1. Open de **beheerinterface van Ranger**.

2. Selecteer **\<ClusterName>_kafka** onder **Kafka**. Er kan één vooraf geconfigureerd beleid worden weergegeven.

3. Selecteer **Nieuw beleid toevoegen** en voer de volgende waarden in:

   |Instelling  |Voorgestelde waarde  |
   |---------|---------|
   |Policy Name  |  hdi sales* policy   |
   |Onderwerp   |  sales* |
   |Select User  |  sales_user1 |
   |Machtigingen  | Publish, Consume, Create |

   U kunt de volgende jokertekens gebruiken in de onderwerpnaam:

   * Gebruik '*' om nul of meer exemplaren van tekens aan te geven.
   * Gebruik '?' om één teken aan te geven.

   :::image type="content" source="./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png" alt-text="Beheerinterface van Apache Ranger: Beleid maken 1" border="true":::

   Als er niet automatisch een domeingebruiker wordt ingevuld bij **Select User**, wacht u even totdat Ranger is gesynchroniseerd met AAD.

4. Selecteer **Toevoegen** om het beleid op te slaan.

5. Selecteer **Nieuw beleid toevoegen** en voer de volgende waarden in:

   |Instelling  |Voorgestelde waarde  |
   |---------|---------|
   |Policy Name  |  hdi marketing policy   |
   |Onderwerp   |  marketingspend |
   |Select User  |  marketing_user1 |
   |Machtigingen  | Publish, Consume, Create |

   :::image type="content" source="./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png" alt-text="Beheerinterface van Apache Ranger: Beleid maken 2" border="true":::  

6. Selecteer **Toevoegen** om het beleid op te slaan.

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>Onderwerpen maken in een Kafka-cluster met ESP

Twee onderwerpen, `salesevents` en `marketingspend`, maken:

1. Gebruik de volgende opdracht om een SSH-verbinding met het cluster op te zetten:

   ```cmd
   ssh DOMAINADMIN@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   Vervang `DOMAINADMIN` door de beheerder van het cluster dat u hebt geconfigureerd tijdens het [maken van het cluster](./apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp) en vervang `CLUSTERNAME` door de naam van uw cluster. Voer het wachtwoord voor het beheerdersaccount in wanneer hierom wordt gevraagd. Zie [SSH gebruiken met HDInsight](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md) voor meer informatie over het gebruik van `SSH` met HDInsight.

2. Gebruik de volgende opdrachten om de clusternaam op te slaan in een variabele en een hulpprogramma voor het parseren van JSON (`jq`) te installeren. Wanneer u daarom wordt gevraagd, geeft u de naam van het Kafka-cluster op.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. Gebruik de volgende opdrachten om de Kafka-brokerhosts op te vragen. Geef desgevraagd het wachtwoord voor het beheerdersaccounts voor het cluster op.

   ```bash
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```

   Voordat u doorgaat, moet u mogelijk uw ontwikkelomgeving instellen als u dit nog niet hebt gedaan. U hebt onderdelen zoals de Java JDK, Apache Maven en een SSH-client met scp nodig. Zie [setup instructions](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer) (installatie-instructies) voor meer informatie.

1. Download de [voorbeelden voor aan een domein gekoppelde Apache Kafka voor producer/consumer](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Volg stap 2 en 3 onder **Het voorbeeld bouwen en implementeren** in [Zelfstudie: Werken met de Producer- en Consumer-API's van Apache Kafka](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example)

1. Voer de volgende opdrachten uit:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/conf/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/conf/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

## <a name="test-the-ranger-policies"></a>Ranger-beleidsregels testen

Op basis van de geconfigureerde Ranger-beleidsregels kan **sales_user** het onderwerp `salesevents` produceren/verbruiken, maar niet het onderwerp `marketingspend`. Omgekeerd kan **marketing_user** het onderwerp `marketingspend` produceren/verbruiken, maar niet het onderwerp `salesevents`.

1. Open een nieuwe SSH-verbinding met het cluster. Gebruik de volgende opdracht om u als **sales_user1** aan te melden:

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. Gebruik de brokernamen uit de vorige sectie om de volgende omgevingsvariabele in te stellen:

   ```bash
   export KAFKABROKERS=<brokerlist>:9092
   ```

   Voorbeeld: `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

3. Volg stap 3 onder **Het voorbeeld bouwen en implementeren** in [Zelfstudie: Werken met de Producer- en Consumer-API's van Apache Kafka](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example) om ervoor te zorgen dat de `kafka-producer-consumer.jar` ook beschikbaar is voor **sales_user**.

   > [!NOTE]  
   > Gebruik voor deze zelfstudie het bestand kafka-producer-consumer.jar onder het project 'DomainJoined-Producer-Consumer' (niet dat onder het project Producer-Consumer, dat bedoeld is voor scenario's zonder gekoppeld domein).

4. Controleer of **sales_user1** het onderwerp `salesevents` kan produceren. Voer hiervoor de volgende opdracht uit:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/conf/kafka_client_jaas.conf kafka-producer-consumer.jar producer salesevents $KAFKABROKERS
   ```

5. Voer de volgende opdracht uit om gebeurtenissen uit het onderwerp `salesevents` te verbruiken:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/conf/kafka_client_jaas.conf kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Controleer of u de berichten kunt lezen.

6. Controleer of **sales_user1** het onderwerp `marketingspend` niet kan produceren. Voer hiervoor het volgende uit in hetzelfde SSH-venster:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/conf/kafka_client_jaas.conf kafka-producer-consumer.jar producer marketingspend $KAFKABROKERS
   ```

   Er treedt een autorisatiefout op, maar die kunt u negeren.

7. U ziet dat **marketing_user1** geen gebeurtenissen van het onderwerp `salesevents` kan verbruiken.

   Herhaal stappen 1-3 hierboven, maar dit keer als **marketing_user1**.

   Voer de volgende opdracht uit om gebeurtenissen uit het onderwerp `salesevents` te verbruiken:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/conf/kafka_client_jaas.conf kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Eerdere berichten kunnen niet worden weergegeven.

8. Bekijk de controlegebeurtenissen voor toegang vanuit de gebruikersinterface van Ranger.

   :::image type="content" source="./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png" alt-text="Access-gebeurtenissen voor het controleren van gebruikers beleidsregels voor Zwerver " border="true":::
   
## <a name="produce-and-consume-topics-in-esp-kafka-by-using-the-console"></a>Onderwerpen in ESP Kafka maken en gebruiken met behulp van de console

> [!NOTE]
> U kunt geen console-opdrachten gebruiken om onderwerpen te maken. In plaats daarvan moet u de Java-code gebruiken die in de voorgaande sectie wordt getoond. Raadpleeg [Onderwerpen maken in een Kafka-cluster met ESP](#create-topics-in-a-kafka-cluster-with-esp) voor meer informatie.

Onderwerpen in ESP Kafka maken en gebruiken met behulp van de console:

1. Gebruik `kinit` met de gebruikersnaam van de gebruiker. Geef het wachtwoord op wanneer u daarom wordt gevraagd.

   ```bash
   kinit sales_user1
   ```

2. Omgevingsvariabelen instellen:

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/conf/kafka_client_jaas.conf"
   export KAFKABROKERS=<brokerlist>:9092
   ```

3. Berichten maken in het onderwerp `salesevents`:

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --topic salesevents --broker-list $KAFKABROKERS --security-protocol SASL_PLAINTEXT
   ```

4. Berichten van onderwerp `salesevents` gebruiken:

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --topic salesevents --from-beginning --bootstrap-server $KAFKABROKERS --security-protocol SASL_PLAINTEXT
   ```

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet meer gebruikt, verwijdert u het Kafka-cluster dat u hebt gemaakt, via de volgende stappen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Typ **HDInsight** in het **Zoekvak** bovenaan.
1. Selecteer onder **Services** de optie **HDInsight-clusters**.
1. Klik in de lijst met HDInsight-clusters die wordt weergegeven, op de **...** naast het cluster dat u voor deze zelfstudie hebt gemaakt. 
1. Klik op **Verwijderen**. Klik op **Ja**.

## <a name="troubleshooting"></a>Problemen oplossen
Als kafka-producer-consumer.jar niet werkt in een aan een domein gekoppeld cluster, controleer dan of u het bestand kafka-producer-consumer.jar onder het project 'DomainJoined-Producer-Consumer' gebruikt (niet dat onder het project Producer-Consumer, dat bedoeld is voor scenario's zonder gekoppeld domein).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Schijfversleuteling met behulp van door klant beheerde sleutel](../disk-encryption.md)

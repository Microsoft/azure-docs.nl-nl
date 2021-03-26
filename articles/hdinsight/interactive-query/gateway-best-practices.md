---
title: Uitgebreide en aanbevolen procedures voor het Apache Hive in azure HDInsight
description: Meer informatie over het navigeren in de aanbevolen procedures voor het uitvoeren van Hive-query's via de Azure HDInsight-gateway
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 549eab1547b75eb9461b23df2c157290943b4ed9
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869783"
---
# <a name="gateway-deep-dive-and-best-practices-for-apache-hive-in-azure-hdinsight"></a>Uitgebreide en aanbevolen procedures voor het Apache Hive in azure HDInsight

De Azure HDInsight-gateway (gateway) is de HTTPS-frontend voor HDInsight-clusters. De gateway is verantwoordelijk voor: authenticatie, host omzetting, service detectie en andere handige functies die nodig zijn voor een modern gedistribueerd systeem. De functies die door de gateway worden verschaft, resulteren in een deel van de overhead waarvoor in dit document de aanbevolen procedures voor het navigeren worden beschreven. Er worden ook technieken voor het oplossen van problemen besproken.

## <a name="the-hdinsight-gateway"></a>De HDInsight-gateway

De HDInsight-gateway is het enige deel van een HDInsight-cluster dat openbaar toegankelijk is via internet. De Gateway Service kan worden geopend zonder het open bare Internet via het `clustername-int.azurehdinsight.net` eind punt van de interne gateway te gebruiken. Met het interne gateway-eind punt kunnen verbindingen tot stand worden gebracht met de Gateway Service zonder het virtuele netwerk van het cluster af te sluiten. De gateway verwerkt alle aanvragen die naar het cluster worden verzonden en stuurt dergelijke aanvragen door naar de juiste onderdelen en clusterhosts.

Het volgende diagram bevat een ruwe illustratie van de manier waarop de gateway een samen vatting vormt van de verschillende mogelijkheden voor het omzetten van de host in HDInsight.

:::image type="content" source="./media/gateway-best-practices/host-resolution-diagram.png " alt-text="Diagram van host-omzetting" border="true":::

## <a name="motivation"></a>Motivatie

De motivatie voor het plaatsen van een gateway voor HDInsight-clusters is een interface te bieden voor service detectie en gebruikers verificatie. De verificatie mechanismen die door de gateway worden gebruikt, zijn vooral relevant voor ESP-clusters.

Voor service detectie is het voor deel van de gateway dat elk onderdeel in het cluster als een ander eind punt kan worden gebruikt in combi natie met de Gateway website ( `clustername.azurehdinsight.net/hive2` ), in tegens telling tot een groot aantal `host:port` paars.

Voor verificatie kan de gateway gebruikers verifiëren met behulp van een `username:password` referentie paar. Voor ESP-clusters zou deze referentie de gebruikers naam en het wacht woord van het domein zijn. Voor verificatie voor HDInsight-clusters via de gateway is de client niet verplicht een Kerberos-ticket te verkrijgen. Omdat de gateway `username:password` referenties accepteert en het Kerberos-ticket van de gebruiker verkrijgt namens de gebruiker, kunnen beveiligde verbindingen worden gemaakt met de gateway vanaf elke client host, inclusief clients die zijn gekoppeld aan verschillende AA-DDS-domeinen dan het (ESP)-cluster.

## <a name="best-practices"></a>Aanbevolen procedures

De gateway is een enkele service (taak verdeling over twee hosts) die verantwoordelijk is voor het door sturen en verifiëren van aanvragen. De gateway kan een doorvoer knelpunt worden voor Hive-query's die een bepaalde grootte overschrijden. Prestatie vermindering van query's kan worden waargenomen wanneer zeer grote **selectie** query's worden uitgevoerd op de gateway via ODBC of JDBC. "Erg groot" betekent query's die meer dan 5 GB aan gegevens in rijen of kolommen opleveren. Deze query kan bestaan uit een lange lijst met rijen en, of een breed aantal kolommen.

De prestaties van de gateway van een grote omvang worden vertraagd omdat de gegevens moeten worden overgebracht van het onderliggende gegevens archief (ADLS Gen2) naar: de HDInsight-Hive-server, de gateway en tot slot via de JDBC-of ODBC-stuur Programma's naar de client host.

In het volgende diagram ziet u de stappen van een SELECT-query.

:::image type="content" source="./media/gateway-best-practices/result-retrieval-diagram.png " alt-text="Resultaat diagram" border="true":::

Apache Hive is een relationele samen vatting van een bestands systeem dat compatibel is met HDFS. Deze abstractie betekent dat **Select** -instructies in Hive overeenkomen met **Lees** bewerkingen op het bestands systeem. De **Lees** bewerkingen worden vertaald naar het juiste schema voordat ze aan de gebruiker worden gerapporteerd. De latentie van dit proces neemt toe met de grootte van de gegevens en het totale aantal hops dat nodig is om de eind gebruiker te bereiken.

Hetzelfde gedrag kan optreden bij het uitvoeren van **Create** -of **Insert** -instructies van grote gegevens, omdat deze opdrachten overeenkomen met **Schrijf** bewerkingen in het onderliggende bestands systeem. Overweeg om gegevens, zoals RAW ORC, te schrijven naar het bestands systeem/datalake in plaats van het te laden met behulp van **Insert** of **Load**.

In bedrijven met een beveiligd beveiligings pakket kan voldoende complex Apache zwerver-beleid een vertraging veroorzaken in de compilatie tijd van de query, wat kan leiden tot een time-out voor de gateway. Als er een time-out voor de gateway is opgetreden in een ESP-cluster, overweeg dan om het aantal zwerver-beleid te verminderen of te combi neren.

## <a name="troubleshooting-techniques"></a>Technieken voor probleem oplossing

Er zijn meerdere locaties voor het beperken en het vaststellen van prestatie problemen die aan het bovenstaande gedrag voldoen. Gebruik de volgende controle lijst wanneer de prestaties van de query worden vertraagd via de HDInsight-gateway:

* Gebruik de component **Limit** wanneer u grote **selectie** query's uitvoert. De component **Limit** vermindert het totaal aantal rijen dat is gerapporteerd aan de client-host. De component **Limit** heeft alleen gevolgen voor het genereren van resultaten en wijzigt het query plan niet. Gebruik de configuratie om de component **Limit** toe te passen op het query plan `hive.limit.optimize.enable` . De **limiet** kan worden gecombineerd met een offset met behulp van het argument formulier **maximum x, y**.

* Geef uw interessante kolommen een naam wanneer u **Select** -query's uitvoert in plaats van **selecteren \***. Als u minder kolommen selecteert, wordt de hoeveelheid gegevens die wordt gelezen verlaagd.

* Probeer de query van interest uit te voeren via Apache Beeline. Als het ophalen van resultaten via Apache Beeline een lange periode duurt, worden er vertragingen verwacht bij het ophalen van dezelfde resultaten via externe hulpprogram ma's.

* Een eenvoudige Hive-query testen om ervoor te zorgen dat er een verbinding met de HDInsight-gateway tot stand kan worden gebracht. Voer een basis query uit twee of meer externe hulpprogram ma's uit om er zeker van te zijn dat er geen afzonderlijk hulp programma wordt uitgevoerd.

* Bekijk alle Apache Ambari-waarschuwingen om ervoor te zorgen dat HDInsight-Services in orde zijn. Ambari-waarschuwingen kunnen worden geïntegreerd met Azure Monitor voor rapportage en analyse.

* Als u een externe meta Store van Hive gebruikt, controleert u of de Azure SQL DB-DTU voor de Hive-meta Store de limiet niet heeft bereikt. Als de DTU de limiet nadert, moet u de grootte van de data base verg Roten.

* Zorg ervoor dat hulpprogram ma's van derden, zoals aan pbi of tableau, paginering gebruiken om tabellen of data bases weer te geven. Raadpleeg uw ondersteunings partners voor deze hulpprogram ma's voor hulp bij de paginering. Het belangrijkste hulp programma dat wordt gebruikt voor paginering is de JDBC- `fetchSize` para meter. Een kleine ophaal grootte kan leiden tot gedegradeerde gateway prestaties, maar een time-out voor het ophalen van de gateway kan resulteren. Het afstemmen van het ophalen van grootte moet worden uitgevoerd op basis van de werk belasting.

* Als uw gegevens pijplijn een grote hoeveelheid gegevens uit de onderliggende opslag van het HDInsight-cluster moet lezen, kunt u een hulp programma gebruiken dat rechtstreeks aan Azure Storage, zoals Azure Data Factory

* Overweeg het gebruik van Apache Hive LLAP bij het uitvoeren van interactieve werk belastingen, omdat LLAP mogelijk een soepelere ervaring biedt om snel query resultaten te retour neren

* Overweeg het aantal threads dat beschikbaar is voor de Hive-meta Store-service met te verhogen `hive.server2.thrift.max.worker.threads` . Deze instelling is vooral relevant wanneer een groot aantal gelijktijdige gebruikers query's naar het cluster verzenden

* Verminder het aantal nieuwe pogingen om de gateway te bereiken vanuit externe hulpprogram ma's. Als er meerdere nieuwe pogingen worden gebruikt, kunt u overwegen om een beleid voor opnieuw proberen van exponentiële back-ups te volgen

* Overweeg de compressie component in te scha kelen met behulp van de configuraties `hive.exec.compress.output` en `hive.exec.compress.intermediate` .

## <a name="next-steps"></a>Volgende stappen

* [Apache Beeline in HDInsight](../hadoop/apache-hadoop-use-hive-beeline.md)
* [Stappen voor probleem oplossing voor HDInsight-gateway](./troubleshoot-gateway-timeout.md)
* [Virtuele netwerken voor HDInsight](../hdinsight-plan-virtual-network-deployment.md)
* [HDInsight met Express route](../connect-on-premises-network.md)
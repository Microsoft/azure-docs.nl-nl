---
title: De beschik baarheid van clusters bewaken met Apache Ambari in azure HDInsight
description: Meer informatie over het gebruik van Apache Ambari voor het bewaken van de cluster status en beschik baarheid.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/01/2020
ms.openlocfilehash: 34f752d4c3d50d5f680e317b2724b1e631537f32
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933197"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-in-azure-hdinsight"></a>De beschik baarheid van clusters bewaken met Apache Ambari in azure HDInsight

HDInsight-clusters bevatten Apache Ambari, waarmee u in één oogopslag en vooraf gedefinieerde waarschuwingen status gegevens kunt bekijken.

In dit artikel wordt beschreven hoe u Ambari kunt gebruiken om uw cluster te bewaken en worden enkele voor beelden gegeven voor het configureren van een Ambari-waarschuwing, het bewaken van de beschik baarheid van knoop punten en het maken van een Azure Monitor waarschuwing die wordt geactiveerd wanneer er in vijf uur geen heartbeat is ontvangen van een of meer knoop punten.

## <a name="dashboard"></a>Dashboard

U kunt het Ambari-dash board openen door de koppeling **Ambari Home** te selecteren in het gedeelte **cluster dashboards** van het overzicht van HDInsight in azure Portal, zoals hieronder wordt weer gegeven. U kunt deze ook openen door te navigeren naar `https://CLUSTERNAME.azurehdinsight.net` in een browser waarbij clustername de naam van uw cluster is.

![Weer gave HDInsight-resource Portal](media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png)

Vervolgens wordt u gevraagd om een gebruikers naam en wacht woord voor het aanmelden bij een cluster. Voer de referenties in die u hebt gekozen tijdens het maken van het cluster.

Vervolgens gaat u naar het Ambari-dash board, dat widgets bevat die een aantal metrische gegevens bevatten om u een kort overzicht te geven van de status van uw HDInsight-cluster. In deze widgets worden metrische gegevens weer gegeven, zoals het aantal live DataNodes (werk knooppunten) en het beschik journalnodes (Zookeeper-knoop punt), NameNodes (hoofd knooppunten), evenals de metrische gegevens die specifiek zijn voor bepaalde cluster typen, zoals de uptime van de Resource Manager voor Spark-en Hadoop-clusters.

![Apache Ambari-weer gave dash board gebruiken](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

## <a name="hosts--view-individual-node-status"></a>Hosts: de status van afzonderlijke knoop punten weer geven

U kunt ook status informatie voor afzonderlijke knoop punten weer geven. Selecteer het tabblad **hosts** om een lijst met alle knoop punten in uw cluster weer te geven en Bekijk basis informatie over elk knoop punt. De groene controle links van elke knooppunt naam geeft aan dat alle onderdelen op het knoop punt zijn ingesteld. Als een onderdeel op een knoop punt niet beschikbaar is, ziet u een rode waarschuwings driehoek in plaats van de groene controle.

![Weer gave HDInsight Apache Ambari-hosts](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

U kunt vervolgens de **naam** van een knoop punt selecteren om meer gedetailleerde metrische gegevens van de host voor dat specifieke knoop punt weer te geven. In deze weer gave wordt de status/Beschik baarheid van elk afzonderlijk onderdeel weer gegeven.

![Apache Ambari host weer gave met één knoop punt](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

## <a name="ambari-alerts"></a>Ambari-waarschuwingen

Ambari biedt ook verschillende Configureer bare waarschuwingen waarmee u bepaalde gebeurtenissen kunt melden. Wanneer waarschuwingen worden geactiveerd, worden ze weer gegeven in de linkerbovenhoek van Ambari in een rode badge met het aantal waarschuwingen. Als u deze badge selecteert, wordt er een lijst met huidige waarschuwingen weer gegeven.

![Aantal huidige waarschuwingen Apache Ambari](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

Als u een lijst met waarschuwings definities en hun statussen wilt weer geven, selecteert u het tabblad **waarschuwingen** , zoals hieronder wordt weer gegeven.

![Weer gave Ambari-waarschuwings definities](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari biedt veel vooraf gedefinieerde waarschuwingen met betrekking tot Beschik baarheid, waaronder:

| Naam van waarschuwing                        | Beschrijving   |
|---|---|
| DataNode status overzicht           | Deze waarschuwing op service niveau wordt geactiveerd als er sprake is van een slechte DataNodes|
| Status van hoge Beschik baarheid van NameNode | Deze waarschuwing op service niveau wordt geactiveerd als de actieve NameNode of stand-NameNode niet actief is.|
| Percentage beschik journalnodes beschikbaar    | Deze waarschuwing wordt geactiveerd als het aantal beschik journalnodes in het cluster hoger is dan de geconfigureerde kritieke drempel waarde. Hiermee worden de resultaten van JournalNode-proces controles geaggregeerd. |
| Percentage DataNodes beschikbaar       | Deze waarschuwing wordt geactiveerd als het aantal DataNodes in het cluster hoger is dan de geconfigureerde kritieke drempel waarde. Hiermee worden de resultaten van DataNode-proces controles geaggregeerd.|


Als u Details voor een waarschuwing wilt weer geven of criteria wilt wijzigen, selecteert u de **naam** van de waarschuwing. Maak een voor beeld van **DataNode-status samenvatting** . U ziet een beschrijving van de waarschuwing en de specifieke criteria die een waarschuwing of kritieke waarschuwing activeren en het controle-interval voor de criteria. Als u de configuratie wilt bewerken, selecteert u de knop **bewerken** in de rechter bovenhoek van het configuratie venster.

![Configuratie van Apache Ambari-waarschuwingen](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Hier kunt u de beschrijving bewerken en, belang rijker, het controle-interval en de drempel waarden voor waarschuwing of kritieke waarschuwingen.

![Ambari waarschuwings configuraties weer gave bewerken](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

In dit voor beeld zou u 2 een slechte DataNodes activeren en een kritieke waarschuwing geven en 1 slecht DataNode alleen een waarschuwing activeren. Selecteer **Opslaan** wanneer u klaar bent met het bewerken van.

## <a name="email-notifications"></a>E-mailmeldingen

U kunt desgewenst ook e-mail meldingen configureren voor Ambari-waarschuwingen. Als u dit wilt doen, klikt u op het tabblad **waarschuwingen** op de knop **acties** in de linkerbovenhoek en vervolgens op **meldingen beheren.**

![Actie Ambari beheren](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Er wordt een dialoog venster geopend voor het beheren van waarschuwings meldingen. Selecteer de aan **+** de onderkant van het dialoog venster en vul de vereiste velden in om Ambari te bieden met de details van de e-mail server waaruit e-mails kunnen worden verzonden.

> [!TIP]
> Het instellen van Ambari-e-mail meldingen kan een goede manier zijn om waarschuwingen op één plek te ontvangen bij het beheren van veel HDInsight-clusters.

## <a name="next-steps"></a>Volgende stappen

- [Beschik baarheid en betrouw baarheid van Apache Hadoop clusters in HDInsight](./hdinsight-business-continuity.md)
- [Clusterbeschikbaarheid - Azure Monitor-logboeken](./cluster-availability-monitor-logs.md)
- [Azure Monitor-Logboeken gebruiken](hdinsight-hadoop-oms-log-analytics-tutorial.md)
- [E-mailmeldingen voor Apache Ambari](apache-ambari-email.md)

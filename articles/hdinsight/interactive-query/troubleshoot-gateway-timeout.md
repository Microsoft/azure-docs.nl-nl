---
title: Uitzonde ring bij het uitvoeren van query's vanuit Apache Ambari-Hive-weer gave in azure HDInsight
description: Stappen voor het oplossen van problemen met het uitvoeren van Apache Hive query's via Apache Ambari-Hive-weer gave in azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 12/23/2019
ms.openlocfilehash: 40ef380cd2bd4743b92daf44a0a5b70ade1cbb35
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933016"
---
# <a name="exception-when-running-queries-from-apache-ambari-hive-view-in-azure-hdinsight"></a>Uitzonde ring bij het uitvoeren van query's vanuit Apache Ambari-Hive-weer gave in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Wanneer u een Apache Hive query uitvoert vanuit Apache Ambari-Hive-weer gave, wordt er regel matig het volgende fout bericht weer gegeven:

```error
Cannot create property 'errors' on string '<!DOCTYPE html PUBLIC '-//W3C//DTD XHTML 1.0 Strict//EN' 'http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd'>
<html xmlns='http://www.w3.org/1999/xhtml'>
<head>
<title>IIS 8.5 Detailed Error - 502.3 - Bad Gateway</title>
```

## <a name="cause"></a>Oorzaak

Een time-out voor de gateway.

De time-outwaarde voor de gateway is 2 minuten. Query's van de Ambari-Hive-weer gave worden `/hive2` via de gateway naar het eind punt verzonden. Zodra de query is gecompileerd en geaccepteerd, retourneert de HiveServer een `queryid` . Clients gebruiken vervolgens polling voor de status van de query. Als de HiveServer tijdens dit proces geen HTTP-antwoord binnen twee minuten retourneert, genereert de HDI-gateway een time-outfout van 502,3-gateway naar de aanroeper. De fouten kunnen zich voordoen wanneer de query wordt verzonden voor verwerking (waarschijnlijker meer) en ook in de aanroep status ophalen (minder waarschijnlijk). Gebruikers zien een van beide.

De http-handler-thread moet snel zijn: de taak voorbereiden en een ophalen `queryid` . Om een aantal redenen kunnen alle handler-threads echter bezet zijn, wat resulteert in time-outs voor nieuwe query's en de status aanvragen ophalen.

### <a name="responsibilities-of-the-http-handler-thread"></a>Verantwoordelijkheden van de HTTP-handler-thread

Wanneer de client een query verzendt naar HiveServer, gebeurt het volgende in de voorgrond thread:

* De aanvraag parseren, semantische verificatie uitvoeren
* Vergren deling ophalen
* Zoek opdracht in de meta Store, indien nodig
* De query compileren (DDL of DML)
* Een query plan voorbereiden
* Autorisatie uitvoeren (alle toepasselijke zwerver-beleids regels uitvoeren in beveiligde clusters)

## <a name="resolution"></a>Oplossing

Enkele algemene aanbevelingen voor het verbeteren van de situatie:

* Als u een externe meta Store van een Hive-archief gebruikt, controleert u de metrische gegevens van de data base en zorgt u ervoor dat deze niet zijn overbelast. Overweeg de laag van de meta store-data base te schalen.

* Zorg ervoor dat parallelle OPS is ingeschakeld (Hiermee kunnen de HTTP-handlers gelijktijdig worden uitgevoerd). Als u de waarde wilt controleren, opent u [Apache Ambari](../hdinsight-hadoop-manage-ambari.md) en navigeert u naar **Hive**  >  **configurations**  >  **Geavanceerde**  >  **aangepaste Hive-site**. De waarde voor `hive.server2.parallel.ops.in.session` moet zijn `true` .

* Zorg ervoor dat de virtuele machine-SKU van het cluster niet te klein is voor de belasting. Overweeg om het werk over meerdere clusters te splitsen. Zie [een cluster type kiezen](../hdinsight-capacity-planning.md#choose-a-cluster-type)voor meer informatie.

* Als zwerver op het cluster is geïnstalleerd, controleert u of er te veel zwerver-beleids regels zijn die moeten worden geëvalueerd voor elke query. Zoek naar dubbele of overbodige beleids regels.

* Controleer de **grootte** waarde van de HiveServer2-heap van Ambari. Navigeer naar **Hive**  >  **configuratie**  >  **instellingen**  >  **optimalisatie**. Zorg ervoor dat de waarde groter is dan 10 GB. Pas zo nodig aan om de prestaties te optimaliseren.

* Zorg ervoor dat de Hive-query goed is afgestemd. Zie [Apache Hive Query's optimaliseren in azure HDInsight](../hdinsight-hadoop-optimize-hive-query.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](../../azure-portal/supportability/how-to-create-azure-support-request.md)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
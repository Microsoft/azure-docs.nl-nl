---
title: Problemen oplossen met een trage of mislukte taak in een Azure HDInsight-cluster
description: Een trage of mislukte taak in een Azure HDInsight-cluster diagnosticeren en problemen oplossen.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: 660c80a471739f7dc0163e915f45e3a067e1e4b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871959"
---
# <a name="troubleshoot-a-slow-or-failing-job-on-a-hdinsight-cluster"></a>Problemen met een trage of niet werkende taak in een HDInsight-cluster oplossen

Als een toepassing die gegevens verwerkt op een HDInsight-cluster langzaam wordt uitgevoerd of als er een fout optreedt, hebt u verschillende opties voor probleem oplossing. Als uw taken langer duren dan verwacht of als u een trage reactie tijd in het algemeen ziet, is er mogelijk een fout opgetreden in de upstream van het cluster, zoals de services waarop het cluster wordt uitgevoerd. De meest voorkomende oorzaak van deze vertragingen is echter onvoldoende schalen. Wanneer u een nieuw HDInsight-cluster maakt, selecteert u de juiste grootte van de [virtuele machine](hdinsight-supported-node-configuration.md).

Als u een langzame of mislukte cluster wilt vaststellen, verzamelt u informatie over alle aspecten van de omgeving, zoals gekoppelde Azure-Services, cluster configuratie en informatie over taak uitvoering. Een nuttige diagnose is om te proberen de fout status te reproduceren op een ander cluster.

* Stap 1: gegevens over het probleem verzamelen.
* Stap 2: de HDInsight-cluster omgeving valideren.
* Stap 3: de status van uw cluster weer geven.
* Stap 4: de stack en versies van de omgeving controleren.
* Stap 5: Controleer de logboek bestanden van het cluster.
* Stap 6: Controleer de configuratie-instellingen.
* Stap 7: Reproduceer de fout op een ander cluster.

## <a name="step-1-gather-data-about-the-issue"></a>Stap 1: gegevens over het probleem verzamelen

HDInsight biedt veel hulpprogram ma's die u kunt gebruiken om problemen met clusters te identificeren en op te lossen. De volgende stappen begeleiden u bij deze hulpprogram ma's en bieden suggesties voor het zoeken van het probleem.

### <a name="identify-the-problem"></a>Het probleem identificeren

Houd rekening met de volgende vragen om het probleem te identificeren:

* Wat had ik verwacht? Wat is er gebeurd?
* Hoe lang duurt het proces om uit te voeren? Hoe lang moet de app worden uitgevoerd?
* Moeten mijn taken altijd langzaam worden uitgevoerd op dit cluster? Zijn ze sneller actief op een ander cluster?
* Wanneer is dit probleem voor het eerst opgetreden? Hoe vaak is dit gebeurd sinds?
* Is er iets gewijzigd in mijn cluster configuratie?

### <a name="cluster-details"></a>Cluster Details

Belang rijke cluster informatie omvat:

* Cluster naam.
* Cluster regio: Controleer op [regio storingen](https://azure.microsoft.com/status/).
* Het type en de versie van het HDInsight-cluster.
* Type en aantal HDInsight-instanties dat is opgegeven voor de hoofd-en worker-knoop punten.

De Azure Portal kan deze informatie leveren:

:::image type="content" source="./media/hdinsight-troubleshoot-failed-cluster/hdi-azure-portal-info.png" alt-text="Gegevens van HDInsight-Azure Portal":::

U kunt ook [Azure cli](/cli/azure/)gebruiken:

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Een andere optie is het gebruik van Power shell. Zie  [Apache Hadoop clusters in HDInsight beheren met Azure PowerShell](hdinsight-administer-use-powershell.md)voor meer informatie.

## <a name="step-2-validate-the-hdinsight-cluster-environment"></a>Stap 2: de HDInsight-cluster omgeving valideren

Elk HDInsight-cluster is afhankelijk van verschillende Azure-Services en op open-source software, zoals Apache HBase en Apache Spark. HDInsight-clusters kunnen ook aanroepen op andere Azure-Services, zoals Azure Virtual Networks.  Een cluster fout kan worden veroorzaakt door een van de actieve services in uw cluster of door een externe service.  Een wijziging in de configuratie van een cluster service kan er ook toe leiden dat het cluster mislukt.

### <a name="service-details"></a>Service Details

* Controleer de versies van de open-source bibliotheek versie.
* Controleren op [uitval van Azure-service](https://azure.microsoft.com/status/).  
* Controleren op gebruiks limieten van Azure-service. 
* Controleer de configuratie van het Azure Virtual Network-subnet.  

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Cluster configuratie-instellingen weer geven met de Ambari-gebruikers interface

Apache Ambari biedt beheer en bewaking van een HDInsight-cluster met een web-UI en een REST API. Ambari is opgenomen in HDInsight-clusters op basis van Linux. Selecteer het deel venster **cluster dashboard** op de pagina Azure Portal HDInsight.  Selecteer het deel venster **dash board voor HDInsight-cluster** om de Ambari-gebruikers interface te openen en voer de aanmeldings referenties voor het cluster in.  

:::image type="content" source="./media/hdinsight-troubleshoot-failed-cluster/apache-ambari-overview.png" alt-text="Overzicht van Apache Ambari-dash board":::

Als u een lijst met Service weergaven wilt openen, selecteert u **Ambari-weer gaven** op de pagina Azure Portal.  Deze lijst is afhankelijk van welke bibliotheken zijn geïnstalleerd. Zo ziet u een voor beeld van een garen van het wachtrij beheer, Hive-weer gave en TEZ.  Selecteer een service koppeling om de configuratie-en service gegevens te bekijken.

#### <a name="check-for-azure-service-outages"></a>Controleren op Azure-servicestoringen

HDInsight is afhankelijk van verschillende Azure-Services. Hiermee worden virtuele servers op Azure HDInsight uitgevoerd, worden gegevens en scripts opgeslagen in Azure Blob Storage of Azure Data Lake Storage, en worden logboek bestanden geïndexeerd in azure Table Storage. Storingen in deze services, hoewel zeldzame, kunnen problemen veroorzaken in HDInsight. Als er onverwachte vertragingen of fouten in uw cluster optreden, controleert u het [dash board van Azure-status](https://azure.microsoft.com/status/). De status van elke service wordt per regio weer gegeven. Controleer de regio van uw cluster en de regio's voor alle gerelateerde services.

#### <a name="check-azure-service-usage-limits"></a>Gebruiks limieten van Azure-service controleren

Als u een groot cluster start of meerdere clusters tegelijkertijd hebt gestart, kan een cluster mislukken als u een limiet van Azure-Services hebt overschreden. Service limieten variëren, afhankelijk van uw Azure-abonnement. Zie [Azure-abonnement- en servicelimieten, quota en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md) voor meer informatie.
U kunt aanvragen dat micro soft het aantal HDInsight-resources dat beschikbaar is (zoals VM-kernen en VM-exemplaren), verg Roten met een aanvraag voor het verhogen van de [kern quota van Resource Manager](../azure-portal/supportability/resource-manager-core-quotas-request.md).

#### <a name="check-the-release-version"></a>De release versie controleren

Vergelijk de Cluster versie met de nieuwste versie van HDInsight. Elke HDInsight-release bevat verbeteringen zoals nieuwe toepassingen, functies, patches en oplossingen voor fouten. Het probleem dat van invloed is op uw cluster is mogelijk opgelost in de meest recente release versie. Voer, indien mogelijk, uw cluster opnieuw uit met de nieuwste versie van HDInsight en de bijbehorende bibliotheken zoals Apache HBase, Apache Spark en anderen.

#### <a name="restart-your-cluster-services"></a>De Cluster Services opnieuw starten

Als u vertragingen in uw cluster ondervindt, kunt u overwegen uw services opnieuw te starten via de Ambari-gebruikers interface of de klassieke Azure-CLI. Het cluster ondervindt mogelijk tijdelijke fouten en het opnieuw opstarten is de snelste manier om uw omgeving te stabiliseren en mogelijk de prestaties te verbeteren.

## <a name="step-3-view-your-clusters-health"></a>Stap 3: de status van uw cluster weer geven

HDInsight-clusters bestaan uit verschillende typen knoop punten die worden uitgevoerd op exemplaren van virtuele machines. Elk knoop punt kan worden bewaakt voor bron beroving, problemen met de netwerk verbinding en andere problemen waardoor het cluster kan vertragen. Elk cluster bevat twee hoofd knooppunten en de meeste cluster typen bevatten een combi natie van worker-en Edge-knoop punten. 

Zie [clusters instellen in HDInsight met Apache Hadoop, Apache Spark, Apache Kafka en meer](hdinsight-hadoop-provision-linux-clusters.md)voor een beschrijving van de verschillende knoop punten die elk cluster type gebruikt.

In de volgende secties wordt beschreven hoe u de status van elk knoop punt en van het hele cluster controleert.

### <a name="get-a-snapshot-of-the-cluster-health-using-the-ambari-ui-dashboard"></a>Een moment opname van de cluster status ophalen met behulp van het dash board Ambari-gebruikers interface

Het [Ambari-dash board van de gebruikers interface](#view-cluster-configuration-settings-with-the-ambari-ui) ( `https://<clustername>.azurehdinsight.net` ) biedt een overzicht van de cluster status, zoals uptime, geheugen, netwerk-en CPU-gebruik, HDFS-schijf gebruik, enzovoort. Gebruik de sectie hosts van Ambari om resources op hostniveau weer te geven. U kunt ook services stoppen en opnieuw starten.

### <a name="check-your-webhcat-service"></a>Controleer uw WebHCat-service

Een veelvoorkomend scenario voor Apache Hive-, Apache Pig-of Apache Sqoop-taken mislukken is een fout met de [WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md) -service (of *Templeton*). WebHCat is een REST-interface voor het uitvoeren van externe taken, zoals Hive, varken, overzicht en MapReduce. WebHCat vertaalt de aanvraag voor het indienen van taken in Apache Hadoop garen-toepassingen en retourneert een status die is afgeleid van de toepassings status van de GARENs.  In de volgende secties worden veelvoorkomende HTTP-status codes voor WebHCat beschreven.

#### <a name="badgateway-502-status-code"></a>BadGateway (502-status code)

Deze code is een algemeen bericht van Gateway knooppunten en de meest voorkomende fout status codes. Een mogelijke oorzaak hiervoor is dat de WebHCat-service actief is op het actieve hoofd knooppunt. Gebruik de volgende krul opdracht om te controleren of dit mogelijk is:

```bash
curl -u admin:{HTTP PASSWD} https://{CLUSTERNAME}.azurehdinsight.net/templeton/v1/status?user.name=admin
```

In Ambari wordt een waarschuwing weer gegeven met de hosts waarop de WebHCat-service niet beschikbaar is. U kunt proberen om de WebHCat-service terug te zetten door de service op de host opnieuw te starten.

:::image type="content" source="./media/hdinsight-troubleshoot-failed-cluster/restart-webhcat-server.png" alt-text="Apache Ambari WebHCat-server opnieuw starten":::

Als er nog geen WebHCat-server beschikbaar is, controleert u het operations-logboek op fout berichten. Controleer de `stderr` en de `stdout` bestanden waarnaar wordt verwezen in het knoop punt voor meer informatie.

#### <a name="webhcat-times-out"></a>WebHCat time-out

Als er een time-out optreedt voor An HDInsight gateway, duurt het langer dan twee minuten om te retour neren `502 BadGateway` . WebHCat queryeert garen Services voor taak statussen, en als GARENs langer dan twee minuten duren, kan de aanvraag een time-out hebben.

In dit geval raadpleegt u de volgende logboeken in de `/var/log/webhcat` map:

* **webhcat. log** is het log4j-logboek waarnaar de server logboeken schrijft
* **webhcat-console. log** is de stdout van de server wanneer deze wordt gestart
* **webhcat-console-error. log** is de stderr van het Server proces

> [!NOTE]  
> Elke `webhcat.log` wordt elke dag doorgevoerd, waarbij bestanden worden gegenereerd met de naam `webhcat.log.YYYY-MM-DD` . Selecteer het juiste bestand voor het tijds bereik dat u wilt onderzoeken.

In de volgende secties worden enkele mogelijke oorzaken voor WebHCat-outs beschreven.

##### <a name="webhcat-level-timeout"></a>WebHCat-out

Wanneer WebHCat wordt geladen en er meer dan 10 open sockets zijn, duurt het langer om nieuwe socket verbindingen tot stand te brengen, wat kan leiden tot een time-out. Als u de netwerk verbindingen van en naar WebHCat wilt weer geven, gebruikt u `netstat`  op de huidige actieve hoofd knooppunt:

```bash
netstat | grep 30111
```

30111 is de poort WebHCat luistert naar. Het aantal open sockets moet kleiner zijn dan 10.

Als er geen open sockets zijn, produceert de vorige opdracht geen resultaat. Als u wilt controleren of Templeton is ingesteld op poort 30111, gebruikt u:

```bash
netstat -l | grep 30111
```

##### <a name="yarn-level-timeout"></a>Time-out voor garen niveau

Templeton belt GARENs om taken uit te voeren en de communicatie tussen Templeton en garen kan een time-out veroorzaken.

Op het niveau van de GARENs zijn er twee soorten time-outs:

1. Het verzenden van een garen taak kan lang genoeg duren om een time-out te veroorzaken.

    Als u het `/var/log/webhcat/webhcat.log` logboek bestand opent en op ' wachtrij taak ' zoekt, ziet u mogelijk meerdere vermeldingen waarbij de uitvoerings tijd buitensporig lang is (>2000 MS), met vermeldingen die de toenemende wacht tijden weer geven.

    De tijd voor de taken in de wachtrij blijft toenemen omdat de snelheid waarmee nieuwe taken worden verzonden hoger is dan de snelheid waarmee de oude taken zijn voltooid. Zodra het garen geheugen 100% is gebruikt, kan de *joblauncher-wachtrij* geen capaciteit meer lenen van de *standaard wachtrij*. Daarom kunnen er geen nieuwe taken meer worden geaccepteerd in de joblauncher-wachtrij. Dit kan ervoor zorgen dat de wacht tijd langer en langer duurt, waardoor er een time-outfout optreedt die meestal door veel andere wordt gevolgd.

    In de volgende afbeelding ziet u de joblauncher-wachtrij met een overgebruik van 714,4%. Dit is acceptabel, zolang er nog steeds vrije capaciteit in de standaard wachtrij staat om van te lenen. Wanneer het cluster echter volledig wordt gebruikt en de capaciteit van het garen ten 100% is, moeten nieuwe taken wachten, waardoor de time-outs uiteindelijk worden veroorzaakt.

    :::image type="content" source="./media/hdinsight-troubleshoot-failed-cluster/hdi-job-launcher-queue.png" alt-text="Wachtrij weergave van HDInsight-taak starten":::

    Er zijn twee manieren om dit probleem op te lossen: Verminder de snelheid van de nieuwe taken die worden verzonden of verg root de verbruiks snelheid van oude taken door het cluster omhoog te schalen.

2. Het verwerken van GARENs kan enige tijd duren. Dit kan leiden tot time-outs.

    * Alle taken weer geven: dit is een tijdrovende oproep. Met deze aanroep worden de toepassingen van de garen-Resource Manager opgesomd en voor elke voltooide toepassing wordt de status opgehaald van het garen JobHistoryServer. Met een hoger aantal taken kan deze aanroep een time-out hebben.

    * Taken weer geven die ouder zijn dan zeven dagen: het HDInsight-garen JobHistoryServer is zo geconfigureerd dat de voltooide taak gegevens zeven dagen ( `mapreduce.jobhistory.max-age-ms` waarde) behouden blijven. Het opsommen van verwijderde taken resulteert in een time-out.

U kunt deze problemen als volgt vaststellen:

1. Het UTC-tijds bereik voor het oplossen van problemen bepalen
2. Selecteer de gewenste `webhcat.log` bestanden
3. Zoeken naar waarschuwingen en fout berichten tijdens die tijd

#### <a name="other-webhcat-failures"></a>Andere WebHCat-fouten

1. HTTP-status code 500

    In de meeste gevallen waarin WebHCat 500 retourneert, bevat het fout bericht Details over de fout. Raadpleeg anders `webhcat.log` voor waarschuwingen en fout berichten.

2. Taak fouten

    Er zijn mogelijk situaties waarin interacties met WebHCat zijn geslaagd, maar de taken mislukken.

    Templeton verzamelt de taak console-uitvoer als `stderr` in `statusdir` , wat vaak handig is voor het oplossen van problemen. `stderr` bevat de toepassings-id van de GARENs van de werkelijke query.

## <a name="step-4-review-the-environment-stack-and-versions"></a>Stap 4: de stack en versies van de omgeving controleren

De pagina Ambari UI- **stack en-versie** bevat informatie over de configuratie van de Cluster Services en de geschiedenis van de service versie.  Onjuiste versies van de Hadoop-service bibliotheek kunnen een storing in de cluster veroorzaken.  Selecteer in de Ambari-gebruikers interface het menu **beheerder** en vervolgens  **stacks en versies**.  Selecteer op de pagina het tabblad **versies** om informatie over de service versie weer te geven:

:::image type="content" source="./media/hdinsight-troubleshoot-failed-cluster/ambari-stack-versions.png" alt-text="Apache Ambari-stack en-versies":::

## <a name="step-5-examine-the-log-files"></a>Stap 5: de logboek bestanden controleren

Er zijn veel soorten logboeken die worden gegenereerd op basis van de vele services en onderdelen waaruit een HDInsight-cluster bestaat. [WebHCat-logboek bestanden](#check-your-webhcat-service) worden eerder beschreven. Er zijn verschillende andere nuttige logboek bestanden die u kunt onderzoeken om problemen met uw cluster op te lossen, zoals wordt beschreven in de volgende secties.

* HDInsight-clusters bestaan uit verschillende knoop punten, waarvan de meeste taken taken kunnen uitvoeren. Taken worden gelijktijdig uitgevoerd, maar logboek bestanden kunnen alleen lineaire resultaten weer geven. HDInsight voert nieuwe taken uit en beëindigt andere die niet eerst zijn voltooid. Al deze activiteit wordt geregistreerd bij de- `stderr` en- `syslog` bestanden.

* In de script actie logboek bestanden worden fouten of onverwachte configuratie wijzigingen weer gegeven tijdens het maken van het cluster.

* De Hadoop-stap logboeken identificeren Hadoop-taken die worden gestart als onderdeel van een stap die fouten bevat.

### <a name="check-the-script-action-logs"></a>Controleer de script actie logboeken

Met HDInsight- [script acties](hdinsight-hadoop-customize-cluster-linux.md) worden scripts hand matig of indien opgegeven in het cluster uitgevoerd. Script acties kunnen bijvoorbeeld worden gebruikt voor het installeren van extra software op het cluster of voor het wijzigen van de configuratie-instellingen van de standaard waarden. Het controleren van de script actie logboeken kan inzicht geven in fouten die zijn opgetreden tijdens het instellen en configureren van het cluster.  U kunt de status van een script actie weer geven door de **OPS** -knop in de Ambari-gebruikers interface te selecteren of door de logboeken te openen vanuit het standaard opslag account.

De script actie logboeken bevinden zich in de `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` map.

### <a name="view-hdinsight-logs-using-ambari-quick-links"></a>HDInsight-logboeken weer geven met Ambari-snelle koppelingen

De gebruikers interface van HDInsight Ambari bevat een aantal secties **snelle koppelingen** .  Als u de logboek koppelingen voor een bepaalde service in uw HDInsight-cluster wilt openen, opent u de Ambari-gebruikers interface voor uw cluster en selecteert u vervolgens de service koppeling in de lijst links. Selecteer de vervolg keuzelijst **snelle koppelingen** , vervolgens het HDInsight-knoop punt en selecteer vervolgens de koppeling voor het bijbehorende logboek.

Bijvoorbeeld voor HDFS-logboeken:

:::image type="content" source="./media/hdinsight-troubleshoot-failed-cluster/apache-ambari-quick-links.png" alt-text="Ambari snelle koppelingen naar logboek bestanden":::

### <a name="view-hadoop-generated-log-files"></a>Door Hadoop gegenereerde logboek bestanden weer geven

An HDInsight-cluster genereert logboeken die zijn geschreven naar Azure-tabellen en Azure Blob-opslag. GARENs maken eigen uitvoerings Logboeken. Zie [logboeken beheren voor een HDInsight-cluster](hdinsight-log-management.md#access-the-hadoop-log-files)voor meer informatie.

### <a name="review-heap-dumps"></a>Heap-dumps controleren

Heap-dumps bevatten een moment opname van het geheugen van de toepassing, met inbegrip van de waarden van variabelen op dat moment, die handig zijn voor het vaststellen van problemen die tijdens runtime optreden. Zie voor meer informatie [heap-dumps inschakelen voor Apache Hadoop Services op HDInsight op basis van Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md).

## <a name="step-6-check-configuration-settings"></a>Stap 6: configuratie-instellingen controleren

HDInsight-clusters zijn vooraf geconfigureerd met standaard instellingen voor gerelateerde services, zoals Hadoop, Hive, HBase, enzovoort. Afhankelijk van het type cluster, de hardwareconfiguratie, het aantal knoop punten, de typen taken die u uitvoert en de gegevens waarmee u werkt (en hoe die gegevens worden verwerkt), moet u mogelijk uw configuratie optimaliseren.

Zie [cluster configuraties optimaliseren met Apache Ambari](hdinsight-changing-configs-via-ambari.md)voor gedetailleerde instructies voor het optimaliseren van prestatie configuraties voor de meeste scenario's. Als u Spark gebruikt, raadpleegt u [Apache Spark-taken optimaliseren voor prestaties](spark/apache-spark-perf.md). 

## <a name="step-7-reproduce-the-failure-on-a-different-cluster"></a>Stap 7: de fout in een ander cluster reproduceren

Om te helpen bij het vaststellen van de bron van een cluster fout, start u een nieuw cluster met dezelfde configuratie en voert u de stappen van de mislukte taak één voor één opnieuw uit. Controleer de resultaten van elke stap voordat u het volgende gaat verwerken. Deze methode biedt u de mogelijkheid om één mislukte stap te corrigeren en opnieuw uit te voeren. Deze methode biedt ook het voor deel dat u slechts één keer uw invoer gegevens hoeft te laden.

1. Maak een nieuw test cluster met dezelfde configuratie als de mislukte cluster.
2. Verzend de eerste taak stap naar het test cluster.
3. Wanneer de stap is voltooid, controleert u of er fouten zijn opgetreden in de logboek bestanden van de stap. Maak verbinding met het hoofd knooppunt van het test cluster en Bekijk de logboek bestanden daar. De stap logboek bestanden worden alleen weer gegeven nadat de stap enige tijd is uitgevoerd, is voltooid of mislukt.
4. Als de eerste stap is geslaagd, voert u de volgende stap uit. Als er fouten zijn opgetreden, onderzoekt u de fout in de logboek bestanden. Als er een fout is opgetreden in de code, maakt u de correctie en voert u de stap opnieuw uit.
5. Ga door totdat alle stappen zonder fouten worden uitgevoerd.
6. Wanneer u klaar bent met het opsporen van fouten in het test cluster, verwijdert u dit.

## <a name="next-steps"></a>Volgende stappen

* [HDInsight-clusters beheren met behulp van de Apache Ambari-webinterface](hdinsight-hadoop-manage-ambari.md)
* [HDInsight-logboeken analyseren](./hdinsight-troubleshoot-guide.md)
* [Toegang tot Apache Hadoop garen van een toepassing aanmelden op basis van Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Heap-dumps inschakelen voor Apache Hadoop Services op HDInsight op basis van Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Bekende problemen met Apache Spark cluster op HDInsight](./spark/apache-spark-known-issues.md)
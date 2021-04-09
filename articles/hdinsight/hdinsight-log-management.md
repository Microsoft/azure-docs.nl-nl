---
title: Logboeken voor een HDInsight-cluster beheren-Azure HDInsight
description: Bepaal de typen, de grootte en het Bewaar beleid voor de logboek bestanden voor HDInsight-activiteiten.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/05/2020
ms.openlocfilehash: 20a7a7392070a6f888add630398516b2f872cd7d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865482"
---
# <a name="manage-logs-for-an-hdinsight-cluster"></a>Logboeken beheren voor een HDInsight-cluster

An HDInsight cluster produceert diverse logboek bestanden. U kunt bijvoorbeeld Apache Hadoop en gerelateerde services, zoals Apache Spark, gedetailleerde logboeken voor taak uitvoering produceren. Logboek bestands beheer maakt deel uit van het onderhouden van een gezonde HDInsight-cluster. Er kunnen ook wettelijke vereisten zijn voor het archiveren van Logboeken.  Als gevolg van het aantal en de grootte van de logboek bestanden helpt u de opslag en archivering van logboeken te optimaliseren met service kosten beheer.

Het beheren van HDInsight-cluster logboeken omvat het bewaren van informatie over alle aspecten van de cluster omgeving. Deze informatie omvat alle gekoppelde Azure-service logboeken, cluster configuratie, informatie over taak uitvoering, eventuele fout statussen en andere gegevens als dat nodig is.

Veelvoorkomende stappen in het logboek beheer van HDInsight zijn:

* Stap 1: beleid voor het bewaren van Logboeken bepalen
* Stap 2: Configuratie logboeken voor Cluster service versies beheren
* Stap 3: logboek bestanden voor het uitvoeren van cluster taken beheren
* Stap 4: het logboek volume grootte en kosten voor de opslag van volumes
* Stap 5: beleid en processen voor logboek archivering bepalen

## <a name="step-1-determine-log-retention-policies"></a>Stap 1: beleid voor het bewaren van Logboeken bepalen

De eerste stap bij het maken van een logboek beheer strategie voor HDInsight-clusters is het verzamelen van informatie over bedrijfs scenario's en opslag vereisten voor taak uitvoerings geschiedenis.

### <a name="cluster-details"></a>Cluster Details

De volgende cluster Details zijn handig voor het verzamelen van informatie in uw strategie voor logboek beheer. Verzamel deze informatie uit alle HDInsight-clusters die u hebt gemaakt in een bepaald Azure-account.

* Clusternaam
* Cluster regio en Azure-beschikbaarheids zone
* Cluster status, inclusief details van de laatste status wijziging
* Type en aantal HDInsight-instanties dat is opgegeven voor de hoofd-, kern-en taak knooppunten

U kunt de meeste gegevens op het hoogste niveau ophalen met behulp van de Azure Portal.  U kunt ook [Azure cli](/cli/azure/) gebruiken om informatie over uw HDInsight-cluster (en) op te halen:

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

U kunt Power shell ook gebruiken om deze informatie weer te geven.  Zie voor meer informatie [Apache Hadoop-clusters in HDInsight beheren met behulp van Azure PowerShell](hdinsight-administer-use-powershell.md).

### <a name="understand-the-workloads-running-on-your-clusters"></a>Meer informatie over de werk belastingen die worden uitgevoerd op uw clusters

Het is belang rijk om inzicht te krijgen in de werkbelasting typen die op uw HDInsight-cluster (s) worden uitgevoerd om de juiste logboek strategieën voor elk type te ontwerpen.

* Zijn de werk belastingen experimenteel (zoals ontwikkelen of testen) of productie kwaliteit?
* Hoe vaak worden de workloads voor productie kwaliteit normaal uitgevoerd?
* Zijn de resources van de werk belasting intensief en/of langlopend?
* Maken een van de werk belastingen gebruik van een complexe set Hadoop-services waarvoor meerdere typen logboeken worden geproduceerd?
* Hebben een van de werk belastingen gekoppelde afkomst vereisten voor de naleving van de regelgeving?

### <a name="example-log-retention-patterns-and-practices"></a>Voor beelden van Bewaar patronen en-procedures voor logboek registratie

* Overweeg het bijhouden van gegevens afkomst door een id toe te voegen aan elk logboek vermelding of via andere technieken. Zo kunt u de oorspronkelijke bron van de gegevens en de bewerking terugvinden en de gegevens in elke fase volgen om de consistentie en de geldigheid ervan te begrijpen.

* Denk na over het verzamelen van Logboeken vanuit het cluster of van meer dan één cluster, en om ze te sorteren voor doel einden, zoals controle, bewaking, planning en waarschuwingen. U kunt een aangepaste oplossing gebruiken om regel matig de logboek bestanden te openen en te downloaden en ze te combi neren en analyseren om een dashboard weergave te bieden. U kunt ook aanvullende mogelijkheden toevoegen voor waarschuwingen voor beveiliging of fout detectie. U kunt deze hulpprogram ma's samen stellen met behulp van Power shell, de HDInsight Sdk's of de code die toegang heeft tot het klassieke Azure-implementatie model.

* Overweeg of een bewakings oplossing of service een nuttig voor deel zou zijn. Micro soft System Center biedt een [HDInsight-Management Pack](https://systemcenter.wiki/?Get_ManagementPackBundle=Microsoft.HDInsight.mpb&FileMD5=10C7D975C6096FFAA22C84626D211259). U kunt ook hulpprogram ma's van derden, zoals Apache Chukwa en ganglia, gebruiken om logboeken te verzamelen en te centraliseren. Veel bedrijven bieden services om op Hadoop gebaseerde big data oplossingen te bewaken, bijvoorbeeld: Centeriteit, Compuware APM, Sematext SPM en Zettaset Orchestrator.

## <a name="step-2-manage-cluster-service-versions-and-view-logs"></a>Stap 2: Cluster-service versies beheren en logboeken weer geven

Een typisch HDInsight-cluster maakt gebruik van verschillende services en open-source software pakketten (zoals Apache HBase, Apache Spark, enzovoort). Voor sommige werk belastingen, zoals Bioinformatics, moet u mogelijk ook de logboek geschiedenis van de service configuratie behouden naast de logboeken voor taak uitvoering.

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Cluster configuratie-instellingen weer geven met de Ambari-gebruikers interface

Apache Ambari vereenvoudigt het beheer, de configuratie en de controle van een HDInsight-cluster door een web-UI en een REST API op te geven. Ambari is opgenomen in HDInsight-clusters op basis van Linux. Selecteer het deel venster **cluster dashboard** op de pagina Azure Portal HDInsight om de pagina **cluster dashboards** koppeling te openen.  Selecteer vervolgens het deel venster **dash board voor HDInsight-cluster** om de Ambari-gebruikers interface te openen.  U wordt gevraagd om de aanmeldings referenties voor uw cluster.

Als u een lijst met Service weergaven wilt openen, selecteert u het deel venster **Ambari weer gaven** op de pagina Azure portal voor HDInsight.  Deze lijst varieert, afhankelijk van de bibliotheken die u hebt geïnstalleerd.  Zo ziet u een voor beeld van een garen van het wachtrij beheer, Hive-weer gave en TEZ.  Selecteer een service koppeling om de configuratie-en service gegevens te bekijken.  De pagina Ambari UI **stack en version** bevat informatie over de configuratie-en service versie geschiedenis van Cluster Services. Als u wilt navigeren naar deze sectie van de Ambari-gebruikers interface, selecteert u het menu **beheerder** en vervolgens **stacks en versies**.  Selecteer het tabblad **versies** om informatie over de service versie weer te geven.

:::image type="content" source="./media/hdinsight-log-management/ambari-stack-versions.png" alt-text="Apache Ambari-beheer stack en-versies":::

Met de Ambari-gebruikers interface kunt u de configuratie voor alle (of alle) services die worden uitgevoerd op een bepaalde host (of een knoop punt) in het cluster downloaden.  Selecteer het menu **hosts** en vervolgens de koppeling voor de gewenste host. Selecteer op de pagina van de host de knop **acties host** en **down load vervolgens client configuraties**.

:::image type="content" source="./media/hdinsight-log-management/download-client-configs.png" alt-text="Configuratie van Ambari-host-client downloaden van Apache":::

### <a name="view-the-script-action-logs"></a>De script actie logboeken weer geven

Met HDInsight- [script acties](hdinsight-hadoop-customize-cluster-linux.md) worden scripts uitgevoerd op een cluster, hetzij hand matig of indien opgegeven. Script acties kunnen bijvoorbeeld worden gebruikt voor het installeren van extra software op het cluster of voor het wijzigen van de configuratie-instellingen van de standaard waarden. Script actie logboeken kunnen inzicht geven in fouten die zijn opgetreden tijdens de installatie van het cluster, en ook wijzigingen in de configuratie-instellingen die van invloed kunnen zijn op de prestaties en beschik baarheid van het cluster.  Als u de status van een script actie wilt zien, selecteert u de knop **OPS** in uw Ambari-gebruikers interface of opent u de status Logboeken in het standaard opslag account. De opslag logboeken zijn beschikbaar op `/STORAGE_ACCOUNT_NAME/DEFAULT_CONTAINER_NAME/custom-scriptaction-logs/CLUSTER_NAME/DATE` .

### <a name="view-ambari-alerts-status-logs"></a>Status logboeken van Ambari-waarschuwingen weer geven

Met Apache Ambari wordt de waarschuwings status gewijzigd in `ambari-alerts.log` . Het volledige pad is `/var/log/ambari-server/ambari-alerts.log` . Als u fout opsporing wilt inschakelen voor het logboek, wijzigt u een eigenschap in `/etc/ambari-server/conf/log4j.properties.` Change en selecteert u onder `# Log alert state changes` van:

```
log4j.logger.alerts=INFO,alerts

to

log4j.logger.alerts=DEBUG,alerts
```

## <a name="step-3-manage-the-cluster-job-execution-log-files"></a>Stap 3: de logboek bestanden voor de cluster taak uitvoering beheren

De volgende stap is het controleren van de logboek bestanden voor taak uitvoering voor de verschillende services.  Services kunnen Apache HBase, Apache Spark en vele andere zijn. Een Hadoop-cluster produceert een groot aantal uitgebreide logboeken, zodat u kunt bepalen welke logboeken nuttig zijn (en dat niet) het tijdrovender is.  Meer informatie over het logboek registratie systeem is belang rijk voor het beheer van logboek bestanden.  De volgende afbeelding is een voor beeld van een logboek bestand.

:::image type="content" source="./media/hdinsight-log-management/hdi-log-file-example.png" alt-text="Voor beeld van een logboek bestand van HDInsight voorbeeld uitvoer":::

### <a name="access-the-hadoop-log-files"></a>Toegang tot de Hadoop-logboek bestanden

HDInsight slaat de logboek bestanden op in het cluster bestandssysteem en in Azure Storage. U kunt logboek bestanden in het cluster onderzoeken door een [SSH](hdinsight-hadoop-linux-use-ssh-unix.md) -verbinding met het cluster te openen en door het bestands systeem te bladeren of door de Hadoop-status Portal op de externe hoofd knooppunt server te gebruiken. U kunt de logboek bestanden in Azure Storage bekijken met een van de hulpprogram ma's waarmee u gegevens van Azure Storage kunt openen en downloaden. Voor beelden zijn [AzCopy](../storage/common/storage-use-azcopy-v10.md), [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer)en Visual Studio Server Explorer. U kunt ook Power shell en de Azure Storage-client bibliotheken of de Azure .NET-Sdk's gebruiken om toegang te krijgen tot gegevens in Azure Blob-opslag.

Hadoop voert het werk van de taken uit als *taak pogingen* op verschillende knoop punten in het cluster. HDInsight kan speculatieve taak pogingen initiëren en eventuele andere taak pogingen beëindigen die niet eerst worden voltooid. Hiermee genereert u belang rijke activiteiten die op het moment van de-vlucht worden vastgelegd in de controller, stderr en syslog-logboek bestanden. Daarnaast worden meerdere taak pogingen tegelijk uitgevoerd, maar een logboek bestand kan alleen de resultaten lineair weer geven.

#### <a name="hdinsight-logs-written-to-azure-blob-storage"></a>HDInsight-logboeken geschreven naar Azure Blob-opslag

HDInsight-clusters zijn geconfigureerd om taak logboeken te schrijven naar een Azure Blob Storage-account voor elke taak die wordt verzonden met de Azure PowerShell-cmdlets of de .NET taak Submission-Api's.  Als u taken via SSH naar het cluster verzendt, worden de gegevens van de uitvoerings logboek registratie opgeslagen in de Azure-tabellen zoals beschreven in de vorige sectie.

Naast de kern logboek bestanden die door HDInsight worden gegenereerd, genereren geïnstalleerde services zoals GARENs ook logboek bestanden voor taak uitvoering.  Het aantal en het type logboek bestanden zijn afhankelijk van de geïnstalleerde services.  Algemene services zijn Apache HBase, Apache Spark, enzovoort.  Onderzoek de uitvoer bestanden voor de taak logboeken voor elke service om inzicht te krijgen in de algemene logboek bestanden die beschikbaar zijn op uw cluster.  Elke service heeft zijn eigen unieke methoden voor logboek registratie en locaties voor het opslaan van logboek bestanden.  Meer informatie over de toegang tot de meest voorkomende service logboek bestanden (van GARENs) wordt beschreven in de volgende sectie.

### <a name="hdinsight-logs-generated-by-yarn"></a>HDInsight-logboeken die zijn gegenereerd door GARENs

GAREN aggregateert logboeken over alle containers op een worker-knoop punt en slaat deze logboeken op als één geaggregeerd logboek bestand per worker-knoop punt. Het logboek wordt opgeslagen op het standaard bestandssysteem nadat een toepassing is voltooid. Uw toepassing kan honderden of duizenden containers gebruiken, maar logboeken voor alle containers die op één worker-knoop punt worden uitgevoerd, worden altijd geaggregeerd naar één bestand. Er is slechts één logboek per werk knooppunt dat door uw toepassing wordt gebruikt. Logboek aggregatie is standaard ingeschakeld op HDInsight-clusters versie 3,0 en hoger. Samengevoegde logboeken bevinden zich in de standaard opslag voor het cluster.

```
/app-logs/<user>/logs/<applicationId>
```

De geaggregeerde logboeken zijn niet direct leesbaar, omdat ze zijn geschreven in een TFile binaire indeling die is geïndexeerd door container. Gebruik de indelings-of CLI-hulpprogram ma's van de garen om deze logboeken als tekst zonder opmaak te bekijken voor toepassingen of containers die van belang zijn.

#### <a name="yarn-cli-tools"></a>Hulp middelen voor garen-CLI

Als u de hulpprogram ma's voor garen-CLI wilt gebruiken, moet u eerst met SSH verbinding maken met het HDInsight-cluster. Geef de `<applicationId>` `<user-who-started-the-application>` gegevens,, `<containerId>` en op `<worker-node-address>` bij het uitvoeren van deze opdrachten. U kunt de logboeken weer geven als tekst zonder opmaak met een van de volgende opdrachten:

```bash
yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
```

#### <a name="yarn-resourcemanager-ui"></a>De gebruikers interface van garen

De gebruikers interface van de garen-server wordt uitgevoerd op het hoofd knooppunt van het cluster en is toegankelijk via de Ambari-webgebruikersinterface. Voer de volgende stappen uit om de GARENs-logboeken weer te geven:

1. Navigeer in een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net`. Vervang CLUSTERNAME door de naam van uw HDInsight-cluster.
2. Selecteer in de lijst met services aan de linkerkant de optie GARENs.
3. Selecteer in de vervolg keuzelijst snelle koppelingen een van de cluster hoofd knooppunten en selecteer vervolgens de weer gave van het **Resource Manager-logboek**. Er wordt een lijst weer gegeven met koppelingen naar GARENs in de logboeken.

## <a name="step-4-forecast-log-volume-storage-sizes-and-costs"></a>Stap 4: het logboek volume grootte en kosten voor de opslag van volumes

Nadat u de voor gaande stappen hebt voltooid, hebt u een goed idee van de typen en volumes van de logboek bestanden die uw HDInsight-cluster (en) produceert.

Analyseer daarna het volume van de logboek gegevens in de opslag locaties van het sleutel logboek gedurende een bepaalde tijd. U kunt bijvoorbeeld het volume en de groei gedurende 30-60-90 dagen analyseren.  Neem deze informatie op in een werk blad of gebruik andere hulpprogram ma's, zoals Visual Studio, de Azure Storage Explorer of Power Query voor Excel. ```

U hebt nu voldoende informatie om een strategie voor logboek beheer voor de sleutel logboeken te maken.  Gebruik uw werk blad (of hulp programma) om zowel de groei van de logboek grootte als de logboek opslag van Azure-service kosten in de toekomst te ramen.  Houd ook rekening met de vereisten voor het bewaren van Logboeken voor de set met logboeken die u wilt onderzoeken.  Nu kunt u toekomstige kosten voor de opslag van het logboek opnieuw ramen nadat u hebt bepaald welke logboek bestanden kunnen worden verwijderd (indien van toepassing) en welke logboeken moeten worden bewaard en naar minder dure Azure Storage worden gearchiveerd.

## <a name="step-5-determine-log-archive-policies-and-processes"></a>Stap 5: beleid en processen voor logboek archivering bepalen

Nadat u hebt vastgesteld welke logboek bestanden kunnen worden verwijderd, kunt u de logboek registratie parameters op veel Hadoop-Services aanpassen om logboek bestanden na een bepaalde periode automatisch te verwijderen.

Voor bepaalde logboek bestanden kunt u gebruikmaken van een gereduceerde aanpak voor het archiveren van het logboek bestand. Voor Azure Resource Manager activiteiten Logboeken kunt u deze aanpak verkennen met behulp van de Azure Portal.  Stel de archivering van de Resource Manager-Logboeken in door de koppeling **activiteiten logboek** te selecteren in het Azure portal voor uw HDInsight-exemplaar.  Selecteer aan de bovenkant van de pagina zoeken in het activiteiten logboek het menu-item **exporteren** om het deel venster **activiteiten logboek exporteren** te openen.  Vul het abonnement, de regio, in of u wilt exporteren naar een opslag account en hoeveel dagen de logboeken moeten worden bewaard. In dit deel venster kunt u ook aangeven of u wilt exporteren naar een Event Hub.

:::image type="content" source="./media/hdinsight-log-management/hdi-export-log-files.png" alt-text="Preview van Azure Portal-activiteiten logboek exporteren":::

U kunt ook scripts archiveren in het logboek met Power shell.  Zie [Azure Automation logboeken archiveren in Azure Blob Storage](https://gallery.technet.microsoft.com/scriptcenter/Archive-Azure-Automation-898a1aa8)voor een voor beeld van een Power shell-script.

### <a name="accessing-azure-storage-metrics"></a>Toegang tot Azure Storage metrische gegevens

Azure Storage kunnen worden geconfigureerd voor het vastleggen van opslag bewerkingen en toegang. U kunt deze zeer gedetailleerde logboeken gebruiken voor het bewaken van capaciteit en het plannen van aanvragen voor opslag. De vastgelegde informatie bevat latentie Details, waarmee u de prestaties van uw oplossingen kunt bewaken en verfijnen.
U kunt de .NET SDK voor Hadoop gebruiken om de logboek bestanden te controleren die zijn gegenereerd voor de Azure Storage die de gegevens voor een HDInsight-cluster bevatten.

### <a name="control-the-size-and-number-of-backup-indexes-for-old-log-files"></a>De grootte en het aantal back-upindexen voor oude logboek bestanden bepalen

Als u de grootte en het aantal logboek bestanden dat moet worden behouden wilt beheren, stelt u de volgende eigenschappen in `RollingFileAppender` :

* `maxFileSize` is de kritieke grootte van het bestand, waarboven het bestand wordt getotaliseerd. De standaard waarde is 10 MB.
* `maxBackupIndex` Hiermee geeft u het aantal back-upbestanden dat moet worden gemaakt, standaard 1.

### <a name="other-log-management-techniques"></a>Andere technieken voor logboek beheer

Om te voor komen dat er onvoldoende schijf ruimte beschikbaar is, kunt u bepaalde hulpprogram ma's voor het besturings systeem, zoals [logrotate](https://linux.die.net/man/8/logrotate) , gebruiken om de verwerking van logboek bestanden te beheren. U kunt configureren `logrotate` om dagelijks uit te voeren, logboek bestanden comprimeren en oude verwijderen. Uw aanpak is afhankelijk van uw vereisten, zoals hoe lang de logboek bestanden op lokale knoop punten bewaard moeten blijven.  

U kunt ook controleren of logboek registratie voor fout opsporing is ingeschakeld voor een of meer services, waardoor de grootte van het uitvoer logboek aanzienlijk toeneemt.  

Als u de logboeken van alle knoop punten op één centrale locatie wilt verzamelen, kunt u een gegevens stroom maken, zoals het opnemen van alle logboek vermeldingen in solr.

## <a name="next-steps"></a>Volgende stappen

* [Bewakings-en logboek registratie praktijken voor HDInsight](/previous-versions/msp-n-p/dn749790(v=pandp.10))
* [Toegang tot Apache Hadoop GARENs van toepassings Logboeken in HDInsight op basis van Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [De grootte van logboek bestanden voor verschillende Apache Hadoop-onderdelen beheren](https://community.hortonworks.com/articles/8882/how-to-control-size-of-log-files-for-various-hdp-c.html)

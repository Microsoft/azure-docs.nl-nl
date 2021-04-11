---
title: Veelgestelde vragen over Azure HDInsight
description: Veelgestelde vragen over HDInsight
keywords: Veelgestelde vragen
author: Ramakoni1
ms.author: ramakoni
ms.service: hdinsight
ms.custom: hdinsightactive,seoapr2020
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: 47f0ea34fa650371d39252ce6b0ee6a15f3124d6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871177"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight: veelgestelde vragen

In dit artikel vindt u antwoorden op enkele van de meest voorkomende vragen over het uitvoeren van [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="creating-or-deleting-hdinsight-clusters"></a>HDInsight-clusters maken of verwijderen

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>Een HDInsight-cluster Hoe kan ik inrichten?

Zie [clusters instellen in HDInsight met Apache Hadoop, Apache Spark, Apache Kafka en meer](./hdinsight-hadoop-provision-linux-clusters.md)om de typen HDInsight-clusters en de inrichtings methoden te bekijken.

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>Hoe kan ik een bestaand HDInsight-cluster verwijderen?

Zie [een HDInsight-cluster verwijderen](hdinsight-delete-cluster.md)voor meer informatie over het verwijderen van een cluster dat niet meer in gebruik is.

Probeer ten minste 30 tot 60 minuten tussen Create-en delete-bewerkingen te laten staan. Anders kan de bewerking mislukken met het volgende fout bericht:

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>Hoe kan ik selecteert u het juiste aantal kernen of knoop punten voor mijn werk belasting?

Het juiste aantal kern geheugens en andere configuratie opties zijn afhankelijk van verschillende factoren.

Zie [capaciteits planning voor HDInsight-clusters](./hdinsight-capacity-planning.md)voor meer informatie.

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>Wat zijn de verschillende typen knoop punten in een HDInsight-cluster?

Zie [resource typen in azure HDInsight-clusters](hdinsight-virtual-network-architecture.md#resource-types-in-azure-hdinsight-clusters).

### <a name="what-are-the-best-practices-for-creating-large-hdinsight-clusters"></a>Wat zijn de aanbevolen procedures voor het maken van grote HDInsight-clusters?

1. Het is raadzaam HDInsight-clusters in te stellen met een [aangepaste Ambari-data base](./hdinsight-custom-ambari-db.md) om de schaal baarheid van het cluster te verbeteren.
2. Gebruik [Azure data Lake Storage Gen2](./hdinsight-hadoop-use-data-lake-storage-gen2.md) om HDInsight-clusters te maken om te profiteren van meer band breedte en andere prestatie kenmerken van Azure data Lake Storage Gen2.
3. Hoofd knooppunten moet voldoende groot zijn om meerdere Master services te kunnen uitvoeren op deze knoop punten.
4. Bepaalde werk belastingen, zoals interactieve Query's, hebben ook grotere Zookeeper-knoop punten nodig. Houd rekening met mini maal 8 kern-Vm's.
5. Gebruik [externe Hive-metastore](./hdinsight-use-external-metadata-stores.md)in het geval van Hive en Spark.

## <a name="individual-components"></a>Afzonderlijke onderdelen

### <a name="can-i-install-additional-components-on-my-cluster"></a>Kan ik extra onderdelen installeren op mijn cluster?

Ja. Als u extra onderdelen wilt installeren of cluster configuratie wilt aanpassen, gebruikt u:

- Scripts tijdens of na het maken. Scripts worden aangeroepen via [script acties](./hdinsight-hadoop-customize-cluster-linux.md). Script actie is een configuratie optie die u kunt gebruiken vanuit de Azure Portal, HDInsight Windows Power shell-cmdlets of de HDInsight .NET SDK. Deze configuratie optie kan worden gebruikt vanuit de Azure Portal, HDInsight Windows Power shell-cmdlets of de HDInsight .NET SDK.

- [HDInsight-toepassings platform](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) voor het installeren van toepassingen.

Zie [Wat zijn de Apache Hadoop onderdelen en versies die beschikbaar zijn in HDInsight?](./hdinsight-component-versioning.md) voor een lijst met ondersteunde onderdelen?

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>Kan ik een upgrade uitvoeren voor de afzonderlijke onderdelen die vooraf zijn geïnstalleerd op het cluster?

Als u een upgrade uitvoert van ingebouwde onderdelen of toepassingen die vooraf zijn geïnstalleerd op uw cluster, wordt de resulterende configuratie niet ondersteund door micro soft. Deze systeem configuraties zijn niet getest door micro soft. Probeer een andere versie van het HDInsight-cluster te gebruiken waarop mogelijk al de bijgewerkte versie van het onderdeel is geïnstalleerd.

Een upgrade van Hive als een afzonderlijk onderdeel wordt bijvoorbeeld niet ondersteund. HDInsight is een beheerde service en veel services zijn geïntegreerd met Ambari-server en getest. Als u een Hive op zichzelf bijwerkt, worden de geïndexeerde binaire bestanden van andere onderdelen gewijzigd en ontstaan er problemen met de integratie van onderdelen in uw cluster.

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>Kan Spark en Kafka worden uitgevoerd op hetzelfde HDInsight-cluster?

Nee, het is niet mogelijk om Apache Kafka en Apache Spark uit te voeren op hetzelfde HDInsight-cluster. Maak afzonderlijke clusters voor Kafka en Spark om problemen met bron conflicten te voor komen.

### <a name="how-do-i-change-timezone-in-ambari"></a>Hoe kan ik tijd zone wijzigen in Ambari?

1. Open de Ambari-webgebruikersinterface op `https://CLUSTERNAME.azurehdinsight.net` , waarbij clustername de naam van uw cluster is.
2. Selecteer in de rechter bovenhoek de optie beheer | Instellingen. 

   :::image type="content" source="media/hdinsight-faq/ambari-settings.png" alt-text="Ambari-instellingen":::

3. Selecteer in het venster gebruikers instellingen de nieuwe tijd zone in de vervolg keuzelijst tijd zone en klik vervolgens op opslaan.

   :::image type="content" source="media/hdinsight-faq/ambari-user-settings.png" alt-text="Gebruikers instellingen Ambari":::

## <a name="metastore"></a>Metastore

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-database"></a>Hoe kan ik migreren van de bestaande meta Store naar Azure SQL Database? 

Als u wilt migreren van SQL Server naar Azure SQL Database, raadpleegt u [zelf studie: SQL Server migreren naar één data base of gegroepeerde Data base in Azure SQL database offline met behulp van DMS](../dms/tutorial-sql-server-to-azure-sql.md).

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>Wordt het Hive-metastore verwijderd wanneer het cluster wordt verwijderd?

Dit is afhankelijk van het type meta Store dat uw cluster heeft geconfigureerd voor gebruik.

Voor een standaard-META Store: de standaard-META Store maakt deel uit van de cluster levenscyclus. Wanneer u een cluster verwijdert, worden ook de corresponderende meta Store-gegevens verwijderd.

Voor een aangepaste meta Store: de levens cyclus van de meta Store is niet gebonden aan de levens cyclus van een cluster. U kunt dus clusters maken en verwijderen zonder dat meta gegevens verloren gaan. Meta gegevens zoals uw Hive-schema's blijven behouden, zelfs nadat u het HDInsight-cluster hebt verwijderd en opnieuw hebt gemaakt.

Zie [externe meta gegevens archieven gebruiken in azure HDInsight](hdinsight-use-external-metadata-stores.md)voor meer informatie.

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>Migreert migratie van een Hive-metastore ook het standaard beleid van de zwerver-data base?

Nee, de beleids definitie bevindt zich in de zwerver-data base, dus migreert de zwerver-data base het beleid.

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-the-other-way-around"></a>Kunt u een Hive-metastore van een Enterprise Security Package (ESP)-cluster migreren naar een niet-ESP-cluster en de andere manier om het probleem op te lossen?

Ja, u kunt een Hive-metastore migreren van een ESP naar een niet-ESP-cluster.

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Hoe kan ik een schatting maken van de omvang van een Hive-metastore-data base?

Een Hive-metastore wordt gebruikt voor het opslaan van de meta gegevens voor gegevens bronnen die door de Hive-server worden gebruikt. De grootte vereisten zijn deels afhankelijk van het aantal en de complexiteit van uw Hive-gegevens bronnen. Deze items kunnen niet vooraf worden geschat. Zoals beschreven in Hive-metastore- [richt lijnen](hdinsight-use-external-metadata-stores.md#hive-metastore-guidelines), kunt u beginnen met een S2-laag. De laag biedt 50 DTU en 250 GB opslag. Als u een knel punt ziet, schaalt u de data base.

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>Ondersteunt u andere data bases dan Azure SQL Database als een externe meta Store?

Nee, micro soft ondersteunt alleen Azure SQL Database als een externe, aangepaste meta Store.

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>Kan ik een meta Store over meerdere clusters delen?

Ja, u kunt een aangepaste meta Store delen in meerdere clusters, zolang ze dezelfde versie van HDInsight gebruiken.

## <a name="connectivity-and-virtual-networks"></a>Connectiviteit en virtuele netwerken  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>Wat zijn de implicaties van het blok keren van poorten 22 en 23 op mijn netwerk?

Als u poort 22 en poort 23 blokkeert, hebt u geen SSH-toegang tot het cluster. Deze poorten worden niet gebruikt door de HDInsight-service.

Raadpleeg de volgende documenten voor meer informatie:

- [Poorten die worden gebruikt door Apache Hadoop Services in HDInsight](./hdinsight-hadoop-port-settings-for-services.md)

- [Binnenkomend verkeer naar HDInsight-clusters in een virtueel netwerk met een persoonlijk eind punt beveiligen](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)

- [IP-adressen beheren met HDInsight](./hdinsight-management-ip-addresses.md)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>Kan ik een extra virtuele machine implementeren in hetzelfde subnet als een HDInsight-cluster?

Ja, u kunt een extra virtuele machine implementeren binnen hetzelfde subnet als een HDInsight-cluster. De volgende configuraties zijn mogelijk:

- Edge-knoop punten: u kunt een ander Edge-knoop punt toevoegen aan het cluster, zoals wordt beschreven in [lege Edge-knoop punten gebruiken op Apache Hadoop clusters in HDInsight](hdinsight-apps-use-edge-node.md).

- Zelfstandige knoop punten: u kunt een zelfstandige virtuele machine toevoegen aan hetzelfde subnet en vanaf die virtuele machine toegang krijgen tot het cluster met behulp van het persoonlijke eind punt `https://<CLUSTERNAME>-int.azurehdinsight.net` . Zie [netwerk verkeer beheren](./control-network-traffic.md)voor meer informatie.

### <a name="should-i-store-data-on-the-local-disk-of-an-edge-node"></a>Moet ik gegevens opslaan op de lokale schijf van een Edge-knoop punt?

Nee, het is niet een goed idee om gegevens op een lokale schijf op te slaan. Als het knoop punt uitvalt, gaan alle lokaal opgeslagen gegevens verloren. Het is raadzaam om gegevens op te slaan in Azure Data Lake Storage Gen2 of Azure Blob-opslag of door een Azure Files share te koppelen voor het opslaan van de gegevens.


### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>Kan ik een bestaand HDInsight-cluster toevoegen aan een ander virtueel netwerk?

Nee, dat kan niet. Het virtuele netwerk moet worden opgegeven op het moment van de inrichting. Als er tijdens het inrichten geen virtueel netwerk is opgegeven, maakt de implementatie een intern netwerk dat niet toegankelijk is vanaf buiten. Zie [HDInsight toevoegen aan een bestaand virtueel netwerk](hdinsight-plan-virtual-network-deployment.md#existingvnet)voor meer informatie.

## <a name="security-and-certificates"></a>Beveiliging en certificaten

### <a name="what-are-the-recommendations-for-malware-protection-on-azure-hdinsight-clusters"></a>Wat zijn de aanbevelingen voor beveiliging tegen schadelijke software in azure HDInsight-clusters?

Zie [micro soft antimalware voor Azure Cloud Services en virtual machines](../security/fundamentals/antimalware.md)voor informatie over beveiliging tegen schadelijke software.

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>Hoe kan ik een keytab voor een HDInsight ESP-cluster maken?

Maak een Kerberos-keytab voor de gebruikers naam van uw domein. U kunt deze keytab later gebruiken om te verifiëren bij clusters die zijn toegevoegd aan een extern domein zonder een wacht woord in te voeren. De domein naam is hoofdletter:

```shell
ktutil
ktutil: addent -password -p <username>@<DOMAIN.COM> -k 1 -e RC4-HMAC
Password for <username>@<DOMAIN.COM>: <password>
ktutil: wkt <username>.keytab
ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>Kan ik een bestaande Azure Active Directory Tenant gebruiken om een HDInsight-cluster te maken dat de ESP heeft?

Schakel Azure Active Directory Domain Services (Azure AD DS) in voordat u een HDInsight-cluster met ESP kunt maken. Open-source Hadoop is afhankelijk van Kerberos voor authenticatie (in plaats van OAuth).

Als u Vm's wilt toevoegen aan een domein, moet u een domein controller hebben. Azure AD DS is de beheerde domein controller en wordt beschouwd als een uitbrei ding van Azure Active Directory. Azure AD DS biedt alle Kerberos-vereisten om een veilig Hadoop-cluster op een beheerde manier te bouwen. HDInsight als een beheerde service kan worden geïntegreerd met Azure AD DS om beveiliging te bieden.

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>Kan ik een zelfondertekend certificaat gebruiken in een beveiligde LDAP-installatie van AAD-DS en een ESP-cluster inrichten?

U wordt aangeraden een certificaat te gebruiken dat is uitgegeven door een certificerings instantie. Maar het gebruik van een zelfondertekend certificaat wordt ook ondersteund op ESP. Zie voor meer informatie:

- [Azure Active Directory Domain Services inschakelen](domain-joined/apache-domain-joined-configure-using-azure-adds.md#enable-azure-ad-ds)

- [Zelfstudie: Secure LDAP configureren voor een door Azure Active Directory Domain Services beheerd domein](../active-directory-domain-services/tutorial-configure-ldaps.md)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>Hoe kan ik de aanmeldings activiteiten in zwerver weer geven?

Voor controle vereisten raadt micro soft aan Azure Monitor Logboeken in te scha kelen zoals beschreven in [Azure monitor Logboeken gebruiken om HDInsight-clusters te controleren](./hdinsight-hadoop-oms-log-analytics-tutorial.md).

### <a name="can-i-disable-clamscan-on-my-cluster"></a>Kan ik uitschakelen `Clamscan` op mijn cluster?

`Clamscan` is de antivirus software die wordt uitgevoerd op het HDInsight-cluster en wordt gebruikt door Azure Security (azsecd) om uw clusters te beschermen tegen virus aanvallen. Micro soft raadt u ten zeerste aan dat gebruikers geen wijzigingen aanbrengen in de standaard `Clamscan` configuratie.

Dit proces heeft geen invloed op de conflicten met of het uitvoeren van andere processen. Er wordt altijd naar een ander proces geoogst. CPU-pieken van `Clamscan` moeten alleen worden gezien wanneer het systeem niet actief is.  

In scenario's waarin u het schema moet beheren, kunt u de volgende stappen uitvoeren:

1. Schakel automatische uitvoering uit met de volgende opdracht:
   
  sudo `usr/local/bin/azsecd config -s clamav -d Disabled` sudo service azsecd restart 
   
1. Voeg een cron-taak toe die de volgende opdracht als root uitvoert:
   
   `/usr/local/bin/azsecd manual -s clamav`

Zie [Hoe kan ik een cron instellen](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)voor meer informatie over het instellen en uitvoeren van een cron-taak?

### <a name="why-is-llap-available-on-spark-esp-clusters"></a>Waarom is LLAP beschikbaar in Spark ESP-clusters?
LLAP is om veiligheids redenen (Apache zwerver), geen prestaties. Gebruik grotere Vm's voor knoop punten voor het resource gebruik van LLAP (bijvoorbeeld minimale D13V2). 

### <a name="how-can-i-add-additional-aad-groups-after-creating-an-esp-cluster"></a>Hoe kan ik extra AAD-groepen toevoegen nadat ik een ESP-cluster heb gemaakt?
Er zijn twee manieren om dit doel te verzorgen: 1. u kunt het cluster opnieuw maken en de extra groep toevoegen op het moment dat het cluster wordt gemaakt. Als u met scoped Synchronization in AAD-DS gebruikt, moet u ervoor zorgen dat groep B is opgenomen in de synchronisatie met het bereik.
2: Voeg de groep toe als een geneste subgroep van de vorige groep die is gebruikt voor het maken van het ESP-cluster. Als u bijvoorbeeld een ESP-cluster met groep hebt gemaakt `A` , kunt u later op groep toevoegen `B` als geneste subgroep van `A` en na ongeveer één uur dat deze automatisch wordt gesynchroniseerd en beschikbaar in het cluster. 

## <a name="storage"></a>Storage

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>Kan ik een Azure Data Lake Storage Gen2 toevoegen aan een bestaand HDInsight-cluster als een extra opslag account?

Nee, het is momenteel niet mogelijk om een Azure Data Lake Storage Gen2 Storage-account toe te voegen aan een cluster met Blob Storage als primaire opslag. Zie voor meer informatie [opslag opties vergelijken](hdinsight-hadoop-compare-storage-options.md).

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Hoe kan ik de momenteel gekoppelde Service-Principal vinden voor een Data Lake Storage-account?

U kunt uw instellingen vinden in **Data Lake Storage gen1 toegang** onder de cluster eigenschappen in de Azure Portal. Zie [cluster installatie controleren](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#verify-cluster-set-up)voor meer informatie.
 
### <a name="how-can-i-calculate-the-usage-of-storage-accounts-and-blob-containers-for-my-hdinsight-clusters"></a>Hoe kan ik het gebruik van opslag accounts en BLOB-containers berekenen voor mijn HDInsight-clusters?

Voer een van de volgende acties uit:

- [PowerShell gebruiken](../storage/scripts/storage-blobs-container-calculate-size-powershell.md)

- De grootte van de */User/Hive/. zoeken Prullen mand/* map op het HDInsight-cluster met behulp van de volgende opdracht regel:
  
  `hdfs dfs -du -h /user/hive/.Trash/`

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>Hoe kan ik controle instellen voor mijn Blob Storage-account?

Als u Blob Storage-accounts wilt controleren, configureert u bewaking met behulp van de procedure bij het [controleren van een opslag account in de Azure Portal](../storage/common/manage-storage-analytics-logs.md). Een HDFS-audit logboek biedt alleen controle-informatie voor het lokale bestands systeem HDFS (hdfs://mycluster).  Het bevat geen bewerkingen die worden uitgevoerd op externe opslag.

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>Hoe kan ik bestanden overdragen tussen een BLOB-container en een HDInsight-hoofd knooppunt?

Voer een script uit dat vergelijkbaar is met het volgende shell script op het hoofd knooppunt:

```shell
for i in cat filenames.txt
do
   hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> Het bestand *filenames.txt* heeft het absolute pad van de bestanden in de BLOB-containers.
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>Zijn er zwerver-invoeg toepassingen voor opslag?

Momenteel bestaat er geen zwerver-invoeg toepassing voor Blob Storage en Azure Data Lake Storage Gen1 of Gen2. Voor ESP-clusters moet u Azure Data Lake Storage gebruiken. U kunt Mini maal nauw keurige machtigingen hand matig instellen op het bestandssysteem niveau met behulp van de gereedschappen van HDFS. Bij het gebruik van Azure Data Lake Storage worden ESP-clusters een deel van het toegangs beheer voor bestands systemen met behulp van Azure Active Directory op cluster niveau. 

U kunt beleid voor gegevens toegang toewijzen aan de beveiligings groepen van uw gebruikers met behulp van de Azure Storage Explorer. Zie voor meer informatie:

- [Hoe kan ik machtigingen instellen voor Azure AD-gebruikers om gegevens op te vragen in Data Lake Storage Gen2 met behulp van Hive of andere services?](hdinsight-hadoop-use-data-lake-storage-gen2.md#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
- [Machtigingen op bestands- en mapniveau instellen voor Azure Data Lake Storage Gen2 met behulp van Azure Storage Explorer](../storage/blobs/data-lake-storage-explorer.md)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>Kan ik de HDFS-opslag op een cluster verg Roten zonder de schijf grootte van worker-knoop punten te verhogen?

Nee. U kunt de schijf grootte van geen enkel werk knooppunt verg Roten. Het is dus de enige manier om de schijf grootte te verg Roten door het cluster te verwijderen en opnieuw te maken met grotere werk-Vm's. Gebruik geen HDFS om uw HDInsight-gegevens op te slaan, omdat de gegevens worden verwijderd als u het cluster verwijdert. In plaats daarvan slaat u uw gegevens op in Azure. Door het cluster omhoog te schalen, kunt u ook extra capaciteit toevoegen aan uw HDInsight-cluster.

## <a name="edge-nodes"></a>Edge-knooppunten

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>Kan ik een Edge-knoop punt toevoegen nadat het cluster is gemaakt?

Zie [lege Edge-knoop punten gebruiken op Apache Hadoop clusters in HDInsight](hdinsight-apps-use-edge-node.md).

### <a name="how-can-i-connect-to-an-edge-node"></a>Hoe kan ik verbinding maken met een Edge-knoop punt?

Nadat u een Edge-knoop punt hebt gemaakt, kunt u er verbinding mee maken via SSH op poort 22. U kunt de naam van het Edge-knoop punt vinden in de cluster Portal. De namen eindigen meestal met *-Ed*.

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>Waarom worden persistente scripts niet automatisch uitgevoerd op nieuw gemaakte Edge-knoop punten?

U gebruikt persistente scripts om nieuwe worker-knoop punten die aan het cluster worden toegevoegd, aan te passen via schaal bewerkingen. Persistente scripts zijn niet van toepassing op Edge-knoop punten.

## <a name="rest-api"></a>REST-API

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>Wat zijn de REST API-aanroepen om een TEZ-query weergave uit het cluster te halen?

U kunt de volgende REST-eind punten gebruiken om de benodigde gegevens in JSON-indeling te halen. Basis verificatie headers gebruiken om de aanvragen uit te voeren.

- `Tez Query View`: *https: \/ / \<cluster name> . azurehdinsight.net/WS/v1/Timeline/HIVE_QUERY_ID/*
- `Tez Dag View`: *https: \/ / \<cluster name> . azurehdinsight.net/WS/v1/Timeline/TEZ_DAG_ID/*

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Hoe kan ik de configuratie gegevens uit het HDI-cluster op te halen met behulp van een Azure Active Directory gebruiker?

Als u de juiste verificatie tokens met uw AAD-gebruiker wilt onderhandelen, gaat u door de gateway via de volgende indeling:

* https:// `<cluster dnsname>` . azurehdinsight.net/API/v1/clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Hoe kan ik Ambari rest API gebruiken om de prestaties van een garen te controleren?

Als u de krul opdracht aanroept in hetzelfde virtuele netwerk of in een gekoppeld virtueel netwerk, is de opdracht:

```curl
curl -u <cluster login username> -sS -G
http://<headnodehost>:8080/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
```
 
Als u de opdracht van buiten het virtuele netwerk of vanuit een niet-gekoppeld virtueel netwerk aanroept, is de opdracht indeling:

- Voor een niet-ESP-cluster:
  
  ```curl
  curl -u <cluster login username> -sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

- Voor een ESP-cluster:
  
  ```curl
  curl -u <cluster login username>-sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

> [!NOTE]
> Met krul wordt u om een wacht woord gevraagd. U moet een geldig wacht woord opgeven voor de gebruikers naam van het cluster.

## <a name="billing"></a>Billing

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>Hoeveel kost het om een HDInsight-cluster te implementeren?

Zie de pagina met [prijzen voor Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) voor meer informatie over de prijzen en veelgestelde vragen met betrekking tot facturering.

### <a name="when-does-hdinsight-billing-start--stop"></a>Wanneer wordt de facturering van HDInsight gestart & stoppen?

De facturering voor het gebruik van HDInsight-clusters begint zodra er een cluster is gemaakt en stopt als een cluster wordt verwijderd. De facturering is Pro-beoordeeld per minuut.

### <a name="how-do-i-cancel-my-subscription"></a>Hoe kan ik mijn abonnement annuleren?

Zie [uw Azure-abonnement annuleren](../cost-management-billing/manage/cancel-azure-subscription.md)voor meer informatie over het annuleren van uw abonnement.

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>Wat gebeurt er nadat ik mijn abonnement heb geannuleerd? voor betalen per gebruik-abonnementen?

Zie [Wat gebeurt er nadat ik mijn abonnement heb geannuleerd?](../cost-management-billing/manage/cancel-azure-subscription.md) voor informatie over uw abonnement nadat dit is geannuleerd?

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-im-running-an-hdinsight-36-cluster"></a>Waarom wordt de Hive-versie weer gegeven als 1.2.1000 in plaats van 2,1 in de Ambari-gebruikers interface, zelfs al wordt er een HDInsight 3,6-cluster uitgevoerd?

Hoewel alleen 1,2 wordt weer gegeven in de Ambari-gebruikers interface, bevat HDInsight 3,6 zowel Hive 1,2 als hive 2,1.

## <a name="other-faq"></a>Overige veelgestelde vragen

### <a name="what-does-hdinsight-offer-for-real-time-stream-processing-capabilities"></a>Wat biedt HDInsight voor realtime stroom verwerkings mogelijkheden?

Zie [een stroom verwerkings technologie kiezen in azure](/azure/architecture/data-guide/technology-choices/stream-processing)voor meer informatie over de integratie mogelijkheden van stroom verwerking.

### <a name="is-there-a-way-to-dynamically-kill-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>Is er een manier om het hoofd knooppunt van het cluster dynamisch te beëindigen wanneer het cluster gedurende een bepaalde periode niet actief is?

U kunt deze actie niet uitvoeren met HDInsight-clusters. U kunt Azure Data Factory voor deze scenario's gebruiken.

### <a name="what-compliance-offerings-does-hdinsight-offer"></a>Welke compliance-aanbiedingen biedt HDInsight-aanbieding?

Zie het [vertrouwens centrum van micro soft](https://www.microsoft.com/trust-center) en het [overzicht van Microsoft Azure naleving](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)voor nalevings informatie.
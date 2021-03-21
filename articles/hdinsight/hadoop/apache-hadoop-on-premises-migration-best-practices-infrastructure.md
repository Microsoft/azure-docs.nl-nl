---
title: 'Infra structuur: on-premises Apache Hadoop naar Azure HDInsight'
description: Ontdek de aanbevolen procedures voor de infra structuur voor het migreren van on-premises Hadoop-clusters naar Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 309053c2d7a0f9482016f1bd83e0c61dcd31bec5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740661"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---infrastructure-best-practices"></a>On-premises Apache Hadoop clusters migreren naar de aanbevolen procedures voor Azure HDInsight-infra structuur

Dit artikel bevat aanbevelingen voor het beheren van de infra structuur van Azure HDInsight-clusters. Het is onderdeel van een serie die aanbevolen procedures biedt voor het migreren van on-premises Apache Hadoop systemen naar Azure HDInsight.

## <a name="plan-for-hdinsight-cluster-capacity"></a>Capaciteit voor HDInsight-cluster plannen

De belangrijkste keuzes voor het maken van de capaciteits planning voor HDInsight-clusters zijn als volgt:

**Regio**  
De Azure-regio bepaalt waar het cluster fysiek wordt ingericht. Het cluster moet zich in dezelfde regio bevinden als de gegevens om de latentie van lees-en schrijf bewerkingen te minimaliseren.

**Opslag locatie en-grootte**  
De standaard opslag moet zich in dezelfde regio bevinden als het cluster. Voor een cluster met een 48-knoop punt is het raadzaam 4 tot 8 opslag accounts te hebben. Hoewel er mogelijk al voldoende totale opslag ruimte beschikbaar is, biedt elk opslag account extra netwerk bandbreedte voor de reken knooppunten. Als er meerdere opslag accounts zijn, gebruikt u een wille keurige naam voor elk opslag account, zonder voor voegsel. Het doel van een wille keurige naam is het verminderen van de kans op opslag knelpunten (beperking) of fouten in de algemene modus voor alle accounts. Gebruik slechts één container per opslag account voor betere prestaties.

**VM-grootte en-type (ondersteunt nu de G-serie)**  
Elk cluster type heeft een reeks knooppunt typen en elk knooppunt type heeft specifieke opties voor de VM-grootte en het bijbehorende type. De grootte en het type van de virtuele machine worden bepaald door CPU-verwerkings kracht, RAM-grootte en netwerk latentie. Een gesimuleerde werk belasting kan worden gebruikt om de optimale grootte en het type van de virtuele machine voor elke knooppunt typen te bepalen.

**Aantal worker-knoop punten**  
Het oorspronkelijke aantal worker-knoop punten kan worden bepaald met behulp van de gesimuleerde werk belastingen. Het cluster kan later worden geschaald door meer worker-knoop punten toe te voegen om te voldoen aan de maximale belasting vereisten. Het cluster kan later weer worden geschaald wanneer de extra worker-knoop punten niet vereist zijn.

Zie het artikel [capaciteits planning voor HDInsight-clusters](../hdinsight-capacity-planning.md)voor meer informatie.

## <a name="use-recommended-virtual-machine-type-for-cluster"></a>Aanbevolen type virtuele machine voor cluster gebruiken

Zie [standaard knooppunt configuratie en grootte van virtuele machines voor clusters](../hdinsight-supported-node-configuration.md) voor aanbevolen typen virtuele machines voor elk type HDInsight-cluster.

## <a name="check-hadoop-components-availability-in-hdinsight"></a>Beschik baarheid van Hadoop-onderdelen in HDInsight controleren

Elke HDInsight-versie is een Cloud distributie van een set Hadoop-eco-systeem onderdelen. Zie [versie beheer van hdinsight-onderdelen](../hdinsight-component-versioning.md) voor meer informatie over alle HDInsight-onderdelen en de huidige versies.

U kunt ook Apache Ambari UI of Ambari REST API gebruiken om de Hadoop-onderdelen en versies in HDInsight te controleren.

Toepassingen of onderdelen die beschikbaar waren in on-premises clusters, maar die geen deel uitmaken van de HDInsight-clusters kunnen worden toegevoegd aan een Edge-knoop punt of op een virtuele machine in hetzelfde VNet als het HDInsight-cluster. Een Hadoop-toepassing van derden die niet beschikbaar is in azure HDInsight, kan worden geïnstalleerd met behulp van de optie ' toepassingen ' in het HDInsight-cluster. Aangepaste Hadoop-toepassingen kunnen worden geïnstalleerd op HDInsight-cluster met behulp van ' script acties '. De volgende tabel bevat enkele van de algemene toepassingen en de opties voor HDInsight-integratie:

|**Toepassing**|**Integratie**
|---|---|
|Lucht stroom|IaaS of HDInsight Edge-knoop punt
|Alluxio|IaaS  
|Arcadia|IaaS 
|Atlas|Geen (alleen HDP)
|Data meer|HDInsight Edge-knoop punt
|Datastax (Cassandra)|IaaS (CosmosDB een alternatief op Azure)
|DataTorrent|IaaS 
|Drill|IaaS 
|Ignite|IaaS
|Jethro|IaaS 
|Mapador|IaaS 
|Mongo|IaaS (CosmosDB een alternatief op Azure)
|NiFi|IaaS 
|Presto|IaaS of HDInsight Edge-knoop punt
|Python 2|PaaS 
|Python 3|PaaS 
|R|PaaS 
|SAS|IaaS 
|Vertica|IaaS (SQLDW een alternatief op Azure)
|Tableau|IaaS 
|Toestand|HDInsight Edge-knoop punt
|StreamSets|HDInsight-rand 
|Palantir|IaaS 
|Sailpoint|IaaS 

Zie het artikel [Apache Hadoop onderdelen die beschikbaar zijn in verschillende versies van HDInsight](../hdinsight-component-versioning.md) voor meer informatie

## <a name="customize-hdinsight-clusters-using-script-actions"></a>HDInsight-clusters aanpassen met behulp van script acties

HDInsight biedt een methode van cluster configuratie die een **script actie** wordt genoemd. Een script actie is een bash script dat wordt uitgevoerd op de knoop punten in een HDInsight-cluster en kan worden gebruikt voor het installeren van extra onderdelen en het wijzigen van configuratie-instellingen.

Script acties moeten worden opgeslagen op een URI die toegankelijk is vanuit het HDInsight-cluster. Ze kunnen worden gebruikt tijdens of na het maken van het cluster en kunnen ook worden beperkt om alleen te worden uitgevoerd op bepaalde knooppunt typen.

Het script kan worden vastgehouden of één keer worden uitgevoerd. De persistente scripts worden gebruikt om nieuwe worker-knoop punten die aan het cluster worden toegevoegd, aan te passen via schaal bewerkingen. Een persistent script kan ook wijzigingen Toep assen op een ander type knoop punt, zoals een hoofd knooppunt, wanneer er schaal bewerkingen optreden.

HDInsight biedt vooraf geschreven scripts voor het installeren van de volgende onderdelen op HDInsight-clusters:

- Een Azure Storage-account toevoegen
- Kleur Toon installeren
- Presto installeren
- Solr installeren
- Giraph installeren
- Hive-bibliotheken vooraf laden
- Mono installeren of bijwerken

> [!Note]  
> HDInsight biedt geen rechtstreekse ondersteuning voor aangepaste Hadoop-onderdelen of onderdelen die zijn geïnstalleerd met behulp van script acties.

Script acties kunnen ook worden gepubliceerd naar Azure Marketplace als een HDInsight-toepassing.

Raadpleeg voor meer informatie de volgende artikelen:

- [Apache Hadoop toepassingen van derden installeren in HDInsight](../hdinsight-apps-install-applications.md)
- [HDInsight-clusters aanpassen met behulp van script acties](../hdinsight-hadoop-customize-cluster-linux.md)
- [Een HDInsight-toepassing publiceren in azure Marketplace](../hdinsight-apps-publish-applications.md)

## <a name="customize-hdinsight-configs-using-bootstrap"></a>HDInsight-configuratie aanpassen met Boots trap

Wijzigingen in configuratie-instellingen in de configuratie bestanden `core-site.xml` , zoals `hive-site.xml` en `oozie-env.xml` kunnen worden gemaakt met Boots trap. Het volgende script is een voor beeld van het gebruik van de Power shell [AZ module](/powershell/azure/new-azureps-module-az) [-cmdlet New-AzHDInsightClusterConfig](/powershell/module/az.hdinsight/new-azhdinsightcluster):

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

$config = New—AzHDInsightClusterConfig '
    | Set—AzHDInsightDefaultStorage
    —StorageAccountName "$defaultStorageAccountName.blob. core.windows.net" `
    —StorageAccountKey "defaultStorageAccountKey " `
    | Add—AzHDInsightConfigValues `
        —HiveSite $hiveConfigValues

New—AzHDInsightCluster `
    —ResourceGroupName $existingResourceGroupName `
    —Cluster-Name $clusterName `
    —Location $location `
    —ClusterSizeInNodes $clusterSizeInNodes `
    —ClusterType Hadoop `
    —OSType Linux `
    —Version "3.6" `
    —HttpCredential $httpCredential `
    —Config $config
```

Zie voor meer informatie het artikel [HDInsight-clusters aanpassen met Boots trap](../hdinsight-hadoop-customize-cluster-bootstrap.md).  Zie ook [HDInsight-clusters beheren met behulp van de Apache Ambari rest API](../hdinsight-hadoop-manage-ambari-rest-api.md).

## <a name="access-client-tools-from-hdinsight-hadoop-cluster-edge-nodes"></a>Toegang tot client hulpprogramma's van HDInsight Hadoop-cluster Edge-knoop punten

Een lege Edge-knoop punt is een virtuele Linux-machine waarop dezelfde client hulpprogramma's zijn geïnstalleerd en geconfigureerd als op de hoofd knooppunten, maar waarvoor geen Hadoop-services worden uitgevoerd. Het Edge-knoop punt kan worden gebruikt voor de volgende doel einden:

- toegang tot het cluster
- client toepassingen testen
- client toepassingen hosten

Edge-knoop punten kunnen worden gemaakt en verwijderd via de Azure Portal en kunnen worden gebruikt tijdens of na het maken van het cluster. Nadat u het Edge-knoop punt hebt gemaakt, kunt u via SSH verbinding maken met het Edge-knoop punt en client hulpprogramma's uitvoeren om toegang te krijgen tot het Hadoop-cluster in HDInsight. Het SSH-eind punt van het Edge-knoop punt is `<EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22` .


Zie het artikel [lege Edge-knoop punten op Apache Hadoop clusters in HDInsight gebruiken](../hdinsight-apps-use-edge-node.md)voor meer informatie.


## <a name="use-scale-up-and-scale-down-feature-of-clusters"></a>De functie voor het omhoog en omlaag schalen van clusters gebruiken

HDInsight biedt elasticiteit door u de mogelijkheid te geven het aantal worker-knoop punten in uw clusters omhoog en omlaag te schalen. Met deze functie kunt u een cluster na uren of in het weekend verkleinen en uitpakken tijdens de piek behoeften van uw bedrijf. Zie voor meer informatie:

* [HDInsight-clusters schalen](../hdinsight-scaling-best-practices.md).
* [Clusters schalen](../hdinsight-administer-use-portal-linux.md#scale-clusters).

## <a name="use-hdinsight-with-azure-virtual-network"></a>HDInsight gebruiken met Azure Virtual Network

Met virtuele Azure-netwerken kunnen Azure-resources, zoals Azure Virtual Machines, veilig communiceren met elkaar, het internet en on-premises netwerken door netwerk verkeer te filteren en te routeren.

Met Azure Virtual Network met HDInsight kunnen de volgende scenario's worden gebruikt:

- Rechtstreeks verbinding maken met HDInsight vanuit een on-premises netwerk.
- Verbinding maken tussen HDInsight en gegevens archieven in een virtueel Azure-netwerk.
- U hebt rechtstreeks toegang tot Hadoop-services die niet openbaar beschikbaar zijn via internet. Bijvoorbeeld Kafka-Api's of de HBase Java-API.

HDInsight kan worden toegevoegd aan een nieuwe of bestaande Azure-Virtual Network. Als HDInsight wordt toegevoegd aan een bestaand Virtual Network, moeten de bestaande netwerk beveiligings groepen en door de gebruiker gedefinieerde routes worden bijgewerkt om onbeperkte toegang tot [meerdere IP-adressen](../hdinsight-management-ip-addresses.md) in het Azure-Data Center mogelijk te maken. Zorg er ook voor dat het verkeer naar de [poorten](../control-network-traffic.md#required-ports)die worden gebruikt door HDInsight-Services niet wordt geblokkeerd.

> [!Note]  
> HDInsight ondersteunt momenteel geen geforceerde tunneling. Geforceerde tunneling is een instelling van het subnet dat uitgaand Internet verkeer naar een apparaat afdwingt voor inspectie en logboek registratie. Verwijder geforceerde tunneling voordat u HDInsight in een subnet installeert of maak een nieuw subnet voor HDInsight. HDInsight biedt ook geen ondersteuning voor het beperken van uitgaande netwerk verbindingen.

Raadpleeg voor meer informatie de volgende artikelen:

- [Virtuele netwerken van Azure-overzicht](../../virtual-network/virtual-networks-overview.md)
- [Azure HDInsight uitbreiden met behulp van een virtueel Azure-netwerk](../hdinsight-plan-virtual-network-deployment.md).

## <a name="securely-connect-to-azure-services-with-azure-virtual-network-service-endpoints"></a>Veilig verbinding maken met Azure-Services met Azure Virtual Network Service-eind punten

HDInsight ondersteunt [service-eind punten voor virtuele netwerken](../../virtual-network/virtual-network-service-endpoints-overview.md), waarmee u veilig verbinding kunt maken met Azure Blob Storage, Azure Data Lake Storage Gen2, Cosmos DB en SQL-data bases. Door een service-eind punt in te scha kelen voor Azure HDInsight, loopt verkeer via een beveiligde route vanuit het Azure-Data Center. Met dit verbeterde beveiligings niveau op de netwerklaag kunt u big data opslag accounts vergren delen op hun opgegeven virtuele netwerken (VNETs) en toch gebruikmaken van HDInsight-clusters om die gegevens te openen en te verwerken.

Raadpleeg voor meer informatie de volgende artikelen:

- [Service-eindpunten voor virtueel netwerk](../../virtual-network/virtual-network-service-endpoints-overview.md)
- [HDInsight-beveiliging verbeteren met Service-eind punten](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/)

## <a name="connect-hdinsight-to-the-on-premises-network"></a>HDInsight verbinden met het on-premises netwerk

HDInsight kan worden verbonden met het on-premises netwerk met behulp van Azure Virtual Networks en een VPN-gateway. De volgende stappen kunnen worden gebruikt om verbinding te maken:

- Gebruik HDInsight in een Azure-Virtual Network die verbinding heeft met het on-premises netwerk.
- Configureer de DNS-naam omzetting tussen het virtuele netwerk en het on-premises netwerk.
- Configureer netwerk beveiligings groepen of door de gebruiker gedefinieerde routes (UDR) om netwerk verkeer te beheren.

Zie het artikel [verbinding maken tussen HDInsight en uw on-premises netwerk](../connect-on-premises-network.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Lees het volgende artikel in deze serie: [Aanbevolen procedures voor opslag voor on-premises om Azure HDInsight Hadoop migratie](apache-hadoop-on-premises-migration-best-practices-storage.md).
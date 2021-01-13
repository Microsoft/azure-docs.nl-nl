---
title: Azure HDInsight controleren en beheren met Ambari-webgebruikersinterface
description: Meer informatie over het gebruik van de Apache Ambari-gebruikers interface voor het bewaken en beheren van HDInsight-clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 01/12/2021
ms.openlocfilehash: 8195cffed077a77c0ad66fda1126e2f3ea53d5f0
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179143"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>HDInsight-clusters beheren met behulp van de Apache Ambari-webinterface

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari vereenvoudigt het beheer en de bewaking van een Apache Hadoop cluster. Deze vereenvoudiging wordt uitgevoerd door een gebruiks vriendelijke webgebruikersinterface en REST API te bieden. Ambari is opgenomen in HDInsight-clusters en wordt gebruikt om het cluster te bewaken en configuratiewijzigingen aan te brengen.

In dit document leert u hoe u de Ambari-webgebruikersinterface kunt gebruiken met een HDInsight-cluster.

## <a name="what-is-apache-ambari"></a><a id="whatis"></a>Wat is Apache Ambari?

[Apache Ambari](https://ambari.apache.org) vereenvoudigt Hadoop-beheer door een gebruiks vriendelijke webgebruikersinterface te bieden. U kunt Ambari gebruiken voor het beheren en bewaken van Hadoop-clusters. Ontwikkel aars kunnen deze mogelijkheden in hun toepassingen integreren met behulp van de [AMBARI rest-api's](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

## <a name="connectivity"></a>Connectiviteit

De Ambari-webgebruikersinterface is beschikbaar op uw HDInsight-cluster op `https://CLUSTERNAME.azurehdinsight.net` , waar `CLUSTERNAME` de naam van uw cluster is.

> [!IMPORTANT]  
> Voor het maken van verbinding met Ambari op HDInsight is HTTPS vereist. Wanneer u om verificatie wordt gevraagd, gebruikt u de account naam en het wacht woord van de beheerder die u hebt opgegeven toen het cluster werd gemaakt. Als u niet wordt gevraagd om referenties, controleert u uw netwerk instellingen om te bevestigen dat er geen connectiviteits problemen zijn tussen de client en de Azure HDInsight-clusters.

## <a name="ssh-tunnel-proxy"></a>SSH-tunnel (proxy)

Hoewel Ambari voor uw cluster rechtstreeks via internet toegankelijk is, zijn sommige koppelingen van de Ambari-webgebruikersinterface (zoals naar de JobTracker) niet beschikbaar op internet. Voor toegang tot deze services moet u een SSH-tunnel maken. Zie [ssh-tunneling gebruiken met HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)voor meer informatie.

## <a name="ambari-web-ui"></a>Ambari-webgebruikersinterface

> [!WARNING]  
> Niet alle functies van de Web-UI van Ambari worden ondersteund in HDInsight. Zie de sectie [niet-ondersteunde bewerkingen](#unsupported-operations) van dit document voor meer informatie.

Wanneer u verbinding maakt met de Ambari-webgebruikersinterface, wordt u gevraagd om u te verifiëren bij de pagina. Gebruik de Cluster beheer gebruiker (standaard beheerder) en het wacht woord dat u hebt gebruikt tijdens het maken van het cluster.

Wanneer de pagina wordt geopend, ziet u de balk aan de bovenkant. Deze balk bevat de volgende informatie en besturings elementen:

![Overzicht van Apache Ambari-dash board](./media/hdinsight-hadoop-manage-ambari/apache-ambari-dashboard.png)

|Item |Beschrijving |
|---|---|
|Ambari-logo|Hiermee opent u het dash board, dat kan worden gebruikt voor het bewaken van het cluster.|
|Cluster naam # OPS|Hiermee wordt het aantal actieve Ambari-bewerkingen weer gegeven. Als u de cluster naam of **# OPS** selecteert, wordt een lijst met achtergrond bewerkingen weer gegeven.|
|aantal waarschuwingen|Hiermee worden waarschuwingen of kritieke waarschuwingen weer gegeven, indien van toepassing, voor het cluster.|
|Dashboard|Hiermee wordt het dash board weer gegeven.|
|Services|Informatie en configuratie-instellingen voor de services in het cluster.|
|Hosts|Informatie en configuratie-instellingen voor de knoop punten in het cluster.|
|Waarschuwingen|Een logboek met informatie, waarschuwingen en kritieke waarschuwingen.|
|Beheerder|Software stack/services die zijn geïnstalleerd op het cluster, informatie over service accounts en Kerberos-beveiliging.|
|Knop beheer|Ambari-beheer, gebruikers instellingen en afmelden.|

## <a name="monitoring"></a>Bewaking

### <a name="alerts"></a>Waarschuwingen

De volgende lijst bevat de algemene statussen van waarschuwingen die worden gebruikt door Ambari:

* **OK**
* **Waarschuwing**
* **KRITIEKE**
* **HERKEND**

Bij andere waarschuwingen dan **OK** wordt **het aantal waarschuwingen aan** de bovenkant van de pagina weer gegeven. Als u dit item selecteert, worden de waarschuwingen en hun status weer gegeven.

Waarschuwingen zijn ingedeeld in verschillende standaard groepen, die kunnen worden weer gegeven op de pagina **waarschuwingen** .

![Pagina overzicht van Apache Ambari-waarschuwingen](./media/hdinsight-hadoop-manage-ambari/hdinsight-alerts-page.png)

U kunt de groepen beheren via het menu **acties** en vervolgens **waarschuwings groepen beheren** selecteren.

![Apache Ambari-waarschuwings groepen beheren](./media/hdinsight-hadoop-manage-ambari/ambari-manage-alerts.png)

U beheert waarschuwings methoden en u maakt waarschuwings meldingen in het menu **acties** door __meldingen beheren__ te selecteren. Alle huidige meldingen worden weer gegeven. Hier kunt u meldingen maken. Meldingen kunnen worden verzonden via **e-mail** of **SNMP** wanneer specifieke combi Naties van waarschuwingen en ernst optreden. U kunt bijvoorbeeld een e-mail bericht verzenden wanneer een van de waarschuwingen in de **standaard groep garen** is ingesteld op **kritiek**.

![Apache Ambari-waarschuwing maken](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

Ten slotte kunt u met het selecteren van __instellingen voor waarschuwingen beheren__ in het menu __acties__ instellen hoe vaak een waarschuwing moet worden gegeven voordat een melding wordt verzonden. Deze instelling kan worden gebruikt om meldingen voor tijdelijke fouten te voor komen.

Zie [Configure Apache Ambari Email Notifications in azure HDInsight](./apache-ambari-email.md)(Engelstalig) voor een zelf studie over een waarschuwings melding met een gratis [SendGrid-account](../sendgrid-dotnet-how-to-send-email.md).

### <a name="cluster"></a>Cluster

Het tabblad **metrische gegevens** van het dash board bevat een reeks widgets waarmee u in één oogopslag de status van uw cluster kunt bewaken. Verschillende widgets, zoals **CPU-gebruik**, bieden aanvullende informatie wanneer erop wordt geklikt.

![Apache Ambari-dash board met metrische gegevens](./media/hdinsight-hadoop-manage-ambari/hdi-metrics-dashboard.png)

Op het tabblad **Heatmaps** worden metrische gegevens weer gegeven als gekleurde Heatmaps, van groen naar rood.

![Apache Ambari-dash board met Heatmaps](./media/hdinsight-hadoop-manage-ambari/hdi-heatmap-dashboard.png)

Selecteer **hosts** voor meer informatie over de knoop punten in het cluster. Selecteer vervolgens het specifieke knoop punt waarin u bent geïnteresseerd.

![Overzichts Details van Apache Ambari-host](./media/hdinsight-hadoop-manage-ambari/ambari-host-details1.png)

### <a name="services"></a>Services

De Sidebar **Services** in het dash board biedt een snelle inzicht in de status van de services die op het cluster worden uitgevoerd. Er worden verschillende pictogrammen gebruikt om de status of acties aan te geven die moeten worden uitgevoerd. Zo wordt bijvoorbeeld een geel recycling symbool weer gegeven als een service opnieuw moet worden gerecycled.


![Apache Ambari Services-zijkants balk](./media/hdinsight-hadoop-manage-ambari/apache-ambari-service-bar.png)

> [!NOTE]  
> De weer gegeven Services verschillen per HDInsight-cluster type en-versie. De services die hier worden weer gegeven, kunnen afwijken van de services die voor uw cluster worden weer gegeven.

Als u een service selecteert, wordt gedetailleerde informatie over de service weer gegeven.

![Overzichts gegevens van Apache Ambari-service](./media/hdinsight-hadoop-manage-ambari/ambari-service-details.png)

#### <a name="quick-links"></a>Snelkoppelingen

Voor sommige Services wordt boven aan de pagina een koppeling **naar de snelkoppeling links** weer gegeven. Deze koppeling kan worden gebruikt om toegang te krijgen tot servicespecifieke Web-UIs, zoals:

* **Taak geschiedenis** -MapReduce-taak geschiedenis.
* **Resource Manager** : de gebruikers interface van het Resource Manager-garen.
* **NameNode** -HADOOP DISTRIBUTED File System (HDFS) NameNode-gebruikers interface.
* **Oozie-webgebruikersinterface** -Oozie-gebruikers interface.

Als u een van deze koppelingen selecteert, wordt er een nieuw tabblad in de browser geopend waarin de geselecteerde pagina wordt weer gegeven.

> [!NOTE]  
> Als u de vermelding voor de **snelle koppelingen** selecteert voor een service, wordt de fout ' server niet gevonden ' weer gegeven. Als deze fout optreedt, moet u een SSH-tunnel gebruiken wanneer u de vermelding **snelle koppelingen** gebruikt voor deze service. Zie [ssh-tunneling gebruiken met HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) voor meer informatie

## <a name="management"></a>Beheer

### <a name="ambari-users-groups-and-permissions"></a>Ambari-gebruikers,-groepen en-machtigingen

Werken met gebruikers, groepen en machtigingen wordt ondersteund. Zie [gebruikers autoriseren voor Apache Ambari-weer gaven](./hdinsight-authorize-users-to-ambari.md)voor lokaal beheer. Zie [HDInsight-clusters](./domain-joined/hdinsight-security-overview.md)die zijn gekoppeld aan een domein voor clusters die lid zijn van een domein.

> [!WARNING]  
> Verwijder of wijzig het wacht woord van de Ambari-watchdog (hdinsightwatchdog) niet op uw op Linux gebaseerde HDInsight-cluster. Als u het wacht woord wijzigt, is het niet meer mogelijk om script acties te gebruiken of om schaal bewerkingen uit te voeren met uw cluster.

### <a name="hosts"></a>Hosts

Op de pagina **hosts** worden alle hosts in het cluster weer gegeven. Voer de volgende stappen uit om hosts te beheren.

![Pagina overzicht van Apache Ambari-hosts](./media/hdinsight-hadoop-manage-ambari/hdinsight-hosts-page.png)

> [!NOTE]  
> Het is niet mogelijk om een host toe te voegen, uit te stellen en opnieuw in te stellen met HDInsight-clusters.

1. Selecteer de host die u wilt beheren.

2. Gebruik het menu **acties** om de actie te selecteren die u wilt uitvoeren:

    |Item |Beschrijving |
    |---|---|
    |Alle onderdelen starten|Start alle onderdelen op de host.|
    |Alle onderdelen stoppen|Stop alle onderdelen op de host.|
    |Alle onderdelen opnieuw starten|Stop en start alle onderdelen op de host.|
    |Onderhouds modus inschakelen|Onderdrukt waarschuwingen voor de host. Deze modus moet zijn ingeschakeld als u acties uitvoert waarmee waarschuwingen worden gegenereerd. Bijvoorbeeld, het stoppen en starten van een service.|
    |Onderhouds modus uitschakelen|Retourneert de host naar normale waarschuwingen.|
    |Stoppen|Stopt DataNode of NodeManagers op de host.|
    |Starten|Start DataNode of NodeManagers op de host.|
    |Opnieuw starten|Stopt en start DataNode of NodeManagers op de host.|
    |Uit bedrijf nemen|Hiermee verwijdert u een host uit het cluster. **Gebruik deze actie niet op HDInsight-clusters.**|
    |Opnieuw provisie|Voegt een eerder buiten gebruik gestelde host toe aan het cluster. **Gebruik deze actie niet op HDInsight-clusters.**|

### <a name="services"></a><a id="service"></a>Services

Gebruik op de pagina **dash board** of **Services** de knop **acties** onder aan de lijst met Services om alle services te stoppen en te starten.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/ambari-service-actions.png" alt-text="Lijst met Apache Ambari-service acties." border="true":::

> [!WARNING]  
> Er moeten nieuwe services worden toegevoegd met behulp van een script actie tijdens het inrichten van het cluster. Zie [HDInsight-clusters aanpassen met behulp van script acties](hdinsight-hadoop-customize-cluster-linux.md)voor meer informatie over het gebruik van script acties.

De **actie** knop kan alle services opnieuw starten, vaak wilt u een specifieke service starten, stoppen of opnieuw starten. Gebruik de volgende stappen om acties uit te voeren voor een afzonderlijke service:

1. Selecteer een service op de pagina **dash board** of **Services** .

2. Klik vanaf de bovenkant van het tabblad **samen vatting** op de knop **service acties** en selecteer de actie die u wilt uitvoeren. Met deze actie wordt de service opnieuw gestart op alle knoop punten.

    ![Ambari afzonderlijke service acties voor Apache](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]  
   > Als er services worden gestart terwijl het cluster wordt uitgevoerd, kunnen er waarschuwingen worden gegenereerd. Als u waarschuwingen wilt voor komen, kunt u de knop **service acties** gebruiken om de **onderhouds modus** voor de service in te scha kelen voordat u de computer opnieuw opstart.

3. Zodra een actie is geselecteerd, wordt de vermelding **# op** aan de bovenkant van de pagina weer gegeven om aan te geven dat er een achtergrond bewerking plaatsvindt. Indien geconfigureerd om weer te geven, wordt de lijst met achtergrond bewerkingen weer gegeven.

   > [!NOTE]  
   > Als u de **onderhouds modus** voor de service hebt ingeschakeld, moet u deze uitschakelen met behulp van de knop **service acties** zodra de bewerking is voltooid.

Als u een service wilt configureren, gebruikt u de volgende stappen:

1. Selecteer een service op de pagina **dash board** of **Services** .

2. Selecteer het tabblad **configuratie** . De huidige configuratie wordt weer gegeven. Er wordt ook een lijst met eerdere configuraties weer gegeven.

    ![Configuratie van Apache Ambari-service](./media/hdinsight-hadoop-manage-ambari/ambari-service-configs.png)

3. Gebruik de weer gegeven velden om de configuratie te wijzigen en selecteer vervolgens **Opslaan**. Of selecteer een vorige configuratie en selecteer vervolgens **huidige maken** om de vorige instellingen terug te draaien.

## <a name="ambari-views"></a>Ambari weer gaven

Met Ambari-weer gaven kunnen ontwikkel aars UI-elementen aan de Ambari-webgebruikersinterface koppelen met behulp van het Apache Ambari views-Framework. HDInsight biedt de volgende weer gaven met Hadoop-cluster typen:

* Hive-weer gave: met de Hive-weer gave kunt u Hive-query's rechtstreeks vanuit uw webbrowser uitvoeren. U kunt query's opslaan, resultaten weer geven, resultaten opslaan in de cluster opslag of resultaten downloaden naar uw lokale systeem. Zie [Apache Hive weergaven gebruiken met HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)voor meer informatie over het gebruik van Hive-weer gaven.

* Weer gave TEZ: de TEZ-weer gave biedt u de mogelijkheid om taken beter te begrijpen en te optimaliseren. U kunt informatie weer geven over hoe TEZ-taken worden uitgevoerd en welke resources er worden gebruikt.

## <a name="unsupported-operations"></a>Niet-ondersteunde bewerkingen

De volgende Ambari-bewerkingen worden niet ondersteund op HDInsight:

* __De metrische Collector service wordt verplaatst__. Bij het weer geven van informatie over de metrische Collector-service wordt een van de acties die beschikbaar zijn via het menu Service acties, de __Collector van metrische gegevens verplaatsen__. Deze actie wordt niet ondersteund met HDInsight.

## <a name="next-steps"></a>Volgende stappen

* [Apache Ambari rest API](hdinsight-hadoop-manage-ambari-rest-api.md) met HDInsight.
* [Apache Ambari gebruiken om de configuratie van HDInsight-clusters te optimaliseren](./hdinsight-changing-configs-via-ambari.md)
* [Azure HDInsight-clusters schalen](./hdinsight-scaling-best-practices.md)

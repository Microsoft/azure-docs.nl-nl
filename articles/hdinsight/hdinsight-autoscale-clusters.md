---
title: Azure HDInsight-clusters automatisch schalen
description: Gebruik de functie voor automatisch schalen om Azure HDInsight-clusters automatische te schalen op basis van een schema of prestatie gegevens.
ms.service: hdinsight
ms.topic: how-to
ms.custom: contperf-fy21q1, contperf-fy21q2
ms.date: 12/14/2020
ms.openlocfilehash: 130a5a58fc7dab6f94c011cf9764743f9114e48a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942646"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Azure HDInsight-clusters automatisch schalen

Met de gratis functie voor automatisch schalen van Azure HDInsight kunt u het aantal worker-knoop punten in uw cluster, op basis van eerder ingestelde criteria, verg Roten of verkleinen. De functie voor automatisch schalen werkt door het aantal knoop punten binnen de vooraf ingestelde limieten te schalen op basis van de metrische gegevens over prestaties of een schema voor het omhoog en omlaag schalen van bewerkingen.

## <a name="how-it-works"></a>Uitleg

De functie voor automatisch schalen maakt gebruik van twee soorten voor waarden voor het activeren van schaal gebeurtenissen: drempel waarden voor diverse metrische gegevens voor cluster prestaties (zogenaamde *op belasting gebaseerd schalen*) en activering op basis van tijd (genoemd *op schema gebaseerd*). Schalen op basis van een belasting wijzigt het aantal knoop punten in het cluster, binnen een bereik dat u instelt, om ervoor te zorgen dat het CPU-gebruik optimaal werkt en de uitgevoerde kosten tot een minimum worden beperkt. Schalen op basis van een schema wijzigt het aantal knoop punten in uw cluster op basis van een schema voor het omhoog schalen en schalen van bewerkingen.

De volgende video biedt een overzicht van de uitdagingen die automatisch schalen en hoe u dit kunt helpen bij het beheren van kosten met HDInsight.

> [!VIDEO https://www.youtube.com/embed/UlZcDGGFlZ0?WT.mc_id=dataexposed-c9-niner]

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Schalen op basis van een werk belasting of op basis van een planning kiezen

Houd rekening met de volgende factoren bij het kiezen van een schaal type:

* Afwijking van belasting: voert de belasting van het cluster een consistent patroon op specifieke tijden uit, op specifieke dagen? Als dat niet het geval is, is planning op basis van belasting een betere optie.
* SLA-vereisten: automatisch schalen schalen is reactief in plaats van voorspellend. Is er voldoende vertraging tussen het moment waarop de belasting begint te verhogen en wanneer het cluster de doel grootte moet hebben? Als er strikte SLA-vereisten zijn en de belasting een vast bekend patroon is, is ' planning gebaseerd ' een betere optie.

### <a name="cluster-metrics"></a>Cluster metrieken

Automatisch schalen bewaakt het cluster voortdurend en verzamelt de volgende metrische gegevens:

|Metrisch|Beschrijving|
|---|---|
|Totale CPU in behandeling|Het totale aantal kern geheugens dat nodig is om de uitvoering van alle in behandeling zijnde containers te starten.|
|Totaal geheugen in behandeling|Het totale geheugen (in MB) dat is vereist om de uitvoering van alle in behandeling zijnde containers te starten.|
|Totale vrije CPU|De som van alle ongebruikte kernen op de actieve worker-knoop punten.|
|Totaal beschikbaar geheugen|De som van het ongebruikte geheugen (in MB) op de actieve worker-knoop punten.|
|Gebruikt geheugen per knoop punt|De belasting van een worker-knoop punt. Een worker-knoop punt waarop 10 GB geheugen wordt gebruikt, wordt beschouwd als meer belasting dan een werk nemer met 2 GB gebruikt geheugen.|
|Aantal toepassings Masters per knoop punt|Het aantal knoop punten in de toepassings Master dat wordt uitgevoerd op een worker-knoop punt. Een worker-knoop punt dat fungeert als host voor twee AM-containers, wordt beschouwd als belang rijker dan een worker-knoop punt dat wordt gehost op nul AM-containers.|

De bovenstaande metrische gegevens worden elke 60 seconden gecontroleerd. U kunt de schaal bewerkingen voor uw cluster instellen met behulp van een van deze metrische gegevens.

### <a name="load-based-scale-conditions"></a>Schaal voorwaarden op basis van een belasting

Wanneer aan de volgende voor waarden wordt voldaan, wordt door automatisch schalen een schaal aanvraag uitgegeven:

|Omhoog schalen|Omlaag schalen|
|---|---|
|De totale CPU in behandeling is groter dan de totale vrije CPU gedurende meer dan drie minuten.|De totale beschik bare CPU is minder dan de totale vrije CPU voor meer dan 10 minuten.|
|Totaal geheugen in behandeling is groter dan het totale beschik bare geheugen gedurende meer dan drie minuten.|Totaal geheugen in behandeling is minder dan het totale beschik bare geheugen gedurende meer dan 10 minuten.|

Voor opschalen: automatisch schalen geeft een opschaal aanvraag om het vereiste aantal knoop punten toe te voegen. De omhoog schalen is gebaseerd op het aantal nieuwe werk knooppunten dat nodig is om te voldoen aan de huidige CPU-en geheugen vereisten.

Voor schalen, automatisch schalen, wordt een aanvraag voor het verwijderen van een bepaald aantal knoop punten opgelost. De schaal is gebaseerd op het aantal AM-containers per knoop punt. En de huidige vereisten voor de CPU en het geheugen. De service detecteert ook welke knoop punten kandidaten zijn voor verwijdering op basis van de huidige taak uitvoering. Met de bewerking omlaag schalen worden de knoop punten eerst buiten gebruik gesteld en vervolgens uit het cluster verwijderd.

### <a name="cluster-compatibility"></a>Cluster compatibiliteit

> [!Important]
> De functie voor automatisch schalen van Azure HDInsight is op 7 november 2019 algemeen beschikbaar gekomen voor Spark- en Hadoop-clusters en bevat verbeteringen die niet beschikbaar zijn in de preview-versie van de functie. Als u vóór 7 november 2019 een Spark-cluster hebt gemaakt en u de functie voor automatisch schalen wilt gebruiken in uw cluster, is het aanbevolen pad om een nieuw cluster te maken en automatisch schalen in te schakelen in het nieuwe cluster.
>
> Automatisch schalen voor interactieve Query's (LLAP) is uitgebracht voor algemene Beschik baarheid voor HDI 4,0 op 27 van 2020. HBase-clusters zijn nog steeds beschikbaar als preview-versie. Automatisch schalen is alleen beschikbaar voor clusters van Spark, Hadoop, Interactive Query en HBase.

In de volgende tabel worden de cluster typen en versies beschreven die compatibel zijn met de functie voor automatisch schalen.

| Versie | Spark | Hive | Interactive Query | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3,6 zonder ESP | Ja | Ja | Ja | Ja* | Nee | Nee | Nee |
| HDInsight 4,0 zonder ESP | Ja | Ja | Ja | Ja* | Nee | Nee | Nee |
| HDInsight 3,6 met ESP | Ja | Ja | Ja | Ja* | Nee | Nee | Nee |
| HDInsight 4,0 met ESP | Ja | Ja | Ja | Ja* | Nee | Nee | Nee |

\* HBase-clusters kunnen alleen worden geconfigureerd voor schalen op basis van een planning, niet op basis van de belasting.

## <a name="get-started"></a>Aan de slag

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Een cluster maken met automatisch schalen op basis van een werk belasting

Als u de functie voor automatisch schalen wilt inschakelen met schalen op basis van een werk belasting, voert u de volgende stappen uit als onderdeel van het normale proces voor het maken van het cluster:

1. Schakel op het tabblad **configuratie en prijzen** het selectie vakje **automatisch schalen inschakelen** in.
1. Selecteer **op belasting gebaseerd** onder **type automatisch schalen**.
1. Voer de gewenste waarden in voor de volgende eigenschappen:  

    * Oorspronkelijk **aantal knoop punten** voor **worker-knoop punt**.
    * **Min** . aantal worker-knoop punten.
    * **Maximum** aantal worker-knoop punten.

    ![Automatisch schalen op basis van werk knooppunten inschakelen](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

Het eerste aantal worker-knoop punten moet liggen tussen het minimum en het maximum, inclusief. Met deze waarde wordt de oorspronkelijke grootte van het cluster gedefinieerd wanneer het wordt gemaakt. Het minimum aantal worker-knoop punten moet worden ingesteld op drie of meer. Het schalen van uw cluster naar minder dan drie knoop punten kan ertoe leiden dat de veilige modus vastloopt vanwege onvoldoende bestands replicatie.  Zie voor meer informatie [ophalen in de veilige modus](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Een cluster maken met automatisch schalen op basis van een planning

Als u de functie voor automatisch schalen wilt inschakelen met schalen op basis van een schema, voert u de volgende stappen uit als onderdeel van het normale proces voor het maken van het cluster:

1. Schakel op het tabblad **configuratie en prijzen** het selectie vakje **automatisch schalen inschakelen** in.
1. Voer het **aantal knoop punten** in voor het **worker-knoop punt**, waarmee de limiet voor het omhoog schalen van het cluster wordt bepaald.
1. Selecteer het optie **schema op basis van** het **type automatisch schalen**.
1. Selecteer **configureren** om het venster **configuratie automatisch schalen** te openen.
1. Selecteer uw tijd zone en klik vervolgens op **+ voor waarde toevoegen**
1. Selecteer de dagen van de week waarop de nieuwe voor waarde moet worden toegepast.
1. Bewerk het tijdstip waarop de voor waarde van kracht moet zijn en het aantal knoop punten waaraan het cluster moet worden geschaald.
1. Voeg indien nodig meer voor waarden toe.

    ![Maken van werk knooppunt plannen toestaan](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

Het aantal knoop punten moet tussen 3 en het maximum aantal worker-knoop punten zijn dat u hebt ingevoerd voordat voor waarden worden toegevoegd.

### <a name="final-creation-steps"></a>Laatste aanmaak stappen

Selecteer het VM-type voor worker-knoop punten door een virtuele machine te selecteren in de vervolg keuzelijst onder **knooppunt grootte**. Nadat u het VM-type voor elk knooppunt type hebt gekozen, ziet u het geschatte kosten bereik voor het hele cluster. Pas de VM-typen aan uw budget aan.

![Knooppunt grootte voor automatisch schalen van werk knooppunten inschakelen](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

Uw abonnement heeft een capaciteits quotum voor elke regio. Het totale aantal kernen van uw hoofd knooppunten en de maximale worker-knoop punten mag het capaciteits quotum niet overschrijden. Dit quotum is echter een zachte limiet; u kunt altijd een ondersteunings ticket maken om het probleem te verhelpen.

> [!Note]  
> Als u het totale aantal kern quotum overschrijdt, wordt er een fout bericht weer gegeven met de melding dat het maximum knooppunt de beschik bare kernen in deze regio heeft overschreden. Kies een andere regio of neem contact op met de ondersteuning om het quotum te verhogen.

Zie op [Linux gebaseerde clusters maken in HDInsight met behulp van de Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md)voor meer informatie over het maken van HDInsight-clusters met behulp van de Azure Portal.  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Een cluster maken met een resource manager-sjabloon

#### <a name="load-based-autoscaling"></a>Automatisch schalen op basis van een werk belasting

U kunt een HDInsight-cluster maken met behulp van automatisch schalen op basis van een Azure Resource Manager sjabloon door een `autoscale` knoop punt toe te voegen aan de `computeProfile`  >  `workernode` sectie met de eigenschappen `minInstanceCount` en `maxInstanceCount` zoals wordt weer gegeven in het JSON-code fragment hieronder. Voor een volledige Resource Manager-sjabloon raadpleegt u [Quick Start-sjabloon: een Spark-cluster implementeren met automatisch schalen op basis van de taak ingeschakeld](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-autoscale-loadbased).

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 3,
          "maxInstanceCount": 10
      }
  },
  "hardwareProfile": {
      "vmSize": "Standard_D13_V2"
  },
  "osProfile": {
      "linuxOperatingSystemProfile": {
          "username": "[parameters('sshUserName')]",
          "password": "[parameters('sshPassword')]"
      }
  },
  "virtualNetworkProfile": null,
  "scriptActions": []
}
```

#### <a name="schedule-based-autoscaling"></a>Automatisch schalen op basis van een planning

U kunt een HDInsight-cluster maken met behulp van een Azure Resource Manager sjabloon automatisch schalen door een `autoscale` knoop punt toe te voegen aan de `computeProfile`  >  `workernode` sectie. Het `autoscale` knoop punt bevat een `recurrence` `timezone` en `schedule` die beschrijft wanneer de wijziging wordt doorgevoerd. Zie voor een volledige Resource Manager-sjabloon [implementeren Spark-cluster met automatisch schalen op basis van planning ingeschakeld](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-autoscale-schedulebased).

```json
{
  "autoscale": {
    "recurrence": {
      "timeZone": "Pacific Standard Time",
      "schedule": [
        {
          "days": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday"
          ],
          "timeAndCapacity": {
            "time": "11:00",
            "minInstanceCount": 10,
            "maxInstanceCount": 10
          }
        }
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Automatisch schalen voor een actief cluster in-en uitschakelen

#### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Selecteer **cluster grootte** onder **instellingen** om automatisch schalen in te scha kelen op een actief cluster. Selecteer vervolgens **automatisch schalen inschakelen**. Selecteer het gewenste type automatisch schalen en voer de opties in voor schalen op basis van de belasting of op basis van een planning. Selecteer ten slotte **Opslaan**.

![Op schema gebaseerde automatische schaal aanpassing van werk knooppunten inschakelen](./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png)

#### <a name="using-the-rest-api"></a>De REST API gebruiken

Als u automatisch schalen op een actief cluster wilt in-of uitschakelen met behulp van de REST API, maakt u een POST-aanvraag naar het eind punt voor automatisch schalen:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

Gebruik de juiste para meters in de aanvraag lading. De onderstaande JSON-nettolading kan worden gebruikt om automatisch schalen in te scha kelen. Gebruik de payload `{autoscale: null}` om automatisch schalen uit te scha kelen.

```json
{ "autoscale": { "capacity": { "minInstanceCount": 3, "maxInstanceCount": 5 } } }
```

Zie de vorige sectie over het [inschakelen van automatisch schalen op basis](#load-based-autoscaling) van een werk belasting voor een volledige beschrijving van alle Payload-para meters.

## <a name="monitoring-autoscale-activities"></a>Activiteiten voor automatisch schalen bewaken

### <a name="cluster-status"></a>De clusterstatus

De cluster status die in de Azure Portal wordt weer gegeven, kan u helpen bij het controleren van de activiteiten voor automatisch schalen.

![De cluster status automatisch schalen van werk knooppunten inschakelen](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Alle cluster status berichten die u mogelijk ziet, worden beschreven in de onderstaande lijst.

| De clusterstatus | Beschrijving |
|---|---|
| Wordt uitgevoerd | Het cluster werkt normaal. Alle vorige activiteiten voor automatisch schalen zijn voltooid. |
| Bijwerken  | De configuratie van het automatisch schalen van clusters wordt bijgewerkt.  |
| HDInsight-configuratie  | Een cluster omhoog of omlaag schalen wordt uitgevoerd.  |
| Fout bij het bijwerken  | Er zijn problemen met HDInsight tijdens de configuratie-update voor automatisch schalen. Klanten kunnen ervoor kiezen om de update opnieuw uit te voeren of automatisch schalen uit te scha kelen.  |
| Fout  | Er is iets mis met het cluster en het is niet bruikbaar. Verwijder dit cluster en maak een nieuwe.  |

Als u het huidige aantal knoop punten in uw cluster wilt weer geven, gaat u naar het diagram **cluster grootte** op de pagina **overzicht** voor uw cluster. Of selecteer **cluster grootte** onder **instellingen**.

### <a name="operation-history"></a>Bewerkings geschiedenis

U kunt de geschiedenis van het cluster omhoog en omlaag schalen als onderdeel van de metrische gegevens van het cluster. U kunt ook alle schaal acties voor de afgelopen dag, week of andere periode weer geven.

Selecteer **metrische gegevens** onder **bewaking**. Selecteer vervolgens **metrische gegevens** en het **aantal actieve werk** rollen toevoegen in de vervolg keuzelijst **metriek** . Selecteer de knop in de rechter bovenhoek om het tijds bereik te wijzigen.

![Metriek voor automatisch schalen op basis van worker-knoop punten inschakelen](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="best-practices"></a>Aanbevolen procedures

### <a name="consider-the-latency-of-scale-up-and-scale-down-operations"></a>Houd rekening met de latentie van het omhoog en omlaag schalen van bewerkingen

Het kan 10 tot 20 minuten duren voordat een schaal bewerking is voltooid. Wanneer u een aangepaste planning instelt, moet u deze vertraging plannen. Als u de grootte van het cluster bijvoorbeeld 20 om 9:00 uur nodig hebt, stelt u de plannings trigger in op een eerder tijdstip, bijvoorbeeld 8:30, zodat de schaal bewerking is voltooid door 9:00 uur.

### <a name="prepare-for-scaling-down"></a>Voorbereiden op omlaag schalen

Tijdens het omlaag schalen van het cluster worden de knoop punten buiten gebruik gesteld om te voldoen aan de doel grootte. Als er taken worden uitgevoerd op die knoop punten, wacht automatisch schalen totdat de taken zijn voltooid voor Spark-en Hadoop-clusters. Omdat elk worker-knoop punt ook een rol in HDFS heeft, worden de tijdelijke gegevens verplaatst naar de resterende knoop punten. Zorg ervoor dat er voldoende ruimte is op de resterende knoop punten om alle tijdelijke gegevens te hosten.

De actieve taken worden voortgezet. De in behandeling zijnde taken wachten op planning met minder beschik bare werk knooppunten.

### <a name="be-aware-of-the-minimum-cluster-size"></a>Houd rekening met de minimale cluster grootte

Schaal uw cluster niet naar minder dan drie knoop punten. Het schalen van uw cluster naar minder dan drie knoop punten kan ertoe leiden dat de veilige modus vastloopt vanwege onvoldoende bestands replicatie. Zie voor meer informatie [ophalen in de veilige modus](hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

### <a name="increase-the-number-of-mappers-and-reducers"></a>Verhoog het aantal toewijzingen en verminderers

Automatisch schalen voor Hadoop-clusters houdt ook het gebruik van HDFS bij. Als de HDFS bezig is, wordt ervan uitgegaan dat het cluster nog steeds de huidige resources nodig heeft. Wanneer er enorme gegevens bij de query betrokken zijn, kunt u het aantal mappers en verlaagers verhogen om de parallelle factor te verhogen en de HDFS-bewerkingen te versnellen. Op deze manier wordt de juiste schaal aanpassing geactiveerd wanneer er sprake is van extra resources. 

### <a name="set-the-hive-configuration-maximum-total-concurrent-queries-for-the-peak-usage-scenario"></a>Het maximum aantal gelijktijdige Query's voor het configureren van het Hive-scenario instellen voor het piek gebruik

Met de functie voor automatisch schalen wijzigt u niet het *maximum aantal gelijktijdige query's* in de Hive-configuratie in Ambari. Dit betekent dat de service component server 2 interactief alleen het opgegeven aantal gelijktijdige query's kan verwerken op elk gewenst moment, zelfs als het aantal interactieve query-daemons omhoog en omlaag wordt geschaald op basis van de belasting en het schema. De algemene aanbeveling is om deze configuratie in te stellen voor het scenario voor het piek gebruik om hand matige tussen komst te voor komen.

Het kan echter voor komen dat een Hive-Server 2 opnieuw wordt gestart als er slechts een klein aantal worker-knoop punten is en de waarde voor het maximum aantal gelijktijdige query's te hoog is geconfigureerd. U hebt mini maal het minimum aantal worker-knoop punten nodig dat kan voldoen aan het gegeven aantal TEZ AMS (gelijk aan de maximum configuratie voor gelijktijdige Query's). 

## <a name="limitations"></a>Beperkingen

### <a name="node-label-file-missing"></a>Label bestand knoop punt ontbreekt

HDInsight automatisch schalen maakt gebruik van een knooppunt label bestand om te bepalen of een knoop punt gereed is om taken uit te voeren. Het label bestand van het knoop punt wordt opgeslagen op HDFS met drie replica's. Als de cluster grootte aanzienlijk wordt geschaald en er sprake is van een grote hoeveelheid tijdelijke gegevens, is er een kleine kans dat alle drie de replica's kunnen worden verwijderd. Als dit gebeurt, treedt er een fout status op in het cluster.

### <a name="interactive-query-daemons-count"></a>Aantal interactieve query-daemons

In het geval van interactieve query clusters waarvoor automatisch schalen is ingeschakeld, wordt ook het aantal interactieve query-daemons omhoog/omlaag geschaald naar het aantal actieve worker-knoop punten. De wijziging van het aantal daemons wordt niet doorgevoerd in de `num_llap_nodes` configuratie in Ambari. Als Hive-Services hand matig opnieuw worden gestart, wordt het aantal interactieve query-daemons opnieuw ingesteld conform de configuratie in Ambari.

Als de interactieve query service hand matig opnieuw wordt gestart, moet u hand matig de `num_llap_node` configuratie wijzigen (het aantal knoop punten dat nodig is voor het uitvoeren van de Hive-interactieve query-daemon) onder *Geavanceerde Hive-Interactive-env* om overeen te komen met het huidige aantal actieve worker-knoop punten.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over richt lijnen voor schalen van clusters hand matig in [richt lijnen voor schaling](hdinsight-scaling-best-practices.md)

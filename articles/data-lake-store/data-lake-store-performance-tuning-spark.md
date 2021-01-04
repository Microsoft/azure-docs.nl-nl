---
title: Prestaties afstemmen-Spark met Azure Data Lake Storage Gen1
description: Meer informatie over richt lijnen voor het afstemmen van de prestaties van Spark in azure HDInsight en Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: twooley
ms.openlocfilehash: 8a39e14b70827947687b7613b9ff86f18017ddfe
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97722501"
---
# <a name="performance-tuning-guidance-for-spark-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Richt lijnen voor het afstemmen van de prestaties van Spark in HDInsight en Azure Data Lake Storage Gen1

Bij het afstemmen van de prestaties van Spark moet u rekening houden met het aantal apps dat op uw cluster wordt uitgevoerd. Standaard kunt u vier apps gelijktijdig uitvoeren op uw HDI-cluster (Opmerking: de standaard instelling is onderhevig aan wijzigingen). U kunt ervoor kiezen om minder apps te gebruiken, zodat u de standaard instellingen en het gebruik van het cluster voor deze apps kan onderdrukken.

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Een Azure data Lake Storage gen1-account**. Zie [aan de slag met Azure data Lake Storage gen1](data-lake-store-get-started-portal.md) voor instructies over het maken van een account.
* **Azure HDInsight-cluster** met toegang tot een Data Lake Storage gen1-account. Zie [een HDInsight-cluster met data Lake Storage gen1 maken](data-lake-store-hdinsight-hadoop-use-portal.md). Zorg ervoor dat Extern bureaublad voor het cluster is ingeschakeld.
* **Spark-cluster wordt uitgevoerd op Data Lake Storage gen1**. Zie [HDInsight Spark-cluster gebruiken voor het analyseren van gegevens in data Lake Storage gen1](../hdinsight/spark/apache-spark-use-with-data-lake-store.md) voor meer informatie
* **Richt lijnen voor het afstemmen van de prestaties van data Lake Storage gen1**. Zie [Data Lake Storage gen1 richt lijnen voor het afstemmen van prestaties](./data-lake-store-performance-tuning-guidance.md) voor algemene concepten. 

## <a name="parameters"></a>Parameters

Wanneer Spark-taken worden uitgevoerd, zijn dit de belangrijkste instellingen die kunnen worden afgestemd op de prestaties van Data Lake Storage Gen1:

* **Num-uitvoerende** modules: het aantal gelijktijdige taken dat kan worden uitgevoerd.

* Uitvoerder **-geheugen** : de hoeveelheid geheugen die aan elke uitvoerder is toegewezen.

* Uitvoerder- **kernen** : het aantal kernen dat aan elke uitvoerder is toegewezen.

**Num-uitvoerende modules** Num-uitvoerende modules instellen het maximum aantal taken dat parallel kan worden uitgevoerd. Het werkelijke aantal taken dat parallel kan worden uitgevoerd, wordt begrensd door het geheugen en de CPU-bronnen die beschikbaar zijn in uw cluster.

Uitvoerder **-geheugen** Dit is de hoeveelheid geheugen die aan elke uitvoerder wordt toegewezen. Het geheugen dat nodig is voor elke uitvoerder is afhankelijk van de taak. Voor complexe bewerkingen moet het geheugen hoger zijn. Voor eenvoudige bewerkingen, zoals lezen en schrijven, zijn de geheugen vereisten lager. De hoeveelheid geheugen voor elke uitvoerder kan worden weer gegeven in Ambari. Navigeer in Ambari naar Spark en Bekijk het tabblad **configuratie** .

Uitvoerder **-kernen** Hiermee stelt u de hoeveelheid kern geheugens in die per uitvoerder worden gebruikt. Hiermee wordt het aantal parallelle threads bepaald dat per bewerkings periode kan worden uitgevoerd. Als de uitvoerder bijvoorbeeld-kernen = 2 is, kan elke uitvoerder twee parallelle taken uitvoeren in de uitvoerder. De vereiste uitvoerder-cores is afhankelijk van de taak. Voor I/O-intensieve taken is geen grote hoeveelheid geheugen per taak vereist, dus elke uitvoerder kan meer parallelle taken verwerken.

Standaard worden twee virtuele garen kernen gedefinieerd voor elke fysieke kern bij het uitvoeren van Spark op HDInsight. Dit aantal biedt een goede balans van gelijktijdigheid en hoeveelheid context wisseling van meerdere threads.

## <a name="guidance"></a>Hulp

Bij het uitvoeren van Spark analytische werk belastingen voor het werken met gegevens in Data Lake Storage Gen1 raden wij u aan de meest recente HDInsight-versie te gebruiken om de beste prestaties te verkrijgen met Data Lake Storage Gen1. Wanneer uw taak meer I/O-intensief is, kunnen bepaalde para meters worden geconfigureerd om de prestaties te verbeteren. Data Lake Storage Gen1 is een uiterst schaalbaar opslag platform dat hoge door Voer kan verwerken. Als de taak voornamelijk uit Lees-of schrijf bewerkingen bestaat, kunnen de prestaties worden verbeterd door de gelijktijdige gelijktijdigheid te verhogen voor I/O van en naar Data Lake Storage Gen1.

Er zijn enkele algemene manieren om gelijktijdigheid te verhogen voor I/O-intensieve taken.

**Stap 1: Bepaal hoeveel apps worden uitgevoerd in uw cluster** . u moet weten hoeveel apps er op het cluster worden uitgevoerd, met inbegrip van het huidige. Bij de standaard waarden voor elke Spark-instelling wordt ervan uitgegaan dat er vier apps gelijktijdig worden uitgevoerd. Daarom is er slechts 25% van het cluster beschikbaar voor elke app. Als u betere prestaties wilt, kunt u de standaard waarden onderdrukken door het aantal uitvoerender te wijzigen.

**Stap 2: uitvoerder instellen-geheugen** : het eerste dat moet worden ingesteld, is de uitvoerder van het geheugen. Het geheugen is afhankelijk van de taak die u wilt uitvoeren. U kunt de gelijktijdigheid verhogen door minder geheugen per uitvoerder toe te wijzen. Als er onvoldoende geheugen-uitzonde ringen worden weer geven wanneer u uw taak uitvoert, moet u de waarde voor deze para meter verhogen. Het is ook mogelijk om meer geheugen te verkrijgen door gebruik te maken van een cluster met een grotere hoeveelheid geheugen of de grootte van het cluster te verg Roten. Meer geheugens worden gebruikt om meer uitvoerders te gebruiken, wat betekent dat dit meer gelijktijdig is.

**Stap 3: de uitvoerder-kernen instellen** : voor I/O-intensieve workloads die geen complexe bewerkingen hebben, is het goed om te beginnen met een groot aantal uitvoeres-cores om het aantal parallelle taken per uitvoerder te verhogen. Het instellen van de uitvoerder-kernen op 4 is een goed begin.

```console
executor-cores = 4
```

Het verhogen van het aantal uitvoeringen-kern geheugens geeft u meer parallellisme zodat u kunt experimenteren met verschillende Voer-cores. Voor taken met complexere bewerkingen moet u het aantal kernen per uitvoerder verminderen. Als de uitvoerder is ingesteld op meer dan 4, wordt de garbagecollection mogelijk inefficiënt en wordt de prestaties verslechteren.

**Stap 4: de hoeveelheid garen geheugen in het cluster bepalen** : deze informatie is beschikbaar in Ambari. Navigeer naar GARENs en Bekijk het tabblad Contigs. Het garen geheugen wordt in dit venster weer gegeven.
Opmerking terwijl u zich in het venster bevindt, ziet u ook de standaard grootte van de garen container. De grootte van de garen container is hetzelfde als het geheugen per uitvoer parameter.

Totaal aantal GARENs = knoop punten * garen geheugen per knoop punt

**Stap 5: berekenen van num-uitvoerende modules**

**Geheugen beperking berekenen** : de para meter voor de num-uitvoerende modules wordt beperkt door het geheugen of de CPU. De geheugen beperking wordt bepaald door de hoeveelheid beschikbaar garen geheugen voor uw toepassing. Haal het totale aantal GARENs in de hele lijn op en Splits het in het geheugen. De beperking moet worden geschaald voor het aantal apps, zodat we delen door het aantal apps.

Geheugen beperking = (totale garen geheugen/uitvoerder geheugen)/aantal apps

**CPU-beperking berekenen** : de CPU-beperking wordt berekend als de totale virtuele kernen gedeeld door het aantal kernen per uitvoerder. Er zijn twee virtuele kernen voor elke fysieke kern. Net als bij de beperking van het geheugen, zijn er delen door het aantal apps.

virtuele kernen = (knoop punten in cluster * # fysieke kernen in knoop punt * 2) CPU-beperking = (totaal aantal virtuele kernen/aantal kernen per uitvoerder)/aantal apps

**Stel num-uitvoerende modules** in: de para meter num-uitvoerende modules wordt bepaald door de minimale geheugen beperking en de CPU-beperking te nemen. 

num-uitvoerende modules = min (totale aantal virtuele kernen/aantal kernen per uitvoerder, beschik bare garen geheugen/uitvoerder geheugen) bij het instellen van een hogere hoeveelheid num-uitvoerende modules is niet noodzakelijkerwijs de prestaties verbeterd. Houd er rekening mee dat het toevoegen van meer uitvoerder extra overhead voor elke extra uitvoerder toevoegt, waardoor de prestaties mogelijk kunnen afnemen. Num-uitvoerende modules worden begrensd door de cluster resources.

## <a name="example-calculation"></a>Voorbeeld berekening

Stel dat u momenteel een cluster hebt dat bestaat uit 8 D4v2-knoop punten met twee apps, met inbegrip van de toepassingen die u wilt uitvoeren.

**Stap 1: bepalen hoeveel apps worden uitgevoerd in uw cluster** : u weet dat u twee apps in uw cluster hebt, inclusief het abonnement dat u wilt uitvoeren.

**Stap 2: uitvoerder instellen-geheugen** : voor dit voor beeld bepalen we dat 6GB van de uitvoerder-geheugen voldoende is voor de I/O-intensieve taak.

```console
executor-memory = 6GB
```

**Stap 3: de uitvoerder instellen-kernen** – omdat dit een I/O-intensieve taak is, kunnen we het aantal kernen voor elke uitvoerder instellen op vier. Het instellen van kernen per uitvoerder tot meer dan vier kan leiden tot garbagecollection-problemen.

```console
executor-cores = 4
```

**Stap 4: het aantal garens in het cluster bepalen** . Ga naar Ambari om erachter te komen dat elke D4V2 een 25 GB van het garen-geheugen heeft. Omdat er 8 knoop punten zijn, wordt het beschik bare garen geheugen vermenigvuldigd met 8.

Totale garen geheugen = knoop punten * garen geheugen * per knoop punt totaal GARENs geheugen = 8 knoop punten * 25 GB = 200 GB

**Stap 5: berekenen van num-uitvoerende modules** : de para meter num-uitvoerende modules wordt bepaald door het minimum van de geheugen beperking en de CPU-beperking te nemen, gedeeld door het aantal apps dat op Spark wordt uitgevoerd.

**Geheugen beperking berekenen** : de geheugen beperking wordt berekend als het totale aantal garens gedeeld door het geheugen per uitvoerder.

Geheugen beperking = (totale garen geheugen/uitvoerbaar geheugen)/aantal apps geheugen beperking = (200 GB/6 GB)/2 geheugen beperking = 16 (afgerond) **CPU-beperking berekenen** : de CPU-beperking wordt berekend als het totale aantal garens op basis van de kernen per uitvoerder.

GARENs van hetzelfde niveau = knoop punten in cluster * aantal kernen per knoop punt * 2 garen kernen = 8 knoop punten * 8 kern geheugens per D14 * 2 = 128 CPU-beperking = (totaal aantal GARENs/aantal kernen per uitvoerder)/aantal apps CPU-beperking = (128/4)/2 CPU-beperking = 16

**Num-uitvoerende modules instellen**

num-executers = min (geheugen beperking, CPU-beperking) num-uitvoerende modules = min (16, 16) num-uitvoerendes = 16
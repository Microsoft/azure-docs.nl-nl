---
title: 'Prestaties afstemmen: Storm, HDInsight & Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Meer informatie over richt lijnen voor het afstemmen van de prestaties van een Azure Storm-topologie op een Azure HDInsight-cluster en Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 4db85357ee970d13d6b4fcce195cae66932bed18
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95912787"
---
# <a name="tune-performance-storm-hdinsight--azure-data-lake-storage-gen2"></a>Prestaties afstemmen: Storm, HDInsight & Azure Data Lake Storage Gen2

Begrijp de factoren die u moet overwegen wanneer u de prestaties van een Azure Storm-topologie afstemt. Het is bijvoorbeeld belang rijk om inzicht te krijgen in de kenmerken van het werk dat door de spouts en de bouten wordt uitgevoerd (of het werk I/O-of geheugen intensief is). In dit artikel wordt een reeks richt lijnen voor het afstemmen van prestaties besproken, waaronder veelvoorkomende problemen.

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Een Azure data Lake Storage Gen2-account**. Zie [Snelstartgids: een opslag account maken voor analyse](../common/storage-account-create.md)voor instructies over het maken van een.
* **Azure HDInsight-cluster** met toegang tot een Data Lake Storage Gen2-account. Zie [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md). Zorg ervoor dat Extern bureaublad voor het cluster is ingeschakeld.
* **Een storm-cluster wordt uitgevoerd op Data Lake Storage Gen2**. Zie [Storm op HDInsight](../../hdinsight/storm/apache-storm-overview.md)voor meer informatie.
* **Richt lijnen voor het afstemmen van de prestaties van data Lake Storage Gen2**.  Zie [Data Lake Storage Gen2 richt lijnen voor het afstemmen van prestaties](data-lake-storage-performance-tuning-guidance.md)voor algemene concepten.   

## <a name="tune-the-parallelism-of-the-topology"></a>De parallellisme van de topologie afstemmen

Mogelijk kunt u de prestaties verbeteren door de gelijktijdigheid van de I/O naar en van Data Lake Storage Gen2 te verhogen. Een storm-topologie bevat een set configuraties die de parallelliteit bepalen:
* Aantal werk processen (de werk nemers worden gelijkmatig verdeeld over de Vm's).
* Aantal exemplaren van de Spout-uitvoerder.
* Aantal exemplaren van de bout-uitvoerder.
* Aantal Spout-taken.
* Aantal Schicht-taken.

U kunt bijvoorbeeld in een cluster met vier Vm's en 4 werk processen, 32 Spout-uitvoeringen en 32 Spout-taken en 256 Schicht-uitvoerendes en 512-flitsen het volgende overwegen:

Elke Super Visor, een worker-knoop punt, heeft een JVM-proces (Java Virtual Machine) van één werk nemer. Dit JVM-proces beheert vier Spout-threads en 64-Schicht-threads. Binnen elke thread worden taken sequentieel uitgevoerd. Met de voor gaande configuratie heeft elke Spout-thread één taak en elke bout thread heeft twee taken.

In Storm zijn dit de verschillende onderdelen die van invloed zijn op het niveau van parallellisme dat u hebt:
* Het hoofd knooppunt (met de naam Nimbus in Storm) wordt gebruikt om taken te verzenden en te beheren. Deze knoop punten hebben geen invloed op de mate van parallelle uitvoering.
* De supervisor knooppunten. In HDInsight komt dit overeen met een worker-knoop punt Azure VM.
* De worker-taken zijn Storm processen die worden uitgevoerd in de Vm's. Elke werk taak komt overeen met een JVM-exemplaar. Storm distribueert zo gelijkmatig mogelijk het aantal werk processen dat u opgeeft voor de worker-knoop punten.
* Exemplaren van de Spout-en bout-uitvoerder. Elk exemplaar van de uitvoerder komt overeen met een thread die binnen de werk nemers wordt uitgevoerd (JVMs).
* Storm-taken. Dit zijn logische taken die voor elk van deze threads worden uitgevoerd. Hiermee wordt het niveau van de parallelle uitvoering niet gewijzigd. u moet dus evalueren of u meerdere taken per uitvoerder nodig hebt of niet.

### <a name="get-the-best-performance-from-data-lake-storage-gen2"></a>Profiteer van de beste prestaties van Data Lake Storage Gen2

Wanneer u met Data Lake Storage Gen2 werkt, krijgt u de beste prestaties als u het volgende doet:
* Coalesce uw kleine toevoegingen tot grotere grootten.
* Doe zoveel gelijktijdige aanvragen als u dat kunt doen. Omdat met elke Schicht-thread Lees bewerkingen worden geblokkeerd, wilt u ergens in het bereik van 8-12-threads per kern hebben. Dit houdt de NIC en de CPU-goed gebruik. Een grotere virtuele machine maakt meer gelijktijdige aanvragen mogelijk.  

### <a name="example-topology"></a>Voorbeeld topologie

Stel dat u een cluster met 8 worker-knoop punten hebt met een D13v2 Azure-VM. Deze VM heeft 8 kernen, dus in de 8 worker-knoop punten hebt u 64 totale kernen.

Stel dat we 8 Schicht-threads per kern uitvoeren. Dit betekent dat er 64 kernen zijn, dat wil zeggen dat 512 totaal aantal malen uitvoeringen (dat wil zeggen, threads). Stel dat we in dit geval beginnen met één JVM per VM en hoofd zakelijk de gelijktijdigheid van de thread in de JVM gebruiken om gelijktijdig in te stellen. Dit betekent dat er 8 werk taken (één per Azure-VM) en 512-flitsen moeten worden uitgevoerd. Op basis van deze configuratie probeert Storm de werk nemers gelijkmatig te verdelen over werk knooppunten (ook wel Super Visor-knoop punten genoemd), waardoor elk worker-knoop punt 1 JVM. Nu binnen de supervisors probeert Storm de uitvoerendeers gelijkmatig te verdelen tussen de supervisors, waarbij elke Super Visor (dat wil zeggen JVM) 8 threads elke.

## <a name="tune-additional-parameters"></a>Aanvullende para meters afstemmen
Nadat u de basis topologie hebt, kunt u overwegen of u de para meters wilt aanpassen:
* **Aantal JVMs per worker-knoop punt.** Als u een grote gegevens structuur (bijvoorbeeld een opzoek tabel) hebt die u in het geheugen host, is voor elke JVM een afzonderlijke kopie vereist. U kunt ook de gegevens structuur van een groot aantal threads gebruiken als u minder JVMs hebt. Voor I/O van de schicht maakt het aantal JVMs niet zo veel verschil als het aantal threads dat in die JVMs is toegevoegd. Voor het gemak is het een goed idee om één JVM per werk nemer te hebben. Afhankelijk van wat uw schicht doet of welke toepassings verwerking u nodig hebt, moet u dit echter mogelijk wijzigen.
* **Aantal Spout-uitvoerders.** Omdat in het voor gaande voor beeld schichten worden gebruikt voor het schrijven naar Data Lake Storage Gen2, is het aantal spouts niet rechtstreeks relevant voor de flits prestaties. Afhankelijk van de hoeveelheid verwerkings-of I/O-bewerkingen in de Spout, is het echter een goed idee om de spouts voor de beste prestaties af te stemmen. Zorg ervoor dat u voldoende spouts hebt om de schichten te kunnen blijven gebruiken. De uitvoer tarieven van de spouts moeten overeenkomen met de door Voer van de schichten. De werkelijke configuratie is afhankelijk van de Spout.
* **Aantal taken.** Elke Schicht wordt uitgevoerd als één thread. Extra taken per Schicht bieden geen verdere gelijktijdigheid. De enige keer dat ze van nut zijn, is als het proces van het bevestigen van de tuple een groot deel van de uitvoerings tijd van de flits kost. Het is een goed idee om veel Tuples in een grotere toevoeg groep te groeperen voordat u een bevestiging van de schicht verzendt. In de meeste gevallen bieden meerdere taken daarom geen extra voor deel.
* **Lokale groep of wille keurige groepering.** Als deze instelling is ingeschakeld, worden Tuples verzonden naar schichten binnen hetzelfde werk proces. Dit beperkt de communicatie tussen processen en netwerk aanroepen. Dit wordt aanbevolen voor de meeste topologieën.

Dit basis scenario is een goed uitgangs punt. Test met uw eigen gegevens om de voor gaande para meters te verfijnen om optimale prestaties te krijgen.

## <a name="tune-the-spout"></a>De Spout afstemmen

U kunt de volgende instellingen wijzigen om de Spout af te stemmen.

- **Tuple-time-out: topologie. Message. timeout. sec**. Met deze instelling wordt bepaald hoe lang het duurt voordat een bericht is voltooid en dat er een bevestiging wordt ontvangen voordat dit wordt beschouwd als mislukt.

- **Maxi maal geheugen per werk proces: worker. childopts**. Met deze instelling kunt u aanvullende opdracht regel parameters voor de Java-werk rollen opgeven. De meest gebruikte instelling hier is XmX, waarmee het maximale geheugen wordt bepaald dat is toegewezen aan de heap van een JVM.

- **Maximum aantal spout in behandeling: topologie. max. Spout. pending**. Met deze instelling bepaalt u het aantal Tuples dat kan vlucht (nog niet bevestigd op alle knoop punten in de topologie) per Spout-thread.

  Een goede berekening is om de grootte van elk van uw Tuples te schatten. Bepaal vervolgens hoeveel geheugen één Spout-thread heeft. Het totale geheugen dat is toegewezen aan een thread, gedeeld door deze waarde, geeft u de bovengrens voor de para meter Max spout in behandeling.

De standaard Data Lake Storage Gen2 Storm-Schicht heeft een para meter voor de grootte van het synchronisatie beleid (fileBufferSize) die kan worden gebruikt om deze para meter af te stemmen.

In I/O-intensieve topologieën is het een goed idee om elke Schicht-thread naar een eigen bestand te schrijven en een file Rotation-beleid (fileRotationSize) in te stellen. Wanneer het bestand een bepaalde grootte bereikt, wordt de stroom automatisch leeg gemaakt en wordt er een nieuw bestand geschreven naar. De aanbevolen bestands grootte voor rotatie is 1 GB.

## <a name="monitor-your-topology-in-storm"></a>Uw topologie bewaken in Storm  
Terwijl uw topologie wordt uitgevoerd, kunt u deze bewaken in de Storm-gebruikers interface. Hier volgen de belangrijkste para meters om te kijken naar:

* **Totale latentie voor het uitvoeren van processen.** Dit is de gemiddelde tijd die een tuple nodig heeft om te worden verzonden door de Spout, verwerkt door de flits en bevestigd.

* **Totale aantal latentie processen van bout.** Dit is de gemiddelde tijd die de tuple bij de flits heeft besteed tot deze een bevestiging ontvangt.

* **Totale latentie van flits uitvoering.** Dit is de gemiddelde tijd die is besteed door de schicht in de methode Execute.

* **Aantal fouten.** Dit verwijst naar het aantal Tuples dat niet volledig kan worden verwerkt voordat er een time-out is opgetreden.

* **Capaciteit.** Dit is een meting van de mate waarin uw systeem actief is. Als dit nummer 1 is, werken uw schichten net zo snel als dat kan. Als deze kleiner is dan 1, verhoogt u de parallelle uitvoering. Als deze groter is dan 1, vermindert u de parallelle uitvoering.

## <a name="troubleshoot-common-problems"></a>Veelvoorkomende problemen oplossen
Hier volgen enkele veelvoorkomende scenario's voor het oplossen van problemen.
* **Veel Tuples hebben een time-out.** Bekijk elk knoop punt in de topologie om te bepalen waar het knel punt zich bevindt. De meest voorkomende reden hiervoor is dat de schichten de spouts niet kunnen blijven gebruiken. Dit leidt tot Tuples clogging de interne buffers tijdens het wachten op verwerking. Overweeg de time-outwaarde te verhogen of het maximum aantal spout in behandeling te verminderen.

* **Er is een hoge latentie voor het uitvoeren van processen, maar een latentie van een beperkt aantal processen.** In dit geval is het mogelijk dat de Tuples niet snel genoeg worden bevestigd. Controleer of er voldoende bevestigingsers zijn. Een andere mogelijkheid is dat ze te lang wachten in de wachtrij voordat de bouten ze gaan verwerken. Verklein het maximum aantal spout in behandeling.

* **Er is een latentie met hoge flits uitvoering.** Dit betekent dat de methode Execute () van uw schicht te lang duurt. Optimaliseer de code of kijk naar het gedrag voor schrijf grootten en leegmaken.

### <a name="data-lake-storage-gen2-throttling"></a>Data Lake Storage Gen2 beperking
Als u de limieten bereikt van de band breedte van Data Lake Storage Gen2, worden er mogelijk taak fouten weer gegeven. Raadpleeg de taak logboeken voor het beperken van fouten. U kunt de parallellisme verkleinen door de container grootte te verg Roten.    

Als u wilt controleren of u een beperking krijgt, schakelt u de logboek registratie voor fout opsporing in aan de client zijde:

1. In **Ambari**  >  **Storm**  >  **config**  >  **Advanced Storm-worker-log4j**, Wijzig **&lt; root level = "info &gt; "** naar **&lt; root level = "Debug &gt; "**. Start alle knoop punten/service opnieuw op om de configuratie van kracht te laten worden.
2. Bewaak de Storm-topologie logboeken op worker-knoop punten (onder/var/log/Storm/worker-Artifacts/ &lt; &gt; / &lt; -topologie poort &gt; /Worker.log) voor data Lake Storage Gen2 beperkings uitzonderingen.

## <a name="next-steps"></a>Volgende stappen
In [deze blog](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)kunt u naar aanvullende prestaties afstemmen voor Storm.

Voor een extra voor beeld dat wordt uitgevoerd, raadpleegt u [dit op github](https://github.com/hdinsight/storm-performance-automation).
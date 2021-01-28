---
title: Aangepaste MapReduce-Program ma's uitvoeren-Azure HDInsight
description: Wanneer en hoe u aangepaste Apache MapReduce-Program ma's uitvoert op Azure HDInsight-clusters.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 0f7a3676db14dbf61d3976690c8f9c71a660f889
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943228"
---
# <a name="run-custom-mapreduce-programs"></a>Aangepaste MapReduce-programma's uitvoeren

Op Apache Hadoop gebaseerde big data systemen zoals HDInsight kunnen gegevens worden verwerkt met behulp van een breed scala aan hulpprogram ma's en technologieën. In de volgende tabel worden de belangrijkste voor delen en overwegingen voor elk item beschreven.

| Query mechanisme | Voordelen | Overwegingen |
| --- | --- | --- |
| **Apache Hive met behulp van HiveQL** | <ul><li>Een uitstekende oplossing voor batch verwerking en analyse van grote hoeveel heden onveranderbare gegevens, voor gegevens samenvatting en voor query's op aanvraag. Er wordt een bekende SQL-achtige syntaxis gebruikt.</li><li>Het kan worden gebruikt voor het produceren van permanente tabellen met gegevens die eenvoudig kunnen worden gepartitioneerd en geïndexeerd.</li><li>Meerdere externe tabellen en weer gaven kunnen worden gemaakt over dezelfde gegevens.</li><li>Het biedt ondersteuning voor een eenvoudige data warehouse-implementatie die enorme mogelijkheden voor uitschalen en fout tolerantie biedt voor gegevens opslag en-verwerking.</li></ul> | <ul><li>De bron gegevens moeten ten minste een bepaalde Identificeer bare structuur hebben.</li><li>Het is niet geschikt voor realtime query's en updates op rijniveau. Het wordt het beste gebruikt voor batch taken via grote gegevens sets.</li><li>Het kan zijn dat sommige typen complexe verwerkings taken niet kunnen worden uitgevoerd.</li></ul> |
| **Apache-Pig met behulp van de Latijnse** | <ul><li>Een uitstekende oplossing voor het bewerken van gegevens als sets, samen voegen en filteren, en het Toep assen van functies op records of groepen records, en voor herstructurering van gegevens door het definiëren van kolommen, het groeperen van waarden of het converteren van kolommen naar rijen.</li><li>Het kan een op werk stroom gebaseerde aanpak gebruiken als een reeks bewerkingen op gegevens.</li></ul> | <ul><li>SQL-gebruikers kunnen de waarde van Pig Latijn minder kennen en moeilijker te gebruiken dan HiveQL.</li><li>De standaard uitvoer is doorgaans een tekst bestand en kan moeilijker worden gebruikt met visualisatie hulpprogramma's zoals Excel. Doorgaans maakt u een Hive-tabel op de uitvoer.</li></ul> |
| **Aangepaste kaart/verminderen** | <ul><li>Het biedt volledige controle over de kaart en verlaagt de fase en de uitvoering.</li><li>Hiermee kunnen query's worden geoptimaliseerd voor maximale prestaties van het cluster, of om de belasting van de servers en het netwerk tot een minimum te beperken.</li><li>De onderdelen kunnen worden geschreven in een bereik van bekende talen.</li></ul> | <ul><li>Het is moeilijker dan het gebruik van Pig of Hive, omdat u een eigen kaart moet maken en onderdelen kunt verminderen.</li><li>Processen waarvoor het samen voegen van gegevens sets is moeilijker te implementeren.</li><li>Hoewel er test raamwerken beschikbaar zijn, is de fout opsporings code complexer dan een normale toepassing omdat de code wordt uitgevoerd als een batch-taak onder het beheer van de Hadoop-taak planner.</li></ul> |
| **Apache HCatalog** | <ul><li>Het bevat de details van de opslag, waardoor het beheer gemakkelijker wordt en het verwijderen van de nood zaak van gebruikers om te weten waar de gegevens zijn opgeslagen.</li><li>Hiermee wordt een melding van gebeurtenissen, zoals de beschik baarheid van gegevens, ingeschakeld, zodat andere hulpprogram ma's, zoals Oozie, kunnen detecteren wanneer er bewerkingen hebben plaatsgevonden.</li><li>Het biedt een relationele weer gave van gegevens, inclusief partitioneren op basis van de sleutel, en maakt de gegevens eenvoudig toegankelijk.</li></ul> | <ul><li>Het biedt standaard ondersteuning voor RCFile, CSV-tekst, JSON-tekst, SequenceFile en ORC-bestands indelingen, maar u moet mogelijk een aangepaste SerDe schrijven voor andere indelingen.</li><li>HCatalog is niet thread-veilig.</li><li>Er gelden enkele beperkingen voor de gegevens typen voor kolommen wanneer u de HCatalog-lader in Pig-scripts gebruikt. Zie [HCatLoader-gegevens typen](https://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes) in de Apache HCatalog-documentatie voor meer informatie.</li></ul> |

Normaal gesp roken gebruikt u de eenvoudigste van deze benaderingen waarmee u de gewenste resultaten krijgt. Het is bijvoorbeeld mogelijk dat u een dergelijke resultaten kunt bedenken met behulp van gewoon Hive, maar voor complexere scenario's moet u wellicht Pig gebruiken of zelfs uw eigen kaart schrijven en onderdelen verminderen. U kunt er ook voor kiezen om, na het experimenteren met hive of varken, dat aangepaste kaart en het verminderen van onderdelen betere prestaties te bieden omdat u de verwerking nauw keuriger en optimaliseert.

## <a name="custom-mapreduce-components"></a>Aangepaste kaart/onderdelen verminderen

Kaart/reductie code bestaat uit twee afzonderlijke functies die zijn geïmplementeerd als **kaart** en **verminderen** onderdelen. Het **overzichts** onderdeel wordt parallel uitgevoerd op meerdere cluster knooppunten, elk knoop punt dat de toewijzing toepast op de eigen subset van de gegevens van het knoop punt. Met het onderdeel **verminderen** worden de resultaten van alle kaart functies gesorteerd en samenvatten. Zie [MapReduce gebruiken in Hadoop op HDInsight](hdinsight-use-mapreduce.md)voor meer informatie over deze twee onderdelen.

In de meeste scenario's voor het verwerkings proces van HDInsight is het eenvoudiger en efficiënter om een abstracte abstractie op een hoger niveau, zoals varken of Hive, te gebruiken. U kunt ook een aangepaste kaart maken en onderdelen beperken voor gebruik in Hive-scripts om geavanceerde verwerking uit te voeren.

Aangepaste kaart-en reductie onderdelen worden meestal geschreven in Java. Hadoop biedt een streaming-interface waarmee u ook onderdelen kunt gebruiken die zijn ontwikkeld in andere talen, zoals C#, F #, Visual Basic, python en Java script.

* Zie [Java MapReduce-Program Ma's ontwikkelen voor Hadoop in HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)voor een overzicht van het ontwikkelen van aangepaste Java MapReduce-Program ma's.

Overweeg het maken van uw eigen kaart en verminder de onderdelen voor de volgende voor waarden:

* U moet gegevens verwerken die volledig niet zijn gestructureerd door de gegevens te parseren en aangepaste logica te gebruiken voor het verkrijgen van gestructureerde informatie.
* U wilt complexe taken uitvoeren die moeilijk (of onmogelijk) zijn om te worden uitdrukken in varken of Hive zonder een UDF te maken. U moet bijvoorbeeld een externe geocoderings service gebruiken om de coördinaten van de breedte-en lengte graad of IP-adressen in de bron gegevens te converteren naar geografische locatie namen.
* U wilt uw bestaande .NET-, python-of Java script-code opnieuw gebruiken in kaart-en reductie onderdelen door gebruik te maken van de Hadoop streaming-interface.

## <a name="upload-and-run-your-custom-mapreduce-program"></a>Uw aangepaste MapReduce-programma uploaden en uitvoeren

De meest voorkomende MapReduce-Program ma's zijn geschreven in Java en gecompileerd naar een jar-bestand.

1. Nadat u uw MapReduce-programma hebt ontwikkeld, gecompileerd en getest, gebruikt u de `scp` opdracht om het jar-bestand naar de hoofd knooppunt te uploaden.

    ```cmd
    scp mycustomprogram.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Vervang CLUSTERNAME door de naam van het cluster. Als u een wacht woord hebt gebruikt om het SSH-account te beveiligen, wordt u gevraagd het wacht woord in te voeren. Als u een certificaat hebt gebruikt, moet u mogelijk de `-i` para meter gebruiken om het bestand met de persoonlijke sleutel op te geven.

1. Gebruik de [ssh-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster. Voer vervolgens deze opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Voer vanuit de SSH-sessie uw MapReduce-programma uit via GARENs.

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    Met deze opdracht wordt de MapReduce-taak naar GARENs verzonden. Het invoer bestand is `/example/data/sample.log` en de uitvoermap `/example/data/logoutput` . Het invoer bestand en uitvoer bestanden worden opgeslagen in de standaard opslag voor het cluster.

## <a name="next-steps"></a>Volgende stappen

* [C# gebruiken met MapReduce streaming op Apache Hadoop in HDInsight](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Java MapReduce-programma's ontwikkelen voor Apache Hadoop in HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Azure-toolkit voor Eclipse gebruiken om Apache Spark-toepassingen voor een HDInsight-cluster te maken](../spark/apache-spark-eclipse-tool-plugin.md)
* [Met python door de gebruiker gedefinieerde functies (UDF) met Apache Hive en Apache varken in HDInsight gebruiken](python-udf-hdinsight.md)

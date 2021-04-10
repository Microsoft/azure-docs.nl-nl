---
title: JSON van & proces analyseren met Apache Hive-Azure HDInsight
description: Meer informatie over het gebruik van JSON-documenten en het analyseren ervan met behulp van Apache Hive in azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: d3c5077450dd4ec59f5ea7bc8f37879f9aa775bf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868933"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>JSON-documenten verwerken en analyseren met behulp van Apache Hive in azure HDInsight

Meer informatie over het verwerken en analyseren van JavaScript Object Notation (JSON)-bestanden met behulp van Apache Hive in azure HDInsight. In dit artikel wordt het volgende JSON-document gebruikt:

```json
{
  "StudentId": "trgfg-5454-fdfdg-4346",
  "Grade": 7,
  "StudentDetails": [
    {
      "FirstName": "Peggy",
      "LastName": "Williams",
      "YearJoined": 2012
    }
  ],
  "StudentClassCollection": [
    {
      "ClassId": "89084343",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "High",
      "Score": 93,
      "PerformedActivity": false
    },
    {
      "ClassId": "78547522",
      "ClassParticipation": "NotSatisfied",
      "ClassParticipationRank": "None",
      "Score": 74,
      "PerformedActivity": false
    },
    {
      "ClassId": "78675563",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "Low",
      "Score": 83,
      "PerformedActivity": true
    }
  ]
}
```

Het bestand bevindt zich op `wasb://processjson@hditutorialdata.blob.core.windows.net/` . Zie voor meer informatie over het gebruik van Azure Blob Storage met HDInsight [gebruik van HDFS-compatibele Azure Blob Storage met Apache Hadoop in HDInsight](../hdinsight-hadoop-use-blob-storage.md). U kunt het bestand kopiëren naar de standaard container van uw cluster.

In dit artikel gebruikt u de Apache Hive-console. Zie [Apache Ambari-Hive View gebruiken met Apache Hadoop in HDInsight](apache-hadoop-use-hive-ambari-view.md)voor instructies over het openen van de Hive-console.

> [!NOTE]  
> De Hive-weer gave is niet meer beschikbaar in HDInsight 4,0.

## <a name="flatten-json-documents"></a>JSON-documenten afvlakken

De methoden die in de volgende sectie worden weer gegeven, vereisen dat het JSON-document uit één rij bestaat. Daarom moet u het JSON-document samen voegen met een teken reeks. Als uw JSON-document al is afgevlakt, kunt u deze stap overs Laan en direct naar de volgende sectie gaan voor het analyseren van JSON-gegevens. Voer het volgende script uit om het JSON-document plat te leggen:

```sql
DROP TABLE IF EXISTS StudentsRaw;
CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

DROP TABLE IF EXISTS StudentsOneLine;
CREATE EXTERNAL TABLE StudentsOneLine
(
  json_body string
)
STORED AS TEXTFILE LOCATION '/json/students';

INSERT OVERWRITE TABLE StudentsOneLine
SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
      GROUP BY INPUT__FILE__NAME;

SELECT * FROM StudentsOneLine
```

Het onbewerkte JSON-bestand bevindt zich op `wasb://processjson@hditutorialdata.blob.core.windows.net/` . De **StudentsRaw** -Hive-tabel verwijst naar het onbewerkte JSON-document dat niet wordt afgevlakt.

De **StudentsOneLine** -Hive-tabel slaat de gegevens op in het standaard bestands systeem HDInsight onder het pad **/JSON/Students/** .

Met de instructie **Insert** wordt de **StudentOneLine** -tabel gevuld met de afgevlakte JSON-gegevens.

De instructie **Select** retourneert alleen één rij.

Dit is de uitvoer van de **Select** -instructie:

:::image type="content" source="./media/using-json-in-hive/hdinsight-flatten-json.png" alt-text="HDInsight-afvlakking van het JSON-document" border="true":::

## <a name="analyze-json-documents-in-hive"></a>JSON-documenten in Hive analyseren

Hive biedt drie verschillende mechanismen voor het uitvoeren van query's op JSON-documenten of u kunt uw eigen methoden schrijven:

* Gebruik de get_json_object door de gebruiker gedefinieerde functie (UDF).
* Gebruik de json_tuple UDF.
* Gebruik de aangepaste Serialisatiefunctie/deserializer (SerDe).
* Schrijf uw eigen UDF door python of andere talen te gebruiken. Zie [PYTHON UDF with Apache Hive en Apache varken](./python-udf-hdinsight.md)voor meer informatie over het uitvoeren van uw eigen python-code met Hive.

### <a name="use-the-get_json_object-udf"></a>De get_json_object UDF gebruiken

Hive biedt een ingebouwde UDF met de naam [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) die tijdens runtime query's uitvoeren op JSON. Deze methode heeft twee argumenten: de tabel naam en de naam van de methode. De naam van de methode heeft het platte JSON-document en het JSON-veld dat moet worden geparseerd. We bekijken een voor beeld om te zien hoe deze UDF werkt.

De volgende query retourneert de voor naam en achternaam voor elke student:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Dit is de uitvoer wanneer u deze query uitvoert in het console venster:

:::image type="content" source="./media/using-json-in-hive/hdinsight-get-json-object.png" alt-text="Apache Hive Hiermee wordt JSON-object opgehaald" border="true":::

Er gelden beperkingen voor de get_json_object UDF:

* Omdat voor elk veld in de query de query opnieuw moet worden geparseerd, heeft dit invloed op de prestaties.
* **Ophalen \_ JSON_OBJECT ()** retourneert de teken reeks weergave van een matrix. Als u deze matrix wilt omzetten in een Hive-matrix, moet u reguliere expressies gebruiken om de vier Kante haken "[" en "]" te vervangen. vervolgens moet u split aanroepen om de matrix op te halen.

Deze conversie is de reden waarom de Hive-wiki adviseert om **json_tuple** te gebruiken.  

### <a name="use-the-json_tuple-udf"></a>De json_tuple UDF gebruiken

Een andere UDF die wordt verschaft door Hive heet [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), wat beter is dan [get_ JSON _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Deze methode heeft een set sleutels en een JSON-teken reeks. Retourneert vervolgens een tuple met waarden. Met de volgende query wordt de student-ID en de kwaliteit van het JSON-document geretourneerd:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

De uitvoer van dit script in de Hive-console:

:::image type="content" source="./media/using-json-in-hive/hdinsight-json-tuple.png" alt-text="Apache Hive JSON-query resultaten" border="true":::

De `json_tuple` UDF maakt gebruik van de syntaxis voor de [zijdelingse weer gave](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) in Hive, waarmee JSON \_ tuple een virtuele tabel kan maken door de UDT-functie toe te passen op elke rij van de oorspronkelijke tabel. Complexe JSONs zijn te moeilijk geworden vanwege het herhaalde gebruik van een **laterere weer gave**. Bovendien kunnen geneste JSONs niet worden verwerkt met **JSON_TUPLE** .

### <a name="use-a-custom-serde"></a>Een aangepaste SerDe gebruiken

SerDe is de beste keuze voor het parseren van geneste JSON-documenten. Hiermee kunt u het JSON-schema definiëren, waarna u het schema kunt gebruiken om de documenten te parseren. Zie [een aangepaste JSON-SerDe gebruiken met Microsoft Azure HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/)voor instructies.

## <a name="summary"></a>Samenvatting

Het type JSON-operator in het onderdeel dat u kiest, is afhankelijk van uw scenario. Als u een eenvoudig JSON-document en één veld wilt opzoeken, kiest u de Hive UDF- **get_json_object**. Als u meer dan één sleutel op wilt zoeken, kunt u **json_tuple** gebruiken. Voor geneste documenten gebruikt u de **JSON-SerDe**.

## <a name="next-steps"></a>Volgende stappen

Zie voor verwante artikelen:

* [Apache Hive en HiveQL met Apache Hadoop in HDInsight gebruiken voor het analyseren van een voor beeld van een Apache log4j-bestand](./hdinsight-use-hive.md)
* [Vertraagde vlucht gegevens analyseren met behulp van interactieve Query's in HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
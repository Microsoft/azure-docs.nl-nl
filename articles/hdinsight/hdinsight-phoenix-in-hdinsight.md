---
title: Apache Phoenix in HDInsight-Azure HDInsight
description: Overzicht van Apache Phoenix
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 3af48c93633709c9b5814caa99c222e24e402a4a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945230"
---
# <a name="apache-phoenix-in-azure-hdinsight"></a>Apache Phoenix in azure HDInsight

[Apache Phoenix](https://phoenix.apache.org/) is een open-source, enorm parallelle relationele data base-laag die is gebouwd op [Apache HBase](hbase/apache-hbase-overview.md). Met Phoenix kunt u SQL-achtige query's gebruiken via HBase. Phoenix maakt gebruik van JDBC-Stuur Programma's onder om gebruikers in staat te stellen SQL-tabellen, indexen, weer gaven en reeksen en upsert te maken, verwijderen, wijzigen en meerdere rijen tegelijk en in bulk. Phoenix gebruikt noSQL systeem eigen compilatie in plaats van MapReduce te gebruiken voor het compileren van query's, waardoor toepassingen met lage latentie op HBase kunnen worden gemaakt. Phoenix voegt coprocessoren toe ter ondersteuning van het uitvoeren van de door de client geleverde code in de adres ruimte van de server, waarbij de code wordt uitgevoerd die zich in de gegevens bevindt. Deze aanpak minimaliseert de client/server-gegevens overdracht.

Apache Phoenix opent big data query's naar niet-ontwikkel aars die een SQL-achtige syntaxis kunnen gebruiken in plaats van Program meren. Phoenix is zeer geoptimaliseerd voor HBase, in tegens telling tot andere hulpprogram ma's, zoals [Apache Hive](hadoop/hdinsight-use-hive.md) en Apache Spark SQL. Het voor deel van ontwikkel aars is het schrijven van zeer krachtige query's met veel minder code.

Wanneer u een SQL-query verzendt, compileert Phoenix de query om systeem eigen HBase-aanroepen uit te voeren en wordt de scan (of het plan) parallel uitgevoerd voor Optima Lise ring. Deze laag van abstractie maakt de ontwikkelaar de mogelijkheid om MapReduce-taken te schrijven, om in plaats daarvan zich te richten op de bedrijfs logica en de werk stroom van hun toepassing rond de big data opslag van Phoenix.

## <a name="query-performance-optimization-and-other-features"></a>Optimalisatie van query prestaties en andere functies

Apache Phoenix voegt diverse prestatie verbeteringen en-functies toe aan HBase query's.

### <a name="secondary-indexes"></a>Secundaire indexen

HBase heeft een enkele index die lexicographically is gesorteerd op de primaire rij. Deze records zijn alleen toegankelijk via de rij met rijen. Voor het verkrijgen van toegang tot records via een andere kolom dan de rij, moeten alle gegevens worden gescand tijdens het Toep assen van het vereiste filter. In een secundaire index vormen de kolommen of expressies die worden geïndexeerd een alternatieve rijdefinitie, waardoor lookups en bereik scans voor die index mogelijk zijn.

Maak een secundaire index met de `CREATE INDEX` opdracht:

```sql
CREATE INDEX ix_purchasetype on SALTEDWEBLOGS (purchasetype, transactiondate) INCLUDE (bookname, quantity);
```

Deze aanpak kan leiden tot een aanzienlijke prestatie verhoging voor het uitvoeren van query's met één geïndexeerde waarde. Dit type secundaire index is een **bedekte index** die alle kolommen bevat die in de query zijn opgenomen. Daarom is het opzoeken van de tabel niet vereist en de index voldoet aan de volledige query.

### <a name="views"></a>Weergaven

Phoenix-weer gaven bieden een manier om een HBase-beperking te overwinnen, waarbij de prestaties beginnen te verminderen wanneer u meer dan ongeveer 100 fysieke tabellen maakt. In Phoenix-weer gaven kunnen meerdere *virtuele tabellen* één onderliggende fysieke HBase-tabel delen.

Het maken van een Phoenix-weer gave is vergelijkbaar met de standaard syntaxis van SQL-weer gave. Het enige verschil is dat u kolommen voor uw weer gave kunt definiëren, naast de kolommen die zijn overgenomen van de basis tabel. U kunt ook nieuwe `KeyValue` kolommen toevoegen.

Dit is bijvoorbeeld een fysieke tabel `product_metrics` met de naam met de volgende definitie:

```sql
CREATE  TABLE product_metrics (
    metric_type CHAR(1),
    created_by VARCHAR,
    created_date DATE,
    metric_id INTEGER
    CONSTRAINT pk PRIMARY KEY (metric_type, created_by, created_date, metric_id));
```

Definieer een weer gave over deze tabel, met aanvullende kolommen:

```sql
CREATE VIEW mobile_product_metrics (carrier VARCHAR, dropped_calls BIGINT) AS
SELECT * FROM product_metrics
WHERE metric_type = 'm';
```

Gebruik de instructie om later meer kolommen toe te voegen `ALTER VIEW` .

### <a name="skip-scan"></a>Scan overs Laan

Scan overs Laan gebruikt een of meer kolommen van een samengestelde index om verschillende waarden te vinden. In tegens telling tot een bereik scan, wordt de functie voor het scannen van de intra-rij door lopen verbeterd, wat leidt tot [betere prestaties](https://phoenix.apache.org/performance.html#Skip-Scan). Tijdens het scannen wordt de eerste overeenkomende waarde overgeslagen samen met de index totdat de volgende waarde wordt gevonden.

Bij een scan overs Laan wordt gebruikgemaakt `SEEK_NEXT_USING_HINT` van de opsomming van het HBase-filter. Met behulp `SEEK_NEXT_USING_HINT` van de overgeslagen scan houdt u bij welke set sleutels of bereiken van sleutels worden gezocht in elke kolom. De scan overs Laan gaat vervolgens naar een sleutel die werd door gegeven tijdens de filter evaluatie en bepaalt of het een van de combi Naties is. Als dat niet het geval is, evalueert de scan overs Laan de volgende hoogste sleutel om naar te gaan.

### <a name="transactions"></a>Transacties

Hoewel HBase voorziet in trans acties op rijniveau, kan Phoenix worden geïntegreerd met [tephra](https://tephra.io/) om ondersteuning voor meerdere rijen en cross-Table trans acties toe te voegen met volledige [acid](https://en.wikipedia.org/wiki/ACID) -semantiek.

Net als bij traditionele SQL-trans acties, kunt u met trans acties die via de Phoenix-transactie beheerder worden ontvangen, ervoor zorgen dat een atomische eenheid van gegevens wordt upserted, waarbij de trans actie wordt teruggedraaid als de upsert-bewerking mislukt voor een tabel die is ingeschakeld voor trans acties.

Raadpleeg de [documentatie over de Apache Phoenix-trans actie](https://phoenix.apache.org/transactions.html)om Phoenix-trans acties in te scha kelen.

Als u een nieuwe tabel wilt maken waarvoor trans acties zijn ingeschakeld, stelt `TRANSACTIONAL` u de eigenschap `true` in op een `CREATE` instructie:

```sql
CREATE TABLE my_table (k BIGINT PRIMARY KEY, v VARCHAR) TRANSACTIONAL=true;
```

Als u een bestaande tabel transactioneel wilt wijzigen, gebruikt u dezelfde eigenschap in een `ALTER` instructie:

```sql
ALTER TABLE my_other_table SET TRANSACTIONAL=true;
```

> [!NOTE]  
> U kunt een transactionele tabel niet weer omzetten naar een niet-transactionele bewerking.

### <a name="salted-tables"></a>Gezouteerde tabellen

De *regio server hotspotting* kan zich voordoen wanneer u records met opeenvolgende sleutels naar HBase schrijft. Hoewel u meerdere regio servers in uw cluster hebt, zijn uw schrijf bewerkingen allemaal op slechts één. Met deze concentratie wordt het hotspotting-probleem gemaakt waarbij in plaats van uw schrijf werk belasting wordt gedistribueerd over alle beschik bare regio servers, maar één de belasting verwerkt. Aangezien elke regio een vooraf gedefinieerde maximum grootte heeft, wordt deze in twee kleine regio's gesplitst wanneer een regio die maximale grootte bereikt. Als dat gebeurt, neemt een van deze nieuwe regio's alle nieuwe records, waardoor de nieuwe hotspot wordt.

Om dit probleem te verhelpen en de prestaties te verbeteren, kunt u tabellen vooraf splitsen, zodat alle regio servers gelijkelijk worden gebruikt. Phoenix biedt *gezouten tabellen*, waarmee de zout byte op transparante wijze kan worden toegevoegd aan de rij voor een bepaalde tabel. De tabel is vooraf gesplitst op de grens van de Salt-byte om te zorgen voor gelijke belasting distributie tussen regio servers tijdens de eerste fase van de tabel. Met deze aanpak wordt de schrijf belasting gedistribueerd over alle beschik bare regio servers, waardoor de schrijf-en lees prestaties worden verbeterd. Als u een tabel wilt Salt, geeft u de `SALT_BUCKETS` eigenschap Table op wanneer de tabel wordt gemaakt:

```sql
CREATE TABLE Saltedweblogs (
    transactionid varchar(500) Primary Key,
    transactiondate Date NULL,
    customerid varchar(50) NULL,
    bookid varchar(50) NULL,
    purchasetype varchar(50) NULL,
    orderid varchar(50) NULL,
    bookname varchar(50) NULL,
    categoryname varchar(50) NULL,
    invoicenumber varchar(50) NULL,
    invoicestatus varchar(50) NULL,
    city varchar(50) NULL,
    state varchar(50) NULL,
    paymentamount DOUBLE NULL,
    quantity INTEGER NULL,
    shippingamount DOUBLE NULL) SALT_BUCKETS=4;
```

## <a name="enable-and-tune-phoenix-with-apache-ambari"></a>Phoenix met Apache Ambari inschakelen en afstemmen

An HDInsight HBase-cluster bevat de [Ambari-gebruikers interface](hdinsight-hadoop-manage-ambari.md) voor het maken van configuratie wijzigingen.

1. Als u Phoenix wilt in-of uitschakelen en de time-outinstellingen voor de service van Phoenix wilt beheren, meldt u zich aan bij de Ambari-webgebruikersinterface ( `https://YOUR_CLUSTER_NAME.azurehdinsight.net` ) met behulp van uw Hadoop-gebruikers referenties.

2. Selecteer **HBase** in de lijst met Services in het menu aan de linkerkant en selecteer vervolgens het tabblad **configuratie** .

    ![Apache Ambari HBase-configuraties](./media/hdinsight-phoenix-in-hdinsight/ambari-hbase-config1.png)

3. Zoek de sectie SQL-configuratie in **Breda** om Phoenix in of uit te scha kelen en de querytime-out in te stellen.

    ![Ambari Phoenix SQL-configuratie sectie](./media/hdinsight-phoenix-in-hdinsight/apache-ambari-phoenix.png)

## <a name="see-also"></a>Zie ook

* [Apache Phoenix gebruiken met HBase-clusters op basis van Linux in HDInsight](hbase/apache-hbase-query-with-phoenix.md)

* [Apache Zeppelin gebruiken om Apache Phoenix query's uit te voeren op Apache HBase in azure HDInsight](./hbase/apache-hbase-phoenix-zeppelin.md)

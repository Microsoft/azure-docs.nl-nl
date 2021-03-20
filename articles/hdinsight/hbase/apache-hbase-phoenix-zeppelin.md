---
title: Apache-basis query's uitvoeren in azure HDInsight met Apache Phoenix
description: Meer informatie over het gebruik van Apache Zeppelin om Apache base-query's uit te voeren met Phoenix.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 10/14/2019
ms.openlocfilehash: 50a72d0400b23162e05b17b37bdad48783261072
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944762"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>Apache Zeppelin gebruiken om Apache Phoenix query's uit te voeren op Apache HBase in azure HDInsight

Apache Phoenix is een open-source, enorm parallelle relationele database laag die is gebouwd op HBase. Met Phoenix kunt u SQL like-query's gebruiken via HBase. Phoenix maakt gebruik van JDBC-Stuur Programma's om u in staat te stellen om SQL-tabellen, indexen, weer gaven en reeksen te maken, te verwijderen en te wijzigen.  U kunt ook Phoenix gebruiken om rijen afzonderlijk en in bulk bij te werken. Phoenix maakt gebruik van een systeem eigen compilatie van NOSQL in plaats van MapReduce te gebruiken om query's te compileren, waardoor toepassingen met lage latentie op HBase kunnen worden gemaakt.

Apache Zeppelin is een open-source webgebaseerd notitie blok waarmee u gegevensgestuurde documenten kunt maken voor samen werking met interactieve gegevens analyse en talen zoals SQL en scala. Het helpt gegevens ontwikkelaars & gegevens wetenschappers te ontwikkelen, te organiseren, uit te voeren en te delen code voor het bewerken van gegevens. U kunt hiermee de resultaten visualiseren zonder naar de opdracht regel te verwijzen of de cluster details te hoeven niet.

HDInsight-gebruikers kunnen Apache Zeppelin gebruiken om een query uit te zoeken naar Breda-tabellen. Apache Zeppelin is geïntegreerd met het HDInsight-cluster en er zijn geen extra stappen om het te gebruiken. Maak een Zeppelin-notebook met JDBC interpreter en begin met het schrijven van uw Phoenix SQL-query's

## <a name="prerequisites"></a>Vereisten

Een Apache HBase-cluster in HDInsight. Zie [aan de slag met Apache HBase](./apache-hbase-tutorial-get-started-linux.md).

## <a name="create-an-apache-zeppelin-note"></a>Een Apache Zeppelin-notitie maken

1. Vervang `CLUSTERNAME` door de naam van uw cluster in de volgende URL: `https://CLUSTERNAME.azurehdinsight.net/zeppelin`. Voer vervolgens de URL in een webbrowser in. Voer uw gebruikersnaam en wachtwoord in om u aan te melden bij het cluster.

1. Selecteer op de pagina Zeppelin de optie **nieuwe notitie maken**.

    ![Zeppelin in Interactive Query in HDInsight](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png)

1. Typ of selecteer in het dialoogvenster **Create new note** de volgende waarden:

    - Note Name (Naam van notitie): Voer een naam voor de notitie in.
    - Default interpreter (Standaard-interpreter): Selecteer **jdbc** in de vervolgkeuzelijst.

    Selecteer vervolgens **notitie maken**.

1. Zorg ervoor dat de kop van het notitie blok een verbonden status bevat. Deze wordt aangeduid met een groene stip in de rechter bovenhoek.

    ![Status van Zeppelin-notebook](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png "Status van Zeppelin-notebook")

1. Maak een HBase-tabel. Voer de volgende opdracht in en druk op **SHIFT + ENTER**:

    ```sql
    %jdbc(phoenix)
    CREATE TABLE Company (
        company_id INTEGER PRIMARY KEY,
        name VARCHAR(225)
    );
    ```

    De instructie **% JDBC (Phoenix)** in de eerste regel geeft aan dat het notitie blok de Phoenix JDBC-interpreter moet gebruiken.

1. Gemaakte tabellen weer geven.

    ```sql
    %jdbc(phoenix)
    SELECT DISTINCT table_name
    FROM SYSTEM.CATALOG
    WHERE table_schem is null or table_schem <> 'SYSTEM';
    ```

1. Voeg waarden in de tabel in.

    ```sql
    %jdbc(phoenix)
    UPSERT INTO dbo.Company VALUES(1, 'Microsoft');
    UPSERT INTO dbo.Company (name, company_id) VALUES('Apache', 2);
    ```

1. Voer een query op de tabel uit.

    ```sql
    %jdbc(phoenix)
    SELECT * FROM dbo.Company;
    ```

1. Verwijder een record.

    ```sql
    %jdbc(phoenix)
    DELETE FROM dbo.Company WHERE COMPANY_ID=1;
    ```

1. Verwijder de tabel.

    ```sql
    %jdbc(phoenix)
    DROP TABLE dbo.Company;
    ```

## <a name="next-steps"></a>Volgende stappen

- [Apache Phoenix ondersteunt nu Zeppelin in azure HDInsight](/archive/blogs/ashish/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight)
- [Apache Phoenix grammatica](https://phoenix.apache.org/language/index.html)
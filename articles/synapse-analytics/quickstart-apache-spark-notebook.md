---
title: "Quickstart: Een serverloze Apache Spark-pool maken met behulp van webhulpprogramma's"
description: In deze quickstart leert u hoe u met de webhulpprogramma's een serverloze Apache Spark-pool maakt in Azure Synapse Analytics, en hoe u een Spark SQL-query uitvoert.
services: synapse-analytics
author: euangMS
ms.author: euang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: quickstart
ms.date: 10/16/2020
ms.openlocfilehash: 38b0f23a44a16125726e7810b2045d244a2e6b06
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96454657"
---
# <a name="quickstart-create-a-serverless-apache-spark-pool-in-azure-synapse-analytics-using-web-tools"></a>Quickstart: Een serverloze Apache Spark-pool maken in Azure Synapse Analytics met behulp van webhulpprogramma's

In deze quickstart leert u hoe u een serverloze Apache Spark-pool maakt in Azure Synapse met behulp van webhulpprogramma's. Vervolgens leert u hoe u verbinding maakt met de Apache Spark-pool en Spark SQL-query's uitvoert op bestanden en tabellen. Apache Spark maakt het mogelijk om snelle gegevensanalyses en clusterberekeningen uit te voeren met behulp van verwerking in het geheugen. Informatie over Spark in Azure Synapse kunt u lezen in [Wat is Apache Spark in Azure Synapse Azure Synapse Analytics](./spark/apache-spark-overview.md).

> [!IMPORTANT]
> Spark-instanties worden pro rato per minuut gefactureerd, ongeacht of u ze wel of niet gebruikt. Zorg er daarom voor dat u de Spark-instantie afsluit wanneer u deze niet meer nodig hebt of stel een korte time-out in. Zie voor meer informatie de sectie **Resources opschonen** van dit artikel.

Als u geen Azure-abonnement hebt, [maakt u een gratis account voordat u begint](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/)
- [Synapse Analytics-werkruimte](quickstart-create-workspace.md)
- [Serverloze Apache Spark-pool](quickstart-create-apache-spark-pool-studio.md)

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-a-notebook"></a>Een notebook maken

Een notebook is een interactieve omgeving die verschillende programmeertalen ondersteunt. Via de notebook kunt u interactie hebben met uw gegevens, code combineren met markdown en tekst, en eenvoudige visualisaties uitvoeren.

1. Ga naar de weergave in de Azure-portal van de Azure Synapse-werkruimte die u wilt gebruiken en selecteer **Synapse Studio starten**.
2. Zodra Synapse Studio is gestart, selecteert u **Ontwikkelen**. Selecteer vervolgens het pictogram **+** om een nieuwe resource toe te voegen.
3. Selecteer van daaruit **Notebook**. Er wordt een nieuwe notebook gemaakt en deze wordt geopend onder een automatisch gegenereerde naam.
 
     ![Nieuwe notebook](./media/quickstart-apache-spark-notebook/spark-get-started-new-notebook.png "Nieuwe notebook")

4. Geef in het venster **Eigenschappen** een naam op voor de notebook.
5. Klik op de werkbalk op **Publiceren**.
6. Als uw werkruimte slechts één Apache Spark-pool bevat, wordt deze standaard geselecteerd. Gebruik de vervolgkeuzelijst om de juiste Apache Spark-pool te selecteren als er meerdere pools beschikbaar zijn.
7. Klik op **Code toevoegen**. De standaardtaal is `Pyspark`. U gaat een mix van Pyspark en Spark SQL gebruiken, dus u kunt de standaardkeuze gewoon accepteren. Andere ondersteunde talen zijn Scala en .NET voor Spark.
8. Vervolgens maakt u een eenvoudig Spark DataFrame-object om te manipuleren. In dit geval maakt u het object met behulp van code. Hiervoor zijn drie rijen en drie kolommen nodig:

   ```python
   new_rows = [('CA',22, 45000),("WA",35,65000) ,("WA",50,85000)]
   demo_df = spark.createDataFrame(new_rows, ['state', 'age', 'salary'])
   demo_df.show()
   ```

9. Voer de cel nu uit met behulp van een van de volgende methoden:

   - Druk op **Shift+Enter**.
   - Selecteer het blauwe afspeelpictogram links van de cel.
   - Selecteer de knop **Alles uitvoeren** op de werkbalk.

       ![DataFrame-object maken](./media/quickstart-apache-spark-notebook/spark-get-started-create-data-frame-object.png)

10. Als de instantie van de Apache Spark-pool nog niet wordt uitgevoerd, wordt deze automatisch gestart. U ziet de status van de instantie van de Apache Spark-pool onder de cel die u uitvoert, evenals in het statuspaneel onderaan de notebook. Afhankelijk van de grootte van de pool, duurt het starten 2-5 minuten. Als de uitvoering van de code is voltooid, ziet u onder de cel hoe lang het uitvoeren van de code heeft geduurd. In de uitvoercel ziet u de uitvoer.

    ![Uitvoer van het uitvoeren van een cel](./media/quickstart-apache-spark-notebook/run-cell-with-output.png)

11. De gegevens bevinden zich nu in een DataFrame, van waaruit u de gegevens op allerlei manieren kunt gebruiken. U hebt de gegevens in verschillende indelingen nodig voor de rest van deze quickstart.
12. Voer de onderstaande code in een andere cel in en voer deze uit om een Spark-tabel, een CSV-bestand en een Parquet-bestand te maken met kopieën van de gegevens:

    ```python
     demo_df.createOrReplaceTempView('demo_df')
     demo_df.write.csv('demo_df', mode='overwrite')
     demo_df.write.parquet('abfss://<<TheNameOfAStorageAccountFileSystem>>@<<TheNameOfAStorageAccount>>.dfs.core.windows.net/demodata/demo_df', mode='overwrite')
    ```

    Als u de opslagverkenner gebruikt, kunt u zien wat het effect is van de twee verschillende manieren die hierboven worden gebruikt om een bestand te schrijven. Als er geen bestandssysteem is opgegeven, wordt de standaardwaarde gebruikt, wat in dit geval `default>user>trusted-service-user>demo_df` is. De gegevens worden opgeslagen op de locatie van het opgegeven bestandssysteem.

    Bij de indelingen 'csv' en 'parquet' wordt bij schrijfbewerkingen een map gemaakt met verschillende gepartitioneerde bestanden.

    ![Weergave van uitvoer in opslagverkenner](./media/quickstart-apache-spark-notebook/spark-get-started-default-storage.png "Weergave van uitvoer in opslagverkenner")

    ![Schermopname waarin het pad default > demodata > demo_df is gemarkeerd.](./media/quickstart-apache-spark-notebook/spark-get-started-default-storage2.png "Weergave van uitvoer in opslagverkenner")

## <a name="run-spark-sql-statements"></a>Spark SQL-instructies uitvoeren

SQL (Structured Query Language) is de meest voorkomende en gebruikte taal voor het uitvoeren van query's en het definiëren van gegevens. Spark SQL fungeert als een uitbreiding van Apache Spark voor het verwerken van gestructureerde gegevens, met behulp van de bekende SQL-syntaxis.

1. Plak de volgende code in een lege cel en voer de code uit. Met de opdracht worden de tabellen in de pool weergegeven.

   ```sql
   %%sql
   SHOW TABLES
   ```

   Als u een notebook gebruikt met uw Apache Spark-pool van Azure Synapse, krijgt u een voorinstelling `sqlContext` die u kunt gebruiken om query's uit te voeren met behulp van Spark SQL. Met `%%sql` krijgt de notebook opdracht om de voorinstelling `sqlContext` te gebruiken voor het uitvoeren van de query. De query haalt de eerste tien rijen op uit een systeemtabel die standaard Apache Spark-pools van Azure Synapse bevat.

2. Voer een andere query uit om de gegevens in `demo_df` te zien.

    ```sql
    %%sql
    SELECT * FROM demo_df
    ```

    De code produceert twee uitvoercellen: een met de resulterende gegevens en de andere met de taakweergave.

    De resultatenweergave bestaat standaard uit een raster. Onder het raster kan echter via een schakeloptie worden gewisseld tussen een raster- en grafiekweergave.

    ![Query-uitvoer in Azure Synapse Spark](./media/quickstart-apache-spark-notebook/spark-get-started-query.png "Query-uitvoer in Azure Synapse Spark")

3. Selecteer **Grafiek** in de schakeloptie **Weergave**.
4. Selecteer helemaal rechts het pictogram **Weergaveopties**.
5. Selecteer 'staafdiagram' in het veld **Grafiektype**.
6. Selecteer 'state' in het veld Kolom X-as.
7. Selecteer 'salary' in het veld Kolom Y-as.
8. Selecteer AVG in het veld **Aggregatie**.
9. Selecteer **Toepassen**.

   ![Grafiekuitvoer in Azure Synapse Spark](./media/quickstart-apache-spark-notebook/spark-get-started-query-chart-output.png "Grafiekuitvoer in Azure Synapse Spark")

10. U kunt dezelfde ervaring krijgen door het uitvoeren van SQL, maar zonder dat u van taal hoeft te veranderen. U kunt dit doen door de SQL-cel hierboven te vervangen door deze PySpark-cel; de uitvoerervaring is hetzelfde omdat de opdracht **weer display** wordt gebruikt:

    ```python
    display(spark.sql('SELECT * FROM demo_df'))
    ```

11. Elk van de cellen die eerder is uitgevoerd had de mogelijkheid om naar **History Server** en **Monitoring** te gaan. Als u op de koppelingen klikt, gaat u naar verschillende onderdelen van de gebruikerservaring.

> [!NOTE]
> Sommige gedeelten van de [officiële Apache Spark-documentatie](https://spark.apache.org/docs/latest/) zijn gebaseerd op het gebruik van de Spark-console. Deze is niet beschikbaar in Synapse Spark. Gebruik in plaats daarvan de ervaring met [notebooks](quickstart-apache-spark-notebook.md) of [IntelliJ](./spark/intellij-tool-synapse.md).

## <a name="clean-up-resources"></a>Resources opschonen

Uw gegevens worden door Azure Synapse opgeslagen in Azure Data Lake Storage. U kunt een Spark-instantie veilig laten uitschakelen wanneer deze niet in gebruik is. Er worden kosten in rekening gebracht voor een serverloze Apache Spark-pool zolang deze wordt uitgevoerd, zelfs als de pool niet in gebruik is. 

Aangezien de kosten voor de pool vaak veel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer om Spark-instanties te laten uitschakelen wanneer ze niet worden gebruikt.

Om ervoor te zorgen dat de Spark-instantie wordt afgesloten, beëindigt u alle verbonden sessies (notebooks). De pool wordt afgesloten wanneer de **niet-actieve tijd** is bereikt die is opgegeven in de Apache Spark-pool. U kunt ook **sessie beëindigen** selecteren op de statusbalk onder aan de notebook.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een serverloze Apache Spark-pool maakt, en een eenvoudige Spark SQL-query uitvoert.

- [Azure Synapse Analytics](overview-what-is.md)
- [Documentatie voor .NET voor Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)




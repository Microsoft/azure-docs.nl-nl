---
title: 'Zelfstudie: Azure HDInsight Apache Spark-gegevens analyseren met Power BI'
description: Zelfstudie - Gebruik Microsoft Power BI om Apache Spark-gegevens te visualiseren die zijn opgeslagen in HDInsight-clusters
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc,seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 3e0632b2ad1ac237d8899e9d3bdc7f1d3350fa76
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057929"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Zelfstudie: Gegevens van Apache Spark analyseren met Power BI in HDInsight

In deze zelfstudie leert u hoe u Microsoft Power BI kunt gebruiken om gegevens te visualiseren in een Apache Spark-cluster in Azure HDInsight.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Spark-gegevens visualiseren met behulp van Power BI

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Deze zelfstudie uitvoeren[: Gegevens laden en query's uitvoeren in een Apache Spark-cluster in Azure HDInsight](./apache-spark-load-data-run-query.md).

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Optioneel: [Power BI-proefabonnement](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>De gegevens controleren

Het [Jupyter Notebook](https://jupyter.org/) dat u hebt gemaakt in de [vorige zelfstudie](apache-spark-load-data-run-query.md) bevat code voor het maken van een `hvac`-tabel. Deze tabel is gebaseerd op het CSV-bestand dat voor alle HDInsight Spark-clusters beschikbaar is op `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv`. Gebruik de volgende procedure om de gegevens te controleren.

1. Plak de volgende code uit het Jupyter-notebook en druk vervolgens op **Shift + Enter**. Deze code controleert of de tabellen bestaan.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    De uitvoer ziet er als volgt uit:

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-show-tables.png" alt-text="Tabellen weergeven in Spark" border="true":::

    Als u het notebook voorafgaand aan deze zelfstudie hebt gesloten, is `hvactemptable` opgeschoond en wordt deze niet opgenomen in de uitvoer.  Alleen Hive-tabellen die zijn opgeslagen in de metastore (aangegeven met **False** in de kolom **isTemporary**) zijn toegankelijk vanuit de BI-hulpprogramma's. In deze zelfstudie maakt u verbinding met de **hvac**-tabel die u hebt gemaakt.

2. Plak de volgende code in een lege cel en druk op **Shift+Enter**. De code controleert de gegevens in de tabel.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    De uitvoer ziet er als volgt uit:

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-select-limit.png" alt-text="Rijen uit de hvac-tabel in Spark weergeven" border="true":::

3. Klik in het menu **File** van het notebook op **Close and Halt**. Sluit het notebook om de resources vrij te geven.

## <a name="visualize-the-data"></a>De gegevens visualiseren

In dit gedeelte gebruikt u Power BI om visualisaties, rapporten en dashboards te maken van de gegevens in het Spark-cluster.

### <a name="create-a-report-in-power-bi-desktop"></a>Een rapport maken in Power BI Desktop

De eerste stappen om te werken met Spark zijn verbinding maken met het cluster in Power BI Desktop, gegevens uit het cluster laden en eenvoudige visualisatie maken op basis van die gegevens.

1. Open Power BI Desktop. Sluit het welkomstscherm als dit wordt geopend.

2. Ga op het tabblad **Start** naar **Gegevens ophalen** > **Meer..** .

    :::image type="content" source="./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png " alt-text="Gegevens ophalen in Power bi Desktop van HDInsight Apache Spark" border="true":::er is een ' True ':::

3. Typ `Spark` in het zoekvak, selecteer **Azure HDInsight Spark** en selecteer vervolgens **Verbinding maken**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png " alt-text="Gegevens ophalen uit Power bi van Apache Spark bi" border="true":::er = "True":::

4. Voer de cluster-URL (in de vorm `mysparkcluster.azurehdinsight.net`) in het tekstvak **Server** in.

5. Onder **Gegevensverbindingsmodus** selecteert u **DirectQuery**. Selecteer vervolgens **OK**.

    U kunt beide gegevensverbindingsmodi gebruiken met Spark. Als u DirectQuery gebruikt, worden wijzigingen doorgevoerd in rapporten zonder dat de hele gegevensset wordt vernieuwd. Als u gegevens importeert, moet u de gegevensset vernieuwen om de wijzigingen te zien. Zie [DirectQuery gebruiken in Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/) voor meer informatie over hoe en wanneer u DirectQuery kunt gebruiken.

6. Voer de gegevens voor het aanmeldingsaccount van HDInsight in en selecteer vervolgens **Verbinding maken**. De standaardaccountnaam is *admin*.

7. Selecteer de tabel `hvac`, wacht tot u een voorbeeld van de gegevens ziet en selecteer dan **Laden**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png " alt-text="Gebruikers naam en wacht woord voor Spark-cluster" border="true":::d "Border =" True ":::

    Power BI Desktop beschikt over de gegevens die nodig zijn om verbinding te maken met het Spark-cluster en om gegevens te laden uit de tabel `hvac`. De tabel en de kolommen worden weergegeven in het deelvenster **Velden**.

8. Visualiseer het verschil tussen de gewenste temperatuur en de werkelijke temperatuur voor elk gebouw:

    1. Selecteer **Vlakdiagram** in het deelvenster **Visualisaties**.

    2. Sleep het veld **BuildingID** naar **As**, en sleep de velden **ActualTemp** en **TargetTemp** naar **Waarde**.

        :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png " alt-text="kolom met waarden toevoegen" border="true":::t = "waarde kolommen toevoegen" Border = "True":::

        Het diagram ziet er zo uit:

        :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png " alt-text="gebieds grafiek Sum" border="true":::lt-text = "gebieds grafiek Sum" Border = "True":::

        De visualisatie bevat standaard de som van **ActualTemp** en **TargetTemp**. Selecteer de pijl-omlaag naast **ActualTemp** en **TragetTemp** in het deelvenster Visualisaties. U ziet dat **Som** is geselecteerd.

    3. Selecteer de pijl omlaag naast **ActualTemp** en **TragetTemp** in het deelvenster Visualisaties, selecteer **Gemiddelde** om voor elk gebouw het gemiddelde weer te geven van de werkelijke temperatuur en de beoogde temperatuur.

        :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png " alt-text="gemiddelde van waarden" border="true":::t = "gemiddelde van waarden" Border = "True":::

        De gegevensvisualisatie moet er ongeveer uitzien zoals in de schermafbeelding. Beweeg de cursor over de visualisatie om knopinfo met relevante gegevens weer te geven.

        :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png " alt-text="vlak Graph" border="true":::. png "Alt-Text =" gebieds grafiek "Border =" True ":::

9. Ga naar **Bestand** > **Opslaan**, voer de naam `BuildingTemperature` voor het bestand in en selecteer **Opslaan**.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Het rapport publiceren naar de Power BI-service (optioneel)

Met behulp van de Power BI-service kunt u rapporten en dashboards delen binnen uw organisatie. In dit gedeelte gaat u eerst de gegevensset en het rapport publiceren. Vervolgens maakt u het rapport vast aan een dashboard. Dashboards worden voornamelijk gebruikt om te focussen op een subset gegevens in een rapport. U hebt slechts één visualisatie in het rapport, maar het is wel handig om de stappen door te lopen.

1. Open Power BI Desktop.

1. Selecteer op het tabblad **Start** de optie **Publiceren**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png " alt-text="Publiceren vanaf Power bi Desktop" border="true"::: Desktop "Border =" True ":::

1. Selecteer de werkruimte waarnaar u de gegevensset wilt publiceren en rapporteren, en selecteer vervolgens **Selecteren**. In de volgende afbeelding is de standaardwerkruimte **Mijn werkruimte** geselecteerd.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png " alt-text="Werk ruimte selecteren voor het publiceren van gegevensset en rapport naar" border="true":::UE "::

1. Nadat het publiceren is voltooid, selecteert u **'BuildingTemperature.pbix' openen in Power BI**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png " alt-text="Publiceer succes, klik om referenties in te voeren" border="true":::= "True":::

1. Selecteer in de Power BI-service **Referenties invoeren**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png " alt-text="Voer de referenties in Power bi-service" border="true":::"Border =" True ":::

1. Selecteer **Referenties bewerken**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png " alt-text="Referenties bewerken in Power bi-service" border="true":::e "Border =" True ":::

1. Voer de gegevens voor het aanmeldingsaccount van HDInsight in en selecteer vervolgens **Aanmelden**. De standaardaccountnaam is *admin*.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png " alt-text="Aanmelden bij Spark-cluster" border="true"::: Spark-cluster "Border =" True ":::

1. Ga in het linkerdeelvenster naar **Werkruimten** > **Mijn werkruimte** > **RAPPORTEN** en selecteer **BuildingTemperature**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png " alt-text="Rapport vermeld onder rapporten in het linkerdeel venster" border="true":::order = "True":::

    Ook moet **BuildingTemperature** worden vermeld **GEGEVENSSETS** in het linkerdeelvenster.

    De visualisatie die u hebt gemaakt in Power BI Desktop is nu beschikbaar in de Power BI-service.

1. Beweeg de cursor over de visualisatie en selecteer vervolgens de speld in de rechterbovenhoek.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png " alt-text="Meld u in de Power bi-service" border="true":::-service "Border =" True ":::

1. Selecteer 'Nieuw dashboard', voer de naam `Building temperature` in en selecteer vervolgens **Vastmaken**.

    Aan het nieuwe Dash :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png " alt-text="Board vastmaken" border="true"::: aan het nieuwe dash board "Border =" True ":::

1. Selecteer in het rapport **Naar dashboard**.

De visualisatie wordt vastgemaakt aan het dashboard. U kunt andere visualisaties toevoegen aan het rapport en deze aan hetzelfde dashboard vastmaken. Zie [Rapporten in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) en [Dashboards in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/) voor meer informatie over rapporten en dashboards.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de zelfstudie hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage zodat u een cluster veilig kunt verwijderen wanneer deze niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt.

Als u een cluster wilt verwijderen, raadpleegt u [HDInsight-cluster verwijderen met behulp van uw browser, PowerShell of de Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u Microsoft Power BI kunt gebruiken om gegevens te visualiseren in een Apache Spark-cluster in Azure HDInsight. Ga naar het volgende artikel om te zien dat u een machine learning-toepassing kunt maken.

> [!div class="nextstepaction"]
> [Een machine learning-toepassing maken](./apache-spark-ipython-notebook-machine-learning.md)
---
title: 'Zelfstudie: Azure HDInsight Apache Spark-gegevens analyseren met Power BI'
description: Zelfstudie - Gebruik Microsoft Power BI om Apache Spark-gegevens te visualiseren die zijn opgeslagen in HDInsight-clusters
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc,seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 285c6c05a1a216303ee9d8019093c963cad60aa0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946476"
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

    ![Tabellen weergeven in Spark](./media/apache-spark-use-bi-tools/apache-spark-show-tables.png)

    Als u het notebook voorafgaand aan deze zelfstudie hebt gesloten, is `hvactemptable` opgeschoond en wordt deze niet opgenomen in de uitvoer.  Alleen Hive-tabellen die zijn opgeslagen in de metastore (aangegeven met **False** in de kolom **isTemporary**) zijn toegankelijk vanuit de BI-hulpprogramma's. In deze zelfstudie maakt u verbinding met de **hvac**-tabel die u hebt gemaakt.

2. Plak de volgende code in een lege cel en druk op **Shift+Enter**. De code controleert de gegevens in de tabel.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    De uitvoer ziet er als volgt uit:

    ![Rijen uit de hvac-tabel in Spark weergeven](./media/apache-spark-use-bi-tools/apache-spark-select-limit.png)

3. Klik in het menu **File** van het notebook op **Close and Halt**. Sluit het notebook om de resources vrij te geven.

## <a name="visualize-the-data"></a>De gegevens visualiseren

In dit gedeelte gebruikt u Power BI om visualisaties, rapporten en dashboards te maken van de gegevens in het Spark-cluster.

### <a name="create-a-report-in-power-bi-desktop"></a>Een rapport maken in Power BI Desktop

De eerste stappen om te werken met Spark zijn verbinding maken met het cluster in Power BI Desktop, gegevens uit het cluster laden en eenvoudige visualisatie maken op basis van die gegevens.

1. Open Power BI Desktop. Sluit het welkomstscherm als dit wordt geopend.

2. Ga op het tabblad **Start** naar **Gegevens ophalen** > **Meer..** .

    ![Gegevens ophalen in Power BI Desktop van HDInsight Apache Spark](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Gegevens in Power BI laden vanuit Apache Spark BI")

3. Typ `Spark` in het zoekvak, selecteer **Azure HDInsight Spark** en selecteer vervolgens **Verbinding maken**.

    ![Gegevens in Power BI laden vanuit Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Gegevens in Power BI laden vanuit Apache Spark BI")

4. Voer de cluster-URL (in de vorm `mysparkcluster.azurehdinsight.net`) in het tekstvak **Server** in.

5. Onder **Gegevensverbindingsmodus** selecteert u **DirectQuery**. Selecteer vervolgens **OK**.

    U kunt beide gegevensverbindingsmodi gebruiken met Spark. Als u DirectQuery gebruikt, worden wijzigingen doorgevoerd in rapporten zonder dat de hele gegevensset wordt vernieuwd. Als u gegevens importeert, moet u de gegevensset vernieuwen om de wijzigingen te zien. Zie [DirectQuery gebruiken in Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/) voor meer informatie over hoe en wanneer u DirectQuery kunt gebruiken.

6. Voer de gegevens voor het aanmeldingsaccount van HDInsight in en selecteer vervolgens **Verbinding maken**. De standaardaccountnaam is *admin*.

7. Selecteer de tabel `hvac`, wacht tot u een voorbeeld van de gegevens ziet en selecteer dan **Laden**.

    ![Gebruikersnaam en wachtwoord van Spark-cluster](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Gebruikersnaam en wachtwoord van Spark-cluster")

    Power BI Desktop beschikt over de gegevens die nodig zijn om verbinding te maken met het Spark-cluster en om gegevens te laden uit de tabel `hvac`. De tabel en de kolommen worden weergegeven in het deelvenster **Velden**.

8. Visualiseer het verschil tussen de gewenste temperatuur en de werkelijke temperatuur voor elk gebouw:

    1. Selecteer **Vlakdiagram** in het deelvenster **Visualisaties**.

    2. Sleep het veld **BuildingID** naar **As**, en sleep de velden **ActualTemp** en **TargetTemp** naar **Waarde**.

        ![waardekolommen toevoegen](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "waardekolommen toevoegen")

        Het diagram ziet er zo uit:

        ![som van gebiedsgrafiek](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "som van gebiedsgrafiek")

        De visualisatie bevat standaard de som van **ActualTemp** en **TargetTemp**. Selecteer de pijl-omlaag naast **ActualTemp** en **TragetTemp** in het deelvenster Visualisaties. U ziet dat **Som** is geselecteerd.

    3. Selecteer de pijl omlaag naast **ActualTemp** en **TragetTemp** in het deelvenster Visualisaties, selecteer **Gemiddelde** om voor elk gebouw het gemiddelde weer te geven van de werkelijke temperatuur en de beoogde temperatuur.

        ![gemiddelde van waarden](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "gemiddelde van waarden")

        De gegevensvisualisatie moet er ongeveer uitzien zoals in de schermafbeelding. Beweeg de cursor over de visualisatie om knopinfo met relevante gegevens weer te geven.

        ![gebiedsgrafiek](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "gebiedsgrafiek")

9. Ga naar **Bestand** > **Opslaan**, voer de naam `BuildingTemperature` voor het bestand in en selecteer **Opslaan**.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Het rapport publiceren naar de Power BI-service (optioneel)

Met behulp van de Power BI-service kunt u rapporten en dashboards delen binnen uw organisatie. In dit gedeelte gaat u eerst de gegevensset en het rapport publiceren. Vervolgens maakt u het rapport vast aan een dashboard. Dashboards worden voornamelijk gebruikt om te focussen op een subset gegevens in een rapport. U hebt slechts één visualisatie in het rapport, maar het is wel handig om de stappen door te lopen.

1. Open Power BI Desktop.

1. Selecteer op het tabblad **Start** de optie **Publiceren**.

    ![Publiceren vanuit Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Publiceren vanuit Power BI Desktop")

1. Selecteer de werkruimte waarnaar u de gegevensset wilt publiceren en rapporteren, en selecteer vervolgens **Selecteren**. In de volgende afbeelding is de standaardwerkruimte **Mijn werkruimte** geselecteerd.

    ![Werkruimte selecteren waarnaar u de gegevensset en het rapport wilt publiceren](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Werkruimte selecteren waarnaar u de gegevensset en het rapport wilt publiceren")

1. Nadat het publiceren is voltooid, selecteert u **'BuildingTemperature.pbix' openen in Power BI**.

    ![Publiceren is voltooid, klik om referenties in te voeren](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Publiceren is voltooid, klik om referenties in te voeren")

1. Selecteer in de Power BI-service **Referenties invoeren**.

    ![Referenties invoeren in Power BI-service](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Referenties invoeren in Power BI-service")

1. Selecteer **Referenties bewerken**.

    ![Referenties bewerken in Power BI-service](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Referenties bewerken in Power BI-service")

1. Voer de gegevens voor het aanmeldingsaccount van HDInsight in en selecteer vervolgens **Aanmelden**. De standaardaccountnaam is *admin*.

    ![Aanmelden bij Spark-cluster](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Aanmelden bij Spark-cluster")

1. Ga in het linkerdeelvenster naar **Werkruimten** > **Mijn werkruimte** > **RAPPORTEN** en selecteer **BuildingTemperature**.

    ![Rapport vermeld onder rapporten in het linkerdeelvenster](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Rapport vermeld onder rapporten in het linkerdeelvenster")

    Ook moet **BuildingTemperature** worden vermeld **GEGEVENSSETS** in het linkerdeelvenster.

    De visualisatie die u hebt gemaakt in Power BI Desktop is nu beschikbaar in de Power BI-service.

1. Beweeg de cursor over de visualisatie en selecteer vervolgens de speld in de rechterbovenhoek.

    ![Rapport in de Power BI-service](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Rapport in de Power BI-service")

1. Selecteer 'Nieuw dashboard', voer de naam `Building temperature` in en selecteer vervolgens **Vastmaken**.

    ![Vastmaken aan nieuw dashboard](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Vastmaken aan nieuw dashboard")

1. Selecteer in het rapport **Naar dashboard**.

De visualisatie wordt vastgemaakt aan het dashboard. U kunt andere visualisaties toevoegen aan het rapport en deze aan hetzelfde dashboard vastmaken. Zie [Rapporten in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) en [Dashboards in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/) voor meer informatie over rapporten en dashboards.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de zelfstudie hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage zodat u een cluster veilig kunt verwijderen wanneer deze niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt.

Als u een cluster wilt verwijderen, raadpleegt u [HDInsight-cluster verwijderen met behulp van uw browser, PowerShell of de Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u Microsoft Power BI kunt gebruiken om gegevens te visualiseren in een Apache Spark-cluster in Azure HDInsight. Ga naar het volgende artikel om te zien dat u een machine learning-toepassing kunt maken.

> [!div class="nextstepaction"]
> [Een machine learning-toepassing maken](./apache-spark-ipython-notebook-machine-learning.md)
---
title: De uitgebreide functies van de Apache Spark geschiedenis server gebruiken om fouten op te sporen in apps-Azure HDInsight
description: Gebruik de uitgebreide functies in de Apache Spark-geschiedenis server voor het opsporen en diagnosticeren van Spark-toepassingen-Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: 1d53d9e9ee2a7fa6588ea0993b3bebdb2a287351
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98941051"
---
# <a name="use-the-extended-features-of-the-apache-spark-history-server-to-debug-and-diagnose-spark-applications"></a>De uitgebreide functies van de Apache Spark History-server gebruiken om fouten op te sporen en op te sporen in Spark-toepassingen

Dit artikel laat u zien hoe u de uitgebreide functies van de Apache Spark geschiedenis server kunt gebruiken voor het opsporen van fouten en het onderzoeken van voltooide of actieve Spark-toepassingen. De uitbrei ding bevat een tabblad **gegevens** , een **grafiek** tabblad en een tabblad **diagnose** . Op het tabblad **gegevens** kunt u de invoer-en uitvoer gegevens van de Spark-taak controleren. Op het tabblad **grafiek** kunt u de gegevens stroom controleren en de taak grafiek opnieuw afspelen. Op het tabblad **diagnose** kunt u de functies **gegevens scheefheid**, **tijds scheefheid** en **gebruiks analyse gebruiken** .

## <a name="get-access-to-the-spark-history-server"></a>Toegang krijgen tot de Spark-geschiedenis server

De Spark-geschiedenis server is de Web-UI voor voltooide en actieve Spark-toepassingen. U kunt deze openen vanuit de Azure Portal of van een URL.

### <a name="open-the-spark-history-server-web-ui-from-the-azure-portal"></a>Open de Web-UI van de Spark-geschiedenis server vanuit de Azure Portal

1. Open in het [Azure Portal](https://portal.azure.com/)het Spark-cluster. Zie voor meer informatie het [overzicht en weer geven van clusters](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Selecteer in **cluster dashboards** de optie  **Spark-geschiedenis server**. Wanneer u hierom wordt gevraagd, voert u de beheerders referenties in voor het Spark-cluster.

    ![Start de Spark-geschiedenis server vanuit het Azure Portal.](./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png "Spark-geschiedenis server")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>De Web-UI van de Spark-geschiedenis server op URL openen

Open de Spark-geschiedenis server door te bladeren naar `https://CLUSTERNAME.azurehdinsight.net/sparkhistory` , waarbij **clustername** de naam is van uw Spark-cluster.

De Web-UI van de Spark-geschiedenis server kan er ongeveer als volgt uitzien:

![De Spark-geschiedenis Server pagina.](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="use-the-data-tab-in-the-spark-history-server"></a>Het tabblad gegevens in de Spark-geschiedenis server gebruiken

Selecteer de taak-ID en selecteer vervolgens **gegevens** in het menu van het hulp programma om de gegevens weergave te bekijken.

+ Controleer **invoer**, **uitvoer** en **tabel bewerkingen** door de afzonderlijke tabbladen te selecteren.

    ![Gegevens tabbladen op de pagina gegevens voor Spark-toepassing.](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ Kopieer alle rijen door de knop **kopiëren** te selecteren.

    ![Gegevens kopiëren op de pagina van de Spark-toepassing.](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ Sla alle gegevens op als een. CSV-bestand door de knop **CSV** te selecteren.

    ![Gegevens opslaan als een. CSV-bestand van de pagina gegevens voor Spark-toepassing.](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ Zoek de gegevens door tref woorden in het **Zoek** veld in te voeren. De zoek resultaten worden onmiddellijk weer gegeven.

    ![Zoek gegevens op de pagina gegevens voor Spark-toepassing.](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ Selecteer de kolomkop om de tabel te sorteren. Selecteer het plus teken om een rij uit te breiden om meer details weer te geven. Selecteer het minteken om een rij samen te vouwen.

    ![De gegevens tabel op de pagina gegevens voor Spark-toepassing.](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ Down load één bestand door de knop **gedeeltelijk downloaden** aan de rechter kant te selecteren. Het geselecteerde bestand wordt lokaal gedownload. Als het bestand niet meer bestaat, wordt er een nieuw tabblad geopend om de fout berichten weer te geven.

    ![De rij voor het downloaden van gegevens op de pagina gegevens voor Spark-toepassing.](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Kopieer een volledig pad of een relatief pad door de optie **volledig pad kopiëren** of **relatief pad kopiëren** te selecteren, die uit het menu downloaden moet worden uitgebreid. Voor Azure Data Lake Storage-bestanden selecteert u **openen in azure Storage Explorer** om Azure Storage Explorer te starten en gaat u naar de map na het aanmelden.

    ![Het volledige pad kopiëren en de opties voor het relatieve pad kopiëren op de pagina gegevens voor Spark-toepassing.](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Als er te veel rijen worden weer gegeven op één pagina, selecteert u de pagina nummers onder aan de tabel om te navigeren.

    ![Pagina nummers op de pagina gegevens voor Spark-toepassing.](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ Beweeg de muis aanwijzer over of selecteer het vraag teken naast **gegevens voor Spark-toepassing** om de knop Info weer te geven voor meer informatie.

    ![Meer informatie over de pagina gegevens voor Spark-toepassing.](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+  Als u feedback over problemen wilt verzenden, selecteert u **feedback geven**.

    ![Feedback geven op de pagina gegevens voor Spark-toepassing.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-graph-tab-in-the-spark-history-server"></a>Het tabblad grafiek in de Spark-geschiedenis server gebruiken

+ Selecteer de taak-ID en selecteer vervolgens **grafiek** in het menu van het hulp programma om de taak grafiek weer te geven. Standaard worden alle taken weer gegeven in de grafiek. Filter de resultaten met behulp van de vervolg keuzelijst **taak-id** .

    ![De vervolg keuzelijst taak-ID op de pagina & taak grafiek van Spark-toepassing.](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ De **voortgang** wordt standaard geselecteerd. Controleer de gegevens stroom door **lezen** of **geschreven** te selecteren in de vervolg keuzelijst **weer geven** .

    ![Controleer de gegevens stroom op de pagina & taak grafiek van Spark-toepassing.](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

+ De achtergrond kleur van elke taak komt overeen met een heatmap.

   ![De heatmap op de pagina & taak grafiek van Spark-toepassing.](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)


    |Kleur |Beschrijving |
    |---|---|
    |Green|De taak is voltooid.|
    |Oranje|De taak is mislukt, maar dit heeft geen invloed op het uiteindelijke resultaat van de taak. Deze taken bevatten dubbele of nieuwe exemplaren die later kunnen worden uitgevoerd.|
    |Blue|De taak wordt uitgevoerd.|
    |Wit|De taak wacht op uitvoering of het stadium is overgeslagen.|
    |Red|De taak is mislukt.|

     ![Er wordt een taak uitgevoerd op de pagina met de taak grafiek van Spark-toepassing &.](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

     De overgeslagen fasen worden in wit weer gegeven.
    ![Een overgeslagen taak op de pagina met & taak grafiek van Spark-toepassing.](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![Een mislukte taak op de pagina met & taak grafiek van Spark-toepassing.](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

     > [!NOTE]  
     > Afspelen is beschikbaar voor voltooide taken. Selecteer de knop **afspelen** om de taak terug te spelen. Stop de taak op elk gewenst moment door de knop stoppen te selecteren. Wanneer een taak wordt afgespeeld, wordt de status van elke taak weer gegeven op kleur. Afspelen wordt niet ondersteund voor onvolledige taken.

+ Schuif omlaag om in of uit te zoomen op de taak grafiek, of selecteer **Inzoomen passend** maken om deze op het scherm aan te passen.

    ![Selecteer in-en uitzoomen op de pagina Spark-toepassing & taak grafiek.](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Wanneer taken mislukken, houdt u de muis aanwijzer over het grafiek knooppunt om de knop Info weer te geven en selecteert u vervolgens de fase om deze te openen op een nieuwe pagina.

    ![Bekijk de knop info op de pagina & taak grafiek van Spark-toepassing.](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ Op de pagina Spark-toepassing & taak grafiek worden knop Info en kleine pictogrammen weer gegeven als de taken aan de volgende voor waarden voldoen:
  + Gegevens scheefheid: gegevens lezen grootte > gemiddelde Lees grootte van gegevens van alle taken in deze fase * 2 *en* grootte van gelezen gegevens > 10 MB.
  + Tijd scheefheid: uitvoerings tijd > gemiddelde uitvoerings tijd van alle taken in deze fase * 2 *en* uitvoerings tijd > 2 minuten.

    ![Het gescheefe taak pictogram op de pagina Spark-toepassing & taak grafiek.](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ In het knoop punt taak grafiek wordt de volgende informatie over elke fase weer gegeven:
  + Id
  + Naam of beschrijving
  + Totaal taak nummer
  + Gegevens lezen: de som van de invoer grootte en de grootte van de Lees bewerking in wille keurige volg orde
  + Gegevens schrijven: de som van de uitvoer grootte en schrijf grootte van wille keurige volg orde
  + Uitvoerings tijd: de tijd tussen de begin tijd van de eerste poging en voltooiings tijd van de laatste poging
  + Aantal rijen: de som van de invoer records, uitvoer records, in wille keurige volg orde records lezen en schrijf records in wille keurige volg orde plaatsen
  + Voortgang

    > [!NOTE]  
    > Het knoop punt taak grafiek geeft standaard informatie weer van de laatste poging van elke fase (met uitzonde ring van de uitvoerings tijd van de fase). Maar tijdens het afspelen wordt in het taak grafiek knooppunt informatie over elke poging weer gegeven.

    > [!NOTE]  
    > Voor lees-en schrijf grootten van gegevens gebruiken we 1MB = 1000 KB = 1000 * 1000 bytes.

+ Feedback over problemen verzenden door **feedback voor ons** te selecteren.

    ![De optie feedback op de pagina taak grafiek van Spark-toepassing &.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-diagnosis-tab-in-the-spark-history-server"></a>Het tabblad diagnose in de Spark-geschiedenis server gebruiken

Selecteer de taak-ID en selecteer vervolgens **diagnose** in het menu van het hulp programma om de job Diagnostics weer te geven. Het tabblad **diagnose** bevat **gegevens scheefheid**, **tijds scheefheid** en **gebruiks analyse**.

+ Bekijk de **gebruiks analyse** van **gegevens scheefheid**, **tijds scheefheid** en voer uit door de tabbladen respectievelijk te selecteren.

    ![Het tabblad gegevens scheefheid op het tabblad diagnose.](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Gegevens scheef trekken

Selecteer het tabblad **gegevens scheefheid** . De overeenkomende gescheefe taken worden weer gegeven op basis van de opgegeven para meters.

#### <a name="specify-parameters"></a>Para meters opgeven

In het gedeelte **para meters opgeven** worden de para meters weer gegeven die worden gebruikt voor het detecteren van gegevens scheefheid. De standaard regel is: de taak gegevens die worden gelezen, zijn groter dan drie keer van de gemiddelde taak gegevens worden gelezen en de gelezen taak gegevens zijn meer dan 10 MB. Als u uw eigen regel wilt definiëren voor gescheefde taken, kunt u de para meters kiezen. De secties **scheefe fase** en **schuine grafiek** worden dienovereenkomstig bijgewerkt.

#### <a name="skewed-stage"></a>Scheefe fase

In het gedeelte **Gescheefe fase** worden fasen weer gegeven die gescheefe taken hebben die voldoen aan de opgegeven criteria. Als er meer dan één scheefe taak in een fase is, wordt in de sectie **scheefe fase** alleen de meest verlaagde taak weer gegeven (dat wil zeggen, de grootste gegevens voor het scheef trekken van gegevens).

![Grotere weer gave van het tabblad gegevens scheefheid op het tabblad diagnose.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

##### <a name="skew-chart"></a>Grafiek scheef trekken

Wanneer u een rij in de tabel **scheefe fase** selecteert, worden in het **schuine diagram** meer details over taak distributie weer gegeven op basis van de lees-en uitvoerings tijd van de gegevens. De gescheefe taken worden rood gemarkeerd en de normale taken worden blauw gemarkeerd. Voor de prestaties van de grafiek worden de voorbeeld taken van Maxi maal 100 weer gegeven. De taak Details worden weer gegeven in het deel venster onder in de rechter benedenhoek.

![Het Scheefe diagram voor fase 10 in de Spark-gebruikers interface.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Tijd verschil

Op het tabblad **tijd verschil** worden scheefe taken weer gegeven op basis van de uitvoerings tijd van de taak.

#### <a name="specify-parameters"></a>Para meters opgeven

In het gedeelte **para meters opgeven** worden de para meters weer gegeven, die worden gebruikt voor het detecteren van tijd scheefheid. De standaard regel is: de uitvoerings tijd van de taak is groter dan drie keer van de gemiddelde uitvoerings tijd en de tijd voor het uitvoeren van de taak is langer dan 30 seconden. U kunt de para meters wijzigen op basis van uw behoeften. In de grafiek **scheefe fase** en **scheefheid** worden de bijbehorende fasen en taak gegevens weer gegeven, net als in het tabblad **gegevens scheefheid** .

Wanneer u **tijds scheefheid** selecteert, wordt het gefilterde resultaat weer gegeven in de sectie **scheefe fase** , op basis van de para meters die zijn ingesteld in de sectie **para meters opgeven** . Wanneer u één item in de sectie **scheefe fase** selecteert, wordt de bijbehorende grafiek geschetst in de derde sectie en worden de taak details weer gegeven in het deel venster onder in de rechter benedenhoek.

![Het tabblad tijd verschil binnen het tabblad diagnose.](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis-graphs"></a>Grafieken voor gebruiks analyse van gebruik

In de **gebruiks grafiek** van de uitvoerder worden de werkelijke uitvoerings toewijzing en uitvoerings status van de taak weer gegeven.  

Wanneer u **gebruiks analyse** uitvoeren selecteert, worden vier verschillende bochten over het gebruik van de **uitvoerder** geschetst: **toegewezen uitvoerende** machines, uitvoeringen van uitvoerende machines, **niet-actieve uitvoerende** machines en **Maxi maal** bewerkings instanties. Bij elke uitvoerder die is **toegevoegd** of de uitvoerder is **verwijderd** , worden de toegewezen uitbreiden verhoogd of verlaagd. U kunt de **gebeurtenis tijdlijn** controleren op het tabblad **taken** voor meer vergelijkingen.

![Het tabblad gebruiks analyse voor voeringen op het tabblad diagnose.](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

Selecteer het kleur pictogram om de bijbehorende inhoud in alle concepten te selecteren of de selectie ervan op te heffen.

 ![Selecteer de grafiek op het tabblad gebruiks analyse van de uitvoerder.](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>Veelgestelde vragen

### <a name="how-do-i-revert-to-the-community-version"></a>Hoe kan ik terugkeren naar de Community-versie?

Voer de volgende stappen uit om terug te keren naar de Community-versie.

1. Open het cluster in Ambari.
1. Ga naar **Spark2**-  >  **configuraties**.
1. Selecteer **aangepaste spark2-standaard waarden**.
1. **Eigenschap toevoegen selecteren...**.
1. Voeg **Spark. UI. Enhancement. Enabled = False** toe en sla het vervolgens op.
1. De eigenschap wordt nu ingesteld op **Onwaar** .
1. Selecteer **Opslaan** om de configuratie op te slaan.

    ![Schakel een functie uit in Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

1. Selecteer **Spark2** in het linkerdeel venster. Selecteer vervolgens op het tabblad **samen vatting** **Spark2 geschiedenis server**.

    ![De weer gave samen vatting in Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

1. Als u de Spark-geschiedenis server opnieuw wilt opstarten, selecteert u de knop **gestart** rechts van de **Spark2-geschiedenis server** en selecteert u **opnieuw opstarten** in de vervolg keuzelijst.

    ![Start de Spark-geschiedenis server opnieuw in Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  

1. Vernieuw de Web-UI van de Spark-geschiedenis server. Er wordt teruggekeerd naar de Community-versie.

### <a name="how-do-i-upload-a-spark-history-server-event-to-report-it-as-an-issue"></a>Hoe kan ik een Spark-geschiedenis server gebeurtenis uploaden om deze als een probleem te melden?

Als er een fout optreedt in de Spark-geschiedenis server, moet u de volgende stappen uitvoeren om de gebeurtenis te rapporteren.

1. Down load de gebeurtenis door **downloaden** te selecteren in de Web-UI van de Spark-geschiedenis server.

    ![Down load de gebeurtenis in de gebruikers interface van de Spark-geschiedenis server.](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Selecteer **feedback geven** op de pagina met de **taak grafiek van Spark-toepassing &** .

    ![Feedback geven op de pagina met & taak grafiek van Spark-toepassing](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Geef de titel en een beschrijving van de fout op. Sleep vervolgens het zip-bestand naar het veld bewerken en selecteer **nieuw probleem verzenden**.

    ![Upload een nieuw probleem en verzend het.](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="how-do-i-upgrade-a-jar-file-in-a-hotfix-scenario"></a>Hoe kan ik u een jar-bestand in een hotfix-scenario bijwerken?

Als u een upgrade wilt uitvoeren met een hotfix, gebruikt u het volgende script dat zal worden bijgewerkt `spark-enhancement.jar*` .

**upgrade_spark_enhancement. sh**:

   ```bash
    #!/usr/bin/env bash

    # Copyright (C) Microsoft Corporation. All rights reserved.

    # Arguments:
    # $1 Enhancement jar path

    if [ "$#" -ne 1 ]; then
        >&2 echo "Please provide the upgrade jar path."
        exit 1
    fi

    install_jar() {
        tmp_jar_path="/tmp/spark-enhancement-hotfix-$( date +%s )"

        if wget -O "$tmp_jar_path" "$2"; then
            for FILE in "$1"/spark-enhancement*.jar
            do
                back_up_path="$FILE.original.$( date +%s )"
                echo "Back up $FILE to $back_up_path"
                mv "$FILE" "$back_up_path"
                echo "Copy the hotfix jar file from $tmp_jar_path   to $FILE"
                cp "$tmp_jar_path" "$FILE"

                "Hotfix done."
                break
            done
        else    
            >&2 echo "Download jar file failed."
            exit 1
        fi
    }

    jars_folder="/usr/hdp/current/spark2-client/jars"
    jar_path=$1

    if ls ${jars_folder}/spark-enhancement*.jar 1>/dev/null 2>&1;   then
        install_jar "$jars_folder" "$jar_path"
    else
        >&2 echo "There is no target jar on this node. Exit with no action."
        exit 0
    fi
   ```

#### <a name="usage"></a>Gebruik

`upgrade_spark_enhancement.sh https://${jar_path}`

#### <a name="example"></a>Voorbeeld

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

#### <a name="use-the-bash-file-from-the-azure-portal"></a>Gebruik het bash-bestand van de Azure Portal

1. Start de [Azure Portal](https://ms.portal.azure.com)en selecteer vervolgens uw cluster.
2. Voer een [script actie](../hdinsight-hadoop-customize-cluster-linux.md) uit met de volgende para meters.

    |Eigenschap |Waarde |
    |---|---|
    |Scripttype|- Aangepast|
    |Naam|UpgradeJar|
    |Bash-script-URI|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |Knooppunttype(n)|Hoofd, werk nemer|
    |Parameters|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     ![Azure Portal script actie verzenden](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>Bekende problemen

+ Op dit moment werkt de Spark-geschiedenis server alleen voor Spark 2,3 en 2,4.

+ Invoer-en uitvoer gegevens die gebruikmaken van RDD, worden niet weer gegeven op het tabblad **gegevens** .

## <a name="next-steps"></a>Volgende stappen

+ [Resources voor een Apache Spark cluster in HDInsight beheren](apache-spark-resource-manager.md)
+ [Apache Spark-instellingen configureren](apache-spark-settings.md)

## <a name="suggestions"></a>Suggesties

Als u feedback hebt of problemen ondervindt bij het gebruik van dit hulp programma, stuurt u een e-mail naar ( [hdivstool@microsoft.com](mailto:hdivstool@microsoft.com) ).

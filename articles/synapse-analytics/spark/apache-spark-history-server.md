---
title: De uitgebreide Spark-geschiedenis server gebruiken om fouten op te sporen in apps
description: Gebruik de uitgebreide Spark-geschiedenis server voor het opsporen en diagnosticeren van Spark-toepassingen in azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 94c30cad1e09a01686a9042a6271a152d0f433fd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995383"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Uitgebreide Apache Spark geschiedenis server gebruiken om fouten op te sporen en op te sporen Apache Spark toepassingen

Dit artikel bevat richt lijnen voor het gebruik van de uitgebreide Apache Spark-geschiedenis server voor het opsporen en diagnosticeren van voltooide en actieve Spark-toepassingen.

De uitbrei ding bevat een tabblad gegevens, het tabblad grafiek en het tabblad diagnose. Gebruik het tabblad **gegevens** om de invoer-en uitvoer gegevens van de Spark-taak te controleren. Op het tabblad **grafiek** ziet u de gegevens stroom en het opnieuw afspelen van de taak grafiek. Het tabblad **diagnose** toont u  **gegevens scheefheid**, **tijds scheefheid** en **gebruiks analyse**.

## <a name="access-the-apache-spark-history-server"></a>De Apache Spark-geschiedenis server openen

Apache Spark geschiedenis server is de webgebruikersinterface voor voltooide en actieve Spark-toepassingen. U kunt de Apache Spark geschiedenis server-webinterface openen vanuit de Azure Synapse Analytics.

### <a name="open-the-spark-history-server-web-ui-from-apache-spark-applications-node"></a>De Web-UI van de Spark-geschiedenis server openen vanuit het knoop punt Apache Spark-toepassingen

1. [Azure Synapse Analytics](https://web.azuresynapse.net/) openen.

2. Selecteer **monitor** en selecteer vervolgens **Apache Spark toepassingen**.

    ![Selecteer monitor en selecteer vervolgens Spark-toepassing.](./media/apache-spark-history-server/click-monitor-spark-application.png)

3. Selecteer een toepassing en open vervolgens **logboek query** door deze te selecteren.

    ![Logboek query venster openen.](./media/apache-spark-history-server/open-application-window.png)

4. Selecteer **Spark-geschiedenis server**, waarna de Web-UI van de Spark-geschiedenis server wordt weer gegeven.

    ![Open Spark-geschiedenis server.](./media/apache-spark-history-server/open-spark-history-server.png)

### <a name="open-the-spark-history-server-web-ui-from-data-node"></a>De Web-UI van de Spark-geschiedenis server van het gegevens knooppunt openen

1. Selecteer vanuit uw Azure Synapse Studio-notebook **Spark-geschiedenis server** in de uitvoer-cel van de taak of in het deel venster status aan de onderkant van het notebook document. Selecteer **Sessiegegevens**.

   ![Spark-geschiedenis server 1 starten](./media/apache-spark-history-server/launch-history-server2.png "Spark-geschiedenis server starten")

2. Selecteer **Spark-geschiedenis server** in het deel venster schuiven.

   ![Spark-geschiedenis Server 2 starten](./media/apache-spark-history-server/launch-history-server.png "Spark-geschiedenis server starten")

## <a name="explore-the-data-tab-in-spark-history-server"></a>Het tabblad gegevens in de Spark-geschiedenis server verkennen

Selecteer de taak-ID voor de taak die u wilt weer geven. Selecteer vervolgens **gegevens** in het menu van het hulp programma om de gegevens weergave op te halen. In deze sectie wordt beschreven hoe u verschillende taken kunt uitvoeren op het tabblad gegevens.

* Controleer de **invoer**, **uitvoer** en **tabel bewerkingen** door de tabbladen afzonderlijk te selecteren.

    ![Gegevens voor de Spark-toepassings tabbladen](./media/apache-spark-history-server/apache-spark-data-tabs.png)

* Kopieer alle rijen door **kopiëren** te selecteren.

    ![Gegevens voor het kopiëren van Spark-toepassingen](./media/apache-spark-history-server/apache-spark-data-copy.png)

* Sla alle gegevens op als CSV-bestand door **CSV** te selecteren.

    ![Gegevens voor opslag van Spark-toepassing](./media/apache-spark-history-server/apache-spark-data-save.png)

* Zoek door tref woorden in **zoeken** in velden in te voeren. De zoek resultaten worden onmiddellijk weer gegeven.

    ![Gegevens voor zoeken in Spark-toepassingen](./media/apache-spark-history-server/apache-spark-data-search.png)

* Selecteer de kolomkop om de tabel te sorteren, selecteer het plus teken om een rij uit te breiden om meer details weer te geven of selecteer het minteken om een rij samen te vouwen.

    ![Gegevens voor de Spark-toepassings tabel](./media/apache-spark-history-server/apache-spark-data-table.png)

* Down load een enkel bestand door **gedeeltelijke down load** te selecteren. Het geselecteerde bestand is gedownload naar lokaal. Als het bestand niet meer bestaat, wordt er een nieuw tabblad weer gegeven met een fout bericht.

    ![Gegevens voor down load rij van Spark-toepassing](./media/apache-spark-history-server/sparkui-data-download-row.png)

* Als u een volledig pad of een relatief pad wilt kopiëren, selecteert u de opties **volledig kopiëren** of **relatief pad** kopiëren die uit de vervolg keuzelijst worden uitgebreid. Voor Azure Data Lake Storage bestanden **opent u in Azure Storage Explorer** Azure Storage Explorer start en zoekt u de map wanneer u bent aangemeld.

    ![Gegevens voor het pad voor het kopiëren van Spark-toepassingen](./media/apache-spark-history-server/sparkui-data-copy-path.png)

* Selecteer pagina nummers onder de tabel om te navigeren in pagina's wanneer er te veel rijen op één pagina worden weer gegeven.

    ![Gegevens voor de Spark-toepassings pagina](./media/apache-spark-history-server/apache-spark-data-page.png)

* Beweeg de muis aanwijzer op het vraag teken naast **gegevens** om de knop Info weer te geven of selecteer het vraag teken voor meer informatie.

    ![Gegevens voor Spark-toepassing meer informatie](./media/apache-spark-history-server/sparkui-data-more-info.png)

* Stuur feedback met problemen door **feedback te geven**.

    ![Spark-grafiek Geef ons feedback](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Tabblad grafiek in Apache Spark geschiedenis server

Selecteer de taak-ID voor de taak die u wilt weer geven. Selecteer vervolgens **grafiek** in het menu van het hulp programma om de weer gave taak grafiek op te halen.

### <a name="overview"></a>Overzicht

U ziet een overzicht van uw taak in het gegenereerde taak diagram. In de grafiek worden standaard alle taken weer gegeven. U kunt deze weer gave filteren op **taak-id**.

![Taak-ID van Spark-toepassing en taak grafiek](./media/apache-spark-history-server/apache-spark-graph-jobid.png)

### <a name="display"></a>Weergave

Standaard wordt de **voortgangs** weergave geselecteerd. U kunt de gegevens stroom controleren door **lezen** of **geschreven** te selecteren in de vervolg keuzelijst **weer geven** .

![Weer gave van Spark-toepassings-en taak grafiek](./media/apache-spark-history-server/sparkui-graph-display.png)

Het grafiek knooppunt geeft de kleuren weer die in de heatmap-legenda worden weer gegeven.

![Spark-toepassing en taak grafiek heatmap](./media/apache-spark-history-server/sparkui-graph-heatmap.png)

### <a name="playback"></a>Afspelen

Selecteer **afspelen** om de taak af te spelen. U kunt op elk gewenst moment **stoppen** selecteren om te stoppen. De taak kleuren tonen verschillende statussen bij het afspelen van:

|Color|Betekenis|
|-|-|
|Green|Geslaagd: de taak is voltooid.|
|Oranje|Opnieuw geprobeerd: exemplaren van taken die zijn mislukt, maar die geen invloed hebben op het uiteindelijke resultaat van de taak. Deze taken bevatten dubbele of nieuwe instanties die later kunnen slagen.|
|Blue|Uitvoeren: de taak wordt uitgevoerd.|
|Wit|Wachtend of overgeslagen: de taak wacht op het uitvoeren of het stadium is overgeslagen.|
|Rood|Mislukt: de taak is mislukt.|

De volgende afbeelding toont de kleuren groen, oranje en blauw.

![Kleur voorbeeld van Spark-toepassing en taak grafiek, uitvoeren](./media/apache-spark-history-server/sparkui-graph-color-running.png)

In de volgende afbeelding ziet u de kleuren van de groene en witte status.

![Kleur voorbeeld van Spark-toepassing en taak grafiek, overs Laan](./media/apache-spark-history-server/sparkui-graph-color-skip.png)

In de volgende afbeelding ziet u de kleuren van de rode en groene status.

![Kleur voorbeeld van Spark-toepassing en taak grafiek, mislukt](./media/apache-spark-history-server/sparkui-graph-color-failed.png)

> [!NOTE]  
> Het afspelen van elke taak is toegestaan. Voor niet-voltooide taken wordt afspelen niet ondersteund.

### <a name="zoom"></a>Zoom

Gebruik de schuif balk van de muis om in en uit te zoomen op de taak grafiek, of selecteer **Inzoomen passend** maken om deze aan te passen aan het scherm.

![Inzoomen op een Spark-toepassing en-taak](./media/apache-spark-history-server/sparkui-graph-zoom2fit.png)

### <a name="tooltips"></a>Knopinfo

Beweeg de muis aanwijzer op het grafiek knooppunt om de knop Info weer te geven wanneer er mislukte taken zijn en selecteer een fase om de pagina fase ervan te openen.

![Knop Info voor Spark-toepassing en taak grafiek](./media/apache-spark-history-server/sparkui-graph-tooltip.png)

Op het tabblad taak grafiek worden de knop Info en een klein pictogram weer gegeven als er taken zijn die voldoen aan de volgende voor waarden:

|Conditie|Description|
|-|-|
|Gegevens scheef trekken|gegevens lezen grootte > gemiddelde grootte van het lezen van gegevens van alle taken in deze fase * 2 en grootte van gegevens lezen > 10 MB|
|Tijd verschil|uitvoerings tijd > gemiddelde uitvoerings tijd van alle taken in deze fase * 2 en uitvoerings tijd > 2 minuten|
   
![Pictogram van Spark-toepassing en taak grafiek scheefheid](./media/apache-spark-history-server/sparkui-graph-skew-icon.png)

### <a name="graph-node-description"></a>Beschrijving van grafiek knooppunt

In het knoop punt taak grafiek worden de volgende gegevens van elke fase weer gegeven:

  * ID.
  * Naam of beschrijving.
  * Totaal taak nummer.
  * Gegevens lezen: de som van de invoer grootte en de grootte van de Lees bewerking in wille keurige volg orde.
  * Gegevens schrijven: de som van de uitvoer grootte en de grootte van schrijf bewerkingen in wille keurige volg orde.
  * Uitvoerings tijd: de tijd tussen de begin tijd van de eerste poging en voltooiings tijd van de laatste poging.
  * Aantal rijen: de som van de invoer records, uitvoer records, in wille keurige volg orde records lezen en schrijf records in wille keurige volg orde opslaan.
  * Gang.

    > [!NOTE]  
    > Het knoop punt taak grafiek geeft standaard informatie weer van de laatste poging van elke fase (met uitzonde ring van de uitvoerings tijd van de fase). Tijdens het afspelen toont het grafiek knooppunt echter informatie van elke poging.
    >  
    > De gegevens grootte van lezen en schrijven is 1 MB = 1000 KB = 1000 * 1000 bytes.

### <a name="provide-feedback"></a>Feedback geven

Stuur feedback met problemen door **feedback te geven**.

![Feedback over Spark-toepassingen en taak grafieken](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="explore-the-diagnosis-tab-in-apache-spark-history-server"></a>Het tabblad diagnose in Apache Spark geschiedenis server verkennen

Selecteer een taak-ID om toegang te krijgen tot het tabblad diagnose. Selecteer vervolgens **diagnose** in het menu van het hulp programma om de taak diagnose weer te geven. Het tabblad diagnose bevat **gegevens scheefheid**, **tijds scheefheid** en **gebruiks analyse**.

Controleer de **gebruiks analyse** van de **gegevens scheefheid**, het **tijds verschil** en de uitvoerder door de tabbladen respectievelijk te selecteren.

![SparkUI-tabblad voor diagnose gegevens](./media/apache-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Gegevens scheef trekken

Wanneer u het tabblad **gegevens scheefheid** selecteert, worden de bijbehorende gescheefe taken weer gegeven op basis van de opgegeven para meters.

* **Para meters opgeven** : in de eerste sectie worden de para meters weer gegeven, die worden gebruikt voor het detecteren van gegevens scheefheid. De standaard regel is: het lezen van de taak gegevens is groter dan drie keer van het gemiddelde van de taak gegevens gelezen en de gelezen taak gegevens zijn meer dan 10 MB. Als u uw eigen regel wilt definiëren voor gescheefde taken, kunt u de para meters kiezen. De secties **scheefe fase** en **scheefe tekens** worden dienovereenkomstig vernieuwd.

* Verdeelde **fase** : in het tweede gedeelte worden fasen weer gegeven, die gescheefe taken hebben die voldoen aan de bovenstaande criteria. Als er meer dan een scheefe taak in een fase is, wordt in de tabel verscheefde fase alleen de meest verlaagde taak weer gegeven (bijvoorbeeld de grootste gegevens voor het scheef trekken van gegevens).

    ![tabblad sparkui-diagnose gegevens scheefheid](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

* **Grafiek scheef trekken** : wanneer een rij in de tabel scheefe fase is geselecteerd, worden in het schuine diagram meer details over taak distributie weer gegeven op basis van de tijd waarop gegevens worden gelezen en uitgevoerd. De gescheefe taken worden rood gemarkeerd en de normale taken worden blauw gemarkeerd. In de grafiek worden een aantal voorbeeld taken van 100 weer gegeven en de taak Details worden weer gegeven in het rechter paneel.

    ![sparkui scheefe-grafiek voor fase 10](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Tijd verschil

Op het tabblad **tijd verschil** worden scheefe taken weer gegeven op basis van de uitvoerings tijd van de taak.

* **Para meters opgeven** : in de eerste sectie worden de para meters weer gegeven, die worden gebruikt voor het detecteren van tijd scheefheid. De standaard criteria voor het detecteren van tijd verschil is: de uitvoerings tijd van de taak is groter dan drie keer na de gemiddelde uitvoerings tijd en de uitvoerings tijd van de taak is langer dan 30 seconden. U kunt de para meters wijzigen op basis van uw behoeften. In de grafiek **scheefe fase** en **scheefheid** worden de bijbehorende fasen en taak gegevens weer gegeven, net als het tabblad **gegevens scheefheid** hierboven.

* Selecteer **tijd scheefheid** en vervolgens gefilterd resultaat wordt weer gegeven in de sectie **scheefe fase** volgens de para meters die zijn ingesteld in **para meters opgeven**. Selecteer één item in de sectie **scheefend stadium** , vervolgens wordt de bijbehorende grafiek geschetst in section3 en worden de taak details weer gegeven in het rechter paneel.

    ![de sectie sparkui-diagnose tijd scheef trekken](./media/apache-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Gebruiks analyse

In de gebruiks grafiek van de uitvoerder wordt de toewijzings-en uitvoerings status van de Spark-taak uitwerkt.  

1. Selecteer **gebruiks analyse** uitvoeren en vervolgens vier typen curven over het gebruik van de uitvoerder, met inbegrip **van toegewezen uitvoerende** machines, het **uitvoeren van uitvoerende** machines, **niet-actieve uitvoerende** machines en **Maxi maal** bewerkings instanties. Voor toegewezen uitvoerendeers verhoogt of verkleint elke ' uitvoerder ' of ' uitvoeringen verwijderd ' de toegewezen uitvoerder. U kunt ' gebeurtenis tijdlijn ' op het tabblad taken voor meer vergelijking controleren.

   ![tabblad sparkui diagnose-uitvoeringen](./media/apache-spark-history-server/sparkui-diagnosis-executors.png)

2. Selecteer het kleur pictogram om de bijbehorende inhoud in alle concepten te selecteren of de selectie ervan op te heffen.

    ![sparkui diagnosticeert diagram selecteren](./media/apache-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="known-issues"></a>Bekende problemen

Invoer-en uitvoer gegevens met behulp van robuuste gedistribueerde gegevens sets (Rdd's) worden niet weer gegeven op het tabblad gegevens.

## <a name="next-steps"></a>Volgende stappen

- [Azure Synapse Analytics](../overview-what-is.md)
- [Documentatie voor .NET voor Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)


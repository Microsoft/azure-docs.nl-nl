---
title: Fouten opsporen in Apache Spark-taken die worden uitgevoerd in Azure HDInsight
description: De GARENs-UI, Spark-gebruikers interface en Spark-geschiedenis server gebruiken voor het bijhouden en opsporen van fouten in taken die worden uitgevoerd op een Spark-cluster in azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 366c77ff94773163b71845b1ccbc6072c503734a
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822298"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Fouten opsporen in Apache Spark-taken die worden uitgevoerd in Azure HDInsight

In dit artikel leert u hoe u Apache Spark-taken die worden uitgevoerd op HDInsight-clusters kunt bijhouden en fouten oplost. Fout opsporing met behulp van de Apache Hadoop GARENs UI, Spark-gebruikers interface en de Spark-geschiedenis server. U start een Spark-taak met behulp van een notitie blok dat beschikbaar is in het Spark-cluster, **machine learning: voorspellende analyse van gegevens van levensmiddelen inspecties met behulp van MLLib**. Gebruik de volgende stappen om een toepassing bij te houden die u hebt verzonden met behulp van een andere methode, bijvoorbeeld **Spark-verzen** ding.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een Apache Spark-cluster in HDInsight. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies.

* U moet het notitie blok, **[machine learning: voorspellende analyse van gegevens van de levensmiddelen inspectie starten met behulp van MLLib](apache-spark-machine-learning-mllib-ipython.md)**. Volg de koppeling voor instructies over het uitvoeren van dit notitie blok.  

## <a name="track-an-application-in-the-yarn-ui"></a>Een toepassing volgen in de garen-gebruikers interface

1. Start de garen-gebruikers interface. Selecteer **garens** onder **cluster dashboards**.

    ![Azure Portal GARENs-UI starten](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)

   > [!TIP]  
   > U kunt ook de gebruikers interface van garen starten vanuit de Ambari-gebruikers interface. Als u de Ambari-gebruikers interface wilt starten, selecteert u **Ambari Home** onder **cluster dashboards**. Ga in de Ambari-gebruikers interface naar de snelle koppelingen voor **garens**  >   > de actieve Resource Manager-> **Resource Manager-gebruikers interface**.

2. Omdat u de Spark-taak met Jupyter-notebooks hebt gestart, heeft de toepassing de naam **remotesparkmagics** (de naam voor alle toepassingen die vanuit de notitie blokken zijn gestart). Selecteer de toepassings-ID voor de toepassings naam om meer informatie over de taak weer te geven. Met deze actie wordt de toepassings weergave gestart.

    ![Spark-geschiedenis server zoeken naar Spark-toepassings-ID](./media/apache-spark-job-debugging/find-application-id1.png)

    Voor dergelijke toepassingen die worden gestart vanuit de Jupyter-notebooks, wordt de status altijd **uitgevoerd** totdat u het notitie blok sluit.

3. Vanuit de toepassings weergave kunt u verder inzoomen op de containers die zijn gekoppeld aan de toepassing en de logboeken (stdout/stderr). U kunt ook de Spark-gebruikers interface starten door te klikken op de koppeling die overeenkomt met de **tracerings-URL**, zoals hieronder wordt weer gegeven.

    ![Container logboeken van de Spark-geschiedenis server downloaden](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Een toepassing volgen in de Spark-gebruikers interface

In de Spark-gebruikers interface kunt u inzoomen op de Spark-taken die worden uitgevoerd door de toepassing die u eerder hebt gestart.

1. Als u de Spark-gebruikers interface wilt starten, selecteert u in de toepassings weergave de koppeling met de **tracerings-URL**, zoals wordt weer gegeven in de bovenstaande scherm opname. U kunt alle Spark-taken zien die worden gestart door de toepassing die wordt uitgevoerd in de Jupyter Notebook.

    ![Het tabblad Server taken van Spark-geschiedenis](./media/apache-spark-job-debugging/view-apache-spark-jobs.png)

2. Selecteer het tabblad **uitvoerende uitvoeringen** om de verwerkings-en opslag informatie voor elke uitvoerder te bekijken. U kunt de aanroep stack ook ophalen door de **thread dump** koppeling te selecteren.

    ![Tabblad voor het uitvoeren van de Spark-geschiedenis server](./media/apache-spark-job-debugging/view-spark-executors.png)

3. Selecteer het tabblad **stadia** om de fasen te zien die zijn gekoppeld aan de toepassing.

    ![Het tabblad Server fasen van Spark-geschiedenis](./media/apache-spark-job-debugging/view-apache-spark-stages.png "Spark-fasen weer geven")

    Elke fase kan meerdere taken bevatten waarvoor u uitvoerings statistieken kunt bekijken, zoals hieronder wordt weer gegeven.

    ![Details van het tabblad Server fasen in Spark-geschiedenis](./media/apache-spark-job-debugging/view-spark-stages-details.png "Details van Spark-fasen weer geven")

4. Op de pagina Details van fase kunt u DAG visualisatie starten. Vouw de koppeling **dag visualisatie** boven aan de pagina uit, zoals hieronder wordt weer gegeven.

    ![Visualisatie van Spark-fasen DAG weer geven](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)

    DAG of direct Aclyic Graph vertegenwoordigt de verschillende fasen in de toepassing. Elk blauw vak in de grafiek vertegenwoordigt een Spark-bewerking die vanuit de toepassing is aangeroepen.

5. Op de pagina Details van fase kunt u ook de weer gave toepassings tijdlijn starten. Vouw de koppeling voor de **tijdlijn gebeurtenis** toe boven aan de pagina, zoals hieronder wordt weer gegeven.

    ![Gebeurtenis tijdlijn van Spark-fasen weer geven](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)

    In deze afbeelding worden de Spark-gebeurtenissen in de vorm van een tijd lijn weer gegeven. De tijdlijn weergave is beschikbaar op drie niveaus, in taken, binnen een taak en binnen een fase. In de bovenstaande afbeelding wordt de tijdlijn weergave voor een bepaalde fase vastgelegd.

   > [!TIP]  
   > Als u het selectie vakje **zoomen inschakelen** inschakelt, kunt u naar links en rechts schuiven in de tijdlijn weergave.

6. Andere tabbladen in de Spark-gebruikers interface geven ook nuttige informatie over de Spark-instantie.

   * Tabblad opslag: als uw toepassing een RDD maakt, kunt u informatie vinden op het tabblad opslag.
   * Tabblad omgeving: dit tabblad bevat nuttige informatie over uw Spark-exemplaar, zoals:
     * Scala-versie
     * De map met gebeurtenis logboeken die aan het cluster zijn gekoppeld
     * Aantal uitvoer kernen voor de toepassing

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Informatie over voltooide taken zoeken met behulp van de Spark-geschiedenis server

Zodra een taak is voltooid, wordt de informatie over de taak opgeslagen in de Spark-geschiedenis server.

1. Als u de Spark-geschiedenis server wilt starten, selecteert u op de pagina **overzicht** de optie **Spark-geschiedenis server** onder **cluster dashboards**.

    ![Azure Portal Spark-geschiedenis server starten](./media/apache-spark-job-debugging/launch-spark-history-server.png "Spark geschiedenis server1 starten")

   > [!TIP]  
   > U kunt ook de gebruikers interface van de Spark-geschiedenis server starten vanuit de Ambari-gebruikers interface. Als u de Ambari-gebruikers interface wilt starten, selecteert u op de Blade overzicht de optie **Ambari Home** onder **cluster dashboards**. Ga in de Ambari-gebruikers interface naar **Spark2**  >  **Quick links**  >  **Spark2 geschiedenis server UI**.

2. U ziet alle voltooide toepassingen die worden weer gegeven. Selecteer een toepassings-ID om in te zoomen op een toepassing voor meer informatie.

    ![Voltooide Spark-geschiedenis server toepassingen](./media/apache-spark-job-debugging/view-completed-applications.png "Spark-geschiedenis server2 starten")

## <a name="see-also"></a>Zie tevens

* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Fouten opsporen Apache Spark taken met uitgebreide Spark-geschiedenis server](apache-azure-spark-history-server.md)
* [Fouten opsporen Apache Spark toepassingen met Azure-toolkit voor IntelliJ via SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

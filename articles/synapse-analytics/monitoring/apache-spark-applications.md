---
title: Apache Spark-toepassingen bewaken met behulp van Synapse Studio
description: Gebruik Synapse Studio om uw Apache Spark-toepassingen te bewaken.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 00407b4a55b3c074fa77d4c3664f6e218c23fe21
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584508"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>Synapse Studio gebruiken om uw Apache Spark-toepassingen te bewaken

Met Azure Synapse Analytics kunt u Apache Spark gebruiken om notitie blokken, taken en andere soorten toepassingen uit te voeren op uw Apache Spark-groepen in uw werk ruimte.

In dit artikel wordt uitgelegd hoe u uw Apache Spark-toepassingen kunt bewaken, zodat u de meest recente status, problemen en voortgang kunt blijven gebruiken.

Deze zelfstudie bestaat uit de volgende taken:

* Controleren op uitvoering Apache Spark toepassing
* Voltooide Apache Spark-toepassing weer geven
* Geannuleerde Apache Spark toepassing weer geven
* Fout opsporing mislukt Apache Spark toepassing

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u aan de volgende vereisten voldoet voordat u met deze zelfstudie begint:

- Een Synapse studio-werk ruimte. Zie [een Synapse studio-werk ruimte maken](../../machine-learning/how-to-manage-workspace.md#create-a-workspace)voor instructies.

- Een Apache Spark-pool.

## <a name="view-apache-spark-applications"></a>Apache Spark-toepassingen weer geven 
U kunt alle Apache Spark toepassingen weer geven om  ->  **Apache Spark toepassingen** te bewaken.
   ![Apache Spark-toepassingen](./media/how-to-monitor-spark-applications/apache-spark-applications.png)

## <a name="view-completed-apache-spark-application"></a>Voltooide Apache Spark-toepassing weer geven

Open **monitor** en selecteer vervolgens **Apache Spark toepassingen**. Als u de details van de voltooide Apache Spark toepassingen wilt weer geven, selecteert u de Apache Spark toepassing en bekijkt u de details.

  ![voltooide taak selecteren](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. Controleer de **voltooide taken**, **status** en **totale duur**.

2. Logboek query vernieuwen.

3. Open de koppeling Apache Spark geschiedenis server door te klikken op de **Spark-geschiedenis server**.

4. Controleer de **samenvattings** informatie.

5. Controleer de **Logboeken**. U kunt verschillende soorten logboeken selecteren in de vervolg keuzelijst, en u kunt de logboek gegevens downloaden door op **Logboeken downloaden** te klikken en het selectie vakje van **filter fouten en waarschuwingen** in te stellen om de fouten en waarschuwingen die u nodig hebt te filteren.

6. U ziet een overzicht van uw taak in het gegenereerde taak diagram. In de grafiek worden standaard alle taken weer gegeven. U kunt deze weer gave filteren op **taak-id**.

7. Standaard wordt de **voortgangs** weergave geselecteerd. U kunt de gegevens stroom controleren door  /  /  / in de vervolg keuzelijst **weer gave** voortgang gelezen **duur** te selecteren.

8. Als u de taak wilt afspelen, klikt u op de knop **afspelen** . U kunt op elk gewenst moment op de knop **stoppen** klikken om te stoppen.

9. Gebruik muis schuiven of schuif balk om in te zoomen en uitzoomen op de taak grafiek, u kunt ook **Inzoomen passend** maken om het aan te passen aan het scherm.

10. In het knoop punt taak grafiek worden de volgende gegevens van elke fase weer gegeven:

    * ID.

    * Naam of beschrijving.

    * Totaal taak nummer.

    * Gegevens lezen: de som van de invoer grootte en de grootte van de Lees bewerking in wille keurige volg orde.

    * Gegevens schrijven: de som van de uitvoer grootte en de grootte van schrijf bewerkingen in wille keurige volg orde.

    * Uitvoerings tijd: de tijd tussen de begin tijd van de eerste poging en voltooiings tijd van de laatste poging.

    * Aantal rijen: de som van de invoer records, uitvoer records, in wille keurige volg orde records lezen en schrijf records in wille keurige volg orde opslaan.

    * Gang.

     ![voltooide taak weer geven](./media/how-to-monitor-spark-applications/view-completed-job.png)
    
11. Klik op **Details weer geven** in de grafiek en vervolgens de details van de fase worden weer gegeven.

    ![Details voor fase](./media/how-to-monitor-spark-applications/details-for-stage.png)


## <a name="monitor-running-apache-spark-application"></a>Controleren op uitvoering Apache Spark toepassing

Open **monitor** en selecteer vervolgens **Apache Spark toepassingen**. Als u de details wilt weer geven van de Apache Spark toepassingen die worden uitgevoerd, selecteert u de toepassing voor het verzenden van Apache Spark en bekijkt u de details. Als de Apache Spark toepassing nog steeds wordt uitgevoerd, kunt u de voortgang bewaken.

   ![actieve taak selecteren](./media/how-to-monitor-spark-applications/select-running-job.png)

1. Controleer de **voltooide taken**, **status** en **totale duur**.

2. De Apache Spark-toepassing **Annuleren** .

3. **Vernieuwen** Query registreren.

4. Klik op de knop met **Spark-gebruikers interface** om naar de Spark-taak pagina te gaan.

5. Bekijk de grafiek. U ziet een overzicht van uw taak in het gegenereerde taak diagram. Raadpleeg stap 6, 7, 8, 9, 10 van [weer gave is voltooid Apache Spark toepassing](#view-completed-apache-spark-application).

6. Controleer de **samenvattings** informatie.

7. Controleer het tabblad Diagnostische gegevens op **diagnose** .

8. Controleer de **Logboeken** op dit tabblad. U kunt verschillende soorten logboeken selecteren in de vervolg keuzelijst, en u kunt de logboek gegevens downloaden door op **Logboeken downloaden** te klikken en het selectie vakje van **filter fouten en waarschuwingen** in te stellen om de fouten en waarschuwingen die u nodig hebt te filteren.

    ![actieve taak weer geven](./media/how-to-monitor-spark-applications/view-running-job.png)

## <a name="view-canceled-apache-spark-application"></a>Geannuleerde Apache Spark toepassing weer geven

Open **monitor** en selecteer vervolgens **Apache Spark toepassingen**. Als u de details van de geannuleerde Apache Spark toepassingen wilt weer geven, selecteert u de Apache Spark toepassing en bekijkt u de details.

 ![geannuleerde taak selecteren](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. Controleer de **voltooide taken**, **status** en **totale duur**.

2. Vernieuw de logboek query.

3. Open Apache geschiedenis server koppeling door te klikken op de **Spark-geschiedenis server**.

4. Bekijk de grafiek. U ziet een overzicht van uw taak in het gegenereerde taak diagram. Raadpleeg stap 6, 7, 8, 9, 10 van [weer gave is voltooid Apache Spark toepassing](#view-completed-apache-spark-application).

5. Controleer de **samenvattings** informatie.

6. Controleer de **Logboeken**. U kunt verschillende soorten logboeken selecteren in de vervolg keuzelijst, en u kunt de logboek gegevens downloaden door op Logboeken **downloaden** te klikken en het selectie vakje van **filter fouten en waarschuwingen** in te stellen om de fouten en waarschuwingen die u nodig hebt te filteren.

7. Klik op **Details weer geven** in de grafiek en vervolgens de details van de fase worden weer gegeven.

   ![geannuleerde taak weer geven](./media/how-to-monitor-spark-applications/view-cancelled-job.png)

## <a name="debug-failed-apache-spark-application"></a>Fout opsporing mislukt Apache Spark toepassing

Open **monitor** en selecteer vervolgens **Apache Spark toepassingen**. Als u de details van de mislukte Apache Spark toepassingen wilt weer geven, selecteert u de Apache Spark toepassing en bekijkt u de details.

![mislukte taak selecteren](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. Controleer de **voltooide taken**, **status** en **totale duur**.

2. Logboek query vernieuwen.

3. Open de koppeling Apache Spark geschiedenis server door te klikken op de **Spark-geschiedenis server**.

4. Bekijk de grafiek. U ziet een overzicht van uw taak in het gegenereerde taak diagram. Raadpleeg stap 6, 7, 8, 9, 10 van [weer gave is voltooid Apache Spark toepassing](#view-completed-apache-spark-application)

5. Controleer de **samenvattings** informatie.

6. Controleer de fout gegevens.

   ![mislukte taak gegevens](./media/how-to-monitor-spark-applications/failed-job-info.png)

## <a name="compare-apache-spark-applications"></a>Apache Spark toepassingen vergelijken
Klik op de knop **toepassingen vergelijken** en kies een toepassing om de prestaties te vergelijken. gebruikers kunnen het verschil tussen de twee toepassingen intuïtief zien.

![toepassingen vergelijken](./media/how-to-monitor-spark-applications/compare-applications.png)

1. Gebruik de muis om op een toepassing aan te wijzen en vervolgens het pictogram **toepassingen vergelijken** wordt weer gegeven.

2. Klik op het pictogram **toepassingen vergelijken** en de pagina toepassingen vergelijken wordt weer gegeven.

3. Klik op de knop **toepassing kiezen** om de pagina **vergelijkings toepassing kiezen** te openen.

4. Bij het kiezen van de vergelijkings toepassing moeten gebruikers de URL van de toepassing invoeren of een keuze uit de terugkerende lijst kiezen. Klik vervolgens op de knop **OK** . 

   ![een vergelijkings toepassing kiezen](./media/how-to-monitor-spark-applications/choose-comparison-application.png)

5. Het resultaat van de vergelijking wordt weer gegeven op de pagina toepassingen vergelijken.

   ![resultaat van vergelijking](./media/how-to-monitor-spark-applications/comparison-result.png)


## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het bewaken van pijplijn uitvoeringen raadpleegt [u de pipeline-bewaken in het artikel Synapse Studio](how-to-monitor-pipeline-runs.md) .

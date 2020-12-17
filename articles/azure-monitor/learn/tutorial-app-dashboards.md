---
title: Aangepaste dashboards maken in Azure Application Insights | Microsoft Docs
description: Zelfstudie over het maken van aangepaste KPI-dashboards met behulp van Azure Application Insights.
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 09/30/2020
ms.custom: mvc, contperf-fy21q1
ms.openlocfilehash: 701480300101b019830c57d9aa000534fa63bb6a
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028526"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Aangepaste KPI-dashboards maken met behulp van Azure Application Insights

U kunt in Azure Portal meerdere dashboards maken die elk tegels bevatten die gegevens weergeven vanuit meerdere Azure-resources in verschillende resourcegroepen en abonnementen.  U kunt verschillende grafieken en weergaven van Azure Application Insights vastmaken om aangepaste dashboards te maken die u een volledig overzicht geven van de status en prestaties van uw toepassing. Deze zelfstudie helpt u bij het maken van een aangepast dashboard met meerdere typen gegevens en visualisaties uit Azure Application Insights.

 In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een aangepast dashboard maken in Azure
> * Een tegel uit de Tegelgalerie toevoegen
> * Standaard metrische gegevens in Application Insights aan het dashboard toevoegen
> * Een aangepaste grafiek met metrische gegevens uit Application Insights aan het dashboard toevoegen
> * De resultaten van een Logs (Analytics)-query aan het dashboard toevoegen

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

- Implementeer een .NET-toepassing in Azure en [schakel de Application Insights-SDK](../app/asp-net.md)in.

> [!NOTE]
> De vereiste machtigingen voor het werken met dashboards worden beschreven in het artikel over [toegangsbeheer voor dashboards](../../azure-portal/azure-portal-dashboard-share-access.md#understanding-access-control-for-dashboards).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-new-dashboard"></a>Een nieuw dashboard maken

> [!WARNING]
> Als u uw Application Insights-resource naar een andere resourcegroep of ander abonnement verplaatst, moet u het dashboard handmatig bijwerken door de oude tegels te verwijderen en nieuwe tegels vast te maken vanuit dezelfde Application Insights-resource op een nieuwe locatie.

Een dashboard kan resources uit meerdere toepassingen, resourcegroepen en abonnementen bevatten.  Begin deze zelfstudie met het maken van een nieuw dashboard voor uw toepassing.  

1. Selecteer **Dashboard** in het menu aan de linkerkant in de Azure Portal.

    ![Vervolgkeuzelijst in het menu van de Azure Portal](media/tutorial-app-dashboards/dashboard-from-menu.png)

2. Selecteer in het dashboarddeelvenster de optie **Nieuw dashboard**, gevolgd door **Leeg dashboard**.

   ![Nieuw dashboard](media/tutorial-app-dashboards/new-dashboard.png)

3. Typ een naam voor het dashboard.
4. Bekijk de **Tegelgalerie** voor de verschillende tegels die u aan het dashboard kunt toevoegen.  U kunt niet alleen tegels uit de galerie toevoegen, maar ook grafieken en andere weergaven rechtstreeks vanuit Application Insights aan het dashboard vastmaken.
5. Zoek de tegel **Markdown** en sleep deze naar uw dashboard.  Met deze tegel kunt u tekst opmaken in Markdown, wat ideaal is om beschrijvende tekst toe te voegen aan uw dashboard. Zie [Een markdown-tegel in Azure-dashboards gebruiken om aangepaste inhoud weer te geven](../../azure-portal/azure-portal-markdown-tile.md) voor meer informatie.
6. Voeg tekst toe aan de tegeleigenschappen en wijzig het formaat van de tegel op het canvas van het dashboard.

    [![Tegel Markdown bewerken](media/tutorial-app-dashboards/markdown.png)](media/tutorial-app-dashboards/dashboard-edit-mode.png#lightbox)

7. Selecteer **Aanpassen voltooid** boven aan het scherm om de modus voor het aanpassen van tegels af te sluiten.

## <a name="add-health-overview"></a>Statusoverzicht toevoegen

Een dashboard met statische tekst is niet erg interessant. Voeg daarom een tegel toe vanuit Application Insights die informatie over uw toepassing weergeeft. U kunt Application Insights-tegels toevoegen uit de Tegelgalerie of ze rechtstreeks vanuit Application Insights-schermen vastmaken. Hiermee kunt u grafieken en weergaven die u al kent, configureren voordat u ze aan uw dashboard vastmaakt.  Voeg eerst het standaardstatusoverzicht voor uw toepassing toe.  Hiervoor is geen configuratie vereist en er is minimale aanpassing mogelijk in het dashboard.


1. Open uw **Application Insights**-resource op het startscherm.
2. Selecteer in het deel venster **Overzicht** het pictogram vastmaken ![pictogram vastmaken](media/tutorial-app-dashboards/pushpin.png) om de tegel toe te voegen aan een dashboard.
3. Selecteer op het tabblad Vastmaken aan dashboard aan welk dashboard de tegel moet worden toegevoegd of dat een nieuw dashboard moet worden gemaakt.
 
3. In de rechterbovenhoek wordt de melding weergegeven dat de tegel is vastgemaakt aan uw dashboard.  Selecteer **Aan dashboard vastgemaakt** in de melding om terug te keren naar het dashboard of om het dashboarddeelvenster te gebruiken.
4. De tegel is nu aan uw dashboard toegevoegd. Selecteer **Bewerken** om de plaatsing van de tegel te wijzigen. Selecteer de tegel en sleep deze naar de gewenste positie. Selecteer vervolgens **Aanpassen voltooid**. Uw dashboard heeft nu een tegel met nuttige informatie.

    [![Dashboard in de bewerkingsmodus](media/tutorial-app-dashboards/dashboard-edit-mode.png)](media/tutorial-app-dashboards/dashboard-edit-mode.png#lightbox)

## <a name="add-custom-metric-chart"></a>Aangepaste grafiek met metrische gegevens toevoegen

In het venster **Metrische gegevens** kunt u waarden die gedurende een periode door Application Insights zijn verzameld, met optionele filters en groepering in een grafiek weergeven.  Net als alle andere items in Application Insights kunt u deze grafiek aan het dashboard toevoegen.  Hiervoor is wel enige aanpassing vereist.

1. Open uw **Application Insights**-resource in het startscherm.
1. Selecteer **Metrische gegevens**.  
2. Er is al een lege grafiek gemaakt en u wordt gevraagd een waarde toe te voegen.  Voeg een waarde toe aan de grafiek en eventueel een filter en groepering.  In het volgende voorbeeld ziet u het aantal serververzoeken gegroepeerd op of ze geslaagd zijn.  Het resultaat is een actieve weergave van geslaagde en mislukte aanvragen.

    [![Waarde toevoegen](media/tutorial-app-dashboards/metrics.png)](media/tutorial-app-dashboards/metrics.png#lightbox)

4. Selecteer **Vastmaken aan dashboard** aan de rechterkant.

3.  In de rechterbovenhoek wordt de melding weergegeven dat de tegel is vastgemaakt aan uw dashboard. Selecteer **Aan dashboard vastgemaakt** in de melding om terug te keren naar het dashboard of om het dashboardtabblad te gebruiken.

4. De tegel is nu aan uw dashboard toegevoegd. Selecteer **Bewerken** om de plaatsing van de tegel te wijzigen. Selecteer de tegel en sleep deze naar de gewenste positie. Selecteer vervolgens **Aanpassen voltooid**.

## <a name="add-logs-query"></a>Query voor logboeken toevoegen

Azure Application Insights Logs biedt een uitgebreide querytaal om alle met Application Insights verzamelde gegevens te analyseren. Net als andere weergaven en grafieken kunt u de uitvoer van een Logs-query aan uw dashboard toevoegen.

1. Open uw **Application Insights**-resource in het startscherm.
2. Selecteer **Logboeken** aan de linkerkant onder Bewaking om het tabblad Logboeken te openen.
3. Typ de volgende query die de 10 meest aangevraagde pagina's en het bijbehorende aantal aanvragen retourneert:

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. Selecteer **Uitvoeren** om de resultaten van de query te valideren.
5. Selecteer het speldpictogram ![Speldpictogram](media/tutorial-app-dashboards/pushpin.png) en selecteer de naam van het dashboard.

5. Voordat u teruggaat naar het dashboard, voegt u nog een query toe, maar nu geeft u het dashboard weer als een diagram. Zo kunt u de verschillende manieren zien waarop u een Logs-query in een dashboard kunt weergeven. Begin met de volgende query die de top 10 bewerkingen met de meeste uitzonderingen weergeeft.

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. Selecteer **Grafiek** en schakel vervolgens over naar een **Ringdiagram** om de uitvoer weer te geven.

    [![Ringdiagram met de bovenstaande query](media/tutorial-app-dashboards/logs-doughnut.png)](media/tutorial-app-dashboards/logs-doughnut.png#lightbox)

6. Selecteer het speldpictogram ![Speldpictogram](media/tutorial-app-dashboards/pushpin.png) in de rechterbovenhoek om de grafiek aan uw dashboard vast te maken. Ga daarna terug naar uw dashboard.
7. De resultaten van de query's worden nu aan uw dashboard toegevoegd in de indeling die u hebt geselecteerd. Selecteer de tegels en sleep ze naar de gewenste positie. Selecteer vervolgens **Aanpassen voltooid**.
8. Selecteer het potloodpictogram ![Potloodpictogram](media/tutorial-app-dashboards/pencil.png) bij elke titel om een beschrijvende titel toe te voegen.

## <a name="share-dashboard"></a>Dashboard delen

1. Selecteer **Delen** aan de bovenkant van het dashboard om uw wijzigingen te publiceren.
2. Desgewenst kunt u specifieke gebruikers opgeven die toegang tot het dashboard moeten krijgen. Zie [Azure-dashboards delen met behulp van op rollen gebaseerd toegangsbeheer](../../azure-portal/azure-portal-dashboard-share-access.md) voor meer informatie.
3. Selecteer **Publiceren**.

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u aangepaste dashboards maakt, kunt u de verdere Application Insights-documentatie bekijken, waaronder een casestudy.

> [!div class="nextstepaction"]
> [Diepe diagnostische gegevens](../app/devops.md)

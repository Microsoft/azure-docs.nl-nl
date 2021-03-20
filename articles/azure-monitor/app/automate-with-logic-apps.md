---
title: Azure-toepassing Insights-processen automatiseren met behulp van Logic Apps
description: Meer informatie over hoe u Herhaal bare processen snel kunt automatiseren door de Application Insights-connector toe te voegen aan uw logische app.
ms.topic: conceptual
ms.date: 03/11/2019
ms.openlocfilehash: d7ff75be3cb847235405a740df4a20803cdc87b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589914"
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Application Insights processen automatiseren met behulp van Logic Apps

Vindt u het herhaaldelijk om dezelfde query's op uw telemetriegegevens uit te voeren om te controleren of uw service goed werkt? Wilt u deze query's automatiseren om trends en afwijkingen op te sporen en vervolgens uw eigen werk stromen rond hen te bouwen? De Azure-toepassing Insights-connector voor Logic Apps is het juiste hulp middel voor dit doel.

> [!NOTE]
> De Azure-toepassing Insights-connector is vervangen door de [Azure monitor-connector](../logs/logicapp-flow-connector.md) die is geïntegreerd met Azure Active Directory in plaats van een API-sleutel te vereisen en u kunt ook gegevens ophalen uit een log Analytics-werk ruimte.

Met deze integratie kunt u talloze processen automatiseren zonder dat u maar één regel code hoeft te schrijven. U kunt een logische app maken met de Application Insights-connector om snel een Application Insights proces te automatiseren. 

U kunt ook extra acties toevoegen. De functie Logic Apps van Azure App Service maakt honderden acties beschikbaar. Als u bijvoorbeeld een logische app gebruikt, kunt u automatisch een e-mail melding verzenden of een bug maken in azure DevOps. U kunt ook een van de vele beschik bare [sjablonen](../../logic-apps/logic-apps-create-logic-apps-from-templates.md) gebruiken om het proces van het maken van uw logische app te versnellen. 

## <a name="create-a-logic-app-for-application-insights"></a>Een logische app maken voor Application Insights

In deze zelf studie leert u hoe u een logische app maakt die gebruikmaakt van het algoritme van de analyse van de analytische methode voor het groeperen van kenmerken in de gegevens voor een webtoepassing. De stroom verzendt de resultaten automatisch per e-mail, maar één voor beeld van hoe u Application Insights Analytics en Logic Apps samen kunt gebruiken. 

### <a name="step-1-create-a-logic-app"></a>Stap 1: een logische app maken
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Klik op **een resource maken**, selecteer **Web en mobiel** en selecteer vervolgens **logische app**.

    ![Venster nieuwe logische app](./media/automate-with-logic-apps/1createlogicapp.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>Stap 2: een trigger maken voor uw logische app
1. Selecteer in het venster **Logic app Designer** onder **beginnen met een algemene trigger de** optie **terugkeer patroon**.

    ![Venster Logic app Designer](./media/automate-with-logic-apps/2logicappdesigner.png)

1. Typ in het vak  **interval** **1** en vervolgens,**frequentie** vak, selecteer **dag**.

    ![Venster ' terugkeer patroon ' van Logic app Designer](./media/automate-with-logic-apps/3recurrence.png)

### <a name="step-3-add-an-application-insights-action"></a>Stap 3: een Application Insights actie toevoegen
1. Klik op **nieuwe stap**.

1. Typ **Azure-toepassing Insights** in het zoekvak **Kies een actie** .

1. Klik onder **acties** op **Azure-toepassing Insights-visualiseren Analytics-query**.

    ![Logic app Designer-venster voor het kiezen van een actie](./media/automate-with-logic-apps/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Stap 4: verbinding maken met een Application Insights-resource

U hebt een toepassings-ID en een API-sleutel voor uw resource nodig om deze stap te volt ooien. U kunt ze ophalen uit de Azure Portal, zoals wordt weer gegeven in het volgende diagram:

![Scherm afbeelding toont de pagina API-toegang in de Azure Portal met de knop API-sleutel maken geselecteerd.](./media/automate-with-logic-apps/5apiaccess.png)

![Toepassings-ID in de Azure Portal](./media/automate-with-logic-apps/6apikey.png)

Geef een naam op voor de verbinding, de toepassings-ID en de API-sleutel.

![Verbindings venster voor Logic app Designer-stroom](./media/automate-with-logic-apps/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Stap 5: de analyse query en het grafiek type opgeven
In het volgende voor beeld selecteert de query de mislukte aanvragen in de afgelopen dag en correleert deze met uitzonde ringen die zijn opgetreden als onderdeel van de bewerking. Analytics verbindt de mislukte aanvragen op basis van de operation_Id-id. De query segmenteert vervolgens de resultaten met behulp van de algoritme van de autocluster. 

Wanneer u uw eigen query's maakt, controleert u of ze correct werken in Analytics voordat u deze toevoegt aan uw stroom.

1. Voeg in het vak **query** de volgende analyse query toe:

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```

1. In het vak **grafiek type** selecteert u **HTML-tabel**.

    ![Configuratie venster Analyse query](./media/automate-with-logic-apps/8query.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>Stap 6: de logische app configureren voor het verzenden van e-mail

1. Klik op **nieuwe stap**.

1. Typ in het zoekvak **Office 365 Outlook**.

1. Klik op **Office 365 Outlook-een E-mail verzenden**.

    ![Office 365 Outlook-selectie](./media/automate-with-logic-apps/9sendemail.png)

1. Ga in het venster **een E-mail verzenden** als volgt te werk:

   a. Typ het e-mail adres van de ontvanger.

   b. Typ een onderwerp voor het e-mail bericht.

   c. Klik op een wille keurige plaats in het vak **hoofd tekst** en selecteer in het menu met dynamische inhoud dat aan de rechter kant wordt geopend, de optie **hoofd tekst**.
    
   d. Klik op de vervolg keuzelijst **nieuwe para meter toevoegen** en selecteer bijlagen en HTML.

      ![Scherm afbeelding toont het venster een e-mail verzenden met het vak tekst gemarkeerd en het menu met dynamische inhoud met de tekst aan de rechter kant gemarkeerd.](./media/automate-with-logic-apps/10emailbody.png)

      ![Office 365 Outlook-configuratie](./media/automate-with-logic-apps/11emailparameter.png)

1. Ga als volgt te werk in het menu dynamische inhoud:

    a. Selecteer de naam van de **bijlage**.

    b. Selecteer de inhoud van de **bijlage**.
    
    c. Selecteer in het vak **is HTML** de optie **Ja**.

      ![Configuratie scherm voor Office 365-e-mail](./media/automate-with-logic-apps/12emailattachment.png)

### <a name="step-7-save-and-test-your-logic-app"></a>Stap 7: uw logische app opslaan en testen
* Klik op **Opslaan** om uw wijzigingen op te slaan.

U kunt wachten tot de trigger de logische app uitvoert, of u kunt de logische app direct uitvoeren door **uitvoeren** te selecteren.

![Het scherm voor het maken van logische apps](./media/automate-with-logic-apps/13save.png)

Wanneer de logische app wordt uitgevoerd, krijgen de ontvangers die u in de e-mail lijst hebt opgegeven een e-mail bericht dat er als volgt uitziet:

![E-mail bericht van logische app](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het maken van [Analytics-query's](../logs/get-started-queries.md).
- Meer informatie over [Logic Apps](../../logic-apps/logic-apps-overview.md).



<!--Link references-->


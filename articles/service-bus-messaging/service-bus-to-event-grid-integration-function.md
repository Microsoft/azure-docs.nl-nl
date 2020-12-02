---
title: Service Bus-gebeurtenissen verwerken via Event Grid met behulp van Azure Functions
description: In dit artikel worden de stappen beschreven voor het verwerken van Service Bus-gebeurtenissen via Event Grid met behulp van Azure Functions.
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: afc0a5bf9b83363d1f4baab955b55148fe3a8498
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95818448"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions"></a>Zelfstudie: Reageren op Azure Service Bus-gebeurtenissen die via Azure Event Grid worden ontvangen met behulp van Azure Functions
In deze zelfstudie leert u hoe u kunt reageren op Azure Service Bus-gebeurtenissen die via Azure Event Grid worden ontvangen met behulp van Azure Functions en Azure Logic Apps. 

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een Service Bus-naamruimte maken
> * Een voorbeeldtoepassing voorbereiden om berichten te verzenden
> * Berichten kunt verzenden naar Service Bus-onderwerp
> * Berichten ontvangen met behulp van Logic Apps
> * Een test kunt instellen op Azure
> * Functie en naamruimte met elkaar verbinden via Event Grid
> * Berichten ontvangen met behulp van Azure Functions

[!INCLUDE [service-bus-event-grid-prerequisites](../../includes/service-bus-event-grid-prerequisites.md)]

## <a name="additional-prerequisites"></a>Aanvullende vereisten
Installeer [Visual Studio 2019](https://www.visualstudio.com/vs) en voeg de **Azure ontwikkelworkload** toe. Deze workload bevat **Azure Function-hulpprogramma’s** die u nodig hebt voor het maken, bouwen en implementeren van Azure Functions-projecten in Visual Studio. 

## <a name="deploy-the-function-app"></a>De functie-app implementeren 

>[!NOTE]
> Zie [Ontwikkelen van Azure Functions met Visual Studio](../azure-functions/functions-develop-vs.md) voor meer informatie over het maken en implementeren van een Azure Functions-app

1. Open het bestand **ReceiveMessagesOnEvent.cs** in het project **FunctionApp1** van de oplossing **SBEventGridIntegration.sln**. 
1. Vervang `<SERCICE BUS NAMESPACE - CONNECTION STRING>` door de verbindingstekenreeks voor uw Service Bus-naamruimte. Dit moet hetzelfde zijn als wat u hebt gebruikt in het bestand **Program.cs** van het project **MessageSender** in dezelfde oplossing. 
1. Klik met de rechtermuisknop op **FunctionApp1** en selecteer **Publiceren**. 
1. Selecteer **Start** op de pagina **Publiceren**. Deze stappen kunnen afwijken van wat u ziet, maar het publicatieproces moet vergelijkbaar zijn. 
1. Selecteer in de wizard **Publiceren** op de pagina **Doel** **Azure** als het **Doel**. 
1. Selecteer op de pagina **Specifiek doel** **Azure Function-app (Windows)** . 
1. Selecteer op de pagina **Functions-exemplaar** **Een nieuwe Azure-functie maken**. 
1. Voer op de pagina **Function-app (Windows)** de volgende stappen uit:
    1. Voer een **naam** in voor de functie-app.
    1. Selecteer een Azure-**abonnement**.
    1. Selecteer een bestaande **resourcegroep** of maak een nieuwe. Voor deze zelfstudie selecteert u de resourcegroep die de Service Bus-naamruimte heeft. 
    1. Selecteer een **plantype** voor App Service.
    1. Selecteer een **locatie**. Selecteer dezelfde locatie als voor de Service Bus-naamruimte. 
    1. Selecteer een bestaande **Azure Storage** of selecteer **Nieuw** om een nieuwe opslagaccount te maken die moet worden gebruikt door de Functions-app. 
    1. Selecteer **Maken** om de Functions-app te maken. 
1. Selecteer op de pagina **Functions-exemplaar** van de wizard  **Publiceren** de optie **Voltooien**. 
1. Selecteer op de pagina **Publiceren** in Visual Studio **Publiceren** om de Functions-app op Azure te publiceren. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/publish-function-app.png" alt-text="Functions-app publiceren":::    
1. Bekijk in het venster **Uitvoer** de berichten van het maken en publiceren en bevestig dat beide zijn geslaagd. 
1. Selecteer nu **Beheren in Azure Portal** op de pagina **Publiceren**. 
1. Selecteer in de Azure Portal, op de pagina **Function-app** **Functions** in het linkermenu en bevestig dat u twee functies ziet: 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/functions.png" alt-text="Twee functies voor handler Event Grid-trigger en HTTP-trigger":::

    > [!NOTE]
    > De `EventGridTriggerFunction` maakt gebruik van een [Event Grid-trigger](../azure-functions/functions-bindings-event-grid-trigger.md) en de `HTTPTriggerFunction` maakt gebruik van een [HTTP-trigger](../azure-functions/functions-bindings-http-webhook-trigger.md).
1. Selecteer **EventGridTriggerFunction** in de lijst. U wordt aangeraden de Event Grid-trigger te gebruiken met Azure Functions omdat deze een aantal voordelen heeft ten opzichte van de HTTP-trigger. Zie [Azure-functie als gebeurtenis-handler voor Event Grid-gebeurtenissen](../event-grid/handler-functions.md) voor meer informatie.
1. Selecteer **Monitor** in het linkermenu op de pagina **Functie** voor de **EventGridTriggerFunction**. 
1. Selecteer **Configureren** om Application Insights te configureren voor het vastleggen van het aanroeplogboek. U gebruikt deze pagina om de uitvoering van functies te controleren bij het ontvangen van Service Bus-gebeurtenissen van Event Grid. 
1. Voer op de pagina **Application Insights** een naam in voor de resource, selecteer een **locatie** voor de resource en selecteer vervolgens **OK**. 
1. Selecteer de functie **EventGridTriggerFunction** bovenaan (navigatiemenu) om terug te gaan naar de pagina **Function**. 
1. Bevestig dat u op de pagina **Monitor** bent. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/monitor-before.png" alt-text="Monitorpagina voor de functie vóór het aanroepen van functies":::
    
    Laat deze pagina geopend op een tabblad van uw webbrowser. U gaat deze pagina later vernieuwen om de aanroepen voor deze functie te zien.

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Functie en naamruimte met elkaar verbinden via Event Grid
In deze sectie koppelt u de functie en de Service Bus-naamruimte met behulp van de Azure Portal. 

Volg de volgende stappen als u een Azure Event Grid-abonnement wilt maken:

1. Ga in Azure Portal naar uw naamruimte en selecteer **Gebeurtenissen** in het linkerdeelvenster. Het venster met de naamruimte wordt geopend, waarbij twee Event Grid-abonnementen in het rechterdeelvenster worden weergegeven. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png" alt-text="Service Bus - pagina gebeurtenissen":::
2. Selecteer **+ Gebeurtenisabonnement** op de werkbalk. 
3. Voer op de pagina **Gebeurtenisabonnement maken** de volgende stappen uit:
    1. Voer een **naam** in voor het abonnement. 
    2. Voer een **naam** in voor het **systeemonderwerp**. Systeemonderwerpen zijn onderwerpen die zijn gemaakt voor Azure-resources, zoals Azure Storage-account en Azure Service Bus. Zie [Overzicht van systeemonderwerpen](../event-grid/system-topics.md) voor meer informatie over systeemonderwerpen.
    2. Selecteer **Azure Function** als **Eindpunttype** en klik op **Een eindpunt selecteren**. 

        ![Service Bus - Event Grid-abonnement](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Selecteer op de pagina **Azure Function selecteren** het abonnement, de resourcegroep, de functie-app, de sleuf en de functie en selecteer vervolgens **Selectie bevestigen**. 

        ![Functie: het eindpunt selecteren](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Ga op de pagina **Gebeurtenisabonnement maken** naar het tabblad **Filters** en voer de volgende taken uit:
        1. Selecteer **Filteren van onderwerpen inschakelen**.
        2. Voer de naam in van het abonnement bij het Service Bus-onderwerp (**S1**) dat u eerder hebt gemaakt.
        3. Selecteer de knop **Create** (Maken). 

            ![Filter Gebeurtenisabonnement](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Ga naar het tabblad **Gebeurtenisabonnementen** van de pagina **Gebeurtenissen** en bevestig dat u het gebeurtenisabonnement in de lijst ziet.

    ![Gebeurtenisabonnement in de lijst](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="monitor-the-functions-app"></a>De Functions-app controleren
De berichten die u eerder naar het Service Bus-onderwerp hebt verzonden, worden doorgestuurd naar het abonnement (S1). Event Grid stuurt de berichten bij het abonnement door naar de Azure-functie. In deze stap van de zelfstudie bevestigt u dat de functie is aangeroepen en bekijkt u de vastgelegde informatieberichten. 

1. Schakel op de pagina voor uw Azure-functie-app over naar het tabblad **Monitor** als dit nog niet actief is. U zou een vermelding moeten zien voor elk bericht dat is gepost in het Microsoft Azure Service Bus-onderwerp. Als u deze niet ziet, vernieuwt u de pagina nadat u een paar minuten hebt gewacht. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-monitor.png" alt-text="Monitorpagina voor de functie na de aanroepen":::
2. Selecteer de aanroep in de lijst om de details weer te geven. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/invocation-details.png" alt-text="Aanroepdetails van de functie" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::

    U kunt ook het tabblad **Logboeken** op de pagina **Monitor** gebruiken om de logboekgegevens te bekijken wanneer de berichten worden verzonden. Er kan wat vertraging optreden, dus wacht een paar minuten om de gelogde berichten te kunnen zien. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-logs.png" alt-text="Functielogboeken" lightbox="./media/service-bus-to-event-grid-integration-example/function-logs.png":::

## <a name="troubleshoot"></a>Problemen oplossen
Als u geen functie-aanroepen ziet na enige tijd wachten en vernieuwen, voert u de volgende stappen uit: 

1. Controleer of de berichten het Service Bus-onderwerp hebben bereikt. Zie de **inkomende berichten** teller op de pagina **Service Bus-onderwerp**. In dit geval heb ik de **MessageSender** -toepassing twee keer uitgevoerd, dus ik zie 10 berichten (5 berichten voor elke uitvoering).

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/topic-incoming-messages.png" alt-text="Pagina met Service Bus-onderwerp - inkomende berichten":::    
1. Controleer of er **geen actieve berichten** bij het Service Bus-abonnement staan. 
    Als u geen gebeurtenissen op deze pagina ziet, controleert u of op de pagina **Service Bus-abonnement** geen **actieve berichtentelling** weergeeft. Als het aantal voor deze teller groter is dan nul, worden de berichten bij het abonnement om de een of andere reden niet doorgestuurd naar de handler-functie (handler voor gebeurtenisabonnement). Controleer of u het gebeurtenisabonnement op de juiste manier hebt ingesteld. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/subscription-active-message-count.png" alt-text="Actieve berichtentelling bij het Service Bus-abonnement":::    
1. U ziet ook **geleverde gebeurtenissen** op de pagina **Gebeurtenissen** van de Service Bus-naamruimte. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-page.png" alt-text="Gebeurtenissenpagina - afgeleverde gebeurtenissen" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::
1. U kunt ook zien dat de gebeurtenissen worden afgeleverd op de pagina **Gebeurtenisabonnement**. U kunt deze pagina openen door het gebeurtenisabonnement te selecteren op de pagina **Gebeurtenissen**. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-delivered-events.png" alt-text="Pagina gebeurtenisabonnement - afgeleverde gebeurtenissen":::
    


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure Event Grid](../event-grid/index.yml).
* Meer informatie over [Azure Functions](../azure-functions/index.yml).
* Meer informatie over de [Logic Apps-functie van Azure App Service](../logic-apps/index.yml).
* Meer informatie over [Azure Service Bus](/azure/service-bus/).


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
---
title: Service Bus-gebeurtenissen verwerken via Event Grid met behulp van Azure Logic Apps
description: In dit artikel worden de stappen beschreven voor het verwerken van Service Bus-gebeurtenissen via Event Grid met behulp van Azure Logic Apps.
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 10/16/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: 93375f6047fbe4eda2132e024dab0e067e83ccf1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95998987"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-logic-apps"></a>Zelfstudie: Reageren op Azure Service Bus-gebeurtenissen die via Azure Event Grid worden ontvangen met behulp van Azure Logic Apps
In deze zelfstudie leert u hoe u kunt reageren op Azure Service Bus-gebeurtenissen die via Azure Event Grid worden ontvangen met behulp van Azure Logic Apps. 

[!INCLUDE [service-bus-event-grid-prerequisites](../../includes/service-bus-event-grid-prerequisites.md)]

## <a name="receive-messages-by-using-logic-apps"></a>Berichten ontvangen met behulp van Logic Apps
In deze stap maakt u een Azure Logic-app die Service Bus-gebeurtenissen ontvangt via Azure Event Grid. 

1. Maak een logische app in de Azure Portal.
    1. Selecteer **+ Een resource maken**, selecteer **Integratie** en selecteer **Logische app**. 
    2. Voer op de pagina **Logische app - Maken** een **naam** in voor de logische app.
    3. Selecteer uw Azure-**abonnement**. 
    4. Selecteer **Bestaande gebruiken** voor de **Resourcegroep** en selecteer de resourcegroep die u hebt gebruikt voor andere resources (zoals Azure-functie, Service Bus-naamruimte) die u eerder hebt gemaakt. 
    5. Selecteer de **Locatie** voor de logische app. 
    6. Selecteer **Controleren + maken**. 
    1. Selecteer **Maken** op de pagina **Beoordelen en maken** op de logische app te maken. 
1. Op de pagina **Ontwerper van logische apps** selecteert u **Lege logische app** onder **Sjablonen**. 
1. Voer in de ontwerper de volgende stappen uit:
    1. Zoek naar **Event Grid**. 
    2. Selecteer **Wanneer een resourcegebeurtenis optreedt - Azure Event Grid**. 

        ![Ontwerper van logische apps: Event Grid-trigger selecteren](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Selecteer **Aanmelden**, voer uw Azure-referenties in en selecteer **Toegang toestaan**. 
5. Voer de volgende stappen uit op de pagina **Wanneer een resourcegebeurtenis zich voordoet**:
    1. Selecteer uw Azure-abonnement. 
    2. Voor **Resourcetype** selecteert u **Microsoft.ServiceBus.Namespaces**. 
    3. Voor **Resourcenaam** selecteert u uw Service Bus-naamruimte. 
    4. Selecteer **Nieuwe parameter toevoegen** en selecteer **Achtervoegselfilter**. 
    5. Voer voor **Achtervoegselfilter** de naam in van het tweede abonnement voor Service Bus-onderwerp. 
        ![Ontwerper van logische apps: gebeurtenis configureren](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. Selecteer **+ Nieuwe stap** in de ontwerper en voer de volgende stappen uit:
    1. Zoek naar **Service Bus**.
    2. Selecteer **Service Bus** in de lijst. 
    3. Selecteer voor **Berichten ophalen** in de lijst **Acties**. 
    4. Selecteer **Berichten van een onderwerpabonnement ontvangen (kort weergeven)** . 

        ![Ontwerper van logische apps: de actie berichten ophalen](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Geef een **naam voor de verbinding op**. Bijvoorbeeld: **Ontvang berichten van het onderwerp-abonnement** en selecteer de Service Bus-naamruimte. 

        ![Ontwerper van logische apps: de Service Bus-naamruimte selecteren](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Selecteer **RootManageSharedAccessKey** en selecteer vervolgens **Maken**.

        ![Ontwerper van logische apps: de gedeelde toegangssleutel selecteren](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    8. Selecteer uw **onderwerp** en **abonnement**. 
    
        ![Schermopname die laat zien waar u uw onderwerp en abonnement selecteert.](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Selecteer **+ Nieuwe stap** en voer de volgende stappen uit: 
    1. Selecteer **Service Bus**.
    2. Selecteer **Het bericht in een onderwerp met een abonnement voltooien** in de lijst met acties. 
    3. Selecteer uw Service Bus-**onderwerp**.
    4. Selecteer de tweede **abonnement** op het onderwerp.
    5. Selecteer voor **Vergrendelingstoken van het bericht** de optie **Vergrendelingstoken** van de **Dynamische inhoud**. 

        ![Logic Apps Designer: het bericht voltooien](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Selecteer **Opslaan** op de werkbalk van de Ontwerper voor logische apps om de logische app op te slaan. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/save-logic-app.png" alt-text="Logische app opslaan":::
1. Als u nog geen testberichten naar het onderwerp hebt verzonden, volg dan de instructies in de sectie [Berichten verzenden naar het Service Bus-onderwerp](#send-messages-to-the-service-bus-topic) om berichten naar het onderwerp te verzenden. 
1. Ga naar de pagina **Overzicht** van uw logische app. U ziet de uitvoeringen van de logische app in de **Geschiedenis van uitvoeringen** voor de verzonden berichten. Het kan enkele minuten duren voordat u de uitvoeringen van de logische app ziet. Selecteer **Vernieuwen** op de werkbalk om de pagina te vernieuwen. 

    ![Ontwerper van logische apps - uitvoeringen van logische app](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)
1. Selecteer een logische app-uitvoering om de details te zien. U ziet dat er 5 berichten zijn verwerkt in de for-lus. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/logic-app-run-details.png" alt-text="Details van de logische app-uitvoering":::    

## <a name="troubleshoot"></a>Problemen oplossen
Als u geen aanroepen ziet na enige tijd wachten en vernieuwen, voert u de volgende stappen uit: 

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
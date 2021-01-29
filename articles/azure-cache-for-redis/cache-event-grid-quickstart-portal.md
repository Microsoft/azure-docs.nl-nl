---
title: 'Quick Start: Azure cache naar redis-gebeurtenissen naar een webeindpunt door sturen met de Azure Portal'
description: Gebruik Azure Event Grid om u te abonneren op Azure cache voor redis-gebeurtenissen, de gebeurtenissen te verzenden naar een webhook en de gebeurtenissen in een webtoepassing te verwerken
ms.date: 1/5/2021
ms.topic: quickstart
ms.service: cache
author: curib
ms.author: cauribeg
ms.openlocfilehash: 5bdd6b0e6f97f7e5a738ab17d68282cf402004b0
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055551"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-the-azure-portal"></a>Quick Start: Azure cache naar redis-gebeurtenissen naar een webeindpunt door sturen met de Azure Portal

Azure Event Grid is een gebeurtenisservice voor de cloud. In deze Quick Start gebruikt u de Azure Portal voor het maken van een Azure-cache voor redis-exemplaar, het abonneren op gebeurtenissen voor dat exemplaar, het activeren van een gebeurtenis en het weer geven van de resultaten. Normaal gesproken verzendt u gebeurtenissen naar een eindpunt dat de gebeurtenisgegevens verwerkt en vervolgens in actie komt. Als u deze Snelstartgids echter wilt vereenvoudigen, verzendt u gebeurtenissen naar een web-app waarmee de berichten worden verzameld en weer gegeven. 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

Wanneer u klaar bent, ziet u dat de gebeurtenis gegevens naar de web-app zijn verzonden.

:::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Schaal van Azure Event Grid-viewer in JSON-indeling.":::

## <a name="create-an-azure-cache-for-redis-cache-instance"></a>Een Azure-cache maken voor een redis-cache-exemplaar 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="create-a-message-endpoint"></a>Het eindpunt van een bericht maken

Voordat u zich abonneert op de gebeurtenissen voor het cache-exemplaar, gaan we het eind punt voor het gebeurtenis bericht maken. Het eindpunt onderneemt normaal gesproken actie op basis van de gebeurtenisgegevens. Als u deze Snelstartgids wilt vereenvoudigen, implementeert u een [vooraf gemaakte web-app](https://github.com/Azure-Samples/azure-event-grid-viewer) waarin de gebeurtenis berichten worden weer gegeven. De geïmplementeerde oplossing omvat een App Service-plan, een App Service-web-app en broncode van GitHub.

1. Selecteer **implementeren naar Azure** in github Leesmij voor het implementeren van de oplossing voor uw abonnement. 

:::image type="content" source="media/cache-event-grid-portal/deploy-to-azure.png" alt-text="Knop implementeren naar Azure.":::

2. Voer op de pagina **Aangepaste implementatie** de volgende stappen uit: 
    1. Voor **resource groep** selecteert u de resource groep die u hebt gemaakt bij het maken van het cache-exemplaar. Het is voor u eenvoudiger om op te schonen door de resourcegroep te verwijderen wanneer u klaar bent met de zelfstudie.  
    2. Voer in het vak **Sitenaam** een naam in voor de web-app.
    3. Voer voor **Naam van hostingplan** een naam in voor het App Service-plan dat u wilt gebruiken voor het hosten van de web-app.
    4. Schakel het selectievakje in voor **Ik ga akkoord met de bovenstaande voorwaarden**. 
    5. Selecteer **Aankoop**. 
    
    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Abonnement** | Open de vervolgkeuzelijst en selecteer uw abonnement. | Het abonnement waarmee deze web-app wordt gemaakt. | 
    | **Resourcegroep** | Open de vervolgkeuzelijst en selecteer een resourcegroep of kies **Nieuwe maken** en geef een naam voor de nieuwe resourcegroep op. | Door al uw app-resources in één resourcegroep te plaatsen, kunt u ze eenvoudig beheren of verwijderen. | 
    | **Sitenaam** | Voer een naam in voor uw web-app. | Deze waarde mag niet leeg zijn. | 
    | **Naam van hosting plan** | Voer een naam in voor het App Service plan dat moet worden gebruikt voor het hosten van de web-app. | Deze waarde mag niet leeg zijn. | 

1. Selecteer Waarschuwingen (belpictogram) in de portal en selecteer vervolgens **Naar de resourcegroep gaan**. 

    :::image type="content" source="media/cache-event-grid-portal/deployment-notification.png" alt-text="Melding over Azure Portal-implementatie.":::

4. Selecteer in de lijst met resources op de pagina **Resourcegroep** de web-app die u hebt gemaakt. U ziet ook het App Service plan en het cache-exemplaar in deze lijst. 

5. Selecteer op de pagina **App Service** voor uw web-app de URL om naar de website te gaan. De URL moet de volgende indeling hebben: `https://<your-site-name>.azurewebsites.net`.

6. Controleer of de site wordt weergegeven en dat er zijn nog geen gebeurtenissen naar zijn gepost.

    :::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Lege Event Grid Viewer-site.":::

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-azure-cache-for-redis-instance"></a>Abonneren op de Azure-cache voor het redis-exemplaar

In deze stap maakt u een abonnement op een onderwerp om te zien Event Grid welke gebeurtenissen u wilt bijhouden en waar u de gebeurtenissen wilt verzenden.

1. Navigeer in de portal naar uw cache-exemplaar dat u eerder hebt gemaakt. 
2. Selecteer op de pagina **Azure-cache voor redis** **gebeurtenissen** in het menu links. 
3. Selecteer een **webhook**. U verzendt gebeurtenissen naar uw viewer-app met behulp van een webhook voor het eindpunt. 

     :::image type="content" source="media/cache-event-grid-portal/event-grid-web-hook.png" alt-text="Pagina Azure Portal gebeurtenissen.":::

4. Voer op de pagina **gebeurtenis abonnement maken** het volgende in: 

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Naam** | Voer een naam in voor het gebeurtenisabonnement. | De waarde moet tussen de 3 en 64 tekens lang zijn. De naam mag alleen letters, cijfers en streepjes bevatten. | 
    | **Gebeurtenis typen** | Vervolg keuzelijst en selecteer de gebeurtenis typen die u naar uw bestemming wilt pushen. Voor deze Snelstartgids gaan we de cache-instantie schalen. | Patches, schalen, importeren en exporteren zijn de beschik bare opties. | 
    | **Type eind punt** | Selecteer een **webhook**. | Gebeurtenis-handler om uw gebeurtenissen te ontvangen. | 
    | **Eindpunt** | Klik op **Selecteer een eind punt** en voer de URL van uw web-app in en voeg deze toe `api/updates` aan de URL van de start pagina (bijvoorbeeld: `https://cache.azurewebsites.net/api/updates` ) en selecteer vervolgens **selectie bevestigen**. | Dit is de URL van uw web-app die u eerder hebt gemaakt. | 

5. Selecteer nu op de pagina **Gebeurtenisabonnement maken** de optie **Maken** om het gebeurtenisabonnement te maken. 

6. Bekijk opnieuw uw web-app en u zult zien dat er een validatiegebeurtenis voor een abonnement naartoe is verzonden. Selecteer het oogpictogram om de gebeurtenisgegevens uit te breiden. Via Event Grid wordt de validatiegebeurtenis verzonden zodat het eindpunt kan controleren of de gebeurtenisgegevens in aanmerking komen om ontvangen te worden. De web-app bevat code waarmee het abonnement kan worden gevalideerd.

    :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure Event Grid viewer.":::

## <a name="send-an-event-to-your-endpoint"></a>Een gebeurtenis verzenden naar het eindpunt

Nu gaan we een gebeurtenis activeren om te zien hoe het bericht via Event Grid naar het eindpunt wordt gedistribueerd. Uw Azure-cache wordt geschaald voor een redis-exemplaar.

1. In de Azure Portal gaat u naar uw Azure-cache voor redis-exemplaar en selecteert u **schalen** in het menu links.

1. Selecteer de gewenste prijs categorie op de pagina **schalen** en klik op **selecteren**. 

    U kunt schalen naar een andere prijs categorie met de volgende beperkingen:
    
    * U kunt niet schalen van een hogere prijs categorie naar een lagere prijs categorie.
      * U kunt de schaal van een **Premium** -cache niet verlagen naar een **Standard** -of een **Basic** -cache.
      * U kunt niet omlaag schalen vanuit een **standaard** cache naar een **Basic** -cache.
    * U kunt schalen van een **basis** cache naar een **standaard** cache, maar u kunt de grootte niet tegelijkertijd wijzigen. Als u een andere grootte nodig hebt, kunt u een volgende schaal bewerking uitvoeren voor de gewenste grootte.
    * U kunt niet rechtstreeks schalen van een **Basic** -cache naar een **Premium** -cache. Schaal eerst van **Basic** naar **Standard** in één schaal bewerking en vervolgens van **standaard** naar **Premium** bij een volgende schaal bewerking.
    * U kunt niet van een grotere grootte schalen naar de grootte van de **C0 (250 MB)** .
 
    Terwijl de cache wordt geschaald naar de nieuwe prijs categorie, wordt een **schaal** status weer gegeven in de Blade **Azure-cache voor redis** . Wanneer het schalen is voltooid, verandert de status van **schalen** in **wordt uitgevoerd**.

1. U hebt de gebeurtenis geactiveerd en Event Grid heeft het bericht verzonden naar het eindpunt dat u hebt geconfigureerd toen u zich abonneerde. Het bericht heeft de JSON-indeling en bevat een matrix met een of meer gebeurtenissen. In het volgende voorbeeld bevat het JSON-bericht een matrix met één gebeurtenis. Bekijk uw web-app en u ziet dat er een **ScalingCompleted** -gebeurtenis is ontvangen. 

    :::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Schaal van Azure Event Grid-viewer in JSON-indeling.":::

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om met deze gebeurtenis te blijven werken, moet u de resources die u in deze Quick Start hebt gemaakt, niet opschonen. Als dat niet het geval is, verwijdert u de resources die u in deze Quick Start hebt gemaakt.

Selecteer de resourcegroep en klik op **Resourcegroep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

U weet nu hoe u aangepaste onderwerpen maakt en hoe u zich abonneert op een gebeurtenis. Kijk waar Event Grid u nog meer bij kan helpen:

- [Reageren op Azure cache voor redis-gebeurtenissen](cache-event-grid.md)
- [Over Event Grid](../event-grid/overview.md)


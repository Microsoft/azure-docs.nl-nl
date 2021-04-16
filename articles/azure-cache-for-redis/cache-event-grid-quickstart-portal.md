---
title: 'Quickstart: Gebeurtenissen Azure Cache voor Redis naar het web-eindpunt met de Azure Portal'
description: Gebruik Azure Event Grid u te abonneren op Azure Cache voor Redis gebeurtenissen, de gebeurtenissen naar een webhook te verzenden en de gebeurtenissen in een webtoepassing af te handelen
author: curib
ms.author: cauribeg
ms.date: 1/5/2021
ms.topic: quickstart
ms.service: cache
ms.custom:
- mode-portal
ms.openlocfilehash: e021f386f255f1cef61e28cbd4fd6116fc2aa727
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529313"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-the-azure-portal"></a>Quickstart: Gebeurtenissen Azure Cache voor Redis naar het web-eindpunt met de Azure Portal

Azure Event Grid is een gebeurtenisservice voor de cloud. In deze quickstart gebruikt u de Azure Portal om een Azure Cache voor Redis-exemplaar te maken, u te abonneren op gebeurtenissen voor dat exemplaar, een gebeurtenis te activeren en de resultaten weer te geven. Normaal gesproken verzendt u gebeurtenissen naar een eindpunt dat de gebeurtenisgegevens verwerkt en vervolgens in actie komt. Ter vereenvoudiging van deze quickstart verzendt u echter gebeurtenissen naar een web-app waarmee de berichten worden verzameld en weergegeven. 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

Wanneer u klaar bent, ziet u dat de gebeurtenisgegevens naar de web-app zijn verzonden.

:::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Azure Event Grid Viewer schalen in JSON-indeling.":::

## <a name="create-an-azure-cache-for-redis-cache-instance"></a>Een cache-Azure Cache voor Redis maken 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="create-a-message-endpoint"></a>Het eindpunt van een bericht maken

Voordat u zich abonneert op de gebeurtenissen voor het cache-exemplaar, maken we het eindpunt voor het gebeurtenisbericht. Het eindpunt onderneemt normaal gesproken actie op basis van de gebeurtenisgegevens. Ter vereenvoudiging van deze quickstart implementeert u een vooraf [gebouwde web-app](https://github.com/Azure-Samples/azure-event-grid-viewer) waarmee de gebeurtenisberichten worden weergegeven. De geïmplementeerde oplossing omvat een App Service-plan, een App Service-web-app en broncode van GitHub.

1. Selecteer **Implementeren in Azure** in GitHub README om de oplossing in uw abonnement te implementeren. 

:::image type="content" source="media/cache-event-grid-portal/deploy-to-azure.png" alt-text="Knop Implementeren in Azure.":::

2. Voer op de pagina **Aangepaste implementatie** de volgende stappen uit: 
    1. Selecteer **bij Resourcegroep** de resourcegroep die u hebt gemaakt bij het maken van het cache-exemplaar. Het is voor u eenvoudiger om op te schonen door de resourcegroep te verwijderen wanneer u klaar bent met de zelfstudie.  
    2. Voer in het vak **Sitenaam** een naam in voor de web-app.
    3. Voer voor **Naam van hostingplan** een naam in voor het App Service-plan dat u wilt gebruiken voor het hosten van de web-app.
    4. Schakel het selectievakje in voor **Ik ga akkoord met de bovenstaande voorwaarden**. 
    5. Selecteer **Aankoop**. 
    
    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Abonnement** | Open de vervolgkeuzelijst en selecteer uw abonnement. | Het abonnement waarmee u deze web-app maakt. | 
    | **Resourcegroep** | Open de vervolgkeuzelijst en selecteer een resourcegroep of kies **Nieuwe maken** en geef een naam voor de nieuwe resourcegroep op. | Door al uw app-resources in één resourcegroep te plaatsen, kunt u ze eenvoudig beheren of verwijderen. | 
    | **Sitenaam** | Voer een naam in voor uw web-app. | Deze waarde mag niet leeg zijn. | 
    | **Naam hostingplan** | Voer een naam in voor de App Service wilt gebruiken voor het hosten van de web-app. | Deze waarde mag niet leeg zijn. | 

1. Selecteer Waarschuwingen (belpictogram) in de portal en selecteer vervolgens **Naar de resourcegroep gaan**. 

    :::image type="content" source="media/cache-event-grid-portal/deployment-notification.png" alt-text="Azure Portal implementatiemelding.":::

4. Selecteer in de lijst met resources op de pagina **Resourcegroep** de web-app die u hebt gemaakt. U ziet ook het App Service en het cache-exemplaar in deze lijst. 

5. Selecteer op de pagina **App Service** voor uw web-app de URL om naar de website te gaan. De URL moet de volgende indeling hebben: `https://<your-site-name>.azurewebsites.net`.

6. Controleer of de site wordt weergegeven en dat er zijn nog geen gebeurtenissen naar zijn gepost.

    :::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Lege Event Grid Viewer-site.":::

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-azure-cache-for-redis-instance"></a>Abonneren op het Azure Cache voor Redis exemplaar

In deze stap abonneert u zich op een onderwerp om u Event Grid welke gebeurtenissen u wilt bijhouden en waar u de gebeurtenissen wilt verzenden.

1. Navigeer in de portal naar het cache-exemplaar dat u eerder hebt gemaakt. 
2. Selecteer op **Azure Cache voor Redis** pagina Gebeurtenissen **in** het menu links. 
3. Selecteer **Web hook**. U verzendt gebeurtenissen naar uw viewer-app met behulp van een webhook voor het eindpunt. 

     :::image type="content" source="media/cache-event-grid-portal/event-grid-web-hook.png" alt-text="Azure Portal pagina Gebeurtenissen.":::

4. Voer op **de pagina Gebeurtenisabonnement** maken het volgende in: 

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Naam** | Voer een naam in voor het gebeurtenisabonnement. | De waarde moet tussen de 3 en 64 tekens lang zijn. Deze mag alleen letters, cijfers en streepjes bevatten. | 
    | **Gebeurtenistypen** | Selecteer in de vervolgkeuze selecteren welk gebeurtenistype(en) u naar uw bestemming wilt pushen. Voor deze quickstart schalen we onze cache-instantie. | Patchen, schalen, importeren en exporteren zijn de beschikbare opties. | 
    | **Eindpunttype** | Selecteer **Web hook**. | Gebeurtenis-handler om uw gebeurtenissen te ontvangen. | 
    | **Eindpunt** | Klik **op Een eindpunt selecteren,** voer de URL van uw web-app in en voeg toe aan de URL van de startpagina (bijvoorbeeld: ) en selecteer `api/updates` vervolgens Selectie `https://cache.azurewebsites.net/api/updates` **bevestigen.** | Dit is de URL van uw web-app die u eerder hebt gemaakt. | 

5. Selecteer nu op de pagina **Gebeurtenisabonnement maken** de optie **Maken** om het gebeurtenisabonnement te maken. 

6. Bekijk opnieuw uw web-app en u zult zien dat er een validatiegebeurtenis voor een abonnement naartoe is verzonden. Selecteer het oogpictogram om de gebeurtenisgegevens uit te breiden. Via Event Grid wordt de validatiegebeurtenis verzonden zodat het eindpunt kan controleren of de gebeurtenisgegevens in aanmerking komen om ontvangen te worden. De web-app bevat code waarmee het abonnement kan worden gevalideerd.

    :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure Event Grid Viewer.":::

## <a name="send-an-event-to-your-endpoint"></a>Een gebeurtenis verzenden naar het eindpunt

Nu gaan we een gebeurtenis activeren om te zien hoe het bericht via Event Grid naar het eindpunt wordt gedistribueerd. We schalen uw Azure Cache voor Redis exemplaar.

1. Navigeer Azure Portal naar uw Azure Cache voor Redis en selecteer **Schalen** in het menu links.

1. Selecteer de gewenste prijscategorie op de **pagina Schaal** en klik op **Selecteren.** 

    U kunt schalen naar een andere prijscategorie met de volgende beperkingen:
    
    * U kunt niet schalen van een hogere prijscategorie naar een lagere prijscategorie.
      * U kunt niet van een **Premium-cache** omlaag schalen naar een **Standard-** of **Basic-cache.**
      * U kunt niet van een **Standard-cache** omlaag schalen naar een **Basic-cache.**
    * U kunt schalen van **een Basic-cache** naar een **Standard-cache,** maar u kunt de grootte niet tegelijkertijd wijzigen. Als u een andere grootte nodig hebt, kunt u een volgende schaalbewerking naar de gewenste grootte uitvoeren.
    * U kunt een **Basic-cache** niet rechtstreeks naar een **Premium-cache schalen.** Schaal eerst van **Basic** naar **Standard** in één schaalbewerking en vervolgens van **Standard** naar **Premium** in een volgende schaalbewerking.
    * U kunt niet van een grotere grootte naar de **C0-grootte (250 MB) schalen.**
 
    Terwijl de cache wordt geschaald naar de nieuwe prijscategorie, wordt de **status** Schalen weergegeven op Azure Cache voor Redis **blade.** Wanneer het schalen is voltooid, verandert de status van **Schalen in** **Wordt uitgevoerd.**

1. U hebt de gebeurtenis geactiveerd en Event Grid heeft het bericht verzonden naar het eindpunt dat u hebt geconfigureerd toen u zich abonneerde. Het bericht heeft de JSON-indeling en bevat een matrix met een of meer gebeurtenissen. In het volgende voorbeeld bevat het JSON-bericht een matrix met één gebeurtenis. Bekijk uw web-app en u ziet dat er **een gebeurtenis ScalingCompleted** is ontvangen. 

    :::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Azure Event Grid Viewer schalen in JSON-indeling.":::

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om door te gaan met deze gebeurtenis, schoont u de resources die u in deze quickstart hebt gemaakt, niet op. Verwijder anders de resources die u in deze quickstart hebt gemaakt.

Selecteer de resourcegroep en klik op **Resourcegroep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

U weet nu hoe u aangepaste onderwerpen maakt en hoe u zich abonneert op een gebeurtenis. Kijk waar Event Grid u nog meer bij kan helpen:

- [Reageren op Azure Cache voor Redis gebeurtenissen](cache-event-grid.md)
- [Over Event Grid](../event-grid/overview.md)

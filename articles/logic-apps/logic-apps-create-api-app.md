---
title: Web-Api's maken & REST-Api's voor Azure Logic Apps
description: Maak Web-Api's & REST-Api's om uw Api's, services of systemen aan te roepen voor systeem integratie in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/26/2017
ms.openlocfilehash: 3fe98160cc10eb3607b8309a9a263d63380dcfb5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89073213"
---
# <a name="create-custom-apis-you-can-call-from-azure-logic-apps"></a>Aangepaste Api's maken die u kunt aanroepen vanuit Azure Logic Apps

Hoewel Azure Logic Apps [honderden connectors](../connectors/apis-list.md) biedt die u kunt gebruiken in werk stromen voor logische apps, wilt u mogelijk api's, systemen en services aanroepen die niet beschikbaar zijn als connectors. U kunt uw eigen Api's maken die acties en triggers bieden die kunnen worden gebruikt in Logic apps. Hier volgen andere redenen waarom u uw eigen Api's wilt maken die u kunt aanroepen vanuit werk stromen voor logische apps:

* Breid uw huidige systeem integratie-en gegevens integratie werk stromen uit.
* Help klanten uw service te gebruiken voor het beheren van professionele of persoonlijke taken.
* Breid het bereik, de vind baarheid en het gebruik voor uw service uit.

Connectors zijn in principe Web-Api's die REST gebruiken voor pluggable interfaces, [Swagger-meta gegevens indeling](https://swagger.io/specification/) voor documentatie en JSON als gegevensuitwisselings indeling. Omdat connectors zijn REST-Api's die communiceren via HTTP-eind punten, kunt u elke taal, zoals .NET, Java, python of Node.js, gebruiken voor het bouwen van connectors. U kunt ook uw Api's hosten op [Azure app service](../app-service/overview.md), een Paas-Aanbieding (platform-as-a-Service) die een van de beste, eenvoudigste en meest schaal bare manieren biedt voor API-hosting. 

Voor aangepaste Api's voor het werken met Logic apps kan uw API [*acties*](./logic-apps-overview.md#logic-app-concepts) bieden waarmee specifieke taken in werk stromen voor logische apps worden uitgevoerd. Uw API kan ook fungeren als een [*trigger*](./logic-apps-overview.md#logic-app-concepts) waarmee een logische app-werk stroom wordt gestart wanneer nieuwe gegevens of een gebeurtenis aan een opgegeven voor waarde voldoet. In dit onderwerp worden algemene patronen beschreven die u kunt volgen voor het bouwen van acties en triggers in uw API, op basis van het gedrag dat u voor uw API wilt bieden.

U kunt uw Api's hosten op [Azure app service](../app-service/overview.md), een Paas-Aanbieding (platform-as-a-Service) die een uiterst schaal bare en eenvoudige API-hosting biedt.

> [!TIP] 
> Hoewel u uw Api's als web-apps kunt implementeren, kunt u overwegen om uw Api's te implementeren als API-apps, waarmee u uw taak gemakkelijker maakt wanneer u Api's in de Cloud en on-premises bouwt, host en verbruikt. U hoeft geen code in uw Api's te wijzigen. Implementeer gewoon uw code naar een API-app. Meer informatie over het bouwen van API-apps die zijn gemaakt met deze talen: 
> 
> * [ASP.net](../app-service/quickstart-dotnetcore.md). 
> * [Java](../app-service/quickstart-java.md)
> * [Node.js](../app-service/quickstart-nodejs.md)
> * [PHP](../app-service/quickstart-php.md)
> * [Python](../app-service/quickstart-python.md)
> * [Ruby](../app-service/quickstart-ruby.md)
>
> Ga naar de [Azure Logic apps github-opslag plaats](https://github.com/logicappsio)voor API-app-voor beelden die zijn gemaakt voor Logic apps.

## <a name="how-do-custom-apis-differ-from-custom-connectors"></a>Wat zijn de verschillen tussen aangepaste Api's en aangepaste connectors?

Aangepaste Api's en [aangepaste connectors](../logic-apps/custom-connector-overview.md) zijn web-API'S die rest gebruiken voor pluggable interfaces, [Swagger-meta gegevens indeling](https://swagger.io/specification/) voor documentatie en JSON als de Data Exchange-indeling. En omdat deze Api's en connectors REST Api's zijn die communiceren via HTTP-eind punten, kunt u elke taal, zoals .NET, Java, python of Node.js, gebruiken voor het bouwen van aangepaste Api's en connectors.

Met aangepaste Api's kunt u Api's aanroepen die geen connectors zijn en eind punten bieden die u met HTTP + Swagger, Azure API Management of App Services aanroept. Aangepaste connectors werken als aangepaste Api's, maar hebben ook deze kenmerken:

* Geregistreerd als Logic Apps Connector-resources in Azure.
* Worden weer gegeven met pictogrammen naast door micro soft beheerde connectors in de Logic Apps Designer.
* Alleen beschikbaar voor auteurs van connectors en logische app-gebruikers met hetzelfde Azure Active Directory Tenant en Azure-abonnement in de regio waar de Logic apps worden geïmplementeerd.

U kunt ook geregistreerde connectors voor micro soft-certificering benoemen. Dit proces controleert of geregistreerde connectors voldoen aan de criteria voor openbaar gebruik en maakt deze connectors beschikbaar voor gebruikers in automatische stroom en micro soft power apps.

Zie voor meer informatie over aangepaste connectors. 

* [Overzicht van aangepaste connectors](../logic-apps/custom-connector-overview.md)
* [Aangepaste connectors maken van web-Api's](/connectors/custom-connectors/create-web-api-connector)
* [Aangepaste connectors registreren in Azure Logic Apps](/connectors/custom-connectors/)

## <a name="helpful-tools"></a>Handige hulpprogram ma's

Een aangepaste API werkt het beste met Logic apps wanneer de API ook een [Swagger-document](https://swagger.io/specification/) heeft waarin de bewerkingen en para meters van de API worden beschreven.
Met veel bibliotheken, zoals [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle), kan het Swagger-bestand automatisch voor u worden gegenereerd. Als u het Swagger-bestand wilt aantekenen voor weergave namen, typen eigenschappen, enzovoort, kunt u ook [TRex](https://github.com/nihaue/TRex) gebruiken zodat uw Swagger-bestand goed werkt met Logic apps.

<a name="actions"></a>

## <a name="action-patterns"></a>Actie patronen

Voor logische apps om taken uit te voeren, moet uw aangepaste API [*acties*](./logic-apps-overview.md#logic-app-concepts)bieden. Elke bewerking in uw API is gekoppeld aan een actie. Een basis actie is een controller die HTTP-aanvragen accepteert en HTTP-antwoorden retourneert. Een logische app verzendt bijvoorbeeld een HTTP-aanvraag naar uw web-app of API-app. Uw app retourneert vervolgens een HTTP-antwoord, samen met inhoud die de logische app kan verwerken.

Voor een standaard actie kunt u een HTTP-aanvraag methode in uw API schrijven en deze methode in een Swagger-bestand beschrijven. U kunt uw API vervolgens rechtstreeks aanroepen met een [http-actie](../connectors/connectors-native-http.md) of een [http + Swagger-](../connectors/connectors-native-http-swagger.md) actie. Standaard moeten antwoorden worden geretourneerd binnen de [time-outlimiet](./logic-apps-limits-and-config.md)van de aanvraag. 

![Standaard actie patroon](./media/logic-apps-create-api-app/standard-action.png)

<a name="pattern-overview"></a> Om ervoor te zorgen dat een logische app wacht terwijl uw API meer actieve taken voltooit, kunt u de API volgen op het [asynchrone polling-patroon](#async-pattern) of het [asynchrone webhook-patroon](#webhook-actions) dat in dit onderwerp wordt beschreven. Voor een analoge toepassing waarmee u de verschillende gedragingen van deze patronen kunt visualiseren, kunt u het proces voor het best Ellen van een aangepaste taart van een bakker. Het polling-patroon komt overeen met het gedrag waarbij u de bakker elke 20 minuten aanroept om te controleren of de taart klaar is. Het webhook-patroon komt overeen met het gedrag waarbij de bakker u vraagt om uw telefoon nummer, zodat u kunt worden gebeld wanneer de taart klaar is.

Ga naar de [Logic apps github-opslag plaats](https://github.com/logicappsio)voor voor beelden. Meer informatie over [gebruiks metingen voor acties](logic-apps-pricing.md).

<a name="async-pattern"></a>

### <a name="perform-long-running-tasks-with-the-polling-action-pattern"></a>Langlopende taken uitvoeren met het patroon van de polling-actie

Als u wilt dat uw API taken uitvoert die langer dan de [time-outlimiet](./logic-apps-limits-and-config.md)van de aanvraag kunnen worden uitgevoerd, kunt u het asynchrone polling-patroon gebruiken. Met dit patroon werkt uw API in een afzonderlijke thread, maar blijft een actieve verbinding met de Logic Apps-Engine. Op die manier heeft de logische app geen time-out of gaat u door met de volgende stap in de werk stroom voordat de API is voltooid.

Dit is het algemene patroon:

1. Zorg ervoor dat de engine weet dat uw API de aanvraag heeft geaccepteerd en aan het werk is.
2. Wanneer de engine volgende aanvragen voor taak status maakt, weet de engine wanneer de-API de taak voltooit.
3. Retour neer relevante gegevens naar de engine zodat de werk stroom van de logische app kan worden voortgezet.

<a name="bakery-polling-action"></a> Pas nu de vorige bakkers-analoog toe op het polling-patroon en stel u voor dat u een bakker aanroept en een aangepaste taart voor levering bestelt. Het proces voor het maken van de taart vergt tijd en u hoeft niet op de telefoon te wachten terwijl de bakker op de taart werkt. De bakker bevestigt uw bestelling en u belt elke 20 minuten voor de status van de taart. Na 20 minuten roept u de bakker aan, maar ziet u dat uw taart niet is voltooid en dat u in een andere 20 minuten moet bellen. Dit back-upproces wordt voortgezet totdat u bent gebeld en de bakker vertelt u dat uw bestelling klaar is en uw taart levert. 

Laten we dit polling-patroon dus weer toewijzen. De bakker vertegenwoordigt uw aangepaste API, terwijl u, de taart-klant, de Logic Apps Engine vertegenwoordigt. Wanneer de engine uw API aanroept met een aanvraag, bevestigt uw API de aanvraag en reageert deze met het tijds interval wanneer de engine de taak status kan controleren. De engine gaat door met het controleren van de taak status totdat uw API reageert dat de taak is voltooid en gegevens retourneert naar uw logische app, die vervolgens de werk stroom voortzet. 

![Patroon van polling actie](./media/logic-apps-create-api-app/custom-api-async-action-pattern.png)

Hier vindt u de specifieke stappen voor de API die u kunt volgen, zoals beschreven in het perspectief van de API:

1. Wanneer uw API een HTTP-aanvraag voor het starten van het werk krijgt, retourneert dan onmiddellijk een HTTP- `202 ACCEPTED` antwoord met de `location` header die verderop in deze stap wordt beschreven. Met deze reactie kan de Logic Apps Engine weten dat uw API de aanvraag heeft ontvangen, dat de aanvraag lading (gegevens invoer) is geaccepteerd en nu wordt verwerkt. 
   
   Het `202 ACCEPTED` antwoord moet deze headers bevatten:
   
   * *Vereist*: een `location` header waarmee het absolute pad naar een URL wordt opgegeven waar de Logic Apps Engine de taak status van de API kan controleren

   * *Optioneel*: een `retry-after` header waarmee het aantal seconden wordt opgegeven dat de engine moet wachten voordat de `location` URL voor de taak status wordt gecontroleerd. 

     Standaard wordt elke 20 seconden gecontroleerd door de engine. Als u een ander interval wilt opgeven, neemt u de `retry-after` koptekst en het aantal seconden op voor de volgende poll.

2. Nadat de opgegeven tijd is verstreken, wordt de URL door de Logic Apps-Engine gecontroleerd `location` om de taak status te controleren. Uw API moet deze controles uitvoeren en deze antwoorden retour neren:
   
   * Als de taak is voltooid, retourneert u een HTTP- `200 OK` antwoord, samen met de reactie lading (invoer voor de volgende stap).

   * Als de taak nog wordt verwerkt, retour neren we nog een HTTP- `202 ACCEPTED` antwoord, maar met dezelfde headers als het oorspronkelijke antwoord.

Wanneer uw API dit patroon volgt, hoeft u niets te doen in de werk stroom definitie van de logische app om door te gaan met het controleren van de taak status. Wanneer de engine een HTTP- `202 ACCEPTED` antwoord en een geldige `location` header ontvangt, respecteert de engine het asynchrone patroon en `location` wordt de header gecontroleerd totdat uw API een niet-202-antwoord retourneert.

> [!TIP]
> Lees voor een voor beeld van een asynchroon patroon het voor beeld van een [asynchroon controller antwoord in github](https://github.com/logicappsio/LogicAppsAsyncResponseSample).

<a name="webhook-actions"></a>

### <a name="perform-long-running-tasks-with-the-webhook-action-pattern"></a>Langlopende taken uitvoeren met het actie patroon webhook

Als alternatief kunt u het webhook-patroon gebruiken voor langlopende taken en asynchrone verwerking. Dit patroon heeft de logische app-pauze en wacht op het terugbellen van uw API om de verwerking te volt ooien voordat u doorgaat met de werk stroom. Deze retour aanroep is een HTTP POST die een bericht verzendt naar een URL wanneer er een gebeurtenis optreedt. 

<a name="bakery-webhook-action"></a> Pas de vorige bakkers-analoge toepassing nu toe op het webhook-patroon en stel u voor dat u een bakker aanroept en een aangepaste taart voor levering bestelt. Het proces voor het maken van de taart vergt tijd en u hoeft niet op de telefoon te wachten terwijl de bakker op de taart werkt. De bakker bevestigt uw bestelling, maar deze keer ontvangt u uw telefoon nummer zodat ze u kunnen bellen wanneer de taart is voltooid. Deze keer vertelt de bakker u wanneer uw bestelling klaar is en uw taart levert.

Wanneer we dit webhook-patroon terugstuurt, vertegenwoordigt de bakker uw aangepaste API, terwijl u, de klant van de taart, de Logic Apps-Engine aanduidt. De engine roept uw API aan met een aanvraag en bevat een call back-URL.
Wanneer de taak is voltooid, gebruikt uw API de URL om de engine op de hoogte te stellen en gegevens te retour neren aan uw logische app, die vervolgens de werk stroom voortzet. 

Stel voor dit patroon twee eind punten in op uw controller: `subscribe` en `unsubscribe`

*  `subscribe` eind punt: wanneer de uitvoering van de API de actie in de werk stroom bereikt, roept de Logic Apps-Engine het `subscribe` eind punt aan. Deze stap zorgt ervoor dat de logische app een call back-URL maakt die uw API opslaat, en wacht vervolgens op de retour aanroep vanuit uw API wanneer het werk is voltooid. Uw API roept vervolgens terug met een HTTP POST naar de URL en geeft alle geretourneerde inhoud en kopteksten als invoer door aan de logische app.

* `unsubscribe` eind punt: als de uitvoering van de logische app wordt geannuleerd, roept de Logic Apps-Engine het `unsubscribe` eind punt aan. Uw API kan de registratie van de call back-URL vervolgens ongedaan maken en alle processen zo nodig stoppen.

![Actie patroon van webhook](./media/logic-apps-create-api-app/custom-api-webhook-action-pattern.png)

Momenteel biedt de Logic app Designer geen ondersteuning voor het detecteren van webhook-eind punten via Swagger. Daarom moet u voor dit patroon een [ **webhook** -actie](../connectors/connectors-native-webhook.md) toevoegen en de URL, headers en hoofd tekst voor uw aanvraag opgeven. Zie ook [werk stroom acties en triggers](logic-apps-workflow-actions-triggers.md#apiconnection-webhook-action). Bekijk voor een voor beeld van een webhook-patroon deze voor beeld [van een webhook-trigger in github](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

Hier volgen enkele andere tips en opmerkingen:

* Als u de URL voor terugbellen wilt door geven, kunt u `@listCallbackUrl()` indien nodig de werk stroom functie in een van de vorige velden gebruiken.

* Als u zowel de logische app als de geabonneerde service hebt, hoeft u het eind punt niet aan te roepen `unsubscribe` nadat de call back-URL is aangeroepen. Anders moet de Logic Apps runtime het `unsubscribe` eind punt aanroepen om aan te geven dat er geen aanroepen meer worden verwacht en dat het opschonen van resources aan de server zijde is toegestaan.

<a name="triggers"></a>

## <a name="trigger-patterns"></a>Trigger patronen

Uw aangepaste API kan fungeren als een [*trigger*](./logic-apps-overview.md#logic-app-concepts) waarmee een logische app wordt gestart wanneer nieuwe gegevens of een gebeurtenis aan een opgegeven voor waarde voldoet. Deze trigger kan regel matig controleren of wachten en Luis teren naar nieuwe gegevens of gebeurtenissen op uw service-eind punt. Als nieuwe gegevens of een gebeurtenis aan de opgegeven voor waarde voldoet, wordt de trigger geactiveerd en wordt de logische app gestart die naar die trigger luistert. Als u logische apps op deze manier wilt starten, kan uw API de [*polling trigger*](#polling-triggers) of het trigger patroon van de [*webhook*](#webhook-triggers) volgen. Deze patronen zijn vergelijkbaar met hun tegen Hangers voor [polling acties](#async-pattern) en [webhook-acties](#webhook-actions). Meer informatie over [gebruiks metingen voor triggers](logic-apps-pricing.md).

<a name="polling-triggers"></a>

### <a name="check-for-new-data-or-events-regularly-with-the-polling-trigger-pattern"></a>Regel matig controleren op nieuwe gegevens of gebeurtenissen met het patroon polling trigger

Een *polling trigger* fungeert zoals de [polling-actie](#async-pattern) die eerder in dit onderwerp is beschreven. Het trigger-eind punt wordt periodiek door de Logic Apps-Engine aangeroepen en gecontroleerd op nieuwe gegevens of gebeurtenissen. Als de engine nieuwe gegevens vindt of een gebeurtenis die voldoet aan de opgegeven voor waarde, wordt de trigger geactiveerd. Vervolgens maakt de engine een exemplaar van een logische app waarmee de gegevens als invoer worden verwerkt. 

![Trigger patroon polling](./media/logic-apps-create-api-app/custom-api-polling-trigger-pattern.png)

> [!NOTE]
> Elke polling aanvraag telt als uitvoering van een actie, zelfs wanneer er geen exemplaar van een logische app wordt gemaakt. Om te voor komen dat dezelfde gegevens meerdere keren worden verwerkt, moet de trigger gegevens opschonen die al zijn gelezen en zijn door gegeven aan de logische app.

Hier vindt u specifieke stappen voor een polling trigger, zoals beschreven in het perspectief van de API:

| Hebt u nieuwe gegevens of gebeurtenissen gevonden?  | API-reactie | 
| ------------------------- | ------------ |
| Gevonden | Een HTTP- `200 OK` status retour neren met de nettolading van het antwoord (invoer voor de volgende stap). <br/>Dit antwoord maakt een exemplaar van een logische app en start de werk stroom. | 
| Niet gevonden | Een HTTP- `202 ACCEPTED` status retour neren met een `location` koptekst en een `retry-after` header. <br/>Voor triggers moet de `location` header ook een `triggerState` query parameter bevatten, die meestal een ' tijds tempel ' is. Uw API kan deze id gebruiken om de laatste keer dat de logische app werd geactiveerd, bij te houden. | 
||| 

Als u bijvoorbeeld uw service regel matig wilt controleren op nieuwe bestanden, kunt u een polling trigger maken die het volgende gedrag heeft:

| Aanvraag bevat `triggerState` ? | API-reactie | 
| -------------------------------- | -------------| 
| Nee | Retour neer een HTTP- `202 ACCEPTED` status plus een `location` header met `triggerState` ingesteld op de huidige tijd en het `retry-after` interval tot 15 seconden. | 
| Ja | Controleer uw service op bestanden die worden toegevoegd na de `DateTime` for `triggerState` . | 
||| 

| Aantal gevonden bestanden | API-reactie | 
| --------------------- | -------------| 
| Eén bestand | Retour neer een HTTP- `200 OK` status en de nettolading van de inhoud, werk de `triggerState` `DateTime` voor het geretourneerde bestand bij en stel het `retry-after` interval in op 15 seconden. | 
| Meerdere bestanden | Retour neer één bestand per keer en een HTTP- `200 OK` status, update en `triggerState` Stel het `retry-after` interval in op 0 seconden. </br>Met deze stappen kan de engine weten dat er meer gegevens beschikbaar zijn en dat de engine onmiddellijk de gegevens van de URL in de header moet aanvragen `location` . | 
| Geen bestanden | Retour neer een HTTP- `202 ACCEPTED` status, Wijzig niet `triggerState` en stel het `retry-after` interval in op 15 seconden. | 
||| 

> [!TIP]
> Voor een voor beeld van polling trigger patroon, Bekijk dit [controle-voorbeeld trigger voor poll triggers in github](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/PollTriggerController.cs).

<a name="webhook-triggers"></a>

### <a name="wait-and-listen-for-new-data-or-events-with-the-webhook-trigger-pattern"></a>Wacht en luister naar nieuwe gegevens of gebeurtenissen met het trigger patroon webhook

Een webhook-trigger is een *Push trigger* waarmee wordt gewacht op nieuwe gegevens of gebeurtenissen op uw service-eind punt. Als nieuwe gegevens of een gebeurtenis aan de opgegeven voor waarde voldoet, wordt de trigger geactiveerd en wordt er een logische app-instantie gemaakt, die de gegevens vervolgens verwerkt als invoer.
Webhook-triggers functioneren op dezelfde manier als de [webhook-acties](#webhook-actions) die eerder in dit onderwerp zijn beschreven en worden ingesteld met `subscribe` en `unsubscribe` eind punten. 

* `subscribe` eind punt: wanneer u een webhook-trigger toevoegt en opslaat in uw logische app, wordt het eind punt aangeroepen door de Logic Apps Engine `subscribe` . Deze stap zorgt ervoor dat de logische app een call back-URL maakt die uw API opslaat. Wanneer er nieuwe gegevens zijn of een gebeurtenis die voldoet aan de opgegeven voor waarde, wordt uw API teruggebeld met een HTTP POST naar de URL. De nettolading van de inhoud en de headers worden door gegeven als invoer voor de logische app.

* `unsubscribe` eind punt: als de webhook-trigger of de volledige logische app wordt verwijderd, roept de Logic Apps-Engine het `unsubscribe` eind punt aan. Uw API kan de registratie van de call back-URL vervolgens ongedaan maken en alle processen zo nodig stoppen.

![Patroon van de webhook-trigger](./media/logic-apps-create-api-app/custom-api-webhook-trigger-pattern.png)

Momenteel biedt de Logic app Designer geen ondersteuning voor het detecteren van webhook-eind punten via Swagger. Daarom moet u voor dit patroon een [ **webhook** -trigger](../connectors/connectors-native-webhook.md) toevoegen en de URL, headers en hoofd tekst voor uw aanvraag opgeven. Zie ook [HTTPWebhook-trigger](logic-apps-workflow-actions-triggers.md#httpwebhook-trigger). Voor een voor beeld van een webhook-patroon controleert u deze [webhook trigger-voor beeld in github](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

Hier volgen enkele andere tips en opmerkingen:

* Als u de URL voor terugbellen wilt door geven, kunt u `@listCallbackUrl()` indien nodig de werk stroom functie in een van de vorige velden gebruiken.

* Om te voor komen dat dezelfde gegevens meerdere keren worden verwerkt, moet de trigger gegevens opschonen die al zijn gelezen en zijn door gegeven aan de logische app.

* Als u zowel de logische app als de geabonneerde service hebt, hoeft u het eind punt niet aan te roepen `unsubscribe` nadat de call back-URL is aangeroepen. Anders moet de Logic Apps runtime het `unsubscribe` eind punt aanroepen om aan te geven dat er geen aanroepen meer worden verwacht en dat het opschonen van resources aan de server zijde is toegestaan.

## <a name="improve-security-for-calls-to-your-apis-from-logic-apps"></a>Verbeter de beveiliging van aanroepen naar uw Api's vanuit Logic apps

Nadat u uw aangepaste Api's hebt gemaakt, moet u verificatie voor uw Api's instellen, zodat u deze veilig kunt aanroepen vanuit Logic apps. Meer informatie [over het verbeteren van de beveiliging voor het aanroepen van aangepaste api's vanuit Logic apps](../logic-apps/logic-apps-custom-api-authentication.md).

## <a name="deploy-and-call-your-apis"></a>Api's implementeren en aanroepen

Nadat u verificatie hebt ingesteld, stelt u de implementatie in voor uw Api's. Meer informatie [over het implementeren en aanroepen van aangepaste api's vanuit Logic apps](../logic-apps/logic-apps-custom-api-host-deploy-call.md).

## <a name="publish-custom-apis-to-azure"></a>Aangepaste Api's publiceren naar Azure

Als u uw aangepaste Api's beschikbaar wilt maken voor andere Logic Apps gebruikers in azure, moet u beveiliging toevoegen en deze registreren als connectors voor logische apps. Zie voor meer informatie het [Overzicht van aangepaste connectors](../logic-apps/custom-connector-overview.md). 

Als u uw aangepaste Api's beschikbaar wilt maken voor alle gebruikers in Logic Apps, energie automatisering en micro soft power apps, moet u beveiliging toevoegen, uw Api's registreren als logische app-connectors en uw connectors benoemen voor het [Microsoft Azure gecertificeerde programma](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/). 

## <a name="get-support"></a>Ondersteuning krijgen

* Voor specifieke hulp bij aangepaste Api's, neemt u contact op met [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com) .

* Ga voor vragen naar de [pagina micro soft Q&een vraag voor Azure Logic apps](/answers/topics/azure-logic-apps.html).

* Ter verbetering van Logic Apps kunt u stemmen op ideeën of ideeën indienen op de [site voor gebruikersfeedback van Logic Apps](https://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Volgende stappen

* [Fouten en uitzonderingen verwerken](../logic-apps/logic-apps-exception-handling.md)
* [Logische apps aanroepen, activeren of nesten met HTTP-eind punten](../logic-apps/logic-apps-http-endpoint.md)
* [Gebruiks meting voor acties en triggers](../logic-apps/logic-apps-pricing.md)

---
title: Veelgestelde vragen (FAQ) - LUIS
description: Dit artikel bevat antwoorden op veelgestelde vragen over Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: troubleshooting
ms.date: 04/13/2021
ms.openlocfilehash: 97b7c02a418a87a0700414e19bc939bda899d073
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503810"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Veelgestelde vragen (FAQ’s) over Language Understanding

Dit artikel bevat antwoorden op veelgestelde vragen over Language Understanding (LUIS).

## <a name="whats-new"></a>Nieuw

[Meer informatie](whats-new.md) over wat er nieuw is in Language Understanding (LUIS).

<a name="luis-authoring"></a>

## <a name="authoring"></a>Ontwerpen

### <a name="what-are-the-luis-best-practices"></a>Wat zijn de best practices voor LUIS?
Begin met de [ontwerpcyclus](luis-concept-app-iteration.md)en lees vervolgens de [best practices.](luis-concept-best-practices.md)

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Wat is de beste manier om te beginnen met het bouwen van mijn app in LUIS?

De beste manier om uw app te bouwen, is via [een incrementeel proces.](luis-concept-app-iteration.md)

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Wat is een goede gewoonte om de intenties van mijn app te modelleren? Moet ik specifiekere of meer algemene intenties maken?

Kies intenties die niet zo algemeen zijn als overlappend, maar niet zo specifiek dat het voor LUIS moeilijk is om onderscheid te maken tussen vergelijkbare intenties. Het maken van discriminatieve specifieke intenties is een van de best practices voor LUIS-modellering.

### <a name="is-it-important-to-train-the-none-intent"></a>Is het belangrijk om de intentie None te trainen?

Ja, het is goed om uw **none-intentie te** trainen met meer uitingen wanneer u meer labels toevoegt aan andere intenties. Een goede verhouding is 1 of 2 labels toegevoegd aan **Geen voor** elke 10 labels die aan een intentie worden toegevoegd. Deze verhouding verhoogt de discriminatieve kracht van LUIS.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Hoe kan ik spelfouten in utterances corrigeren?

Zie de [zelfstudie Bing Spellingcontrole API V7.](luis-tutorial-bing-spellcheck.md) LUIS dwingt limieten af die zijn Bing Spellingcontrole API V7.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Hoe kan ik mijn LUIS-app programmatisch bewerken?
Als u uw LUIS-app programmatisch wilt bewerken, gebruikt u [de ontwerp-API](https://go.microsoft.com/fwlink/?linkid=2092087). Zie [Luis-ontwerp-API aanroepen](./get-started-get-model-rest-apis.md) en Programmatisch een [LUIS-app ](./luis-tutorial-node-import-utterances-csv.md) bouwen met behulp van Node.jsvoor voorbeelden van het aanroepen van de ontwerp-API. De ontwerp-API vereist dat u een [ontwerpsleutel gebruikt in](luis-how-to-azure-subscription.md#azure-resources-for-luis) plaats van een eindpuntsleutel. Programmatisch schrijven staat maximaal 1.000.000 aanroepen per maand en vijf transacties per seconde toe. Zie Sleutels beheren voor meer informatie over de sleutels die u met LUIS [gebruikt.](./luis-how-to-azure-subscription.md)

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Waar is de patroonfunctie waarmee reguliere expressies aan elkaar zijn gematcht?
De vorige **patroonfunctie** is momenteel afgeschaft, vervangen door **[Patterns](luis-concept-patterns.md)**.

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Hoe kan ik entiteit gebruiken om de juiste gegevens op te halen?
Zie [entiteiten en](luis-concept-entity-types.md) [gegevensextractie.](luis-concept-data-extraction.md)

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Moeten variaties van een voorbeeld-utterance leestekens bevatten?
Gebruik een van de volgende oplossingen:
* [Leestekens negeren](luis-reference-application-settings.md#punctuation-normalization)
* Voeg de verschillende variaties als voorbeeld-utterances toe aan de intentie
* Voeg het patroon van de voorbeelduiting toe met de [syntaxis om de leestekens](luis-concept-patterns.md#pattern-syntax) te negeren.

### <a name="does-luis-currently-support-cortana"></a>Ondersteunt LUIS momenteel Cortana?

Vooraf gebouwde Cortana-apps zijn afgeschaft in 2017. Ze worden niet meer ondersteund.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Hoe kan ik eigendom van een LUIS-app overdragen?
Als u een LUIS-app wilt overdragen naar een ander Azure-abonnement, exporteert u de LUIS-app en importeert u deze met een nieuw account. Werk de LUIS-app-id bij in de clienttoepassing die deze aanroept. De nieuwe app retourneert mogelijk iets andere LUIS-scores dan de oorspronkelijke app.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>Een vooraf gemaakte entiteit wordt getagd in een voorbeeld-utterance in plaats van mijn aangepaste entiteit. Hoe kan ik dit oplossen?

In de LUIS-portal kunt u tekst labelen voor de exacte entiteit die u wilt extraheren. Als in de LUIS-portal niet de juiste entiteitsvoorspelling wordt weergegeven, moet u mogelijk meer utterances toevoegen en de entiteit labelen in de tekst of een functie toevoegen.

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Ik heb geprobeerd een app of versiebestand te importeren, maar er is een fout opgetreden. Wat is er gebeurd?

Lees meer over fouten [bij het importeren van versies.](luis-how-to-manage-versions.md#import-errors)

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>Samenwerken en bijdragen

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-azure-role-based-access-control-azure-rbac"></a>Hoe kan ik samenwerkers toegang geven tot LUIS met Azure Active Directory (Azure AD) of op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC)?

Zie [Azure Active Directory resources en](luis-how-to-collaborate.md#azure-active-directory-resources)  Azure Active Directory [tenantgebruiker voor](luis-how-to-collaborate.md#azure-active-directory-tenant-user) meer informatie over het verlenen van toegang aan samenwerkers.

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Eindpunt

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>Ik heb een HTTP 403-foutstatuscode ontvangen. Hoe kan ik dit probleem oplossen?

U krijgt de foutstatuscodes 403 en 429 wanneer u de transacties per seconde of transacties per maand voor uw prijscategorie overschrijdt. Verhoog uw prijscategorie of gebruik Language Understanding [containers](luis-container-howto.md).

Wanneer u al deze gratis 1000 eindpuntquery's gebruikt of als u het quotum voor maandelijkse transacties van uw prijscategorie overschrijdt, ontvangt u een HTTP 403-foutstatuscode.

Als u deze fout wilt [](luis-how-to-azure-subscription.md#change-the-pricing-tier) oplossen, moet u de prijscategorie wijzigen in een hogere laag of een nieuwe [resource](get-started-portal-deploy-app.md#create-the-endpoint-resource) maken en deze [toewijzen aan uw app.](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal)

Oplossingen voor deze fout zijn onder andere:

* Wijzig in [Azure Portal](https://portal.azure.com), op uw Language Understanding-resource, in de prijscategorie **Resource Management ->,** uw prijscategorie in een hogere TPS-laag. U hoeft niets te doen in de Language Understanding portal als uw resource al is toegewezen aan uw Language Understanding app.
*  Als uw gebruik de hoogste prijscategorie overschrijdt, voegt u meer Language Understanding toe met een load balancer er voorin. De [Language Understanding container](luis-container-howto.md) met Kubernetes of Docker Compose kan daarbij helpen.

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>Ik heb een HTTP 429-foutstatuscode ontvangen. Hoe kan ik dit probleem oplossen?

U krijgt 403- en 429-foutstatuscodes wanneer u de transacties per seconde of transacties per maand voor uw prijscategorie overschrijdt. Verhoog uw prijscategorie of gebruik Language Understanding [containers](luis-container-howto.md).

Deze statuscode wordt geretourneerd wanneer uw transacties per seconde de prijscategorie overschrijden.

Oplossingen zijn onder andere:

* U kunt [uw prijscategorie verhogen](luis-how-to-azure-subscription.md#change-the-pricing-tier)als u niet de hoogste laag hebt.
* Als uw gebruik de hoogste prijscategorie overschrijdt, voegt u meer resources Language Understanding toevoegen met een load balancer voor deze resources. De [Language Understanding container](luis-container-howto.md) met Kubernetes of Docker Compose kan daarbij helpen.
* U kunt aanvragen van uw clienttoepassing gateen met [een beleid voor](/azure/architecture/best-practices/transient-faults#general-guidelines) opnieuw proberen dat u zelf implementeert wanneer u deze statuscode krijgt.

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>Mijn eindpuntquery heeft onverwachte resultaten geretourneerd. Wat moet ik doen?

Onverwachte queryvoorspellingsresultaten zijn gebaseerd op de status van het gepubliceerde model. Als u het model wilt corrigeren, moet u het model mogelijk wijzigen, trainen en opnieuw publiceren.

Het corrigeren van het model begint met [actief leren.](luis-how-to-review-endpoint-utterances.md)

U kunt niet-deterministische training verwijderen door de [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) voor toepassingsversie-instellingen bij te werken zodat alle trainingsgegevens worden gebruikt.

Bekijk de [best practices voor](luis-concept-best-practices.md) andere tips.

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Waarom voegt LUIS spaties toe aan de query rond of in het midden van woorden?
LUIS [tokeniseert de](luis-glossary.md#token) utterance op basis van de [cultuur](luis-language-support.md#tokenization). Zowel de oorspronkelijke waarde als de tokenized waarde zijn beschikbaar voor [gegevensextractie.](luis-concept-data-extraction.md#tokenized-entity-returned)

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Hoe kan ik luis-eindpuntsleutel maken en toewijzen?
[Maak de eindpuntsleutel](luis-how-to-azure-subscription.md) voor uw [serviceniveau](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) in Azure. [Wijs de sleutel](luis-how-to-azure-subscription.md) toe op de **[pagina Azure-resources.](luis-how-to-azure-subscription.md)** Er is geen bijbehorende API voor deze actie. Vervolgens moet u de HTTP-aanvraag wijzigen in het eindpunt om [de nieuwe eindpuntsleutel te gebruiken.](luis-how-to-azure-subscription.md)

### <a name="how-do-i-interpret-luis-scores"></a>Hoe kan ik LUIS-scores interpreteren?
Uw systeem moet de hoogst scorende intentie gebruiken, ongeacht de waarde. Bijvoorbeeld een score lager dan 0,5 (minder dan 50%) betekent niet noodzakelijkerwijs dat LUIS een lage betrouwbaarheid heeft. Door meer trainingsgegevens op te geven, kunt u [de score](luis-concept-prediction-score.md) van de meest waarschijnlijke intentie verhogen.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Waarom zie ik mijn eindpunt niet in het dashboard van mijn app?
Het totale aantal treffers in het dashboard van uw app wordt periodiek bijgewerkt, maar de metrische gegevens die zijn gekoppeld aan uw LUIS-eindpuntsleutel in de Azure Portal vaker bijgewerkt.

Als u bijgewerkte eindpunttreffers niet ziet in het dashboard, meldt u zich aan bij de Azure Portal, gaat u naar de resource die is gekoppeld aan uw LUIS-eindpuntsleutel en opent u Metrische gegevens om de metrische waarde Totaal aantal aanroepen **te** selecteren.  Als de eindpuntsleutel wordt gebruikt voor meer dan één LUIS-app, toont de metriek in de Azure Portal het cumulatief aantal aanroepen van alle LUIS-apps die deze gebruiken.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>Is er een PowerShell-opdracht voor het eindpuntquotum?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

U kunt een PowerShell-opdracht gebruiken om het eindpuntquotum te bekijken:

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
```

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>Mijn LUIS-app werkte gisteren, maar vandaag krijg ik 403-fouten. Ik heb de app niet gewijzigd. Hoe kan ik dit probleem oplossen?
Volg deze [instructies om](#how-do-i-create-and-assign-a-luis-endpoint-key) een LUIS-eindpuntsleutel te maken en deze toe te wijzen aan de app. Vervolgens moet u de HTTP-aanvraag van de clienttoepassing wijzigen in het eindpunt om de [nieuwe eindpuntsleutel te gebruiken.](luis-how-to-azure-subscription.md) Als u een nieuwe resource in een andere regio hebt gemaakt, wijzigt u ook de regio van de HTTP-clientaanvraag.

### <a name="how-do-i-secure-my-luis-endpoint"></a>Hoe kan ik luis-eindpunt beveiligen?
Zie [Het eindpunt beveiligen.](luis-how-to-azure-subscription.md#securing-the-endpoint)

## <a name="working-within-luis-limits"></a>Werken binnen LUIS-limieten

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Wat is het maximum aantal intenties en entiteiten dat een LUIS-app kan ondersteunen?
Zie de [naslag voor grenzen.](luis-limits.md)

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Ik wil een LUIS-app bouwen met meer dan het maximum aantal intenties. Wat moet ik doen?

Zie [Best practices for intents (Best practices voor intenties).](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Wat zijn de limieten voor het aantal en de grootte van woordgroepenlijsten?
Zie de naslag voor grenzen [voor](./luis-concept-feature.md)de maximale lengte van een [frasenlijst.](luis-limits.md)

### <a name="what-are-the-limits-on-example-utterances"></a>Wat zijn de limieten voor voorbeeld-utterances?
Zie de [naslag voor grenzen.](luis-limits.md)

## <a name="testing-and-training"></a>Testen en trainen

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Ik zie enkele fouten in het batchtestdeelvenster voor een aantal modellen in mijn app. Hoe kan ik dit probleem oplossen?

De fouten geven aan dat er een discrepantie is tussen uw labels en de voorspellingen van uw modellen. U kunt het probleem oplossen door een of beide van de volgende taken uit te voeren:
* Voeg meer labels toe om LUIS te helpen de intenties te verbeteren.
* Om LUIS sneller te laten leren, voegt u woordgroepenlijstfuncties toe die domeinspecifieke woordenlijst introduceren.

Zie de [zelfstudie Batch-testen.](./luis-how-to-batch-test.md)

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Wanneer een app wordt geëxporteerd en vervolgens opnieuw wordt geimporteerd in een nieuwe app (met een nieuwe app-id), zijn de LUIS-voorspellingsscores anders. Waarom gebeurt dit?

Zie [Prediction differences between copies of same app (Voorspellingsverschillen tussen kopieën van dezelfde app).](luis-concept-prediction-score.md#review-intents-with-similar-scores)

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Sommige utterances gaan naar de verkeerde intentie nadat ik wijzigingen in mijn app heb aangebracht. Het probleem lijkt willekeurig te verdwijnen. Hoe kan ik dit probleem oplossen?

Zie [Trainen met alle gegevens.](luis-how-to-train.md#train-with-all-data)

## <a name="app-publishing"></a>App-publicatie

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Wat is de tenant-id in het venster Een sleutel toevoegen aan uw app?
In Azure vertegenwoordigt een tenant de client of organisatie die is gekoppeld aan een service. Zoek uw tenant-id in het Azure Portal in het vak **Map-id door** Azure Active Directory  >  **Eigenschappen beheren te**  >  **selecteren.**

![Tenant-id in de Azure Portal](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Waarom zijn er meer eindpuntsleutels toegewezen aan mijn app dan ik heb toegewezen?
Elke LUIS-app heeft voor het gemak de ontwerp-/startersleutel in de lijst met eindpunten. Deze sleutel staat slechts enkele eindpunttreffers toe, zodat u LUIS kunt uitproberen.

Als uw app bestond voordat LUIS algemeen beschikbaar was, worden LUIS-eindpuntsleutels in uw abonnement automatisch toegewezen. Dit is gedaan om de migratie van de ga naar een eenvoudigere migratie te maken. Nieuwe LUIS-eindpuntsleutels in de Azure Portal _worden_ niet automatisch toegewezen aan LUIS.

## <a name="key-management"></a>Sleutelbeheer

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Hoe kan ik welke sleutel ik nodig heb, waar vind ik deze en wat doe ik daarmee?

Zie [Eindpuntsleutels voor ontwerp-](luis-how-to-azure-subscription.md) en queryvoorspelling in LUIS voor meer informatie over de verschillen tussen de ontwerpsleutel en de voorspellingsruntimesleutel.

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Ik heb een foutmelding over het overschrijden van het quotum. Hoe kan ik dit probleem oplossen?

Zie FIX HTTP status code 403 and 429 (HTTP-statuscode [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) en [429 herstellen)](#i-received-an-http-429-error-status-code-how-do-i-fix-it) voor meer informatie.

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>Ik moet meer eindpuntquery's verwerken. Hoe doe ik dat?

Zie FIX HTTP status code 403 and 429 (HTTP-statuscode [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) en [429 herstellen)](#i-received-an-http-429-error-status-code-how-do-i-fix-it) voor meer informatie.

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>Ik heb een ontwerpsleutel gemaakt, maar deze wordt niet weergegeven in de LUIS-portal. Wat is er gebeurd?

Ontwerpsleutels zijn beschikbaar in de LUIS-portal [na de migratie naar de ontwerpsleutelervaring](luis-migration-authoring.md).

## <a name="app-management"></a>Appbeheer

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Hoe kan ik logboek met uitingen van gebruikers downloaden?
Standaard registreert uw LUIS-app utterances van gebruikers. Als u een logboek wilt downloaden met utterances die gebruikers naar uw LUIS-app verzenden, gaat u **naar Mijn apps** en selecteert u de app. Selecteer In de contextuele werkbalk **Eindpuntlogboeken exporteren.** Het logboek is opgemaakt als een bestand met door komma's gescheiden waarden (CSV).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Hoe kan ik de logboekregistratie van utterances uitschakelen?
U kunt de logboekregistratie van gebruikers-utterances uitschakelen door de eindpunt-URL in te stellen die uw clienttoepassing gebruikt om query's uit te voeren `log=false` op LUIS. Het uitschakelen van logboekregistratie schakelt echter de mogelijkheid van uw LUIS-app uitingen voor te stellen of prestaties te verbeteren die zijn gebaseerd op [actief leren.](luis-concept-review-endpoint-utterances.md#what-is-active-learning) Als u gegevensbescherming in stelt, kunt u geen record van deze gebruikers-utterances downloaden van LUIS of deze utterances gebruiken om uw `log=false` app te verbeteren.

Logboekregistratie is de enige opslag van utterances.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Waarom wil ik niet dat al mijn eindpunt-utterances worden vastgelegd?
Als u uw logboek gebruikt voor voorspellingsanalyse, moet u geen test-utterances vastleggen in uw logboek.

## <a name="data-management"></a>Gegevensbeheer

### <a name="can-i-delete-data-from-luis"></a>Kan ik gegevens uit LUIS verwijderen?

* U kunt altijd voorbeeld-utterances verwijderen die worden gebruikt voor het trainen van LUIS. Als u een voorbeelduiting uit uw LUIS-app verwijdert, wordt deze verwijderd uit de LUIS-webservice en is deze niet beschikbaar voor export.
* U kunt utterances verwijderen uit de lijst met uitingen van gebruikers die luis voorstelt op de pagina **Eindpuntuitingen** beoordelen. Als u utterances uit deze lijst verwijdert, voorkomt u dat ze worden voorgesteld, maar worden ze niet uit logboeken verwijderd.
* Als u een account verwijdert, worden alle apps verwijderd, samen met hun voorbeeld-utterances en logboeken. De gegevens worden 60 dagen bewaard op de servers voordat ze permanent worden verwijderd.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>Hoe beheert Microsoft de gegevens die ik naar LUIS verzend?

In [het Vertrouwenscentrum](https://www.microsoft.com/trustcenter) worden onze toezeggingen en uw opties voor gegevensbeheer en -toegang in Azure Services uitgelegd.

## <a name="language-and-translation-support"></a>Ondersteuning voor taal en vertaling

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Ik heb een app in één taal en wil een parallelle app in een andere taal maken. Wat is de eenvoudigste manier om dit te doen?
1. Exporteert u uw app.
2. Vertaal de gelabelde utterances in het JSON-bestand van de geëxporteerde app naar de doeltaal.
3. Mogelijk moet u de namen van de intenties en entiteiten wijzigen of deze laten zoals ze zijn.
4. Importeer ten slotte de app om een LUIS-app in de doeltaal te hebben.

## <a name="app-notification"></a>App-melding

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Waarom krijg ik een e-mail met de vraag of ik bijna een quotum heb?
Uw ontwerp-/startersleutel is slechts 1000 eindpuntquery's per maand toegestaan. Maak een LUIS-eindpuntsleutel (gratis of betaald) en gebruik die sleutel bij het maken van eindpuntquery's. Als u eindpuntquery's maakt vanuit een bot of een andere clienttoepassing, moet u daar de LUIS-eindpuntsleutel wijzigen.

## <a name="bots"></a>Bots

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>Mijn LUIS-bot werkt niet. Wat moet ik doen?

Het eerste probleem is om te isoleren als het probleem te maken heeft met LUIS of zich buiten de LUIS-middleware voor doet.

#### <a name="resolve-issue-in-luis"></a>Probleem in LUIS oplossen
Geef dezelfde utterance door aan LUIS vanaf het [LUIS-eindpunt](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint). Als er een foutmelding wordt weergegeven, lost u het probleem in LUIS op totdat de fout niet meer wordt geretourneerd. Veelvoorkomende fouten zijn onder andere:

* `Out of call volume quota. Quota will be replenished in <time>.`- Dit probleem geeft aan dat u moet wijzigen van een ontwerpsleutel in een eindpuntsleutel [of](luis-how-to-azure-subscription.md) dat u [servicelagen moet wijzigen.](luis-how-to-azure-subscription.md#change-the-pricing-tier)

#### <a name="resolve-issue-in-azure-bot-service"></a>Probleem in Azure Bot Service

Als u de Azure Bot Service gebruikt en het probleem is dat de **test in** Webchat retourneert, `Sorry, my bot code is having an issue` controleert u uw logboeken:

1. Selecteer in Azure Portal voor uw bot in de sectie **Botbeheer** de optie **Bouwen.**
1. Open de online code-editor.
1. Selecteer in de blauwe bovenste navigatiebalk de naam van de bot (het tweede item aan de rechterkant).
1. Selecteer Kudu-console openen in de **resulterende vervolgkeuzelijst.**
1. Selecteer **LogFiles** en selecteer vervolgens **Toepassing**. Controleer alle logboekbestanden. Als de fout niet wordt weergegeven in de toepassingsmap, controleert u alle logboekbestanden onder **LogFiles**.
1. Vergeet niet om uw project opnieuw te bouwen als u een gecompileerde taal zoals C# gebruikt.

> [!Tip]
> De -console kan ook pakketten installeren.

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Los het probleem op tijdens het oplossen van de probleemopsporing op de lokale computer met Bot Framework.

Zie Fouten opsporen in een bot voor meer informatie over lokale [foutopsporing van een bot.](/azure/bot-service/bot-service-debug-bot)

## <a name="integrating-luis"></a>LUIS integreren

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Waar wordt mijn LUIS-app gemaakt tijdens het abonnementsproces van de Azure-web-app-bot?
Als u een LUIS-sjabloon  selecteert en de knop Selecteren selecteert in het sjabloondeelvenster, wordt het deelvenster aan de linkerkant gewijzigd om het sjabloontype op te nemen en wordt u gevraagd in welke regio de LUIS-sjabloon moet worden gemaakt. Het web-app-botproces maakt echter geen LUIS-abonnement.

![Regio van luis-sjabloonweb-app-bot](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Welke LUIS-regio's Bot Framework spraak-priming?
[Spraakpriming](/bot-framework/bot-service-manage-speech-priming) wordt alleen ondersteund voor LUIS-apps in het centrale exemplaar (VS).

## <a name="api-programming-strategies"></a>STRATEGIEËN VOOR API-programmering

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Hoe kan ik programmatisch de LUIS-regio van een resource op te halen?

Gebruik het LUIS-voorbeeld om [regio's programmatisch](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) te vinden met C# of Node.Js.

## <a name="luis-service"></a>LUIS-service

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>Is Language Understanding (LUIS) on-premises of in de privécloud beschikbaar?

Ja, u kunt de [LUIS-container voor](luis-container-howto.md) deze scenario's gebruiken als u de benodigde verbinding hebt met het metergebruik.

## <a name="migrating-to-the-next-version"></a>Migreren naar de volgende versie

### <a name="how-do-i-migrate-to-preview-v3-api"></a>Hoe kan ik migreren naar preview-versie van de V3-API?

Zie [migratiehandleiding voor API v2 naar v3 voor LUIS-apps](luis-migration-api-v3.md)

## <a name="build-2019-conference-announcements"></a>Aankondigingen van de Build 2019-conferentie

De volgende functies zijn uitgebracht op de Build 2019 Conference:

* [Preview van de V3 API-migratiehandleiding](luis-migration-api-v3.md)
* [Verbeterd analysedashboard](luis-how-to-use-dashboard.md)
* [Verbeterde vooraf gemaakte domeinen](luis-reference-prebuilt-domains.md)
* [Dynamische lijstentiteiten](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Externe entiteiten](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

Video's:

* [Gespreks-AI van Azure gebruiken om uw bedrijf te schalen voor de volgende generatie](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie over LUIS:
* [Stack Overflow vragen die zijn getagd met LUIS](https://stackoverflow.com/questions/tagged/luis)
* [Microsoft Q&Een vragenpagina voor MSDN Language Understanding Intelligent Services (LUIS)](/answers/topics/azure-language-understanding.html)
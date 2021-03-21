---
title: Veelgestelde vragen (FAQ)-LUIS
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: troubleshooting
ms.date: 05/06/2020
ms.openlocfilehash: b5e25e9ed25ced96d38994928bcb6275ce79420f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102612793"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Veelgestelde vragen (FAQ’s) over Language Understanding

In dit artikel vindt u antwoorden op veelgestelde vragen over Language Understanding (LUIS).

## <a name="whats-new"></a>Nieuw

Meer [informatie](whats-new.md) over wat er nieuw is in language UNDERSTANDING (Luis).

<a name="luis-authoring"></a>

## <a name="authoring"></a>Ontwerpen

### <a name="what-are-the-luis-best-practices"></a>Wat zijn de best practices voor LUIS?
Begin met de [ontwerp cyclus](luis-concept-app-iteration.md)en lees vervolgens de [Aanbevolen procedures](luis-concept-best-practices.md).

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Wat is de beste manier om mijn app te bouwen in LUIS?

De beste manier om uw app te bouwen, is via een [Incrementeel proces](luis-concept-app-iteration.md).

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Wat is een goede gewoonte om de intenties van mijn app te model leren? Moet ik meer specifieke of meer algemene intenties maken?

Kies intenties die niet zo algemeen zijn dat ze elkaar overlappen, maar niet zo specifiek, waardoor het moeilijk is voor LUIS onderscheid te maken tussen vergelijk bare intenten. Het maken van discriminative-specifieke intenties is een van de aanbevolen procedures voor het LUIS-model leren.

### <a name="is-it-important-to-train-the-none-intent"></a>Is het belang rijk dat u de geen intentie traint?

Ja **, het is een goed** idee om uw eigen intentie te trainen met meer uitingen wanneer u meer labels toevoegt aan andere intenties. Een goede verhouding is 1 of 2 labels worden toegevoegd aan **geen** voor elke 10 labels die aan een intentie worden toegevoegd. Deze ratio verhoogt de discriminative kracht van LUIS.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Hoe kan ik spel fouten in uitingen corrigeren?

Raadpleeg de [Bing spellingcontrole-API V7](luis-tutorial-bing-spellcheck.md) -zelf studie. LUIS dwingt limieten af die zijn opgelegd door Bing Spellingcontrole-API v7.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Hoe kan ik mijn LUIS-app programmatisch bewerken?
Als u de LUIS-app programmatisch wilt bewerken, gebruikt u de [ontwerp-API](https://go.microsoft.com/fwlink/?linkid=2092087). Zie [Luis-ontwerp-API aanroepen](./get-started-get-model-rest-apis.md) en [een Luis-app bouwen met behulp van Node.js](./luis-tutorial-node-import-utterances-csv.md) voor voor beelden van het aanroepen van de API voor ontwerpen. De ontwerp-API vereist dat u een [ontwerp sleutel](luis-how-to-azure-subscription.md#azure-resources-for-luis) gebruikt in plaats van een eindpunt sleutel. Via programmatische ontwerp kunnen Maxi maal 1.000.000 oproepen per maand en vijf trans acties per seconde worden uitgevoerd. Zie [sleutels beheren](./luis-how-to-azure-subscription.md)voor meer informatie over de sleutels die u gebruikt met Luis.

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Waar bevindt zich de patroon functie die een reguliere expressie in overeenstemming voorziet?
De vorige **patroon functie** is momenteel afgeschaft, vervangen door **[patronen](luis-concept-patterns.md)**.

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Hoe kan ik een entiteit gebruiken om de juiste gegevens op te halen?
Zie [entiteiten](luis-concept-entity-types.md) en [gegevens extractie](luis-concept-data-extraction.md).

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Moeten variaties van een voor beeld-utterance een interpunctie omvatten?
Gebruik een van de volgende oplossingen:
* [Interpunctie](luis-reference-application-settings.md#punctuation-normalization) negeren
* De verschillende variaties als voor beeld uitingen toevoegen aan de intentie
* Voeg het patroon van het voor beeld-utterance toe met de syntaxis om de interpunctie [te negeren](luis-concept-patterns.md#pattern-syntax) .

### <a name="does-luis-currently-support-cortana"></a>Ondersteunt LUIS momenteel Cortana?

Met Cortana vooraf gebouwde apps zijn afgeschaft in 2017. Ze worden niet meer ondersteund.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Hoe kan ik eigendom van een LUIS-app overdragen?
Als u een LUIS-app wilt overdragen naar een ander Azure-abonnement, exporteert u de LUIS-app en importeert u deze met behulp van een nieuw account. Werk de App-ID van de LUIS bij in de client toepassing die deze aanroept. De nieuwe app retourneert mogelijk iets verschillende LUIS-scores van de oorspronkelijke app.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>Een vooraf samengestelde entiteit is gelabeld in een voor beeld-utterance in plaats van mijn aangepaste entiteit. Hoe kan ik dit probleem oplossen?

In de LUIS-Portal kunt u tekst labelen voor de exacte entiteit die u wilt uitpakken. Als de LUIS-Portal niet de juiste voor spelling van de entiteit weergeeft, moet u mogelijk meer uitingen toevoegen en de entiteit voorzien van een label in de tekst of een functie toevoegen.

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Ik heb geprobeerd een app of versie bestand te importeren, maar ik kreeg een fout melding. Wat is er gebeurd?

Meer informatie over [fouten](luis-how-to-manage-versions.md#import-errors)bij het importeren van versies.

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>Samen werken en bijdragen

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-azure-role-based-access-control-azure-rbac"></a>Hoe kan ik deel nemers toegang geven tot LUIS met Azure Active Directory (Azure AD) of op rollen gebaseerd toegangs beheer (Azure RBAC) van Azure?

Zie [Azure Active Directory resources](luis-how-to-collaborate.md#azure-active-directory-resources)  en [Azure Active Directory Tenant gebruiker](luis-how-to-collaborate.md#azure-active-directory-tenant-user) voor meer informatie over hoe u samen werkers toegang kunt verlenen.

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Eindpunt

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>Ik heb een HTTP 403-fout status code ontvangen. Hoe kan ik dit probleem oplossen?

U krijgt de status codes 403 en 429 wanneer u de trans acties per seconde of trans acties per maand voor uw prijs categorie overschrijdt. Verhoog uw prijs categorie of gebruik Language Understanding [containers](luis-container-howto.md).

Wanneer u al deze gratis 1000-eindpunt query's gebruikt of als u het quotum voor maandelijkse trans acties van uw prijs categorie overschrijdt, ontvangt u een HTTP 403-fout status code.

U kunt deze fout oplossen door [de prijs categorie te wijzigen](luis-how-to-azure-subscription.md#change-the-pricing-tier) in een hogere laag of door [een nieuwe resource te maken](get-started-portal-deploy-app.md#create-the-endpoint-resource) en [deze toe te wijzen aan uw app](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal).

Oplossingen voor deze fout zijn onder andere:

* Wijzig in de [Azure Portal](https://portal.azure.com), op uw language Understanding-resource, in de **prijs categorie resource management->** de prijs categorie in een hogere TPS-laag. U hoeft niets te doen in de Language Understanding portal als uw resource al is toegewezen aan uw Language Understanding app.
*  Als uw gebruik de hoogste prijs categorie overschrijdt, voegt u meer Language Understanding resources toe met een load balancer. De [Language Understanding-container](luis-container-howto.md) met Kubernetes of docker-opstellen kan u hierbij helpen.

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>Ik heb een HTTP 429-fout status code ontvangen. Hoe kan ik dit probleem oplossen?

U krijgt de status codes 403 en 429 wanneer u de trans acties per seconde of trans acties per maand voor uw prijs categorie overschrijdt. Verhoog uw prijs categorie of gebruik Language Understanding [containers](luis-container-howto.md).

Deze status code wordt geretourneerd wanneer uw trans acties per seconde uw prijs categorie overschrijden.

Oplossingen omvatten:

* U kunt [de prijs categorie verhogen](luis-how-to-azure-subscription.md#change-the-pricing-tier)als u niet de hoogste laag hebt.
* Als uw gebruik de hoogste prijs categorie overschrijdt, voegt u meer Language Understanding resources toe met een load balancer. De [Language Understanding-container](luis-container-howto.md) met Kubernetes of docker-opstellen kan u hierbij helpen.
* U kunt de aanvragen van uw client toepassing gateiseren met een [beleid voor opnieuw proberen](/azure/architecture/best-practices/transient-faults#general-guidelines) dat u zelf implementeert wanneer u deze status code ophaalt.

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>Mijn eindpunt query heeft onverwachte resultaten geretourneerd. Wat moet ik doen?

Onverwachte query Voorspellings resultaten zijn gebaseerd op de status van het gepubliceerde model. Als u het model wilt corrigeren, moet u mogelijk het model, de training en de publicatie opnieuw wijzigen.

Corrigeren van het model begint met [actief leren](luis-how-to-review-endpoint-utterances.md).

U kunt niet-deterministische trainingen verwijderen door de [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) voor de versie-instellingen van de toepassing bij te werken om alle trainings gegevens te kunnen gebruiken.

Bekijk de [Aanbevolen procedures](luis-concept-best-practices.md) voor andere tips.

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Waarom voegen LUIS spaties toe aan de query rondom of in het midden van woorden?
LUIS [tokenizes](luis-glossary.md#token) de utterance op basis van de [cultuur](luis-language-support.md#tokenization). Zowel de oorspronkelijke waarde als de tokend-waarde zijn beschikbaar voor [gegevens extractie](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Hoe kan ik een LUIS-eindpunt sleutel maken en toewijzen?
[Maak de eindpunt sleutel](luis-how-to-azure-subscription.md) in azure voor uw [service](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) niveau. [Wijs de sleutel toe](luis-how-to-azure-subscription.md) op de pagina **[Azure-resources](luis-how-to-azure-subscription.md)** . Er is geen bijbehorende API voor deze actie. Vervolgens moet u de HTTP-aanvraag voor het eind punt wijzigen om [de nieuwe eindpunt sleutel te gebruiken](luis-how-to-azure-subscription.md).

### <a name="how-do-i-interpret-luis-scores"></a>Hoe kan ik LUIS-scores interpreteren?
Uw systeem moet de hoogste score intentie gebruiken, ongeacht de waarde. Bijvoorbeeld een score onder 0,5 (minder dan 50%) betekent niet noodzakelijkerwijs dat LUIS weinig vertrouwen heeft. Het leveren van meer trainings gegevens kan helpen om de [Score](luis-concept-prediction-score.md) van de meest waarschijnlijke intentie te verg Roten.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Waarom zie ik mijn eindpunt treffers in het dash board van mijn app niet?
Het totale aantal treffers in het dash board van uw app wordt regel matig bijgewerkt, maar de metrische gegevens die zijn gekoppeld aan uw LUIS-eindpunt sleutel in de Azure Portal, worden vaker bijgewerkt.

Als er geen bijgewerkte eindpunt treffers in het dash board worden weer gegeven, meldt u zich aan bij de Azure Portal, zoekt u de resource die is gekoppeld aan uw LUIS-eindpunt sleutel en opent u **metrische gegevens** om het **totale aantal aanroepen** te selecteren. Als de eindpunt sleutel wordt gebruikt voor meer dan één LUIS-app, wordt in de waarde in het Azure Portal het cumulatieve aantal aanroepen weer gegeven van alle LUIS-apps die deze gebruiken.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>Is er een Power shell-opdracht die naar het eindpunt quotum gaat?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

U kunt een Power shell-opdracht gebruiken om het eind punt quotum te bekijken:

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
```

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>Mijn LUIS-app werkte gisteren, maar nu krijg ik 403 fouten. Ik heb de app niet gewijzigd. Hoe kan ik dit probleem oplossen?
Volg deze [instructies](#how-do-i-create-and-assign-a-luis-endpoint-key) voor het maken van een Luis-eindpunt sleutel en wijs deze toe aan de app. Vervolgens moet u de HTTP-aanvraag van de client toepassing wijzigen in het eind punt om [de nieuwe eindpunt sleutel te gebruiken](luis-how-to-azure-subscription.md). Als u een nieuwe resource hebt gemaakt in een andere regio, wijzigt u de regio van de aanvraag voor de HTTP-client.

### <a name="how-do-i-secure-my-luis-endpoint"></a>Hoe kan ik mijn LUIS-eind punt beveiligen?
Zie [het eind punt beveiligen](luis-how-to-azure-subscription.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>Binnen LUIS-limieten werken

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Wat is het maximum aantal intents en entiteiten dat een LUIS-app kan ondersteunen?
Zie de [begrenzings](luis-limits.md) verwijzing.

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Ik wil een LUIS-app bouwen met meer dan het maximale aantal intenties. Wat moet ik doen?

Bekijk [Aanbevolen procedures voor intents](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Ik wil een app maken in LUIS met meer dan het maximum aantal entiteiten. Wat moet ik doen?

[Aanbevolen procedures voor entiteiten](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities) bekijken

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Wat zijn de limieten voor het aantal en de grootte van woordgroepen lijsten?
Zie de verwijzing [grenzen](luis-limits.md) voor de maximum lengte van een [woordgroepen lijst](./luis-concept-feature.md).

### <a name="what-are-the-limits-on-example-utterances"></a>Wat zijn de limieten voor voorbeeld uitingen?
Zie de [begrenzings](luis-limits.md) verwijzing.

## <a name="testing-and-training"></a>Testen en trainen

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Ik zie enkele fouten in het deel venster Batch testen voor een aantal modellen in mijn app. Hoe kan ik dit probleem oplossen?

De fouten geven aan dat er een verschil is tussen de labels en de voor spellingen van uw modellen. Voer een van de volgende taken uit om het probleem op te lossen:
* Voeg meer labels toe om LUIS te helpen verbeteren.
* Als u LUIS meer wilt weten, voegt u woordgroepen lijst functies toe die een serverspecifieke vocabulaire opleveren.

Zie de zelf studie over [batch testen](./luis-how-to-batch-test.md) .

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Wanneer een app wordt geëxporteerd en vervolgens opnieuw in een nieuwe app (met een nieuwe app-ID) wordt geïmporteerd, zijn de LUIS-Voorspellings scores verschillend. Waarom gebeurt dit?

Zie [Voorspellings verschillen tussen exemplaren van dezelfde app](luis-concept-prediction-score.md#review-intents-with-similar-scores).

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Sommige uitingen gaan naar het verkeerde doel nadat ik wijzigingen in mijn app heb aangebracht. Het probleem lijkt wille keurig te verdwijnen. Hoe kan ik dit probleem oplossen?

Bekijk [de trein met alle gegevens](luis-how-to-train.md#train-with-all-data).

## <a name="app-publishing"></a>App-publicatie

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Wat is de Tenant-ID in het venster ' een sleutel toevoegen aan uw app '?
In azure vertegenwoordigt een Tenant de client of organisatie die aan een service is gekoppeld. Zoek uw Tenant-id in de Azure Portal in het vak **Directory-id** door **Azure Active Directory**  >    >  **Eigenschappen** beheren te selecteren.

![Tenant-ID in de Azure Portal](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Waarom zijn er meer eindpunt sleutels toegewezen aan mijn app dan ik heb toegewezen?
Elke LUIS-app heeft de ontwerp/starter sleutel in de lijst met eind punten als gemak. Met deze sleutel kunnen slechts enkele eindpunt treffers worden uitgevoerd, zodat u LUIS kunt uitproberen.

Als uw app bestond voordat LUIS algemeen beschikbaar is (GA), worden LUIS-eindpunt sleutels in uw abonnement automatisch toegewezen. Dit is gedaan om de GA-migratie gemakkelijker te maken. Nieuwe LUIS-eindpunt sleutels in de Azure Portal worden _niet_ automatisch toegewezen aan Luis.

## <a name="key-management"></a>Sleutelbeheer

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Hoe kan ik weet welke sleutel ik nodig heb, waar ik deze vind?

Zie [ontwerp-en query Voorspellings eindpunt sleutels in Luis](luis-how-to-azure-subscription.md) voor meer informatie over de verschillen tussen de ontwerp sleutel en de Voorspellings runtime sleutel.

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Ik heb een fout melding ontvangen over het verlopen van het quotum. Hoe kan ik dit probleem oplossen?

Zie HTTP-status code [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) en [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) voor meer informatie.

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>Ik moet meer eindpunt query's verwerken. Hoe doe ik dat?

Zie HTTP-status code [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) en [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) voor meer informatie.

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>Ik heb een ontwerp sleutel gemaakt, maar deze wordt niet weer gegeven in de LUIS-Portal. Wat is er gebeurd?

Bewerkings sleutels zijn beschikbaar in de LUIS-Portal na [de migratie naar de ervaring voor de ontwerp sleutel](luis-migration-authoring.md).

## <a name="app-management"></a>Appbeheer

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Hoe kan ik een logboek van de uitingen van de gebruiker downloaden?
Uw LUIS-app registreert standaard uitingen van gebruikers. Als u een logboek van uitingen wilt downloaden dat gebruikers naar uw LUIS-app verzenden, gaat u naar **mijn apps** en selecteert u de app. Selecteer op de contextuele werk balk de optie **eindpunt logboeken exporteren**. Het logboek wordt opgemaakt als een bestand met door komma's gescheiden waarden (CSV).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Hoe kan ik de logboek registratie van uitingen uitschakelen?
U kunt de logboek registratie van de gebruikers uitingen uitschakelen door `log=false` in te stellen in de eind punt-URL die door uw client toepassing wordt gebruikt voor het opvragen van Luis. Door logboek registratie uit te scha kelen, wordt de mogelijkheid van uw LUIS-app echter uitgeschakeld om uitingen te suggereren of de prestaties te verbeteren op basis van [actief leren](luis-concept-review-endpoint-utterances.md#what-is-active-learning). Als u `log=false` vanwege problemen met de privacy hebt ingesteld, kunt u geen record van die gebruiker uitingen downloaden van Luis of deze uitingen gebruiken om uw app te verbeteren.

Logboek registratie is de enige opslag van uitingen.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Waarom wilt u niet alle mijn eind punt uitingen in het logboek registreren?
Als u uw logboek gebruikt voor Voorspellings analyse, moet u geen test uitingen vastleggen in het logboek.

## <a name="data-management"></a>Gegevensbeheer

### <a name="can-i-delete-data-from-luis"></a>Kan ik gegevens verwijderen uit LUIS?

* U kunt altijd een voor beeld uitingen gebruiken dat wordt gebruikt voor training LUIS. Als u een voorbeeld utterance uit uw LUIS-app verwijdert, wordt deze verwijderd uit de LUIS-webservice en is deze niet beschikbaar voor export.
* U kunt uitingen verwijderen uit de lijst met gebruikers uitingen die LUIS worden voorgesteld op de pagina **eind punt controleren uitingen** . Als u uitingen uit deze lijst verwijdert, kunnen ze niet worden voorgesteld, maar worden ze niet verwijderd uit de logboeken.
* Als u een account verwijdert, worden alle apps verwijderd, samen met hun voor beeld-uitingen en-Logboeken. De gegevens worden gedurende 60 dagen op de servers bewaard voordat deze permanent worden verwijderd.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>Hoe beheert micro soft gegevens die ik verzend naar LUIS?

In het [vertrouwens centrum](https://www.microsoft.com/trustcenter) worden onze verplichtingen en opties voor gegevens beheer en-toegang in Azure-Services uitgelegd.

## <a name="language-and-translation-support"></a>Ondersteuning voor talen en vertalingen

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Ik heb een app in één taal en wil een parallelle app maken in een andere taal. Wat is de eenvoudigste manier om dit te doen?
1. Exporteer uw app.
2. Vertaal de gelabelde uitingen in het JSON-bestand van de geëxporteerde app naar de doel taal.
3. Mogelijk moet u de namen van de intenties en entiteiten wijzigen of ze laten staan.
4. Importeer ten slotte de app voor een LUIS-app in de doel taal.

## <a name="app-notification"></a>App-melding

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Waarom krijg ik een e-mail met de melding dat er bijna geen quotum is?
Uw ontwerp/starter sleutel is alleen toegestaan 1000-eind punt query's per maand. Maak een LUIS-eindpunt sleutel (gratis of betaald) en gebruik deze sleutel bij het maken van eindpunt query's. Als u eindpunt query's van een bot of een andere client toepassing maakt, moet u de LUIS-eindpunt sleutel daar wijzigen.

## <a name="bots"></a>Bots

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>Mijn LUIS-bot werkt niet. Wat moet ik doen?

De eerste fout is het isoleren als het probleem betrekking heeft op LUIS of zich buiten de LUIS-middleware bevindt.

#### <a name="resolve-issue-in-luis"></a>Probleem oplossen in LUIS
Geef dezelfde utterance door aan LUIS van het [Luis-eind punt](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint). Als u een fout bericht ontvangt, lost u het probleem op in LUIS totdat de fout niet meer wordt geretourneerd. Veelvoorkomende fouten zijn onder andere:

* `Out of call volume quota. Quota will be replenished in <time>.` : Dit probleem geeft aan dat u moet overschakelen van een ontwerp sleutel naar een [eindpunt sleutel](luis-how-to-azure-subscription.md) of dat u de [service lagen](luis-how-to-azure-subscription.md#change-the-pricing-tier)moet wijzigen.

#### <a name="resolve-issue-in-azure-bot-service"></a>Probleem in Azure Bot Service oplossen

Als u de Azure Bot Service gebruikt en het probleem is dat de **test in Web Chat** als resultaat wordt gegeven `Sorry, my bot code is having an issue` , controleert u de logboeken:

1. In de Azure Portal, voor uw bot, in de sectie **bot Management** , selecteert u **Build**.
1. Open de online code-editor.
1. Selecteer in de bovenste blauwe navigatie balk de naam van de bot (het tweede item aan de rechter kant).
1. Selecteer in de vervolg keuzelijst resulterende **kudu-console openen**.
1. Selecteer **Logboeken** en selecteer vervolgens **toepassing**. Controleer alle logboek bestanden. Als u de fout niet in de toepassingsmap ziet, controleert u alle logboek bestanden onder **Logboeken**.
1. Vergeet niet om uw project opnieuw samen te stellen als u een gecompileerde taal gebruikt, zoals C#.

> [!Tip]
> De console kan ook pakketten installeren.

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Los het probleem op bij het opsporen van fouten op een lokale computer met bot Framework.

Zie [fouten opsporen in een bot](/azure/bot-service/bot-service-debug-bot)voor meer informatie over de lokale fout opsporing van een bot.

## <a name="integrating-luis"></a>LUIS integreren

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Waar is mijn LUIS-app gemaakt tijdens het proces Azure web app-bot?
Als u een LUIS-sjabloon selecteert en de knop **selecteren** in het deel venster sjabloon selecteert, wordt het sjabloon type in het deel venster aan de linkerkant gewijzigd en wordt u gevraagd in welke regio u de sjabloon Luis wilt maken. Het web app-bot proces maakt echter geen LUIS-abonnement.

![LUIS-sjabloon web-app-bot](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Welke LUIS-regio's ondersteunen bot Framework speech gebeuren?
[Speech gebeuren](/bot-framework/bot-service-manage-speech-priming) wordt alleen ondersteund voor Luis-apps in het centrale exemplaar (US).

## <a name="api-programming-strategies"></a>API-programmeer strategieën

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Hoe kan ik de regio LUIS van een resource programmatisch ophalen?

Gebruik het LUIS-voor beeld om de regio programmatisch te [zoeken](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) met C# of Node.Js.

## <a name="luis-service"></a>LUIS-service

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>Is Language Understanding (LUIS) on-premises of in de privécloud beschikbaar?

Ja, u kunt de LUIS- [container](luis-container-howto.md) voor deze scenario's gebruiken als u de benodigde connectiviteit voor het gebruik van metingen hebt.

## <a name="migrating-to-the-next-version"></a>Migreren naar de volgende versie

### <a name="how-do-i-migrate-to-preview-v3-api"></a>Hoe kan ik migreren naar Preview v3 API?

Zie [API v2 naar v3 migratie handleiding voor Luis-apps](luis-migration-api-v3.md)

## <a name="build-2019-conference-announcements"></a>Build 2019-conferentie aankondigingen

De volgende functies zijn uitgebracht op de Build 2019 Conference:

* [Preview van de V3 API-migratiehandleiding](luis-migration-api-v3.md)
* [Verbeterd analysedashboard](luis-how-to-use-dashboard.md)
* [Verbeterde vooraf gemaakte domeinen](luis-reference-prebuilt-domains.md)
* [Dynamische lijstentiteiten](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Externe entiteiten](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

Video's:

* [Gespreks-AI van Azure gebruiken om uw bedrijf te schalen voor de volgende generatie](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende bronnen voor meer informatie over LUIS:
* [Stack Overflow vragen die zijn gelabeld met LUIS](https://stackoverflow.com/questions/tagged/luis)
* [Micro soft Q&een vraag pagina voor MSDN Language Understanding intelligent Services (LUIS)](/answers/topics/azure-language-understanding.html)
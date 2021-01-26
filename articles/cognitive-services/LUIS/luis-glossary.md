---
title: Woorden lijst-LUIS
description: In de woorden lijst worden de termen beschreven die u kunt tegen komen tijdens het werken met de LUIS API-service.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/08/2020
ms.openlocfilehash: 7c65c8272172cab9f5361d16141bf7b229037480
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98786941"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Taal informatie over woorden lijst met veelgestelde woorden en concepten
In de woorden lijst Language Understanding (LUIS) worden de termen beschreven die u kunt tegen komen tijdens het werken met de LUIS-service.

## <a name="active-version"></a>Actieve versie

De actieve versie is de [versie](luis-how-to-manage-versions.md) van uw app die wordt bijgewerkt wanneer u wijzigingen aanbrengt in het model met behulp van de Luis-Portal. Als u in de LUIS-Portal wijzigingen wilt aanbrengen in een versie die niet de actieve versie is, moet u deze versie eerst instellen als actief.

## <a name="active-learning"></a>Actief leren

Actief leren is een techniek van machine learning waarin het model van de computer wordt gebruikt om informatieve nieuwe voor beelden te identificeren. In LUIS verwijst actief Learning naar het toevoegen van uitingen van het eindpunt verkeer waarvan de huidige voor spellingen onduidelijk zijn om uw model te verbeteren. Klik op eind punt uitingen controleren om uitingen te bekijken.

Zie ook:
* [Conceptuele informatie](luis-concept-review-endpoint-utterances.md)
* [Zelf studie evaluatie van eind punt uitingen](luis-tutorial-review-endpoint-utterances.md)
* De LUIS-app verbeteren door [eind punt uitingen te controleren](luis-how-to-review-endpoint-utterances.md)

## <a name="application-app"></a>Toepassing (app)

In LUIS is uw toepassing of app een verzameling geleerde modellen, gebouwd op dezelfde gegevensset, die samen werken om intenten en entiteiten voor een bepaald scenario te voors pellen. Elke toepassing heeft een afzonderlijk Voorspellings eindpunt.

Als u een HR-bot bouwt, hebt u mogelijk een set van intenties, zoals "verlof tijd plannen", "informeren over voor delen" en "persoonlijke gegevens bijwerken" en entiteiten voor elk van de intenties die u in één toepassing groepeert.

## <a name="authoring"></a>Ontwerpen

Ontwerpen is de mogelijkheid om een LUIS-app te maken, te beheren en te implementeren met behulp van de LUIS-portal of de ontwerp-Api's.

### <a name="authoring-key"></a>Sleutel ontwerpen

De [ontwerp sleutel](luis-how-to-azure-subscription.md) wordt gebruikt om de app te ontwerpen. Niet gebruikt voor eindpunt query's op productie niveau. Zie [sleutel limieten](luis-limits.md#key-limits)voor meer informatie.

### <a name="authoring-resource"></a>Resource ontwerpen

Uw LUIS- [ontwerp bron](luis-how-to-azure-subscription.md#azure-resources-for-luis) is een beheerbaar item dat beschikbaar is via Azure. De resource is uw toegang tot de bijbehorende ontwerp-, trainings-en publicatie mogelijkheden van de Azure-service. De resource bevat verificatie-, autorisatie-en beveiligings informatie die u nodig hebt om toegang te krijgen tot de gekoppelde Azure-service.

De ontwerp resource heeft een ' soort ' van Azure `LUIS-Authoring` .

## <a name="batch-test"></a>Batch-test

Batch tests zijn de mogelijkheid om de modellen van de huidige LUIS-app te valideren met een consistente en bekende testset van gebruikers uitingen. De batch test is gedefinieerd in een [JSON-bestand](./luis-how-to-batch-test.md#batch-test-file).


Zie ook:
* [Concepten](./luis-how-to-batch-test.md)
* Een batch [test uitvoeren](luis-how-to-batch-test.md)
* [Zelf studie](./luis-how-to-batch-test.md) -een batch test maken en uitvoeren

### <a name="f-measure"></a>F-meting

Bij het testen van batches wordt de nauw keurigheid van de test gemeten.

### <a name="false-negative-fn"></a>ONWAAR negatief (FN)

In batch tests vertegenwoordigen de gegevens punten uitingen waarin de afwezigheid van het doel intentie/entiteit onjuist is voor speld door uw app.

### <a name="false-positive-fp"></a>Onjuist positief (FP)

In batch tests vertegenwoordigen de gegevens punten uitingen waarin de app het bestaan van de doel intentie/entiteit onjuist heeft voor speld.

### <a name="precision"></a>Precisie
In batch testen is precisie (ook wel positieve Voorspellings waarde genoemd) de Fractie van relevante uitingen van de opgehaalde uitingen.

Een voor beeld van een dierlijke batch test is het aantal schapen dat is voor speld, gedeeld door het totale aantal dieren (schaap en niet-schaap).

### <a name="recall"></a>Relevante overeenkomsten

In batch testen, intrekken (ook wel gevoeligheid genoemd), is de mogelijkheid om LUIS te generaliseren.

Een voor beeld van een dierlijke batch test is het aantal schapen dat is voor speld, gedeeld door het totale aantal beschik bare schapen.

### <a name="true-negative-tn"></a>Waar negatief (TN)

Een echte negatieve waarde is wanneer uw app op de juiste wijze geen overeenkomst voor spelt. Bij het uitvoeren van batch tests treedt er een fout op wanneer uw app een intentie of entiteit voor spelt voor een voor beeld dat niet is gelabeld met die intentie of entiteit.

### <a name="true-positive-tp"></a>True positief (TP)

True positief (TP) een echte positieve waarde is wanneer uw app een overeenkomst correct voor spelt. Bij het uitvoeren van batch tests treedt een echte positieve situatie op wanneer uw app voor spelt voor een intentie of entiteit voor een voor beeld dat is gelabeld met die intentie of entiteit.

## <a name="classifier"></a>Classificatie

Een classificatie is een door de machine geleerde model waarmee wordt gedicteerd op welke categorie of klasse een invoer past.

Een [intentie](#intent) is een voor beeld van een classificatie.

## <a name="collaborator"></a>Samenwerker

Een samen werker is conceptueel hetzelfde als een [mede werker](#contributor). Aan een samen werker wordt toegang verleend wanneer een eigenaar het e-mail adres van de mede werker toevoegt aan een app die niet wordt beheerd met Azure op rollen gebaseerd toegangs beheer (Azure RBAC). Als u nog steeds deel nemers gebruikt, moet u uw LUIS-account migreren en LUIS-ontwerp resources gebruiken om inzenders te beheren met Azure RBAC.

## <a name="contributor"></a>Inzender

Een bijdrager is niet de [eigenaar](#owner) van de app, maar heeft dezelfde machtigingen voor het toevoegen, bewerken en verwijderen van de intenties, entiteiten, uitingen. Een Inzender biedt op rollen gebaseerd toegangs beheer van Azure (Azure RBAC) voor een LUIS-app.

Zie ook:
* [](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource) Mede werkers toevoegen

## <a name="descriptor"></a>Descriptor

Een descriptor is de term die voorheen werd gebruikt voor een machine learning- [functie](#features).

## <a name="domain"></a>Domain

In de context van LUIS is een domein een kennis gebied. Uw domein is specifiek voor uw scenario. Verschillende domeinen gebruiken een specifieke taal en terminologie die in de context van het domein betekenis heeft. Als u bijvoorbeeld een toepassing bouwt voor het afspelen van muziek, heeft uw toepassing voor waarden en talen die specifiek zijn voor muziek: woorden als "nummer, track, album, Song teksten, b-zijde, artiest". Zie voor voor beelden van domeinen [vooraf ontwikkelde domeinen](#prebuilt-domain).

## <a name="endpoint"></a>Eindpunt

### <a name="authoring-endpoint"></a>Eind punt ontwerpen

De LUIS-ontwerp eind punt-URL is de locatie waar u uw App ontwerpt, traint en publiceert. De eind punt-URL bevat de regio of het aangepaste subdomein van de gepubliceerde app en de App-ID.

Meer informatie over het programmatisch ontwerpen van uw app vanuit de [referentie voor ontwikkel aars](developer-reference-resource.md#rest-endpoints)

### <a name="prediction-endpoint"></a>Voorspellings eindpunt

De LUIS-eind punt-URL is de plaats waar u LUIS query's verzendt nadat de [Luis-app](#application-app) is gemaakt en gepubliceerd. De eind punt-URL bevat de regio of het aangepaste subdomein van de gepubliceerde app en de App-ID. U vindt het eind punt op de pagina met **[Azure-resources](luis-how-to-azure-subscription.md)** van uw app of u kunt de eind punt-URL ophalen uit de informatie-API voor het [ophalen van apps](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) .

Uw toegang tot het Voorspellings eindpunt is geautoriseerd met de LUIS-Voorspellings sleutel.

## <a name="entity"></a>Entiteit

[Entiteiten](luis-concept-entity-types.md) zijn woorden in uitingen die informatie beschrijven die wordt gebruikt om een intentie te voldoen of te identificeren. Als uw entiteit complex is en u wilt dat uw model specifieke onderdelen identificeert, kunt u uw model in subentiteiten opdelen. U kunt bijvoorbeeld model leren om een adres te voors pellen, maar ook de subentiteiten van straat, plaats, provincie en ZipCode. Entiteiten kunnen ook worden gebruikt als onderdelen voor modellen. Uw antwoord van de LUIS-app bevat zowel voorspelde intenten als alle entiteiten.

### <a name="entity-extractor"></a>Entiteits-Extractor

Een entiteits-Extractor die soms alleen bekend staat als Extractor, is het type van het geleerde computer model dat LUIS gebruikt om entiteiten te voors pellen.

### <a name="entity-schema"></a>Entiteits schema

Het entiteits schema is de structuur die u definieert voor door de machine geleerde entiteiten met subentiteiten. Het Voorspellings eindpunt retourneert alle geëxtraheerde entiteiten en subentiteiten die in het schema zijn gedefinieerd.

### <a name="entitys-subentity"></a>Subentiteit van de entiteit

Een subentiteit is een onderliggende entiteit van een machine learning-entiteit.

### <a name="non-machine-learning-entity"></a>Niet-machine learning-entiteit

Een entiteit die gebruikmaakt van tekst matching om gegevens te extra heren:
* Lijstentiteit
* Een entiteit in de vorm van een reguliere expressie

### <a name="list-entity"></a>Lijstentiteit

Een [lijst entiteit](reference-entity-list.md) vertegenwoordigt een vaste, gesloten set verwante woorden samen met hun synoniemen. Lijst entiteiten zijn exacte overeenkomsten, in tegens telling tot gewerkte geleerde entiteiten.

De entiteit wordt voor speld als een woord in de lijst entiteit is opgenomen in de lijst. Als u bijvoorbeeld een lijst entiteit met de naam ' size ' hebt en u de woorden ' klein, normaal, groot ' in de lijst hebt, wordt de entiteit grootte voor speld voor alle uitingen waarbij de woorden ' kleine ', ' medium ' of ' groot ' worden gebruikt, ongeacht de context.

### <a name="regular-expression"></a>Reguliere expressie

Een [reguliere expressie-entiteit](reference-entity-regular-expression.md) vertegenwoordigt een reguliere expressie. Reguliere expressie-entiteiten zijn exacte overeenkomsten, in tegens telling tot gewerkte geleerde entiteiten.
### <a name="prebuilt-entity"></a>Vooraf gebouwde entiteit

Bekijk de invoer van het vooraf ontwikkelde model voor de [vooraf samengestelde entiteit](#prebuilt-entity)

## <a name="features"></a>Functies

In machine learning is een functie een kenmerk waarmee het model een bepaald concept kan herkennen. Het is een hint die LUIS kan gebruiken, maar geen vaste regel.

Deze term wordt ook wel een **[machine learning-functie](luis-concept-feature.md)** genoemd.

Deze hints worden gebruikt in combi natie met de labels voor meer informatie over het voors pellen van nieuwe gegevens. LUIS ondersteunt beide woordgroepen lijsten en het gebruik van andere modellen als onderdelen.

### <a name="required-feature"></a>Vereiste functie

Een vereiste functie is een manier om de uitvoer van een LUIS-model te beperken. Wanneer een functie voor een entiteit is gemarkeerd als vereist, moet de functie aanwezig zijn in het voor beeld zodat de entiteit kan worden voor speld, ongeacht wat de voor spellingen van het model van de machine heeft geleerd.

Bekijk een voor beeld waarin u een vooraf ontwikkelde functie hebt die u hebt gemarkeerd als vereist voor de entiteit hoeveelheid voor een bot menu volgorde. Wanneer uw bot ziet `I want a bajillion large pizzas?` , wordt bajillion niet voorspeld als hoeveelheid, ongeacht de context waarin deze wordt weer gegeven. Bajillion is geen geldig nummer en wordt niet voor speld door de vooraf gemaakte entiteit number.

## <a name="intent"></a>Intentie

Een [intentie](luis-concept-intent.md) vertegenwoordigt een taak of actie die de gebruiker wil uitvoeren. Het is een doel of doel dat wordt weer gegeven in de invoer van een gebruiker, zoals het reserveren van een vlucht of het betalen van een factuur. In LUIS wordt een utterance als geheel geclassificeerd als intentie, maar delen van de utterance worden geëxtraheerd als entiteiten

## <a name="labeling-examples"></a>Voor beelden van labels

Labelen of markeren is het proces van het koppelen van een positief of negatief voor beeld met een model.

### <a name="labeling-for-intents"></a>Labels voor intenties
In LUIS zijn de intenten in een app wederzijds uitsluitend van elkaar. Dit betekent dat wanneer u een utterance aan een intentie toevoegt, het een _positief_ voor beeld voor dat doel en een _negatief_ voor beeld voor alle andere intenties wordt beschouwd. Negatieve voor beelden mogen niet worden verward met de intentie ' geen ', die uitingen vertegenwoordigt die buiten het bereik van de app vallen.

### <a name="labeling-for-entities"></a>Labelen voor entiteiten
In LUIS [labelt](label-entity-example-utterance.md) u een woord of woord groep in het voor beeld van een intentie utterance met een entiteit als een _positief_ voor beeld. Labelen toont de intentie waarvan het voor speld moet worden voor die utterance. De gelabelde uitingen worden gebruikt om de intentie te trainen.

## <a name="luis-app"></a>LUIS-app

Zie de definitie voor de [toepassing (app)](#application-app).

## <a name="model"></a>Model

A (machine geleerd) is een functie die een voor spelling van invoer gegevens maakt. In LUIS verwijzen we naar intentie-classificaties en entiteits uittreksels algemeen als ' modellen ' en verwijzen we naar een verzameling modellen die zijn getraind, gepubliceerd en samen met een ' app '.

## <a name="normalized-value"></a>Genormaliseerde waarde

U voegt waarden aan uw [lijst](#list-entity) entiteiten toe. Elk van deze waarden kan een lijst met een of meer synoniemen hebben. Alleen de genormaliseerde waarde wordt geretourneerd in het antwoord.

## <a name="overfitting"></a>Overfitting

De overmontage vindt plaats wanneer het model wordt aan de specifieke voor beelden en niet goed kan worden gegeneraliseerd.

## <a name="owner"></a>Eigenaar

Elke app heeft één eigenaar die de app heeft gemaakt. De eigenaar beheert de machtigingen voor de toepassing in de Azure Portal.

## <a name="phrase-list"></a>Woordgroepen lijst

Een [woordgroepen lijst](luis-concept-feature.md) is een specifiek type machine learning functie dat een groep waarden (woorden of zinsdelen) bevat die deel uitmaken van dezelfde klasse en die op dezelfde manier moet worden behandeld (bijvoorbeeld namen van steden of producten).

## <a name="prebuilt-model"></a>Vooraf gebouwd model

Een [vooraf gebouwd model](luis-concept-prebuilt-model.md) is een intentie, entiteit of verzameling van beide, samen met gelabelde voor beelden. Deze algemene vooraf gemaakte modellen kunnen worden toegevoegd aan uw app om het ontwerp van modellen te verminderen dat vereist is voor uw app.

### <a name="prebuilt-domain"></a>Vooraf gebouwd domein

Een vooraf gebouwd domein is een LUIS-app die is geconfigureerd voor een specifiek domein, zoals Home Automation (HomeAutomation) of restaurant reserveringen (RestaurantReservation). De intenties, uitingen en entiteiten zijn geconfigureerd voor dit domein.

### <a name="prebuilt-entity"></a>Vooraf gebouwde entiteit

Een vooraf samengestelde entiteit is een entiteit LUIS biedt algemene typen informatie, zoals Number, URL en e-mail. Deze worden gemaakt op basis van open bare gegevens. U kunt ervoor kiezen om een vooraf samengestelde entiteit toe te voegen als een zelfstandige entiteit, of als een functie aan een entiteit

### <a name="prebuilt-intent"></a>Vooraf gebouwde intentie

Een vooraf ontwikkelde intentie is een intentie LUIS biedt algemene soorten informatie en wordt geleverd met een eigen gelabeld voor beeld uitingen.

## <a name="prediction"></a>Voorspelling

Een voor spelling is een REST-aanvraag voor de Azure LUIS prediction-service die nieuwe gegevens (gebruiker utterance) neemt, en past de getrainde en gepubliceerde toepassing toe op die gegevens om te bepalen welke intenties en entiteiten worden gevonden.

### <a name="prediction-key"></a>Voorspellings sleutel

De [Voorspellings sleutel](luis-how-to-azure-subscription.md) (voorheen bekend als de sleutel abonnement) is de sleutel die is gekoppeld aan de Luis-service die u in azure hebt gemaakt en die uw gebruik van het prediction-eind punt toestaat.

Deze sleutel is niet de ontwerp sleutel. Als u een voor spelling-eindpunt sleutel hebt, moet deze worden gebruikt voor alle eindpunt aanvragen in plaats van de ontwerp sleutel. U kunt de huidige Voorspellings sleutel weer geven binnen de eind punt-URL onder aan de pagina Azure-resources in de LUIS-website. Het is de waarde van de naam/waarde-paar van het abonnement.

### <a name="prediction-resource"></a>Voorspellingsresource

Uw LUIS-Voorspellings resource is een beheerbaar item dat beschikbaar is via Azure. De resource is uw toegang tot de bijbehorende voor spelling van de Azure-service. De resource bevat voor spellingen.

De Voorspellings resource heeft een ' soort ' van Azure `LUIS` .

### <a name="prediction-score"></a>Voorspellingsscore

De [Score](luis-concept-prediction-score.md) is een getal tussen 0 en 1 dat een meting is van het systeem dat een bepaalde invoer utterance overeenkomt met een bepaald doel. Een score dichter bij 1 betekent dat het systeem zeer betrouwbaar is over de uitvoer en een score dichter bij 0 betekent dat het systeem zeker weet dat de invoer niet overeenkomt met een bepaalde uitvoer. Scores in het midden betekent dat het systeem zeer onzeker weet hoe u de beslissing kunt nemen.

Neem bijvoorbeeld een model dat wordt gebruikt om te bepalen of bepaalde klant tekst een levens order bevat. Het kan een Score van 1 geven voor ' Ik wil een koffie best Ellen ' (het systeem is zeer zeker dat dit een bestelling is) en een Score van 0 voor ' mijn team heeft de afgelopen avond gewonnen ' (het systeem is zeker dat dit geen bestelling is). En het kan een Score van 0,5 hebben voor ' laten we een aantal thee ' (niet zeker als dit een bestelling is of niet).

## <a name="programmatic-key"></a>Programmatische sleutel

De naam van de [ontwerp sleutel](#authoring-key)is gewijzigd.

## <a name="publish"></a>Publiceren

[Publiceren](luis-how-to-publish-app.md) houdt in dat er een Luis actieve versie beschikbaar is op het staging-of productie- [eind punt](#endpoint).

## <a name="quota"></a>Quota

LUIS quota is de beperking van de Azure-abonnementweergave. Het quotum voor de LUIS kan worden beperkt door beide aanvragen per seconde (HTTP-status 429) en in totaal aantal aanvragen in een maand (HTTP-status 403).

## <a name="schema"></a>Schema

Uw schema bevat uw intentie en entiteiten samen met de subentiteiten. Het schema wordt in eerste instantie gepland voor en vervolgens na verloop van tijd. Het schema bevat geen app-instellingen, functies of voor beeld-uitingen.

## <a name="sentiment-analysis"></a>Sentimentanalyse
Sentiment analyse biedt positieve of negatieve waarden van de uitingen die worden geleverd door [Text Analytics](../text-analytics/overview.md).

## <a name="speech-priming"></a>Spraak gebeuren

Speech gebeuren verbetert de herkenning van gesp roken woorden en zinsdelen die veel worden gebruikt in uw scenario met [spraak Services](../speech-service/overview.md). Voor speech gebeuren-toepassingen worden alle voor beelden met LUIS-labels gebruikt om de nauw keurigheid van spraak herkenning te verbeteren door een aangepast spraak model te maken voor deze specifieke toepassing. In een schaak spel wilt u er bijvoorbeeld voor zorgen dat als de gebruiker ' verplaatsen ' Knight ' wordt genoemd, dit niet wordt geïnterpreteerd als ' nacht avond '. De LUIS-app moet voor beelden bevatten waarin "Knight" als entiteit is gemarkeerd.

## <a name="starter-key"></a>Start sleutel

Een gratis sleutel die moet worden gebruikt voor het eerst op basis van LUIS.

## <a name="synonyms"></a>Synoniemen

In LUIS [lijst entiteiten](reference-entity-list.md)kunt u een genormaliseerde waarde maken, die elk een lijst met synoniemen kan hebben. Als u bijvoorbeeld een grootte-entiteit maakt die genormaliseerde waarden van klein, gemiddeld, groot en extra groot heeft. U kunt als volgt synoniemen maken voor elke waarde:

|Waarde Nomalized| Synoniemen|
|--|--|
|Klein| de kleine, 8 ounce|
|Normaal| normaal, 12 ounce|
|Groot| Big, 16 ounce|
|Xtra groot| de grootste, 24 ounce|

Het model retourneert de genormaliseerde waarde voor de entiteit wanneer er synoniemen worden weer gegeven in de invoer.

## <a name="test"></a>Testen

Als u een LUIS-app [test](luis-concept-test.md) , worden de voor spellingen van modellen weer gegeven.

## <a name="timezone-offset"></a>Verschuiving van tijd zone

Het eind punt bevat [time zone offset](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). Dit is het aantal minuten dat u wilt toevoegen aan of verwijderen uit de datetimeV2-vooraf gedefinieerde entiteit. Als de utterance bijvoorbeeld ' wat is de tijd is? ' is, is de geretourneerde datetimeV2 de huidige tijd voor de client aanvraag. Als uw client aanvraag afkomstig is van een bot of een andere toepassing die niet dezelfde is als de gebruiker van uw bot, moet u de verschuiving tussen de bot en de gebruiker door geven.

Zie de [tijd zone van de vooraf gemaakte datetimeV2-entiteit wijzigen](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Token
Een [token](luis-language-support.md#tokenization) is de kleinste tekst eenheid die door Luis kan worden herkend. Dit wijkt enigszins af van de verschillende talen.

Voor **Engels** is een token een doorlopende periode (geen spaties of lees tekens) van letters en cijfers. Een spatie is geen token.

|Woordgroep|Aantal tokens|Uitleg|
|--|--|--|
|`Dog`|1|Eén woord zonder Lees tekens of spaties.|
|`RMT33W`|1|Een record locator-nummer. De waarde mag cijfers en letters bevatten, maar heeft geen interpunctie.|
|`425-555-5555`|5|Een telefoon nummer. Elk lees teken is een enkel token dat  `425-555-5555` 5 tokens zou zijn:<br>`425`<br>`-`<br>`555`<br>`-`<br>`5555` |
|`https://luis.ai`|7|`https`<br>`:`<br>`/`<br>`/`<br>`luis`<br>`.`<br>`ai`<br>|

## <a name="train"></a>Trainen

[Training](luis-how-to-train.md) is het proces van het onderwijs van Luis over eventuele wijzigingen in de actieve versie sinds de laatste training.

### <a name="training-data"></a>Trainingsgegevens

Trainings gegevens zijn de informatie die nodig is voor het trainen van een model. Dit omvat het schema, de gelabelde uitingen, onderdelen en toepassings instellingen.

### <a name="training-errors"></a>Trainings fouten

Trainings fouten zijn voor spellingen van uw trainings gegevens die niet overeenkomen met hun labels.

## <a name="utterance"></a>Uiting

Een [utterance](luis-concept-utterance.md) is gebruikers invoer die korte tekst representatief is voor een zin in een gesprek. Het is een term in natuurlijke taal, zoals ' Book 2-tickets naar Seattle Next dinsdag '. Voor beelden van uitingen worden toegevoegd om het model en de voor spellingen van het model te trainen op nieuwe utterance tijdens runtime

## <a name="version"></a>Versie

Een LUIS- [versie](luis-how-to-manage-versions.md) is een specifiek exemplaar van een Luis-toepassing die is gekoppeld aan een Luis-app-id en het gepubliceerde eind punt. Elke LUIS-app heeft ten minste één versie.

---
title: Waar en hoe u-Personaler kunt gebruiken
description: Personaler kan in elke situatie worden toegepast, waarbij uw toepassing het juiste item, de actie of het product kan selecteren om de ervaring te verbeteren, betere bedrijfs resultaten te leveren of de productiviteit te verbeteren.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 87179950c33a6facb59ce499aa0ae393e53fb37f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91777221"
---
# <a name="where-and-how-to-use-personalizer"></a>Waar en hoe persoonlijker gebruiken

Gebruik Personaler in elke situatie waarbij uw toepassing de juiste actie (inhoud) moet selecteren om de ervaring beter te maken, betere bedrijfs resultaten te krijgen of de productiviteit te verbeteren.

Persoonlijkere training gebruikt versterking van de actie (inhoud) om de gebruiker weer te geven. De selectie kan aanzienlijk variëren, afhankelijk van de hoeveelheid, de kwaliteit en de distributie van de gegevens die naar de service worden verzonden.

## <a name="example-use-cases-for-personalizer"></a>Voor beelden van use cases for Personaler

* **Verklaring van intentie &** ondubbelzinnigheid: Help uw gebruikers een betere ervaring te bieden wanneer hun intentie niet duidelijk is door een persoonlijke optie op te geven.
* **Standaard suggesties** voor menu's & opties: laat de bot het meest waarschijnlijke item op een gepersonaliseerde manier Voorst Ellen als een eerste stap, in plaats van een niet-persoonlijk menu of lijst met alternatieven te presen teren.
* **Bot-eigenschappen & Toon**: voor bots die de Toon, uitgebreidheid en schrijf stijl kunnen variëren, kunt u overwegen deze eigenschappen te variëren.
* **Inhoud van meldings & waarschuwing**: Beslis welke tekst u voor waarschuwingen wilt gebruiken om gebruikers meer te benaderen.
* **Timing van meldings & waarschuwing**: laat u weten wanneer u meldingen naar gebruikers wilt verzenden om ze te benaderen.


## <a name="expectations-required-to-use-personalizer"></a>Verwachtingen die vereist zijn voor het gebruik van Personaler

U kunt persoonlijker Toep assen in situaties waarin u aan de volgende richt lijnen voldoet of kan implementeren.

|Richtlijn|Uitleg|
|--|--|
|Zakelijk doel|U hebt een zakelijk of bruikbaar doel voor uw toepassing.|
|Content|U hebt een plaats in uw toepassing waar u een contextuele beslissing neemt om het doel van gebruikers te verbeteren.|
|Hoeveelheid inhoud|U hebt minder dan 50 acties om per oproep te rangschikken.|
|Statistische gegevens|De beste keuze kan en moeten worden geleerd van het gedrag van de collectieve gebruiker en de totale belonings Score.|
|Ethische gebruik|Het gebruik van machine learning voor personalisatie is gebaseerd op de [verantwoordelijke richt lijnen](ethics-responsible-use.md) en keuzes die u hebt gekozen.
|Beste enkelvoudige optie|De contextuele beslissing kan worden uitgedrukt als een classificatie van de beste optie (actie) van een beperkt aantal keuzes.|
|Resultaat van de Score|Hoe goed de geclassificeerde keuze voor uw toepassing kan worden bepaald door een aspect van het gedrag van de gebruiker te meten en deze in een _[belonings Score](concept-rewards.md)_ af te drukken.|
|Relevante timing|De belonings Score brengt niet te veel congevonden of externe factoren in rekening. De duur van het experiment is laag genoeg om de belonings score te berekenen, terwijl deze nog steeds relevant is.|
|Voldoende context functies|U kunt de context voor de rang orde als een lijst van ten minste vijf [functies](concepts-features.md) die u denkt wilt gebruiken om de juiste keuze te maken en die geen gebruikersspecifieke herken bare informatie bevat.|
|Voldoende actie functies|U hebt informatie over elke inhouds keuze, _actie_, als een lijst met ten minste vijf [functies](concepts-features.md) die u kunt gebruiken om de juiste keuze te maken.|
|Dagelijkse gegevens|Er zijn voldoende gebeurtenissen om optimaal te kunnen voldoen aan de optimale persoonlijke instellingen als het probleem zich in de loop van de tijd (zoals voor keuren in nieuws of mode) bevindt. Personaler wordt aangepast aan doorlopende wijzigingen in de echte wereld, maar de resultaten zijn niet optimaal als er niet voldoende gebeurtenissen en gegevens zijn om te leren van het detecteren en afrekenen van nieuwe patronen. U moet een use-case kiezen die vaak voldoende optreedt. Overweeg voor het zoeken naar use cases die ten minste 500 keer per dag plaatsvinden.|
|Historische gegevens|Uw toepassing kan gegevens lang genoeg bewaren om een geschiedenis van ten minste 100.000 interacties te verzamelen. Hierdoor kan Personaler voldoende gegevens verzamelen om offline-evaluaties en optimalisatie van het beleid uit te voeren.|

**Gebruik persoonlijke instellingen niet** als het persoonlijke gedrag niet iets is dat kan worden gedetecteerd voor alle gebruikers. Als u bijvoorbeeld met Personaler een voor beeld van een pizza-volg orde wilt gebruiken in een lijst met 20 mogelijke menu-items, is het nuttig, maar welke contact persoon moet worden aangeroepen vanuit de lijst met contact personen van de gebruiker wanneer u hulp nodig hebt bij Childcare (zoals ' Grandma ') is niet iets dat persoonlijk is in uw gebruikers database.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Personaliseren gebruiken in een webtoepassing

Het toevoegen van een Learning-lus aan een webtoepassing omvat:

* Bepaal welke ervaring u wilt aanpassen, welke acties en functies u hebt, welke context functies u moet gebruiken en welke beloning u moet instellen.
* Voeg een verwijzing naar de personalisatie-SDK in uw toepassing toe.
* Roep de classificatie-API aan wanneer u klaar bent om te personaliseren.
* Sla de gebeurtenis-eigenbestand op. U stuurt later een beloning met de belonings-API.
1. Roep Activate aan voor de gebeurtenis zodra u zeker weet dat de gebruiker uw persoonlijke pagina heeft bekeken.
1. Wacht tot de gebruiker geclassificeerde inhoud heeft geselecteerd.
1. Roep belonings-API om op te geven hoe goed de uitvoer van de Rank API is.

## <a name="how-to-use-personalizer-with-a-chat-bot"></a>Personaler gebruiken met een chat sessie

In dit voor beeld ziet u hoe u personalisatie kunt gebruiken om een standaard suggestie te maken in plaats van de gebruiker elke keer een reeks menu's of keuzes te verzenden.

* Haal de [code](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/ChatbotExample) op voor dit voor beeld.
* Stel uw bot-oplossing in. Zorg ervoor dat u uw LUIS-toepassing publiceert.
* Rang en beloning-API-aanroepen voor bot beheren.
    * Voeg code toe om LUIS intentie verwerking te beheren. Als de **geen** wordt geretourneerd als de hoogste intentie of als de Score van het hoogste niveau lager is dan de drempel waarde voor uw bedrijfs logica, stuurt u de lijst met intenties naar persoonlijker om de intentie te rangschikken.
    * De lijst met intents weer geven aan de gebruiker als selecteerbaar koppelingen met de eerste intentie van de rang orde die het beste bij de positie API-reactie ligt.
    * Leg de selectie van de gebruiker vast en verzend deze in de API-aanroep van beloning.

### <a name="recommended-bot-patterns"></a>Aanbevolen bot-patronen

* Stel de aanroepen van de gebruikers interface van de persoonlijker-API telkens wanneer er een ondubbelzinnigheid nodig is, in tegens telling tot cache resultaten voor elke gebruiker. Het resultaat van disambiguating intentie kan in de loop van de tijd worden gewijzigd voor één persoon en de classificatie-API om afwijkingen te verkennen, versnelt het algehele leer proces.
* Kies een interactie die gemeen schappelijk is voor veel gebruikers, zodat u voldoende gegevens hebt om te personaliseren. Inleidende vragen kunnen bijvoorbeeld beter worden afgestemd op de diepte van kleinere toelichtingen in de conversatie grafiek die slechts een paar gebruikers kan krijgen.
* Gebruik rank API-aanroepen om de conversaties ' eerste suggestie is geschikt ' in te scha kelen, waarbij de gebruiker wordt gevraagd om X? of ' hebt u X? ' en de gebruiker kan gewoon bevestigen; in plaats van opties te geven aan de gebruiker, waar ze moeten kiezen in een menu. Bijvoorbeeld gebruiker: ' Ik wil graag een koffie-bot best Ellen: ' wilt u graag een dubbele espresso hebben? '. Op deze manier is het belonings signaal ook sterk, omdat het direct op de ene suggestie van toepassing is.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>Personaler gebruiken met een aanbevelings oplossing

Veel bedrijven gebruiken aanbevolen engines, marketing-en campagne hulpprogramma's, segmentering van een doel groep, clustering en het samen stellen van gezamenlijke filters, en andere middelen om producten van een grote catalogus aan klanten te aanbevelen.

De [github-opslag plaats van micro soft adviseert](https://github.com/Microsoft/Recommenders) voor beelden en aanbevolen procedures voor het bouwen van aanbevelings systemen, als Jupyter-notebooks. Hierin worden voor beelden gegeven van het voorbereiden van gegevens, het bouwen van modellen, het evalueren, afstemmen en inzet van de aanbevolen engines, voor veel gang bare benaderingen zoals xDeepFM, SAR, als, RBM'S, DKN.

Personaler kan samen werken met een aanbevelings engine wanneer deze aanwezig is.

* Aanbevelings engines nemen grote hoeveel heden items (bijvoorbeeld 500.000) en raden een subset (zoals de top 20) van honderden of duizenden opties aan.
* Personaler neemt een klein aantal acties met veel informatie over hen en rangschikt deze in realtime voor een bepaalde uitgebreide context, terwijl de meeste aanbevolen engines slechts enkele kenmerken van gebruikers, producten en hun interacties gebruiken.
* Personaler is ontworpen voor het autonoom verkennen van gebruikers voorkeuren, waardoor er betere resultaten worden verkregen waarbij inhoud snel wordt gewijzigd, zoals nieuws, live evenementen, inhoud van Live Community, inhoud met dagelijkse updates of seizoen inhoud.

Een veelgebruikte toepassing is het uitvoeren van de uitvoer van een aanbevelings Engine (bijvoorbeeld de top 20 van producten voor een bepaalde klant) en die als invoer acties voor persoonlijk gebruik.

## <a name="adding-content-safeguards-to-your-application"></a>Inhouds beveiligingen toevoegen aan uw toepassing

Als uw toepassing grote afwijkingen toestaat in inhoud die wordt weer gegeven voor gebruikers, en een deel van die inhoud onveilig of ongepast is voor sommige gebruikers, moet u ervoor zorgen dat de juiste veiligheids maatregelen worden getroffen om te voor komen dat uw gebruikers onaanvaardbare inhoud zien. Het beste patroon om beveiligingen te implementeren is:
    * De lijst met te rangschikken acties ophalen.
    * Filter de items die niet geschikt zijn voor de doel groep.
    * Rang Schik deze levensvat bare acties alleen.
    * De bovenste geclassificeerde actie weer geven voor de gebruiker.

In sommige architecturen is de bovenstaande reeks mogelijk moeilijk te implementeren. In dat geval is er een alternatieve benadering voor het implementeren van veiligheids maatregelen na de rang schikking, maar een inrichting moet worden gemaakt, zodat acties die buiten de beveiliging vallen, niet worden gebruikt voor het trainen van het Personaler model.

* Haal de lijst met acties op die moeten worden gerangschikt, waarbij learning is gedeactiveerd.
* Rang schikkings acties.
* Controleer of de meest voorkomende actie levensvatbaar is.
    * Als de meest voorkomende actie haalbaar is, kunt u learning voor deze classificatie activeren en deze vervolgens weer geven aan de gebruiker.
    * Als de meest voorkomende actie niet haalbaar is, moet u learning voor deze classificatie niet activeren en zelf bepalen hoe u uw eigen logica of alternatieve benaderingen kunt zien wat u aan de gebruiker kunt weer geven. Ook als u de optie voor de tweede beste gerangschikt gebruikt, moet u learning voor deze classificatie niet activeren.


## <a name="next-steps"></a>Volgende stappen

[Ethische & verantwoordelijk gebruik](ethics-responsible-use.md).

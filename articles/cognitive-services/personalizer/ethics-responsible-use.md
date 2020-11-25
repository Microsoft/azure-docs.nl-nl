---
title: Ethiek en verantwoordelijk gebruik-Personaler
titleSuffix: Azure Cognitive Services
description: Deze richt lijnen zijn bedoeld om u te helpen bij het implementeren van personalisatie op een manier waarmee u vertrouwen in uw bedrijf en service kunt bouwen. Zorg ervoor dat u pauzeert om onderzoek te doen, meer te weten te komen over de impact van de persoonlijke instellingen op het leven van mensen. Zoek richt lijnen wanneer dat niet het geval is.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 602da28f0c235fb0e797a493bc1160631c042a9d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005976"
---
# <a name="guidelines-for-responsible-implementation-of-personalizer"></a>Richt lijnen voor de verantwoordelijke implementatie van Personaler

Voor personen en samenleving om optimaal te kunnen profiteren van de mogelijkheden van AI, moeten implementaties zodanig worden ontworpen dat ze de vertrouwens relatie van die AI toevoegen aan hun toepassingen en de gebruikers van toepassingen die met AI zijn gebouwd. Deze richt lijnen zijn bedoeld om u te helpen bij het implementeren van personalisatie op een manier waarmee u vertrouwen in uw bedrijf en service kunt bouwen. Zorg ervoor dat u pauzeert om onderzoek te doen, meer te weten te komen over de impact van de persoonlijke instellingen op het leven van mensen. Zoek richt lijnen wanneer dat niet het geval is.

Deze richt lijnen zijn niet bedoeld als juridisch advies en u moet er ook voor zorgen dat uw toepassing voldoet aan de snelle ontwikkelingen in de wet op dit gebied en in uw sector.

Bij het ontwerpen van uw toepassing met behulp van Personaler, moet u ook rekening houden met een brede set verantwoordelijkheden tijdens het ontwikkelen van een gegevensgericht AI-systeem, met inbegrip van de ethiek, privacy, beveiliging, veiligheid, insluiting, transparantie en verantwoording. Meer informatie hierover vindt u in de [Aanbevolen Lees](#recommended-reading) sectie.

U kunt de volgende inhoud als een begin controlelijst gebruiken en deze aan uw scenario aanpassen en verfijnen. Dit document bevat twee hoofd secties: de eerste is speciaal bedoeld voor het markeren van de verantwoordelijke gebruiks overwegingen bij het kiezen van scenario's, functies en voor delen voor persoonlijk gebruik. In de tweede reeks worden de waarden van micro soft volgens elkaar overwogen bij het bouwen van AI-systemen en worden er suggesties en risico's voor acties geboden voor het gebruik van persoonlijke instellingen.


## <a name="your-responsibility"></a>Uw verantwoordelijkheid

Alle richt lijnen voor de verantwoordelijke implementatie bouwen op de basis die ontwikkel aars en bedrijven die persoonlijker gebruiken zijn verantwoordelijk en kunnen worden verwerkt voor de gevolgen van het gebruik van deze algoritmen in de samenleving. Als u een toepassing ontwikkelt die uw organisatie gaat implementeren, moet u uw rol en verantwoordelijkheid voor de werking ervan herkennen en bepalen hoe deze van invloed is op personen. Als u een toepassing ontwerpt die door een derde partij moet worden geïmplementeerd, moet u een gemeen schappelijke inzichten hebben met wie deze uiteindelijk verantwoordelijk is voor het gedrag van de toepassing, en documenten die ze kennen.

Trust is gebaseerd op het principe van vervulde verbintenissen: Houd rekening met uw gebruikers, de samenleving en het juridische Framework waarmee uw toepassingen werken, om expliciete en impliciete toezeg gingen te identificeren.

Micro soft brengt voortdurend inspanningen in de hulp middelen en documenten waarmee u op deze verantwoordelijkheden kunt handelen. [Feedback geven over micro soft](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) als u meer hulp middelen denkt, product functies en documenten helpen u bij het implementeren van deze richt lijnen voor het gebruik van personaler.


## <a name="factors-for-responsibly-implementing-personalizer"></a>Factoren voor een verantwoorde persoonlijke implementatie

Het implementeren van Personaler is een fantastische waarde voor uw gebruikers en uw bedrijf. Als u een verantwoorde Personaler wilt implementeren, moet u eerst de volgende richt lijnen overwegen:

* Kies cases gebruiken om persoonlijke instellingen toe te passen.
* Bouwen van [belonings functies](concept-rewards.md).
* Kiezen welke [functies](concepts-features.md) over de context en mogelijke acties u wilt gebruiken voor personalisatie.


## <a name="choosing-use-cases-for-personalizer"></a>Use cases voor Personaler kiezen

Het gebruik van een service die leert om inhoud te personaliseren en gebruikers interfaces is nuttig. Het kan ook worden toegepast als de manier waarop de personalisatie negatieve neven effecten in de echte wereld creëert,, ook als gebruikers niet op de hoogte zijn van personalisatie van inhoud.

Voor beelden van het gebruik van Personaler met verhoogde mogelijkheden voor negatieve neven effecten of een gebrek aan transparantie zijn onder andere scenario's waarbij de ' beloning ' afhankelijk is van veel lange termijn complexe factoren die, wanneer over een onmiddellijke berekening kan leiden tot ongunstige resultaten voor individuen. Deze opties worden meestal beschouwd als ' keuze mogelijkheden ' of ' keuzen ' die een risico op schade inhouden. Bijvoorbeeld:


* **Financiën**: aanbiedingen voor leningen, financiële en verzekerings producten personaliseren, waarbij risico factoren zijn gebaseerd op gegevens die de personen niet kennen, kunnen niet worden opgehaald of niet kunnen worden betwist.
* **Onderwijs**: classificaties aanpassen aan school cursussen en onderwijs instellingen, waarbij aanbevelingen kunnen leiden tot bias en de bewustmaking van andere opties verminderen.
* **Democratie en Civice participatie**: het personaliseren van inhoud voor gebruikers die het doel hebben om meningen te vervolledigen en te manipuleren.
* **Evaluatie van derden**: het aanpassen van items waarbij de vergoeding is gebaseerd op een evaluatie van een derde partij van de gebruiker, in plaats van een beloning die door het eigen gedrag van de gebruiker wordt gegenereerd.
* **Intolerantie voor exploratie**: elke situatie waarbij het exploratie gedrag van personaler schade kan veroorzaken.

Bij het kiezen van use cases voor Personaler:

* Start het ontwerp proces om te bepalen hoe de persoonlijke instellingen uw gebruikers helpen.
* Houd rekening met de negatieve gevolgen voor de echte wereld als sommige items niet worden geclassificeerd voor gebruikers als gevolg van personalisatie patronen of verkennen.
* Houd rekening met het feit of uw use-case geautomatiseerde verwerking is die van invloed is op de gegevens onderwerpen die worden geregeld onder [AVG](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32016R0679) artikel 22 of andere wetgeving.
* Overweeg zelf te voldoen aan Prophecy-lussen. Dit kan gebeuren als een persoonlijke compensatie beloning een model traint, zodat het mogelijk is dat een demografische groep van toegang tot relevante inhoud wordt uitgesloten. Het is bijvoorbeeld mogelijk dat de meeste mensen in een lage inkomsten groep geen Premium-verzekerings aanbieding verkrijgen en dat ze nooit in de groep worden weer geven om de aanbieding helemaal te zien als er onvoldoende is.
* Sla kopieën van modellen en learning-beleids regels op voor het geval dat het nodig is om in de toekomst een persoonlijker te maken. U kunt dit regel matig doen of elke vernieuwings periode voor het model.
* Denk na over het niveau van exploratie dat voldoende is voor de ruimte en hoe u het kunt gebruiken als hulp middel om de effecten van de echo kamer te verminderen.


## <a name="selecting-features-for-personalizer"></a>Functies selecteren voor persoonlijkere

Het personaliseren van inhoud is afhankelijk van nuttige informatie over de inhoud en de gebruiker. Houd er rekening mee dat voor sommige toepassingen en branches sommige gebruikers functies direct of indirect als discriminerend en mogelijk illegaal kunnen worden beschouwd.

Houd rekening met het effect van deze functies:

* **Demografische** gegevens van gebruikers: functies met betrekking tot geslacht, geslacht, leeftijd, race, religion: deze functies zijn mogelijk niet toegestaan in bepaalde toepassingen om wettelijke redenen en kunnen niet worden gepersonaliseerd om ze te personaliseren, omdat de personalisatie generalisaties en bias zou door geven. Een voor beeld van deze bias-doorgifte is een taak boeking voor engineering die niet wordt weer gegeven voor doel groepen op basis van oude of geslachte producten.
* **Land informatie**: in veel locaties van de wereld kunnen locatie gegevens (zoals een post code, post code of groeps naam) in hoge mate worden gecorreleerd met inkomsten, race-en Religion.
* **Gebruikers perceptie van verdeling**: zelfs in gevallen waarin uw toepassing geluids beslissingen maakt, moet u rekening houden met het effect van gebruikers die zien dat de inhoud die in uw toepassing wordt weer gegeven, verandert in een manier die is afgestemd op functies die discriminerend zijn.
* **Onbedoelde afwijking in functies**: er zijn soorten bias die kunnen worden geïntroduceerd met behulp van functies die alleen van invloed zijn op een subset van de populatie. Dit vereist extra aandacht als er functies worden gegenereerd algorithmically, zoals wanneer u afbeeldings analyse gebruikt om items in een afbeelding of tekst analyse te extra heren om entiteiten in tekst te detecteren. Let op de kenmerken van de services die u gebruikt om deze functies te maken.

Pas de volgende procedures toe bij het kiezen van functies voor het verzenden van contexten en acties naar Personaler:

* Denk na over de wettigheid en de ethiek van het gebruik van bepaalde functies voor sommige toepassingen, en of onschuldieve functies proxy's kunnen zijn voor anderen die u wilt of moet vermijden,
* Wees transparant voor gebruikers die algoritmen en gegevens analyse worden gebruikt om de weer te geven opties te personaliseren.
* Vraag uzelf: zouden mijn gebruikers voorzichtig zijn als ik deze informatie gebruik om de inhoud voor hen te personaliseren? Zou ik graag zien hoe de beslissing is genomen om bepaalde items te markeren of te verbergen?
* Gebruik gedrag in plaats van classificatie of segmentatie gegevens op basis van andere kenmerken. Demografische informatie werd traditioneel gebruikt door detail handelaren voor historische redenen: demografische kenmerken leken eenvoudig te verzamelen en te handelen vóór een digitale vormgeving, maar vraag hoe relevante demografische gegevens zich voordoen wanneer u werkelijke interacties, contextuele en historische gegevens hebt die nauw keuriger zijn voor de voor keuren en identiteit van gebruikers.
* Bedenk hoe u kunt voor komen dat functies die worden vervalst door kwaadwillende gebruikers, wanneer ze worden misbruikt in grote aantallen, kunnen leiden tot het aanpassen van persoonlijke voor keuren op misleidende manieren om purposefully te verstoren, verlegen en lastigvallen te maken van bepaalde klassen gebruikers.
* Als dat nodig is, kunt u uw toepassing zo ontwerpen dat uw gebruikers zich kunnen aanmelden of willen afmelden dat er bepaalde persoonlijke functies worden gebruikt. Deze kunnen worden gegroepeerd, zoals ' locatie-informatie ', ' apparaatgegevens ', ' eerdere aankoop geschiedenis ' enzovoort.


## <a name="computing-rewards-for-personalizer"></a>Computer beloningen voor persoonlijk gebruik

Personaler streeft ernaar om de keuze van de actie te verbeteren op basis van de belonings Score die wordt geboden door de bedrijfs logica van uw toepassing.

Een goed ontwikkelde belonings Score fungeert als een proxy voor een korte periode voor een bedrijfs doel, die is gekoppeld aan de missie van een organisatie.

Als u bijvoorbeeld op klikken klikt, wordt de Personaler service gevraagd om te klikken op de kosten van alle andere, zelfs als op de knop wordt geklikt of deze niet aan een zakelijk resultaat is gekoppeld.

Een voor beeld hiervan is het mogelijk dat er voor een nieuws site beloningen worden ingesteld die zijn gekoppeld aan iets wat betekenisvoler is dan klikken, zoals ' heeft de gebruiker genoeg tijd besteed aan het lezen van de inhoud? ' "Had hij of zij klikken op relevante artikelen of verwijzingen?". Met Personaler is het eenvoudig om meet waarden nauw keurig te koppelen aan beloningen. Let er echter op dat u geen gebruik kunt maken van de gebruikers betrokkenheid op korte termijn met goede resultaten.

### <a name="unintended-consequences-from-reward-scores"></a>Onbedoelde gevolgen van belonings scores
Belonings scores kunnen worden gebouwd met het beste van de bedoelingen, maar kunnen nog steeds onverwachte gevolgen of onbedoelde resultaten maken voor de rang schikking van de inhoud van Personaler.

Bekijk de volgende voorbeelden:

* Het maken van een persoonlijke aanpassing van de video-inhoud op het percentage van de gevolgde video heeft waarschijnlijk een kortere hoeveelheid Video's.
* Het belonen van sociale-media shares zonder sentiment analyse van de manier waarop het wordt gedeeld of de inhoud zelf, kan leiden tot een beledigend, ongecontroleerd of inflammatory inhoud, die een groot deel van ' betrokkenheid ' indeelt, maar een kleine waarde toevoegt.
* Het maken van de actie voor gebruikers interface-elementen die gebruikers niet verwachten te wijzigen, kan de bruikbaarheid en voorspel baarheid van de gebruikers interface belemmeren, waarbij de knoppen verrassend de locatie of het doel zonder waarschuwing veranderen, waardoor het moeilijker is voor bepaalde groepen gebruikers om productief te blijven.

Implementeer deze aanbevolen procedures:

* Voer offline experimenten uit met uw systeem met behulp van verschillende benaderingen om de impact en neven effecten te begrijpen.
* Beoordeel uw belonens functies en vraag uzelf hoe zou een extreem naïvee de interpretatie van het programma te buigen en ongewenste resultaten te bereiken.


## <a name="responsible-design-considerations"></a>Overwegingen voor verantwoordelijkheids ontwerp

Hier volgen de ontwerp gebieden voor de verantwoordelijke implementaties van AI. Meer informatie over dit Framework vindt u in [de toekomst](https://news.microsoft.com/futurecomputed/)over.

![AI-waarden van toekomstige berekeningen](media/ethics-and-responsible-use/ai-values-future-computed.png)

### <a name="accountability"></a>Verantwoordelijkheden
*Personen die AI-systemen ontwerpen en implementeren, moeten verantwoordelijk zijn voor de werking van hun systemen*.

* Maak interne richt lijnen voor het implementeren van persoonlijke instellingen, documenten en communicatie met uw team, leidinggevenden en leveranciers.
* Voer periodieke beoordelingen uit over de berekening van belonings cijfers, voer offline-evaluaties uit om te zien welke functies van invloed zijn op personalisatie, en gebruik de resultaten om onnodige en overbodige functies te elimineren.
* Communiceer duidelijk aan uw gebruikers hoe Personaler wordt gebruikt, wat doel en met welke gegevens.
* Archiveer informatie en assets, zoals modellen, leer beleid en andere gegevens, die Personaler gebruikt om te werken, om de resultaten te kunnen reproduceren.

### <a name="transparency"></a>Transparantie
*AI-systemen moeten begrijpelijk zijn*. Met persoonlijke instellingen:

* *Geef gebruikers informatie over de manier waarop de inhoud is aangepast.* U kunt bijvoorbeeld uw gebruikers weer geven met een knop met het label `Why These Suggestions?` dat wordt weer gegeven met de belangrijkste functies van de gebruiker en acties die een rol hebben gespeeld in de resultaten van personaler.
* Zorg ervoor dat uw gebruiksrecht overeenkomst vermeldt dat u informatie over gebruikers en hun gedrag gaat gebruiken om de ervaring te personaliseren.

### <a name="fairness"></a>Verdeling
*AI-systemen moeten alle mensen redelijk behandelen*.

* Gebruik persoonlijke voor keuren niet voor use-cases waarin de resultaten op lange termijn worden uitgevoerd, of die echte schade betreffen.
* Gebruik geen functies die niet geschikt zijn voor het personaliseren van inhoud met of die het mogelijk maakt om ongewenste BIASS door te geven. Iedereen met vergelijk bare financiële omstandigheden zou bijvoorbeeld dezelfde gepersonaliseerde aanbevelingen voor financiële producten moeten zien.
* Begrijp wat er mogelijk is in functies die zijn opgenomen in de editors, hulpprogram ma's voor algoritmen of gebruikers zelf.

### <a name="reliability-and-safety"></a>Betrouwbaarheid en veiligheid
*AI-systemen moeten betrouwbaar en veilig worden uitgevoerd*. Voor Personaler:

* *Geef geen persoonlijke voor keuren op die niet moeten worden gekozen*. Zo moeten niet-geëigende films worden gefilterd op basis van de acties die persoonlijk moeten worden uitgevoerd als er een aanbeveling wordt gedaan voor een anonieme gebruiker of een gebruiks duur van de leeftijd.
* *Beheer uw persoonlijker model als een bedrijfs activum*.  Bedenk hoe vaak u het model en het trainings beleid achter uw Personaler-lus wilt opslaan en er een back-up van wilt maken. u kunt dit ook behandelen als een belang rijk bedrijfs activum. Het reproduceren van eerdere resultaten is belang rijk voor zelf controle en het meten van de verbetering.
* *Geef kanalen op om direct feedback van gebruikers te ontvangen*. Naast het coderen van veiligheids controles om ervoor te zorgen dat alleen de juiste doel groepen de juiste inhoud zien, kunt u een feedback mechanisme geven waarmee gebruikers inhoud kunnen rapporteren die kan worden verrassende of verstoord. Met name als uw inhoud afkomstig is van gebruikers of derden, kunt u overwegen om de inhoud te controleren en te valideren met behulp van micro soft Content Moderator of extra hulp middelen.
* *Voer frequente offline-evaluaties uit*. Dit helpt u trends te bewaken en ervoor te zorgen dat de effectiviteit bekend is.
* *Stel een proces voor het detecteren en reageren op schadelijke manipulatie*. Er zijn actors die profiteren van de mogelijkheden van machine learning en AI-systemen om te leren van hun omgeving om het resultaat te verschuiven naar hun doel stellingen. Als uw gebruik van Personaler in staat is om belang rijke keuzes te beïnvloeden, moet u ervoor zorgen dat u de juiste middelen hebt om deze klassen aanvallen te detecteren en te verhelpen, met inbegrip van de beoordeling van mensen in de relevante omstandigheden.

### <a name="security-and-privacy"></a>Beveiliging en privacy
*AI-systemen moeten veilig zijn en de privacy eerbiedigen*. Bij het gebruik van Personaler:

* *Informeer gebruikers vooraf over de verzamelde gegevens en de wijze waarop deze worden gebruikt en verschaf vóór* de voorafgaande toestemming, volgens uw lokale en industriële voor Schriften.
* *Beveilig gebruikers besturings elementen voor privacy.* Voor toepassingen waarin persoonlijke gegevens worden opgeslagen, kunt u een gemakkelijk te vinden knop bieden voor functies zoals:
   * `Show me all you know about me`
   * `Forget my last interaction`
   * `Delete all you know about me`

In sommige gevallen kunnen deze juridisch verplicht zijn. Houd rekening met de compromissen bij het opnieuw trainen van modellen, zodat deze geen traceringen van verwijderde gegevens bevatten.

### <a name="inclusiveness"></a>Inclusiviteit
*Adresseer een breed scala aan menselijke behoeften en ervaring*.
* *Een persoonlijke ervaring bieden voor toegankelijkheids interfaces.* De efficiëntie die afkomstig is van goede persoonlijke instellingen: wordt toegepast om de hoeveelheid inspanning, verplaatsing en onnodige herhaling in interacties te verminderen. Dit kan vooral nuttig zijn voor mensen met een handicap.
* *Stel toepassings gedrag in op context*. U kunt Personaler gebruiken om dubbel zinnigheid te maken tussen intenties in een chat-bot, bijvoorbeeld omdat de juiste interpretatie context gevoelig is en één grootte mogelijk niet helemaal past.


## <a name="proactive-readiness-for-increased-data-protection-and-governance"></a>Proactieve gereedheid voor verhoogde gegevens bescherming en-beheer

Het is moeilijk om specifieke wijzigingen in reglementaire contexten te voors pellen, maar in het algemeen is het verstandig om verder te gaan dan het minimale juridische kader om het respectful gebruik van persoons gegevens te waarborgen en om transparantie en keuze te bieden die betrekking hebben op het maken van een algoritme beslissing.


* Denk na over een situatie waarin er nieuwe beperkingen zijn voor gegevens die door individuen worden verzameld, en er moet worden aangegeven hoe deze is gebruikt om beslissingen te nemen.
* Houd rekening met extra gereedheid waarbij gebruikers onder andere marginale kwets bare populaties, kinderen, gebruikers in economische Risico's of gebruikers kunnen bevatten die van invloed zijn op de manipulatie van algoritmen.
* Houd rekening met het verbreiden van de betrouw bare afweging met de manier waarop toepassingen en algoritmen voor het verzamelen van doel stellingen en doel groepen worden uitgespeeld en hoe u beproefde strategische fouten kunt voor komen.


## <a name="proactive-assessments-during-your-project-lifecycle"></a>Proactieve analyses tijdens de levens cyclus van uw project

Overweeg om methoden te maken voor team leden, gebruikers en zakelijke eigen aren om problemen met het verantwoordelijke gebruik te melden, en een proces te maken dat prioriteit geeft aan de oplossing en voor komt dat Retaliation.

Een persoon die op de hoogte is van de neven effecten van het gebruik van een technologie wordt beperkt door hun perspectief en levens ervaring. Breid het bereik van de beschik bare meningen uit door in meer uiteenlopende stemmen in uw teams, gebruikers of advies borden te brengen. zo is het mogelijk en gestimuleerd dat ze kunnen praten. Overweeg trainings-en leer materiaal om de team kennis in dit domein verder uit te breiden en de mogelijkheid toe te voegen om complexe en gevoelige onderwerpen te bespreken.

Denk na over het behandelen van taken met betrekking tot verantwoordelijk gebruik, net zoals andere crosscutting taken in de toepassings levenscyclus, zoals taken met betrekking tot gebruikers ervaring, beveiliging of DevOps. Deze taken en hun vereisten kunnen geen behandeld zijn. Het verantwoordelijke gebruik moet worden besproken en geverifieerd tijdens de levens cyclus van de toepassing.

## <a name="questions-and-feedback"></a>Vragen en feedback

Micro soft zet zich voortdurend bezig met hulp middelen en documenten om u op deze verantwoordelijkheden te helpen. Ons team nodigt u uit om [feedback te geven aan micro soft](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D)  als u meer hulp middelen, product functies en documenten zou kunnen helpen bij het implementeren van deze richt lijnen voor het gebruik van personaler.

## <a name="recommended-reading"></a>Aanbevolen Lees bewerkingen

* Zie de zes principes van micro soft voor de verantwoordelijke ontwikkeling van AI die is gepubliceerd in het boek van januari 2018, [de toekomst berekende](https://news.microsoft.com/futurecomputed/)
* [Wie is eigenaar van de toekomst?](https://www.goodreads.com/book/show/15802693-who-owns-the-future) door Jaron Lanier.
* [Wapens van wiskunde](https://www.goodreads.com/book/show/28186015-weapons-of-math-destruction) door-Cathy O'Neil
* [Ethiek en gegevens wetenschap](https://www.oreilly.com/library/view/ethics-and-data/9781492043898/) door DJ Patil, Hilary Mets, Mike Loukides.
* [ACM-code van de ethiek](https://www.acm.org/code-of-ethics)
* [Ondiscriminatie van genetische informatie-GINA](https://en.wikipedia.org/wiki/Genetic_Information_Nondiscrimination_Act)
* [FATML-principes voor berekenings algoritmen](https://www.fatml.org/resources/principles-for-accountable-algorithms)


## <a name="next-steps"></a>Volgende stappen

[Functies: actie en context](concepts-features.md).

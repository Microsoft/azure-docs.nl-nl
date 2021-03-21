---
title: Aangepaste spraak voorbeelden vastleggen-spraak service
titleSuffix: Azure Cognitive Services
description: Maak een aangepaste stem voor de kwaliteit van de productie door een robuust script voor te bereiden, goede stem talen in te huren en een professionele opname te maken.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: erhopf
ms.openlocfilehash: 6d2ad87f6a8c79f61d62140a1549968cd88d83aa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101722310"
---
# <a name="record-voice-samples-to-create-a-custom-voice"></a>Stemvoorbeelden vastleggen om een aangepaste stem te maken

Het maken van een volledig nieuwe, aangepaste stem op de productie is geen informele onderneming. Het centrale onderdeel van een aangepaste stem is een grote verzameling audio voorbeelden van menselijke spraak. Het is essentieel dat deze audio-opnames van hoge kwaliteit zijn. Kies een voicemail talen interface die ervaring heeft met het maken van dit type opnamen en die ze hebben vastgelegd door een bevoegde opname technicus met behulp van professionele apparatuur.

Voordat u deze opnamen kunt maken, hebt u echter een script nodig: de woorden die door uw stem talen worden afgesp roken om de audio voorbeelden te maken. Voor de beste resultaten moet uw script een goede fonetische dekking en voldoende variëteit hebben om het aangepaste spraak model te trainen.

Veel kleine, maar belang rijke informatie gaat over het maken van een professionele spraak opname. Deze hand leiding is een route kaart voor een proces waarmee u goede, consistente resultaten krijgt.

> [!NOTE]
> Als u een Neural-stem wilt trainen, moet u een profiel voor spraak-talen opgeven met het bestand met de bestands toestemming van de stem talen bevestiging dat zijn/haar spraak gegevens worden gebruikt voor het trainen van een aangepast spraak model. Zorg ervoor dat u de onderstaande zin opneemt wanneer u het opname script voorbereidt. 

> "I [Geef uw voor-en achternaam op] Houd er rekening mee dat de opnamen van mijn stem worden gebruikt door [de naam van het bedrijf te vermelden] om een synthetische versie van mijn stem te maken en te gebruiken."
Deze zin wordt gebruikt om te controleren of de trainings gegevens worden uitgevoerd door dezelfde persoon die de toestemming doet. Lees hier meer over de [Voice-talen verificatie](/legal/cognitive-services/speech-service/custom-neural-voice/data-privacy-security-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) .

> Aangepaste Neural Voice is beschikbaar met beperkte toegang. Zorg ervoor dat u bekend bent met de [vereiste AI-vereisten](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) en [Pas de toegang hier toe](https://aka.ms/customneural). 

> [!TIP]
> Voor de hoogst mogelijke kwaliteits resultaten kunt u micro soft helpen bij het ontwikkelen van uw eigen stem. Micro soft heeft uitgebreide ervaring met het produceren van stemmen van hoge kwaliteit voor hun eigen producten, waaronder Cortana en kantoor.

## <a name="voice-recording-roles"></a>Spraak opname rollen

Er zijn vier basis rollen in een aangepaste spraak registratie project:

Rol|Doel
-|-
Stem talen        |De stem van deze persoon vormt de basis van de aangepaste stem.
Registratie-Engineer  |Houdt toezicht op de technische aspecten van de registratie en exploiteert de opname apparatuur.
Directeur            |Bereidt het script voor en bussen de prestaties van het stem talen.
Editor              |Voltooit de audio bestanden en bereidt ze voor op het uploaden naar de aangepaste Voice Portal.

Een individu kan meer dan één rol vullen. In deze hand leiding wordt ervan uitgegaan dat u de rol van Director voornamelijk invult en zowel een stem-en registratie technicus aanneemt. Als u de opnamen zelf wilt maken, bevat dit artikel informatie over de rol van de opname technicus. De rol van de editor is niet nodig tot na de sessie, dus kan worden uitgevoerd door de Director of de registratie technicus.

## <a name="choose-your-voice-talent"></a>Kies uw stem talen

Actors die ervaring hebben met Voice of spraak, maken goed aangepaste stem talen mogelijk. U kunt ook vaak een passend, onder andere aankondigen en nieuws lezers vinden.

Kies Voice-talen waarvan u de natuurlijke stem wilt bevallen. Het is mogelijk om unieke ' teken ' stemmen te maken, maar het is veel moeilijker voor de meeste talen om ze consistent uit te voeren en de inspanningen kunnen een spraak spanning veroorzaken.

> [!TIP]
> Vermijd het gebruik van herken bare stemmen voor het maken van een aangepaste stem, tenzij natuurlijk uw doel is om een beroemdheden-stem te creëren. Minder bekende stemmen worden meestal minder afleiden aan gebruikers.

De enige belangrijkste factor voor het kiezen van Voice-talen is consistentie. Uw opnamen moeten allemaal klinken alsof ze op dezelfde dag in dezelfde ruimte zijn gemaakt. U kunt dit ideaal doen met behulp van goede vastleggen en technische procedures.

Uw stem talen zijn de andere helft van de vergelijking. Ze moeten kunnen praten met een consistent tempo, volume niveau, Toon hoogte en Toon. De selectie van de dicteer functie is. De talen moeten ook hun pitch-variatie, emotioneel effect en spraak mannerisms kunnen beheren.

Het vastleggen van aangepaste spraak voorbeelden kan meer dan andere soorten gesp roken werk zijn. De meeste stem talen kunnen twee of drie uur per dag registreren. Beperk sessies tot drie of vier weken, waarbij een dag in voorkomend geval is.

Werk samen met uw Voice-talen voor het ontwikkelen van een ' persoona ' die de algehele geluids-en emotioneel Toon van de aangepaste stem definieert. In het proces kunt u zien wat ' neutraal ' klinkt voor die persoona. Met de aangepaste Neural Voice-mogelijkheid kunt u een model trainen dat spreekt met emoties. Definieer de "spreek stijlen" en vraag uw stem talen aan om het script te lezen op een manier die de gewenste stijlen weer klank vindt.  

Een persoona kan bijvoorbeeld een natuurlijke bijstel persoonlijkheid hebben. Het is dus mogelijk dat de stem van Optimism ook als er sprake is van een onpartijdige aanspreken. Dergelijke persoonlijkheids kenmerken moeten echter subtiel en consistent zijn. Luister naar leesingen door bestaande stemmen om een idee te krijgen van wat u wilt.

> [!TIP]
> Normaal gesp roken wilt u eigenaar zijn van de gesp roken opnamen die u maakt. Uw stem talen moeten worden geschikter aan een arbeids contract voor het project.

## <a name="create-a-script"></a>Een script maken

Het begin punt van een aangepaste spraak opname sessie is het script, dat de uitingen bevat die moet worden gesp roken door uw stem talen. (De term ' uitingen ' omvat zowel volledige zinnen als korte zinnen.)

De uitingen in uw script kan vanaf elke locatie worden opgehaald: fictief, niet-fictief, transcripten van toespraken, Nieuws rapporten en andere beschik bare formulieren. Als u er zeker van wilt zijn dat uw stem goed werkt op bepaalde typen woorden (zoals medische terminologie of het jargon van Program meren), wilt u misschien zinnen uit weten schappelijk papers of technische documenten toevoegen. Zie de sectie [juridische](#legalities) zaken voor een korte bespreking van mogelijke juridische problemen. U kunt ook uw eigen tekst schrijven.

Uw uitingen hoeft niet afkomstig te zijn uit dezelfde bron of van hetzelfde soort bron. Ze hoeven nog niets te doen met elkaar. Als u echter woord sets gebruiken (bijvoorbeeld ' u bent aangemeld ') in uw spraak toepassing, moet u deze in uw script toevoegen. Hiermee krijgt u uw aangepaste stem een betere kans op Pronouncing deze zinsdelen goed. En als u besluit om een opname te gebruiken in plaats van de gesynthesizerde spraak, hebt u deze al in dezelfde stem.

Consistentie is het kiezen van Voice-talen, het is de Hallmark van een goed script. Uw script moet veel verschillende woorden en zinnen bevatten met verschillende lengten, structuren en stemmingen van zinnen. Elk geluid in de taal moet meerdere keren worden weer gegeven en in talrijke contexten ( *fonetische dekking* genoemd).

Bovendien moet de tekst alle manieren bevatten waarop een bepaald geluid kan worden weer gegeven en kan elk geluid op verschillende plaatsen in de zinnen worden geplaatst. Zowel declaratieve zinnen als vragen moeten worden opgenomen en gelezen met de juiste intonation.

Het is moeilijk om een script te schrijven dat *voldoende* gegevens bevat zodat de Custom speech Portal een goede stem kan bouwen. In de praktijk is de eenvoudigste manier om een script te maken dat zorgt voor een robuuste fonetische dekking, het opnemen van een groot aantal steek proeven. De standaard stemmen die micro soft biedt, zijn gebaseerd op tien duizenden uitingen. U moet voor bereid zijn om ten minste enkele duizenden uitingen te registreren om een aangepaste stem voor productie kwaliteit te maken.

Controleer het script zorgvuldig op fouten. Als dat mogelijk is, moet iemand anders deze ook controleren. Wanneer u het script via uw talen door lopen, zult u waarschijnlijk nog enkele fouten ondervangen.

### <a name="script-format"></a>Script indeling

U kunt uw script schrijven in micro soft Word. Het script is voor gebruik tijdens de opname sessie, zodat u het kunt instellen op een manier waar u gemakkelijk aan de slag gaat. Maak het tekst bestand dat door de aangepaste spraak Portal afzonderlijk wordt vereist.

Een Basic-script indeling bevat drie kolommen:

* Het nummer van de utterance, beginnend bij 1. Met nummering kan iedereen in de Studio gemakkelijk verwijzen naar een bepaalde utterance ("laten we proberen nummer 356 opnieuw"). U kunt de functie nummering van alinea's van Word gebruiken om de rijen van de tabel automatisch te nummeren.
* Een lege kolom waarin u het nummer of de tijd code van elke utterance schrijft, zodat u deze kunt vinden in de voltooide opname.
* De tekst van de utterance zelf.

![Voorbeeldscript](media/custom-voice/script.png)

> [!NOTE]
> De meeste Studios records in korte segmenten worden *ook wel meegenomen.* Deze hebben doorgaans 10 tot 24 uitingen. U hoeft alleen maar het aantal nummers te volgen om later een utterance te vinden. Als u in een studio vastlegt dat liever meer opnamen maakt, wilt u in plaats daarvan de tijd code noteren. De Studio krijgt een prominente tijd weergave.

Zorg ervoor dat er voldoende ruimte is na elke rij om notities te schrijven. Zorg ervoor dat er geen utterance is verdeeld over de pagina's. Nummer de pagina's en druk uw script af op één zijde van het papier.

U kunt drie exemplaren van het script afdrukken: één voor het talen, één voor de engineer en één voor de regisseur (u). Een paper clip gebruiken in plaats van nietmachine: een ervaren Voice Artist scheidt de pagina's om te voor komen dat er ruis wordt gemaakt wanneer de pagina's worden ingeschakeld.

### <a name="legalities"></a>Juridische zaken

Onder copyright recht kan een actor het lezen van auteurs rechtelijk beschermde tekst de prestaties zijn waarvoor de auteur van het werk moet worden gecompenseerd. Deze prestaties zullen niet worden herkend in het eind product, de aangepaste stem. Ook is de legaal gebruik van een auteurs rechtelijk beschermd werk voor dit doel niet goed tot stand gebracht. Micro soft kan geen juridisch advies bieden over dit probleem; Neem contact op met uw eigen adviseur.

Gelukkig is het mogelijk om deze problemen volledig te voor komen. Er zijn veel tekst bronnen die u kunt gebruiken zonder toestemming of licentie.

|Tekst bron|Beschrijving|
|-|-|
|[CMU Arctic verzameling](http://festvox.org/cmu_arctic/)|Ongeveer 1100 zinnen die u hebt geselecteerd in auteurs rechtelijk beschermde werken speciaal voor gebruik in spraak-synthese projecten. Een uitstekend uitgangs punt.|
|Werkt niet meer<br>onder copyright|Werkt normaal gesp roken vóór 1923. Voor het Engels biedt [Project Gutenberg](https://www.gutenberg.org/) tien duizenden van deze werken. Mogelijk wilt u zich richten op nieuwere werken, omdat de taal dichter bij het moderne Engels ligt.|
|Overheids &nbsp; werkzaamheden|De door de Verenigde Staten overheid gemaakte werkzaamheden worden niet in het Verenigde Staten auteurs rechtelijk beschermd, hoewel de overheid copyright kan doen in andere landen/regio's.|
|Openbaar domein|Er wordt gewerkt waarvoor copyright expliciet is vrijgemaakt of die zijn toegewezen aan het open bare domein. Het is niet mogelijk om auteurs volledig in sommige jurisdicties te ontzeggen.|
|Permissively-gelicentieerde werken|Gedistribueerd onder een licentie zoals Creative Commons of de versie van de GNU gratis documentatie (GFDL). Wikipedia maakt gebruik van de GFDL. Sommige licenties kunnen echter beperkingen opleggen aan de prestaties van de Gelicentieerde inhoud die van invloed kan zijn op het maken van een aangepast spraak model. Lees de licentie daarom aandachtig door.|

## <a name="recording-your-script"></a>Uw script opnemen

Neem uw script op in een professionele opname studio die is gespecialiseerd in spraak werk. Ze hebben een opname stand, de juiste apparatuur en de juiste mensen om deze te laten werken. Het betaalt niet skimp bij de registratie.

Bespreek uw project met de registratie technicus van Studio en luister naar advies. De opname moet weinig of geen dynamische bereik compressie hebben (Maxi maal 4:1). Het is essentieel dat de audio consistente volume en een hoge signaal-naar-ruis verhouding heeft, terwijl er geen ongewenste geluiden zijn.

### <a name="do-it-yourself"></a>Doe het zelf

Als u de opname zelf wilt maken, in plaats van naar een opname studio te gaan, is hier een korte primer. Dankzij de toename van de opname en podcasting van uw huis is het eenvoudiger dan ooit om een goede opname advies en bronnen online te vinden.

Uw "opname stand" moet een kleine kamer zijn zonder merkbaar echo of "room-Toon". Dit moet zo stil en soundproof mogelijk zijn. Drapes op de wanden kunnen worden gebruikt om echo's te verminderen en het geluid van de kamer te neutraliseren of ' dood '.

Gebruik een hoogwaardige studio longer-microfoon ("MIC" voor korte) die is bedoeld voor het opnemen van spraak. Sennheiser, AKG en zelfs nieuwere zoom mics kunnen goede resultaten opleveren. U kunt een Mic kopen of een gehuurde van een lokale onderneming met een hoogwaardige audio weergave. Zoek er een met een USB-interface. Met dit type Mic kunt u het preamp-microfoon element en het analoge-naar-digitale-converter combi neren in één pakket, waardoor u Application kunt vereenvoudigen.

U kunt ook een analoge microfoon gebruiken. Veel huur huizen bieden ' geoogste ' gerenommeerde voor hun spraak personage. Houd er rekening mee dat professioneel analoog vistuig gebruikmaakt van evenwichtige XLR-connectors, in plaats van de 1/4-inch stekker die wordt gebruikt in consumenten apparatuur. Als u analoog bent, hebt u ook een preamp en een computer audio-interface met deze connectors nodig.

Installeer de microfoon op een standaard-of een opvallende en installeer een pop-filter voor de microfoon om ruis te elimineren van "plosive"-mede klinkers als "p" en "b". Sommige microfoons worden geleverd met een suspensie koppeling waarmee ze worden geïsoleerd van trillingen in de standaard, wat handig is.

De stem talen moeten op een consistente afstand van de microfoon blijven staan. Gebruik tape op de vloer om te markeren waar ze zich bevinden. Als het gaat om het goed keuren van de talen, neemt u speciale aandacht voor het bewaken van de microfoon afstand en vermijdt u lawaai voor stoel.

Gebruik een standaard om het script te bewaren. Vermijd Angling de standaard, zodat deze geluid naar de microfoon kan weer spie gelen.

De persoon die de opname apparatuur, de engineer, moet zich in een afzonderlijke ruimte van het talen systeem bevinden, met een andere manier om te praten met de talen in de opname stand (een *Talkback-circuit).*

De opname moet zo weinig mogelijk ruis bevatten, met als doel een verhouding van een signaal naar ruis van 80-dB of beter.

Luister nauw naar een opname van stilte in het ' stand-by ', wat een wille keurige ruis is en Elimineer de oorzaak. Veelvoorkomende geluids bronnen zijn lucht ventilatie, fluorescerende licht ballasten, verkeer op nabijgelegen weg en ventilatoren (zelfs op notebook Pc's kunnen ventilatoren zijn). Bij microfoons en kabels kan een stroom storing worden opgenomen in de buurt van wisselstroom bedrading, meestal een Hum of de zoem. Een zoem kan ook worden veroorzaakt door een *wegdek*. dit wordt veroorzaakt door het koppelen van apparatuur aan meer dan één elektrisch circuit.

> [!TIP]
> In sommige gevallen kunt u mogelijk een equalizer of een invoeg toepassing voor ruis reductie gebruiken om ruis te verwijderen uit uw opnamen, hoewel het altijd het beste is om het te stoppen op de bron.

Stel niveaus zo in dat het grootste deel van het beschik bare dynamische bereik van digitale registratie wordt gebruikt zonder overstappen. Dit betekent dat u de audio hardop hebt ingesteld, maar niet zo hard dat deze wordt vervormd. In de volgende afbeelding ziet u een voor beeld van een golf vorm van een goede opname:

![Een goede golf opname](media/custom-voice/good-recording.png)

Hier wordt het meren deel van het bereik (hoogte) gebruikt, maar de hoogste pieken van het signaal komen niet boven of onder in het venster terecht. U kunt ook zien dat de stilte in de opname ongeveer een dunne horizontale lijn is, wat aangeeft dat er een lage ruis vloer is. Deze opname heeft een acceptabele, dynamische verhouding tussen het bereik en signaal en ruis.

Neem rechtstreeks op de computer op via een audio-interface van hoge kwaliteit of een USB-poort, afhankelijk van de Mic die u gebruikt. Voor analoog moet u de audio keten eenvoudig: Mic, preamp, audio interface, computer gebruiken. U kunt een licentie voor zowel [Avid Pro-Hulpprogram ma's](https://www.avid.com/en/pro-tools) als [Adobe Audition](https://www.adobe.com/products/audition.html) maandelijks tegen een redelijke prijs. Als uw budget erg nauw keurig is, probeert u het gratis [Audacity](https://www.audacityteam.org/).

Neem een record op van 44,1 kHz 16-bits monophonic (CD-kwaliteit) of beter. De huidige geavanceerde status is 48 kHz 24 bits, als uw apparatuur dit ondersteunt. U maakt een voor beeld van uw audio naar 16 kHz 16-bits voordat u deze naar de aangepaste Voice Portal verzendt. Toch betaalt het een oorspronkelijke opname van hoogwaardige kwaliteit in de gebeurtenis bewerkingen.

In het ideale geval hebben verschillende mensen de rol van Director, Engineer en talen. Probeer het niet helemaal zelf. In een kneep kan één persoon zowel de regisseur als de engineer zijn.

### <a name="before-the-session"></a>Vóór de sessie

Om te voor komen dat Studio tijd verspilt, voert u door het script uit met uw stem talen voor de opname sessie. Hoewel de stem talen bekend zijn met de tekst, kunnen ze de uitspraak van onbekende woorden verduidelijken.

> [!NOTE]
> De meeste vastleg Studios bieden elektronische weer gave van scripts in de opname stand. In dit geval typt u uw doorgangs notities rechtstreeks in het document van het script. U kunt wel een papieren kopie maken om notities tijdens de sessie op te nemen. De meeste engineers zullen ook een harde kopie willen. En u wilt een derde afgedrukte kopie maken als back-up voor het talen voor het geval de computer niet beschikbaar is.

Uw stem talen kunnen vragen welk woord u wilt benadrukken in een utterance (het ' in het ' werkende woord '). Vertel hen dat u een natuurlijke Lees bewerking zonder een bepaalde nadruk wilt geven. De nadruk kan worden toegevoegd wanneer de spraak wordt gesynthesizerd; het mag geen deel uitmaken van de oorspronkelijke opname.

Leid het talen aan om woorden afzonderlijk aan te spreken. Elk woord van het script moet worden uitgesp roken als geschreven. Geluiden mogen niet worden wegge laten of worden Slurred, evenals gebruikelijk in ingrijpende spraak, *tenzij ze op die manier zijn geschreven in het script*.

|Geschreven tekst|Ongewenste informe uitspraak|
|-|-|
|u hoeft u nooit aan te bieden|u hoeft u nooit aan te bieden|
|Er zijn vier lichten|Er zijn vier lampjes|
|Hoe gaat het vandaag?|Hoe later gaat u vandaag nog aan de slag|
|zeg hallo bij mijn kleine vriend|zeg hallo bij mijn Lil ' vriend|

Het talen mag *geen* DISTINCT pauzes tussen woorden toevoegen. De zin moet nog steeds op natuurlijke wijze stromen, zelfs tijdens het klinken van een beetje formeel. Dit fijne onderscheid kan worden aanbevolen om aan de slag te gaan.

### <a name="the-recording-session"></a>De opname sessie

Maak een referentie registratie of een *overeenkomend bestand* van een typische utterance aan het begin van de sessie. Vraag het talender deze regel op elke pagina te herhalen. Telkens wanneer u de nieuwe record naar de verwijzing vergelijkt. Deze werk wijze helpt de talen consistent te blijven in volume, tijdelijke, Pitch en intonation. Ondertussen kan de engineer het bestand match gebruiken als referentie voor de niveaus en de algehele consistentie van het geluid.

Het bestand matching is vooral belang rijk wanneer u de opname na een onderbreking of op een andere dag hervat. U wilt deze een paar keer afspelen voor het talen en ze moeten elke keer herhalen totdat ze goed overeenkomen.

Voer uw talender uit om een diepe adem te nemen en wacht even voor elke utterance. Registreer een aantal seconden stilte tussen uitingen. Woorden moeten op dezelfde manier worden uitgesp roken wanneer ze worden weer gegeven, waarbij context wordt overwogen. Bijvoorbeeld: ' record ' is een andere uitspreekt van ' record ' als een zelfstandig naam woord.

Neem een goede stilte van vijf seconden op voor de eerste opname om de ' room-Toon ' vast te leggen. Op deze manier kunt u de aangepaste Voice Portal gebruiken om alle resterende ruis in de opnamen te compenseren.

> [!TIP]
> Alles wat u echt moet vastleggen is het stem talen, dus u kunt een monophonic-record (Single Channel) van alleen hun lijnen maken. Als u echter in stereo opneemt, kunt u het tweede kanaal gebruiken om de chatter in de beheer ruimte vast te leggen voor het vastleggen van de discussies over bepaalde regels of het duurt. Verwijder dit nummer uit de versie die is geüpload naar de aangepaste Voice Portal.

Luister nauw keurig, met behulp van hoofd telefoon, naar de prestaties van de Voice-talen. U bent op zoek naar een goede maar natuurlijke dicteer, juiste uitspraak en een gebrek aan ongewenste geluiden. Aarzel niet om te vragen of u een utterance die niet aan deze normen voldoet, opnieuw moet vastleggen.

> [!TIP]
> Als u een groot aantal uitingen gebruikt, heeft een enkele utterance mogelijk geen merk bare invloed op de resulterende aangepaste stem. Het kan beter zijn om uitingen met problemen te noteren, ze uit te sluiten van uw gegevensset en te zien hoe uw aangepaste stem wordt uitgeschakeld. U kunt altijd terugkeren naar de studio en de gemiste voor beelden later vastleggen.

Houd rekening met het nummer of de tijd code in uw script voor elke utterance. Vraag de engineer om elke utterance te markeren in de meta gegevens of het Hint blad van de registratie.

Maak regel matig pauzes en bied een drank waarmee u uw stem in goede vorm kunt houden.

### <a name="after-the-session"></a>Na de sessie

Op computers moderne vastleg Studios worden uitgevoerd. Aan het einde van de sessie ontvangt u een of meer audio bestanden, niet een tape. Deze bestanden zijn waarschijnlijk WAV-of AIFF-indeling in CD-kwaliteit (44,1 kHz 16-bits) of beter. 48 kHz 24-bits is gebruikelijk en gewenst. Hogere sampling frequenties, zoals 96 kHz, zijn over het algemeen niet nodig.

De aangepaste Voice Portal vereist dat elke verschafte utterance in zijn eigen bestand is. Elk audio bestand dat door Studio wordt geleverd, bevat meerdere uitingen. Daarom is de primaire taak na de productie om de opnamen te splitsen en voor te bereiden voor verzen ding. De registratie technicus heeft mogelijk markeringen in het bestand geplaatst (of heeft een afzonderlijk Hint blad gegeven) om aan te geven waar elke utterance begint.

Gebruik uw notities om precies te vinden wat u zoekt en gebruik vervolgens een hulp programma voor het bewerken van geluid, zoals [Avid Pro-Hulpprogram ma's](https://www.avid.com/en/pro-tools), [Adobe Audition](https://www.adobe.com/products/audition.html)of de gratis [Audacity](https://www.audacityteam.org/), om elke utterance naar een nieuw bestand te kopiëren.

Houd alleen ongeveer 0,2 seconden stilte aan het begin en het einde van elke clip, met uitzonde ring van de eerste. Dit bestand moet beginnen met een volledige stilte tijd. Gebruik geen audio-editor om delen van het bestand op de achtergrond af te zetten. De ' room-Toon ' helpt de aangepaste spraak algoritmen te gebruiken voor het compenseren van een rest achtergrond geluid.

Luister zorgvuldig naar elk bestand. In deze fase kunt u kleine ongewenste geluiden bewerken die u tijdens de opname hebt gemist, zoals een lichte lip-Smack vóór een regel, maar let erop dat u geen daad werkelijke spraak hoeft te verwijderen. Als u een bestand niet kunt herstellen, verwijdert u het uit uw gegevensset en ziet u dat u dit hebt gedaan.

Converteer elk bestand naar 16 bits en een sample frequentie van 16 kHz voordat u opslaat. Als u de Studio chatter hebt vastgelegd, verwijdert u het tweede kanaal. Sla elk bestand op in de WAV-indeling en noem de bestanden met het utterance nummer uit het script.

Maak tot slot de *transcript* die elk WAV-bestand koppelt aan een tekst versie van de bijbehorende utterance. Het [maken van aangepaste stemmen](./how-to-custom-voice-create-voice.md) bevat details over de vereiste indeling. U kunt de tekst rechtstreeks vanuit uw script kopiëren. Maak vervolgens een zip-bestand van de WAV-bestanden en de tekst transcript.

Archiveer de oorspronkelijke opnamen op een veilige plaats voor het geval u ze later nodig hebt. Bewaar ook het script en de notities.

## <a name="next-steps"></a>Volgende stappen

U bent klaar om uw opnamen te uploaden en uw aangepaste stem te maken.

> [!div class="nextstepaction"]
> [Aangepaste spraak lettertypen maken](./how-to-custom-voice-create-voice.md)
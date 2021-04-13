---
title: Azure AI-hand leiding voor voorspellende onderhouds oplossingen-team data Science process
description: Een uitgebreide beschrijving van de gegevens wetenschap die voorspellende onderhouds oplossingen in meerdere verticale branches belicht.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 9b6cf2abfa797965d75bacd0b9c5e2eaf02b57a4
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308571"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Azure AI-hand leiding voor voorspellende onderhouds oplossingen

## <a name="summary"></a>Samenvatting

Voor speld onderhoud iseen populaire toepassing van Predictive Analytics die bedrijven in verschillende branches kan helpen bij het realiseren van het gebruik van hoge activa en besparingen in operationele kosten. Deze hand leiding bevat de bedrijfs-en analyse richtlijnen en best practices voor het ontwikkelen en implementeren van PdM-oplossingen met behulp van de [Microsoft Azure AI-platform](https://azure.microsoft.com/overview/ai-platform) technologie.

Voor starters introduceert deze hand leiding branchespecifieke bedrijfs scenario's en het proces van het kwalificeren van deze scenario's voor PdM. De gegevens vereisten en modelleer technieken voor het bouwen van PdM-oplossingen zijn ook opgenomen. De belangrijkste inhoud van de hand leiding bevindt zich op het data Science-proces, met inbegrip van de stappen voor het voorbereiden van gegevens, het maken van een model en het model uitoefening. Deze hand leiding bevat een reeks oplossings sjablonen waarmee u de ontwikkeling van PdM-toepassingen kunt versnellen om deze basis concepten aan te vullen. De hand leiding wijst ook naar nuttige trainings bronnen voor de arts om meer te weten te komen over de AI achter de gegevens wetenschap. 

### <a name="data-science-guide-overview-and-target-audience"></a>Overzicht van data Science Guide en doel groep
In de eerste helft van deze hand leiding worden veelvoorkomende zakelijke problemen beschreven, de voor delen van het implementeren van PdM om deze problemen op te lossen en een lijst met veelvoorkomende gebruiks voorbeelden. Besluit vormers (BDMs) van de onderneming profiteren van deze inhoud. De tweede helft legt de data Science achter PdM uit en biedt een lijst van PdM-oplossingen die zijn gemaakt met behulp van de principes beschreven in deze hand leiding. Het biedt ook leer paden en aanwijzers naar trainings materiaal. Technische besluit vormers (TDMs) zullen deze inhoud nuttig vinden.

| Beginnen met... | Als u bent... |
|:---------------|:---------------|
| [Business Case voor predictief onderhoud](#business-case-for-predictive-maintenance) |een BDM (Business Decision Maker) die op zoek is naar minder downtime en operationele kosten, en het gebruik van apparatuur verbeteren |
| [Gegevens wetenschap voor voor speld onderhoud](#data-science-for-predictive-maintenance) |een technisch besluit Maker (TDM) met een beoordeling van PdM-technologieën om inzicht te krijgen in de unieke gegevens verwerking en AI-vereisten voor voor speld onderhoud |
| [Oplossings sjablonen voor voor speld onderhoud](#solution-templates-for-predictive-maintenance)|een software architect of AI-ontwikkelaar die snel een demo of een proef van het concept bekijkt |
| [Trainings bronnen voor predictief onderhoud](#training-resources-for-predictive-maintenance) | een of meer van de bovenstaande en u wilt meer informatie over de basis concepten achter de gegevens wetenschap, hulpprogram ma's en technieken.

### <a name="prerequisite-knowledge"></a>Vereiste kennis
De BDM-inhoud verwacht niet dat de lezer een eerdere data Science-kennis heeft. Voor de TDM-inhoud is basis kennis van statistieken en gegevens wetenschap nuttig. U wordt aangeraden kennis te hebben van Azure data-en AI-Services, Python, R, XML en JSON. AI-technieken worden geïmplementeerd in Python-en R-pakketten. Oplossings sjablonen worden geïmplementeerd met Azure-Services, hulpprogram ma's voor ontwikkel aars en Sdk's.

## <a name="business-case-for-predictive-maintenance"></a>Business Case voor predictief onderhoud

Bedrijven vereisen dat essentiële apparatuur wordt uitgevoerd met een piek efficiëntie en gebruik om het rendement van investeringen te realiseren. Deze activa kunnen variëren van vliegtuig motoren, turbines, liften of industriële koelers, die een fantastische prijs opdoen op alledaagse apparaten zoals fotocopiers, koffie machines of water kouden.
- Standaard zijn de meeste bedrijven afhankelijk van het _corrigerende onderhoud_, waarbij onderdelen worden vervangen als en wanneer ze mislukken. Corrigerende onderhouds werkzaamheden zorgen ervoor dat onderdelen volledig worden gebruikt (waardoor de levens duur van het onderdeel niet wordt verspild), maar dat de kosten van het bedrijf in downtime, arbeid en niet-geplande onderhouds vereisten (uit-of ongeschikte locaties) worden bespaard.
- Op het volgende niveau gebruiken bedrijven  _preventief onderhoud_, waar ze de nuttige levens duur voor een deel bepalen, en deze hand haven of vervangen vóór een storing. Preventief onderhoud voor komt ongeplande en onherstelbare storingen. Maar de hoge kosten van de geplande downtime, onder het gebruik van het onderdeel tijdens de levens duur, en arbeid blijven actief.
- Het doel van _voorspellend onderhoud_ is het optimaliseren van het evenwicht tussen corrigerend en preventief onderhoud door _just-in-time_ vervanging van onderdelen in te scha kelen. Deze aanpak vervangt deze onderdelen alleen wanneer ze zich dicht bij een fout voordoen. Door de levens duur van het onderdeel (vergeleken met preventief onderhoud) uit te breiden en niet-gepland onderhoud en arbeids kosten te verminderen (meer dan correct onderhoud), kunnen bedrijven kosten besparingen en concurrentie voordelen verkrijgen.

## <a name="business-problems-in-pdm"></a>Zakelijke problemen in PdM
Bedrijven hebben een hoog operationeel risico vanwege onverwachte fouten en hebben een beperkt inzicht in de hoofd oorzaak van problemen in complexe systemen. Enkele belang rijke zakelijke vragen zijn:

- Detecteer afwijkingen in apparatuur of systeem prestaties of-functionaliteit.
- Voors pellen of een asset in de nabije toekomst kan mislukken.
- De resterende levens duur van een Asset schatten.
- De belangrijkste oorzaken van het mislukken van een Asset identificeren.
- Bepaal welke onderhouds acties moeten worden uitgevoerd, op wanneer, op een activum.

Typische doel stellingen van PdM zijn:

- Beperk operationeel risico op essentiële apparatuur.
- Verhoog het rendement van de activa door fouten te voors pellen voordat ze optreden.
- Beheer de kosten van onderhoud door just-in-time-onderhouds bewerkingen in te scha kelen.
- Verlaag klant Attrition, verbeter de merk afbeelding en de omzet verloren.
- Lagere voorraad kosten door de voorraad niveaus te verlagen door het bestel punt te voors pellen.
- Detecteer patronen die zijn verbonden met verschillende onderhouds problemen.
- Geef Kpi's (Key Performance Indica tors) op, zoals status scores voor de voor waarden van de activa.
- Schatting van de resterende levens duur van activa.
- Aanbevelingen voor tijdige onderhouds activiteiten.
- Schakel just-in-time-inventaris in door order datums te schatten voor vervanging van onderdelen.

Deze doel stellings instructies zijn de start punten voor:

- _gegevens wetenschappers_ om specifieke voorspellende problemen te analyseren en op te lossen.
- _Cloud architecten en-ontwikkel aars_ om een end-to-end-oplossing samen te brengen.

## <a name="qualifying-problems-for-predictive-maintenance"></a>Problemen kwalificeren voor predictief onderhoud
Het is belang rijk om te benadrukken dat niet alle use-cases of bedrijfs problemen effectief kunnen worden opgelost door PdM. Er zijn drie belang rijke criteria die moeten worden overwogen tijdens de selectie van het probleem:

- Het probleem moet voorspellend zijn; dat wil zeggen dat er een doel of een resultaat moet zijn om te voors pellen. Het probleem moet ook een leeg pad van de actie hebben om fouten te voor komen wanneer ze worden gedetecteerd.
- Het probleem moet een record bevatten van de operationele geschiedenis van de apparatuur die _zowel goede als slechte resultaten_ bevat. De set acties die zijn ondernomen om onjuiste resultaten te verhelpen, moet ook beschikbaar zijn als onderdeel van deze records. Fout rapporten, onderhouds logboeken met prestatie vermindering, herstel en vervangen logboeken zijn ook belang rijk. Daarnaast zijn er reparaties uitgevoerd om ze te verbeteren en vervangende records zijn ook nuttig.
- De vastgelegde geschiedenis moet worden weer gegeven in _relevante_ gegevens die _voldoende_ kwaliteit hebben om de use-case te ondersteunen. Zie voor meer informatie over relevantie en bekrachtiging van [gegevens, vereisten voor voor speld onderhoud](#data-requirements-for-predictive-maintenance).
- Ten slotte moet het bedrijf domein experts hebben die duidelijk inzicht hebben in het probleem. Ze moeten rekening houden met de interne processen en procedures om de analist te helpen bij het begrijpen en interpreteren van de gegevens. Ze moeten ook de benodigde wijzigingen aanbrengen in bestaande bedrijfs processen om de juiste gegevens te verzamelen voor de problemen, indien nodig.

## <a name="sample-pdm-use-cases"></a>Voor beeld van PdM-use cases
Deze sectie richt zich op een verzameling van PdM-gebruiks gevallen van verschillende branches, zoals lucht vaart, Hulpprogram Ma's en Trans Port. Elke sectie begint met een bedrijfs probleem en behandelt de voor delen van PdM, de relevante gegevens van het zakelijke probleem en tot slot de voor delen van een PdM-oplossing.

| Bedrijfs probleem | Voor delen van PdM |
|:-----------------|-------------------|
|**Luchtvaart**      |                   |
|_Vlucht vertraging en annuleringen_ vanwege mechanische problemen. Fouten die niet op tijd kunnen worden hersteld, kunnen ertoe leiden dat vluchten worden geannuleerd en de planning en bewerkingen worden verstoord. |PdM-oplossingen kunnen de kans op een vertraagde of geannuleerde lucht vaartuig voors pellen als gevolg van mechanische storingen.|
|_Defecte vliegtuig motor onderdelen_: vervanging van het vliegtuig motor onderdeel ligt onder de meest voorkomende onderhouds taken binnen de luchtvaart industrie. Onderhouds oplossingen vereisen een zorgvuldig beheer van Beschik baarheid, levering en planning van onderdeel voorraad|Het verzamelen van informatie over betrouw baarheid van onderdelen kan leiden tot aanzienlijke vermindering van de investerings kosten.|
|**Financieel** |                         |
|_ATM-fout_ is een veelvoorkomend probleem binnen de Bank-industrie. Het probleem is hier het rapporteren van de kans dat een ATM-trans actie voor contante opname wordt onderbroken als gevolg van een storing in het papier of een deel fout in de kassa dispenser. Op basis van voor spellingen van transactie fouten kan ATMs proactief worden onderhouden om te voor komen dat er fouten optreden.| In plaats van de computer in staat te stellen een failover uit te voeren voor een trans actie, is het wenselijk alternatief om de computer te Program meren om de service te weigeren op basis van de voor spelling.|
|**Energie** |                          |
|_Wind turbine-storingen_: wind turbines zijn de belangrijkste energie bronnen in omgevings bewuste landen/regio's, en omvatten hoge kapitaal kosten. Een belang rijk onderdeel in wind turbines is de generator motor, waarvan de fout de turbine ingaat weer geven. Het is ook zeer kostbaar om te herstellen.|Het voors pellen van Kpi's zoals MTTF (gemiddelde time to failure) kan de energie bedrijven helpen turbine-fouten te voor komen en minimale downtime te garanderen. Bij storings kansen worden technici geïnformeerd over het bewaken van turbines die waarschijnlijk binnenkort zullen mislukken, en plannen op tijd gebaseerde onderhouds regelingen. Voorspellende modellen bieden inzicht in verschillende factoren die bijdragen aan de fout, waardoor technici beter inzicht krijgen in de hoofd oorzaken van problemen.|
|_Storingen van de circuit onderbreker_: voor de distributie van elektriciteit naar huizen en bedrijven moeten stroom lijnen altijd operationeel zijn om de energie levering te garanderen. Circuit onderbrekers helpen bij het beperken of voor komen van schade aan energie lijnen tijdens overbelasting of ongunstige weers omstandigheden. Het bedrijfs probleem hier is het voor spelt storingen van circuit onderbrekers.| Met PdM-oplossingen kunt u de reparatie kosten verlagen en de levens duur van apparaten, zoals circuit onderbrekers, verhogen. Ze helpen de kwaliteit van het energie netwerk te verbeteren door onverwachte storingen en service onderbrekingen te verminderen.|
|**Transport en logistiek** |    |
|_Storingen in lift deuren_: grote lift bedrijven bieden een volledige stack-service voor miljoenen functionele liften over de hele wereld. De veiligheid, betrouw baarheid en uptime van de lift zijn de belangrijkste zorgen voor hun klanten. Deze bedrijven volgen deze en verschillende andere kenmerken via Sens oren, om hen te helpen bij het corrigeren en preventief onderhoud. In een lift is het meest prominente probleem van de klant storingen in de lift deuren. Het bedrijfs probleem in dit geval is het bieden van een Knowledge Base-voorspellende toepassing die de potentiële oorzaken van deur storingen voorspelt.| Liften zijn kapitaal investeringen voor een levens duur van 20-30 jaar. Elke potentiële verkoop kan dus zeer concurrerend zijn. Daarom zijn de verwachtingen voor service en ondersteuning hoog. Voor speld onderhoud kan deze bedrijven een voor deel hebben van hun concurrenten in hun product-en service aanbod.|
|_Wiel storingen_: account voor wiel fouten voor de helft van alle trein-en kosten miljarden aan de wereld wijde spoorweg industrie. Wiel storingen zorgen er ook voor dat rails achteruit gaan, waardoor het mogelijk is dat de rail voor tijdig wordt afgebroken. Spoorweg einden leiden tot onherstelbare gebeurtenissen zoals Derails. Om dergelijke instanties te vermijden, worden de prestaties van wielen gecontroleerd en op een preventieve manier vervangen. Het bedrijfs probleem hier is de voor spelling van wiel storingen.| Voor speld onderhoud van wielen helpt u bij het just-in-time vervangen van wielen |
|_Storingen_ in de Subway-trein: een grote reden voor vertragingen in de Subway-activiteiten zijn deur storingen bij het trainen van auto's. Het bedrijfs probleem is hier om storingen van de trein te voors pellen.|Als er een deur fout is opgetreden, of het aantal dagen tot een deur defect is, helpt het bedrijf de planning van de trein deur te optimaliseren.|

In de volgende sectie vindt u meer informatie over het realiseren van de voor delen van PdM die hierboven worden beschreven.

## <a name="data-science-for-predictive-maintenance"></a>Gegevens wetenschap voor voor speld onderhoud

Deze sectie bevat algemene richt lijnen voor de principes van data wetenschappen en de praktijk voor PdM. Het is bedoeld om een TDM, Solution architect of een ontwikkelaar inzicht te geven in de vereisten en het proces voor het bouwen van end-to-end AI-toepassingen voor PdM. U kunt deze sectie lezen, samen met een overzicht van de demo's en sjabloon testen die worden vermeld in [oplossings sjablonen voor predictief onderhoud](#solution-templates-for-predictive-maintenance). U kunt deze principes en aanbevolen procedures gebruiken om uw PdM-oplossing in azure te implementeren.

> [!NOTE]
> Deze hand leiding is niet bedoeld voor het leren van de Reader data Science. Er zijn verschillende nuttige bronnen voor het verder lezen van de sectie voor [trainings bronnen voor voor speld onderhoud](#training-resources-for-predictive-maintenance). De [oplossings sjablonen](#solution-templates-for-predictive-maintenance) die in de hand leiding worden vermeld, demonstreren enkele van deze AI-technieken voor specifieke PdM-problemen.

## <a name="data-requirements-for-predictive-maintenance"></a>Gegevens vereisten voor predictief onderhoud

Het succes van een leer proces is afhankelijk van (a) de kwaliteit van de duur van het leven en (b) de mogelijkheid van de kenniser. Voorspellende modellen leren patronen uit historische gegevens en stellen toekomstige resultaten voor op basis van deze waargenomen patronen. De voorspellende nauw keurigheid van een model is afhankelijk van de relevancy, de bestaans graad en de kwaliteit van de training en test gegevens. De nieuwe gegevens die gebruikmaken van dit model, moeten dezelfde functies en hetzelfde schema hebben als de trainings-en test gegevens. De functie kenmerken (type, dichtheid, distributie, enzovoort) van nieuwe gegevens moeten overeenkomen met die van de gegevens sets training en test. De focus van deze sectie is op deze gegevens vereisten.

### <a name="relevant-data"></a>Relevante gegevens

Ten eerste moeten de gegevens relevant zijn _voor het probleem_. Bekijk de hierboven beschreven situatie voor het gebruik van een _Wheel-fout_ . de trainings gegevens moeten functies bevatten die betrekking hebben op de wiel bewerkingen. Als het probleem zich voordoet om de storing van het  _tractie systeem_ te voors pellen, moeten de opleidings gegevens alle verschillende onderdelen voor het tractie systeem omvatten. De eerste case streeft naar een specifiek onderdeel, terwijl het tweede geval gericht is op het mislukken van een groter subsysteem. De algemene aanbeveling is om Voorspellings systemen te ontwerpen over specifieke onderdelen in plaats van grotere subsystemen, aangezien de laatste meer verspreide gegevens bevat. De domein expert (Zie [problemen kwalificeren voor voor speld onderhoud](#qualifying-problems-for-predictive-maintenance)) moet helpen bij het selecteren van de meest relevante subsets met gegevens voor de analyse. De relevante gegevens bronnen worden uitgebreid beschreven in [gegevens voorbereiding voor voor speld onderhoud](#data-preparation-for-predictive-maintenance).

### <a name="sufficient-data"></a>Voldoende gegevens
Er worden meestal twee vragen gesteld ten aanzien van gegevens over de fout geschiedenis: (1) ' hoeveel fout gebeurtenissen zijn er vereist voor het trainen van een model? ' (2) "hoeveel records worden als" voldoende beschouwd "?" Er zijn geen definitieve antwoorden, maar alleen regels voor de duim. Voor (1), meer het aantal fout gebeurtenissen, beter het model. Voor (2), en het exacte aantal fout gebeurtenissen is afhankelijk van de gegevens en de context van het probleem dat wordt opgelost. Als een machine echter te vaak niet kan worden gespiegeld, wordt deze door het bedrijf vervangen, waardoor de fout instanties worden verminderd. Hier is de richt lijnen van de domein expert belang rijk. Er zijn echter methoden om het probleem van _zeldzame gebeurtenissen_ op te lossen. Deze worden besproken in de sectie het verwerken van niet- [sluitende gegevens](#handling-imbalanced-data).

### <a name="quality-data"></a>Kwaliteits gegevens
De kwaliteit van de gegevens is kritiek: elke waarde van het Voorspellings kenmerk moet _nauw keurig_ zijn in combi natie met de waarde van de doel variabele. Gegevens kwaliteit is een goed bestudeerd gebied in statistieken en gegevens beheer, en is dus buiten het bereik voor deze hand leiding.

> [!NOTE]
> Er zijn verschillende bronnen en ondernemings producten voor het leveren van kwaliteits gegevens. Hieronder vindt u een voor beeld van verwijzingen:
> - Dasu, T, Johnson, T., experimentele gegevens analyse en gegevens reiniging, Wiley, 2003.
> - [Experimentele gegevens analyse, Wikipedia](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Hellerstein, J, kwantitatieve gegevens reiniging voor grote data bases](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de jonge, E, van der Loo, M, inleiding tot het opschonen van gegevens met R](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

## <a name="data-preparation-for-predictive-maintenance"></a>Gegevens voorbereiding voor voor speld onderhoud

### <a name="data-sources"></a>Gegevensbronnen

De relevante gegevens bronnen voor voorspellend onderhoud zijn, maar zijn niet beperkt tot:
- Geschiedenis van fouten
- Geschiedenis van onderhoud/reparatie
- Machine-werk omstandigheden
- Meta gegevens van apparatuur

#### <a name="failure-history"></a>Geschiedenis van fouten
Fout gebeurtenissen zijn zeldzaam in PdM-toepassingen. Bij het maken van voorspellings modellen moet het algoritme echter meer weten over het normale operationele patroon van een onderdeel, evenals de bijbehorende storings patronen. Daarom moeten de trainings gegevens voldoende aantal voor beelden uit beide categorieën bevatten. Onderhouds records en geschiedenis van vervangen onderdelen zijn goede bronnen voor het vinden van fout gebeurtenissen. Met de hulp van sommige domein kennis kunnen afwijkingen in de trainings gegevens ook als storingen worden gedefinieerd.

#### <a name="maintenancerepair-history"></a>Geschiedenis van onderhoud/reparatie
De onderhouds geschiedenis van een Asset bevat details over de vervangen onderdelen, de herstel activiteiten die zijn uitgevoerd, enzovoort. Met deze gebeurtenissen worden patronen voor degradatie vastgelegd. Het ontbreken van deze essentiële informatie in de trainings gegevens kan leiden tot misleidende model resultaten. De geschiedenis van de fout kan ook worden gevonden in de onderhouds geschiedenis als speciale fout codes of order datums voor onderdelen. Aanvullende gegevens bronnen die invloed hebben op fouten patronen, moeten worden onderzocht en door domein experts worden verschaft.

#### <a name="machine-operating-conditions"></a>Machine-werk omstandigheden
Gegevens stromen op basis van sensors (of andere) streaming van de apparatuur in bewerking is een belang rijke gegevens bron. Een belang rijke hypo these in PdM is dat de status van een computer tijdens de routine bewerking in de loop van de tijd wordt gedegradeerd. Er wordt naar verwachting tijd-variërende functies voor de gegevens die dit ouderdoms patroon vastleggen en eventuele afwijkingen die leiden tot degradatie. Het tijdelijke aspect van de gegevens is vereist voor de algoritme om de storingen en niet-mislukte patronen in de loop van de tijd te ontdekken. Op basis van deze gegevens punten leert u hoe veel meer tijds eenheden kunnen blijven werken voordat de machine uitvalt.

#### <a name="static-feature-data"></a>Gegevens van statische functie
Statische functies zijn meta gegevens over de apparatuur. Voor beelden zijn het merk, het model, de productie datum, de start datum van de service, de locatie van het systeem en andere technische specificaties.

Voor beelden van relevante gegevens voor de voor beelden van het [gebruik van PdM-aanvragen](#sample-pdm-use-cases) worden hieronder in de tabel beschreven:

| Toepassing | Voor beelden van relevante gegevens |
|:---------|---------------------------|
|_Vlucht vertraging en annuleringen_ | Vlucht gegevens in de vorm van vlucht poten en pagina Logboeken. Vlucht gegevens omvatten routerings gegevens, zoals vertrek/aankomst datum, tijd, lucht haven, layovers, enzovoort. Het pagina logboek bevat een reeks fout-en onderhouds codes die zijn vastgelegd door het personeel van het terrein onderhoud.|
|_Storing in vliegtuig motor onderdelen_ | Gegevens die worden verzameld uit Sens oren in het vlieg tuig die informatie geven over de toestand van de verschillende onderdelen. Onderhouds records helpen bij het identificeren van defecte onderdelen en toen ze werden vervangen.|
|_ATM-fout_ | Belezing van sensors voor elke trans actie (kas/controleren) en het afleggen van geld. Informatie over de tussenruimte meting tussen notities, de notitie breedte, de aankomst afstand van de notitie, kenmerken, enzovoort. Onderhouds records met fout codes, herstel gegevens, laatste keer dat de kassa dispenser is gevuld.|
|_Wind turbine-fout_ | Sens oren bewaken de turbine-omstandigheden, zoals Tempe ratuur, wind richting, gegenereerde stroom, Generator snelheid, enzovoort. Gegevens worden verzameld uit meerdere wind turbines van Wind-Farms die zich in verschillende regio's bevinden. Normaal gesp roken heeft elke turbine meerdere sensor leesingen voor het door geven van metingen met een vast tijds interval.|
|_Storingen van circuit onderbreker_ | Onderhouds logboeken die corrigerende, preventieve en systematische acties bevatten. Operationele gegevens die automatische en hand matige opdrachten bevatten die worden verzonden naar circuit onderbrekers, zoals voor open en Close-acties. Meta gegevens van apparaten, zoals de datum van productie, locatie, model enz. Specificaties van circuit onderbreker, zoals spannings niveaus, geolocatie en omgevings omstandigheden.|
|_Storingen in lift deuren_| De meta gegevens van de Lift, zoals het type Lift, de gefabriceerde datum, de onderhouds frequentie, het type gebouw, enzovoort. Operationele informatie, zoals het aantal deur cycli, de gemiddelde tijd voor het sluiten van de deur. Fout geschiedenis met oorzaken.|
|_Wiel fouten_ | Sensor gegevens die de wiel versnelling, het remmen, de afstand, de snelheid enz. Statische informatie over wielen zoals fabrikant, productie datum. Fout gegevens die zijn afgeleid van een part order-data base die order datums en aantallen volgen.|
|_Storingen in Subway-trein deuren_ | Opening en sluit tijden van de deur, andere operationele gegevens, zoals de huidige toestand van trein deuren. Statische gegevens zijn onder andere de kolom waarden voor activa-id, tijd en voor waarde.|

### <a name="data-types"></a>Gegevenstypen
Gezien de bovenstaande gegevens bronnen zijn de twee belangrijkste gegevens typen in het domein PdM:

- _Tijdelijke gegevens_: operationele telemetrie, computer voorwaarden, typen werk orders, prioriteits codes met tijds tempels op het moment van de registratie. Fout, onderhoud/herstel en gebruiks geschiedenis hebben ook tijds tempels die aan elke gebeurtenis zijn gekoppeld.
- _Statische gegevens_: computer functies en operator functies in het algemeen zijn statisch, omdat hierin de technische specificaties van computers of operator kenmerken worden beschreven. Als deze functies na verloop van tijd kunnen veranderen, moeten er ook tijds tempels aan zijn gekoppeld.

Voorspellings-en doel variabelen moeten worden voorverwerkt/getransformeerd in [numerieke, categorische en andere gegevens typen](https://www.statsdirect.com/help/basics/measurement_scales.htm) , afhankelijk van de algoritme die wordt gebruikt.

### <a name="data-preprocessing"></a>Voor verwerking van gegevens
Als een vereiste voor het ontwikkelen van functies, _moet u de_ gegevens voorbereiden op basis van verschillende stromen om een schema op te stellen van waaruit het eenvoudig is om onderdelen te bouwen. De gegevens eerst visualiseren als een tabel met records. Elke rij in de tabel vertegenwoordigt een trainings exemplaar en de kolommen bevatten _Voorspellings_ functies (ook wel onafhankelijke kenmerken of variabelen genoemd). Organiseer de gegevens zo dat de laatste kolom (men) het _doel_ (afhankelijke variabele) is. Wijs voor elk trainings exemplaar een _Label_ toe als de waarde van deze kolom.

Voor tijdelijke gegevens verdeelt u de duur van sensor gegevens in tijds eenheden. Elke record moet behoren tot een tijds eenheid voor een Asset _en moet afzonderlijke informatie_ bevatten. Tijds eenheden worden gedefinieerd op basis van bedrijfs behoeften in veelvouden van seconden, minuten, uren, dagen, maanden, enzovoort. De tijds eenheid hoeft _niet hetzelfde te zijn als de frequentie van gegevens verzameling_. Als de frequentie hoog is, kunnen de gegevens geen significant verschil van de ene eenheid naar de andere bevatten. Stel bijvoorbeeld dat omgevings temperatuur elke 10 seconden is verzameld. Het gebruik van datzelfde interval voor trainings gegevens heeft alleen betrekking op het aantal voor beelden zonder aanvullende informatie. Voor dit geval is een betere strategie het gebruiken van de gemiddelde gegevens over tien minuten of een uur op basis van de zakelijke rechtvaardiging.

Voor statische gegevens
- _Onderhouds records_: onbewerkte onderhouds gegevens hebben een activa-id en tijds tempel met informatie over onderhouds activiteiten die op een bepaald moment zijn uitgevoerd. Onderhouds activiteiten transformeren in _categorische_ -kolommen, waarbij elke categorie descriptor uniek wordt toegewezen aan een specifieke onderhouds actie. Het schema voor onderhouds records bevat de actie activa-id, tijd en onderhoud.

- _Fout records_: fouten of fout oorzaken kunnen worden vastgelegd als specifieke fout codes of fout gebeurtenissen die door specifieke bedrijfs voorwaarden worden gedefinieerd. In gevallen waarin de apparatuur meerdere fout codes heeft, moet de domein expert deze helpen identificeren die relevant zijn voor de doel variabele. Gebruik de resterende fout codes of voor waarden om _Voorspellings_ functies samen te stellen die met deze fouten overeenkomen. Het schema voor fout records zou de activa-id, de tijd, de fout of de fout reden kunnen bevatten, indien beschikbaar.

- _Meta gegevens van de computer en de operator_: de machine-en operator gegevens samen voegen in één schema om een Asset te koppelen aan de bijbehorende operator, samen met de bijbehorende kenmerken. Het schema voor computer voorwaarden zou activa-id, Asset-functies, operator-id en operator functies kunnen bevatten.

Andere stappen voor voor verwerking van gegevens zijn onder andere het _verwerken van ontbrekende waarden_ en _normalisatie_ van kenmerk waarden. Een gedetailleerde discussie valt buiten het bereik van deze hand leiding. Zie de volgende sectie voor een aantal nuttige Naslag informatie.

Met de bovenstaande voorverwerkte gegevens bronnen is de laatste trans formatie vóór functie techniek het samen voegen van de bovenstaande tabellen op basis van de Asset-id en tijds tempel. De resulterende tabel zou Null-waarden hebben voor de fout kolom wanneer de machine zich in een normale werking bevindt. Deze null-waarden kunnen worden toegerekend door een indicator voor een normale werking. Gebruik deze fout kolom om _labels voor het voorspellende model_ te maken. Zie de sectie over [model technieken voor voor speld onderhoud](#modeling-techniques-for-predictive-maintenance)voor meer informatie.

## <a name="feature-engineering"></a>Functie-engineering
Functie techniek is de eerste stap voorafgaand aan het model leren van de gegevens. De rol in het data Science proces [wordt hier beschreven](./create-features.md). Een _functie_ is een voorspellend kenmerk voor het model, zoals de Tempe ratuur, druk, trilling, enzovoort. Voor PdM is functie techniek een samen vatting van de status van een machine ten opzichte van historische gegevens die zijn verzameld over een bepaalde duur. In dat opzicht wijkt het af van de peers, zoals externe controle, afwijkings detectie en detectie van fouten. 

### <a name="time-windows"></a>Tijd Vensters
Externe controle omvat het rapporteren van de gebeurtenissen die vanaf _tijdstippen_ plaatsvinden. Afwijkende detectie modellen evalueren (Score) inkomende streams van gegevens om afwijkingen op te sporen vanaf tijdstippen. Fout detectie classificeert storingen van specifieke typen, aangezien er Points op tijd plaatsvinden. Daarentegen omvat PdM het voors pellen van fouten in een _toekomstige tijds periode_, op basis van functies die het gedrag van de computer voor de _historische_ periode vertegenwoordigen. Voor PdM zijn de functie gegevens van afzonderlijke tijdstippen te veel tijd om voorspellend te zijn. De gegevens voor elke functie moeten dus worden _smoothened_ door het samen voegen van gegevens punten in een periode van Windows.

### <a name="lag-features"></a>Vertragings functies
De zakelijke vereisten bepalen hoe ver het model in de toekomst moet voors pellen. Op zijn beurt kan deze duur bepalen hoe ver terug het model te zien heeft om deze voor spellingen te maken. Deze ' gezochte periode ' wordt de _vertraging_ genoemd en functies die zijn toegepast op deze vertragings periode worden _vertragings functies_ genoemd. In deze sectie worden de vertragings functies beschreven die kunnen worden samengesteld uit gegevens bronnen met tijds tempels en het maken van functies vanuit statische gegevens bronnen. Vertragings functies zijn _doorgaans_ genummerd.

> [!IMPORTANT]
> De grootte van het venster wordt bepaald via experimenten en moet worden voltooid met behulp van een domein expert. Hetzelfde behoud geldt voor de selectie en definitie van de functies van de vertraging, de aggregaties en het type Windows.

#### <a name="rolling-aggregates"></a>Rolling aggregaties
Voor elke record van een Asset wordt een rollend venster met de grootte "W" gekozen als het aantal tijds eenheden voor het berekenen van de aggregaties. De vertragings functies worden vervolgens berekend met behulp van de W-Peri Oden _vóór de datum_ van die record. In afbeelding 1 tonen de blauwe lijnen sensor waarden voor een activum voor elke tijds eenheid. Ze geven een gematigd gemiddelde van functie waarden over in een venster met een grootte van meer dan 3. Het voortschrijdende gemiddelde wordt berekend over alle records met tijds tempels in het bereik t<sub>1</sub> (oranje) tot t<sub>2</sub> (groen). De waarde voor W is doorgaans in minuten of uren, afhankelijk van de aard van de gegevens. Maar voor bepaalde problemen kan het kiezen van een grote W (12 maanden) de volledige geschiedenis van een activum bieden tot de tijd van de record.

![Afbeelding 1. Statistische functies voor Rolling](./media/predictive-maintenance-playbook/rolling-aggregate-features.png)

Afbeelding 1. Statistische functies voor Rolling

Voor beelden van Rolling aggregaten over een tijd venster zijn Count, Average, CUMESUM (cumulatief totaal), min/max-waarden. Daarnaast worden de verschillen, de standaard afwijking en het aantal uitschieters verder dan N standaard deviaties gebruikt. Hieronder vindt u voor beelden van statistische functies die kunnen worden toegepast voor de [use cases](#sample-pdm-use-cases) in deze hand leiding. 
- _Vlucht vertraging_: aantal fout codes in de afgelopen dag/week.
- Fout in het onderdeel van het _lucht vaartuig_: Rolling gemiddelde, standaard afwijking en som over de afgelopen dag, week, enzovoort. Deze metriek moet samen met de expert voor het zakelijke domein worden bepaald.
- _ATM-fouten_: Rolling gemiddelden, mediaan, bereik, standaard afwijking, aantal uitschieters tot meer dan drie standaard afwijkingen, bovenste en onderste CUMESUM.
- _Storingen in Subway_: het aantal gebeurtenissen in de afgelopen dag, week, twee weken etc.
- _Storingen in de circuit onderbreker_: aantal fouten in de afgelopen week, het jaar, drie jaar, enzovoort.

Een andere handige techniek in PdM is het vastleggen van trend wijzigingen, pieken en niveau wijzigingen met behulp van algoritmen die afwijkingen in gegevens detecteren.

#### <a name="tumbling-aggregates"></a>Tumblingvenstertriggers-aggregaties
Voor elke gelabelde record van een Asset wordt een venster met een grootte van _w-<sub>k</sub>_ gedefinieerd, waarbij _k_ het aantal vensters van grootte _w_ is. Aggregaties worden vervolgens gemaakt over _k_ _tumblingvenstertriggers Windows_ _w-k, w-<sub>(k-1)</sub>,..., w-<sub>2</sub>, W-<sub>1</sub>_ voor de peri Oden vóór de tijds tempel van een record. _k_ kan een klein getal zijn voor het vastleggen van korte-termijn effecten of een groot aantal voor het vastleggen van patronen met een lange termijn vermindering. (zie afbeelding 2).

![Afbeelding 2. Statistische tumblingvenstertriggers-functies](./media/predictive-maintenance-playbook/tumbling-aggregate-features.png)

Afbeelding 2. Statistische tumblingvenstertriggers-functies

Vertragings functies voor de wind turbines-gebruiks case kunnen bijvoorbeeld worden gemaakt met W = 1 en k = 3. Ze impliceren de vertraging voor elk van de afgelopen drie maanden, met behulp van de voor-en onderkant van de voor grond.

### <a name="static-features"></a>Statische functies

Technische specificaties van de apparatuur, zoals de datum van productie, model nummer, locatie, zijn enkele voor beelden van statische functies. Ze worden behandeld als _categorische_ -variabelen voor model lering. Enkele voor beelden voor het gebruik van de circuit onderbreker zijn spanning, actueel, energie capaciteit, transformator type en energie bron. Voor wiel fouten is het type banden wiel (legering VS staal) een voor beeld.

De gegevens voorbereidings inspanningen die tot nu toe worden besproken, moeten leiden tot de gegevens die worden georganiseerd, zoals hieronder wordt weer gegeven. Trainings-, test-en validatie gegevens moeten dit logische schema hebben (in dit voor beeld wordt de tijd in eenheden van dagen weer gegeven).

| Activa-ID | Tijd | \<Feature Columns> | Label |
| ---- | ---- | --- | --- |
| A123 |Dag 1 | . . . | . |
| A123 |Dag 2 | . . . | . |
| ...  |...   | . . . | . |
| B234 |Dag 1 | . . . | . |
| B234 |Dag 2 | . . . | . |
| ...  |...   | . . . | . |

De laatste stap in Feature engineering is de **labeling** van de doel variabele. Dit proces is afhankelijk van de model techniek. De model techniek is op zijn beurt afhankelijk van het bedrijfs probleem en de aard van de beschik bare gegevens. Labelen wordt in de volgende sectie besproken.

> [!IMPORTANT]
> Gegevens voorbereiding en functie techniek zijn net zo belang rijk als modelleer technieken voor het aankomen van succes volle PdM-oplossingen. De domein expert en de artsen moeten aanzienlijke tijd investeren in het aankomen van de juiste functies en gegevens voor het model. Hieronder vindt u een klein voor beeld van veel boeken over functie techniek:
> - Pyle, D. gegevens voorbereiding voor gegevens analyse (de Morgan Kaufmann-serie in Gegevensbeheer Systems), 1999
> - Zheng, A., Casari, A. Feature engineering voor Machine Learning: principes en technieken voor gegevens wetenschappers, O'Reilly, 2018.
> - Dong, G. Liu, H. (editors), functie techniek voor Machine Learning en gegevens analyse (Chapman & hal/CRC data mining en Knowledge Discovery Series), CRC pers, 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>Model technieken voor voor speld onderhoud

In deze sectie worden de belangrijkste modelleer technieken voor PdM-problemen beschreven, samen met de specifieke methoden voor het bouwen van labels. U ziet dat één model techniek kan worden gebruikt in verschillende branches. De model techniek wordt gekoppeld aan het data Science-probleem in plaats van de context van de gegevens die bij de hand zijn.

> [!IMPORTANT]
> De keuze van labels voor de fout cases en de labeling-strategie  
> in overleg met het domein expert moet worden bepaald.

### <a name="binary-classification"></a>Binaire classificatie
Binaire classificatie wordt gebruikt om te voors _pellen dat een stuk van apparatuur in een toekomstige periode uitvalt_ , met de naam van de _toekomstige periode X_. X wordt bepaald door het bedrijfs probleem en de gegevens die bij de hand worden geleverd, in overleg met het domein expert. Een aantal voorbeelden:
- _Mini maal vereiste doorloop tijd_ voor het vervangen van onderdelen, het implementeren van onderhouds resources, het uitvoeren van onderhoud om een probleem te voor komen dat in die periode zou kunnen optreden.
- het _minimum aantal gebeurtenissen_ dat kan plaatsvinden voordat er een probleem optreedt.

In deze techniek worden twee typen trainings voorbeelden aangeduid. Een positief voor beeld, _wat een fout aangeeft_, met label = 1. Een negatief voor beeld, waarmee normale bewerkingen worden aangeduid, met label = 0. De doel variabele en daarom zijn de label waarden _categorische_. Het model moet elk nieuw voor beeld identificeren, zoals waarschijnlijk mislukt of normaal werken tijdens de volgende X-tijds eenheden.

#### <a name="label-construction-for-binary-classification"></a>Aanleg van labels voor binaire classificatie
De vraag is: ' wat is de kans dat de activa in de volgende X-tijds eenheden mislukken? ' Om deze vraag te beantwoorden, labelt u X records vóór het mislukken van een activum als "about to fail" (label = 1) en labelt u alle andere records als "normaal" (label = 0). (zie afbeelding 3).

![Afbeelding 3. Labelen voor binaire classificatie](./media/predictive-maintenance-playbook/labelling-for-binary-classification.png)

Afbeelding 3. Labelen voor binaire classificatie

Hieronder vindt u voor beelden van de strategie voor labels voor enkele van de use cases.
- _Vlucht vertragingen_: X kan worden gekozen als één dag, om vertragingen in de komende 24 uur te voors pellen. Vervolgens worden alle vluchten die binnen 24 uur voor fouten vallen, aangeduid als 1.
- Afwijkings _storingen_ in de ATM: een doel is het bepalen van de kans op fouten bij een trans actie in het volgende uur. In dat geval worden alle trans acties die zijn opgetreden in het afgelopen uur van de fout aangeduid als 1. Om de kans op fouten te voors pellen bij de volgende N-valuta notities, worden alle notities die in de laatste N notities van een storing zijn afgemerkt aangeduid als 1.
- _Storingen van circuit onderbreker_: het doel is om de volgende storing van de circuit onderbreker te voors pellen. In dat geval wordt X gekozen als een toekomstige opdracht.
- _Problemen met de trein deuren_: X kan worden gekozen als twee dagen.
- _Wind turbine-fouten_: X kan worden gekozen als twee maanden.

### <a name="regression-for-predictive-maintenance"></a>Regressie voor predictief onderhoud
Regressie modellen worden gebruikt voor _het berekenen van de resterende levens duur (resterende levens duur) van een Asset_. RESTERENDE levens duur is gedefinieerd als de hoeveelheid tijd die een Asset operationeel is voordat de volgende fout optreedt. Elk voor beeld van een training is een record die deel uitmaakt van een tijds eenheid- _nY_ voor een Asset, waarbij _n_ het veelvoud is. Het model moet het resterende levens duur van elk nieuw voor beeld berekenen als een _doorlopend nummer_. Dit nummer geeft de resterende tijd voor de fout aan.

#### <a name="label-construction-for-regression"></a>Aanleg van labels voor regressie
De volgende vraag is: ' wat is de resterende levens duur (resterende levens duur) van de apparatuur? ' Voor elke record vóór de fout moet u het label berekenen als het aantal resterende tijds eenheden voordat de volgende storing optreedt. In deze methode zijn labels doorlopende variabelen. (Zie afbeelding 4)

![Afbeelding 4: Labels voor regressie](./media/predictive-maintenance-playbook/labelling-for-regression.png)

Afbeelding 4: Labels voor regressie

Voor regressie wordt labeling uitgevoerd met een verwijzing naar een fout punt. De berekening is niet mogelijk zonder te weten hoe lang het activum voor een fout heeft geduurd. In tegens telling tot binaire classificatie kunnen activa zonder storingen in de gegevens niet worden gebruikt voor model lering. Dit probleem wordt het best aangepakt door een andere statistische techniek die [overlevings analyse](https://en.wikipedia.org/wiki/Survival_analysis)wordt genoemd. Er kunnen wel mogelijke complicaties optreden bij het Toep assen van deze techniek op aanvragen van PdM die tijdgebonden gegevens met regel matige intervallen omvatten. Zie [Deze pagina](https://www.cscu.cornell.edu/news/news.php/stnews78.pdf)voor meer informatie over het opoverlevings analyse.

### <a name="multi-class-classification-for-predictive-maintenance"></a>Classificatie met meerdere klassen voor voor speld onderhoud
Classificatie technieken met meerdere klassen kunnen worden gebruikt in PdM-oplossingen voor twee scenario's:
- Voor speling _twee toekomstige uitkomsten_: het eerste resultaat is _een periode_ voor het mislukken van een Asset. Het activum wordt toegewezen aan een van de verschillende mogelijke Peri Oden. Het tweede resultaat is de kans op fouten in een toekomstige periode als gevolg _van een van de verschillende hoofd oorzaken_. Met deze voor spelling kunt u de onderhouds bemanning controleren op symptomen en onderhouds planningen plannen.
- _De meest waarschijnlijke hoofd oorzaak_ van een bepaalde fout voors pellen. Dit resultaat beveelt de juiste set onderhouds acties aan om een fout op te lossen. Een geclassificeerde lijst met hoofd oorzaken en aanbevolen reparaties kan technici helpen bij het bepalen van de herstel acties na een storing.

#### <a name="label-construction-for-multi-class-classification"></a>Aanleg van labels voor classificatie met meerdere klassen
De vraag is als volgt: ' wat is de kans dat een Asset mislukt in de volgende _Nz_ -eenheden, waarbij _n_ het aantal Peri Oden? ' Om deze vraag te beantwoorden, label nZ records vóór het mislukken van een Asset met behulp van tijds verzamelingen (3Z, 2Z, Z). Voorzie alle andere records als ' normaal ' (label = 0). In deze methode bevatten de doel variabele _categorische_ -waarden. (Zie afbeelding 5).

![Afbeelding 5. Voorspellings labels voor uitval tijd voor classificatie met een multi klasse](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-failure-time-prediction.png)

Afbeelding 5. Labelen voor classificatie met meerdere klassen voor de voor spelling van uitval tijd

De vraag is als volgt: "wat is de kans dat de activa in de volgende X tijds eenheden mislukken vanwege hoofd oorzaak/probleem _P <sub>i</sub>_?" waar _het_ aantal mogelijke hoofd oorzaken is. Als u deze vraag wilt beantwoorden, labelt u X records vóór het mislukken van een Asset als ' overs welke mislukken vanwege hoofd oorzaak _p <sub>i</sub>_' (label = _P <sub>i</sub>_). Voorzie alle andere records als ' normaal ' (label = 0). In deze methode zijn ook labels categorische (zie afbeelding 6).

![Afbeelding 6. Voor Spellings labels voor de hoofd oorzaak voor de classificatie met een klasse](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-root-cause-prediction.png)

Afbeelding 6. Labels voor de classificatie van meerdere klassen voor de voor spelling van hoofd oorzaken

Het model wijst een fout kans toe als gevolg van elke _P <sub>i</sub>_ en de kans op geen fout. Deze kansen kunnen worden besteld op basis van de grootte, zodat de problemen die in de toekomst waarschijnlijk zullen optreden, kunnen worden voor speld.

De volgende vraag is: "welke onderhouds acties worden u aanbevolen na een storing?" Voor het beantwoorden van deze vraag is het _niet nodig om een toekomstige horizon te worden gekozen_, omdat het model niet in de toekomst fout voorspelt. De meest waarschijnlijke hoofd oorzaak wordt alleen voor speld _wanneer de fout al is opgetreden_.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Trainings-, validatie-en test methoden voor predictief onderhoud
Het [proces van de team data Science](./overview.md) biedt een volledige dekking van het model Train-Test-validate Cycle. In deze sectie worden aspecten besproken die uniek zijn voor PdM.

### <a name="cross-validation"></a>Kruis validatie
Het doel van [Kruis validatie](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) is het definiëren van een gegevensset om het model in de trainings fase te testen. Deze gegevensset wordt de _validatieset_ genoemd. Deze techniek helpt u bij het beperken van _problemen zoals het_ overschaalpen en biedt inzicht in de manier waarop het model voor een onafhankelijke gegevensset wordt gegeneraliseerd. Dat wil zeggen een onbekende gegevensset, die van een echt probleem kan zijn. De trainings-en test routine voor PdM moet rekening houden met de tijd die nodig is voor een betere generalisatie van toekomstige gegevens.

Veel machine learning algoritmen zijn afhankelijk van een aantal Hyper parameters dat de prestaties van het model aanzienlijk kan wijzigen. De optimale waarden van deze Hyper parameters worden niet automatisch berekend wanneer het model wordt getraind. Ze moeten worden opgegeven door de data wetenschapper. Er zijn verschillende manieren om goede waarden van Hyper parameters te vinden.

Het meest voorkomende is een _Kruis validatie met k-vouwen_ waarmee de voor beelden wille keurig worden gesplitst in _k_ vouwen. Voer voor elke set Hyper parameters waarden het leer algoritme _k_ keer uit. Gebruik bij elke herhaling de voor beelden in de huidige vouw als een validatieset en de rest van de voor beelden als een Trainingsset. Train het algoritme over trainings voorbeelden en reken de prestatie gegevens over de validatie voorbeelden. Aan het einde van deze lus berekent u het gemiddelde van de metrische gegevens voor prestaties van _k_ . Kies voor elke set afstemming waarden de waarde die de beste gemiddelde prestaties hebben. De taak voor het kiezen van Hyper parameters is vaak een experimenteel karakter.

In problemen met PdM worden gegevens vastgelegd als een tijd reeks gebeurtenissen die afkomstig zijn uit verschillende gegevens bronnen. Deze records kunnen worden besteld op basis van de tijd van labelen. Als de gegevensset in _wille keurige volg orde_ wordt gesplitst in de trainings-en validatieset, _kunnen enkele voor beelden van de training later in de tijd worden opgegeven dan sommige validatie voorbeelden_. Toekomstige prestaties van afstemming-waarden worden geschat op basis van een aantal gegevens die zijn aangekomen _voordat_ het model werd getraind. Deze schattingen kunnen zich in de tussen tijd bevindt, vooral als de tijd reeks niet is gestationair en zich in de loop van de periode ontwikkelt. Als gevolg hiervan zijn de gekozen afstemming-waarden mogelijk suboptimaal.

De aanbevolen manier is om de voor beelden te splitsen in trainings-en validatie sets op een _tijdrovende_ manier, waarbij alle validatie voorbeelden later in de tijd vallen dan alle voor beelden van training. Voor elke set afstemming waarden traint u het algoritme voor de gegevensset van de training. Meet de prestaties van het model in dezelfde validatieset. Kies afstemming waarden die de beste prestaties weer geven. Afstemming-waarden die zijn gekozen door Train/validatie-splitsing leiden tot betere prestaties van het toekomstige model dan met de waarden die wille keurig door Kruis validatie worden gekozen.

Het uiteindelijke model kan worden gegenereerd door een leer algoritme te trainen op volledige trainings gegevens met behulp van de beste afstemming-waarden.

### <a name="testing-for-model-performance"></a>Testen op model prestaties
Zodra een model is gebouwd, is een schatting van de toekomstige prestaties van nieuwe gegevens vereist. Een goede schatting is de prestatie metriek van afstemming-waarden die worden berekend over de validatieset of een gemiddelde prestatie waarde die wordt berekend op basis van kruis validatie. Deze schattingen zijn vaak te optimistisch. Het bedrijf heeft mogelijk vaak enkele extra richt lijnen over hoe ze het model willen testen.

De aanbevolen manier voor PdM is de voor beelden op een _tijdrovende_ manier te splitsen in trainings-, validatie-en test gegevens sets. Alle test voorbeelden moeten later in de tijd vallen dan alle trainingen en validatie voorbeelden. Genereer het model na het splitsen en meet de prestaties zoals eerder beschreven.

Wanneer de tijds reeksen stationair zijn en eenvoudig te voors pellen, genereren zowel wille keurig als tijdgebonden benaderingen vergelijk bare schattingen van toekomstige prestaties. Maar wanneer de time series niet-stationair en/of moeilijk te voors pellen is, genereert de tijdgebonden benadering meer realistische schattingen van toekomstige prestaties.

### <a name="time-dependent-split"></a>Tijd afhankelijke splitsing
In deze sectie worden aanbevolen procedures beschreven voor het implementeren van tijd afhankelijke splitsing. Een tijd afhankelijke twee richtings splitsing tussen trainingen en test sets wordt hieronder beschreven.

Aannemen een stroom van gebeurtenissen met een tijds tempel, zoals metingen van verschillende Sens oren. Definieer functies en labels van training-en test voorbeelden over tijds perioden die meerdere gebeurtenissen bevatten. U kunt bijvoorbeeld voor binaire classificatie functies maken op basis van gebeurtenissen in het verleden en labels maken op basis van toekomstige gebeurtenissen binnen ' X ' tijds eenheden in de toekomst (Zie de secties over [functie techniek](#feature-engineering) en model technieken). Het frame van de labeling van een voor beeld komt dus later aan dan het tijds bestek van de functies.

Voor een tijdgebonden splitsing kiest u een time-outwaarde voor de _training T <sub>c</sub>_ waarmee een model kan worden getraind, waarbij Hyper parameters is afgestemd op het gebruik van historische gegevens tot T <sub>c</sub>. Om te voor komen dat toekomstige labels meer dan T<sub>c</sub> in de trainings gegevens worden gelekt, kiest u de meest recente tijd om de voor beelden van de training te markeren als X eenheden vóór T<sub>c</sub>. In het voor beeld weer gegeven in afbeelding 7 vertegenwoordigt elk vier kant een record in de gegevensset waarin functies en labels worden berekend, zoals hierboven wordt beschreven. In de afbeelding ziet u de records die moeten worden getraind en test sets voor X = 2 en W = 3:

![Afbeelding 7. Tijd afhankelijke splitsing voor binaire classificatie](./media/predictive-maintenance-playbook/time-dependent-split-for-binary-classification.png)

Afbeelding 7. Tijd afhankelijke splitsing voor binaire classificatie

De groene vier kanten vertegenwoordigen records die horen bij de tijds eenheden die kunnen worden gebruikt voor de training. Elk voor beeld van een training wordt gegenereerd door de laatste drie Peri Oden te overwegen voor het genereren van onderdelen, en twee toekomstige Peri Oden voor het labelen vóór T<sub>c</sub>. Wanneer een deel van de twee toekomstige Peri Oden groter is dan T<sub>c</sub>, moet u dat voor beeld uitsluiten van de gegevensset voor training, omdat er geen zicht baarheid wordt aangenomen buiten t<sub>c</sub>.

De zwarte vier kantjes vertegenwoordigen de records van de uiteindelijke gegevensset met labels die niet moeten worden gebruikt in de set met trainingen, op voor waarde dat hierboven wordt vermeld. Deze records worden ook niet gebruikt voor het testen van gegevens, omdat ze vóór T<sub>c</sub>vallen. Daarnaast zijn de labels tijd frames deels afhankelijk van het tijds bestek van de training. Dit is niet ideaal. Trainings-en test gegevens moeten afzonderlijke label tijd frames hebben om te voor komen dat label gegevens lekken.

De techniek die tot nu toe is besproken, maakt overlap ping tussen de voor beelden van training en testen die een tijds tempel in de buurt van T<sub>c</sub>hebben. Een oplossing voor een grotere schei ding is het uitsluiten van voor beelden die binnen een tijds eenheid van T<sub>c</sub> vallen van de testset. Maar zo'n agressieve splitsing is afhankelijk van de ruime Beschik baarheid van de gegevens.

Regressie modellen die worden gebruikt voor het voors pellen van resterende levens duur worden aanzienlijk beïnvloed door het lekkage probleem. Het gebruik van de wille keurige Splits methode leidt tot extreem meer. Voor regressie problemen moet de splitsing zodanig zijn dat de records die deel uitmaken van activa met storingen voordat T<sub>c</sub> naar de Trainingsset gaat. Records met activa die fouten hebben na de afsluit bewerkingen, gaan naar de testset.

Een andere best practice voor het splitsen van gegevens voor training en tests is het gebruik van een splitsing per activum-ID. De splitsing moet zodanig zijn dat geen van de activa die in de Trainingsset worden gebruikt, worden gebruikt bij het testen van de model prestaties. Met deze methode heeft een model een grotere kans om realistischere resultaten te bieden met nieuwe activa.

### <a name="handling-imbalanced-data"></a>Niet-sluitende gegevens verwerken
Als er meer voor beelden zijn van een klasse dan andere, wordt in de classificatie problemen aangegeven dat de gegevensset niet meer in _balans_ moet worden gegeven. In het ideale geval hebben voldoende vertegenwoordigers van elke klasse in de trainings gegevens de voor keur om onderscheid tussen verschillende klassen mogelijk te maken. Als één klasse kleiner is dan 10% van de gegevens, worden de gegevens als onbalans beschouwd. De ondervertegenwoordigde klasse wordt een _minderheids klasse_ genoemd.

Veel PdM-problemen hebben betrekking op dergelijke niet-sluitende gegevens sets, waarbij één klasse ernstig onderhevig is aan de andere klasse, of klassen. In sommige gevallen kan de klasse minderheid slechts 0,001% van de totale gegevens punten vormen. Onevenwichtige klasse is niet uniek voor PdM. Andere domeinen waarin storingen en afwijkingen zich in zeldzame gevallen voordoen, hebben een soortgelijk probleem voor voor beelden, fraude detectie en indringing van het netwerk. Deze fouten vormen de voor beelden van de minderheids klasse.

Met een klasse-onevenwichtigheid in gegevens is de prestaties van de meeste algoritmen van het leer proces aangetast, omdat ze het totale aantal fouten beperken. Voor een gegevensset met 99% negatief en 1% positieve voor beelden kan een model worden weer gegeven met een nauw keurigheid van 99% door alle exemplaren als negatief te labelen. Maar in het model worden alle positieve voor beelden verkeerd ingedeeld. Dus zelfs als de nauw keurigheid hoog is, is het algoritme niet nuttig. Daarom zijn conventionele evaluatie-metrische gegevens, zoals de _algehele nauw keurigheid van de fout_ , onvoldoende voor Learning. Als er niet-sluitende gegevens sets worden gebruikt, worden er voor de model evaluatie andere metrieken gehanteerd:
- Precisie
- Relevante overeenkomsten
- F1-scores
- Kosten gecorrigeerde ROC (eigendoms kenmerken van de ontvanger)

Zie [Model Evaluation](#model-evaluation)(Engelstalig) voor meer informatie over deze metrische gegevens.

Er zijn echter een aantal methoden waarmee u het probleem klasse kunt verhelpen. De twee belangrijkste zijn de _bemonsterings technieken_ en _kosten gevoelige lessen_.

#### <a name="sampling-methods"></a>Bemonsterings methoden
In het gebalanceerde leer proces wordt gebruikgemaakt van bemonsterings methoden voor het wijzigen van de opleidings gegevensset in een evenwichtige gegevensset. Bemonsterings methoden zijn niet van toepassing op de testset. Hoewel er verschillende bemonsterings technieken zijn, zijn de meeste direct doorstuur servers _wille keurige oversteek proef_ en _onder steek proeven_.

_Wille keurige oversteek proeven_ moeten een wille keurig voor beeld van een minderheids klasse selecteren, deze voor beelden repliceren en toevoegen aan de set met trainings gegevens. Daarom wordt het aantal voor beelden in minderheids klasse verhoogd en sluiten uiteindelijk het aantal voor beelden van verschillende klassen. Een nadeel van oversampling is dat meerdere exemplaren van bepaalde voor beelden ertoe kunnen leiden dat de classificatie te vaak te specifiek wordt. In het model kan de nauw keurigheid van een hoge training worden weer gegeven, maar de prestaties van onzichtbaar test gegevens zijn mogelijk suboptimaal.

Daarentegen _wordt een wille keurig voor_ beeld van een meerderheids klasse geselecteerd en worden de voor beelden uit de set met trainings gegevens verwijderd. Het verwijderen van voor beelden van de klasse meerderheid kan er echter toe leiden dat de classificatie belang rijke concepten van de hoofd klasse kan missen. _Hybride steek proeven_ waarbij de klasse minderheids meer wordt bemonsterd en de hoofd klasse is op hetzelfde moment een andere levensvat bare benadering.

Er zijn veel geavanceerde sampling technieken. De gekozen techniek is afhankelijk van de gegevens eigenschappen en resultaten van iteratieve experimenten door de gegevens wetenschapper.

#### <a name="cost-sensitive-learning"></a>Kosten gevoelige lessen
In PdM zijn storingen die deel uitmaken van de klasse minderheid meer belang rijker dan normale voor beelden. Daarom is de focus voornamelijk afhankelijk van de prestaties van het algoritme op fouten. Het onjuist voors pellen van een positieve klasse als een negatieve klasse kan meer dan vice versa kosten. Deze situatie wordt doorgaans aangeduid als ongelijk verlies of asymmetrische kosten van het verkeerd classificeren van elementen naar verschillende klassen. De ideale classificatie moet een hoge Voorspellings nauwkeurigheid bieden ten opzichte van de minderheids klasse zonder in te boeten voor de nauw keurigheid van de hoofd klasse.

Er zijn meerdere manieren om dit saldo te krijgen. Om het probleem van ongelijk verlies op te lossen, wijst u een hoge kosten toe aan mis-classificatie van de minderheids klasse en probeert u de totale kosten tot een minimum te beperken. Met algoritmen als _SVMs (Support Vector machines)_ wordt deze methode inherent toegepast, doordat de kosten van positieve en negatieve voor beelden tijdens de training kunnen worden opgegeven. Op dezelfde manier geven de Boosting methoden zoals _versterkte beslissings structuren_ doorgaans goede prestaties weer met gegevens die niet in evenwicht zijn.

## <a name="model-evaluation"></a>Modelevaluatie
Mis-classificatie is een belang rijk probleem voor PdM-scenario's waarbij de kosten van valse waarschuwingen voor het bedrijf hoog zijn. Zo kan een beslissing om een vlieg tuig op te leggen op basis van een onjuiste voor spelling van een engine storing, schema's en reis plannen verstoren. Het offline halen van een machine vanuit een assemblage lijn kan leiden tot verlies van omzet. Model evaluatie met de juiste waarden voor prestaties voor nieuwe test gegevens is daarom van cruciaal belang.

De gebruikelijke metrische gegevens voor prestaties die worden gebruikt voor het evalueren van PdM-modellen worden hieronder beschreven:

- [Nauw keurigheid](https://en.wikipedia.org/wiki/Accuracy_and_precision) is de populairste waarde die wordt gebruikt voor het beschrijven van de prestaties van een classificatie. Maar nauw keurigheid is gevoelig voor gegevens distributie en is een ingrijpende meting voor scenario's met niet-sluitende gegevens sets. In plaats daarvan worden er andere metrische gegevens gebruikt. Hulpprogram ma's als [Verwar ring matrix](https://en.wikipedia.org/wiki/Confusion_matrix) worden gebruikt om de nauw keurigheid van het model te berekenen en daarom te bepalen.
- De [precisie](https://en.wikipedia.org/wiki/Precision_and_recall) van PdM-modellen heeft betrekking op de frequentie van valse waarschuwingen. Een lagere precisie van het model komt doorgaans overeen met een hogere frequentie van valse waarschuwingen.
- Frequentie van [intrekken](https://en.wikipedia.org/wiki/Precision_and_recall) geeft aan hoeveel fouten in de testset correct zijn geïdentificeerd door het model. Hogere ophaal tarieven betekenen dat het model is geslaagd voor het identificeren van de werkelijke storingen.
- [F1 Score](https://en.wikipedia.org/wiki/F1_score) is het harmonische gemiddelde van Precision en intrekken, waarbij de waarde tussen 0 (slechtste) en 1 (beste) ligt.

Voor binaire classificatie
- De [werk curven van ontvangers (ROC)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) is ook een populaire metrische waarde. In ROC curven worden model prestaties geïnterpreteerd op basis van één vast besturings punt op de ROC.
- _Decile-tabellen_ en _Lift grafieken_ zijn echter meer informatie over PdM-problemen. Ze zijn alleen gericht op de positieve klasse (fouten) en bieden een complexere afbeelding van de algoritme prestaties dan ROC curven.
  - _Decile-tabellen_ worden gemaakt met behulp van test voorbeelden in een aflopende volg orde van fout kansen. De geordende voor beelden worden vervolgens gegroepeerd in deciles (10% van de voor beelden met de hoogste kans, vervolgens 20%, 30%, enzovoort). De verhouding (ware positieve snelheid)/(wille keurige basis lijn) voor elke decile helpt bij het schatten van de algoritme prestaties bij elke decile. De wille keurige basis lijn neemt waarden 0,1, 0,2, enzovoort.
  - Met een [Lift diagram](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) wordt het decile ware positieve rente vergeleken met een wille keurig positief positieve rente voor alle deciles. De eerste deciles zijn doorgaans de focus van de resultaten, omdat ze de grootste toename tonen. De eerste deciles kan ook worden gezien als representatief voor ' risico ', wanneer deze wordt gebruikt voor PdM.

## <a name="model-operationalization-for-predictive-maintenance"></a>Model uitoefening voor predictief onderhoud

Het voor deel van de data Science-oefening wordt alleen gerealiseerd wanneer het getrainde model operationeel is gemaakt. Dat wil zeggen dat het model moet worden geïmplementeerd in de bedrijfs systemen om voor spellingen te maken op basis van nieuwe, voorheen onbekende gegevens.  De nieuwe gegevens moeten op twee manieren overeenkomen met de _model handtekening_ van het getrainde model:
- alle functies moeten aanwezig zijn in elke logische instantie (zeg een rij in een tabel) van de nieuwe gegevens.
- de nieuwe gegevens moeten vooraf worden verwerkt en elk van de functies die zijn ontworpen, op exact dezelfde manier als de trainings gegevens.

Het bovenstaande proces wordt op veel manieren in academische en industrie literatuur vermeld. Maar alle volgende instructies betekenen hetzelfde:
- _Nieuwe gegevens scoren_ met behulp van het model
- _Het model Toep assen_ op nieuwe gegevens
- Het model _operationeel maken_
- Het model _implementeren_
- _Voer het model uit_ op basis van nieuwe gegevens

Zoals eerder vermeld, is model uitoefening voor PdM afwijkend van de peers. Scenario's met betrekking tot anomalie detectie en fout detectie implementeren doorgaans _online scores_ (ook wel _realtime scores_ genoemd). Hier vertrouwt _het model elk_ binnenkomend record en retourneert een voor spelling. Voor een afwijkings detectie is de voor spelling een indicatie dat er een afwijkend probleem is opgetreden (bijvoorbeeld: SVM van één klasse). Voor een fout detectie zou het type of de klasse van de fout zijn.

In tegens telling tot het uitvoeren van een _batch-Score_. Om te voldoen aan de model handtekening, moeten de functies in de nieuwe gegevens op dezelfde manier als de trainings gegevens worden ontworpen. Voor de grote gegevens sets die gebruikelijk zijn voor nieuwe gegevens, worden de functies geaggregeerd in een periode van Windows en gescoord in batch. Batch scores worden doorgaans uitgevoerd in gedistribueerde systemen zoals [Spark](https://spark.apache.org/) of [Azure batch](../../batch/batch-service-workflow-features.md). Er zijn een aantal alternatieven-beide:
- Streaming data-engines ondersteunen aggregatie over Windows in het geheugen. Daarom kan worden aangevoerd dat ze online scores ondersteunen. Deze systemen zijn echter geschikt voor compacte gegevens in smalle tijd Vensters of sparse elementen over bredere Vensters. Ze kunnen niet goed worden geschaald voor de uitgebreide gegevens in meer tijd Vensters, zoals wordt weer gegeven in PdM-scenario's.
- Als batch score niet beschikbaar is, is de oplossing om online scores aan te passen om nieuwe gegevens in kleine batches tegelijk af te handelen.

## <a name="solution-templates-for-predictive-maintenance"></a>Oplossings sjablonen voor voor speld onderhoud

In het laatste gedeelte van deze hand leiding vindt u een lijst met oplossingen voor de oplossings sjablonen, zelf studies en experimenten die in azure zijn geïmplementeerd. Deze PdM-toepassingen kunnen in sommige gevallen binnen enkele minuten in een Azure-abonnement worden geïmplementeerd. Ze kunnen worden gebruikt als proef van concept demo's, sandboxes om te experimenteren met alternatieven of Accelerators voor werkelijke productie-implementaties. Deze sjablonen bevinden zich in de [Azure AI Gallery](https://gallery.azure.ai) of [Azure github](https://github.com/Azure). Deze verschillende voor beelden worden gedurende een bepaalde periode in deze oplossings sjabloon getotaliseerd.

| # | Titel | Beschrijving |
|--:|:------|-------------|
| 2 | [Oplossings sjabloon Azure predictief onderhoud](https://github.com/Azure/AI-PredictiveMaintenance) | Een open-source oplossings sjabloon waarmee Azure ML-modellering en een volledige Azure-infra structuur kunnen worden ondersteund die ondersteuning bieden voor voorspellende onderhouds scenario's in de context van IoT externe controle. |
| 3 | [Deep Learning voor predictief onderhoud](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | Azure notebook met een demo oplossing voor het gebruik van LSTM-netwerken Short-Term (een klasse van Neural netwerken) voor predictief onderhoud, met een [blog bericht over dit voor beeld](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance).|
| 4 | [Azure predictief onderhoud voor B.v.](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | Een van de eerste PdM-oplossings sjablonen op basis van Azure ML v 1.0 voor het onderhoud van vlieg tuigen. Deze hand leiding is afkomstig uit dit project. |
| 5 | [Azure AI-werkset voor IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge) | AI in het IoT Edge met behulp van tensor flow; Toolkit verpakt uitgebreide leer modellen in Azure IoT Edge-compatibele docker-containers en maakt deze modellen beschikbaar als REST Api's.
| 6 | [Azure IoT-voor speld onderhoud](https://github.com/Azure/azure-iot-predictive-maintenance) | Azure IoT Suite PC'S-vooraf geconfigureerde oplossing. PdM-sjabloon voor vliegtuig onderhoud met IoT Suite. [Een ander document en een](/previous-versions/azure/iot-accelerators/about-iot-accelerators) [scenario](/previous-versions/azure/iot-accelerators/iot-accelerators-predictive-walkthrough) met betrekking tot hetzelfde project. |
| 7 | [Voorspellende onderhouds sjabloon met SQL Server R Services](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Demo van een resterend bruikbaar leven-scenario op basis van R Services. |
| 8 | [Model gids voor voor speld onderhoud](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | Functie voor het bijhouden van vlieg tuigen die zijn ontworpen met R met [experimenten](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) en [gegevens sets](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) en Azure notebook en [experimenten](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2) in AzureML v 1.0|

## <a name="training-resources-for-predictive-maintenance"></a>Trainings bronnen voor predictief onderhoud

Microsoft Azure biedt leer trajecten voor de basis concepten achter PdM-technieken, behalve inhoud en training over algemene AI-concepten en-prak tijken.

| Trainings resource  | Beschikbaarheid |
|:-------------------|--------------|
| [Microsoft Docs: rol gegevens wetenschapper](https://docs.microsoft.com/learn/roles/data-scientist) | Openbaar |
| [Microsoft Docs: AI-Engineer](https://docs.microsoft.com/learn/roles/ai-engineer) | Openbaar |
| [Microsoft Docs: rol gegevens ingenieur](https://docs.microsoft.com/learn/roles/data-engineer) | Openbaar |
| [Micro soft AI school](https://www.microsoft.com/ai/ai-school) | Openbaar |
| [LinkedIn Learning](https://www.linkedin.com/learning) | Openbaar |
| [Micro soft: afspeel lijsten op YouTube voor kunst matige intelligentie en analyse](https://www.youtube.com/c/MicrosoftAzure/playlists?view=50&sort=dd&shelf_id=7) | Openbaar |
| [Micro soft AI-weer gave](https://channel9.msdn.com/Shows/AI-Show) | Openbaar |
| [Overzicht van het AI-platform](https://azure.microsoft.com/overview/ai-platform/) | Openbaar |
| [AI lab](https://www.microsoft.com/ai/ai-lab) | Openbaar |
| [Micro soft AI](https://www.microsoft.com/AI) | Openbaar |
| [Microsoft Partner Network](https://partner.microsoft.com/training/training-center) | Partners |

Daarnaast worden gratis Massive (enorme open online cursussen) op AI online aangeboden door academische instellingen als Stanford en MIT, en andere onderwijs bedrijven.

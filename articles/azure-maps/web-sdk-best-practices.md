---
title: Aanbevolen procedures voor Azure Maps Web SDK | Microsoft Azure kaarten
description: Leer tips & slagen om uw gebruik van de Azure Maps Web SDK te optimaliseren.
author: rbrundritt
ms.author: richbrun
ms.date: 3/22/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: ba351053c876c31d945ec7e4127a5caebd6a71ce
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878039"
---
# <a name="azure-maps-web-sdk-best-practices"></a>Aanbevolen procedures voor Azure Maps Web SDK

Dit document richt zich op de aanbevolen procedures voor de Azure Maps Web-SDK, maar veel van de best practices en optimalisaties die worden beschreven, kunnen worden toegepast op alle andere Azure Maps Sdk's.

De Azure Maps Web-SDK biedt een krachtig canvas om grote ruimtelijke gegevens sets op verschillende manieren te renderen. In sommige gevallen zijn er meerdere manieren om gegevens op dezelfde manier weer te geven, maar afhankelijk van de grootte van de gegevensset en de gewenste functionaliteit kan de ene methode beter worden uitgevoerd dan andere. In dit artikel worden aanbevolen procedures en tips en trucs besproken om de prestaties te maximaliseren en een soepele gebruikers ervaring te creëren.

Over het algemeen kunt u bij het verbeteren van de prestaties van de kaart zoeken naar manieren om het aantal lagen en bronnen te verminderen en de complexiteit van de gegevens sets en rendering-stijlen te gebruiken.

## <a name="security-basics"></a>Basis beginselen van beveiliging

Het belangrijkste deel van uw toepassing is de beveiliging. Als uw toepassing niet is beveiligd, kan een hacker elke wille keurige toepassing Ruin, ongeacht hoe goed de gebruikers ervaring kan zijn. Hier volgen enkele tips om uw Azure Maps-toepassing veilig te maken. Wanneer u Azure gebruikt, moet u vertrouwd raken met de beveiligings hulpprogramma's die voor u beschikbaar zijn. Raadpleeg dit document voor een [Inleiding tot Azure-beveiliging](https://docs.microsoft.com/azure/security/fundamentals/overview).

> [!IMPORTANT]
> Azure Maps biedt twee verificatie methoden.
> * Verificatie op basis van een abonnement
> * Azure Active Directory authenticatie gebruiken Azure Active Directory in alle productie toepassingen.
> Verificatie op basis van een abonnement is eenvoudig en de meeste toewijzings platforms gebruiken een manier om uw gebruik van het platform te meten voor facturerings doeleinden. Dit is echter geen beveiligde vorm van verificatie en mag alleen lokaal worden gebruikt bij het ontwikkelen van apps. Sommige platforms bieden de mogelijkheid om te beperken welke IP-adressen en/of HTTP-verwijzende bronnen zich in aanvragen bevinden, maar deze informatie kan gemakkelijk worden vervalst. Als u abonnements sleutels gebruikt, moet u [ze regel matig roteren](how-to-manage-authentication.md#manage-and-rotate-shared-keys).
> Azure Active Directory is een Enter prise Identity-service met een groot aantal beveiligings functies en-instellingen voor allerlei toepassings scenario's. Micro soft raadt aan dat alle productie toepassingen die gebruikmaken van Azure Maps Azure Active Directory voor verificatie gebruiken.
> Meer informatie over het [beheren van verificatie in azure Maps](how-to-manage-authentication.md) in dit document.

### <a name="secure-your-private-data"></a>Uw persoonlijke gegevens beveiligen

Wanneer gegevens worden toegevoegd aan de Azure Maps interactieve toewijzings-Sdk's, wordt deze lokaal weer gegeven op het apparaat van de eind gebruiker en wordt er nooit een back-up naar Internet verzonden.

Als uw toepassing gegevens laadt die niet openbaar moeten zijn, moet u ervoor zorgen dat de gegevens worden opgeslagen op een veilige locatie, op een veilige manier worden benaderd en dat de toepassing zelf is vergrendeld en alleen beschikbaar is voor uw gewenste gebruikers. Als een van deze stappen wordt overgeslagen, heeft een niet-gemachtigde persoon toegang tot deze gegevens. Azure Active Directory kunt u helpen met het vergren delen van dit.

Raadpleeg deze zelf studie over [het toevoegen van verificatie aan uw web-app die wordt uitgevoerd op Azure app service](https://docs.microsoft.com/azure/app-service/scenario-secure-app-authentication-app-service)

### <a name="use-the-latest-versions-of-azure-maps"></a>De nieuwste versies van Azure Maps gebruiken

De Azure Maps Sdk's voeren regel matig beveiligings tests uit, samen met alle externe afhankelijkheids bibliotheken die door de Sdk's kunnen worden gebruikt. Een bekend beveiligings probleem wordt tijdig opgelost en is vrijgegeven voor productie. Als uw toepassing verwijst naar de meest recente primaire versie van de gehoste versie van de Azure Maps Web SDK, ontvangt deze automatisch alle secundaire versie-updates die beveiligings problemen bevatten.

Als u de Azure Maps Web-SDK zelf host via de module NPM, moet u ervoor zorgen dat u het caret-teken (^) gebruikt in combi natie met het pakket versie nummer van de Azure Maps NPM in uw `package.json` bestand, zodat het altijd naar de meest recente secundaire versie verwijst.

```json
"dependencies": {
  "azure-maps-control": "^2.0.30"
}
```

## <a name="optimize-initial-map-load"></a>Initiële toewijzings belasting optimaliseren

Wanneer een webpagina wordt geladen, wordt een van de eerste dingen die u wilt doen zo snel mogelijk beginnen met rendering, zodat de gebruiker niet op een leeg scherm wordt gesterd.

### <a name="watch-the-maps-ready-event"></a>De gebeurtenis voor het voorbereiden van kaarten bekijken

Op dezelfde manier is het zo dat als de kaart in eerste instantie zo snel mogelijk gegevens laadt, zodat de gebruiker niet naar een lege kaart kijkt. Omdat de toewijzing van resources asynchroon wordt geladen, moet u wachten tot de toewijzing klaar is om met elkaar te communiceren voordat u uw eigen gegevens op de kaart wilt weer geven. Er zijn twee gebeurtenissen die u kunt wachten, een `load` gebeurtenis en een `ready` gebeurtenis. De gebeurtenis laden wordt gestart nadat de kaart volledig is geladen de eerste kaart weergave en elke kaart tegel is geladen. De gebeurtenis ready wordt geactiveerd wanneer de minimale toewijzings bronnen die nodig zijn om te beginnen met de kaart. De gebeurtenis ready kan vaak worden geactiveerd in de helft van de laad gebeurtenis. zo kunt u beginnen met het laden van uw gegevens in de kaart eerder.

### <a name="lazy-load-the-azure-maps-web-sdk"></a>Lazy laadt de Azure Maps Web-SDK

Als de kaart niet meteen is vereist, wordt de Azure Maps Web-SDK door Lazy geladen totdat deze nodig is. Hiermee wordt het laden van de Java script-en CSS-bestanden die door de Azure Maps Web-SDK worden gebruikt, vertraagd tot dat nodig is. Een veelvoorkomend scenario waarbij dit gebeurt wanneer de kaart wordt geladen in een tab of flyout dat niet wordt weer gegeven bij het laden van de pagina.
In het volgende code voorbeeld ziet u hoe u het laden van de Azure Maps Web-SDK uitstelt totdat een knop wordt ingedrukt.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Lazy laadt de kaart" src="https://codepen.io/azuremaps/embed/vYEeyOv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de pen <a href='https://codepen.io/azuremaps/pen/vYEeyOv'>langzaam laden van de kaart</a> via Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="add-a-placeholder-for-the-map"></a>Een tijdelijke aanduiding voor de kaart toevoegen

Als de kaart even lang duurt om te laden vanwege netwerk beperkingen of andere prioriteiten binnen uw toepassing, kunt u een kleine achtergrond afbeelding toevoegen aan de kaart `div` als tijdelijke aanduiding voor de kaart. Hiermee wordt het vernietigen van de kaart gevuld `div` tijdens het laden.

### <a name="set-initial-map-style-and-camera-options-on-initialization"></a>Eerste kaart stijl en camera opties instellen bij initialisatie

Vaak willen apps de kaart naar een specifieke locatie of stijl laden. Soms worden ontwikkel aars gewacht totdat de kaart is geladen (of gewacht op de `ready` gebeurtenis) en vervolgens `setCemer` de `setStyle` functies of van de kaart gebruiken. Het duurt vaak langer om de gewenste initiële kaart weergave te openen omdat veel resources uiteindelijk worden geladen voordat de resources die nodig zijn voor de gewenste kaart weergave, worden geladen. Een betere benadering is het door geven van de gewenste kaart camera en stijl opties in de kaart wanneer deze wordt geïnitialiseerd.

## <a name="optimize-data-sources"></a>Gegevens bronnen optimaliseren

De Web-SDK heeft twee gegevens bronnen,

* **Geojson-bron**: ook bekend als de `DataSource` klasse, beheert gegevens van onbewerkte locaties lokaal in geojson-indeling. Geschikt voor kleine tot middel grote gegevens sets (van honderden duizenden functies).
* **Vector tegel bron**: bekend bij de `VectorTileSource` klasse, laadt gegevens die zijn opgemaakt als vector tegels voor de huidige kaart weergave, op basis van het Maps-systeem. Ideaal voor grote tot enorme gegevens sets (miljoenen of miljarden functies).

### <a name="use-tile-based-solutions-for-large-datasets"></a>Oplossingen op basis van een tegel gebruiken voor grote gegevens sets

Als u werkt met grotere gegevens sets die miljoenen functies bevatten, is de aanbevolen manier om optimaal te pres teren, de gegevens beschikbaar te maken met behulp van een oplossing aan de server zijde, zoals een vector-of raster afbeeldingen tegel service.  
Vector tegels zijn geoptimaliseerd voor het laden van alleen de gegevens die in de weer gave zijn geknipt naar het focus gebied van de tegel en gegeneraliseerd, zodat deze overeenkomen met de resolutie van de kaart voor het zoom niveau van de tegel.

Het [Azure Maps Creator-platform](creator-indoor-maps.md) biedt de mogelijkheid om gegevens op te halen in de indeling Vector tegel. Andere gegevens indelingen kunnen gebruikmaken van hulpprogram ma's zoals [Tippecanoe](https://github.com/mapbox/tippecanoe) of een van de lijsten met veel [resources op deze pagina](https://github.com/mapbox/awesome-vector-tiles).

Het is ook mogelijk om een aangepaste service te maken waarmee gegevens sets worden gerenderd als raster afbeelding tegels op de server en laad de gegevens met behulp van de TileLayer-klasse in de kaart-SDK. Dit biedt buitengewone prestaties omdat de kaart alleen maar een aantal tien installatie kopieën tegelijk hoeft te laden en te beheren. Er zijn echter enkele beperkingen bij het gebruik van raster tegels omdat de onbewerkte gegevens niet lokaal beschikbaar zijn. Een secundaire service is vaak vereist om elk type interactie-ervaring te stroom te geven, bijvoorbeeld om te ontdekken op welke vorm een gebruiker heeft geklikt. Daarnaast is de bestands grootte van een raster tegel vaak groter dan een gecomprimeerde vector tegel met gegeneraliseerde en zoom niveau geoptimaliseerde geometrische elementen.

Meer informatie over gegevens bronnen vindt u in het document [een gegevens bron maken](create-data-source-web-sdk.md) .

### <a name="combine-multiple-datasets-into-a-single-vector-tile-source"></a>Meerdere gegevens sets in één vector tegel bron combi neren

Hoe minder gegevens bronnen de kaart heeft om te beheren, des te sneller alle functies kunnen worden weer gegeven. In het bijzonder, wanneer het gaat om tegel bronnen, waarbij twee vector tegel bronnen gezamenlijk worden gecombineerd, wordt het aantal HTTP-aanvragen voor het ophalen van de tegels in de helft geknipt en wordt de totale hoeveelheid gegevens iets kleiner omdat er slechts één bestands header is.

Het combi neren van meerdere gegevens sets in één vector tegel bron kan worden bereikt met een hulp programma zoals [Tippecanoe](https://github.com/mapbox/tippecanoe). Gegevens sets kunnen worden gecombineerd tot één functie verzameling of worden gescheiden in afzonderlijke lagen in de vector tegel, ook wel bron lagen genoemd. Wanneer u een vector tegel bron verbindt met een render-laag, geeft u de bronlaag op die de gegevens bevat die u wilt weer geven met de laag.

### <a name="reduce-the-number-of-canvas-refreshes-due-to-data-updates"></a>Het aantal vernieuwings doeken verminderen door gegevens updates

Er kunnen op verschillende manieren gegevens in een `DataSource` klasse worden toegevoegd of bijgewerkt. Hieronder vindt u de verschillende methoden en enkele aandachtspunten om te zorgen voor goede prestaties.

* De functie gegevens bronnen `add` kan worden gebruikt om een of meer functies aan een gegevens bron toe te voegen. Telkens wanneer deze functie wordt aangeroepen, wordt het vernieuwen van het kaart-canvas geactiveerd. Als u veel functies toevoegt, kunt u deze combi neren in een matrix of functie verzameling en deze eenmaal door geven in deze functie, in plaats van een lus te herhalen op een gegevensset en deze functie aan te roepen voor elke functie.
* De functie gegevens bronnen `setShapes` kan worden gebruikt om alle vormen in een gegevens bron te overschrijven. Onder de motorkap worden de gegevens bronnen `clear` en functies gecombineerd `add` en wordt er een enkel kaart-canvas vernieuwd in plaats van twee. Dit is veel sneller. Zorg ervoor dat u deze gebruikt wanneer u alle gegevens in een gegevens bron wilt bijwerken.
* De functie gegevens bronnen `importDataFromUrl` kan worden gebruikt om een GEOjson-bestand te laden via een URL in een gegevens bron. Zodra de gegevens zijn gedownload, wordt deze door gegeven aan de functie gegevens bronnen `add` . Als het geojson-bestand wordt gehost op een ander domein, moet u ervoor zorgen dat het andere domein meerdere domein aanvragen (CORs) ondersteunt. Als het niet mogelijk is om de gegevens te kopiëren naar een lokaal bestand in uw domein of een proxy service te maken waarvoor CORs is ingeschakeld. Als het bestand groot is, kunt u het converteren naar een vector tegel bron.
* Als functies zijn verpakt met de `Shape` -klasse, `addProperty` de `setCoordinates` functies, en en de `setProperties` functie van de vorm, wordt een update geactiveerd in de gegevens bron en het vernieuwen van het kaart-canvas. Alle functies die worden geretourneerd door de gegevens bronnen `getShapes` en `getShapeById` functies worden automatisch verpakt met de- `Shape` klasse. Als u meerdere vormen wilt bijwerken, is het sneller om ze te converteren naar JSON met de functie gegevens bronnen `toJson` , het bewerken van de geojson en het door geven van deze gegevens in de gegevens bronnen `setShapes` functie.

### <a name="avoid-calling-the-data-sources-clear-function-unnecessarily"></a>Vermijd het aanroepen van de functie voor het wissen van gegevens bronnen

Het aanroepen van de functie Clear van de klasse resulteert in het vernieuwen van het `DataSource` kaart-canvas. Als de `clear` functie meerdere keren in een rij wordt genoemd, kan er een vertraging optreden terwijl de kaart wacht totdat elke bewerking wordt uitgevoerd.

Een veelvoorkomend scenario waarbij dit vaak in toepassingen wordt weer gegeven, is wanneer een app de gegevens bron wist, nieuwe gegevens downloadt, de gegevens bron opnieuw wist en vervolgens de nieuwe gegevens toevoegt aan de gegevens bron. Afhankelijk van de gewenste gebruikers ervaring zijn de volgende alternatieven beter.

* Wis de gegevens voordat u de nieuwe gegevens downloadt en geef de nieuwe gegevens door aan de gegevens bronnen `add` of `setShapes` functie. Als dit de enige gegevensset op de kaart is, is de kaart leeg terwijl de nieuwe gegevens worden gedownload.
* Down load de nieuwe gegevens en geef deze door aan de functie gegevens bronnen `setShapes` . Hiermee worden alle gegevens op de kaart vervangen.

### <a name="remove-unused-features-and-properties"></a>Ongebruikte onderdelen en eigenschappen verwijderen

Als uw gegevensset functies bevat die niet in uw app zullen worden gebruikt, verwijdert u deze. Op dezelfde manier verwijdert u de eigenschappen van functies die niet nodig zijn. Dit heeft verschillende voor delen:

* Hiermee vermindert u de hoeveelheid gegevens die moet worden gedownload.
* Hiermee beperkt u het aantal functies dat moet worden herhaald bij het renderen van de gegevens.
* Kan soms helpen om gegevensgestuurde expressies en filters te vereenvoudigen of te verwijderen, wat betekent dat er minder verwerkings tijd nodig is om te renderen.

Wanneer functies een groot aantal eigenschappen of inhoud hebben, is het veel meer handiger om te beperken wat er aan de gegevens bron wordt toegevoegd, alleen die voor rendering en een afzonderlijke methode of service voor het ophalen van de extra eigenschap of inhoud wanneer dat nodig is. Als u bijvoorbeeld een eenvoudige kaart hebt waarmee locaties op een kaart worden weer gegeven wanneer u op een van de gedetailleerde inhoud klikt Als u gegevensgestuurde opmaak wilt gebruiken om aan te passen hoe de locaties op de kaart worden weer gegeven, laadt u alleen de eigenschappen die nodig zijn voor de gegevens bron. Als u de gedetailleerde inhoud wilt weer geven, gebruikt u de ID van de functie om de extra inhoud afzonderlijk op te halen. Als de inhoud wordt opgeslagen op de server, kan er een service worden gebruikt om deze asynchroon op te halen, waardoor de hoeveelheid gegevens die moet worden gedownload wanneer de kaart voor het eerst wordt geladen aanzienlijk wordt verminderd.

Daarnaast kan het verminderen van het aantal significante cijfers in de coördinaten van functies ook de gegevens grootte aanzienlijk verminderen. Het is niet ongebruikelijk dat coördinaten 12 of meer decimalen bevatten. zes decimalen hebben echter een nauw keurigheid van ongeveer 0,1 meter, wat vaak nauw keuriger is dan de locatie die door de coördinaat wordt aangegeven (zes decimalen worden aanbevolen bij het werken met kleine locatie gegevens, zoals het bouwen van bouw schema's). Als er meer dan zes decimalen zijn, is er waarschijnlijk geen verschil in de manier waarop de gegevens worden gerenderd en is het voor de gebruiker alleen nodig om meer gegevens te downloaden zonder extra voor deel.

Hier volgt een lijst met [nuttige hulpprogram ma's voor het werken met GEOjson-gegevens](https://github.com/tmcw/awesome-geojson).

### <a name="use-a-separate-data-source-for-rapidly-changing-data"></a>Een afzonderlijke gegevens bron gebruiken om snel gegevens te wijzigen

Soms is het nodig om snel gegevens op de kaart bij te werken voor zaken zoals het weer geven van live updates van streaminggegevens of het animeren van functies. Wanneer een gegevens bron wordt bijgewerkt, wordt door de rendering-engine gelusd en worden alle functies in de gegevens bron weer gegeven. Het scheiden van statische gegevens van snel veranderende gegevens in verschillende gegevens bronnen kan het aantal functies die opnieuw worden weer gegeven op elke update in de gegevens bron aanzienlijk verminderen en de algehele prestaties verbeteren.

Als u vector tegels met Live-gegevens gebruikt, is het gebruik van de antwoord header een eenvoudige manier om updates te ondersteunen `expires` . Standaard worden naast elke vector tegel bron of raster tegel laag automatisch tegels opnieuw geladen wanneer de `expires` datum. De tegels stroom verkeer en incidenten in de kaart gebruiken deze functie om ervoor te zorgen dat de gegevens in realtime verkeer op de kaart worden weer gegeven. Deze functie kan worden uitgeschakeld door de optie kaarten `refreshExpiredTiles` service in te stellen op `false` .

### <a name="adjust-the-buffer-and-tolerance-options-in-geojson-data-sources"></a>De opties voor de buffer en de tolerantie in geojson-gegevens bronnen aanpassen

Met de `DataSource` klasse worden gegevens van onbewerkte locaties omgezet in vector tegels die lokaal zijn voor weer gave in de vlucht. Met deze lokale vector tegels worden de onbewerkte gegevens gefragmenteerd aan de grenzen van het tegel gebied met een bit buffer, zodat de tegels vloeiend kunnen worden weer gegeven. Hoe kleiner de `buffer` optie is, hoe minder overlappende gegevens worden opgeslagen in de lokale vector tegels en de betere prestaties, maar hoe groter de wijziging van het renderen van artefacten plaatsvindt. Probeer deze optie te verfijnen om de juiste combi natie van prestaties te krijgen met minimale rendering-artefacten.

De `DataSource` klasse heeft ook een `tolerance` optie die wordt gebruikt met de Douglas-Peucker-vereenvoudiging-algoritme bij het verminderen van de resolutie van geometrieën voor rendering. Door deze tolerantie waarde te verhogen, vermindert u de resolutie van geometrieën en verbetert u de prestaties. Verfijnen deze optie om de juiste combi natie van geometrie omzetting en prestaties voor uw gegevensset te verkrijgen.

### <a name="set-the-max-zoom-option-of-geojson-data-sources"></a>De maximale zoom optie van geojson-gegevens bronnen instellen

Met de `DataSource` klasse worden gegevens van onbewerkte locaties omgezet in vector tegels die lokaal zijn voor weer gave in de vlucht. De standaard instelling is dat de gegevens van de tegels die zijn gegenereerd voor zoom niveau 18 worden gesampled totdat het zoom niveau 18, op dat moment wordt weer gegeven. Dit is geschikt voor de meeste gegevens sets die hoge resolutie moeten hebben wanneer op deze niveaus wordt ingezoomd. Wanneer u echter werkt met gegevens sets die waarschijnlijk meer worden weer gegeven wanneer er meer uitzoomen, zoals bij het weer geven van de staat of provincie veelhoeken, stelt `minZoom` u de optie van de gegevens bron in op een kleinere waarde, zoals `12` de berekening van het bedrag, het genereren van lokale tegels en het geheugen dat door de gegevens bron wordt gebruikt en de prestaties verbeteren.

### <a name="minimize-geojson-response"></a>Geojson-antwoord minimaliseren

Bij het laden van geojson-gegevens vanaf een server via een service of door het laden van een plat bestand, moet u ervoor zorgen dat de gegevens geminimaliseerd blijven om overbodige ruimte tekens te verwijderen die de grootte van de down load groter maken dan nodig is.

### <a name="access-raw-geojson-using-a-url"></a>Toegang tot RAW geojson met behulp van een URL

Het is mogelijk om geojson Objects in te sluiten in Java script, maar dit maakt gebruik van veel geheugen als er kopieën van worden opgeslagen in de variabele die u voor dit object hebt gemaakt en de gegevens bron instantie die deze beheert in een afzonderlijke webwerker. Bespreek de geojson voor uw app met behulp van een URL en de gegevens bron laadt één kopie van gegevens rechtstreeks naar de webwerker van de gegevens bronnen.  

## <a name="optimize-rendering-layers"></a>Weergave lagen optimaliseren

Azure Maps voorziet in verschillende lagen voor het weer geven van gegevens op een kaart. Er zijn veel optimalisaties die u kunt gebruiken om deze lagen aan te passen aan uw scenario de hogere prestaties en de algehele gebruikers ervaring.

### <a name="create-layers-once-and-reuse-them"></a>Eenmaal lagen maken en ze opnieuw gebruiken

De Azure Maps Web-SDK moet worden aangestuurd als gegevensgestuurd. Gegevens worden weer gegeven in gegevens bronnen, die vervolgens zijn verbonden met het renderen van lagen. Als u de gegevens op de kaart wilt wijzigen, werkt u de gegevens in de gegevens bron bij of wijzigt u de stijl opties op een laag. Dit is vaak veel sneller dan het verwijderen en opnieuw maken van lagen wanneer er een wijziging is.

### <a name="consider-bubble-layer-over-symbol-layer"></a>Een ring laag met een symbool laag overwegen

De laag Bubble geeft punten weer als cirkels op de kaart en kan de RADIUS-en kleur notatie van een Data gegevensgestuurde expressie gebruiken. Aangezien de cirkel een eenvoudige vorm is voor WebGL, kan de rendering-engine deze veel sneller weer geven dan een symbool laag, die een afbeelding moet laden en weer geven. Het prestatie verschil van deze twee weergave lagen is merkbaar wanneer er tien duizenden punten worden weer gegeven.

### <a name="use-html-markers-and-popups-sparingly"></a>Maak spaarzaam gebruik van HTML-markeringen en popups

In tegens telling tot de meeste lagen in de Azure Maps web control die WebGL gebruiken voor rendering, gebruiken HTML-markeringen en pop-ups traditionele DOM-elementen voor rendering. Hoe meer HTML-markeringen en pop-upvensters een pagina hebben toegevoegd, hoe meer DOM-elementen er zijn. Prestaties kunnen afnemen nadat u honderden HTML-markeringen of popups hebt toegevoegd. Voor grotere gegevens sets kunt u de gegevens clusteren of een symbool of Bubble laag gebruiken. Voor pop-ups is een gemeen schappelijke strategie het maken van één pop-up en het opnieuw gebruiken door het bijwerken van de inhoud en positie, zoals wordt weer gegeven in het onderstaande voor beeld:

<br/>

<iframe height='500' scrolling='no' title='Pop-up opnieuw gebruiken met verschillende punaises' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bekijk de pen met behulp van Azure Maps () op CodePen <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>met meerdere pincodes</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

Als u slechts enkele punten wilt weer geven op de kaart, kunt u de eenvoud van HTML-markeringen het beste laten staan. Daarnaast kunnen HTML-markeringen zo nodig eenvoudig worden gesleept.

### <a name="combine-layers"></a>Lagen combi neren

De kaart kan honderden lagen weer geven, maar hoe meer lagen er zijn, des te meer tijd het duurt om een scène te renderen. Eén strategie om het aantal lagen te verminderen is het combi neren van lagen met vergelijk bare stijlen of kan worden opgemaakt met behulp van een [gegevensgestuurde](data-driven-style-expressions-web-sdk.md)stijl.

Denk bijvoorbeeld aan een gegevensset waarbij alle functies een `isHealthy` eigenschap hebben die een waarde van of kan hebben `true` `false` . Als u een Bubble laag maakt die verschillende gekleurde bellen op basis van deze eigenschap rendert, zijn er verschillende manieren om dit te doen, zoals hieronder vermeld, van de minst meest uitgevoerde uitvoering.

* Splits de gegevens in twee gegevens bronnen op basis van de `isHealthy` waarde en koppel een Bubble laag met een hardcoded kleur optie op elke gegevens bron.
* Plaats alle gegevens in één gegevens bron en maak twee bellen lagen met een hardcoded kleur optie en een filter op basis van de `isHealthy` eigenschap. 
* Plaats alle gegevens in één gegevens bron, maak een laag met een `case` stijl expressie voor de optie kleur op basis van de `isHealthy` eigenschap. Hier staat een codevoorbeeld waarin dit wordt gedemonstreerd.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: [
        'case',

        //Get the 'isHealthy' property from the feature.
        ['get', 'isHealthy'],

        //If true, make the color 'green'. 
        'green',

        //If false, make the color red.
        'red'
    ]
});
```

### <a name="create-smooth-symbol-layer-animations"></a>Animatie van laag boog symbool maken

De detectie van botsingen op symbool lagen is standaard ingeschakeld. Deze botsing detectie is erop gericht om ervoor te zorgen dat er geen twee symbolen elkaar overlappen. De opties voor pictogrammen en tekst van een Symbol-laag hebben twee opties,

* `allowOverlap` -geeft aan of het symbool wordt weer gegeven als het strijdig is met andere symbolen.
* `ignorePlacement` -geeft aan of de andere symbolen kunnen conflicteren met het symbool.

Beide opties zijn standaard ingesteld op `false` . Bij het animeren van een symbool wordt de detectie van botsingen uitgevoerd op elk frame van de animatie, waardoor de animatie kan vertragen en er minder hydraulica wordt weer geven. Stel deze opties in op om de animatie uit te vloeienden `true` .

De volgende voorbeeld code is een eenvoudige manier om een symbool laag te animeren.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Animatie van symbool laag" src="https://codepen.io/azuremaps/embed/oNgGzRd?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/oNgGzRd'>laag animatie</a> van het pen-symbool door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="specify-zoom-level-range"></a>Bereik van zoom niveau opgeven

Als uw gegevens voldoen aan een van de volgende criteria, moet u het minimale en maximale zoom niveau van de laag opgeven, zodat de rendering-engine deze kan overs Laan wanneer deze buiten het bereik van het zoom niveau valt.

* Als de gegevens afkomstig zijn uit een vector tegel bron, zijn vaak bron lagen voor verschillende gegevens typen alleen beschikbaar in een bereik van zoom niveaus.
* Als u een tegel laag gebruikt die geen tegels voor alle zoom niveaus 0 tot en met 24 heeft en u wilt dat deze alleen wordt weer gegeven op de niveaus die tegels bevatten, en u niet probeert ontbrekende tegels op te vullen met tegels van andere zoom niveaus.
* Als u alleen een laag op bepaalde zoom niveaus wilt weer geven.
Alle lagen hebben een `minZoom` en- `maxZoom` optie waarbij de laag wordt gerenderd wanneer deze tussen deze zoom niveaus op basis van deze logica wordt weer gegeven ` maxZoom > zoom >= minZoom` .

**Voorbeeld**

```javascript
//Only render this layer between zoom levels 1 and 9. 
var layer = new atlas.layer.BubbleLayer(dataSource, null, {
    minZoom: 1,
    maxZoom: 10
});
```

### <a name="specify-tile-layer-bounds-and-source-zoom-range"></a>Grenzen van de tegel laag en het bron zoom bereik opgeven

Tegel lagen laden standaard tegels over de hele wereld. Als de tegel service echter alleen tegels voor een bepaald gebied heeft, probeert de kaart tegels te laden wanneer dit zich buiten dit gebied bevindt. Als dit gebeurt, wordt er een aanvraag voor elke tegel gemaakt en wordt gewacht op een reactie waarbij andere aanvragen door de kaart kunnen worden geblokkeerd, waardoor de rendering van andere lagen kan worden vertraagd. Het opgeven van de grenzen van een tegel laag leidt ertoe dat de kaart alleen tegels aanvraagt die binnen dat selectie kader zijn. Als de laag van de tegel alleen beschikbaar is tussen bepaalde zoom niveaus, geeft u ook de minimale en maximale bron zoom op om dezelfde reden.

**Voorbeeld**

```javascript
var tileLayer = new atlas.layer.TileLayer({
    tileUrl: 'myTileServer/{z}/{y}/{x}.png',
    bounds: [-101.065, 14.01, -80.538, 35.176],
    minSourceZoom: 1,
    maxSourceZoom: 10
});
```

### <a name="use-a-blank-map-style-when-base-map-not-visible"></a>Een lege kaart stijl gebruiken wanneer de basis kaart niet zichtbaar is

Als er een laag wordt weer gegeven op de kaart die volledig de basis kaart bedekt, kunt u overwegen om de kaart stijl in te stellen op `blank` of `blank_accessible` zodat de basis kaart niet wordt gerenderd. Een veelvoorkomend scenario hiervoor is het bedekken van een volledige wereldbol zonder dekking of doorzichtig gebied boven de basis kaart.

### <a name="smoothly-animate-image-or-tile-layers"></a>Afbeeldings-of tegel lagen vloeiend van animatie voorzien

Als u een reeks afbeeldingen of tegel lagen op de kaart wilt animeren. Het is vaak sneller om een laag te maken voor elke afbeelding of tegel laag en om de dekking te wijzigen dan om de bron van één laag op elk animatie frame bij te werken. Als u een laag wilt verbergen door de dekking in te stellen op nul en een nieuwe laag weer te geven door de dekking in te stellen op een waarde die groter is dan nul, is veel sneller dan het bijwerken van de bron in de laag. U kunt ook de zicht baarheid van de lagen in-of uitschakelen, maar u moet de duur van de laag vervagen instellen op nul, anders wordt de laag van animatie voorzien wanneer deze wordt weer gegeven. Dit resulteert in een Flik kering omdat de vorige laag werd verborgen voordat de nieuwe laag zichtbaar wordt.

### <a name="tweak-symbol-layer-collision-detection-logic"></a>Logica voor detectie van laag conflicten voor tweak symbolen

De symbool laag heeft twee opties die bestaan voor zowel het pictogram als de tekst met de naam `allowOverlap` en `ignorePlacement` . Met deze twee opties geeft u op of het pictogram of de tekst van een symbool overlapt of overlapt. Als deze zijn ingesteld op, worden er bij `false` het renderen van elk punt berekeningen uitgevoerd om te zien of het conflicteert met een ander symbool dat al wordt weer gegeven in de laag. als dat wel het geval is, wordt het conflicterende symbool niet weer gegeven. Dit is handig als u de kaart overzichtelijker maakt en het aantal objecten dat wordt weer gegeven vermindert. Als u deze opties instelt op `false` , wordt deze logica voor detectie van conflicten overgeslagen en worden alle symbolen op de kaart weer gegeven. Verfijnen deze optie om de beste combi natie van prestaties en gebruikers ervaring te verkrijgen.

### <a name="cluster-large-point-data-sets"></a>Gegevens sets van het cluster met een groot punt

Wanneer u werkt met grote sets gegevens punten, kan het voor komen dat veel van de punten elkaar overlappen en slechts gedeeltelijk zichtbaar zijn, als u dat wel doet. Clustering is het proces van het samen voegen van punten die dicht bij elkaar staan en die als één geclusterd punt worden weer gegeven. Wanneer de gebruiker inzoomt op de kaart in, worden clusters gesplitst in hun afzonderlijke punten. Dit kan de hoeveelheid gegevens die moet worden weer gegeven, aanzienlijk verminderen, waardoor de kaart minder overzichtelijk wordt en de prestaties verbeteren. De `DataSource` klasse heeft opties voor het lokaal clusteren van gegevens. Daarnaast hebben veel hulpprogram ma's die vector tegels genereren ook cluster opties.

Verg root bovendien de grootte van de cluster RADIUS om de prestaties te verbeteren. Hoe groter de cluster RADIUS, hoe minder geclusterde punten er moeten worden bijgehouden en weer gegeven.
Meer informatie in het [gegevens document voor clustering Point](clustering-point-data-web-sdk.md)

### <a name="use-weighted-clustered-heat-maps"></a>Gewogen geclusterde heatmap gebruiken

De laag van de heatmap kan eenvoudig tien duizenden gegevens punten weer geven. Voor grotere gegevens sets kunt u de clustering inschakelen voor de gegevens bron en een kleine cluster RADIUS gebruiken en de `point_count` eigenschap clusters als gewicht voor de hoogte kaart. Wanneer de RADIUS van het cluster slechts een paar pixels groot is, is er weinig visueel verschil in de weer gegeven heatmap. Het gebruik van een grotere cluster RADIUS verbetert de prestaties meer, maar kan de resolutie van de gerenderde heatmap verlagen.

```javascript
var layer = new atlas.layer.HeatMapLayer(source, null, {
   weight: ['get', 'point_count']
});
```

Meer informatie vindt u in de [clustering-en heatmap-kaarten in dit document](clustering-point-data-web-sdk.md #clustering-and-the-heat-maps-layer)

### <a name="keep-image-resources-small"></a>Bewaar de afbeeldings resources klein

Er kunnen installatie kopieën worden toegevoegd aan de toewijzings afbeelding sprite voor het renderen van pictogrammen in een symbool-laag of patronen in een polygoon laag. Houd deze installatie kopieën klein om de hoeveelheid gegevens te minimaliseren die moeten worden gedownload en de hoeveelheid ruimte die ze in de Maps-afbeelding sprite hebben. Wanneer u een Symbol-laag gebruikt waarmee het pictogram wordt geschaald met behulp van de `size` optie, gebruikt u een afbeelding met de maximale grootte die uw plan op de kaart kan weer geven en niet groter. Zo zorgt u ervoor dat het pictogram wordt weer gegeven met een hoge resolutie, terwijl de resources die het gebruikt worden geminimaliseerd. Daarnaast kan SVG ook worden gebruikt als een kleinere bestands indeling voor eenvoudige pictogram afbeeldingen.

## <a name="optimize-expressions"></a>Expressies optimaliseren

[Gegevensgestuurde stijl expressies](data-driven-style-expressions-web-sdk.md) bieden veel flexibiliteit en kracht voor het filteren en opmaken van gegevens op de kaart. Er zijn veel manieren waarop expressies kunnen worden geoptimaliseerd. Hier volgen enkele tips.

### <a name="reduce-the-complexity-of-filters"></a>De complexiteit van filters verminderen

Hiermee wordt gefilterd op alle gegevens in een gegevens bron en wordt gecontroleerd of elk filter overeenkomt met de logica in het filter. Als filters complex worden, kan dit leiden tot prestatie problemen. Hier volgen enkele mogelijke strategieën om dit op te lossen.

* Als u vector tegels gebruikt, splitst u de gegevens op in verschillende bron lagen.
* Als u de `DataSource` klasse gebruikt, moet u de gegevens opsplitsen in afzonderlijke gegevens bronnen. Probeer het aantal gegevens bronnen te verdelen met de complexiteit van het filter. Te veel gegevens bronnen kunnen ook prestatie problemen veroorzaken, dus u moet mogelijk iets testen om erachter te komen wat het beste werkt voor uw scenario.
* Wanneer u een complex filter gebruikt voor een laag, kunt u meerdere lagen met stijl expressies gebruiken om de complexiteit van het filter te verminderen. Vermijd het maken van een heleboel lagen met hardcoded stijlen wanneer stijl expressies kunnen worden gebruikt als een groot aantal lagen kan ook prestatie problemen veroorzaken.

### <a name="make-sure-expressions-dont-produce-errors"></a>Zorg ervoor dat expressies geen fouten produceren

Expressies worden vaak gebruikt om code te genereren voor het uitvoeren van berekeningen of logische bewerkingen op het moment van renderen. Net als bij de code in de rest van uw toepassing, moet u ervoor zorgen dat de berekeningen en logische betekenis zijn en niet gevoelig zijn voor fouten. Fouten in expressies veroorzaken problemen bij het evalueren van de expressie, wat kan leiden tot minder prestatie-en weergave problemen.

Een veelvoorkomende fout bij het mindful van is een expressie die afhankelijk is van een functie-eigenschap die mogelijk niet voor alle functies bestaat. De volgende code gebruikt bijvoorbeeld een expressie om de eigenschap Color van een Bubble laag in te stellen op de `myColor` eigenschap van een functie.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: ['get', 'myColor']
});
```

De bovenstaande code werkt prima als alle functies in de gegevens bron een eigenschap hebben `myColor` en de waarde van die eigenschap een kleur is. Dit is mogelijk geen probleem als u volledige controle over de gegevens in de gegevens bron hebt en u zeker weet dat alle onderdelen een geldige kleur in een eigenschap hebben `myColor` . Als u deze code veilig wilt maken op basis van fouten, `case` kunt u een expressie gebruiken met de `has` expressie om te controleren of de functie de `myColor` eigenschap heeft. Als dat het geval is, `to-color` kan de type-expressie vervolgens worden gebruikt om te proberen de waarde van die eigenschap naar een kleur te converteren. Als de kleur ongeldig is, kan een terugval kleur worden gebruikt. De volgende code laat zien hoe u dit doet en de terugval kleur instelt op groen.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: [
        'case',

        //Check to see if the feature has a 'myColor' property.
        ['has', 'myColor'],

        //If true, try validating that 'myColor' value is a color, or fallback to 'green'. 
        ['to-color', ['get', 'myColor'], 'green'],

        //If false, return a fallback value.
        'green'
    ]
});
```

### <a name="order-boolean-expressions-from-most-specific-to-least-specific"></a>Boole-expressies van de meest specifieke waarde naar de minst specifieke rangschikken

Als u Boole-expressies gebruikt die meerdere voorwaardelijke tests bevatten, kunt u de voorwaardelijke tests van de meest specifieke voor waarden rangschikken op minst specifiek. Als u dit doet, moet de eerste voor waarde de hoeveelheid gegevens verminderen waarvoor de tweede voor waarde moet worden getest, waardoor het totale aantal voorwaardelijke tests dat moet worden uitgevoerd, wordt verminderd.

### <a name="simplify-expressions"></a>Expressies vereenvoudigen

Expressies kunnen krachtig en soms complex zijn. Hoe eenvoudiger een expressie is, hoe sneller deze wordt geëvalueerd. Als er bijvoorbeeld een eenvoudige vergelijking vereist is, is een expressie zoals in dat `['==', ['get', 'category'], 'restaurant']` geval beter dan het gebruik van een match-expressie zoals `['match', ['get', 'category'], 'restaurant', true, false]` . Als de eigenschap die wordt gecontroleerd, in dit geval een Booleaanse waarde is, `get` zou een expressie nog eenvoudiger zijn `['get','isRestaurant']` .

## <a name="web-sdk-troubleshooting"></a>Problemen met Web SDK oplossen

Hier volgen enkele tips voor het opsporen van fouten in een aantal veelvoorkomende problemen bij het ontwikkelen met de Azure Maps Web-SDK.

**Waarom wordt de kaart niet weer gegeven wanneer ik het Webbe sturings element laad?**

Ga als volgt te werk:

* Zorg ervoor dat u de toegevoegde verificatie opties hebt toegevoegd aan de kaart. Als deze niet wordt toegevoegd, wordt de kaart geladen met een leeg canvas, omdat deze geen toegang heeft tot de gegevens van het basis overzicht zonder verificatie en er 401 fouten worden weer gegeven op het tabblad netwerk van de ontwikkel hulpprogramma's van de browser.
* Zorg ervoor dat u een Internet verbinding hebt.
* Controleer de console op fouten in de ontwikkel hulpprogramma's van de browser. Sommige fouten kunnen ertoe leiden dat de kaart niet wordt weer gegeven. Fouten opsporen in uw toepassing.
* Zorg ervoor dat u een [ondersteunde browser](supported-browsers.md)gebruikt.

**Al mijn gegevens worden weer gegeven aan de andere kant van de wereld, wat gebeurt er dan?**
Coördinaten, ook wel posities genoemd, in de Azure Maps Sdk's worden uitgelijnd met de indeling van de georuimtelijke industrie standaard `[longitude, latitude]` . Deze indeling is ook de manier waarop coördinaten worden gedefinieerd in het geojson-schema. de kern gegevens die worden gebruikt in de Azure Maps Sdk's. Als uw gegevens aan de andere kant van de wereld worden weer gegeven, is dit waarschijnlijk het gevolg van de lengte-en breedte waarden die worden omgedraaid in uw coördinaten/positie-informatie.

**Waarom worden HTML-markeringen op de verkeerde plaats in het Webbe sturings element weer gegeven?**

Te controleren items:

* Als u aangepaste inhoud voor de markering gebruikt, controleert u of de `anchor` `pixelOffset` Opties en juist zijn. Standaard wordt het midden van de inhoud uitgelijnd op de positie van de kaart.
* Zorg ervoor dat het CSS-bestand voor Azure Maps is geladen.
* Inspecteer het DOM-element HTML-markering om te zien of een CSS-bestand van uw app aan de markering is toegevoegd en dat dit van invloed is op de positie.

**Waarom worden pictogrammen of tekst in de laag van het symbool op de verkeerde plaats weer gegeven?**
Controleer of de `anchor` Opties en `offset` juist zijn geconfigureerd om te worden uitgelijnd met het deel van de afbeelding of tekst die u met de coördinaat op de kaart wilt uitlijnen.
Als het symbool alleen buiten werking is wanneer de kaart wordt geroteerd, controleert u de `rotationAlignment` optie. Standaard worden symbolen die we draaien met de View Port kaarten, zodat ze rechtop worden weer gegeven voor de gebruiker. Afhankelijk van uw scenario kan het echter wenselijk zijn om het symbool te vergren delen met de richting van de kaart. Stel hiervoor de `rotationAlignment` optie in `’map’` .
Als het symbool alleen buiten werking is wanneer de kaart wordt vertoond of gekanteld, controleert u de `pitchAlignment` optie. Standaard blijven symbolen die we rechtop houden met de View Port kaarten als de kaart wordt weer of gekanteld. Afhankelijk van uw scenario kan het echter wenselijk zijn om het symbool te vergren delen met de hoogte van de kaart. Stel hiervoor de `pitchAlignment` optie in `’map’` .

**Waarom worden er geen gegevens op de kaart weer gegeven?**

Te controleren items:

* Controleer de console in de ontwikkel hulpprogramma's van de browser op fouten.
* Zorg ervoor dat er een gegevens bron is gemaakt en toegevoegd aan de kaart en dat de gegevens bron is verbonden met een render-laag die ook is toegevoegd aan de kaart.
* Voeg breek punten toe aan uw code en stap voor om ervoor te zorgen dat gegevens worden toegevoegd aan de gegevens bron en dat de gegevens bron en de lagen worden toegevoegd aan de kaart zonder dat er fouten optreden.
* Probeer gegevensgestuurde expressies te verwijderen uit de laag voor rendering. Het is mogelijk dat er een fout optreedt in een van deze problemen waardoor het probleem wordt veroorzaakt.

**Kan ik de Azure Maps Web-SDK in een in de sandbox geteste iframe gebruiken?**

Ja. In [Safari is er een fout opgetreden](https://bugs.webkit.org/show_bug.cgi?id=170075) die voor komt dat iframes in de sandbox van webwerkers worden uitgevoerd. Dit is de vereiste van de Azure Maps Web-SDK. De oplossing is om de tag toe te voegen `"allow-same-origin"` aan de eigenschap sandbox van het iframe.

## <a name="get-support"></a>Ondersteuning krijgen

Hier volgen de verschillende manieren om ondersteuning te krijgen voor Azure Maps, afhankelijk van uw probleem.

**Hoe kan ik een gegevens probleem of een probleem met een adres melden?**

Azure Maps heeft een hulp programma voor gegevens feedback waar gegevens problemen kunnen worden gerapporteerd en bijgehouden. [https://feedback.azuremaps.com/](https://feedback.azuremaps.com/) Elk verzonden probleem genereert een unieke URL die u kunt gebruiken om de voortgang van het gegevens probleem bij te houden. De tijd die nodig is om een gegevens probleem op te lossen, is afhankelijk van het type probleem en hoe eenvoudig het is om te controleren of de wijziging juist is. Zodra de render-service is opgelost, wordt de update in de wekelijkse update weer gegeven, terwijl andere services, zoals geocodering en route ring, de update in de maandelijkse update zien. Gedetailleerde instructies voor het melden van een gegevens probleem vindt u in dit [document](how-to-use-feedback-tool.md).

**Hoe kan ik een bug in een service of API melden?**

https://azure.com/support

**Waar vind ik technische hulp voor Azure Maps?**

Als deze is gerelateerd aan de Azure Maps Visual in Power BI: https://powerbi.microsoft.com/support/ voor alle andere Azure Maps Services: https://azure.com/support of de forums van ontwikkel aars: https://docs.microsoft.com/answers/topics/azure-maps.html

**Hoe kan ik een functie aanvraag maken?**

Maak een functie aanvraag op onze gebruikers spraak site: https://feedback.azure.com/forums/909172-azure-maps

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer tips over het verbeteren van de gebruikers ervaring in uw toepassing.

> [!div class="nextstepaction"]
> [Uw toepassing toegankelijk maken](map-accessibility.md)

Meer informatie over de terminologie die wordt gebruikt door Azure Maps en de georuimtelijke industrie.

> [!div class="nextstepaction"]
> [Azure Maps-woordenlijst](glossary.md)

---
title: Een toegankelijke toewijzings toepassing maken met Azure Maps | Microsoft Azure kaarten
description: Meer informatie over toegankelijkheids overwegingen in Azure Maps. Bekijk welke functies beschikbaar zijn voor het toegankelijk maken van kaart toepassingen en Bekijk tips voor toegankelijkheid.
services: azure-maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2019
ms.topic: conceptual
ms.service: azure-maps
manager: cpendleton
ms.openlocfilehash: ec88437a0fad3a6bd94a67a5ef5c75b3e506f9e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88006210"
---
# <a name="building-an-accessible-application"></a>Een toegankelijke toepassing bouwen

Van 20% van Internet gebruikers is een nood zaak voor toegankelijke webtoepassingen. Daarom is het belang rijk om ervoor te zorgen dat uw toepassing zo is ontworpen dat elke gebruiker deze eenvoudig kan gebruiken. Als onderdeel van uw algemene gebruikers ervaring kunt u beter zien wat de toegankelijkheid is van een reeks taken. Hoe meer toegankelijk uw toepassing is, hoe meer mensen deze kunnen gebruiken. 

Wanneer het gaat om uitgebreide, interactieve inhoud zoals een kaart, zijn enkele veelvoorkomende aandachtspunten voor toegankelijkheid:
- Biedt ondersteuning voor de scherm lezer voor gebruikers die moeite hebben met het bekijken van de webtoepassing.
- Meerdere methoden hebben voor interactie met en het navigeren door de webtoepassing, zoals muis, touch en toetsen bord.
- Zorg ervoor dat kleur contrast zodanig is dat kleuren niet samen lopen en lastig van elkaar te onderscheiden zijn. 

De Azure Maps Web-SDK is gebaseerd op een groot aantal toegankelijkheids functies, zoals:
- Beschrijvingen van scherm lezers wanneer de kaart wordt verplaatst en wanneer de gebruiker zich richt op een besturings element of pop-up.
- Ondersteuning voor muis, aanraking en toetsen bord.
- Ondersteuning voor toegankelijk kleur contrast in de kaart stijl weg.
- Ondersteuning voor hoog contrast.

Meer informatie over de conformiteit van de volledige toegankelijkheid van alle micro soft-producten vindt u [hier](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/). Zoek naar ' Azure Maps Web ' om het document speciaal te vinden voor de Azure Maps Web-SDK. 

## <a name="navigating-the-map"></a>Navigeren in de kaart

Er zijn verschillende manieren waarop de kaart kan worden ingezoomd, panned, gedraaid en in hoogte kan worden gesteld. Hieronder vindt u meer informatie over de verschillende manieren waarop u kunt navigeren door de kaart.

**De kaart zoomen**

- Met een muis dubbelklikt u op de kaart om op één niveau te zoomen.
- Schuif met behulp van een muis op het wieltje om in te zoomen op de kaart.
- Met behulp van een touchscreen kunt u de kaart met twee vingers aanraken om uit te zoomen of de vingers van elkaar splitsen om in te zoomen.
- Dubbeltik op de kaart om op één niveau te zoomen met behulp van een aanraak scherm.
- Gebruik het plus teken ( `+` ) of het gelijkteken ( `=` ) om op één niveau in te zoomen met behulp van de kaart gericht.
- Als de kaart is gericht, gebruikt u het minteken, afbreek streepje ( `-` ) of onderstrepings teken ( `_` ) om één niveau uit te zoomen.
- Het besturings element zoomen met behulp van een muis, Touch-of toetsenbord tabblad/Enter-toets.
- Houd de knop ingedrukt `Shift` en druk op de knop met de rechter muisknop op de kaart en sleep om een gebied te tekenen om in te zoomen op de kaart in.
- Met een aantal meerdere Touch-pad's kunt u twee vingers omhoog slepen om uit te zoomen of omlaag om in te zoomen.

**Kaart pannen**

- Met een muis houdt u omlaag met de linkermuisknop op de kaart en sleept u deze naar een wille keurige richting.
- Gebruik een aanraak scherm om de kaart aan te raken en te slepen in een wille keurige richting.
- Met de kaart focus, gebruikt u de pijl toetsen om de kaart te verplaatsen.

**De kaart draaien**

- Met een muis kunt u op omlaag drukken met de rechter muisknop op de kaart en naar links of rechts slepen. 
- Gebruik een touchscreen om de kaart met twee vingers te raken en te draaien.
- Met de kaart focus, gebruikt u de toetsen SHIFT en pijl links of rechts.
- Het besturings element voor rotatie gebruiken met een muis, aanrakend of toetsen bord/Enter-toets.

**De kaart verkopen**

- Gebruik de muis door met de rechter muisknop op de kaart te klikken en omhoog of omlaag te slepen. 
- Met een aanraak scherm raakt u de kaart met twee vingers en sleept u deze naar boven of beneden.
- Met de kaart focus, gebruikt u de Shift-toets plus de toetsen pijl-omhoog of omlaag. 
- Het besturings element pitch gebruiken met een muis, aanrakend of toetsen bord/Enter-toets.

## <a name="change-the-map-style"></a>De kaart stijl wijzigen

Niet alle ontwikkel aars willen dat alle mogelijke kaart stijlen beschikbaar zijn in hun toepassing. Als de ontwikkelaar het besturings element stijl kiezer van de kaart weergeeft, kan de gebruiker de kaart stijl wijzigen met behulp van de muis, een touch of het toetsen bord met het tabblad of Enter-toets. De ontwikkelaar kan opgeven welke kaart stijlen ze beschikbaar willen maken in het besturings element kaart stijl kiezer. De ontwikkelaar kan de kaart stijl ook programmatisch instellen en wijzigen.

**Hoog contrast gebruiken**

- Wanneer het kaart besturings element wordt geladen, wordt gecontroleerd of er een hoog contrast is ingeschakeld en wordt dit door de browser ondersteund.
- Het kaart besturings element bewaakt de modus Hoog contrast van het apparaat niet. Als de modus apparaat wordt gewijzigd, wordt de kaart niet. Daarom moet de gebruiker de kaart opnieuw laden door de pagina te vernieuwen.
- Wanneer een hoog contrast wordt gedetecteerd, wordt automatisch overgeschakeld naar een hoog contrast en worden alle ingebouwde besturings elementen gebruikt als stijl met een hoog contrast. Voor beeld: ZoomControl, PitchControl, CompassControl, StyleControl en andere ingebouwde besturings elementen, wordt een stijl met een hoog contrast gebruikt.
- Er zijn twee typen hoog contrast, licht en donker. Als het type van het hoge contrast kan worden gedetecteerd door de besturings elementen van de kaart, wordt het gedrag van de kaart dienovereenkomstig aangepast. Als Light, wordt de stijl van de grayscale_light kaart geladen. Als het type niet kan worden gedetecteerd of donker is, wordt de high_contrast_dark stijl geladen.
- Als u aangepaste besturings elementen maakt, is het handig om te weten of de ingebouwde besturings elementen een stijl met een hoog contrast gebruiken. Ontwikkel aars kunnen een CSS-klasse toevoegen op de kaart container div om te controleren. De CSS-klassen die worden toegevoegd, zijn `high-contrast-dark` en `high-contrast-light` . Als u wilt controleren met behulp van Java script, gebruikt u:

```javascript
map.getMapContainer().classList.contains("high-contrast-dark")
```

of gebruik:

```javascript
map.getMapContainer().classList.contains("high-contrast-light")
```

## <a name="keyboard-shortcuts"></a>Sneltoetsen

De kaart bevat een aantal ingebouwde sneltoetsen waarmee u de kaart gemakkelijker kunt gebruiken. Deze sneltoetsen werken op het moment dat de kaart de focus heeft.

| Sleutel      | Bewerking                            |
|----------|-----------------------------------|
| `Tab` | Navigeer over de besturings elementen en pop-upvensters in de kaart. |
| `ESC` | De focus verplaatsen van een wille keurig element in de kaart naar het kaart element op het hoogste niveau. |
| `Ctrl` + `Shift` + `D` | Detail niveau van de scherm lezer in-of uitschakelen.  |
| Pijltoets-links | De kaart links van 100 pixels pannen |
| Pijltoets-rechts | Kaart naar rechts 100 pixels pannen |
| Pijl-omlaag | De kaart omhoog pannen 100 pixels |
| Pijl-omhoog | Kaart omhoog 100 pixels pannen |
| `Shift` + pijl-omhoog | De kaart hoogte met 10 graden verg Roten |
| `Shift` + pijl-omlaag | De kaart hoogte met 10 graden verlagen |
| `Shift` + pijl-rechts | De kaart 15 graden rechtsom draaien |
| `Shift` + pijl-links | De kaart 15 graden linksom draaien |
| Plus teken ( `+` ) of <sup>*</sup> gelijkteken ( `=` ) | Inzoomen |
| Minteken, afbreek streepje ( `-` ) of <sup>*</sup> onderstrepings teken ( `_` ) | Uitzoomen | 
| `Shift` + muis slepen op kaart naar teken gebied | Inzoomen op gebied |

<sup>*</sup> Deze sneltoetsen delen meestal dezelfde sleutel op een toetsen bord. Deze snelkoppelingen zijn toegevoegd om de gebruikers ervaring te verbeteren. Het maakt ook niet uit of de gebruiker de Shift-toets gebruikt of niet voor deze sneltoetsen.

## <a name="screen-reader-support"></a>Ondersteuning voor scherm lezers

Gebruikers kunnen met behulp van het toetsen bord door de kaart navigeren. Als er een scherm lezer wordt uitgevoerd, wordt de gebruiker op de hoogte gebracht van wijzigingen in de status van de kaart. Gebruikers worden bijvoorbeeld op de hoogte gebracht van de toewijzings wijzigingen wanneer de kaart wordt panned of ingezoomd. De kaart biedt standaard vereenvoudigde beschrijvingen waarmee het zoom niveau en de coördinaten van het midden van de kaart worden uitgesloten. De gebruiker kan het detail niveau van deze beschrijvingen in-of uitschakelen met behulp van de kort snede voor het toetsen bord `Ctrl`  +  `Shift`  +  `D` .

Aanvullende informatie die op de basis kaart wordt geplaatst, moet bijbehorende tekstuele informatie bevatten voor gebruikers van scherm lezers. Zorg ervoor dat u waar nodig toegang hebt tot [Aria (Rich Internet Applications)](https://www.w3.org/WAI/standards-guidelines/aria/), de ALT-en title-kenmerken. 

## <a name="make-popups-keyboard-accessible"></a>Pop-upvensters toegankelijk maken voor het toetsen bord

Een markering of symbool wordt vaak gebruikt om een locatie op de kaart weer te geven. Aanvullende informatie over de locatie wordt doorgaans weer gegeven in een pop-upvenster wanneer de gebruiker met de markering communiceert. In de meeste toepassingen worden pop-ups weer gegeven wanneer een gebruiker op een markering klikt of tikt. Als u echter wilt klikken en tikken, moet de gebruiker respectievelijk een muis en een aanraak scherm gebruiken. Het is een goed idee om pop-ups toegankelijk te maken wanneer u een toetsen bord gebruikt. Deze functionaliteit kan worden bereikt door een pop-up te maken voor elk gegevens punt en deze toe te voegen aan de kaart. 

In het volgende voor beeld worden punten van belangen op de kaart geladen met behulp van een symbool laag en wordt een pop-upvenster toegevoegd aan de kaart voor elk gewenst moment. Een verwijzing naar elke pop-up wordt opgeslagen in de eigenschappen van elk gegevens punt. Het kan ook worden opgehaald voor een markering, bijvoorbeeld wanneer er op een markering wordt geklikt. Als er op de kaart wordt gestreefd en u op TAB drukt, kan de gebruiker elke pop-up door lopen op de kaart.

<br/>

<iframe height='500' scrolling='no' title='Een toegankelijke toepassing maken' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>een toegankelijke toepassing maken</a> met behulp van Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>. </iframe>

<br/>

## <a name="additional-accessibility-tips"></a>Aanvullende tips voor toegankelijkheid

Hier volgen enkele extra tips om uw webtoewijzings toepassing toegankelijker te maken.

- Als veel interactieve punt gegevens op de kaart worden weer gegeven, kunt u overwegen het wirwar te verminderen en clustering te gebruiken. 
- Zorg ervoor dat de contrast verhouding van de kleur tussen tekst/symbolen en achtergrond kleuren is 4,5:1 of meer.
- Houd uw scherm lezer (ARIA, ALT en titel kenmerken) berichten kort, beschrijvende en zinvol. Vermijd onnodig jargon en acroniemen.
- Optimaliseer berichten die naar de scherm lezer worden verzonden om kort herken bare informatie te geven die gemakkelijk is voor de gebruiker om te worden gedigesteerd. Als u bijvoorbeeld de scherm lezer op een hoge frequentie wilt bijwerken, bijvoorbeeld wanneer de kaart wordt verplaatst, kunt u de volgende punten overwegen:
    - Wacht totdat de toewijzing is voltooid om de scherm lezer bij te werken.
    - De updates tot één keer per seconde beperken. 
    - Combi neer berichten op logische wijze samen. 
- Vermijd het gebruik van kleur als enige manier om informatie over te brengen. Gebruik tekst, pictogrammen of patronen om de kleur aan te vullen of te vervangen. Enkele aandachtspunten:
    - Als u een Bubble laag gebruikt om de relatieve waarde tussen gegevens punten weer te geven, kunt u overwegen om de RADIUS van elke bel te schalen, de ballon te kleuren of beide. 
    - Overweeg het gebruik van een symbool laag met verschillende pictogrammen voor verschillende metrische categorieën, zoals drie hoeken, sterren en kwadraten. De Symbol-laag biedt ook ondersteuning voor het schalen van de grootte van het pictogram. Er kan ook een tekst label worden weer gegeven.
    - Als regel gegevens worden weer gegeven, kan de breedte worden gebruikt om het gewicht of de grootte aan te geven. Een patroon met een streepjes matrix kan worden gebruikt om verschillende categorieën met lijnen weer te geven. Een symbool laag kan worden gebruikt in combi natie met een lijn om pictogrammen langs de lijn te bedekken. Het gebruik van een pijl pictogram is handig voor het weer geven van de stroom of richting van de regel.
    - Als er veelhoek gegevens worden weer gegeven, kan een patroon, zoals strepen, worden gebruikt als alternatief voor kleur. 
- Sommige visualisaties, zoals Heatmaps, tegel lagen en afbeeldings lagen, zijn niet toegankelijk voor gebruikers met een visuele handicap. Enkele aandachtspunten:
    - De scherm lezer beschrijven wat de laag wordt weer gegeven wanneer deze wordt toegevoegd aan de kaart. Als er bijvoorbeeld een weer gave laag met het radar diagram wordt weer gegeven, laat u de scherm lezer zien dat "weer radar gegevens op de kaart worden overlapt".
- Beperk de hoeveelheid functionaliteit waarvoor een muis aanwijzer nodig is. Deze functionaliteit is niet toegankelijk voor gebruikers die een toetsen bord of aanraak apparaat gebruiken om te communiceren met uw toepassing. Houd er rekening mee dat het een goed idee is om een aanwijs stijl te hebben voor interactieve inhoud, zoals klikbaar pictogrammen, koppelingen en knoppen.
- Probeer uw toepassing te navigeren met behulp van het toetsen bord. Zorg ervoor dat de volg orde van het tabblad logisch is.
- Als u sneltoetsen wilt maken, probeert u het te beperken tot twee sleutels of minder. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over toegankelijkheid in de Web SDK-modules.

> [!div class="nextstepaction"]
> [Toegankelijkheid van teken hulpprogramma's](drawing-tools-interactions-keyboard-shortcuts.md)

Meer informatie over het ontwikkelen van toegankelijke apps met Microsoft Learn:

> [!div class="nextstepaction"]
> [Leer traject voor toegankelijkheid in actie digitale badge](https://ready.azurewebsites.net/learning/track/2940)

Bekijk deze nuttige hulpprogram ma's voor toegankelijkheid:
> [!div class="nextstepaction"]
> [Toegankelijke apps ontwikkelen](https://developer.microsoft.com/windows/accessible-apps)

> [!div class="nextstepaction"]
> [Overzicht van WAI-ARIA](https://www.w3.org/WAI/standards-guidelines/aria/)

> [!div class="nextstepaction"]
> [Hulp programma voor het evalueren van webaccessibility (WAVE)](https://wave.webaim.org/)

> [!div class="nextstepaction"]
> [WebAim-hulp programma voor kleur contrast](https://webaim.org/resources/contrastchecker/)

> [!div class="nextstepaction"]
> [Geen koffie Vision Simulator](https://chrome.google.com/webstore/detail/nocoffee/jjeeggmbnhckmgdhmgdckeigabjfbddl?hl=en-US)

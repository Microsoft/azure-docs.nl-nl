---
title: Een pop-upvenster toevoegen aan een punt op een kaart | Microsoft Azure kaarten
description: Meer informatie over pop-ups, pop-upsjablonen en popup-gebeurtenissen in Azure Maps. Zie een pop-upvenster toevoegen aan een punt op een kaart en pop-ups opnieuw gebruiken en aanpassen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 903574a8c8696f10d9903a685bab8d12b3e6395f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92891033"
---
# <a name="add-a-popup-to-the-map"></a>Een pop-upvenster toevoegen aan de kaart

In dit artikel wordt uitgelegd hoe u een pop-upvenster kunt toevoegen aan een punt op een kaart.

## <a name="understand-the-code"></a>De code begrijpen

Met de volgende code wordt een punt functie met `name` en `description` Eigenschappen aan de kaart toegevoegd met behulp van een Symbol-laag. Er wordt een exemplaar van de [pop-upklasse](/javascript/api/azure-maps-control/atlas.popup) gemaakt, maar dit wordt niet weer gegeven. Muis gebeurtenissen worden toegevoegd aan de Symbol-laag om het openen en sluiten van de pop-up te activeren. Wanneer het markerings symbool wordt aangevallen, wordt de eigenschap van het pop-upvenster `position` bijgewerkt met de positie van de markering en de `content` optie wordt bijgewerkt met een aantal HTML-code die de  `name` `description` Eigenschappen van de punt en de plaats van de functie in de stand zet. De pop-up wordt vervolgens met behulp van de functie weer gegeven op de kaart `open` .

```javascript
//Define an HTML template for a custom popup content laypout.
var popupTemplate = '<div class="customInfobox"><div class="name">{name}</div>{description}</div>';

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

dataSource.add(new atlas.data.Feature(new atlas.data.Point([-122.1333, 47.63]), {
  name: 'Microsoft Building 41', 
  description: '15571 NE 31st St, Redmond, WA 98052'
}));

//Create a layer to render point data.
var symbolLayer = new atlas.layer.SymbolLayer(dataSource);

//Add the polygon and line the symbol layer to the map.
map.layers.add(symbolLayer);

//Create a popup but leave it closed so we can update it and display it later.
popup = new atlas.Popup({
  pixelOffset: [0, -18],
  closeButton: false
});

//Add a hover event to the symbol layer.
map.events.add('mouseover', symbolLayer, function (e) {
  //Make sure that the point exists.
  if (e.shapes && e.shapes.length > 0) {
    var content, coordinate;
    var properties = e.shapes[0].getProperties();
    content = popupTemplate.replace(/{name}/g, properties.name).replace(/{description}/g, properties.description);
    coordinate = e.shapes[0].getCoordinates();

    popup.setOptions({
      //Update the content of the popup.
      content: content,

      //Update the popup's position with the symbol's coordinate.
      position: coordinate

    });
    //Open the popup.
    popup.open(map);
  }
});

map.events.add('mouseleave', symbolLayer, function (){
  popup.close();
});
```

Hieronder ziet u het volledige programma voor het uitvoeren van code van de bovenstaande functionaliteit.

<br/>

<iframe height='500' scrolling='no' title='Een pop-up toevoegen met Azure Maps' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>een pop-up toevoegen met Azure Maps</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Een pop-upvenster met meerdere punten opnieuw gebruiken

Er zijn gevallen waarin de beste aanpak is om één pop-up te maken en deze opnieuw te gebruiken. Zo hebt u mogelijk een groot aantal punten en wilt u slechts één pop-up tegelijk weer geven. Door de pop-up opnieuw te gebruiken, is het aantal DOM-elementen dat door de toepassing is gemaakt, aanzienlijk verminderd, waardoor betere prestaties kunnen worden geboden. In het volgende voor beeld worden drie punt functies gemaakt. Als u op een van deze functies klikt, wordt er een pop-upvenster weer gegeven met de inhoud voor die punt functie.

<br/>

<iframe height='500' scrolling='no' title='Pop-up opnieuw gebruiken met verschillende punaises' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bekijk de pen met behulp van Azure Maps () op CodePen <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>met meerdere pincodes</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="customizing-a-popup"></a>Een pop-upvenster aanpassen

Standaard heeft de pop-up een witte achtergrond, een aanwijzer pijl aan de onderkant en een knop Sluiten in de rechter bovenhoek. In het volgende voor beeld wordt de achtergrond kleur gewijzigd in zwart met behulp `fillColor` van de optie van de pop-up. De knop Sluiten wordt verwijderd door de `CloseButton` optie in te stellen op ONWAAR. De HTML-inhoud van de pop-up maakt gebruik van 10 pixels van de randen van de pop-up. De tekst wordt wit gemaakt, zodat deze fraai op de zwarte achtergrond wordt weer gegeven.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Aangepaste pop-up" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>aangepaste</a> Pen door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>().
</iframe>

## <a name="add-popup-templates-to-the-map"></a>Pop-upsjablonen toevoegen aan de kaart

Met pop-upsjablonen kunt u eenvoudig gegevensgestuurde indelingen voor pop-ups maken. In de volgende secties ziet u hoe u verschillende pop-upsjablonen kunt gebruiken om opgemaakte inhoud te genereren met behulp van eigenschappen van functies.

> [!NOTE]
> Standaard wordt alle inhoud die wordt weer gegeven, de pop-upsjabloon gebruikt, in de sandbox als beveiligings functie van een IFRAME. Er zijn echter beperkingen:
>
> - Alle scripts, formulieren, aanwijzer vergrendeling en de bovenste navigatie functionaliteit zijn uitgeschakeld. Koppelingen mogen worden geopend op een nieuw tabblad wanneer erop wordt geklikt. 
> - Oudere browsers die de `srcdoc` para meter niet op iframes ondersteunen, zijn beperkt tot het renderen van een kleine hoeveelheid inhoud.
> 
> Als u de gegevens die in pop-upvensters worden geladen, vertrouwt en u wilt dat deze scripts die in pop-upvensters worden geladen, toegang hebben tot uw toepassing, kunt u dit uitschakelen door de optie pop-upsjablonen `sandboxContent` in te stellen op false. 

### <a name="string-template"></a>Teken reeks sjabloon

De teken reeks sjabloon vervangt tijdelijke aanduidingen door de waarden van de functie-eigenschappen. Aan de eigenschappen van de functie hoeft geen waarde van het type teken reeks te worden toegewezen. Een voor beeld `value1` bevat een geheel getal. Deze waarden worden vervolgens door gegeven aan de eigenschap content van `popupTemplate` . 

`numberFormat`Met de optie geeft u de indeling van het getal op dat moet worden weer gegeven. Als `numberFormat` dat niet is opgegeven, gebruikt de code de datum notatie voor de pop-up sjablonen. `numberFormat`Met de optie worden getallen opgemaakt met de functie [Number. toLocaleString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) . Als u grote getallen wilt opmaken, kunt u de `numberFormat` optie gebruiken met functies van [NumberFormat. Format](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat/format). In het onderstaande code fragment wordt bijvoorbeeld gebruikt `maximumFractionDigits` om het aantal breuk cijfers te beperken tot twee.

> [!Note]
> Er is slechts één manier waarop de teken reeks sjabloon afbeeldingen kan genereren. Eerst moet de teken reeks sjabloon een afbeeldings code bevatten. De waarde die wordt door gegeven aan de afbeeldings code moet een URL naar een afbeelding zijn. Vervolgens moet de teken reeks sjabloon zijn `isImage` ingesteld op True in de `HyperLinkFormatOptions` . De `isImage` optie geeft aan dat de Hyper link voor een afbeelding is en de Hyper link wordt geladen in een afbeeldings code. Wanneer op de Hyper link wordt geklikt, wordt de afbeelding geopend.

```javascript
var templateOptions = {
  content: 'This template uses a string template with placeholders.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 1 - String template',
    value1: 1.2345678,
    value2: {
        subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6]
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="propertyinfo-template"></a>Property info-sjabloon

De Property info-sjabloon geeft de beschik bare eigenschappen van de functie weer. `label`Met de optie geeft u de tekst op die voor de gebruiker moet worden weer gegeven. Als `label` niet wordt opgegeven, wordt de Hyper link weer gegeven. En als de Hyper Link een afbeelding is, wordt de waarde die is toegewezen aan de tag ' Alt ' weer gegeven. `dateFormat`Hiermee geeft u de notatie van de datum op en als de datum notatie niet is opgegeven, wordt de datum weer gegeven als een teken reeks. `hyperlinkFormat`Met de optie worden klik bare koppelingen weer gegeven, op dezelfde manier, de `email` optie kan worden gebruikt voor het weer geven van klik bare e-mail adressen.

Voordat de Property info-sjabloon de eigenschappen weer geven voor de eind gebruiker, controleert het recursief of de eigenschappen inderdaad voor die functie zijn gedefinieerd. De weer gave van eigenschappen van stijl en titel wordt ook genegeerd. Er wordt bijvoorbeeld geen weer gegeven `color` ,,, `size` `anchor` `strokeOpacity` , en `visibility` . Wanneer het controleren van de eigenschappen van de eigenschap is voltooid in de back-end, wordt de inhoud in een tabel indeling weer gegeven in de sjabloon info.

```javascript
var templateOptions = {
  content: [
    {
        propertyPath: 'createDate',
        label: 'Created Date'
    },
    {
        propertyPath: 'dateNumber',
        label: 'Formatted date from number',
        dateFormat: {
          weekday: 'long',
          year: 'numeric',
          month: 'long',
          day: 'numeric',
          timeZone: 'UTC',
          timeZoneName: 'short'
        }
    },
    {
        propertyPath: 'url',
        label: 'Code samples',
        hideLabel: true,
        hyperlinkFormat: {
          lable: 'Go to code samples!',
          target: '_blank'
        }
    },
    {
        propertyPath: 'email',
        label: 'Email us',
        hideLabel: true,
        hyperlinkFormat: {
          target: '_blank',
          scheme: 'mailto:'
        }
    }
  ]
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 2 - PropertyInfo',
    createDate: new Date(),
    dateNumber: 1569880860542,
    url: 'https://aka.ms/AzureMapsSamples',
    email: 'info@microsoft.com'
}),

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="multiple-content-templates"></a>Meerdere inhouds sjablonen

Een functie kan ook inhoud weer geven met een combi natie van de teken reeks sjabloon en de info label-sjabloon. In dit geval worden de waarden van de tijdelijke aanduidingen op een witte achtergrond weer gegeven met de teken reeks sjabloon.  En in de sjabloon info wordt een afbeelding met volledige breedte in een tabel weer gegeven. De eigenschappen in dit voor beeld zijn vergelijkbaar met de eigenschappen die we in de vorige voor beelden hebben uitgelegd.

```javascript
var templateOptions = {
  content: [
    'This template has two pieces of content; a string template with placeholders and a array of property info which renders a full width image.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
    [{
      propertyPath: 'imageLink',
      label: 'Image',
      hideImageLabel: true,
      hyperlinkFormat: {
        isImage: true
      }
    }]
  ],
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 3 - Multiple content template',
    value1: 1.2345678,
    value2: {
    subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6],
    imageLink: 'https://azuremapscodesamples.azurewebsites.net/common/images/Pike_Market.jpg'
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="points-without-a-defined-template"></a>Punten zonder een gedefinieerde sjabloon

Wanneer de pop-upsjabloon niet is gedefinieerd als een teken reeks sjabloon, een Property info-sjabloon of een combi natie van beide, worden de standaard instellingen gebruikt. Wanneer de `title` en de `description` enige toegewezen eigenschappen zijn, toont de pop-upsjabloon een witte achtergrond, een knop Sluiten in de rechter bovenhoek. En op kleine en middel grote schermen wordt een pijl onderaan weer gegeven. De standaard instellingen worden in een tabel weer gegeven voor alle eigenschappen, met uitzonde ring van de `title` `description` . Zelfs wanneer u terugkeert naar de standaard instellingen, kan de pop-upsjabloon nog steeds programmatisch worden gemanipuleerd. Gebruikers kunnen bijvoorbeeld de detectie van Hyper links uitschakelen en de standaard instellingen blijven van toepassing op andere eigenschappen.

Klik op de punten op de kaart in de CodePen. Er is een punt op de kaart voor elk van de volgende pop-upsjablonen: teken reeks sjabloon, Property info-sjabloon en meerdere inhouds sjablonen. Er zijn ook drie punten om weer te geven hoe sjablonen worden weer gegeven met de standaard instellingen.

<br/>

<iframe height='500' scrolling='no' title='PopupTemplates' src='//codepen.io/azuremaps/embed/dyovrzL/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/dyovrzL/'>PopupTemplates</a> by Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reuse-popup-template"></a>Pop-upsjabloon opnieuw gebruiken

Net als bij het opnieuw gebruiken van pop-up kunt u pop-upsjablonen opnieuw gebruiken. Deze methode is handig als u slechts één pop-upsjabloon tegelijk wilt weer geven voor meerdere punten. Als u de pop-upsjabloon opnieuw gebruikt, wordt het aantal DOM-elementen dat door de toepassing is gemaakt, beperkt, waardoor de prestaties van uw toepassing worden verbeterd. In het volgende voor beeld wordt dezelfde pop-upsjabloon gebruikt voor drie punten. Als u op een van deze functies klikt, wordt er een pop-upvenster weer gegeven met de inhoud voor die punt functie.

<br/>

<iframe height='500' scrolling='no' title='ReusePopupTemplate' src='//codepen.io/azuremaps/embed/WNvjxGw/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/WNvjxGw/'>ReusePopupTemplate</a> by Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="popup-events"></a>Pop-upgebeurtenisen

Pop-ups kunnen worden geopend, gesloten en gesleept. De pop-upklasse bevat gebeurtenissen waarmee ontwikkel aars op deze gebeurtenissen kunnen reageren. In het volgende voor beeld ziet u welke gebeurtenissen worden geactiveerd wanneer de gebruiker de pop-up opent, sluit of versleept. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Pop-upgebeurtenisen" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>pop-up gebeurtenissen</a> van de Pen per Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Achtergrond](/javascript/api/azure-maps-control/atlas.popup)

> [!div class="nextstepaction"]
> [PopupOptions](/javascript/api/azure-maps-control/atlas.popupoptions)

> [!div class="nextstepaction"]
> [PopupTemplate](/javascript/api/azure-maps-control/atlas.popuptemplate)

Raadpleeg de volgende fantastische artikelen voor voor beelden van volledige code:

> [!div class="nextstepaction"]
> [Een symboollaag toevoegen](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Een HTML-markering toevoegen](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Een lijnlaag toevoegen](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Een polygoonlaag toevoegen](map-add-shape.md)
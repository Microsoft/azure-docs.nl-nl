---
title: Klik op Analytics-invoeg toepassing voor automatische verzamelingen voor Application Insights java script SDK
description: Klik hier voor meer informatie over het installeren en gebruiken van de invoeg toepassing Analytics automatische verzamelingen voor Application Insights java script SDK.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: lagayhar
ms.openlocfilehash: 5ad3e1a5a4ff47fe3d5fee8b8bc79235838995b8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593626"
---
# <a name="click-analytics-auto-collection-plugin-for-application-insights-javascript-sdk"></a>Klik op Analytics-invoeg toepassing voor automatische verzamelingen voor Application Insights java script SDK

Met deze invoeg toepassing worden automatisch getraceerde gebeurtenissen op webpagina's en worden data-* attributen voor HTML-elementen gebruikt voor het invullen van de telemetrie van gebeurtenissen.

## <a name="getting-started"></a>Aan de slag

Gebruikers kunnen de invoeg toepassing voor het automatisch verzamelen van analyses via NPM instellen.

### <a name="npm-setup"></a>NPM-installatie

NPM-pakket installeren:

```bash
npm install --save @microsoft/applicationinsights-clickanalytics-js @microsoft/applicationinsights-web
```

```js

import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ClickAnalyticsPlugin } from '@microsoft/applicationinsights-clickanalytics-js';

const clickPluginInstance = new ClickAnalyticsPlugin();
// Click Analytics configuration
const clickPluginConfig = {
  autoCapture: true
};
// Application Insights Configuration
const configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: clickPluginConfig
  },
};

const appInsights = new ApplicationInsights({ config: configObj });
appInsights.loadAppInsights();
```

## <a name="how-to-effectively-use-the-plugin"></a>De invoeg toepassing effectief gebruiken

1. Telemetriegegevens die zijn gegenereerd op basis van de klik gebeurtenissen worden opgeslagen als `customEvents` in het gedeelte Application Insights van de Azure Portal.
2. De `name` customEvent wordt ingevuld op basis van de volgende regels:
    1.  De `id` waarde die wordt gegeven in de `data-*-id` wordt gebruikt als de naam van de customEvent. Als het geklikte HTML-element bijvoorbeeld het kenmerk ' data-sample-id ' = ' Knop1 ' heeft, is ' Knop1 ' de customEvent-naam.
    2. Als dit kenmerk niet bestaat en als de `useDefaultContentNameOrId` is ingesteld op `true` in de configuratie, wordt het HTML-kenmerk of de `id` inhouds naam van het element gebruikt als de naam van het customEvent.
    3. Als `useDefaultContentNameOrId` is ingesteld op False, wordt de naam van de customEvent "not_specified".

    > [!TIP]
    > Onze aanbevelingen moeten worden ingesteld `useDefaultContentNameOrId` op waar om bruikbare gegevens te genereren.  
3. `parentDataTag` heeft twee dingen:
    1. Als deze tag aanwezig is, haalt de invoeg toepassing de `data-*` kenmerken en waarden op uit alle bovenliggende HTML-elementen van het geklikte element.
    2. Ter verbetering van de efficiëntie wordt deze tag door de invoeg toepassing gebruikt als een vlag, wanneer deze wordt aangetroffen, waardoor de DOM (Document Object Model) niet meer hoeft te worden verwerkt.
    
    > [!CAUTION]
    > Eenmaal `parentDataTag` wordt gebruikt, zoekt de SDK naar bovenliggende labels in uw hele toepassing en niet alleen het HTML-element waarin u het hebt gebruikt.
4. `customDataPrefix` van de gebruiker moet altijd beginnen met `data-` , bijvoorbeeld `data-sample-` . In HTML `data-*` vormen globale kenmerken een klasse kenmerken met de naam aangepaste gegevens kenmerken, waarmee de eigendoms gegevens kunnen worden uitgewisseld tussen de HTML en de bijbehorende dom-weer gave door scripts. Oudere browsers (Internet Explorer, Safari) verwijderen kenmerken die niet worden herkend, tenzij ze beginnen met `data-` .

    De `*` in `data-*`  kan worden vervangen door een naam die volgt op de [productie regel van XML-namen](https://www.w3.org/TR/REC-xml/#NT-Name) met de volgende beperkingen:
    - De naam mag niet beginnen met ' XML ', ongeacht het geval wordt gebruikt voor deze letters.
    - De naam mag geen punt komma (U + 003A) bevatten.
    - De naam mag geen hoofd letters bevatten.

## <a name="configuration"></a>Configuratie

| Naam                  | Type                               | Standaard | Beschrijving                                                                                                                              |
| --------------------- | -----------------------------------| --------| ---------------------------------------------------------------------------------------------------------------------------------------- |
| autocapture           | booleaans                            | true    | Automatische vastleg configuratie.                                                                                                         |
| terugpostactie              | [IValueCallback](#ivaluecallback)  | null    | Configuratie van retour aanroepen.                                                                                                                 |
| pageTags              | tekenreeks                             | null    | Pagina Tags.                                                                                                                               |
| dataTags              | [ICustomDataTags](#icustomdatatags)| null    | Aangepaste gegevenslabels die worden gebruikt voor het overschrijven van standaard tags voor het vastleggen van klik gegevens.                                                           |
| urlCollectHash        | booleaans                            | onjuist   | Hiermee schakelt u de logboek registratie in van waarden na een ' # '-teken van de URL.                                                                          |
| urlCollectQuery       | booleaans                            | onjuist   | Hiermee schakelt u de logboek registratie van de query teken reeks van de URL in.                                                                                      |
| behaviorValidator     | Functie                           | null  | Call back functie die moet worden gebruikt voor de validatie van de `data-*-bhvr` waarde. Ga naar de [sectie behaviorValidator](#behaviorvalidator)voor meer informatie.|
| defaultRightClickBhvr | teken reeks (of) nummer                 | ''      | Standaard waarde voor gedrag wanneer de rechter muisknop op gebeurtenis heeft plaatsgevonden. Deze waarde wordt overschreven als het element het kenmerk heeft `data-*-bhvr` . |
| dropInvalidEvents     | booleaans                            | onjuist   | Markering voor het neerzetten van gebeurtenissen die geen nuttige Klik gegevens hebben.                                                                                   |

### <a name="ivaluecallback"></a>IValueCallback

| Naam               | Type     | Standaard | Beschrijving                                                                             |
| ------------------ | -------- | ------- | --------------------------------------------------------------------------------------- |
| pageName           | Functie | null    | Functie voor het overschrijven van de standaard instelling voor het vastleggen van de pagina naam.                           |
| pageActionPageTags | Functie | null    | Een call back-functie voor het verbeteren van de standaard pageTags die tijdens de pageAction-gebeurtenis is verzameld.  |
| contentName        | Functie | null    | Een call back-functie om een aangepaste inhouds naam in te vullen.                                 |

### <a name="icustomdatatags"></a>ICustomDataTags

| Naam                      | Type    | Standaard   | Standaard label voor gebruik in HTML |   Description                                                                                |
|---------------------------|---------|-----------|-------------|----------------------------------------------------------------------------------------------|
| useDefaultContentNameOrId | booleaans | onjuist     | N.v.t.         |Hiermee wordt het standaard-HTML-kenmerk voor de inhoudsnaam verzameld wanneer een bepaald element niet is gelabeld met de standaard customDataPrefix of wanneer customDataPrefix niet is opgegeven door de gebruiker. |
| customDataPrefix          | tekenreeks  | `data-`   | `data-*`| Automatische vastleg-inhouds naam en-waarde van elementen die zijn gelabeld met het voor voegsel. `data-*-id` `data-<yourcustomattribute>` Kan bijvoorbeeld worden gebruikt in de HTML-tags.   |
| aiBlobAttributeTag        | tekenreeks  | `ai-blob` |  `data-ai-blob`| De invoeg toepassing ondersteunt een JSON BLOB-kenmerk in plaats van afzonderlijke `data-*` kenmerken. |
| metaDataPrefix            | tekenreeks  | null      | N.v.t.  | Automatische opname van de META-element naam en-inhoud van de HTML-kop met het voorziene voor voegsel tijdens het vastleggen. `custom-`Kan bijvoorbeeld worden gebruikt in de HTML-META code. |
| captureAllMetaDataContent | booleaans | onjuist     | N.v.t.   | Automatisch de namen en inhoud van het META-element van de HTML-kop vastleggen. De standaardinstelling is onwaar. Als u dit inschakelt, wordt het gegeven metaDataPrefix overschreven. |
| parentDataTag             | tekenreeks  | null      |  N.v.t.  | Stopt met het door lopen van de DOM om de inhouds naam en waarde van elementen vast te leggen wanneer deze tag wordt aangetroffen. `data-<yourparentDataTag>`Kan bijvoorbeeld worden gebruikt in de HTML-tags.|
| dntDataTag                | tekenreeks  | `ai-dnt`  |  `data-ai-dnt`| HTML-elementen met dit kenmerk worden door de invoeg toepassing genegeerd voor het vastleggen van telemetriegegevens.|

### <a name="behaviorvalidator"></a>behaviorValidator

Met de behaviorValidator-functies wordt automatisch gecontroleerd of gelabelde gedragingen in code voldoen aan een vooraf gedefinieerde lijst. Dit zorgt ervoor dat gelabeld gedrag consistent is met de bestaande taxonomie van uw onderneming. Het is niet vereist of verwacht dat de meeste Azure Monitor klanten dit zullen gebruiken, maar het is wel beschikbaar voor geavanceerde scenario's. Er zijn drie verschillende behaviorValidator call back-functies beschikbaar als onderdeel van deze uitbrei ding. Gebruikers kunnen echter hun eigen call back-functies gebruiken als de blootgestelde functies uw vereiste niet oplossen. Het doel is om uw eigen gedrags gegevens structuur te nemen, de invoeg toepassing gebruikt deze functie voor validatie tijdens het extra heren van het gedrag uit de gegevenslabels.

| Naam                   | Beschrijving                                                                        |
| ---------------------- | -----------------------------------------------------------------------------------|
| BehaviorValueValidator | Gebruik deze call back-functie als uw gedrags gegevens structuur een matrix met teken reeksen is.|
| BehaviorMapValidator   | Gebruik deze call back-functie als de gegevens structuur van het gedrag een woorden lijst is.       |
| BehaviorEnumValidator  | Gebruik deze call back-functie als uw gedrags gegevens structuur een Enum-waarde is.            |

#### <a name="sample-usage-with-behaviorvalidator"></a>Voor beeld van gebruik met behaviorValidator

```js
var clickPlugin = Microsoft.ApplicationInsights.ClickAnalyticsPlugin;
var clickPluginInstance = new clickPlugin();

// Behavior enum values
var behaviorMap = {
  UNDEFINED: 0, // default, Undefined

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Page Experience [1-19]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  NAVIGATIONBACK: 1, // Advancing to the previous index position within a webpage
  NAVIGATION: 2, // Advancing to a specific index position within a webpage
  NAVIGATIONFORWARD: 3, // Advancing to the next index position within a webpage
  APPLY: 4, // Applying filter(s) or making selections
  REMOVE: 5, // Applying filter(s) or removing selections
  SORT: 6, // Sorting content
  EXPAND: 7, // Expanding content or content container
  REDUCE: 8, // Sorting content
  CONTEXTMENU: 9, // Context Menu
  TAB: 10, // Tab control
  COPY: 11, // Copy the contents of a page
  EXPERIMENTATION: 12, // Used to identify a third party experimentation event
  PRINT: 13, // User printed page
  SHOW: 14, //  Displaying an overlay
  HIDE: 15, //  Hiding an overlay
  MAXIMIZE: 16, //  Maximizing an overlay
  MINIMIZE: 17, // Minimizing an overlay
  BACKBUTTON: 18, //  Clicking the back button

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Scenario Process [20-39]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  STARTPROCESS: 20, // Initiate a web process unique to adopter
  PROCESSCHECKPOINT: 21, // Represents a checkpoint in a web process unique to adopter
  COMPLETEPROCESS: 22, // Page Actions that complete a web process unique to adopter
  SCENARIOCANCEL: 23, // Actions resulting from cancelling a process/scenario

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Download [40-59]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  DOWNLOADCOMMIT: 40, // Initiating an unmeasurable off-network download
  DOWNLOAD: 41, // Initiating a download

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Search [60-79]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SEARCHAUTOCOMPLETE: 60, // Auto-completing a search query during user input
  SEARCH: 61, // Submitting a search query
  SEARCHINITIATE: 62, // Initiating a search query
  TEXTBOXINPUT: 63, // Typing or entering text in the text box

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Commerce [80-99]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIEWCART: 82, // Viewing the cart
  ADDWISHLIST: 83, // Adding a physical or digital good or services to a wishlist
  FINDSTORE: 84, // Finding a physical store
  CHECKOUT: 85, // Before you fill in credit card info
  REMOVEFROMCART: 86, // Remove an item from the cart
  PURCHASECOMPLETE: 87, // Used to track the pageView event that happens when the CongratsPage or Thank You page loads after a successful purchase
  VIEWCHECKOUTPAGE: 88, // View the checkout page
  VIEWCARTPAGE: 89, // View the cart page
  VIEWPDP: 90, // View a PDP
  UPDATEITEMQUANTITY: 91, // Update an item's quantity
  INTENTTOBUY: 92, // User has the intent to buy an item
  PUSHTOINSTALL: 93, // User has selected the push to install option

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Authentication [100-119]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNIN: 100, // User sign-in
  SIGNOUT: 101, // User sign-out

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Social [120-139]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SOCIALSHARE: 120, // "Sharing" content for a specific social channel
  SOCIALLIKE: 121, // "Liking" content for a specific social channel
  SOCIALREPLY: 122, // "Replying" content for a specific social channel
  CALL: 123, // Click on a "call" link
  EMAIL: 124, // Click on an "email" link
  COMMUNITY: 125, // Click on a "community" link

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Feedback [140-159]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VOTE: 140, // Rating content or voting for content
  SURVEYCHECKPOINT: 145, // reaching the survey page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Registration, Contact [160-179]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  REGISTRATIONINITIATE: 161, // Initiating a registration process
  REGISTRATIONCOMPLETE: 162, // Completing a registration process
  CANCELSUBSCRIPTION: 163, // Canceling a subscription
  RENEWSUBSCRIPTION: 164, // Renewing a subscription
  CHANGESUBSCRIPTION: 165, // Changing a subscription
  REGISTRATIONCHECKPOINT: 166, // Reaching the registration page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Chat [180-199]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  CHATINITIATE: 180, // Initiating a chat experience
  CHATEND: 181, // Ending a chat experience

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Trial [200-209]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  TRIALSIGNUP: 200, // Signing-up for a trial
  TRIALINITIATE: 201, // Initiating a trial

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Signup [210-219]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNUP: 210, // Signing-up for a notification or service
  FREESIGNUP: 211, // Signing-up for a free service

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Referals [220-229]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  PARTNERREFERRAL: 220, // Navigating to a partner's web property

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Intents [230-239]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  LEARNLOWFUNNEL: 230, // Engaging in learning behavior on a commerce page (ex. "Learn more click")
  LEARNHIGHFUNNEL: 231, // Engaging in learning behavior on a non-commerce page (ex. "Learn more click")
  SHOPPINGINTENT: 232, // Shopping behavior prior to landing on a commerce page

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Video [240-259]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIDEOSTART: 240, // Initiating a video
  VIDEOPAUSE: 241, // Pausing a video
  VIDEOCONTINUE: 242, // Pausing or resuming a video.
  VIDEOCHECKPOINT: 243, // Capturing predetermined video percentage complete.
  VIDEOJUMP: 244, // Jumping to a new video location.
  VIDEOCOMPLETE: 245, // Completing a video (or % proxy)
  VIDEOBUFFERING: 246, // Capturing a video buffer event
  VIDEOERROR: 247, // Capturing a video error
  VIDEOMUTE: 248, // Muting a video
  VIDEOUNMUTE: 249, // Unmuting a video
  VIDEOFULLSCREEN: 250, // Making a video full screen
  VIDEOUNFULLSCREEN: 251, // Making a video return from full screen to original size
  VIDEOREPLAY: 252, // Making a video replay
  VIDEOPLAYERLOAD: 253, // Loading the video player
  VIDEOPLAYERCLICK: 254, //  Click on a button within the interactive player
  VIDEOVOLUMECONTROL: 255, //  Click on video volume control
  VIDEOAUDIOTRACKCONTROL: 256, // Click on audio control within a video
  VIDEOCLOSEDCAPTIONCONTROL: 257, //  Click on the closed caption control
  VIDEOCLOSEDCAPTIONSTYLE: 258, //  Click to change closed caption style
  VIDEORESOLUTIONCONTROL: 259, //  Click to change resolution

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  //    Advertisement Engagement [280-299]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  ADBUFFERING: 283, // Ad is buffering
  ADERROR: 284, // Ad error
  ADSTART: 285, // Ad start
  ADCOMPLETE: 286, // Ad complete
  ADSKIP: 287, // Ad skipped
  ADTIMEOUT: 288, // Ad timed-out
  OTHER: 300 // Other
};

// Application Insights Configuration
var configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: {
      behaviorValidator: Microsoft.ApplicationInsights.BehaviorMapValidator(behaviorMap),
      defaultRightClickBhvr: 9
    },
  },
};
var appInsights = new Microsoft.ApplicationInsights.ApplicationInsights({
  config: configObj
});
appInsights.loadAppInsights();
```

## <a name="sample-app"></a>Voorbeeldapp

[Eenvoudige web-app met Klik op Analytics-invoeg toepassing voor automatische verzamelingen ingeschakeld](https://go.microsoft.com/fwlink/?linkid=2152871).

## <a name="next-steps"></a>Volgende stappen

- Bekijk de [github-opslag plaats](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-clickanalytics-js) en het [NPM-pakket](https://www.npmjs.com/package/@microsoft/applicationinsights-clickanalytics-js) voor de invoeg toepassing voor automatische verzamelingen van de analyse.
- Gebruik [gebeurtenissen analyse in de gebruiks ervaring](usage-segmentation.md) om de meeste klikken en segment te analyseren op beschik bare dimensies.
- Zoek op gegevens onder inhouds veld in het kenmerk customDimensions in de tabel CustomEvents in [log Analytics](../logs/log-analytics-tutorial.md#write-a-query). Zie voor [beeld-app](https://go.microsoft.com/fwlink/?linkid=2152871) voor aanvullende richt lijnen.
- Een [werkmap](../visualize/workbooks-overview.md) bouwen om aangepaste visualisaties van klik gegevens te maken.

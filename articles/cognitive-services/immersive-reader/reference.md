---
title: Naslag Gids voor insluitende lezers SDK
titleSuffix: Azure Cognitive Services
description: De insluitende lezer-SDK bevat een Java script-bibliotheek waarmee u de insluitende lezer kunt integreren in uw toepassing.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metang
ms.openlocfilehash: f2f5c8193454a3b7fa6be1cea7a1236b613d6c8f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92636524"
---
# <a name="immersive-reader-javascript-sdk-reference-v11"></a>Naslag informatie voor de Inge sloten Reader java script SDK (v 1.1)

De insluitende lezer-SDK bevat een Java script-bibliotheek waarmee u de insluitende lezer kunt integreren in uw toepassing.

## <a name="functions"></a>Functions

De SDK biedt de volgende functies beschikbaar:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

<br>

## <a name="launchasync"></a>launchAsync

Hiermee start u de insluitende lezer binnen een `iframe` in uw webtoepassing. Houd er rekening mee dat de grootte van uw inhoud beperkt is tot een maximum van 50 MB.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

#### <a name="launchasync-parameters"></a>launchAsync-para meters

| Naam | Type | Description |
| ---- | ---- |------------ |
| `token` | tekenreeks | Het Azure AD-verificatie token. Zie [How-een resource voor een insluitende lezer maken](./how-to-create-immersive-reader.md) voor meer informatie. |
| `subdomain` | tekenreeks | Het aangepaste subdomein van uw insluitende lezer-resource in Azure. Zie [How-een resource voor een insluitende lezer maken](./how-to-create-immersive-reader.md) voor meer informatie. |
| `content` | [Inhoud](#content) | Een object met de inhoud die in de insluitende lezer moet worden weer gegeven. |
| `options` | [Opties](#options) | Opties voor het configureren van bepaald gedrag van de insluitende lezer. Optioneel. |

#### <a name="returns"></a>Retouren

Retourneert een `Promise<LaunchResponse>` , die wordt omgezet wanneer de insluitende lezer wordt geladen. De wordt `Promise` omgezet in een- [`LaunchResponse`](#launchresponse) object.

#### <a name="exceptions"></a>Uitzonderingen

De geretourneerde `Promise` wordt met een [`Error`](#error) object afgewezen als de insluitende lezer niet kan worden geladen. Zie de [fout codes](#error-codes)voor meer informatie.

<br>

## <a name="close"></a>sluiten

Hiermee wordt de insluitende lezer gesloten.

Een voor beeld van een use-case voor deze functie is als de knop Afsluiten verborgen is door ```hideExitButton: true``` in de [Opties](#options)in te stellen. Vervolgens kan een andere knop (bijvoorbeeld de pijl-terug van een mobiele koptekst) deze functie aanroepen ```close``` Wanneer erop wordt geklikt.

```typescript
close(): void;
```

<br>

## <a name="immersive-reader-launch-button"></a>Knop Start van insluitende lezer

De SDK biedt standaard stijlen voor de knop voor het starten van de insluitende lezer. Gebruik het `immersive-reader-button` kenmerk Class om deze stijl in te scha kelen. Zie [How-to Customize the insluitende lezer (knop)](./how-to-customize-launch-button.md) voor meer informatie.

```html
<div class='immersive-reader-button'></div>
```

#### <a name="optional-attributes"></a>Optionele kenmerken

Gebruik de volgende kenmerken om het uiterlijk van de knop te configureren.

| Kenmerk | Beschrijving |
| --------- | ----------- |
| `data-button-style` | Hiermee wordt de stijl van de knop ingesteld. De waarde kan `icon`, `text`of `iconAndText` zijn. Wordt standaard ingesteld op `icon` . |
| `data-locale` | Hiermee stelt u de land instelling. Bijvoorbeeld `en-US` of `fr-FR`. Wordt standaard ingesteld op Engels `en` . |
| `data-icon-px-size` | Hiermee stelt u de grootte van het pictogram in pixels. De standaard waarde is 20px. |

<br>

## <a name="renderbuttons"></a>renderButtons

De ```renderButtons``` functie is niet nodig als u de richt lijnen [voor de insluitende lezer-knop aanpassen](./how-to-customize-launch-button.md) gebruikt.

Deze functie stijlen en werkt de elementen van de insluitende lezer van het document bij. Als ```options.elements``` wordt vermeld, worden de knoppen weer gegeven in elk element dat is opgenomen in ```options.elements``` . Het gebruik van de ```options.elements``` para meter is handig als u meerdere secties in uw document hebt waarop u de insluitende lezer wilt starten en een vereenvoudigde manier wilt gebruiken om meerdere knoppen met dezelfde stijl weer te geven, of als u de knoppen wilt weer geven met een eenvoudig en consistent ontwerp patroon. Als u deze functie wilt gebruiken met de para meter [renderButtons Options](#renderbuttons-options) , roept u de ```ImmersiveReader.renderButtons(options: RenderButtonsOptions);``` pagina belasting aan zoals gedemonstreerd in het onderstaande code fragment. Anders worden de knoppen weer gegeven in de elementen van het document die de klasse hebben ```immersive-reader-button``` , zoals wordt weer gegeven in de [knop voor de insluitende lezer aanpassen](./how-to-customize-launch-button.md) .

```typescript
// This snippet assumes there are two empty div elements in
// the page HTML, button1 and button2.
const btn1: HTMLDivElement = document.getElementById('button1');
const btn2: HTMLDivElement = document.getElementById('button2');
const btns: HTMLDivElement[] = [btn1, btn2];
ImmersiveReader.renderButtons({elements: btns});
```

Zie de bovenstaande [optionele kenmerken](#optional-attributes) voor meer rendering-opties. Als u deze opties wilt gebruiken, voegt u de optie kenmerken toe aan elk van de ```HTMLDivElement``` HTML van uw pagina.

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

#### <a name="renderbuttons-parameters"></a>renderButtons-para meters

| Naam | Type | Beschrijving |
| ---- | ---- |------------ |
| `options` | [opties voor renderButtons](#renderbuttons-options) | Opties voor het configureren van bepaald gedrag van de functie renderButtons. Optioneel. |

### <a name="renderbuttons-options"></a>Opties voor renderButtons

Opties voor het weer geven van de knoppen voor insluitende lezer.

```typescript
{
    elements: HTMLDivElement[];
}
```

#### <a name="renderbuttons-options-parameters"></a>Para meters voor renderButtons-opties

| Instelling | Type | Beschrijving |
| ------- | ---- | ----------- |
| opties | HTMLDivElement[] | Elementen voor het weer geven van de insluitende lezer in. |

##### `elements`
```Parameters
Type: HTMLDivElement[]
Required: false
```

<br>

## <a name="launchresponse"></a>LaunchResponse

Bevat de reactie van de aanroep van `ImmersiveReader.launchAsync` . Houd er rekening mee dat er een verwijzing naar de `iframe` met de insluitende lezer kan worden geopend via `container.firstChild` .

```typescript
{
    container: HTMLDivElement;
    sessionId: string;
}
```

#### <a name="launchresponse-parameters"></a>LaunchResponse-para meters

| Instelling | Type | Beschrijving |
| ------- | ---- | ----------- |
| container | HTMLDivElement | HTML-element dat de insluitende Reader iframe bevat. |
| sessionId | Tekenreeks | Wereld wijd unieke id voor deze sessie, die wordt gebruikt voor fout opsporing. |
 
## <a name="error"></a>Fout

Bevat informatie over een fout.

```typescript
{
    code: string;
    message: string;
}
```

#### <a name="error-parameters"></a>Fout parameters

| Instelling | Type | Beschrijving |
| ------- | ---- | ----------- |
| code | Tekenreeks | Een van een set fout codes. Zie [Foutcodes](#error-codes). |
| message | Tekenreeks | Door de mens lees bare weer gave van de fout. |

#### <a name="error-codes"></a>Foutcodes

| Code | Beschrijving |
| ---- | ----------- |
| BadArgument | Het opgegeven argument is ongeldig `message` . Zie de para meter van de [fout](#error). |
| Time-out | De insluitende lezer kan niet worden geladen binnen de opgegeven time-out. |
| TokenExpired | Het opgegeven token is verlopen. |
| Beperkt | De limiet voor de aanroep frequentie is overschreden. |

<br>

## <a name="types"></a>Typen

### <a name="content"></a>Content

Bevat de inhoud die in de insluitende lezer moet worden weer gegeven.

```typescript
{
    title?: string;
    chunks: Chunk[];
}
```

#### <a name="content-parameters"></a>Inhouds parameters

| Naam | Type | Beschrijving |
| ---- | ---- |------------ |
| title | Tekenreeks | Titel tekst die boven aan de insluitende lezer wordt weer gegeven (optioneel) |
| segmenten | [Segment []](#chunk) | Matrix van segmenten |

##### `title`
```Parameters
Type: String
Required: false
Default value: "Immersive Reader" 
```

##### `chunks`
```Parameters
Type: Chunk[]
Required: true
Default value: null 
```

<br>

### <a name="chunk"></a>Chunk

Eén gegevens segment dat wordt door gegeven aan de inhoud van de insluitende lezer.

```typescript
{
    content: string;
    lang?: string;
    mimeType?: string;
}
```

#### <a name="chunk-parameters"></a>Segment parameters

| Naam | Type | Beschrijving |
| ---- | ---- |------------ |
| inhoud | Tekenreeks | De teken reeks met de inhoud die naar de insluitende lezer wordt verzonden. |
| lang | Tekenreeks | De taal van de tekst is de waarde in de indeling van de taal code van de IETF BCP 47, bijvoorbeeld en, es-ES. De taal wordt automatisch gedetecteerd als deze niet is opgegeven. Zie [Ondersteunde talen](#supported-languages) voor meer informatie. |
| MIME type | tekenreeks | Onbewerkte tekst, MathML, HTML & micro soft Word DOCX-indelingen worden ondersteund. Zie [ondersteunde MIME-typen](#supported-mime-types) voor meer informatie. |

##### `content`
```Parameters
Type: String
Required: true
Default value: null 
```

##### `lang`
```Parameters
Type: String
Required: false
Default value: Automatically detected 
```

##### `mimeType`
```Parameters
Type: String
Required: false
Default value: "text/plain"
```

#### <a name="supported-mime-types"></a>Ondersteunde MIME-typen

| MIME-type | Beschrijving |
| --------- | ----------- |
| tekst/zonder opmaak | Tekst zonder opmaak. |
| text/html | HTML-inhoud. [Meer informatie](#html-support)|
| Application/MathML + XML | MathML (wiskundige Markup Language). [Meer informatie](./how-to/display-math.md).
| Application/vnd.openxmlformats-officedocument.wordprocessingml.document | Micro soft Word. docx-indelings document.


<br>

## <a name="options"></a>Opties

Bevat eigenschappen die bepaald gedrag van de insluitende lezer configureren.

```typescript
{
    uiLang?: string;
    timeout?: number;
    uiZIndex?: number;
    useWebview?: boolean;
    onExit?: () => any;
    allowFullscreen?: boolean;
    hideExitButton?: boolean;
    cookiePolicy?: CookiePolicy;
    disableFirstRun?: boolean;
    readAloudOptions?: ReadAloudOptions;
    translationOptions?: TranslationOptions;
    displayOptions?: DisplayOptions;
    preferences?: string;
    onPreferencesChanged?: (value: string) => any;
    customDomain?: string;
}
```

#### <a name="options-parameters"></a>Opties para meters

| Naam | Type | Beschrijving |
| ---- | ---- |------------ |
| uiLang | Tekenreeks | Taal van de gebruikers interface, de waarde is in de indeling van de code van het IETF BCP 47-taal label, bijvoorbeeld en, es-ES. De standaard instelling is browser taal als deze niet is opgegeven. |
| timeout | Aantal | De duur (in milliseconden) voordat [launchAsync](#launchasync) mislukt met een time-outfout (de standaard waarde is 15000 MS). Deze time-out is alleen van toepassing op de eerste keer dat de lees pagina wordt gestart, waar succes wordt waargenomen wanneer de lees pagina wordt geopend en het kring veld wordt gestart. De aanpassing van de time-out moet niet nodig zijn. |
| uiZIndex | Aantal | Z-index van het iframe dat wordt gemaakt (de standaard waarde is 1000). |
| useWebview | Booleaans| Gebruik een webweergave-tag in plaats van een IFRAME voor compatibiliteit met Chrome-apps (de standaard waarde is False). |
| Verlaten | Functie | Wordt uitgevoerd wanneer de insluitende lezer wordt afgesloten. |
| allowFullscreen | Booleaans | De mogelijkheid om fullscreen te wisselen (standaard waarde is True). |
| hideExitButton | Booleaans | Hiermee wordt aangegeven of de pijl voor de afsluit knop van de insluitende lezer moet worden verborgen (de standaard waarde is False). Dit moet alleen waar zijn als er een alternatief mechanisme beschikbaar is voor het afsluiten van de insluitende lezer (bijvoorbeeld de pijl van een mobiele werk balk). |
| cookiePolicy | [CookiePolicy](#cookiepolicy-options) | Instelling voor het cookie gebruik van de insluitende lezer (de standaard waarde is *CookiePolicy. Disable*). Het is de verantwoordelijkheid van de hosttoepassing om elke vereiste toestemming van de gebruiker te verkrijgen in overeenstemming met het nalevings beleid van de EU-cookie. Zie [cookie-beleids opties](#cookiepolicy-options). |
| disableFirstRun | Booleaans | Schakel de eerste sessie uit. |
| readAloudOptions | [ReadAloudOptions](#readaloudoptions) | Opties voor het configureren van hardop voor lezen. |
| translationOptions | [TranslationOptions](#translationoptions) | Opties voor het configureren van de vertaling. |
| displayOptions | [DisplayOptions](#displayoptions) | Opties voor het configureren van tekst grootte, letter type, enzovoort. |
| kun | Tekenreeks | De teken reeks die is geretourneerd door onPreferencesChanged die de voor keuren van de gebruiker in de insluitende lezer vertegenwoordigt. Zie [instellingen-para meters](#settings-parameters) en [instructies voor het opslaan van gebruikers voorkeuren](./how-to-store-user-preferences.md) voor meer informatie. |
| onPreferencesChanged | Functie | Wordt uitgevoerd wanneer de voor keuren van de gebruiker zijn gewijzigd. Zie [How-to Store User Preferences (Engelstalig)](./how-to-store-user-preferences.md) voor meer informatie. |
| customDomain | Tekenreeks | Gereserveerd voor intern gebruik. Aangepast domein waarin de insluitende Reader webapp wordt gehost (standaard is null). |

##### `uiLang`
```Parameters
Type: String
Required: false
Default value: User's browser language 
```

##### `timeout`
```Parameters
Type: Number
Required: false
Default value: 15000
```

##### `uiZIndex`
```Parameters
Type: Number
Required: false
Default value: 1000
```

##### `onExit`
```Parameters
Type: Function
Required: false
Default value: null
```

##### `preferences`

> [!CAUTION]
> **Belang rijk** Probeer niet programmatisch de waarden te wijzigen van de `-preferences` teken reeks die wordt verzonden naar en van de toepassing voor insluitende lezer. Dit kan leiden tot onverwacht gedrag als gevolg van een gedegradeerde gebruikers ervaring voor uw klanten. Hosttoepassingen mogen nooit een aangepaste waarde toewijzen aan of de `-preferences` teken reeks bewerken. `-preferences`Gebruik de optie teken reeks om alleen de exacte waarde te gebruiken die is geretourneerd met de `-onPreferencesChanged` optie terugbellen.

```Parameters
Type: String
Required: false
Default value: null
```

##### `onPreferencesChanged`
```Parameters
Type: Function
Required: false
Default value: null
```

##### `customDomain`
```Parameters
Type: String
Required: false
Default value: null
```

<br>

## <a name="readaloudoptions"></a>ReadAloudOptions

```typescript
type ReadAloudOptions = {
    voice?: string;
    speed?: number;
    autoplay?: boolean;
};
```

#### <a name="readaloudoptions-parameters"></a>ReadAloudOptions-para meters

| Naam | Type | Beschrijving |
| ---- | ---- |------------ |
| stem | Tekenreeks | Stem, "vrouwelijk" of "mannelijk". Houd er rekening mee dat niet alle talen zowel geslachten ondersteunen. |
| snelheid | Aantal | De afspeel snelheid moet liggen tussen 0,5 en 2,5. |
| Zorgen | Booleaans | Automatisch hardop lezen starten wanneer de insluitende lezer wordt geladen. |

##### `voice`
```Parameters
Type: String
Required: false
Default value: "Female" or "Male" (determined by language) 
Values available: "Female", "Male"
```

##### `speed`
```Parameters
Type: Number
Required: false
Default value: 1
Values available: 0.5, 0.75, 1, 1.25, 1.5, 1.75, 2, 2.25, 2.5
```

> [!NOTE]
> Vanwege beperkingen van de browser wordt AutoPlay niet ondersteund in Safari.

<br>

## <a name="translationoptions"></a>TranslationOptions

```typescript
type TranslationOptions = {
    language: string;
    autoEnableDocumentTranslation?: boolean;
    autoEnableWordTranslation?: boolean;
};
```

#### <a name="translationoptions-parameters"></a>TranslationOptions-para meters

| Naam | Type | Beschrijving |
| ---- | ---- |------------ |
| language | Tekenreeks | Hiermee stelt u de taal van de vertaling in, de waarde is in de indeling van de taal code van de IETF BCP 47, bijvoorbeeld fr-FR, es-MX, zh-Hans-CN. Vereist om automatische vertaling van woorden of documenten in te scha kelen. |
| autoEnableDocumentTranslation | Booleaans | Het hele document automatisch vertalen. |
| autoEnableWordTranslation | Booleaans | Automatische vertaling van woorden inschakelen. |

##### `language`
```Parameters
Type: String
Required: true
Default value: null 
Values available: See the Supported Languages section
```

<br>

## <a name="displayoptions"></a>DisplayOptions

```typescript
type DisplayOptions = {
    textSize?: number;
    increaseSpacing?: boolean;
    fontFamily?: string;
};
```

#### <a name="displayoptions-parameters"></a>DisplayOptions-para meters

| Naam | Type | Beschrijving |
| ---- | ---- |------------ |
| textSize | Aantal | Hiermee stelt u de gekozen tekst grootte. |
| increaseSpacing | Booleaans | Hiermee wordt ingesteld of tekst afstand wordt in-of uitgeschakeld. |
| fontFamily | Tekenreeks | Hiermee stelt u het gekozen letter type (' Calibri ', ' ComicSans ' of ' Sitka '). |

##### `textSize`
```Parameters
Type: Number
Required: false
Default value: 20, 36 or 42 (Determined by screen size)
Values available: 14, 20, 28, 36, 42, 48, 56, 64, 72, 84, 96
```

##### `fontFamily`
```Parameters
Type: String
Required: false
Default value: "Calibri"
Values available: "Calibri", "Sitka", "ComicSans"
```

<br>

## <a name="cookiepolicy-options"></a>Opties voor CookiePolicy

```typescript
enum CookiePolicy { Disable, Enable }
```

**De onderstaande instellingen zijn alleen ter informatie bedoeld**. De insluitende lezer slaat de instellingen, of gebruikers voorkeuren, op in cookies. Met deze *cookiePolicy* -optie wordt het gebruik van cookies standaard **uitgeschakeld** om te voldoen aan de nalevings wetgeving van de EU. Als u cookies opnieuw wilt inschakelen en de standaard functionaliteit voor insluitende lezers gebruikers voorkeuren wilt herstellen, moet u ervoor zorgen dat uw website of toepassing de juiste toestemming van de gebruiker krijgt om cookies in te scha kelen. Als u cookies in de insluitende lezer opnieuw wilt inschakelen, moet u de optie *cookiePolicy* expliciet instellen op *cookiePolicy. enable* bij het starten van de insluitende lezer. In de volgende tabel wordt beschreven welke instellingen de insluitende lezer opslaat in de cookie wanneer de optie *cookiePolicy* is ingeschakeld.

#### <a name="settings-parameters"></a>Instellingen parameters

| Instelling | Type | Beschrijving |
| ------- | ---- | ----------- |
| textSize | Aantal | Hiermee stelt u de gekozen tekst grootte. |
| fontFamily | Tekenreeks | Hiermee stelt u het gekozen letter type (' Calibri ', ' ComicSans ' of ' Sitka '). |
| textSpacing | Aantal | Hiermee wordt ingesteld of tekst afstand wordt in-of uitgeschakeld. |
| formattingEnabled | Booleaans | Hiermee wordt ingesteld of HTML-opmaak wordt in-of uitgeschakeld. |
| ThemeName | Tekenreeks | Hiermee stelt u het gekozen thema (bijvoorbeeld ' licht ', ' donker '...). |
| syllabificationEnabled | Booleaans | Hiermee wordt ingesteld of syllabification is in-of uitgeschakeld. |
| nounHighlightingEnabled | Booleaans | Hiermee stelt u in of het markeren van zelfstandig naam woord wordt in-of uitgeschakeld. |
| nounHighlightingColor | Tekenreeks | Hiermee stelt u de gekozen kleur voor de uitnaam van de uitwoording. |
| verbHighlightingEnabled | Booleaans | Hiermee wordt ingesteld of het markeren van woorden wordt in-of uitgeschakeld. |
| verbHighlightingColor | Tekenreeks | Hiermee stelt u de markerings kleur van de gekozen term in. |
| adjectiveHighlightingEnabled | Booleaans | Hiermee wordt ingesteld of het markeren van de samenvoegings functie is in-of uitgeschakeld. |
| adjectiveHighlightingColor | Tekenreeks | Hiermee stelt u de gekozen kleur voor de markering van de bijnaam. |
| adverbHighlightingEnabled | Booleaans | Hiermee wordt ingesteld of het markeren van de belichtingen moet worden in-of uitgeschakeld. |
| adverbHighlightingColor | Tekenreeks | Hiermee stelt u de geselecteerde belichtings kleur voor de markering in. |
| pictureDictionaryEnabled | Booleaans | Hiermee stelt u in of afbeeldings woordenlijst moet worden in-of uitgeschakeld. |
| posLabelsEnabled | Booleaans | Hiermee wordt ingesteld of het tekst label Super script van elk gemarkeerd deel van de spraak wordt in-of uitgeschakeld.  |

<br>

## <a name="supported-languages"></a>Ondersteunde talen

De Vertaal functie van insluitende lezer ondersteunt veel talen. Zie [taal ondersteuning](./language-support.md) voor meer informatie.

<br>

## <a name="html-support"></a>HTML-ondersteuning

Wanneer de opmaak is ingeschakeld, wordt de volgende inhoud weer gegeven als HTML in de insluitende lezer.

| HTML | Ondersteunde inhoud |
| --------- | ----------- |
| Lettertype stijlen | Vet, cursief, onderstrepen, code, doorhaling, Super script |
| Niet-geordende lijsten | Schijf, cirkel, vier kant |
| Geordende lijsten | Decimaal, hoofd letter, kleine letter alfa, bovenste Romeinse, Lower-Roman |

Niet-ondersteunde labels worden weer gegeven comparably. Afbeeldingen en tabellen worden momenteel niet ondersteund.

<br>

## <a name="browser-support"></a>Browserondersteuning

Gebruik de meest recente versies van de volgende browsers voor de beste ervaring met de insluitende lezer.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

<br>

## <a name="next-steps"></a>Volgende stappen

* De [insluitende lezer-SDK op github](https://github.com/microsoft/immersive-reader-sdk) verkennen
* [Snelstartgids: een web-app maken die de insluitende lezer (C#) start](./quickstarts/client-libraries.md?pivots=programming-language-csharp)

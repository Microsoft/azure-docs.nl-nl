---
title: Translator v 2.0
titleSuffix: Azure Cognitive Services
description: Referentie documentatie voor de Translator v 2.0.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: 25dda63c6450040e396de9ee0d3fb0a459416343
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97606824"
---
# <a name="translator-v20"></a>Translator v 2.0

> [!IMPORTANT]
> Deze versie van het conversie programma is afgeschaft. [Raadpleeg de documentatie voor versie 3 van het conversie programma](v3-0-reference.md).

Versie 2 van het conversie programma kan naadloos worden geïntegreerd in uw apps, websites, hulpprogram ma's of andere oplossingen om gebruikers ervaring in meerdere talen te bieden. U kunt deze gebruiken op elk platform en met elk besturings systeem voor het uitvoeren van taal omzetting en andere aan de taal gerelateerde taken, zoals tekst taal detectie en tekst-naar-spraak, volgens de industrie normen. Zie [Translator](../translator-info-overview.md)voor meer informatie.

## <a name="getting-started"></a>Aan de slag
Voor toegang tot de vertaler moet u [zich aanmelden voor Microsoft Azure](../translator-how-to-signup.md).

## <a name="authentication"></a>Verificatie 
Voor alle aanroepen van het conversie programma is een abonnements sleutel vereist voor verificatie. De API ondersteunt drie verificatie methoden:

- Een toegangs token. Gebruik de abonnements sleutel om een toegangs token te maken door een POST-aanvraag te doen voor de verificatie service. Raadpleeg de documentatie van de token-service voor meer informatie. Geef het toegangs token door aan de Translator-service met behulp van de `Authorization` header of de `access_token` query parameter. Het toegangs token is 10 minuten geldig. Verkrijg elke 10 minuten een nieuw toegangs token en blijf hetzelfde toegangs token gebruiken voor herhaalde aanvragen gedurende de 10 minuten.
- Een abonnements sleutel die rechtstreeks wordt gebruikt. Geef uw abonnements sleutel door als een waarde in de koptekst die is `Ocp-Apim-Subscription-Key` opgenomen in uw aanvraag voor het conversie programma. Wanneer u de abonnements sleutel rechtstreeks gebruikt, hoeft u de token verificatie service niet aan te roepen om een toegangs token te maken.
- Een [Azure Cognitive Services multi-service-abonnement](https://azure.microsoft.com/pricing/details/cognitive-services/). Met deze methode kunt u een enkele geheime sleutel gebruiken om aanvragen voor meerdere services te verifiëren.
Wanneer u een geheime sleutel van meerdere services gebruikt, moet u twee verificatie headers met uw aanvraag meevoegen. De eerste header geeft de geheime sleutel door. De tweede header bevat de regio die aan uw abonnement is gekoppeld:
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

De regio is vereist voor het multi-service-tekst-API-abonnement. De regio die u selecteert, is de enige regio die u voor tekst vertalingen kunt gebruiken wanneer u de sleutel voor meerdere service abonnementen gebruikt. Het moet dezelfde regio zijn die u hebt geselecteerd toen u zich registreerde voor uw abonnement op meerdere services op de Azure Portal.

De beschik bare regio's zijn `australiaeast` , `brazilsouth` ,, `canadacentral` `centralindia` , `centraluseuap` , `eastasia` , `eastus` , `eastus2` , `japaneast` `northeurope` `southcentralus` `southeastasia` `uksouth` `westcentralus` `westeurope` `westus` `westus2` ,,,,,,, en.

Uw abonnements sleutel en toegangs token zijn geheimen die in de weer gave moeten worden verborgen.

## <a name="profanity-handling"></a>Behandeling van scheld woorden
Normaal gesp roken behoudt de Translator-service dat aanwezig is in de bron. De mate van scheld woorden en de context die het ongepaste woord is, verschillen per cultuur. De graad van scheld woorden in de doel taal kan dus worden verg root of verkleind.

Als u wilt voor komen dat de vertaling ongebruik maakt, zelfs als deze zich in de bron tekst bevindt, kunt u de filter optie voor scheld woorden gebruiken voor de methoden die deze ondersteunen. Met deze optie kunt u kiezen of u wilt zien of de woordenset is verwijderd of gemarkeerd met de juiste Tags, of dat u de scheld woorden in het doel wilt toestaan. De geaccepteerde waarden van `ProfanityAction` zijn `NoAction` (standaard), `Marked` en `Deleted` .


|ProfanityAction    |Bewerking |Voorbeeld bron (Japans)  |Voor beeld van vertaling (Engels)  |
|:--|:--|:--|:--|
|Geen actie   |Standaard. Hetzelfde als de optie niet instellen. Scheld is van bron naar doel. |彼はジャッカスです Marketplace. |Hij is een Jackass.  |
|Duidelijke |Ongepaste woorden worden omgeven door XML-tags \<profanity> en \</profanity> . |彼はジャッカスです Marketplace.   |Hij is een \<profanity> Jackass \</profanity> .  |
|Verwijderd    |Ongepaste woorden worden verwijderd uit de uitvoer zonder vervanging. |彼はジャッカスです Marketplace. |Hij is een.   |

    
## <a name="excluding-content-from-translation"></a>Inhoud uitsluiten van vertaling
Wanneer u inhoud omzet met tags, zoals HTML ( `contentType=text/html` ), is het soms handig om specifieke inhoud uit te sluiten van de vertaling. U kunt het kenmerk gebruiken `class=notranslate` om inhoud op te geven die in de oorspronkelijke taal moet blijven. In het volgende voor beeld wordt de inhoud in het eerste `div` element niet vertaald, maar wordt de inhoud van het tweede `div` element vertaald.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>/Translate ophalen

### <a name="implementation-notes"></a>Opmerkingen bij de implementatie
Zet een teken reeks om van de ene taal naar de andere.

De aanvraag-URI is `https://api.microsofttranslator.com/V2/Http.svc/Translate` .

**Retour waarde:** Een teken reeks die de vertaalde tekst weergeeft.

Als u eerder hebt gebruikt `AddTranslation` of `AddTranslationArray` een vertaling hebt opgegeven met een classificatie van 5 of hoger voor dezelfde bron zin, `Translate` retourneert alleen de meest geschikte keuze die beschikbaar is voor uw systeem. "Dezelfde bron zin" betekent precies hetzelfde (100% match), met uitzonde ring van het hoofdletter gebruik, spaties, label waarden en interpunctie aan het einde van een zin. Als er geen classificatie is opgeslagen met de classificatie 5 of hoger, wordt het geretourneerde resultaat de automatische vertaling van micro soft Translator.

### <a name="response-class-status-200"></a>Response-klasse (status 200)

tekenreeks

Type antwoord inhoud: Application/XML

### <a name="parameters"></a>Parameters

|Parameter|Waarde|Beschrijving    |Parameter type|gegevens type|
|:--|:--|:--|:--|:--|
|AppID  |gelaten    |Vereist. Als de `Authorization` of `Ocp-Apim-Subscription-Key` -header wordt gebruikt, laat u het `appid` veld leeg. Als dat niet het geval is, voegt u een teken reeks met `"Bearer" + " " + "access_token"` .|query|tekenreeks|
|tekst|gelaten   |Vereist. Een teken reeks die de tekst vertegenwoordigt die moet worden vertaald. De tekst mag niet meer dan 10.000 tekens bevatten.|query|tekenreeks|
|from|gelaten   |Optioneel. Een teken reeks die de taal code vertegenwoordigt van de tekst die wordt vertaald. Bijvoorbeeld, en voor Engels.|query|tekenreeks|
|tot|gelaten |Vereist. Een teken reeks die de code van de taal voor de omzetting van de tekst in aangeeft.|query|tekenreeks|
|Invoer|gelaten    |Optioneel. De indeling van de tekst die wordt vertaald. Ondersteunde indelingen zijn `text/plain` (standaard) en  `text/html` . HTML-elementen moeten goed opgemaakte, volledige elementen zijn.|query|tekenreeks|
|category|gelaten   |Optioneel. Een teken reeks die de categorie (het domein) van de vertaling bevat. De standaardwaarde is `general`.|query|tekenreeks|
|Autorisatie|gelaten  |Vereist als zowel het `appid` veld als de `Ocp-Apim-Subscription-Key` kop leeg blijven. Autorisatie token:  `"Bearer" + " " + "access_token"` .|koptekst|tekenreeks|
|Ocp-Apim-Subscription-Key|gelaten  |Vereist als zowel het `appid` veld als de `Authorization` kop leeg blijven.|koptekst|tekenreeks|


### <a name="response-messages"></a>Antwoord berichten

|HTTP-statuscode|Reden|
|:--|:--|
|400    |Ongeldige aanvraag. Controleer de invoer parameters en het gedetailleerde fout bericht.|
|401    |Ongeldige referenties.|
|500    |Server fout. Als de fout zich blijft voordoen, laat het ons dan weten. Geef ons de geschatte datum & tijd van de aanvraag en met de aanvraag-ID die is opgenomen in de antwoord header `X-MS-Trans-Info` .|
|503    |De service is tijdelijk niet beschikbaar. Probeer het opnieuw en laat het ons weten als de fout zich blijft voordoen.|

## <a name="post-translatearray"></a>/TranslateArray plaatsen

### <a name="implementation-notes"></a>Opmerkingen bij de implementatie
Hiermee worden vertalingen voor meerdere bron teksten opgehaald.

De aanvraag-URI is `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray` .

Dit is de indeling van de hoofd tekst van de aanvraag:

```
<TranslateArrayRequest>
  <AppId />
  <From>language-code</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" />
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</TranslateArrayRequest>
```

Deze elementen bevinden zich in `TranslateArrayRequest` :


* `AppId`: Vereist. Als de `Authorization` of `Ocp-Apim-Subscription-Key` -header wordt gebruikt, laat u het `AppId` veld leeg. Als dat niet het geval is, voegt u een teken reeks met `"Bearer" + " " + "access_token"` .
* `From`Beschrijving. Een teken reeks die de taal code vertegenwoordigt van de tekst die wordt vertaald. Als dit veld leeg blijft, wordt in het antwoord het resultaat van automatische taal detectie vermeld.
* `Options`Beschrijving. Een `Options` object dat de volgende waarden bevat. Ze zijn allemaal optioneel en standaard ingesteld op de meest voorkomende instellingen. Opgegeven elementen moeten in alfabetische volg orde worden weer gegeven.
    - `Category`: Een teken reeks die de categorie (het domein) van de vertaling bevat. De standaardwaarde is `general`.
    - `ContentType`: De indeling van de tekst die wordt vertaald. De ondersteunde indelingen zijn `text/plain` (standaard), `text/xml` en `text/html` . HTML-elementen moeten goed opgemaakte, volledige elementen zijn.
    - `ProfanityAction`: Hiermee geeft u op hoe scheld woorden worden verwerkt, zoals eerder is beschreven. Geaccepteerde waarden zijn `NoAction` (standaard), `Marked` en `Deleted` .
    - `State`: De gebruikers status die helpt bij het correleren van de aanvraag en het antwoord. Dezelfde inhoud wordt in het antwoord geretourneerd.
    - `Uri`: Hiermee worden de resultaten van deze URI gefilterd. Standaard: `all`.
    - `User`: Resultaten filteren op deze gebruiker. Standaard: `all`.
* `Texts`: Vereist. Een matrix die de tekst voor vertaling bevat. Alle teken reeksen moeten zich in dezelfde taal bestaan. Het totaal van de tekst die moet worden vertaald, mag niet langer zijn dan 10.000 tekens. Het maximum aantal matrix elementen is 2.000.
* `To`: Vereist. Een teken reeks die de code van de taal voor de omzetting van de tekst in aangeeft.

U kunt optionele elementen weglaten. Elementen die directe onderliggende items zijn van, `TranslateArrayRequest` moeten in alfabetische volg orde worden weer gegeven.

De `TranslateArray` methode accepteert `application/xml` of `text/xml` voor `Content-Type` .

**Retour waarde:** Een `TranslateArrayResponse` matrix. Elk `TranslateArrayResponse` heeft deze elementen:

* `Error`: Geeft aan dat er een fout is als er een optreedt. Anders ingesteld op null.
* `OriginalSentenceLengths`: Een matrix met gehele getallen die de lengte van elke zin in de bron tekst aangeeft. De lengte van de matrix geeft het aantal zinnen aan.
* `TranslatedText`: De vertaalde tekst.
* `TranslatedSentenceLengths`: Een matrix met gehele getallen die de lengte van elke zin in de vertaalde tekst aangeeft. De lengte van de matrix geeft het aantal zinnen aan.
* `State`: De gebruikers status die helpt bij het correleren van de aanvraag en het antwoord. Retourneert dezelfde inhoud als de aanvraag.

Dit is de indeling van de antwoord tekst:

```
<ArrayOfTranslateArrayResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <TranslateArrayResponse>
    <From>language-code</From>
    <OriginalTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </OriginalTextSentenceLengths>
    <State/>
    <TranslatedText>string-value</TranslatedText>
    <TranslatedTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </TranslatedTextSentenceLengths>
  </TranslateArrayResponse>
</ArrayOfTranslateArrayResponse>
```

### <a name="response-class-status-200"></a>Response-klasse (status 200)
Een geslaagde reactie omvat een matrix met `TranslateArrayResponse` matrices in de indeling die eerder is beschreven.

tekenreeks

Type antwoord inhoud: Application/XML

### <a name="parameters"></a>Parameters

|Parameter|Waarde|Beschrijving|Parameter type|Gegevenstype|
|:--|:--|:--|:--|:--|
|Autorisatie|gelaten  |Vereist als zowel het `appid` veld als de `Ocp-Apim-Subscription-Key` kop leeg blijven. Autorisatie token:  `"Bearer" + " " + "access_token"` .|koptekst|tekenreeks|
|Ocp-Apim-Subscription-Key|gelaten|Vereist als zowel het `appid` veld als de `Authorization` kop leeg blijven.|koptekst|tekenreeks|

### <a name="response-messages"></a>Antwoord berichten

|HTTP-statuscode   |Reden|
|:--|:--|
|400    |Ongeldige aanvraag. Controleer de invoer parameters en het gedetailleerde fout bericht. Veelvoorkomende fouten zijn onder andere: <ul><li>Het matrix element mag niet leeg zijn.</li><li>Ongeldige categorie.</li><li>Van taal is ongeldig.</li><li>De taal is ongeldig.</li><li>De aanvraag bevat te veel elementen.</li><li>De van-taal wordt niet ondersteund.</li><li>De aan-taal wordt niet ondersteund.</li><li>De Vertaal aanvraag heeft te veel gegevens.</li><li>HTML heeft niet de juiste indeling.</li><li>Er zijn te veel teken reeksen door gegeven in de Vertaal aanvraag.</li></ul>|
|401    |Ongeldige referenties.|
|500    |Server fout. Als de fout zich blijft voordoen, laat het ons dan weten. Geef ons de geschatte datum & tijd van de aanvraag en met de aanvraag-ID die is opgenomen in de antwoord header `X-MS-Trans-Info` .|
|503    |De service is tijdelijk niet beschikbaar. Probeer het opnieuw en laat het ons weten als de fout zich blijft voordoen.|

## <a name="post-getlanguagenames"></a>/GetLanguageNames plaatsen

### <a name="implementation-notes"></a>Opmerkingen bij de implementatie
Hiermee haalt u beschrijvende namen op voor de talen die worden door gegeven als de para meter `languageCodes` , gelokaliseerd in de door gegeven `locale` taal.

De aanvraag-URI is `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames` .

De aanvraag tekst bevat een teken reeks matrix die de ISO 639-1-taal codes vertegenwoordigt waarvoor de beschrijvende namen moeten worden opgehaald. Hier volgt een voorbeeld:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Retour waarde:** Een teken reeks matrix die taal namen bevat die worden ondersteund door de Vertaal Service, gelokaliseerd in de aangevraagde taal.

### <a name="response-class-status-200"></a>Response-klasse (status 200)
Een teken reeks matrix die talen namen bevat die worden ondersteund door de Vertaal Service, gelokaliseerd in de aangevraagde taal.

tekenreeks

Type antwoord inhoud: Application/XML
 
### <a name="parameters"></a>Parameters

|Parameter|Waarde|Beschrijving|Parameter type|Gegevenstype|
|:--|:--|:--|:--|:--|
|AppID|gelaten|Vereist. Als de `Authorization` of `Ocp-Apim-Subscription-Key` -header wordt gebruikt, laat u het `appid` veld leeg. Als dat niet het geval is, voegt u een teken reeks met `"Bearer" + " " + "access_token"` .|query|tekenreeks|
|landinstelling|gelaten |Vereist. Een teken reeks die staat voor een van de volgende, die wordt gebruikt om de taal namen te lokaliseren: <ul><li>De combi natie van een ISO 639 2-letter code met kleine letters en cultuur die is gekoppeld aan een taal en een ISO 3166 2-letter hoofd code subcultuur. <li>Een ISO 639-kleine cultuur code op zichzelf.|query|tekenreeks|
|Autorisatie|gelaten  |Vereist als zowel het `appid` veld als de `Ocp-Apim-Subscription-Key` kop leeg blijven. Autorisatie token:  `"Bearer" + " " + "access_token"` .|koptekst|tekenreeks|
|Ocp-Apim-Subscription-Key|gelaten  |Vereist als zowel het `appid` veld als de `Authorization` kop leeg blijven.|koptekst|tekenreeks|

### <a name="response-messages"></a>Antwoord berichten

|HTTP-statuscode|Reden|
|:--|:--|
|400    |Ongeldige aanvraag. Controleer de invoer parameters en het gedetailleerde fout bericht.|
|401    |Ongeldige referenties.|
|500    |Server fout. Als de fout zich blijft voordoen, laat het ons dan weten. Geef ons de geschatte datum & tijd van de aanvraag en met de aanvraag-ID die is opgenomen in de antwoord header `X-MS-Trans-Info` .|
|503    |De service is tijdelijk niet beschikbaar. Probeer het opnieuw en laat het ons weten als de fout zich blijft voordoen.|

## <a name="get-getlanguagesfortranslate"></a>/GetLanguagesForTranslate ophalen

### <a name="implementation-notes"></a>Opmerkingen bij de implementatie
Hiermee haalt u een lijst met taal codes die talen vertegenwoordigen die door de Vertaal service worden ondersteund.  `Translate` en `TranslateArray` kan tussen twee van deze talen worden vertaald.

De aanvraag-URI is `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate` .

**Retour waarde:** Een teken reeks matrix die de taal codes bevat die door de service Translator worden ondersteund.

### <a name="response-class-status-200"></a>Response-klasse (status 200)
Een teken reeks matrix die de taal codes bevat die door de service Translator worden ondersteund.

tekenreeks

Type antwoord inhoud: Application/XML
 
### <a name="parameters"></a>Parameters

|Parameter|Waarde|Beschrijving|Parameter type|Gegevenstype|
|:--|:--|:--|:--|:--|
|AppID|gelaten|Vereist. Als de `Authorization` of `Ocp-Apim-Subscription-Key` -header wordt gebruikt, laat u het `appid` veld leeg. Als dat niet het geval is, voegt u een teken reeks met `"Bearer" + " " + "access_token"` .|query|tekenreeks|
|Autorisatie|gelaten  |Vereist als zowel het `appid` veld als de `Ocp-Apim-Subscription-Key` kop leeg blijven. Autorisatie token:  `"Bearer" + " " + "access_token"` .|koptekst|tekenreeks|
|Ocp-Apim-Subscription-Key|gelaten|Vereist als zowel het `appid` veld als de `Authorization` kop leeg blijven.|koptekst|tekenreeks|

### <a name="response-messages"></a>Antwoord berichten

|HTTP-statuscode|Reden|
|:--|:--|
|400    |Ongeldige aanvraag. Controleer de invoer parameters en het gedetailleerde fout bericht.|
|401    |Ongeldige referenties.|
|500    |Server fout. Als de fout zich blijft voordoen, laat het ons dan weten. Geef ons de geschatte datum & tijd van de aanvraag en met de aanvraag-ID die is opgenomen in de antwoord header `X-MS-Trans-Info` .|
|503|De service is tijdelijk niet beschikbaar. Probeer het opnieuw en laat het ons weten als de fout zich blijft voordoen.|

## <a name="get-getlanguagesforspeak"></a>/GetLanguagesForSpeak ophalen

### <a name="implementation-notes"></a>Opmerkingen bij de implementatie
Hiermee worden de beschik bare talen voor spraak synthese opgehaald.

De aanvraag-URI is `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak` .

**Retour waarde:** Een teken reeks matrix die de taal codes bevat die worden ondersteund voor spraak synthese door de Translator-service.

### <a name="response-class-status-200"></a>Response-klasse (status 200)
Een teken reeks matrix die de taal codes bevat die worden ondersteund voor spraak synthese door de Translator-service.

tekenreeks

Type antwoord inhoud: Application/XML

### <a name="parameters"></a>Parameters

|Parameter|Waarde|Beschrijving|Parameter type|Gegevenstype|
|:--|:--|:--|:--|:--|
|AppID|gelaten|Vereist. Als de `Authorization` of `Ocp-Apim-Subscription-Key` -header wordt gebruikt, laat u het `appid` veld leeg. Als dat niet het geval is, voegt u een teken reeks met `"Bearer" + " " + "access_token"` .|query|tekenreeks|
|Autorisatie|gelaten|Vereist als zowel het `appid` veld als de `Ocp-Apim-Subscription-Key` kop leeg blijven. Autorisatie token:  `"Bearer" + " " + "access_token"` .|koptekst|tekenreeks|
|Ocp-Apim-Subscription-Key|gelaten|Vereist als zowel het `appid` veld als de `Authorization` kop leeg blijven.|koptekst|tekenreeks|
 
### <a name="response-messages"></a>Antwoord berichten

|HTTP-statuscode|Reden|
|:--|:--|
|400|Ongeldige aanvraag. Controleer de invoer parameters en het gedetailleerde fout bericht.|
|401|Ongeldige referenties.|
|500    |Server fout. Als de fout zich blijft voordoen, laat het ons dan weten. Geef ons de geschatte datum & tijd van de aanvraag en met de aanvraag-ID die is opgenomen in de antwoord header `X-MS-Trans-Info` .|
|503    |De service is tijdelijk niet beschikbaar. Probeer het opnieuw en laat het ons weten als de fout zich blijft voordoen.|

## <a name="get-speak"></a>/Speak ophalen

### <a name="implementation-notes"></a>Opmerkingen bij de implementatie
Retourneert een WAV-of MP3-stroom van de door gegeven tekst, gesp roken in de gewenste taal.

De aanvraag-URI is `https://api.microsofttranslator.com/V2/Http.svc/Speak` .

**Retour waarde:** Een WAV-of MP3-stroom van de door gegeven tekst, gesp roken in de gewenste taal.

### <a name="response-class-status-200"></a>Response-klasse (status 200)

binair

Type antwoord inhoud: Application/XML

### <a name="parameters"></a>Parameters

|Parameter|Waarde|Beschrijving|Parameter type|Gegevenstype|
|:--|:--|:--|:--|:--|
|AppID|gelaten|Vereist. Als de `Authorization` of `Ocp-Apim-Subscription-Key` -header wordt gebruikt, laat u het `appid` veld leeg. Als dat niet het geval is, voegt u een teken reeks met `"Bearer" + " " + "access_token"` .|query|tekenreeks|
|tekst|gelaten   |Vereist. Een teken reeks die een of meer zinnen bevat die moeten worden gesp roken voor de stream, in de opgegeven taal. De tekst mag niet langer zijn dan 2.000 tekens.|query|tekenreeks|
|language|gelaten   |Vereist. Een teken reeks die de ondersteunde taal code vertegenwoordigt van de taal waarin de tekst moet worden gesp roken. De code moet een van de codes zijn die door de methode worden geretourneerd `GetLanguagesForSpeak` .|query|tekenreeks|
|indeling|gelaten|Optioneel. Een teken reeks waarmee de ID van het inhouds type wordt opgegeven. Momenteel  `audio/wav` en `audio/mp3` zijn beschikbaar. De standaardwaarde is `audio/wav`.|query|tekenreeks|
|opties|gelaten    |Optioneel. Een teken reeks die de eigenschappen van de gesynthesizerde spraak opgeeft:<ul><li>`MaxQuality` en `MinSize` Geef de kwaliteit van het audio signaal op. `MaxQuality` biedt de hoogste kwaliteit. `MinSize` biedt de kleinste bestands grootte. De standaard waarde is  `MinSize` .</li><li>`female` en `male` Geef het gewenste geslacht van de stem op. De standaardwaarde is `female`. Gebruik de verticale balk ( <code>\|</code> ) om meerdere opties te bevatten. Bijvoorbeeld `MaxQuality|Male`.</li></li></ul>  |query|tekenreeks|
|Autorisatie|gelaten|Vereist als zowel het `appid` veld als de `Ocp-Apim-Subscription-Key` kop leeg blijven. Autorisatie token:  `"Bearer" + " " + "access_token"` .|koptekst|tekenreeks|
|Ocp-Apim-Subscription-Key|gelaten  |Vereist als zowel het `appid` veld als de `Authorization` kop leeg blijven.|koptekst|tekenreeks|

### <a name="response-messages"></a>Antwoord berichten

|HTTP-statuscode|Reden|
|:--|:--|
|400    |Ongeldige aanvraag. Controleer de invoer parameters en het gedetailleerde fout bericht.|
|401    |Ongeldige referenties.|
|500    |Server fout. Als de fout zich blijft voordoen, laat het ons dan weten. Geef ons de geschatte datum & tijd van de aanvraag en met de aanvraag-ID die is opgenomen in de antwoord header `X-MS-Trans-Info` .|
|503    |De service is tijdelijk niet beschikbaar. Probeer het opnieuw en laat het ons weten als de fout zich blijft voordoen.|

## <a name="get-detect"></a>/Detect ophalen

### <a name="implementation-notes"></a>Opmerkingen bij de implementatie
Hiermee wordt de taal van een gedeelte van de tekst bepaald.

De aanvraag-URI is `https://api.microsofttranslator.com/V2/Http.svc/Detect` .

**Retour waarde:** Een teken reeks die een taal code van twee tekens bevat voor de tekst.

### <a name="response-class-status-200"></a>Response-klasse (status 200)

tekenreeks

Type antwoord inhoud: Application/XML

### <a name="parameters"></a>Parameters

|Parameter|Waarde|Beschrijving|Parameter type|Gegevenstype|
|:--|:--|:--|:--|:--|
|AppID|gelaten  |Vereist. Als de `Authorization` of `Ocp-Apim-Subscription-Key` -header wordt gebruikt, laat u het `appid` veld leeg. Als dat niet het geval is, voegt u een teken reeks met `"Bearer" + " " + "access_token"` .|query|tekenreeks|
|tekst|gelaten|Vereist. Een teken reeks die tekst bevat waarvan de taal moet worden geïdentificeerd. De tekst mag niet langer zijn dan 10.000 tekens.|query|  tekenreeks|
|Autorisatie|gelaten|Vereist als zowel het `appid` veld als de `Ocp-Apim-Subscription-Key` kop leeg blijven. Autorisatie token:  `"Bearer" + " " + "access_token"` .|koptekst|tekenreeks|
|Ocp-Apim-Subscription-Key  |gelaten    |Vereist als zowel het `appid` veld als de `Authorization` kop leeg blijven.|koptekst|tekenreeks|

### <a name="response-messages"></a>Antwoord berichten

|HTTP-statuscode|Reden|
|:--|:--|
|400|Ongeldige aanvraag. Controleer de invoer parameters en het gedetailleerde fout bericht.|
|401    |Ongeldige referenties.|
|500    |Server fout. Als de fout zich blijft voordoen, laat het ons dan weten. Geef ons de geschatte datum & tijd van de aanvraag en met de aanvraag-ID die is opgenomen in de antwoord header `X-MS-Trans-Info` .|
|503    |De service is tijdelijk niet beschikbaar. Probeer het opnieuw en laat het ons weten als de fout zich blijft voordoen.|


## <a name="post-detectarray"></a>/DetectArray plaatsen

### <a name="implementation-notes"></a>Opmerkingen bij de implementatie

Hiermee worden de talen in een matrix met teken reeksen geïdentificeerd. Detecteert onafhankelijk de taal van elk afzonderlijk matrix element en retourneert een resultaat voor elke rij van de matrix.

De aanvraag-URI is `https://api.microsofttranslator.com/V2/Http.svc/DetectArray` .

Dit is de indeling van de hoofd tekst van de aanvraag:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

De tekst mag niet langer zijn dan 10.000 tekens.

**Retour waarde:** Een teken reeks matrix die een taal code van twee tekens bevat voor elke rij in de invoer matrix.

Dit is de indeling van de antwoord tekst:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Response-klasse (status 200)
`DetectArray` is voltooid. Retourneert een teken reeks matrix die een taal code van twee tekens bevat voor elke rij van de invoer matrix.

tekenreeks

Type antwoord inhoud: Application/XML
 
### <a name="parameters"></a>Parameters

|Parameter|Waarde|Beschrijving|Parameter type|Gegevenstype|
|:--|:--|:--|:--|:--|
|AppID|gelaten|Vereist. Als de `Authorization` of `Ocp-Apim-Subscription-Key` -header wordt gebruikt, laat u het `appid` veld leeg. Als dat niet het geval is, voegt u een teken reeks met `"Bearer" + " " + "access_token"` .|query|tekenreeks|
|Autorisatie|gelaten|Vereist als zowel het `appid` veld als de `Ocp-Apim-Subscription-Key` kop leeg blijven.  Autorisatie token:  `"Bearer" + " " + "access_token"` .|koptekst|tekenreeks|
|Ocp-Apim-Subscription-Key|gelaten|Vereist als zowel het `appid` veld als de `Authorization` kop leeg blijven.|koptekst|tekenreeks|

### <a name="response-messages"></a>Antwoord berichten

|HTTP-statuscode|Reden|
|:--|:--|
|400    |Ongeldige aanvraag. Controleer de invoer parameters en het gedetailleerde fout bericht.|
|401    |Ongeldige referenties.|
|500    |Server fout. Als de fout zich blijft voordoen, laat het ons dan weten. Geef ons de geschatte datum & tijd van de aanvraag en met de aanvraag-ID die is opgenomen in de antwoord header `X-MS-Trans-Info` .|
|503    |De service is tijdelijk niet beschikbaar. Probeer het opnieuw en laat het ons weten als de fout zich blijft voordoen.|

## <a name="get-addtranslation"></a>/AddTranslation ophalen

### <a name="implementation-notes"></a>Opmerkingen bij de implementatie

> [!IMPORTANT]
> **Opmerking voor afschaffing:** Na 31 januari 2018 accepteert deze methode geen nieuwe zin-inzendingen. Er wordt een fout bericht weer gegeven. Raadpleeg de aankondiging over wijzigingen in het samen werkende Vertaal raamwerk (COLLABORATIVE).

Hiermee voegt u een vertaling toe aan het Vertaal geheugen.

De aanvraag-URI is `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation` .

### <a name="response-class-status-200"></a>Response-klasse (status 200)

tekenreeks

Type antwoord inhoud: toepassing: XML
 
### <a name="parameters"></a>Parameters

|Parameter|Waarde|Beschrijving|Parameter type|Gegevenstype   |
|:--|:--|:--|:--|:--|
|AppID|gelaten|Vereist. Als de `Authorization` of `Ocp-Apim-Subscription-Key` -header wordt gebruikt, laat u het `appid` veld leeg. Als dat niet het geval is, voegt u een teken reeks met `"Bearer" + " " + "access_token"` .|query|tekenreeks|
|originalText|gelaten|Vereist. Een teken reeks die de tekst bevat die moet worden vertaald. De maximale lengte van de teken reeks is 1.000 tekens.|query|tekenreeks|
|translatedText|gelaten |Vereist. Een teken reeks met tekst die in de doel taal is vertaald. De maximale lengte van de teken reeks is 2.000 tekens.|query|tekenreeks|
|from|gelaten   |Vereist. Een teken reeks die de taal code van de oorspronkelijke taal van de tekst aangeeft. Bijvoorbeeld, en voor Engels en de Duitse versie.|query|tekenreeks|
|tot|gelaten|Vereist. Een teken reeks die de taal code vertegenwoordigt van de taal waarin de tekst moet worden vertaald.|query|tekenreeks|
|rating|gelaten |Optioneel. Een geheel getal dat de kwaliteits classificatie voor de teken reeks vertegenwoordigt. De waarde ligt tussen-10 en 10. De standaardwaarde is 1.|query|geheel getal|
|Invoer|gelaten    |Optioneel. De indeling van de tekst die wordt vertaald. De ondersteunde indelingen zijn `text/plain` en `text/html` . HTML-elementen moeten goed opgemaakte, volledige elementen zijn.    |query|tekenreeks|
|category|gelaten|Optioneel. Een teken reeks die de categorie (het domein) van de vertaling bevat. De standaardwaarde is `general`.|query|tekenreeks|
|gebruiker|gelaten|Vereist. Een teken reeks die wordt gebruikt om de afzender van de inzending bij te houden.|query|tekenreeks|
|URI|gelaten|Optioneel. Een teken reeks die de inhouds locatie van de vertaling bevat.|query|tekenreeks|
|Autorisatie|gelaten|Vereist als zowel het `appid` veld als de `Ocp-Apim-Subscription-Key` kop leeg blijven.  Autorisatie token:  `"Bearer" + " " + "access_token"` .  |koptekst|tekenreeks|
|Ocp-Apim-Subscription-Key|gelaten|Vereist als zowel het `appid` veld als de `Authorization` kop leeg blijven.|koptekst|tekenreeks|

### <a name="response-messages"></a>Antwoord berichten

|HTTP-statuscode|Reden|
|:--|:--|
|400    |Ongeldige aanvraag. Controleer de invoer parameters en het gedetailleerde fout bericht.|
|401    |Ongeldige referenties.|
|410|`AddTranslation` wordt niet meer ondersteund.|
|500    |Server fout. Als de fout zich blijft voordoen, laat het ons dan weten. Geef ons de geschatte datum & tijd van de aanvraag en met de aanvraag-ID die is opgenomen in de antwoord header `X-MS-Trans-Info` .|
|503    |De service is tijdelijk niet beschikbaar. Probeer het opnieuw en laat het ons weten als de fout zich blijft voordoen.|

## <a name="post-addtranslationarray"></a>/AddTranslationArray plaatsen

### <a name="implementation-notes"></a>Opmerkingen bij de implementatie

> [!IMPORTANT]
> **Opmerking voor afschaffing:** Na 31 januari 2018 accepteert deze methode geen nieuwe zin-inzendingen. Er wordt een fout bericht weer gegeven. Raadpleeg de aankondiging over wijzigingen in het samen werkende Vertaal raamwerk (COLLABORATIVE).

Hiermee voegt u een matrix met vertalingen toe aan het Vertaal geheugen. Deze methode is een matrix versie van `AddTranslation` .

De aanvraag-URI is `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray` .

Dit is de indeling van de hoofd tekst van de aanvraag:

```
<AddtranslationsRequest>
  <AppId></AppId>
  <From>A string containing the language code of the source language</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
  </Options>
  <To>A string containing the language code of the target language</To>
  <Translations>
    <Translation xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
      <OriginalText>string-value</OriginalText>
      <Rating>int-value</Rating>
      <Sequence>int-value</Sequence>
      <TranslatedText>string-value</TranslatedText>
    </Translation>
  </Translations>
</AddtranslationsRequest>
```

Deze elementen bevinden zich in `AddtranslationsRequest` :

* `AppId`: Vereist. Als de `Authorization` of `Ocp-Apim-Subscription-Key` -header wordt gebruikt, laat u het `AppId` veld leeg. Als dat niet het geval is, voegt u een teken reeks met `"Bearer" + " " + "access_token"` .
* `From`: Vereist. Een teken reeks die de taal code van de bron taal bevat. Moet een van de talen zijn die door de `GetLanguagesForTranslate` methode worden geretourneerd.
* `To`: Vereist. Een teken reeks die de taal code van de doel taal bevat. Moet een van de talen zijn die door de `GetLanguagesForTranslate` methode worden geretourneerd.
* `Translations`: Vereist. Een matrix met vertalingen die moeten worden toegevoegd aan het Vertaal geheugen. Elke vertaling moet `OriginalText` , `TranslatedText` en bevatten `Rating` . De maximum grootte van elke `OriginalText` en `TranslatedText` is 1.000 tekens. Het totaal van alle- `OriginalText` en- `TranslatedText` elementen mag niet langer zijn dan 10.000 tekens. Het maximum aantal matrix elementen is 100.
* `Options`: Vereist. Een set opties, waaronder `Category` ,, `ContentType` en `Uri` `User` . `User` is vereist. `Category`, `ContentType` en `Uri` zijn optioneel. Opgegeven elementen moeten in alfabetische volg orde worden weer gegeven.

### <a name="response-class-status-200"></a>Response-klasse (status 200)
`AddTranslationArray` methode is voltooid. 

Na 31 januari 2018 worden inzendingen niet geaccepteerd. De service reageert met fout code 410.

tekenreeks

Type antwoord inhoud: Application/XML
 
### <a name="parameters"></a>Parameters

|Parameter|Waarde|Beschrijving|Parameter type|Gegevenstype|
|:--|:--|:--|:--|:--|
|Autorisatie|gelaten|Vereist als zowel het `appid` veld als de `Ocp-Apim-Subscription-Key` kop leeg blijven.  Autorisatie token:  `"Bearer" + " " + "access_token"` .|koptekst|tekenreeks|
|Ocp-Apim-Subscription-Key|gelaten|Vereist als zowel het `appid` veld als de `Authorization` kop leeg blijven.|koptekst|tekenreeks|

### <a name="response-messages"></a>Antwoord berichten

|HTTP-statuscode|Reden|
|:--|:--|
|400    |Ongeldige aanvraag. Controleer de invoer parameters en het gedetailleerde fout bericht.|
|401    |Ongeldige referenties.|
|410    |`AddTranslation` wordt niet meer ondersteund.|
|500    |Server fout. Als de fout zich blijft voordoen, laat het ons dan weten. Geef ons de geschatte datum & tijd van de aanvraag en met de aanvraag-ID die is opgenomen in de antwoord header `X-MS-Trans-Info` .|
|503|De service is tijdelijk niet beschikbaar. Probeer het opnieuw en laat het ons weten als de fout zich blijft voordoen.|

## <a name="get-breaksentences"></a>/BreakSentences ophalen

### <a name="implementation-notes"></a>Opmerkingen bij de implementatie
Hiermee wordt een sectie met tekst in zinnen gesplitst en wordt een matrix geretourneerd die de lengte van elke zin bevat.

De aanvraag-URI is `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences` .

**Retour waarde:** Een matrix van gehele getallen die de lengte van de zinnen aangeeft. De lengte van de matrix geeft het aantal zinnen aan. De waarden vertegenwoordigen de lengte van elke zin.

### <a name="response-class-status-200"></a>Response-klasse (status 200)
Een matrix van gehele getallen die de lengte van de zinnen aangeeft. De lengte van de matrix geeft het aantal zinnen aan. De waarden vertegenwoordigen de lengte van elke zin.

geheel getal

Type antwoord inhoud: Application/XML

### <a name="parameters"></a>Parameters

|Parameter|Waarde|Beschrijving|Parameter type|Gegevenstype|
|:--|:--|:--|:--|:--|
|AppID|gelaten  |Vereist. Als de `Authorization` of `Ocp-Apim-Subscription-Key` -header wordt gebruikt, laat u het `appid` veld leeg. Als dat niet het geval is, voegt u een teken reeks met `"Bearer" + " " + "access_token"` .|query| tekenreeks|
|tekst|gelaten   |Vereist. Een teken reeks die de tekst vertegenwoordigt die in zinnen moet worden gesplitst. De maximum grootte van de tekst is 10.000 tekens.|query|tekenreeks|
|language   |gelaten    |Vereist. Een teken reeks die de taal code van de invoer tekst aangeeft.|query|tekenreeks|
|Autorisatie|gelaten|Vereist als zowel het `appid` veld als de `Ocp-Apim-Subscription-Key` kop leeg blijven. Autorisatie token:  `"Bearer" + " " + "access_token"` .   |koptekst|tekenreeks|
|Ocp-Apim-Subscription-Key|gelaten|Vereist als zowel het `appid` veld als de `Authorization` kop leeg blijven.|koptekst|tekenreeks|

### <a name="response-messages"></a>Antwoord berichten

|HTTP-statuscode|Reden|
|:--|:--|
|400|Ongeldige aanvraag. Controleer de invoer parameters en het gedetailleerde fout bericht.|
|401|Ongeldige referenties.|
|500|Server fout. Als de fout zich blijft voordoen, laat het ons dan weten. Geef ons de geschatte datum & tijd van de aanvraag en met de aanvraag-ID die is opgenomen in de antwoord header `X-MS-Trans-Info` .|
|503|De service is tijdelijk niet beschikbaar. Probeer het opnieuw en laat het ons weten als de fout zich blijft voordoen.|

## <a name="post-gettranslations"></a>/GetTranslations plaatsen

### <a name="implementation-notes"></a>Opmerkingen bij de implementatie
Haalt een matrix van vertalingen voor een opgegeven taal paar op uit het archief en de MT-engine. `GetTranslations` verschilt van `Translate` in dat alle beschik bare vertalingen worden geretourneerd.

De aanvraag-URI is `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations` .

De hoofd tekst van de aanvraag bevat het optionele object, met de volgende `TranslationOptions` indeling:

```
<TranslateOptions xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
  <Category>string-value</Category>
  <ContentType>text/plain</ContentType>
  <ReservedFlags></ReservedFlags>
  <State>int-value</State>
  <Uri>string-value</Uri>
  <User>string-value</User>
</TranslateOptions>
```

Het `TranslateOptions` object bevat de waarden in de volgende lijst. Ze zijn allemaal optioneel en standaard ingesteld op de meest voorkomende instellingen. Opgegeven elementen moeten in alfabetische volg orde worden weer gegeven.

* `Category`: Een teken reeks die de categorie (het domein) van de vertaling bevat. De standaardwaarde is `general`.
* `ContentType`: De enige optie die wordt ondersteund en de standaard waarde is `text/plain` .
* `IncludeMultipleMTAlternatives`: Een Booleaanse vlag die aangeeft of er meer dan één alternatief moet worden geretourneerd door de MT-engine. Geldige waarden zijn `true` en `false` (hoofdletter gevoelig). De standaard waarde is `false` , waarmee slechts één alternatief wordt geretourneerd. Door de vlag in te stellen `true` , kunt u kunst matige alternatieven maken, volledig geïntegreerd met het samen werkende Vertaal raamwerk (Collaborative). De functie maakt het mogelijk om alternatieven te retour neren voor zinnen die geen vertalingen hebben in COLLABORATIVE door kunst matige alternatieven toe te voegen uit de *n*-beste lijst van de decoder.
    - Inhoudrestricties. De classificaties worden als volgt toegepast: 
         - De beste automatische vertaling heeft de classificatie 5.
       - De alternatieven van COLLABORATIVE weer spie gelen de instantie van de revisor. Ze variëren van-10 tot + 10.
       - De automatisch gegenereerde Vertaal alternatieven (*n*-best) hebben een classificatie van 0 en een overeenkomst met een afwijkings graad van 100.
    - Aantal alternatieven. Het aantal geretourneerde alternatieven kan net zo hoog zijn als de waarde die is opgegeven in `maxTranslations` , maar kan lager zijn.
    - Taal paren. Deze functionaliteit is niet beschikbaar voor vertalingen tussen vereenvoudigd Chinees en traditioneel Chinees, in beide richtingen. Het is beschikbaar voor alle andere taal paren die door micro soft Translator worden ondersteund.
* `State`: De gebruikers status die helpt bij het correleren van de aanvraag en het antwoord. Dezelfde inhoud wordt in het antwoord geretourneerd.
* `Uri`: Hiermee worden de resultaten van deze URI gefilterd. Als er geen waarde is ingesteld, is de standaard instelling `all` .
* `User`: Resultaten filteren op deze gebruiker. Als er geen waarde is ingesteld, is de standaard instelling `all` .

De aanvraag `Content-Type` moet zijn `text/xml` .

**Retour waarde:** Dit is de indeling van het antwoord:

```
<GetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <From>Two character language code</From>
  <State/>
  <Translations>
    <TranslationMatch>
      <Count>int-value</Count>
      <MatchDegree>int-value</MatchDegree>
      <MatchedOriginalText/>
      <Rating>int value</Rating>
      <TranslatedText>string-value</TranslatedText>
    </TranslationMatch>
  </Translations>
</GetTranslationsResponse>
```

Dit antwoord bevat een- `GetTranslationsResponse` element dat de volgende waarden bevat:

* `Translations`: Er is een matrix gevonden met overeenkomende items die zijn opgeslagen in `TranslationMatch` objecten (zoals beschreven in de volgende sectie). De vertalingen bevatten mogelijk lichte varianten van de oorspronkelijke tekst (fuzzy matching). De vertalingen worden gesorteerd: 100% komt overeen met de eerste, fuzzy treffers volgende.
* `From`: Als de methode geen taal opgeeft `From` , wordt deze waarde opgehaald uit de automatische taal detectie. Anders wordt de opgegeven taal weer gegeven `From` .
* `State`: De gebruikers status die helpt bij het correleren van de aanvraag en het antwoord. Bevat de waarde die in de `TranslateOptions` para meter is opgegeven.

Het `TranslationMatch` object bestaat uit de volgende waarden:

* `Error`: De fout code, als er een fout optreedt voor een specifieke invoer teken reeks. Anders is dit veld leeg.
* `MatchDegree`: Hiermee wordt aangegeven hoe nauw de invoer tekst overeenkomt met de oorspronkelijke tekst die in de Store is gevonden. Het systeem komt overeen met invoer zinnen in de Store, inclusief onjuiste overeenkomsten. De waarde retourneert bereiken van 0 tot 100, waarbij 0 geen gelijkenis is en 100 een exacte, hoofdletter gevoelige overeenkomst is.
* `MatchedOriginalText`: Oorspronkelijke tekst die overeenkomt met dit resultaat. Deze waarde wordt alleen geretourneerd als de overeenkomende oorspronkelijke tekst verschilt van de invoer tekst. Het wordt gebruikt om de bron tekst van een overeenkomst met fuzzy te retour neren. Deze waarde wordt niet geretourneerd voor micro soft Translator-resultaten.
* `Rating`: Geeft de instantie van de persoon die de kwaliteits beslissing doet. De resultaten van computer vertalingen hebben de classificatie 5. De anoniem gestelde vertalingen hebben over het algemeen een classificatie van 1 tot en met 4. Voor gezaghebbende vertalingen geldt doorgaans een classificatie van 6 tot en met 10.
* `Count`: Het aantal keren dat deze vertaling met deze classificatie is geselecteerd. De waarde is 0 voor de automatisch vertaalde reactie.
* `TranslatedText`: De vertaalde tekst.

### <a name="response-class-status-200"></a>Response-klasse (status 200)
Een `GetTranslationsResponse` object in de indeling die eerder is beschreven.

tekenreeks

Type antwoord inhoud: Application/XML
 
### <a name="parameters"></a>Parameters

|Parameter|Waarde|Beschrijving|Parameter type|Gegevenstype|
|:--|:--|:--|:--|:--|
|AppID|gelaten|Vereist. Als de `Authorization` of `Ocp-Apim-Subscription-Key` -header wordt gebruikt, laat u het `appid` veld leeg. Als dat niet het geval is, voegt u een teken reeks met `"Bearer" + " " + "access_token"` .|query|tekenreeks|
|tekst|gelaten|Vereist. Een teken reeks die de tekst vertegenwoordigt die moet worden vertaald. De maximum grootte van de tekst is 10.000 tekens.|query|tekenreeks|
|from|gelaten|Vereist. Een teken reeks die de taal code vertegenwoordigt van de tekst die wordt vertaald.|query|tekenreeks|
|tot |gelaten    |Vereist. Een teken reeks die de taal code vertegenwoordigt van de taal waarin de tekst moet worden vertaald.|query|tekenreeks|
|maxTranslations|gelaten|Vereist. Een geheel getal dat het maximum aantal vertalingen vertegenwoordigt dat moet worden geretourneerd.|query|geheel getal|
|Autorisatie| gelaten|Vereist als zowel het `appid` veld als de `Ocp-Apim-Subscription-Key` kop leeg blijven. Autorisatie token:  `"Bearer" + " " + "access_token"` .|tekenreeks|  koptekst|
|Ocp-Apim-Subscription-Key|gelaten  |Vereist als zowel het `appid` veld als de `Authorization` kop leeg blijven.|koptekst|tekenreeks|

### <a name="response-messages"></a>Antwoord berichten

|HTTP-statuscode|Reden|
|:--|:--|
|400    |Ongeldige aanvraag. Controleer de invoer parameters en het gedetailleerde fout bericht.|
|401    |Ongeldige referenties.|
|500    |Server fout. Als de fout zich blijft voordoen, laat het ons dan weten. Geef ons de geschatte datum & tijd van de aanvraag en met de aanvraag-ID die is opgenomen in de antwoord header `X-MS-Trans-Info` .|
|503|De service is tijdelijk niet beschikbaar. Probeer het opnieuw en laat het ons weten als de fout zich blijft voordoen.|

## <a name="post-gettranslationsarray"></a>/GetTranslationsArray plaatsen

### <a name="implementation-notes"></a>Opmerkingen bij de implementatie
Hiermee worden meerdere vertalingen kandidaten voor meerdere bron teksten opgehaald.

De aanvraag-URI is `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray` .

Dit is de indeling van de hoofd tekst van de aanvraag:

```
<GetTranslationsArrayRequest>
  <AppId></AppId>
  <From>language-code</From>
  <MaxTranslations>int-value</MaxTranslations>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"/>
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</GetTranslationsArrayRequest>
```

`GetTranslationsArrayRequest` bevat de volgende elementen:

* `AppId`: Vereist. `Authorization`Laat het veld leeg als de header wordt gebruikt `AppId` . Als dat niet het geval is, voegt u een teken reeks met `"Bearer" + " " + "access_token"` .
* `From`: Vereist. Een teken reeks die de taal code vertegenwoordigt van de tekst die wordt vertaald.
* `MaxTranslations`: Vereist. Een geheel getal dat het maximum aantal vertalingen vertegenwoordigt dat moet worden geretourneerd.
* `Options`Beschrijving. Een `Options` object dat de volgende waarden bevat. Ze zijn allemaal optioneel en standaard ingesteld op de meest voorkomende instellingen. Opgegeven elementen moeten in alfabetische volg orde worden weer gegeven.
    - `Category`: Een teken reeks die de categorie (het domein) van de vertaling bevat. De standaardwaarde is `general`.
    - `ContentType`: De enige optie die wordt ondersteund en de standaard waarde is `text/plain` .
    - `IncludeMultipleMTAlternatives`: Een Booleaanse vlag die aangeeft of er meer dan één alternatief moet worden geretourneerd door de MT-engine. Geldige waarden zijn `true` en `false` (hoofdletter gevoelig). De standaard waarde is `false` , waarmee slechts één alternatief wordt geretourneerd. Het instellen van de vlag voor het `true` genereren van kunst matige alternatieven in de vertaling is volledig geïntegreerd met het Framework voor samen werking van het samenwerkings verband (Collaborative). De functie maakt het mogelijk om alternatieven te retour neren voor zinnen die geen alternatieven hebben in COLLABORATIVE door kunst matige alternatieven toe te voegen uit de *n*-beste lijst van de decoder.
        - Beoordelingen de classificaties worden als volgt toegepast:
          - De beste automatische vertaling heeft de classificatie 5.
          - De alternatieven van COLLABORATIVE weer spie gelen de instantie van de revisor. Ze variëren van-10 tot + 10.
          - De automatisch gegenereerde Vertaal alternatieven (*n*-best) hebben een classificatie van 0 en een overeenkomst met een afwijkings graad van 100.
        - Aantal alternatieven. Het aantal geretourneerde alternatieven kan net zo hoog zijn als de waarde die is opgegeven in `maxTranslations` , maar kan lager zijn.
        - Taal paren. Deze functionaliteit is niet beschikbaar voor vertalingen tussen vereenvoudigd Chinees en traditioneel Chinees, in beide richtingen. Het is beschikbaar voor alle andere taal paren die door micro soft Translator worden ondersteund.
* `State`: De gebruikers status die helpt bij het correleren van de aanvraag en het antwoord. Dezelfde inhoud wordt in het antwoord geretourneerd.
* `Uri`: Hiermee worden de resultaten van deze URI gefilterd. Als er geen waarde is ingesteld, is de standaard instelling `all` .
* `User`: Resultaten filteren op deze gebruiker. Als er geen waarde is ingesteld, is de standaard instelling `all` .
* `Texts`: Vereist. Een matrix die de tekst voor vertaling bevat. Alle teken reeksen moeten zich in dezelfde taal bestaan. Het totaal van de tekst die moet worden vertaald, mag niet langer zijn dan 10.000 tekens. Het maximum aantal matrix elementen is 10.
* `To`: Vereist. Een teken reeks die de taal code vertegenwoordigt van de taal waarin de tekst moet worden vertaald.

U kunt optionele elementen weglaten. Elementen die directe onderliggende items zijn van, `GetTranslationsArrayRequest` moeten in alfabetische volg orde worden weer gegeven.

De aanvraag `Content-Type` moet zijn `text/xml` .

**Retour waarde:** Dit is de indeling van het antwoord:

```
<ArrayOfGetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <GetTranslationsResponse>
    <From>language-code</From>
    <State/>
    <Translations>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText>string-value</MatchedOriginalText>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText/>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
    </Translations>
  </GetTranslationsResponse>
</ArrayOfGetTranslationsResponse>
```

Elk `GetTranslationsResponse` element bevat deze waarden:

* `Translations`: Er is een matrix gevonden met overeenkomende items die zijn opgeslagen in `TranslationMatch` objecten (zoals beschreven in de volgende sectie). De vertalingen bevatten mogelijk lichte varianten van de oorspronkelijke tekst (fuzzy matching). De vertalingen worden gesorteerd: 100% komt overeen met de eerste, fuzzy treffers volgende.
* `From`: Als de methode geen taal opgeeft `From` , wordt deze waarde opgehaald uit de automatische taal detectie. Anders wordt de opgegeven taal weer gegeven `From` .
* `State`: De gebruikers status die helpt bij het correleren van de aanvraag en het antwoord. Bevat de waarde die in de `TranslateOptions` para meter is opgegeven.

Het `TranslationMatch` object bevat de volgende waarden:
* `Error`: De fout code, als er een fout optreedt voor een specifieke invoer teken reeks. Anders is dit veld leeg.
* `MatchDegree`: Hiermee wordt aangegeven hoe nauw de invoer tekst overeenkomt met de oorspronkelijke tekst die in de Store is gevonden. Het systeem komt overeen met invoer zinnen in de Store, inclusief onjuiste overeenkomsten. De waarde retourneert bereiken van 0 tot 100, waarbij 0 geen gelijkenis is en 100 een exacte, hoofdletter gevoelige overeenkomst is.
* `MatchedOriginalText`: Oorspronkelijke tekst die overeenkomt met dit resultaat. Deze waarde wordt alleen geretourneerd als de overeenkomende oorspronkelijke tekst verschilt van de invoer tekst. Het wordt gebruikt om de bron tekst van een overeenkomst met fuzzy te retour neren. Deze waarde wordt niet geretourneerd voor micro soft Translator-resultaten.
* `Rating`: Geeft de instantie van de persoon die de kwaliteits beslissing doet. De resultaten van computer vertalingen hebben de classificatie 5. De anoniem gestelde vertalingen hebben over het algemeen een classificatie van 1 tot en met 4. Voor bindend gegeven vertalingen hebben over het algemeen een classificatie van 6 tot en met 10.
* `Count`: Het aantal keren dat deze vertaling met deze classificatie is geselecteerd. De waarde is 0 voor de automatisch vertaalde reactie.
* `TranslatedText`: De vertaalde tekst.


### <a name="response-class-status-200"></a>Response-klasse (status 200)

tekenreeks

Type antwoord inhoud: Application/XML
 
### <a name="parameters"></a>Parameters

|Parameter|Waarde|Beschrijving|Parameter type|Gegevenstype|
|:--|:--|:--|:--|:--|
|Autorisatie  |gelaten    |Vereist als zowel het `appid` veld als de `Ocp-Apim-Subscription-Key` kop leeg blijven.  Autorisatie token:  `"Bearer" + " " + "access_token"` .|koptekst|tekenreeks|
|Ocp-Apim-Subscription-Key|gelaten  |Vereist als zowel het `appid` veld als de `Authorization` kop leeg blijven.|koptekst|tekenreeks|

### <a name="response-messages"></a>Antwoord berichten

|HTTP-statuscode|Reden|
|:--|:--|
|400    |Ongeldige aanvraag. Controleer de invoer parameters en het gedetailleerde fout bericht.|
|401    |Ongeldige referenties.|
|500    |Server fout. Als de fout zich blijft voordoen, laat het ons dan weten. Geef ons de geschatte datum & tijd van de aanvraag en met de aanvraag-ID die is opgenomen in de antwoord header  `X-MS-Trans-Info` .|
|503    |De service is tijdelijk niet beschikbaar. Probeer het opnieuw en laat het ons weten als de fout zich blijft voordoen.|

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Migreren naar Translator v3](../migrate-to-v3.md)
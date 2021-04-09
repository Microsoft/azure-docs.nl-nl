---
title: Vertaal methode voor Translator
titleSuffix: Azure Cognitive Services
description: Meer informatie over de para meters, kopteksten en hoofd berichten voor de Vertaal methode van Azure Cognitive Services Translator om tekst te vertalen.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 08/06/2020
ms.author: lajanuar
ms.openlocfilehash: d46fef0159b983f2685be40e2a0ab5471b96883b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895438"
---
# <a name="translator-30-translate"></a>Translator 3,0: vertalen

Vertaalt tekst.

## <a name="request-url"></a>Aanvraag-URL

Een `POST` aanvraag verzenden naar:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Aanvraag parameters

Aanvraag parameters die zijn door gegeven voor de query reeks zijn:

### <a name="required-parameters"></a>Vereiste para meters

<table width="100%">
  <th width="20%">Queryparameter</th>
  <th>Beschrijving</th>
  <tr>
    <td>api-versie</td>
    <td><em>Vereiste para meter</em>.<br/>De versie van de API die door de client is aangevraagd. Waarde moet zijn <code>3.0</code> .</td>
  </tr>
  <tr>
    <td>tot</td>
    <td><em>Vereiste para meter</em>.<br/>Hiermee geeft u de taal van de uitvoer tekst op. De doel taal moet een van de <a href="./v3-0-languages.md">ondersteunde talen</a> zijn die in het <code>translation</code> bereik zijn opgenomen. Gebruik bijvoorbeeld <code>to=de</code> om naar Duits te vertalen.<br/>Het is mogelijk om naar meerdere talen tegelijk te vertalen door de para meter in de query teken reeks te herhalen. Gebruik bijvoorbeeld <code>to=de&to=it</code> om te vertalen naar Duits en Italiaans.</td>
  </tr>
</table>

### <a name="optional-parameters"></a>Optionele parameters

<table width="100%">
  <th width="20%">Queryparameter</th>
  <th>Beschrijving</th>
  <tr>
    <td>from</td>
    <td><em>Optionele para meter</em>.<br/>Geeft de taal van de invoer tekst aan. Ga na welke talen kunnen worden vertaald door <a href="./v3-0-languages.md">ondersteunde talen</a> te zoeken met behulp van de <code>translation</code> Scope. Als de <code>from</code> para meter niet is opgegeven, wordt automatische taal detectie toegepast om de bron taal te bepalen. <br/><br/>U moet de <code>from</code> para meter gebruiken in plaats van automatische detectie wanneer u de functie <a href="/azure/cognitive-services/translator/dynamic-dictionary">dynamische woorden lijst</a> gebruikt.</td>
  </tr>  
  <tr>
    <td>textType</td>
    <td><em>Optionele para meter</em>.<br/>Hiermee wordt bepaald of de tekst die wordt vertaald tekst zonder opmaak of HTML-tekst is. Een HTML-bestand moet een goed gevormd, volledig element zijn. Mogelijke waarden zijn: <code>plain</code> (standaard) of <code>html</code>.</td>
  </tr>
  <tr>
    <td>category</td>
    <td><em>Optionele para meter</em>.<br/>Een teken reeks waarmee de categorie (domein) van de vertaling wordt opgegeven. Deze para meter wordt gebruikt voor het ophalen van vertalingen van een aangepast systeem dat is gebouwd met <a href="../customization.md">aangepaste vertaler</a>. Voeg de categorie-ID uit de <a href="/azure/cognitive-services/translator/custom-translator/how-to-create-project#view-project-details">Project gegevens</a> van uw aangepaste vertaler toe aan deze para meter om uw geïmplementeerde aangepaste systeem te gebruiken. De standaard waarde is: <code>general</code> .</td>
  </tr>
  <tr>
    <td>profanityAction</td>
    <td><em>Optionele para meter</em>.<br/>Hiermee geeft u op hoe scheld woorden moeten worden behandeld in vertalingen. Mogelijke waarden zijn: <code>NoAction</code> (standaard) <code>Marked</code> of <code>Deleted</code> . Zie voor het <a href="#handle-profanity">afhandelen</a>van scheld woorden.</td>
  </tr>
  <tr>
    <td>profanityMarker</td>
    <td><em>Optionele para meter</em>.<br/>Hiermee geeft u op hoe scheld woorden moeten worden gemarkeerd in vertalingen. Mogelijke waarden zijn: <code>Asterisk</code> (standaard) of <code>Tag</code> . Zie voor het <a href="#handle-profanity">afhandelen</a>van scheld woorden.</td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td><em>Optionele para meter</em>.<br/>Hiermee wordt aangegeven of uitlijnings projectie van de bron tekst naar de vertaalde tekst moet worden meegenomen. Mogelijke waarden zijn: <code>true</code> of <code>false</code> (standaard). </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td><em>Optionele para meter</em>.<br/>Hiermee wordt aangegeven of de rand grenzen voor de invoer tekst en de vertaalde tekst moeten worden opgegeven. Mogelijke waarden zijn: <code>true</code> of <code>false</code> (standaard).</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td><em>Optionele para meter</em>.<br/>Hiermee geeft u een terugval taal op als de taal van de invoer tekst niet kan worden geïdentificeerd. Automatische taal detectie wordt toegepast wanneer de <code>from</code> para meter wordt wegge laten. Als de detectie mislukt, <code>suggestedFrom</code> wordt ervan uitgegaan dat de taal wordt gebruikt.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td><em>Optionele para meter</em>.<br/>Geeft het script van de invoer tekst aan.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td><em>Optionele para meter</em>.<br/>Hiermee geeft u het script van de vertaalde tekst op.</td>
  </tr>
  <tr>
    <td>allowFallback</td>
    <td><em>Optionele para meter</em>.<br/>Hiermee geeft u op dat de service een algemeen systeem mag terugvallen wanneer er geen aangepast systeem bestaat. Mogelijke waarden zijn: <code>true</code> (standaard) of <code>false</code> .<br/><br/><code>allowFallback=false</code> Hiermee geeft u op dat de vertaling alleen systeem getraind moet gebruiken voor het <code>category</code> opgegeven door de aanvraag. Als voor een vertaling voor taal X naar taal Y een koppeling is vereist via een draai taal E, moeten alle systemen in de keten (X->E en E->Y) aangepast zijn en dezelfde categorie hebben. Als er geen systeem met de specifieke categorie wordt gevonden, wordt door de aanvraag een 400-status code geretourneerd. <code>allowFallback=true</code> Hiermee geeft u op dat de service een algemeen systeem mag terugvallen wanneer er geen aangepast systeem bestaat.
</td>
  </tr>
</table> 

Aanvraag headers zijn onder andere:

<table width="100%">
  <th width="20%">Kopteksten</th>
  <th>Beschrijving</th>
  <tr>
    <td>Verificatieheader(s)</td>
    <td><em>Vereiste aanvraagheader</em>.<br/>Zie <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">Beschikbare opties voor verificatie</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td><em>Vereiste aanvraagheader</em>.<br/>Hiermee geeft u het inhoudstype van de payload op.<br/> De geaccepteerde waarde is <code>application/json; charset=UTF-8</code>.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td><em>Vereiste aanvraagheader</em>.<br/>De lengte van de aanvraagtekst.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td><em>Optioneel</em>.<br/>Een door de client gegenereerde GUID om de aanvraag op unieke wijze te identificeren. U kunt deze header weglaten als u de tracerings-id in de queryreeks opneemt middels een queryparameter met de naam <code>ClientTraceId</code>.</td>
  </tr>
</table> 

## <a name="request-body"></a>Aanvraagbody

De hoofd tekst van de aanvraag is een JSON-matrix. Elk matrix element is een JSON-object met een teken reeks eigenschap met `Text` de naam, die de teken reeks vertegenwoordigt die moet worden vertaald.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

De volgende beperkingen zijn van toepassing:

* De matrix kan Maxi maal 100 elementen bevatten.
* De volledige tekst die in de aanvraag is opgenomen, mag niet langer zijn dan 10.000 tekens, inclusief spaties.

## <a name="response-body"></a>Hoofdtekst van de reactie

Een geslaagde reactie is een JSON-matrix met één resultaat voor elke teken reeks in de invoer matrix. Een resultaat object bevat de volgende eigenschappen:

  * `detectedLanguage`: Een object dat de gedetecteerde taal beschrijft via de volgende eigenschappen:

      * `language`: Een teken reeks die de code van de gedetecteerde taal weergeeft.

      * `score`: Een float-waarde waarmee het vertrouwen in het resultaat wordt aangegeven. De Score ligt tussen nul en een en een lage score geeft een lage betrouw baarheid aan.

    De `detectedLanguage` eigenschap is alleen aanwezig in het resultaat object als automatische taal detectie is aangevraagd.

  * `translations`: Een matrix met Vertaal resultaten. De grootte van de matrix komt overeen met het aantal doel talen dat is opgegeven met behulp van de `to` query parameter. Elk element in de matrix bevat:

    * `to`: Een teken reeks die de taal code van de doel taal aangeeft.

    * `text`: Een teken reeks met de vertaalde tekst.

    * `transliteration`: Een object met de vertaalde tekst in het script dat is opgegeven met de `toScript` para meter.

      * `script`: Een teken reeks waarmee het doel script wordt opgegeven.   

      * `text`: Een teken reeks die de vertaalde tekst in het doel script geeft.

    Het `transliteration` object wordt niet opgenomen als het vele niet wordt uitgevoerd.

    * `alignment`: Een object met een enkele teken reeks eigenschap met de naam `proj` , waarmee invoer tekst wordt toegewezen aan de vertaalde tekst. De uitlijnings informatie wordt alleen gegeven wanneer de aanvraag parameter `includeAlignment` is `true` . Uitlijning wordt geretourneerd als een teken reeks waarde van de volgende indeling: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]` .  De dubbele punt scheidt begin-en eind index, het streepje scheidt de talen en de spatie scheidt de woorden. Eén woord kan worden uitgelijnd met nul, één of meerdere woorden in de andere taal en de uitgelijnde woorden kunnen niet-aaneengesloten zijn. Als er geen uitlijnings gegevens beschikbaar zijn, is het element uitlijning leeg. Zie [informatie over uitlijning verkrijgen](#obtain-alignment-information) voor een voor beeld en beperkingen.

    * `sentLen`: Een object dat zin retourneert in de invoer-en uitvoer teksten.

      * `srcSentLen`: Een matrix met gehele getallen die de lengte van de zinnen in de invoer tekst aangeeft. De lengte van de matrix is het aantal zinnen en de waarden zijn de lengte van elke zin.

      * `transSentLen`: Een matrix met gehele getallen die de lengte van de zinnen in de vertaalde tekst aangeeft. De lengte van de matrix is het aantal zinnen en de waarden zijn de lengte van elke zin.

    Grenzen van zinnen worden alleen opgenomen wanneer de aanvraag parameter `includeSentenceLength` is `true` .

  * `sourceText`: Een object met een enkele teken reeks eigenschap met `text` de naam, die de invoer tekst in het standaard script van de bron taal levert. `sourceText` de eigenschap is alleen beschikbaar wanneer de invoer wordt uitgedrukt in een script dat niet het gebruikelijke script is voor de taal. Als de invoer bijvoorbeeld Arabisch is geschreven in Latijns schrift, `sourceText.text` zou de Arabische tekst in het Arabische script worden omgezet.

Voor beeld van JSON-antwoorden vindt u in de sectie [voor beelden](#examples) .

## <a name="response-headers"></a>Antwoordheaders

<table width="100%">
  <th width="20%">Kopteksten</th>
  <th>Beschrijving</th>
    <tr>
    <td>X-aanvraag-}</td>
    <td>De waarde die door de service is gegenereerd om de aanvraag te identificeren. Dit wordt gebruikt voor het oplossen van problemen.</td>
  </tr>
  <tr>
    <td>X-MT-systeem</td>
    <td>Hiermee geeft u het systeem type op dat voor de vertaling is gebruikt voor elke ' naar '-taal die is aangevraagd voor vertaling. De waarde is een door komma's gescheiden lijst met teken reeksen. Elke teken reeks geeft een type aan:<br/><ul><li>Aangepaste aanvraag bevat een aangepast systeem en ten minste één aangepast systeem is tijdens de omzetting gebruikt.</li><li>Team-alle andere aanvragen</li></td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Antwoord status codes

Hier volgen de mogelijke HTTP-status codes die een aanvraag retourneert. 

<table width="100%">
  <th width="20%">Statuscode</th>
  <th>Description</th>
  <tr>
    <td>200</td>
    <td>Voltooid.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Een van de queryparameters ontbreekt of is ongeldig. Corrigeer de aanvraag parameters voordat u het opnieuw probeert.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>De aanvraag kan niet worden geverifieerd. Controleer of de referenties zijn opgegeven en geldig zijn.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>De aanvraag is niet geautoriseerd. Controleer het fout bericht Details. Dit betekent vaak dat alle gratis vertalingen van een proef abonnement zijn gebruikt.</td>
  </tr>
  <tr>
    <td>408</td>
    <td>De aanvraag kan niet worden voltooid omdat er een resource ontbreekt. Controleer het fout bericht Details. Wanneer u een aangepast gebruikt <code>category</code> , geeft dit vaak aan dat het aangepaste Vertaal systeem nog niet beschikbaar is voor het leveren van aanvragen. De aanvraag moet opnieuw worden uitgevoerd na een wacht tijd (bijvoorbeeld 1 minuut).</td>
  </tr>
  <tr>
    <td>429</td>
    <td>De server heeft de aanvraag geweigerd omdat de aanvraag limieten voor de client is overschreden.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Er is een onverwachte fout opgetreden. Als de fout zich blijft voordoen, meldt u deze met: datum en tijd van de fout, aanvraag-id van antwoord header <code>X-RequestId</code> en client-id uit aanvraag header <code>X-ClientTraceId</code> .</td>
  </tr>
  <tr>
    <td>503</td>
    <td>De server is tijdelijk niet beschikbaar. Voer de aanvraag opnieuw uit. Als de fout zich blijft voordoen, meldt u deze met: datum en tijd van de fout, aanvraag-id van antwoord header <code>X-RequestId</code> en client-id uit aanvraag header <code>X-ClientTraceId</code> .</td>
  </tr>
</table> 

Als er een fout optreedt, wordt door de aanvraag ook een JSON-fout bericht geretourneerd. De fout code is een getal van 6 cijfers, waarbij de HTTP-status code van 3 cijfers wordt gevolgd door een getal van drie cijfers om de fout verder te categoriseren. Algemene fout codes vindt u op de [referentie pagina van v3 Translator](./v3-0-reference.md#errors). 

## <a name="examples"></a>Voorbeelden

### <a name="translate-a-single-input"></a>Eén invoer vertalen

In dit voor beeld ziet u hoe u een enkele zin van het Engels naar vereenvoudigd Chinees omzet.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

De antwoord tekst is:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

De `translations` matrix bevat één element, dat de vertaling levert van het enkele tekst gedeelte in de invoer.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Eén invoer vertalen met automatische taal detectie

In dit voor beeld ziet u hoe u een enkele zin van het Engels naar vereenvoudigd Chinees omzet. In de aanvraag wordt de invoer taal niet opgegeven. In plaats daarvan wordt de automatische detectie van de bron taal gebruikt.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

De antwoord tekst is:

```
[
    {
        "detectedLanguage": {"language": "en", "score": 1.0},
        "translations":[
            {"text": "你好, 你叫什么名字？", "to": "zh-Hans"}
        ]
    }
]
```
Het antwoord is vergelijkbaar met het antwoord van het vorige voor beeld. Omdat automatische taal detectie is aangevraagd, bevat het antwoord ook informatie over de taal die is gedetecteerd voor de invoer tekst. De automatische taal detectie werkt beter met meer invoer tekst.

### <a name="translate-with-transliteration"></a>Vertalen met vele

We gaan het vorige voor beeld uitbreiden door vele toe te voegen. De volgende aanvraag vraagt om een Chinese vertaling geschreven in Latijns schrift.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

De antwoord tekst is:

```
[
    {
        "detectedLanguage":{"language":"en","score":1.0},
        "translations":[
            {
                "text":"你好, 你叫什么名字？",
                "transliteration":{"script":"Latn", "text":"nǐ hǎo , nǐ jiào shén me míng zì ？"},
                "to":"zh-Hans"
            }
        ]
    }
]
```

Het Vertaal resultaat bevat nu een `transliteration` eigenschap, waarmee de vertaalde tekst wordt omgezet met behulp van Latijnse tekens.

### <a name="translate-multiple-pieces-of-text"></a>Meerdere tekst gedeelten vertalen

Als u meerdere teken reeksen in één keer verdeelt, hoeft u alleen een matrix met teken reeksen op te geven in de hoofd tekst van de aanvraag.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

Het antwoord bevat de vertaling van alle stukjes tekst in dezelfde volg orde als in de aanvraag.
De antwoord tekst is:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    },            
    {
        "translations":[
            {"text":"我很好，谢谢你。","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-to-multiple-languages"></a>Vertalen naar meerdere talen

In dit voor beeld ziet u hoe u dezelfde invoer in verschillende talen in één aanvraag kunt vertalen.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

De antwoord tekst is:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"},
            {"text":"Hallo, was ist dein Name?","to":"de"}
        ]
    }
]
```

### <a name="handle-profanity"></a>Grove woorden afhandelen

Normaal gesp roken behoudt de Translator-service inhoud die aanwezig is in de bron in de vertaling. De mate van scheld woorden en de context die het grove woord van de Culture Cultuur afwijkt, en als gevolg hiervan kan de mate van groveheid in de doel taal worden versterkt of gereduceerd.

Als u wilt voor komen dat de vertaling in het gemoeds oog komt, ongeacht de aanwezigheid van scheld woorden in de bron tekst, kunt u de filter optie voor scheld woorden gebruiken. Met deze optie kunt u kiezen of u ongepaste woorden wilt weer geven, of u woorden met de juiste tags wilt markeren (zodat u de optie hebt om uw eigen verwerking toe te voegen) of u wilt geen actie ondernemen. De geaccepteerde waarden van `ProfanityAction` zijn `Deleted` , `Marked` en `NoAction` (standaard).

<table width="100%">
  <th width="20%">ProfanityAction</th>
  <th>Bewerking</th>
  <tr>
    <td><code>NoAction</code></td>
    <td>Dit is de standaardinstelling. Scheld is van bron naar doel.<br/><br/>
    <strong>Voorbeeld bron (Japans)</strong>: 彼はジャッカスです Marketplace.<br/>
    <strong>Voor beeld van vertaling (Engels)</strong>: He is een Jackass.
    </td>
  </tr>
  <tr>
    <td><code>Deleted</code></td>
    <td>Ongepaste woorden worden verwijderd uit de uitvoer zonder vervanging.<br/><br/>
    <strong>Voorbeeld bron (Japans)</strong>: 彼はジャッカスです Marketplace.<br/>
    <strong>Voor beeld van vertaling (Engels)</strong>: hij is een.
    </td>
  </tr>
  <tr>
    <td><code>Marked</code></td>
    <td>Ongepaste woorden worden vervangen door een markering in de uitvoer. De markering is afhankelijk van de <code>ProfanityMarker</code> para meter.<br/><br/>
Voor <code>ProfanityMarker=Asterisk</code> zijn ongepaste woorden vervangen door <code>***</code> :<br/>
    <strong>Voorbeeld bron (Japans)</strong>: 彼はジャッカスです Marketplace.<br/>
    <strong>Voor beeld van vertaling (Engels)</strong>: hij is een \* \* \* .<br/><br/>
Voor <code>ProfanityMarker=Tag</code> zijn ongepaste woorden omgeven door XML-tags &lt; , Gods taal &gt; en &lt; /profanity &gt; :<br/>
    <strong>Voorbeeld bron (Japans)</strong>: 彼はジャッカスです Marketplace.<br/>
    <strong>Voor beeld van een vertaling (Engels)</strong>: hij is een &lt; Gods- &gt; Jackass &lt; /profanity &gt; .
  </tr>
</table> 

Bijvoorbeeld:

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```
Dit geeft als resultaat:

```
[
    {
        "translations":[
            {"text":"Das ist eine *** gute Idee.","to":"de"}
        ]
    }
]
```

Vergelijken met:

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```

Die laatste aanvraag retourneert:

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Inhoud vertalen met markeringen en bepalen wat er wordt vertaald

Het is gebruikelijk om inhoud te vertalen die aantekeningen bevat zoals inhoud van een HTML-pagina of inhoud van een XML-document. Neem een query parameter `textType=html` op voor het vertalen van inhoud met tags. Daarnaast is het soms handig specifieke inhoud uit te sluiten van de vertaling. U kunt het kenmerk gebruiken `class=notranslate` om inhoud op te geven die in de oorspronkelijke taal moet blijven. In het volgende voor beeld wordt de inhoud in het eerste `div` element niet vertaald, terwijl de inhoud van het tweede `div` element wordt vertaald.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Hier volgt een voor beeld van een aanvraag om te illustreren.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

Het antwoord is:

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="obtain-alignment-information"></a>Informatie over uitlijning ophalen

Uitlijning wordt geretourneerd als een teken reeks waarde van de volgende notatie voor elk woord van de bron. De gegevens voor elk woord worden gescheiden door een spatie, met inbegrip van talen die niet met een spatie zijn gescheiden (scripts), zoals Chinees:

[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

Voor beeld van een uitlijnings teken reeks: "0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21".

Met andere woorden, de dubbele punt scheidt begin-en eind index, het streepje scheidt de talen en de spaties scheidt de woorden. Eén woord kan worden uitgelijnd met nul, één of meerdere woorden in de andere taal en de uitgelijnde woorden kunnen niet-aaneengesloten zijn. Als er geen uitlijnings gegevens beschikbaar zijn, is het element uitlijning leeg. De methode retourneert in dat geval geen fout.

Als u uitlijnings gegevens wilt ontvangen, geeft u `includeAlignment=true` op de query teken reeks op.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true&quot; -H &quot;Ocp-Apim-Subscription-Key: <client-secret>&quot; -H &quot;Content-Type: application/json; charset=UTF-8&quot; -d &quot;[{'Text':'The answer lies in machine translation.'}]"
```

Het antwoord is:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique.",
                "to":"fr",
                "alignment":{"proj":"0:2-0:1 4:9-3:9 11:14-11:19 16:17-21:24 19:25-40:50 27:37-29:38 38:38-51:51"}
            }
        ]
    }
]
```

De uitlijnings gegevens beginnen met `0:2-0:1` , wat betekent dat de eerste drie tekens in de bron tekst ( `The` ) worden toegewezen aan de eerste twee tekens in de vertaalde tekst ( `La` ).

#### <a name="limitations"></a>Beperkingen
Het verkrijgen van informatie over uitlijning is een experimentele functie die is ingeschakeld voor het prototypen van onderzoek en ervaringen met mogelijke woordgroepen toewijzingen. We kunnen ervoor kiezen deze in de toekomst niet meer te ondersteunen. Hier volgen enkele van de belangrijkste beperkingen waarbij uitlijningen niet worden ondersteund:

* Uitlijning is niet beschikbaar voor tekst in HTML-indeling, dat wil zeggen textType = HTML
* Uitlijning wordt alleen geretourneerd voor een subset van de taal paren:
  - Engels naar/van een andere taal, behalve traditioneel Chinees, Kantonees (traditioneel) of Servisch (cyrillisch).
  - van Japans naar Koreaans of van Koreaans tot Japans.
  - van Japans tot Chinees vereenvoudigd en vereenvoudigd Chinees in het Japans. 
  - van vereenvoudigd Chinees in traditioneel Chinees en traditioneel Chinees in vereenvoudigd Chinees. 
* U ontvangt geen uitlijning als de zin een ingeblikte vertaling is. Een voor beeld van een ingestelde vertaling is ' Dit is een test ', ' Ik ben gek ' en andere hoge frequentie zinnen.
* Uitlijning is niet beschikbaar wanneer u een van de benaderingen toepast om omzetting te voor komen, zoals [hier](../prevent-translation.md) wordt beschreven

### <a name="obtain-sentence-boundaries"></a>Grenzen van zinnen verkrijgen

Als u informatie over de lengte van de zin wilt ontvangen in de bron tekst en de vertaalde tekst, geeft u `includeSentenceLength=true` op de query teken reeks op.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

Het antwoord is:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique. La meilleure technologie de traduction automatique ne peut pas toujours fournir des traductions adaptées à un site ou des utilisateurs comme un être humain. Il suffit de copier et coller un extrait de code n’importe où.",
                "to":"fr",
                "sentLen":{"srcSentLen":[40,117,46],"transSentLen":[53,157,62]}
            }
        ]
    }
]
```

### <a name="translate-with-dynamic-dictionary"></a>Vertalen met dynamische woorden lijst

Als u al weet wat de vertaling is die u wilt Toep assen op een woord of een woord groep, kunt u deze als aantekeningen opgeven in de aanvraag. De dynamische woorden lijst is alleen veilig voor de juiste zelfstandige naam woorden, zoals persoonlijke namen en product namen.

De op te geven opmaak maakt gebruik van de volgende syntaxis.

``` 
<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>
```

Neem bijvoorbeeld de Engelse zin ' het woord wordomatic is een vermelding van een woorden lijst '. Als u het woord _wordomatic_ in de vertaling wilt behouden, verzendt u de aanvraag:

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

Het resultaat is:

```
[
    {
        "translations":[
            {"text":"Das Wort \"wordomatic\" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

Deze functie werkt op dezelfde manier met `textType=text` of met `textType=html` . De functie moet spaarzaam worden gebruikt. De juiste en veel betere manier om de vertaling aan te passen, is door aangepaste Translator te gebruiken. Aangepaste vertaler maakt volledig gebruik van context-en statistische kansen. Als u trainings gegevens hebt of kunt gebruiken die uw werk of zin in context weer geven, krijgt u veel betere resultaten. Meer [informatie over Custom Translator](../customization.md).
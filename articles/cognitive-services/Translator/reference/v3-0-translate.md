---
title: Methode Translator Translate
titleSuffix: Azure Cognitive Services
description: Inzicht in de parameters, headers en hoofdtekstberichten voor de methode Translate van Azure Cognitive Services Translator om tekst te vertalen.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 08/06/2020
ms.author: lajanuar
ms.openlocfilehash: 148aa722515d9364ce5af85b3f7c3b39958c9c91
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388377"
---
# <a name="translator-30-translate"></a>Translator 3.0: Vertalen

Vertaalt tekst.

## <a name="request-url"></a>Aanvraag-URL

Verzend een `POST` aanvraag naar:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Aanvraagparameters

Aanvraagparameters die worden doorgegeven aan de queryreeks zijn:

### <a name="required-parameters"></a>Vereiste parameters

<table width="100%">
  <th width="20%">Queryparameter</th>
  <th>Beschrijving</th>
  <tr>
    <td>api-versie</td>
    <td><em>Vereiste parameter</em>.<br/>Versie van de API die is aangevraagd door de client. De waarde moet <code>3.0</code> zijn.</td>
  </tr>
  <tr>
    <td>tot</td>
    <td><em>Vereiste parameter</em>.<br/>Hiermee geeft u de taal van de uitvoertekst. De doeltaal moet een van de <a href="./v3-0-languages.md">ondersteunde talen in</a> het bereik <code>translation</code> zijn. Gebruik bijvoorbeeld om <code>to=de</code> te vertalen naar het Duits.<br/>Het is mogelijk om naar meerdere talen tegelijk te vertalen door de parameter in de queryreeks te herhalen. Gebruik bijvoorbeeld om <code>to=de&to=it</code> te vertalen naar het Duits en Italiaans.</td>
  </tr>
</table>

### <a name="optional-parameters"></a>Optionele parameters

<table width="100%">
  <th width="20%">Queryparameter</th>
  <th>Beschrijving</th>
  <tr>
    <td>from</td>
    <td><em>Optionele parameter</em>.<br/>Hiermee geeft u de taal van de invoertekst. Zoek uit welke talen u kunt vertalen door ondersteunde talen op <a href="./v3-0-languages.md">te zoeken met</a> behulp van het <code>translation</code> bereik. Als de <code>from</code> parameter niet is opgegeven, wordt automatische taaldetectie toegepast om de brontaal te bepalen. <br/><br/>U moet de <code>from</code> parameter gebruiken in plaats van autodetection bij het gebruik van de functie voor dynamische <a href="/azure/cognitive-services/translator/dynamic-dictionary">woordenlijsten.</a></td>
  </tr>  
  <tr>
    <td>textType</td>
    <td><em>Optionele parameter</em>.<br/>Hiermee definieert u of de tekst die wordt vertaald platte tekst of HTML-tekst is. Elke HTML moet een goed gevormd, volledig element zijn. Bij het vertalen van HTML-tekst bevat de uitvoertekst de volgende speciale tekens in de vorm van een escape-teken: '&', '<' en '>'. Dit staat los van het gegeven of de html-invoertekst de tekens bevat met een escape-teken. Mogelijke waarden zijn: <code>plain</code> (standaard) of <code>html</code>.</td>
  </tr>
  <tr>
    <td>category</td>
    <td><em>Optionele parameter</em>.<br/>Een tekenreeks die de categorie (domein) van de vertaling opgeeft. Deze parameter wordt gebruikt om vertalingen op te halen van een aangepast systeem dat is gebouwd <a href="../customization.md">met Custom Translator</a>. Voeg de categorie-id van uw Custom Translator <a href="/azure/cognitive-services/translator/custom-translator/how-to-create-project#view-project-details">projectdetails</a> toe aan deze parameter om uw geïmplementeerde aangepaste systeem te gebruiken. De standaardwaarde is: <code>general</code> .</td>
  </tr>
  <tr>
    <td>profanityAction</td>
    <td><em>Optionele parameter</em>.<br/>Hiermee geeft u op hoe grof taalgebruik moet worden behandeld in vertalingen. Mogelijke waarden zijn: <code>NoAction</code> (standaard) <code>Marked</code> of <code>Deleted</code> . Zie Verwerking van grof taalgebruik voor meer inzicht in manieren om grof <a href="#handle-profanity">taalgebruik te behandelen.</a></td>
  </tr>
  <tr>
    <td>profanityMarker</td>
    <td><em>Optionele parameter</em>.<br/>Hiermee geeft u op hoe grof taalgebruik moet worden gemarkeerd in vertalingen. Mogelijke waarden zijn: <code>Asterisk</code> (standaard) of <code>Tag</code> . Zie Verwerking van grof taalgebruik voor meer inzicht in manieren om grof <a href="#handle-profanity">taalgebruik te behandelen.</a></td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td><em>Optionele parameter</em>.<br/>Hiermee geeft u op of uitlijningsprojectie van brontekst naar vertaalde tekst moet worden opnemen. Mogelijke waarden zijn: <code>true</code> of <code>false</code> (standaard). </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td><em>Optionele parameter</em>.<br/>Hiermee geeft u op of u zingrenzen wilt opnemen voor de invoertekst en de vertaalde tekst. Mogelijke waarden zijn: <code>true</code> of <code>false</code> (standaard).</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td><em>Optionele parameter</em>.<br/>Hiermee geeft u een terugvaltaal op als de taal van de invoertekst niet kan worden geïdentificeerd. Automatische taaldetectie wordt toegepast wanneer de <code>from</code> parameter wordt weggelaten. Als de detectie mislukt, <code>suggestedFrom</code> wordt ervan uitgegaan dat de taal wordt gebruikt.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td><em>Optionele parameter</em>.<br/>Hiermee geeft u het script van de invoertekst.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td><em>Optionele parameter</em>.<br/>Hiermee geeft u het script van de vertaalde tekst.</td>
  </tr>
  <tr>
    <td>allowFallback</td>
    <td><em>Optionele parameter</em>.<br/>Hiermee geeft u op dat de service mag terugvallen op een algemeen systeem wanneer er geen aangepast systeem bestaat. Mogelijke waarden zijn: <code>true</code> (standaard) of <code>false</code> .<br/><br/><code>allowFallback=false</code> geeft aan dat de vertaling alleen systemen mag gebruiken die zijn getraind voor <code>category</code> de die zijn opgegeven door de aanvraag. Als voor een vertaling van taal X naar taal Y ketens via een draaitaal E vereist zijn, moeten alle systemen in de keten (X->E en E->Y) aangepast zijn en dezelfde categorie hebben. Als er geen systeem wordt gevonden met de specifieke categorie, retournt de aanvraag een statuscode van 400. <code>allowFallback=true</code> geeft aan dat de service mag terugvallen op een algemeen systeem wanneer er geen aangepast systeem bestaat.
</td>
  </tr>
</table> 

Aanvraagheaders zijn onder andere:

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

De body van de aanvraag is een JSON-matrix. Elk matrixelement is een JSON-object met een tekenreeks-eigenschap met de naam , die de tekenreeks vertegenwoordigt `Text` die moet worden vertaald.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

De volgende beperkingen zijn van toepassing:

* De matrix kan uit niet meer dan 100 elementen bevatten.
* De volledige tekst die in de aanvraag is opgenomen, mag niet langer zijn dan 10.000 tekens, inclusief spaties.

## <a name="response-body"></a>Hoofdtekst van de reactie

Een geslaagd antwoord is een JSON-matrix met één resultaat voor elke tekenreeks in de invoermatrix. Een resultaatobject bevat de volgende eigenschappen:

  * `detectedLanguage`: Een object dat de gedetecteerde taal beschrijft via de volgende eigenschappen:

      * `language`: Een tekenreeks die de code van de gedetecteerde taal vertegenwoordigt.

      * `score`: Een float-waarde die het vertrouwen in het resultaat aangeeft. De score ligt tussen nul en één en een lage score geeft een lage betrouwbaarheid aan.

    De `detectedLanguage` eigenschap is alleen aanwezig in het resultaatobject wanneer automatische taaldetectie wordt aangevraagd.

  * `translations`: Een matrix met vertaalresultaten. De grootte van de matrix komt overeen met het aantal doeltalen dat is opgegeven via de `to` queryparameter. Elk element in de matrix bevat:

    * `to`: Een tekenreeks die de taalcode van de doeltaal vertegenwoordigt.

    * `text`: Een tekenreeks die de vertaalde tekst geeft.

    * `transliteration`: Een -object dat de vertaalde tekst geeft in het script dat is opgegeven door de `toScript` parameter .

      * `script`: Een tekenreeks die het doelscript opgeeft.   

      * `text`: Een tekenreeks die de vertaalde tekst in het doelscript geeft.

    Het `transliteration` object wordt niet opgenomen als er geen transliteratie wordt plaatsvinden.

    * `alignment`: Een object met één tekenreeks-eigenschap met de naam `proj` , waarmee invoertekst wordt toegerekend aan vertaalde tekst. De uitlijningsinformatie wordt alleen opgegeven wanneer de aanvraagparameter `includeAlignment` `true` is. Uitlijning wordt geretourneerd als een tekenreekswaarde met de volgende indeling: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]` .  De dubbele punt scheidt de begin- en eindindex, het streepje scheidt de talen en spatie scheidt de woorden. Het ene woord kan worden uitgelijnd met nul, één of meerdere woorden in de andere taal en de uitgelijnde woorden kunnen niet-aaneengesloten zijn. Als er geen uitlijningsinformatie beschikbaar is, is het uitlijningselement leeg. Zie [Informatie over uitlijning](#obtain-alignment-information) verkrijgen voor een voorbeeld en beperkingen.

    * `sentLen`: Een object dat zinsgrenzen retourneert in de invoer- en uitvoerteksten.

      * `srcSentLen`: Een matrix met gehele getallen die de lengte van de zinnen in de invoertekst vertegenwoordigt. De lengte van de matrix is het aantal zinnen en de waarden zijn de lengte van elke zin.

      * `transSentLen`: Een matrix met gehele getallen die de lengte van de zinnen in de vertaalde tekst vertegenwoordigt. De lengte van de matrix is het aantal zinnen en de waarden zijn de lengte van elke zin.

    Zinsgrenzen worden alleen opgenomen wanneer de aanvraagparameter `includeSentenceLength` `true` is.

  * `sourceText`: Een object met één tekenreeks-eigenschap met de naam `text` , waarmee de invoertekst wordt weergegeven in het standaardscript van de brontaal. `sourceText` De eigenschap is alleen aanwezig wanneer de invoer wordt uitgedrukt in een script dat niet het gebruikelijke script voor de taal is. Als de invoer bijvoorbeeld Arabisch is geschreven in het Latijnse script, zou dan dezelfde Arabische tekst zijn die is `sourceText.text` geconverteerd naar het Script van Script.

Voorbeeld van JSON-antwoorden wordt gegeven in de [sectie met](#examples) voorbeelden.

## <a name="response-headers"></a>Antwoordheaders

<table width="100%">
  <th width="20%">Kopteksten</th>
  <th>Beschrijving</th>
    <tr>
    <td>X-RequestId</td>
    <td>De waarde die door de service wordt gegenereerd om de aanvraag te identificeren. Deze wordt gebruikt voor het oplossen van problemen.</td>
  </tr>
  <tr>
    <td>X-MT-System</td>
    <td>Hiermee geeft u het systeemtype op dat is gebruikt voor vertaling voor elke 'naar'-taal die is aangevraagd voor vertaling. De waarde is een door komma's gescheiden lijst met tekenreeksen. Elke tekenreeks geeft een type aan:<br/><ul><li>Aangepast: de aanvraag bevat een aangepast systeem en er is ten minste één aangepast systeem gebruikt tijdens de vertaling.</li><li>Team : alle andere aanvragen</li></td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Antwoordstatuscodes

Hier volgen de mogelijke HTTP-statuscodes die een aanvraag retourneert. 

<table width="100%">
  <th width="20%">Statuscode</th>
  <th>Beschrijving</th>
  <tr>
    <td>200</td>
    <td>Voltooid.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Een van de queryparameters ontbreekt of is ongeldig. Corrigeert de aanvraagparameters voordat u het opnieuw doet.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>De aanvraag kan niet worden geverifieerd. Controleer of de referenties zijn opgegeven en geldig zijn.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>De aanvraag is niet geautoriseerd. Controleer het foutbericht met details. Dit geeft vaak aan dat alle gratis vertalingen die worden geleverd met een proefabonnement, zijn gebruikt.</td>
  </tr>
  <tr>
    <td>408</td>
    <td>De aanvraag kan niet worden voltooid omdat een resource ontbreekt. Controleer het foutbericht met details. Wanneer u een aangepaste gebruikt, geeft dit vaak aan dat het aangepaste vertaalsysteem nog niet beschikbaar <code>category</code> is voor het verwerken van aanvragen. De aanvraag moet opnieuw worden ingediend na een wachttijd (bijvoorbeeld 1 minuut).</td>
  </tr>
  <tr>
    <td>429</td>
    <td>De server heeft de aanvraag afgewezen omdat de client de aanvraaglimieten heeft overschreden.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Er is een onverwachte fout opgetreden. Als de fout zich blijft voordoen, meldt u deze met: datum en tijd van de fout, aanvraag-id uit <code>X-RequestId</code> antwoordheader en client-id uit aanvraagheader <code>X-ClientTraceId</code> .</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Server tijdelijk niet beschikbaar. De aanvraag opnieuw proberen. Als de fout zich blijft voordoen, meldt u deze met: datum en tijd van de fout, aanvraag-id uit <code>X-RequestId</code> antwoordheader en client-id uit aanvraagheader <code>X-ClientTraceId</code> .</td>
  </tr>
</table> 

Als er een fout optreedt, retourneert de aanvraag ook een JSON-foutbericht. De foutcode is een 6-cijferig getal dat de 3-cijferige HTTP-statuscode combineert, gevolgd door een getal van 3 cijfers om de fout verder te categoriseren. Algemene foutcodes vindt u op de [v3 Translator-referentiepagina.](./v3-0-reference.md#errors) 

## <a name="examples"></a>Voorbeelden

### <a name="translate-a-single-input"></a>Eén invoer vertalen

In dit voorbeeld ziet u hoe u één zin van het Engels kunt vertalen naar vereenvoudigd Chinees.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

De antwoord-body is:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

De matrix bevat één element, waarmee één stukje tekst in de `translations` invoer wordt vertaald.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Eén invoer vertalen met automatische taaldetectie

In dit voorbeeld ziet u hoe u één zin van het Engels kunt vertalen naar vereenvoudigd Chinees. De aanvraag geeft de invoertaal niet op. In plaats daarvan wordt automatische detectie van de brontaal gebruikt.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

De antwoord-body is:

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
Het antwoord is vergelijkbaar met het antwoord uit het vorige voorbeeld. Omdat automatische taaldetectie is aangevraagd, bevat het antwoord ook informatie over de taal die is gedetecteerd voor de invoertekst. De automatische taaldetectie werkt beter met langere invoertekst.

### <a name="translate-with-transliteration"></a>Vertalen met transliteratie

We gaan het vorige voorbeeld uitbreiden door transliteratie toe te voegen. In de volgende aanvraag wordt gevraagd om een Chinese vertaling die is geschreven in het Latijnse script.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

De antwoord-body is:

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

Het vertaalresultaat bevat nu een eigenschap, waarmee de vertaalde tekst wordt `transliteration` voorzien van Latijnse tekens.

### <a name="translate-multiple-pieces-of-text"></a>Meerdere tekstteksten vertalen

Het vertalen van meerdere tekenreeksen tegelijk is simpelweg een kwestie van het opgeven van een matrix met tekenreeksen in de aanvraag body.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

Het antwoord bevat de vertaling van alle tekst in exact dezelfde volgorde als in de aanvraag.
De antwoord-body is:

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

In dit voorbeeld ziet u hoe u dezelfde invoer in meerdere talen in één aanvraag kunt vertalen.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

De antwoord-body is:

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

### <a name="handle-profanity"></a>Grof taalgebruik afhandelen

Normaal gesproken behoudt de Translator-service grof taalgebruik dat aanwezig is in de bron in de vertaling. De mate van grof taalgebruik en de context waardoor woorden grof zijn, verschillen tussen culturen. Als gevolg hiervan kan de mate van grof taalgebruik in de doeltaal worden versterkt of verminderd.

Als u wilt voorkomen dat de vertaling grof taalgebruik krijgt, ongeacht de aanwezigheid van grof taalgebruik in de brontekst, kunt u de filteroptie voor grof taalgebruik gebruiken. Met de optie kunt u kiezen of u grof taalgebruik wilt zien, of u grof taalgebruik wilt markeren met de juiste tags (zodat u uw eigen naverwerking kunt toevoegen) of dat u geen actie wilt ondernemen. De geaccepteerde waarden `ProfanityAction` van zijn , en `Deleted` `Marked` `NoAction` (standaard).

<table width="100%">
  <th width="20%">ProfanityAction</th>
  <th>Bewerking</th>
  <tr>
    <td><code>NoAction</code></td>
    <td>Dit is de standaardinstelling. Grof taalgebruik wordt van bron naar doel doorgeven.<br/><br/>
    <strong>Voorbeeldbron (Japans)</strong>: 彼はジャsカ extraです"<br/>
    <strong>Voorbeeldvertaling (Engels)</strong>: Hij is een jackass.
    </td>
  </tr>
  <tr>
    <td><code>Deleted</code></td>
    <td>Scheldwoorden worden zonder vervanging uit de uitvoer verwijderd.<br/><br/>
    <strong>Voorbeeldbron (Japans)</strong>: 彼はジャsカ extraです"<br/>
    <strong>Voorbeeldvertaling (Engels)</strong>: Hij is een.
    </td>
  </tr>
  <tr>
    <td><code>Marked</code></td>
    <td>Scheldwoorden worden vervangen door een markering in de uitvoer. De markering is afhankelijk van de <code>ProfanityMarker</code> parameter .<br/><br/>
Voor <code>ProfanityMarker=Asterisk</code> worden aaneenlaste woorden vervangen door <code>***</code> :<br/>
    <strong>Voorbeeldbron (Japans)</strong>: 彼はジャsカ extraです"<br/>
    <strong>Voorbeeldvertaling (Engels)</strong>: Hij is een \* \* \* .<br/><br/>
Voor worden scheldwoorden omgeven door XML-tags grof taalgebruik en <code>ProfanityMarker=Tag</code> &lt; &gt; &lt; /grof taalgebruik: &gt;<br/>
    <strong>Voorbeeldbron (Japans)</strong>: 彼はジャ hoe カ de です.<br/>
    <strong>Voorbeeldvertaling (Engels)</strong>: Hij is een grof taalgebruik &lt; &gt; jackass &lt; /profanity &gt; .
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

Deze laatste aanvraag retourneert:

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

Het is gebruikelijk om inhoud te vertalen die markeringen bevat, zoals inhoud van een HTML-pagina of inhoud uit een XML-document. Neem de `textType=html` queryparameter op bij het vertalen van inhoud met tags. Bovendien is het soms handig om specifieke inhoud uit te sluiten van vertaling. U kunt het kenmerk gebruiken `class=notranslate` om inhoud op te geven die in de oorspronkelijke taal moet blijven. In het volgende voorbeeld wordt de inhoud in het eerste element niet vertaald, terwijl de inhoud in het tweede `div` `div` element wordt vertaald.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Hier ziet u een voorbeeldaanvraag ter illustratie.

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

### <a name="obtain-alignment-information"></a>Informatie over uitlijning verkrijgen

Uitlijning wordt geretourneerd als een tekenreekswaarde met de volgende notatie voor elk woord van de bron. De informatie voor elk woord wordt gescheiden door een spatie, inclusief voor niet-door ruimte gescheiden talen (scripts) zoals Chinees:

[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

Voorbeeld van een uitlijningsreeks: '0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21'.

Met andere woorden, de dubbele punt scheidt de begin- en eindindex, het streepje scheidt de talen en spatie scheidt de woorden. Het ene woord kan worden uitgelijnd met nul, één of meerdere woorden in de andere taal en de uitgelijnde woorden kunnen niet-aaneengesloten zijn. Wanneer er geen uitlijningsinformatie beschikbaar is, is het element Uitlijning leeg. De methode retourneert in dat geval geen fout.

Als u uitlijningsinformatie wilt ontvangen, geeft `includeAlignment=true` u op in de queryreeks.

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

De uitlijningsinformatie begint met , wat betekent dat de eerste drie tekens in de brontekst ( ) worden afgestemd op de eerste twee tekens `0:2-0:1` `The` in de vertaalde tekst ( `La` ).

#### <a name="limitations"></a>Beperkingen
Het verkrijgen van uitlijningsinformatie is een experimentele functie die we hebben ingeschakeld voor het prototypen van onderzoek en ervaringen met mogelijke woordgroepentoewijzingen. We kunnen ervoor kiezen om dit in de toekomst niet meer te ondersteunen. Hier zijn enkele van de belangrijkste beperkingen waarbij uitlijning niet wordt ondersteund:

* Uitlijning is niet beschikbaar voor tekst in HTML-indeling, dat wil zeggen textType=html
* Uitlijning wordt alleen geretourneerd voor een subset van de taalparen:
  - Engels naar/van een andere taal, met uitzondering van traditioneel Chinees, Cantonees (traditioneel) of Servisch (Cyrillisch).
  - van Japans naar Koreaans of van Koreaans tot Japans.
  - van Japans naar Chinees Vereenvoudigd en Chinees Vereenvoudigd naar Japans. 
  - van Chinees vereenvoudigd tot traditioneel Chinees en Traditioneel Chinees tot Vereenvoudigd Chinees. 
* U ontvangt geen uitlijning als de zin een vertaling in een canned is. Voorbeeld van een verwerkte vertaling is 'This is a test', 'I love you' en andere zinnen met een hoge frequentie.
* Uitlijning is niet beschikbaar wanneer u een van de benaderingen wilt toepassen om vertaling te voorkomen, zoals hier wordt [beschreven](../prevent-translation.md)

### <a name="obtain-sentence-boundaries"></a>Grenzen van zinnen verkrijgen

Als u informatie wilt ontvangen over de zinlengte in de brontekst en vertaalde tekst, geeft u `includeSentenceLength=true` op in de queryreeks.

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

### <a name="translate-with-dynamic-dictionary"></a>Vertalen met dynamische woordenlijst

Als u al weet welke vertaling u wilt toepassen op een woord of woordgroep, kunt u deze als markering in de aanvraag inleveren. De dynamische woordenlijst is alleen veilig voor de juiste zelfstandige naamwoorden, zoals persoonlijke namen en productnamen.

De te leveren markering maakt gebruik van de volgende syntaxis.

``` 
<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>
```

Denk bijvoorbeeld aan de Engelse zin 'Het woord wordomatic is een woordenlijstinvoer'. Als u het woord _wordomatic_ in de vertaling wilt behouden, verzendt u de aanvraag:

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

Deze functie werkt op dezelfde manier met `textType=text` of met `textType=html` . De functie moet spaarzaam worden gebruikt. De juiste en veel betere manier om vertalingen aan te passen, is door gebruik te Custom Translator. Custom Translator maakt volledig gebruik van context en statistische waarschijnlijkheden. Als u trainingsgegevens hebt of zich kunt veroorloven om uw werk of woordgroep in context weer te geven, krijgt u veel betere resultaten. [Meer informatie over Custom Translator](../customization.md).

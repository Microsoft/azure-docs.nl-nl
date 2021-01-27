---
title: 'Zelfstudie: Een Flask-app bouwen om tekst te vertalen, synthetiseren en analyseren - Translator'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie bouwt u een op Flask gebaseerde web-app om tekst te vertalen, sentiment te analyseren en vertaalde tekst in spraak te synthetiseren.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 05/26/2020
ms.author: lajanuar
ms.custom: devx-track-python, devx-track-js
ms.openlocfilehash: 755e6370883bf39596850b45dc10f7efd3c9b55b
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896677"
---
# <a name="tutorial-build-a-flask-app-with-azure-cognitive-services"></a>Zelfstudie: Een Flask-app bouwen met Azure Cognitive Services

In deze zelfstudie bouwt u een op Flask-web-app die gebruikmaakt van Azure Cognitive Services om tekst te vertalen, sentiment te analyseren en vertaalde tekst in spraak te synthetiseren. De nadruk ligt op de Python-code en Flask-routes die de toepassing mogelijk maken. U krijgt hulp met de HTML en JavaScript waarmee de app wordt opgebouwd. Als u problemen ondervindt, laat het ons weten met de feedback-knop hieronder.

Deze zelfstudie bevat de volgende onderwerpen:

> [!div class="checklist"]
> * Azure-abonnementssleutels ophalen
> * Ontwikkelomgeving instellen en afhankelijkheden installeren
> * Flask-app maken
> * Translator gebruiken om tekst te vertalen
> * Text Analytics gebruiken om positief/negatief sentiment van invoertekst en vertalingen te analyseren
> * Speech Services gebruiken om vertaalde tekst te converteren naar gesynthetiseerde spraak
> * Uw Flask-app lokaal uitvoeren

> [!TIP]
> Als u meteen met de code wilt beginnen, kunt u het volledige voorbeeld, inclusief de instructies voor het bouwen, vinden op [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="what-is-flask"></a>Wat is Flask?

Flask is een microframework voor het maken van webtoepassingen. Dit houdt in dat Flask hulpprogramma's, bibliotheken en technologieën bevat waarmee u een webtoepassing kunt bouwen. Deze webtoepassing kan bestaan uit enkele webpagina's, een blog of een wiki, of dienstdoen als een onafhankelijke, webgebaseerde agendatoepassing of een commerciële website.

Voor degenen die zich na deze zelfstudie in de stof willen verdiepen, volgen hier een paar nuttige koppelingen:

* [Flask documentation](http://flask.pocoo.org/) (Documentatie over Flask)
* [Flask for Dummies - A Beginner's Guide to Flask](https://codeburst.io/flask-for-dummies-a-beginners-guide-to-flask-part-uno-53aec6afc5b1) (Flask voor Dummies: een beginnershandleiding)

## <a name="prerequisites"></a>Vereisten

Laten we de software en abonnementssleutels bekijken die u voor deze zelfstudie nodig hebt.

* [Python 3.5.2 of hoger](https://www.python.org/downloads/)
* [Git-hulpprogramma's](https://git-scm.com/downloads)
* Een IDE of teksteditor, zoals [Visual Studio Code](https://code.visualstudio.com/) of [Atom](https://atom.io/)  
* [Chrome](https://www.google.com/chrome/browser/) of [Firefox](https://www.mozilla.org/firefox)
* Een **Translator**-abonnementssleutel (u hoeft geen regio te selecteren.)
* Een **Text Analytics**-abonnementssleutel in de regio **US - west**.
* Een **Speech Services**-abonnementssleutel in de regio **US - west**.

## <a name="create-an-account-and-subscribe-to-resources"></a>Een account maken en op resources abonneren

Zoals eerder vermeld, hebt u voor deze zelfstudie drie abonnementssleutels nodig. Dit betekent dat u binnen uw Azure-account een resource moet maken voor:
* Translator
* Text Analytics
* Speech Services

Gebruik [Een Cognitive Services-account maken in de Azure-portal](../cognitive-services-apis-create-account.md) voor stapsgewijze instructies om resources te maken.

> [!IMPORTANT]
> Voor deze zelfstudie maakt u uw resources in de regio US - west. Als u een andere regio gebruikt, moet u de basis-URL in elk van uw Python-bestanden aanpassen.

## <a name="set-up-your-dev-environment"></a>Uw ontwikkelaarsomgeving instellen

Voordat u uw Flask-web-app bouwt, moet u een werkmap voor het project maken en enkele Python-pakketten installeren.

### <a name="create-a-working-directory"></a>Werkmap maken

1. Open de opdrachtregel (Windows) of Terminal (macOS/Linux). Maak vervolgens een werkmap en submappen voor uw project:  

   ```
   mkdir -p flask-cog-services/static/scripts && mkdir flask-cog-services/templates
   ```
2. Ga naar de werkmap van het project:  

   ```
   cd flask-cog-services
   ```

### <a name="create-and-activate-your-virtual-environment-with-virtualenv"></a>Een virtuele omgeving maken en activeren met `virtualenv`

We gaan met behulp van `virtualenv` een virtuele omgeving maken voor de Flask-app. Als u een virtuele omgeving gebruikt, beschikt u over een schone omgeving waarin u kunt werken.

1. Voer in uw werkmap de volgende opdracht uit om een virtuele omgeving te maken: **macOS/Linux:**
   ```
   virtualenv venv --python=python3
   ```
   Er is expliciet aangegeven dat in de virtuele omgeving Python 3 moet worden gebruikt. Dit zorgt ervoor dat gebruikers met meerdere Python-installaties de juiste versie gebruiken.

   **Windows CMD/Windows Bash:**
   ```
   virtualenv venv
   ```
   De virtuele omgeving krijgt de naam venv.

2. De opdrachten voor het activeren van uw virtuele omgeving variëren, afhankelijk van uw platform/shell:   

   | Platform | Shell | Opdracht |
   |----------|-------|---------|
   | macOS/Linux | bash/zsh | `source venv/bin/activate` |
   | Windows | bash | `source venv/Scripts/activate` |
   | | Opdrachtregel | `venv\Scripts\activate.bat` |
   | | PowerShell | `venv\Scripts\Activate.ps1` |

   Nadat u deze opdracht hebt uitgevoerd, moet de opdrachtregel of de Terminal-sessie het voorvoegsel `venv` krijgen.

3. U kunt de sessie op elk gewenst moment deactiveren door `deactivate`in de opdrachtregel of Terminal te typen.

> [!NOTE]
> Python bevat uitgebreide documentatie voor het maken en beheren van virtuele omgevingen: zie [virtualenv](https://virtualenv.pypa.io/en/latest/).

### <a name="install-requests"></a>Requests installeren

Requests is een populaire module die wordt gebruikt voor het verzenden van HTTP 1.1-aanvragen. U hoeft niet handmatig querytekenreeksen aan uw URL's toe te voegen of uw POST-gegevens formeel te coderen.

1. Voor het installeren van Requests, voert u het volgende uit:

   ```
   pip install requests
   ```

> [!NOTE]
> Als u meer informatie wilt over Requests, raadpleegt u [Requests: HTTP for Humans](https://2.python-requests.org/en/master/) (Requests: HTTP voor de mens).

### <a name="install-and-configure-flask"></a>Flask installeren en configureren

Vervolgens moet Flask worden geïnstalleerd. Flask verwerkt de routering voor de web-app en maakt server-naar-server-aanroepen mogelijk die de abonnementssleutels voor de eindgebruiker verbergen.

1. Voor het installeren van Flask, voert u het volgende uit:
   ```
   pip install Flask
   ```
   We controleren of Flask is geïnstalleerd. Uitvoeren:
   ```
   flask --version
   ```
   De versie moet in Terminal worden weergegeven. Iets anders betekent dat er iets fout is gegaan.

2. Als u de Flask-app wilt uitvoeren, kunt u de opdracht flask gebruiken of in Python switch-instructie -m voor Flask gebruiken. Voordat u dit kunt doen, moet u Terminal laten weten welke app moet worden gebruikt door omgevingsvariabele `FLASK_APP` te exporteren:

   **macOS/Linux**:
   ```
   export FLASK_APP=app.py
   ```

   **Windows**:
   ```
   set FLASK_APP=app.py
   ```

## <a name="create-your-flask-app"></a>De Flask-app maken

In deze sectie gaat u een uitgeklede versie van de Flask-app maken die een HTML-bestand retourneert wanneer een gebruiker de hoofdmap van uw app selecteren. Besteed niet te veel tijd aan het ontrafelen van de code. Later wordt dit bestand bijgewerkt.

### <a name="what-is-a-flask-route"></a>Wat is een Flask-route?

We nemen even de tijd om het over [routes](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.route) te hebben. Routering wordt gebruikt om een URL aan een specifieke functie te koppelen. Flask maakt gebruik van route-decorators voor het registreren van functies voor specifieke URL's. Wanneer een gebruiker bijvoorbeeld naar de hoofdmap (`/`) van de web-app navigeert, wordt `index.html` weergegeven.  

```python
@app.route('/')
def index():
    return render_template('index.html')
```

Laten we naar een ander voorbeeld kijken om dit duidelijk te maken.

```python
@app.route('/about')
def about():
    return render_template('about.html')
```

Deze code zorgt ervoor dat wanneer een gebruiker naar `http://your-web-app.com/about` navigeert, het bestand `about.html` wordt weergegeven.

Terwijl deze voorbeelden laten zien hoe HTML-pagina's voor een gebruiker moeten worden weergegeven, kunnen routes ook worden gebruikt voor het aanroepen van API's wanneer een knop wordt ingedrukt, of om een willekeurig aantal acties uit te voeren zonder dat u de startpagina hoeft te verlaten. U ziet de werking hiervan wanneer u routes voor vertaling, sentiment en spraaksynthese maakt.

### <a name="get-started"></a>Aan de slag

1. Open het project in de IDE en maak een bestand met de naam `app.py` in de hoofdmap van uw werkmap. Vervolgens kopieert u deze code in `app.py` en slaat u deze op:

   ```python
   from flask import Flask, render_template, url_for, jsonify, request

   app = Flask(__name__)
   app.config['JSON_AS_ASCII'] = False

   @app.route('/')
   def index():
       return render_template('index.html')
   ```

   Met dit codeblok wordt aangegeven dat `index.html` moet worden weergegeven wanneer een gebruiker naar de hoofdmap van de web-app (`/`) navigeert.

2. Vervolgens maken we de front-end voor de web-app. Maak een bestand met de naam `index.html` in de map `templates`. Kopieer deze code vervolgens in `templates/index.html`.

   ```html
   <!doctype html>
   <html lang="en">
     <head>
       <!-- Required metadata tags -->
       <meta charset="utf-8">
       <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
       <meta name="description" content="Translate and analyze text with Azure Cognitive Services.">
       <!-- Bootstrap CSS -->
       <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
       <title>Translate and analyze text with Azure Cognitive Services</title>
     </head>
     <body>
       <div class="container">
         <h1>Translate, synthesize, and analyze text with Azure</h1>
         <p>This simple web app uses Azure for text translation, text-to-speech conversion, and sentiment analysis of input text and translations. Learn more about <a href="https://docs.microsoft.com/azure/cognitive-services/">Azure Cognitive Services</a>.
        </p>
        <!-- HTML provided in the following sections goes here. -->

        <!-- End -->
       </div>

       <!-- Required Javascript for this tutorial -->
       <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
       <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
       <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
       <script type = "text/javascript" src ="static/scripts/main.js"></script>
     </body>
   </html>
   ```

3. We gaan de Flask-app testen. Voer de volgende code uit in Terminal:

   ```
   flask run
   ```

4. Open een browser en navigeer naar de opgegeven URL. U moet nu de app met één pagina zien. Druk op **Ctrl+C** om de app af te sluiten.

## <a name="translate-text"></a>Tekst vertalen

Nu u een idee hebt van de werking van een eenvoudige Flask-app, gaan we het volgende doen:

* Enkele Python-instructies schrijven om Translator aan te roepen en een respons te retourneren
* Een Flask-route maken om de Python-code aan te roepen
* De HTML bijwerken met een gebied voor tekstinvoer en -vertaling, een taalselector en een vertaalknop
* JavaScript-code schrijven waarmee gebruikers met behulp van HTML met de Flask-app kunnen communiceren

### <a name="call-the-translator"></a>Translator aanroepen

Het eerste wat u moet doen, is een functie schrijven om Translator aan te roepen. De functie gebruikt twee argumenten: `text_input` en `language_output`. De functie wordt aangeroepen wanneer een gebruiker op de vertaalknop in de app drukt. Het tekstgebied in de HTML wordt als `text_input` verzonden en de waarde voor taalselectie in de HTML wordt als `language_output` verzonden.

1. We beginnen door een bestand met de naam `translate.py` te maken in de hoofdmap van de werkmap.
2. Voeg vervolgens deze code toe aan `translate.py`. Deze functie gebruikt twee argumenten: `text_input` en `language_output`.
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   # If you prefer to use environment variables, see Extra Credit for more info.
   subscription_key = 'YOUR_TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
   
   # Don't forget to replace with your Cog Services location!
   # Our Flask route will supply two arguments: text_input and language_output.
   # When the translate text button is pressed in our Flask app, the Ajax request
   # will grab these values from our web app, and use them in the request.
   # See main.js for Ajax calls.
   def get_translation(text_input, language_output):
       base_url = 'https://api.cognitive.microsofttranslator.com'
       path = '/translate?api-version=3.0'
       params = '&to=' + language_output
       constructed_url = base_url + path + params

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Ocp-Apim-Subscription-Region': 'location',
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = [{
           'text' : text_input
       }]
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Voeg de abonnementssleutel van Translator toe en sla deze op.

### <a name="add-a-route-to-apppy"></a>Een route aan `app.py` toevoegen

Vervolgens moet u een route maken in de Flask-app maken waarmee `translate.py` wordt aangeroepen. Telkens wanneer een gebruiker op de vertaalknop in de app drukt, wordt deze route aangeroepen.

Voor deze app gaat uw route `POST`-aanvragen accepteren. Dat komt omdat de functie de te vertalen tekst en een uitvoertaal voor de vertaling verwacht.

Flask bevat helperfuncties waarmee elke aanvraag kan worden geparseerd en beheerd. In de opgegeven code retourneert `get_json()` de gegevens van de `POST`-aanvraag als JSON. Met `data['text']` en `data['to']` worden vervolgens de waarden voor tekst en uitvoertaal doorgegeven aan de functie `get_translation()`, die beschikbaar is via `translate.py`. De laatste stap bestaat uit het retourneren van de response als JSON, omdat u deze gegevens in de web-app moet weergeven.

In de volgende secties herhaalt u dit proces tijdens het maken van routes voor sentimentanalyse en spraaksynthese.

1. Open `app.py`, zoek de import-instructie boven aan `app.py` en voeg de volgende regel toe:

   ```python
   import translate
   ```
   De Flask-app kan nu gebruikmaken van de methode die beschikbaar is via `translate.py`.

2. Kopieer deze code aan het einde van `app.py` en sla op:

   ```python
   @app.route('/translate-text', methods=['POST'])
   def translate_text():
       data = request.get_json()
       text_input = data['text']
       translation_output = data['to']
       response = translate.get_translation(text_input, translation_output)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>`index.html` bijwerken

Nu u een functie hebt om tekst te vertalen en een route in uw Flask-app om deze aan te roepen, gaat u in de volgende stap de HTML-code voor de app schrijven. De onderstaande HTML biedt het volgende:

* Een tekstgebied waar gebruikers tekst kunnen invoeren die moet worden vertaald.
* Een taalselector.
* HTML-elementen om de gedetecteerde taal en betrouwbaarheidsscores weer te geven die tijdens de vertaling worden geretourneerd.
* Een alleen-lezentekstgebied waarin de uitvoer van de vertaling wordt weergegeven.
* Tijdelijke aanduidingen voor code voor sentimentanalyse en spraaksynthese die u later in de zelfstudie aan dit bestand gaat toevoegen.

We gaan `index.html` bijwerken.

1. Open `index.html` en zoek de volgende opmerkingen bij de code:
   ```html
   <!-- HTML provided in the following sections goes here. -->

   <!-- End -->
   ```

2. Vervang de opmerkingen door dit HTML-blok:
   ```html
   <div class="row">
     <div class="col">
       <form>
         <!-- Enter text to translate. -->
         <div class="form-group">
           <label for="text-to-translate"><strong>Enter the text you'd like to translate:</strong></label>
           <textarea class="form-control" id="text-to-translate" rows="5"></textarea>
         </div>
         <!-- Select output language. -->
         <div class="form-group">
           <label for="select-language"><strong>Translate to:</strong></label>
           <select class="form-control" id="select-language">
             <option value="ar">Arabic</option>
             <option value="ca">Catalan</option>
             <option value="zh-Hans">Chinese (Simplified)</option>
             <option value="zh-Hant">Chinese (Traditional)</option>
             <option value="hr">Croatian</option>
             <option value="en">English</option>
             <option value="fr">French</option>
             <option value="de">German</option>
             <option value="el">Greek</option>
             <option value="he">Hebrew</option>
             <option value="hi">Hindi</option>
             <option value="it">Italian</option>
             <option value="ja">Japanese</option>
             <option value="ko">Korean</option>
             <option value="pt">Portuguese</option>
             <option value="ru">Russian</option>
             <option value="es">Spanish</option>
             <option value="th">Thai</option>
             <option value="tr">Turkish</option>
             <option value="vi">Vietnamese</option>
           </select>
         </div>
         <button type="submit" class="btn btn-primary mb-2" id="translate">Translate text</button></br>
         <div id="detected-language" style="display: none">
           <strong>Detected language:</strong> <span id="detected-language-result"></span><br />
           <strong>Detection confidence:</strong> <span id="confidence"></span><br /><br />
         </div>

         <!-- Start sentiment code-->

         <!-- End sentiment code -->

       </form>
     </div>
     <div class="col">
       <!-- Translated text returned by the Translate API is rendered here. -->
       <form>
         <div class="form-group" id="translator-text-response">
           <label for="translation-result"><strong>Translated text:</strong></label>
           <textarea readonly class="form-control" id="translation-result" rows="5"></textarea>
         </div>

         <!-- Start voice font selection code -->

         <!-- End voice font selection code -->

       </form>

       <!-- Add Speech Synthesis button and audio element -->

       <!-- End Speech Synthesis button -->

     </div>
   </div>
   ```

De volgende stap bestaat uit het schrijven van wat code in JavaScript. Dit is de brug tussen de HTML en de Flask-route.

### <a name="create-mainjs"></a>`main.js` maken  

Het `main.js`-bestand is de brug tussen de HTML en de Flask-route. De app gebruikt een combinatie van jQuery, Ajax en XMLHttpRequest voor het weergeven van inhoud en het maken van `POST`-aanvragen bij de Flask-routes.

In de onderstaande code wordt inhoud van de HTML gebruikt om een aanvraag voor de Flask-route te maken. In het bijzonder worden de inhoud van het tekstgebied en de taalselector aan variabelen toegewezen en vervolgens doorgegeven aan de aanvraag voor `translate-text`.

De code doorloopt vervolgens de respons en werkt de HTML bij met de vertaling, de gedetecteerde taal en de betrouwbaarheidsscore.

1. Maak vanuit de IDE een bestand met de naam `main.js` in de map `static/scripts`.
2. Kopieer deze code in `static/scripts/main.js`:
   ```javascript
   //Initiate jQuery on load.
   $(function() {
     //Translate text with flask route
     $("#translate").on("click", function(e) {
       e.preventDefault();
       var translateVal = document.getElementById("text-to-translate").value;
       var languageVal = document.getElementById("select-language").value;
       var translateRequest = { 'text': translateVal, 'to': languageVal }

       if (translateVal !== "") {
         $.ajax({
           url: '/translate-text',
           method: 'POST',
           headers: {
               'Content-Type':'application/json'
           },
           dataType: 'json',
           data: JSON.stringify(translateRequest),
           success: function(data) {
             for (var i = 0; i < data.length; i++) {
               document.getElementById("translation-result").textContent = data[i].translations[0].text;
               document.getElementById("detected-language-result").textContent = data[i].detectedLanguage.language;
               if (document.getElementById("detected-language-result").textContent !== ""){
                 document.getElementById("detected-language").style.display = "block";
               }
               document.getElementById("confidence").textContent = data[i].detectedLanguage.score;
             }
           }
         });
       };
     });
     // In the following sections, you'll add code for sentiment analysis and
     // speech synthesis here.
   })
   ```

### <a name="test-translation"></a>Vertaling testen

We gaan de vertaling in de app testen.

```
flask run
```

Ga naar het opgegeven serveradres. Typ tekst in het invoergebied, selecteer een taal en druk op 'vertalen'. U moet nu een vertaling te zien krijgen. Als het niet werkt, controleert u of u de abonnementssleutel hebt toegevoegd.

> [!TIP]
> Als de wijzigingen die u hebt aangebracht, niet worden weergegeven, of als de app niet werkt zoals verwacht, wist u de cache of opent u een privé-/incognitovenster.

Druk op **Ctrl+C** om de app af te sluiten. Ga vervolgens naar de volgende sectie.

## <a name="analyze-sentiment"></a>Stemming analyseren

De [Text Analytics-API](../text-analytics/overview.md) kan worden gebruikt voor het uitvoeren van sentiment-analyse, het uitpakken van sleutelzinnen uit tekst of het detecteren van de brontaal. In deze app gaan we sentimentanalyse gebruiken om te bepalen of de opgegeven tekst positief, neutraal of negatief is. De API retourneert een numerieke score tussen 0 en 1. Scores in de buurt van 1 wijzen op een positief gevoel, terwijl scores in de buurt van 0 op een negatief gevoel wijzen.

In deze sectie gaat u de volgende dingen doen:

* Python-code schrijven om de Text Analytics-API aan te roepen om de sentimentanalyse uit te voeren en een response te retourneren
* Een Flask-route maken om de Python-code aan te roepen
* De HTML bijwerken met een gebied voor sentimentscores en een knop voor het uitvoeren van analyses
* JavaScript-code schrijven waarmee gebruikers met behulp van HTML met de Flask-app kunnen communiceren

### <a name="call-the-text-analytics-api"></a>De Tekstanalyse-API aanroepen

We gaan een functie schrijven waarmee de Text Analytics-API wordt aangeroepen. De functie gebruikt vier argumenten: `input_text`, `input_language`, `output_text` en `output_language`. De functie wordt aangeroepen wanneer een gebruiker op de knop voor sentimentanalyse in de app drukt. Gegevens die door de gebruiker vanuit het tekstgebied en de taalselector worden verstrekt, evenals de gedetecteerde taal en de vertaaluitvoer, worden bij elke aanvraag meegeleverd. Het responseobject bevat sentimentscores voor de bron en de vertaling. In de volgende secties gaat u code in JavaScript schrijven om de response te parseren en in de app te gebruiken. We richten ons nu voorlopig op het aanroepen van de Text Analytics-API.

1. We maken een bestand met de naam `sentiment.py` in de hoofdmap van de werkmap.
2. Voeg vervolgens deze code toe aan `sentiment.py`.
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   subscription_key = 'YOUR_TEXT_ANALYTICS_SUBSCRIPTION_KEY'

   # Our Flask route will supply four arguments: input_text, input_language,
   # output_text, output_language.
   # When the run sentiment analysis button is pressed in our Flask app,
   # the Ajax request will grab these values from our web app, and use them
   # in the request. See main.js for Ajax calls.

   def get_sentiment(input_text, input_language, output_text, output_language):
       base_url = 'https://westus.api.cognitive.microsoft.com/text/analytics'
       path = '/v2.0/sentiment'
       constructed_url = base_url + path

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = {
           'documents': [
               {
                   'language': input_language,
                   'id': '1',
                   'text': input_text
               },
               {
                   'language': output_language,
                   'id': '2',
                   'text': output_text
               }
           ]
       }
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Voeg de abonnementssleutel voor Text Analytics toe en sla deze op.

### <a name="add-a-route-to-apppy"></a>Een route aan `app.py` toevoegen

We gaan een route maken in de Flask-app maken waarmee `sentiment.py` wordt aangeroepen. Telkens wanneer een gebruiker op de knop voor sentimentanalyse in de app drukt, wordt deze route aangeroepen. Net als de route voor de vertaling accepteert deze route `POST`-aanvragen, omdat de functie argumenten verwacht.

1. Open `app.py` en zoek de import-instructie boven aan `app.py` en werk deze bij:

   ```python
   import translate, sentiment
   ```
   De Flask-app kan nu gebruikmaken van de methode die beschikbaar is via `sentiment.py`.

2. Kopieer deze code aan het einde van `app.py` en sla op:
   ```python
   @app.route('/sentiment-analysis', methods=['POST'])
   def sentiment_analysis():
       data = request.get_json()
       input_text = data['inputText']
       input_lang = data['inputLanguage']
       output_text = data['outputText']
       output_lang =  data['outputLanguage']
       response = sentiment.get_sentiment(input_text, input_lang, output_text, output_lang)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>`index.html` bijwerken

Nu u een functie hebt om sentimentanalyse uit te voeren en een route in uw Flask-app om deze aan te roepen, gaat u in de volgende stap de HTML-code voor de app schrijven. De onderstaande HTML biedt het volgende:

* Toevoegen van een knop aan uw app voor het uitvoeren van sentiment-analyse
* Toevoegen van een element dat uitleg geeft over de sentimentscores
* Toevoegen van een element om de sentimentscores weer te geven

1. Open `index.html` en zoek de volgende opmerkingen bij de code:
   ```html
   <!-- Start sentiment code-->

   <!-- End sentiment code -->
   ```

2. Vervang de opmerkingen door dit HTML-blok:
   ```html
   <button type="submit" class="btn btn-primary mb-2" id="sentiment-analysis">Run sentiment analysis</button></br>
   <div id="sentiment" style="display: none">
      <p>Sentiment scores are provided on a 1 point scale. The closer the sentiment score is to 1, indicates positive sentiment. The closer it is to 0, indicates negative sentiment.</p>
      <strong>Sentiment score for input:</strong> <span id="input-sentiment"></span><br />
      <strong>Sentiment score for translation:</strong> <span id="translation-sentiment"></span>
   </div>
   ```

### <a name="update-mainjs"></a>`main.js` bijwerken

In de onderstaande code wordt inhoud van de HTML gebruikt om een aanvraag voor de Flask-route te maken. In het bijzonder worden de inhoud van het tekstgebied en de taalselector aan variabelen toegewezen en vervolgens doorgegeven aan de aanvraag voor de `sentiment-analysis`-route.

De code doorloopt vervolgens de response en werkt de HTML bij met de sentimentscores.

1. Maak vanuit de IDE een bestand met de naam `main.js` in de map `static`.

2. Kopieer deze code in `static/scripts/main.js`:
   ```javascript
   //Run sentinment analysis on input and translation.
   $("#sentiment-analysis").on("click", function(e) {
     e.preventDefault();
     var inputText = document.getElementById("text-to-translate").value;
     var inputLanguage = document.getElementById("detected-language-result").innerHTML;
     var outputText = document.getElementById("translation-result").value;
     var outputLanguage = document.getElementById("select-language").value;

     var sentimentRequest = { "inputText": inputText, "inputLanguage": inputLanguage, "outputText": outputText,  "outputLanguage": outputLanguage };

     if (inputText !== "") {
       $.ajax({
         url: "/sentiment-analysis",
         method: "POST",
         headers: {
             "Content-Type":"application/json"
         },
         dataType: "json",
         data: JSON.stringify(sentimentRequest),
         success: function(data) {
           for (var i = 0; i < data.documents.length; i++) {
             if (typeof data.documents[i] !== "undefined"){
               if (data.documents[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.documents[i].score;
               }
               if (data.documents[i].id === "2") {
                 document.getElementById("translation-sentiment").textContent = data.documents[i].score;
               }
             }
           }
           for (var i = 0; i < data.errors.length; i++) {
             if (typeof data.errors[i] !== "undefined"){
               if (data.errors[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.errors[i].message;
               }
               if (data.errors[i].id === "2") {
                 document.getElementById("translation-sentiment").textContent = data.errors[i].message;
               }
             }
           }
           if (document.getElementById("input-sentiment").textContent !== '' && document.getElementById("translation-sentiment").textContent !== ""){
             document.getElementById("sentiment").style.display = "block";
           }
         }
       });
     }
   });
   // In the next section, you'll add code for speech synthesis here.
   ```

### <a name="test-sentiment-analysis"></a>Sentimentanalyse testen

We gaan sentimentanalyse in de app testen.

```
flask run
```

Ga naar het opgegeven serveradres. Typ tekst in het invoergebied, selecteer een taal en druk op 'vertalen'. U moet nu een vertaling te zien krijgen. Klik vervolgens op de knop voor het uitvoeren van sentimentanalyse. U moet twee scores te zien krijgen. Als het niet werkt, controleert u of u de abonnementssleutel hebt toegevoegd.

> [!TIP]
> Als de wijzigingen die u hebt aangebracht, niet worden weergegeven, of als de app niet werkt zoals verwacht, wist u de cache of opent u een privé-/incognitovenster.

Druk op **Ctrl+C** om de app af te sluiten. Ga vervolgens naar de volgende sectie.

## <a name="convert-text-to-speech"></a>Tekst naar spraak converteren

Met de [Tekst-naar-spraak-API](../speech-service/text-to-speech.md) kan uw app tekst converteren naar natuurlijke, mensachtige, gesynthetiseerde spraak. De service biedt ondersteuning voor standaard-, neurale en aangepaste stemmen. In de voorbeeld-app wordt een aantal beschikbare stemmen gebruikt. Zie [Ondersteunde talen](../speech-service/language-support.md#text-to-speech) voor een volledige lijst.

In deze sectie gaat u de volgende dingen doen:

* Python-code schrijven om tekst naar spraak te converteren met de Tekst-naar-spraak-API
* Een Flask-route maken om de Python-code aan te roepen
* De HTML bijwerken met een knop om tekst naar spraak te converteren, en een element bijwerken voor het afspelen van audio
* JavaScript-code schrijven waarmee gebruikers met de Flask-app kunnen communiceren

### <a name="call-the-text-to-speech-api"></a>De Tekst-naar-spraak-API aanroepen

We gaan een functie schrijven om tekst naar spraak te converteren. De functie gebruikt twee argumenten: `input_text` en `voice_font`. De functie wordt aangeroepen wanneer een gebruiker op de knop voor het converteren van tekst naar spraak in de app drukt. `input_text` is de vertaaluitvoer die wordt geretourneerd door de aanroep om tekst te vertalen; `voice_font` is de waarde van de spraakstijlselector in de HTML.

1. We maken een bestand met de naam `synthesize.py` in de hoofdmap van de werkmap.

2. Voeg vervolgens deze code toe aan `synthesize.py`.
   ```Python
   import os, requests, time
   from xml.etree import ElementTree

   class TextToSpeech(object):
       def __init__(self, input_text, voice_font):
           subscription_key = 'YOUR_SPEECH_SERVICES_SUBSCRIPTION_KEY'
           self.subscription_key = subscription_key
           self.input_text = input_text
           self.voice_font = voice_font
           self.timestr = time.strftime('%Y%m%d-%H%M')
           self.access_token = None

       # This function performs the token exchange.
       def get_token(self):
           fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
           headers = {
               'Ocp-Apim-Subscription-Key': self.subscription_key
           }
           response = requests.post(fetch_token_url, headers=headers)
           self.access_token = str(response.text)

       # This function calls the TTS endpoint with the access token.
       def save_audio(self):
           base_url = 'https://westus.tts.speech.microsoft.com/'
           path = 'cognitiveservices/v1'
           constructed_url = base_url + path
           headers = {
               'Authorization': 'Bearer ' + self.access_token,
               'Content-Type': 'application/ssml+xml',
               'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
               'User-Agent': 'YOUR_RESOURCE_NAME',
           }
           # Build the SSML request with ElementTree
           xml_body = ElementTree.Element('speak', version='1.0')
           xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
           voice = ElementTree.SubElement(xml_body, 'voice')
           voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
           voice.set('name', 'Microsoft Server Speech Text to Speech Voice {}'.format(self.voice_font))
           voice.text = self.input_text
           # The body must be encoded as UTF-8 to handle non-ascii characters.
           body = ElementTree.tostring(xml_body, encoding="utf-8")

           #Send the request
           response = requests.post(constructed_url, headers=headers, data=body)

           # Write the response as a wav file for playback. The file is located
           # in the same directory where this sample is run.
           return response.content
   ```
3. Voeg de abonnementssleutel voor Speech Services toe en sla deze op.

### <a name="add-a-route-to-apppy"></a>Een route aan `app.py` toevoegen

We gaan een route in de Flask-app maken waarmee `synthesize.py` wordt aangeroepen. Telkens wanneer een gebruiker op de knop voor het converteren van tekst naar spraak in de app drukt, wordt deze route aangeroepen. Net als de routes voor de analyse van vertalingen en sentiment accepteert deze route `POST`-aanvragen, aangezien de functie twee argumenten verwacht: de tekst die moet worden gesynthetiseerd en spraakstijl voor het afspelen.

1. Open `app.py` en zoek de import-instructie boven aan `app.py` en werk deze bij:

   ```python
   import translate, sentiment, synthesize
   ```
   De Flask-app kan nu gebruikmaken van de methode die beschikbaar is via `synthesize.py`.

2. Kopieer deze code aan het einde van `app.py` en sla op:

   ```Python
   @app.route('/text-to-speech', methods=['POST'])
   def text_to_speech():
       data = request.get_json()
       text_input = data['text']
       voice_font = data['voice']
       tts = synthesize.TextToSpeech(text_input, voice_font)
       tts.get_token()
       audio_response = tts.save_audio()
       return audio_response
   ```

### <a name="update-indexhtml"></a>`index.html` bijwerken

Nu u een functie hebt om tekst naar spraak te converteren en een route in uw Flask-app om deze aan te roepen, gaat u in de volgende stap de HTML-code voor de app schrijven. De onderstaande HTML biedt het volgende:

* Een vervolgkeuzelijst voor de selectie van spraak
* Een knop voor het converteren van tekst naar spraak
* Een audio-element dat wordt gebruikt voor het afspelen van de gesynthetiseerde spraak

1. Open `index.html` en zoek de volgende opmerkingen bij de code:
   ```html
   <!-- Start voice font selection code -->

   <!-- End voice font selection code -->
   ```

2. Vervang de opmerkingen door dit HTML-blok:
   ```html
   <div class="form-group">
     <label for="select-voice"><strong>Select voice font:</strong></label>
     <select class="form-control" id="select-voice">
       <option value="(ar-SA, Naayf)">Arabic | Male | Naayf</option>
       <option value="(ca-ES, HerenaRUS)">Catalan | Female | HerenaRUS</option>
       <option value="(zh-CN, HuihuiRUS)">Chinese (Mainland) | Female | HuihuiRUS</option>
       <option value="(zh-CN, Kangkang, Apollo)">Chinese (Mainland) | Male | Kangkang, Apollo</option>
       <option value="(zh-HK, Tracy, Apollo)">Chinese (Hong Kong)| Female | Tracy, Apollo</option>
       <option value="(zh-HK, Danny, Apollo)">Chinese (Hong Kong) | Male | Danny, Apollo</option>
       <option value="(zh-TW, Yating, Apollo)">Chinese (Taiwan)| Female | Yating, Apollo</option>
       <option value="(zh-TW, Zhiwei, Apollo)">Chinese (Taiwan) | Male | Zhiwei, Apollo</option>
       <option value="(hr-HR, Matej)">Croatian | Male | Matej</option>
       <option value="(en-US, AriaRUS)">English (US) | Female | AriaRUS</option>
       <option value="(en-US, Guy24kRUS)">English (US) | Male | Guy24kRUS</option>
       <option value="(en-IE, Sean)">English (IE) | Male | Sean</option>
       <option value="(fr-FR, Julie, Apollo)">French | Female | Julie, Apollo</option>
       <option value="(fr-FR, HortenseRUS)">French | Female | Julie, HortenseRUS</option>
       <option value="(fr-FR, Paul, Apollo)">French | Male | Paul, Apollo</option>
       <option value="(de-DE, Hedda)">German | Female | Hedda</option>
       <option value="(de-DE, HeddaRUS)">German | Female | HeddaRUS</option>
       <option value="(de-DE, Stefan, Apollo)">German | Male | Apollo</option>
       <option value="(el-GR, Stefanos)">Greek | Male | Stefanos</option>
       <option value="(he-IL, Asaf)">Hebrew (Isreal) | Male | Asaf</option>
       <option value="(hi-IN, Kalpana, Apollo)">Hindi | Female | Kalpana, Apollo</option>
       <option value="(hi-IN, Hemant)">Hindi | Male | Hemant</option>
       <option value="(it-IT, LuciaRUS)">Italian | Female | LuciaRUS</option>
       <option value="(it-IT, Cosimo, Apollo)">Italian | Male | Cosimo, Apollo</option>
       <option value="(ja-JP, Ichiro, Apollo)">Japanese | Male | Ichiro</option>
       <option value="(ja-JP, HarukaRUS)">Japanese | Female | HarukaRUS</option>
       <option value="(ko-KR, HeamiRUS)">Korean | Female | Heami</option>
       <option value="(pt-BR, HeloisaRUS)">Portuguese (Brazil) | Female | HeloisaRUS</option>
       <option value="(pt-BR, Daniel, Apollo)">Portuguese (Brazil) | Male | Daniel, Apollo</option>
       <option value="(pt-PT, HeliaRUS)">Portuguese (Portugal) | Female | HeliaRUS</option>
       <option value="(ru-RU, Irina, Apollo)">Russian | Female | Irina, Apollo</option>
       <option value="(ru-RU, Pavel, Apollo)">Russian | Male | Pavel, Apollo</option>
       <option value="(ru-RU, EkaterinaRUS)">Russian | Female | EkaterinaRUS</option>
       <option value="(es-ES, Laura, Apollo)">Spanish | Female | Laura, Apollo</option>
       <option value="(es-ES, HelenaRUS)">Spanish | Female | HelenaRUS</option>
       <option value="(es-ES, Pablo, Apollo)">Spanish | Male | Pablo, Apollo</option>
       <option value="(th-TH, Pattara)">Thai | Male | Pattara</option>
       <option value="(tr-TR, SedaRUS)">Turkish | Female | SedaRUS</option>
       <option value="(vi-VN, An)">Vietnamese | Male | An</option>
     </select>
   </div>
   ```

3. Zoek de volgende opmerkingen bij de code:
   ```html
   <!-- Add Speech Synthesis button and audio element -->

   <!-- End Speech Synthesis button -->
   ```

4. Vervang de opmerkingen door dit HTML-blok:

```html
<button type="submit" class="btn btn-primary mb-2" id="text-to-speech">Convert text-to-speech</button>
<div id="audio-playback">
  <audio id="audio" controls>
    <source id="audio-source" type="audio/mpeg" />
  </audio>
</div>
```

5. Sla uw werk op.

### <a name="update-mainjs"></a>`main.js` bijwerken

In de onderstaande code wordt inhoud van de HTML gebruikt om een aanvraag voor de Flask-route te maken. In het bijzonder worden de vertaling en de spraakstijl aan variabelen toegewezen en vervolgens doorgegeven aan de aanvraag voor de `text-to-speech`-route.

De code doorloopt vervolgens de response en werkt de HTML bij met de sentimentscores.

1. Maak vanuit de IDE een bestand met de naam `main.js` in de map `static`.
2. Kopieer deze code in `static/scripts/main.js`:
   ```javascript
   // Convert text-to-speech
   $("#text-to-speech").on("click", function(e) {
     e.preventDefault();
     var ttsInput = document.getElementById("translation-result").value;
     var ttsVoice = document.getElementById("select-voice").value;
     var ttsRequest = { 'text': ttsInput, 'voice': ttsVoice }

     var xhr = new XMLHttpRequest();
     xhr.open("post", "/text-to-speech", true);
     xhr.setRequestHeader("Content-Type", "application/json");
     xhr.responseType = "blob";
     xhr.onload = function(evt){
       if (xhr.status === 200) {
         audioBlob = new Blob([xhr.response], {type: "audio/mpeg"});
         audioURL = URL.createObjectURL(audioBlob);
         if (audioURL.length > 5){
           var audio = document.getElementById("audio");
           var source = document.getElementById("audio-source");
           source.src = audioURL;
           audio.load();
           audio.play();
         }else{
           console.log("An error occurred getting and playing the audio.")
         }
       }
     }
     xhr.send(JSON.stringify(ttsRequest));
   });
   // Code for automatic language selection goes here.
   ```
3. U bent bijna klaar. Ten slotte gaat u code toevoegen aan `main.js` om automatisch een spraakstijl te selecteren op basis van de taal die voor de vertaling is geselecteerd. Voeg dit codeblok toe aan `main.js`:
   ```javascript
   // Automatic voice font selection based on translation output.
   $('select[id="select-language"]').change(function(e) {
     if ($(this).val() == "ar"){
       document.getElementById("select-voice").value = "(ar-SA, Naayf)";
     }
     if ($(this).val() == "ca"){
       document.getElementById("select-voice").value = "(ca-ES, HerenaRUS)";
     }
     if ($(this).val() == "zh-Hans"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "zh-Hant"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "hr"){
       document.getElementById("select-voice").value = "(hr-HR, Matej)";
     }
     if ($(this).val() == "en"){
       document.getElementById("select-voice").value = "(en-US, Jessa24kRUS)";
     }
     if ($(this).val() == "fr"){
       document.getElementById("select-voice").value = "(fr-FR, HortenseRUS)";
     }
     if ($(this).val() == "de"){
       document.getElementById("select-voice").value = "(de-DE, HeddaRUS)";
     }
     if ($(this).val() == "el"){
       document.getElementById("select-voice").value = "(el-GR, Stefanos)";
     }
     if ($(this).val() == "he"){
       document.getElementById("select-voice").value = "(he-IL, Asaf)";
     }
     if ($(this).val() == "hi"){
       document.getElementById("select-voice").value = "(hi-IN, Kalpana, Apollo)";
     }
     if ($(this).val() == "it"){
       document.getElementById("select-voice").value = "(it-IT, LuciaRUS)";
     }
     if ($(this).val() == "ja"){
       document.getElementById("select-voice").value = "(ja-JP, HarukaRUS)";
     }
     if ($(this).val() == "ko"){
       document.getElementById("select-voice").value = "(ko-KR, HeamiRUS)";
     }
     if ($(this).val() == "pt"){
       document.getElementById("select-voice").value = "(pt-BR, HeloisaRUS)";
     }
     if ($(this).val() == "ru"){
       document.getElementById("select-voice").value = "(ru-RU, EkaterinaRUS)";
     }
     if ($(this).val() == "es"){
       document.getElementById("select-voice").value = "(es-ES, HelenaRUS)";
     }
     if ($(this).val() == "th"){
       document.getElementById("select-voice").value = "(th-TH, Pattara)";
     }
     if ($(this).val() == "tr"){
       document.getElementById("select-voice").value = "(tr-TR, SedaRUS)";
     }
     if ($(this).val() == "vi"){
       document.getElementById("select-voice").value = "(vi-VN, An)";
     }
   });
   ```

### <a name="test-your-app"></a>Uw app testen

We gaan de spraaksynthese testen in de app.

```
flask run
```

Ga naar het opgegeven serveradres. Typ tekst in het invoergebied, selecteer een taal en druk op 'vertalen'. U moet nu een vertaling te zien krijgen. Selecteer vervolgens een stem en klik op de knop voor het converteren van tekst naar spraak. De vertaling moet worden afgespeeld als een gesynthetiseerde spraak. Als het niet werkt, controleert u of u de abonnementssleutel hebt toegevoegd.

> [!TIP]
> Als de wijzigingen die u hebt aangebracht, niet worden weergegeven, of als de app niet werkt zoals verwacht, wist u de cache of opent u een privé-/incognitovenster.

Dat is alles. U hebt een werkende app waarmee vertalingen worden gemaakt, sentiment geanalyseerd en spraak gesynthetiseerd. Druk op **Ctrl+C** om de app af te sluiten. Bekijk ook andere [Azure Cognitive Services](../index.yml).

## <a name="get-the-source-code"></a>De broncode ophalen

De broncode voor dit project is beschikbaar op [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="next-steps"></a>Volgende stappen

* [Naslaginformatie over Translator](./reference/v3-0-reference.md)
* [Naslaginformatie over de Text Analytics-API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)
* [Naslaginformatie voor de Text to Speech-API](../speech-service/rest-text-to-speech.md)
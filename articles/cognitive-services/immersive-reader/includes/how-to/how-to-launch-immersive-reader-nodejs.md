---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: 1b0d929e667023640906baa87a2fd25c61c5d488
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102620081"
---
## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* Een insluitende lezer-resource die is geconfigureerd voor Azure Active Directory-verificatie. Volg [deze instructies](../../how-to-create-immersive-reader.md) om deze in te stellen.  U hebt enkele waarden nodig die u hier hebt gemaakt bij het configureren van de eigenschappen van de omgeving. Sla de uitvoer van uw sessie op in een tekstbestand voor later gebruik.
* [Node.js](https://nodejs.org/) en [Yarn](https://yarnpkg.com)
* Een code-editor, zoals [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Een Node.js-web-app maken met Express

Maak een Node.js-web-app met het hulpprogramma `express-generator`.

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

Installeer Yarn-afhankelijkheden en voeg de afhankelijkheden `request` en `dotenv` toe. Deze worden verderop in de zelfstudie gebruikt.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Een Azure AD-verificatietoken verkrijgen

Schrijf vervolgens een back-end-API om een Azure AD-verificatietoken op te halen.

Voor dit onderdeel hebt u enkele waarden nodig uit de hierboven beschreven configuratie voor Azure AD-verificatie. Raadpleeg het tekstbestand dat u van die sessie hebt opgeslagen.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Wanneer u deze waarden hebt, maakt u een nieuw bestand met de naam _.env_ en plakt u de volgende code erin, waarbij u de eerder aangepaste eigenschapswaarden opgeeft. Geef geen aanhalingstekens of accolades erbij op.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Zorg ervoor dat u dit bestand niet doorvoert in broncodebeheer, omdat het geheimen bevat die niet openbaar moeten worden gemaakt.

Open vervolgens _app.js_ en voeg het volgende toe boven aan het bestand. Hiermee worden de eigenschappen die in het ENV-bestand zijn gedefinieerd, geladen als omgevingsvariabelen in Node.

```javascript
require('dotenv').config();
```

Open het bestand _routes\index.js_ en vervang de inhoud ervan door de volgende code.

Met deze code wordt een API-eindpunt gemaakt waarmee een Azure AD-verificatietoken wordt verkregen met behulp van uw wachtwoord voor de service-principal. Ook wordt het subdomein opgehaald. Vervolgens wordt een-object met het token en het subdomein geretourneerd.

```javascript
var request = require('request');
var express = require('express');
var router = express.Router();

router.get('/getimmersivereaderlaunchparams', function(req, res) {
    request.post ({
                headers: {
                    'content-type': 'application/x-www-form-urlencoded'
                },
                url: `https://login.windows.net/${process.env.TENANT_ID}/oauth2/token`,
                form: {
                    grant_type: 'client_credentials',
                    client_id: process.env.CLIENT_ID,
                    client_secret: process.env.CLIENT_SECRET,
                    resource: 'https://cognitiveservices.azure.com/'
                }
        },
        function(err, resp, tokenResponse) {
                if (err) {
                    return res.status(500).send('CogSvcs IssueToken error');
                }

                const token = JSON.parse(tokenResponse).access_token;
                const subdomain = process.env.SUBDOMAIN;
                return res.send({token: token, subdomain: subdomain});
        }
  );
});

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

module.exports = router;

```

Het API-eindpunt **getimmersivereaderlaunchparams** moet worden beveiligd achter een vorm van verificatie (bijvoorbeeld [OAuth-](https://oauth.net/2/)) om te voorkomen dat niet-geautoriseerde gebruikers tokens verkrijgen die kunnen worden gebruikt voor uw Insluitende lezer-service en -facturering. Dit valt buiten het bereik van deze zelfstudie.

## <a name="launch-the-immersive-reader-with-sample-content"></a>De Insluitende lezer starten met voorbeeldinhoud

1. Open _views\layout.pug_ en voeg de volgende code toe onder de tag `head`, vóór de tag `body`. Met deze `script`-tags worden de [Insluitende lezer SDK](https://github.com/microsoft/immersive-reader-sdk) en jQuery geladen.

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. Open _views\index.pug_ en vervang de inhoud ervan door de volgende code. Met deze code wordt de pagina met wat voorbeeldinhoud gevuld en wordt een knop toegevoegd waarmee Insluitende lezer kan worden gestart.

    ```pug
    extends layout

    block content
          h2(id='title') Geography
          p(id='content') The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers.
          div(class='immersive-reader-button' data-button-style='iconAndText' data-locale='en-US' onclick='launchImmersiveReader()')
          script.

            function getImmersiveReaderLaunchParamsAsync() {
                    return new Promise((resolve, reject) => {
                        $.ajax({
                                url: '/getimmersivereaderlaunchparams',
                                type: 'GET',
                                success: data => {
                                        resolve(data);
                                },
                                error: err => {
                                        console.log('Error in getting token and subdomain!', err);
                                        reject(err);
                                }
                        });
                    });
            }

            async function launchImmersiveReader() {
                    const content = {
                            title: document.getElementById('title').innerText,
                            chunks: [{
                                    content: document.getElementById('content').innerText + '\n\n',
                                    lang: 'en'
                            }]
                    };

                    const launchParams = await getImmersiveReaderLaunchParamsAsync();
                    const token = launchParams.token;
                    const subdomain = launchParams.subdomain;

                    ImmersiveReader.launchAsync(token, subdomain, content);
            }
    ```

3. Onze web-app is nu klaar. Start de app met de volgende opdracht:

    ```bash
    npm start
    ```

4. Open uw browser en ga naar _http://localhost:3000_ . De bovenstaande inhoud wordt weergegeven op de pagina. Klik op de knop **Insluitende lezer** om deze met uw inhoud te starten.

## <a name="specify-the-language-of-your-content"></a>De taal van uw inhoud opgeven

Insluitende lezer biedt ondersteuning voor veel verschillende talen. U kunt de taal van uw inhoud opgeven door de volgende stappen uit te voeren.

1. Open _views\index.pug_ en voeg de volgende code toe onder de tag `p(id=content)` die u in de vorige stap hebt toegevoegd. Met deze code wordt enige Spaanse inhoud van inhoud aan uw pagina toegevoegd.

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. Voeg in de JavaScript-code het volgende toe boven de aanroep van `ImmersiveReader.launchAsync`. Met deze code wordt de Spaanse inhoud doorgegeven aan Insluitende lezer.

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. Ga opnieuw naar _http://localhost:3000_ . De Spaanse tekst wordt op de pagina weergegeven. Wanneer u op **Insluitende lezer** klikt, wordt deze ook in de Insluitende lezer getoond.

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>De taal van de Insluitende lezer-interface opgeven

De taal van de Insluitende lezer-interface komt standaard overeen met de taalinstellingen van de browser. U kunt ook de taal van de Insluitende lezer-interface opgeven met de volgende code.

1. Vervang in _views\index.pug_ de aanroep van `ImmersiveReader.launchAsync(token, subdomain, content)` door de onderstaande code.

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, subdomain, content, options);
    ```

2. Navigeer naar _http://localhost:3000_ . Wanneer u de Insluitende lezer start, wordt de interface weergegeven in het Frans.

## <a name="launch-the-immersive-reader-with-math-content"></a>De Insluitende lezer starten met rekenkundige inhoud

U kunt rekenkundige inhoud in de Insluitende lezer toevoegen met behulp van [MathML](https://developer.mozilla.org/en-US/docs/Web/MathML).

1. Voeg in _views\index.pug_ de volgende code toe boven de aanroep van `ImmersiveReader.launchAsync`:

    ```javascript
    const mathML = '<math xmlns="https://www.w3.org/1998/Math/MathML" display="block"> \
      <munderover> \
        <mo>∫</mo> \
        <mn>0</mn> \
        <mn>1</mn> \
      </munderover> \
      <mrow> \
        <msup> \
          <mi>x</mi> \
          <mn>2</mn> \
        </msup> \
        <mo>ⅆ</mo> \
        <mi>x</mi> \
      </mrow> \
    </math>';

    content.chunks.push({
      content: mathML,
      mimeType: 'application/mathml+xml'
    });
    ```

2. Navigeer naar _http://localhost:3000_ . Wanneer u de Insluitende lezer start en naar beneden schuift, ziet u de rekenkundige formule.

## <a name="next-steps"></a>Volgende stappen

* De [SDK voor Insluitende lezer](https://github.com/microsoft/immersive-reader-sdk) en de [naslaginformatie voor de SDK voor Insluitende lezer](../../reference.md) verkennen
* Codevoorbeelden op [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp) bekijken

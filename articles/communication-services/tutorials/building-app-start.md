---
title: Zelf studie-een web-app voorbereiden voor Azure Communication Services (Node.js)
titleSuffix: An Azure Communication Services tutorial
description: Meer informatie over het maken van een basislijn webtoepassing die ondersteuning biedt voor Azure Communication Services
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 01/03/2012
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 4655a20ddd419993f5a73ec54420abec96d32a62
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546173"
---
# <a name="tutorial-prepare-a-web-app-for-azure-communication-services-nodejs"></a>Zelf studie: een web-app voorbereiden voor Azure Communication Services (Node.js)

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Met Azure Communication Services kunt u realtime-communicatie aan uw toepassingen toevoegen. In deze zelf studie leert u hoe u een webtoepassing kunt instellen die Azure Communication Services ondersteunt. Dit is een inleidende zelf studie die is bedoeld voor nieuwe ontwikkel aars die aan de slag willen gaan met realtime-communicatie.

Aan het einde van deze zelf studie beschikt u over een basislijn webtoepassing die is geconfigureerd met Azure Communication Services-client bibliotheken die u kunt gebruiken om te beginnen met het bouwen van uw realtime-communicatie oplossing.

Ga naar de [Azure Communication Services github](https://github.com/Azure/communication) -pagina om feedback te geven.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Uw ontwikkel omgeving configureren
> * Een lokale webserver instellen
> * De Azure Communication Services-pakketten toevoegen aan uw website
> * Uw website publiceren naar statische Azure-websites

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. Zie [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voor meer informatie. Houd er rekening mee dat $200 u met de gratis account een wille keurige combi natie van Services kunt uitproberen in azure-tegoed.
- [Visual Studio code](https://code.visualstudio.com/): we gebruiken dit om code te bewerken in uw lokale ontwikkel omgeving.
- [webpack](https://webpack.js.org/): dit wordt gebruikt om uw code te bundelen en lokaal te hosten.
- [Node.js](https://nodejs.org/en/): dit wordt gebruikt voor het installeren en beheren van afhankelijkheden zoals Azure Communication Services-client bibliotheken en webpack.
- [NVM en NPM](https://docs.microsoft.com/windows/nodejs/setup-on-windows) voor het afhandelen van versie beheer.
- De [uitbrei ding](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) van de Azure Storage voor Visual Studio code. Deze extensie is vereist voor het publiceren van uw toepassing in Azure Storage. [Meer informatie over het hosten van statische websites in Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website)
- De [uitbrei ding Azure app service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Met de uitbrei ding kunnen websites worden geïmplementeerd (vergelijkbaar met de vorige), maar met de optie voor het configureren van de volledig beheerde continue integratie en continue levering (CI/CD).
- De [Azure function-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) voor het bouwen van uw eigen serverloze toepassingen. U kunt bijvoorbeeld uw verificatie toepassing hosten in azure functions.
- Een actieve Communication Services-resource en verbindingsreeks. [Een Communication Services-resource maken](../quickstarts/create-communication-resource.md).
- Een token voor gebruikers toegang. Zie de [Snelstartgids voor toegangs tokens](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens?pivots=programming-language-javascript) of de [zelf studie voor vertrouwde services](https://docs.microsoft.com/azure/communication-services/tutorials/trusted-service-tutorial) voor instructies.


## <a name="configure-your-development-environment"></a>Uw ontwikkel omgeving configureren

Uw lokale ontwikkel omgeving wordt als volgt geconfigureerd:

:::image type="content" source="./media/step-one-pic-one.png" alt-text="Architectuur van ontwikkelaars omgeving":::


### <a name="install-nodejs-nvm-and-npm"></a>Node.js, NVM en NPM installeren

We gebruiken Node.js voor het downloaden en installeren van verschillende afhankelijkheden die we nodig hebben voor onze toepassing aan de client zijde. We gebruiken deze voor het genereren van statische bestanden die we vervolgens in azure hosten, zodat u zich geen zorgen hoeft te maken over de configuratie op uw server.

Windows-ontwikkel aars kunnen [deze NodeJS-zelf studie](https://docs.microsoft.com/windows/nodejs/setup-on-windows) volgen voor het configureren van node, NVM en NPM. 

Deze zelf studie is getest met behulp van de LTS 12.20.0-versie. Nadat u NVM hebt geïnstalleerd, gebruikt u de volgende Power shell-opdracht voor het implementeren van de versie die u wilt gebruiken:

```PowerShell
nvm list available
nvm install 12.20.0
nvm use 12.20.0
```

:::image type="content" source="./media/step-one-pic-two.png" alt-text="Werken met NVM om Node.jste implementeren ":::

### <a name="configure-visual-studio-code"></a>Visual Studio Code configureren

U kunt [Visual Studio code](https://code.visualstudio.com/) downloaden op een van de [ondersteunde platforms](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

### <a name="create-a-workspace-for-your-azure-communication-services-projects"></a>Een werk ruimte maken voor uw Azure Communication Services-projecten

Maak een nieuwe map om uw project bestanden op te slaan, zoals: `C:\Users\Documents\ACS\CallingApp` . Klik in Visual Studio code op ' bestand ', ' map toevoegen aan de werk ruimte ' en voeg de map toe aan uw werk ruimte.

:::image type="content" source="./media/step-one-pic-three.png" alt-text="Nieuwe werk plek maken":::

Ga in Visual Studio code in het linkerdeel venster naar "Verkenner" en u ziet de map "CallingApp" in de werk ruimte "naamloos".

:::image type="content" source="./media/step-one-pic-four.png" alt-text="Explorer":::

U kunt de naam van uw werk ruimte gratis bijwerken. U kunt uw Node.js-versie valideren door met de rechter muisknop op de map ' CallingApp ' te klikken en ' openen in geïntegreerde Terminal ' te selecteren.

:::image type="content" source="./media/step-one-pic-five.png" alt-text="Een Terminal openen":::

Typ in de Terminal de volgende opdracht om de node.js-versie te valideren die in de vorige stap is geïnstalleerd:

```JavaScript
node --version
```

:::image type="content" source="./media/step-one-pic-six.png" alt-text="Node.js versie valideren":::

### <a name="install-azure-extensions-for-visual-studio-code"></a>Azure-extensies voor Visual Studio code installeren

Installeer de [Azure Storage-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) via de Visual Studio Marketplace of met Visual Studio code (Bekijk > extensies > Azure Storage).

:::image type="content" source="./media/step-one-pic-seven.png" alt-text="Azure Storage-extensie 1 installeren":::

Volg dezelfde stappen voor de uitbrei dingen [Azure functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) en [Azure app service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) .


## <a name="set-up-a-local-webserver"></a>Een lokale webserver instellen

### <a name="create-a-new-npm-package"></a>Een nieuw NPM-pakket maken

Typ in de terminal van het pad van de map met de werk ruimte:

``` console
npm init -y
```

Met deze opdracht initialiseert u een nieuw NPM-pakket en voegt u toe aan `package.json` de hoofdmap van uw project.

:::image type="content" source="./media/step-one-pic-eight.png" alt-text="JSON van het pakket":::

Aanvullende documentatie over de opdracht NPM init vindt u [hier](https://docs.npmjs.com/cli/v6/commands/npm-init)

### <a name="install-webpack"></a>Webpakket installeren

met [webpack](https://webpack.js.org/) kunt u code bundelen in statische bestanden die u kunt implementeren in Azure. Het bevat ook een ontwikkel server die wordt geconfigureerd voor gebruik met het aanroepende voor beeld.

Typ in het Terminal het volgende om webpakket te installeren:

``` Console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Deze zelf studie is getest met de bovenstaande specifieke versies. Opgeven `-dev` geeft aan dat package manager deze afhankelijkheid voor ontwikkelings doeleinden is en niet moet worden opgenomen in de code die we implementeren naar Azure.

U ziet dat er twee nieuwe pakketten worden toegevoegd aan het `package.json` bestand als ' devDependencies '. De pakketten worden in de Directory geïnstalleerd `./CallingApp/node_modules/` .

:::image type="content" source="./media/step-one-pic-ten.png" alt-text="webpakket configuratie":::

### <a name="configure-the-development-server"></a>De ontwikkel server configureren

Het uitvoeren van een statische toepassing (zoals uw `index.html` bestand) vanuit uw browser gebruikt het `file://` protocol. Voor een goede werking van uw NPM-modules moet het HTTP-protocol met webpack als een lokale ontwikkel server worden gebruikt.

We maken twee configuraties: één voor ontwikkeling en de andere voor productie. Bestanden die zijn voor bereid op productie, worden minified, wat inhoudt dat er ongebruikte spaties en tekens worden verwijderd. Dit is geschikt voor productie scenario's waarbij latentie moet worden geminimaliseerd of waar code moet worden verborgen.

Het `webpack-merge` hulp programma wordt gebruikt om te werken met [verschillende configuratie bestanden voor webpacks](https://webpack.js.org/guides/production/)

Laten we beginnen met de ontwikkel omgeving. Eerst moet u installeren `webpack merge` . Voer de volgende handelingen uit in uw terminal:

```Console
npm install --save-dev webpack-merge
```

In het `package.json` bestand ziet u een meer afhankelijkheid die is toegevoegd aan het ' devDependencies '.

In de volgende stap moet u een nieuw bestand maken `webpack.common.js` en de volgende code toevoegen:

```JavaScript
const path = require('path');
module.exports ={
    entry: './app.js',
    output: {
        filename:'app.js',
        path: path.resolve(__dirname, 'dist'),
    }     
}
```

We voegen twee meer bestanden toe, één voor elke configuratie:

* webpack.dev.js
* webpack.prod.js

In de volgende stap moet het bestand worden gewijzigd `webpack.dev.js` . Voeg de volgende code toe aan dat bestand:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
    mode: 'development',
    devtool: 'inline-source-map',
});
```
In deze configuratie importeren we algemene para meters uit `webpack.common.js` , voegt u de twee bestanden samen, stelt u de modus in op ' ontwikkeling ' en configureert u SourceMap als ' inline-source-map '.

De ontwikkel modus vertelt webpacks dat de bestanden niet worden minimaliseren en geen geoptimaliseerde productie bestanden worden geproduceerd. Gedetailleerde documentatie over webpack-modi vindt u [hier](https://webpack.js.org/configuration/mode/).

De opties voor de bron kaart worden [hier](https://webpack.js.org/configuration/devtool/#root)weer gegeven. Het instellen van de bron map maakt het gemakkelijker om fouten op te sporen via uw browser.

:::image type="content" source="./media/step-one-pic-11.png" alt-text="Webpakket configureren":::

Als u de ontwikkel server wilt uitvoeren, gaat u naar `package.json` en voegt u de volgende code toe onder scripts:

```JavaScript
    "build:dev": "webpack-dev-server --config webpack.dev.js"
```

Het bestand moet er nu als volgt uitzien:

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3"
  }
}
```

U hebt de opdracht toegevoegd die kan worden gebruikt vanuit NPM. 

:::image type="content" source="./media/step-one-pic-12.png" alt-text="package.jswijzigen op":::

### <a name="testing-the-development-server"></a>De ontwikkelings server testen

 Maak in Visual Studio code drie bestanden onder het project:

* `index.html`
* `app.js`
* `app.css` (optioneel, Hiermee kunt u een stijl voor uw app Toep assen)

Plak dit in `index.html` :

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My first ACS application</title>
    <link rel="stylesheet" href="./app.css"/>
    <script src="./app.js" defer></script>
</head>
<body>
    <h1>Hello from ACS!</h1>
</body>
</html>
```
:::image type="content" source="./media/step-one-pic-13.png" alt-text="HTML-bestand":::

Voeg de volgende code toe aan `app.js`:

```JavaScript
alert('Hello world alert!');
console.log('Hello world console!');
```
Voeg de volgende code toe aan `app.css`:

```CSS
html {
    font-family: sans-serif;
  }
```
Vergeet niet om op te slaan. Het niet-opgeslagen bestand wordt aangeduid met witte punten naast bestands namen in Verkenner.

 :::image type="content" source="./media/step-one-pic-14.png" alt-text="App.js bestand met JS-code":::

Wanneer u deze pagina opent, ziet u dat uw bericht wordt weer gegeven met een waarschuwing en binnen de console van uw browser.

:::image type="content" source="./media/step-one-pic-15.png" alt-text="App. CSS-bestand":::

Gebruik de volgende Terminal opdracht om uw ontwikkel configuratie te testen:

```Console
npm run build:dev
```

In de-console ziet u waar de-server wordt uitgevoerd. Standaard is dit `http://localhost:8080` . De opdracht build: dev is de opdracht die we eerder hebben toegevoegd `package.json` .

 :::image type="content" source="./media/step-one-pic-16.png" alt-text="Een ontwikkelings server starten":::
 
 Navigeer naar het adres in uw browser en controleer de pagina en waarschuwing, die zijn geconfigureerd in de vorige stappen.
 
  :::image type="content" source="./media/step-one-pic-17.png" alt-text="HTML-pagina":::
  
 
Terwijl de server wordt uitgevoerd, kunt u de code wijzigen en de server en de HTML-pagina worden automatisch opnieuw geladen. 

Ga vervolgens naar het `app.js` bestand in Visual Studio code en Verwijder `alert('Hello world alert!');` . Sla het bestand op en controleer of de waarschuwing verdwijnt uit uw browser.

Als u de server wilt stoppen, kunt u uitvoeren `Ctrl+C` in uw Terminal. Als u de server wilt starten, typt u `npm run build:dev` op elk gewenst moment.

## <a name="add-the-azure-communication-services-packages"></a>De Azure Communication Services-pakketten toevoegen

Gebruik de opdracht `npm install` voor het installeren van de clientbibliotheek voor oproepen voor Javascript in Azure Communication Services.

```Console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Met deze actie worden de gemeen schappelijke en aanroepende pakketten van Azure Communication Services toegevoegd als afhankelijkheden van uw pakket. U ziet dat er twee nieuwe pakketten worden toegevoegd aan het `package.json` bestand. Meer informatie over de `npm install` opdracht vindt u [hier](https://docs.npmjs.com/cli/v6/commands/npm-install).

:::image type="content" source="./media/step-one-pic-nine.png" alt-text="Azure Communication Services-pakketten installeren":::

Deze pakketten worden geleverd door het team van Azure Communication Services en bevatten de verificatie-en aanroepende bibliotheken. De opdracht '--opslaan ' geeft aan dat onze toepassing afhankelijk is van deze pakketten voor productie gebruik en wordt opgenomen in het `dependencies` `package.json` bestand. Wanneer de toepassing voor productie wordt gebouwd, worden de pakketten opgenomen in onze productie code.


## <a name="publish-your-website-to-azure-static-websites"></a>Uw website publiceren naar statische Azure-websites

### <a name="create-a-configuration-for-production-deployment"></a>Een configuratie maken voor de productie-implementatie

Voeg de volgende code toe aan de `webpack.prod.js` :

```JavaScript
const { merge } = require('webpack-merge');
 const common = require('./webpack.common.js');

 module.exports = merge(common, {
   mode: 'production',
 });
 ```

Opmerking: deze configuratie wordt samengevoegd met de webpack.common.js (waarbij het invoer bestand is opgegeven en waar de resultaten worden opgeslagen) en de modus wordt ingesteld op ' productie '.
 
Voeg in `package.json` de volgende code toe:

```JavaScript
"build:prod": "webpack --config webpack.prod.js" 
```

Uw bestand ziet er als volgt uit:

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js",
    "build:prod": "webpack --config webpack.prod.js" 
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@azure/communication-calling": "^1.0.0-beta.3",
    "@azure/communication-common": "^1.0.0-beta.3"
  },
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3",
    "webpack-merge": "^5.7.3"
  }
}
```

 :::image type="content" source="./media/step-one-pic-20.png" alt-text="Geconfigureerde bestanden":::


Voer de volgende handelingen uit in de terminal:

```Console
npm run build:prod
```

Met deze opdracht maakt `dist` `app.js` u een statisch bestand in de map en productie gereed. 

 :::image type="content" source="./media/step-one-pic-21.png" alt-text="Productie-build":::
 
 
### <a name="deploy-your-app-to-azure-storage"></a>Uw app implementeren naar Azure Storage
 
Kopieer `index.html` en `app.css` naar de `dist` map.

`dist`Maak een nieuw bestand met de naam in de map `404.html` . Kopieer de volgende opmaak in dat bestand:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="./app.css"/>
    <title>Document</title>
</head>
<body>
    <h1>The page does not exists.</h1>
</body>
</html>
```

Sla het bestand op (CTRL + S).

Klik met de rechter muisknop en selecteer implementeren naar statische website via Azure Storage.

:::image type="content" source="./media/step-one-pic-22.png" alt-text="Implementatie naar Azure starten":::
 
Selecteer in het `Select subscription` veld aanmelden bij Azure (of een gratis Azure-account maken als u nog geen abonnement hebt gemaakt)
 
:::image type="content" source="./media/step-one-pic-23.png" alt-text="Aanmelden bij Azure":::
 
Selecteer `Create new Storage Account`  >  `Advanced` :

 :::image type="content" source="./media/step-one-pic-24.png" alt-text="De opslag account groep maken":::
 
 Geef de naam van de opslag groep op:
 
 :::image type="content" source="./media/step-one-pic-25.png" alt-text="Een naam voor het account toevoegen":::
 
Maak indien nodig een nieuwe resource groep:
 
  :::image type="content" source="./media/step-one-pic-26.png" alt-text="Nieuwe groep maken":::
  
  Antwoord ' ja ' wilt u een statische website-hosting inschakelen? '
  
  :::image type="content" source="./media/step-one-pic-27.png" alt-text="Optie selecteren om statische website-hosting in te scha kelen":::
  
Accepteer de standaard naam van het bestand in ' Voer de naam van het index document in ' tijdens het maken van het bestand `index.html` .

Typ het `404.html` voor ' Voer het 404-fout document in het pad ' in.  
  
Selecteer de locatie van de toepassing. Op de locatie die u selecteert, wordt gedefinieerd welke media processor wordt gebruikt in uw toekomstige aanroepende toepassing in groeps aanroepen. 

Azure Communication Services selecteert de media processor op basis van de locatie van de toepassing.

:::image type="content" source="./media/step-one-pic-28.png" alt-text="Locatie selecteren":::
  
Wacht tot de resource en uw website zijn gemaakt. 
 
Klik op Bladeren naar website:

:::image type="content" source="./media/step-one-pic-29.png" alt-text="Implementatie is voltooid":::
 
Vanuit de ontwikkel hulpprogramma's van uw browser kunt u de bron inspecteren en ons bestand zien, dat is voor bereid voor productie.
 
:::image type="content" source="./media/step-one-pic-30.png" alt-text="Website":::

Ga naar de [Azure Portal](https://portal.azure.com/#home), selecteer uw resource groep, selecteer de toepassing die u hebt gemaakt en ga naar `Settings`  >  `Static website` . U kunt zien dat statische websites zijn ingeschakeld en noteert het primaire eind punt, het index document en de fout paden document bestanden.

:::image type="content" source="./media/step-one-pic-31.png" alt-text="Statische website selecteren":::

Onder Blob service selecteert u de containers en ziet u twee containers die zijn gemaakt, een voor logboeken ($logs) en inhoud van uw website ($web)

:::image type="content" source="./media/step-one-pic-32.png" alt-text="Container configuratie":::

Als u naar wilt gaan `$web` , ziet u uw bestanden die u in Visual Studio hebt gemaakt en die zijn geïmplementeerd in Azure. 

:::image type="content" source="./media/step-one-pic-33.png" alt-text="Implementatie":::

U kunt de toepassing op elk gewenst moment opnieuw implementeren vanuit Visual Studio code.

U bent nu klaar om uw eerste Azure Communication Services-webtoepassing te maken.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Spraakoproep toevoegen aan uw app](../quickstarts/voice-video-calling/getting-started-with-calling.md)

U wilt mogelijk ook:

- [Chat toevoegen aan uw app](../quickstarts/chat/get-started.md)
- [Tokens voor gebruikerstoegang maken](../quickstarts/access-tokens.md)
- [Meer leren over architectuur van client en server](../concepts/client-and-server-architecture.md)
- [Meer leren over verificatie](../concepts/authentication.md)

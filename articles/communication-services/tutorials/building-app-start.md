---
title: Zelf studie-een web-app voorbereiden voor Azure Communication Services (Node.js)
titleSuffix: An Azure Communication Services tutorial
description: Meer informatie over het maken van een basislijn webtoepassing die ondersteuning biedt voor Azure Communication Services
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 7eb44987dca033ecdac9ef2ca63fb1da97dc9678
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109179"
---
# <a name="tutorial-prepare-a-web-app-for-azure-communication-services-nodejs"></a>Zelf studie: een web-app voorbereiden voor Azure Communication Services (Node.js)

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

U kunt Azure Communication Services gebruiken om realtime communicatie toe te voegen aan uw toepassingen. In deze zelf studie leert u hoe u een webtoepassing kunt instellen die Azure Communication Services ondersteunt. Dit is een inleidende zelf studie voor nieuwe ontwikkel aars die aan de slag willen gaan met realtime-communicatie.

Aan het einde van deze zelf studie beschikt u over een basislijn webtoepassing die is geconfigureerd met de Azure Communication Services Sdk's. U kunt deze toepassing vervolgens gebruiken om aan de slag te gaan met het bouwen van uw realtime-communicatie oplossing.

Ga naar de [Azure Communication Services github-pagina](https://github.com/Azure/communication) om feedback te geven.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Configureer uw ontwikkel omgeving.
> * Stel een lokale webserver in.
> * Voeg de Azure Communication Services-pakketten toe aan uw website.
> * Publiceer uw website naar statische Azure-websites.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. Zie [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voor meer informatie. Het gratis account biedt $200 aan Azure-tegoed om een combi natie van services uit te proberen.
- [Visual Studio code](https://code.visualstudio.com/) voor het bewerken van code in uw lokale ontwikkel omgeving.
- [webpakket](https://webpack.js.org/) om uw code te bundelen en lokaal te hosten.
- [Node.js](https://nodejs.org/en/) voor het installeren en beheren van afhankelijkheden zoals Azure Communication Services sdk's en webpack.
- [NVM en NPM](/windows/nodejs/setup-on-windows) voor het afhandelen van versie beheer.
- De [uitbrei ding](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) van de Azure Storage voor Visual Studio code. U hebt deze uitbrei ding nodig om uw toepassing te publiceren in Azure Storage. [Lees meer over het hosten van statische websites in azure Storage](../../storage/blobs/storage-blob-static-website.md).
- De [uitbrei ding Azure app service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Met de uitbrei ding kunnen websites worden geïmplementeerd met de optie voor het configureren van volledig beheerde continue integratie en continue levering (CI/CD).
- De [Azure functions-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) voor het bouwen van uw eigen serverloze toepassingen. U kunt bijvoorbeeld uw verificatie toepassing in Azure Functions hosten.
- Een actieve Communication Services-resource en verbindingsreeks. [Meer informatie over het maken van een communicatie Services-resource](../quickstarts/create-communication-resource.md).
- Een token voor gebruikers toegang. Zie de [Snelstartgids voor het maken en beheren van toegangs tokens](../quickstarts/access-tokens.md?pivots=programming-language-javascript) of de [zelf studie voor het bouwen van een vertrouwde verificatie service](./trusted-service-tutorial.md)voor instructies.


## <a name="configure-your-development-environment"></a>Uw ontwikkel omgeving configureren

Uw lokale ontwikkel omgeving wordt als volgt geconfigureerd:

:::image type="content" source="./media/step-one-pic-one.png" alt-text="Diagram dat de architectuur van de ontwikkel omgeving illustreert.":::


### <a name="install-nodejs-nvm-and-npm"></a>Node.js, NVM en NPM installeren

We gebruiken Node.js om verschillende afhankelijkheden te downloaden en te installeren die we nodig hebben voor onze toepassing aan de client zijde. We gebruiken deze voor het genereren van statische bestanden die we vervolgens in azure hosten, zodat u zich geen zorgen hoeft te maken over de configuratie op uw server.

Windows-ontwikkel aars kunnen [deze Node.js zelf studie](/windows/nodejs/setup-on-windows) volgen om node, NVM en NPM te configureren. 

Deze zelf studie is gebaseerd op de LTS 12.20.0-versie. Nadat u NVM hebt geïnstalleerd, gebruikt u de volgende Power shell-opdracht voor het implementeren van de versie die u wilt gebruiken:

```PowerShell
nvm list available
nvm install 12.20.0
nvm use 12.20.0
```

:::image type="content" source="./media/step-one-pic-two.png" alt-text="Scherm opname van de opdrachten voor het implementeren van een knooppunt versie.":::

### <a name="configure-visual-studio-code"></a>Visual Studio Code configureren

U kunt [Visual Studio code](https://code.visualstudio.com/) downloaden op een van de [ondersteunde platforms](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

### <a name="create-a-workspace-for-your-azure-communication-services-projects"></a>Een werk ruimte maken voor uw Azure Communication Services-projecten

Maak een map om de project bestanden op te slaan, zoals: `C:\Users\Documents\ACS\CallingApp` . In Visual Studio code selecteert u **bestand**  >  **map toevoegen aan werk ruimte** en voegt u de map toe aan uw werk ruimte.

:::image type="content" source="./media/step-one-pic-three.png" alt-text="Scherm opname van selecties voor het toevoegen van een bestand aan een werk ruimte.":::

Ga in het linkerdeel venster naar **Verkenner** en u ziet uw `CallingApp` map in de werk ruimte zonder **titel** .

:::image type="content" source="./media/step-one-pic-four.png" alt-text="Scherm afbeelding met Verkenner en de naamloze werk ruimte.":::

U kunt de naam van uw werk ruimte gratis bijwerken. U kunt uw Node.js-versie valideren door met de rechter muisknop op de map te klikken `CallingApp` en **openen in geïntegreerde Terminal** te selecteren.

:::image type="content" source="./media/step-one-pic-five.png" alt-text="Scherm opname van de selectie voor het openen van een map in een geïntegreerde Terminal.":::

Voer in de Terminal de volgende opdracht in om de Node.js versie te valideren die in de vorige stap is geïnstalleerd:

```JavaScript
node --version
```

:::image type="content" source="./media/step-one-pic-six.png" alt-text="Scherm opname van de validatie van de knooppunt versie.":::

### <a name="install-azure-extensions-for-visual-studio-code"></a>Azure-extensies voor Visual Studio code installeren

Installeer de [Azure Storage-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) via Visual Studio Marketplace of via Visual Studio code (  >  **uitbrei dingen**  >  **Azure Storage** weer geven).

:::image type="content" source="./media/step-one-pic-seven.png" alt-text="Scherm afbeelding met de knop voor het installeren van de Azure Storage extensie.":::

Volg dezelfde stappen voor de uitbrei dingen [Azure functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) en [Azure app service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) .


## <a name="set-up-a-local-web-server"></a>Een lokale webserver instellen

### <a name="create-an-npm-package"></a>Een NPM-pakket maken

Voer in de terminal in het pad van de map met de werk ruimte het volgende in:

``` console
npm init -y
```

Met deze opdracht initialiseert u een nieuw NPM-pakket en voegt u toe aan `package.json` de hoofdmap van uw project.

:::image type="content" source="./media/step-one-pic-eight.png" alt-text="Scherm opname van het pakket J S O N.":::

`npm init`Zie de [pagina met NPM-documenten voor deze opdracht](https://docs.npmjs.com/cli/v6/commands/npm-init)voor meer informatie over de.

### <a name="install-webpack"></a>Webpakket installeren

U kunt [webpack](https://webpack.js.org/) gebruiken om code te bundelen in statische bestanden die u kunt implementeren in Azure. Het bevat ook een ontwikkel server die u configureert voor gebruik met het aanroepende voor beeld.

Voer in de Terminal de volgende opdracht in om webpakket te installeren:

``` Console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Deze zelf studie is getest met de versies die zijn opgegeven in de voor gaande opdracht. Opgeven `-dev` geeft aan dat package manager deze afhankelijkheid voor ontwikkelings doeleinden is en niet moet worden opgenomen in de code die u in azure implementeert.

U ziet dat er twee nieuwe pakketten worden toegevoegd aan het `package.json` bestand als `devDependencies` . De pakketten worden geïnstalleerd in de `./CallingApp/node_modules/` map.

:::image type="content" source="./media/step-one-pic-ten.png" alt-text="Scherm opname van de webpack-configuratie.":::

### <a name="configure-the-development-server"></a>De ontwikkel server configureren

Het uitvoeren van een statische toepassing (zoals uw `index.html` bestand) vanuit uw browser gebruikt het `file://` protocol. Voor een goede werking van uw NPM-modules moet u het HTTP-protocol gebruiken met webpack als een lokale ontwikkel server.

U maakt twee configuraties: één voor ontwikkeling en de andere voor productie. Bestanden die zijn voor bereid voor productie, worden minified, wat betekent dat u ongebruikte spaties en tekens verwijdert. Deze configuratie is geschikt voor productie scenario's waarbij latentie moet worden geminimaliseerd of waar code moet worden verborgen.

U gebruikt het `webpack-merge` hulp programma voor het werken met [verschillende configuratie bestanden voor webpacks](https://webpack.js.org/guides/production/).

Laten we beginnen met de ontwikkel omgeving. Eerst moet u installeren `webpack merge` . Voer in de Terminal de volgende opdracht uit:

```Console
npm install --save-dev webpack-merge
```

In uw `package.json` bestand ziet u een meer afhankelijkheid die is toegevoegd aan `devDependencies` .

Maak vervolgens een bestand `webpack.common.js` met de naam en voeg de volgende code toe:

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

Voeg vervolgens nog twee bestanden toe, één voor elke configuratie:

* `webpack.dev.js`
* `webpack.prod.js`

Wijzig het `webpack.dev.js` bestand nu door de volgende code toe te voegen:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
    mode: 'development',
    devtool: 'inline-source-map',
});
```
In deze configuratie importeert u algemene para meters uit `webpack.common.js` , voegt u de twee bestanden samen, stelt u de modus in op `development` en configureert u de bron toewijzing als `inline-source-map` .

De ontwikkel modus vertelt webpacks dat de bestanden niet worden minimaliseren en geen geoptimaliseerde productie bestanden worden geproduceerd. Op de [webpagina webpack modus](https://webpack.js.org/configuration/mode/)vindt u gedetailleerde documentatie over webpakket modi.

De opties voor bron kaarten worden weer gegeven op de [webpagina webpakket devtool](https://webpack.js.org/configuration/devtool/#root). Het instellen van de bron map maakt het gemakkelijker om fouten op te sporen via uw browser.

:::image type="content" source="./media/step-one-pic-11.png" alt-text="Scherm opname van de code voor het configureren van webpack.":::

Als u de ontwikkel server wilt uitvoeren, gaat u naar `package.json` en voegt u de volgende code toe onder `scripts` :

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

:::image type="content" source="./media/step-one-pic-12.png" alt-text="Scherm afbeelding waarin de wijziging van package.jsop wordt weer gegeven.":::

### <a name="test-the-development-server"></a>De ontwikkelings server testen

 Maak in Visual Studio code drie bestanden onder het project:

* `index.html`
* `app.js`
* `app.css` (optioneel, voor het opmaak van uw app)

Plak deze code in `index.html` :

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
:::image type="content" source="./media/step-one-pic-13.png" alt-text="Scherm opname van het bestand H T M L.":::

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
Vergeet niet om op te slaan. Het niet-opgeslagen bestand wordt aangeduid met witte punten naast de bestands namen in de Explorer.

:::image type="content" source="./media/step-one-pic-14.png" alt-text="Scherm opname van het App.js-bestand met Java script-code.":::

Wanneer u deze pagina opent, ziet u dat uw bericht wordt weer gegeven met een waarschuwing in de console van uw browser.

:::image type="content" source="./media/step-one-pic-15.png" alt-text="Scherm opname van het bestand app. CSS.":::

Gebruik de volgende Terminal opdracht om uw ontwikkel configuratie te testen:

```Console
npm run build:dev
```

In de-console ziet u waar de-server wordt uitgevoerd. Standaard is dit `http://localhost:8080` . De `build:dev` opdracht is de opdracht die u eerder hebt toegevoegd `package.json` .

:::image type="content" source="./media/step-one-pic-16.png" alt-text="Scherm opname van het starten van een ontwikkelings server.":::
 
Ga naar het adres in uw browser en Bekijk de pagina en waarschuwing die in de vorige stappen zijn geconfigureerd.
 
:::image type="content" source="./media/step-one-pic-17.png" alt-text="Scherm afbeelding van de pagina H T/M L.":::
  
 
Terwijl de server wordt uitgevoerd, kunt u de code en de server wijzigen. De HTML-pagina wordt automatisch opnieuw geladen. 

Ga vervolgens naar het `app.js` bestand in Visual Studio code en Verwijder `alert('Hello world alert!');` . Sla het bestand op en controleer of de waarschuwing verdwijnt uit uw browser.

Als u de server wilt stoppen, kunt u uitvoeren `Ctrl+C` in uw Terminal. Als u de server wilt starten, voert u `npm run build:dev` op elk gewenst moment in.

## <a name="add-the-azure-communication-services-packages"></a>De Azure Communication Services-pakketten toevoegen

Gebruik de `npm install` opdracht om de Azure Communication Services-aanroepende SDK voor Java script te installeren.

```Console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Met deze actie worden de gemeen schappelijke en aanroepende pakketten van Azure Communication Services toegevoegd als afhankelijkheden van uw pakket. U ziet dat er twee nieuwe pakketten worden toegevoegd aan het `package.json` bestand. Meer informatie over `npm install` de [pagina NPM docs voor die opdracht](https://docs.npmjs.com/cli/v6/commands/npm-install)vindt u in.

:::image type="content" source="./media/step-one-pic-nine.png" alt-text="Scherm opname van de code voor het installeren van Azure Communication Services-pakketten.":::

Deze pakketten worden geleverd door het team van Azure Communication Services en bevatten de verificatie-en aanroepende bibliotheken. De `--save` opdracht geeft aan dat de toepassing afhankelijk is van deze pakketten voor productie gebruik en die wordt opgenomen in in `devDependencies` het `package.json` bestand. Wanneer u de toepassing voor productie bouwt, worden de pakketten opgenomen in uw productie code.


## <a name="publish-your-website-to-azure-static-websites"></a>Uw website publiceren naar statische Azure-websites

### <a name="create-a-configuration-for-production-deployment"></a>Een configuratie maken voor de productie-implementatie

Voeg de volgende code toe aan `webpack.prod.js`:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
  mode: 'production',
});
```

Deze configuratie wordt samengevoegd met `webpack.common.js` (waar u het invoer bestand hebt opgegeven en waar u de resultaten wilt opslaan). Met de configuratie wordt de modus ook ingesteld op `production` .
 
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
    "@azure/communication-calling": "^1.0.0-beta.6",
    "@azure/communication-common": "^1.0.0"
  },
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3",
    "webpack-merge": "^5.7.3"
  }
}
```

:::image type="content" source="./media/step-one-pic-20.png" alt-text="Scherm opname van de geconfigureerde bestanden.":::


Voer in de Terminal de volgende handelingen uit:

```Console
npm run build:prod
```

Met deze opdracht maakt `dist` u een map en een `app.js` statisch bestand dat gereed is voor productie. 

:::image type="content" source="./media/step-one-pic-21.png" alt-text="Scherm afbeelding waarin de productie-build wordt weer gegeven.":::
 
 
### <a name="deploy-your-app-to-azure-storage"></a>Uw app implementeren naar Azure Storage

Kopieer `index.html` en `app.css` naar de `dist` map.

`dist`Maak een bestand met de naam in de map `404.html` . Kopieer de volgende opmaak in dat bestand:

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

Klik met de rechter muisknop op de `dist` map en selecteer **implementeren naar statische Website via Azure Storage**.

:::image type="content" source="./media/step-one-pic-22.png" alt-text="Scherm afbeelding van de selecties om te beginnen met de implementatie naar Azure.":::
 
Onder **abonnement selecteren** selecteert **u aanmelden bij Azure** (of **Maak een gratis Azure-account** als u nog geen abonnement hebt gemaakt).
 
:::image type="content" source="./media/step-one-pic-23.png" alt-text="Scherm opname van de selecties voor het aanmelden bij Azure.":::
 
Selecteer **Nieuw opslag account maken**  >  **Geavanceerd**.

:::image type="content" source="./media/step-one-pic-24.png" alt-text="Scherm opname van de selecties voor het maken van de opslag account groep.":::
 
Geef de naam van de opslag groep op.
 
:::image type="content" source="./media/step-one-pic-25.png" alt-text="Scherm opname van het toevoegen van een naam voor het account.":::
 
Maak indien nodig een nieuwe resource groep.
 
:::image type="content" source="./media/step-one-pic-26.png" alt-text="Scherm opname van de selectie voor het maken van een nieuwe resource groep.":::
  
Voor **wilt u statische website-hosting inschakelen? selecteert u** **Ja**.

:::image type="content" source="./media/step-one-pic-27.png" alt-text="Scherm opname van het selecteren van de optie voor het inschakelen van statische website-hosting.":::
  
Accepteer de standaard bestands naam voor **de naam van het index document**. U hebt het bestand al gemaakt `index.html` .

Voer **404.html** in bij **het pad naar het 404-fout document**.  
  
Selecteer de locatie van de toepassing. De locatie die u selecteert, bepaalt welke media processor wordt gebruikt in uw toekomstige aanroepende toepassing in groeps aanroepen. 

Azure Communication Services selecteert de media processor op basis van de locatie van de toepassing.

:::image type="content" source="./media/step-one-pic-28.png" alt-text="Scherm opname van een lijst met locaties.":::
  
Wacht tot de resource en uw website zijn gemaakt. 
 
Selecteer **Bladeren naar website**.

:::image type="content" source="./media/step-one-pic-29.png" alt-text="Scherm afbeelding met een bericht dat de implementatie is voltooid, met de knop voor het bladeren naar een website.":::
 
Vanuit de ontwikkel hulpprogramma's van uw browser kunt u de bron controleren en het bestand zien dat is voor bereid voor productie.
 
:::image type="content" source="./media/step-one-pic-30.png" alt-text="Scherm afbeelding van de website bron met het bestand.":::

Ga naar de [Azure Portal](https://portal.azure.com/#home), selecteer de resource groep en selecteer de toepassing die u hebt gemaakt. Selecteer vervolgens **instellingen**  >  **statische website**. U kunt zien dat statische websites zijn ingeschakeld. Noteer het primaire eind punt, de naam van het index document en het pad naar het fout document.

:::image type="content" source="./media/step-one-pic-31.png" alt-text="Scherm afbeelding waarin de selectie van statische websites wordt weer gegeven.":::

Selecteer onder **Blob service** de optie **Containers**. In de lijst ziet u twee containers die zijn gemaakt: één voor logboeken ( `$logs` ) en één voor de inhoud van uw website ( `$web` ).

:::image type="content" source="./media/step-one-pic-32.png" alt-text="Scherm opname waarin de container configuratie wordt weer gegeven.":::

Als u de `$web` container opent, ziet u de bestanden die u hebt gemaakt in Visual Studio en die zijn geïmplementeerd in Azure. 

:::image type="content" source="./media/step-one-pic-33.png" alt-text="Scherm opname van bestanden die worden geïmplementeerd in Azure.":::

U kunt de toepassing op elk gewenst moment opnieuw implementeren vanuit Visual Studio code.

U bent nu klaar om uw eerste Azure Communication Services-webtoepassing te maken.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Spraakoproep toevoegen aan uw app](../quickstarts/voice-video-calling/getting-started-with-calling.md)

U kunt ook het volgende doen:

- [Chat aan uw app toevoegen](../quickstarts/chat/get-started.md)
- [Tokens voor gebruikers toegang maken](../quickstarts/access-tokens.md)
- [Meer leren over architectuur van client en server](../concepts/client-and-server-architecture.md)
- [Meer leren over verificatie](../concepts/authentication.md)

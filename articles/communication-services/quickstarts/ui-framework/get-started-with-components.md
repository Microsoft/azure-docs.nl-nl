---
title: Aan de slag met de basis onderdelen van het micro interface Framework van Azure Communication Services
titleSuffix: An Azure Communication Services quickstart
description: In deze Quick Start leert u hoe u aan de slag gaat met basis onderdelen van UI Framework
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 6f4a8e8f26e88a73fc73c309ef336813282589f3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103488173"
---
# <a name="quickstart-get-started-with-ui-framework-base-components"></a>Snelstartgids: aan de slag met basis onderdelen van UI Framework

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Ga aan de slag met Azure Communication Services met behulp van het UI-Framework om snel communicatie ervaringen in uw toepassingen te integreren. In deze Quick Start leert u hoe u de basis onderdelen van UI Framework integreert in uw toepassing om communicatie-ervaringen te bouwen. 

UI Framework-onderdelen zijn beschikbaar in twee soorten: basis en samengesteld.

- **Basis onderdelen** vertegenwoordigen discrete communicatie mogelijkheden. ze zijn de basis bouwstenen die kunnen worden gebruikt voor het bouwen van complexe communicatie-ervaringen. 
- **Samengestelde onderdelen** zijn een belang rijke ervaring voor veelvoorkomende communicatie scenario's die zijn gebouwd met **basis onderdelen** als bouw stenen en verpakt om eenvoudig te worden geïntegreerd in toepassingen.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js](https://nodejs.org/) Actieve LTS en onderhouds LTS-versies (node 12 wordt aanbevolen).
- Een actieve Communication Services-resource. [Een Communication Services maken](./../create-communication-resource.md).
- Een token voor gebruikerstoegang voor het instantiëren van de client voor oproepen. Meer informatie over [tokens voor gebruikerstoegang maken en beheren](./../access-tokens.md).

## <a name="setting-up"></a>Instellen

Voor UI-Framework moet een reageer omgeving worden ingesteld. Nu gaan we dat doen. Als u al een reagerende app hebt, kunt u deze sectie overs Laan.

### <a name="set-up-react-app"></a>App reageren instellen

We gebruiken de sjabloon Create-reageert-app voor deze Quick Start. Zie voor meer informatie: [aan de slag met reageren](https://reactjs.org/docs/create-a-new-react-app.html)

```console

npx create-react-app my-app

cd my-app

```

Aan het einde van dit proces moet u een volledige toepassing in de map hebben `my-app` . Voor deze Snelstartgids worden de bestanden in de `src` map gewijzigd.

### <a name="install-the-package"></a>Het pakket installeren

Gebruik de opdracht `npm install` voor het installeren van de clientbibliotheek voor oproepen voor Javascript in Azure Communication Services. Verplaats de meegeleverde tarball (private preview) naar de map My-app.

```console

//For Private Preview install tarball

npm install --save ./{path for tarball}

```

Met de optie `--save` wordt de bibliotheek als een afhankelijkheid in bestand **package.json** vermeld.

### <a name="run-create-react-app"></a>App voor reageren maken uitvoeren

We gaan de installatie voor het maken van een reageren-app testen door uit te voeren:

```console

npm run start   

```

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de client bibliotheek voor de Azure Communication Services-gebruikers interface:

| Naam                                  | Beschrijving                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| Provider| Fluent-UI-provider waarmee ontwikkel aars onderliggende elementen van de gebruikers interface kunnen wijzigen|
| CallingProvider| Aanroepende provider voor het instantiëren van een aanroep. Vereist om extra onderdelen toe te voegen|
| ChatProvider | Chat provider voor het instantiëren van een chat thread. Vereist om extra onderdelen toe te voegen|
| MediaGallery | Basis onderdeel waarin deel nemers en hun externe video-streams worden weer gegeven |
| MediaControls | Basis onderdeel voor het beheren van de oproep, waaronder dempen, video, share scherm |
| ChatThread | Basis onderdeel dat een chat thread weergeeft met typen indicatoren, lees bevestigingen, enzovoort. |
| SendBox | Basis onderdeel dat gebruikers in staat stelt berichten in te voeren die naar de gekoppelde thread worden verzonden|

## <a name="initialize-calling-and-chat-providers-using-azure-communication-services-credentials"></a>Providers voor bellen en chatten initialiseren met Azure Communication Services-referenties

Ga naar de `src` map in `my-app` en zoek naar het bestand `app.js` . Hier gaan we de volgende code neerzetten om de providers voor bellen en chatten te initialiseren. Deze providers zijn verantwoordelijk voor het onderhouden van de context van de gespreks-en chat-ervaringen. U kunt kiezen welke er moet worden gebruikt, afhankelijk van het type communicatie-ervaring dat u bouwt. Als dat nodig is, kunt u beide tegelijk gebruiken. Voor het initialiseren van de onderdelen hebt u een toegangs token opgehaald van Azure Communication Services. Zie [toegangs tokens maken en beheren](./../access-tokens.md)voor meer informatie over het verkrijgen van toegangs tokens.

> [!NOTE]
> De onderdelen genereren geen toegangs tokens, groeps-Id's of thread-Id's. Deze elementen zijn afkomstig van services die de juiste stappen door lopen om deze Id's te genereren en door te geven aan de client toepassing. Zie voor meer informatie: [client-server architectuur](./../../concepts/client-and-server-architecture.md).
> 
> Bijvoorbeeld: de chat provider verwacht dat de `userId` gekoppelde voor de `token` initialisatie hiervan al is toegevoegd aan de die wordt gebruikt `threadId` . Als het token niet is toegevoegd aan de thread-ID, zal de chat provider mislukken. Zie voor meer informatie over chatten: [aan de slag met chat](./../chat/get-started.md)

We gebruiken een Fluent-gebruikers interface thema om het uiterlijk van de toepassing te verbeteren:

`App.js`
```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"
import { mergeThemes, teamsTheme } from '@fluentui/react-northstar';
import { Provider } from '@fluentui/react-northstar/dist/commonjs/components/Provider/Provider';
import { svgIconStyles } from '@fluentui/react-northstar/dist/es/themes/teams/components/SvgIcon/svgIconStyles';
import { svgIconVariables } from '@fluentui/react-northstar/dist/es/themes/teams/components/SvgIcon/svgIconVariables';
import * as siteVariables from '@fluentui/react-northstar/dist/es/themes/teams/siteVariables';

const iconTheme = {
  componentStyles: {
    SvgIcon: svgIconStyles
  },
  componentVariables: {
    SvgIcon: svgIconVariables
  },
  siteVariables
};

function App(props) {

  return (
    <Provider theme={mergeThemes(iconTheme, teamsTheme)}>
        <CallingProvider
        displayName={/*Insert Display Name to be used for the user*/}
        groupId={/*Insert GUID for group call to be joined*/}
        token={/*Insert the Azure Communication Services access token*/}
        refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
        >
            // Add Calling Components Here
        </CallingProvider>

        {/*Note: Make sure that the userId associated to the token has been added to the provided threadId*/}

        <ChatProvider
        token={/*Insert the Azure Communication Services access token*/}
        displayName={/*Insert Display Name to be used for the user*/}
        threadId={/*Insert id for group chat thread to be joined*/}
        endpointUrl={/*Insert the environment URL for the Azure Resource used*/}
        refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
        >
            //  Add Chat Components Here
        </ChatProvider>
    </Provider>
  );
}

export default App;

```

Zodra u deze provider hebt geïnitialiseerd, kunt u uw eigen indeling bouwen met behulp van de basis onderdelen van het interface raamwerk en eventuele extra indelings logica. De provider zorgt voor het initialiseren van alle onderliggende logica en het op de juiste wijze verbinden van de verschillende onderdelen. Vervolgens gebruiken we verschillende basis onderdelen van UI Framework om communicatie-ervaringen te bouwen. U kunt de indeling van deze onderdelen aanpassen en andere aangepaste onderdelen toevoegen die u met hen wilt renderen. 

## <a name="build-ui-framework-calling-component-experiences"></a>UI Framework bouwen onderdeel ervaringen aanroepen

Voor aanroepen gebruiken we de- `MediaGallery` en- `MediaControls` onderdelen. Zie [UI Framework-mogelijkheden](./../../concepts/ui-framework/ui-sdk-features.md)voor meer informatie hierover. Maak een nieuw bestand met de naam in de map om te starten `src` `CallingComponents.js` . Hier wordt een functie component geïnitialiseerd waarmee de basis onderdelen worden opgeslagen in `app.js` . U kunt extra lay-out en stijlen rond de onderdelen toevoegen. 

`CallingComponents.js`
```javascript

import {MediaGallery, MediaControls, MapToCallConfigurationProps, connectFuncsToContext} from "@azure/acs-ui-sdk"

function CallingComponents(props) {

  if (props.isCallInitialized) {props.joinCall()}

  return (
    <div style = {{height: '35rem', width: '30rem', float: 'left'}}>
        <MediaGallery/>
        <MediaControls/>
    </div>
  );
}

export default connectFuncsToContext(CallingComponents, MapToCallConfigurationProps);

```

Onder aan dit bestand zijn de aanroepende onderdelen geëxporteerd met behulp  `connectFuncsToContext` van de methode vanuit het Framework van de gebruikers interface om de aanroepende UI-componenten te verbinden met de onderliggende status met behulp van de toewijzings functie `MapToCallingSetupProps` . Deze methode levert het onderdeel met de bijbehorende eigenschappen, die we gebruiken om de status te controleren en deel te nemen aan de aanroep. Gebruik de `isCallInitialized` eigenschap om te controleren of de `CallAgent` is gereed en vervolgens de `joinCall` methode gebruiken om lid te worden van. UI Framework ondersteunt aangepaste toewijzings functies die moeten worden gebruikt voor scenario's waarin ontwikkel aars willen bepalen hoe gegevens worden gepusht naar de onderdelen.

## <a name="build-ui-framework-chat-component-experiences"></a>Chat onderdeel ervaringen van UI Framework bouwen

Voor chat worden de-en- `ChatThread` `SendBox` onderdelen gebruikt. Zie [UI Framework capabilities](./../../concepts/ui-framework/ui-sdk-features.md)(Engelstalig) voor meer informatie over deze onderdelen. Maak een nieuw bestand met de naam in de map om te starten `src` `ChatComponents.js` . Hier wordt een functie component geïnitialiseerd waarmee de basis onderdelen worden opgeslagen in `app.js` .

`ChatComponents.js`
```javascript

import {ChatThread, SendBox} from '@azure/acs-ui-sdk'

function ChatComponents() {

  return (
    <div style = {{height: '35rem', width: '30rem', float: 'left'}}>
        <ChatThread />
        <SendBox />
    </div >
  );
}

export default ChatComponents;

```

## <a name="add-calling-and-chat-components-to-the-main-application"></a>Onderdelen voor bellen en chatten toevoegen aan de hoofd toepassing

In het `app.js` bestand gaan we de onderdelen nu toevoegen aan de `CallingProvider` en `ChatProvider` zoals hieronder wordt weer gegeven.

`App.js`
```javascript

import ChatComponents from './ChatComponents';
import CallingComponents from './CallingComponents';

<Provider ... >
    <CallingProvider .... >
        <CallingComponents/>
    </CallingProvider>

    <ChatProvider .... >
        <ChatComponents />
    </ChatProvider>
</Provider>

```

## <a name="run-quickstart"></a>Snelstartgids uitvoeren

Gebruik de opdracht om de bovenstaande code uit te voeren:

```console

npm run start   

```

Als u de mogelijkheden volledig wilt testen, moet u een tweede client hebben met de functie voor bellen en chatten om lid te worden van de aanroep-en chat-thread. Bekijk het voor beeld van een [held](./../../samples/calling-hero-sample.md) -en [Chat Room](./../../samples/chat-hero-sample.md) als mogelijke opties.

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Communication Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd. Meer informatie over het [opschonen van resources](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Probeer samengestelde UI Framework-onderdelen](./get-started-with-composites.md)

Zie de volgende resources voor meer informatie:
- [Overzicht van UI Framework](../../concepts/ui-framework/ui-sdk-overview.md)
- [Mogelijkheden van UI-Framework](./../../concepts/ui-framework/ui-sdk-features.md)

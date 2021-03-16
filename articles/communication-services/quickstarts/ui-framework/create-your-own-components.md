---
title: Uw eigen UI Framework-onderdeel maken
titleSuffix: An Azure Communication Services quickstart
description: In deze Quick Start leert u hoe u een aangepast onderdeel bouwt dat compatibel is met het UI-Framework
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 093fcfd95d291d959ed49cc39a227a99f14a0383
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488241"
---
# <a name="quickstart-create-your-own-ui-framework-component"></a>Snelstartgids: uw eigen UI Framework-onderdeel maken

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Ga aan de slag met Azure Communication Services met behulp van het UI-Framework om snel communicatie ervaringen in uw toepassingen te integreren.

In deze Quick Start leert u hoe u uw eigen componenten maakt met behulp van de vooraf gedefinieerde status interface die wordt geboden door UI Framework. Deze aanpak is ideaal voor ontwikkel aars die meer aanpassing nodig hebben en hun eigen ontwerp assets willen gebruiken voor de ervaring. 

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js](https://nodejs.org/) Actieve LTS en onderhouds LTS-versies (node 12 wordt aanbevolen).
- Een actieve Communication Services-resource. [Een Communication Services maken](./../create-communication-resource.md).
- Een token voor gebruikerstoegang voor het instantiëren van de client voor oproepen. Meer informatie over [tokens voor gebruikerstoegang maken en beheren](./../access-tokens.md).

Voor UI-Framework moet een reagerende omgeving worden ingesteld. Nu gaan we dat doen. Als u al een reagerende app hebt, kunt u deze sectie overs Laan.

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
| CallingProvider| Aanroepende provider voor het instantiëren van een aanroep. Vereist om basis onderdelen toe te voegen|
| ChatProvider | Chat provider voor het instantiëren van een chat thread. Vereist om basis onderdelen toe te voegen|
| connectFuncsToContext | Methode voor het verbinden van UI Framework-onderdelen met onderliggende providers met mappers |
| MapToChatMessageProps | Chat bericht gegevenslaag Mapper dat onderdelen bevat met de chat-bericht eigenschappen |


## <a name="initialize-chat-providers-using-azure-communication-services-credentials"></a>Chat providers initialiseren met Azure Communication Services-referenties

Voor deze Snelstartgids gebruiken we chat als voor beeld voor meer informatie over het aanroepen van de Snelstartgids voor [basis componenten Snelstartgids](./get-started-with-components.md) en [samengestelde onderdelen](./get-started-with-composites.md).

Ga naar de `src` map in `my-app` en zoek naar het bestand `app.js` . Hier gaan we de volgende code verwijderen om onze chat provider te initialiseren. Deze provider is verantwoordelijk voor het onderhouden van de context van de gespreks-en chat-ervaringen. Voor het initialiseren van de onderdelen hebt u een toegangs token opgehaald van Azure Communication Services. Zie voor meer informatie over het ophalen van een toegangs token: [toegangs tokens maken en beheren](./../access-tokens.md).

UI Framework-onderdelen volgen dezelfde algemene architectuur voor de rest van de service. De onderdelen genereren geen toegangs tokens, groeps-Id's of thread-Id's. Deze elementen zijn afkomstig van services die de juiste stappen door lopen om deze Id's te genereren en door te geven aan de client toepassing. Zie [client server architecture](./../../concepts/client-and-server-architecture.md)(Engelstalig) voor meer informatie.

`App.js`
```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"

function App(props) {

  return (
    <ChatProvider
    token={/*Insert the Azure Communication Services access token*/}
    userId={/*Insert the Azure Communication Services user id*/}
    displayName={/*Insert Display Name to be used for the user*/}
    threadId={/*Insert id for group chat thread to be joined*/}
    endpointUrl={/*Insert the environment URL for the Azure Resource used*/}
    refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
    >
        //  Add Chat Components Here
    </ChatProvider>
  );
}

export default App;

```

Zodra u deze provider hebt geïnitialiseerd, kunt u uw eigen indeling bouwen met behulp van UI Framework component en eventuele extra indelings logica. De provider zorgt voor het initialiseren van alle onderliggende logica en het op de juiste wijze verbinden van de verschillende onderdelen. Vervolgens maken we een aangepast onderdeel met behulp van de interface van het micro soft-Framework voor verbinding met onze chat provider.


## <a name="create-a-custom-component-using-mappers"></a>Een aangepast onderdeel maken met mappers

We beginnen met het maken van een nieuw bestand met `SimpleChatThread.js` de naam waarin we het onderdeel zullen maken. We gaan nu de onderdelen van de UI-Framework importeren die we nodig hebben. Hier zullen we gebruikmaken van de standaard-HTML en reageren op het maken van een volledig aangepast onderdeel voor een eenvoudige chat-thread. Met de `connectFuncsToContext` -methode zullen we de `MapToChatMessageProps` Mapper gebruiken om de eigenschappen van aangepaste onderdelen toe te wijzen  `SimpleChatThread` . Met deze eigenschappen krijgt u toegang tot de chat berichten die worden verzonden en ontvangen om ze te vullen met onze eenvoudige thread.

`SimpleChatThread.js`
```javascript

import {connectFuncsToContext, MapToChatMessageProps} from "@azure/acs-ui-sdk"

function SimpleChatThread(props) {

    return (
        <div>
            {props.chatMessages?.map((message) => (
                <div key={message.id ?? message.clientMessageId}> {`${message.senderDisplayName}: ${message.content}`}</div>
            ))}
        </div>
    );
}

export default connectFuncsToContext(SimpleChatThread, MapToChatMessageProps);

```

## <a name="add-your-custom-component-to-your-application"></a>Uw aangepaste onderdeel toevoegen aan uw toepassing

Nu we ons aangepaste onderdeel gereed hebben, zullen we het importeren en toevoegen aan onze lay-out.

```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"
import SimpleChatThread from "./SimpleChatThread"

function App(props) {

  return (
        <ChatProvider ... >
            <SimpleChatThread />
        </ChatProvider>
  );
}

export default App;

```

## <a name="run-quickstart"></a>Snelstartgids uitvoeren

Als u de bovenstaande code wilt uitvoeren, gebruikt u de opdracht:

```console

npm run start   

```

Als u de mogelijkheden volledig wilt testen, hebt u een tweede client met chat functionaliteit nodig om berichten te verzenden die worden ontvangen door onze eenvoudige chat-thread. Bekijk het voor beeld van een [held](./../../samples/calling-hero-sample.md) -en [Chat Room](./../../samples/chat-hero-sample.md) als mogelijke opties.

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Communication Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd. Meer informatie over het [opschonen van resources](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Probeer samengestelde UI Framework-onderdelen](./get-started-with-composites.md)

Zie de volgende resources voor meer informatie:
- [Overzicht van UI Framework](../../concepts/ui-framework/ui-sdk-overview.md)
- [Mogelijkheden van UI-Framework](./../../concepts/ui-framework/ui-sdk-features.md)
- [Quick start voor UI Framework-basis onderdelen](./get-started-with-components.md)

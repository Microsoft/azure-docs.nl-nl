---
title: Aan de slag met de gecombineerde onderdelen van de Azure Communication Services UI Framework SDK
titleSuffix: An Azure Communication Services quickstart
description: In deze Quick Start leert u hoe u aan de slag gaat met samengestelde UI Framework-onderdelen
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 7356fb90914e948b6a74a478ce1e19722b224346
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103488054"
---
# <a name="quickstart-get-started-with-ui-framework-composite-components"></a>Snelstartgids: aan de slag met samengestelde UI Framework-onderdelen

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Ga aan de slag met Azure Communication Services met behulp van het UI-Framework om snel communicatie ervaringen in uw toepassingen te integreren. In deze Quick Start leert u hoe u in uw toepassing de samengestelde onderdelen van UI Framework integreert om communicatie ervaringen te bouwen.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js](https://nodejs.org/) Actieve LTS en onderhouds LTS-versies (node 12 wordt aanbevolen).
- Een actieve Communication Services-resource. [Een Communication Services maken](./../create-communication-resource.md).
- Een token voor gebruikers toegang om de samen stelling van de aanroep te instantiëren. Meer informatie over [tokens voor gebruikerstoegang maken en beheren](./../access-tokens.md).

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

//Private Preview install tarball

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
| GroupCall | Samengestelde component waarmee een groeps ervaring wordt weer gegeven met de galerie met deel nemers en besturings elementen. |
| GroupChat | Samengestelde component waarmee een groeps ervaring wordt weer gegeven met de chat-thread en-invoer |


## <a name="initialize-group-call-and-group-chat-composite-components"></a>Samengestelde onderdelen van groeps aanroep-en groeps functie initialiseren

Ga naar de `src` map in `my-app` en zoek naar het bestand `app.js` . Hier gaan we de volgende code verwijderen om de samengestelde onderdelen voor groeps-chat en aanroepen te initialiseren. U kunt kiezen welke er moet worden gebruikt, afhankelijk van het type communicatie-ervaring dat u bouwt. Als dat nodig is, kunt u beide tegelijk gebruiken. Voor het initialiseren van de onderdelen hebt u een toegangs token opgehaald van Azure Communication Services. Zie [tokens voor gebruikers toegang maken en beheren](./../access-tokens.md)voor meer informatie over het ophalen van toegangs tokens.

> [!NOTE]
> De onderdelen genereren geen toegangs tokens, groeps-Id's of thread-Id's. Deze elementen zijn afkomstig van services die de juiste stappen door lopen om deze Id's te genereren en door te geven aan de client toepassing. Zie voor meer informatie: [client-server architectuur](./../../concepts/client-and-server-architecture.md).
> 
> Bijvoorbeeld: de combi natie van groeps chatten verwacht dat de `userId` gekoppelde voor de `token` initialisatie hiervan al deel uitmaakte `threadId` van de waarde die wordt gebruikt. Als het token niet is toegevoegd aan de thread-ID, mislukt de combi natie van groeps chatten. Zie voor meer informatie over chatten: [aan de slag met chat](./../chat/get-started.md)


`App.js`
```javascript

import {GroupCall, GroupChat} from "@azure/acs-ui-sdk"

function App(){

    return(<>
        {/* Example styling provided, developers can provide their own styling to position and resize components */}
        <div style={{height: "35rem", width: "50rem", float: "left"}}>
            <GroupCall
                displayName={DISPLAY_NAME} //Required, Display name for the user entering the call
                token={TOKEN} // Required, Azure Communication Services access token retrieved from authentication service
                refreshTokenCallback={CALLBACK} //Optional, Callback to refresh the token in case it expires
                groupId={GROUPID} //Required, Id for group call that will be joined. (GUID)
                onEndCall = { () => {
                    //Optional, Action to be performed when the call ends
                }}
            />
        </div>

        {/*Note: Make sure that the userId associated to the token has been added to the provided threadId*/}
        {/* Example styling provided, developers can provide their own styling to position and resize components */}
        <div style={{height: "35rem", width: "30rem", float: "left"}}>
            <GroupChat 
                displayName={DISPLAY_NAME} //Required, Display name for the user entering the call
                token={TOKEN} // Required, Azure Communication Services access token retrieved from authentication service
                threadId={THREADID} //Required, Id for group chat thread that will be joined.
                endpointUrl={ENDPOINT_URL} //Required, URL for Azure endpoint being used for Azure Communication Services
                onRenderAvatar = { (acsId) => {
                    //Optional, function to override the avatar image on the chat thread. Function receives one parameters for the Azure Communication Services Identity. Must return a React element.
                }}
                refreshToken = { () => {
                    //Optional, function to refresh the access token in case it expires
                }}
                options = {{
                    //Optional, options to define chat behavior
                    sendBoxMaxLength: number | undefined //Optional, Limit the max send box length based on viewport size change.
                }}
            />
        </div>
    </>);
}

export default App;

```

## <a name="run-quickstart"></a>Snelstartgids uitvoeren

Als u de bovenstaande code wilt uitvoeren, gebruikt u de opdracht:

```console

npm run start 

```

Als u de mogelijkheden volledig wilt testen, moet u een tweede client hebben met de functie voor bellen en chatten om lid te worden van de aanroep-en chat-thread. Bekijk het voor beeld van een [held](./../../samples/calling-hero-sample.md) -en [Chat Room](./../../samples/chat-hero-sample.md) als mogelijke opties.

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Communication Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd. Meer informatie over het [opschonen van resources](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [De basis onderdelen van UI Framework uitproberen](./get-started-with-components.md)

Zie de volgende resources voor meer informatie:
- [Overzicht van UI Framework](../../concepts/ui-framework/ui-sdk-overview.md)
- [Mogelijkheden van UI-Framework](./../../concepts/ui-framework/ui-sdk-features.md)

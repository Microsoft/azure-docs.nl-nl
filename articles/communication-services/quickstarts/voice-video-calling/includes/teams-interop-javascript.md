---
title: Quickstart - Deelnemen aan een vergadering in Teams
author: chpalm
ms.author: mikben
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: ab13e02c9154dbfc1bd28872f1bd6f993019a604
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94915285"
---
## <a name="prerequisites"></a>Vereisten

- Een werkende [App voor bellen met Communication Services](../getting-started-with-calling.md).
- Een [Teams implementatie](/deployoffice/teams-install).

## <a name="enable-teams-interoperability"></a>Interoperabiliteit met Teams inschakelen

De interoperabiliteitsfunctie van Teams bevindt zich op dit moment in de beperkte preview. Als u deze functie wilt inschakelen voor uw Communication Services-resource, kunt u [acsfeedback@microsoft.com](mailto:acsfeedback@microsoft.com) e-mailen met:

1. De abonnements-id van het Azure-abonnement dat uw Communication Services-resource bevat.
2. De tenant-id van uw Teams. De eenvoudigste manier om dit te verkrijgen is door [een koppeling naar het Team te verkrijgen en te delen](https://support.microsoft.com/office/create-a-link-or-a-code-for-joining-a-team-11b0de3b-9288-4cb4-bc49-795e7028296f#:~:text=Create%20a%20link%20If%20you%E2%80%99re%20a%20team%20owner%2C,link%20into%20any%20browser%20to%20join%20the%20team).

U moet lid zijn van de organisatie die eigenaar is van beide entiteiten om deze functie te kunnen gebruiken.

## <a name="add-the-teams-ui-controls"></a>De besturingselementen voor de gebruikersinterface van Teams toevoegen

Een nieuw tekstvak en een nieuwe knop in uw HTML toevoegen. Het tekstvak wordt gebruikt voor het invoeren van de context van de Teams-vergadering en de knop wordt gebruikt om deel te nemen aan de opgegeven vergadering:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px;"
    />
    <input 
      id="teams-id-input"
      type="text"
      placeholder="Teams meeting context"
      style="margin-bottom:1em; width: 300px;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
         <button id="meeting-button" type="button" disabled="false">
        Join Teams Meeting
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>De besturingselementen voor de gebruikersinterface van Teams inschakelen

Nu kunnen we de knop **Deelnemen aan Teams-vergadering** aan de code verbinden waardoor aan de opgegeven Teams-vergadering wordt deelgenomen:

```javascript
meetingButton.addEventListener("click", () => {
    
    // set display name in the meeting
    callAgent.updateDisplayName('YOUR_NAME');
    
    // join with meeting link
    call = callAgent.join({meetingLink: 'MEETING_LINK'}, {});

     // join with meeting coordinates
     call = callAgent.join({
        threadId: 'CHAT_THREAD_ID',
        organizerId: 'ORGANIZER_ID',
        tenantId: 'TENANT_ID',
        messageId: 'MESSAGE_ID'
    }, {})
    
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
    meetingButton.disabled = true;
});
```

## <a name="get-the-meeting-context"></a>De context van de vergadering ophalen

De context van Teams kan worden opgehaald met behulp van Graph API’s. Dit wordt beschreven in [Graph-documentatie](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta).

U kunt ook de vereiste gegevens van de vergadering ophalen van de URL **Deelnemen aan vergadering** in de uitnodiging voor de vergadering zelf.

## <a name="run-the-code"></a>De code uitvoeren

Webpack-gebruikers kunnen de `webpack-dev-server` gebruiken om uw app te bouwen en uit te voeren. Voer de volgende opdracht uit om de toepassingshost op een lokale webserver te bundelen:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Open uw browser en ga naar http://localhost:8080/. U ziet nu het volgende:

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="Schermopname van de voltooide JavaScript-toepassing.":::

Voeg de context van Teams in het tekstvak in en druk op *Deelnemen aan Teams-vergadering* om deel te nemen aan de Teams-vergadering vanuit uw Communication Services-toepassing.
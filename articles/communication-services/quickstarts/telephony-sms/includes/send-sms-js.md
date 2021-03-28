---
title: bestand opnemen
description: bestand opnemen
services: azure-communication-services
author: bertong
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/11/2021
ms.topic: include
ms.custom: include file
ms.author: bertong
ms.openlocfilehash: 8fe8b853fe07af40603950a61c0dd2a1df74d14e
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644384"
---
Ga aan de slag met Azure Communication Services met de SMS SDK voor communicatie services java script om SMS-berichten te verzenden.

Voor het voltooien van deze quickstart worden kosten van een paar dollarcent of minder in rekening gebracht bij uw Azure-account.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-js-pr/tree/feature/communication/sdk/communication/communication-sms) | [Package (NPM)](https://www.npmjs.com/package/@azure/communication-sms) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js](https://nodejs.org/), Active LTS en Maintenance LTS-versies (8.11.1 en 10.14.1 aanbevolen).
- Een actieve Communication Services-resource en verbindingsreeks. [Een Communication Services-resource maken](../../create-communication-resource.md).
- Een telefoonnummer met sms-functionaliteit. [Een telefoonnummer aanvragen](../get-phone-number.md).

### <a name="prerequisite-check"></a>Controle van vereisten

- Voer `node --version` in een terminal of opdrachtvenster uit om te controleren of Node.js is geïnstalleerd.
- Als u de telefoonnummers wilt weergeven die zijn gekoppeld aan uw Communication Services-resource, meldt u zich aan bij [Azure Portal](https://portal.azure.com/), zoekt u uw Communication Services-resource en opent u het tabblad **telefoonnummers** vanuit het navigatiedeelvenster aan de linkerkant.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Open eerst uw terminal of opdrachtvenster, maak een nieuwe map voor uw app en navigeer daar naartoe.

```console
mkdir sms-quickstart && cd sms-quickstart
```

Voer `npm init -y` uit om een **package.json**-bestand te maken met de standaardinstellingen.

```console
npm init -y
```

Gebruik een teksteditor om een bestand met de naam **send-sms.js** te maken in de hoofdmap van het project. In de volgende secties voegt u alle broncode voor deze quickstart toe aan dit bestand.

### <a name="install-the-package"></a>Het pakket installeren

Gebruik de `npm install` opdracht voor het installeren van de Azure Communication Services SMS SDK voor Java script.

```console
npm install @azure/communication-sms --save
```

De optie `--save` geeft de bibliotheek weer als afhankelijkheid in het **package.json**-bestand.

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de SMS SDK van Azure Communication Services voor Node.js.

| Naam                                  | Beschrijving                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Deze klasse is vereist voor alle sms-functionaliteit. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om sms-berichten te verzenden. |
| SmsSendRequest | Deze interface is het model voor het bouwen van de sms-aanvraag (bijvoorbeeld: configureer de telefoonnummers van de afzender en ontvanger en de sms-inhoud). |
| SmsSendOptions | Deze interface biedt opties voor het configureren van leveringsrapporten. Als `enableDeliveryReport` is ingesteld op `true` , wordt een gebeurtenis verzonden wanneer de levering is geslaagd. |
| SmsSendResult               | Deze klasse bevat het resultaat van de SMS-service.                                          |

## <a name="authenticate-the-client"></a>De client verifiëren

Importeer de **SmsClient** uit de SDK en instantie deze met uw Connection String. Met de onderstaande code wordt de verbindingsreeks voor de resource opgehaald uit een omgevingsvariabele met de naam `COMMUNICATION_SERVICES_CONNECTION_STRING`. Meer informatie over het [beheren van de Connection String van uw resource](../../create-communication-resource.md#store-your-connection-string).

Maak en open een bestand met de naam **send-sms.js** en voeg de volgende code toe:

```javascript
const { SmsClient } = require('@azure/communication-sms');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
```

## <a name="send-a-1n-sms-message"></a>Een SMS-bericht van 1: N verzenden

Als u een SMS-bericht naar een lijst met ontvangers wilt verzenden, roept u de `send` functie aan vanuit de SmsClient met een lijst met telefoon nummers van ontvangers (als u een bericht wilt verzenden naar één ontvanger, neemt u slechts één nummer op in de lijst). Voeg deze code toe aan het einde van **send-sms.js**:

```javascript
async function main() {
  const sendResults = await smsClient.send({
    from: "<from-phone-number>",
    to: ["<to-phone-number-1>", "<to-phone-number-2>"],
    message: "Hello World 👋🏻 via SMS"
  });

  // individual messages can encounter errors during sending
  // use the "successful" property to verify
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
}

main();
```
Vervang door `<from-phone-number>` een SMS-telefoon nummer dat is gekoppeld aan uw communicatie Services-bron en `<to-phone-number-1>` en `<to-phone-number-2>` met de telefoon nummer (s) waarnaar u een bericht wilt verzenden.

> [!WARNING]
> Telefoonnummers moeten worden opgegeven in de internationale standaardindeling E.164. (bijvoorbeeld: + 14255550123).

## <a name="send-a-1n-sms-message-with-options"></a>Een SMS-bericht van 1: N verzenden met opties

U kunt ook een Options-object door geven om op te geven of het leverings rapport moet worden ingeschakeld en aangepaste labels moeten worden ingesteld.

```javascript

async function main() {
  const sendResults = await smsClient.send({
    from: "<from-phone-number>",
    to: ["<to-phone-number-1>", "<to-phone-number-2>"],
    message: "Weekly Promotion!"
  }, {
    //Optional parameters
    enableDeliveryReport: true,
    tag: "marketing"
  });

  // individual messages can encounter errors during sending
  // use the "successful" property to verify
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
}

main();
```

Vervang door `<from-phone-number>` een SMS-telefoon nummer dat is gekoppeld aan uw communicatie Services-bron en `<to-phone-number-1>` en `<to-phone-number-2>` met een telefoon nummer (s) waarnaar u een bericht wilt verzenden.

> [!WARNING]
> Telefoonnummers moeten worden opgegeven in de internationale standaardindeling E.164. (bijvoorbeeld: + 14255550123).

De parameter `enableDeliveryReport` is een optionele parameter die u kunt gebruiken voor het configureren van leveringsrapporten. Dit is handig voor scenario's waarin u gebeurtenissen wilt verzenden wanneer sms-berichten worden bezorgd. Raadpleeg de quickstart [Sms-gebeurtenissen verwerken](../handle-sms-events.md) voor het configureren van leveringsrapporten voor uw sms-berichten.
`tag` is een optionele para meter die u kunt gebruiken om een label toe te passen op het leverings rapport.

## <a name="run-the-code"></a>De code uitvoeren

Gebruik de opdracht `node` om de code uit te voeren die u aan het bestand **send-sms.js** hebt toegevoegd.

```console

node ./send-sms.js

```

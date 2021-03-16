---
title: bestand opnemen
description: bestand opnemen
services: azure-communication-services
author: dademath
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: dademath
ms.openlocfilehash: fc20396053dee32ac7976139a634b4592389ab5f
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488297"
---
Ga aan de slag met Azure Communication Services door de sms-clientbibliotheek in JavaScript van Communications Services te gebruiken om sms-berichten te verzenden.

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

Gebruik de `npm install`-opdracht voor het installeren van de sms-clientbibliotheek voor Javascript in Azure Communication Services.

```console
npm install @azure/communication-sms --save
```

De optie `--save` geeft de bibliotheek weer als afhankelijkheid in het **package.json**-bestand.

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de sms-clientbibliotheek voor Node.js in Azure Communication Services.

| Naam                                  | Beschrijving                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Deze klasse is vereist voor alle sms-functionaliteit. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om sms-berichten te verzenden. |
| SendSmsOptions | Deze interface biedt opties voor het configureren van leveringsrapporten. Als `enable_delivery_report` op `true` is ingesteld, wordt een gebeurtenis verzonden wanneer de levering is geslaagd. |
| SendMessageRequest | Deze interface is het model voor het bouwen van de sms-aanvraag (bijvoorbeeld: configureer de telefoonnummers van de afzender en ontvanger en de sms-inhoud). |

## <a name="authenticate-the-client"></a>De client verifiëren

Importeer de **SmsClient** uit de clientbibliotheek en instantieer deze met uw verbindingsreeks. Met de onderstaande code wordt de verbindingsreeks voor de resource opgehaald uit een omgevingsvariabele met de naam `COMMUNICATION_SERVICES_CONNECTION_STRING`. Meer informatie over het [beheren van de verbindingsreeks van uw resource](../../create-communication-resource.md#store-your-connection-string).

Voeg de volgende code toe aan het bestand **send-sms.js**:

```javascript
const { SmsClient } = require('@azure/communication-sms');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
```

## <a name="send-an-sms-message"></a>Een sms-bericht verzenden

Een sms-bericht verzenden door de `send`-methode aan te roepen. Voeg deze code toe aan het einde van **send-sms.js**:

```javascript
async function main() {
  await smsClient.send({
    from: "<leased-phone-number>",
    to: ["<to-phone-number>"],
    message: "Hello World 👋🏻 via Sms"
  }, {
    enableDeliveryReport: true //Optional parameter
  });
}

main();
```

U moet `<leased-phone-number>` vervangen door een telefoonnummer met sms-functionaliteit dat is gekoppeld aan uw Communication Services-resources en `<to-phone-number>` met het telefoonnummer waarnaar u een bericht wilt verzenden.

De parameter `enableDeliveryReport` is een optionele parameter die u kunt gebruiken voor het configureren van leveringsrapporten. Dit is handig voor scenario's waarin u gebeurtenissen wilt verzenden wanneer sms-berichten worden bezorgd. Raadpleeg de quickstart [Sms-gebeurtenissen verwerken](../handle-sms-events.md) voor het configureren van leveringsrapporten voor uw sms-berichten.

## <a name="run-the-code"></a>De code uitvoeren

Gebruik de opdracht `node` om de code uit te voeren die u aan het bestand **send-sms.js** hebt toegevoegd.

```console

node ./send-sms.js

```

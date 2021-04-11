---
ms.openlocfilehash: 956c92c5c020f892b8148e9d43d403b1099fbdba
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112854"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js](https://nodejs.org/), Active LTS en Maintenance LTS-versies (8.11.1 en 10.14.1 aanbevolen).
- Een actieve Communication Services-resource en verbindingsreeks. [Een Communication Services-resource maken](../../create-communication-resource.md).

### <a name="prerequisite-check"></a>Controle van vereisten

- Voer `node --version` in een terminal of opdrachtvenster uit om te controleren of Node.js is geïnstalleerd.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Open eerst uw terminal of opdrachtvenster, maak een nieuwe map voor uw app en navigeer daar naartoe.

```console
mkdir phone-numbers-quickstart && cd phone-numbers-quickstart
```

Voer `npm init -y` uit om een **package.json**-bestand te maken met de standaardinstellingen.

```console
npm init -y
```

Maak een bestand met de naam **phone-numbers-quickstart.js** in de hoofdmap van de map die u zojuist hebt gemaakt. Voeg het volgende code fragment toe:

```javascript
async function main() {
    // quickstart code will here
}

main();
```

### <a name="install-the-package"></a>Het pakket installeren

Gebruik de `npm install` opdracht voor het installeren van de Azure Communication Services-client bibliotheek voor telefoon nummers voor Java script.

```console
npm install @azure/communication-phone-numbers --save
```

Met deze `--save` optie wordt de bibliotheek toegevoegd als een afhankelijkheid in uw **package.jsvoor** het bestand.

## <a name="authenticate-the-client"></a>De client verifiëren

Importeer de **PhoneNumbersClient** uit de client bibliotheek en instantie deze met uw Connection String. Met de onderstaande code wordt de verbindingsreeks voor de resource opgehaald uit een omgevingsvariabele met de naam `COMMUNICATION_SERVICES_CONNECTION_STRING`. Meer informatie over het [beheren van de Connection String van uw resource](../../create-communication-resource.md#store-your-connection-string).

Voeg de volgende code toe boven aan **phone-numbers-quickstart.js**:

```javascript
const { PhoneNumbersClient } = require('@azure/communication-phone-numbers');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the phone numbers client
const phoneNumbersClient = new PhoneNumbersClient(connectionString);
```

## <a name="manage-phone-numbers"></a>Telefoon nummers beheren

### <a name="search-for-available-phone-numbers"></a>Zoeken naar beschik bare telefoon nummers

Als u telefoon nummers wilt kopen, moet u eerst zoeken naar beschik bare telefoon nummers. Als u wilt zoeken naar telefoon nummers, geeft u het netnummer, het type toewijzing, de mogelijkheden voor het [telefoon nummer](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services), het [telefoon nummer](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)en de hoeveelheid op. Houd er rekening mee dat voor het type gratis telefoon nummer de area code optioneel is.

Voeg het volgende code fragment toe aan uw `main` functie:

```javascript
/**
 * Search for Available Phone Number
 */

// Create search request
const searchRequest = {
    countryCode: "US",
    phoneNumberType: "tollFree",
    assignmentType: "application",
    capabilities: {
      sms: "outbound",
      calling: "none"
    },
    areaCode: "833",
    quantity: 1
  };

const searchPoller = await phoneNumbersClient.beginSearchAvailablePhoneNumbers(searchRequest);

// The search is underway. Wait to receive searchId.
const { searchId, phoneNumbers } = searchPoller.pollUntilDone();
const phoneNumber = phoneNumbers[0];

console.log(`Found phone number: ${phoneNumber}`);
console.log(`searchId: ${searchId}`);
```

### <a name="purchase-phone-number"></a>Telefoon nummer kopen

Het resultaat van het zoeken naar telefoon nummers is een `PhoneNumberSearchResult` . Deze bevat een `searchId` die kan worden door gegeven aan de aankoop nummers API om de getallen in de zoek opdracht op te halen. Houd er rekening mee dat het aanroepen van de telefoon nummers van de kopen-API ertoe leidt dat uw Azure-account in rekening wordt gebracht.

Voeg het volgende code fragment toe aan uw `main` functie:

```javascript
/**
 * Purchase Phone Number
 */

const purchasePoller = await phoneNumbersClient.beginPurchasePhoneNumbers(searchId);

// Purchase is underway.
await purchasePoller.pollUntilDone();
console.log(`Successfully purchased ${phoneNumber}`);
```

### <a name="update-phone-number-capabilities"></a>Mogelijkheden voor telefoon nummer bijwerken

Wanneer u nu een telefoon nummer hebt gekocht, voegt u de volgende code toe om de mogelijkheden bij te werken:

```javascript
/**
 * Update Phone Number Capabilities
 */

// Create update request.
// This will update phone number to send and receive sms, but only send calls.
const updateRequest = {
  sms: "inbound+outbound",
  calling: "outbound"
};

const updatePoller = await phoneNumbersClient.beginUpdatePhoneNumberCapabilities(
  phoneNumber,
  updateRequest
);

// Update is underway.
await updatePoller.pollUntilDone();
console.log("Phone number updated successfully.");
```

### <a name="get-purchased-phone-numbers"></a>Aankoop nummer (s) ophalen

Na een aankoop nummer kunt u het ophalen van de client. Voeg de volgende code toe aan de `main` functie om het telefoon nummer op te halen dat u zojuist hebt aangeschaft:

```javascript
/**
 * Get Purchased Phone Number
 */

const { capabilities } = await phoneNumbersClient.getPurchasedPhoneNumber(phoneNumber);
console.log(`These capabilities: ${capabilities}, should be the same as these: ${updateRequest}.`);
```

U kunt ook alle gekochte telefoon nummers ophalen.

```javascript
const phoneNumbers = await phoneNumbersClient.listPurchasedPhoneNumbers();

for await (const purchasedPhoneNumber of phoneNumbers) {
  console.log(`Phone number: ${purchasedPhoneNumber.phoneNumber}, country code: ${purchasedPhoneNumber.countryCode}.`);
}
```

### <a name="release-phone-number"></a>Telefoon nummer van release

U kunt nu het aangeschafte telefoon nummer vrijgeven. Voeg het volgende code fragment toe aan uw `main` functie:

```javascript
/**
 * Release Purchased Phone Number
 */

const releasePoller = await client.beginReleasePhoneNumber(phoneNumber);

// Release is underway.
await releasePoller.pollUntilDone();
console.log("Successfully release phone number.");
```

## <a name="run-the-code"></a>De code uitvoeren

Gebruik de `node` opdracht om de code uit te voeren die u aan het **phone-numbers-quickstart.js** -bestand hebt toegevoegd.

```console
node phone-numbers-quickstart.js
```
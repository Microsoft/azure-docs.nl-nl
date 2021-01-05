---
title: Quickstart - Azure Key Vault-clientbibliotheek voor sleutels voor JavaScript (versie 4)
description: Meer informatie over het maken, ophalen en verwijderen van sleutels van een Azure-sleutelkluis met behulp van de clientbibliotheek voor JavaScript
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 1b23fa9f9cbf7b385a04835149b5d53cc42351eb
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825896"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-javascript-version-4"></a>Quickstart: Azure Key Vault-clientbibliotheek voor sleutels voor JavaScript (versie 4)

Ga aan de slag met de Azure Key Vault-clientbibliotheek voor sleutels voor JavaScript. [Azure Key Vault](../general/overview.md) is een cloudservice die werkt als een beveiligd archief voor cryptografische sleutels. U kunt veilig sleutels, wachtwoorden, certificaten en andere geheime informatie opslaan. Azure-sleutelkluizen kunnen worden gemaakt en beheerd via Azure Portal. In deze quickstart krijgt u meer informatie over het maken, ophalen en verwijderen van sleutels van een Azure-sleutelkluis met behulp van de clientbibliotheek voor sleutels voor JavaScript

Resources voor de Key Vault-clientbibliotheek:

[API-referentiedocumentatie](/javascript/api/overview/azure/key-vault-index) | [Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [Package (npm)](https://www.npmjs.com/package/@azure/keyvault-keys)

Zie voor meer informatie over Key Vault en sleutels:
- [Overzicht van Key Vault](../general/overview.md)
- [Overzicht van sleutels](about-keys.md).

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement (u kunt [een gratis abonnement maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)).
- De huidige versie van [Node.js](https://nodejs.org) voor uw besturingssysteem.
- [Azure-CLI](/cli/azure/install-azure-cli)
- Een sleutelkluis: u kunt er een maken met behulp van [Azure Portal](../general/quick-create-portal.md), [Azure CLI](../general/quick-create-cli.md) of [Azure PowerShell](../general/quick-create-powershell.md)

In deze quickstart wordt ervan uitgegaan dat u [Azure CLI](/cli/azure/install-azure-cli) uitvoert.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

1. Voer de opdracht `login` uit.

    ```azurecli-interactive
    az login
    ```

    Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een Azure-aanmeldingspagina geladen.

    Als dat niet het geval is, opent u een browserpagina op [https://aka.ms/devicelogin](https://aka.ms/devicelogin) en voert u de autorisatiecode in die wordt weergegeven in uw terminal.

2. Meldt u zich in de browser aan met uw accountreferenties.

## <a name="create-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak vervolgens een Node.js-toepassing die kan worden geïmplementeerd in de cloud. 

1. Maak in een opdrachtshell een map met de naam `key-vault-node-app`:

```azurecli
mkdir key-vault-node-app
```

1. Ga naar de map van de zojuist gemaakte *key-vault-node-app* en voer de opdracht 'init' uit om het node-project te initialiseren:

```azurecli
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>Key Vault-pakketten installeren

Installeer vanuit het consolevenster de Azure Key Vault-[sleutelbibliotheek](https://www.npmjs.com/package/@azure/keyvault-keys) voor Node.js.

```azurecli
npm install @azure/keyvault-keys
```

Installeer het [azure.identity](https://www.npmjs.com/package/@azure/identity)-pakket om te verifiëren bij een Key Vault

```azurecli
npm install @azure/identity
```

## <a name="set-environment-variables"></a>Omgevingsvariabelen instellen

In deze applicatie wordt de naam van de sleutelkluis gebruikt als een omgevingsvariabele met de naam `KEY_VAULT_NAME`.

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME=<your-key-vault-name>
```

macOS of Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="grant-access-to-your-key-vault"></a>Toegang verlenen tot uw sleutelkluis

Maak toegangsbeleid voor de sleutelkluis waarmee sleutelmachtigingen worden verleend aan uw gebruikersaccount

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --key-permissions delete get list create purge
```

## <a name="code-examples"></a>Codevoorbeelden

In de onderstaande codevoorbeelden ziet u hoe u een client maakt, een sleutel instelt, een sleutel ophaalt en een sleutel verwijdert. 

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

1. Maak een nieuw tekstbestand en sla dat op als index.js

1. Voeg de vereiste aanroepen toe om Azure- en Node.js-modules te laden

1. Maak de structuur voor het programma, waaronder eenvoudige afhandeling van uitzonderingen

```javascript
const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {
    
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
```

### <a name="add-directives"></a>Instructies toevoegen

Voeg de volgende instructies toe aan het begin van de code:

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { KeyClient } = require("@azure/keyvault-keys");
```

### <a name="authenticate-and-create-a-client"></a>Een client verifiëren en maken

In deze quickstart wordt de aangemelde gebruiker gebruikt voor de verificatie bij de sleutelkluis. Dit is de voorkeursmethode voor lokale ontwikkeling. Voor toepassingen die zijn geïmplementeerd in Azure, moet beheerde identiteit worden toegewezen aan App Service of aan Virtuele machine. Zie [Overzicht van beheerde identiteiten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) voor meer informatie.

In het onderstaande voorbeeld wordt de naam van de sleutelkluis uitgebreid naar de sleutelkluis-URI, met de indeling https://\<your-key-vault-name\>.vault.azure.net. In dit voorbeeld wordt de klasse [DefaultAzureCredential()](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) uit [Azure Identity Library](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme) gebruikt. Hiermee kunt u dezelfde code gebruiken in verschillende omgevingen, met verschillende opties om identiteiten te bieden. Zie [Gids voor ontwikkelaars](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code) voor meer informatie over het verifiëren van een sleutelkluis.

Voeg de volgende code aan de 'main()'-functie

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new KeyClient(KVUri, credential);
```

### <a name="save-a-key"></a>Een sleutel opslaan

Nu uw toepassing is geverifieerd, kunt u een sleutel in uw sleutelkluis plaatsen met de [createKey-methode](/javascript/api/@azure/keyvault-keys/keyclient?#createKey_string__KeyType__CreateKeyOptions_) De parameters van de methode accepteren een sleutelnaam en het [sleuteltype](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keytype)

```javascript
await client.createKey(keyName, keyType);
```

### <a name="retrieve-a-key"></a>Een sleutel ophalen

U kunt nu de eerder ingestelde waarde ophalen met de [getKey-methode](/javascript/api/@azure/keyvault-keys/keyclient?#getKey_string__GetKeyOptions_).

```javascript
const retrievedKey = await client.getKey(keyName);
 ```

### <a name="delete-a-key"></a>Een sleutel verwijderen

Ten slotte kunt u de sleutel uit uw sleutelkluis verwijderen en leegmaken met de methoden [beginDeleteKey](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keyclient?#beginDeleteKey_string__BeginDeleteKeyOptions_) en [purgeDeletedKey](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keyclient?#purgeDeletedKey_string__PurgeDeletedKeyOptions_).

```javascript
const deletePoller = await client.beginDeleteKey(keyName);
await deletePoller.pollUntilDone();
await client.purgeDeletedKey(keyName);
```

## <a name="sample-code"></a>Voorbeeldcode

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { KeyClient } = require("@azure/keyvault-keys");

const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {

  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new KeyClient(KVUri, credential);

  const keyName = "myKey";
  
  console.log("Creating a key in " + keyVaultName + " called '" + keyName + "` ...");
  await client.createKey(keyName, "RSA");

  console.log("Done.");

  console.log("Retrieving your key from " + keyVaultName + ".");

  const retrievedKey = await client.getKey(keyName);

  console.log("Your key version is '" + retrievedKey.properties.version + "'.");

  console.log("Deleting your key from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteKey(keyName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your key from {keyVaultName} ...");
  await client.purgeDeletedKey(keyName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>Testen en verifiëren

Voer de volgende opdrachten uit om de app uit te voeren.

```azurecli
npm install
npm index.js
```

Er verschijnt een variant van de volgende uitvoer:

```azurecli
Creating a key in <your-unique-keyvault-name> called 'myKey' ... done.
Retrieving your key from mykeyvault.
Your key version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your key from <your-unique-keyvault-name> ... done.  
Purging your key from <your-unique-keyvault-name> ... done.   
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een sleutelkluis gemaakt, een sleutel opgeslagen en die sleutel opgehaald. Voor meer informatie over Key Vault en hoe u Key Vault integreert met uw toepassingen gaat u verder naar de artikelen hieronder.

- Lees een [Overzicht van Azure Key Vault](../general/overview.md)
- Lees een [Overzicht van Azure Key Vault-sleutels](about-keys.md)
- Instructies voor [veilige toegang tot een sleutelkluis](../general/secure-your-key-vault.md)
- Zie de [Gids voor Azure Key Vault-ontwikkelaars](../general/developers-guide.md)
- Bekijk de [best practices voor Azure Key Vault](../general/best-practices.md)

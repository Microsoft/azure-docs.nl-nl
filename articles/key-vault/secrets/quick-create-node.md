---
title: Quickstart - Azure Key Vault-clientbibliotheek voor geheimen voor JavaScript (versie 4)
description: Meer informatie over het maken, ophalen en verwijderen van geheimen van een Azure-sleutelkluis met behulp van de clientbibliotheek voor JavaScript
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 769a63819925caa50ff364869b8d9a14c3258a2e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102214538"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-javascript-version-4"></a>Quickstart: Azure Key Vault-clientbibliotheek voor geheimen voor JavaScript (versie 4)

Ga aan de slag met de Azure Key Vault-clientbibliotheek voor geheimen voor JavaScript. [Azure Key Vault](../general/overview.md) is een cloudservice die werkt als een beveiligd archief voor geheimen. U kunt veilig sleutels, wachtwoorden, certificaten en andere geheime informatie opslaan. Azure-sleutelkluizen kunnen worden gemaakt en beheerd via Azure Portal. In deze quickstart krijgt u meer informatie over het maken, ophalen en verwijderen van geheimen uit een Azure-sleutelkluis met behulp van de clientbibliotheek voor JavaScript

Resources voor de Key Vault-clientbibliotheek:

[API-referentiedocumentatie](/javascript/api/overview/azure/key-vault-index) | [Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [Package (npm)](https://www.npmjs.com/package/@azure/keyvault-secrets)

Zie deze artikelen voor meer informatie over Key Vault en geheimen:
- [Overzicht van Key Vault](../general/overview.md)
- [Overzicht van geheimen](about-secrets.md).

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

```terminal
mkdir key-vault-node-app
```

1. Ga naar de map van de zojuist gemaakte *key-vault-node-app* en voer de opdracht 'init' uit om het node-project te initialiseren:

```terminal
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>Key Vault-pakketten installeren

Installeer vanuit het consolevenster de Azure Key Vault-[geheimenbibliotheek](https://www.npmjs.com/package/@azure/keyvault-secrets) voor Node.js.

```terminal
npm install @azure/keyvault-secrets
```

Installeer het [azure.identity](https://www.npmjs.com/package/@azure/identity)-pakket om te verifiëren bij een Key Vault

```terminal
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
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS of Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="grant-access-to-your-key-vault"></a>Toegang verlenen tot uw sleutelkluis

Maak toegangsbeleid voor de sleutelkluis waarmee geheimmachtigingen worden verleend aan uw gebruikersaccount

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
```

## <a name="code-examples"></a>Codevoorbeelden

In de onderstaande codevoorbeelden ziet u hoe u een client maakt, een geheim instelt, een geheim ophaalt en een geheim verwijdert. 

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
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>Een client verifiëren en maken

In deze quickstart wordt de aangemelde gebruiker gebruikt voor de verificatie bij de sleutelkluis. Dit is de voorkeursmethode voor lokale ontwikkeling. Voor toepassingen die zijn geïmplementeerd in Azure, moet beheerde identiteit worden toegewezen aan App Service of aan Virtuele machine. Zie [Overzicht van beheerde identiteiten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) voor meer informatie.

In het onderstaande voorbeeld wordt de naam van de sleutelkluis uitgebreid naar de sleutelkluis-URI, met de indeling https://\<your-key-vault-name\>.vault.azure.net. In dit voor beeld wordt de klasse [DefaultAzureCredential ()](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) uit de [Azure Identity-bibliotheek](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme)gebruikt, waarmee we dezelfde code kunnen gebruiken in verschillende omgevingen met verschillende opties om identiteiten te bieden. Zie [Gids voor ontwikkelaars](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code) voor meer informatie over het verifiëren van een sleutelkluis.

Voeg de volgende code aan de 'main()'-functie

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>Een geheim opslaan

Nu uw toepassing is geverifieerd, kunt u een geheim in uw sleutelkluis plaatsen met behulp van de [methode SetSecret](/javascript/api/@azure/keyvault-secrets/secretclient#setSecret_string__string__SetSecretOptions_). Hiervoor is een naam vereist voor het geheim. In dit voorbeeld gebruiken we 'mySecret'.  

```javascript
await client.setSecret(secretName, secretValue);
```

### <a name="retrieve-a-secret"></a>Een geheim ophalen

U kunt nu de eerder ingestelde waarde ophalen met de [methode getSecret](/javascript/api/@azure/keyvault-secrets/secretclient#getSecret_string__GetSecretOptions_).

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

Uw geheim wordt nu opgeslagen als `retrievedSecret.value`.

### <a name="delete-a-secret"></a>Een geheim verwijderen

Ten slotte kunt u het geheim uit uw sleutelkluis verwijderen en leegmaken met de methoden [beginDeleteSecret](https://docs.microsoft.com/javascript/api/@azure/keyvault-secrets/secretclient?#beginDeleteSecret_string__BeginDeleteSecretOptions_) en [purgeDeletedSecret](https://docs.microsoft.com/javascript/api/@azure/keyvault-secrets/secretclient?#purgeDeletedSecret_string__PurgeDeletedSecretOptions_).

```javascript
const deletePoller = await client.beginDeleteSecret(secretName);
await deletePoller.pollUntilDone();
await client.purgeDeletedSecret(secretName);
```

## <a name="sample-code"></a>Voorbeeldcode

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");

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
  const client = new SecretClient(KVUri, credential);

  const secretName = "mySecret";
  var secretValue = await askQuestion("Input the value of your secret > ");

  console.log("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");
  await client.setSecret(secretName, secretValue);

  console.log("Done.");

  console.log("Forgetting your secret.");
  secretValue = "";
  console.log("Your secret is '" + secretValue + "'.");

  console.log("Retrieving your secret from " + keyVaultName + ".");

  const retrievedSecret = await client.getSecret(secretName);

  console.log("Your secret is '" + retrievedSecret.value + "'.");

  console.log("Deleting your secret from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteSecret(secretName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your secret from {keyVaultName} ...");
  await client.purgeDeletedSecret(secretName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>Testen en verifiëren

1. Voer de volgende opdrachten uit om de app uit te voeren.

    ```terminal
    npm install
    node index.js
    ```

1. Voer een geheime waarde in wanneer u hierom wordt gevraagd. Bijvoorbeeld mySecretPassword (mijn geheime wachtwoord).

    Er verschijnt een variant van de volgende uitvoer:

    ```azurecli
    Input the value of your secret > mySecretPassword
    Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
    Forgetting your secret.
    Your secret is ''.
    Retrieving your secret from <your-unique-keyvault-name>.
    Your secret is 'mySecretPassword'.
    Deleting your secret from <your-unique-keyvault-name> ... done.  
    Purging your secret from <your-unique-keyvault-name> ... done.   
    ```


## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een sleutelkluis gemaakt, een geheim opgeslagen en dat geheim opgehaald. Voor meer informatie over Key Vault en hoe u Key Vault integreert met uw toepassingen gaat u verder naar de artikelen hieronder.

- Lees een [Overzicht van Azure Key Vault](../general/overview.md)
- Lees een [Overzicht van Azure Key Vault-geheimen](about-secrets.md)
- Instructies voor [veilige toegang tot een sleutelkluis](../general/secure-your-key-vault.md)
- Zie de [Gids voor Azure Key Vault-ontwikkelaars](../general/developers-guide.md)
- Raadpleeg het [Overzicht voor Key Vault-beveiliging](../general/security-overview.md)

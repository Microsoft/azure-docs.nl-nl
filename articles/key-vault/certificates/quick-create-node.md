---
title: 'Quickstart: Azure Key Vault-clientbibliotheek voor certificaten voor JavaScript (versie 4)'
description: Meer informatie over het maken, ophalen en verwijderen van certificaten van een Azure-sleutelkluis met behulp van de clientbibliotheek voor JavaScript
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 1064c6a1e2dddaae98e94ccceca7b1d550897393
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97930851"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-javascript-version-4"></a>Quickstart: Azure Key Vault-clientbibliotheek voor certificaten voor JavaScript (versie 4)

Ga aan de slag met de Azure Key Vault-clientbibliotheek met certificaten voor JavaScript. [Azure Key Vault](../general/overview.md) is een cloudservice die werkt als een beveiligd archief voor certificaten. U kunt veilig sleutels, wachtwoorden, certificaten en andere geheime informatie opslaan. Azure-sleutelkluizen kunnen worden gemaakt en beheerd via Azure Portal. In deze quickstart krijt u meer informatie over het maken, ophalen en verwijderen van certificaten van een Azure-sleutelkluis met behulp van de clientbibliotheek voor JavaScript

Resources voor de Key Vault-clientbibliotheek:

[API-referentiedocumentatie](/javascript/api/overview/azure/key-vault-index) | [Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [Package (npm)](https://www.npmjs.com/package/@azure/keyvault-certificates)

Zie voor meer informatie over Key Vault en certificaten:
- [Overzicht van Key Vault](../general/overview.md)
- [Overzicht van certificaten](about-certificates.md).

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

Installeer vanuit het consolevenster de Azure Key Vault-[certificatenbibliotheek](https://www.npmjs.com/package/@azure/keyvault-certificates) voor Node.js.

```azurecli
npm install @azure/keyvault-certificates
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
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS of Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="grant-access-to-your-key-vault"></a>Toegang verlenen tot uw sleutelkluis

Maak toegangsbeleid voor de sleutelkluis waarmee certificaatmachtigingen worden verleend aan uw gebruikersaccount

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --certificate-permissions delete get list create purge
```

## <a name="code-examples"></a>Codevoorbeelden

In de onderstaande codevoorbeelden ziet u hoe u een client maakt en een certificaat instelt, ophaalt en verwijdert. 

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
const { CertificateClient } = require("@azure/keyvault-certificates");
```

### <a name="authenticate-and-create-a-client"></a>Een client verifiëren en maken

In deze quickstart wordt de aangemelde gebruiker gebruikt voor de verificatie bij de sleutelkluis. Dit is de voorkeursmethode voor lokale ontwikkeling. Voor toepassingen die zijn geïmplementeerd in Azure, moet beheerde identiteit worden toegewezen aan App Service of aan Virtuele machine. Zie [Overzicht van beheerde identiteiten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) voor meer informatie.

In het onderstaande voorbeeld wordt de naam van de sleutelkluis uitgebreid naar de sleutelkluis-URI, met de indeling https://\<your-key-vault-name\>.vault.azure.net. In dit voorbeeld wordt de klasse [DefaultAzureCredential()](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) uit [Azure Identity Library](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme) gebruikt. Hiermee kunt u dezelfde code gebruiken in verschillende omgevingen, met verschillende opties om identiteiten te bieden. Zie [Gids voor ontwikkelaars](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code) voor meer informatie over het verifiëren van een sleutelkluis.

Voeg de volgende code aan de 'main()'-functie

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new Certificate(KVUri, credential);
```

### <a name="save-a-certificate"></a>Een certificaat opslaan

Nu uw toepassing is geverifieerd, kunt u een certificaat in uw sleutelkluis plaatsen met behulp van de [methode beginCreateCertificate](/javascript/api/@azure/keyvault-certificates/certificateclient?#beginCreateCertificate_string__CertificatePolicy__BeginCreateCertificateOptions_). Hiervoor is een naam vereist voor het certificaat en het certificaatbeleid[certificaatbeleid](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificatepolicy) met [eigenschappen van het certificaatbeleid](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificatepolicyproperties)

```javascript
const certificatePolicy = {
  issuerName: "Self",
  subject: "cn=MyCert"
};
const createPoller = await client.beginCreateCertificate(certificateName, certificatePolicy);
const certificate = await poller.pollUntilDone();
```

> [!NOTE]
> Als de certificaatnaam bestaat, wordt met de code hierboven, een nieuwe versie van dat certificaat gemaakt.
### <a name="retrieve-a-certificate"></a>Een certificaat ophalen

U kunt nu de eerder ingestelde waarde ophalen met de methode [getCertificate](/javascript/api/@azure/keyvault-certificates/certificateclient?#getCertificate_string__GetCertificateOption).

```javascript
const retrievedCertificate = await client.getCertificate(certificateName);
 ```

### <a name="delete-a-certificate"></a>Een certificaat verwijderen

Ten slotte kunt u het certificaat uit uw sleutelkluis verwijderen en leegmaken met de methoden [beginDeleteCertificate]https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificateclient?#beginDeleteCertificate_string__BeginDeleteCertificateOptions_) en [purgeDeletedCertificate](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificateclient?#purgeDeletedCertificate_string__PurgeDeletedCertificateOptions_).

```javascript
const deletePoller = await client.beginDeleteCertificate(certificateName);
await deletePoller.pollUntilDone();
await client.purgeDeletedCertificate(certificateName);
```

## <a name="sample-code"></a>Voorbeeldcode

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { CertificateClient } = require("@azure/keyvault-certificates");

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

  const string certificateName = "myCertificate";
  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new CertificateClient(KVUri, credential);

  console.log("Creating a certificate in " + keyVaultName + " called '" + certificateName +  "` ...");
  const certificatePolicy = {
  issuerName: "Self",
  subject: "cn=MyCert"
  };
  const createPoller = await client.beginCreateCertificate(certificateName, certificatePolicy);
  const certificate = await poller.pollUntilDone();

  console.log("Done.");

  console.log("Retrieving your certificate from " + keyVaultName + ".");

  const retrievedCertificate = await client.getCertificate(certificateName);

  console.log("Your certificate version is '" + retrievedCertificate.properties.version + "'.");

  console.log("Deleting your certificate from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteCertificate(certificateName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your certificate from {keyVaultName} ...");
  await client.purgeDeletedCertificate(certificateName);
  
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
Creating a certificate in mykeyvault called 'myCertificate' ... done.
Retrieving your certificate from mykeyvault.
Your certificate version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your certificate from mykeyvault ... done
Purging your certificate from mykeyvault ... done 
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een sleutelkluis gemaakt, een certificaat opgeslagen en dat certificaat opgehaald. Voor meer informatie over Key Vault en hoe u Key Vault integreert met uw toepassingen gaat u verder naar de artikelen hieronder.

- Lees een [Overzicht van Azure Key Vault](../general/overview.md)
- Lees een [Overzicht van certificaten](about-certificates.md)
- Zie een [zelfstudie over toegang tot Key Vault vanuit een App Service-toepassing](../general/tutorial-net-create-vault-azure-web-app.md)
- Zie een [zelfstudie over toegang tot Key Vault vanuit een virtuele machine](../general/tutorial-net-virtual-machine.md)
- Zie de [Gids voor Azure Key Vault-ontwikkelaars](../general/developers-guide.md)
- Raadpleeg het [Overzicht voor Key Vault-beveiliging](../general/security-overview.md)

---
title: 'Quickstart: Azure Key Vault-clientbibliotheek voor sleutels voor .NET (versie 4)'
description: Meer informatie over het maken, ophalen en verwijderen van sleutels van een Azure-sleutelkluis met behulp van de clientbibliotheek voor .NET (versie 4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 658fa81c972846292b1bf608110fc95ffe1a730d
ms.sourcegitcommit: e5f9126c1b04ffe55a2e0eb04b043e2c9e895e48
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96318437"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-net-sdk-v4"></a>Quickstart: Azure Key Vault-clientbibliotheek voor sleutels voor .NET (SDK v4)

Ga aan de slag met de Azure Key Vault-clientbibliotheek voor sleutels voor .NET. [Azure Key Vault](../general/overview.md) is een cloudservice die werkt als een beveiligd archief voor cryptografische sleutels. U kunt veilig cryptografische sleutels, wachtwoorden, certificaten en andere geheime informatie opslaan. Azure-sleutelkluizen kunnen worden gemaakt en beheerd via Azure Portal. In deze quickstart krijt u meer informatie over het maken, ophalen en verwijderen van sleutels van een Azure-sleutelkluis met behulp van de clientbibliotheek voor sleutels voor .NET

Resources voor de Key Vault-clientbibliotheek voor sleutels:

[API-referentiedocumentatie](/dotnet/api/azure.security.keyvault.keys) | [Bibliotheek broncode](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [Package (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.keys/)

Zie voor meer informatie over Key Vault en sleutels:
- [Overzicht van Key Vault](../general/overview.md)
- [Overzicht van sleutels](about-keys.md).

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement - [Een gratis abonnement maken](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3.1 SDK of hoger](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure-CLI](/cli/azure/install-azure-cli)
* Een Key Vault - U kunt er een maken met [Azure Portal](../general/quick-create-portal.md), [Azure CLI](../general/quick-create-cli.md) of [Azure PowerShell](../general/quick-create-powershell.md).

Deze quickstart maakt gebruik van `dotnet` en Azure CLI

## <a name="setup"></a>Instellen

Deze quickstart maakt gebruik van de Azure Identity-bibliotheek met Azure CLI om de gebruiker te verifiëren bij Azure-services. Ontwikkelaars kunnen ook Visual Studio of Visual Studio Code gebruiken om hun oproepen te verifiëren: zie [De client verifiëren met de Azure Identity-clientbibliotheek](/dotnet/api/overview/azure/identity-readme?#authenticate-the-client&preserve-view=true) (Engelstalig) voor meer informatie.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

1. Voer de opdracht `login` uit.

    ```azurecli-interactive
    az login
    ```

    Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een Azure-aanmeldingspagina geladen.

    Als dat niet het geval is, opent u een browserpagina op [https://aka.ms/devicelogin](https://aka.ms/devicelogin) en voert u de autorisatiecode in die wordt weergegeven in uw terminal.

2. Meldt u zich in de browser aan met uw accountreferenties.


### <a name="create-new-net-console-app"></a>Nieuwe .NET-console-app maken

1. Voer in een opdrachtshell de volgende opdracht uit om een project te maken met de naam `key-vault-console-app`:

    ```dotnetcli
    dotnet new console --name key-vault-console-app
    ```

1. Ga naar de map van zojuist gemaakte *key-vault-console-app* en voer de volgende opdracht uit om het project te bouwen:

    ```dotnetcli
    dotnet build
    ```

    De build-uitvoer mag geen waarschuwingen of fouten bevatten.
    
    ```console
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ```

### <a name="install-the-packages"></a>De pakketten installeren

Installeer vanuit de opdrachtshell de Azure Key Vault-clientbibliotheek voor sleutels voor .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Keys
```

Voor deze quickstart moet u ook de Azure SDK-clientbibliotheek voor Azure Identity installeren:

```dotnetcli
dotnet add package Azure.Identity
```

#### <a name="grant-access-to-your-key-vault"></a>Toegang verlenen tot uw sleutelkluis

Maak een toegangsbeleid voor de sleutelkluis waarmee sleutelmachtigingen aan uw gebruikersaccount worden verleend

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --key-permissions delete get list create purge
```

#### <a name="set-environment-variables"></a>Omgevingsvariabelen instellen

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
```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Objectmodel

Met de Azure Key Vault-clientbibliotheek voor sleutels voor .NET kunt u sleutels beheren. In de sectie [Codevoorbeelden](#code-examples) ziet u hoe u een client maakt, een sleutel instelt, een sleutel ophaalt en een sleutel verwijdert.

## <a name="code-examples"></a>Codevoorbeelden

### <a name="add-directives"></a>Instructies toevoegen

Voeg de volgende instructies toe aan het begin van *Program.cs*:

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Keys;
```

### <a name="authenticate-and-create-a-client"></a>Een client verifiëren en maken

In deze quickstart wordt de aangemelde gebruiker gebruikt voor de verificatie bij de sleutelkluis. Dit is de voorkeursmethode voor lokale ontwikkeling. Voor toepassingen die zijn geïmplementeerd in Azure, moet beheerde identiteit worden toegewezen aan App Service of aan Virtuele machine. Zie [Overzicht van beheerde identiteiten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) voor meer informatie.

In het onderstaande voorbeeld wordt de naam van de sleutelkluis uitgebreid naar de sleutelkluis-URI, met de indeling https://\<your-key-vault-name\>.vault.azure.net. In dit voorbeeld wordt de klasse ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential) gebruikt, waarmee u dezelfde code kunt gebruiken in verschillende omgevingen met verschillende opties om identiteiten te bieden. Zie [Gids voor ontwikkelaars](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code) voor meer informatie over het verifiëren van een sleutelkluis.

```csharp
var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
var kvUri = "https://" + keyVaultName + ".vault.azure.net";

var client = new KeyClient(new Uri(kvUri), new DefaultAzureCredential());
```

### <a name="save-a-key"></a>Een sleutel opslaan

Gebruik voor deze taak de methode [CreateKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.createkeyasync). De parameters van de methode accepteren een sleutelnaam en het [sleuteltype](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.keys.keytype).

```csharp
var key = await client.CreateKeyAsync("myKey", KeyType.Rsa);
```

> [!NOTE]
> Als de sleutelnaam bestaat, wordt met de code hierboven, een nieuwe versie van die sleutel gemaakt.

### <a name="retrieve-a-key"></a>Een sleutel ophalen

U kunt nu de eerder gemaakte sleutel ophalen met de methode [GetKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.getkeyasync).

```csharp
var key = await client.GetKeyAsync("myKey");
```

### <a name="delete-a-key"></a>Een sleutel verwijderen

Ten slotte kunt u de sleutel uit uw sleutelkluis verwijderen en leegmaken met de methoden [StartDeleteKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.startdeletekeyasync) en [PurgeDeletedKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.purgedeletedkeyasync).

```csharp
var operation = await client.StartDeleteKeyAsync("myKey");

// You only need to wait for completion if you want to purge or recover the key.
await operation.WaitForCompletionAsync();

var key = operation.Value;
await client.PurgeDeletedKeyAsync("myKey");
```

## <a name="sample-code"></a>Voorbeeldcode

Voer de volgende stappen uit om de .NET Core-console-app aan te passen voor interactie met Key Vault:

- Vervang de code in *Program.cs* door de volgende code:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Keys;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string keyName = "myKey";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new KeyClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write($"Creating a key in {keyVaultName} called '{keyName}' ...");
                var createdKey = await client.CreateKeyAsync(keyName, KeyType.Rsa);
                Console.WriteLine("done.");
    
                Console.WriteLine($"Retrieving your key from {keyVaultName}.");
                var key = await client.GetKeyAsync(keyName);
                Console.WriteLine($"Your key version is '{key.Value.Properties.Version}'.");
    
                Console.Write($"Deleting your key from {keyVaultName} ...");
                var deleteOperation = await client.StartDeleteKeyAsync(keyName);
                // You only need to wait for completion if you want to purge or recover the key.
                await deleteOperation.WaitForCompletionAsync();
                Console.WriteLine("done.");

                Console.Write($"Purging your key from {keyVaultName} ...");
                await client.PurgeDeletedKeyAsync(keyName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>Testen en verifiëren

1.  Voer de volgende opdracht uit om het project te bouwen

    ```dotnetcli
    dotnet build
    ```

1. Voer de volgende opdracht uit om de app uit te voeren.

    ```dotnetcli
    dotnet run
    ```

1. Voer een geheime waarde in wanneer u hierom wordt gevraagd. Bijvoorbeeld mySecretPassword (mijn geheime wachtwoord).

    Er verschijnt een variant van de volgende uitvoer:

    ```console
    Creating a key in mykeyvault called 'myKey' ... done.
    Retrieving your key from mykeyvault.
    Your key version is '8532359bced24e4bb2525f2d2050738a'.
    Deleting your key from jl-kv ... done
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de sleutelkluis en de bijbehorende resourcegroep niet meer nodig hebt, kunt u Azure CLI of Azure PowerShell gebruiken om ze te verwijderen.

### <a name="delete-a-key-vault"></a>Een sleutelkluis verwijderen

```azurecli
az keyvault delete --name <your-unique-keyvault-name>
```

```azurepowershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name>
```

### <a name="purge-a-key-vault"></a>Een sleutelkluis opschonen

```azurecli
az keyvault purge --location eastus --name <your-unique-keyvault-name>
```

```azurepowershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name> -InRemovedState -Location eastus
```

### <a name="delete-a-resource-group"></a>Een resourcegroep verwijderen

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een sleutelkluis gemaakt, een sleutel opgeslagen en die sleutel opgehaald. 

Zie de volgende artikelen voor meer informatie over Key Vault en hoe u Key Vault integreert met uw apps:

- Lees een [Overzicht van Azure Key Vault](../general/overview.md)
- Lees een [Overzicht van sleutels](about-keys.md)
- Zie een [zelfstudie over toegang tot Key Vault vanuit een App Service-toepassing](../general/tutorial-net-create-vault-azure-web-app.md)
- Zie een [zelfstudie over toegang tot Key Vault vanuit een virtuele machine](../general/tutorial-net-virtual-machine.md)
- Zie de [Gids voor Azure Key Vault-ontwikkelaars](../general/developers-guide.md)
- Bekijk de [best practices voor Azure Key Vault](../general/best-practices.md)

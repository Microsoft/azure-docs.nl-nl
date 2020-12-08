---
title: 'Quickstart: Azure Key Vault-clientbibliotheek voor geheimen voor .NET (versie 4)'
description: Meer informatie over het maken, ophalen en verwijderen van geheimen van een Azure-sleutelkluis met behulp van de clientbibliotheek voor .NET (versie 4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: ecd5fd4f5af883d26f904181796a78f61669b37a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187354"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-net-sdk-v4"></a>Quickstart: Azure Key Vault-clientbibliotheek voor geheimen voor .NET (SDK v4)

Ga aan de slag met de Azure Key Vault-clientbibliotheek voor geheimen voor .NET. [Azure Key Vault](../general/overview.md) is een cloudservice die werkt als een beveiligd archief voor geheimen. U kunt veilig sleutels, wachtwoorden, certificaten en andere geheime informatie opslaan. Azure-sleutelkluizen kunnen worden gemaakt en beheerd via Azure Portal. In deze quickstart krijgt u meer informatie over het maken, ophalen en verwijderen van geheimen uit een Azure-sleutelkluis met behulp van de clientbibliotheek voor .NET

Resources voor de Key Vault-clientbibliotheek:

[API-referentiedocumentatie](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet&preserve-view=true) | [Bibliotheek broncode](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [Package (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

Zie deze artikelen voor meer informatie over Key Vault en geheimen:
- [Overzicht van Key Vault](../general/overview.md)
- [Overzicht van geheimen](about-secrets.md).

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

Installeer vanuit de opdrachtshell de Azure Key Vault-clientbibliotheek voor geheimen voor .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Secrets
```

Voor deze quickstart moet u ook de Azure SDK-clientbibliotheek voor Azure Identity installeren:

```dotnetcli
dotnet add package Azure.Identity
```

#### <a name="grant-access-to-your-key-vault"></a>Toegang verlenen tot uw sleutelkluis

Een toegangsbeleid maken voor de sleutelkluis waarmee geheime machtigingen aan uw gebruikersaccount worden verleend

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
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

Met de Azure Key Vault-clientbibliotheek voor geheimen voor .NET kunt u geheimen beheren. In de sectie [Codevoorbeelden](#code-examples) ziet u hoe u een client maakt, een geheim instelt, een geheim ophaalt en een geheim verwijdert.

## <a name="code-examples"></a>Codevoorbeelden

### <a name="add-directives"></a>Instructies toevoegen

Voeg de volgende instructies toe aan het begin van *Program.cs*:

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Een client verifiëren en maken

In deze quickstart wordt de aangemelde gebruiker gebruikt voor de verificatie bij de sleutelkluis. Dit is de voorkeursmethode voor lokale ontwikkeling. Voor toepassingen die zijn geïmplementeerd in Azure, moet beheerde identiteit worden toegewezen aan App Service of aan Virtuele machine. Zie [Overzicht van beheerde identiteiten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) voor meer informatie.

In het onderstaande voorbeeld wordt de naam van de sleutelkluis uitgebreid naar de sleutelkluis-URI, met de indeling https://\<your-key-vault-name\>.vault.azure.net. In dit voorbeeld wordt de klasse ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential) gebruikt, waarmee u dezelfde code kunt gebruiken in verschillende omgevingen met verschillende opties om identiteiten te bieden. Zie het artikel over [standaardverificatie van Azure-referenties](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme?#defaultazurecredential) (Engelstalig) voor meer informatie. 

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Een geheim opslaan

Nu de console-app is geverifieerd, voegt u een geheim toe aan de sleutelkluis. Gebruik voor deze taak de methode [SetSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.setsecretasync). De eerste parameter van de methode in dit voorbeeld accepteert een naam voor het geheim &mdash;"mySecret".

```csharp
await client.SetSecretAsync(secretName, secretValue);
```

> [!NOTE]
> Als de naam van het geheim bestaat, wordt met de code hierboven een nieuwe versie van dat geheim gemaakt.


### <a name="retrieve-a-secret"></a>Een geheim ophalen

U kunt nu de eerder ingestelde waarde ophalen met de methode [GetSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.getsecretasync).

```csharp
var secret = await client.GetSecretAsync(secretName);
``````

Uw geheim wordt nu opgeslagen als `secret.Value`.

### <a name="delete-a-secret"></a>Een geheim verwijderen

Ten slotte gaan we het geheim uit uw sleutelkluis verwijderen met de methode [StartDeleteSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.startdeletesecretasync).

```csharp
await client.StartDeleteSecretAsync(secretName);
```

## <a name="sample-code"></a>Voorbeeldcode

Voer de volgende stappen uit om de .NET Core-console-app aan te passen voor interactie met Key Vault:

1. Vervang de code in *Program.cs* door de volgende code:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Secrets;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string secretName = "mySecret";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write("Input the value of your secret > ");
                var secretValue = Console.ReadLine();
    
                Console.Write($"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...");
                await client.SetSecretAsync(secretName, secretValue);
                Console.WriteLine(" done.");
    
                Console.WriteLine("Forgetting your secret.");
                secretValue = string.Empty;
                Console.WriteLine($"Your secret is '{secretValue}'.");
    
                Console.WriteLine($"Retrieving your secret from {keyVaultName}.");
                var secret = await client.GetSecretAsync(secretName);
                Console.WriteLine($"Your secret is '{secret.Value}'.");
    
                Console.Write($"Deleting your secret from {keyVaultName} ...");
                DeleteSecretOperation operation = await client.StartDeleteSecretAsync(secretName);
                // You only need to wait for completion if you want to purge or recover the secret.
                await operation.WaitForCompletionAsync();
                Console.WriteLine(" done.");
                
                Console.Write($"Purging your secret from {keyVaultName} ...");
                await client.PurgeDeletedSecretAsync(secretName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>Testen en verifiëren

1. Voer de volgende opdracht uit om de app uit te voeren.

    ```dotnetcli
    dotnet run
    ```

1. Voer een geheime waarde in wanneer u hierom wordt gevraagd. Bijvoorbeeld mySecretPassword (mijn geheime wachtwoord).

    Er verschijnt een variant van de volgende uitvoer:

    ```console
    Input the value of your secret > mySecretPassword
    Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
    Forgetting your secret.
    Your secret is ''.
    Retrieving your secret from <your-unique-keyvault-name>.
    Your secret is 'mySecretPassword'.
    Deleting your secret from <your-unique-keyvault-name> ... done.    
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

Zie de volgende artikelen voor meer informatie over Key Vault en hoe u Key Vault integreert met uw apps:

- Lees een [Overzicht van Azure Key Vault](../general/overview.md)
- Zie een [zelfstudie over toegang tot Key Vault vanuit een App Service-toepassing](../general/tutorial-net-create-vault-azure-web-app.md)
- Zie een [zelfstudie over toegang tot Key Vault vanuit een virtuele machine](../general/tutorial-net-virtual-machine.md)
- Zie de [Gids voor Azure Key Vault-ontwikkelaars](../general/developers-guide.md)
- Bekijk de [best practices voor Azure Key Vault](../general/best-practices.md)
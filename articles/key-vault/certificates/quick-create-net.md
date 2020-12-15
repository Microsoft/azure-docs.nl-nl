---
title: 'Quickstart: Azure Key Vault-clientbibliotheek voor certificaten voor .NET (versie 4)'
description: Meer informatie over het maken, ophalen en verwijderen van certificaten van een Azure-sleutelkluis met behulp van de clientbibliotheek voor .NET (versie 4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: b40a13b84a0191b5c454d7edac2226f8f06fadcd
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780158"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-net-sdk-v4"></a>Quickstart: Azure Key Vault-clientbibliotheek met certificaten voor .NET (SDK v4)

Ga aan de slag met de Azure Key Vault-clientbibliotheek met certificaten voor .NET. [Azure Key Vault](../general/overview.md) is een cloudservice die werkt als een beveiligd archief voor certificaten. U kunt veilig sleutels, wachtwoorden, certificaten en andere geheime informatie opslaan. Azure-sleutelkluizen kunnen worden gemaakt en beheerd via Azure Portal. In deze quickstart krijt u meer informatie over het maken, ophalen en verwijderen van certificaten van een Azure-sleutelkluis met behulp van de clientbibliotheek voor .NET

Resources voor de Key Vault-clientbibliotheek:

[API-referentiedocumentatie](/dotnet/api/azure.security.keyvault.certificates) | [Bibliotheek broncode](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [Package (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)

Zie voor meer informatie over Key Vault en certificaten:
- [Overzicht van Key Vault](../general/overview.md)
- [Overzicht van certificaten](about-certificates.md).

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

#### <a name="grant-access-to-your-key-vault"></a>Toegang verlenen tot uw sleutelkluis

Maak toegangsbeleid voor de sleutelkluis waarmee certificaatmachtigingen worden verleend aan uw gebruikersaccount

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --certificate-permissions delete get list create purge
```

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

Installeer vanuit de opdrachtshell de Azure Key Vault-clientbibliotheek voor certificaten voor .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Certificates
```

Voor deze quickstart moet u ook de Azure SDK-clientbibliotheek voor Azure Identity installeren:

```dotnetcli
dotnet add package Azure.Identity
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

Met de Azure Key Vault-clientbibliotheek met certificaten voor .NET kunt u certificaten beheren. In de sectie [Codevoorbeelden](#code-examples) ziet u hoe u een client maakt en een certificaat instelt, ophaalt en verwijdert.

## <a name="code-examples"></a>Codevoorbeelden

### <a name="add-directives"></a>Instructies toevoegen

Voeg de volgende instructies toe aan het begin van *Program.cs*:

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Certificates;
```

### <a name="authenticate-and-create-a-client"></a>Een client verifiëren en maken

In deze quickstart wordt de aangemelde gebruiker gebruikt voor de verificatie bij de sleutelkluis. Dit is de voorkeursmethode voor lokale ontwikkeling. Voor toepassingen die zijn geïmplementeerd in Azure, moet beheerde identiteit worden toegewezen aan App Service of aan Virtuele machine. Zie [Overzicht van beheerde identiteiten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) voor meer informatie.

In het onderstaande voorbeeld wordt de naam van de sleutelkluis uitgebreid naar de sleutelkluis-URI, met de indeling https://\<your-key-vault-name\>.vault.azure.net. In dit voorbeeld wordt de klasse [DefaultAzureCredential()](/dotnet/api/azure.identity.defaultazurecredential) uit [Azure Identity Library](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme) gebruikt. Hiermee kunt u dezelfde code gebruiken in verschillende omgevingen, met verschillende opties om identiteiten te bieden. Zie [Gids voor ontwikkelaars](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code) voor meer informatie over het verifiëren van een sleutelkluis.

```csharp
string keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
var kvUri = "https://" + keyVaultName + ".vault.azure.net";

var client = new CertificateClient(new Uri(kvUri), new DefaultAzureCredential());
```

### <a name="save-a-certificate"></a>Een certificaat opslaan

In dit voorbeeld kunt u om het eenvoudig te houden, het zelfondertekende certificaat met het standaard uitgiftebeleid gebruiken. Voor deze taak gebruikt u de methode [StartCreateCertificateAsync](/dotnet/api/azure.security.keyvault.certificates.certificateclient.startcreatecertificateasync). De parameters van de methode accepteren een certificaatnaam en het [certificaatbeleid](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.certificates.certificatepolicy).

```csharp
var operation = await client.StartCreateCertificateAsync("myCertificate", CertificatePolicy.Default);
var certificate = await operation.WaitForCompletionAsync();
```

> [!NOTE]
> Als de certificaatnaam bestaat, wordt met de code hierboven, een nieuwe versie van dat certificaat gemaakt.

### <a name="retrieve-a-certificate"></a>Een certificaat ophalen

U kunt nu het eerder gemaakte certificaat ophalen met de methode [GetCertificateAsync](/dotnet/api/azure.security.keyvault.certificates.certificateclient.getcertificateasync).

```csharp
var certificate = await client.GetCertificateAsync("myCertificate");
```

### <a name="delete-a-certificate"></a>Een certificaat verwijderen

Ten slotte kunt u het certificaat uit uw sleutelkluis verwijderen en leegmaken met de methoden [StartDeleteCertificateAsync](/dotnet/api/azure.security.keyvault.certificates.certificateclient.startdeletecertificateasync) en [PurgeDeletedCertificateAsync](/dotnet/api/azure.security.keyvault.certificates.certificateclient.purgedeletedcertificateasync).

```csharp
var operation = await client.StartDeleteCertificateAsync("myCertificate");

// You only need to wait for completion if you want to purge or recover the certificate.
await operation.WaitForCompletionAsync();

var certificate = operation.Value;
await client.PurgeDeletedCertificateAsync("myCertificate");
```

## <a name="sample-code"></a>Voorbeeldcode

Voer de volgende stappen uit om de .NET Core-console-app aan te passen voor interactie met Key Vault:

- Vervang de code in *Program.cs* door de volgende code:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Certificates;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string certificateName = "myCertificate";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new CertificateClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write($"Creating a certificate in {keyVaultName} called '{certificateName}' ...");
                CertificateOperation operation = await client.StartCreateCertificateAsync(certificateName, CertificatePolicy.Default);
                await operation.WaitForCompletionAsync();
                Console.WriteLine(" done.");
    
                Console.WriteLine($"Retrieving your certificate from {keyVaultName}.");
                var certificate = await client.GetCertificateAsync(certificateName);
                Console.WriteLine($"Your certificate version is '{certificate.Value.Properties.Version}'.");
    
                Console.Write($"Deleting your certificate from {keyVaultName} ...");
                DeleteCertificateOperation deleteOperation = await client.StartDeleteCertificateAsync(certificateName);
                // You only need to wait for completion if you want to purge or recover the certificate.
                await deleteOperation.WaitForCompletionAsync();
                Console.WriteLine(" done.");

                Console.Write($"Purging your certificate from {keyVaultName} ...");
                await client.PurgeDeletedCertificateAsync(certificateName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>Testen en verifiëren

Voer de volgende opdracht uit om het project te bouwen

```dotnetcli
dotnet build
```

Er verschijnt een variant van de volgende uitvoer:

```console
Creating a certificate in mykeyvault called 'myCertificate' ... done.
Retrieving your certificate from mykeyvault.
Your certificate version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your certificate from mykeyvault ... done
Purging your certificate from mykeyvault ... done
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een sleutelkluis gemaakt, een certificaat opgeslagen en dat certificaat opgehaald. 

Zie de volgende artikelen voor meer informatie over Key Vault en hoe u Key Vault integreert met uw apps:

- Lees een [Overzicht van Azure Key Vault](../general/overview.md)
- Lees een [Overzicht van certificaten](about-certificates.md)
- Zie een [zelfstudie over toegang tot Key Vault vanuit een App Service-toepassing](../general/tutorial-net-create-vault-azure-web-app.md)
- Zie een [zelfstudie over toegang tot Key Vault vanuit een virtuele machine](../general/tutorial-net-virtual-machine.md)
- Zie de [Gids voor Azure Key Vault-ontwikkelaars](../general/developers-guide.md)
- Bekijk de [best practices voor Azure Key Vault](../general/best-practices.md)

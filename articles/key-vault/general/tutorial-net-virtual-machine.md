---
title: 'Zelfstudie: Azure Key Vault gebruiken met een virtuele machine in .NET | Microsoft Docs'
description: In deze zelfstudie configureert u een virtuele machine en een ASP.NET-kerntoepassing om een geheim uit uw sleutelkluis te lezen.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: a56c08e5bf6054d24af3ade571ec625969286a77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102455641"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-net"></a>Zelfstudie: Azure Key Vault gebruiken met een virtuele machine in .NET

Azure Key Vault helpt u bij het beveiligen van geheimen zoals API-sleutels, de databaseverbindingsreeksen die nodig zijn voor toegang tot uw toepassingen, services en IT-resources.

In deze zelfstudie leert u hoe u ervoor zorgt dat een consoletoepassing informatie ophaalt uit Azure Key Vault. De toepassing gebruikt de beheerde identiteit van de virtuele machine voor het verifiëren van Key Vault. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een resourcegroep.
> * Een sleutelkluis maken.
> * Voeg een geheim toe aan de sleutelkluis.
> * Een geheim ophalen uit de sleutelkluis.
> * Een virtuele Azure-machine maken.
> * Een [beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md) inschakelen voor de virtuele machine.
> * Wijs machtigingen toe aan de VM-identiteit.

Lees voordat u verdergaat eerst [Basisconcepten van Key Vault](basic-concepts.md). 

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan.

## <a name="prerequisites"></a>Vereisten

Voor Windows, Mac en Linux:
  * [Git](https://git-scm.com/downloads)
  * De [.NET Core 3.1 SDK of hoger](https://dotnet.microsoft.com/download/dotnet-core/3.1).
  * [Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-resources-and-assign-permissions"></a>Resources maken en machtigingen toewijzen

Voordat u begint met het coderen, moet u enkele resources maken, een geheim in uw sleutelkluis plaatsen en machtigingen toewijzen.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Als u zich met behulp van Azure CLI wilt aanmelden bij Azure, voert u het volgende in:

```azurecli
az login
```

## <a name="create-a-resource-group-and-key-vault"></a>Een resourcegroep en sleutelkluis maken

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>Vul uw sleutelkluis in met een geheim

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken
Gebruik een van de volgende manieren om een virtuele Windows of Linux-machine te maken:

| Windows | Linux |
|--|--|
| [Azure-CLI](../../virtual-machines/windows/quick-create-cli.md) | [Azure-CLI](../../virtual-machines/linux/quick-create-cli.md) |  
| [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) | [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) |
| [Azure-portal](../../virtual-machines/windows/quick-create-portal.md) | [Azure-portal](../../virtual-machines/linux/quick-create-portal.md) |

## <a name="assign-an-identity-to-the-vm"></a>Een identiteit toewijzen aan de virtuele machine
Maak een door het systeem toegewezen identiteit voor de virtuele machine met de opdracht [az vm identity assign](/cli/azure/vm/identity#az-vm-identity-assign):

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Bekijk de door het systeem toegewezen identiteit die wordt weergegeven in de volgende code. De uitvoer van de vorige opdracht ziet er als volgt uit: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Machtigingen toewijzen aan de VM-identiteit
Wijs de eerder gemaakte identiteitsmachtigingen toe aan uw sleutelkluis met de opdracht [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy):

```azurecli
az keyvault set-policy --name '<your-unique-key-vault-name>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="sign-in-to-the-virtual-machine"></a>Aanmelden bij de virtuele machine

Als u zich wilt aanmelden bij de virtuele machine, volgt u de instructies in [Verbinding maken met en aanmelden bij een virtuele Azure-machine met Windows](../../virtual-machines/windows/connect-logon.md) of [Verbinding maken en aanmelden bij de virtuele Azure-machine die Linux uitvoert](../../virtual-machines/linux/login-using-aad.md).

## <a name="set-up-the-console-app"></a>De console-app instellen

Maak een console-app en installeer de vereiste pakketten met de opdracht `dotnet`.

### <a name="install-net-core"></a>.NET Core installeren

Als u .NET Core wilt installeren, gaat u naar de pagina [.NET-downloads](https://www.microsoft.com/net/download).

### <a name="create-and-run-a-sample-net-app"></a>Een .NET-voorbeeld-app maken en uitvoeren

Open een opdrachtprompt.

U kunt 'Hallo wereld' afdrukken naar de console door de volgende opdrachten uit te voeren:

```console
dotnet new console -n keyvault-console-app
cd keyvault-console-app
dotnet run
```

### <a name="install-the-package"></a>Het pakket installeren

Installeer vanuit het consolevenster de Azure Key Vault Secrets-clientbibliotheek voor .NET:

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

Voor deze quickstart moet u het volgende identiteitspakket installeren om u te verifiëren bij Azure Key Vault:

```console
dotnet add package Azure.Identity
```

## <a name="edit-the-console-app"></a>De console-app bewerken

Open het bestand *Program.cs* en voeg deze pakketten toe:

```csharp
using System;
using Azure.Core;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
```

Voeg deze regels toe, waarbij u de URI bijwerkt volgens de `vaultUri` van uw sleutelkluis. De onderstaande code maakt gebruik van ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential) voor verificatie naar de sleutelkluis, die een token van de beheerde identiteit van de toepassing gebruikt voor verificatie. Er wordt ook exponentieel uitstel gebruikt voor nieuwe pogingen wanneer de sleutelkluis wordt vertraagd.

```csharp
  class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            var kvUri = "https://<your-key-vault-name>.vault.azure.net";
            SecretClientOptions options = new SecretClientOptions()
            {
                Retry =
                {
                    Delay= TimeSpan.FromSeconds(2),
                    MaxDelay = TimeSpan.FromSeconds(16),
                    MaxRetries = 5,
                    Mode = RetryMode.Exponential
                 }
            };

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential(),options);

            Console.Write("Input the value of your secret > ");
            string secretValue = Console.ReadLine();

            Console.Write("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");

            client.SetSecret(secretName, secretValue);

            Console.WriteLine(" done.");

            Console.WriteLine("Forgetting your secret.");
            secretValue = "";
            Console.WriteLine("Your secret is '" + secretValue + "'.");

            Console.WriteLine("Retrieving your secret from " + keyVaultName + ".");

            KeyVaultSecret secret = client.GetSecret(secretName);

            Console.WriteLine("Your secret is '" + secret.Value + "'.");

            Console.Write("Deleting your secret from " + keyVaultName + " ...");

            client.StartDeleteSecret(secretName);

            System.Threading.Thread.Sleep(5000);
            Console.WriteLine(" done.");

        }
    }
```

## <a name="clean-up-resources"></a>Resources opschonen

Als ze niet meer nodig zijn, verwijdert u de virtuele machine en de sleutelkluis.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [REST-API van Azure Key Vault](/rest/api/keyvault/)

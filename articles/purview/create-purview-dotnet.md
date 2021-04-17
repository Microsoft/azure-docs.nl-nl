---
title: Een Purview-account maken met behulp van .NET SDK
description: Maak een Azure Purview-account met behulp van .NET SDK.
author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 4/2/2021
ms.author: nayenama
ms.openlocfilehash: b3dc7bf8ac7650a7219c15a09a31d4dcf84a40bf
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587796"
---
# <a name="quickstart-create-a-purview-account-using-net-sdk"></a>Quickstart: Een purview-account maken met behulp van .NET SDK

In deze quickstart wordt beschreven hoe u .NET SDK gebruikt om een Azure Purview-account te maken 

> [!IMPORTANT]
> Azure Purview is momenteel in PREVIEW. De [Aanvullende voorwaarden voor gebruik van Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) omvatten aanvullende juridische voorwaarden die van toepassing zijn op Azure-functies die in bèta of preview zijn of die anders nog niet algemeen beschikbaar zijn.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* Uw eigen [Azure Active Directory-tenant](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Uw account moet gemachtigd zijn om resources te maken in het abonnement

* Als **Azure Policy** alle toepassingen ervan weerhoudt om een **Storage-account** en een **EventHub-naamruimte** te maken, moet u met behulp van tags uitzonderingen voor het beleid instellen. Deze kunt u invoeren tijdens het proces voor het maken van een Purview-account. De belangrijkste reden hiervoor is dat voor elk gemaakt Purview-account een beheerde resourcegroep wordt gemaakt met daarin een Storage-account en een EventHub-naamruimte. Raadpleeg Catalogusportal maken [voor meer informatie](create-catalog-portal.md)

### <a name="visual-studio"></a>Visual Studio

De procedures in dit artikel zijn gebaseerd op Visual Studio 2019. De procedures voor Visual Studio 2013, 2015 en 2017 verschillen enigszins.

### <a name="azure-net-sdk"></a>Azure .NET SDK

Download en installeer [Azure .NET SDK](https://azure.microsoft.com/downloads/) op uw computer.

## <a name="create-an-application-in-azure-active-directory"></a>Een toepassing maken in Azure Active Directory

Volg in de secties in *Procedure: de portal gebruiken voor het maken van een Azure AD-toepassing en service-principal die toegang hebben tot resources* de instructies om de volgende taken uit te voeren:

1. Maak in [Een Azure Active Directory-toepassing maken](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) een toepassing in Azure Active Directory die staat voor de .NET-toepassing die u in deze zelfstudie maakt. Voor de aanmeldings-URL kunt u een dummy-URL opgeven, zoals wordt getoond in het artikel (`https://contoso.org/exampleapp`).
2. Haal in [Waarden ophalen voor aanmelden](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) de **toepassings-id** en **tenant-id** op, en noteer deze waarden zodat u ze later in deze zelfstudie kunt gebruiken. 
3. Haal in [Certificaten en geheimen](../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options) de **verificatiesleutel** op, en noteer deze waarde zodat u deze later in deze zelfstudie kunt gebruiken.
4. Wijs in [De toepassing toewijzen aan een rol](../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) de toepassing toe aan de rol **Inzender** op het niveau van het abonnement, zodat met de toepassing gegevensfactory's in het abonnement kunnen worden gemaakt.

## <a name="create-a-visual-studio-project"></a>Een Visual Studio-project maken

Maak vervolgens een C# .NET-consoletoepassing in Visual Studio:

1. Start **Visual Studio**.
2. Selecteer in het Startvenster de optie **Een nieuw project maken** > **Console-app (.NET Framework)** . .NET versie 4.5.2 of hoger is vereist.
3. Voer **in Projectnaam** **PurviewQuickStart in.**
4. Selecteer **Maken** om het project te maken.

## <a name="install-nuget-packages"></a>NuGet-pakketten installeren

1. Selecteer **Hulpprogramma's** > **NuGet Package Manager** > **Package Manager-console**.
2. Voer in het deelvenster **Package Manager Console** de volgende opdrachten uit om pakketten te installeren. Zie het [Microsoft.Azure.Management.Purview NuGet-pakket voor meer informatie.](https://www.nuget.org/packages/Microsoft.Azure.Management.Purview/)

    ```powershell
    Install-Package Microsoft.Azure.Management.Purview
    Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-purview-client"></a>Een Purview-client maken

1. Open **Program.cs** en neem de volgende instructies op om verwijzingen naar naamruimten toe te voegen.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
      using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.Purview;
      using Microsoft.Azure.Management.Purview.Models;
      using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Voeg de volgende code toe aan de methode **Main** waarmee de variabelen worden ingesteld. Vervang de plaatsaanduidingen door uw eigen waarden. Voor een lijst met Azure-regio's waarin Purview momenteel beschikbaar is, zoekt u in **Azure Purview** en selecteert u de regio's waarin u geïnteresseerd bent op de volgende pagina: Producten beschikbaar [per regio.](https://azure.microsoft.com/global-infrastructure/services/)

   ```csharp
   // Set variables
   string tenantID = "<your tenant ID>";
   string applicationId = "<your application ID>";
   string authenticationKey = "<your authentication key for the application>";
   string subscriptionId = "<your subscription ID where the data factory resides>";
   string resourceGroup = "<your resource group where the data factory resides>";
   string region = "<the location of your resource group>";
   string purviewAccountName = 
       "<specify the name of purview account to create. It must be globally unique.>";
   ```

3. Voeg de volgende code toe aan de **methode Main** om een exemplaar van de **klasse PurviewManagementClient te** maken. U gebruikt dit object om een account voor het opsmaken van gegevens op te maken.

   ```csharp
   // Authenticate and create a purview management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync(
   "https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new PurviewManagementClient(cred)
   {
      SubscriptionId = subscriptionId           
   };
   ```

## <a name="create-a-purview-account"></a>Een Purview-account maken

Voeg de volgende code toe aan de **methode Main** om een account voor het opsmaken van een account op **te geven.** 

```csharp
    // Create a purview Account
    Console.WriteLine("Creating Purview Account " + purviewAccountName + "...");
    Account account = new Account()
    {
    Location = region,
    Identity = new Identity(type: "SystemAssigned"),
    Sku = new AccountSku(name: "Standard", capacity: 4)
    };            
    try
    {
      client.Accounts.CreateOrUpdate(resourceGroup, purviewAccountName, account);
      Console.WriteLine(client.Accounts.Get(resourceGroup, purviewAccountName).ProvisioningState);                
    }
    catch (ErrorResponseModelException purviewException)
    {
      Console.WriteLine(purviewException.StackTrace);
    }
    Console.WriteLine(
      SafeJsonConvert.SerializeObject(account, client.SerializationSettings));
    while (client.Accounts.Get(resourceGroup, purviewAccountName).ProvisioningState ==
           "PendingCreation")
    {
      System.Threading.Thread.Sleep(1000);
    }
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
```

## <a name="run-the-code"></a>De code uitvoeren

Bouw en start de toepassing en controleer vervolgens de uitvoering.

In de console wordt de voortgang van het maken van een account voor het opsmaken van een account afgedrukt.

### <a name="sample-output"></a>Voorbeelduitvoer

```json
Creating Purview Account testpurview...
Succeeded
{
  "sku": {
    "capacity": 4,
    "name": "Standard"
  },
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "southcentralus"
}

Press any key to exit...
```

## <a name="verify-the-output"></a>De uitvoer controleren

Ga naar de **pagina Accounts opsmaken** in [de Azure Portal](https://portal.azure.com) controleer het account dat is gemaakt met behulp van de bovenstaande code. 

## <a name="delete-purview-account"></a>Purview-account verwijderen

Voeg de volgende regels code toe aan het programma om programmatisch een account voor het verwijderen van een account op te geven: 

```csharp
    Console.WriteLine("Deleting the Purview Account");
    client.Accounts.Delete(resourceGroup, purviewAccountName);
```

## <a name="check-if-purview-account-name-is-available"></a>Controleer of de accountnaam Purview beschikbaar is

Gebruik de volgende code om de beschikbaarheid van een purview-account te controleren: 

```csharp
    CheckNameAvailabilityRequest checkNameAvailabilityRequest = new CheckNameAvailabilityRequest()
    {
      Name = purviewAccountName,
      Type =  "Microsoft.Purview/accounts"
    };
    Console.WriteLine("Check Purview account name");
    Console.WriteLine(client.Accounts.CheckNameAvailability(checkNameAvailabilityRequest).NameAvailable);
```

De bovenstaande code met afdrukken 'True' als de naam beschikbaar is en 'False' als de naam niet beschikbaar is.


## <a name="next-steps"></a>Volgende stappen

Met de code in deze zelfstudie maakt u een purview-account, verwijdert u een account voor het opsmaken van gegevens en controleert u op de beschikbaarheid van de naam van het purview-account. U kunt nu de .NET SDK downloaden en meer informatie vinden over andere resourceprovideracties die u kunt uitvoeren voor een Purview-account.

Ga naar het volgende artikel voor informatie over hoe u gebruikers toegang kunt geven tot uw Azure Purview-account. 

> [!div class="nextstepaction"]
> [Gebruikers toevoegen aan uw Azure Purview-account](catalog-permissions.md)

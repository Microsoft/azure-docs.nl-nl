---
title: Een door het systeem toegewezen beheerde identiteit gebruiken voor toegang tot Azure Cosmos DB-gegevens
description: Meer informatie over het configureren van Azure Active Directory door het systeem toegewezen beheerde identiteit (managed service identity) voor toegang tot sleutels van Azure Cosmos DB.
author: j-patrick
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: b85e1fc74688f2883531bd3a6e724a2ce326a9db
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600247"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>Door het systeem toegewezen beheerde identiteiten gebruiken voor toegang tot Azure Cosmos DB gegevens
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In dit artikel stelt u een *robuuste, agnostische* oplossing voor sleutelrotatie in voor toegang tot Azure Cosmos DB sleutels met behulp van [beheerde identiteiten.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) In het voorbeeld in dit artikel wordt gebruikgemaakt Azure Functions, maar u kunt elke service gebruiken die beheerde identiteiten ondersteunt. 

U leert hoe u een functie-app maakt die toegang heeft tot uw Azure Cosmos DB zonder dat u de sleutels Azure Cosmos DB kopiëren. De functie-app wordt elke minuut ontwaken en neemt de huidige temperatuur van een aquaviscontainer vast. Zie het artikel Een functie maken in Azure die wordt geactiveerd door een timer voor meer informatie over het instellen van een door een [timer geactiveerde functie-app.](../azure-functions/functions-create-scheduled-function.md)

Ter vereenvoudiging van het scenario is er al een [Time To Live-instelling](./time-to-live.md) geconfigureerd om oudere temperatuurdocumenten op te schonen. 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>Een door het systeem toegewezen beheerde identiteit toewijzen aan een functie-app

In deze stap wijst u een door het systeem toegewezen beheerde identiteit toe aan uw functie-app.

1. Open in [Azure Portal](https://portal.azure.com/)het deelvenster **Azure-functie** en ga naar uw functie-app. 

1. Open het **tabblad Identiteit van**  >  **platformfuncties:** 

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-selection.png" alt-text="Schermopname met platformfuncties en identiteitsopties voor de functie-app.":::

1. Schakel op **het tabblad Identiteit** de optie Systeemidentiteitsstatus in **en** selecteer **Opslaan.**  Het **deelvenster Identiteit** moet er als volgt uitzien:  

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-system-managed-on.png" alt-text="Schermopname van de status van de systeemidentiteit die is ingesteld op Aan.":::

## <a name="grant-access-to-your-azure-cosmos-account"></a>Toegang verlenen tot uw Azure Cosmos-account

In deze stap wijst u een rol toe aan de door het systeem toegewezen beheerde identiteit van de functie-app. Azure Cosmos DB heeft meerdere ingebouwde rollen die u kunt toewijzen aan de beheerde identiteit. Voor deze oplossing gebruikt u de volgende twee rollen:

|Ingebouwde rol  |Description  |
|---------|---------|
|[Inzender voor DocumentDB-account](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Kan uw Azure Cosmos DB beheren. Hiermee kunnen lees-/schrijfsleutels worden opgehaald. |
|[Cosmos DB rol accountlezer](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Kan de Azure Cosmos DB lezen. Hiermee kunt u leessleutels ophalen. |

> [!IMPORTANT]
> Ondersteuning voor op rollen gebaseerd toegangsbeheer in Azure Cosmos DB is alleen van toepassing op besturingsvlakbewerkingen. Gegevensvlakbewerkingen worden beveiligd via primaire sleutels of resourcetokens. Zie het artikel Beveiligde toegang tot gegevens [voor meer](secure-access-to-data.md) informatie.

> [!TIP] 
> Wanneer u rollen toewijst, wijst u alleen de benodigde toegang toe. Als voor uw service alleen gegevens moeten worden gelezen, wijst u de rol **Cosmos DB accountlezer** toe aan de beheerde identiteit. Zie het artikel Minder blootstelling van bevoegde [accounts](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts) voor meer informatie over het belang van toegang met minste bevoegdheden.

In dit scenario leest de functie-app de temperatuur van hetwater en schrijft deze gegevens vervolgens terug naar een container in Azure Cosmos DB. Omdat de functie-app de gegevens moet schrijven, moet u de rol **DocumentDB-accountbijdrager** toewijzen. 

### <a name="assign-the-role-using-azure-portal"></a>De rol toewijzen met behulp van Azure Portal

1. Meld u aan bij Azure Portal en ga naar uw Azure Cosmos DB account. Open het **deelvenster Toegangsbeheer (IAM)** en vervolgens het **tabblad Roltoewijzingen:**

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab.png" alt-text="Schermopname van het deelvenster Toegangsbeheer en het tabblad Roltoewijzingen.":::

1. Selecteer **+ Toevoegen** > **Roltoewijzing toevoegen**.

1. Het **deelvenster Roltoewijzing** toevoegen wordt aan de rechterkant geopend:

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png" alt-text="Schermopname van het deelvenster Roltoewijzing toevoegen.":::

   * **Rol:** Selecteer **Inzender voor DocumentDB-account**
   * **Toegang toewijzen aan**: Selecteer onder de **subsectie Door het** systeem toegewezen beheerde identiteit selecteren de optie **Functie-app.**
   * **Selecteren:** het deelvenster wordt gevuld met alle functie-apps in uw abonnement die een **beheerde systeemidentiteit hebben.** Selecteer in dit geval de **functie-app FishTankTemperatureService:** 

      :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png" alt-text="Schermopname van het deelvenster Roltoewijzing toevoegen, gevuld met voorbeelden.":::

1. Nadat u uw functie-app hebt geselecteerd, selecteert u **Opslaan.**

### <a name="assign-the-role-using-azure-cli"></a>De rol toewijzen met behulp van Azure CLI

Als u de rol wilt toewijzen met behulp van Azure CLI, opent u de Azure Cloud Shell en voert u de volgende opdrachten uit:

```azurecli-interactive

scope=$(az cosmosdb show --name '<Your_Azure_Cosmos_account_name>' --resource-group '<CosmosDB_Resource_Group>' --query id)

principalId=$(az webapp identity show -n '<Your_Azure_Function_name>' -g '<Azure_Function_Resource_Group>' --query principalId)

az role assignment create --assignee $principalId --role "DocumentDB Account Contributor" --scope $scope
```

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>Programmatisch toegang krijgen tot de Azure Cosmos DB sleutels

We hebben nu een functie-app met een door het systeem toegewezen beheerde identiteit met de **rol Inzender voor DocumentDB-account** in Azure Cosmos DB machtigingen. De volgende functie-app-code krijgt de Azure Cosmos DB-sleutels, maakt een CosmosClient-object, krijgt de temperatuur van het aquabulk en sla deze op in Azure Cosmos DB.

In dit voorbeeld wordt de [API List Keys gebruikt](/rest/api/cosmos-db-resource-provider/2021-03-15/databaseaccounts/listkeys) voor toegang tot uw Azure Cosmos DB accountsleutels.

> [!IMPORTANT] 
> Als u de [rol van Cosmos DB accountlezer wilt](#grant-access-to-your-azure-cosmos-account) toewijzen, moet u de API Alleen-lezen sleutels lijst [gebruiken.](/rest/api/cosmos-db-resource-provider/2021-03-15/databaseaccounts/listreadonlykeys) Hiermee worden alleen de alleen-lezensleutels ingevuld.

De API List Keys retourneert het `DatabaseAccountListKeysResult` -object. Dit type is niet gedefinieerd in de C#-bibliotheken. De volgende code toont de implementatie van deze klasse:  

```csharp 
namespace Monitor 
{
  public class DatabaseAccountListKeysResult
  {
      public string primaryMasterKey {get;set;}
      public string primaryReadonlyMasterKey {get; set;}
      public string secondaryMasterKey {get; set;}
      public string secondaryReadonlyMasterKey {get;set;}
  }
}
```

In het voorbeeld wordt ook een eenvoudig document met de naam TemperatureRecord gebruikt, dat als volgt wordt gedefinieerd:

```csharp
using System;

namespace Monitor
{
    public class TemperatureRecord
    {
        public string id { get; set; } = Guid.NewGuid().ToString();
        public DateTime RecordTime { get; set; }
        public int Temperature { get; set; }

    }
}
```

U gebruikt de bibliotheek [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) om het token voor de door het systeem toegewezen beheerde identiteit op te halen. Zie het artikel `Microsoft.Azure.Service.AppAuthentication` [Service-to-serviceverificatie](/dotnet/api/overview/azure/service-to-service-authentication) voor andere manieren om het token op te halen en meer informatie over de bibliotheek te krijgen.


```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Azure.Cosmos;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;

namespace Monitor
{
    public static class FishTankTemperatureService
    {
        private static string subscriptionId =
        "<azure subscription id>";
        private static string resourceGroupName =
        "<name of your azure resource group>";
        private static string accountName =
        "<Azure Cosmos DB account name>";
        private static string cosmosDbEndpoint =
        "<Azure Cosmos DB endpoint>";
        private static string databaseName =
        "<Azure Cosmos DB name>";
        private static string containerName =
        "<container to store the temperature in>";

        [FunctionName("FishTankTemperatureService")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // Authenticate to the Azure Resource Manager to get the Service Managed token.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // Setup the List Keys API to get the Azure Cosmos DB keys.
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // Setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the result back as a DatabaseAccountListKeysResult.
            DatabaseAccountListKeysResult keys = await result.Content.ReadAsAsync<DatabaseAccountListKeysResult>();

            log.LogInformation("Starting to create the client");

            CosmosClient client = new CosmosClient(cosmosDbEndpoint, keys.primaryMasterKey);

            log.LogInformation("Client created");

            var database = client.GetDatabase(databaseName);
            var container = database.GetContainer(containerName);

            log.LogInformation("Get the temperature.");

            var tempRecord = new TemperatureRecord() { RecordTime = DateTime.UtcNow, Temperature = GetTemperature() };

            log.LogInformation("Store temperature");

            await container.CreateItemAsync<TemperatureRecord>(tempRecord);

            log.LogInformation($"Ending temperature monitor: {DateTime.Now}");
        }

        private static int GetTemperature()
        {
            // Fake the temperature sensor for this demo.
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

U bent nu klaar om uw [functie-app te implementeren.](../azure-functions/create-first-function-vs-code-csharp.md)

## <a name="next-steps"></a>Volgende stappen

* [Verificatie op basis van certificaten met Azure Cosmos DB en Azure Active Directory](certificate-based-authentication.md)
* [Uw Azure Cosmos DB beveiligen met Azure Key Vault](access-secrets-from-keyvault.md)
* [Beveiligingsbasislijn voor Azure Cosmos DB](security-baseline.md)

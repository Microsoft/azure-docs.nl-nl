---
title: Een door het systeem toegewezen beheerde identiteit gebruiken voor toegang tot Azure Cosmos DB-gegevens
description: Meer informatie over het configureren van een door Azure Active Directory het systeem toegewezen beheerde identiteit (beheerde service-identiteit voor Azure AD) voor het verkrijgen van toegang tot sleutels van Azure Cosmos DB.
author: j-patrick
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 4d9845fad8c9013bd20499c45a8d1714e30e9dbf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98927409"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>Door het systeem toegewezen beheerde identiteiten gebruiken om toegang te krijgen tot Azure Cosmos DB gegevens
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In dit artikel gaat u een *robuuste neutraal-* oplossing voor het draaien van sleutels instellen om toegang te krijgen tot Azure Cosmos DB toetsen door [beheerde identiteiten](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)te gebruiken. In het voor beeld in dit artikel wordt gebruikgemaakt van Azure Functions, maar u kunt elke service gebruiken die beheerde identiteiten ondersteunt. 

U leert hoe u een functie-app kunt maken die toegang heeft tot Azure Cosmos DB gegevens zonder dat u Azure Cosmos DB sleutels hoeft te kopiëren. De functie-app wordt elke minuut geactiveerd en registreert de huidige Tempe ratuur van een aquarium uit de Vries. Zie voor meer informatie over het instellen van een door een timer geactiveerde functie-app het artikel [een functie maken in azure die wordt geactiveerd door een gebeurtenis](../azure-functions/functions-create-scheduled-function.md) .

Voor het vereenvoudigen van het scenario is de instelling [time to Live](./time-to-live.md) al geconfigureerd voor het opschonen van oudere temperatuur documenten. 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>Een door het systeem toegewezen beheerde identiteit toewijzen aan een functie-app

In deze stap wijst u een door het systeem toegewezen beheerde identiteit toe aan uw functie-app.

1. Open in het [Azure Portal](https://portal.azure.com/)het deel venster **Azure** en ga naar uw functie-app. 

1. Open het tabblad identiteit van **platform functies**  >   : 

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-selection.png" alt-text="Scherm opname van platform functies en identiteits opties voor de functie-app.":::

1. Schakel op het tabblad **identiteit** de **status** van de systeem identiteit **in** en selecteer **Opslaan**. Het **identiteits** venster moet er als volgt uitzien:  

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-system-managed-on.png" alt-text="Scherm afbeelding met de status van de systeem identiteit ingesteld op aan.":::

## <a name="grant-access-to-your-azure-cosmos-account"></a>Toegang verlenen tot uw Azure Cosmos-account

In deze stap wijst u een rol toe aan de door het systeem toegewezen beheerde identiteit van de functie-app. Azure Cosmos DB heeft meerdere ingebouwde rollen die u aan de beheerde identiteit kunt toewijzen. Voor deze oplossing gebruikt u de volgende twee rollen:

|Ingebouwde rol  |Beschrijving  |
|---------|---------|
|[Inzender voor DocumentDB-accounts](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Kan Azure Cosmos DB accounts beheren. Het ophalen van sleutels voor lezen/schrijven is toegestaan. |
|[Rol van Cosmos DB-account lezer](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Kan gegevens van Azure Cosmos DB-account lezen. Kan Lees sleutels ophalen. |

> [!IMPORTANT]
> Ondersteuning voor op rollen gebaseerd toegangs beheer in Azure Cosmos DB is alleen van toepassing op beheer vlak bewerkingen. Gegevensvlak bewerkingen worden beveiligd via primaire sleutels of bron tokens. Zie het artikel [beveiligde toegang tot gegevens](secure-access-to-data.md) voor meer informatie.

> [!TIP] 
> Wijs, wanneer u rollen toewijst, alleen de benodigde toegang toe. Als uw service alleen gegevens heeft gelezen, wijst u de rol van **Cosmos DB-account lezer** toe aan de beheerde identiteit. Zie het artikel [onderbelichting van geprivilegieerde accounts](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts) voor meer informatie over het belang van de minimale toegang tot bevoegdheden.

In dit scenario leest de functie-app de Tempe ratuur van het aquarium en schrijft vervolgens de gegevens terug naar een container in Azure Cosmos DB. Omdat de functie-app de gegevens moet schrijven, moet u de rol Inzender voor het **DocumentDB-account** toewijzen. 

### <a name="assign-the-role-using-azure-portal"></a>De rol toewijzen met behulp van Azure Portal

1. Meld u aan bij de Azure Portal en ga naar uw Azure Cosmos DB-account. Open het deel venster **toegangs beheer (IAM)** en vervolgens het tabblad **roltoewijzingen** :

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab.png" alt-text="Scherm opname van het deel venster toegangs beheer en het tabblad roltoewijzingen.":::

1. Selecteer **+ Toevoegen** > **Roltoewijzing toevoegen**.

1. Het deel venster **roltoewijzing toevoegen** wordt aan de rechter kant geopend:

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png" alt-text="Scherm opname van het deel venster toewijzing van rol toevoegen.":::

   * **Rol**: Selecteer **Inzender** voor het DocumentDB-account
   * **Toegang toewijzen aan**: Selecteer onder de Subsectie door het **systeem toegewezen beheerde identiteit selecteren** de optie **functie-app**.
   * **Selecteren**: het deel venster wordt gevuld met alle functie-apps in uw abonnement die een **beheerde systeem identiteit** hebben. In dit geval selecteert u de functie-app **FishTankTemperatureService** : 

      :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png" alt-text="Scherm opname waarin het deel venster roltoewijzing toevoegen is ingevuld met voor beelden.":::

1. Nadat u de functie-app hebt geselecteerd, selecteert u **Opslaan**.

### <a name="assign-the-role-using-azure-cli"></a>De rol toewijzen met behulp van Azure CLI

Als u de functie wilt toewijzen met behulp van Azure CLI, opent u de Azure Cloud Shell en voert u de volgende opdrachten uit:

```azurecli-interactive

scope=$(az cosmosdb show --name '<Your_Azure_Cosmos_account_name>' --resource-group '<CosmosDB_Resource_Group>' --query id)

principalId=$(az webapp identity show -n '<Your_Azure_Function_name>' -g '<Azure_Function_Resource_Group>' --query principalId)

az role assignment create --assignee $principalId --role "DocumentDB Account Contributor" --scope $scope
```

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>Programmatisch toegang tot de Azure Cosmos DB sleutels

Nu hebben we een functie-app met een door het systeem toegewezen beheerde identiteit met de rol Inzender voor het **DocumentDB-account** in de Azure Cosmos DB machtigingen. Met de volgende functie-app code worden de Azure Cosmos DB sleutels opgehaald, wordt een CosmosClient-object gemaakt, wordt de Tempe ratuur van het aquarium opgehaald en wordt dit vervolgens opgeslagen op Azure Cosmos DB.

In dit voor beeld wordt de [List Keys API](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listkeys) gebruikt om toegang te krijgen tot de sleutels van uw Azure Cosmos DB-account.

> [!IMPORTANT] 
> Als u de rol van [de Cosmos DB-account lezer wilt toewijzen](#grant-access-to-your-azure-cosmos-account) , moet u de [API lijst met alleen-lezen sleutels](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listreadonlykeys)gebruiken. Hiermee worden alleen-lezen sleutels gevuld.

De List Keys-API retourneert het `DatabaseAccountListKeysResult` object. Dit type is niet gedefinieerd in de C#-bibliotheken. De volgende code toont de implementatie van deze klasse:  

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

In het voor beeld wordt ook een eenvoudig document met de naam ' TemperatureRecord ' gebruikt. dit wordt als volgt gedefinieerd:

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

U gebruikt de bibliotheek [micro soft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) om het door het systeem toegewezen beheerde identiteits token op te halen. `Microsoft.Azure.Service.AppAuthentication`Zie het artikel [service-naar-service-verificatie](/dotnet/api/overview/azure/service-to-service-authentication) voor meer informatie over andere manieren om het token op te halen en meer te weten te komen over de-bibliotheek.


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

U bent nu klaar om [uw functie-app te implementeren](../azure-functions/create-first-function-vs-code-csharp.md).

## <a name="next-steps"></a>Volgende stappen

* [Verificatie op basis van certificaten met Azure Cosmos DB en Azure Active Directory](certificate-based-authentication.md)
* [Azure Cosmos DB sleutels beveiligen met behulp van Azure Key Vault](access-secrets-from-keyvault.md)
* [Beveiligings basislijn voor Azure Cosmos DB](security-baseline.md)
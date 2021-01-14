---
title: Programmatisch Azure Service Bus entiteiten maken | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u dynamisch of programmatisch Service Bus naam ruimten en entiteiten kunt inrichten.
ms.devlang: dotnet
ms.topic: article
ms.date: 01/13/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 97d89db17af9cde3afadee430b3d0c2a434e12c9
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210134"
---
# <a name="dynamically-provision-service-bus-namespaces-and-entities"></a>Service Bus naam ruimten en entiteiten dynamisch inrichten 
De Azure Service Bus-beheer bibliotheken kunnen Service Bus naam ruimten en entiteiten dynamisch inrichten. Zo kunt u complexe implementaties en bericht scenario's maken en kunt u programmatisch bepalen welke entiteiten moeten worden ingericht. Deze bibliotheken zijn momenteel beschikbaar voor .NET.

## <a name="supported-functionality"></a>Ondersteunde functionaliteit

* Naam ruimte maken, bijwerken, verwijderen
* Wachtrij maken, bijwerken, verwijderen
* Onderwerp maken, bijwerken, verwijderen
* Abonnementen maken, bijwerken, verwijderen

## <a name="azuremessagingservicebusadministration-recommended"></a>Azure. Messa ging. ServiceBus. Administration (aanbevolen)
U kunt de klasse [ServiceBusAdministrationClient](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient) in de naam ruimte [Azure. Messa ging. ServiceBus. Administration](/dotnet/api/azure.messaging.servicebus.administration) gebruiken voor het beheren van naam ruimten, wacht rijen, onderwerpen en abonnementen. Hier volgt de voorbeeld code. Zie voor een volledig voor beeld [ruwe voorbeeld](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/servicebus/Azure.Messaging.ServiceBus/tests/Samples/Sample07_CrudOperations.cs).

```csharp
using System;
using System.Threading.Tasks;

using Azure.Messaging.ServiceBus.Administration;

namespace adminClientTrack2
{
    class Program
    {
        public static void Main()
        {
            MainAsync().GetAwaiter().GetResult();
        }

        private static async Task MainAsync()
        {
            string connectionString = "SERVICE BUS NAMESPACE CONNECTION STRING";
            string QueueName = "QUEUE NAME";
            string TopicName = "TOPIC NAME";
            string SubscriptionName = "SUBSCRIPTION NAME";

            var adminClient = new ServiceBusAdministrationClient(connectionString);
            bool queueExists = await adminClient.QueueExistsAsync(QueueName);
            if (!queueExists)
            {
                var options = new CreateQueueOptions(QueueName)
                {
                    MaxDeliveryCount = 3                    
                };
                await adminClient.CreateQueueAsync(options);
            }


            bool topicExists = await adminClient.TopicExistsAsync(TopicName);
            if (!topicExists)
            {
                var options = new CreateTopicOptions(TopicName)
                {
                    MaxSizeInMegabytes = 1024
                };
                await adminClient.CreateTopicAsync(options);
            }

            bool subscriptionExists = await adminClient.SubscriptionExistsAsync(TopicName, SubscriptionName);
            if (!subscriptionExists)
            {
                var options = new CreateSubscriptionOptions(TopicName, SubscriptionName)
                {
                    DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0)
                };
                await adminClient.CreateSubscriptionAsync(options);
            }
        }
    }
}

```


## <a name="microsoftazureservicebusmanagement"></a>Micro soft. Azure. ServiceBus. Management 
U kunt de klasse [ManagementClient](/dotnet/api/microsoft.azure.servicebus.management.managementclient) in de naam ruimte [micro soft. Azure. ServiceBus. Management](/dotnet/api/microsoft.azure.servicebus.management) gebruiken voor het beheren van naam ruimten, wacht rijen, onderwerpen en abonnementen. Hier volgt een voor beeld van de code: 

> [!NOTE]
> U wordt aangeraden de `ServiceBusAdministrationClient` klasse uit de `Azure.Messaging.ServiceBus.Administration` bibliotheek te gebruiken. Dit is de meest recente SDK. Zie de [eerste sectie](#azuremessagingservicebusadministration-recommended)voor meer informatie. 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.ServiceBus.Management;

namespace SBusManagementClient
{
    class Program
    {
        public static void Main()
        {
            MainAsync().GetAwaiter().GetResult();
        }

        private static async Task MainAsync()
        {
            string connectionString = "SERVICE BUS NAMESPACE CONNECTION STRING";
            string QueueName = "QUEUE NAME";
            string TopicName = "TOPIC NAME";
            string SubscriptionName = "SUBSCRIPTION NAME";

            var managementClient = new ManagementClient(connectionString);
            bool queueExists = await managementClient.QueueExistsAsync(QueueName);
            if (!queueExists)
            {
                QueueDescription qd = new QueueDescription(QueueName);
                qd.MaxSizeInMB = 1024;
                qd.MaxDeliveryCount = 3;
                await managementClient.CreateQueueAsync(qd);
            }


            bool topicExists = await managementClient.TopicExistsAsync(TopicName);
            if (!topicExists)
            {
                TopicDescription td = new TopicDescription(TopicName);
                td.MaxSizeInMB = 1024;
                td.DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0);
                await managementClient.CreateTopicAsync(td);
            }

            bool subscriptionExists = await managementClient.SubscriptionExistsAsync(TopicName, SubscriptionName);
            if (!subscriptionExists)
            {
                SubscriptionDescription sd = new SubscriptionDescription(TopicName, SubscriptionName);
                sd.DefaultMessageTimeToLive = new TimeSpan(2, 0, 0, 0);
                sd.MaxDeliveryCount = 3;
                await managementClient.CreateSubscriptionAsync(sd);
            }
        }
    }
}
```


## <a name="microsoftazuremanagementservicebus"></a>Microsoft.Azure.Management.ServiceBus 
Deze bibliotheek maakt deel uit van de SDK voor het beheer vlak op basis van het Azure Resource Manager. 

### <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan met deze bibliotheek moet u zich verifiëren bij de service Azure Active Directory (Azure AD). Voor Azure AD moet u worden geverifieerd als Service-Principal, waarmee u toegang hebt tot uw Azure-resources. Zie een van de volgende artikelen voor meer informatie over het maken van een Service-Principal:  

* [Gebruik de Azure Portal om Active Directory-toepassing en Service-Principal te maken die toegang hebben tot resources](../active-directory/develop/howto-create-service-principal-portal.md)
* [Azure PowerShell gebruiken om een service-principal te maken voor toegang tot resources](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Azure CLI gebruiken om een service-principal te maken voor toegang tot resources](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)

Deze zelf studies bieden u een `AppId` (client-id), `TenantId` , en `ClientSecret` (verificatie sleutel), die allemaal worden gebruikt voor verificatie door de beheer bibliotheken. U moet ten minste [**Azure Service Bus gegevens eigenaar**](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) of [**Inzender**](../role-based-access-control/built-in-roles.md#contributor) machtigingen hebben voor de resource groep waarop u wilt uitvoeren.

### <a name="programming-pattern"></a>Programmerings patroon

Het patroon voor het bewerken van een Service Bus resource volgt een gemeen schappelijk Protocol:

1. Een token verkrijgen van Azure AD met behulp van de **micro soft. Identity model. clients. ActiveDirectory** -bibliotheek:
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.azure.com/", new ClientCredential(clientId, clientSecret));
   ```
2. Maak het `ServiceBusManagementClient` object:

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. Stel de `CreateOrUpdate` para meters in op uw opgegeven waarden:

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. De aanroep uitvoeren:

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

### <a name="complete-code-to-create-a-queue"></a>Volledige code voor het maken van een wachtrij
Hier volgt de voorbeeld code voor het maken van een Service Bus wachtrij. Zie het voor [beeld van .net Management op github](https://github.com/Azure-Samples/service-bus-dotnet-management/)voor een volledig voor beeld. 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.Management.ServiceBus;
using Microsoft.Azure.Management.ServiceBus.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;

namespace SBusADApp
{
    class Program
    {
        static void Main(string[] args)
        {
            CreateQueue().GetAwaiter().GetResult();
        }

        private static async Task CreateQueue()
        {
            try
            {
                var subscriptionID = "<SUBSCRIPTION ID>";
                var resourceGroupName = "<RESOURCE GROUP NAME>";
                var namespaceName = "<SERVICE BUS NAMESPACE NAME>";
                var queueName = "<NAME OF QUEUE YOU WANT TO CREATE>";

                var token = await GetToken();

                var creds = new TokenCredentials(token);
                var sbClient = new ServiceBusManagementClient(creds)
                {
                    SubscriptionId = subscriptionID,
                };

                var queueParams = new SBQueue();

                Console.WriteLine("Creating queue...");
                await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, queueName, queueParams);
                Console.WriteLine("Created queue successfully.");
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not create a queue...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

        private static async Task<string> GetToken()
        {
            try
            {
                var tenantId = "<AZURE AD TENANT ID>";
                var clientId = "<APPLICATION/CLIENT ID>";
                var clientSecret = "<CLIENT SECRET>";

                var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

                var result = await context.AcquireTokenAsync(
                    "https://management.azure.com/",
                    new ClientCredential(clientId, clientSecret)
                );

                // If the token isn't a valid string, throw an error.
                if (string.IsNullOrEmpty(result.AccessToken))
                {
                    throw new Exception("Token result is empty!");
                }

                return result.AccessToken;
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not get a token...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

    }
}
```

## <a name="fluent-library"></a>Fluent-bibliotheek
Voor een voor beeld van het gebruik van de Fluent-bibliotheek voor het beheren van Service Bus entiteiten raadpleegt u [dit voor beeld](https://github.com/Azure/azure-libraries-for-net/tree/master/Samples/ServiceBus). 

## <a name="next-steps"></a>Volgende stappen
Zie de volgende onderwerpen voor naslag informatie: 

- [Azure. Messa ging. ServiceBus. Administration](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient)
- [Micro soft. Azure. ServiceBus. Management](/dotnet/api/microsoft.azure.servicebus.management.managementclient)
- [Microsoft.Azure.Management.ServiceBus](/dotnet/api/microsoft.azure.management.servicebus.servicebusmanagementclient)
- [Fluent](/dotnet/api/microsoft.azure.management.servicebus.fluent)
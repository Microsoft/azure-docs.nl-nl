---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 443595a52458d7ff7c168f4c120257cfb60fad2e
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110883"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/dotnet/)
- De nieuwste versie [.net core SDK](https://dotnet.microsoft.com/download/dotnet-core) voor uw besturings systeem.
- Down load de nieuwste versie van de [.net Identity SDK](/dotnet/api/azure.identity).
- Down load de nieuwste versie van de [.net Management SDK](../../concepts/sdk-options.md).

## <a name="installing-the-sdk"></a>De SDK installeren

Neem eerst de Communication Services Management SDK op in uw C#-project:

```csharp
using Azure.ResourceManager.Communication;
```

## <a name="subscription-id"></a>Abonnements-id

U hebt de ID van uw Azure-abonnement nodig. U vindt deze in het portaal:

1.  Meld u aan bij uw Azure-account
2.  Selecteer Abonnementen in de zijbalk aan de linkerzijde
3.  Selecteer het abonnement dat u nodig hebt
4.  Klik op Overzicht
5.  Selecteer uw Abonnements-ID

In deze quickstart wordt ervan uitgegaan dat u de abonnements-ID hebt opgeslagen in een omgevingsvariabele met de naam `AZURE_SUBSCRIPTION_ID`.

## <a name="authentication"></a>Verificatie

Als u wilt communiceren met Azure Communication Services, moet u zichzelf eerst verifiëren bij Azure. Doorgaans doet u dit met behulp van een service-principal-identiteit.

### <a name="option-1-managed-identity"></a>Optie 1: Beheerde identiteit

Als uw code als een service wordt uitgevoerd in Azure, is de eenvoudigste manier om te verifiëren om een beheerde identiteit te verkrijgen van Azure. Meer informatie over [beheerde identiteiten](../../../active-directory/managed-identities-azure-resources/overview.md).

[Azure-services die beheerde identiteiten ondersteunen](../../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

[Beheerde identiteiten gebruiken voor App Service en Azure Functions](../../../app-service/overview-managed-identity.md?tabs=dotnet)

#### <a name="system-assigned-managed-identity"></a>[Door het systeem toegewezen beheerde identiteit](../../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = "AZURE_SUBSCRIPTION_ID";
var acsClient = new CommunicationManagementClient(subscriptionId, new ManagedIdentityCredential());
```

#### <a name="user-assigned-managed-identity"></a>[Door de gebruiker toegewezen beheerde identiteit](../../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity)

De ClientId van de beheerde identiteit die u hebt gemaakt, moet expliciet worden doorgegeven aan de `ManagedIdentityCredential`.

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = "AZURE_SUBSCRIPTION_ID";
var managedIdentityCredential = new ManagedIdentityCredential("AZURE_CLIENT_ID");
var acsClient = new CommunicationManagementClient(subscriptionId, managedIdentityCredential);
```

### <a name="option-2-service-principal"></a>Optie 2: Service-principal

In plaats van een beheerde identiteit te gebruiken, kunt u zich bij Azure verifiëren met behulp van een service-principal die u zelf beheert. Kom meer te weten aan de hand van de documentatie over [het maken en beheren van een service-principal in Azure Active Directory](../../../active-directory/develop/howto-create-service-principal-portal.md).

Nadat u de service-principal hebt gemaakt, moet u de volgende informatie in het Azure-portaal:

- **Client ID**
- **Clientgeheim**
- **Tenant ID**

Sla deze waarden op in omgevingsvariabelen en geef ze respectievelijk de naam `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET` en `AZURE_TENANT_ID`. U kunt vervolgens een client voor Communication Services-beheer maken als volgt:

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
var acsClient = new CommunicationManagementClient(subscriptionId, new EnvironmentCredential());
```

### <a name="option-3-user-identity"></a>Optie 3: Gebruikersidentiteit

Als u Azure namens een interactieve gebruiker wilt aanroepen, in plaats van een service-identiteit te gebruiken, kunt u de volgende code gebruiken om een client voor Azure Communication Services-beheer te maken. Dit opent een browservenster waarin de gebruiker wordt gevraagd om de referenties van MSA of Azure AD op te geven.

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
var communicationServiceClient = new CommunicationManagementClient(subscriptionId, new InteractiveBrowserCredential());
```

## <a name="managing-communication-services-resources"></a>Communication Services-resources beheren

### <a name="interacting-with-azure-resources"></a>Interactie met Azure-resources

Wanneer u geverifieerd bent, kunt u uw beheerclient gebruiken voor API-aanroepen.

Voor elk van de volgende voorbeelden wijzen we onze Communication Services-resources toe aan een bestaande resourcegroep.

Als u een resource groep wilt maken, kunt u dit doen met behulp van de [Azure Portal](../../../azure-resource-manager/management/manage-resource-groups-portal.md) of de [Azure Resource Manager-SDK](https://github.com/Azure/azure-sdk-for-net/blob/master/doc/mgmt_preview_quickstart.md).

### <a name="create-and-manage-a-communication-services-resource"></a>Een Communication Services-resource maken en beheren

Ons exemplaar van de Communication Services Management SDK-client ( ``Azure.ResourceManager.Communication.CommunicationManagementClient`` ) kan worden gebruikt voor het uitvoeren van bewerkingen op communicatie services-resources.

#### <a name="create-a-communication-services-resource"></a>Een Communication Services-resource maken

Wanneer u een Communication Services-resource maakt, geeft u de naam van de resourcegroep en de naam van de resource op. Houd er rekening mee dat de eigenschap `Location` altijd `global` zal zijn, en dat tijdens de openbare preview de waarde `DataLocation` `UnitedStates` moet zijn.

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var resource = new CommunicationServiceResource { Location = "Global", DataLocation = "UnitedStates"  };
var operation = await acsClient.CommunicationService.StartCreateOrUpdateAsync(resourceGroupName, resourceName, resource);
await operation.WaitForCompletionAsync();
```

#### <a name="update-a-communication-services-resource"></a>Een Communication Services-resource bijwerken

```csharp
...
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var resource = new CommunicationServiceResource { Location = "Global", DataLocation = "UnitedStates" };
resource.Tags.Add("environment","test");
resource.Tags.Add("department","tech");
// Use existing resource name and new resource object
var operation = await acsClient.CommunicationService.StartCreateOrUpdateAsync(resourceGroupName, resourceName, resource);
await operation.WaitForCompletionAsync();
```

#### <a name="list-all-communication-services-resources"></a>Een lijst met alle Communication Services-resources maken

```csharp
var resources = acsClient.CommunicationService.ListBySubscription();
foreach (var resource in resources)
{
    Console.WriteLine(resource.Name);
}
```

#### <a name="delete-a-communication-services-resource"></a>Een Communication Services-resource verwijderen

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
await acsClient.CommunicationService.StartDeleteAsync(resourceGroupName, resourceName);
```

## <a name="managing-keys-and-connection-strings"></a>Sleutels en verbindingsreeksen beheren

Elke Communication Services-resource heeft een paar toegangssleutels en bijbehorende verbindingsreeksen. Deze sleutels zijn toegankelijk met de Management SDK en kunnen vervolgens worden gebruikt door andere Sdk's voor communicatie Services om zichzelf te verifiëren bij Azure Communication Services.

#### <a name="get-access-keys-for-a-communication-services-resource"></a>Toegangssleutels voor een Communication Services-resource ophalen

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var keys = await acsClient.CommunicationService.ListKeysAsync(resourceGroupName, resourceName);

Console.WriteLine(keys.Value.PrimaryConnectionString);
Console.WriteLine(keys.Value.SecondaryConnectionString);
```

#### <a name="regenerate-an-access-key-for-a-communication-services-resource"></a>Een toegangssleutel voor een Communication Services-resource opnieuw genereren

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var keyParams = new RegenerateKeyParameters { KeyType = KeyType.Primary };
var keys = await acsClient.CommunicationService.RegenerateKeyAsync(resourceGroupName, resourceName, keyParams);

Console.WriteLine(keys.Value.PrimaryKey);
```
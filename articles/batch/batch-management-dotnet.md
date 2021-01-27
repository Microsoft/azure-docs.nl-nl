---
title: De Batch Management .NET-bibliotheek gebruiken voor het beheren van account resources
description: U kunt Azure Batch account resources maken, verwijderen en wijzigen met behulp van de Batch Management .NET-bibliotheek.
ms.topic: how-to
ms.date: 01/26/2021
ms.custom: seodec18, has-adal-ref, devx-track-csharp
ms.openlocfilehash: f6acf23742b8d4c5c31a5ea952aba5c76b64cae0
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896728"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Batch-accounts en-quota's beheren met de batch-beheer-client bibliotheek voor .NET

U kunt onderhouds overhead in uw Azure Batch-toepassingen verlagen door de [Batch Management .net](/dotnet/api/overview/azure/batch) -bibliotheek te gebruiken voor het automatiseren van het maken van batch-accounts, verwijderen, sleutel beheer en quotum detectie.

- **Maak en verwijder batch-accounts** binnen een wille keurige regio. Als u als een onafhankelijke software leverancier (ISV) bijvoorbeeld een service voor uw clients hebt waarin elk een apart batch-account voor facturerings doeleinden wordt toegewezen, kunt u het maken en verwijderen van accounts toevoegen aan uw klanten Portal.
- De account sleutels op een programmatische manier **ophalen en opnieuw genereren** voor uw batch-accounts. Dit kan u helpen om te voldoen aan het beveiligings beleid dat periodieke rollover of verval datum van account sleutels afdwingt. Wanneer u meerdere batch-accounts hebt in verschillende Azure-regio's, verhoogt de automatisering van dit rollover proces de efficiëntie van uw oplossing.
- **Controleer de account quota's** en neem de proef versie en de fout bij het bepalen van welke batch-accounts zijn beperkt. Door uw account quota's te controleren voordat u taken start, Pools maakt of reken knooppunten toevoegt, kunt u proactief aanpassen waar of wanneer deze reken bronnen worden gemaakt. U kunt bepalen welke accounts quota moeten overschrijden voordat u extra resources in deze accounts toewijst.
- **Combi neer functies van andere Azure-Services** voor een volledig functionele beheer ervaring met behulp van Batch Management .net, [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)en het [Azure Resource Manager](../azure-resource-manager/management/overview.md) samen in dezelfde toepassing. Door deze functies en hun api's te gebruiken, kunt u een wrijvings verificatie-ervaring bieden, de mogelijkheid om resource groepen te maken en te verwijderen en de mogelijkheden die hierboven worden beschreven voor een end-to-end-beheer oplossing.

> [!NOTE]
> Hoewel dit artikel gericht is op het programmatisch beheer van uw batch-accounts, sleutels en quota's, kunt u ook veel van deze activiteiten uitvoeren met [behulp van de Azure Portal](batch-account-create-portal.md).

## <a name="create-and-delete-batch-accounts"></a>Batch-accounts maken en verwijderen

Een van de primaire functies van de API voor batch-beheer is het maken en verwijderen van [batch-accounts](accounts.md) in een Azure-regio. Gebruik hiervoor [BatchManagementClient. account. CreateAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.createasync) en [DeleteAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.deleteasync), of hun synchrone tegen hangers.

Met het volgende code fragment wordt een account gemaakt, wordt het zojuist gemaakte account opgehaald uit de batch-service en vervolgens verwijderd. In dit code fragment en de andere in dit artikel `batchManagementClient` is een volledig geïnitialiseerd exemplaar van [BatchManagementClient](/dotnet/api/microsoft.azure.management.batch.batchmanagementclient).

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [!NOTE]
> Toepassingen die gebruikmaken van de Batch Management .NET-bibliotheek en de BatchManagementClient-klasse, hebben een service beheerder of mede beheerder toegang tot het abonnement dat eigenaar is van het batch-account dat moet worden beheerd. Zie de sectie Azure Active Directory en het code voorbeeld [AccountManagement](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/AccountManagement) voor meer informatie.

## <a name="retrieve-and-regenerate-account-keys"></a>Account sleutels ophalen en opnieuw genereren

Primaire en secundaire account sleutels verkrijgen van een batch-account in uw abonnement met behulp van [GetKeysAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.getkeysasync). U kunt deze sleutels opnieuw genereren met behulp van [RegenerateKeyAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.regeneratekeyasync).

```csharp
// Get and print the primary and secondary keys
BatchAccountGetKeyResult accountKeys =
    await batchManagementClient.Account.GetKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [!TIP]
> U kunt een gestroomlijnde verbindings werk stroom maken voor uw beheer toepassingen. U moet eerst een account sleutel verkrijgen voor het batch-account dat u wilt beheren met [GetKeysAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.getkeysasync). Gebruik deze sleutel bij het initialiseren van de [BatchSharedKeyCredentials](/dotnet/api/microsoft.azure.batch.auth.batchsharedkeycredentials) -klasse van de batch .net-bibliotheek, die wordt gebruikt bij het initialiseren van [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient).

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Azure-abonnement en batch-account quota's controleren

Azure-abonnementen en de afzonderlijke Azure-Services, zoals batch, hebben allemaal standaard quota's die het aantal bepaalde entiteiten in de service beperken. Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md)voor de standaard Quota's voor Azure-abonnementen. Zie [quota's en limieten voor de Azure batch-service](batch-quota-limit.md)voor de standaard quota's van de batch-service. U kunt deze quota's in uw toepassingen controleren met behulp van de Batch Management .NET-bibliotheek. Zo kunt u toewijzings beslissingen nemen voordat u accounts toevoegt of resources zoals groepen en reken knooppunten.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Een Azure-abonnement voor batch-account quota's controleren

Voordat u een batch-account in een regio maakt, kunt u uw Azure-abonnement controleren om te zien of u een account in die regio kunt toevoegen.

In het onderstaande code fragment gebruiken we eerst **ListAsync** voor het ophalen van een verzameling van alle batch-accounts die zich binnen een abonnement bevinden. Zodra deze verzameling is opgehaald, bepalen we hoeveel accounts zich in de doel regio bevinden. Vervolgens gebruiken we **GetQuotasAsync** om het batch-account quotum te verkrijgen en te bepalen hoeveel accounts (indien van toepassing) in die regio kunnen worden gemaakt.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.BatchAccount.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Location.GetQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

In het bovenstaande code fragment `creds` is een instantie van **TokenCredentials**. Zie het [AccountManagement](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/AccountManagement) -code voorbeeld op github voor een voor beeld van het maken van dit object.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Een batch-account voor reken resource quota's controleren

Voordat u de reken resources in uw batch-oplossing verhoogt, kunt u controleren of de resources die u wilt toewijzen, de quota van het account niet overschrijden. In het onderstaande code fragment worden de quotum gegevens voor het batch-account met de naam afgedrukt `mybatchaccount` . In uw eigen toepassing kunt u deze informatie gebruiken om te bepalen of het account kan omgaan met de extra resources die moeten worden gemaakt.

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [!IMPORTANT]
> Hoewel er standaard quota's voor Azure-abonnementen en-services zijn, kunnen veel van deze limieten worden verhoogd door [een quota verhoging in de Azure Portal](batch-quota-limit.md#increase-a-quota)aan te vragen.

## <a name="use-azure-ad-with-batch-management-net"></a>Azure AD gebruiken met Batch Management .NET

De Batch Management .NET-bibliotheek is een Azure resource provider-client en wordt gebruikt in combi natie met [Azure Resource Manager](../azure-resource-manager/management/overview.md) om account bronnen programmatisch te beheren. Azure AD is vereist voor de verificatie van aanvragen die worden gedaan via elke client van een Azure-resource provider, met inbegrip van de Batch Management .NET-bibliotheek en via Azure Resource Manager. Zie [Azure Active Directory gebruiken om batch-oplossingen te verifiëren](batch-aad-auth.md)voor meer informatie over het gebruik van Azure AD met de Batch Management .net-bibliotheek.

## <a name="sample-project-on-github"></a>Voorbeeld project op GitHub

Bekijk het [AccountManagement](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement) -voorbeeld project op github voor meer informatie over Batch Management .net in actie. De voorbeeld toepassing AccountManagement demonstreert de volgende bewerkingen:

1. Verkrijg een beveiligings token van Azure AD met behulp van [ADAL](../active-directory/azuread-dev/active-directory-authentication-libraries.md). Als de gebruiker nog niet is aangemeld, wordt deze gevraagd naar hun Azure-referenties.
2. Met het beveiligings token dat is verkregen van Azure AD, maakt u een [SubscriptionClient](/dotnet/api/microsoft.azure.management.resourcemanager.subscriptionclient) om een lijst met abonnementen te zoeken die aan het account zijn gekoppeld. De gebruiker kan een abonnement in de lijst selecteren als deze meer dan één abonnement bevat.
3. Referenties ophalen die zijn gekoppeld aan het geselecteerde abonnement.
4. Maak een [ResourceManagementClient](/dotnet/api/microsoft.azure.management.resourcemanager.resourcemanagementclient) -object met behulp van de referenties.
5. Gebruik een [ResourceManagementClient](/dotnet/api/microsoft.azure.management.resourcemanager.resourcemanagementclient) -object om een resource groep te maken.
6. Gebruik een [BatchManagementClient](/dotnet/api/microsoft.azure.management.batch.batchmanagementclient) -object om verschillende batch-account bewerkingen uit te voeren:
   - Maak een batch-account in de nieuwe resource groep.
   - Haal het zojuist gemaakte account uit de batch-service.
   - De account sleutels voor het nieuwe account afdrukken.
   - Genereer een nieuwe primaire sleutel voor het account.
   - De quotum gegevens voor het account afdrukken.
   - De quotum gegevens voor het abonnement afdrukken.
   - Alle accounts in het abonnement afdrukken.
   - Verwijder het zojuist gemaakte account.
7. Verwijder de resource groep.

Als u de voorbeeld toepassing wilt uitvoeren, moet u deze eerst registreren bij uw Azure AD-Tenant in de Azure Portal en machtigingen verlenen voor de Azure Resource Manager-API. Volg de stappen in [Batch Management-oplossingen verifiëren met Active Directory](batch-aad-auth-management.md).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [Werkstroom van de batch-service en primaire resources](batch-service-workflow-features.md) als pools, knooppunten, jobs en taken.
- Lees de basisbeginselen van het ontwikkelen van een voor Batch geschikte toepassing met behulp van de [clientbibliotheek Batch .NET](quick-run-dotnet.md) of [Python](quick-run-python.md). Deze Quick starts begeleiden u bij een voorbeeld toepassing die gebruikmaakt van de batch-service om een werk belasting op meerdere reken knooppunten uit te voeren, met behulp van Azure Storage voor het klaarzetten en ophalen van het workload-bestand. git pus

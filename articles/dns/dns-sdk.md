---
title: DNS-zones en-record sets maken met behulp van de .NET SDK
titleSuffix: Azure DNS
description: In dit leer traject gaat u aan de slag met het maken van DNS-zones en-record sets in Azure DNS met behulp van de .NET SDK.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.assetid: eed99b87-f4d4-4fbf-a926-263f7e30b884
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2016
ms.author: rohink
ms.custom: devx-track-csharp
ms.openlocfilehash: 8e116096afbd01af4914be49d5675881724d5069
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015057"
---
# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>DNS-zones en-record sets maken met behulp van de .NET SDK

U kunt bewerkingen automatiseren om DNS-zones, record sets en records te maken, verwijderen of bijwerken met behulp van de DNS-SDK met de .NET DNS-beheer bibliotheek. Hier vindt u een volledig Visual Studio-project [.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Een Service-Principal-account maken

Normaal gesp roken wordt programmatische toegang tot Azure-resources verleend via een toegewezen account in plaats van uw eigen gebruikers referenties. Deze toegewezen accounts worden ' Service Principal-accounts ' genoemd. Als u het voorbeeld project van de Azure DNS SDK wilt gebruiken, moet u eerst een Service-Principal-account maken en de juiste machtigingen toewijzen.

1. Volg [deze instructies](../active-directory/develop/howto-authenticate-service-principal-powershell.md) voor het maken van een Service-Principal-account (in het Azure DNS SDK-voorbeeld project wordt uitgegaan van verificatie op basis van wacht woorden.)
2. Een resource groep maken ([hoe](../azure-resource-manager/templates/deploy-portal.md)).
3. Gebruik Azure RBAC om de machtigingen voor de DNS-zone bijdrager van het service-account te verlenen aan de resource groep ([hoe](../role-based-access-control/role-assignments-portal.md).)
4. Als u het voorbeeld project van de Azure DNS SDK gebruikt, bewerkt u het bestand Program. CS als volgt:

   * Voer de juiste waarden in voor de `tenantId` , `clientId` (ook wel account-id genoemd), `secret` (Service Principal account password) en is `subscriptionId` gebruikt in stap 1.
   * Voer de naam in van de resource groep die u in stap 2 hebt gekozen.
   * Voer de naam van de DNS-zone van uw keuze in.

## <a name="nuget-packages-and-namespace-declarations"></a>NuGet-pakketten en naam ruimte declaraties

Als u de Azure DNS .NET SDK wilt gebruiken, moet u het **Azure DNS Management Library** NuGet-pakket en andere vereiste Azure-pakketten installeren.

1. Open in **Visual Studio** een project of nieuw project.
2. Ga naar **hulpprogram ma's** **>** **NuGet package manager** **>** **NuGet-pakketten beheren voor oplossing...**.
3. Klik op **Bladeren**, schakel het selectie vakje **include Prerelease** in en typ **micro soft. Azure. Management. DNS** in het zoekvak.
4. Selecteer het pakket en klik op **installeren** om dit toe te voegen aan uw Visual Studio-project.
5. Herhaal het bovenstaande proces om ook de volgende pakketten te installeren: **micro soft. rest. ClientRuntime. Azure. Authentication** en **micro soft. Azure. Management. Resource Manager**.

## <a name="add-namespace-declarations"></a>Naamruimtedeclaraties toevoegen

De volgende naam ruimte declaraties toevoegen

```cs
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.Dns;
using Microsoft.Azure.Management.Dns.Models;
```

## <a name="initialize-the-dns-management-client"></a>De DNS-beheer client initialiseren

De `DnsManagementClient` bevat de methoden en eigenschappen die nodig zijn voor het beheren van DNS-zones en-record sets.  De volgende code meldt zich aan bij het account van de Service-Principal en maakt een `DnsManagementClient` object.

```cs
// Build the service credentials and DNS management client
var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
var dnsClient = new DnsManagementClient(serviceCreds);
dnsClient.SubscriptionId = subscriptionId;
```

## <a name="create-or-update-a-dns-zone"></a>Een DNS-zone maken of bijwerken

Als u een DNS-zone wilt maken, maakt u eerst een ' zone '-object dat de para meters van de DNS-zone bevat. Omdat DNS-zones niet zijn gekoppeld aan een specifieke regio, wordt de locatie ingesteld op Global. In dit voor beeld wordt een [Azure Resource Manager ' tag '](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) ook toegevoegd aan de zone.

Als u de zone in Azure DNS daad werkelijk wilt maken of bijwerken, wordt het zone-object dat de zone parameters bevat door gegeven aan de- `DnsManagementClient.Zones.CreateOrUpdateAsyc` methode.

> [!NOTE]
> DnsManagementClient ondersteunt drie werk modi: synchroon (' CreateOrUpdate '), asynchroon (' CreateOrUpdateAsync ') of asynchroon met toegang tot het HTTP-antwoord (' CreateOrUpdateWithHttpMessagesAsync ').  U kunt een van deze modi kiezen, afhankelijk van de behoeften van uw toepassing.

Azure DNS ondersteunt optimistische gelijktijdigheid, met de naam [eTags](./dns-getstarted-powershell.md). In dit voor beeld geeft u ' * ' op voor de header If-None-Match om Azure DNS om een DNS-zone te maken als deze nog niet bestaat.  De aanroep mislukt als er al een zone met de opgegeven naam bestaat in de opgegeven resource groep.

```cs
// Create zone parameters
var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"

// Create an Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
dnsZoneParams.Tags = new Dictionary<string, string>();
dnsZoneParams.Tags.Add("dept", "finance");

// Create the actual zone.
// Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
// Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
// Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");
```

## <a name="create-dns-record-sets-and-records"></a>DNS-record sets en-records maken

DNS-records worden beheerd als een recordset. Een recordset is een set records met dezelfde naam en hetzelfde record type in een zone.  De naam van de recordset is relatief ten opzichte van de zone naam en niet de volledig gekwalificeerde DNS-naam.

Om een RecordSet te maken of bij te werken, wordt het object verzamelings parameters gemaakt en door gegeven aan `DnsManagementClient.RecordSets.CreateOrUpdateAsync` . Net als bij DNS-zones zijn er drie werk modi: synchroon (' CreateOrUpdate '), asynchroon (' CreateOrUpdateAsync ') of asynchroon met toegang tot het HTTP-antwoord (' CreateOrUpdateWithHttpMessagesAsync ').

Net als bij DNS-zones bevatten bewerkingen op record sets ondersteuning voor optimistische gelijktijdigheid.  In dit voor beeld, omdat geen if-Match of If-None-Match is opgegeven, wordt de recordset altijd gemaakt.  Deze aanroep overschrijft een bestaande recordset met dezelfde naam en hetzelfde record type in deze DNS-zone.

```cs
// Create record set parameters
var recordSetParams = new RecordSet();
recordSetParams.TTL = 3600;

// Add records to the record set parameter object.  In this case, we'll add a record of type 'A'
recordSetParams.ARecords = new List<ARecord>();
recordSetParams.ARecords.Add(new ARecord("1.2.3.4"));

// Add metadata to the record set.  Similar to Azure Resource Manager tags, this is optional and you can add multiple metadata name/value pairs
recordSetParams.Metadata = new Dictionary<string, string>();
recordSetParams.Metadata.Add("user", "Mary");

// Create the actual record set in Azure DNS
// Note: no ETAG checks specified, will overwrite existing record set if one exists
var recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSetParams);
```

## <a name="get-zones-and-record-sets"></a>Zones en record sets ophalen

`DnsManagementClient.Zones.Get`Met de `DnsManagementClient.RecordSets.Get` methoden en worden respectievelijk afzonderlijke zones en record sets opgehaald. Record sets worden geïdentificeerd aan de hand van het type, de naam en de zone en de resource groep waarin ze zich bevinden. Zones worden geïdentificeerd aan de hand van hun naam en de resource groep waarin ze zich bevinden.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
```

## <a name="update-an-existing-record-set"></a>Een bestaande recordset bijwerken

Als u een bestaande DNS-recordset wilt bijwerken, moet u eerst de recordset ophalen, vervolgens de inhoud van de recordset bijwerken en vervolgens de wijziging verzenden.  In dit voor beeld geven we de ' ETAG ' op uit de opgehaalde recordset in de if-match-para meter. De aanroep mislukt als een gelijktijdige bewerking de recordset in de tussen tijd heeft gewijzigd.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

// Add a new record to the local object.  Note that records in a record set must be unique/distinct
recordSet.ARecords.Add(new ARecord("5.6.7.8"));

// Update the record set in Azure DNS
// Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);
```

## <a name="list-zones-and-record-sets"></a>Zones en record sets weer geven

Als u zones wilt weer geven, gebruikt u de methoden *DnsManagementClient. zones. list...* , die ondersteuning bieden voor alle zones in een bepaalde resource groep of voor alle zones in een bepaald Azure-abonnement (in resource groepen). Als u record sets wilt weer geven, gebruikt u *DnsManagementClient. recordsets. list...* -methoden, die ondersteuning bieden voor alle record sets in een bepaalde zone of alleen de record sets van een bepaald type.

Opmerking wanneer zones en record sets worden vermeld die de resultaten kunnen pagineren.  In het volgende voor beeld ziet u hoe u de pagina's met resultaten doorloopt. (Een kunst matige kleine pagina grootte van ' 2 ' wordt gebruikt om paginering af te dwingen. in de praktijk moet u deze para meter weglaten en de standaard pagina grootte gebruiken.)

```cs
// Note: in this demo, we'll use a very small page size (2 record sets) to demonstrate paging
// In practice, to improve performance you would use a large page size or just use the system default
int recordSets = 0;
var page = await dnsClient.RecordSets.ListAllInResourceGroupAsync(resourceGroupName, zoneName, "2");
recordSets += page.Count();

while (page.NextPageLink != null)
{
    page = await dnsClient.RecordSets.ListAllInResourceGroupNextAsync(page.NextPageLink);
    recordSets += page.Count();
}
```

## <a name="next-steps"></a>Volgende stappen

Down load het [Azure DNS .NET SDK-voorbeeld project](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True), dat aanvullende voor beelden bevat over het gebruik van de Azure DNS .NET SDK, met inbegrip van voor beelden voor andere DNS-record typen.
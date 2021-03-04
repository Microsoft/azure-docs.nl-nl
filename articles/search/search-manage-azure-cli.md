---
title: Azure CLI-scripts met de zoek module AZ
titleSuffix: Azure Cognitive Search
description: Een Azure Cognitive Search-service maken en configureren met de Azure CLI. U kunt een service omhoog of omlaag schalen, beheer-en query-API-sleutels beheren en query's uitvoeren op systeem gegevens.
manager: luisca
author: DerekLegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: 6287215233ae9baa220df37c6b820c1d1bec7720
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032514"
---
# <a name="manage-your-azure-cognitive-search-service-with-the-azure-cli"></a>Uw Azure Cognitive Search-service beheren met de Azure CLI
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [Azure-CLI](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)

U kunt Azure CLI-opdrachten en-scripts uitvoeren op Windows, macOS, Linux of in [Azure Cloud shell](../cloud-shell/overview.md) om Azure Cognitive Search te maken en te configureren. De [**AZ-Zoek**](/cli/azure/search) module breidt de [Azure cli](/cli/) uit met volledige pariteit van de [rest api's voor zoek beheer](/rest/api/searchmanagement) en de mogelijkheid om de volgende taken uit te voeren:

> [!div class="checklist"]
> * [Zoek Services in een abonnement weer geven](#list-search-services)
> * [Service-informatie retour neren](#get-search-service-information)
> * [Een service maken of verwijderen](#create-or-delete-a-service)
> * [Een service maken met een persoonlijk eind punt](#create-a-service-with-a-private-endpoint)
> * [Beheer-API opnieuw genereren-sleutels](#regenerate-admin-keys)
> * [Query-API-sleutels maken of verwijderen](#create-or-delete-query-keys)
> * [Omhoog of omlaag schalen met replica's en partities](#scale-replicas-and-partitions)
> * [Een gedeelde persoonlijke koppelings bron maken](#create-a-shared-private-link-resource)

Af en toe worden vragen gesteld over taken die *niet* voor komen in de bovenstaande lijst. Op dit moment kunt u niet de **AZ-Zoek** module of het beheer rest API gebruiken om een server naam,-regio of-laag te wijzigen. Toegewezen resources worden toegewezen wanneer een service wordt gemaakt. Voor het wijzigen van de onderliggende hardware (locatie of type knoop punt) is een nieuwe service vereist. Op dezelfde manier zijn er geen hulpprogram ma's of Api's voor het overdragen van inhoud, zoals een index, van de ene service naar de andere.

Binnen een service zijn het maken en beheren van inhoud via [Search Service rest API](/rest/api/searchservice/) of [.NET SDK](/dotnet/api/overview/azure/search.documents-readme). Hoewel er geen speciale Power shell-opdrachten zijn voor inhoud, kunt u scripts schrijven die REST-of .NET-Api's aanroepen om indexen te maken en te laden.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-upgrade"></a>Versies en upgrades controleren

De voor beelden in dit artikel zijn interactief en vereisen verhoogde machtigingen. De Azure CLI moet zijn geïnstalleerd. Zie [De Azure CLI installeren](/cli/azure/install-azure-cli) voor meer informatie.

U kunt nu de Azure CLI uitvoeren met de `az` opdracht vanaf een Windows-opdracht prompt, Power shell of [Azure Cloud shell](../cloud-shell/overview.md). PowerShell biedt enkele functies voor tabvoltooiing die niet beschikbaar zijn in de opdrachtprompt van Windows. 

### <a name="check-the-azure-cli-version"></a>De versie van Azure CLI controleren

Als u niet zeker weet of de Azure CLI is geïnstalleerd, voert u de volgende opdracht uit als een verificatie stap. 

```azurecli-interactive
az --version
```
Als deze opdracht niet werkt, raadpleegt u [de Azure cli installeren](/cli/azure/install-azure-cli) om de Azure CLI op te halen.

Als u versie 2.11.0 of nieuwer hebt, kunt u de `az upgrade` opdracht uitvoeren om de CLI bij te werken naar de meest recente versie.

```azurecli-interactive
az upgrade
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Verbinding maken met Azure met een browser aanmeldings token

U kunt uw aanmeldings referenties voor uw portal gebruiken om verbinding te maken met een abonnement in de Azure CLI. U kunt ook [niet-interactief verifiëren met een Service-Principal](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal).

```azurecli-interactive
az login
```

Als u meerdere Azure-abonnementen hebt, stelt u uw Azure-abonnement in. Voer deze opdracht uit om een lijst met uw huidige abonnementen weer te geven.

```azurecli-interactive
az account list --output table
```

Voer de volgende opdracht uit om het abonnement op te geven. In het volgende voor beeld is de naam van het abonnement `ContosoSubscription` .

```azurecli-interactive
az account set --subscription "ContosoSubscription"
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Services in een abonnement weer geven

De volgende opdrachten zijn van [**AZ resource**](/cli/azure/resource), waarbij informatie wordt geretourneerd over bestaande resources en services die al zijn ingericht in uw abonnement. Als u niet weet hoeveel Zoek Services al zijn gemaakt, retour neren deze opdrachten die informatie en bespaart u een reis naar de portal.

Met de eerste opdracht worden alle zoek services geretourneerd.

```azurecli-interactive
az resource list --resource-type Microsoft.Search/searchServices --output table
```

Ga in de lijst met Services naar informatie over een specifieke resource.

```azurecli-interactive
az resource list --name <service-name>
```

## <a name="list-all-az-search-commands"></a>Alle alle AZ-Zoek opdrachten weer geven

U kunt informatie weer geven over de subgroepen en opdrachten die beschikbaar zijn in [**AZ Search**](/cli/azure/search) vanuit de cli. U kunt ook de [documentatie](/cli/azure/search)raadplegen.

Als u de subgroepen wilt bekijken die beschikbaar zijn in `az search` , voert u de volgende opdracht uit.

```azurecli-interactive
az search --help
```

Het antwoord moet er ongeveer uitzien als in de volgende uitvoer.

```
Group
    az search : Manage Azure Search services, admin keys and query keys.
        WARNING: This command group is in preview and under development. Reference and support
        levels: https://aka.ms/CLI_refstatus
Subgroups:
    admin-key                    : Manage Azure Search admin keys.
    private-endpoint-connection  : Manage Azure Search private endpoint connections.
    private-link-resource        : Manage Azure Search private link resources.
    query-key                    : Manage Azure Search query keys.
    service                      : Manage Azure Search services.
    shared-private-link-resource : Manage Azure Search shared private link resources.

For more specific examples, use: az find "az search"
```

Binnen elke subgroep zijn meerdere opdrachten beschikbaar. U kunt de beschik bare opdrachten voor de `service` subgroep bekijken door de volgende regel uit te voeren.

```azurecli-interactive
az search service --help
```

U kunt ook de beschik bare argumenten voor een bepaalde opdracht bekijken.

```azurecli-interactive
az search service create --help
```

## <a name="get-search-service-information"></a>Informatie over de zoek service ophalen

Als u de resource groep met uw zoek service kent, voert u de [**opdracht AZ Search service show**](/cli/azure/search/service#az_search_service_show) uit om de service definitie te retour neren, inclusief naam, regio, laag en aantal partities.

```azurecli-interactive
az search service show --name <service-name> --resource-group <resource-group-name>
```

## <a name="create-or-delete-a-service"></a>Een service maken of verwijderen

Als u [een nieuwe zoek service wilt maken](search-create-service-portal.md), gebruikt u de opdracht [**AZ Search service Create**](/cli/azure/search/service#az_search_service_show) .

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1
``` 

De resultaten moeten er ongeveer uitzien als in de volgende uitvoer:

```
{
  "hostingMode": "default",
  "id": "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp",
  "identity": null,
  "location": "West US",
  "name": "my-demo-searchapp",
  "networkRuleSet": {
    "bypass": "None",
    "ipRules": []
  },
  "partitionCount": 1,
  "privateEndpointConnections": [],
  "provisioningState": "succeeded",
  "publicNetworkAccess": "Enabled",
  "replicaCount": 1,
  "resourceGroup": "demo-westus",
  "sharedPrivateLinkResources": [],
  "sku": {
    "name": "standard"
  },
  "status": "running",
  "statusDetails": "",
  "tags": null,
  "type": "Microsoft.Search/searchServices"
}
```

### <a name="create-a-service-with-ip-rules"></a>Een service met IP-regels maken

Afhankelijk van uw beveiligings vereisten wilt u mogelijk een zoek service maken met een [geconfigureerde IP-firewall](service-configure-firewall.md). Als u dit wilt doen, geeft u de open bare IP-adressen (v4) of CIDR-bereiken door aan het `ip-rules` argument zoals hieronder wordt weer gegeven. De regels moeten worden gescheiden door een komma ( `,` ) of een punt komma ( `;` ).

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --ip-rules "55.5.63.73;52.228.215.197;101.37.221.205"
```

### <a name="create-a-service-with-a-system-assigned-managed-identity"></a>Een service maken met een door het systeem toegewezen beheerde identiteit

In sommige gevallen, zoals bij het [gebruik van beheerde identiteit om verbinding te maken met een gegevens bron](search-howto-managed-identities-storage.md), moet u de door het [systeem toegewezen beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md)inschakelen. Dit wordt gedaan door toe `--identity-type SystemAssigned` te voegen aan de opdracht.

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --identity-type SystemAssigned
```

## <a name="create-a-service-with-a-private-endpoint"></a>Een service maken met een persoonlijk eind punt

[Privé-eind punten](../private-link/private-endpoint-overview.md) voor Azure Cognitive Search een client in een virtueel netwerk in staat stellen om veilig toegang te krijgen tot gegevens in een zoek index via een [persoonlijke koppeling](../private-link/private-link-overview.md). Het persoonlijke eind punt gebruikt een IP-adres uit de [adres ruimte van het virtuele netwerk](../virtual-network/private-ip-addresses.md) voor uw zoek service. Netwerk verkeer tussen de client en de zoek service gaat over het virtuele netwerk en een privé koppeling op het micro soft-backbone-netwerk, waardoor de bloot stelling van het open bare Internet wordt geëlimineerd. Raadpleeg de documentatie over het [maken van een persoonlijk eind punt voor Azure Cognitive Search](service-create-private-endpoint.md) voor meer informatie.

In het volgende voor beeld ziet u hoe u een zoek service met een persoonlijk eind punt maakt.

Implementeer eerst een zoek service met `PublicNetworkAccess` ingesteld op `Disabled` .

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --public-access Disabled
```

Maak vervolgens een virtueel netwerk en het persoonlijke eind punt.

```azurecli-interactive
# Create the virtual network
az network vnet create \
    --resource-group <resource-group-name> \
    --location "West US" \
    --name <virtual-network-name> \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name <subnet-name> \
    --subnet-prefixes 10.1.0.0/24

# Update the subnet to disable private endpoint network policies
az network vnet subnet update \
    --name <subnet-name> \
    --resource-group <resource-group-name> \
    --vnet-name <virtual-network-name> \
    --disable-private-endpoint-network-policies true

# Get the id of the search service
id=$(az search service show \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --query [id]' \
    --output tsv)

# Create the private endpoint
az network private-endpoint create \
    --name <private-endpoint-name> \
    --resource-group <resource-group-name> \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --private-connection-resource-id $id \
    --group-id searchService \
    --connection-name <private-link-connection-name>  
```

Maak ten slotte een privé-DNS-zone. 

```azurecli-interactive
## Create private DNS zone
az network private-dns zone create \
    --resource-group <resource-group-name> \
    --name "privatelink.search.windows.net"

## Create DNS network link
az network private-dns link vnet create \
    --resource-group <resource-group-name> \
    --zone-name "privatelink.search.windows.net" \
    --name "myLink" \
    --virtual-network <virtual-network-name> \
    --registration-enabled false

## Create DNS zone group
az network private-endpoint dns-zone-group create \
   --resource-group <resource-group-name>\
   --endpoint-name <private-endpoint-name> \
   --name "myZoneGroup" \
   --private-dns-zone "privatelink.search.windows.net" \
   --zone-name "searchServiceZone"
```

Zie voor meer informatie over het maken van privé-eind punten in Power shell deze [koppeling Quick](https://docs.microsoft.com/azure/private-link/create-private-endpoint-cli) start

### <a name="manage-private-endpoint-connections"></a>Verbindingen met privé-eind punten beheren

Naast het maken van een verbinding voor een persoonlijk eind punt, kunt u ook, `show` `update` en `delete` de verbinding.

Als u een verbinding met een privé-eind punt wilt ophalen en de status wilt bekijken, gebruikt u [**AZ Search private-endpoint-Connection show**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_show).

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

Gebruik [**AZ Search private-endpoint-Connection update**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_update)om de verbinding bij te werken. In het volgende voor beeld wordt de verbinding van een persoonlijk eind punt ingesteld op geweigerd:

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
    --status Rejected \
    --description "Rejected" \
    --actions-required "Please fix XYZ"
```

Als u de verbinding met een privé-eind punt wilt verwijderen, gebruikt u [**AZ Search private-endpoint-Connection delete**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_delete).

```azurecli-interactive
az search private-endpoint-connection delete \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

## <a name="regenerate-admin-keys"></a>Beheer sleutels opnieuw genereren

Als u de beheer- [API-sleutels](search-security-api-keys.md)wilt herstellen, gebruikt u [**AZ Search admin-Key renew**](/cli/azure/search/admin-key#az_search_admin_key_renew). Voor geverifieerde toegang worden twee beheer sleutels gemaakt met elke service. Sleutels zijn vereist voor elke aanvraag. Beide beheerders sleutels zijn functioneel gelijkwaardig, waarbij volledige schrijf toegang wordt verleend aan een zoek service met de mogelijkheid om gegevens op te halen of om een wille keurig object te maken en te verwijderen. Er bestaan twee sleutels, zodat u deze kunt gebruiken terwijl u de andere vervangt. 

U kunt slechts één keer opnieuw genereren, opgegeven als de `primary` of- `secondary` sleutel. Voor een ononderbroken service moet u alle client code bijwerken om een secundaire sleutel te gebruiken terwijl u de primaire sleutel rolt. Vermijd het wijzigen van de sleutels tijdens de vlucht.

Zoals u zou verwachten, zullen aanvragen die gebruikmaken van de oude sleutel, mislukken als u sleutels opnieuw genereert zonder de client code bij te werken. Door alle nieuwe sleutels te genereren, wordt uw service niet permanent vergrendeld en hebt u nog steeds toegang tot de service via de portal. Nadat u de primaire en secundaire sleutels opnieuw hebt gegenereerd, kunt u de client code bijwerken voor het gebruik van de nieuwe sleutels en bewerkingen worden hervat.

De waarden voor de API-sleutels worden gegenereerd door de service. U kunt geen aangepaste sleutel opgeven voor Azure Cognitive Search te gebruiken. Op dezelfde manier is er geen door de gebruiker gedefinieerde naam voor beheer-API-sleutels. Verwijzingen naar de sleutel zijn vaste teken reeksen, ofwel `primary` of `secondary` . 

```azurecli-interactive
az search admin-key renew \
    --resource-group <resource-group-name> \
    --service-name <search-service-name> \
    --key-kind primary
```

De resultaten moeten er ongeveer uitzien als in de volgende uitvoer. Beide sleutels worden geretourneerd, zelfs als u slechts één per keer wijzigt.

```   
{
  "primaryKey": <alphanumeric-guid>,
  "secondaryKey": <alphanumeric-guid>  
}
```

## <a name="create-or-delete-query-keys"></a>Query sleutels maken of verwijderen

Als u query- [API-sleutels](search-security-api-keys.md) wilt maken voor alleen-lezen toegang vanuit client-apps naar een Azure Cognitive search-index, gebruikt u [**AZ search query-Key Create**](/cli/azure/search/query-key#az_search_query_key_create). Query sleutels worden gebruikt om te verifiëren bij een specifieke index om Zoek resultaten op te halen. Query sleutels geven geen alleen-lezen toegang tot andere items in de service, zoals een index, gegevens bron of Indexeer functie.

U kunt geen sleutel opgeven voor Azure Cognitive Search te gebruiken. De API-sleutels worden gegenereerd door de service.

```azurecli-interactive
az search query-key create \
    --name myQueryKey \
    --resource-group <resource-group-name> \
    --service-name <search-service-name>
```

## <a name="scale-replicas-and-partitions"></a>Replica's en partities schalen

Gebruik [**AZ Search service update**](/cli/azure/search/service#az_search_service_update)om [replica's en partities te verg Roten of verkleinen](search-capacity-planning.md) . Het verg Roten van replica's of partities wordt toegevoegd aan uw factuur, met zowel vaste als variabele kosten. Als u een tijdelijke behoefte hebt aan extra verwerkings kracht, kunt u replica's en partities verg Roten om de werk belasting te verwerken. Het bewakings gebied op de overzichts portal pagina bevat tegels op query latentie, query's per seconde en beperking, om aan te geven of de huidige capaciteit voldoende is.

Het kan even duren om een item toe te voegen of te verwijderen. Aanpassingen van de capaciteit worden op de achtergrond uitgevoerd, waardoor bestaande workloads kunnen worden voortgezet. Er wordt extra capaciteit gebruikt voor inkomende aanvragen zodra deze klaar zijn, zonder dat er aanvullende configuratie is vereist. 

Het verwijderen van capaciteit kan storend zijn. Het wordt aanbevolen om te voor komen dat alle indexerings-en indexerings taken worden gestopt voordat de capaciteit wordt verminderd. Als dat niet mogelijk is, kunt u overwegen om de capaciteit incrementeel, één replica en partitie tegelijk te verlagen, totdat de nieuwe doel niveaus zijn bereikt.

Wanneer u de opdracht hebt verzonden, is het niet mogelijk om deze halverwege te beëindigen. U moet wachten tot de opdracht is voltooid voordat u de aantallen wijzigt.

```azurecli-interactive
az search service update \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --partition-count 6 \
    --replica-count 6
```

Naast het bijwerken van replica-en partitie tellingen, kunt u ook een update `ip-rules` , `public-access` , en `identity-type` .

## <a name="create-a-shared-private-link-resource"></a>Een gedeelde persoonlijke koppelings bron maken

Privé-eind punten van beveiligde resources die zijn gemaakt via Azure Cognitive Search Api's worden *gedeelde persoonlijke koppelings resources* genoemd. Dit komt doordat u toegang hebt tot een resource, zoals een opslag account, die is geïntegreerd met de [Azure Private Link-service](https://azure.microsoft.com/services/private-link/).

Als u een Indexeer functie gebruikt om gegevens in azure Cognitive Search te indexeren en uw gegevens bron zich op een particulier netwerk bevindt, kunt u een uitgaand [privé-eindpunt verbinding](../private-link/private-endpoint-overview.md) maken om de gegevens te bereiken.

Een volledige lijst met de Azure-resources waarvoor u uitgaande privé-eind punten kunt maken van Azure Cognitive Search, vindt u [hier](search-indexer-howto-access-private.md#shared-private-link-resources-management-apis) samen met de waarden van de verwante **groeps-id's** .

Als u de gedeelde persoonlijke koppelings resource wilt maken, gebruikt u [**AZ Search Shared-Private-link-resource create**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list). Houd er rekening mee dat er mogelijk een configuratie vereist is voor de gegevens bron voordat u deze opdracht uitvoert.

```azurecli-interactive
az search shared-private-link-resource create \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> \
    --group-id blob \
    --resource-id "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/myBlobStorage"  \
    --request-message "Please approve" 
```


Als u de resources van de gedeelde persoonlijke koppeling wilt ophalen en hun status wilt bekijken, gebruikt u [**AZ Search Shared-Private-link-Resource List**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list).

```azurecli-interactive
az search shared-private-link-resource list \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

U moet de verbinding goed keuren met de volgende opdracht voordat deze kan worden gebruikt. De ID van de verbinding voor het persoonlijke eind punt moet worden opgehaald uit de onderliggende resource. In dit geval krijgen we de verbindings-ID van AZ Storage.

```azurecli-interactive
id = (az storage account show -n myBlobStorage --query "privateEndpointConnections[0].id")

az network private-endpoint-connection approve --id $id
```

Als u de gedeelde persoonlijke koppelings resource wilt verwijderen, gebruikt u [**AZ Search Shared-Private-link-resource delete**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_delete).

```azurecli-interactive
az search shared-private-link-resource delete \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

Zie de documentatie over het [maken van Indexeer functie verbindingen via een persoonlijk eind punt](search-indexer-howto-access-private.md)voor meer informatie over het instellen van gedeelde persoonlijke koppelings bronnen.

## <a name="next-steps"></a>Volgende stappen

Bouw een [index](search-what-is-an-index.md), [Zoek een query op een index](search-query-overview.md) met behulp van de portal, rest-API'S of de .NET SDK.

* [Een Azure Cognitive Search-index maken in de Azure Portal](search-get-started-portal.md)
* [Een Indexeer functie instellen voor het laden van gegevens uit andere services](search-indexer-overview.md)
* [Query's uitvoeren op een Azure Cognitive Search-index met behulp van Search Explorer in de Azure Portal](search-explorer.md)
* [Azure Cognitive Search gebruiken in .NET](search-howto-dotnet-sdk.md)

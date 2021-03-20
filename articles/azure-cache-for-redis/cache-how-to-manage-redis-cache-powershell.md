---
title: Azure-cache beheren voor redis met Azure PowerShell
description: Meer informatie over het uitvoeren van beheer taken voor Azure cache voor redis met behulp van Azure PowerShell.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: yegu
ms.openlocfilehash: 88e433dbfa87d8ea483789b1fd838c62a6a481c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92536756"
---
# <a name="manage-azure-cache-for-redis-with-azure-powershell"></a>Azure-cache beheren voor redis met Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [Azure-CLI](cache-manage-cli.md)
> 
> 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In dit onderwerp wordt beschreven hoe u algemene taken uitvoert, zoals het maken, bijwerken en schalen van uw Azure-cache voor redis-exemplaren, het opnieuw genereren van toegangs sleutels en het weer geven van informatie over uw caches. Zie [Azure cache for redis-cmdlets](/powershell/module/az.rediscache)voor een volledige lijst met Azure cache voor redis Power shell-cmdlets.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Zie Azure Resource Manager vs. klassieke implementatie voor meer informatie over het klassieke implementatie model [: inzicht in implementatie modellen en de status van uw resources](../azure-resource-manager/management/deployment-models.md).

## <a name="prerequisites"></a>Vereisten
Als u Azure PowerShell al hebt geïnstalleerd, moet u Azure PowerShell versie 1.0.0 of hoger hebben. U kunt controleren welke versie van Azure PowerShell u met deze opdracht hebt geïnstalleerd bij de Azure PowerShell opdracht prompt.

```azurepowershell
    Get-Module Az | format-table version
```

U moet zich eerst aanmelden bij Azure met deze opdracht.

```azurepowershell
    Connect-AzAccount
```

Geef het e-mail adres van uw Azure-account en het bijbehorende wacht woord op in het dialoog venster Microsoft Azure aanmelden.

Als u meerdere Azure-abonnementen hebt, moet u uw Azure-abonnement instellen. Voer deze opdracht uit om een lijst met uw huidige abonnementen weer te geven.

```azurepowershell
    Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Voer de volgende opdracht uit om het abonnement op te geven. In het volgende voor beeld is de naam van het abonnement `ContosoSubscription` .

```azurepowershell
    Select-AzSubscription -SubscriptionName ContosoSubscription
```

Voordat u Windows Power shell met Azure Resource Manager kunt gebruiken, hebt u het volgende nodig:

* Windows Power shell, versie 3,0 of 4,0. Als u de versie van Windows Power shell wilt zoeken, typt u: `$PSVersionTable` en verifieert u de waarde van `PSVersion` 3,0 of 4,0. Zie [Windows Management Framework 3,0](https://www.microsoft.com/download/details.aspx?id=34595)om een compatibele versie te installeren.

Gebruik de cmdlet Get-Help om gedetailleerde informatie te krijgen over de cmdlets die u in deze zelf studie ziet.

```azurepowershell
    Get-Help <cmdlet-name> -Detailed
```

Als u bijvoorbeeld hulp voor de cmdlet wilt weer geven `New-AzRedisCache` , typt u:

```azurepowershell
    Get-Help New-AzRedisCache -Detailed
```

### <a name="how-to-connect-to-other-clouds"></a>Verbinding maken met andere Clouds
De Azure-omgeving is standaard `AzureCloud` , die de algemene Azure Cloud-instantie vertegenwoordigt. Als u verbinding wilt maken met een ander exemplaar, gebruikt u de `Connect-AzAccount` opdracht met de `-Environment` of- `EnvironmentName` opdracht regel optie met de gewenste omgeving of omgevings naam.

Voer de cmdlet uit om de lijst met beschik bare omgevingen weer te geven `Get-AzEnvironment` .

### <a name="to-connect-to-the-azure-government-cloud"></a>Verbinding maken met de Azure Government Cloud
Als u verbinding wilt maken met de Azure Government Cloud, gebruikt u een van de volgende opdrachten.

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
```

of

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureUSGovernment)
```

Als u een cache in de Azure Government Cloud wilt maken, gebruikt u een van de volgende locaties.

* USGov Virginia
* USGov Iowa

Zie [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) en [Microsoft Azure Government ontwikkelaars handleiding](../azure-government/documentation-government-developer-guide.md)voor meer informatie over de Azure Government Cloud.

### <a name="to-connect-to-the-azure-china-cloud"></a>Verbinding maken met de cloud van Azure China
Als u verbinding wilt maken met de Azure China-Cloud, gebruikt u een van de volgende opdrachten.

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureChinaCloud
```

of

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureChinaCloud)
```

Als u een cache wilt maken in de Azure China-Cloud, gebruikt u een van de volgende locaties.

* China East
* China - noord

Zie [AzureChinaCloud voor Azure beheerd door 21vianet in China](https://www.windowsazure.cn/)voor meer informatie over de cloud van Azure China.

### <a name="to-connect-to-microsoft-azure-germany"></a>Verbinding maken met Microsoft Azure Duitsland
Als u verbinding wilt maken met Microsoft Azure Duitsland, gebruikt u een van de volgende opdrachten.

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureGermanCloud
```

of

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureGermanCloud)
```

Als u een cache in Microsoft Azure Duitsland wilt maken, gebruikt u een van de volgende locaties.

* Duitsland - centraal
* Duitsland - noordoost

Zie [Microsoft Azure Duitsland](https://azure.microsoft.com/overview/clouds/germany/)voor meer informatie over Microsoft Azure Duitsland.

### <a name="properties-used-for-azure-cache-for-redis-powershell"></a>Eigenschappen die worden gebruikt voor Azure-cache voor redis Power shell
De volgende tabel bevat eigenschappen en beschrijvingen voor veelgebruikte para meters bij het maken en beheren van uw Azure-cache voor redis-exemplaren met behulp van Azure PowerShell.

| Parameter | Beschrijving | Standaard |
| --- | --- | --- |
| Name |Naam van de cache | |
| Locatie |Locatie van de cache | |
| ResourceGroupName |Naam van de resource groep waarin de cache moet worden gemaakt | |
| Grootte |De grootte van de cache. Geldige waarden zijn: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250MB, 1GB, 2,5 GB, 6GB, 13GB, 26GB, 53GB |1 GB |
| ShardCount |Het aantal Shards dat moet worden gemaakt bij het maken van een Premium-cache met clustering ingeschakeld. Geldige waarden zijn: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| SKU |Hiermee geeft u de SKU van de cache op. Geldige waarden zijn: Basic, Standard, Premium |Standard |
| RedisConfiguration |Hiermee geeft u de redis-configuratie-instellingen. Zie de volgende tabel met [RedisConfiguration-eigenschappen](#redisconfiguration-properties) voor meer informatie over elke instelling. | |
| EnableNonSslPort |Hiermee wordt aangegeven of de niet-SSL-poort is ingeschakeld. |Niet waar |
| MaxMemoryPolicy |Deze para meter is afgeschaft: gebruik in plaats daarvan RedisConfiguration. | |
| StaticIP |Bij het hosten van uw cache in een VNET, geeft u een uniek IP-adres op in het subnet voor de cache. Indien niet opgegeven, wordt er een gekozen uit het subnet. | |
| Subnet |Wanneer u uw cache host in een VNET, geeft u de naam op van het subnet waarin de cache moet worden geïmplementeerd. | |
| VirtualNetwork |Wanneer u uw cache in een VNET host, geeft u de resource-ID op van het VNET waarin de cache moet worden geïmplementeerd. | |
| KeyType |Hiermee geeft u op welke toegangs sleutel opnieuw moet worden gegenereerd bij het vernieuwen van toegangs sleutels. Geldige waarden zijn: primair, secundair | |

### <a name="redisconfiguration-properties"></a>RedisConfiguration-eigenschappen
| Eigenschap | Beschrijving | Prijscategorieën |
| --- | --- | --- |
| RDB-back-up-ingeschakeld |Of [redis-gegevens persistentie](cache-how-to-premium-persistence.md) is ingeschakeld |Alleen Premium |
| RDB-Storage-verbindings reeks |Het connection string naar het opslag account voor [redis-gegevens persistentie](cache-how-to-premium-persistence.md) |Alleen Premium |
| RDB-back-up-frequentie |De back-upfrequentie voor [redis-gegevens persistentie](cache-how-to-premium-persistence.md) |Alleen Premium |
| maxmemory-gereserveerd |Hiermee configureert u het geheugen dat is [gereserveerd](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) voor niet-cache processen |Standard en Premium |
| maxmemory-beleid |Hiermee configureert u het [verwijderings beleid](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) voor de cache |Alle prijs Categorieën |
| melding: gebeurtenis ruimte-gebeurtenissen |Hiermee configureert u meldingen voor de [beschik bare ruimte](cache-configure.md#keyspace-notifications-advanced-settings) |Standard en Premium |
| hash-Max-ziplist-vermeldingen |Configureert [geheugen optimalisatie](https://redis.io/topics/memory-optimization) voor kleine statistische gegevens typen |Standard en Premium |
| hash-Max-ziplist-value |Configureert [geheugen optimalisatie](https://redis.io/topics/memory-optimization) voor kleine statistische gegevens typen |Standard en Premium |
| set-Max-intset-items |Configureert [geheugen optimalisatie](https://redis.io/topics/memory-optimization) voor kleine statistische gegevens typen |Standard en Premium |
| zset-Max-ziplist-vermeldingen |Configureert [geheugen optimalisatie](https://redis.io/topics/memory-optimization) voor kleine statistische gegevens typen |Standard en Premium |
| zset-Max-ziplist-value |Configureert [geheugen optimalisatie](https://redis.io/topics/memory-optimization) voor kleine statistische gegevens typen |Standard en Premium |
| databases |Hiermee configureert u het aantal data bases. Deze eigenschap kan alleen worden geconfigureerd bij het maken van de cache. |Standard en Premium |

## <a name="to-create-an-azure-cache-for-redis"></a>Een Azure-cache maken voor redis
Nieuwe Azure-cache voor redis-exemplaren worden gemaakt met de cmdlet [New-AzRedisCache](/powershell/module/az.rediscache/new-azrediscache) .

> [!IMPORTANT]
> De eerste keer dat u een Azure-cache maakt voor redis in een abonnement met behulp van de Azure Portal, registreert de Portal de `Microsoft.Cache` naam ruimte voor dat abonnement. Als u probeert de eerste Azure-cache voor redis in een abonnement te maken met behulp van Power shell, moet u die naam ruimte eerst registreren met de volgende opdracht. andere cmdlets, zoals `New-AzRedisCache` en `Get-AzRedisCache` mislukken.
> 
> `Register-AzResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

Voer de volgende opdracht uit om een lijst met beschik bare para meters en beschrijvingen ervan weer te geven voor `New-AzRedisCache` .

```azurepowershell
    PS C:\> Get-Help New-AzRedisCache -detailed

    NAME
        New-AzRedisCache

    SYNOPSIS
        Creates a new Azure Cache for Redis.


    SYNTAX
        New-AzRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]


    DESCRIPTION
        The New-AzRedisCache cmdlet creates a new Azure Cache for Redis.


    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to create.

        -ResourceGroupName <String>
            Name of resource group in which to create the Azure Cache for Redis.

        -Location <String>
            Location in which to create the Azure Cache for Redis.

        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.

        -Size <String>
            Size of the Azure Cache for Redis. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of Azure Cache for Redis. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Cache for Redis. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}

        -Subnet <String>
            Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network.

        -StaticIP <String>
            Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

Als u een cache met standaard parameters wilt maken, voert u de volgende opdracht uit.

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"
```

`ResourceGroupName`, `Name` en `Location` zijn vereiste para meters, maar de rest is optioneel en hebben standaard waarden. Als u de vorige opdracht uitvoert, wordt een standaard-SKU Azure-cache gemaakt voor een redis-exemplaar met de opgegeven naam, locatie en resource groep, die 1 GB groot is als de niet-SSL-poort is uitgeschakeld.

Als u een Premium-cache wilt maken, geeft u een grootte van P1 (6 GB-60 GB), P2 (13 GB-130 GB), P3 (26 GB-260 GB) of P4 (53 GB-530 GB). Als u Clustering wilt inschakelen, geeft u een aantal Shard op met behulp van de `ShardCount` para meter. In het volgende voor beeld wordt een P1 Premium-cache gemaakt met 3 Shards. Een P1 Premium-cache is 6 GB groot en omdat we drie Shards hebben opgegeven, is de totale grootte 18 GB (3 x 6 GB).

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3
```

Als u waarden voor de `RedisConfiguration` para meter wilt opgeven, moet u de waarden in `{}` als sleutel/waarde-paren `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}` . In het volgende voor beeld wordt een standaard cache van 1 GB gemaakt met `allkeys-random` maxmemory-beleid en-meldingen die zijn geconfigureerd met `KEA` . Zie voor meer informatie de [ruimte meldingen (geavanceerde instellingen)](cache-configure.md#keyspace-notifications-advanced-settings) en het [geheugen beleid](cache-configure.md#memory-policies).

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}
```

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>De instellingen van de data base configureren tijdens het maken van de cache
De `databases` instelling kan alleen worden geconfigureerd tijdens het maken van de cache. In het volgende voor beeld wordt een Premium P3 (26 GB) cache met 48-data bases gemaakt met behulp van de cmdlet [New-AzRedisCache](/powershell/module/az.rediscache/New-azRedisCache) .

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}
```

Zie voor meer informatie over de `databases` eigenschap [standaard Azure-cache voor redis-server configuratie](cache-configure.md#default-redis-server-configuration). Voor meer informatie over het maken van een cache met behulp van de cmdlet [New-AzRedisCache](/powershell/module/az.rediscache/new-azrediscache) raadpleegt u de sectie voor het maken van een Azure-cache voor redis.

## <a name="to-update-an-azure-cache-for-redis"></a>Een Azure-cache bijwerken voor redis
Azure cache voor redis-exemplaren wordt bijgewerkt met de cmdlet [set-AzRedisCache](/powershell/module/az.rediscache/Set-azRedisCache) .

Voer de volgende opdracht uit om een lijst met beschik bare para meters en beschrijvingen ervan weer te geven voor `Set-AzRedisCache` .

```azurepowershell
    PS C:\> Get-Help Set-AzRedisCache -detailed

    NAME
        Set-AzRedisCache

    SYNOPSIS
        Set Azure Cache for Redis updatable parameters.

    SYNTAX
        Set-AzRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]

    DESCRIPTION
        The Set-AzRedisCache cmdlet sets Azure Cache for Redis parameters.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to update.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -Size <String>
            Size of the Azure Cache for Redis. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of Azure Cache for Redis. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Cache for Redis. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

De `Set-AzRedisCache` cmdlet kan worden gebruikt voor het bijwerken van eigenschappen zoals `Size` ,, `Sku` `EnableNonSslPort` en de `RedisConfiguration` waarden. 

Met de volgende opdracht wordt het maxmemory-beleid voor de Azure-cache bijgewerkt voor redis met de naam myCache.

```azurepowershell
    Set-AzRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}
```

<a name="scale"></a>

## <a name="to-scale-an-azure-cache-for-redis"></a>Een Azure-cache schalen voor redis
`Set-AzRedisCache` kan worden gebruikt voor het schalen van een Azure-cache voor redis-instantie wanneer de `Size` , `Sku` of- `ShardCount` eigenschappen worden gewijzigd. 

> [!NOTE]
> Voor het schalen van een cache met behulp van Power shell gelden dezelfde limieten en richt lijnen als voor het schalen van een cache van de Azure Portal. U kunt schalen naar een andere prijs categorie met de volgende beperkingen.
> 
> * U kunt niet schalen van een hogere prijs categorie naar een lagere prijs categorie.
> * U kunt de schaal van een **Premium** -cache niet verlagen naar een **Standard** -of een **Basic** -cache.
> * U kunt niet omlaag schalen vanuit een **standaard** cache naar een **Basic** -cache.
> * U kunt schalen van een **basis** cache naar een **standaard** cache, maar u kunt de grootte niet tegelijkertijd wijzigen. Als u een andere grootte nodig hebt, kunt u een volgende schaal bewerking uitvoeren voor de gewenste grootte.
> * U kunt niet rechtstreeks schalen van een **Basic** -cache naar een **Premium** -cache. U moet van **Basic** naar **Standard** schalen in één schaal bewerking en vervolgens van **standaard** naar **Premium** bij een volgende schaal bewerking.
> * U kunt niet van een grotere grootte schalen naar de grootte van de **C0 (250 MB)** .
> 
> Zie [Azure-cache schalen voor redis](cache-how-to-scale.md)voor meer informatie.
> 
> 

In het volgende voor beeld ziet u hoe u een cache kunt schalen met de naam `myCache` in een cache van 2,5 GB. Houd er rekening mee dat deze opdracht voor zowel een basis-als een standaard cache werkt.

```azurepowershell
    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

Nadat deze opdracht is gegeven, wordt de status van de cache geretourneerd (vergelijkbaar met aanroepen `Get-AzRedisCache` ). Houd er rekening mee dat `ProvisioningState` `Scaling` .

```azurepowershell
    PS C:\> Set-AzRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB


    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Scaling
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
                         [maxmemory-delta, 150]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : mygroup
    PrimaryKey         : ....
    SecondaryKey       : ....
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :
```

Wanneer de schaal bewerking is voltooid, worden de `ProvisioningState` wijzigingen in `Succeeded` . Als u een volgende schaal bewerking wilt uitvoeren, zoals het wijzigen van de standaard waarde in Standard en u de grootte wilt wijzigen, moet u wachten tot de vorige bewerking is voltooid of wordt er een fout bericht weer gegeven dat vergelijkbaar is met het volgende.

```azurepowershell
    Set-AzRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.
```

## <a name="to-get-information-about-an-azure-cache-for-redis"></a>Informatie over een Azure-cache voor redis ophalen
U kunt informatie over een cache ophalen met behulp van de cmdlet [Get-AzRedisCache](/powershell/module/az.rediscache/get-azrediscache) .

Voer de volgende opdracht uit om een lijst met beschik bare para meters en beschrijvingen ervan weer te geven voor `Get-AzRedisCache` .

```azurepowershell
    PS C:\> Get-Help Get-AzRedisCache -detailed

    NAME
        Get-AzRedisCache

    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.

    SYNTAX
        Get-AzRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]

    DESCRIPTION
        The Get-AzRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzRedisCache will return details about the
        specific cache name provided.

        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.

        If no parameters are given than it will return details about all caches the current subscription.

    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzRedisCache
            returns the details for the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

Om informatie over alle caches in het huidige abonnement te retour neren `Get-AzRedisCache` zonder para meters.

```azurepowershell
    Get-AzRedisCache
```

Om informatie over alle caches in een specifieke resource groep te retour neren, voert u uit `Get-AzRedisCache` met de `ResourceGroupName` para meter.

```azurepowershell
    Get-AzRedisCache -ResourceGroupName myGroup
```

Om informatie over een specifieke cache te retour neren, voert u uit `Get-AzRedisCache` met de `Name` para meter met de naam van de cache en de `ResourceGroupName` para meter met de resource groep die die cache bevat.

```azurepowershell
    PS C:\> Get-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Succeeded
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
                         [maxclients, 1000]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : myGroup
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :
```

## <a name="to-retrieve-the-access-keys-for-an-azure-cache-for-redis"></a>De toegangs sleutels voor een Azure-cache ophalen voor redis
Als u de toegangs sleutels voor uw cache wilt ophalen, kunt u de cmdlet [Get-AzRedisCacheKey](/powershell/module/az.rediscache/Get-azRedisCacheKey) gebruiken.

Voer de volgende opdracht uit om een lijst met beschik bare para meters en beschrijvingen ervan weer te geven voor `Get-AzRedisCacheKey` .

```azurepowershell
    PS C:\> Get-Help Get-AzRedisCacheKey -detailed

    NAME
        Get-AzRedisCacheKey

    SYNOPSIS
        Gets the accesskeys for the specified Azure Cache for Redis.


    SYNTAX
        Get-AzRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]

    DESCRIPTION
        The Get-AzRedisCacheKey cmdlet gets the access keys for the specified cache.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

Als u de sleutels voor uw cache wilt ophalen, roept u de `Get-AzRedisCacheKey` cmdlet aan en geeft u de naam van de cache door in de naam van de resource groep die de cache bevat.

```azurepowershell
    PS C:\> Get-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=
```

## <a name="to-regenerate-access-keys-for-your-azure-cache-for-redis"></a>Toegangs sleutels voor uw Azure-cache opnieuw genereren voor redis
Als u de toegangs sleutels voor uw cache opnieuw wilt genereren, kunt u de cmdlet [New-AzRedisCacheKey](/powershell/module/az.rediscache/New-azRedisCacheKey) gebruiken.

Voer de volgende opdracht uit om een lijst met beschik bare para meters en beschrijvingen ervan weer te geven voor `New-AzRedisCacheKey` .

```azurepowershell
    PS C:\> Get-Help New-AzRedisCacheKey -detailed

    NAME
        New-AzRedisCacheKey

    SYNOPSIS
        Regenerates the access key of an Azure Cache for Redis.

    SYNTAX
        New-AzRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]

    DESCRIPTION
        The New-AzRedisCacheKey cmdlet regenerate the access key of an Azure Cache for Redis.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -KeyType <String>
            Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.

        -Force
            When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

Als u de primaire of secundaire sleutel voor uw cache opnieuw wilt genereren, roept u de `New-AzRedisCacheKey` cmdlet aan en geeft u de naam en de resource groep op, en geeft u `Primary` of `Secondary` voor de `KeyType` para meter op. In het volgende voor beeld wordt de secundaire toegangs sleutel voor een cache opnieuw gegenereerd.

```azurepowershell
    PS C:\> New-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=
```

## <a name="to-delete-an-azure-cache-for-redis"></a>Een Azure-cache voor redis verwijderen
Als u een Azure-cache voor redis wilt verwijderen, gebruikt u de cmdlet [Remove-AzRedisCache](/powershell/module/az.rediscache/remove-azrediscache) .

Voer de volgende opdracht uit om een lijst met beschik bare para meters en beschrijvingen ervan weer te geven voor `Remove-AzRedisCache` .

```azurepowershell
    PS C:\> Get-Help Remove-AzRedisCache -detailed

    NAME
        Remove-AzRedisCache

    SYNOPSIS
        Remove Azure Cache for Redis if exists.

    SYNTAX
        Remove-AzRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>

    DESCRIPTION
        The Remove-AzRedisCache cmdlet removes an Azure Cache for Redis if it exists.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to remove.

        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.

        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.

        -PassThru
            By default Remove-AzRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzRedisCache returns a boolean value indicating the success of the operatio

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```

In het volgende voor beeld wordt de naam van de cache `myCache` verwijderd.

```azurepowershell
    PS C:\> Remove-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```


## <a name="to-import-an-azure-cache-for-redis"></a>Een Azure-cache voor redis importeren
U kunt gegevens importeren in een Azure-cache voor redis-exemplaar met behulp van de- `Import-AzRedisCache` cmdlet.

> [!IMPORTANT]
> Import/export is alleen beschikbaar voor [Premium-laag](cache-overview.md#service-tiers) caches. Zie [gegevens importeren en exporteren in azure-cache voor redis](cache-how-to-import-export-data.md)voor meer informatie over importeren/exporteren.
> 
> 

Voer de volgende opdracht uit om een lijst met beschik bare para meters en beschrijvingen ervan weer te geven voor `Import-AzRedisCache` .

```azurepowershell
    PS C:\> Get-Help Import-AzRedisCache -detailed

    NAME
        Import-AzRedisCache

    SYNOPSIS
        Import data from blobs to Azure Cache for Redis.


    SYNTAX
        Import-AzRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Import-AzRedisCache cmdlet imports data from the specified blobs into Azure Cache for Redis.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Files <String[]>
            SAS urls of blobs whose content should be imported into the cache.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -Force
            When the Force parameter is provided, import will be performed without any confirmation prompts.

        -PassThru
            By default Import-AzRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzRedisCache returns a boolean value indicating the success of the
            operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```


Met de volgende opdracht worden gegevens uit de blob die is opgegeven door de SAS-URI in azure cache voor redis geïmporteerd.

```azurepowershell
    PS C:\>Import-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force
```

## <a name="to-export-an-azure-cache-for-redis"></a>Een Azure-cache exporteren voor redis
U kunt gegevens exporteren vanuit een Azure-cache voor een redis-exemplaar met behulp van de- `Export-AzRedisCache` cmdlet.

> [!IMPORTANT]
> Import/export is alleen beschikbaar voor [Premium-laag](cache-overview.md#service-tiers) caches. Zie [gegevens importeren en exporteren in azure-cache voor redis](cache-how-to-import-export-data.md)voor meer informatie over importeren/exporteren.
> 
> 

Voer de volgende opdracht uit om een lijst met beschik bare para meters en beschrijvingen ervan weer te geven voor `Export-AzRedisCache` .

```azurepowershell
    PS C:\> Get-Help Export-AzRedisCache -detailed

    NAME
        Export-AzRedisCache

    SYNOPSIS
        Exports data from Azure Cache for Redis to a specified container.


    SYNTAX
        Export-AzRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Export-AzRedisCache cmdlet exports data from Azure Cache for Redis to a specified container.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Prefix <String>
            Prefix to use for blob names.

        -Container <String>
            SAS url of container where data should be exported.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -PassThru
            By default Export-AzRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```


Met de volgende opdracht worden gegevens uit een Azure-cache voor redis-exemplaar geëxporteerd naar de container die is opgegeven door de SAS-URI.

```azurepowershell
    PS C:\>Export-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
    -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
    pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"
```

## <a name="to-reboot-an-azure-cache-for-redis"></a>Een Azure-cache opnieuw opstarten voor redis
U kunt uw Azure-cache opnieuw opstarten voor redis-exemplaar met behulp van de- `Reset-AzRedisCache` cmdlet.

> [!IMPORTANT]
> Opnieuw opstarten is alleen beschikbaar voor [Premium-laag](cache-overview.md#service-tiers) caches. Zie [cache Administration-reboot (](cache-administration.md#reboot)Engelstalig) voor meer informatie over het opnieuw opstarten van de cache.
> 
> 

Voer de volgende opdracht uit om een lijst met beschik bare para meters en beschrijvingen ervan weer te geven voor `Reset-AzRedisCache` .

```azurepowershell
    PS C:\> Get-Help Reset-AzRedisCache -detailed

    NAME
        Reset-AzRedisCache

    SYNOPSIS
        Reboot specified node(s) of an Azure Cache for Redis instance.


    SYNTAX
        Reset-AzRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Reset-AzRedisCache cmdlet reboots the specified node(s) of an Azure Cache for Redis instance.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -RebootType <String>
            Which node to reboot. Possible values are "PrimaryNode", "SecondaryNode", "AllNodes".

        -ShardId <Integer>
            Which shard to reboot when rebooting a premium cache with clustering enabled.

        -Force
            When the Force parameter is provided, reset will be performed without any confirmation prompts.

        -PassThru
            By default Reset-AzRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
```


Met de volgende opdracht worden beide knoop punten van de opgegeven cache opnieuw opgestart.

```azurepowershell
    PS C:\>Reset-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
    -Force
```


## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende bronnen voor meer informatie over het gebruik van Windows Power shell met Azure:

* [Documentatie voor Azure cache for redis-cmdlets op MSDN](/powershell/module/az.rediscache)
* [Azure Resource Manager-cmdlets](/powershell/module/): meer informatie over het gebruik van de cmdlets in de module Azure Resource Manager.
* [Resource groepen gebruiken om uw Azure-resources te beheren](../azure-resource-manager/templates/deploy-portal.md): informatie over het maken en beheren van resource groepen in de Azure Portal.
* [Azure-blog](https://azure.microsoft.com/blog/): meer informatie over nieuwe functies in Azure.
* [Windows Power shell-blog](https://devblogs.microsoft.com/powershell/): meer informatie over nieuwe functies in Windows Power shell.
* ["Hey, Scripting Guy!" Blog](https://devblogs.microsoft.com/scripting/tag/hey-scripting-guy/): krijg praktische tips en trucs van de Windows Power shell-community.
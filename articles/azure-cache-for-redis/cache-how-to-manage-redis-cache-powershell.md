---
title: Uw Azure Cache voor Redis beheren met Azure PowerShell
description: Meer informatie over het uitvoeren van beheertaken voor Azure Cache voor Redis met Azure PowerShell.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: yegu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ac1456e2dc640e1076857da78cf4145b61ea69d4
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832784"
---
# <a name="manage-azure-cache-for-redis-with-azure-powershell"></a>Uw Azure Cache voor Redis beheren met Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [Azure-CLI](cache-manage-cli.md)
> 
> 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In dit onderwerp ziet u hoe u algemene taken uitvoert, zoals het maken, bijwerken en schalen van uw Azure Cache voor Redis-exemplaren, het opnieuw maken van toegangssleutels en het weergeven van informatie over uw caches. Zie voor een volledige lijst Azure Cache voor Redis PowerShell-cmdlets [Azure Cache voor Redis cmdlets.](/powershell/module/az.rediscache)

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Zie Implementatiemodellen versus Azure Resource Manager klassieke implementatie: Implementatiemodellen en de status van uw resources begrijpen voor meer informatie over het [klassieke implementatiemodel.](../azure-resource-manager/management/deployment-models.md)

## <a name="prerequisites"></a>Vereisten
Als u al een Azure PowerShell hebt geïnstalleerd, moet u Azure PowerShell versie 1.0.0 of hoger hebben. U kunt de versie van de Azure PowerShell die u met deze opdracht hebt geïnstalleerd, controleren Azure PowerShell opdrachtprompt.

```azurepowershell
    Get-Module Az | format-table version
```

Eerst moet u zich aanmelden bij Azure met deze opdracht.

```azurepowershell
    Connect-AzAccount
```

Geef het e-mailadres van uw Azure-account en het wachtwoord op in Microsoft Azure aanmeldingsvenster.

Als u meerdere Azure-abonnementen hebt, moet u vervolgens uw Azure-abonnement instellen. Voer deze opdracht uit om een lijst met uw huidige abonnementen weer te geven.

```azurepowershell
    Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Voer de volgende opdracht uit om het abonnement op te geven. In het volgende voorbeeld is de naam van het abonnement `ContosoSubscription` .

```azurepowershell
    Select-AzSubscription -SubscriptionName ContosoSubscription
```

Voordat u een Windows PowerShell met Azure Resource Manager, hebt u het volgende nodig:

* Windows PowerShell versie 3.0 of 4.0. Als u de versie van Windows PowerShell, typt u: en controleert u of de waarde `$PSVersionTable` `PSVersion` van 3.0 of 4.0 is. Zie voor het installeren van een [compatibele versie Windows Management Framework 3.0.](https://www.microsoft.com/download/details.aspx?id=34595)

Als u gedetailleerde hulp wilt krijgen voor elke cmdlet die u in deze zelfstudie ziet, gebruikt u de cmdlet Get-Help cmdlet.

```azurepowershell
    Get-Help <cmdlet-name> -Detailed
```

Als u bijvoorbeeld hulp wilt krijgen voor de `New-AzRedisCache` cmdlet, typt u:

```azurepowershell
    Get-Help New-AzRedisCache -Detailed
```

### <a name="how-to-connect-to-other-clouds"></a>Verbinding maken met andere clouds
De Azure-omgeving is standaard `AzureCloud` . Deze vertegenwoordigt het globale Azure-cloud-exemplaar. Als u verbinding wilt maken met een ander exemplaar, gebruikt u de opdracht met de `Connect-AzAccount` opdrachtregelschakelaar or - met de `-Environment` `EnvironmentName` gewenste omgevings- of omgevingsnaam.

Voer de cmdlet uit om de lijst met beschikbare `Get-AzEnvironment` omgevingen te bekijken.

### <a name="to-connect-to-the-azure-government-cloud"></a>Verbinding maken met Azure Government Cloud
Gebruik een van de volgende opdrachten Azure Government verbinding te maken met de Azure Government Cloud.

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
```

of

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureUSGovernment)
```

Als u een cache wilt maken in Azure Government Cloud, gebruikt u een van de volgende locaties.

* USGov Virginia
* USGov Iowa

Zie de ontwikkelaarshandleiding voor Azure Government cloud [voor Microsoft Azure Government](https://azure.microsoft.com/features/gov/) en Microsoft Azure Government informatie over [de cloud.](../azure-government/documentation-government-developer-guide.md)

### <a name="to-connect-to-the-azure-china-cloud"></a>Verbinding maken met de Azure China Cloud
Gebruik een van de volgende opdrachten om verbinding te maken met de Azure China Cloud.

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureChinaCloud
```

of

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureChinaCloud)
```

Gebruik een van de volgende locaties om een cache te maken in de Azure China Cloud.

* China East
* China - noord

Zie [AzureChinaCloud voor Azure beheerd door 21Vianet in China](https://www.windowsazure.cn/)voor meer informatie over de Azure China Cloud.

### <a name="to-connect-to-microsoft-azure-germany"></a>Verbinding maken met Microsoft Azure Duitsland
Als u verbinding wilt Microsoft Azure Duitsland, gebruikt u een van de volgende opdrachten.

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureGermanCloud
```

of

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureGermanCloud)
```

Als u een cache in Microsoft Azure Duitsland, gebruikt u een van de volgende locaties.

* Duitsland - centraal
* Duitsland - noordoost

Zie voor meer informatie Microsoft Azure Duitsland over [Microsoft Azure Duitsland.](https://azure.microsoft.com/overview/clouds/germany/)

### <a name="properties-used-for-azure-cache-for-redis-powershell"></a>Eigenschappen die worden gebruikt Azure Cache voor Redis PowerShell
De volgende tabel bevat eigenschappen en beschrijvingen voor veelgebruikte parameters bij het maken en beheren van uw Azure Cache voor Redis met behulp van Azure PowerShell.

| Parameter | Beschrijving | Standaard |
| --- | --- | --- |
| Name |Naam van de cache | |
| Locatie |Locatie van de cache | |
| ResourceGroupName |Resourcegroepnaam voor het maken van de cache | |
| Grootte |De grootte van de cache. Geldige waarden zijn: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250 MB, 1 GB, 2,5 GB, 6 GB, 13 GB, 26 GB, 53 GB |1 GB |
| ShardCount |Het aantal shards dat moet worden maken bij het maken van een Premium-cache met clustering ingeschakeld. Geldige waarden zijn: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| SKU |Hiermee geeft u de SKU van de cache op. Geldige waarden zijn: Basic, Standard, Premium |Standard |
| RedisConfiguration |Hiermee geeft u de Redis-configuratie-instellingen op. Zie de volgende tabel met Eigenschappen van [RedisConfiguration](#redisconfiguration-properties) voor meer informatie over elke instelling. | |
| EnableNonSslPort |Geeft aan of de niet-SSL-poort is ingeschakeld. |Niet waar |
| MaxMemoryPolicy |Deze parameter is afgeschaft. Gebruik in plaats daarvan RedisConfiguration. | |
| StaticIP |Bij het hosten van uw cache in een VNET, geeft een uniek IP-adres op in het subnet voor de cache. Als dit niet is opgegeven, wordt er een voor u gekozen vanuit het subnet. | |
| Subnet |Bij het hosten van uw cache in een VNET, geeft u de naam op van het subnet waarin de cache moet worden geïmplementeerd. | |
| VirtualNetwork |Bij het hosten van uw cache in een VNET, geeft u de resource-id op van het VNET waarin de cache moet worden geïmplementeerd. | |
| Keytype |Hiermee geeft u op welke toegangssleutel opnieuw moet worden ge regenereren bij het vernieuwen van toegangssleutels. Geldige waarden zijn: Primair, Secundair | |

### <a name="redisconfiguration-properties"></a>Eigenschappen van RedisConfiguration
| Eigenschap | Beschrijving | Prijscategorieën |
| --- | --- | --- |
| rdb-back-up ingeschakeld |Of [Redis-gegevens persistentie](cache-how-to-premium-persistence.md) is ingeschakeld |Alleen Premium |
| rdb-storage-connection-string |De connection string het opslagaccount voor [Redis-gegevens persistentie](cache-how-to-premium-persistence.md) |Alleen Premium |
| rdb-back-upfrequentie |De back-upfrequentie voor [Redis-gegevens persistentie](cache-how-to-premium-persistence.md) |Alleen Premium |
| maxmemory-reserved |Hiermee configureert u [het geheugen dat is](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) gereserveerd voor niet-cacheprocessen |Standard en Premium |
| maxmemory-policy |Hiermee configureert [u het beleid voor het verwijderen](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) van de cache |Alle prijslagen |
| notify-keyspace-events |Hiermee configureert [u keyspace-meldingen](cache-configure.md#keyspace-notifications-advanced-settings) |Standard en Premium |
| hash-max-ziplist-entries |Hiermee configureert [u geheugenoptimalisatie](https://redis.io/topics/memory-optimization) voor kleine geaggregeerde gegevenstypen |Standard en Premium |
| hash-max-ziplist-value |Hiermee configureert [u geheugenoptimalisatie](https://redis.io/topics/memory-optimization) voor kleine geaggregeerde gegevenstypen |Standard en Premium |
| set-max-intset-entries |Hiermee configureert [u geheugenoptimalisatie](https://redis.io/topics/memory-optimization) voor kleine geaggregeerde gegevenstypen |Standard en Premium |
| zset-max-ziplist-entries |Hiermee configureert [u geheugenoptimalisatie](https://redis.io/topics/memory-optimization) voor kleine geaggregeerde gegevenstypen |Standard en Premium |
| zset-max-ziplist-value |Hiermee configureert [u geheugenoptimalisatie](https://redis.io/topics/memory-optimization) voor kleine geaggregeerde gegevenstypen |Standard en Premium |
| databases |Hiermee configureert u het aantal databases. Deze eigenschap kan alleen worden geconfigureerd bij het maken van de cache. |Standard en Premium |

## <a name="to-create-an-azure-cache-for-redis"></a>Een Azure Cache voor Redis
Nieuwe Azure Cache voor Redis worden gemaakt met behulp van de [cmdlet New-AzRedisCache.](/powershell/module/az.rediscache/new-azrediscache)

> [!IMPORTANT]
> De eerste keer dat u een Azure Cache voor Redis in een abonnement maakt met behulp van de Azure Portal, registreert de portal de `Microsoft.Cache` naamruimte voor dat abonnement. Als u probeert de eerste Azure Cache voor Redis in een abonnement te maken met behulp van PowerShell, moet u die naamruimte eerst registreren met de volgende opdracht; anders mislukken cmdlets zoals `New-AzRedisCache` en `Get-AzRedisCache` .
> 
> `Register-AzResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

Voer de volgende opdracht uit om een lijst met beschikbare parameters en de bijbehorende beschrijvingen `New-AzRedisCache` voor te bekijken.

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

Voer de volgende opdracht uit om een cache met standaardparameters te maken.

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"
```

`ResourceGroupName`, `Name` en zijn vereiste `Location` parameters, maar de rest is optioneel en heeft standaardwaarden. Met de vorige opdracht maakt u een Standard SKU Azure Cache voor Redis-exemplaar met de opgegeven naam, locatie en resourcegroep, die 1 GB groot is en de niet-SSL-poort is uitgeschakeld.

Als u een Premium-cache wilt maken, geeft u een grootte op van P1 (6 GB - 60 GB), P2 (13 GB - 130 GB), P3 (26 GB - 260 GB) of P4 (53 GB - 530 GB). Als u clustering wilt inschakelen, geeft u een shard-telling op met behulp van de `ShardCount` parameter . In het volgende voorbeeld wordt een P1 Premium-cache met 3 shards gemaakt. Een P1 Premium-cache is 6 GB groot en omdat we drie shards hebben opgegeven, is de totale grootte 18 GB (3 x 6 GB).

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3
```

Als u waarden voor de parameter wilt opgeven, sluit u de `RedisConfiguration` waarden in als `{}` sleutel-waardeparen, zoals `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}` . In het volgende voorbeeld wordt een standaardcache van 1 GB gemaakt met `allkeys-random` maxmemory-beleid en keyspacemeldingen geconfigureerd met `KEA` . Zie [Keyspace-meldingen (geavanceerde instellingen)](cache-configure.md#keyspace-notifications-advanced-settings) en Geheugenbeleid voor [meer informatie.](cache-configure.md#memory-policies)

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}
```

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>De instelling voor databases configureren tijdens het maken van de cache
De `databases` instelling kan alleen worden geconfigureerd tijdens het maken van de cache. In het volgende voorbeeld wordt een Premium P3-cache (26 GB) met 48 databases gemaakt met behulp van de cmdlet [New-AzRedisCache.](/powershell/module/az.rediscache/New-azRedisCache)

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}
```

Zie Default Azure Cache voor Redis server configuration (Standaardconfiguratie van `databases` [Azure Cache voor Redis-server) voor meer informatie](cache-configure.md#default-redis-server-configuration)over de eigenschap . Zie de vorige sectie Een cache maken met de cmdlet [New-AzRedisCache](/powershell/module/az.rediscache/new-azrediscache) voor meer informatie over het maken van Azure Cache voor Redis cache.

## <a name="to-update-an-azure-cache-for-redis"></a>Een Azure Cache voor Redis
Azure Cache voor Redis-exemplaren worden bijgewerkt met behulp van de cmdlet [Set-AzRedisCache.](/powershell/module/az.rediscache/Set-azRedisCache)

Voer de volgende opdracht uit om een lijst met beschikbare parameters en de bijbehorende beschrijvingen `Set-AzRedisCache` voor te zien.

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

De `Set-AzRedisCache` cmdlet kan worden gebruikt om eigenschappen zoals `Size` , , en de waarden bij te `Sku` `EnableNonSslPort` `RedisConfiguration` werken. 

Met de volgende opdracht wordt het maxmemory-beleid bijgewerkt voor de Azure Cache voor Redis met de naam myCache.

```azurepowershell
    Set-AzRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}
```

<a name="scale"></a>

## <a name="to-scale-an-azure-cache-for-redis"></a>Een Azure Cache voor Redis
`Set-AzRedisCache` kan worden gebruikt om een Azure Cache voor Redis te schalen wanneer `Size` de eigenschappen , of worden `Sku` `ShardCount` gewijzigd. 

> [!NOTE]
> Voor het schalen van een cache met Behulp van PowerShell gelden dezelfde limieten en richtlijnen als voor het schalen van een cache vanuit de Azure Portal. U kunt schalen naar een andere prijscategorie met de volgende beperkingen.
> 
> * U kunt niet schalen van een hogere prijscategorie naar een lagere prijscategorie.
> * U kunt niet van een **Premium-cache** omlaag schalen naar een **Standard-** of **Basic-cache.**
> * U kunt niet van een **Standard-cache** omlaag schalen naar een **Basic-cache.**
> * U kunt schalen van **een Basic-cache** naar een **Standard-cache,** maar u kunt de grootte niet tegelijkertijd wijzigen. Als u een andere grootte nodig hebt, kunt u een volgende schaalbewerking naar de gewenste grootte uitvoeren.
> * U kunt een **Basic-cache** niet rechtstreeks naar een **Premium-cache schalen.** U moet in één schaalbewerking van **Basic** naar **Standard** schalen en vervolgens van **Standard** naar **Premium** in een volgende schaalbewerking.
> * U kunt niet van een grotere grootte naar de **C0-grootte (250 MB) schalen.**
> 
> Zie How [to Scale Azure Cache voor Redis voor meer Azure Cache voor Redis.](cache-how-to-scale.md)
> 
> 

In het volgende voorbeeld ziet u hoe u een cache met de naam kunt `myCache` schalen naar een cache van 2,5 GB. Houd er rekening mee dat deze opdracht werkt voor zowel een Basic- als een Standard-cache.

```azurepowershell
    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

Nadat deze opdracht is uitgegeven, wordt de status van de cache geretourneerd (vergelijkbaar met het aanroepen `Get-AzRedisCache` van ). Houd er rekening mee `ProvisioningState` dat de `Scaling` is.

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

Wanneer de schaalbewerking is voltooid, verandert `ProvisioningState` de in `Succeeded` . Als u een volgende schaalbewerking wilt uitvoeren, zoals het wijzigen van Basic in Standard en vervolgens het wijzigen van de grootte, moet u wachten tot de vorige bewerking is voltooid of er wordt een fout weergegeven die er ongeveer als volgt uit ziet.

```azurepowershell
    Set-AzRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.
```

## <a name="to-get-information-about-an-azure-cache-for-redis"></a>Informatie over een Azure Cache voor Redis
U kunt informatie over een cache ophalen met behulp van [de cmdlet Get-AzRedisCache.](/powershell/module/az.rediscache/get-azrediscache)

Voer de volgende opdracht uit om een lijst met beschikbare parameters en de bijbehorende beschrijvingen `Get-AzRedisCache` voor te bekijken.

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

Als u informatie wilt retourneren over alle caches in het huidige abonnement, moet u `Get-AzRedisCache` uitvoeren zonder parameters.

```azurepowershell
    Get-AzRedisCache
```

Voer uit met de parameter om informatie over alle caches in een specifieke resourcegroep `Get-AzRedisCache` te `ResourceGroupName` retourneren.

```azurepowershell
    Get-AzRedisCache -ResourceGroupName myGroup
```

Als u informatie over een specifieke cache wilt retourneren, moet u uitvoeren met de parameter die de naam van de cache bevat en de parameter met de `Get-AzRedisCache` `Name` `ResourceGroupName` resourcegroep die die cache bevat.

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

## <a name="to-retrieve-the-access-keys-for-an-azure-cache-for-redis"></a>De toegangssleutels voor een Azure Cache voor Redis
Als u de toegangssleutels voor uw cache wilt ophalen, kunt u de cmdlet [Get-AzRedisCacheKey](/powershell/module/az.rediscache/Get-azRedisCacheKey) gebruiken.

Voer de volgende opdracht uit om een lijst met beschikbare parameters en de bijbehorende beschrijvingen `Get-AzRedisCacheKey` voor te bekijken.

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

Als u de sleutels voor uw cache wilt ophalen, roept u de cmdlet aan en gebruikt u de naam van uw cache om de naam door te geven van de `Get-AzRedisCacheKey` resourcegroep die de cache bevat.

```azurepowershell
    PS C:\> Get-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=
```

## <a name="to-regenerate-access-keys-for-your-azure-cache-for-redis"></a>Toegangssleutels voor uw Azure Cache voor Redis
Als u de toegangssleutels voor uw cache opnieuw wilt maken, kunt u de cmdlet [New-AzRedisCacheKey](/powershell/module/az.rediscache/New-azRedisCacheKey) gebruiken.

Voer de volgende opdracht uit om een lijst met beschikbare parameters en de bijbehorende beschrijvingen `New-AzRedisCacheKey` voor te bekijken.

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

Als u de primaire of secundaire sleutel voor uw cache opnieuw wilt maken, roept u de cmdlet aan en geeft u de naam en resourcegroep door en geeft u of op `New-AzRedisCacheKey` `Primary` voor de parameter `Secondary` `KeyType` . In het volgende voorbeeld wordt de secundaire toegangssleutel voor een cache opnieuw ge regenereerd.

```azurepowershell
    PS C:\> New-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=
```

## <a name="to-delete-an-azure-cache-for-redis"></a>Een Azure Cache voor Redis
Als u een Azure Cache voor Redis, gebruikt u de cmdlet [Remove-AzRedisCache.](/powershell/module/az.rediscache/remove-azrediscache)

Voer de volgende opdracht uit om een lijst met beschikbare parameters en de bijbehorende beschrijvingen `Remove-AzRedisCache` voor te zien.

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

In het volgende voorbeeld wordt de cache met de `myCache` naam verwijderd.

```azurepowershell
    PS C:\> Remove-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```


## <a name="to-import-an-azure-cache-for-redis"></a>Een Azure Cache voor Redis
U kunt gegevens importeren in een Azure Cache voor Redis met behulp van de `Import-AzRedisCache` cmdlet .

> [!IMPORTANT]
> Importeren/exporteren is alleen beschikbaar voor [Premium-caches.](cache-overview.md#service-tiers) Zie Gegevens importeren en exporteren in Azure Cache voor Redis voor [meer informatie Azure Cache voor Redis.](cache-how-to-import-export-data.md)
> 
> 

Voer de volgende opdracht uit om een lijst met beschikbare parameters en de bijbehorende beschrijvingen `Import-AzRedisCache` voor te zien.

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


Met de volgende opdracht importeert u gegevens uit de blob die is opgegeven door de SAS-URI naar Azure Cache voor Redis.

```azurepowershell
    PS C:\>Import-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force
```

## <a name="to-export-an-azure-cache-for-redis"></a>Een Azure Cache voor Redis
U kunt gegevens uit een Azure Cache voor Redis exporteren met behulp van `Export-AzRedisCache` de cmdlet .

> [!IMPORTANT]
> Importeren/exporteren is alleen beschikbaar voor [Premium-caches.](cache-overview.md#service-tiers) Zie Gegevens importeren en exporteren in Azure Cache voor Redis voor [meer informatie Azure Cache voor Redis.](cache-how-to-import-export-data.md)
> 
> 

Voer de volgende opdracht uit om een lijst met beschikbare parameters en de bijbehorende beschrijvingen `Export-AzRedisCache` voor te zien.

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


Met de volgende opdracht exporteert u gegevens van een Azure Cache voor Redis naar de container die is opgegeven door de SAS-URI.

```azurepowershell
    PS C:\>Export-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
    -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
    pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"
```

## <a name="to-reboot-an-azure-cache-for-redis"></a>Een apparaat opnieuw Azure Cache voor Redis
U kunt uw exemplaar Azure Cache voor Redis opnieuw opstarten met behulp van `Reset-AzRedisCache` de cmdlet .

> [!IMPORTANT]
> Opnieuw opstarten is alleen beschikbaar voor [Premium-caches.](cache-overview.md#service-tiers) Zie Cachebeheer - opnieuw opstarten voor meer informatie over het opnieuw opstarten van [uw cache.](cache-administration.md#reboot)
> 
> 

Voer de volgende opdracht uit om een lijst met beschikbare parameters en de bijbehorende beschrijvingen `Reset-AzRedisCache` voor te bekijken.

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


Met de volgende opdracht worden beide knooppunten van de opgegeven cache opnieuw opgestart.

```azurepowershell
    PS C:\>Reset-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
    -Force
```


## <a name="next-steps"></a>Volgende stappen
Zie de volgende resources voor Windows PowerShell over het gebruik van azure:

* [Azure Cache voor Redis cmdlet-documentatie op MSDN](/powershell/module/az.rediscache)
* [Azure Resource Manager Cmdlets:](/powershell/module/)leer hoe u de cmdlets in de module Azure Resource Manager gebruiken.
* [Resourcegroepen gebruiken voor het beheren van uw Azure-resources:](../azure-resource-manager/templates/deploy-portal.md)informatie over het maken en beheren van resourcegroepen in de Azure Portal.
* [Azure-blog:](https://azure.microsoft.com/blog/)meer informatie over nieuwe functies in Azure.
* [Windows PowerShell blog:](https://devblogs.microsoft.com/powershell/)meer informatie over nieuwe functies in Windows PowerShell.
* ["Hey, Scripting Guy!" Blog:](https://devblogs.microsoft.com/scripting/tag/hey-scripting-guy/)ontvang echte tips en trucs van de Windows PowerShell community.

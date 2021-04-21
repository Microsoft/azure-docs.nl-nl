---
title: Een Azure Cache voor Redis schalen
description: Leer hoe u uw Azure Cache voor Redis-exemplaren kunt schalen met de Azure Portal en hulpprogramma's zoals Azure PowerShell en Azure CLI
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 43db8d4c094ec1b08a24c29fdaccf97f63ef29b9
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833972"
---
# <a name="scale-an-azure-cache-for-redis-instance"></a>Een Azure Cache voor Redis schalen
Azure Cache voor Redis heeft verschillende cacheaanbiedingen, die flexibiliteit bieden bij de keuze van cachegrootte en -functies. Voor een Basic-, Standard- of Premium-cache kunt u de grootte en laag ervan wijzigen nadat deze zijn gemaakt om te voldoen aan de behoeften van uw toepassing. In dit artikel wordt beschreven hoe u uw cache kunt schalen met behulp van Azure Portal en hulpprogramma's zoals Azure PowerShell en Azure CLI.

## <a name="when-to-scale"></a>Wanneer schalen
U kunt de [bewakingsfuncties](cache-how-to-monitor.md) van Azure Cache voor Redis om de status en prestaties van uw cache te bewaken en te helpen bepalen wanneer de cache moet worden geschaald. 

U kunt de volgende metrische gegevens controleren om te bepalen of u moet schalen.

* Belasting van Redis-server
* Geheugengebruik
* Netwerkbandbreedte
* CPU-gebruik

Als u bepaalt dat uw cache niet meer voldoet aan de vereisten van uw toepassing, kunt u schalen naar een grotere of kleinere cacheprijscategorie die het beste bij uw toepassing passen. Zie De juiste categorie kiezen voor meer informatie over het bepalen van de te gebruiken [cacheprijscategorie.](cache-overview.md#choosing-the-right-tier)

## <a name="scale-a-cache"></a>Een cache schalen
Als u de cache wilt schalen, [bladert u](cache-configure.md#configure-azure-cache-for-redis-settings) naar de cache in [Azure Portal](https://portal.azure.com) klikt **u** in het menu Resource op **Schalen.**

![Schalen](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Selecteer de gewenste prijscategorie op de blade **Prijscategorie selecteren** en klik op **Selecteren.**

![Prijscategorie][redis-cache-pricing-tier-blade]


U kunt schalen naar een andere prijscategorie met de volgende beperkingen:

* U kunt niet schalen van een hogere prijscategorie naar een lagere prijscategorie.
  * U kunt niet van een **Premium-cache** omlaag schalen naar een **Standard-** of **Basic-cache.**
  * U kunt niet van een **Standard-cache** omlaag schalen naar een **Basic-cache.**
* U kunt schalen van **een Basic-cache** naar een **Standard-cache,** maar u kunt de grootte niet tegelijkertijd wijzigen. Als u een andere grootte nodig hebt, kunt u een volgende schaalbewerking naar de gewenste grootte uitvoeren.
* U kunt een **Basic-cache** niet rechtstreeks naar een **Premium-cache schalen.** Schaal eerst van **Basic** naar **Standard** in één schaalbewerking en vervolgens van **Standard** naar **Premium** in een volgende schaalbewerking.
* U kunt niet van een grotere grootte omlaag schalen naar **de C0-grootte (250 MB).** U kunt echter omlaag schalen naar een andere grootte binnen dezelfde prijscategorie. U kunt bijvoorbeeld omlaag schalen van C5 Standard naar C1 Standard.
 
Terwijl de cache wordt geschaald naar de nieuwe prijscategorie, wordt de **status** Schalen weergegeven op Azure Cache voor Redis **blade.**

![Schalen][redis-cache-scaling]

Wanneer het schalen is voltooid, verandert de status van **Schalen in** **Wordt uitgevoerd.**

## <a name="how-to-automate-a-scaling-operation"></a>Een schaalbewerking automatiseren
Naast het schalen van uw cache-exemplaren in de Azure Portal, kunt u schalen met PowerShell-cmdlets, Azure CLI en met behulp van de Microsoft Azure Management Libraries (MAML). 

* [Schalen met PowerShell](#scale-using-powershell)
* [Schalen met Behulp van Azure CLI](#scale-using-azure-cli)
* [Schalen met MAML](#scale-using-maml)

### <a name="scale-using-powershell"></a>Schalen met PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U kunt uw Azure Cache voor Redis met PowerShell schalen met behulp van de cmdlet [Set-AzRedisCache](/powershell/module/az.rediscache/set-azrediscache) wanneer de eigenschappen `Size` , of worden `Sku` `ShardCount` gewijzigd. In het volgende voorbeeld ziet u hoe u een cache met de naam kunt `myCache` schalen naar een cache van 2,5 GB. 

```powershell
   Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

Zie Een app schalen met PowerShell voor meer informatie over het schalen [Azure Cache voor Redis powershell.](cache-how-to-manage-redis-cache-powershell.md#scale)

### <a name="scale-using-azure-cli"></a>Schalen met Behulp van Azure CLI
Als u uw Azure Cache voor Redis-exemplaren wilt schalen met behulp van Azure CLI, roept u de opdracht aan en meldt u de gewenste configuratiewijzigingen door die een nieuwe grootte, SKU of clustergrootte bevatten, afhankelijk van de gewenste `azure rediscache set` schaalbewerking.

Zie Instellingen van een bestaand apparaat wijzigen voor meer informatie over schalen met Azure CLI [Azure Cache voor Redis.](cache-manage-cli.md#scale)

### <a name="scale-using-maml"></a>Schalen met MAML
Als u uw Azure Cache voor Redis wilt schalen met behulp van [de Microsoft Azure Management Libraries (MAML),](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/)roept u de methode aan en geeft u de nieuwe grootte voor de `IRedisOperations.CreateOrUpdate` `RedisProperties.SKU.Capacity` door.

```csharp
    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }
```

Zie het voorbeeld Manage [Azure Cache voor Redis using MAML (Een app Azure Cache voor Redis MAML) voor meer](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) informatie.

## <a name="scaling-faq"></a>Veelgestelde vragen over schalen
De volgende lijst bevat antwoorden op veelgestelde vragen over Azure Cache voor Redis schalen.

* [Kan ik schalen naar, van of binnen een Premium-cache?](#can-i-scale-to-from-or-within-a-premium-cache)
* [Moet ik na het schalen mijn cachenaam of toegangssleutels wijzigen?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Hoe werkt schalen?](#how-does-scaling-work)
* [Verlies ik gegevens uit mijn cache tijdens het schalen?](#will-i-lose-data-from-my-cache-during-scaling)
* [Wordt de instelling voor mijn aangepaste databases beïnvloed tijdens het schalen?](#is-my-custom-databases-setting-affected-during-scaling)
* [Is mijn cache beschikbaar tijdens het schalen?](#will-my-cache-be-available-during-scaling)
* Als geo-replicatie is geconfigureerd, waarom kan ik mijn cache niet schalen of de shards in een cluster wijzigen?
* [Bewerkingen die niet worden ondersteund](#operations-that-are-not-supported)
* [Hoe lang duurt schalen?](#how-long-does-scaling-take)
* [Hoe weet ik wanneer het schalen is voltooid?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Kan ik schalen naar, van of binnen een Premium-cache?
* U kunt niet van een **Premium-cache** omlaag schalen naar een **Prijscategorie Basic** **of** Standard.
* U kunt schalen van de **ene Prijscategorie voor Premium-cache** naar de andere.
* U kunt een **Basic-cache** niet rechtstreeks naar een **Premium-cache schalen.** Schaal eerst van **Basic** naar **Standard** in één schaalbewerking en vervolgens van **Standard** naar **Premium** in een volgende schaalbewerking.
* Als u clustering hebt ingeschakeld tijdens het maken van uw **Premium-cache,** kunt u [de clustergrootte wijzigen.](cache-how-to-premium-clustering.md#cluster-size) Als uw cache is gemaakt zonder dat clustering is ingeschakeld, kunt u clustering op een later tijdstip configureren.
  
  Zie [Clustering voor een Premium Azure Cache voor Redis configureren](cache-how-to-premium-clustering.md) voor meer informatie.

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Moet ik na het schalen mijn cachenaam of toegangssleutels wijzigen?
Nee, de cachenaam en sleutels blijven ongewijzigd tijdens een schaalbewerking.

### <a name="how-does-scaling-work"></a>Hoe werkt schalen?
* Wanneer een **Basic-cache** naar een andere grootte wordt geschaald, wordt deze afgesloten en wordt er een nieuwe cache ingericht met de nieuwe grootte. Gedurende deze tijd is de cache niet beschikbaar en gaan alle gegevens in de cache verloren.
* Wanneer een **Basic-cache** wordt geschaald naar een **Standard-cache,** wordt er een replicacache ingericht en worden de gegevens gekopieerd van de primaire cache naar de replicacache. De cache blijft beschikbaar tijdens het schalen.
* Wanneer een **Standard-cache** wordt geschaald naar een andere grootte of naar een **Premium-cache,** wordt een van de replica's afgesloten en opnieuw in de nieuwe grootte geplaatst en worden de gegevens overgedragen. Vervolgens voert de andere replica een failover uit voordat deze opnieuw wordt gepland, vergelijkbaar met het proces dat optreedt bij een storing in een van de cacheknooppunten.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Verlies ik gegevens uit mijn cache tijdens het schalen?
* Wanneer een **Basic-cache** naar een nieuwe grootte wordt geschaald, gaan alle gegevens verloren en is de cache niet beschikbaar tijdens de schaalbewerking.
* Wanneer een **Basic-cache** wordt geschaald naar een **Standard-cache,** blijven de gegevens in de cache doorgaans behouden.
* Wanneer een **Standard-cache** wordt geschaald naar een grotere grootte of categorie, of wanneer een **Premium-cache** wordt geschaald naar een grotere grootte, blijven alle gegevens doorgaans behouden. Wanneer u een **Standard-** of **Premium-cache** omlaag schaalt naar een kleinere grootte, kunnen gegevens verloren gaan, afhankelijk van hoeveel gegevens er in de cache zijn gerelateerd aan de nieuwe grootte wanneer deze wordt geschaald. Als gegevens verloren gaan bij het omlaag schalen, worden sleutels verwijderen met behulp van het [allkeys-lru-beleid](https://redis.io/topics/lru-cache) voor het verwijderen. 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>Wordt de instelling voor mijn aangepaste databases beïnvloed tijdens het schalen?
Als u tijdens het maken van de cache een aangepaste waarde voor de instelling hebt geconfigureerd, moet u er rekening mee houden dat sommige prijslagen `databases` verschillende [databaseslimieten hebben.](cache-configure.md#databases) Hier zijn enkele overwegingen bij het schalen in dit scenario:

* Bij het schalen naar een prijscategorie met een `databases` lagere limiet dan de huidige laag:
  * Als u het standaardnummer gebruikt van , wat `databases` 16 is voor alle prijslagen, gaan er geen gegevens verloren.
  * Als u een aangepast aantal gebruikt dat binnen de limieten valt voor de laag waarvoor u schaalt, blijft deze instelling behouden en gaan er geen `databases` `databases` gegevens verloren.
  * Als u een aangepast aantal gebruikt dat de limieten van de nieuwe laag overschrijdt, wordt de instelling verlaagd tot de limieten van de nieuwe laag en gaan alle gegevens in de verwijderde `databases` `databases` databases verloren.
* Wanneer u schaalt naar een prijscategorie met dezelfde of een hogere limiet dan de huidige laag, blijft uw instelling behouden en gaan er `databases` `databases` geen gegevens verloren.

Hoewel Standard- en Premium-caches een SLA van 99,9% hebben voor beschikbaarheid, is er geen SLA voor gegevensverlies.

### <a name="will-my-cache-be-available-during-scaling"></a>Is mijn cache beschikbaar tijdens het schalen?
* **Standard-** **en Premium-caches** blijven beschikbaar tijdens de schaalbewerking. Verbindingsdebups kunnen echter optreden tijdens het schalen van Standard- en Premium-caches, en ook tijdens het schalen van Basic naar Standard-caches. Van deze verbindingsdeten wordt verwacht dat ze klein zijn en dat redis-clients hun verbinding direct opnieuw tot stand kunnen brengen.
* **Basic-caches** zijn offline tijdens het schalen naar een andere grootte. Basic-caches blijven beschikbaar bij het schalen van **Basic** naar **Standard,** maar kunnen een klein verbindingspunt ervaren. Als er een verbindingsverbinding plaatsvindt, moeten redis-clients hun verbinding direct opnieuw tot stand kunnen brengen.


### <a name="scaling-limitations-with-geo-replication"></a>Beperkingen voor schalen met geo-replicatie

Nadat u een geo-replicatiekoppeling tussen twee caches hebt toegevoegd, kunt u geen schaalbewerking meer initiëren of het aantal shards in een cluster wijzigen. U moet de cache ontkoppelen om deze opdrachten uit te voeren. Zie Geo-replicatie [configureren voor meer informatie.](cache-how-to-geo-replication.md)


### <a name="operations-that-are-not-supported"></a>Bewerkingen die niet worden ondersteund
* U kunt niet van een hogere prijscategorie naar een lagere prijscategorie schalen.
  * U kunt niet van een **Premium-cache** omlaag schalen naar een **Standard-** of **Basic-cache.**
  * U kunt niet van een **Standard-cache** omlaag schalen naar een **Basic-cache.**
* U kunt schalen van **een Basic-cache** naar een **Standard-cache,** maar u kunt de grootte niet tegelijkertijd wijzigen. Als u een andere grootte nodig hebt, kunt u een volgende schaalbewerking naar de gewenste grootte uitvoeren.
* U kunt een **Basic-cache** niet rechtstreeks naar een **Premium-cache schalen.** Schaal eerst van **Basic** naar **Standard** in één schaalbewerking en vervolgens van **Standard** naar **Premium** in een volgende bewerking.
* U kunt niet van een grotere grootte omlaag schalen naar **de C0-grootte (250 MB).**

Als een schaalbewerking mislukt, probeert de service de bewerking terug te keren en keert de cache terug naar de oorspronkelijke grootte.


### <a name="how-long-does-scaling-take"></a>Hoe lang duurt schalen?
De schaaltijd is afhankelijk van de hoeveelheid gegevens in de cache, waarbij het langer duurt om grotere hoeveelheden gegevens te voltooien. Schalen duurt ongeveer 20 minuten. Voor geclusterde caches duurt het schalen ongeveer 20 minuten per shard.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Hoe weet ik wanneer het schalen is voltooid?
In de Azure Portal ziet u dat de schaalbewerking wordt uitgevoerd. Wanneer het schalen is voltooid, verandert de status van de cache in **Wordt uitgevoerd.**

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png

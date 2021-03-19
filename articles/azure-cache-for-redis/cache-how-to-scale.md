---
title: Een Azure-cache schalen voor redis-instantie
description: Meer informatie over het schalen van uw Azure-cache voor redis-exemplaren met behulp van de Azure Portal en hulpprogram ma's zoals Azure PowerShell en Azure CLI
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: d91b62afacad31d78feb7d4743cd4050fcc1bd4e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581640"
---
# <a name="scale-an-azure-cache-for-redis-instance"></a>Een Azure-cache schalen voor redis-instantie
Azure cache voor redis heeft verschillende cache aanbiedingen, die flexibiliteit bieden bij het kiezen van de cache grootte en-functies. Voor een Basic-, Standard-of Premium-cache kunt u de grootte en laag wijzigen nadat deze is gemaakt om de behoeften van uw toepassing te blijven gebruiken. Dit artikel laat u zien hoe u uw cache kunt schalen met behulp van de Azure Portal en hulpprogram ma's zoals Azure PowerShell en Azure CLI.

## <a name="when-to-scale"></a>Wanneer schalen
U kunt de [bewakings](cache-how-to-monitor.md) functies van Azure cache gebruiken voor redis om de status en prestaties van uw cache te controleren en te bepalen wanneer de cache moet worden geschaald. 

U kunt de volgende metrische gegevens controleren om te helpen bepalen of u wilt schalen.

* Redis-server belasting
* Geheugengebruik
* Netwerk bandbreedte
* CPU-gebruik

Als u vaststelt dat uw cache niet langer voldoet aan de vereisten van uw toepassing, kunt u schalen naar een grotere of kleinere prijs categorie voor de cache die geschikt is voor uw toepassing. Zie [de juiste laag kiezen](cache-overview.md#choosing-the-right-tier)voor meer informatie over het bepalen van de prijs categorie voor de cache die u wilt gebruiken.

## <a name="scale-a-cache"></a>Een cache schalen
Als u de cache wilt schalen, [bladert u naar de cache](cache-configure.md#configure-azure-cache-for-redis-settings) in de [Azure Portal](https://portal.azure.com) en klikt u op **schalen** in het **menu resource**.

![Schalen](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Selecteer de gewenste prijs categorie op de Blade **prijs categorie selecteren** en klik op **selecteren**.

![Prijscategorie][redis-cache-pricing-tier-blade]


U kunt schalen naar een andere prijs categorie met de volgende beperkingen:

* U kunt niet schalen van een hogere prijs categorie naar een lagere prijs categorie.
  * U kunt de schaal van een **Premium** -cache niet verlagen naar een **Standard** -of een **Basic** -cache.
  * U kunt niet omlaag schalen vanuit een **standaard** cache naar een **Basic** -cache.
* U kunt schalen van een **basis** cache naar een **standaard** cache, maar u kunt de grootte niet tegelijkertijd wijzigen. Als u een andere grootte nodig hebt, kunt u een volgende schaal bewerking uitvoeren voor de gewenste grootte.
* U kunt niet rechtstreeks schalen van een **Basic** -cache naar een **Premium** -cache. Schaal eerst van **Basic** naar **Standard** in één schaal bewerking en vervolgens van **standaard** naar **Premium** bij een volgende schaal bewerking.
* U kunt niet van een grotere grootte schalen naar de grootte van de **C0 (250 MB)** . U kunt echter omlaag schalen naar een andere grootte binnen dezelfde prijs categorie. U kunt bijvoorbeeld omlaag schalen van C5 Standard naar C1 Standard.
 
Terwijl de cache wordt geschaald naar de nieuwe prijs categorie, wordt een **schaal** status weer gegeven in de Blade **Azure-cache voor redis** .

![Schalen][redis-cache-scaling]

Wanneer het schalen is voltooid, verandert de status van **schalen** in **wordt uitgevoerd**.

## <a name="how-to-automate-a-scaling-operation"></a>Een schaal bewerking automatiseren
Naast het schalen van uw cache-instanties in de Azure Portal, kunt u de schaal aanpassen met behulp van Power shell-cmdlets, Azure CLI en met behulp van de Microsoft Azure Management libraries (MAML). 

* [Schalen met behulp van Power shell](#scale-using-powershell)
* [Schalen met behulp van Azure CLI](#scale-using-azure-cli)
* [Schalen met behulp van MAML](#scale-using-maml)

### <a name="scale-using-powershell"></a>Schalen met behulp van Power shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U kunt uw Azure-cache schalen voor redis-instanties met Power shell met behulp van de cmdlet [set-AzRedisCache](/powershell/module/az.rediscache/set-azrediscache) wanneer de `Size` , `Sku` of- `ShardCount` eigenschappen worden gewijzigd. In het volgende voor beeld ziet u hoe u een cache kunt schalen met de naam `myCache` in een cache van 2,5 GB. 

```powershell
   Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

Zie [een Azure-cache schalen voor redis met behulp van Power shell](cache-how-to-manage-redis-cache-powershell.md#scale)voor meer informatie over schalen met Power shell.

### <a name="scale-using-azure-cli"></a>Schalen met behulp van Azure CLI
Als u uw Azure-cache wilt schalen voor redis-exemplaren met behulp van Azure CLI, roept u de `azure rediscache set` opdracht aan en geeft u de gewenste configuratie wijzigingen op die een nieuwe grootte, SKU of cluster grootte bevatten, afhankelijk van de gewenste schaal bewerking.

Zie [instellingen van een bestaande Azure-cache wijzigen voor redis](cache-manage-cli.md#scale)voor meer informatie over schalen met Azure cli.

### <a name="scale-using-maml"></a>Schalen met behulp van MAML
Als u uw Azure-cache wilt schalen voor redis-exemplaren met behulp van de [Microsoft Azure Management libraries (MAML)](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/), roept u de- `IRedisOperations.CreateOrUpdate` methode aan en geeft u de nieuwe grootte voor de `RedisProperties.SKU.Capacity` .

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

Zie voor meer informatie het voor beeld [voor het beheren van Azure-cache voor redis met MAML](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) .

## <a name="scaling-faq"></a>Veelgestelde vragen over schalen
De volgende lijst bevat antwoorden op veelgestelde vragen over Azure cache voor redis-schaling.

* [Kan ik schalen naar, van of binnen een Premium-cache?](#can-i-scale-to-from-or-within-a-premium-cache)
* [Moet ik na het schalen mijn cache naam of toegangs sleutels wijzigen?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Hoe werkt schalen?](#how-does-scaling-work)
* [Gaan er gegevens verloren in mijn cache tijdens het schalen?](#will-i-lose-data-from-my-cache-during-scaling)
* [Worden de instellingen van mijn aangepaste data base beïnvloed tijdens het schalen?](#is-my-custom-databases-setting-affected-during-scaling)
* [Is mijn cache beschikbaar tijdens het schalen?](#will-my-cache-be-available-during-scaling)
* Waarom kan ik niet mijn cache schalen of de Shards in een cluster wijzigen als geo-replicatie is geconfigureerd?
* [Bewerkingen die niet worden ondersteund](#operations-that-are-not-supported)
* [Hoe lang duurt het voor schalen?](#how-long-does-scaling-take)
* [Hoe kan ik zien wanneer schalen is voltooid?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Kan ik schalen naar, van of binnen een Premium-cache?
* U kunt de schaal van een **Premium** -cache niet verlagen naar een **basis** -of **standaard** prijs categorie.
* U kunt schalen van één **Premium** -cache prijs categorie naar een andere.
* U kunt niet rechtstreeks schalen van een **Basic** -cache naar een **Premium** -cache. Schaal eerst van **Basic** naar **Standard** in één schaal bewerking en vervolgens van **standaard** naar **Premium** bij een volgende schaal bewerking.
* Als u clustering hebt ingeschakeld tijdens het maken van uw **Premium** -cache, kunt u [de cluster grootte wijzigen](cache-how-to-premium-clustering.md#cluster-size). Als uw cache is gemaakt zonder dat Clustering is ingeschakeld, kunt u Clustering op een later tijdstip configureren.
  
  Zie [Clustering voor een Premium Azure Cache voor Redis configureren](cache-how-to-premium-clustering.md) voor meer informatie.

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Moet ik na het schalen mijn cache naam of toegangs sleutels wijzigen?
Nee, uw cache naam en sleutels zijn niet gewijzigd tijdens een schaal bewerking.

### <a name="how-does-scaling-work"></a>Hoe werkt schalen?
* Wanneer een **basis** cache wordt geschaald naar een andere grootte, wordt deze afgesloten en wordt een nieuwe cache ingericht met behulp van de nieuwe grootte. Tijdens deze periode is de cache niet beschikbaar en zijn alle gegevens in de cache verloren gegaan.
* Wanneer een **basis** cache wordt geschaald naar een **standaard** cache, wordt een replica-cache ingericht en worden de gegevens van de primaire cache naar de replica cache gekopieerd. De cache blijft beschikbaar tijdens het schaal proces.
* Wanneer een **standaard** cache wordt geschaald naar een andere grootte of naar een **Premium** -cache, wordt een van de replica's afgesloten en opnieuw ingericht naar de nieuwe grootte en de gegevens die worden overgedragen. vervolgens voert de andere replica een failover uit voordat deze opnieuw wordt ingericht, vergelijkbaar met het proces dat optreedt tijdens een storing van een van de cache knooppunten.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Gaan er gegevens verloren in mijn cache tijdens het schalen?
* Wanneer een **basis** cache wordt geschaald naar een nieuwe grootte, gaan alle gegevens verloren en wordt de cache niet beschikbaar tijdens de schaal bewerking.
* Wanneer een **basis** cache wordt geschaald naar een **standaard** cache, blijven de gegevens in de cache doorgaans bewaard.
* Wanneer een **standaard** cache wordt geschaald naar een grotere grootte of laag, of als een **Premium** -cache naar een grotere grootte wordt geschaald, worden alle gegevens doorgaans bewaard. Bij het schalen van een **Standard** -of **Premium** -cache naar een kleinere grootte, kunnen gegevens verloren gaan, afhankelijk van de hoeveelheid gegevens in de cache die is gerelateerd aan de nieuwe grootte wanneer deze wordt geschaald. Als gegevens verloren zijn gegaan bij het omlaag schalen, worden sleutels verwijderd met behulp van het verwijderings beleid voor [AllKeys-LRU](https://redis.io/topics/lru-cache) . 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>Worden de instellingen van mijn aangepaste data base beïnvloed tijdens het schalen?
Als u een aangepaste waarde voor de `databases` instelling hebt geconfigureerd tijdens het maken van de cache, moet u er rekening mee houdt dat sommige prijs categorieën verschillende [database limieten](cache-configure.md#databases)hebben. Hier volgen enkele overwegingen bij het schalen in dit scenario:

* Bij het schalen naar een prijs categorie met een lagere `databases` limiet dan de huidige laag:
  * Als u het standaard aantal van `databases` 16 voor alle prijs categorieën gebruikt, gaan er geen gegevens verloren.
  * Als u een aangepast aantal gebruikt `databases` dat binnen de limieten voor de laag ligt die u wilt schalen, `databases` wordt deze instelling behouden en gaan er geen gegevens verloren.
  * Als u een aangepast aantal gebruikt `databases` dat de limieten van de nieuwe laag overschrijdt, wordt de `databases` instelling verlaagd tot de limieten van de nieuwe laag en gaan alle gegevens in de verwijderde data base verloren.
* Bij het schalen naar een prijs categorie met dezelfde of een hogere `databases` limiet dan de huidige laag, `databases` wordt uw instelling behouden en gaan er geen gegevens verloren.

Hoewel Standard-en Premium-caches een SLA van 99,9% voor Beschik baarheid hebben, is er geen SLA voor gegevens verlies.

### <a name="will-my-cache-be-available-during-scaling"></a>Is mijn cache beschikbaar tijdens het schalen?
* **Standard** -en **Premium** -caches blijven beschikbaar tijdens de schaal bewerking. Verbindings problemen kan echter optreden tijdens het schalen van standaard-en Premium-caches en tijdens het schalen van Basic naar Standard-caches. Deze verbindings problemen zijn klein en redis-clients moeten hun verbinding direct kunnen herstellen.
* **Basic** -caches zijn offline tijdens het schalen naar een andere grootte. Basic-caches blijven beschikbaar wanneer het schalen van **Basic** naar **Standard** , maar kan een kleine verbindings Blip. Als er een verbindings Blip optreedt, moeten redis-clients onmiddellijk hun verbinding kunnen herstellen.


### <a name="scaling-limitations-with-geo-replication"></a>Beperkingen voor schalen met geo-replicatie

Zodra u een geo-replicatie koppeling tussen twee caches hebt toegevoegd, kunt u geen schaal bewerking meer initiëren of het aantal Shards in een cluster niet wijzigen. U moet de cache ontkoppelen om deze opdrachten te kunnen geven. Zie [geo-replicatie configureren](cache-how-to-geo-replication.md)voor meer informatie.


### <a name="operations-that-are-not-supported"></a>Bewerkingen die niet worden ondersteund
* U kunt niet schalen van een hogere prijs categorie naar een lagere prijs categorie.
  * U kunt de schaal van een **Premium** -cache niet verlagen naar een **Standard** -of een **Basic** -cache.
  * U kunt niet omlaag schalen vanuit een **standaard** cache naar een **Basic** -cache.
* U kunt schalen van een **basis** cache naar een **standaard** cache, maar u kunt de grootte niet tegelijkertijd wijzigen. Als u een andere grootte nodig hebt, kunt u een volgende schaal bewerking uitvoeren voor de gewenste grootte.
* U kunt niet rechtstreeks schalen van een **Basic** -cache naar een **Premium** -cache. Eerst schalen van **Basic** naar **Standard** in één schaal bewerking en vervolgens schalen van **standaard** naar **Premium** in een volgende bewerking.
* U kunt niet van een grotere grootte schalen naar de grootte van de **C0 (250 MB)** .

Als een schaal bewerking mislukt, probeert de service de bewerking ongedaan te maken en wordt de cache teruggezet naar de oorspronkelijke grootte.


### <a name="how-long-does-scaling-take"></a>Hoe lang duurt het voor schalen?
De schaal tijd is afhankelijk van de hoeveelheid gegevens in de cache, waarbij grotere hoeveel heden gegevens langer duren om te volt ooien. Het schalen duurt ongeveer 20 minuten. Voor geclusterde caches duurt het schalen ongeveer 20 minuten per Shard.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Hoe kan ik zien wanneer schalen is voltooid?
In de Azure Portal ziet u dat de schaal bewerking wordt uitgevoerd. Wanneer het schalen is voltooid, verandert de status van de cache in **actief**.

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png

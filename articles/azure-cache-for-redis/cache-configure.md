---
title: Een Azure Cache voor Redis
description: De standaardconfiguratie van Redis voor Azure Cache voor Redis en leren hoe u uw Azure Cache voor Redis configureert
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: yegu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ffcaf7fc50a310cdd4773868c62bbbb801619e3c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833144"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>Een Azure Cache voor Redis
In dit onderwerp worden de configuraties beschreven die beschikbaar zijn voor Azure Cache voor Redis-exemplaren. In dit onderwerp wordt ook de standaardconfiguratie van de Redis-server voor Azure Cache voor Redis besproken.

> [!NOTE]
> Zie Persistentie [configureren,](cache-how-to-premium-persistence.md) [Clustering](cache-how-to-premium-clustering.md)configureren en Ondersteuning Virtual Network configureren voor meer informatie over het configureren en gebruiken van [premium-cachefuncties.](cache-how-to-premium-vnet.md)
>
>

## <a name="configure-azure-cache-for-redis-settings"></a>Instellingen Azure Cache voor Redis configureren
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Azure Cache voor Redis instellingen worden weergegeven en geconfigureerd op de **Azure Cache voor Redis** blade met behulp van **het resourcemenu**.

![Azure Cache voor Redis instellingen](./media/cache-configure/redis-cache-settings.png)

U kunt de volgende instellingen weergeven en configureren met behulp van **het resourcemenu**.

* [Overzicht](#overview)
* [Activiteitenlogboek](#activity-log)
* [Toegangsbeheer (IAM)](#access-control-iam)
* [Tags](#tags)
* [Problemen vaststellen en oplossen](#diagnose-and-solve-problems)
* [Instellingen](#settings)
    * [Toegangssleutels](#access-keys)
    * [Geavanceerde instellingen](#advanced-settings)
    * [Azure Cache voor Redis Advisor](#azure-cache-for-redis-advisor)
    * [Schalen](#scale)
    * [Grootte van cluster](#cluster-size)
    * [Gegevenspersistentie](#redis-data-persistence)
    * [Updates plannen](#schedule-updates)
    * [Geo-replicatie](#geo-replication)
    * [Virtual Network](#virtual-network)
    * [Firewall](#firewall)
    * [Eigenschappen](#properties)
    * [Vergrendelingen](#locks)
    * [Automation-script](#automation-script)
* Beheer
    * [Gegevens importeren](#importexport)
    * [Gegevens exporteren](#importexport)
    * [Opnieuw opstarten](#reboot)
* [Controle](#monitoring)
    * [Metrische gegevens van Redis](#redis-metrics)
    * [Waarschuwingsregels](#alert-rules)
    * [Diagnostics](#diagnostics)
* Instellingen voor & voor probleemoplossing
    * [Status van resources](#resource-health)
    * [Nieuwe ondersteuningsaanvraag](#new-support-request)


## <a name="overview"></a>Overzicht

**Overzicht** biedt u basisinformatie over uw cache, zoals naam, poorten, prijscategorie en geselecteerde metrische gegevens voor de cache.

### <a name="activity-log"></a>Activiteitenlogboek

Klik **op Activiteitenlogboek** om acties weer te geven die op uw cache zijn uitgevoerd. U kunt ook filteren gebruiken om deze weergave uit te vouwen om andere resources op te nemen. Zie Bewerkingen controleren met Resource Manager voor meer informatie over het werken [met auditlogboeken.](../azure-resource-manager/management/view-activity-logs.md) Zie Bewerkingen en waarschuwingen voor meer Azure Cache voor Redis bewaking [van gebeurtenissen.](cache-how-to-monitor.md#operations-and-alerts)

### <a name="access-control-iam"></a>Toegangsbeheer (IAM)

De **sectie Toegangsbeheer (IAM)** biedt ondersteuning voor op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) in Azure Portal. Met deze configuratie kunnen organisaties eenvoudig en nauwkeurig voldoen aan hun vereisten voor toegangsbeheer. Zie Op rollen gebaseerd toegangsbeheer [van Azure in](../role-based-access-control/role-assignments-portal.md)de Azure Portal.

### <a name="tags"></a>Tags

De **sectie Tags** helpt u bij het organiseren van uw resources. Zie [Tags gebruiken om uw Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md) voor meer informatie.


### <a name="diagnose-and-solve-problems"></a>Problemen vaststellen en oplossen

Klik **op Problemen vaststellen en oplossen** die moeten worden opgegeven met veelvoorkomende problemen en strategieën voor het oplossen ervan.



## <a name="settings"></a>Instellingen
In **de sectie** Instellingen kunt u de volgende instellingen voor uw cache openen en configureren.

* [Toegangssleutels](#access-keys)
* [Geavanceerde instellingen](#advanced-settings)
* [Azure Cache voor Redis Advisor](#azure-cache-for-redis-advisor)
* [Schalen](#scale)
* [Grootte van cluster](#cluster-size)
* [Gegevenspersistentie](#redis-data-persistence)
* [Updates plannen](#schedule-updates)
* [Geo-replicatie](#geo-replication)
* [Virtual Network](#virtual-network)
* [Firewall](#firewall)
* [Eigenschappen](#properties)
* [Vergrendelingen](#locks)
* [Automation-script](#automation-script)



### <a name="access-keys"></a>Toegangssleutels
Klik **op Toegangssleutels** om de toegangssleutels voor uw cache weer te geven of opnieuw te maken. Deze sleutels worden gebruikt door de clients die verbinding maken met uw cache.

![Azure Cache voor Redis-toegangssleutels](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Geavanceerde instellingen
De volgende instellingen worden geconfigureerd op de blade **Geavanceerde** instellingen.

* [Toegangspoorten](#access-ports)
* [Geheugenbeleid](#memory-policies)
* [Keyspace-meldingen (geavanceerde instellingen)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Toegangspoorten
Niet-TLS/SSL-toegang is standaard uitgeschakeld voor nieuwe caches. Als u de niet-TLS-poort wilt inschakelen, klikt u op De toegang **alleen via SSL** toestaan op de blade Geavanceerde instellingen en klikt u vervolgens op **Opslaan.**  

> [!NOTE]
> TLS-toegang tot Azure Cache voor Redis biedt momenteel ondersteuning voor TLS 1.0, 1.1 en 1.2, maar versies 1.0 en 1.1 worden binnenkort uit gebruik genomen.  Lees onze [pagina TLS 1.0 en 1.1 verwijderen](cache-remove-tls-10-11.md) voor meer informatie.

![Azure Cache voor Redis-toegangspoorten](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Geheugenbeleid
Met **het beleid Maxmemory,** **maxmemory-reserved** en **maxfragmentationmemory-reserved op** de blade Geavanceerde instellingen configureert u het geheugenbeleid voor de cache. 

![Azure Cache voor Redis Maxmemory-beleid](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Met het maxmemory-beleid** wordt het beleid voor het verwijderen van de cache geconfigureerd en kunt u kiezen uit de volgende beleidsregels voor verwijderen:

* `volatile-lru` - Dit is het standaardverzettingsbeleid.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

Zie `maxmemory` [Eviction policies (Beleid voor het uitzettingsbeleid) voor meer informatie over beleidsregels.](https://redis.io/topics/lru-cache#eviction-policies)

De **instelling maxmemory-reserved** configureert de hoeveelheid geheugen, in MB per exemplaar in een cluster, die is gereserveerd voor niet-cachebewerkingen, zoals replicatie tijdens failover. Door deze waarde in te stellen, kunt u een consistentere Redis-serverervaring hebben wanneer uw belasting varieert. Deze waarde moet hoger worden ingesteld voor workloads die veel schrijven. Wanneer geheugen is gereserveerd voor dergelijke bewerkingen, is het niet beschikbaar voor opslag van gegevens in de cache.

De **instelling maxfragmentationmemory-reserved** configureert de hoeveelheid geheugen, in MB per instantie in een cluster, die is gereserveerd voor geheugenfragmentatie. Door deze waarde in te stellen, kunt u een consistentere Redis-serverervaring hebben wanneer de cache vol of bijna vol is en de fragmentatieverhouding hoog is. Wanneer geheugen is gereserveerd voor dergelijke bewerkingen, is het niet beschikbaar voor opslag van gegevens in de cache.

Eén ding om rekening mee te houden bij het kiezen van een nieuwe geheugenreserveringswaarde **(maxmemory-reserved** of **maxfragmentationmemory-reserved)** is hoe deze wijziging van invloed kan zijn op een cache die al wordt uitgevoerd met grote hoeveelheden gegevens. Als u bijvoorbeeld een cache van 53 GB met 49 GB aan gegevens hebt, wijzigt u de reserveringswaarde in 8 GB. Met deze wijziging wordt het maximaal beschikbare geheugen voor het systeem omlaag 45 GB. Als uw huidige of uw waarden hoger zijn dan de nieuwe limiet van 45 GB, moet het systeem gegevens verwijderen totdat beide en lager zijn dan `used_memory` `used_memory_rss` `used_memory` `used_memory_rss` 45 GB. Uitzetting kan de serverbelasting en geheugenfragmentatie verhogen. Zie Beschikbare metrische gegevens en rapportage-intervallen voor meer informatie over metrische gegevens in de cache, zoals `used_memory` `used_memory_rss` [en](cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

> [!IMPORTANT]
> De **instellingen maxmemory-reserved** en **maxfragmentationmemory-reserved** zijn alleen beschikbaar voor Standard- en Premium-caches.
>
>

#### <a name="keyspace-notifications-advanced-settings"></a>Keyspace-meldingen (geavanceerde instellingen)
Redis Keyspace-meldingen worden geconfigureerd op de blade **Geavanceerde** instellingen. Met Keyspace-meldingen kunnen clients meldingen ontvangen wanneer bepaalde gebeurtenissen optreden.

![Azure Cache voor Redis Geavanceerde instellingen](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Keyspace-meldingen en de **instelling notify-keyspace-events** zijn alleen beschikbaar voor Standard- en Premium-caches.
>
>

Zie [Redis Keyspace Notifications (Redis Keyspace-meldingen) voor meer informatie.](https://redis.io/topics/notifications) Zie het bestand [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) in het [Hello world-voorbeeld](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) voor voorbeeldcode.


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Azure Cache voor Redis Advisor
Op **Azure Cache voor Redis Advisor-blade** worden aanbevelingen voor uw cache weergegeven. Tijdens normale bewerkingen worden er geen aanbevelingen weergegeven.

![Schermopname die laat zien waar de aanbevelingen worden weergegeven.](./media/cache-configure/redis-cache-no-recommendations.png)

Als er voorwaarden optreden tijdens de bewerkingen van uw cache, zoals een hoog geheugengebruik, netwerkbandbreedte of serverbelasting, wordt er een waarschuwing weergegeven op de Azure Cache voor Redis **blade.**

![Schermopname die laat zien waar waarschuwingen worden weergegeven in de Azure Cache voor Redis sectie.](./media/cache-configure/redis-cache-recommendations-alert.png)

Meer informatie vindt u op **de** blade Aanbevelingen.

![Aanbevelingen](./media/cache-configure/redis-cache-recommendations.png)

U kunt deze metrische gegevens [](cache-how-to-monitor.md#usage-charts) bewaken in [de secties Bewakingsgrafieken](cache-how-to-monitor.md#monitoring-charts) en Gebruiksgrafieken van de **Azure Cache voor Redis** blade.

Elke prijscategorie heeft verschillende limieten voor clientverbindingen, geheugen en bandbreedte. Als uw cache de maximale capaciteit voor deze metrische gegevens gedurende een langere periode benadert, wordt een aanbeveling gemaakt. Zie de volgende tabel voor meer informatie over de metrische gegevens en limieten **die worden** gecontroleerd door het hulpprogramma Aanbevelingen:

| Azure Cache voor Redis metrische gegevens | Meer informatie |
| --- | --- |
| Gebruik van netwerkbandbreedte |[Cacheprestaties - beschikbare bandbreedte](cache-planning-faq.md#azure-cache-for-redis-performance) |
| Verbonden clients |[Standaardconfiguratie van Redis-server - maxclients](#maxclients) |
| Server laden |[Gebruiksgrafieken - Laden van Redis-server](cache-how-to-monitor.md#usage-charts) |
| Geheugengebruik |[Cacheprestaties - grootte](cache-planning-faq.md#azure-cache-for-redis-performance) |

Als u uw cache wilt upgraden, klikt **u op Nu upgraden** om de prijscategorie te wijzigen en uw cache [te schalen.](#scale) Zie De juiste laag kiezen voor meer informatie over het [kiezen van een prijscategorie](cache-overview.md#choosing-the-right-tier)


### <a name="scale"></a>Schalen
Klik **op Schalen** om de prijscategorie voor uw cache te bekijken of te wijzigen. Zie How to Scale Azure Cache voor Redis voor [meer informatie over Azure Cache voor Redis.](cache-how-to-scale.md)

![Azure Cache voor Redis prijscategorie](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Grootte van Redis-cluster
Klik **op Clustergrootte** om de clustergrootte te wijzigen voor een actief Premium-cache met clustering ingeschakeld.

![Grootte van cluster](./media/cache-configure/redis-cache-redis-cluster-size.png)

Als u de clustergrootte wilt wijzigen, gebruikt u de schuifregelaar of typt u een getal tussen 1 en 10 in het **tekstvak Shard count** en klikt u op **OK** om op te slaan.

> [!IMPORTANT]
> Redis-clustering is alleen beschikbaar voor Premium-caches. Zie [Clustering voor een Premium Azure Cache voor Redis configureren](cache-how-to-premium-clustering.md) voor meer informatie.
>
>


### <a name="redis-data-persistence"></a>Redis-gegevenspersistentie
Klik **op Gegevens persistentie** om gegevens persistentie voor uw Premium-cache in te schakelen, uit te schakelen of te configureren. Azure Cache voor Redis biedt Redis-persistentie met behulp van RDB-persistentie of AOF-persistentie.

Zie [Persistentie configureren voor een Premium Azure Cache voor Redis](cache-how-to-premium-persistence.md) voor meer informatie.


> [!IMPORTANT]
> Redis-gegevens persistentie is alleen beschikbaar voor Premium-caches.
>
>

### <a name="schedule-updates"></a>Updates plannen
Op de blade **Updates** plannen kunt u een onderhoudsvenster voor Redis-serverupdates voor uw cache aanwijzen.

> [!IMPORTANT]
> Het onderhoudsvenster is alleen van toepassing op Redis-serverupdates en niet op Azure-updates of -updates voor het besturingssysteem van de VM's die als host voor de cache worden gebruikt.
>
>

![Updates plannen](./media/cache-configure/redis-schedule-updates.png)

Als u een onderhoudsvenster wilt opgeven, controleert u de gewenste dagen en geeft u het beginuur van het onderhoudsvenster voor elke dag op en klikt u op **OK.** De tijd van het onderhoudsvenster is in UTC.

Zie Beheer van Azure Cache voor Redis - Updates [plannen voor meer informatie en instructies](cache-administration.md#schedule-updates)

### <a name="geo-replication"></a>Geo-replicatie

De **blade Geo-replicatie** biedt een mechanisme voor het koppelen van twee Premium-Azure Cache voor Redis exemplaren. De ene cache wordt aangewezen als de primaire gekoppelde cache en de andere als de secundaire gekoppelde cache. De secundaire gekoppelde cache wordt alleen-lezen en gegevens die naar de primaire cache worden geschreven, worden gerepliceerd naar de secundaire gekoppelde cache. Deze functionaliteit kan worden gebruikt om een cache te repliceren tussen Azure-regio's.

> [!IMPORTANT]
> **Geo-replicatie** is alleen beschikbaar voor Premium-caches. Zie [Geo-replicatie](cache-how-to-geo-replication.md)configureren voor Azure Cache voor Redis voor meer Azure Cache voor Redis.
>
>

### <a name="virtual-network"></a>Virtual Network
In **Virtual Network** sectie kunt u de instellingen van het virtuele netwerk voor uw cache configureren. Zie How to configure Virtual Network Support for a Premium Azure Cache voor Redis (Ondersteuning voor een Premium-cache configureren voor een Premium-cache met VNET-ondersteuning en het bijwerken van de [instellingen Virtual Network).](cache-how-to-premium-vnet.md)

> [!IMPORTANT]
> Instellingen voor virtuele netwerken zijn alleen beschikbaar voor Premium-caches die tijdens het maken van de cache zijn geconfigureerd met VNET-ondersteuning.
>
>

### <a name="firewall"></a>Firewall

Configuratie van firewallregels is beschikbaar voor alle Azure Cache voor Redis-lagen.

Klik **op Firewall om** firewallregels voor de cache te bekijken en configureren.

![Firewall](./media/cache-configure/redis-firewall-rules.png)

U kunt firewallregels opgeven met een begin- en eind-IP-adresbereik. Wanneer firewallregels zijn geconfigureerd, kunnen alleen clientverbindingen uit de opgegeven IP-adresbereiken verbinding maken met de cache. Wanneer een firewallregel wordt opgeslagen, is er een korte vertraging voordat de regel van kracht wordt. Deze vertraging is doorgaans minder dan één minuut.

> [!IMPORTANT]
> Verbindingen van Azure Cache voor Redis bewakingssystemen zijn altijd toegestaan, zelfs als firewallregels zijn geconfigureerd.
>
>

### <a name="properties"></a>Eigenschappen
Klik **op Eigenschappen** om informatie over uw cache weer te geven, met inbegrip van het cache-eindpunt en de poorten.

![Azure Cache voor Redis eigenschappen](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Vergrendelingen
In **de** sectie Vergrendelingen kunt u een abonnement, resourcegroep of resource vergrendelen om te voorkomen dat andere gebruikers in uw organisatie per ongeluk kritieke resources verwijderen of wijzigen. Zie voor meer informatie [Resources vergrendelen met Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).

### <a name="automation-script"></a>Automation-script

Klik **op Automation-script** om een sjabloon van uw geïmplementeerde resources te bouwen en te exporteren voor toekomstige implementaties. Zie Resources implementeren met sjablonen voor meer informatie over [het werken Azure Resource Manager sjablonen.](../azure-resource-manager/templates/deploy-powershell.md)

## <a name="administration-settings"></a>Beheerinstellingen
Met de instellingen in **de sectie** Beheer kunt u de volgende beheertaken uitvoeren voor uw cache.

![Beheer](./media/cache-configure/redis-cache-administration.png)

* [Gegevens importeren](#importexport)
* [Gegevens exporteren](#importexport)
* [Opnieuw opstarten](#reboot)


### <a name="importexport"></a>Import/Export
Importeren/exporteren is een Azure Cache voor Redis-bewerking voor gegevensbeheer, waarmee u gegevens in de cache kunt importeren en exporteren door een RDB-momentopname (Azure Cache voor Redis Database) van een Premium-cache te importeren en exporteren naar een pagina-blob in een Azure Storage-account. Met Import/Export kunt u migreren tussen verschillende Azure Cache voor Redis exemplaren of de cache vullen met gegevens vóór gebruik.

Importeren kan worden gebruikt om Redis-compatibele RDB-bestanden te importeren vanaf elke Redis-server die wordt uitgevoerd in elke cloud of omgeving, waaronder Redis die wordt uitgevoerd op Linux, Windows of een cloudprovider zoals Amazon Web Services en andere. Het importeren van gegevens is een eenvoudige manier om een cache met vooraf ingevulde gegevens te maken. Tijdens het importproces worden Azure Cache voor Redis RDB-bestanden uit Azure Storage in het geheugen geladen en worden de sleutels vervolgens in de cache geplaatst.

Met Exporteren kunt u de gegevens die zijn opgeslagen in Azure Cache voor Redis exporteren naar redis-compatibele RDB-bestanden. U kunt deze functie gebruiken om gegevens van het ene exemplaar Azure Cache voor Redis verplaatsen naar een andere of naar een andere Redis-server. Tijdens het exportproces wordt er een tijdelijk bestand gemaakt op de VM die als host voor het Azure Cache voor Redis-serverexe geval wordt gebruikt en wordt het bestand geüpload naar het aangewezen opslagaccount. Wanneer de exportbewerking is voltooid met de status Geslaagd of Mislukt, wordt het tijdelijke bestand verwijderd.

> [!IMPORTANT]
> Importeren/exporteren is alleen beschikbaar voor Premium-caches. Zie Gegevens importeren en exporteren in Azure Cache voor Redis voor [meer Azure Cache voor Redis.](cache-how-to-import-export-data.md)
>
>

### <a name="reboot"></a>Opnieuw opstarten
Op **de** blade Opnieuw opstarten kunt u de knooppunten van uw cache opnieuw opstarten. Met deze mogelijkheid voor opnieuw opstarten kunt u uw toepassing testen op tolerantie als er een storing in een cache-knooppunt is.

![Opnieuw opstarten](./media/cache-configure/redis-cache-reboot.png)

Als u een Premium-cache hebt waarvoor clustering is ingeschakeld, kunt u selecteren welke shards van de cache opnieuw moeten worden opgestart.

![Schermopname die laat zien waar u kunt selecteren welke shards van de cache opnieuw moeten worden opgestart.](./media/cache-configure/redis-cache-reboot-cluster.png)

Als u een of meer knooppunten van uw cache opnieuw wilt opstarten, selecteert u de gewenste knooppunten en klikt u op **Opnieuw opstarten.** Als u een Premium-cache hebt met clustering ingeschakeld, selecteert u de shard(s) om opnieuw op te starten en klikt u vervolgens op **Opnieuw opstarten.** Na enkele minuten worden de geselecteerde knooppunt(en) opnieuw opgestart en zijn ze een paar minuten later weer online.

> [!IMPORTANT]
> Opnieuw opstarten is nu beschikbaar voor alle prijslagen. Zie Beheer van Azure Cache voor Redis - Opnieuw [opstarten voor meer informatie en instructies.](cache-administration.md#reboot)
>
>


## <a name="monitoring"></a>Bewaking

In **de** sectie Bewaking kunt u diagnostische gegevens en bewaking configureren voor uw Azure Cache voor Redis.
Zie How to monitor Azure Cache voor Redis voor meer informatie over Azure Cache voor Redis en [diagnostische Azure Cache voor Redis.](cache-how-to-monitor.md)

![Diagnostiek](./media/cache-configure/redis-cache-diagnostics.png)

* [Metrische redis-gegevens](#redis-metrics)
* [Waarschuwingsregels](#alert-rules)
* [Diagnostics](#diagnostics)

### <a name="redis-metrics"></a>Metrische redis-gegevens
Klik **op Metrische gegevens van Redis** om [metrische gegevens voor](cache-how-to-monitor.md#view-cache-metrics) uw cache weer te geven.

### <a name="alert-rules"></a>Waarschuwingsregels

Klik **op Waarschuwingsregels om** waarschuwingen te configureren op basis van Azure Cache voor Redis metrische gegevens. Zie Waarschuwingen voor [meer informatie.](cache-how-to-monitor.md#alerts)

### <a name="diagnostics"></a>Diagnostiek

Standaard worden metrische cachegegevens in Azure Monitor [30](../azure-monitor/essentials/data-platform-metrics.md) dagen opgeslagen en vervolgens verwijderd. Als u de metrische gegevens van uw  cache [](cache-how-to-monitor.md#export-cache-metrics) langer dan 30 dagen wilt bewaren, klikt u op Diagnostische gegevens om het opslagaccount te configureren dat wordt gebruikt voor het opslaan van diagnostische gegevens in de cache.

>[!NOTE]
>Naast het archiveren van de metrische gegevens van uw cache naar opslag, kunt u ze ook streamen naar een Event Hub of ze verzenden [naar Azure Monitor logboeken.](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)
>
>

## <a name="support--troubleshooting-settings"></a>Instellingen voor & voor probleemoplossing
De instellingen in de **sectie Ondersteuning en probleemoplossing** bieden opties voor het oplossen van problemen met uw cache.

![Ondersteuning en probleemoplossing](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Status van resources](#resource-health)
* [Nieuwe ondersteuningsaanvraag](#new-support-request)

### <a name="resource-health"></a>Status van resources
**Resource Health** houdt uw resource in de hand en geeft aan of deze wordt uitgevoerd zoals verwacht. Zie Overzicht van Azure Resource Health voor meer informatie over [de Azure Resource Health-service.](../service-health/resource-health-overview.md)

> [!NOTE]
> Resource health kan momenteel niet rapporteren over de status van Azure Cache voor Redis exemplaren die worden gehost in een virtueel netwerk. Zie Werken alle cachefuncties bij het hosten van een [cache in een VNET voor meer informatie?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network)
>
>

### <a name="new-support-request"></a>Nieuwe ondersteuningsaanvraag
Klik **op Nieuwe ondersteuningsaanvraag** om een ondersteuningsaanvraag voor uw cache te openen.





## <a name="default-redis-server-configuration"></a>Standaardconfiguratie van Redis-server
Nieuwe Azure Cache voor Redis zijn geconfigureerd met de volgende standaardwaarden voor Redis-configuratie:

> [!NOTE]
> De instellingen in deze sectie kunnen niet worden gewijzigd met behulp van de `StackExchange.Redis.IServer.ConfigSet` methode . Als deze methode wordt aangeroepen met een van de opdrachten in deze sectie, wordt er een uitzondering geseed die vergelijkbaar is met het volgende voorbeeld:  
>
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>
> Waarden die configureerbaar zijn, zoals **max-memory-policy,** kunnen worden geconfigureerd via de Azure Portal of opdrachtregelbeheerprogramma's zoals Azure CLI of PowerShell.
>
>

| Instelling | Standaardwaarde | Beschrijving |
| --- | --- | --- |
| `databases` |16 |Het standaardaantal databases is 16, maar u kunt een ander aantal configureren op basis van de prijscategorie. <sup>1</sup> De standaarddatabase is DB 0. U kunt per verbinding een andere database selecteren met behulp van , waarbij `connection.GetDatabase(dbid)` een getal tussen en `dbid` `0` `databases - 1` is. |
| `maxclients` |Is afhankelijk van prijscategorie<sup>2</sup> |Deze waarde is het maximum aantal verbonden clients dat tegelijkertijd is toegestaan. Zodra de limiet is bereikt, sluit Redis alle nieuwe verbindingen en wordt de fout 'maximaal aantal clients bereikt' weergegeven. |
| `maxmemory-policy` |`volatile-lru` |Maxmemory-beleid is de instelling voor de manier waarop Redis selecteert wat moet worden verwijderd wanneer (de grootte van de cacheaanbieding die u hebt geselecteerd tijdens het maken van de `maxmemory` cache) is bereikt. Met Azure Cache voor Redis de standaardinstelling is , waarmee de sleutels met een vervaldatumset worden verwijderd `volatile-lru` met behulp van een LRU-algoritme. Deze instelling kan worden geconfigureerd in de Azure Portal. Zie Geheugenbeleid [voor meer informatie.](#memory-policies) |
| `maxmemory-samples` |3 |Om geheugen te besparen, zijn LRU en minimale TTL-algoritmen bij benadering algoritmen in plaats van nauwkeurige algoritmen. Redis controleert standaard drie sleutels en kiest de sleutel die minder recent is gebruikt. |
| `lua-time-limit` |5\.000 |Maximale uitvoeringstijd van een Lua-script in milliseconden. Als de maximale uitvoeringstijd is bereikt, registreert Redis dat een script nog steeds wordt uitgevoerd na de maximaal toegestane tijd en wordt gestart met het beantwoorden van query's met een fout. |
| `lua-event-limit` |500 |Maximale grootte van de scriptgebeurteniswachtrij. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032 MB 8 MB 60 |De bufferlimieten voor clientuitvoer kunnen worden gebruikt om de verbinding van clients af te dwingen die om de een of andere reden niet snel genoeg gegevens van de server lezen (een veelvoorkomende reden is dat een Pub/Sub-client niet zo snel berichten kan verbruiken als de uitgever ze kan produceren). Zie [https://redis.io/topics/clients](https://redis.io/topics/clients) voor meer informatie. |

<a name="databases"></a>
<sup>1</sup> De limiet voor is voor elke Azure Cache voor Redis prijscategorie en `databases` kan worden ingesteld bij het maken van de cache. Als er `databases` geen instelling is opgegeven tijdens het maken van de cache, is de standaardwaarde 16.

* Basic- en Standard-caches
  * C0-cache (250 MB) - maximaal 16 databases
  * C1-cache (1 GB) - maximaal 16 databases
  * C2-cache (2,5 GB) - maximaal 16 databases
  * C3-cache (6 GB) - maximaal 16 databases
  * C4-cache (13 GB) - maximaal 32 databases
  * C5-cache (26 GB) - maximaal 48 databases
  * C6-cache (53 GB) - maximaal 64 databases
* Premium-caches
  * P1 (6 GB - 60 GB) - maximaal 16 databases
  * P2 (13 GB - 130 GB) - maximaal 32 databases
  * P3 (26 GB - 260 GB) - maximaal 48 databases
  * P4 (53 GB - 530 GB) - maximaal 64 databases
  * Alle Premium-caches met Redis-cluster ingeschakeld: redis-cluster ondersteunt alleen het gebruik van database 0, dus de limiet voor elke `databases` Premium-cache met Redis-cluster ingeschakeld is effectief 1 en de [opdracht](https://redis.io/commands/select) Selecteren is niet toegestaan. Zie Moet ik wijzigingen aanbrengen in mijn clienttoepassing om clustering te gebruiken? voor [meer informatie.](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Zie Wat zijn Redis-databases? voor [meer informatie over databases.](cache-development-faq.md#what-are-redis-databases)

> [!NOTE]
> De instelling kan alleen worden geconfigureerd tijdens het maken van de cache en alleen met `databases` behulp van PowerShell, CLI of andere beheer-clients. Zie `databases` [New-AzRedisCache](cache-how-to-manage-redis-cache-powershell.md#databases)voor een voorbeeld van het configureren tijdens het maken van de cache met behulp van PowerShell.
>
>

<a name="maxclients"></a>
<sup>2</sup> `maxclients` verschilt voor elke Azure Cache voor Redis prijscategorie.

* Basic- en Standard-caches
  * C0-cache (250 MB) - maximaal 256 verbindingen
  * C1-cache (1 GB) - maximaal 1000 verbindingen
  * C2-cache (2,5 GB) - maximaal 2000 verbindingen
  * C3-cache (6 GB) - maximaal 5000 verbindingen
  * C4-cache (13 GB) - maximaal 10.000 verbindingen
  * C5-cache (26 GB) - maximaal 15.000 verbindingen
  * C6-cache (53 GB) - maximaal 20.000 verbindingen
* Premium-caches
  * P1 (6 GB - 60 GB) - maximaal 7500 verbindingen
  * P2 (13 GB - 130 GB) - maximaal 15.000 verbindingen
  * P3 (26 GB - 260 GB) - maximaal 30.000 verbindingen
  * P4 (53 GB - 530 GB) - maximaal 40.000 verbindingen

> [!NOTE]
> Hoewel elke cachegrootte *een bepaald* aantal verbindingen toestaat, is aan elke verbinding met Redis overhead gekoppeld. Een voorbeeld van dergelijke overhead is cpu- en geheugengebruik als gevolg van TLS/SSL-versleuteling. De maximale verbindingslimiet voor een bepaalde cachegrootte gaat uit van een licht geladen cache. Als de belasting van de overhead van de *verbinding plus* de belasting van clientbewerkingen de capaciteit voor het systeem overschrijdt, kan de cache capaciteitsproblemen ervaren, zelfs als u de verbindingslimiet voor de huidige cachegrootte niet hebt overschreden.
>
>



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>Redis-opdrachten worden niet ondersteund in Azure Cache voor Redis
> [!IMPORTANT]
> Omdat de configuratie en het beheer Azure Cache voor Redis exemplaren door Microsoft worden beheerd, zijn de volgende opdrachten uitgeschakeld. Als u deze probeert aan te roepen, ontvangt u een foutbericht dat vergelijkbaar is met `"(error) ERR unknown command"` .
>
> * BGREWRITEAOF
> * BGSAVE
> * Config
> * FOUTOPSPORING
> * Migreren
> * OPSLAAN
> * Afsluiten
> * SLAVEOF
> * CLUSTER: schrijfopdrachten voor clusters zijn uitgeschakeld, maar alleen-lezen clusteropdrachten zijn toegestaan.
>
>

Zie voor meer informatie over Redis-opdrachten. [https://redis.io/commands](https://redis.io/commands)

## <a name="redis-console"></a>Redis-console
U kunt veilig opdrachten geven aan uw Azure Cache voor Redis-exemplaren met behulp van de **Redis-console**, die beschikbaar is in de Azure Portal voor alle cachelagen.

> [!IMPORTANT]
> - De Redis-console werkt niet met [VNET](cache-how-to-premium-vnet.md). Wanneer uw cache deel uitmaakt van een VNET, hebben alleen clients in het VNET toegang tot de cache. Omdat de Redis-console wordt uitgevoerd in uw lokale browser, die zich buiten het VNET, kan deze geen verbinding maken met uw cache.
> - Niet alle Redis-opdrachten worden ondersteund in Azure Cache voor Redis. Voor een lijst met Redis-opdrachten die zijn uitgeschakeld voor Azure Cache voor Redis, zie de vorige [Redis-opdrachten](#redis-commands-not-supported-in-azure-cache-for-redis) die niet worden ondersteund in Azure Cache voor Redis sectie. Zie voor meer informatie over Redis-opdrachten. [https://redis.io/commands](https://redis.io/commands)
>
>

Als u toegang wilt krijgen tot de Redis-console, klikt u op **Console** **Azure Cache voor Redis** blade.

![Schermopname met de knop Console.](./media/cache-configure/redis-console-menu.png)

Als u opdrachten wilt uitvoeren voor uw cache-exemplaar, typt u de gewenste opdracht in de console.

![Schermopname van thas toont de Redis-console met de invoeropdracht en de resultaten.](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>De Redis-console gebruiken met een Premium-geclusterde cache

Wanneer u de Redis-console gebruikt met een Premium-geclusterde cache, kunt u opdrachten geven aan één shard van de cache. Als u een opdracht wilt geven aan een specifieke shard, maakt u eerst verbinding met de gewenste shard door erop te klikken in de shard- picker.

![Redis-console](./media/cache-configure/redis-console-premium-cluster.png)

Als u probeert toegang te krijgen tot een sleutel die is opgeslagen in een andere shard dan de verbonden shard, ontvangt u een foutbericht dat lijkt op het volgende bericht:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

In het vorige voorbeeld is shard 1 de geselecteerde shard, maar bevindt zich in shard 0, zoals aangegeven door het gedeelte `myKey` `(shard 0)` van het foutbericht. In dit voorbeeld selecteert u voor toegang shard 0 met behulp van `myKey` de shard- picker en geeft u vervolgens de gewenste opdracht uit.


## <a name="move-your-cache-to-a-new-subscription"></a>Uw cache verplaatsen naar een nieuw abonnement
U kunt uw cache verplaatsen naar een nieuw abonnement door te klikken op **Verplaatsen.**

![Verplaatsen Azure Cache voor Redis](./media/cache-configure/redis-cache-move.png)

Zie Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement voor meer informatie over het verplaatsen van resources van de ene naar de [andere resourcegroep en](../azure-resource-manager/management/move-resource-group-and-subscription.md)van het ene naar het andere abonnement.

## <a name="next-steps"></a>Volgende stappen
* Zie Hoe kan ik Redis-opdrachten uitvoeren? voor meer informatie over het werken [met Redis-opdrachten.](cache-development-faq.md#how-can-i-run-redis-commands)

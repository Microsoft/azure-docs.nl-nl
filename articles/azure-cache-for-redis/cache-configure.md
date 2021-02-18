---
title: Azure-cache configureren voor redis
description: Inzicht in de standaard redis-configuratie voor Azure cache voor redis en meer informatie over het configureren van uw Azure-cache voor redis-instanties
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: yegu
ms.openlocfilehash: ba021d280f71504ad5c1e1aea0dc1c8c6b507b1e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100581072"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>Azure-cache configureren voor redis
In dit onderwerp worden de configuraties beschreven die beschikbaar zijn voor uw Azure-cache voor redis-exemplaren. In dit onderwerp wordt ook de standaard redis-server configuratie voor Azure cache voor redis-exemplaren besproken.

> [!NOTE]
> Zie voor meer informatie over het configureren en gebruiken van Premium-cache functies, [persistentie configureren](cache-how-to-premium-persistence.md), [clustering configureren](cache-how-to-premium-clustering.md)en [Virtual Network ondersteuning configureren](cache-how-to-premium-vnet.md).
>
>

## <a name="configure-azure-cache-for-redis-settings"></a>Azure-cache voor redis-instellingen configureren
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Azure cache voor redis-instellingen worden weer gegeven en geconfigureerd op de Blade **Azure-cache voor redis** met behulp van het **resource menu**.

![Azure cache voor redis-instellingen](./media/cache-configure/redis-cache-settings.png)

U kunt de volgende instellingen weer geven en configureren met behulp van het **resource menu**.

* [Overzicht](#overview)
* [Activiteitenlogboek](#activity-log)
* [Toegangsbeheer (IAM)](#access-control-iam)
* [Tags](#tags)
* [Problemen vaststellen en oplossen](#diagnose-and-solve-problems)
* [Instellingen](#settings)
    * [Toegangssleutels](#access-keys)
    * [Geavanceerde instellingen](#advanced-settings)
    * [Azure-cache voor redis Advisor](#azure-cache-for-redis-advisor)
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
    * [Metrische gegevens van redis](#redis-metrics)
    * [Waarschuwingsregels](#alert-rules)
    * [Diagnostics](#diagnostics)
* Ondersteuning voor het oplossen van problemen met instellingen &
    * [Status van resources](#resource-health)
    * [Nieuwe ondersteuningsaanvraag](#new-support-request)


## <a name="overview"></a>Overzicht

**Overzicht** geeft u algemene informatie over uw cache, zoals naam, poorten, prijs categorie en geselecteerde cache-metrische gegevens.

### <a name="activity-log"></a>Activiteitenlogboek

Klik op **activiteiten logboek** om de acties weer te geven die zijn uitgevoerd op uw cache. U kunt ook filteren gebruiken om deze weer gave uit te breiden tot ook andere resources. Zie [bewerkingen controleren met Resource Manager](../azure-resource-manager/management/view-activity-logs.md)voor meer informatie over het werken met audit Logboeken. Zie [bewerkingen en waarschuwingen](cache-how-to-monitor.md#operations-and-alerts)voor meer informatie over het bewaken van Azure cache voor redis-gebeurtenissen.

### <a name="access-control-iam"></a>Toegangsbeheer (IAM)

De sectie **toegangs beheer (IAM)** biedt ondersteuning voor op rollen gebaseerd toegangs beheer (Azure RBAC) van Azure in de Azure Portal. Met deze configuratie kunnen organisaties eenvoudig en nauw keurig voldoen aan de vereisten voor toegangs beheer. Zie voor meer informatie op [rollen gebaseerd toegangs beheer van Azure in de Azure Portal](../role-based-access-control/role-assignments-portal.md).

### <a name="tags"></a>Tags

De sectie **Tags** helpt u bij het organiseren van uw resources. Zie [Tags gebruiken om uw Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md) voor meer informatie.


### <a name="diagnose-and-solve-problems"></a>Problemen vaststellen en oplossen

Klik op **problemen vaststellen en oplossen** om te voorzien in veelvoorkomende problemen en strategieën voor het oplossen ervan.



## <a name="settings"></a>Instellingen
De sectie **instellingen** biedt u de mogelijkheid om de volgende instellingen voor uw cache te openen en te configureren.

* [Toegangssleutels](#access-keys)
* [Geavanceerde instellingen](#advanced-settings)
* [Azure-cache voor redis Advisor](#azure-cache-for-redis-advisor)
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
Klik op **toegangs sleutels** om de toegangs sleutels voor uw cache weer te geven of opnieuw te genereren. Deze sleutels worden gebruikt door de clients die verbinding maken met uw cache.

![Azure-cache voor redis-toegangs sleutels](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Geavanceerde instellingen
De volgende instellingen worden geconfigureerd op de Blade **Geavanceerde instellingen** .

* [Toegangs poorten](#access-ports)
* [Geheugen beleid](#memory-policies)
* [Meldingen voor de beschik bare ruimte (geavanceerde instellingen)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Toegangs poorten
Standaard is niet-TLS/SSL-toegang uitgeschakeld voor nieuwe caches. Als u de niet-TLS-poort wilt inschakelen, klikt u op **Nee** voor **alleen toegang via SSL toestaan** op de Blade **Geavanceerde instellingen** en klikt u vervolgens op **Opslaan**.

> [!NOTE]
> TLS-toegang tot Azure cache voor redis ondersteunt momenteel TLS 1,0, 1,1 en 1,2, maar versies 1,0 en 1,1 worden binnenkort buiten gebruik gesteld.  Lees onze [pagina Remove TLS 1,0 en 1,1](cache-remove-tls-10-11.md) voor meer informatie.

![Azure-cache voor redis-toegangs poorten](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Geheugen beleid
De instellingen voor **Maxmemory-beleid**, **Maxmemory-reserved** en **maxfragmentationmemory-reserved** op de Blade **Geavanceerde instellingen** configureren het geheugen beleid voor de cache.

![Azure-cache voor redis Maxmemory-beleid](./media/cache-configure/redis-cache-maxmemory-policy.png)

Met **Maxmemory-beleid** configureert u het verwijderings beleid voor de cache en kunt u kiezen uit het volgende verwijderings beleid:

* `volatile-lru` -Dit is het standaard verwijderings beleid.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

`maxmemory`Zie [verwijderings beleid](https://redis.io/topics/lru-cache#eviction-policies)voor meer informatie over beleid.

De instelling **maxmemory-gereserveerde** configureert de hoeveelheid geheugen, in MB per exemplaar in een cluster, dat is gereserveerd voor niet-cache bewerkingen, zoals replicatie tijdens failover. Door deze waarde in te stellen, kunt u een consistente redis-server ervaring hebben wanneer uw belasting varieert. Deze waarde moet hoger worden ingesteld voor werk belastingen die zijn geschreven. Wanneer geheugen is gereserveerd voor dergelijke bewerkingen, is het niet beschikbaar voor opslag van gegevens in de cache.

De instelling **maxfragmentationmemory-gereserveerde** configureert de hoeveelheid geheugen, in MB per exemplaar in een cluster, dat is gereserveerd voor geheugen fragmentatie. Door deze waarde in te stellen, kunt u een consistente redis-server ervaring hebben wanneer de cache vol is of bijna vol is en de fragmentatie verhouding hoog is. Wanneer geheugen is gereserveerd voor dergelijke bewerkingen, is het niet beschikbaar voor opslag van gegevens in de cache.

Een ding waarmee u rekening moet houden bij het kiezen van een nieuwe waarde voor geheugen reservering (**maxmemory** of **maxfragmentationmemory-** Reserved), is de manier waarop deze wijziging van invloed kan zijn op een cache die al wordt uitgevoerd met grote hoeveel heden gegevens. Als u bijvoorbeeld een cache van 53 GB met gegevens van 49 GB hebt, wijzigt u de reserverings waarde in 8 GB. deze wijziging zal de Maxi maal beschik bare hoeveelheid geheugen voor het systeem verlagen tot 45 GB. Als uw huidige `used_memory` waarde of uw `used_memory_rss` waarden hoger zijn dan de nieuwe limiet van 45 GB, dan moet het systeem gegevens verwijderen tot beide `used_memory` en `used_memory_rss` onder 45 GB vallen. Verwijderen kan de belasting van de server en de geheugen fragmentatie verhogen. `used_memory` `used_memory_rss` Zie [beschik bare metrische gegevens en rapportage-intervallen](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)voor meer informatie over de cache-metrische gegevens, zoals en.

> [!IMPORTANT]
> De instellingen voor **maxmemory** en **maxfragmentationmemory-reserve** ring zijn alleen beschikbaar voor de standaard-en Premium-caches.
>
>

#### <a name="keyspace-notifications-advanced-settings"></a>Meldingen voor de beschik bare ruimte (geavanceerde instellingen)
Redis moeten worden geconfigureerd op de Blade **Geavanceerde instellingen** . Met deze meldingen kunnen clients meldingen ontvangen wanneer bepaalde gebeurtenissen optreden.

![Azure cache voor geavanceerde instellingen voor redis](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> De instellingen voor het instellen van de gebeurtenis-ruimte en de instelling voor de **bericht-** Keys-fout ruimte zijn alleen beschikbaar voor de standaard-en Premium-caches.
>
>

Zie [redis-meldingen](https://redis.io/topics/notifications)voor meer informatie. Zie het [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) -bestand in het [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) -voor beeld voor een voorbeeld code.


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Azure-cache voor redis Advisor
In de Blade **Azure cache voor redis Advisor** worden aanbevelingen voor uw cache weer gegeven. Tijdens normale bewerkingen worden er geen aanbevelingen weer gegeven.

![Scherm afbeelding die laat zien waar de aanbevelingen worden weer gegeven.](./media/cache-configure/redis-cache-no-recommendations.png)

Als er omstandigheden optreden tijdens de bewerkingen van uw cache, zoals intensief geheugen gebruik, netwerk bandbreedte of server belasting, wordt er een waarschuwing weer gegeven op de Blade **Azure-cache voor redis** .

![Scherm afbeelding die laat zien waar waarschuwingen worden weer gegeven in de sectie Azure-cache voor redis.](./media/cache-configure/redis-cache-recommendations-alert.png)

Meer informatie vindt u op de Blade **aanbevelingen** .

![Aanbevelingen](./media/cache-configure/redis-cache-recommendations.png)

U kunt deze metrische gegevens bewaken in de secties [controle grafieken](cache-how-to-monitor.md#monitoring-charts) en [gebruiks grafieken](cache-how-to-monitor.md#usage-charts) van de Blade **Azure-cache voor redis** .

Elke prijs categorie heeft verschillende limieten voor client verbindingen, geheugen en band breedte. Als uw cache de maximale capaciteit voor deze metrische gegevens gedurende een langere periode nadert, wordt er een aanbeveling gemaakt. Zie de volgende tabel voor meer informatie over de metrische gegevens en limieten die door het hulp programma **aanbevelingen** worden gecontroleerd:

| Azure-cache voor redis-metriek | Meer informatie |
| --- | --- |
| Gebruik van netwerkbandbreedte |[Beschik bare band breedte in cache opslaan](cache-planning-faq.md#azure-cache-for-redis-performance) |
| Verbonden clients |[Standaard redis-server configuratie-MaxClients](#maxclients) |
| Server belasting |[Gebruiks grafieken-redis-server belasting](cache-how-to-monitor.md#usage-charts) |
| Geheugengebruik |[Cache prestaties-grootte](cache-planning-faq.md#azure-cache-for-redis-performance) |

Als u uw cache wilt bijwerken, klikt u op **Nu bijwerken** om de prijs categorie te wijzigen en uw cache te [schalen](#scale) . Zie [de juiste laag kiezen](cache-overview.md#choosing-the-right-tier) voor meer informatie over het kiezen van een prijs categorie


### <a name="scale"></a>Schalen
Klik op **schalen** om de prijs categorie voor uw cache weer te geven of te wijzigen. Zie [Azure-cache schalen voor redis](cache-how-to-scale.md)voor meer informatie over schalen.

![Azure cache voor redis-prijs categorie](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Grootte van redis-cluster
Klik op **cluster grootte** om de cluster grootte te wijzigen voor een actieve Premium-cache waarbij Clustering is ingeschakeld.

![Grootte van cluster](./media/cache-configure/redis-cache-redis-cluster-size.png)

Als u de cluster grootte wilt wijzigen, gebruikt u de schuif regelaar of typt u een getal tussen 1 en 10 in het tekstvak **Shard aantal** en klikt u op **OK** om op te slaan.

> [!IMPORTANT]
> Redis-Clustering is alleen beschikbaar voor Premium-caches. Zie [Clustering voor een Premium Azure Cache voor Redis configureren](cache-how-to-premium-clustering.md) voor meer informatie.
>
>


### <a name="redis-data-persistence"></a>Redis-gegevenspersistentie
Klik op **gegevens persistentie** om gegevens persistentie voor uw Premium-cache in te scha kelen, uit te scha kelen of te configureren. Azure cache voor redis biedt redis persistentie met behulp van RDB-persistentie of AOF-persistentie.

Zie [Persistentie configureren voor een Premium Azure Cache voor Redis](cache-how-to-premium-persistence.md) voor meer informatie.


> [!IMPORTANT]
> Redis-gegevens persistentie is alleen beschikbaar voor Premium-caches.
>
>

### <a name="schedule-updates"></a>Updates plannen
Op de Blade **updates plannen** kunt u een onderhouds venster voor redis-server updates voor uw cache aanwijzen.

> [!IMPORTANT]
> Het onderhouds venster is alleen van toepassing op redis-server updates en niet op Azure-updates of updates voor het besturings systeem van de virtuele machines die de cache hosten.
>
>

![Updates plannen](./media/cache-configure/redis-schedule-updates.png)

Als u een onderhouds venster wilt opgeven, controleert u de gewenste dagen en geeft u het onderhouds venster voor elke dag op en klikt u op **OK**. De tijd van het onderhouds venster is UTC.

Zie voor meer informatie en instructies [Azure cache voor redis-beheer-updates plannen](cache-administration.md#schedule-updates)

### <a name="geo-replication"></a>Geo-replicatie

De Blade **geo-replicatie** biedt een mechanisme voor het koppelen van twee Premium-laag Azure-cache voor redis-exemplaren. Eén cache wordt aangeduid als de primaire gekoppelde cache en de andere als de secundaire gekoppelde cache. De secundaire gekoppelde cache wordt alleen-lezen en de gegevens die naar de primaire cache worden geschreven, worden gerepliceerd naar de secundaire gekoppelde cache. Deze functionaliteit kan worden gebruikt om een cache te repliceren tussen Azure-regio's.

> [!IMPORTANT]
> **Geo-replicatie** is alleen beschikbaar voor Premium-laag caches. Zie [geo-replicatie voor Azure cache configureren voor redis](cache-how-to-geo-replication.md)voor meer informatie en instructies.
>
>

### <a name="virtual-network"></a>Virtual Network
Met de sectie **Virtual Network** kunt u de instellingen van het virtuele netwerk voor uw cache configureren. Zie [Virtual Network ondersteuning configureren voor een Premium Azure-cache voor redis](cache-how-to-premium-vnet.md)voor meer informatie over het maken van een Premium-cache met VNET-ondersteuning en het bijwerken van de instellingen.

> [!IMPORTANT]
> De instellingen voor het virtuele netwerk zijn alleen beschikbaar voor Premium-caches die zijn geconfigureerd met VNET-ondersteuning tijdens het maken van de cache.
>
>

### <a name="firewall"></a>Firewall

De configuratie van de firewall regels is beschikbaar voor alle Azure-cache voor redis-lagen.

Klik op **firewall** om firewall regels voor cache weer te geven en te configureren.

![Firewall](./media/cache-configure/redis-firewall-rules.png)

U kunt Firewall regels met een begin-en eind-IP-adres bereik opgeven. Wanneer de firewall regels zijn geconfigureerd, kunnen alleen client verbindingen van de opgegeven IP-adresbereiken verbinding maken met de cache. Wanneer een firewall regel wordt opgeslagen, is er een korte vertraging voordat de regel effectief is. Deze vertraging is doorgaans minder dan een minuut.

> [!IMPORTANT]
> Verbindingen van Azure cache voor redis-bewakings systemen zijn altijd toegestaan, zelfs als de firewall regels zijn geconfigureerd.
>
>

### <a name="properties"></a>Eigenschappen
Klik op **Eigenschappen** om informatie over uw cache weer te geven, met inbegrip van het cache-eind punt en de poorten.

![Azure cache voor redis-eigenschappen](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Vergrendelingen
Met de sectie **vergren** delen kunt u een abonnement, een resource groep of een resource om te voor komen dat andere gebruikers in uw organisatie per ongeluk essentiële bronnen verwijderen of wijzigen. Zie voor meer informatie [Resources vergrendelen met Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).

### <a name="automation-script"></a>Automation-script

Klik op **Automation script** om een sjabloon van uw geïmplementeerde resources te bouwen en exporteren voor toekomstige implementaties. Zie [resources implementeren met Azure Resource Manager sjablonen](../azure-resource-manager/templates/deploy-powershell.md)voor meer informatie over het werken met sjablonen.

## <a name="administration-settings"></a>Beheer instellingen
Met de instellingen in de sectie **beheer** kunt u de volgende beheer taken voor uw cache uitvoeren.

![Beheer](./media/cache-configure/redis-cache-administration.png)

* [Gegevens importeren](#importexport)
* [Gegevens exporteren](#importexport)
* [Opnieuw opstarten](#reboot)


### <a name="importexport"></a>Import/Export
Import/export is een Azure cache voor redis-gegevens beheer bewerking, waarmee u gegevens in de cache kunt importeren en exporteren door een Azure-cache voor een redis-data base (RDB)-moment opname te importeren en exporteren van een Premium-cache naar een pagina-Blob in een Azure Storage-account. Met importeren/exporteren kunt u tussen verschillende Azure-caches migreren voor redis-exemplaren of de cache vullen met gegevens voor gebruik.

Importeren kan worden gebruikt om redis compatibele RDB-bestanden te halen van elke redis-server die in een wille keurige Cloud of omgeving wordt uitgevoerd, waaronder redis die wordt uitgevoerd op Linux, Windows of een Cloud provider zoals Amazon Web Services en anderen. Het importeren van gegevens is een eenvoudige manier om een cache met vooraf gevulde gegevens te maken. Tijdens het import proces laadt Azure cache voor redis de RDB-bestanden vanuit Azure Storage in het geheugen en voegt vervolgens de sleutels in de cache in.

Met exporteren kunt u de gegevens die zijn opgeslagen in azure cache exporteren voor redis naar redis-compatibele RDB-bestanden. U kunt deze functie gebruiken om gegevens van één Azure-cache te verplaatsen voor een redis-exemplaar naar een ander of een andere redis-server. Tijdens het export proces wordt er een tijdelijk bestand gemaakt op de virtuele machine die als host fungeert voor de Azure-cache voor redis server-exemplaar, en wordt het bestand geüpload naar het aangewezen opslag account. Wanneer de export bewerking is voltooid met de status geslaagd of mislukt, wordt het tijdelijke bestand verwijderd.

> [!IMPORTANT]
> Import/export is alleen beschikbaar voor Premium-laag caches. Zie [gegevens importeren en exporteren in azure-cache voor redis](cache-how-to-import-export-data.md)voor meer informatie en instructies.
>
>

### <a name="reboot"></a>Opnieuw opstarten
Op de Blade **opnieuw opstarten** kunt u de knoop punten van de cache opnieuw opstarten. Met deze mogelijkheid voor opnieuw opstarten kunt u uw toepassing testen op tolerantie als er een fout optreedt in een cache knooppunt.

![Opnieuw opstarten](./media/cache-configure/redis-cache-reboot.png)

Als u een Premium-cache met clustering hebt ingeschakeld, kunt u selecteren welke Shards de cache moet worden opgestart.

![Scherm afbeelding die laat zien waar u kunt selecteren welke Shards van de cache moet worden opgestart.](./media/cache-configure/redis-cache-reboot-cluster.png)

Als u een of meer knoop punten van uw cache opnieuw wilt opstarten, selecteert u de gewenste knoop punten en klikt u op **opnieuw opstarten**. Als u een Premium-cache met clustering hebt ingeschakeld, selecteert u de Shard (s) om opnieuw op te starten en klikt u vervolgens op **opnieuw opstarten**. Na een paar minuten worden de geselecteerde knoop punten opnieuw opgestart en zijn ze een paar minuten later weer online.

> [!IMPORTANT]
> Opnieuw opstarten is nu beschikbaar voor alle prijs categorieën. Zie voor meer informatie en instructies [Azure cache voor redis Administration-reboot](cache-administration.md#reboot).
>
>


## <a name="monitoring"></a>Bewaking

In de sectie **bewaking** kunt u Diagnostische gegevens en bewaking configureren voor uw Azure-cache voor redis.
Zie [Azure-cache bewaken voor redis](cache-how-to-monitor.md)voor meer informatie over Azure cache voor redis-controle en-diagnose.

![Diagnostiek](./media/cache-configure/redis-cache-diagnostics.png)

* [Metrische gegevens van redis](#redis-metrics)
* [Waarschuwingsregels](#alert-rules)
* [Diagnostics](#diagnostics)

### <a name="redis-metrics"></a>Metrische gegevens van redis
Klik op **redis metrische gegevens** om de [metrische gegevens](cache-how-to-monitor.md#view-cache-metrics) voor uw cache weer te geven.

### <a name="alert-rules"></a>Waarschuwingsregels

Klik op **waarschuwings regels** om waarschuwingen te configureren op basis van Azure cache voor redis metrische gegevens. Zie [waarschuwingen](cache-how-to-monitor.md#alerts)voor meer informatie.

### <a name="diagnostics"></a>Diagnostiek

Standaard worden de metrische gegevens in de cache van Azure Monitor [30 dagen opgeslagen](../azure-monitor/essentials/data-platform-metrics.md) en vervolgens verwijderd. Als u de cache gegevens langer dan 30 dagen wilt behouden, klikt u op **diagnose** om [het opslag account te configureren](cache-how-to-monitor.md#export-cache-metrics) dat wordt gebruikt voor het opslaan van de diagnostische gegevens van de cache.

>[!NOTE]
>Naast het archiveren van uw cache-metrische gegevens naar opslag, kunt u [ze ook streamen naar een event hub of naar Azure monitor-logboeken verzenden](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md).
>
>

## <a name="support--troubleshooting-settings"></a>Ondersteuning voor het oplossen van problemen met instellingen &
De instellingen in de sectie **ondersteuning en probleem oplossing** bieden u opties voor het oplossen van problemen met uw cache.

![Ondersteuning en probleemoplossing](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Status van resources](#resource-health)
* [Nieuwe ondersteuningsaanvraag](#new-support-request)

### <a name="resource-health"></a>Status van resources
De **resource status** houdt uw resource in de gaten en vertelt u of deze wordt uitgevoerd zoals verwacht. Zie [overzicht van Azure resource Health](../service-health/resource-health-overview.md)voor meer informatie over de Azure resource Health-Service.

> [!NOTE]
> Resource Health kan momenteel niet rapporteren over de status van Azure-cache voor redis-exemplaren die worden gehost in een virtueel netwerk. Zie [alle cache functies werken bij het hosten van een cache in een VNET](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network) voor meer informatie.
>
>

### <a name="new-support-request"></a>Nieuwe ondersteuningsaanvraag
Klik op **nieuwe ondersteunings aanvraag** om een ondersteunings aanvraag voor uw cache te openen.





## <a name="default-redis-server-configuration"></a>Standaard configuratie van de redis-server
Nieuwe Azure-cache voor redis-exemplaren worden geconfigureerd met de volgende standaard instellingen voor redis-configuratie:

> [!NOTE]
> De instellingen in deze sectie kunnen niet worden gewijzigd met de- `StackExchange.Redis.IServer.ConfigSet` methode. Als deze methode wordt aangeroepen met een van de opdrachten in deze sectie, wordt een uitzonde ring gegenereerd die lijkt op het volgende voor beeld:  
>
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>
> Alle waarden die kunnen worden geconfigureerd, zoals **Max-Memory-Policy**, kunnen worden geconfigureerd via de Azure portal of opdracht regel beheer Programma's zoals Azure CLI of Power shell.
>
>

| Instelling | Standaardwaarde | Beschrijving |
| --- | --- | --- |
| `databases` |16 |Het standaard aantal data bases is 16, maar u kunt een ander getal configureren op basis van de prijs categorie. <sup>1</sup> de standaard database is db 0. u kunt een andere waarde per verbinding selecteren, `connection.GetDatabase(dbid)` waarbij u `dbid` een getal tussen `0` en gebruikt `databases - 1` . |
| `maxclients` |Is afhankelijk van de prijs categorie<sup>2</sup> |Deze waarde is het maximum aantal aangesloten clients dat tegelijkertijd is toegestaan. Zodra de limiet is bereikt, sluit redis alle nieuwe verbindingen af en wordt de fout ' maximum aantal clients bereikt ' geretourneerd. |
| `maxmemory-policy` |`volatile-lru` |Maxmemory-beleid is de instelling voor hoe redis selecteert wat moet worden verwijderd wanneer `maxmemory` (de grootte van de cache aanbieding die u hebt geselecteerd tijdens het maken van de cache) is bereikt. Met Azure cache voor redis is de standaard instelling `volatile-lru` , waarmee de sleutels met een verloopset worden verwijderd met behulp van een LRU-algoritme. Deze instelling kan worden geconfigureerd in de Azure Portal. Zie [geheugen beleid](#memory-policies)voor meer informatie. |
| `maxmemory-samples` |3 |Voor het opslaan van geheugen zijn LRU en minimale TTL-algoritmen een geschatte algoritme in plaats van nauw keurige algoritmen. Standaard worden met redis drie sleutels gecontroleerd en wordt de versie gekozen die minder recent is gebruikt. |
| `lua-time-limit` |5\.000 |Maximale uitvoerings tijd van een Lua-script in milliseconden. Als de maximale uitvoerings tijd is bereikt, redis logboeken dat een script nog steeds wordt uitgevoerd na de Maxi maal toegestane tijd en begint met het beantwoorden van query's met een fout. |
| `lua-event-limit` |500 |Maximale grootte van script gebeurtenis wachtrij. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8mb 60 |De limieten voor client uitvoer buffers kunnen worden gebruikt om te voor komen dat clients die geen gegevens van de server snel genoeg worden gelezen, om een of andere reden. (een veelvoorkomende reden is dat een pub/sub-client berichten niet kan gebruiken als de uitgever deze kan produceren). Zie [https://redis.io/topics/clients](https://redis.io/topics/clients) voor meer informatie. |

<a name="databases"></a>
<sup>1</sup> De limiet voor `databases` is verschillend voor elke Azure-cache voor redis prijs categorie en kan worden ingesteld tijdens het maken van de cache. Als er geen `databases` instelling wordt opgegeven tijdens het maken van de cache, is de standaard waarde 16.

* Basic-en Standard-caches
  * C0 (250 MB) cache-Maxi maal 16 data bases
  * C1 (1 GB) cache-Maxi maal 16 data bases
  * C2 (2,5 GB) cache-Maxi maal 16 data bases
  * C3 (6 GB) cache-Maxi maal 16 data bases
  * C4 (13 GB) cache-Maxi maal 32 data bases
  * C5 (26 GB) cache-Maxi maal 48 data bases
  * C6 (53 GB) cache-up-to-64-data bases
* Premium-caches
  * P1 (6 GB-60 GB)-Maxi maal 16 data bases
  * P2 (13 GB-130 GB)-Maxi maal 32 data bases
  * P3 (26 GB-260 GB)-Maxi maal 48 data bases
  * P4 (53 GB-530 GB)-Maxi maal 64 data bases
  * Alle Premium-caches met redis-cluster enabled-redis cluster ondersteunt alleen het gebruik van data base 0, dus de `databases` limiet voor een Premium-cache waarvoor redis-cluster is ingeschakeld, is in feite 1 en de [selectie](https://redis.io/commands/select) opdracht is niet toegestaan. Zie voor meer informatie [moet ik wijzigingen aanbrengen in mijn client toepassing voor het gebruik van clusters?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Zie [Wat zijn redis-data bases?](cache-development-faq.md#what-are-redis-databases) voor meer informatie over data bases.

> [!NOTE]
> De `databases` instelling kan alleen worden geconfigureerd tijdens het maken van de cache en alleen Power shell, CLI of andere management-clients gebruiken. Zie New-AzRedisCache voor een voor beeld van het configureren van het maken van een `databases` cache met behulp van Power shell. [](cache-how-to-manage-redis-cache-powershell.md#databases)
>
>

<a name="maxclients"></a>
<sup>2</sup> `maxclients` is verschillend voor elke Azure-cache voor redis prijs categorie.

* Basic-en Standard-caches
  * C0 (250 MB) cache-Maxi maal 256 verbindingen
  * C1 (1 GB) cache-Maxi maal 1.000 verbindingen
  * C2 (2,5 GB) cache-Maxi maal 2.000 verbindingen
  * C3 (6 GB) cache-Maxi maal 5.000 verbindingen
  * C4 (13 GB) cache-Maxi maal 10.000 verbindingen
  * C5 (26 GB) cache-Maxi maal 15.000 verbindingen
  * C6 (53 GB) cache-Maxi maal 20.000 verbindingen
* Premium-caches
  * P1 (6 GB-60 GB)-Maxi maal 7.500 verbindingen
  * P2 (13 GB-130 GB)-Maxi maal 15.000 verbindingen
  * P3 (26 GB-260 GB)-Maxi maal 30.000 verbindingen
  * P4 (53 GB-530 GB)-tot 40.000 verbindingen

> [!NOTE]
> Hoewel elke grootte van de cache een bepaald aantal *verbindingen toestaat,* is er aan elke verbinding met redis overhead gekoppeld. Een voor beeld van een dergelijke overhead is het CPU-en geheugen gebruik als gevolg van TLS/SSL-versleuteling. Voor de maximum verbindings limiet voor een bepaalde cache grootte wordt uitgegaan van een licht geladen cache. Als de belasting van de verbindings overhead *plus* de belasting van client bewerkingen de capaciteit voor het systeem overschrijdt, kan de cache capaciteits problemen ondervinden, zelfs als u de verbindings limiet voor de huidige cache grootte niet overschrijdt.
>
>



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>Redis-opdrachten die niet worden ondersteund in azure cache voor redis
> [!IMPORTANT]
> Omdat de configuratie en het beheer van Azure cache voor redis-exemplaren door micro soft worden beheerd, worden de volgende opdrachten uitgeschakeld. Als u deze probeert aan te roepen, wordt een fout bericht van de volgende strekking weer gegeven `"(error) ERR unknown command"` .
>
> * BGREWRITEAOF
> * BGSAVE
> * CONFIGURATIES
> * FOUTOPSPORING
> * MIGRAT
> * OPSLAAN
> * MATIG
> * SLAVEOF
> * CLUSTER-schrijf opdrachten in het cluster zijn uitgeschakeld, maar alleen-lezen cluster opdrachten zijn toegestaan.
>
>

Zie voor meer informatie over redis-opdrachten [https://redis.io/commands](https://redis.io/commands) .

## <a name="redis-console"></a>Redis-console
U kunt met behulp van de **redis-console**, die beschikbaar is in de Azure portal voor alle cache-lagen, veilig opdrachten verzenden naar uw Azure-cache voor redis-instanties.

> [!IMPORTANT]
> - De redis-console werkt niet met [VNET](cache-how-to-premium-vnet.md). Wanneer uw cache deel uitmaakt van een VNET, hebben alleen clients in het VNET toegang tot de cache. Omdat de redis-console wordt uitgevoerd in uw lokale browser, die zich buiten het VNET bevindt, kan er geen verbinding worden gemaakt met uw cache.
> - Niet alle redis-opdrachten worden ondersteund in azure cache voor redis. Zie de vorige [redis-opdrachten die niet worden ondersteund in azure cache for redis](#redis-commands-not-supported-in-azure-cache-for-redis) voor een lijst met redis-opdrachten die zijn uitgeschakeld voor Azure-cache voor redis. Zie voor meer informatie over redis-opdrachten [https://redis.io/commands](https://redis.io/commands) .
>
>

Om toegang te krijgen tot de redis-console, klikt u op **console** in de Blade **Azure-cache voor redis** .

![Scherm opname van de knop voor de console.](./media/cache-configure/redis-console-menu.png)

Typ de gewenste opdracht in de-console om opdrachten uit te voeren voor uw cache-exemplaar.

![In de scherm afbeelding Thas wordt de redis-console weer gegeven met de invoer opdracht en de resultaten.](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>De redis-console gebruiken met een Premium geclusterde cache

Wanneer u de redis-console gebruikt met een Premium geclusterde cache, kunt u opdrachten uitgeven aan één Shard van de cache. Als u een opdracht wilt uitgeven voor een specifieke Shard, moet u eerst verbinding maken met de gewenste Shard door erop te klikken in de Shard-kiezer.

![Redis-console](./media/cache-configure/redis-console-premium-cluster.png)

Als u probeert toegang te krijgen tot een sleutel die is opgeslagen in een andere Shard dan de verbonden Shard, wordt een fout bericht weer gegeven dat vergelijkbaar is met het volgende bericht:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

In het vorige voor beeld is Shard 1 de geselecteerde Shard, maar `myKey` bevindt zich in Shard 0, zoals wordt aangegeven door het `(shard 0)` gedeelte van het fout bericht. In dit voor beeld selecteert u `myKey` Shard 0 met de Shard-kiezer en geeft u vervolgens de gewenste opdracht op.


## <a name="move-your-cache-to-a-new-subscription"></a>Uw cache verplaatsen naar een nieuw abonnement
U kunt uw cache verplaatsen naar een nieuw abonnement door te klikken op **verplaatsen**.

![Azure-cache verplaatsen voor redis](./media/cache-configure/redis-cache-move.png)

Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)voor informatie over het verplaatsen van resources van de ene resource groep naar een andere en van het ene naar het andere abonnement.

## <a name="next-steps"></a>Volgende stappen
* Zie voor meer informatie over het werken met redis-opdrachten [Hoe kan ik redis-opdrachten uitvoeren?](cache-development-faq.md#how-can-i-run-redis-commands)

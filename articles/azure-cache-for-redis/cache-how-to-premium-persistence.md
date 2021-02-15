---
title: Gegevens persistentie configureren-Premium Azure cache voor redis
description: Meer informatie over het configureren en beheren van gegevens persistentie voor Azure-cache voor redis-instanties in uw Premium-laag
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 58148e3a20ba41ae9707543be290f2d632cb1185
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375286"
---
# <a name="configure-data-persistence-for-a-premium-azure-cache-for-redis-instance"></a>Gegevens persistentie configureren voor een Premium Azure-cache voor een redis-exemplaar

Met [redis persistentie](https://redis.io/topics/persistence) kunt u gegevens bewaren die zijn opgeslagen in redis. U kunt ook moment opnamen maken en back-ups maken van de gegevens, die u in het geval van een hardwarestoring kunt laden. Dit is een enorm voor deel van de Basic-of Standard-laag waarbij alle gegevens worden opgeslagen in het geheugen en er mogelijk gegevens verlies kan optreden in het geval van een storing waarbij cache knooppunten niet beschikbaar zijn. 

Azure cache voor redis biedt redis persistentie met de volgende modellen:

* **RDB-persistentie** : wanneer de persistentie van RDB (redis data base) is geconfigureerd, blijft Azure cache voor redis een moment opname van de Azure-cache voor redis in een redis binaire indeling op schijf op basis van een Configureer bare back-upfrequentie. Als er sprake is van een onherstelbare gebeurtenis die de primaire cache en de replica in de replicatie uitschakelt, wordt de cache opnieuw opgebouwd met de meest recente moment opname. Meer informatie over de [voor](https://redis.io/topics/persistence#rdb-advantages) -en [nadelen](https://redis.io/topics/persistence#rdb-disadvantages) van RDB-persistentie.
* **AOF Persistence** : wanneer AOF (alleen bestand toevoegen) persistentie is geconfigureerd, slaat Azure cache voor redis elke schrijf bewerking op in een logboek dat ten minste eenmaal per seconde in een Azure Storage-account is opgeslagen. Als er sprake is van een onherstelbare gebeurtenis die de primaire cache en de replica in de replicatie uitschakelt, wordt de cache opnieuw opgebouwd met behulp van de opgeslagen schrijf bewerkingen. Meer informatie over de [voor](https://redis.io/topics/persistence#aof-advantages) -en [nadelen](https://redis.io/topics/persistence#aof-disadvantages) van AOF persistentie.

Met persistentie worden redis-gegevens naar een Azure Storage-account geschreven dat u bezit en beheert. U kunt configureren vanaf de Blade **nieuwe Azure-cache voor redis** tijdens het maken van de cache en in het **resource menu** voor bestaande Premium-caches.

> [!NOTE]
> 
> Azure Storage versleutelt gegevens automatisch wanneer deze persistent worden gemaakt. U kunt uw eigen sleutels gebruiken voor de versleuteling. Zie [door de klant beheerde sleutels met Azure Key Vault](../storage/common/storage-service-encryption.md)voor meer informatie.
> 
> 

## <a name="set-up-data-persistence"></a>Gegevens persistentie instellen

1. Als u een Premium-cache wilt maken, meldt u zich aan bij de [Azure Portal](https://portal.azure.com) en selecteert u **een resource maken**. U kunt caches niet alleen in Azure Portal maken. U kunt ze ook maken met Resource Manager-sjablonen, PowerShell of Azure CLI. Zie [Een cache maken](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache) voor meer informatie over het maken van een Azure Cache voor Redis.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Resource maken.":::
   
2. Selecteer op de pagina **Nieuw** de optie **Databases** en selecteer vervolgens **Azure Cache voor Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Selecteer Azure Cache voor Redis.":::

3. Configureer op de pagina **nieuw redis cache** de instellingen voor uw nieuwe Premium-cache.
   
   | Instelling      | Voorgestelde waarde  | Beschrijving |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-naam** | Geef een wereldwijd unieke naam op. | De cache naam moet een teken reeks zijn tussen 1 en 63 tekens die alleen cijfers, letters of afbreek streepjes bevatten. De naam moet beginnen en eindigen met een cijfer of letter en mag geen opeenvolgende afbreekstreepjes bevatten. De *hostnaam* van uw cache-exemplaar wordt *\<DNS name>.redis.cache.windows.net*. | 
   | **Abonnement** | Vervolg keuzelijst en selecteer uw abonnement. | Het abonnement waarmee dit nieuwe Azure Cache voor Redis-exemplaar wordt gemaakt. | 
   | **Resourcegroep** | Vervolg keuzelijst en selecteer een resource groep of selecteer **nieuwe maken** en voer een nieuwe naam voor de resource groep in. | Naam voor de resourcegroep waarin de cache en andere resources moeten worden gemaakt. Door al uw app-resources in één resourcegroep te plaatsen, kunt u ze eenvoudig beheren of verwijderen. | 
   | **Locatie** | Vervolg keuzelijst en selecteer een locatie. | Selecteer een [regio](https://azure.microsoft.com/regions/) in de buurt van andere services die gaan gebruikmaken van de cache. |
   | **Cachetype** | En selecteer een Premium-cache om Premium-functies te configureren. Zie [Azure cache for redis prijzen](https://azure.microsoft.com/pricing/details/cache/)voor meer informatie. |  De prijscategorie bepaalt de grootte, prestaties en functies die beschikbaar zijn voor de cache. Zie het [Azure Cache voor Redis-overzicht](cache-overview.md) voor meer informatie. |

4. Selecteer het tabblad **Netwerken** of klik op de knop **Netwerken** onderaan de pagina.

5. Selecteer uw verbindingsmethode op het tabblad **Netwerk**. Voor Premium-cache-instanties kunt u openbaar, via open bare IP-adressen of service-eind punten, of privé, verbinding maken met behulp van een persoonlijk eind punt.

6. Selecteer het tabblad **Volgende: Geavanceerd** of klik op de knop **Volgende: Geavanceerd** onderaan de pagina.

7. Configureer op het tabblad **Geavanceerd** voor een Premium-cache-exemplaar de instellingen voor niet-TLS-poort, clustering en gegevens persistentie. Voor gegevens persistentie kunt u een **RDB** -of **AOF** -persistentie kiezen. 

8. Als u RDB-persistentie wilt inschakelen, klikt u op **RDB** en configureert u de instellingen. 
   
   | Instelling      | Voorgestelde waarde  | Beschrijving |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Back-upfrequentie** | Vervolg keuzelijst en selecteer een back-upinterval, met de volgende opties: **15 minuten**, **30 minuten**, **60 minuten**, **6 uur**, **12 uur** en **24 uur**. | Dit interval begint met tellen nadat de vorige back-upbewerking is voltooid en wanneer wordt gewacht tot een nieuwe back-up wordt gestart. | 
   | **Opslagaccount** | Vervolg keuzelijst en selecteer uw opslag account. | U moet een opslag account in dezelfde regio en hetzelfde abonnement als de cache kiezen en een **Premium Storage** account wordt aanbevolen omdat Premium Storage een hogere door Voer heeft.  | 
   | **Opslagsleutel** | En kies de **primaire** of **secundaire sleutel** die u wilt gebruiken. | Als de opslag sleutel voor uw persistentie account opnieuw wordt gegenereerd, moet u de gewenste sleutel opnieuw configureren in de vervolg keuzelijst **opslag sleutel** . | 

    De eerste back-up wordt gestart zodra het interval voor de back-upfrequentie is verstreken.
    
   > [!NOTE]
   > Wanneer u een back-up van RDB-bestanden naar de opslag maakt, worden deze opgeslagen in de vorm van pagina-blobs.

9. Als u AOF-persistentie wilt inschakelen, klikt u op **AOF** en configureert u de instellingen. 
   
   | Instelling      | Voorgestelde waarde  | Beschrijving |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Eerste opslag account** | Vervolg keuzelijst en selecteer uw opslag account. | Dit opslag account moet zich in dezelfde regio en hetzelfde abonnement bevinden als de cache, en een **Premium Storage** -account wordt aanbevolen omdat Premium Storage een hogere door Voer heeft. | 
   | **Eerste opslag sleutel** | En kies de **primaire** of **secundaire sleutel** die u wilt gebruiken. | Als de opslag sleutel voor uw persistentie account opnieuw wordt gegenereerd, moet u de gewenste sleutel opnieuw configureren in de vervolg keuzelijst **opslag sleutel** . | 
   | **Tweede opslag account** | Beschrijving Vervolg keuzelijst en selecteer uw secundaire opslag account. | U kunt optioneel een extra opslag account configureren. Als er een tweede opslag account is geconfigureerd, worden de schrijf bewerkingen naar de replica cache naar dit tweede opslag account geschreven. | 
   | **Tweede opslag sleutel** | Beschrijving En kies de **primaire** of **secundaire sleutel** die u wilt gebruiken. | Als de opslag sleutel voor uw persistentie account opnieuw wordt gegenereerd, moet u de gewenste sleutel opnieuw configureren in de vervolg keuzelijst **opslag sleutel** . | 

    Wanneer AOF-persistentie is ingeschakeld, worden schrijf bewerkingen naar de cache opgeslagen in het toegewezen opslag account (of de accounts als u een tweede opslag account hebt geconfigureerd). In het geval van een onherstelbare fout die zowel de primaire als de replica cache inneemt, wordt het opgeslagen AOF-logboek gebruikt om de cache opnieuw op te bouwen.

10. Selecteer het tabblad **Volgende: Tags** of klik op de knop **Volgende: Tags** onderaan de pagina.

11. Voer desgewenst in het tabblad **Tags** de naam en waarde in om de resource te categoriseren. 

12. Selecteer **Controleren + maken**. Het tabblad Beoordelen + maken wordt weergegeven, waar uw configuratie wordt gevalideerd in Azure.

13. Selecteer **Maken** nadat het groene bericht Validatie geslaagd verschijnt.

Het duurt even voor de cache is gemaakt. U kunt de voortgang bekijken op de **overzichtspagina** van Azure Cache voor Redis. Als u bij **Status** **Wordt uitgevoerd** ziet staan, kunt u de cache gebruiken. 

## <a name="persistence-faq"></a>Veelgestelde vragen over persistentie
De volgende lijst bevat antwoorden op veelgestelde vragen over Azure cache voor redis persistentie.

* [Kan ik persistentie inschakelen voor een eerder gemaakte cache?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Kan ik AOF en RDB-persistentie op hetzelfde moment inschakelen?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Welk persistentie model moet ik kiezen?](#which-persistence-model-should-i-choose)
* [Wat gebeurt er als u een andere grootte hebt geschaald en een back-up wordt hersteld die is gemaakt vóór de schaal bewerking?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)
* [Kan ik hetzelfde opslag account gebruiken voor persistentie over twee verschillende caches?](#can-i-use-the-same-storage-account-for-persistence-across-two-different-caches)
* [Worden er kosten in rekening gebracht voor de opslag die wordt gebruikt in gegevens persistentie](#will-i-be-charged-for-the-storage-being-used-in-data-persistence)

### <a name="rdb-persistence"></a>RDB-persistentie
* [Kan ik de frequentie van de RDB-back-up wijzigen nadat ik de cache heb gemaakt?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Waarom is er meer dan 60 minuten tussen back-ups als ik een RDB-back-upfrequentie van 60 minuten heb?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [Wat gebeurt er met de oude RDB-back-ups wanneer een nieuwe back-up wordt gemaakt?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>AOF-persistentie
* [Wanneer moet ik een tweede opslag account gebruiken?](#when-should-i-use-a-second-storage-account)
* [Is AOF persistentie van invloed op de gehele, latentie of prestaties van mijn cache?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Hoe kan ik het tweede opslag account verwijderen?](#how-can-i-remove-the-second-storage-account)
* [Wat is een herschrijf bewerking en wat is van invloed op mijn cache?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [Wat moet ik verwachten bij het schalen van een cache met AOF ingeschakeld?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Hoe worden mijn AOF-gegevens ingedeeld in opslag?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Kan ik persistentie inschakelen voor een eerder gemaakte cache?
Ja, redis persistentie kan worden geconfigureerd bij het maken van de cache en op bestaande Premium-caches.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>Kan ik AOF en RDB-persistentie op hetzelfde moment inschakelen?

Nee, u kunt alleen RDB of AOF inschakelen, maar niet beide tegelijk.

### <a name="which-persistence-model-should-i-choose"></a>Welk persistentie model moet ik kiezen?

AOF-persistentie slaat elke schrijf bewerking op in een logboek, wat invloed heeft op de door Voer, vergeleken met RDB-persistentie, waarmee back-ups worden opgeslagen op basis van het geconfigureerde back-upinterval, met minimale gevolgen voor de prestaties. Kies AOF-persistentie als uw primaire doel is om gegevens verlies te minimaliseren en u een afname van de door Voer voor uw cache kunt afnemen. Kies RDB-persistentie als u een optimale door Voer voor uw cache wilt behouden, maar toch een mechanisme wilt voor het herstellen van gegevens.

* Meer informatie over de [voor](https://redis.io/topics/persistence#rdb-advantages) -en [nadelen](https://redis.io/topics/persistence#rdb-disadvantages) van RDB-persistentie.
* Meer informatie over de [voor](https://redis.io/topics/persistence#aof-advantages) -en [nadelen](https://redis.io/topics/persistence#aof-disadvantages) van AOF persistentie.

Zie voor meer informatie over prestaties bij het gebruik van AOF-persistentie [de AOF persistentie is van invloed op de hele, latentie of prestaties van mijn cache?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Wat gebeurt er als u een andere grootte hebt geschaald en een back-up wordt hersteld die is gemaakt vóór de schaal bewerking?

Voor zowel RDB-als AOF-persistentie:

* Als u naar een grotere grootte hebt geschaald, is er geen impact.
* Als u naar een kleinere grootte hebt geschaald en u een aangepaste [Data Base](cache-configure.md#databases) -instelling hebt die groter is dan de limiet voor de [Data Base](cache-configure.md#databases) voor de nieuwe grootte, worden de gegevens in die data bases niet hersteld. Zie voor meer informatie [de instelling mijn aangepaste data bases wordt beïnvloed tijdens het schalen?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Als u naar een kleinere grootte hebt geschaald en er onvoldoende ruimte is in de kleinere grootte om alle gegevens van de laatste back-up te bewaren, worden de sleutels tijdens het herstel proces verwijderd, meestal met behulp van het verwijderings beleid voor [AllKeys-LRU](https://redis.io/topics/lru-cache) .

### <a name="can-i-use-the-same-storage-account-for-persistence-across-two-different-caches"></a>Kan ik hetzelfde opslag account gebruiken voor persistentie over twee verschillende caches?
Ja, u kunt hetzelfde opslag account gebruiken voor persistentie over twee verschillende caches

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Kan ik de frequentie van de RDB-back-up wijzigen nadat ik de cache heb gemaakt?
Ja, u kunt de back-upfrequentie voor RDB-persistentie op de Blade **gegevens persistentie** wijzigen. Zie redis-persistentie configureren voor instructies.

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Waarom is er meer dan 60 minuten tussen back-ups als ik een RDB-back-upfrequentie van 60 minuten heb?
Het interval voor de back-upfrequentie voor de RDB-persistentie wordt pas gestart als het vorige back-upproces is voltooid. Als de back-upfrequentie 60 minuten is en het back-upproces 15 minuten duurt, wordt de volgende back-up pas 75 minuten na de begin tijd van de vorige back-up gestart.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>Wat gebeurt er met de oude RDB-back-ups wanneer een nieuwe back-up wordt gemaakt?
Alle RDB-persistentie back-ups, met uitzonde ring van de meest recente, worden automatisch verwijderd. Deze verwijdering gebeurt mogelijk niet onmiddellijk, maar oudere back-ups worden niet voor onbepaalde tijd bewaard.


### <a name="when-should-i-use-a-second-storage-account"></a>Wanneer moet ik een tweede opslag account gebruiken?

U moet een tweede opslag account gebruiken voor AOF-persistentie wanneer u vermoedt dat u meer dan de verwachte set-bewerkingen op de cache hebt.  Het instellen van het secundaire opslag account helpt ervoor te zorgen dat uw cache geen limieten voor opslag bandbreedte bereikt.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>Is AOF persistentie van invloed op de gehele, latentie of prestaties van mijn cache?

AOF persistentie is van invloed op de door Voer met ongeveer 15% – 20% wanneer de cache lager is dan de maximale belasting (CPU en server belasting onder 90%). Er mogen geen latentie problemen optreden wanneer de cache binnen deze limieten valt. De cache zal echter eerder deze limieten bereiken met AOF ingeschakeld.

### <a name="how-can-i-remove-the-second-storage-account"></a>Hoe kan ik het tweede opslag account verwijderen?

U kunt het secundaire opslag account AOF Persistence verwijderen door het tweede opslag account in te stellen op hetzelfde als het eerste opslag account. Voor bestaande caches wordt de Blade **gegevens persistentie** geopend vanuit het **resource menu** voor uw cache. Als u AOF-persistentie wilt uitschakelen, klikt u op **uitgeschakeld**.

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>Wat is een herschrijf bewerking en wat is van invloed op mijn cache?

Wanneer het AOF-bestand groot genoeg wordt, wordt een herschrijf bewerking automatisch in de wachtrij geplaatst in de cache. Met de herschrijf bewerking wordt de grootte van het AOF-bestand aangepast met de minimale set bewerkingen die nodig zijn om de huidige gegevensset te maken. Tijdens het herschrijven verwachten de prestaties van de prestatie limieten eerder met name bij het omgaan met grote gegevens sets. Herschrijf bewerkingen worden minder vaak uitgevoerd, omdat het AOF-bestand groter wordt, maar een aanzienlijke hoeveelheid tijd in beslag neemt.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>Wat moet ik verwachten bij het schalen van een cache met AOF ingeschakeld?

Als het AOF-bestand op het moment van schalen aanzienlijk groot is, verwacht u dat de schaal bewerking langer duurt dan verwacht, omdat het bestand opnieuw wordt geladen nadat het schalen is voltooid.

Zie [Wat gebeurt er als ik een andere grootte heb geschaald en een back-up is hersteld die is gemaakt vóór de schaal bewerking?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation) voor meer informatie over schalen.

### <a name="how-is-my-aof-data-organized-in-storage"></a>Hoe worden mijn AOF-gegevens ingedeeld in opslag?

Gegevens die zijn opgeslagen in AOF-bestanden, worden onderverdeeld in meerdere pagina-blobs per knoop punt om de prestaties van het opslaan van de gegevens in de opslag te verbeteren. In de volgende tabel ziet u hoeveel pagina-blobs voor elke prijs categorie worden gebruikt:

| Premium-laag | Blobs |
|--------------|-------|
| P1           | 4 per Shard    |
| P2           | 8 per Shard    |
| P3           | 16 per Shard   |
| P4           | 20 per Shard   |

Wanneer Clustering is ingeschakeld, heeft elke Shard in de cache een eigen set pagina-blobs, zoals aangegeven in de vorige tabel. Een P2-cache met drie Shards verdeelt bijvoorbeeld het AOF-bestand over 24 pagina-blobs (8 blobs per Shard, met 3 Shards).

Na het opnieuw schrijven bestaan er twee sets met AOF-bestanden in de opslag. Herschrijf bewerkingen worden uitgevoerd op de achtergrond en worden toegevoegd aan de eerste set bestanden, terwijl tijdens het toevoegen van de herschrijf bewerking worden verzonden naar de cache. Een back-up wordt tijdelijk opgeslagen tijdens het herschrijven in geval van een storing, maar wordt onmiddellijk verwijderd nadat een herschrijf bewerking is voltooid.

### <a name="will-i-be-charged-for-the-storage-being-used-in-data-persistence"></a>Worden er kosten in rekening gebracht voor de opslag die wordt gebruikt voor gegevens persistentie?

Ja, er worden kosten in rekening gebracht voor de opslag die wordt gebruikt volgens het prijs model van het gebruikte opslag account.


## <a name="next-steps"></a>Volgende stappen
Meer informatie over Azure cache voor redis-functies.

* [Azure-cache voor redis Premium-Service lagen](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png

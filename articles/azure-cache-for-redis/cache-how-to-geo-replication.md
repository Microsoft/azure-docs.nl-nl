---
title: Geo-replicatie configureren voor Premium Azure Cache voor Redis-instanties
description: Meer informatie over het repliceren van uw Azure Cache voor Redis Premium-exemplaren tussen Azure-regio's
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: 0be2bb59b46dc827001d89f8e0f1be23f35a714d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536092"
---
# <a name="configure-geo-replication-for-premium-azure-cache-for-redis-instances"></a>Geo-replicatie configureren voor Premium Azure Cache voor Redis-instanties

In dit artikel leert u hoe u een Geo-Gerepliceerde Azure Cache configureert met behulp van de Azure Portal.

Geo-replicatie koppelt twee Premium Azure Cache voor Redis-exemplaren en maakt een relatie voor gegevensreplicatie. Deze cache-exemplaren bevinden zich meestal in verschillende Azure-regio's, maar dit is niet vereist. Het ene exemplaar fungeert als de primaire en de andere als de secundaire instantie. De primaire verwerkt lees- en schrijfaanvragen en geeft wijzigingen door aan de secundaire. Dit proces gaat door totdat de koppeling tussen de twee exemplaren wordt verwijderd.

> [!NOTE]
> Geo-replicatie is ontworpen als een oplossing voor herstel na noodherstel.
>
>

## <a name="geo-replication-prerequisites"></a>Vereisten voor geo-replicatie

Als u geo-replicatie tussen twee caches wilt configureren, moet aan de volgende vereisten worden voldaan:

- Beide caches zijn [Premium-caches.](cache-overview.md#service-tiers)
- Beide caches maken gebruik van hetzelfde Azure-abonnement.
- De secundaire gekoppelde cache heeft dezelfde cachegrootte of een grotere cachegrootte dan de primaire gekoppelde cache.
- Beide caches worden gemaakt en worden uitgevoerd.

Sommige functies worden niet ondersteund met geo-replicatie:

- Persistentie wordt niet ondersteund met geo-replicatie.
- Clustering wordt ondersteund als voor beide caches clustering is ingeschakeld en hetzelfde aantal shards is ingeschakeld.
- Caches in hetzelfde VNET worden ondersteund.
- Caches in verschillende VT's worden ondersteund met waarschuwingen. Zie [Kan ik geo-replicatie gebruiken met mijn caches in een VNET?](#can-i-use-geo-replication-with-my-caches-in-a-vnet) voor meer informatie.

Nadat geo-replicatie is geconfigureerd, gelden de volgende beperkingen voor uw gekoppelde cachepaar:

- De secundaire gekoppelde cache is alleen-lezen; U kunt er wel van lezen, maar u kunt er geen gegevens naar schrijven. Als u ervoor kiest om het Geo-Secondary-exemplaar te lezen, is het belangrijk om te weten dat wanneer er een volledige gegevenssynchronisatie tussen de Geo-Primary en de Geo-Secondary (gebeurt wanneer Geo-Primary of Geo-Secondary wordt bijgewerkt en bij sommige scenario's voor opnieuw opstarten ook), het Geo-Secondary-exemplaar erorrs (wordt aangegeven dat er een volledige gegevenssynchronisatie wordt uitgevoerd) op een Redis-bewerking wordt uitgevoerd totdat de volledige gegevenssynchronisatie tussen Geo-Primary en Geo-Secondary is voltooid. Toepassingen die lezen Geo-Seocndary moeten worden gebouwd om terug te vallen op de Geo-Primary wanneer de Geo-Seocndary dergelijke fouten heeft. 
- Alle gegevens die zich in de secundaire gekoppelde cache stonden voordat de koppeling werd toegevoegd, worden verwijderd. Als de geo-replicatie later echter wordt verwijderd, blijven de gerepliceerde gegevens in de secundaire gekoppelde cache.
- U kunt geen van [beide](cache-how-to-scale.md) caches schalen terwijl de caches zijn gekoppeld.
- U kunt het [aantal shards niet wijzigen](cache-how-to-premium-clustering.md) als clustering is ingeschakeld voor de cache.
- U kunt geen persistentie inschakelen voor beide caches.
- U kunt [exporteren](cache-how-to-import-export-data.md#export) vanuit beide caches.
- U kunt niet importeren [in de](cache-how-to-import-export-data.md#import) secundaire gekoppelde cache.
- U kunt de gekoppelde cache of de resourcegroep die deze bevat pas verwijderen als u de caches ontkoppelt. Zie Waarom is de bewerking mislukt wanneer ik heb geprobeerd mijn [gekoppelde cache te verwijderen?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache) voor meer informatie.
- Als de caches zich in verschillende regio's hebben, zijn de kosten voor netwerk-egress van toepassing op de gegevens die tussen regio's worden verplaatst. Zie Hoeveel kost het om mijn gegevens te repliceren tussen [Azure-regio's? voor meer informatie.](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Automatische failover vindt niet plaats tussen de primaire en secundaire gekoppelde cache. Zie Hoe werkt een failover naar de secundaire gekoppelde cache? voor meer informatie en informatie over het maken van een failover van een [clienttoepassing.](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Een geo-replicatiekoppeling toevoegen

1. Als u twee caches wilt koppelen voor geo-replicatie, klikt u op **Geo-replicatie** in het menu Resource van de cache die u de primaire gekoppelde cache wilt zijn. Klik vervolgens op **de koppeling Cachereplicatie toevoegen** op de blade **Geo-replicatie.**

    ![Koppeling toevoegen](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Klik in de lijst Compatibele caches op de naam van de beoogde **secundaire cache.** Als uw secundaire cache niet wordt weergegeven in de lijst, controleert u of aan de vereisten voor [geo-replicatie](#geo-replication-prerequisites) voor de secundaire cache is voldaan. Als u de caches wilt filteren op regio, klikt u op de regio op de kaart om alleen die caches weer te geven in de lijst **Compatibele caches.**

    ![Met geo-replicatie compatibele caches](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    U kunt ook het koppelingsproces starten of details over de secundaire cache weergeven met behulp van het contextmenu.

    ![Contextmenu voor geo-replicatie](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Klik **op Koppeling** om de twee caches aan elkaar te koppelen en het replicatieproces te starten.

    ![Koppelingscaches](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. U kunt de voortgang van het replicatieproces bekijken op de blade **Geo-replicatie.**

    ![Koppelingsstatus](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    U kunt ook de koppelingsstatus bekijken op **de** blade Overzicht voor zowel de primaire als de secundaire cache.

    ![Schermopname die laat zien hoe u de koppelingsstatus voor de primaire en secundaire cache kunt weergeven.](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Zodra het replicatieproces is voltooid, verandert **de koppelingsstatus** in **Geslaagd.**

    ![Cachestatus](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    De primaire gekoppelde cache blijft beschikbaar voor gebruik tijdens het koppelingsproces. De secundaire gekoppelde cache is pas beschikbaar als het koppelingsproces is voltooid.

## <a name="remove-a-geo-replication-link"></a>Een geo-replicatiekoppeling verwijderen

1. Als u de koppeling tussen twee caches wilt verwijderen en geo-replicatie wilt stoppen, klikt u op **Caches** ontkoppelen op de blade **Geo-replicatie.**
    
    ![Caches ontkoppelen](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Wanneer het ontkoppelingsproces is voltooid, is de secundaire cache beschikbaar voor zowel lees- als schrijfproces.

>[!NOTE]
>Wanneer de geo-replicatiekoppeling wordt verwijderd, blijven de gerepliceerde gegevens uit de primaire gekoppelde cache in de secundaire cache.
>
>

## <a name="geo-replication-faq"></a>Veelgestelde vragen over geo-replicatie

- [Kan ik geo-replicatie gebruiken met een Standard- of Basic-laagcache?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [Is mijn cache beschikbaar voor gebruik tijdens het koppelen of loskoppelen?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Kan ik meer dan twee caches aan elkaar koppelen?](#can-i-link-more-than-two-caches-together)
- [Kan ik twee caches uit verschillende Azure-abonnementen koppelen?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Kan ik twee caches met verschillende grootten koppelen?](#can-i-link-two-caches-with-different-sizes)
- [Kan ik geo-replicatie gebruiken met clustering ingeschakeld?](#can-i-use-geo-replication-with-clustering-enabled)
- [Kan ik geo-replicatie gebruiken met mijn caches in een VNET?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Wat is het replicatieschema voor geo-replicatie van Redis?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Hoe lang duurt geo-replicatiereplicatie?](#how-long-does-geo-replication-replication-take)
- [Wordt het replicatieherstelpunt gegarandeerd?](#is-the-replication-recovery-point-guaranteed)
- [Kan ik PowerShell of Azure CLI gebruiken om geo-replicatie te beheren?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Wat kost het om mijn gegevens te repliceren tussen Azure-regio's?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Waarom is de bewerking mislukt toen ik heb geprobeerd mijn gekoppelde cache te verwijderen?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Welke regio moet ik gebruiken voor mijn secundaire gekoppelde cache?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Hoe werkt een failing over naar de secundaire gekoppelde cache?](#how-does-failing-over-to-the-secondary-linked-cache-work)
- [Kan ik firewall configureren met geo-replicatie?](#can-i-configure-a-firewall-with-geo-replication)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Kan ik geo-replicatie gebruiken met een Standard- of Basic-cache?

Nee, geo-replicatie is alleen beschikbaar voor Premium-caches.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>Is mijn cache beschikbaar voor gebruik tijdens het koppelen of loskoppelen?

- Bij het koppelen blijft de primaire gekoppelde cache beschikbaar terwijl het koppelingsproces is voltooid.
- Bij het koppelen is de secundaire gekoppelde cache pas beschikbaar als het koppelingsproces is voltooid.
- Bij het ontkoppelen blijven beide caches beschikbaar terwijl het ontkoppelingsproces is voltooid.

### <a name="can-i-link-more-than-two-caches-together"></a>Kan ik meer dan twee caches aan elkaar koppelen?

Nee, u kunt slechts twee caches aan elkaar koppelen.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Kan ik twee caches uit verschillende Azure-abonnementen koppelen?

Nee, beide caches moeten zich in hetzelfde Azure-abonnement.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Kan ik twee caches met verschillende grootten koppelen?

Ja, zolang de secundaire gekoppelde cache groter is dan de primaire gekoppelde cache.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Kan ik geo-replicatie gebruiken met ingeschakelde clustering?

Ja, zolang beide caches hetzelfde aantal shards hebben.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Kan ik geo-replicatie gebruiken met mijn caches in een VNET?

Ja, geo-replicatie van caches in VTE's wordt ondersteund met waarschuwingen:

- Geo-replicatie tussen caches in hetzelfde VNET wordt ondersteund.
- Geo-replicatie tussen caches in verschillende VTE's wordt ook ondersteund.
  - Als de VNET's zich in dezelfde regio, kunt u ze verbinden met behulp van [VNET-peering](../virtual-network/virtual-network-peering-overview.md) of een [VPN Gateway VNET-naar-VNET-verbinding.](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
  - Als de VNET's zich in verschillende regio's, geo-replicatie met behulp van VNET-peering wordt ondersteund, maar een client-VM in VNET 1 (regio 1) heeft geen toegang tot de cache in VNET 2 (regio 2) via de DNS-naam vanwege een beperking met interne Basic load balancers. Zie Virtual Network - Peering - Vereisten en beperkingen voor meer informatie over beperkingen [voor VNET-peering.](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) De aanbevolen oplossing is het gebruik van een VPN Gateway VNET-naar-VNET-verbinding.
  
Met behulp van deze [Azure-sjabloon](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/)kunt u snel twee geo-gerepliceerde caches implementeren in een VNET dat is verbonden met een VPN Gateway VNET-naar-VNET-verbinding.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Wat is het replicatieschema voor geo-replicatie van Redis?

Replicatie is doorlopend en asynchroon en gebeurt niet volgens een specifiek schema. Alle schrijfberichten die naar de primaire replica worden uitgevoerd, worden onmiddellijk en asynchroon gerepliceerd op de secundaire replica.

### <a name="how-long-does-geo-replication-replication-take"></a>Hoe lang duurt geo-replicatiereplicatie?

Replicatie is incrementeel, asynchroon en continu en de tijd die nodig is, verschilt niet veel van de latentie tussen regio's. Onder bepaalde omstandigheden kan het nodig zijn dat de secundaire cache een volledige synchronisatie van de gegevens van de primaire cache heeft. De replicatietijd is in dit geval afhankelijk van een aantal factoren, zoals: belasting van de primaire cache, beschikbare netwerkbandbreedte en latentie tussen regio's. We hebben geconstateerd dat de replicatietijd voor een volledig geo-gerepliceerd paar van 53 GB tussen de 5 en 10 minuten kan zijn.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Wordt het replicatieherstelpunt gegarandeerd?

Voor caches in een geo-gerepliceerde modus is persistentie uitgeschakeld. Als een geo-gerepliceerd paar is losgekoppeld, zoals een door de klant geïnitieerde failover, worden de gesynchroniseerde gegevens tot dat tijdstip opgeslagen in de secundaire gekoppelde cache. In dergelijke situaties is er geen herstelpunt gegarandeerd.

Als u een herstelpunt wilt verkrijgen, [exporteert u](cache-how-to-import-export-data.md#export) vanuit een van beide caches. U kunt later [importeren in](cache-how-to-import-export-data.md#import) de primaire gekoppelde cache.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Kan ik PowerShell of Azure CLI gebruiken om geo-replicatie te beheren?

Ja, geo-replicatie kan worden beheerd met de Azure Portal, PowerShell of Azure CLI. Zie de [PowerShell-documenten](/powershell/module/az.rediscache/#redis_cache) of Azure CLI-documenten voor [meer informatie.](/cli/azure/redis/server-link)

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Wat kost het om mijn gegevens te repliceren tussen Azure-regio's?

Wanneer u geo-replicatie gebruikt, worden gegevens uit de primaire gekoppelde cache gerepliceerd naar de secundaire gekoppelde cache. Er worden geen kosten in rekening gebracht voor de gegevensoverdracht als de twee gekoppelde caches zich in dezelfde regio hebben. Als de twee gekoppelde caches zich in verschillende regio's hebben, zijn de kosten voor gegevensoverdracht de kosten voor het uit het netwerk verwijderen van gegevens die tussen beide regio's worden verplaatst. Zie Prijsinformatie voor bandbreedte [voor meer informatie.](https://azure.microsoft.com/pricing/details/bandwidth/)

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Waarom is de bewerking mislukt toen ik probeerde mijn gekoppelde cache te verwijderen?

Geo-gerepliceerde caches en hun resourcegroepen kunnen pas worden verwijderd als u de geo-replicatiekoppeling verwijdert. Als u probeert de resourcegroep te verwijderen die een of beide gekoppelde caches bevat, worden de andere resources in de resourcegroep verwijderd, maar blijft de resourcegroep in de status en blijven alle gekoppelde caches in de resourcegroep in de `deleting` `running` status. Als u de resourcegroep en de gekoppelde caches daarin volledig wilt verwijderen, ontkoppelt u de caches zoals beschreven in [Een geo-replicatiekoppeling verwijderen.](#remove-a-geo-replication-link)

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Welke regio moet ik gebruiken voor mijn secundaire gekoppelde cache?

Over het algemeen is het raadzaam dat uw cache zich in dezelfde Azure-regio belandt als de toepassing die toegang tot de cache heeft. Voor toepassingen met afzonderlijke primaire en terugvalregio's is het raadzaam om uw primaire en secundaire caches in dezelfde regio's te gebruiken. Zie Best Practices - Gekoppelde Azure-regio's voor meer informatie [over gekoppelde regio's.](../best-practices-availability-paired-regions.md)

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Hoe werkt een failing over naar de secundaire gekoppelde cache?

Automatische failover tussen Azure-regio's wordt niet ondersteund voor geo-gerepliceerde caches. In een noodherstelscenario moeten klanten de volledige toepassingsstack op een gecoördineerde manier in hun back-upregio gebruiken. Afzonderlijke toepassingsonderdelen laten bepalen wanneer ze zelf moeten overschakelen naar hun back-ups, kunnen een negatieve invloed hebben op de prestaties. Een van de belangrijkste voordelen van Redis is dat het een opslag met een zeer lage latentie is. Als de hoofdtoepassing van de klant zich in een andere regio dan de cache, zou de toegevoegde retourtijd een merkbare invloed hebben op de prestaties. Daarom wordt voorkomen dat er automatisch een fout wordt overgenomen vanwege tijdelijke beschikbaarheidsproblemen.

Als u een door de klant geïnitieerde failover wilt starten, moet u eerst de caches ontkoppelen. Wijzig vervolgens uw Redis-client om het verbindings-eindpunt van de (voorheen gekoppelde) secundaire cache te gebruiken. Wanneer de twee caches zijn losgekoppeld, wordt de secundaire cache weer een gewone cache voor lezen/schrijven en worden aanvragen rechtstreeks van Redis-clients geaccepteerd.

### <a name="can-i-configure-a-firewall-with-geo-replication"></a>Kan ik een firewall configureren met geo-replicatie?

Ja, u kunt een [firewall configureren](./cache-configure.md#firewall) met geo-replicatie. Geo-replicatie werkt alleen naast een firewall als het IP-adres van de secundaire cache is toegevoegd aan de firewallregels van de primaire cache.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure Cache voor Redis functies.

* [Azure Cache voor Redis servicelagen](cache-overview.md#service-tiers)
* [Hoge beschikbaarheid voor Azure Cache voor Redis](cache-high-availability.md)

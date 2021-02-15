---
title: Geo-replicatie voor Premium Azure cache configureren voor redis-instanties
description: Meer informatie over het repliceren van uw Azure-cache voor redis Premium-instanties in azure-regio's
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: 27ccc81ddf0a771de9fb15f60820dfd3efa6146e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386869"
---
# <a name="configure-geo-replication-for-premium-azure-cache-for-redis-instances"></a>Geo-replicatie voor Premium Azure cache configureren voor redis-instanties

In dit artikel leert u hoe u een geo-gerepliceerde Azure-cache kunt configureren met behulp van de Azure Portal.

Geo-replicatie koppelingen combi neren twee Premium Azure-cache voor redis-instanties en maakt een gegevens replicatie relatie. Deze cache-instanties bevinden zich doorgaans in verschillende Azure-regio's, maar ze zijn niet vereist voor. De ene instantie fungeert als de primaire en de andere als de secundaire. De primaire ingangen hebben lees-en schrijf aanvragen en wijzigingen worden door gegeven aan de secundaire. Dit proces wordt voortgezet totdat de koppeling tussen de twee exemplaren wordt verwijderd.

> [!NOTE]
> Geo-replicatie is ontworpen als oplossing voor herstel na nood gevallen.
>
>

## <a name="geo-replication-prerequisites"></a>Vereisten voor geo-replicatie

Als u geo-replicatie tussen twee caches wilt configureren, moet aan de volgende vereisten worden voldaan:

- Beide caches zijn [Premium-laag](cache-overview.md#service-tiers) caches.
- Beide caches bevinden zich in hetzelfde Azure-abonnement.
- De secundaire gekoppelde cache heeft dezelfde cache grootte of een grotere cache grootte dan de primaire gekoppelde cache.
- Beide caches worden gemaakt en de status wordt uitgevoerd.

Sommige functies worden niet ondersteund met geo-replicatie:

- Persistentie wordt niet ondersteund met geo-replicatie.
- Clustering wordt ondersteund als Clustering is ingeschakeld voor beide caches en hetzelfde aantal Shards.
- Caches in hetzelfde VNET worden ondersteund.
- Caches in verschillende VNETs worden ondersteund met aanvullende opmerkingen. Zie [kan ik geo-replicatie gebruiken met mijn caches in een VNET?](#can-i-use-geo-replication-with-my-caches-in-a-vnet) voor meer informatie.

Nadat geo-replicatie is geconfigureerd, zijn de volgende beperkingen van toepassing op uw gekoppelde cache paar:

- De secundaire gekoppelde cache heeft het kenmerk alleen-lezen. u kunt de gegevens lezen, maar niet naar een wille keurige data schrijven. 
- Gegevens die zich in de secundaire gekoppelde cache bevonden voordat de koppeling werd toegevoegd, worden verwijderd. Als de geo-replicatie later echter wordt verwijderd, blijven de gerepliceerde gegevens in de secundaire gekoppelde cache.
- U kunt geen cache [schalen](cache-how-to-scale.md) terwijl de caches zijn gekoppeld.
- U kunt [het aantal Shards niet wijzigen](cache-how-to-premium-clustering.md) als Clustering is ingeschakeld voor de cache.
- U kunt persistentie niet inschakelen voor beide caches.
- U kunt vanuit beide caches [exporteren](cache-how-to-import-export-data.md#export) .
- U kunt niet [importeren](cache-how-to-import-export-data.md#import) in de secundaire gekoppelde cache.
- U kunt geen gekoppelde cache of de resource groep die deze bevat, verwijderen totdat u de koppeling tussen de caches ongedaan maakt. Zie [Waarom is de bewerking mislukt wanneer ik mijn gekoppelde cache probeerde te verwijderen?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache) voor meer informatie.
- Als de caches zich in verschillende regio's bevinden, zijn de kosten voor het uitvallen van het netwerk van toepassing op de gegevens die worden verplaatst tussen regio's. Zie [hoeveel kost het om mijn gegevens te repliceren tussen Azure-regio's?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions) voor meer informatie.
- Er wordt geen automatische failover uitgevoerd tussen de primaire en de secundaire gekoppelde cache. Voor meer informatie en informatie over het uitvoeren van een failover van een client toepassing raadpleegt u [Hoe werkt de secundaire gekoppelde cache niet?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Een geo-replicatie koppeling toevoegen

1. Als u twee caches voor geo-replicatie wilt koppelen, klikt u op **geo-replicatie** in het menu resource van de cache die u als primair gekoppelde cache wilt maken. Klik vervolgens op **koppeling voor cache toevoegen** van de Blade **geo-replicatie** .

    ![Koppeling toevoegen](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Klik op de naam van de gewenste secundaire cache in de lijst met **compatibele caches** . Als uw secundaire cache niet in de lijst wordt weer gegeven, controleert u of aan de [geo-replicatie vereisten](#geo-replication-prerequisites) voor de secundaire cache is voldaan. Als u de caches per regio wilt filteren, klikt u op de regio in de kaart om alleen de caches in de lijst met **compatibele caches** weer te geven.

    ![Caches die compatibel zijn met geo-replicatie](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    U kunt ook het koppelings proces starten of Details over de secundaire cache weer geven met behulp van het context menu.

    ![Context menu van geo-replicatie](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Klik op **koppeling** om de twee caches samen te koppelen en het replicatie proces te starten.

    ![Caches koppelen](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. U kunt de voortgang van het replicatie proces bekijken op de Blade **geo-replicatie** .

    ![Koppelings status](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    U kunt ook de koppelings status weer geven op de Blade **overzicht** voor de primaire en secundaire caches.

    ![Scherm opname van het weer geven van de koppelings status voor de primaire en secundaire caches.](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Zodra het replicatie proces is voltooid, verandert de status van de **koppeling** in **geslaagd**.

    ![Cache status](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    De primaire gekoppelde cache blijft beschikbaar voor gebruik tijdens het koppelings proces. De secundaire gekoppelde cache is niet beschikbaar totdat het koppelings proces is voltooid.

## <a name="remove-a-geo-replication-link"></a>Een geo-replicatie koppeling verwijderen

1. Als u de koppeling tussen twee caches wilt verwijderen en geo-replicatie wilt stoppen, klikt u op **caches ontkoppelen** van de Blade **geo-replicatie** .
    
    ![Caches ontkoppelen](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Wanneer het ontkoppelings proces is voltooid, is de secundaire cache beschikbaar voor zowel lees-als schrijf bewerkingen.

>[!NOTE]
>Wanneer de koppeling geo-replicatie wordt verwijderd, blijven de gerepliceerde gegevens van de primaire gekoppelde cache in de secundaire cache.
>
>

## <a name="geo-replication-faq"></a>Veelgestelde vragen over geo-replicatie

- [Kan ik geo-replicatie met een Standard-of Basic-laag cache gebruiken?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [Is mijn cache beschikbaar voor gebruik tijdens het koppelen of ontkoppelen van het proces?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Kan ik meer dan twee caches tegelijk koppelen?](#can-i-link-more-than-two-caches-together)
- [Kan ik twee caches van verschillende Azure-abonnementen koppelen?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Kan ik twee caches met verschillende grootten koppelen?](#can-i-link-two-caches-with-different-sizes)
- [Kan ik geo-replicatie gebruiken als Clustering is ingeschakeld?](#can-i-use-geo-replication-with-clustering-enabled)
- [Kan ik geo-replicatie gebruiken met mijn caches in een VNET?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Wat is het replicatie schema voor redis geo-replicatie?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Hoe lang duurt het repliceren van geo-replicatie?](#how-long-does-geo-replication-replication-take)
- [Is het replicatie herstel punt gegarandeerd?](#is-the-replication-recovery-point-guaranteed)
- [Kan ik Power shell of Azure CLI gebruiken voor het beheren van geo-replicatie?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Wat kost het om mijn gegevens te repliceren tussen Azure-regio's?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Waarom is de bewerking mislukt wanneer ik mijn gekoppelde cache probeerde te verwijderen?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Welke regio moet ik voor mijn secundaire gekoppelde cache gebruiken?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Hoe werkt het uitvoeren van een failover naar de secundaire gekoppelde cache?](#how-does-failing-over-to-the-secondary-linked-cache-work)
- [Kan ik Firewall configureren met geo-replicatie?](#can-i-configure-a-firewall-with-geo-replication)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Kan ik geo-replicatie met een Standard-of Basic-laag cache gebruiken?

Nee, geo-replicatie is alleen beschikbaar voor Premium-laag caches.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>Is mijn cache beschikbaar voor gebruik tijdens het koppelen of ontkoppelen van het proces?

- Wanneer u een koppeling maakt, blijft de primaire gekoppelde cache beschikbaar terwijl het koppelings proces is voltooid.
- Wanneer u een koppeling maakt, is de secundaire gekoppelde cache niet beschikbaar totdat het koppelings proces is voltooid.
- Wanneer het ontkoppelen is, blijven beide caches beschikbaar terwijl het ontkoppelen van het proces is voltooid.

### <a name="can-i-link-more-than-two-caches-together"></a>Kan ik meer dan twee caches tegelijk koppelen?

Nee, u kunt slechts twee caches tegelijk koppelen.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Kan ik twee caches van verschillende Azure-abonnementen koppelen?

Nee, beide caches moeten zich in hetzelfde Azure-abonnement bevinden.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Kan ik twee caches met verschillende grootten koppelen?

Ja, zolang de secundaire gekoppelde cache groter is dan de primaire gekoppelde cache.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Kan ik geo-replicatie gebruiken als Clustering is ingeschakeld?

Ja, zolang beide caches hetzelfde aantal Shards hebben.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Kan ik geo-replicatie gebruiken met mijn caches in een VNET?

Ja, geo-replicatie van caches in VNETs wordt ondersteund met aanvullende opmerkingen:

- Geo-replicatie tussen caches in hetzelfde VNET wordt ondersteund.
- Geo-replicatie tussen caches in verschillende VNETs wordt ook ondersteund.
  - Als de VNETs zich in dezelfde regio bevinden, kunt u deze verbinden met [vnet-peering](../virtual-network/virtual-network-peering-overview.md) of een [VPN gateway vnet-naar-vnet-verbinding](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
  - Als de VNETs zich in verschillende regio's bevinden, wordt geo-replicatie met behulp van VNET-peering ondersteund, maar een client-VM in VNET 1 (regio 1) heeft geen toegang tot de cache in VNET 2 (regio 2) via de DNS-naam vanwege een beperking met basis interne load balancers. Zie [Virtual Network-peering-vereisten en beperkingen](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)voor meer informatie over de beperkingen voor VNET-peering. De aanbevolen oplossing is een VPN Gateway VNET-naar-VNET-verbinding te gebruiken.
  
Met [deze Azure-sjabloon](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/)kunt u snel twee geo-gerepliceerde caches implementeren in een vnet dat is verbonden met een VPN gateway vnet-naar-vnet-verbinding.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Wat is het replicatie schema voor redis geo-replicatie?

Replicatie is doorlopend en asynchroon en gebeurt niet volgens een specifiek schema. Alle schrijf bewerkingen die naar de primaire worden uitgevoerd, worden onmiddellijk en asynchroon gerepliceerd op de secundaire.

### <a name="how-long-does-geo-replication-replication-take"></a>Hoe lang duurt het repliceren van geo-replicatie?

Replicatie is incrementeel, asynchroon en doorlopend en de gebruikte tijd is niet veel anders dan de latentie tussen de verschillende regio's. Onder bepaalde omstandigheden is de secundaire cache mogelijk vereist voor een volledige synchronisatie van de gegevens van de primaire. De replicatie tijd in dit geval is afhankelijk van het aantal factoren zoals: belasting voor de primaire cache, beschik bare netwerk bandbreedte en interregionale latentie. Er is een replicatie tijd gevonden voor een Maxi maal aantal van 53 GB gerepliceerde paar van Maxi maal vijf tot tien minuten.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Is het replicatie herstel punt gegarandeerd?

Voor caches in een geo-gerepliceerde modus is persistentie uitgeschakeld. Als een geo-gerepliceerd paar is ontkoppeld, zoals een door de klant geïnitieerde failover, blijven de gesynchroniseerde gegevens van de secundaire gekoppelde cache tot dat moment. In dergelijke situaties wordt er geen herstel punt gegarandeerd.

Als u een herstel punt wilt verkrijgen, moet u uit de cache [exporteren](cache-how-to-import-export-data.md#export) . U kunt later [importeren](cache-how-to-import-export-data.md#import) in de primaire gekoppelde cache.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Kan ik Power shell of Azure CLI gebruiken voor het beheren van geo-replicatie?

Ja, geo-replicatie kan worden beheerd met behulp van de Azure Portal, Power shell of Azure CLI. Zie [Power shell docs](/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache) of [Azure cli docs](/cli/azure/redis/server-link?view=azure-cli-latest)(Engelstalig) voor meer informatie.

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Wat kost het om mijn gegevens te repliceren tussen Azure-regio's?

Wanneer u geo-replicatie gebruikt, worden gegevens van de primaire gekoppelde cache gerepliceerd naar de secundaire gekoppelde cache. Er worden geen kosten in rekening gebracht voor de gegevens overdracht als de twee gekoppelde caches zich in dezelfde regio bevinden. Als de twee gekoppelde caches zich in verschillende regio's bevinden, zijn de kosten voor de overdracht van gegevens het netwerk voor uitgaand verkeer van gegevens die over een van beide regio's worden verplaatst. Zie [prijs informatie voor band breedte](https://azure.microsoft.com/pricing/details/bandwidth/)voor meer informatie.

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Waarom is de bewerking mislukt wanneer ik mijn gekoppelde cache probeerde te verwijderen?

Geo-gerepliceerde caches en hun resource groepen kunnen pas worden verwijderd wanneer u de koppeling geo-replicatie verwijdert. Als u probeert de resource groep te verwijderen die een of beide gekoppelde caches bevat, worden de andere resources in de resource groep verwijderd, maar de resource groep blijft in de `deleting` status en de gekoppelde caches in de resource groep blijven in de `running` status. Als u de resource groep en de gekoppelde caches erin volledig wilt verwijderen, ontkoppelt u de caches zoals beschreven in [een geo-replicatie koppeling verwijderen](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Welke regio moet ik voor mijn secundaire gekoppelde cache gebruiken?

Over het algemeen is het raadzaam om uw cache in dezelfde Azure-regio te bevinden als de toepassing die er toegang toe heeft. Voor toepassingen met afzonderlijke primaire en terugval regio's worden uw primaire en secundaire caches in dezelfde regio's aangeraden. Zie [Aanbevolen procedures-gekoppelde Azure-regio's](../best-practices-availability-paired-regions.md)voor meer informatie over gekoppelde regio's.

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Hoe werkt het uitvoeren van een failover naar de secundaire gekoppelde cache?

Automatische failover tussen Azure-regio's wordt niet ondersteund voor geo-gerepliceerde caches. In een scenario voor herstel na nood gevallen moeten klanten de volledige toepassings stack op een gecoördineerde manier in hun back-upregio weer geven. Door afzonderlijke toepassings onderdelen toe te staan, kan dit een negatieve invloed hebben op de prestaties om te scha kelen naar hun eigen back-ups. Een van de belangrijkste voor delen van redis is dat dit een zeer lage latentie heeft. Als de hoofd toepassing van de klant zich in een andere regio bevindt dan de cache, zou de toegevoegde tijd van de round trip een merkbaar effect hebben op de prestaties. Daarom voor komt u dat er automatisch een failover wordt uitgevoerd vanwege problemen met de tijdelijke Beschik baarheid.

Als u een door de klant geïnitieerde failover wilt starten, koppelt u de caches dan eerst. Wijzig vervolgens uw redis-client om het eind punt van de verbinding van de secundaire cache (voorheen gekoppeld) te gebruiken. Wanneer de twee caches zijn ontkoppeld, wordt de secundaire cache opnieuw een regel matige lees-/schrijftoegang en worden aanvragen rechtstreeks van redis-clients geaccepteerd.

### <a name="can-i-configure-a-firewall-with-geo-replication"></a>Kan ik een firewall met geo-replicatie configureren?

Ja, u kunt een [firewall](./cache-configure.md#firewall) met geo-replicatie configureren. Om geo-replicatie naast een firewall te laten werken, moet u ervoor zorgen dat het IP-adres van de secundaire cache wordt toegevoegd aan de firewall regels van de primaire cache.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure cache voor redis-functies.

* [Azure-cache voor redis-service lagen](cache-overview.md#service-tiers)
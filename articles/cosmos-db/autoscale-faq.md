---
title: Veelgestelde vragen over het automatisch schalen van ingerichte door Voer in Azure Cosmos DB
description: Veelgestelde vragen over het automatisch schalen van ingerichte door Voer voor Azure Cosmos DB data bases en containers
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/11/2020
ms.openlocfilehash: 31b96f03a8519b068eaa816443be0a0f374a4a8c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98247026"
---
# <a name="frequently-asked-questions-about-autoscale-provisioned-throughput-in-azure-cosmos-db"></a>Veelgestelde vragen over het automatisch schalen van ingerichte door Voer in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Met de ingerichte door Voer voor automatisch schalen beheert en schaalt Azure Cosmos DB de RU/s van uw data base of container, op basis van het gebruik. In dit artikel vindt u antwoorden op veelgestelde vragen over automatisch schalen.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="what-is-the-difference-between-autopilot-and-autoscale-in-azure-cosmos-db"></a>Wat is het verschil tussen "Auto Pilot" en "automatisch schalen" in Azure Cosmos DB?
"Automatisch schalen" of "door Voer ingericht voor automatisch schalen" is de bijgewerkte naam voor de functie, voorheen bekend als "Auto Pilot". Met de huidige versie van automatisch schalen hebben we nieuwe functies toegevoegd, inclusief de mogelijkheid om aangepaste Max. RU/s en programmatische ondersteuning in te stellen. 

### <a name="what-happens-to-databases-or-containers-created-in-the-previous-autopilot-tier-model"></a>Wat gebeurt er met data bases of containers die zijn gemaakt in het vorige model van automatische test fase?
Resources die zijn gemaakt met het vorige laag model worden automatisch ondersteund met het nieuwe aangepaste maximum RU/s-model voor automatisch schalen. De bovengrens van de laag wordt het nieuwe maximum aantal RU/s, wat resulteert in hetzelfde schaal bereik. 

Als u bijvoorbeeld eerder de laag hebt geselecteerd die tussen 400 en 4000 RU/s is geschaald, wordt de data base of container nu weer gegeven als een maximum van RU/s 4000 RU/s, waardoor de schaal tussen 400 en 4000 RU/s. Hier kunt u het maximum aantal RU/s wijzigen in een aangepaste waarde om uw werk belasting aan te passen. 

### <a name="how-quickly-will-autoscale-scale-up-based-on-spikes-in-traffic"></a>Hoe snel wordt de schaal automatisch aangepast op basis van pieken in verkeer?
Met automatisch schalen wordt de door Voer (RU/s) `T` omhoog of omlaag geschaald binnen het `0.1 * Tmax` bereik en op `Tmax` basis van binnenkomend verkeer. Omdat de schaal automatisch en onmiddellijk kan worden gebruikt, kunt u op elk moment tot aan het inrichten zonder `Tmax` vertraging. 

### <a name="how-do-i-determine-what-rus-the-system-is-currently-scaled-to"></a>Hoe kan ik bepalen van welke RU/s het systeem momenteel is geschaald?
Gebruik [Azure monitor metrische gegevens](how-to-choose-offer.md#measure-and-monitor-your-usage) voor het bewaken van zowel het ingerichte maximum aantal ru/s als de huidige door Voer (ru/s) waarop het systeem is geschaald. 

### <a name="what-is-the-pricing-for-autoscale"></a>Wat zijn de prijzen voor automatisch schalen?
Elk uur wordt u gefactureerd voor de hoogste door Voer `T` die het systeem binnen het uur is geschaald. Als uw resource gedurende het uur geen aanvragen heeft of niet verder is geschaald `0.1 * Tmax` , wordt u gefactureerd voor het minimum van `0.1 * Tmax` . Raadpleeg de pagina met [prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/) voor Azure Cosmos DB voor meer informatie. 

### <a name="how-does-autoscale-show-up-on-my-bill"></a>Hoe wordt automatisch schalen weer gegeven op mijn factuur?
In de accounts voor enkele schrijf regio's is het automatisch schalen per 100 RU/s 1,5 x het gemiddelde van de ingerichte door Voer (hand matige) standaard. Op uw factuur ziet u de bestaande standaard doorvoer meter voor ingerichte door voer. De hoeveelheid van deze meter wordt vermenigvuldigd met 1,5. Als bijvoorbeeld de hoogste RU/s voor het systeem is geschaald naar binnen een uur 6000 RU/s, wordt u 60 * 1,5 = 90 eenheden van de meter voor dat uur gefactureerd.

In accounts met meerdere schrijf regio's is het automatisch schalen per 100 RU/s hetzelfde als het gemiddelde voor de Provisioning van de standaard (hand matig) beschik bare meerdere schrijf regio's. Op uw factuur ziet u de bestaande meter meerdere schrijf regio's. Aangezien de tarieven hetzelfde zijn, ziet u als u automatisch schalen gebruikt, dezelfde hoeveelheid als bij standaard doorvoer.

### <a name="does-autoscale-work-with-reserved-capacity"></a>Werkt automatisch schalen met gereserveerde capaciteit?
Ja. Wanneer u gereserveerde capaciteit aanschaft voor accounts met enkelvoudige schrijf regio's, wordt de reserverings korting voor resources automatisch schalen toegepast op het gebruik van de meter tegen een verhouding van 1,5 * de [verhouding van de specifieke regio](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md#reservation-discount-per-region). 

Gereserveerde capaciteit van regio voor meerdere schrijf bewerkingen werkt hetzelfde voor de ingerichte door Voer van automatisch schalen en standaard (hand matig). Zie [Azure Cosmos DB gereserveerde capaciteit](cosmos-db-reserved-capacity.md)

### <a name="does-autoscale-work-with-free-tier"></a>Werkt automatisch schalen met de gratis laag?
Ja. In de laag gratis kunt u de door Voer van automatisch schalen gebruiken voor een container. Ondersteuning voor het automatisch schalen van gedeelde doorvoer databases met aangepaste Max. RU/s is nog niet beschikbaar. Bekijk hoe [Facturering met gratis lagen werkt met automatisch schalen](understand-your-bill.md#azure-free-tier).

### <a name="is-autoscale-supported-for-all-apis"></a>Wordt automatisch schalen ondersteund voor alle Api's?
Ja, automatisch schalen wordt ondersteund voor alle Api's: core (SQL), Gremlin, Table, Cassandra en API voor MongoDB.

### <a name="is-autoscale-supported-for-multi-region-write-accounts"></a>Wordt automatisch schalen ondersteund voor het schrijven van meerdere regio's?
Ja. Het maximum aantal RU/s is beschikbaar in elke regio die wordt toegevoegd aan het Azure Cosmos DB-account. 

### <a name="how-do-i-enable-autoscale-on-new-databases-or-containers"></a>Hoe kan ik automatisch schalen op nieuwe data bases of containers inschakelen?
Raadpleeg dit artikel voor meer [informatie over het inschakelen van automatisch schalen](how-to-provision-autoscale-throughput.md).

### <a name="can-i-enable-autoscale-on-an-existing-database-or-a-container"></a>Kan ik automatisch schalen inschakelen voor een bestaande data base of container?
Ja. U kunt ook overschakelen tussen automatisch schalen en standaard ingerichte door Voer, indien nodig. Op dit moment kunt u voor alle Api's alleen de [Azure Portal](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container) gebruiken om deze bewerkingen uit te voeren.

### <a name="how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work"></a>Hoe werkt de migratie tussen automatisch schalen en standaard (hand matig) ingerichte door Voer?
Conceptueel gezien is het wijzigen van het doorvoer type een proces met twee fasen. Eerst verzendt u een aanvraag voor het wijzigen van de doorvoer instellingen om automatisch schalen of hand matige ingerichte door voer te gebruiken. In beide gevallen bepaalt het systeem automatisch de eerste RU/s-waarde, op basis van de huidige instellingen voor door Voer en opslag. Tijdens deze stap wordt er geen door de gebruiker aangegeven RU/s-waarde geaccepteerd. Nadat de update is voltooid, kunt u [de ru/s](#can-i-change-the-max-rus-on-the-database-or-container) aanpassen aan uw werk belasting. 

**Migratie van standaard (hand matig) ingerichte door voer naar automatisch schalen**

Gebruik voor een container de volgende formule om de eerste automatische schaal aanpassing te ramen/s: ``MAX(4000, current manual provisioned RU/s, maximum RU/s ever provisioned / 10, storage in GB * 100)`` , afgerond op de dichtstbijzijnde 1000 ru/s. De werkelijke initiële automatische schaal aanpassing van RU/s kan variëren, afhankelijk van de configuratie van uw account.

Voor beeld #1: Stel dat u een container hebt met een hand matige door Voer van 10.000 RU/s en 25 GB aan opslag ruimte. Wanneer u automatisch schalen inschakelt, wordt de eerste automatische schaal aanpassing van RU/s: 10.000 RU/s, die wordt geschaald tussen 1000-10.000 RU/s. 

Voor beeld #2: Stel dat u een container hebt met 50.000 RU/s hand matige door Voer en 2500 GB aan opslag ruimte. Wanneer u automatisch schalen inschakelt, wordt de eerste automatische schaal aanpassing van RU/s: 250.000 RU/s, die wordt geschaald tussen 25.000-250.000 RU/s. 

**Migratie van automatisch schalen naar standaard (hand matig) ingerichte door Voer**

De eerste hand matige door Voer van de inrichting is gelijk aan de huidige automatisch schalen Max RU/s. 

Voor beeld: Stel dat u een Data Base of container hebt met een maximale grootte van RU/s 20.000 RU/s (schalen tussen 2000-20.000 RU/s). Wanneer u bijwerkt om hand matig ingerichte door voer te gebruiken, is de initiële door Voer 20.000 RU/s. 

### <a name="is-there-azure-cli-or-powershell-support-to-manage-databases-or-containers-with-autoscale"></a>Is er Azure CLI of Power Shell-ondersteuning voor het beheren van data bases of containers met automatisch schalen?
Op dit moment kunt u alleen resources maken en beheren met automatisch schalen vanuit de Azure Portal, .NET v3 SDK, Java v4 SDK en Azure Resource Manager. Ondersteuning in azure CLI, Power shell en andere Sdk's is nog niet beschikbaar.

### <a name="is-autoscale-supported-for-shared-throughput-databases"></a>Wordt automatisch schalen ondersteund voor gedeelde doorvoer databases?
Ja, automatisch schalen wordt ondersteund voor gedeelde doorvoer databases. Als u deze functie wilt inschakelen, selecteert u automatisch schalen en de optie **door Voer** bij het maken van de data base. 

### <a name="what-is-the-number-of-allowed-containers-per-shared-throughput-database-when-autoscale-is-enabled"></a>Wat is het aantal toegestane containers per gedeelde doorvoer database wanneer automatisch schalen is ingeschakeld?
Azure Cosmos DB dwingt een maximum van 25 containers af in een gedeelde doorvoer database, die van toepassing is op data bases met automatisch schalen of standaard (hand matig) door voer. 

### <a name="what-is-the-impact-of-autoscale-on-database-consistency-level"></a>Wat is de invloed van automatisch schalen op consistentie niveau van de data base?
Er is geen gevolgen voor het automatisch schalen op consistentie niveau van de data base.
Zie het artikel [consistentie niveaus](consistency-levels.md) voor meer informatie over de beschik bare consistentie niveaus.

### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Wat is de opslag limiet die is gekoppeld aan elke maximum RU/s optie?  
De opslag limiet in GB voor elke maximum versie van RU/s is: Max RU/s van data base of container/100. Als het maximum aantal RU/s 20.000 RU/s is, kan de resource bijvoorbeeld 200 GB aan opslag ondersteunen. Zie het artikel [limieten voor automatisch schalen](provision-throughput-autoscale.md#autoscale-limits) voor de beschik bare Max ru/s en opslag opties. 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>Wat gebeurt er als ik de opslag limiet overschrijdt die is gekoppeld aan mijn maximale door Voer?
Als de opslag limiet die is gekoppeld aan de maximale door Voer van de data base of container wordt overschreden, wordt door Azure Cosmos DB de maximale door Voer automatisch verhoogd naar de volgende hoogste RU/s die dat opslag niveau kan ondersteunen.

Als u bijvoorbeeld begint met een maximum van RU/s 50.000 RU/s (schalen tussen 5000-50.000 RU/s), kunt u Maxi maal 500 GB aan gegevens opslaan. Als u meer dan 500 GB hebt - opslag is nu bijvoorbeeld 600 GB -, zal het nieuwe maximum aantal RU/s 60.000 RU/s zijn (schaalbaar tussen 6000 - 60.000 RU/s).

### <a name="can-i-change-the-max-rus-on-the-database-or-container"></a>Kan ik het maximum aantal RU/s voor de data base of container wijzigen? 
Ja. Zie dit [artikel](how-to-provision-autoscale-throughput.md) voor meer informatie over het wijzigen van het maximum aantal ru/s. Wanneer u de maximale grootte van RU/s wijzigt, kan dit een asynchrone bewerking zijn die enige tijd kan duren (Maxi maal 4-6 uur, afhankelijk van de geselecteerde RU/s)

#### <a name="increasing-the-max-rus"></a>Het maximum aantal RU/s verhogen
Wanneer u een aanvraag verzendt om het maximum aantal RU/s te verhogen `Tmax` , is afhankelijk van het geselecteerde maximum aantal ru/s, de service meer bronnen voor ondersteuning van het maximale aantal ru/s. Als dit gebeurt, worden uw bestaande werk belasting en bewerkingen niet beïnvloed. Het systeem blijft de schaal van uw data base of container tussen de vorige tot en met `0.1*Tmax` `Tmax` het nieuwe schaal bereik van `0.1*Tmax_new` tot `Tmax_new` is voltooid.

#### <a name="lowering-the-max-rus"></a>Het maximum aantal RU/s verlagen
Wanneer u het maximum aantal RU/s verlaagt, kunt u de minimum waarde instellen op: `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)` , afgerond op de dichtstbijzijnde 1000 ru/s. 

Voor beeld #1: Stel dat u een container voor automatisch schalen hebt met een maximum van RU/s 20.000 RU/s (schaalt tussen 2000-20.000 RU/s) en 50 GB aan opslag ruimte. De laagste, minimale waarde waarmee u het maximum aantal RU/s kunt instellen: MAX (4000, 20.000/10, **50 * 100**) = 5000 ru/s (schaalbaar tussen 500-5000 ru/s).

Voor beeld #2: Stel dat u een container voor automatisch schalen hebt met Maxi maal RU/s 100.000 RU/s en 100 GB aan opslag ruimte. Nu kunt u Max. RU/s tot 150.000 RU/s schalen (schalen tussen 15.000-150.000 RU/s). De laagste, minimale waarde waarmee u het maximum aantal RU/s kunt instellen: MAX (4000, **150.000/10**, 100 * 100) = 15.000 ru/s (schalen tussen 1500-15.000 ru/s). 

Wanneer u het maximum aantal RU/s verlaagt, kunt u voor een gedeelde doorvoer database de minimum waarde instellen op: `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))` , afgerond op de dichtstbijzijnde 1000 ru/s.  

De bovenstaande formules en voor beelden hebben betrekking op de minimale automatisch schaal bare grootte van RU/s die u kunt instellen, en verschilt van het `0.1 * Tmax` tot-bereik dat door `Tmax` het systeem wordt geschaald. Ongeacht het maximum aantal RU/s, wordt het systeem altijd geschaald `0.1 * Tmax` naar `Tmax` . 

### <a name="how-does-ttl-work-with-autoscale"></a>Hoe werkt TTL met automatisch schalen?
Met automatisch schalen hebben TTL-bewerkingen geen invloed op de schaal van RU/s. Elk RUs dat door TTL wordt gebruikt, maakt geen deel uit van de gefactureerde RU/s van de container voor automatisch schalen. 

Stel dat u een container voor automatisch schalen hebt met 400 – 4000 RU/s:
- Uur 1: T = 0: de container heeft geen gebruik (geen TTL-of workload-aanvragen). De factureer bare RU/s is 400 RU/s.
- Uur 1: T = 1: TTL is ingeschakeld.
- Uur 1: T = 2: de container begint met het ophalen van aanvragen, waarbij 1000 RU in 1 seconde wordt gebruikt. Er zijn ook 200 RUs-waarde voor TTL die moet plaatsvinden. De factureer bare RU/s is nog steeds 1000 RU/s. Ongeacht wanneer de TTLs zich voordoet, heeft dit geen invloed op de logica voor automatisch schalen schalen.

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Wat is de toewijzing tussen de Max RU/s en fysieke partities?
Wanneer u voor het eerst het maximum aantal RU/s selecteert, wordt Azure Cosmos DB ingericht: max. RU/s/10.000 RU/s = aantal fysieke partities. Elke [fysieke partitie](partitioning-overview.md#physical-partitions) kan maxi maal 10.000 ru/s en 50 GB aan opslag ondersteunen. Wanneer de grootte van de opslag groeit, neemt Azure Cosmos DB automatisch de partities op om meer fysieke partities toe te voegen om de opslag toename te verwerken of om het maximum aantal RU/s te verhogen als [de opslag de bijbehorende limiet overschrijdt](#what-is-the-storage-limit-associated-with-each-max-rus-option). 

Het maximum aantal RU/s van de data base of container wordt gelijkmatig verdeeld over alle fysieke partities. De totale door Voer van elke fysieke partitie kan worden geschaald naar is: Max RU/s data base of container/# fysieke partities. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>Wat gebeurt er als inkomende aanvragen het maximum aantal RU/s van de data base of container overschrijden?
Als de totale hoeveelheid opgehaalde RU/s het maximum aantal RU/s van de data base of container overschrijdt, worden aanvragen die groter zijn dan de maximale grootte van RU/s, beperkt en wordt de status code 429 geretourneerd. Aanvragen die resulteren in meer dan 100% genormaliseerd gebruik, worden ook beperkt. Genormaliseerd gebruik wordt gedefinieerd als het maximum van het gebruik van de RU/s voor alle fysieke partities. Stel bijvoorbeeld dat uw maximale door Voer 20.000 RU/s is en dat u twee fysieke partities, P_1 en P_2 hebt, die allemaal kunnen worden geschaald naar 10.000 RU/s. Als P_1 6000 RUs heeft gebruikt in een gegeven tweede, is het genormaliseerde gebruik Maxi maal (6000 RU/10.000 RU, 8000 RU/10.000 RU) = 0,8, als de geP_2 8000.

> [!NOTE]
> De Azure Cosmos DB-hulpprogram ma's voor client-Sdk's en gegevens import (Azure Data Factory, bulk-uitvoerder bibliotheek) maken automatisch opnieuw een nieuwe poging op 429s, zodat af en toe 429s goed zijn. Een aanhoudende groot aantal 429s kan erop duiden dat u de maximale hoeveelheid RU/s moet verhogen of uw partitie strategie voor een [Hot partitie](#autoscale-rate-limiting)kunt controleren.

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autoscale-is-enabled"></a><a id="autoscale-rate-limiting"></a> Is het nog steeds mogelijk om 429s (beperking/snelheids beperking) te zien wanneer automatisch schalen is ingeschakeld? 
Ja. In de volgende twee scenario's kunnen er 429-fouten optreden. Als de totale hoeveelheid gebruikte RU/s het maximum aantal RU/s van de data base of container overschrijdt, worden de aanvragen voor de service dienovereenkomstig beperkt. 

Ten tweede, als er sprake is van een dynamische partitie, dat wil zeggen een logische partitie sleutel waarde met een onevenredig groter aantal aanvragen vergeleken met andere partitie sleutel waarden, is het mogelijk dat de onderliggende fysieke partitie het bovenliggende item/s-budget overschrijdt. Als best practice om dynamische partities te voorkomen, [kiest u een goede partitiesleutel](partitioning-overview.md#choose-partitionkey) die resulteert in een gelijkmatige verdeling van zowel opslag als doorvoer. 

Als u bijvoorbeeld de optie Max-doorvoer capaciteit 20.000 RU/s selecteert en 200 GB aan opslag hebt, met vier fysieke partities, kan elke fysieke partitie automatisch worden geschaald naar 5000 RU/s. Als er een hete partitie is op een bepaalde logische-partitie sleutel, ziet u 429s wanneer de onderliggende fysieke partitie in meer dan 5000 RU/s is, d.w.z. meer dan 100% genormaliseerd gebruik.


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [inschakelen van automatisch schalen op een Azure Cosmos DB-Data Base of-container](how-to-provision-autoscale-throughput.md).
* Meer informatie over de [voor delen van ingerichte door Voer met automatisch schalen](provision-throughput-autoscale.md#benefits-of-autoscale).
* Meer informatie over [logische en fysieke partities](partitioning-overview.md).
                        

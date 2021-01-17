---
title: Kies een prijscategorie
titleSuffix: Azure Cognitive Search
description: "Meer informatie over de prijs categorieën (of Sku's) voor Azure Cognitive Search. Een zoek service kan worden ingericht op de volgende lagen: gratis, basis en standaard. Standaard is beschikbaar in verschillende bron configuraties en capaciteits niveaus."
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.custom: contperf-fy21q2
ms.openlocfilehash: 1a1fc0ce634282ffd4fcf374138fe97a04f32062
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539591"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Een prijs categorie kiezen voor Azure Cognitive Search

Wanneer u [een zoek service maakt](search-create-service-portal.md), kiest u een prijs categorie (of SKU) die is vastgesteld voor de levens duur van de service. De geschatte maandelijkse kosten worden weer gegeven op de pagina **prijs categorie selecteren** in de portal. Als u in plaats daarvan een service maakt via Power shell of Azure CLI, wordt de laag opgegeven via de **`-Sku`** para meter.

De laag die u selecteert, bepaalt het volgende:

+ Het maximum aantal indexen en andere objecten dat u op de service kunt maken
+ Grootte en snelheid van partities (fysieke opslag)
+ Factureerbaar percentage als vaste maandelijkse kosten, maar ook een incrementele kosten als u capaciteit toevoegt

In enkele gevallen bepaalt de laag die u kiest, de beschik baarheid van [Premium-functies](#premium-features).

## <a name="tier-descriptions"></a>Beschrijvingen van lagen

Lagen zijn onder meer **gratis**, **Basic**, **Standard** en **opslag geoptimaliseerd**. De geoptimaliseerde standaard-en opslag ruimte zijn beschikbaar met verschillende configuraties en capaciteit. De volgende scherm afbeelding van Azure Portal toont de beschik bare lagen, minus prijzen (die u kunt vinden in de portal en op de [pagina met prijzen](https://azure.microsoft.com/pricing/details/search/). 

:::image type="content" source="media/search-sku-tier/tiers.png" alt-text="Grafiek met prijs Categorieën" border="true":::

Met **gratis** maakt u een beperkte zoek service voor kleinere projecten, zoals zelf studies en code voorbeelden. Interne systeem bronnen worden gedeeld door meerdere abonnees. U kunt een gratis service niet schalen of aanzienlijke werk belastingen uitvoeren.

**Basic** en **Standard** zijn de meest gebruikte factureer bare lagen, waarbij **standaard** de standaard instelling is, omdat u hiermee meer flexibiliteit hebt bij het schalen van werk belastingen. Met speciale resources onder uw besturings element kunt u grotere projecten implementeren, de prestaties optimaliseren en de capaciteit verg Roten.

Sommige lagen zijn ontworpen voor bepaalde soorten werk. **Standaard 3 High density (S3 HD)** is bijvoorbeeld een *hosting modus* voor S3, waarbij de onderliggende hardware is geoptimaliseerd voor een groot aantal kleinere indexen en die is bedoeld voor multitenancy-scenario's. S3 HD heeft dezelfde kosten per eenheid als S3, maar de hardware is geoptimaliseerd voor snelle bestands Lees bewerkingen op een groot aantal kleinere indexen.

**Opslag geoptimaliseerde** lagen bieden een grotere opslag capaciteit tegen een lagere prijs per TB dan de standaard lagen. De primaire balans is een hogere query latentie, die u moet valideren voor uw specifieke toepassings vereisten. Zie [overwegingen voor prestaties en optimalisatie](search-performance-optimization.md)voor meer informatie over de prestatie overwegingen van deze laag.

Meer informatie over de verschillende lagen op de [pagina met prijzen](https://azure.microsoft.com/pricing/details/search/)vindt u in het artikel [service limieten in azure Cognitive Search](search-limits-quotas-capacity.md) en op de portal pagina wanneer u een service inricht.

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>Beschik baarheid van functies per laag

De meeste functies zijn beschikbaar op alle lagen, met inbegrip van de gratis laag. In enkele gevallen is de laag die u kiest, van invloed op de mogelijkheid om een functie te implementeren. In de volgende tabel worden de functie beperkingen beschreven die betrekking hebben op de servicelaag.

| Functie | Beperkingen |
|---------|-------------|
| [Indexeer functies](search-indexer-overview.md) | Indexeer functies zijn niet beschikbaar op S3 HD.  |
| [AI-verrijking](search-security-manage-encryption-keys.md) | Wordt uitgevoerd op de gratis laag, maar wordt niet aanbevolen. |
| [Beheerde of vertrouwde identiteiten voor uitgaande toegang (Indexeer functie)](search-howto-managed-identities-data-sources.md) | Niet beschikbaar in de gratis laag.|
| [Door de klant beheerde versleutelingssleutels](search-security-manage-encryption-keys.md) | Niet beschikbaar in de gratis laag. |
| [Toegang tot IP-firewall](service-configure-firewall.md) | Niet beschikbaar in de gratis laag. |
| [Persoonlijk eind punt (integratie met persoonlijke Azure-koppeling)](service-create-private-endpoint.md) | Voor inkomende verbindingen met een zoek service, niet beschikbaar in de gratis laag. Voor uitgaande verbindingen door Indexeer functies aan andere Azure-resources, niet beschikbaar op Free of S3 HD. Voor Indexeer functies die gebruikmaken van vaardig heden, niet beschikbaar op Free, Basic, S1 of S3 HD.|

Resource-intensieve functies werken mogelijk niet goed, tenzij u voldoende capaciteit hebt. [AI-verrijking](cognitive-search-concept-intro.md) heeft bijvoorbeeld langlopende vaardig heden waarvoor een time-out optreedt op een gratis service, tenzij de gegevensset klein is.

## <a name="upper-limits"></a>Bovengrens

Lagen bepalen de maximale opslag van de service zelf, evenals het maximum aantal indexen, Indexeer functies, gegevens bronnen, vaardig heden en synoniemen die u kunt maken. Zie [service limieten in Azure Cognitive Search](search-limits-quotas-capacity.md)voor een volledige uitsplitsing van alle limieten. 

## <a name="partition-size-and-speed"></a>Partitie grootte en snelheid

De prijs van de laag bevat details over opslag per partitie die variëren van 2 GB voor Basic, Maxi maal 2 TB voor opslag geoptimaliseerde (L2)-lagen. Andere hardwarekenmerken, zoals snelheid van bewerkingen, latentie en overdrachts snelheden, worden niet gepubliceerd, maar lagen die zijn ontworpen voor specifieke oplossings architecturen zijn gebaseerd op hardware met de functies voor het ondersteunen van die scenario's.

## <a name="billing-rates"></a>Facturerings tarieven

Lagen hebben verschillende facturerings tarieven, met hogere tarieven voor lagen die worden uitgevoerd op meer dure hardware of die meer dure functies bieden. Het facturerings tarief per laag kan worden gevonden op de [Azure-prijzen pagina's](https://azure.microsoft.com/pricing/details/search/) voor Azure Cognitive Search.

Zodra u een service hebt gemaakt, wordt het facturerings tarief ingesteld op de *vaste kosten* van het uitvoeren van de service rondom de klok en een *incrementele kosten* als u ervoor kiest om meer capaciteit toe te voegen.

Zoek Services zijn toegewezen computer bronnen in de vorm van *partities* (voor opslag) en *replica's* (exemplaren van de query-engine). In eerste instantie wordt een service gemaakt met een van beide en is het facturerings tarief inclusief beide resources. Als u de capaciteit schaalt, worden de kosten echter hoger of lager in stappen van het factureer bare percentage.

In het volgende voor beeld ziet u een afbeelding. Neem een hypothetisch factuur tarief van $100 per maand. Als u de zoek service bij de eerste capaciteit van één partitie en één replica behoudt, is $100 wat u kunt verwachten aan het einde van de maand. Als u echter twee meer replica's toevoegt voor een hoge Beschik baarheid, neemt de maandelijkse factuur toe tot $300 ($100 voor het eerste replica partitie-paar, gevolgd door $200 voor de twee replica's).

Dit prijs model is gebaseerd op het concept van het Toep assen van het factuur tarief op het aantal *Zoek eenheden* (su) dat wordt gebruikt door een zoek service. Alle services worden in eerste instantie ingericht met een SU, maar u kunt de SUs verhogen door partities of replica's toe te voegen voor het verwerken van grotere workloads. Zie [kosten schatten van een zoek service](search-sku-manage-costs.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

De beste manier om een prijs categorie te kiezen, is om te beginnen met een laagste kosten categorie en vervolgens de ervaring en het testen op de hoogte te stellen van uw besluit om de service te houden of om een nieuwe te maken op een hogere laag. Voor de volgende stappen wordt u aangeraden een zoek service te maken op een laag die kan voldoen aan het niveau van de tests die u kunt Voorst Ellen en raadpleegt u de volgende richt lijnen voor aanbevelingen voor het schatten van de kosten en capaciteit.

+ [Een zoek service maken](search-create-service-portal.md)
+ [Kosten schatten](search-sku-manage-costs.md)
+ [Capaciteit schatten](search-sku-manage-costs.md)
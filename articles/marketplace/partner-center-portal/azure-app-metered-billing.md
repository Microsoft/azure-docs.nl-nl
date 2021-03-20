---
title: Factuur met data limiet voor beheerde toepassingen met Marketplace-meet service | Azure Marketplace
description: Deze documentatie is een hand leiding voor Isv's die Azure-toepassingen publiceren met flexibele facturerings modellen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: d015cec30e516541b50c2acfac38fad898965e1b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96436348"
---
# <a name="managed-application-metered-billing"></a>Factuur voor beheerde toepassing met data limiet 

Met de Marketplace-meet service kunt u beheerde toepassings abonnementen maken voor Azure-toepassing aanbiedingen die worden gefactureerd op basis van niet-standaard eenheden. Voordat u deze aanbieding publiceert, definieert u de facturerings dimensies, zoals band breedte, tickets of e-mail berichten die zijn verwerkt. Klanten betalen vervolgens op basis van het verbruik van deze dimensies.  Uw systeem brengt micro soft op de hoogte van de berekenings-API van factureer bare gebeurtenissen van de Marketplace-service.

## <a name="prerequisites-for-metered-billing"></a>Vereisten voor facturering met data limiet

Om ervoor te zorgen dat een beheerd toepassings abonnement gebruikmaakt van facturering met data limiet, moet dit:

* Voldoen aan de vereisten van de aanbieding, zoals beschreven in [een Azure-toepassings aanbieding maken](../create-new-azure-apps-offer.md).
* **Prijzen** voor het opladen van klanten configureren voor de kosten per maand voor uw service. De prijs kan nul zijn als u geen vaste kosten wilt in rekening brengen en in plaats daarvan volledig wilt vertrouwen op factuur met data limiet.
* **Facturerings dimensies** instellen voor de meter gebeurtenissen die door de klant worden betaald boven op het vaste tarief.
* Integreer met de [api's van de Marketplace-meet service](./marketplace-metering-service-apis.md) om micro soft te informeren over factureer bare gebeurtenissen.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Hoe gefactureerde facturering past bij de prijzen

Wanneer het gaat om het definiëren van de aanbieding samen met de prijs modellen, is het belang rijk om inzicht te krijgen in de aanbiedings hiërarchie.

* Elke Azure-toepassing aanbieding kan oplossings sjabloon of beheerde toepassings abonnementen hebben.
* Factuur met data limiet wordt alleen geïmplementeerd met beheerde toepassings abonnementen.
* Aan elk beheerd toepassings abonnement is een prijs model gekoppeld. 
* Prijs model bevat een maandelijks terugkerend tarief dat kan worden ingesteld op $0.
* Naast de terugkerende kosten kan het plan ook optionele dimensies bevatten die worden gebruikt om klanten in rekening te brengen voor gebruik dat niet in het vaste tarief is opgenomen. Elke dimensie vertegenwoordigt een factureer bare eenheid die uw service communiceert met micro soft via de [Marketplace meter Service-API](marketplace-metering-service-apis.md).

## <a name="sample-offer"></a>Voor beeld aanbieding

Contoso is bijvoorbeeld een uitgever met een beheerde toepassings service met de naam contoso Analytics (CoA). Met CoA kunnen klanten grote hoeveel heden gegevens analyseren voor rapportage en gegevens opslag. Contoso is geregistreerd als een uitgever in het partner centrum voor het programma voor commerciële Marketplace voor het publiceren van aanbiedingen aan Azure-klanten. Er zijn twee abonnementen gekoppeld aan CoA, hieronder beschreven:

* Basis plan
    * Analyseer 100 GB en Genereer 100 rapporten voor $0/maand
    * Meer dan 100 GB $10 betaalt u voor elke 1 GB
    * Na de 100-rapporten betaalt u voor elk rapport $1.
* Premium-abonnement
    * Analyseer 1000 GB en Genereer 1000 rapporten voor $350/maand
    * Na de 1000 GB betaalt u $100 voor elke 1 TB
    * Na de 1000-rapporten betaalt u voor elk rapport $0,5.

Een Azure-klant die zich abonneert op een CoA-service kan rapporten per maand analyseren en genereren op basis van het geselecteerde plan. Contoso meet het gebruik tot het inbegrepen aantal zonder gebruik van gebeurtenissen naar micro soft te verzenden. Wanneer klanten meer dan het inbegrepen aantal verbruiken, hoeven ze geen plannen te wijzigen of iets anders te doen. Contoso meet de overschrijding buiten het inbegrepen aantal en start het verzenden van gebruiks gebeurtenissen naar micro soft voor extra gebruik met de [Marketplace meter Service-API](./marketplace-metering-service-apis.md). Micro soft brengt de klant in rekening voor het aanvullende gebruik zoals opgegeven door de uitgever.

## <a name="billing-dimensions"></a>Facturerings dimensies

Facturerings dimensies worden gebruikt om aan de klant te communiceren over hoe ze worden gefactureerd voor het gebruik van de software.  Deze dimensies worden ook gebruikt om gebruiks gebeurtenissen te communiceren met micro soft. Ze worden als volgt gedefinieerd:

* **Dimensie-id**: de onveranderbare id waarnaar wordt verwezen tijdens het verzenden van gebruiks gebeurtenissen.
* **Dimensie naam**: de weergave naam die aan de dimensie is gekoppeld, bijvoorbeeld ' verzonden tekst berichten '.
* **Maat eenheid**: de beschrijving van de facturerings eenheid, bijvoorbeeld ' per SMS-bericht ' of ' per 100 e-mail berichten '.
* **Prijs per eenheid**: de prijs voor één eenheid van de dimensie.
* **Inbegrepen hoeveelheid voor maandelijkse periode**: de hoeveelheid dimensie die per maand wordt opgenomen voor klanten die de terugkerende maandelijkse kosten betalen, moet een geheel getal zijn.

Facturerings dimensies worden gedeeld in alle abonnementen voor een aanbieding. Sommige kenmerken zijn van toepassing op de dimensie over alle plannen en andere kenmerken zijn specifiek voor een plan.

De kenmerken die de dimensie zelf definiëren, worden gedeeld met alle plannen voor een aanbieding. Voordat u de aanbieding publiceert, is een wijziging in deze kenmerken van de context van een plan van invloed op de dimensie definitie voor alle plannen. Zodra u de aanbieding hebt gepubliceerd, kunnen deze kenmerken niet meer worden bewerkt. De kenmerken zijn:

* Id
* Name
* Meeteenheid

De andere kenmerken van een dimensie zijn specifiek voor elk plan en kunnen verschillende waarden hebben van plan tot plan.  Voordat u het plan publiceert, kunt u deze waarden bewerken en wordt alleen dit abonnement beïnvloed. Zodra u het abonnement hebt gepubliceerd, kunnen deze kenmerken niet meer worden bewerkt. De kenmerken zijn:

* Prijs per eenheid
* Inbegrepen hoeveelheid voor maandelijkse klanten 
* Inbegrepen hoeveelheid voor jaarlijkse klanten 

Dimensies hebben ook twee speciale concepten: ' enabled ' en ' infinite '.

* **Ingeschakeld** geeft aan dat dit plan deelneemt aan deze dimensie.  U kunt deze optie uitschakelen als u een nieuw abonnement maakt dat geen gebruiks gebeurtenissen op basis van deze dimensie verzendt. Daarnaast worden nieuwe dimensies die zijn toegevoegd nadat een plan voor het eerst werd gepubliceerd, weer gegeven als ' niet ingeschakeld ' in het al gepubliceerde abonnement.  Een uitgeschakelde dimensie wordt niet weer gegeven in een lijst met dimensies voor een plan dat door klanten wordt gezien.
* **Oneindig**, vertegenwoordigd door het oneindigheids teken ' ∞ ', geeft aan dat dit plan deelneemt aan deze dimensie, zonder gebruik van een Data limiet voor deze dimensie. Als u aan uw klanten wilt aangeven dat de functionaliteit die wordt vertegenwoordigd door deze dimensie, in het plan is opgenomen, maar zonder limiet voor gebruik.  Een dimensie met oneindig gebruik wordt weer gegeven in een lijst met dimensies voor een plan dat door klanten wordt weer gegeven.  Voor dit abonnement worden nooit kosten in rekening gebracht.

>[!Note] 
>De volgende scenario's worden expliciet ondersteund:  <br> -U kunt een nieuwe dimensie toevoegen aan een nieuw plan.  De nieuwe dimensie wordt niet ingeschakeld voor al gepubliceerde plannen. <br> -U kunt een abonnement publiceren met een vast maand tarief en zonder dimensies, en vervolgens een nieuw plan toevoegen en een nieuwe dimensie voor dat plan configureren. De nieuwe dimensie wordt niet ingeschakeld voor al gepubliceerde plannen.

## <a name="constraints"></a>Beperkingen

### <a name="locking-behavior"></a>Vergrendelings gedrag

Een dimensie die wordt gebruikt met de Marketplace-meet service vertegenwoordigt een uitleg over hoe een klant betaalt voor de service.  Alle details van een dimensie zijn niet meer bewerkbaar wanneer een aanbieding wordt gepubliceerd.  Voordat u uw aanbieding publiceert, is het belang rijk dat u uw dimensies volledig hebt gedefinieerd.

Zodra een aanbieding is gepubliceerd met een dimensie, kunnen de details van het aanbod niveau voor die dimensie niet meer worden gewijzigd:

* Id
* Name
* Meeteenheid

Zodra een plan is gepubliceerd, kunnen de details op plan niveau niet meer worden gewijzigd:

* Prijs per eenheid
* Inbegrepen hoeveelheid voor maandelijkse periode
* Hiermee wordt aangegeven of de dimensie is ingeschakeld voor het plan

>[!Note]
>Facturering met data limieten met Marketplace-meet service wordt nog niet ondersteund in de Azure Government Cloud.

### <a name="upper-limits"></a>Bovengrens

Het maximum aantal dimensies dat voor een enkele aanbieding kan worden geconfigureerd, is 30 unieke dimensies.

## <a name="get-support"></a>Ondersteuning krijgen

Als u een van de volgende problemen hebt, kunt u een ondersteunings ticket openen.

* Technische problemen met Marketplace meter Service-API.
* Een probleem dat moet worden geëscaleerd vanwege een fout of bug aan de zijkant (bijvoorbeeld verkeerde gebruiks gebeurtenis).
* Alle andere problemen met betrekking tot facturering met data limiet.

Volg de instructies in [ondersteuning voor het programma voor commerciële Marketplace in het partner centrum om de](../support.md) ondersteunings opties voor Publisher te begrijpen en het ondersteunings ticket te openen met micro soft.

## <a name="next-steps"></a>Volgende stappen

- Zie [api's voor Marketplace metering service](./marketplace-metering-service-apis.md) voor meer informatie.

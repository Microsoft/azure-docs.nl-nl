---
title: Factuur met data limiet voor SaaS-aanbiedingen met behulp van de micro soft Commercial Marketplace-meet service
description: Meer informatie over flexibele facturerings modellen voor SaaS-aanbiedingen met de commerciële Marketplace-meter service.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/08/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 9f72d54fda8f66c2fce35f0520b51406aa276bb0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92892750"
---
# <a name="metered-billing-for-saas-using-the-commercial-marketplace-metering-service"></a>Factuur met data limiet voor SaaS met de commerciële Marketplace-meet service

Met de commerciële Marketplace meter service kunt u SaaS-aanbiedingen (Software as a Service) maken die worden gefactureerd op basis van niet-standaard eenheden. Voordat u een SaaS-aanbieding naar de commerciële Marketplace publiceert, definieert u de facturerings dimensies, zoals band breedte, tickets of e-mails die zijn verwerkt.  Klanten betalen vervolgens op basis van het verbruik van deze dimensies, waarbij uw systeem micro soft informeert via de commerciële Marketplace meter Service-API van factureer bare gebeurtenissen wanneer deze zich voordoen.  

## <a name="prerequisites-for-metered-billing"></a>Vereisten voor facturering met data limiet

Als u een SaaS-aanbieding wilt gebruiken, moet deze eerst:

- Voldoen aan de vereisten van de aanbieding voor een [verkoop via micro soft-aanbieding](../plan-saas-offer.md#listing-options) , zoals beschreven in [een SaaS-aanbieding maken in de commerciële Marketplace](../create-new-saas-offer.md).
- Integreer met de [SaaS-fulfillment-api's](./pc-saas-fulfillment-api-v2.md) voor klanten om uw aanbieding in te richten en er verbinding mee te maken.  
- Worden geconfigureerd voor het prijs model voor **vaste tarieven** bij het opladen van klanten voor uw service.  Dimensies zijn een optionele uitbrei ding voor het prijs model voor vaste tarieven. 

De SaaS-aanbieding kan vervolgens worden geïntegreerd met de Api's van de [commerciële Marketplace meter service](./marketplace-metering-service-apis.md) om micro soft te informeren over factureer bare gebeurtenissen.

>[!Note]
>Marketplace-meet service is alleen beschikbaar voor het facturerings model vast tarief en is niet van toepassing op het facturerings model per gebruiker.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Hoe gefactureerde facturering past bij de prijzen

Het is belang rijk dat u begrijpt wat de aanbiedings hiërarchie is, wanneer het gaat om het definiëren van de aanbieding samen met de prijs modellen.

- Elke SaaS-aanbieding is zo geconfigureerd dat deze wordt verkocht via micro soft of niet.  Zodra een aanbieding is gepubliceerd, kunt u deze optie niet meer wijzigen.
- Elk SaaS-aanbod dat is geconfigureerd om te worden verkocht via micro soft, kan een of meer abonnementen hebben.  Een gebruiker meldt zich aan bij de SaaS-aanbieding, maar wordt aangeschaft via micro soft binnen de context van een abonnement.
- Aan elk abonnement is een prijs model gekoppeld: **vast tarief** of **per gebruiker**. Alle abonnementen in een aanbieding moeten aan hetzelfde prijs model zijn gekoppeld. Er kan bijvoorbeeld geen abonnement zijn met plannen voor een vast prijs model en een ander prijs model per gebruiker.
- Binnen elk schema dat is geconfigureerd voor een facturerings model met een vast tarief, is ten minste één terugkerend tarief (dat kan $0), opgenomen:
    - Terugkerende **maandelijkse** kosten: vaste maandelijkse kosten die vooraf worden betaald op een maandelijks terugkeer patroon wanneer de gebruiker het abonnement koopt.
    - Terugkerende **jaarlijkse** kosten: forfaitaire jaarlijkse kosten die vooraf worden betaald op een jaarlijks terugkeer patroon wanneer de gebruiker het abonnement koopt.
- Naast de terugkerende kosten kan een vast tarief plan ook optionele aangepaste dimensies bevatten die worden gebruikt om klanten in rekening te brengen voor het overschrijding-gebruik dat niet in het vast tarief is opgenomen.  Elke dimensie vertegenwoordigt een factureer bare eenheid die uw service communiceert met micro soft met behulp van de [commerciële Marketplace meter Service-API](./marketplace-metering-service-apis.md).

## <a name="sample-offer"></a>Voor beeld aanbieding

Contoso is bijvoorbeeld een uitgever met een SaaS-service met de naam contoso Notification Services (CNS). Met CNS kunnen klanten meldingen verzenden via e-mail of tekst. Contoso is geregistreerd als een uitgever in het partner centrum voor het programma voor commerciële Marketplace voor het publiceren van SaaS-aanbiedingen aan Azure-klanten.  Er zijn twee plannen gekoppeld aan CNS, die hieronder worden beschreven:

- Basic-abonnement
    - 10000 e-mails en 1000 teksten verzenden voor $0/maand (vaste maandelijkse kosten)
    - Na de 10000 e-mails betaalt u $1 voor elke 100 e-mail berichten
    - Na de 1000 teksten betaalt u voor elke tekst een bedrag van $0,02

    [![Prijzen basis abonnement](./media/saas-basic-pricing.png "Klik voor een vergrote weer gave")](./media/saas-basic-pricing.png)

- Premium-abonnement
    - 50000 e-mails en 10000 teksten verzenden voor $350/maand-of 5 min.-e-mail berichten en 1M teksten van $3500 per jaar
    - Na de 50000 e-mails betaalt u $0,5 voor elke 100 e-mail berichten
    - Na de 10000 teksten betaalt u voor elke tekst een bedrag van $0,01

    [![Prijzen voor Premium-abonnementen](./media/saas-premium-pricing.png "Klik voor een vergrote weer gave")](./media/saas-premium-pricing.png)

- Enter prise-plan
    - Verzend onbeperkt aantal e-mails en 50000 teksten voor $400/maand
    - Meer dan 50000 teksten betalen $0,005 voor elke txt

    [![Prijzen voor Enter prise-abonnement](./media/saas-enterprise-pricing.png "Klik voor een vergrote weer gave")](./media/saas-enterprise-pricing.png)

Op basis van het geselecteerde plan kan een Azure klant aankoop abonnement op de CNS SaaS-aanbieding het bijgevoegde aantal tekst en e-mails per abonnements periode (maand of jaar) verzenden, zoals wordt weer gegeven in de abonnements Details: start date en endDate.  Contoso telt het gebruik tot aan de totale hoeveelheid in de basis, zonder dat er gebruiks gebeurtenissen naar micro soft worden verzonden. Wanneer klanten meer dan het inbegrepen aantal verbruiken, hoeven ze geen plannen te wijzigen of iets anders te doen.  Contoso meet de overschrijding buiten het inbegrepen aantal en start het verzenden van gebruiks gebeurtenissen naar micro soft voor het opladen van het overschrijding-gebruik met de [commerciële Marketplace meter Service-API](./marketplace-metering-service-apis.md).  Micro soft brengt de klant in rekening voor het overschrijding-gebruik zoals opgegeven door de uitgever in de aangepaste dimensies. De facturering van de overschrijding wordt uitgevoerd in de volgende facturerings cyclus (maandelijks, maar kan voor sommige klanten per kwar taal of vroegtijdig worden beschouwd).  Voor een maandelijks vast tarief plan wordt de facturering voor overschrijding gemaakt voor elke maand waarin overschrijding heeft plaatsgevonden.  Wanneer de hoeveelheid die is opgenomen in de basis per jaar, wordt gebruikt voor een jaarlijks vast tarief plan, wordt alle extra gebruik dat door de aangepaste meter wordt gegenereerd, gefactureerd als overschrijding tijdens elke facturerings cyclus (maandelijks) tot aan het einde van de jaar periode van het abonnement.

## <a name="billing-dimensions"></a>Facturerings dimensies

Elke facturerings dimensie definieert een aangepaste eenheid waarmee de ISV gebruiks gebeurtenissen kan verzenden.  Facturerings dimensies worden ook gebruikt om met de klant te communiceren over hoe ze worden gefactureerd voor het gebruik van de software.  Ze worden als volgt gedefinieerd:

- **Id**: de onveranderbare dimensie-id waarnaar wordt verwezen tijdens het verzenden van gebruiks gebeurtenissen.
- **Weergave naam**: de weergave naam die aan de dimensie is gekoppeld, bijvoorbeeld ' verzonden tekst berichten '.
- **Maat eenheid**: de beschrijving van de facturerings eenheid, bijvoorbeeld ' per SMS-bericht ' of ' per 100 e-mail berichten '.
- **Prijs per eenheid in USD**: de prijs voor één eenheid van de dimensie.  Dit kan 0 zijn. 
- **Maandelijks aantal dat is opgenomen in de basis**: de hoeveelheid dimensie die per maand wordt opgenomen voor klanten die de terugkerende maandelijkse kosten betalen, moet een geheel getal zijn. Dit kan 0 of onbeperkt zijn.
- **Jaarlijkse hoeveelheid die is inbegrepen in de basis**: de hoeveelheid van de dimensie die per jaar wordt opgenomen voor klanten die de terugkerende jaarlijkse vergoeding betalen, moet een geheel getal zijn. Kan 0 of onbeperkt zijn.

Facturerings dimensies worden gedeeld in alle abonnementen voor een aanbieding.  Sommige kenmerken zijn van toepassing op de dimensie over alle plannen en andere kenmerken zijn specifiek voor een plan.

De kenmerken die de dimensie zelf definiëren, worden gedeeld met alle plannen voor een aanbieding.  Voordat u de aanbieding publiceert, is een wijziging in deze kenmerken van de context van een plan van invloed op de dimensie definitie voor alle plannen.  Zodra u de aanbieding hebt gepubliceerd, kunnen deze kenmerken niet meer worden bewerkt.  Deze kenmerken zijn:

- Id
- Weergavenaam
- Meeteenheid

De andere kenmerken van een dimensie zijn specifiek voor elk plan en kunnen verschillende waarden hebben van plan tot plan.  Voordat u het plan publiceert, kunt u deze waarden bewerken en wordt alleen dit abonnement beïnvloed.  Zodra u het abonnement hebt gepubliceerd, kunnen deze kenmerken niet meer worden bewerkt.  Deze kenmerken zijn:

- Prijs per eenheid in USD
- De maandelijkse hoeveelheid die is opgenomen in de basis  
- Jaarlijks opgenomen hoeveelheid in baseIncluded  

Dimensies hebben ook twee speciale concepten: ' enabled ' en ' infinite '.

- **Ingeschakeld** geeft aan dat dit plan deelneemt aan deze dimensie.  Als u een nieuw abonnement maakt dat geen gebruiks gebeurtenissen op basis van deze dimensie verzendt, kunt u deze optie het beste uitschakelen.  Daarnaast worden nieuwe dimensies die zijn toegevoegd nadat een plan voor het eerst werd gepubliceerd, weer gegeven als ' niet ingeschakeld ' in het al gepubliceerde abonnement.  Een uitgeschakelde dimensie wordt niet weer gegeven in een lijst met dimensies voor een plan dat door klanten wordt gezien.
- **Oneindig** vertegenwoordigd door het oneindigheids teken ' ∞ ', geeft aan dat dit plan deelneemt aan deze dimensie, maar geen gebruik maakt van deze dimensie.  Als u aan uw klanten wilt aangeven dat de functionaliteit die wordt vertegenwoordigd door deze dimensie, in het plan is opgenomen, maar zonder limiet voor gebruik.  Een dimensie met oneindig gebruik wordt weer gegeven in een lijst met dimensies voor een plan dat door klanten wordt gezien, met een indicatie dat er nooit kosten in rekening worden gebracht voor dit abonnement.

>[!Note] 
>De volgende scenario's worden expliciet ondersteund: <br> -U kunt een nieuwe dimensie toevoegen aan een nieuw plan.  De nieuwe dimensie wordt niet ingeschakeld voor al gepubliceerde plannen. <br> -U kunt een schema voor **vaste kosten** publiceren zonder dimensies, vervolgens een nieuw plan toevoegen en een nieuwe dimensie voor dat plan configureren. De nieuwe dimensie wordt niet ingeschakeld voor al gepubliceerde plannen.

### <a name="setting-dimension-price-per-unit-per-supported-market"></a>De dimensie prijs per eenheid per ondersteunde markt instellen

De prijs van factuur dimensies kan worden ingesteld per land of regio, zoals een prijs voor vaste prijzen. U moet de functie voor het importeren en exporteren van prijzen in partner centrum als volgt gebruiken.

1. Definieer de gewenste dimensies en markeer welke markten worden ondersteund. 
1. Deze gegevens exporteren naar een bestand.
1. Voeg de juiste prijzen per land/regio toe en importeer het bestand in partner centrum.

De gebruikers interface van de meter wordt gewijzigd om aan te geven dat de prijzen van de dimensie alleen in het bestand kunnen worden weer gegeven.

[![commerciële Marketplace-meet service dimensies](media/metering-service-dimensions.png "Klik voor een vergrote weer gave")](media/metering-service-dimensions.png)

### <a name="private-plan"></a>Persoonlijk abonnement

Net als bij vaste-rente plannen kan een plan met dimensies worden ingesteld als privé-abonnement, alleen toegankelijk via de gedefinieerde doel groep van het abonnement.

## <a name="constraints"></a>Beperkingen

### <a name="trial-behavior"></a>Proef gedrag

Facturering met data limieten met behulp van de commerciële Marketplace-meet service is niet compatibel met een gratis proef versie.  Het is niet mogelijk om een abonnement te configureren voor het gebruik van zowel een gefactureerde facturering als een gratis proef versie.

### <a name="locking-behavior"></a>Vergrendelings gedrag

Omdat een dimensie die wordt gebruikt met de commerciële Marketplace-meet service, een goed idee is van de manier waarop een klant betaalt voor de service, kunnen alle Details voor een dimensie niet meer worden bewerkt nadat u deze hebt gepubliceerd.  Het is belang rijk dat u uw dimensies volledig hebt gedefinieerd voor een plan voordat u publiceert.

Zodra een aanbieding is gepubliceerd met een dimensie, kunnen de details van het aanbod niveau voor die dimensie niet meer worden gewijzigd:

- Id
- Weergavenaam
- Meeteenheid

Zodra een plan is gepubliceerd, kunnen de details op plan niveau niet meer worden gewijzigd:

- Prijs per eenheid in USD
- De maandelijkse hoeveelheid die is opgenomen in de basis
- Jaarlijkse hoeveelheid die is opgenomen in de basis
- Hiermee wordt aangegeven of de dimensie is ingeschakeld voor het plan of niet

### <a name="upper-limits"></a>Bovengrens

Het maximum aantal dimensies dat voor een enkele aanbieding kan worden geconfigureerd, is 30 unieke dimensies.

## <a name="get-support"></a>Ondersteuning krijgen

Als u een van de volgende problemen hebt, kunt u een ondersteunings ticket openen.

- Technische problemen met Marketplace meter Service-API.
- Een probleem dat moet worden geëscaleerd vanwege een fout of bug aan de zijkant (bijvoorbeeld verkeerde gebruiks gebeurtenis).
- Alle andere problemen met betrekking tot facturering met data limiet.

Als u meer wilt weten over de ondersteunings opties voor Publisher en een ondersteunings ticket wilt openen met micro soft, volgt u de instructies in [ondersteuning voor het programma voor commerciële Marketplace in partner centrum](../support.md).

## <a name="next-steps"></a>Volgende stappen

- [Service-API's voor Marketplace-meting](./marketplace-metering-service-apis.md)

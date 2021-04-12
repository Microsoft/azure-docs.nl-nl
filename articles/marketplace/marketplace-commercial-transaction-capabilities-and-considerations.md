---
title: Micro soft Commercial Marketplace Transact-mogelijkheden
description: In dit artikel worden de prijs-, facturerings-, facturerings-en uitbetalings overwegingen voor de optie commerciële Marketplace Transact beschreven.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 0a25e1b50455cad5bdbe5b76b2a291f2a1c11940
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107003"
---
# <a name="commercial-marketplace-transact-capabilities"></a>De Transact-mogelijkheden voor commerciële Marketplace

In dit artikel worden de prijs-, facturerings-, facturerings-en uitbetalings overwegingen voor de micro soft Commercial Marketplace beschreven.

## <a name="transactions-by-listing-option"></a>De optie trans acties per aanbieding

De uitgever of micro soft is verantwoordelijk voor het beheer van software licentie transacties voor aanbiedingen in de commerciële Marketplace. De optie die u kiest voor uw aanbieding bepaalt wie de trans actie beheert. Zie [Inleiding tot aanbiedings opties](determine-your-listing-type.md) voor Beschik baarheid en uitleg van elke publicatie optie

### <a name="contact-me-free-trial-and-byol-options"></a>Contact opnemen, gratis proef versie en BYOL-opties

Uitgevers kunnen kiezen voor de _contact persoon_ en _gratis proef versie_, opties voor promotie-en gebruikers verwervings doeleinden. Voor sommige aanbiedings typen kunnen uitgevers kiezen voor de optie _uw eigen licentie_ (BYOL) meenemen zodat klanten een abonnement op uw aanbieding kunnen kopen met een licentie die hij of zij rechtstreeks van u heeft gekocht. Met deze opties neemt micro soft niet rechtstreeks deel aan de software licentie transacties van de uitgever en zijn er geen kosten verbonden aan de trans actie, zodat uitgevers al deze inkomsten houden.

Met deze opties zijn uitgevers verantwoordelijk voor het ondersteunen van alle aspecten van de software licentie transactie. Dit omvat, maar is niet beperkt tot order, verwerking, meting, facturering, facturering, betaling en incasso. Met de optie contact opnemen met mij kunnen uitgevers 100% van de licentie kosten voor de uitgever van de software blijven ontvangen van de klant.

### <a name="transact-publishing-option"></a>Optie voor het publiceren van Transact

Door micro soft te verkopen kunt u profiteren van de mogelijkheden van micro soft commerce en biedt een end-to-end-ervaring van detectie en evaluatie tot aankoop en implementatie. Een _transactable_ -aanbieding is een waarin micro soft de uitwisseling van geld voor een software licentie voor de uitgever vereenvoudigt. De aanbiedingen voor de Transact worden gefactureerd op basis van een bestaand micro soft-abonnement of een credit card, waardoor micro soft Cloud-Marketplace-trans acties kan hosten namens de uitgever.

U kiest de optie Transact wanneer u een nieuwe aanbieding maakt in het partner centrum. Deze optie wordt alleen weer gegeven als Transact beschikbaar is voor het type van uw aanbieding.

## <a name="transact-overview"></a>Overzicht van Transact

Wanneer u de optie Transact gebruikt, maakt micro soft de verkoop van software van derden en de implementatie van sommige aanbiedings typen mogelijk voor het Azure-abonnement van de klant. De uitgever moet rekening houden met de facturering van de kosten voor de infra structuur en uw eigen software licentie kosten als u een prijs model selecteert voor een aanbieding.

De optie voor het publiceren van Transact wordt momenteel ondersteund voor de volgende aanbiedings typen:

| Type aanbieding | Facturerings uitgebracht | Factuur met data limiet | Prijsmodel |
| ------------ | ------------- | ------------- | ------------- |
| Azure Application<br>(Beheerde toepassing) | Maandelijks | Yes | Op basis van gebruik |
| Virtuele Azure-machine | Keren | No | Op gebruik gebaseerd, BYOL |
| Software als een dienst (SaaS) | Maandelijks en jaarlijks | Yes | Vast aantal, per gebruiker, op basis van gebruik. |
|||||

`*` Azure virtual machine biedt ondersteuning voor op gebruik gebaseerde facturerings plannen. Deze plannen worden maandelijks gefactureerd voor elk uur gebruik van het abonnement op basis van per kern, per kern grootte, of op basis van het gebruik van de markt en de kern grootte.

### <a name="metered-billing"></a>Factuur met data limiet

Met de _Marketplace-meet service_ kunt u kosten voor betalen per gebruik (op basis van verbruik) opgeven naast de maandelijkse of jaarlijkse kosten die zijn opgenomen in het contract (recht). U kunt gebruiks kosten berekenen voor de dimensies voor Marketplace-meet service die u opgeeft, zoals band breedte, tickets of e-mails die zijn verwerkt. Zie voor meer informatie over het factureren van een Data limiet voor SaaS-aanbiedingen naar [gecontroleerde facturering voor SaaS met behulp van de commerciële Marketplace meter service](./partner-center-portal/saas-metered-billing.md). Zie voor meer informatie over het gebruik van facturen met een Data limiet voor Azure-toepassing aanbiedingen [beheerde toepassing met gecontroleerde facturering](./partner-center-portal/azure-app-metered-billing.md).

### <a name="billing-infrastructure-costs"></a>Kosten van facturerings infrastructuur

Voor **virtuele machines** en **Azure-toepassingen** worden Azure-infrastructuur gebruiks kosten in rekening gebracht voor het Azure-abonnement van de klant. De gebruikskosten voor de infrastructuur zijn geprijsd en worden afzonderlijk van de licentiekosten van de softwareprovider weergegeven op de factuur van de klant.

Voor **SaaS-apps** moet de uitgever rekening met de gebruiks kosten voor Azure-infra structuur en de kosten voor software licenties als één kosten item. Het wordt weer gegeven als een vast bedrag voor de klant. Het gebruik van de Azure-infra structuur wordt rechtstreeks beheerd en gefactureerd met de uitgever. De werkelijke gebruiks kosten voor de infra structuur zijn niet zichtbaar voor de klant. Uitgevers willen doorgaans de gebruiks kosten voor Azure-infra structuur bundelen in hun prijzen voor software licenties. Software licentie kosten worden niet gemeten of gebaseerd op het verbruik van de gebruiker.

## <a name="pricing-models"></a>Prijsmodellen

Afhankelijk van de gebruikte transactie optie zijn abonnements kosten als volgt:

- **Nu downloaden (gratis)**: geen kosten voor software licenties. Gratis aanbiedingen kunnen niet worden omgezet in een betaald abonnement. Klanten moeten een betaalde aanbieding best Ellen.
- **Bring your own License** (BYOL): als een aanbieding wordt vermeld in de commerciële Marketplace, worden alle toepasselijke kosten voor software licenties rechtstreeks beheerd tussen de uitgever en de klant. Micro soft brengt alleen toepasselijke Azure-infrastructuur gebruiks kosten in rekening voor het Azure-abonnement van de klant.
- **Abonnements prijzen**: de kosten voor software licenties worden weer gegeven als maandelijks of jaarlijks tarief voor terugkerende abonnementen, gefactureerd als een vast tarief of per seat. Nieuwe abonnements kosten worden niet evenredig geclassificeerd voor annuleringen van klanten op de middel lange termijn, of ongebruikte services. De kosten voor het opnieuw uitvoeren van het abonnement kunnen worden gefactureerd als de klant het abonnement in het midden van de abonnements termijn bijupgradet of versnelt.
- **Prijzen op basis van gebruik**: voor Azure Virtual Machine-aanbiedingen worden klanten in rekening gebracht op basis van de omvang van hun gebruik van de aanbieding. Voor installatie kopieën van virtuele machines worden de kosten van een Azure Marketplace in rekening gebracht, zoals ingesteld door de uitgever, voor het gebruik van virtuele machines die zijn geïmplementeerd vanuit de VM-installatie kopieën. Het uurtarief kan uniform zijn of variëren tussen de grootten van virtuele machines. Gedeeltelijke uren worden per minuut in rekening gebracht. Abonnementen worden maandelijks gefactureerd.
- **Prijzen in licentie**: voor Azure-toepassing aanbiedingen en SaaS-aanbiedingen kunnen uitgevers de [Marketplace-meet service](./partner-center-portal/marketplace-metering-service-apis.md) gebruiken om verbruik te factureren op basis van de aangepaste meter dimensies die ze configureren. Deze wijzigingen zijn een aanvulling op de maandelijkse of jaarlijkse kosten die zijn opgenomen in het contract (recht). Voor beelden van aangepaste meter dimensies zijn band breedte, tickets of e-mails die zijn verwerkt. Uitgevers kunnen een of meer dimensies met metingen definiëren voor elk abonnement, maar Maxi maal 30 per aanbieding. Uitgevers zijn verantwoordelijk voor het bijhouden van het gebruik van afzonderlijke klanten, waarbij elke meter in de aanbieding is gedefinieerd. Gebeurtenissen moeten binnen een uur aan micro soft worden gerapporteerd. Micro soft berekent klanten op basis van de gebruiks gegevens die door uitgevers zijn gerapporteerd voor de desbetreffende facturerings periode.
- **Gratis proef versie**: kosten voor software licenties die variëren van 30 dagen tot zes maanden, afhankelijk van het type aanbieding. Als uitgevers een gratis proef versie bieden op meerdere abonnementen binnen dezelfde aanbieding, kunnen klanten overschakelen naar een gratis proef versie op een ander abonnement, maar de proef periode wordt niet opnieuw opgestart. Voor de aanbiedingen van virtuele machines worden klanten de kosten van Azure-infra structuur in rekening gebracht voor het gebruik van de aanbieding tijdens een proef periode. Nadat de proef periode is verlopen, worden klanten automatisch in rekening gebracht voor het laatste abonnement dat ze hebben geprobeerd op basis van standaard tarieven, tenzij ze vóór het einde van de proef periode worden geannuleerd.

> [!NOTE]
> Aanbiedingen die worden gefactureerd op basis van verbruik nadat een oplossing is gebruikt, komen niet in aanmerking voor restituties.

Uitgevers die de gebruiks kosten voor een aanbieding willen wijzigen, moeten eerst de aanbieding (of het specifieke abonnement binnen de aanbieding) verwijderen van de commerciële Marketplace. De verwijdering moet worden uitgevoerd in overeenstemming met de vereisten van de [micro soft Publisher-overeenkomst](https://go.microsoft.com/fwlink/?LinkID=699560). Vervolgens kan de uitgever een nieuwe aanbieding (of plan binnen een aanbieding) publiceren waarin de nieuwe gebruiks kosten zijn opgenomen. Zie voor meer informatie over het verwijderen van een aanbieding of plan stoppen om te [verkopen van een aanbieding of abonnement](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).

### <a name="free-contact-me-and-bring-your-own-license-byol-pricing"></a>Gratis, neem contact met mij op en breng uw BYOL-prijzen (uw eigen licentie)

Wanneer u een aanbieding publiceert met de optie nu downloaden (gratis), contact opnemen of BYOL, speelt micro soft geen rol af bij het vergemakkelijken van de verkoop transactie voor uw software licentie kosten. De uitgever houdt 100% van de kosten voor software licenties bij.

### <a name="usage-based-and-subscription-pricing"></a>Prijzen op basis van gebruik en abonnementen

Wanneer u een aanbieding publiceert als een gebruik-of abonnements transactie, biedt micro soft de technologie en services voor het verwerken van software licentie-aankopen, retour neren en terugrekenen. In dit scenario wordt micro soft door de uitgever geautoriseerd om als agent te fungeren voor deze doel einden. De uitgever stelt micro soft in staat om de Software Licensing-trans actie te vergemakkelijken. De uitgever, behoud uw aanwijzing als verkoper, provider, distributeur en licentie gever.

Micro soft stelt klanten in staat om uw software te best Ellen, te bestemmen en te gebruiken, onder voor waarden van zowel de commerciële Marketplace van micro soft als uw licentie overeenkomst voor eind gebruikers. U moet uw eigen gebruiksrecht overeenkomst voor de eind gebruiker opgeven of het [standaard contract](./standard-contract.md) selecteren bij het maken van de aanbieding.

### <a name="free-software-trials"></a>Gratis software-experimenten

Voor scenario's voor het publiceren van Transact kunt u een software licentie gratis beschikbaar maken gedurende 30 tot 120 dagen, afhankelijk van het abonnement. Klanten worden gewijzigd voor het juiste gebruik van Azure-infra structuur.

### <a name="examples-of-pricing-and-store-fees"></a>Voor beelden van prijzen en winkel kosten

**Op basis van gebruik**

Op gebruik gebaseerde prijzen hebben de volgende kosten structuur:

| **De licentie kosten** | **$1,00 per uur** |
|---------|---------|
| Kosten voor Azure-gebruik (D1/1-core) | $0,14 per uur |
| _Klant wordt gefactureerd door micro soft_ | _$1,14 per uur_ |
||

In dit scenario $1,14 factureert micro soft per uur voor het gebruik van uw gepubliceerde VM-installatie kopie.

| **Micro soft-facturen** | **$1,14 per uur**  |
|---------|---------|
| Micro soft betaalt u 80% van uw licentie kosten | $0,80 per uur |
| Micro soft bewaart 20% van de licentie kosten  |  $0,20 per uur |
| Micro soft bewaart 100% van de kosten voor Azure-gebruik | $0,14 per uur |
||

**Neem uw eigen licentie mee (BYOL)**

BYOL heeft de volgende kosten structuur:

| **De licentie kosten** | **Licentie kosten die door u worden onderhandeld en gefactureerd** |
|---------|---------|
|Kosten voor Azure-gebruik (D1/1-core)    |   $0,14 per uur     |
| _Klant wordt gefactureerd door micro soft_ | _$0,14 per uur_ |
||

In dit scenario $0,14 factureert micro soft per uur voor het gebruik van uw gepubliceerde VM-installatie kopie.

| **Micro soft-facturen** | **$0,14 per uur** |
|---------|---------|
| Micro soft behoudt de kosten voor Azure-gebruik | $0,14 per uur |
| Micro soft bewaart 0% van de licentie kosten | $0,00 per uur |
||

**SaaS-app-abonnement**

SaaS-abonnementen kunnen maandelijks of jaarlijks worden geprijsd op basis van een vast bedrag of per gebruiker. Als u de optie  **verkopen via micro soft** inschakelt voor een SaaS-aanbieding, hebt u de volgende kosten structuur:

| **De licentie kosten** | **$100,00 per maand** |
|--------------|---------|
| Kosten voor Azure-gebruik (D1/1-core) | Rechtstreeks aan de uitgever gefactureerd, niet de klant |
| _Klant wordt gefactureerd door micro soft_ | _$100,00 per maand (uitgever moet rekening worden gehouden met de kosten voor het ontstaan of door geven van infra structuur in de licentie kosten)_ |
||

In dit scenario wordt micro soft Bills $100,00 voor uw software licentie en betaalt $80,00 of $90,00 aan u, afhankelijk van of de aanbieding in aanmerking komt voor een gereduceerde winkel service-tarief.

| **Micro soft-facturen** | **$100,00 per maand** |
|---------|---------|
| Micro soft betaalt u 80% van uw licentie kosten <br> \* Micro soft betaalt u 90% van de licentie kosten voor alle gekwalificeerde SaaS-apps | $80,00 per maand <br> \* $90,00 per maand |
| Micro soft bewaart 20% van de licentie kosten <br> \* Micro soft houdt 10% van de licentie kosten voor alle gekwalificeerde SaaS-apps. | $20,00 per maand <br> \* $10,00 |

### <a name="commercial-marketplace-service-fees"></a>Kosten voor commerciële Marketplace-service

Er worden 20% Standard Store-service kosten in rekening gebracht wanneer klanten uw koop aanbieding kopen via de commerciële Marketplace. Zie sectie 5c van de [micro soft Publisher-overeenkomst](https://go.microsoft.com/fwlink/?LinkID=699560)voor meer informatie over deze kosten.

Voor bepaalde Transact-aanbiedingen die u publiceert naar de commerciële Marketplace, kunt u in aanmerking komen voor een lagere winkel service tarief van 10%. Voor een aanbieding die in aanmerking komt, moet deze door micro soft zijn aangewezen als _Azure IP-gemotiveerd_. Voor het einde van elke kalender maand moet aan de voor waarden worden voldaan ten minste vijf werk dagen voordat de kosten voor de lagere Marketplace-service worden ontvangen. Zodra aan de voor waarden is voldaan, worden de gereduceerde service kosten in rekening gebracht voor alle trans acties die ingaan op de eerste dag van de volgende maand, totdat de gemotiveerd status van _Azure IP co-sell_ verloren is gegaan. Zie [vereisten voor co-sell-status](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status)voor meer informatie over de geschiktheid voor het verkopen van IP-adressen.

De lagere kosten voor Marketplace-service zijn van toepassing op Azure IP gemotiveerd SaaS, Vm's, beheerde apps en alle andere gekwalificeerde transactable IaaS-oplossingen die beschikbaar worden gesteld via de commerciële Marketplace. Betaalde SaaS-aanbiedingen die zijn gekoppeld aan een micro soft teams-app of ten minste twee Microsoft 365-invoeg toepassingen (Excel, Power Point, Word, Outlook en share point) en gepubliceerd op Microsoft AppSource kunnen ook in aanmerking komen voor deze korting.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Facturering, betaling, facturering en verzamelingen van klanten

**Facturering en betaling**: u kunt de voorkeurs facturerings methode van de klant gebruiken voor het leveren van abonnements-of [PAYGO](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) software licentie kosten.

**Enterprise overeenkomst**: als de voorkeurs facturerings methode van de klant de micro soft-Enterprise overeenkomst is, worden de kosten voor software licenties gefactureerd op basis van deze facturerings methode als een gespecificeerde kosten, gescheiden van eventuele Azure-specifieke gebruiks kosten.

**Credit cards en maandelijkse factuur**: klanten kunnen betalen met een credit card en een maandelijkse factuur. In dit geval worden de kosten voor software licenties gefactureerd op dezelfde manier als het Enterprise Overeenkomst scenario, als een gespecificeerde kosten, gescheiden van eventuele Azure-specifieke gebruiks kosten.

**Gratis tegoed en monetaire toezeg ging**: sommige klanten kiezen Azure voor de monetaire toezeg ging in de Enterprise overeenkomst of zijn gratis tegoed voor gebruik voor Azure-gebruik. Hoewel deze tegoeden kunnen worden gebruikt om te betalen voor gebruik van Azure, kunnen ze niet worden gebruikt voor het betalen van licentie kosten voor software licenties.

**Facturering en verzamelingen**: de facturering van uitgever software licenties wordt weer gegeven op basis van de door de klant geselecteerde methode voor facturering en volgt de facturerings tijdlijn. Klanten zonder een Enterprise Overeenkomst op locatie worden maandelijks gefactureerd voor software licenties voor Marketplace. Klanten met een Enterprise Overeenkomst worden maandelijks gefactureerd via een factuur die elk kwar taal wordt gepresenteerd.

Wanneer het abonnement of het betalen per gebruik (ook wel op basis van gebruik) prijzen modellen is geselecteerd, fungeert micro soft als de agent van de uitgever en is verantwoordelijk voor alle aspecten van facturering, betaling en verzameling.

### <a name="publisher-payout-and-reporting"></a>Uitbetaling en rapportage van Uitgever

Alle software licentie kosten die door micro soft worden verzameld als agent, zijn onderhevig aan 20% transactie kosten, tenzij anders vermeld en worden afgetrokken op het moment van de uitgever.

Klanten kopen normaal gesp roken gebruik van de Enterprise Overeenkomst of een op een credit card ingeschakelde betalen naar gebruik-overeenkomst. Het overeenkomst type bepaalt de timing van facturering, facturering, verzamelen en uitbetaling.

>[!NOTE]
>Alle rapportage en inzichten voor de optie voor het publiceren van Transact zijn beschikbaar via het gedeelte Analytics van partner Center.

#### <a name="billing-questions-and-support"></a>Vragen en ondersteuning voor facturering

Zie de [micro soft Publisher-overeenkomst](https://go.microsoft.com/fwlink/?LinkID=699560)voor meer informatie en juridisch beleid. Neem contact op met de ondersteuning voor [commerciële Marketplace-Uitgever](https://aka.ms/marketplacepublishersupport)voor hulp bij het vragen over facturering.

## <a name="transact-requirements"></a>Transact-vereisten

In deze sectie worden de vereisten voor trans acties voor verschillende typen aanbiedingen beschreven.

### <a name="requirements-for-all-offer-types"></a>Vereisten voor alle aanbiedings typen

- Er zijn een Microsoft-account en financiële informatie vereist voor de optie voor het publiceren van de Transact, ongeacht het prijs model van de aanbieding.
- Verplichte financiële informatie omvat het uitbetalings account en het BTW-profiel.

Zie [uw commerciële Marketplace-account beheren in partner centrum](manage-account.md)voor meer informatie over het instellen van deze accounts.

### <a name="requirements-for-specific-offer-types"></a>Vereisten voor specifieke aanbiedings typen

De mogelijkheid om te handelen via micro soft is alleen beschikbaar voor de volgende typen commerciële Marketplace-aanbiedingen. Deze lijst bevat de vereisten voor het maken van deze aanbiedings typen die kunnen worden verwerkt in de commerciële Marketplace.

- **Azure-toepassing (oplossings sjabloon en beheerde toepassings abonnementen**: in sommige gevallen worden de gebruiks kosten voor Azure-infra structuur door gegeven aan de klant, onafhankelijk van software licentie kosten, maar op hetzelfde factuur overzicht. Als u echter een beheerd app-plan voor de ISV-infrastructuur kosten configureert, worden de Azure-resources in rekening gebracht voor de uitgever en ontvangt de klant een vast bedrag dat de kosten van infra structuur, software licenties en beheer Services omvat.

- **Virtuele Azure-machine**: Selecteer een gratis, BYOL of op gebruik gebaseerde prijs modellen. Op de Azure-factuur van de klant presenteert micro soft de licentie kosten van de uitgever software afzonderlijk van de onderliggende kosten voor Azure-infra structuur. Kosten voor Azure-infra structuur worden aangestuurd door gebruik te maken van de software van de uitgever.

- **SaaS-toepassing**: moet een multi tenant-oplossing zijn, [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) gebruiken voor verificatie en worden geïntegreerd met de [SaaS-fulfillment-api's](partner-center-portal/pc-saas-fulfillment-api-v2.md). Het gebruik van Azure-infra structuur wordt beheerd en u wordt rechtstreeks gefactureerd aan u (de uitgever). u moet dus rekening doen met de gebruiks kosten voor Azure-infra structuur en software licentie kosten als één kosten item. Zie [een SaaS-aanbieding plannen voor de commerciële Marketplace](plan-saas-offer.md#plans)voor gedetailleerde richt lijnen.

## <a name="private-plans"></a>Privé plannen

U kunt een privé-abonnement maken voor een aanbieding, compleet met onderhandelde, specifieke prijzen of aangepaste configuraties.

Met persoonlijke abonnementen kunt u een hogere of lagere prijs bieden aan specifieke klanten dan de openbaar beschik bare aanbieding. Privé abonnementen kunnen worden gebruikt om korting te bieden of een Premium toe te voegen aan een aanbieding. Privé abonnementen kunnen aan een of meer klanten beschikbaar worden gesteld door hun Azure-abonnement op plan niveau te vermelden.

## <a name="next-steps"></a>Volgende stappen

- Bekijk de publicatie patronen in de online winkel voor beelden van hoe uw oplossing is gekoppeld aan een type en configuratie van een aanbieding.
- [Publicatie handleiding per aanbiedings type](publisher-guide-by-offer-type.md).

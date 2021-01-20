---
title: Micro soft Commercial Marketplace Transact-mogelijkheden
description: In dit artikel worden de prijs-, facturerings-, facturerings-en uitbetalings overwegingen voor de optie commerciële Marketplace Transact beschreven.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 5a2a7abf0227812826428557b86c4468f7693c82
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600389"
---
# <a name="commercial-marketplace-transact-capabilities"></a>De Transact-mogelijkheden voor commerciële Marketplace

In dit artikel worden de prijs-, facturerings-, facturerings-en uitbetalings overwegingen voor de micro soft Commercial Marketplace beschreven.

## <a name="transactions-by-listing-option"></a>De optie trans acties per aanbieding

De uitgever of micro soft is verantwoordelijk voor het beheer van software licentie transacties voor aanbiedingen in de commerciële Marketplace. De optie die u kiest voor uw aanbieding bepaalt wie de trans actie beheert. Zie [Inleiding tot aanbiedings opties](determine-your-listing-type.md) voor Beschik baarheid en uitleg van elke publicatie optie

### <a name="contact-me-free-trial-and-byol-options"></a>Contact opnemen, gratis proef versie en BYOL-opties

Uitgevers kunnen kiezen voor de _contact persoon_ en _gratis proef versie_, opties voor promotie-en gebruikers verwervings doeleinden. Voor sommige aanbiedings typen kunnen uitgevers kiezen voor de optie uw eigen licentie (BYOL) meenemen zodat klanten een abonnement op uw aanbieding kunnen kopen met een licentie die hij of zij rechtstreeks van u heeft gekocht. Met deze opties neemt micro soft niet rechtstreeks deel aan de software licentie transacties van de uitgever en zijn er geen transactie kosten in rekening gebracht.

Uitgevers zijn verantwoordelijk voor de ondersteuning van alle aspecten van de software licentie transacties. Dit omvat, maar is niet beperkt tot order, verwerking, meting, facturering, facturering, betaling en incasso. Met de optie contact opnemen met mij kunnen uitgevers 100% van de licentie kosten voor de uitgever van de software blijven ontvangen van de klant.

### <a name="transact-publishing-option"></a>Optie voor het publiceren van Transact

Door micro soft te verkopen kunt u profiteren van de mogelijkheden van micro soft commerce en biedt een end-to-end-ervaring van detectie en evaluatie tot aankoop en implementatie. Een aanbieding die kan worden transactable is een waarin micro soft de uitwisseling van geld voor een software licentie voor de uitgever vereenvoudigt. Aanbiedingen die kunnen worden verwerkt, worden gefactureerd tegen een bestaand micro soft-abonnement of een credit card, waardoor micro soft Cloud-Marketplace-trans acties kan hosten namens de uitgever.

U kiest de optie Transact wanneer u een nieuwe aanbieding maakt in het partner centrum. Deze optie wordt alleen weer gegeven als Transact beschikbaar is voor het type van uw aanbieding.

## <a name="transact-overview"></a>Overzicht van Transact

Wanneer u de optie Transact gebruikt, maakt micro soft de verkoop van software van derden en de implementatie van sommige aanbiedings typen mogelijk voor het Azure-abonnement van de klant. De uitgever moet rekening houden met de facturering van de kosten voor de infra structuur en uw eigen software licentie kosten wanneer u een prijs model selecteert voor een aanbieding.

De optie voor het publiceren van Transact wordt momenteel ondersteund voor de volgende aanbiedings typen:

- Virtuele machines
- Azure-toepassingen
- SaaS-toepassingen

### <a name="billing-infrastructure-costs"></a>Kosten van facturerings infrastructuur

Voor **virtuele machines** en **Azure-toepassingen** worden Azure-infrastructuur gebruiks kosten in rekening gebracht voor het Azure-abonnement van de klant. De kosten voor het gebruik van de infra structuur zijn geprijsd en worden afzonderlijk van de licentie kosten van de software provider weer gegeven op de factuur van de klant.

Voor **SaaS-apps** moet u de uitgever de Azure-infrastructuur gebruiks kosten en software licentie kosten account als één kosten item.  Het wordt weer gegeven als een vast bedrag voor de klant. Het gebruik van de Azure-infra structuur wordt rechtstreeks beheerd en gefactureerd met de uitgever. De werkelijke gebruiks kosten voor de infra structuur zijn niet zichtbaar voor de klant. Uitgevers willen doorgaans de gebruiks kosten voor Azure-infra structuur bundelen in hun prijzen voor software licenties. Software licentie kosten worden niet gemeten of gebaseerd op het verbruik van de gebruiker.

## <a name="pricing-models"></a>Prijsmodellen

Afhankelijk van de gebruikte transactie optie zijn abonnements kosten als volgt:

- **Nu downloaden (gratis)** : er worden geen kosten in rekening gebracht voor software licenties. Klanten betalen geen Azure Marketplace-kosten voor het gebruik van een gratis aanbieding. Gratis aanbiedingen kunnen niet worden omgezet in een betaald abonnement. Klanten moeten een betaalde aanbieding best Ellen.
- **Bring your own License** (BYOL): alle toepasselijke kosten voor software licenties worden rechtstreeks beheerd tussen de uitgever en de klant. Micro soft passeert alleen de gebruiks kosten voor Azure-infra structuur. Als een aanbieding wordt vermeld in de commerciële Marketplace, worden klanten die toegang krijgen tot of gebruik van de aanbieding buiten de commerciële Marketplace, niet in rekening gebracht voor commerciële Marketplace-kosten.
- **Abonnements prijzen** : de kosten voor software licenties worden weer gegeven als maandelijks of jaarlijks tarief voor terugkerende abonnementen, gefactureerd als een vast tarief of per seat. Nieuwe abonnements kosten worden niet evenredig geclassificeerd voor annuleringen van klanten op de middel lange termijn, of ongebruikte services. De kosten voor het opnieuw uitvoeren van het abonnement kunnen worden gefactureerd als de klant het abonnement in het midden van de abonnements termijn bijupgradet of versnelt.
- **Prijzen op basis van gebruik** : voor aanbiedingen van Azure virtual machine worden klanten in rekening gebracht op basis van de omvang van het gebruik van de aanbieding. Voor installatie kopieën van virtuele machines worden klanten een gratis Azure Marketplace-tarief in rekening gebracht, zoals ingesteld door uitgevers, voor het gebruik van virtuele machines die zijn geïmplementeerd vanuit de VM-installatie kopieën. Het uurtarief kan uniform zijn of variëren tussen de grootten van virtuele machines. Gedeeltelijke uren worden per minuut in rekening gebracht. Abonnementen worden maandelijks gefactureerd.
- **Prijzen in licentie** : voor Azure-toepassing aanbiedingen en SaaS-aanbiedingen kunnen uitgevers de [Marketplace-meet service](./partner-center-portal/marketplace-metering-service-apis.md) gebruiken om verbruik te factureren op basis van de meter dimensies die ze kiezen. Bijvoorbeeld band breedte, tickets of e-mail berichten verwerkt. Uitgevers kunnen een of meer meter dimensies definiëren voor elk abonnement. Uitgevers zijn verantwoordelijk voor het bijhouden van het gebruik van afzonderlijke klanten, waarbij elke meter in de aanbieding is gedefinieerd. Gebeurtenissen moeten binnen een uur aan micro soft worden gerapporteerd. Micro soft berekent klanten op basis van de gebruiks gegevens die door uitgevers zijn gerapporteerd voor de desbetreffende facturerings periode.
- **Gratis proef versie** : kosten voor software licenties die variëren van 30 dagen tot zes maanden, afhankelijk van het type aanbieding. Als uitgevers een gratis proef versie bieden op meerdere abonnementen binnen dezelfde aanbieding, kunnen klanten overschakelen naar een gratis proef versie op een ander abonnement, maar de proef periode wordt niet opnieuw opgestart. Voor de aanbiedingen van virtuele machines worden klanten de kosten van Azure-infra structuur in rekening gebracht voor het gebruik van de aanbieding tijdens een proef periode. Nadat de proef periode is verlopen, worden klanten automatisch in rekening gebracht voor het laatste abonnement dat ze hebben geprobeerd op basis van standaard tarieven, tenzij ze vóór het einde van de proef periode worden geannuleerd.

> [!NOTE]
> Aanbiedingen die worden gefactureerd op basis van verbruik nadat een oplossing is gebruikt, komen niet in aanmerking voor restituties.

Uitgevers die de gebruiks kosten voor een aanbieding willen wijzigen, moeten eerst de aanbieding (of het specifieke abonnement binnen de aanbieding) verwijderen van de commerciële Marketplace. De verwijdering moet worden uitgevoerd in overeenstemming met de vereisten van de [micro soft Publisher-overeenkomst](https://go.microsoft.com/fwlink/?LinkID=699560). Vervolgens kan de uitgever een nieuwe aanbieding (of plan binnen een aanbieding) publiceren waarin de nieuwe gebruiks kosten zijn opgenomen. Zie voor meer informatie over het verwijderen van een aanbieding of plan stoppen om te [verkopen van een aanbieding of abonnement](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).

### <a name="free-contact-me-and-bring-your-own-license-byol-pricing"></a>Gratis, neem contact met mij op en breng uw BYOL-prijzen (uw eigen licentie)

Wanneer u een aanbieding publiceert met de optie nu downloaden (gratis), contact opnemen of BYOL, speelt micro soft geen rol af bij het vergemakkelijken van de verkoop transactie voor uw software licentie kosten. Net als de lijst en de publicatie opties voor gratis proef versies, houdt de uitgever 100% van de software licentie kosten in rekening.

### <a name="usage-based-and-subscription-pricing"></a>Prijzen op basis van gebruik en abonnementen

Bij het publiceren van een aanbieding op basis van een gebruiker of een abonnement, biedt micro soft de technologie en services voor het verwerken van software licentie-aankopen, retour neren en terugstortingen. In dit scenario wordt micro soft door de uitgever geautoriseerd om als agent te fungeren voor deze doel einden. De uitgever stelt micro soft in staat om de Software Licensing-trans actie te vergemakkelijken, waarbij de toewijzing wordt behouden als verkoper, provider, distributeur en licentie gever.

Micro soft stelt klanten in staat om uw software te best Ellen, te bestemmen en te gebruiken, onder voor waarden van zowel de commerciële Marketplace van micro soft als uw licentie overeenkomst voor eind gebruikers. U moet uw eigen gebruiksrecht overeenkomst voor de eind gebruiker opgeven of het [standaard contract](./standard-contract.md) selecteren bij het maken van de aanbieding.

### <a name="free-software-trials"></a>Gratis software-experimenten

Voor scenario's voor het publiceren van Transact kunt u een software licentie gratis beschikbaar maken gedurende 30 tot 120 dagen, afhankelijk van het abonnement. Deze functie korting omvat niet de kosten van het gebruik van Azure-infra structuur dat wordt aangestuurd door het gebruik van de partner oplossing.

### <a name="private-offers"></a>Persoonlijke aanbiedingen

Naast het gebruik van aanbiedings typen en facturerings modellen om een aanbieding te geld verdienen, kunt u een persoonlijke aanbieding uitvoeren, compleet met onderhandelde, gefactureerde prijzen of aangepaste configuraties. Persoonlijke aanbiedingen worden door alle drie de opties voor het publiceren van transacts ondersteund.

Met deze optie kunnen hogere of lagere prijzen worden gemaakt dan voor de openbaar beschik bare aanbieding. U kunt privé aanbiedingen gebruiken om korting te bieden of een Premium toe te voegen aan een aanbieding. U kunt persoonlijke aanbiedingen beschikbaar maken voor een of meer klanten door wit-hun Azure-abonnement weer te geven op het niveau van de aanbieding.

### <a name="commercial-marketplace-service-fees"></a>Kosten voor commerciële Marketplace-service

Er worden 20% Standard Store-service kosten in rekening gebracht wanneer klanten uw koop aanbieding kopen via de commerciële Marketplace. Zie sectie 5c van de [micro soft Publisher-overeenkomst](https://go.microsoft.com/fwlink/?LinkID=699560)voor meer informatie over deze kosten.

Voor bepaalde transactable-aanbiedingen die u publiceert naar de commerciële Marketplace, kunt u in aanmerking komen voor een lagere winkel service tarief van 10%. Voor een aanbieding die in aanmerking komt, moet deze door micro soft zijn aangewezen als Azure IP-gemotiveerd. Voor het einde van elke kalender maand moet aan de voor waarden worden voldaan ten minste vijf werk dagen voordat de kosten voor de lagere Marketplace-service worden ontvangen. Zodra aan de voor waarden is voldaan, worden de gereduceerde service kosten in rekening gebracht voor alle trans acties die ingaan op de eerste dag van de volgende maand, totdat de gemotiveerd status van Azure IP co-sell verloren is gegaan. Zie [vereisten voor co-sell-status](https://aka.ms/CertificationPolicies#3000-requirements-for-co-sell-status)voor meer informatie over de geschiktheid voor het verkopen van IP-adressen.

De lagere kosten voor Marketplace-service zijn van toepassing op Azure IP gemotiveerd SaaS, Vm's, beheerde apps en alle andere gekwalificeerde transactable IaaS-oplossingen die beschikbaar worden gesteld via de commerciële Marketplace. Betaalde SaaS-aanbiedingen die zijn gekoppeld aan een micro soft teams-app of ten minste twee Microsoft 365-invoeg toepassingen (Excel, Power Point, Word, Outlook en share point) en naar Microsoft AppSource worden gepubliceerd, ontvangen ook deze korting.

### <a name="examples"></a>Voorbeelden

**Op basis van gebruik** 

Op gebruik gebaseerde prijzen hebben de volgende kosten structuur:

|De licentie kosten  | $1,00 per uur   |
|---------|---------|
|Kosten voor Azure-gebruik (D1/1-core)    |   $0,14 per uur     |
|*Klant wordt gefactureerd door micro soft*    |  *$1,14 per uur*       |
||

In dit scenario $1,14 factureert micro soft per uur voor het gebruik van uw gepubliceerde VM-installatie kopie.

|Micro soft-facturen  | $1,14 per uur  |
|---------|---------|
|Micro soft betaalt u 80% van uw licentie kosten|   $0,80 per uur     |
|Micro soft bewaart 20% van de licentie kosten  |  $0,20 per uur       |
|Micro soft bewaart 100% van de kosten voor Azure-gebruik | $0,14 per uur |
||

**Neem uw eigen licentie mee (BYOL)**

BYOL heeft de volgende kosten structuur:

|De licentie kosten  | Licentie kosten die door u worden onderhandeld en gefactureerd  |
|---------|---------|
|Kosten voor Azure-gebruik (D1/1-core)    |   $0,14 per uur     |
|*Klant wordt gefactureerd door micro soft*    |  *$0,14 per uur*       |
||

In dit scenario $0,14 factureert micro soft per uur voor het gebruik van uw gepubliceerde VM-installatie kopie.

|Micro soft-facturen  | $0,14 per uur  |
|---------|---------|
|Micro soft behoudt de kosten voor Azure-gebruik    |   $0,14 per uur     |
|Micro soft bewaart 0% van de licentie kosten   |  $0,00 per uur       |
||

**SaaS-app-abonnement**

Deze optie moet worden geconfigureerd om via micro soft te worden verkocht en kan maandelijks of per jaar worden geprijsd op basis van een vast bedrag. Als u de optie **verkopen via micro soft** inschakelt voor een SaaS-aanbieding, hebt u de volgende kosten structuur:

| De licentie kosten       | $100,00 per maand  |
|--------------|---------|
| Kosten voor Azure-gebruik (D1/1-core)    | Rechtstreeks aan de uitgever gefactureerd, niet de klant |
| *Klant wordt gefactureerd door micro soft*    |  *$100,00 per maand (uitgever moet rekening worden gehouden met de kosten voor het ontstaan of door geven van infra structuur in de licentie kosten)*  |
||

In dit scenario betaalt micro soft de $100,00 voor uw software licentie en wordt $80,00 op de Publisher uitgewisseld.

In dit scenario betaalt micro soft de $100,00 voor uw software licentie en wordt $90,00 op de Publisher uitgewisseld:

|Micro soft-facturen  | $100,00 per maand  |
|---------|---------|
|Micro soft betaalt u 80% van uw licentie kosten <br> \* Micro soft betaalt u 90% van de licentie kosten voor alle gekwalificeerde SaaS-apps   |   $80,00 per maand <br> \* $90,00 per maand    |
|Micro soft bewaart 20% van de licentie kosten <br> \* Micro soft houdt 10% van de licentie kosten voor alle gekwalificeerde SaaS-apps.  |  $20,00 per maand <br> \* $10,00     |

### <a name="customer-invoicing-payment-billing-and-collections"></a>Facturering, betaling, facturering en verzamelingen van klanten

**Facturering en betaling** : u kunt de voorkeurs facturerings methode van de klant gebruiken voor het leveren van abonnements-of PAYGO software licentie kosten.

**Enterprise Agreement** : als de voorkeurs facturerings methode van de klant de micro soft-Enterprise Agreement is, worden de kosten voor software licenties gefactureerd op basis van deze facturerings methode als een gespecificeerde kosten, gescheiden van eventuele Azure-specifieke gebruiks kosten.

**Credit cards en maandelijkse factuur** : klanten kunnen ook betalen met een credit card en een maandelijkse factuur. In dit geval worden de kosten voor software licenties gefactureerd op dezelfde manier als het Enterprise Agreement scenario, als een gespecificeerde kosten, gescheiden van eventuele Azure-specifieke gebruiks kosten.

**Gratis tegoeden en Azure-voor uitbetaling** : sommige klanten kiezen voor het probetalen van Azure met Azure-voor uitbetaling (voorheen monetaire toezeg ging genoemd) in het Enterprise Agreement of hebben gratis tegoed voor gebruik met Azure. Hoewel deze tegoeden kunnen worden gebruikt om te betalen voor gebruik van Azure, kunnen ze niet worden gebruikt voor het betalen van licentie kosten voor software licenties.

**Facturering en verzamelingen** : de facturering van uitgever software licenties wordt weer gegeven op basis van de klant-geselecteerde facturerings methode en volgt de facturerings tijdlijn. Klanten zonder een Enterprise Agreement op locatie worden maandelijks gefactureerd voor software licenties voor Marketplace. Klanten met een Enterprise Agreement worden maandelijks gefactureerd via een factuur die elk kwar taal wordt gepresenteerd.

Als er prijs modellen voor abonnementen of betalen per gebruik zijn geselecteerd, fungeert micro soft als de agent van de uitgever en is verantwoordelijk voor alle aspecten van facturering, betaling en verzameling.

### <a name="publisher-payout-and-reporting"></a>Uitbetaling en rapportage van Uitgever

Alle software licentie kosten die door micro soft worden verzameld als agent, zijn onderhevig aan 20% transactie kosten, tenzij anders vermeld en worden afgetrokken op het moment van de uitgever.

Klanten kopen normaal gesp roken gebruik van de Enterprise Agreement of een op een credit card ingeschakelde betalen naar gebruik-overeenkomst. Het overeenkomst type bepaalt de timing van facturering, facturering, verzamelen en uitbetaling.

>[!NOTE]
>Alle rapportage en inzichten voor de optie voor het publiceren van Transact zijn beschikbaar via het gedeelte Analytics van partner Center.

#### <a name="billing-questions-and-support"></a>Vragen en ondersteuning voor facturering

Zie de [micro soft Publisher Agreement](https://go.microsoft.com/fwlink/?LinkID=699560) (beschikbaar in Partner Center) voor meer informatie en juridisch beleid.

Neem contact op met de ondersteuning voor [commerciële Marketplace-Uitgever](https://aka.ms/marketplacepublishersupport)voor hulp bij het vragen over facturering.

## <a name="transact-requirements"></a>Transact-vereisten

In deze sectie worden de vereisten voor trans acties voor verschillende typen aanbiedingen beschreven.

### <a name="requirements-for-all-offer-types"></a>Vereisten voor alle aanbiedings typen

- Er zijn een Microsoft-account en financiële informatie vereist voor de optie voor het publiceren van de Transact, ongeacht het prijs model van de aanbieding.
- Verplichte financiële informatie omvat het uitbetalings account en het BTW-profiel.
- De uitgever moet actief zijn in een [ondersteund land of regio](sell-from-countries.md).

Zie [uw commerciële Marketplace-account beheren in partner centrum](partner-center-portal/manage-account.md)voor meer informatie over het instellen van deze accounts.

### <a name="requirements-for-specific-offer-types"></a>Vereisten voor specifieke aanbiedings typen

De optie voor het publiceren van Transact is alleen beschikbaar voor gebruik met de volgende typen Marketplace-aanbiedingen:

- **Virtuele machine van Azure** : Selecteer een gratis, uw eigen licentie of op gebruik gebaseerde prijs modellen, en presenteer deze als plannen die zijn gedefinieerd op het niveau van de aanbieding. Op de Azure-factuur van de klant presenteert micro soft de licentie kosten van de uitgever software afzonderlijk van de onderliggende kosten voor Azure-infra structuur. Kosten voor Azure-infra structuur worden aangestuurd door het gebruik van de uitgever software.

- **Azure-toepassing: oplossings sjabloon of beheerde app** : moet een of meer virtuele machines inrichten en de som van de prijzen van de virtuele machine ophalen. Voor beheerde apps in één abonnement kan een maandelijks abonnement met een vaste frequentie worden geselecteerd als het prijs model in plaats van de prijzen van de virtuele machine. In sommige gevallen worden gebruiks kosten voor Azure-infra structuur door gegeven aan de klant, onafhankelijk van software licentie kosten, maar op hetzelfde factuur overzicht. Als u echter een beheerde app-aanbieding voor ISV-infrastructuur kosten configureert, worden de Azure-resources gefactureerd aan de uitgever en ontvangt de klant een vast bedrag dat de kosten van infra structuur, software licenties en beheer Services omvat.

- **SaaS-toepassing** : moet een multi tenant oplossing zijn, gebruik [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) voor verificatie en kan worden geïntegreerd met de [SaaS-fulfillment-api's](partner-center-portal/pc-saas-fulfillment-api-v2.md). Het gebruik van Azure-infra structuur wordt beheerd en rechtstreeks aan u (de partner) gefactureerd. u moet dus rekening doen met de gebruiks kosten voor Azure-infra structuur en software licentie kosten als één kosten item. Zie [een nieuwe SaaS-aanbieding maken in de commerciële Marketplace](./create-new-saas-offer.md)voor gedetailleerde richt lijnen.

## <a name="next-steps"></a>Volgende stappen

- Bekijk de geschiktheids vereisten in de sectie publicatie opties per aanbiedings type om de selectie en configuratie van uw aanbieding te volt ooien.
- Bekijk de publicatie patronen in de online winkel voor beelden van hoe uw oplossing is gekoppeld aan een type en configuratie van een aanbieding.

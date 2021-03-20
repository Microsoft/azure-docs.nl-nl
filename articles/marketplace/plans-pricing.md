---
title: Abonnementen en prijzen voor commerciële Marketplace-aanbiedingen
description: Meer informatie over abonnementen voor micro soft-Commercial Marketplace-aanbiedingen in Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: mingshen
ms.author: mingshen
ms.date: 09/02/2020
ms.openlocfilehash: e97171e61814b832e78f7bd815d8d6f341a90840
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91858169"
---
# <a name="plans-and-pricing-for-commercial-marketplace-offers"></a>Abonnementen en prijzen voor commerciële Marketplace-aanbiedingen

Een plan definieert het bereik en de limieten van de aanbieding en de bijbehorende prijzen, indien van toepassing. Afhankelijk van het type aanbieding kunt u bijvoorbeeld regionale markten selecteren en kiezen of een plan zichtbaar is voor het publiek of alleen voor een privé publiek. Sommige aanbiedings typen ondersteunen terugkerende abonnementen, enige ondersteunde prijs op basis van gebruik, en sommige bieden een klant de mogelijkheid om een licentie te kopen die hij of zij rechtstreeks van de uitgever heeft aangeschaft. Dit biedt u de flexibiliteit om uw klanten te voorzien van verschillende technische en prijs opties, indien van toepassing.

U kunt Maxi maal 100 abonnementen maken voor elk aanbod dat abonnementen ondersteunt, en Maxi maal 45 van deze abonnementen kunnen [privé](#plan-visibility)zijn. U kunt betaalde abonnementen alleen maken voor Azure Virtual Machine-aanbiedingen, Azure-toepassingen (beheerde toepassingen) en software as a Service (SaaS)-aanbiedingen. Wanneer u ervoor kiest om een van deze aanbiedingen te verkopen via micro soft (aanbiedingen), moet u ten minste één abonnement maken. U kunt plannen maken voor een aantal van de andere aanbiedings typen, maar de plannen voor deze aanbiedings typen bevatten geen prijs opties.

> [!TIP]
> Een transactable-aanbieding is een waarin micro soft de uitwisseling van geld voor een software licentie voor de uitgever vereenvoudigt.

## <a name="plans-by-offer-type"></a>Plannen per aanbiedings type

In de volgende tabel worden de plan opties voor elk type aanbieding weer gegeven. In de volgende secties vindt u meer informatie over deze opties. 

| Type aanbieding | Abonnementen met prijs opties | Plannen zonder prijs opties | Optie privé publiek |
| ------------ | ------------- | ------------- | ------------- |
| Door Azure beheerde toepassing | &#10004; | | &#10004; |
| Sjabloon voor Azure-oplossing | | &#10004; | &#10004; |
| Azure-container | | &#10004; (BYOL) | |
| Module IoT Edge |  | &#10004; |  |
| Beheerde service |  | &#10004; (BYOL) | &#10004; |
| Software als een dienst | &#10004; |  | &#10004; |
| Azure virtuele machine | &#10004; |  | &#10004; |
|||||

Abonnementen worden niet ondersteund voor de volgende aanbiedings typen:

- Consulting Service
- Dynamics 365 Business Central
- Dynamics 365 Customer engagement & PowerApps
- Dynamics 365 for Operations
- Power BI-app

## <a name="plan-information"></a>Informatie plannen

Elk aanbiedings type vereist andere informatie wanneer u een nieuw abonnement maakt. U vindt koppelingen naar specifieke artikelen in de [publicatie handleiding per aanbieding](publisher-guide-by-offer-type.md). Wanneer u een nieuw abonnement op de **overzichts** pagina van het abonnement hebt gemaakt, ziet u verschillende tabbladen, zoals het plannen van een **lijst** of **prijzen en beschik baarheid** om verschillende Details voor uw abonnement te configureren. Op elk tabblad ziet u de status onvolledig of volledig tijdens het werken met deze velden.

![Illustreert de pagina met de plan vermelding in het partner centrum. De velden plan naam, samen vatting van plan en beschrijving zijn gemarkeerd.](./media/commercial-marketplace-plans/plan-listing-tab.png)

Er zijn enkele algemene details die u moet uitvoeren voor een nieuw plan:

- **Plan-id**: Maak een unieke id voor elk abonnement in deze aanbieding. Gebruik Maxi maal 50 tekens: alleen kleine letters, alfanumerieke tekens, streepjes en onderstrepingen. Deze ID is zichtbaar voor klanten in de product-URL en Azure Resource Manager sjablonen (indien van toepassing). U kunt deze ID niet wijzigen nadat u de aanbieding hebt gepubliceerd.
- **Plan naam**: (bijschrift 1 in de bovenstaande afbeelding). Maak in deze aanbieding een unieke naam voor elk abonnement. Gebruik Maxi maal 50 tekens. De naam van het abonnement wordt gebruikt om software-abonnementen te onderscheiden die deel kunnen uitmaken van dezelfde aanbieding (bijvoorbeeld aanbiedings naam: standaard abonnement en ondernemings abonnement). Klanten zien deze naam wanneer ze bepalen welk abonnement binnen uw aanbieding moet worden geselecteerd.
- **Samen vatting plannen**: (bijschrift 2 in de bovenstaande afbeelding). Deze samen vatting wordt weer gegeven in de zoek resultaten van Azure Marketplace en kan Maxi maal 100 tekens bevatten.
   > [!NOTE]
   > Dit veld is niet van toepassing op SaaS-aanbiedingen.
- **Plan beschrijving**: (bijschrift 3 in de bovenstaande afbeelding). Voeg een plan beschrijving toe waarin wordt uitgelegd wat dit plan uniek is van andere abonnementen voor uw aanbieding. Gebruik Maxi maal 500 tekens. Deze inhoud wordt weer gegeven aan uw klanten op de pagina met de aanbieding (en) wanneer ze door bladeren en een abonnement voor uw aanbieding selecteren.

De naam en beschrijving van het abonnement worden weer gegeven op de aanbiedings pagina in de online winkel (s) van de commerciële Marketplace. In de volgende scherm afbeelding ziet u drie plannen voor een overzicht van SaaS-aanbiedingen in azure Marketplace.

![Illustreert een aanbiedings pagina in het partner centrum. Er worden drie plannen weer gegeven.](./media/commercial-marketplace-plans/offer-listing-page.png)

Zodra u uw plannen hebt gemaakt, wordt op de **overzichts** pagina voor het abonnement een lijst weer gegeven met de naam, id, andere details, huidige publicatie status en eventuele beschik bare acties. De beschik bare acties zijn afhankelijk van de status van uw abonnement en kunnen het volgende omvatten:

- Als de plan status **concept** -concept verwijderen is.
- Als de status van het abonnement **Live** is: verkoop abonnement stoppen of privé publiek synchroniseren.

In de volgende scherm afbeelding ziet u twee concept aanbiedingen.

![Illustreert de overzichts pagina voor het abonnement in partner centrum. Er worden twee abonnementen weer gegeven.](./media/commercial-marketplace-plans/plan-overview-tab.png)

## <a name="pricing-and-availability"></a>Prijzen en beschikbaarheid

De commerciële Marketplace werkt op een agentuur model, waardoor uitgevers prijzen instellen, micro soft billt klanten en micro soft de inkomsten van uitgevers betaalt bij het vastleggen van een tarief van een organisatie. U definieert de markten, zicht baarheid en prijzen van uw aanbod (indien van toepassing) op het tabblad **prijzen en beschik baarheid** of **Beschik baarheid** .

- **Markten**: elk plan moet beschikbaar zijn op ten minste één markt.  U hebt de optie om alleen de landen ' BTW geremitteerd ' te selecteren, waarin micro soft verkoop en belasting namens u verkrijgt.
- **Prijzen**: prijs modellen zijn alleen van toepassing op plannen voor Azure Managed Application, SaaS en Azure Virtual Machine-aanbiedingen. Alle abonnementen voor hetzelfde aanbod moeten hetzelfde prijs model gebruiken.  
- **Plan zicht baarheid**: afhankelijk van het type aanbieding kunt u een persoonlijke doel groep definiëren of de aanbieding of het plan verbergen via Azure Marketplace. Verderop in dit artikel vindt u meer informatie over de [zicht baarheid](#plan-visibility) van een plan.

> [!TIP]
> We raden u aan om plannen te maken die het meest geschikt zijn voor de gebruiks patronen van uw doel klant. Dit reduceert gebruikers vaak van plannen op basis van hun wijzigingen in het gebruik. Zie voor een voor beeld van een SaaS-aanbieding met drie abonnementen op Data limieten voor [beeld aanbieding](./partner-center-portal/saas-metered-billing.md#sample-offer).

### <a name="pricing-models"></a>Prijsmodellen

U moet een prijs model koppelen aan elk abonnement voor de volgende aanbiedings typen. Elk van deze aanbiedings typen heeft verschillende beschik bare prijs modellen:

- Door **Azure beheerde toepassing**: vast tarief (maandelijks) en prijzen op basis van gebruik (afmetingen van service metingen).
- **Software as a Service**: vast tarief (maandelijks of jaarlijks), per gebruiker en prijzen op basis van gebruik (meting van service dimensies). 
- **Virtuele Azure-machine**: Bring your own License (BYOL) en prijzen op basis van gebruik. Voor een prijs model op basis van gebruik kunt u kosten per kern, per kern grootte of per markt en kern grootte gebruiken. Een BYOL-licentie model staat geen extra kosten op basis van gebruik toe.   (Voor BYOL-aanbiedingen voor virtuele machines is geen prijs model vereist.)

Alle abonnementen voor hetzelfde aanbod moeten hetzelfde prijs model gebruiken. Een SaaS-aanbieding kan bijvoorbeeld niet één abonnement hebben dat een vast tempo heeft en een ander abonnement dat per gebruiker is. Zie de documentatie van de specifieke aanbieding voor meer informatie.

Als u al prijzen voor uw abonnement hebt ingesteld in Verenigde Staten dollars (USD) en een andere markt locatie toevoegt, wordt de prijs voor de nieuwe markt berekend op basis van de huidige wissel koersen. Nadat u uw wijzigingen hebt opgeslagen, ziet u een koppeling voor **export prijzen (XLSX)** die u kunt gebruiken om de prijs voor elke markt te bekijken en te wijzigen voordat u deze publiceert.

> [!IMPORTANT]
> Nadat uw aanbieding is gepubliceerd, kan de prijs model keuze niet worden gewijzigd.

Met een forfaitaire SaaS-aanbieding en beheerde toepassingen wordt ondersteuning geboden voor facturering via de Marketplace-meter service. Dit is een op gebruik gebaseerd facturerings model waarmee u niet-standaard eenheden kunt definiëren, zoals band breedte of e-mail berichten, die uw klanten zullen betalen op basis van verbruik. Zie verwante documentatie voor meer informatie over het factureren van data limieten voor [beheerde toepassingen](./partner-center-portal/azure-app-metered-billing.md) en [SaaS-apps](./partner-center-portal/saas-metered-billing.md).

## <a name="custom-prices"></a>Aangepaste prijzen

Als u aangepaste prijzen wilt instellen in een afzonderlijke markt, exporteert, wijzigt en importeert u vervolgens de prijs informatie in het werk blad. U bent verantwoordelijk voor het valideren van deze prijzen en de eigenaar van deze instellingen.

Controleer uw prijzen zorgvuldig voordat u deze publiceert. er zijn enkele beperkingen voor wat er kan worden gewijzigd nadat een plan is gepubliceerd:

- Nadat een plan is gepubliceerd, kan het prijs model niet worden gewijzigd.
- Nadat een facturerings termijn voor een abonnement is gepubliceerd, kan deze later niet meer worden verwijderd.
- Nadat een prijs voor een markt in uw abonnement is gepubliceerd, kunt u deze later niet meer wijzigen.

Prijzen die zijn ingesteld in Verenigde Staten dollars (USD), worden omgezet in de lokale valuta van alle geselecteerde markten met de huidige wissel koersen wanneer deze worden opgeslagen. Valideer deze prijzen vóór de publicatie door het prijs werk blad te exporteren en de prijs op elke geselecteerde markt te bekijken.

1. U moet uw prijs wijzigingen eerst opslaan om het exporteren van prijs gegevens in te scha kelen. Klik onder aan het tabblad **prijzen en beschik baarheid** op **concept opslaan**.
1. Selecteer onder **prijzen** de koppeling **prijs gegevens exporteren** .
1. Open het exportedPrice.xlsx-bestand in micro soft Excel.
1. Breng de gewenste wijzigingen aan in de prijs informatie in het werk blad en sla het op. CSV-bestand.
    > [!NOTE]
    > Mogelijk moet u bewerken in Excel inschakelen voordat u het bestand kunt bijwerken.
1. Selecteer op het tabblad **prijzen en beschik baarheid** onder **prijzen** de koppeling **prijs gegevens importeren** .
1. In het dialoog venster dat wordt weer gegeven, selecteert u **Ja**.
1. Selecteer het exportedPrice.xlsx bestand dat u hebt bijgewerkt en selecteer vervolgens **openen**.

## <a name="plan-visibility"></a>Zicht baarheid van plan

U kunt plannen maken voor sommige aanbiedings typen die openbaar beschikbaar of beschikbaar zijn voor een specifieke (privé) doel groep. Aanbiedingen met persoonlijke abonnementen worden gepubliceerd op de Azure Portal. Meer informatie over privé-abonnementen [vindt u in de micro soft Commercial Marketplace](private-offers.md).

U definieert uw persoonlijke doel groep met behulp van Azure-Tenant-Id's of Azure-abonnement-Id's, afhankelijk van het type aanbieding. U kunt Maxi maal 10 Id's hand matig invoeren of tot 10, 00 abonnements-Id's of Tenant-Id's van 20.000 (indien van toepassing) importeren met een. CSV-bestand. U kunt ook persoonlijke doel groepen definiëren voor advies Services en Dynamics 365-aanbiedingen die geen plannen hebben.

Nadat uw aanbieding is gepubliceerd met een privé-abonnement, kunt u de doel groep bijwerken of ervoor kiezen om het plan beschikbaar te stellen aan iedereen. Nadat een plan is gepubliceerd als zichtbaar voor iedereen, moet het worden weer gegeven voor iedereen en kan het niet meer worden geconfigureerd als een persoonlijk abonnement.

> [!NOTE]
> Een persoonlijke doel groep wijkt af van een preview-doel groep. Op de pagina **Beschik baarheid** voor sommige aanbiedings typen kunt u een doel groep definiëren die een voor beeld van uw aanbieding voordoet voordat de aanbieding in de commerciële Marketplace Live wordt gepubliceerd. Hoewel de aanduiding van een privé-doel groep alleen van toepassing is op een specifiek abonnement, kan de preview-doel groep alle plannen (privé of niet) weer geven, maar alleen tijdens de beperkte preview-periode terwijl het abonnement wordt getest en gevalideerd.

U kunt er ook voor kiezen om het plan volledig te verbergen in azure Marketplace als uw aanbieding wordt geïmplementeerd als onderdeel van een beheerde toepassing of oplossings sjabloon.

## <a name="free-trials"></a>Gratis proefversies

U kunt een gratis proef versie inschakelen voor abonnementen voor transactable Azure virtual machine en SaaS-aanbiedingen.

> [!NOTE]
> In deze sectie worden gratis proef versies van betaalde abonnementen voor aanbiedingen beschreven die worden verkocht via micro soft. Dit wijkt af van de aanbiedingen met een gratis proef versie van partners die ervoor kiezen hun trans acties onafhankelijk te verwerken. Voor de volgende aanbiedings typen kunt u een gratis proef versie maken (die niet is gekoppeld aan een abonnement):
> - Azure virtuele machine 
> - SaaS 
> - Dynamics 365 Business Central
> - Dynamics 365 voor klant betrokkenheid & PowerApps
> - Dynamics 365 for Operations
>
> Zie [uw publicatie optie bepalen](determine-your-listing-type.md)voor meer informatie over de opties voor de aanbieding.

Gratis proef versies worden ondersteund voor alle facturerings modellen, met uitzonde ring van plannen met een Data limiet. SaaS-abonnementen bieden een gratis proef versie van 1 maand. Met Azure virtual machine-abonnementen kunt u 1, 3 of 6 maanden gratis proef versies maken.

Wanneer een klant een gratis proef versie selecteert, verzamelen we de facturerings gegevens, maar we beginnen met het factureren van de klant tot de proef versie is geconverteerd naar een betaald abonnement. Gratis proef versies worden automatisch naar een betaald abonnement aan het eind van de proef versie geconverteerd, tenzij de klant het abonnement annuleert voordat de proef periode afloopt.

Tijdens de proef periode kunnen klanten een van de ondersteunde abonnementen evalueren binnen dezelfde aanbieding waarvoor een gratis proef versie is ingeschakeld. Als ze in dezelfde aanbieding naar een andere proef versie overschakelen, wordt de proef periode niet opnieuw opgestart. Als een klant bijvoorbeeld 15 dagen gebruikmaakt van een gratis proef versie en vervolgens overschakelt naar een andere gratis proef versie voor dezelfde aanbieding, wordt de nieuwe proef periode voor vijf tien dagen gebruikt. De gratis proef versie die de klant heeft geëvalueerd op het moment dat de proef periode afloopt, is het abonnement dat automatisch wordt omgezet naar een betaalde versie.

Nadat een klant een gratis proef versie voor een abonnement heeft geselecteerd, kunnen ze niet converteren naar een betaald abonnement voor dat plan totdat de proef periode is verstreken. Als een klant ervoor kiest om te converteren naar een ander abonnement in de aanbieding die geen gratis proef versie heeft, wordt de conversie uitgevoerd, maar wordt de gratis proef versie onmiddellijk beëindigd en gaan alle gegevens verloren.

> [!NOTE]
> Wanneer een klant begint met de betaling voor een abonnement, kunnen ze niet opnieuw een gratis proef versie ontvangen, zelfs als ze overstappen op een ander abonnement dat gratis proef versies ondersteunt.

Als u informatie wilt verkrijgen over klant abonnementen die momenteel deel nemen aan een gratis proef versie, gebruikt u de nieuwe API `isFreeTrial` -eigenschap, die wordt gemarkeerd als waar of onwaar. Zie de [API voor SaaS Get-abonnementen](./partner-center-portal/pc-saas-fulfillment-api-v2.md#get-subscription)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie over het toevoegen of bijwerken van plannen voor een bestaande aanbieding [een bestaande aanbieding in de commerciële Marketplace bijwerken](./partner-center-portal/update-existing-offer.md).
- Zie voor meer informatie over transactie opties en de bijbehorende prijs modellen [commerciële Marketplace Transact-mogelijkheden](./marketplace-commercial-transaction-capabilities-and-considerations.md).

---
title: Een beheerde service aanbieding voor de micro soft Commercial Marketplace plannen
description: Een nieuwe beheerde service aanbieding voor Azure Marketplace plannen met behulp van het commerciële Marketplace-programma in micro soft Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.date: 12/23/2020
ms.openlocfilehash: f096e53f8054039f361bde1c5f2adffac615c53d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100371937"
---
# <a name="how-to-plan-a-managed-service-offer-for-the-microsoft-commercial-marketplace"></a>Een beheerde service aanbieding plannen voor de micro soft Commercial Marketplace

In dit artikel worden de vereisten geïntroduceerd voor het publiceren van een beheerde service aanbieding naar de micro soft Commercial Marketplace via partner Center.

Beheerde services zijn Azure Marketplace-aanbiedingen waarmee cross-tenants en beheer van meerdere tenants met Azure Lighthouse mogelijk zijn. Zie [Wat is Azure Lighthouse?](../lighthouse/overview.md) voor meer informatie. Wanneer een klant een beheerd service aanbod koopt, kunnen ze een of meer abonnementen of resource groepen delegeren. U kunt vervolgens aan deze resources werken met behulp van de beheer mogelijkheden voor [Azure-gedelegeerde resources](../lighthouse/concepts/azure-delegated-resource-management.md) van Azure Lighthouse.

## <a name="eligibility-requirements"></a>Geschiktheidsvereisten

Als u een beheerd service aanbod wilt publiceren, moet u een goud of zilver micro soft-competentie hebben behaald in het Cloud platform. Deze competentie toont uw expertise aan klanten. Zie [Microsoft Partner Network-competenties](https://partner.microsoft.com/membership/competencies)voor meer informatie.

Aanbiedingen moeten voldoen aan alle toepasselijke [certificerings beleidsregels voor commerciële Marketplace](/legal/marketplace/certification-policies) om te worden gepubliceerd op Azure Marketplace.

## <a name="customer-leads"></a>Leads van klanten

U moet uw aanbieding verbinden met het CRM-systeem (Customer Relationship Management) om klant gegevens te verzamelen. De klant wordt gevraagd om toestemming te krijgen om hun gegevens te delen. Deze klant gegevens, samen met de naam van de aanbieding, de ID en de online winkel waar ze uw aanbieding vinden, worden verzonden naar het CRM-systeem dat u hebt geconfigureerd. De commerciële Marketplace ondersteunt verschillende soorten CRM-systemen, samen met de optie voor het gebruik van een Azure-tabel of het configureren van een HTTPS-eind punt met behulp van energie automatisering.

U kunt op elk gewenst moment een CRM-verbinding toevoegen of wijzigen tijdens of na het maken van de aanbieding. Zie [leads van klanten van uw aanbieding voor commerciële Marketplace](partner-center-portal/commercial-marketplace-get-customer-leads.md)voor gedetailleerde richt lijnen.

## <a name="legal-contracts"></a>Juridische contracten

Op de pagina eigenschappen van partner centrum wordt u gevraagd om **voor waarden te verstrekken voor** het gebruik van uw aanbieding. U kunt uw voor waarden rechtstreeks invoeren in het partner centrum of de URL opgeven waar ze kunnen worden gevonden. Klanten moeten deze voor waarden accepteren voordat ze uw aanbieding kopen.

## <a name="offer-listing-details"></a>Details aanbiedings vermelding

Wanneer u een beheerde service aanbieding in het partner centrum maakt, voert u tekst, afbeeldingen, documenten en andere details van de aanbieding in. Dit is wat klanten te zien krijgen wanneer ze uw aanbieding op Azure Marketplace ontdekken. Zie het volgende voorbeeld:

:::image type="content" source="media/example-managed-service.png" alt-text="Illustreert hoe een beheerd service aanbod wordt weer gegeven op Azure Marketplace.":::

**Beschrijvingen van aanroepen**

1. Logo
1. Name
1. Korte beschrijving
1. Categorieën
1. Juridische contracten en privacybeleid
1. Description
1. Scherm afbeeldingen/Video's
1. Handige koppelingen

Hier volgt een voor beeld van hoe de aanbiedings vermelding wordt weer gegeven in de Azure Portal:

:::image type="content" source="media/example-managed-service-azure-portal.png" alt-text="Illustreert hoe deze aanbieding wordt weer gegeven in de Azure Portal.":::

**Beschrijvingen van aanroepen**

1. Naam
2. Beschrijving
3. Handige koppelingen
4. Scherm afbeeldingen/Video's

> [!NOTE]
> Als uw aanbieding een andere taal dan Engels heeft, kan de aanbiedings vermelding zich in die taal bevinden, maar moet de beschrijving beginnen of eindigen met de Engelse zin "deze service is beschikbaar in de &lt; taal van uw aanbiedings inhoud>". U kunt ook ondersteunende documenten opgeven in een andere taal dan die wordt gebruikt in de details van de aanbieding.

Om uw aanbieding gemakkelijker te maken, moet u enkele van deze items vooraf voorbereiden. De volgende items zijn vereist tenzij anders vermeld.

**Naam**: dit wordt weer gegeven als de titel van uw aanbieding in de commerciële Marketplace. De naam kan worden aangemerkt. Het mag geen emojis bevatten (tenzij ze de symbolen van het handels merk en copyright zijn) en moet beperkt zijn tot 50 tekens.

**Samen vatting van zoek resultaten**: Beschrijf het doel of doel van uw aanbieding in 100 tekens of minder. Deze samen vatting wordt gebruikt in de zoek resultaten van de commerciële Marketplace-vermelding. Het mag niet identiek zijn aan de titel. Houd rekening met uw beste SEO-tref woorden.

**Korte beschrijving**: Geef een korte beschrijving van uw aanbieding op (maxi maal 256 tekens). Deze wordt weer gegeven in de aanbieding van uw aanbieding in Azure Portal.

**Beschrijving**: Beschrijf uw aanbieding in 3.000 tekens of minder. Deze beschrijving wordt weer gegeven in de commerciële Marketplace-lijst. Overweeg het opnemen van een toegevoegde waarde, een belang rijk voor deel, categorie-of branche koppelingen en eventuele nood zakelijke mede delingen.

Hier volgen enkele tips voor het schrijven van uw beschrijving:

* Geef duidelijk de waarde van uw aanbieding in de eerste paar zinnen op, waaronder:
    * Het type gebruiker dat van de aanbieding profiteert.
    * Wat klanten nodig hebben of de adressen van de aanbieding.
* Houd er rekening mee dat de eerste paar zinnen kunnen worden weer gegeven in Zoek resultaten.
* Branchespecifieke woorden lijst gebruiken.

U kunt HTML-tags gebruiken om uw beschrijving op te maken. Zie [HTML-tags die worden ondersteund in de beschrijvingen van de commerciële Marketplace-aanbieding](./supported-html-tags.md)voor meer informatie over HTML-opmaak.

**Koppeling naar privacybeleid**: Geef een URL naar het privacybeleid op dat wordt gehost op uw site. U bent verantwoordelijk om ervoor te zorgen dat uw aanbieding voldoet aan de wetten en voor schriften van de privacy en om een geldig privacybeleid te bieden.

**Nuttige koppelingen** (optioneel): Upload aanvullende online documenten over uw aanbieding.

**Contact gegevens**: Geef de naam, het e-mail adres en het telefoon nummer van twee personen in uw bedrijf op (u kunt er een van maken): een ondersteunings contact en een technische contact persoon. We gebruiken deze informatie om met u te communiceren over uw aanbieding. Deze informatie wordt niet weer gegeven aan klanten, maar kan worden verstrekt aan de Cloud Solution Provider (CSP)-partners

**Ondersteunings-url's** (optioneel): als u ondersteuning voor websites voor Azure Global klanten en/of Azure Government klanten hebt, moet u deze url's opgeven.

**Media voor Marketplace-logo's**: Geef een PNG-bestand op voor het logo van de grote grootte van uw aanbieding. Het partner centrum gebruikt het om middel grote en kleine logo's te maken. U kunt deze logo's desgewenst later vervangen door een andere afbeelding.

* Het grote logo (van 216 x 216 tot 350 x 350 px) wordt weer gegeven in uw aanbieding op Azure Marketplace.
* Het gemiddelde logo (90 x 90 px) wordt weer gegeven wanneer er een nieuwe resource wordt gemaakt.
* Het kleine logo (48 x 48 px) wordt gebruikt op de Azure Marketplace-Zoek resultaten.

Volg deze richt lijnen voor uw logo's:

* Zorg ervoor dat de afbeelding niet is uitgerekt.
* Het Azure-ontwerp heeft een eenvoudig kleurenpalet. Beperk het aantal primaire en secundaire kleuren in uw logo.
* De Azure Portal kleuren zijn wit en zwart. Gebruik deze niet als de achtergrond van uw logo. We raden u aan eenvoudige primaire kleuren te maken die uw logo prominent opvallen.
* Als u een transparante achtergrond gebruikt, moeten het logo en de tekst niet wit, zwart of blauw zijn.
* Het uiterlijk van uw logo moet plat zijn. Vermijd kleur overgangen. Plaats geen tekst in het logo, ook niet de naam van uw bedrijf of merk.

**Marketplace-media – scherm afbeeldingen** (optioneel): Voeg Maxi maal vijf installatie kopieën toe die laten zien hoe uw aanbieding werkt. Alle afbeeldingen moeten 1280 x 720 pixels groot zijn en in. PNG-indeling.

**Marketplace-media – Video's** (optioneel): Upload Maxi maal vijf Video's die uw aanbieding aantonen. De Video's moeten worden gehost op YouTube of Vimeo en hebben een miniatuur (1280 x 720 PNG-bestand).

## <a name="preview-audience"></a>Voor beeld van doel groep

Een preview-doel groep heeft toegang tot uw aanbieding voordat deze wordt gepubliceerd op Azure Marketplace, zodat deze kan worden getest. Op de pagina **voor beeld van een doel groep** van partner Center kunt u een beperkte preview-doel groep definiëren.

> [!NOTE]
> Een preview-doel groep wijkt af van een privé-abonnement. Een privé-abonnement is één die u alleen beschikbaar maakt voor een specifieke doel groep. Zo kunt u een aangepast plan met specifieke klanten onderhandelen.

U kunt uitnodigingen verzenden naar e-mail adressen van micro soft-accounts (MSA) of Azure Active Directory (Azure AD). Voeg Maxi maal 10 e-mail adressen hand matig toe of importeer Maxi maal 20 met een CSV-bestand. Als uw aanbieding al Live is, kunt u nog steeds een preview-doel groep definiëren om eventuele wijzigingen of updates naar uw aanbieding te testen.

## <a name="plans-and-pricing"></a>Abonnementen en prijzen

Voor aanbiedingen voor beheerde services is ten minste één abonnement vereist. In een plan worden het oplossings bereik, de limieten en de bijbehorende prijzen gedefinieerd, indien van toepassing. U kunt meerdere plannen maken voor uw aanbieding om uw klanten verschillende technische en prijs opties te geven. Zie [Abonnementen en prijzen voor commerciële Marketplace-aanbiedingen](plans-pricing.md)voor algemene richt lijnen met betrekking tot abonnementen, waaronder persoonlijke abonnementen.

Beheerde services ondersteunen slechts één prijs model: **Bring your own License (BYOL)**. Dit betekent dat u uw klanten rechtstreeks factureert en micro soft brengt u geen kosten in rekening.

## <a name="next-steps"></a>Volgende stappen

* [Een aanbieding voor beheerde service maken](./create-managed-service-offer.md)
* [Best practices voor aanbiedingsvermeldingen](./gtm-offer-listing-best-practices.md)
---
title: Dynamics 365-aanbiedingen voor Microsoft AppSource
description: Dynamics 365-aanbiedingen voor Microsoft AppSource
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: vamahtan
ms.author: vamahtan
ms.date: 04/16/2021
ms.openlocfilehash: c5746b70f84ff68ea578ba141cc5251cc5ce07ea
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819379"
---
# <a name="plan-a-microsoft-dynamics-365-offer"></a>Een Microsoft Dynamics 365-aanbieding plannen

In dit artikel worden de verschillende opties en functies van een Dynamics 365-aanbieding in Microsoft AppSource commerciële marketplace uitgelegd. AppSource bevat aanbiedingen die zijn gebaseerd op Microsoft 365, Dynamics 365, PowerApps en Power BI. Met AppSource kunnen betaalde aanbiedingen *(Nu downloaden),* lijst *(contact opnemen)* en proefabonnementen *(Nu proberen)* worden vermeld.

Voordat u begint, maakt u een commerciële marketplace-account in [Partner Center](./partner-center-portal/create-account.md) en zorgt u ervoor dat het is ingeschreven in het commerciële marketplace-programma. Lees ook het [publicatieproces en de richtlijnen.](/office/dev/store/submit-to-appsource-via-partner-center)

## <a name="licensing-options"></a>Licentieopties

Wanneer u zich voorbereidt op het publiceren van een nieuwe aanbieding, moet u beslissen welke licentieoptie u moet kiezen. Hiermee wordt bepaald welke aanvullende informatie u later moet verstrekken bij het maken van de aanbieding in Partner Center.

Dit zijn de beschikbare licentieopties voor Dynamics 365-aanbiedingen:

| Optie voor licentieverlening | Transactieproces |
| --- | --- |
| Nu downloaden (gratis) | Vermeld uw aanbieding gratis aan klanten. |
| Gratis proefversie (vermelding) | Bied uw klanten een gratis proefversie van één, drie of zes maanden. Aanbiedingen met gratis proefversies worden gemaakt, beheerd en geconfigureerd door uw service en hebben geen abonnementen die worden beheerd door Microsoft. |
| Contact met mij opnemen | Verzamel contactgegevens van klanten door verbinding te maken met uw CRM-systeem (Customer Relationship Management). De klant wordt gevraagd toestemming te geven om de gegevens te delen. Deze klantgegevens, samen met de naam van de aanbieding, de id en de marketplace-bron waar ze uw aanbieding hebben gevonden, worden verzonden naar het CRM-systeem dat u hebt geconfigureerd. Zie de sectie Klanten leads van  de pagina Aanbieding instellen van uw aanbiedingstype voor meer informatie over het configureren **van uw** CRM. |
|

## <a name="test-drive"></a>Test drive

[!INCLUDE [Test drives section](includes/test-drives.md)]

## <a name="customer-leads"></a>Leads van klanten

Wanneer u een aanbieding publiceert op de commerciële marketplace met Partner Center, moet u deze verbinden met uw CRM-systeem (Customer Relationship Management). Hiermee kunt u contactgegevens van klanten ontvangen zodra iemand interesse heeft in uw product of uw product gebruikt. Verbinding maken met een CRM is vereist als u een crm-test drive; Anders is verbinding maken met een CRM optioneel. Partner Center biedt ondersteuning voor Azure Table, Dynamics 365 Customer Engagement, HTTPS-eindpunt, Marketo en Salesforce.

## <a name="legal"></a>Juridisch

Maak uw **voorwaarden**. Klanten moeten deze accepteren voordat ze uw aanbieding kunnen proberen. Microsoft heeft een standaardcontract, maar is niet van toepassing op Dynamics 365-aanbiedingen.

## <a name="offer-listing-details"></a>Details aanbiedingsvermelding

Hier is een voorbeeld van hoe aanbiedingsgegevens worden weergegeven in Microsoft AppSource (alle vermelde prijzen zijn alleen bedoeld voor voorbeelddoeleinden en weerspiegelen niet de werkelijke kosten):

:::image type="content" source="media/dynamics-365/example-dynamics-365-operations.png" alt-text="Illustreert hoe deze aanbieding wordt weergegeven in Microsoft AppSource.":::

> [!NOTE]
> Aanbiedingsvermeldingsinhoud hoeft niet in het Engels te zijn als de beschrijving van de aanbieding begint met de zin 'Deze toepassing is alleen beschikbaar in [niet-Engelse taal]'.

Bereid deze items van tevoren voor om uw aanbieding gemakkelijker te maken. Alle zijn vereist, behalve indien vermeld.

- **Naam:** de naam wordt weergegeven als de titel van uw aanbiedingsvermelding in de commerciële marketplace. De naam kan handelsmerken zijn. Het mag geen emoji's bevatten (tenzij het handelsmerk- en auteursrechtsymbolen zijn) en is beperkt tot 50 tekens.
- **Samenvatting van zoekresultaten:** het doel of de functie van uw aanbieding als één zin zonder regelbreak in 100 tekens of minder. Dit wordt gebruikt in de zoekresultaten van de commerciële marketplace.
- **Beschrijving:** deze beschrijving wordt weergegeven in het overzicht van de commerciële marketplace. U kunt een waardepropositie, belangrijke voordelen, beoogde gebruikersbasis, alle categorieën of branchekoppelingen, in-app-aankoopmogelijkheden, eventuele vereiste openbaarmakingen en een koppeling voor meer informatie toevoegen. Dit tekstvak bevat besturingselementen voor teksteditors met uitgebreide tekst om uw beschrijving aantrekkelijker te maken. Gebruik eventueel HTML-tags voor opmaak.
- **Trefwoorden zoeken** (optioneel) : maximaal drie zoektermen die klanten kunnen gebruiken om uw aanbieding te vinden. Neem de naam en beschrijving van **de aanbieding** **niet op;** die tekst wordt automatisch opgenomen in de zoekopdracht.
- **Product waarmee uw app werkt** (optioneel) : de namen van maximaal drie producten waarmee uw aanbieding werkt.
- **Koppelingen naar Help en privacybeleid:** de URL's voor de help van uw aanbieding en het privacybeleid van uw bedrijf. U bent verantwoordelijk om ervoor te zorgen dat uw app voldoet aan de privacywetgeving en -voorschriften.
- **Contactgegevens**
  - **Contactpersoon voor** ondersteuning: de naam, telefoon en e-mail die Microsoft-partners gebruiken wanneer uw klanten tickets openen. Voeg de URL voor uw ondersteuningswebsite toe.
  - **Technische contactpersoon:** de naam, telefoon en e-mail die Microsoft rechtstreeks kan gebruiken wanneer er problemen zijn met uw aanbieding. Deze contactgegevens worden niet vermeld in de commerciële marketplace.
- **Ondersteunende documenten:** maximaal drie klantgerichte documenten, zoals whitepapers, checklists of PowerPoint-presentaties, in PDF-vorm.
- **Media**
    - **Logo's:** een PNG-bestand voor het **logo Large.** Partner Center gebruikt deze om andere vereiste logogrootten te maken. U kunt deze eventueel later vervangen door andere afbeeldingen.
    - **Schermopnamen:** ten minste één en maximaal vijf schermafbeeldingen die laten zien hoe uw aanbieding werkt. Afbeeldingen moeten 1280 x 720 pixels in PNG-indeling zijn en een bijschrift bevatten.
    - **Video's** (optioneel) : maximaal vier video's die uw aanbieding demonstreren. Neem een naam, URL voor YouTube of Vimeo en een PNG-miniatuur van 1280 x 720 pixels op.

> [!Note]
> Uw aanbieding moet voldoen aan het algemene [certificeringsbeleid voor de commerciële marketplace](/legal/marketplace/certification-policies#100-general) om te worden gepubliceerd op de commerciële marketplace.

## <a name="additional-sales-opportunities"></a>Aanvullende verkoopkansen

U kunt ervoor kiezen om te kiezen voor door Microsoft ondersteunde marketing- en verkoopkanalen. Wanneer u uw aanbieding in Partner Center, ziet u twee tabbladen aan het einde van het proces voor **co-sell met Microsoft**. Met deze optie kunnen Microsoft-verkoopteams uw in aanmerking komende ip-oplossing voor co-verkoop overwegen bij het evalueren van de behoeften van hun klanten. Zie [Optie voor co-verkoop in Partner Center](commercial-marketplace-co-sell.md) voor gedetailleerde informatie over het voorbereiden van uw aanbieding voor evaluatie.

## <a name="next-steps"></a>Volgende stappen

Nadat u de hierboven beschreven planningsitems hebt bekeken, selecteert u een van de volgende opties (ook beschikbaar in de inhoudsopgave aan de linkerkant) om te beginnen met het maken van uw aanbieding.

| Publicatiehandleiding    | Notities  |
| :------------------- | :-------------------|
| [Dynamics 365 for Operations](partner-center-portal/create-new-operations-offer.md) | Wanneer u bouwt voor Enterprise Edition, bekijkt u eerst deze aanvullende [publicatieprocessen en richtlijnen.](/dynamics365/fin-ops-core/dev-itpro/lcs-solutions/lcs-solutions-app-source) |
| [Dynamics 365 for Business Central](partner-center-portal/create-new-business-central-offer.md) |   |
| [Dynamics 365 for Customer Engagement & Power Apps](dynamics-365-customer-engage-offer-setup.md) | Bekijk eerst deze aanvullende [publicatieprocessen en richtlijnen.](/dynamics365/customer-engagement/developer/publish-app-appsource) |
| [Power BI](/partner-center-portal/create-power-bi-app-offer.md) | Bekijk eerst deze aanvullende [publicatieprocessen en richtlijnen.](/power-bi/developer/office-store) |
|

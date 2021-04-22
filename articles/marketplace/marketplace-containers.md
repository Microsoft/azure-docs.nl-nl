---
title: Publicatiehandleiding voor containeraanbiedingen in Azure Marketplace
description: In dit artikel worden de vereisten beschreven voor het publiceren van containeraanbiedingen in Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/30/2021
ms.openlocfilehash: 2dd44706e77252d3d00721c0e1cda22707eb28c3
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107887899"
---
# <a name="plan-an-azure-container-offer"></a>Een Azure-containeraanbieding plannen

Azure Container biedt u hulp bij het publiceren van uw container-Azure Marketplace. Gebruik deze handleiding om inzicht te krijgen in de vereisten voor dit type aanbieding.

Azure-containeraanbiedingen zijn transactieaanbiedingen die worden geïmplementeerd en gefactureerd via Azure Marketplace. De lijstoptie die een gebruiker ziet, is **Nu krijgen.**

Gebruik het azure-containeraanbiedingstype wanneer uw oplossing een Docker-containerafbeelding is die is ingesteld als een Op Kubernetes gebaseerde Azure Container-instantie.

> [!NOTE]
> Een Azure Container Instance is een run-time docker-exemplaar dat de snelste en eenvoudigste manier biedt om een container in Azure uit te voeren, zonder dat u virtuele machines hoeft te beheren of een service op een hoger niveau hoeft te gebruiken. Container-exemplaren kunnen rechtstreeks in Azure worden geïmplementeerd of worden geseed door Azure Kubernetes Services of Azure Kubernetes Service Engine.  

## <a name="licensing-options"></a>Licentieopties

Dit zijn de beschikbare licentieopties voor Azure Container-aanbiedingen:

| Optie voor licentieverlening | Transactieproces |
| --- | --- |
| Gratis | Vermeld uw aanbieding gratis aan klanten. |
| BYOL (Bring Your Own License) | Met de optie Bring Your Own Licensing kunnen uw klanten bestaande softwarelicenties naar Azure brengen.\* |
|

\* Als uitgever ondersteunt u alle aspecten van de softwarelicentietransactie, waaronder (maar niet beperkt tot) order, uitvoering, meting, facturering, facturering, betaling en verzameling.

## <a name="customer-leads"></a>Leads van klanten

Wanneer u een aanbieding publiceert naar de commerciële marketplace met Partner Center, moet u deze verbinden met uw CRM-systeem (Customer Relationship Management). Hiermee kunt u contactgegevens van klanten ontvangen zodra iemand interesse heeft in uw product of uw product gebruikt. Verbinding maken met een CRM is vereist als u een crm-test drive; anders is verbinding maken met een CRM optioneel. Partner Center biedt ondersteuning voor Azure Table, Dynamics 365 Customer Engagement, HTTPS-eindpunt, Marketo en Salesforce.

## <a name="legal-contracts"></a>Juridische contracten

Om het inkoopproces voor klanten te vereenvoudigen en de juridische complexiteit voor softwareleveranciers te verminderen, biedt Microsoft een standaardcontract dat u kunt gebruiken voor uw aanbiedingen in de commerciële marketplace. Wanneer u uw software onder het standaardcontract aanbiedt, hoeven klanten deze slechts één keer te lezen en te accepteren en hoeft u geen aangepaste voorwaarden te maken.

U kunt ervoor kiezen om uw eigen voorwaarden op te geven in plaats van het standaardcontract. Klanten moeten deze voorwaarden accepteren voordat ze uw aanbieding kunnen proberen.

## <a name="offer-listing-details"></a>Details aanbiedingsvermelding

> [!NOTE]
> Aanbiedingsvermeldingsinhoud hoeft niet in het Engels te zijn als de beschrijving van de aanbieding begint met de zin 'Deze toepassing is alleen beschikbaar in [niet-Engelse taal]'.

Bereid deze items van tevoren voor om uw aanbieding gemakkelijker te maken. Alle zijn vereist, behalve indien vermeld.

- **Naam:** de naam wordt weergegeven als de titel van uw aanbiedingsvermelding in de commerciële marketplace. De naam kan handelsmerken zijn. Het mag geen emoji's bevatten (tenzij het handelsmerk- en auteursrechtsymbolen zijn) en is beperkt tot 50 tekens.
- **Samenvatting van zoekresultaten:** het doel of de functie van uw aanbieding als één zin zonder regelbreaks in 100 tekens of minder. Dit wordt gebruikt in de zoekresultaten van de commerciële marketplace.
- **Korte beschrijving:** details van het doel of de functie van de aanbieding, geschreven in tekst zonder regelbreak. Deze wordt weergegeven op de detailpagina van uw aanbieding.
- **Beschrijving:** deze beschrijving wordt weergegeven in het overzicht van de commerciële marketplace. U kunt een waardepropositie, belangrijke voordelen, beoogde gebruikersbasis, alle categorieën of branchekoppelingen, in-app-aankoopmogelijkheden, eventuele vereiste openbaarmakingen en een koppeling voor meer informatie toevoegen. Dit tekstvak bevat besturingselementen voor de editor voor tekst met uitgebreide tekst om uw beschrijving aantrekkelijker te maken. Gebruik eventueel HTML-tags voor opmaak.
- **Koppeling naar privacybeleid:** de URL voor het privacybeleid van uw bedrijf. U bent verantwoordelijk om ervoor te zorgen dat uw app voldoet aan de privacywetgeving en -voorschriften.
- **Nuttige koppelingen** (optioneel): koppelingen naar verschillende resources voor gebruikers van uw aanbieding. Bijvoorbeeld forums, veelgestelde vragen en opmerkingen bij de release.
- **Contactgegevens**
  - **Contactpersoon voor** ondersteuning: de naam, telefoon en e-mail die Microsoft-partners gebruiken wanneer uw klanten tickets openen. Voeg de URL voor uw ondersteuningswebsite toe.
  - **Technische contactpersoon:** de naam, telefoon en e-mail die Microsoft rechtstreeks kan gebruiken wanneer er problemen zijn met uw aanbieding. Deze contactgegevens worden niet vermeld in de commerciële marketplace.
  - **Contactpersoon voor het CSP-programma** (optioneel): de naam, de telefoon en het e-mailadres als u zich voor het CSP-programma hebt gekozen, zodat deze partners contact met u kunnen opnemen als u vragen hebt. U kunt ook een URL naar uw marketingmateriaal opnemen.
- **Media**
    - **Logo's:** een PNG-bestand voor het **logo Large.** Partner Center gebruikt deze om andere vereiste logogrootten te maken. U kunt deze eventueel later vervangen door andere afbeeldingen.
    - **Schermopnamen:** ten minste één en maximaal vijf schermafbeeldingen die laten zien hoe uw aanbieding werkt. Afbeeldingen moeten 1280 x 720 pixels in PNG-indeling zijn en een bijschrift bevatten.
    - **Video's** (optioneel) : maximaal vier video's die uw aanbieding demonstreren. Voeg een naam, URL voor YouTube of Vimeo en een PNG-miniatuur van 1280 x 720 pixels toe.

> [!Note]
> Uw aanbieding moet voldoen aan het algemene [certificeringsbeleid voor de commerciële marketplace](/legal/marketplace/certification-policies#100-general) om te worden gepubliceerd op de commerciële marketplace.

## <a name="preview-audience"></a>Preview-doelgroep

Een preview-doelgroep heeft toegang tot uw aanbieding voordat deze live wordt gepubliceerd in de online winkels om de end-to-end-functionaliteit te testen voordat u deze live publiceert. Op de **pagina Preview-doelgroep** kunt u een beperkte preview-doelgroep definiëren.

U kunt uitnodigingen verzenden naar azure-abonnements-ID's. Voeg maximaal 10 ID's handmatig toe of importeer maximaal 100 met een CSV-bestand. Als uw aanbieding al live is, kunt u nog steeds een preview-doelgroep definiëren voor het testen van wijzigingen of updates in uw aanbieding.

## <a name="plans-and-pricing"></a>Abonnementen en prijzen

Voor containeraanbiedingen is ten minste één plan vereist. Een plan definieert het bereik en de limieten van de oplossing. U kunt meerdere abonnementen voor uw aanbieding maken om uw klanten verschillende technische en licentieopties te bieden. 

Containers ondersteunen twee licentiemodellen: Gratis of Bring Your Own License (BYOL). BYOL betekent dat u uw klanten rechtstreeks factureerd en dat Microsoft u geen kosten in rekening zal brengen. Microsoft doorstaat alleen de kosten voor het gebruik van de Azure-infrastructuur. Zie Commerciële [marketplace-mogelijkheden voor meer informatie.](marketplace-commercial-transaction-capabilities-and-considerations.md)

## <a name="additional-sales-opportunities"></a>Aanvullende verkoopkansen

U kunt ervoor kiezen om te kiezen voor door Microsoft ondersteunde marketing- en verkoopkanalen. Wanneer u uw aanbieding in Partner Center, ziet u twee tabbladen aan het einde van het proces:

- **Opnieuw verkopen via CSP's:** laat Microsoft CSP-partners (Cloud Solution Providers) uw oplossing opnieuw verkopen als onderdeel van een gebundelde aanbieding. Zie voor meer informatie over [dit programma Cloud Solution Provider programma](cloud-solution-providers.md).
- **Co-sell met Microsoft:** laat Microsoft-verkoopteams uw in aanmerking komende ip-oplossing voor co-verkoop overwegen bij het evalueren van de behoeften van hun klanten. Zie Vereisten voor de status van co-verkoop voor meer informatie over geschiktheid [voor co-verkoop.](/legal/marketplace/certification-policies) Zie Optie voor co-verkoop in Partner Center voor meer informatie over het voorbereiden van [uw aanbieding Partner Center.](commercial-marketplace-co-sell.md)

## <a name="container-offer-requirements"></a>Vereisten voor containeraanbiedingen

| Vereiste | Details |  
|:--- |:--- |  
| Facturering en meting | Ondersteuning voor het gratis factureringsmodel of het BYOL-factureringsmodel. |
| Een afbeelding die is gebouwd op basis van een Dockerfile | Container-afbeeldingen moeten zijn gebaseerd op de specificatie van de Docker-afbeelding en zijn gebouwd op basis van een Dockerfile. Zie de sectie Gebruik van Dockerfile-verwijzing voor meer informatie over het bouwen van [Docker-afbeeldingen.](https://docs.docker.com/engine/reference/builder/#usage) |
| Hosting in een Azure Container Registry opslagplaats | Container-afbeeldingen moeten worden gehost in een Azure Container Registry opslagplaats. Zie [Quickstart:](../container-registry/container-registry-get-started-portal.md)Een privécontainerregister maken met behulp van Azure Container Registry de Azure Portal voor meer informatie over het werken met Azure Portal.<br><br> |
| Afbeeldingen taggen | Container-afbeeldingen moeten ten minste één tag bevatten (maximaal aantal tags: 16). Zie de pagina op de documentatiesite van Docker voor meer informatie over het taggen `docker tag` [van een](https://docs.docker.com/engine/reference/commandline/tag) afbeelding. |

## <a name="next-steps"></a>Volgende stappen

- [Technische assets voorbereiden](azure-container-technical-assets.md)

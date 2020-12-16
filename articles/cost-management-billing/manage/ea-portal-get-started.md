---
title: Aan de slag met de Azure Enterprise-portal
description: In dit artikel wordt uitgelegd hoe klanten van de Azure Enterprise Agreement (Azure EA) de Azure Enterprise-portal kunnen gebruiken.
author: bandersmsft
ms.reviewer: baolcsva
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 10/28/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 88bb4c24489b973aa4230ba6f2b34756f83c7dda
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030685"
---
# <a name="get-started-with-the-azure-enterprise-portal"></a>Aan de slag met de Azure Enterprise-portal

In dit artikel wordt uitgelegd hoe directe en indirecte klanten van de Azure Enterprise Agreement (Azure EA) aan de slag kunnen gaan met de [Azure Enterprise-portal](https://ea.azure.com). U vindt hier algemene informatie over:

- De indeling van de Azure Enterprise-portal.
- Welke rollen in de Azure Enterprise-portal worden gebruikt.
- Hoe u abonnementen maakt.
- Kostenanalyse in de Azure Enterprise-portal en de Azure-portal.

## <a name="get-started-with-ea-onboarding"></a>Aan de slag met EA-onboarding

Download de [Azure EA Onboarding Guide (PDF)](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide) voor een onboardinghandleiding voor Azure EA.

Bekijk deze video voor een volledige onboardingsessie van de Azure Enterprise-portal:

> [!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="understanding-ea-user-roles-and-introduction-to-user-hierarchy"></a>EA-gebruikersrollen begrijpen en inleiding tot gebruikershiërarchie

Voor het beheren van het gebruik en de uitgaven van uw organisatie kunnen Azure-klanten met een Enterprise Agreement (EA) vijf beheerdersrollen toewijzen:

- Zakelijke beheerder
- Zakelijke beheerder (alleen lezen)
- Afdelingsbeheerder
- Afdelingsbeheerder (alleen lezen)
- Accounteigenaar

Elke rol heeft andere gebruikerslimieten en -machtigingen. Zie [De organisatiestructuur en machtigingen per rol](./understand-ea-roles.md#organization-structure-and-permissions-by-role) voor meer informatie.

## <a name="activate-your-enrollment-create-a-subscription-and-other-administrative-tasks"></a>Uw inschrijving activeren, een abonnement maken, en andere beheertaken

Zie [Beheer van de Azure EA-portal](./ea-portal-administration.md) voor meer informatie over het activeren van uw inschrijving, het maken van een afdeling of abonnement, het toevoegen van beheerders en accounteigenaren, en andere beheertaken.

Zie [Azure Enterprise-overdrachten](./ea-transfers.md) als u meer wilt weten over het overdragen van een Enterprise-abonnement naar een abonnement met betalen per gebruik.

## <a name="view-usage-summary-and-download-reports"></a>Gebruiksoverzicht weergeven en rapporten downloaden

U kunt uw Azure EA-factuur beheren en er actie op ondernemen. Uw factuur is een representatie van uw rekening en moet worden gecontroleerd om te zien of deze klopt.

Zie [Facturen voor Azure Enterprise-inschrijvingen](./ea-portal-enrollment-invoices.md) om een gebruiksoverzicht te bekijken, rapporten te downloaden en inschrijvingsfacturen te beheren.

## <a name="now-that-youre-familiar-with-the-basics-here-are-some-additional-links-to-help-you-get-onboarded"></a>Nu u bekend bent met de basisprincipes, volgen hier een aantal aanvullende koppelingen om u te helpen met de onboarding.

[Azure EA-prijzen](./ea-pricing-overview.md) biedt details over hoe gebruik wordt berekend en gaat over kosten voor verschillende Azure-services in de Enterprise Agreement waar de berekeningen ingewikkelder zijn.

Zie [Gereserveerde VM-exemplaren in Azure EA](./ea-portal-vm-reservations.md) als u meer wilt weten over hoe u met Azure-reserveringen voor gereserveerde VM-exemplaren geld kunt besparen met uw Enterprise-inschrijving.

Zie [REST-API's in Azure Enterprise](./ea-portal-rest-apis.md) voor informatie over welke REST-API's u moet gebruiken met uw Azure Enterprise-inschrijving en hoe u veelvoorkomende problemen met REST-API's oplost.

In [Azure EA-overeenkomsten en -aanpassingen](./ea-portal-agreements.md) wordt beschreven hoe Azure EA-overeenkomsten en -aanpassingen van invloed kunnen zijn op uw toegang, gebruik en betalingen voor Azure-services.

In [Azure Marketplace](./ea-azure-marketplace.md) wordt uitgelegd hoe EA-klanten en -partners de Marketplace-kosten kunnen bekijken en Azure Marketplace-aankopen kunnen inschakelen.

Zie [Beheer van de Azure EA-portal voor partners](./ea-partner-portal-administration.md) voor een uitleg over de algemene taken die een EA-partnerbeheerder uitvoert in de Azure EA-portal.

## <a name="get-started-on-azure-ea---faq"></a>Aan de slag met Azure EA - Veelgestelde vragen

In dit gedeelte vindt u antwoorden op veelvoorkomende vragen die klanten ons tijdens het onboardingproces stellen.  

### <a name="i-accidentally-associated-my-existing-azure-account-with-azure-ea-enrollment-as-a-result-i-lost-my-monthly-credit-can-i-get-my-monthly-credit-back"></a>Ik heb mijn bestaande Azure-account per ongeluk aan een Azure EA-inschrijving gekoppeld. Hierdoor ben ik mijn maandelijkse tegoed kwijtgeraakt. Kan ik het maandelijkse tegoed terugkrijgen?

Als u zich hebt aangemeld als Azure EA-accounteigenaar met de aanmeldingsgegevens die u ook gebruikt voor uw Visual Studio-abonnement, kunt u het Azure-voordeel van uw afzonderlijke Visual Studio-abonnement herstellen door een van de volgende acties uit te voeren:

- Verwijder de accounteigenaar uit de Azure Enterprise-portal nadat u bijbehorende Azure-abonnementen hebt verwijderd of verplaatst. Meld u vervolgens opnieuw aan voor Azure-voordelen voor het individuele Visual Studio-abonnement.
- Verwijder de Visual Studio-abonnee uit de beheersite in VLSC en wijs het abonnement opnieuw toe, maar nu aan een account met andere aanmeldingsgegevens. Meld u vervolgens opnieuw aan voor Azure-voordelen voor het individuele Visual Studio-abonnement.

### <a name="what-type-of-subscription-should-i-create"></a>Welk type abonnement moet ik maken?

De Azure Enterprise-portal biedt twee typen abonnementen voor ondernemingsklanten:

- Microsoft Azure Enterprise, ideaal voor:
  - Gebruik in elke productieomgeving
  - Beste prijzen op basis van uitgaven aan infrastructuur

  [Neem contact op met de verkoopafdeling van Azure](https://azure.microsoft.com/pricing/enterprise-agreement/) voor meer informatie.

- Enterprise Dev/Test, ideaal voor:
  - Alle werkbelastingen voor ontwikkelen en testen van het team
  - Gemiddelde tot intensieve individuele werkbelastingen voor ontwikkelen en testen
  - Toegang tot speciale MSDN-installatiekopieën en voorkeurstarieven voor services

  Bekijk de [Enterprise Dev/Test-aanbieding](https://azure.microsoft.com/offers/ms-azr-0148p/) voor meer informatie.

### <a name="my-subscription-name-is-the-same-as-the-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>Mijn abonnement heeft dezelfde naam als de aanbieding. Moet ik de abonnementsnaam wijzigen in iets dat voor mijn organisatie handig is?

Als u een abonnement maakt, krijgt het standaard de naam van het gekozen aanbiedingstype. Het wordt aanbevolen de abonnementsnaam te wijzigen in een naam waardoor u het abonnement gemakkelijk kunt traceren.

Ga als volgt te werk om de naam te wijzigen:

1. Meld u aan bij [https://account.windowsazure.com](https://account.windowsazure.com).
1. Selecteer de lijst met abonnementen.
1. Selecteer het abonnement dat u wilt bewerken.
1. Selecteer het pictogram **Abonnement beheren**.
1. Bewerk de abonnementsgegevens.

### <a name="how-can-i-track-costs-incurred-by-a-cost-center"></a>Hoe kan ik de kosten bijhouden die door een kostenplaats zijn gemaakt?

Als u de kosten van een kostenplaats wilt bijhouden, moet u de kostenplaats definiëren op een van de volgende niveaus:

- Afdeling
- Account
- Abonnement

Op basis van uw behoeften kunt u dezelfde kostenplaats gebruiken om het gebruik en de kosten van een specifieke kostenplaats bij te houden.

Als u bijvoorbeeld kosten wilt bijhouden voor een speciaal project waaraan meerdere afdelingen hebben gewerkt, kunt u de kostenplaats op abonnementsniveau definiëren om het gebruik en de kosten bij te houden.

Het is niet mogelijk om een kostenplaats op serviceniveau te definiëren. Als u gebruik op serviceniveau wilt bijhouden, kunt u de functie _Taggen_ gebruiken.

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>Hoe kan ik het gebruik en de uitgaven van de verschillende afdelingen in mijn organisatie bijhouden?

U kunt binnen uw Azure EA-inschrijving net zoveel afdelingen maken als u nodig hebt. Als u het gebruik zorgvuldig wilt bijhouden, is het belangrijk dat abonnementen niet met meerdere afdelingen worden gedeeld.

Nadat u afdelingen en abonnementen hebt gemaakt, kunt u gegevens bekijken in het gebruiksrapport. Deze informatie kan u helpen bij het bijhouden van gebruik en het beheren van kosten en uitgaven op afdelingsniveau.

U kunt gebruiksgegevens ook bekijken via de rapportage-API. Raadpleeg [Azure Enterprise REST API’s](./ea-portal-rest-apis.md) voor gedetailleerde informatie en voorbeeldcode.

### <a name="can-i-set-a-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>Kan ik een bestedingsquotum instellen en meldingen krijgen als ik in de buurt kom van mijn limiet?

U kunt een bestedingsquotum op afdelingsniveau instellen. U krijgt dan van het systeem automatisch een melding wanneer u 50%, 75%, 90% en 100% van het door u gedefinieerde quotum hebt bereikt.

Selecteer een afdeling en vervolgens het pictogram Bewerken om een bestedingsquotum in te stellen. Nadat u de gegevens voor de bestedingslimiet hebt bewerkt, selecteert u **Opslaan**.

### <a name="i-used-resource-groups-to-implement-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>Ik heb resourcegroepen gebruikt om RBAC te implementeren en gebruik bij te houden. Hoe kan ik de bijbehorende gebruiksgegevens bekijken?

Als u _resourcegroepen_ en _tags_ gebruikt, wordt deze informatie bijgehouden op serviceniveau en kunt u de gegevens raadplegen in het downloadbare CSV-bestand met gedetailleerde gebruiksinformatie. Zie het gedeelte [Gebruiksrapport downloaden](https://ea.azure.com/report/downloadusage) in de Azure Enterprise-portal.

U kunt de gegevens ook raadplegen via een API. Raadpleeg [Azure Enterprise REST API’s](./ea-portal-rest-apis.md) voor gedetailleerde informatie en voorbeeldcode.

> [!NOTE]
> U kunt alleen tags toepassen op resources die ondersteuning bieden voor Azure Resource Manager-bewerkingen. Als u een virtuele machine, virtueel netwerk of opslag hebt gemaakt via het klassieke implementatiemodel (zoals via de klassieke portal), kunt u geen tag op die resource toepassen. U moet deze resources opnieuw implementeren via de Resource Manager om tags te ondersteunen. Tags worden wel door alle andere resources ondersteund.

### <a name="can-i-perform-analyses-using-power-bi"></a>Kan ik een analyse uitvoeren met behulp van Power BI?

Ja. Met het Microsoft Azure Enterprise-inhoudspakket voor Power BI kunt u:

- Snel Azure-gebruik importeren en analyseren voor uw ondernemingsinschrijving.
- Nagaan welke afdeling, welk account of welk abonnement het meest heeft gebruikt.
- Erachter komen welke service uw organisatie het meest heeft gebruikt.
- Trends in uitgaven en gebruik bijhouden.

U kunt als volgt aan de slag met Power BI:

1. Ga naar de Power BI-website.
1. Meld u aan met een geldig werk- of schoolaccount.

   U kunt hetzelfde werk- of schoolaccount gebruiken of een ander werk- of schoolaccount dan het account dat u hebt gebruikt voor toegang tot de inschrijving via de Azure Enterprise-portal.
1. Kies op het dashboard met services de tegel Microsoft Azure Enterprise en selecteer **Verbinding maken**.
1. Voer het volgende in op het scherm **Verbinding maken met Azure Enterprise**:
    - URL voor Azure-omgeving: [https://ea.azure.com](https://ea.azure.com)
    - Aantal maanden: getal tussen 1 en 36
    - Inschrijvingsnummer: uw inschrijvingsnummer
1. Selecteer **Next**.
1. Voer in het vak **Accountsleutel** de API-sleutel in.

   U vindt de API-sleutel in de Azure Enterprise-portal. Ga naar het tabblad **Gebruiksgegevens downloaden** en selecteer **API-toegangssleutel**. Kopieer de sleutel en plak deze in het vak **Accountsleutel** in Power BI.

Afhankelijk van de grootte van de gegevensset kan het vijf tot dertig minuten duren voordat de gegevens in Power BI worden geladen.

Power BI-rapportage is beschikbaar voor rechtstreekse Azure EA-klanten, partners en indirecte klanten die factureringsgegevens kunnen zien.

## <a name="next-steps"></a>Volgende stappen

- Azure Enterprise-portalbeheerders kunnen [Beheer van Azure EA Portal](ea-portal-administration.md) lezen voor meer informatie over algemene beheertaken.
- Raadpleeg [Problemen met toegang tot Azure EA Portal oplossen](ea-portal-troubleshoot.md) als u hulp nodig hebt om problemen op te lossen.
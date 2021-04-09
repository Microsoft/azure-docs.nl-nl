---
title: Azure Lighthouse in zakelijke scenario's
description: De mogelijkheden van Azure Lighthouse kunnen worden gebruikt voor het vereenvoudigen van cross-Tenant beheer binnen een onderneming die gebruikmaakt van meerdere Azure AD-tenants.
ms.date: 03/12/2021
ms.topic: conceptual
ms.openlocfilehash: 97b44f71750bdb533e889546f370a9b36ea5d3b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419351"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>Azure Lighthouse in zakelijke scenario's

Een veelvoorkomend scenario voor [Azure Lighthouse](../overview.md) is wanneer een service provider resources beheert in azure Active Directory-tenants (Azure AD) die deel uitmaken van klanten. De mogelijkheden van Azure Lighthouse kunnen ook worden gebruikt voor het vereenvoudigen van cross-Tenant beheer binnen een onderneming die gebruikmaakt van meerdere Azure AD-tenants.

## <a name="single-vs-multiple-tenants"></a>Enkele versus meerdere tenants

Voor de meeste organisaties is het beheer eenvoudiger met één Azure AD-Tenant. Met alle resources binnen één Tenant kunnen beheer taken worden gecentraliseerd door bepaalde gebruikers, gebruikers groepen of service-principals binnen die Tenant. We raden u aan om waar mogelijk één Tenant voor uw organisatie te gebruiken.

Sommige organisaties moeten mogelijk meerdere Azure AD-tenants gebruiken. Dit kan een tijdelijke situatie zijn, alsof er verwervingen hebben plaatsgevonden en een strategie voor een langdurige Tenant consolidatie nog niet is gedefinieerd. Het is ook mogelijk dat organisaties meerdere tenants moeten blijven hand haven als gevolg van volledige onafhankelijke dochter ondernemingen, geografische of wettelijke vereisten of andere overwegingen.

In gevallen waarin een architectuur met meerdere tenants is vereist, kunt u met Azure Lighthouse beheer bewerkingen centraliseren en stroom lijnen. Met behulp van [Azure delegated resource management](azure-delegated-resource-management.md)kunnen gebruikers met een Tenant [beheer functies voor meerdere tenants](cross-tenant-management-experience.md) op een gecentraliseerde, schaal bare manier uitvoeren.

## <a name="tenant-management-architecture"></a>Architectuur van Tenant beheer

Als u Azure Lighthouse in een onderneming wilt gebruiken, moet u bepalen welke Tenant de gebruikers bevat die beheer bewerkingen uitvoeren op de andere tenants. Met andere woorden, u moet één Tenant aanwijzen als Tenant beheren voor de andere tenants.

Stel bijvoorbeeld dat uw organisatie een enkele Tenant heeft die *Tenant a* oproept. Uw organisatie verwerft vervolgens *Tenant B* en *Tenant C*, en u hebt zakelijke redenen die u nodig hebben om ze te onderhouden als afzonderlijke tenants. U wilt echter dezelfde beleids definities, back-upmethoden en beveiligings processen gebruiken voor al deze, met beheer taken die door dezelfde set gebruikers worden uitgevoerd.

Aangezien Tenant A al gebruikers in uw organisatie bevat die deze taken voor Tenant A hebben uitgevoerd, kunt u abonnementen opdoen binnen Tenant B en Tenant C, waardoor dezelfde gebruikers in Tenant A deze taken kunnen uitvoeren op alle tenants.

![Diagram waarin gebruikers worden weer gegeven in tenants die een resources beheren in Tenant B en Tenant C.](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>Aandachtspunten voor beveiliging en toegang

In de meeste bedrijfs scenario's wilt u een volledig abonnement delegeren naar Azure Lighthouse. U kunt er ook voor kiezen om alleen specifieke resource groepen binnen een abonnement te delegeren.

In beide gevallen moet u [het principe van minimale bevoegdheid volgen om te definiëren welke gebruikers toegang hebben tot gedelegeerde resources](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege). Dit zorgt ervoor dat gebruikers alleen over de benodigde machtigingen beschikken om de vereiste taken uit te voeren en de kans op onbedoelde fouten te verminderen.

Azure Lighthouse biedt alleen logische koppelingen tussen een beheer Tenant en beheerde tenants, in plaats van het fysiek verplaatsen van gegevens of resources. Bovendien gaat de Access altijd in slechts één richting, van de Tenant beheren naar de beheerde tenants.  Gebruikers en groepen in de Tenant beheren moeten multi-factor Authentication blijven gebruiken bij het uitvoeren van beheer bewerkingen op beheerde Tenant bronnen.

Ondernemingen met interne of externe governance-en nalevings Guardrails kunnen [Azure-activiteiten logboeken](../../azure-monitor/essentials/platform-logs-overview.md) gebruiken om te voldoen aan hun transparantie vereisten. Wanneer ondernemings tenants beheer-en beheerde Tenant relaties hebben ingesteld, kunnen gebruikers in elke Tenant vastgelegde activiteiten weer geven om te zien welke acties door gebruikers zijn uitgevoerd in de Tenant beheren.

## <a name="onboarding-considerations"></a>Onboarding-overwegingen

Abonnementen (of resource groepen binnen een abonnement) kunnen worden gedistribueerd naar Azure Lighthouse door Azure Resource Manager sjablonen of via beheerde services te implementeren die zijn gepubliceerd op Azure Marketplace.

Omdat zakelijke gebruikers normaal gesp roken directe toegang hebben tot de tenants van de onderneming, en er geen beheer aanbod hoeft te worden gemarket of bevorderd, is het meestal sneller en eenvoudiger om Azure Resource Manager sjablonen te implementeren. Hoewel de [richt lijnen voor onboarding](../how-to/onboard-customer.md) naar service providers en klanten verwijzen, kunnen bedrijven dezelfde processen gebruiken om hun tenants uit te staan.

Indien gewenst kunnen tenants binnen een onderneming onboarding worden uitgevoerd door [een managed services-aanbod naar Azure Marketplace te publiceren](../how-to/publish-managed-services-offers.md). Om ervoor te zorgen dat de aanbieding alleen beschikbaar is voor de juiste tenants, moet u ervoor zorgen dat uw plannen als privé zijn gemarkeerd. Met een privé-abonnement geeft u de abonnements-Id's op voor elke Tenant die u wilt voorbereiden. niemand anders kan uw aanbieding ontvangen.

## <a name="terminology-notes"></a>Terminologie notities

Voor cross-Tenant beheer binnen de onderneming kunnen verwijzingen naar service providers in de Azure Lighthouse-documentatie worden geïnterpreteerd voor het beheren van de Tenant binnen een onderneming, dat wil zeggen, de Tenant die de gebruikers bevat die resources in andere tenants gaan beheren via Azure Lighthouse. Op dezelfde manier kunnen verwijzingen naar klanten worden geïnterpreteerd voor de tenants die resources overdragen die worden beheerd door gebruikers in de Tenant beheren.

In het bovenstaande voor beeld kan Tenant A bijvoorbeeld worden beschouwd als de Tenant van de service provider (de Tenant beheren) en Tenant B en Tenant C kunnen worden beschouwd als de tenants van de klant.

Als u dit voor beeld voortzet, kunnen gebruikers met de juiste machtigingen [gedelegeerde resources weer geven en beheren](../how-to/view-manage-customers.md) op de pagina **mijn klanten** van de Azure Portal. Daarnaast kunnen Tenant B-en Tenant C-gebruikers met de juiste machtigingen [de resources weer geven en beheren die zijn gedelegeerd](../how-to/view-manage-service-providers.md) aan Tenant A op de pagina **service providers** van de Azure Portal.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [beheerervaring in meerdere tenants](cross-tenant-management-experience.md).
- Meer informatie over [gedelegeerd Azure-resourcebeheer](azure-delegated-resource-management.md).

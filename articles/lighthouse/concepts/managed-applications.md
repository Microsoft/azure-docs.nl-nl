---
title: Azure Lighthouse en door Azure beheerde toepassingen
description: Krijg inzicht in de manier waarop Azure Lighthouse en door Azure beheerde toepassingen verschillende scenario's kunnen ondersteunen en hoe ze samen kunnen worden gebruikt.
ms.date: 08/12/2020
ms.topic: conceptual
ms.openlocfilehash: b3d6c83a50f1da006f2618f7f4ff59759dac2555
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436518"
---
# <a name="azure-lighthouse-and-azure-managed-applications"></a>Azure Lighthouse en door Azure beheerde toepassingen

Zowel door Azure beheerde toepassingen als Azure Lighthouse werken door een service provider in te scha kelen voor toegang tot bronnen die zich in de Tenant van de klant bevinden. Het kan handig zijn om inzicht te krijgen in de verschillen in de manier waarop ze werken en de scenario's die ze kunnen inschakelen, en hoe ze samen worden gebruikt.

> [!TIP]
> Hoewel we in dit onderwerp naar service providers en klanten verwijzen, kunnen [bedrijven die meerdere tenants beheren](enterprise.md) , gebruikmaken van dezelfde processen en hulpprogram ma's.

## <a name="comparing-azure-lighthouse-and-azure-managed-applications"></a>Azure Lighthouse en door Azure beheerde toepassingen vergelijken

### <a name="azure-lighthouse"></a>Azure Lighthouse

Met [Azure Lighthouse](../overview.md)kan een service provider een breed scala aan beheer taken rechtstreeks uitvoeren op het abonnement van een klant (of de resource groep). Deze toegang wordt bereikt door middel van een logische projectie, waardoor service providers zich kunnen aanmelden bij hun eigen Tenant en toegang hebben tot resources die deel uitmaken van de Tenant van de klant. De klant kan bepalen welke abonnementen of resource groepen u wilt delegeren aan de service provider. de klant houdt volledige toegang tot deze resources. Ze kunnen de toegang tot de service provider ook op elk gewenst moment verwijderen.

Als u Azure Lighthouse wilt gebruiken, zijn klanten onboarding voor [Azure-gedelegeerd resource beheer](azure-delegated-resource-management.md) door [arm-sjablonen te implementeren](../how-to/onboard-customer.md) of via een [beheerde service aanbieding in azure Marketplace](managed-services-offers.md). U kunt uw impact op klant afspraken bijhouden door [uw partner-id te koppelen](../how-to/partner-earned-credit.md).

Azure Lighthouse wordt doorgaans gebruikt wanneer een service provider beheer taken voor een klant voortdurend uitvoert.

### <a name="azure-managed-applications"></a>Door Azure beheerde toepassingen

Met door [Azure beheerde toepassingen](../../azure-resource-manager/managed-applications/overview.md) kunnen service providers of isv's cloud oplossingen aanbieden die gemakkelijk zijn voor klanten om te implementeren en gebruiken in hun eigen abonnementen.

In een beheerde toepassing worden de resources die door de toepassing worden gebruikt samen gebundeld en geïmplementeerd naar een resource groep die wordt beheerd door de uitgever. Deze resource groep is aanwezig in het abonnement van de klant, maar een identiteit in de Tenant van de uitgever heeft er toegang toe. De ISV gaat door met het beheren en onderhouden van de beheerde toepassing, terwijl de klant geen rechtstreekse toegang heeft tot het werk in de resource groep of enige toegang tot de resources.

Beheerde toepassingen ondersteunen aangepaste [Azure Portal ervaringen](../../azure-resource-manager/managed-applications/concepts-view-definition.md) en [integratie met aangepaste providers](../../azure-resource-manager/managed-applications/tutorial-create-managed-app-with-custom-provider.md). Deze opties kunnen worden gebruikt om een meer aangepaste en geïntegreerde ervaring te bieden, waardoor klanten eenvoudiger een aantal beheer taken kunnen uitvoeren.

Beheerde toepassingen kunnen worden [gepubliceerd naar Azure Marketplace](../../marketplace/create-new-azure-apps-offer.md), hetzij als een persoonlijke aanbieding voor het gebruik van een specifieke klant, hetzij als een openbaar aanbod dat meerdere klanten kunnen kopen. Ze kunnen ook worden afgeleverd aan gebruikers binnen uw organisatie door [het publiceren van beheerde toepassingen naar uw service catalogus](../../azure-resource-manager/managed-applications/publish-service-catalog-app.md). U kunt zowel Service catalogus-als Marketplace-instanties implementeren met ARM-sjablonen, die een unieke id van een commerciële Marketplace-partner kunnen bevatten om de [toewijzing van klant gebruik](../../marketplace/azure-partner-customer-usage-attribution.md)bij te houden.

Door Azure beheerde toepassingen worden doorgaans gebruikt voor een specifieke klant behoefte die kan worden bereikt via een kant-en-klare oplossing die volledig wordt beheerd door de service provider.

## <a name="using-azure-lighthouse-and-azure-managed-applications-together"></a>Met Azure Lighthouse en met Azure beheerde toepassingen tegelijk

Hoewel Azure Lighthouse en Azure Managed Applications verschillende toegangs mechanismen gebruiken om verschillende doel stellingen te bereiken, zijn er mogelijk scenario's waarin het zinvol is dat een service provider beide gebruikt voor dezelfde klant.

Een klant wil bijvoorbeeld beheerde services die door een service provider worden geleverd via Azure Lighthouse, zodat ze inzicht hebben in de acties van de partner en de voortdurende controle over hun overgedragen abonnement. Het is echter mogelijk dat de service provider niet wil dat de klant toegang heeft tot bepaalde resources die worden opgeslagen in de Tenant van de klant, of dat er aangepaste acties op deze resources zijn toegestaan. Om aan deze doel stellingen te voldoen, kan de service provider een persoonlijke aanbieding publiceren als een beheerde toepassing. De beheerde toepassing kan een resource groep bevatten die is geïmplementeerd in de Tenant van de klant, maar die niet rechtstreeks door de klant kan worden geopend.

Klanten kunnen ook geïnteresseerd zijn in beheerde toepassingen van meerdere service providers, ongeacht of ze ook beheerde services gebruiken via Azure Lighthouse van een van deze service providers. Daarnaast kunnen partners in het programma van de Cloud Solution Provider (CSP) bepaalde beheerde toepassingen die door andere Isv's zijn gepubliceerd door verkopen aan klanten die door Azure Lighthouse worden ondersteund. Met een breed scala aan opties kunnen service providers het juiste saldo kiezen om te voldoen aan de behoeften van klanten, en zo nodig de toegang tot resources beperken.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over door [Azure beheerde toepassingen](../../azure-resource-manager/managed-applications/overview.md).
- Meer informatie over het [voorbereiden van een abonnement op Azure Lighthouse](../how-to/onboard-customer.md).
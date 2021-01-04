---
title: Azure-Lighthouse in ISV-scenario's
description: De mogelijkheden van Azure Lighthouse kunnen door Isv's worden gebruikt voor meer flexibiliteit met klant aanbiedingen.
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: d6a12a51d360ad236563b871dbd94cc442ade434
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/19/2020
ms.locfileid: "97696353"
---
# <a name="azure-lighthouse-in-isv-scenarios"></a>Azure-Lighthouse in ISV-scenario's

Een veelvoorkomend scenario voor [Azure Lighthouse](../overview.md) is een service provider die resources beheert in de Azure Active Directory (Azure AD)-tenants van klanten. De mogelijkheden van Azure Lighthouse kunnen ook worden gebruikt door onafhankelijke software leveranciers (Isv's) met behulp van SaaS-aanbiedingen met hun klanten. Azure Lighthouse kan vooral nuttig zijn voor Isv's die beheerde services of ondersteuning bieden waarvoor toegang tot het abonnements bereik is vereist.

## <a name="managed-service-offers-in-azure-marketplace"></a>Aanbiedingen voor beheerde service in Azure Marketplace

Als ISV is het mogelijk dat u al oplossingen hebt gepubliceerd voor Azure Marketplace. Als u beheerde services aan uw klanten levert, kunt u dit doen door een beheerde service aanbieding te publiceren. Deze aanbiedingen stroom lijnen het voorbereidings proces en maken uw services schaalbaar voor zoveel klanten als nodig is. Azure Lighthouse ondersteunt een breed scala aan [Beheer taken en scenario's](cross-tenant-management-experience.md#enhanced-services-and-scenarios) die kunnen worden gebruikt om uw klanten een waarde te bieden.

Zie [een beheerde service aanbieding naar Azure Marketplace publiceren](../how-to/publish-managed-services-offers.md)voor meer informatie.

## <a name="using-azure-lighthouse-with-azure-managed-applications"></a>Azure Lighthouse gebruiken met door Azure beheerde toepassingen

[Azure Managed Applications](../../azure-resource-manager/managed-applications/overview.md) is een andere manier waarop isv's services aan hun klanten kunnen leveren. U kunt Azure Lighthouse samen met uw door Azure beheerde toepassingen gebruiken om verbeterde scenario's mogelijk te maken.

Zie [Azure Lighthouse en Azure Managed Applications](managed-applications.md)(Engelstalig) voor meer informatie.

## <a name="saas-based-multi-tenant-offerings"></a>Multi tenant-aanbod op basis van SaaS

Een aanvullend scenario is dat ISV de resources in een abonnement in hun eigen Tenant host en vervolgens Azure Lighthouse gebruikt om klanten toegang te geven tot deze resources. De klant kan zich vervolgens aanmelden bij hun eigen Tenant en zo nodig toegang tot deze resources krijgen. Isv's behouden hun IP-adres in hun eigen Tenant en kunnen hun eigen ondersteunings abonnementen gebruiken om tickets te genereren die zijn gerelateerd aan de oplossing die wordt gehost in hun Tenant, in plaats van het abonnement van de klant te gebruiken. Omdat de resources zich in de Tenant van de ISV bevinden, kunnen alle acties rechtstreeks door de ISV worden uitgevoerd, zoals het aanmelden bij Vm's, het installeren van apps en het uitvoeren van onderhouds taken.

In dit scenario worden gebruikers in de Tenant van de klant in feite toegang verleend als ' Tenant beheren ', hoewel de klant de resources van de ISV niet beheert. Omdat deze rechtstreeks toegang krijgen tot de Tenant van de ISV, is het belang rijk dat u alleen de mini maal vereiste machtigingen verleent, zodat klanten niet per ongeluk wijzigingen kunnen aanbrengen in de oplossing of andere ISV-resources.

Om deze architectuur in te scha kelen, moet de ISV de object-ID voor een gebruikers groep in de Azure AD-Tenant van de klant, samen met hun Tenant-ID, ophalen. De ISV bouwt vervolgens een ARM-sjabloon die deze gebruikers groep de juiste machtigingen geeft en [implementeert deze in het abonnement van de ISV](../how-to/onboard-customer.md) met de resources die de klant kan openen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [beheerervaring in meerdere tenants](cross-tenant-management-experience.md).
- Meer informatie over [gedelegeerd Azure-resourcebeheer](azure-delegated-resource-management.md).
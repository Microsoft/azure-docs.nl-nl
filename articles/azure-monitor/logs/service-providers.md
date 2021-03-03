---
title: Azure Monitor logboeken voor service providers | Microsoft Docs
description: Met Azure Monitor Logboeken kunt u Msp's (Managed Service Providers), grote ondernemingen, Isv's (Independent Software Vendors) en hosting service providers servers beheren en bewaken in de on-premises of Cloud infrastructuur van de klant.
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 02/03/2020
ms.openlocfilehash: 09b5a6bd11db1a8faa29e7b3871156f9c28d1140
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101717380"
---
# <a name="azure-monitor-logs-for-service-providers"></a>Azure Monitor logboeken voor service providers

Log Analytics-werk ruimten in Azure Monitor kunnen Msp's (Managed Service Providers), grote ondernemingen, onafhankelijke software leveranciers (Isv's) en hosting serviceproviders servers in de on-premises of Cloud infrastructuur van de klant beheren en bewaken.

Grote bedrijven delen veel overeenkomsten met service providers, met name wanneer er sprake is van een gecentraliseerd IT-team dat verantwoordelijk is voor het beheer van een groot aantal verschillende bedrijfs eenheden. Voor het gemak gebruikt dit document de term *service provider* , maar dezelfde functionaliteit is ook beschikbaar voor ondernemingen en andere klanten.

Voor partners en service providers die deel uitmaken van het programma [Cloud Solution Provider (CSP)](https://partner.microsoft.com/membership/cloud-solution-provider) , is Log Analytics in azure monitor een van de Azure-Services die beschikbaar zijn in azure CSP-abonnementen.

Log Analytics in Azure Monitor kunnen ook worden gebruikt door een service provider die klant resources beheert via de Azure-beheer functie voor gedelegeerde resources in [Azure Lighthouse](../../lighthouse/overview.md).

## <a name="architectures-for-service-providers"></a>Architecturen voor service providers

Log Analytics-werk ruimten bieden een methode voor de beheerder om de stroom en isolatie van [logboek](../logs/data-platform-logs.md) gegevens te beheren en een architectuur te maken voor de specifieke zakelijke behoeften. In [dit artikel](../logs/design-logs-deployment.md) worden de aandachtspunten voor het ontwerp, de implementatie en de migratie van een werk ruimte beschreven en het artikel [toegangs beheer](../logs/manage-access.md) bevat informatie over het Toep assen en beheren van machtigingen voor logboek gegevens. Service providers hebben extra overwegingen.

Er zijn drie mogelijke architecturen voor service providers die betrekking hebben op Log Analytics-werk ruimten:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. gedistribueerde logboeken worden opgeslagen in werk ruimten die zich bevinden in de Tenant van de klant

In deze architectuur wordt een werk ruimte geïmplementeerd in de Tenant van de klant die wordt gebruikt voor alle logboeken van die klant.

Er zijn twee manieren om toegang te krijgen tot een Log Analytics werkruimte in een Tenant van de klant:

- Een klant kan afzonderlijke gebruikers van de service provider toevoegen als [Azure Active Directory gast gebruikers (B2B)](../../active-directory/external-identities/what-is-b2b.md). De beheerder van de service provider moet zich aanmelden bij de directory van elke klant in het Azure Portal om toegang te kunnen krijgen tot deze werk ruimten. Dit vereist ook dat de klanten afzonderlijke toegang beheren voor elke beheerder van de service provider.
- Voor een grotere schaal baarheid en flexibiliteit kunnen service providers de Azure-functie voor [gedelegeerde resource beheer](../../lighthouse/concepts/azure-delegated-resource-management.md) van [Azure Lighthouse](../../lighthouse/overview.md) gebruiken om toegang te krijgen tot de Tenant van de klant. Met deze methode worden de service provider beheerders opgenomen in een Azure AD-gebruikers groep in de Tenant van de service provider en krijgt deze groep toegang tijdens het voorbereidings proces voor elke klant. Deze beheerders hebben vervolgens vanaf hun eigen Tenant van de service provider toegang tot de werk ruimten van elke klant, in plaats van zich afzonderlijk aan te melden bij de Tenant van elke klant. Door toegang te krijgen tot de resources van de Log Analytics-werk ruimte van uw klanten op deze manier vermindert u het werk dat aan de klant is vereist en kunt u gemakkelijker gegevens verzamelen en analyseren voor meerdere klanten die worden beheerd door dezelfde service provider via hulpprogram ma's als [Azure monitor werkmappen](../visualize/workbooks-overview.md). Zie voor meer informatie [klant resources op schaal bewaken](../../lighthouse/how-to/monitor-at-scale.md).

De voor delen van de gedistribueerde architectuur zijn:

* De klant kan specifieke machtigings niveaus bevestigen via [Azure delegated resource management](../../lighthouse/concepts/azure-delegated-resource-management.md), of u kunt de toegang tot de logboeken beheren met hun eigen [Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../../role-based-access-control/overview.md).
* Logboeken kunnen worden verzameld van alle typen resources, niet alleen voor de VM-gegevens op basis van de agent. Bijvoorbeeld Azure audit logs.
* Elke klant kan verschillende instellingen voor hun werk ruimte hebben, zoals retentie en het afgetopten van gegevens.
* Isolatie tussen klanten voor regelgevings-en nalevings.
* De kosten voor elke werk ruimte worden meegenomen in het abonnement van de klant.

De nadelen van de gedistribueerde architectuur zijn:

* Het centraal visualiseren en analyseren van gegevens [over de tenants van klanten](cross-workspace-query.md) met hulpprogram ma's als Azure monitor werkmappen kan leiden tot tragere ervaringen, met name bij het analyseren van gegevens over meer dan 50 werk ruimten.
* Als klanten niet zijn voor bereid voor Azure delegated resource management, moeten beheerders van service providers worden ingericht in de klanten Directory en is het moeilijker voor de service provider om een groot aantal klant tenants tegelijk te beheren.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. centrale logboeken worden opgeslagen in een werk ruimte die zich bevindt in de Tenant van de service provider

In deze architectuur worden de logboeken niet opgeslagen in de tenants van de klant, maar alleen op een centrale locatie binnen een van de abonnementen van de service provider. De agents die zijn geïnstalleerd op de virtuele machines van de klant, zijn geconfigureerd voor het verzenden van hun logboeken naar deze werk ruimte met behulp van de werk ruimte-ID en de geheime sleutel.

De voor delen van de gecentraliseerde architectuur zijn:

* Het is eenvoudig om een groot aantal klanten te beheren en ze te integreren op verschillende back-end-systemen.
* De service provider heeft volledig eigendom van de logboeken en de verschillende artefacten, zoals functies en opgeslagen query's.
* De service provider kan analyses uitvoeren voor alle klanten.

De nadelen van de gecentraliseerde architectuur zijn:

* Deze architectuur is alleen van toepassing op VM-gegevens op basis van een agent, die geen PaaS, SaaS en Azure Fabric-gegevens bronnen bevat.
* Het kan lastig zijn om de gegevens tussen de klanten te scheiden wanneer ze in één werk ruimte worden samengevoegd. De enige manier om dit te doen is door de Fully Qualified Domain Name van de computer (FQDN) of via de ID van het Azure-abonnement te gebruiken.
* Alle gegevens van alle klanten worden opgeslagen in dezelfde regio met één factuur en dezelfde Bewaar periode en configuratie-instellingen.
* Voor Azure Fabric-en PaaS-services zoals Azure Diagnostics en Azure-audit logboeken moet de werk ruimte zich in dezelfde Tenant bevinden als de resource, waardoor de logboeken niet kunnen worden verzonden naar de centrale werk ruimte.
* Alle VM-agents van alle klanten worden met dezelfde werk ruimte-ID en-sleutel geauthenticeerd naar de centrale werk ruimte. Er is geen methode om logboeken van een specifieke klant te blok keren zonder andere klanten te onderbreken.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. hybride logboeken worden opgeslagen in de werk ruimte die zich bevindt in de Tenant van de klant en sommige daarvan worden naar een centrale locatie verplaatst.

De derde architectuur mix tussen de twee opties. Het is gebaseerd op de eerste gedistribueerde architectuur waarbij de logboeken lokaal zijn voor elke klant, maar een bepaald mechanisme gebruiken om een centrale opslag plaats van logboeken te maken. Een gedeelte van de logboeken wordt binnengebracht in een centrale locatie voor rapportage en analyse. Dit gedeelte kan klein aantal gegevens typen zijn of een samen vatting van de activiteit, zoals dagelijkse statistieken.

Er zijn twee opties voor het implementeren van Logboeken op een centrale locatie:

1. Centrale werk ruimte: de service provider kan een werk ruimte maken in de Tenant en een script gebruiken dat gebruikmaakt van de [query-API](https://dev.loganalytics.io/) met de [API voor gegevens verzameling](../logs/data-collector-api.md) om de gegevens van de verschillende werk ruimten naar deze centrale locatie te brengen. Een andere optie, behalve een script, is het gebruik van [Azure Logic apps](../../logic-apps/logic-apps-overview.md).

2. Power BI als centrale locatie: Power BI kunnen fungeren als centrale locatie wanneer de verschillende werk ruimten gegevens exporteren met behulp van de integratie tussen de Log Analytics-werk ruimte en [Power bi](../visualize/powerbi.md).

## <a name="next-steps"></a>Volgende stappen

* Het maken en configureren van werk ruimten automatiseren met behulp van [Resource Manager-sjablonen](../logs/resource-manager-workspace.md)

* Het maken van werk ruimten automatiseren met [Power shell](../logs/powershell-workspace-configuration.md)

* [Waarschuwingen](../alerts/alerts-overview.md) gebruiken om te integreren met bestaande systemen

* Samenvattings rapporten genereren met behulp van [Power bi](../visualize/powerbi.md)

* Onboarding van klanten naar [Azure gedelegeerd resource beheer](../../lighthouse/concepts/azure-delegated-resource-management.md).

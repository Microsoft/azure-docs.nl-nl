---
title: Azure-Sentinel uitbreiden in werk ruimten en tenants | Microsoft Docs
description: Azure Sentinel gebruiken voor het opvragen en analyseren van gegevens in werk ruimten en tenants.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2020
ms.author: yelevin
ms.openlocfilehash: dfd8f94dc7ab4e428647b8f44a86a2cd06634e2a
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807238"
---
# <a name="extend-azure-sentinel-across-workspaces-and-tenants"></a>Azure-Sentinel uitbreiden in werkruimten en tenants

## <a name="the-need-to-use-multiple-azure-sentinel-workspaces"></a>De nood zaak om meerdere Azure Sentinel-werk ruimten te gebruiken

Azure Sentinel is gebaseerd op een Log Analytics-werk ruimte. U ziet dat de eerste stap bij het onboarding van Azure-Sentinel de Log Analytics werk ruimte moet selecteren die u voor dat doel wilt gebruiken.

U kunt het volledige voordeel van de Azure Sentinel-ervaring verkrijgen wanneer u één werkruimte gebruikt. Er zijn ook enkele omstandigheden waarin u mogelijk meerdere werk ruimten moet hebben. In de volgende tabel worden enkele van deze situaties vermeld en wordt, indien mogelijk, voorgesteld hoe aan de vereiste kan worden voldaan met één werk ruimte:

| Vereiste | Beschrijving | Manieren om het aantal werkruimten te verminderen |
|-------------|-------------|--------------------------------|
| Soevereiniteit en naleving van regelgeving | Een werkruimte is gekoppeld aan een bepaalde regio. Als gegevens in verschillende [Azure-regio's](https://azure.microsoft.com/global-infrastructure/geographies/) moeten worden bewaard om te voldoen aan de wettelijke vereisten, moet deze worden opgesplitst in afzonderlijke werk ruimten. |  |
| Eigendom van gegevens | De grenzen van gegevens eigendom, bijvoorbeeld door dochter ondernemingen of gelieerde ondernemingen, worden beter afgebakend met afzonderlijke werk ruimten. |  |
| Meerdere Azure-tenants | Azure Sentinel ondersteunt het verzamelen van gegevens van micro soft en Azure SaaS-resources alleen binnen een eigen Azure Active Directory (Azure AD)-Tenant grens. Daarom is voor elke Azure AD-tenant een afzonderlijke werkruimte vereist. |  |
| Gedetailleerde controle over gegevenstoegang | Een organisatie moet mogelijk verschillende groepen binnen of buiten de organisatie toestaan om toegang te krijgen tot een aantal van de gegevens die worden verzameld door Azure Sentinel. Bijvoorbeeld:<br><ul><li>Resource-eigen aren hebben toegang tot gegevens met betrekking tot hun resources</li><li>Regionale of subsidiaire SOCs toegang tot gegevens die relevant zijn voor hun delen van de organisatie</li></ul> | [Resource-Azure RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/controlling-access-to-azure-sentinel-data-resource-rbac/ba-p/1301463) of [tabel niveau gebruiken Azure RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) |
| Gedetailleerde instellingen voor retentie | In het verleden waren meerdere werk ruimten de enige manier om verschillende Bewaar perioden voor verschillende gegevens typen in te stellen. Dit is in veel gevallen niet langer nodig, dankzij de introductie van instellingen voor het bewaren van een tabel niveau. | Instellingen voor het [bewaren van tabel niveau](https://techcommunity.microsoft.com/t5/azure-sentinel/new-per-data-type-retention-is-now-available-for-azure-sentinel/ba-p/917316) gebruiken of [gegevens verwijdering](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data) automatiseren |
| Gesplitste facturering | Door werk ruimten in afzonderlijke abonnementen te plaatsen, kunnen ze worden gefactureerd aan verschillende partijen. | Gebruiksrapportages en doorberekening |
| Verouderde architectuur | Het gebruik van meerdere werk ruimten kan van een historisch ontwerp zijn dat rekening houdt met de beperkingen of aanbevolen procedures die niet meer waar zijn. Het kan ook een willekeurige ontwerpkeuze zijn geweest, die kan worden aangepast voor een betere aansluiting op Azure Sentinel.<br><br>Enkele voorbeelden:<br><ul><li>Een standaard werkruimte per abonnement gebruiken bij het implementeren van Azure Security Center</li><li>De nood zaak van nauw keurigere toegangs beheer-of Bewaar instellingen, de oplossingen waarvoor relatief nieuwe</li></ul> | Werkruimten opnieuw ontwerpen |

### <a name="managed-security-service-provider-mssp"></a>MSSP (Managed Security service provider)

Een bepaalde use-case die meerdere werk ruimten verplicht is, is een MSSP Azure-Sentinel service. In dit geval zijn veel van de bovenstaande vereisten van toepassing, waardoor er meerdere werk ruimten, tussen tenants, het best practice. De MSSP kan [Azure Lighthouse](../lighthouse/overview.md) gebruiken om de functionaliteit van Azure Sentinel cross-Workspace over meerdere tenants uit te breiden.

## <a name="azure-sentinel-multiple-workspace-architecture"></a>Azure Sentinel-architectuur met meerdere werkruimten

Zoals geïmpliceerd door de bovenstaande vereisten, zijn er gevallen waarin meerdere Azure Sentinel-werk ruimten, mogelijk via Azure Active Directory (Azure AD)-tenants, centraal moeten worden bewaakt en beheerd door één SOC.

- Een MSSP Azure-Sentinel service.

- Een wereld wijde SOC met meerdere dochter ondernemingen, elk met een eigen lokale SOC.

- Een SOC bewaking van meerdere Azure AD-tenants binnen een organisatie.

Om deze vereiste op te lossen, biedt Azure Sentinel meerdere werk ruimte mogelijkheden die centrale bewaking, configuratie en beheer mogelijk maken, waarbij één enkel deel venster wordt geboden over alles wat door het SOC wordt gedekt, zoals wordt weer gegeven in het onderstaande diagram.

:::image type="content" source="media/extend-sentinel-across-workspaces-tenants/cross-workspace-architecture.png" alt-text="Architectuur van meerdere werk ruimten":::

Dit model biedt grote voor delen ten opzichte van een volledig gecentraliseerd model waarin alle gegevens naar één werk ruimte worden gekopieerd:

- Flexibele roltoewijzing aan het globale en lokale SOCs, of aan de MSSP zijn klanten.

- Minder uitdagingen met betrekking tot de eigendom van gegevens, de privacy en de naleving van de regelgeving.

- Minimale netwerklatentie en -kosten.

- Eenvoudige onboarding en offboarding van nieuwe dochter ondernemingen of klanten.

In de volgende secties wordt uitgelegd hoe u dit model kunt uitvoeren en met name:

- Bewaak meerdere werk ruimten, mogelijk via tenants centraal, waardoor het SOC met één glas venster kan worden geboden.

- Configureer en beheer meerdere werk ruimten, mogelijk via tenants, centraal met automatisering.

## <a name="cross-workspace-monitoring"></a>Bewaking in meerdere werk ruimten

### <a name="manage-incidents-on-multiple-workspaces"></a>Incidenten op meerdere werk ruimten beheren

Azure Sentinel ondersteunt een [incident weergave van meerdere werk ruimten](./multiple-workspace-view.md) waarmee de bewaking en het beheer van centrale incidenten in meerdere werk ruimten kan worden vergemakkelijkt. Met de weer gave gecentraliseerde incidenten kunt u incidenten direct beheren of op transparante wijze inzoomen op de details van het incident in de context van de werk ruimte die oorspronkelijk is gemaakt.

### <a name="cross-workspace-querying"></a>Query's in meerdere werk ruimten

Azure Sentinel ondersteunt het opvragen van [meerdere werk ruimten in één query](../azure-monitor/log-query/cross-workspace-query.md), zodat u gegevens uit meerdere werk ruimten in één query kunt zoeken en correleren. 

- Gebruik de [werk ruimte ()-expressie](../azure-monitor/log-query/workspace-expression.md) om te verwijzen naar een tabel in een andere werk ruimte. 
- Gebruik de [operator Union](/azure/data-explorer/kusto/query/unionoperator?pivots=azuremonitor) naast de werk ruimte ()-expressie om een query uit te voeren voor tabellen in meerdere werk ruimten.

U kunt opgeslagen [functies](../azure-monitor/log-query/functions.md) gebruiken om query's in meerdere werk ruimten te vereenvoudigen. Als een verwijzing naar een werk ruimte bijvoorbeeld lang is, wilt u de expressie wellicht opslaan `workspace("customer-A's-hard-to-remember-workspace-name").SecurityEvent` als een functie met de naam `SecurityEventCustomerA` . U kunt vervolgens query's schrijven als `SecurityEventCustomerA | where ...` .

Een functie kan ook een vaak gebruikte samen voeging vereenvoudigen. U kunt bijvoorbeeld de volgende expressie opslaan als een functie met de naam `unionSecurityEvent` :

`union workspace(“hard-to-remember-workspace-name-1”).SecurityEvent, workspace(“hard-to-remember-workspace-name-2”).SecurityEvent`

U kunt vervolgens een query op beide werk ruimten schrijven door te beginnen met `unionSecurityEvent | where ...` .

#### <a name="cross-workspace-analytics-rules"></a>Regels voor cross-Workspace Analytics<a name="scheduled-alerts"></a>
<!-- Bookmark added for backward compatibility with old heading -->
Query's voor meerdere werk ruimten kunnen nu worden opgenomen in geplande analyse regels, afhankelijk van de volgende beperkingen:

- Er kunnen Maxi maal 15 werk ruimten in één query worden opgenomen.
- Azure Sentinel moet worden geïmplementeerd op elke werk ruimte waarnaar wordt verwezen in de query.

> [!NOTE] 
> Het uitvoeren van query's in meerdere werk ruimten in dezelfde query kan van invloed zijn op de prestaties en wordt daarom alleen aanbevolen als de logica deze functionaliteit vereist.

#### <a name="cross-workspace-workbooks"></a>Werkmappen in meerdere werk ruimten<a name="using-cross-workspace-workbooks"></a>
<!-- Bookmark added for backward compatibility with old heading -->
[Werkmappen](./overview.md#workbooks) bieden Dash boards en apps naar Azure Sentinel. Wanneer u werkt met meerdere werk ruimten, bieden ze bewaking en acties in verschillende werk ruimten.

Werkmappen kunnen query's in meerdere werk ruimten bieden op een van de volgende drie manieren, elk met verschillende niveaus van expertise op eind gebruiker:

| Methode  | Beschrijving | Wanneer moet ik het gebruiken? |
|---------|-------------|--------------------|
| Query's voor meerdere werk ruimten schrijven | De maker van de werkmap kan query's voor meerdere werk ruimten schrijven (hierboven beschreven) in de werkmap. | Met deze optie kunnen makers van de werkmap de gebruiker volledig uit de werkruimte structuur afschermen. |
| Een werkruimte kiezer toevoegen aan de werkmap | De maker van de werkmap kan een werkruimte kiezer implementeren als onderdeel van de werkmap, zoals [hier](https://techcommunity.microsoft.com/t5/azure-sentinel/making-your-azure-sentinel-workbooks-multi-tenant-or-multi/ba-p/1402357)wordt beschreven. | Deze optie geeft de gebruiker de controle over de werk ruimten die door de werkmap worden weer gegeven, door middel van een eenvoudig te gebruiken vervolg keuzelijst. |
| De werkmap interactief bewerken | Een ervaren gebruiker die een bestaande werkmap wijzigt, kan de query's hierin bewerken en de doel werkruimte selecteren met behulp van de werkruimte kiezer in de editor. | Met deze optie kan een hoofd gebruiker gemakkelijk bestaande werkmappen aanpassen om met meerdere werk ruimten te werken. |
|

#### <a name="cross-workspace-hunting"></a>Jacht in meerdere werk ruimten

Azure Sentinel biedt vooraf geladen query voorbeelden, ontworpen om u op weg te helpen en u vertrouwd te raken met de tabellen en de query taal. Deze ingebouwde jacht-query's worden doorlopend ontwikkeld door micro soft-beveiligings onderzoekers, zowel het toevoegen van nieuwe query's en het verfijnen van bestaande query's, om u een ingangs punt te bieden om te zoeken naar nieuwe detecties en te identificeren van de aanspraken van indringing die mogelijk niet worden gedetecteerd door uw beveiligings Programma's.  

Met de functie voor het zoeken in meerdere werk ruimten kunt u met behulp van de operator Union en de expressie werk ruimte (), zoals hierboven wordt weer gegeven, uw bedreigingen voor het maken van nieuwe jacht-query's en het aanpassen van bestaande werk ruimten.

## <a name="cross-workspace-management-using-automation"></a>Beheer van meerdere werk ruimten met automatisering

Als u meerdere Azure-Sentinel-werk ruimten wilt configureren en beheren, moet u het gebruik van de Azure Sentinel Management API automatiseren. Zie voor meer informatie over het automatiseren van de implementatie van Azure Sentinel-resources, waaronder waarschuwings regels, jacht-query's, werkmappen en playbooks, [uitbrei ding van Azure Sentinel: api's, integratie en beheer automatisering](https://techcommunity.microsoft.com/t5/azure-sentinel/extending-azure-sentinel-apis-integration-and-management/ba-p/1116885).

Zie ook [Azure Sentinel implementeren en beheren als code](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928) en [Azure Lighthouse combi neren met de DevOps-mogelijkheden van Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/combining-azure-lighthouse-with-sentinel-s-devops-capabilities/ba-p/1210966) voor een geconsolideerde, door de Community gestuurde methodologie voor het beheren van Azure Sentinel as code en voor het implementeren en configureren van bronnen vanuit een persoonlijke github-opslag plaats. 

## <a name="managing-workspaces-across-tenants-using-azure-lighthouse"></a>Werk ruimten beheren via tenants met behulp van Azure Lighthouse

Zoals eerder vermeld, kunnen de verschillende Azure Sentinel-werk ruimten in veel scenario's in verschillende Azure AD-tenants worden geplaatst. U kunt [Azure Lighthouse](../lighthouse/overview.md) gebruiken om alle activiteiten van meerdere werk ruimten over de grenzen van de Tenant uit te breiden, zodat gebruikers in uw Tenant beheren kunnen werken met de Sentinel-werk ruimten van Azure in alle tenants. Zodra Azure [Lighthouse is](../lighthouse/how-to/onboard-customer.md)uitgevallen, gebruikt u de [map + abonnement kiezer](./multiple-tenants-service-providers.md#how-to-access-azure-sentinel-in-managed-tenants) op het Azure Portal om alle abonnementen te selecteren die werk ruimten bevatten die u wilt beheren, om ervoor te zorgen dat ze allemaal beschikbaar zijn in de verschillende selecters van de werk ruimte in de portal.

Wanneer u Azure Lighthouse gebruikt, is het raadzaam om een groep te maken voor elke Azure-Sentinel-rol en machtigingen van elke Tenant te delegeren aan die groepen.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe de mogelijkheden van Azure Sentinel kunnen worden uitgebreid over meerdere werk ruimten en tenants. Raadpleeg de volgende artikelen voor praktische richt lijnen voor de implementatie van de multi-Workspace-architectuur van Azure Sentinel:

- Meer informatie over het [werken met meerdere tenants](./multiple-tenants-service-providers.md) in azure Sentinel, met behulp van Azure Lighthouse.
- Meer informatie over het [weer geven en beheren van incidenten in meerdere werk ruimten](./multiple-workspace-view.md) .
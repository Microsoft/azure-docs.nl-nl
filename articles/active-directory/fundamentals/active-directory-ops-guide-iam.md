---
title: Naslag Gids voor identiteits-en toegangs beheer van Azure Active Directory
description: In deze hand leiding voor bewerkingen worden de controles en acties beschreven die u moet uitvoeren om de bewerkingen voor identiteits-en toegangs beheer te beveiligen
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 66bce573be5a31641bdff809b8e9a79b617a703a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92370998"
---
# <a name="azure-active-directory-identity-and-access-management-operations-reference-guide"></a>Naslag Gids voor identiteits-en toegangs beheer van Azure Active Directory

In deze sectie van de [Naslag Gids voor Azure AD-bewerkingen](active-directory-ops-guide-intro.md) worden de controles en acties beschreven waarmee u rekening moet houden om de levens cyclus van identiteiten en hun toewijzingen te beveiligen en te beheren.

> [!NOTE]
> Deze aanbevelingen zijn actueel vanaf de datum van publicatie, maar kunnen worden gewijzigd in de loop van de tijd. Organisaties moeten voortdurend hun identiteits methoden evalueren omdat micro soft-producten en-services zich in de loop van de tijd ontwikkelen.

## <a name="key-operational-processes"></a>Belangrijkste operationele processen

### <a name="assign-owners-to-key-tasks"></a>Eigen aren toewijzen aan belang rijke taken

Voor het beheren van Azure Active Directory is een continue uitvoering vereist van belang rijke operationele taken en-processen die mogelijk geen deel uitmaken van een implementatie project. Het is nog belang rijk dat u deze taken instelt om uw omgeving te onderhouden. De belangrijkste taken en de aanbevolen eigen aren zijn onder andere:

| Taak | Eigenaar |
| :- | :- |
| Het proces voor het maken van Azure-abonnementen definiëren | Is afhankelijk van de organisatie |
| Beslissen wie Enterprise Mobility + Security-licenties krijgt | IAM Operations-team |
| Beslissen wie Microsoft 365-licenties krijgt | Team van productiviteit |
| Bepaal wie andere licenties krijgt, bijvoorbeeld Dynamics, Visual Studio Codespaces | Eigenaar van de toepassing |
| Licenties toewijzen | IAM Operations-team |
| Fouten met betrekking tot licentie toewijzingen oplossen en herstellen | IAM Operations-team |
| Identiteiten inrichten voor toepassingen in azure AD | IAM Operations-team |

Wanneer u uw lijst bekijkt, moet u mogelijk een eigenaar toewijzen voor taken waarvoor een eigenaar ontbreekt of het eigendom van taken met eigen aars aanpassen die niet zijn afgestemd op de bovenstaande aanbevelingen.

#### <a name="assigning-owners-recommended-reading"></a>Eigen aren toewijzen aanbevolen lezen

- [Beheerdersrollen toewijzen in Azure Active Directory](../roles/permissions-reference.md)
- [Governance in Azure](../../governance/index.yml)

## <a name="on-premises-identity-synchronization"></a>Synchronisatie van on-premises identiteiten

### <a name="identify-and-resolve-synchronization-issues"></a>Synchronisatie problemen identificeren en oplossen

Micro soft raadt u aan een goede basis lijn te hebben en te weten te komen over de problemen in uw on-premises omgeving die kunnen leiden tot synchronisatie problemen met de Cloud. Omdat automatische hulpprogram ma's, zoals [IdFix](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) en [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md#why-use-azure-ad-connect-health) , een groot aantal fout-positieven kunnen genereren, raden we u aan om synchronisatie fouten te identificeren die meer dan 100 dagen niet zijn geadresseerd door het opschonen van die objecten in een fout. Met lange termijn onopgeloste synchronisatie fouten kunnen ondersteunings incidenten worden gegenereerd. [Bij het oplossen van problemen tijdens de synchronisatie](../hybrid/tshoot-connect-sync-errors.md) wordt een overzicht gegeven van de verschillende typen synchronisatie fouten, enkele van de mogelijke scenario's die deze fouten veroorzaken en mogelijke manieren om de fouten op te lossen.

### <a name="azure-ad-connect-sync-configuration"></a>Configuratie van Azure AD Connect synchronisatie

Als u alle hybride ervaringen, op apparaten gebaseerde beveiligings postuur en integratie met Azure AD wilt inschakelen, moet u gebruikers accounts synchroniseren die uw werk nemers gebruiken om zich aan te melden bij hun bureau blad.

Als u de forest-gebruikers zich niet synchroniseert, moet u de synchronisatie wijzigen van het juiste forest.

#### <a name="synchronization-scope-and-object-filtering"></a>Synchronisatie bereik en object filtering

Het verwijderen van bekende buckets van objecten die niet hoeven te worden gesynchroniseerd, heeft de volgende operationele voor delen:

- Minder bronnen van synchronisatie fouten
- Snellere synchronisatie cycli
- Minder garbage om vanaf on-premises te transporteren, bijvoorbeeld vervuiling van de algemene adres lijst voor on-premises service accounts die niet relevant zijn in de Cloud

> [!NOTE]
> Als u vindt dat u veel objecten importeert die niet worden geëxporteerd naar de Cloud, moet u filteren op OE of specifieke kenmerken.

Voor beelden van objecten die moeten worden uitgesloten:

- Service accounts die niet worden gebruikt voor Cloud toepassingen
- Groepen die niet bedoeld zijn om te worden gebruikt in Cloud scenario's zoals die worden gebruikt om toegang te verlenen tot resources
- Gebruikers of contact personen die externe identiteiten zijn die zijn bedoeld om te worden weer gegeven met Azure AD B2B-samen werking
- Computer accounts waar werk nemers geen toegang tot Cloud toepassingen hebben, bijvoorbeeld servers

> [!NOTE]
> Als één menselijke identiteit meerdere accounts heeft ingericht van iets zoals een verouderde domein migratie, fusie of overname, moet u het account dat door de gebruiker wordt gebruikt, bijvoorbeeld alleen op basis van een dag per dag synchroniseren, zoals wat ze gebruiken om zich aan te melden bij hun computer.

In het ideale geval moet u een evenwicht bereiken tussen het verminderen van het aantal objecten dat moet worden gesynchroniseerd en de complexiteit van de regels. Over het algemeen is een combi natie tussen organisatie-eenheid/container [filtering](../hybrid/how-to-connect-sync-configure-filtering.md) en een eenvoudige kenmerk toewijzing aan het kenmerk cloudFiltered een efficiënte filter combinatie.

> [!IMPORTANT]
> Als u groeps filters gebruikt in de productie, moet u overstappen op een andere filter methode.

#### <a name="sync-failover-or-disaster-recovery"></a>Failover of herstel na nood gevallen synchroniseren

Azure AD Connect speelt een belang rijke rol in het inrichtings proces. Als de synchronisatie server om een of andere reden offline gaat, kunnen wijzigingen in on-premises niet worden bijgewerkt in de Cloud en kan dit leiden tot toegangs problemen voor gebruikers. Daarom is het belang rijk om een failover-strategie te definiëren waarmee beheerders snel de synchronisatie kunnen hervatten nadat de synchronisatie server offline gaat. Deze strategieën kunnen worden onderverdeeld in de volgende categorieën:

- **Azure AD Connect-server (s) implementeren in de faserings modus** : Hiermee kan een beheerder de staging-server promo veren tot productie door een eenvoudige configuratie-switch.
- **Virtualisatie gebruiken** : als Azure AD Connect is geïmplementeerd op een virtuele machine (VM), kunnen beheerders hun virtualisatiehost gebruiken om live te migreren of om snel de virtuele machine opnieuw te implementeren, zodat de synchronisatie wordt hervat.

Als uw organisatie geen nood herstel-en failover-strategie heeft voor synchronisatie, mag u Azure AD Connect niet in de Faserings modus zetten. Als er sprake is van een verschil tussen uw productie-en faserings configuratie, moet u Azure AD Connect de faserings modus opnieuw basis lijn aanpassen zodat deze overeenkomt met de productie configuratie, inclusief software versies en configuraties.

![Een scherm opname van Azure AD Connect configuratie van de faserings modus](./media/active-directory-ops-guide/active-directory-ops-img1.png)

#### <a name="stay-current"></a>Blijf op de hoogte

Micro soft updates Azure AD Connect regel matig. Blijf op de hoogte om te profiteren van de prestatie verbeteringen, fout oplossingen en nieuwe mogelijkheden die elke nieuwe versie biedt.

Als uw Azure AD Connect-versie meer dan zes maanden achter is, moet u een upgrade uitvoeren naar de meest recente versie.

#### <a name="source-anchor"></a>Bron anker

Als u **MS-DS-consistencyguid** als [bron-anker](../hybrid/plan-connect-design-concepts.md) gebruikt, kan de migratie van objecten tussen forests en domeinen eenvoudiger worden gemigreerd, wat gebruikelijk is in AD-domein consolidatie/opschoning, samen voegingen, acquisities en divestitures.

Als u **ObjectGuid** momenteel als bron-anker gebruikt, raden we u aan om te scha kelen naar het gebruik van **MS-DS-ConsistencyGuid**.

#### <a name="custom-rules"></a>Aangepaste regels

Azure AD Connect aangepaste regels bieden de mogelijkheid om de stroom van kenmerken te beheren tussen on-premises objecten en Cloud objecten. Door het gebruik van aangepaste regels te vervolgen of te gebruiken, kunnen de volgende Risico's worden geïntroduceerd:

- Problemen met complexiteit oplossen
- Prestatie vermindering bij het uitvoeren van complexe bewerkingen tussen objecten
- Hogere kans op verschillen tussen de configuratie van de productie server en de staging-server
- Extra overhead bij het upgraden van Azure AD Connect als aangepaste regels worden gemaakt binnen de prioriteit groter dan 100 (gebruikt door ingebouwde regels)

Als u over meer complexe regels beschikt, moet u de redenen voor de complexiteit onderzoeken en mogelijkheden voor vereenvoudiging vinden. Als u aangepaste regels met een prioriteits waarde van meer dan 100 hebt gemaakt, moet u ook de regels corrigeren zodat ze geen risico lopen of conflicteren met de standaardset.

Voor beelden van het gebruik van aangepaste regels zijn:

- Vervollediging **van gewijzigde gegevens in de Directory** : in dit geval is het raadzaam om te werken met de eigen aren van het AD-team en de gegevens in de map op te schonen als een herstel taak, en de processen aan te passen om te voor komen dat er ongeldige gegevens worden geïntroduceerd.
- **Eenmalig herstel van afzonderlijke gebruikers** : het is gebruikelijk om regels te vinden die een speciaal geval uitbijten, meestal vanwege een probleem met een specifieke gebruiker.
- **Overgecompliceerde ' CloudFiltering '** : het verminderen van het aantal objecten is een goede gewoonte, maar er is een risico voor het maken en overkelderen van het synchronisatie bereik met behulp van veel synchronisatie regels. Als er complexe logica is om objecten buiten het OE-filter op te nemen/uit te sluiten, is het raadzaam deze logica buiten de synchronisatie te plaatsen en de objecten te voorzien van een eenvoudig ' cloudFiltered-kenmerk dat kan stromen met een eenvoudige synchronisatie regel.

#### <a name="azure-ad-connect-configuration-documenter"></a>Azure AD Connect-configuratie Documenter

De [Azure AD Connect-configuratie Documenter](https://github.com/Microsoft/AADConnectConfigDocumenter) is een hulp programma dat u kunt gebruiken om documentatie van een Azure AD Connect-installatie te genereren om een beter inzicht te krijgen in de synchronisatie configuratie, het vertrouwen in te stellen op basis van rechten en te weten wat er is gewijzigd tijdens het Toep assen van een nieuwe build of configuratie van Azure AD Connect of het toevoegen of bijwerken van aangepaste synchronisatie regels. De huidige mogelijkheden van het hulp programma zijn:

- Documentatie over de volledige configuratie van Azure AD Connect Sync.
- Documentatie over wijzigingen in de configuratie van twee Azure AD Connect synchronisatie servers of wijzigingen van een bepaalde configuratie basislijn.
- Het genereren van een Power shell-implementatie script voor het migreren van de synchronisatie regel verschillen of aanpassingen van de ene server naar een andere.

## <a name="assignment-to-apps-and-resources"></a>Toewijzing aan apps en resources

### <a name="group-based-licensing-for-microsoft-cloud-services"></a>Op groepen gebaseerde licentie verlening voor micro soft-Cloud Services

Azure Active Directory stroomlijnt het beheer van licenties via [op groepen gebaseerde licentie verlening](./active-directory-licensing-whatis-azure-portal.md) voor micro soft-Cloud Services. Op deze manier biedt IAM de groeps infrastructuur en gedelegeerd beheer van deze groepen aan de juiste teams in de organisaties. Er zijn meerdere manieren om het lidmaatschap van groepen in azure AD in te stellen, waaronder:

- De **synchronisatie van on-premises** groepen kan afkomstig zijn uit on-premises directory's. Dit kan handig zijn voor organisaties die groeps beheer processen hebben ingesteld die kunnen worden uitgebreid om licenties toe te wijzen in Microsoft 365.

- Op **kenmerken gebaseerde/dynamische** groepen kunnen worden gemaakt in de Cloud op basis van een expressie op basis van gebruikers kenmerken, bijvoorbeeld afdeling is gelijk aan ' verkoop '. Azure AD houdt de leden van de groep bij, zodat deze consistent zijn met de gedefinieerde expressie. Het gebruik van dit soort groep voor licentie toewijzing maakt het mogelijk een licentie toewijzing op basis van kenmerken te maken die geschikt is voor organisaties met een hoge kwaliteit van gegevens in hun Directory.

- **Overgedragen eigendom** : groepen kunnen worden gemaakt in de Cloud en kunnen aangewezen eigen aren zijn. Op deze manier kunt u zakelijke eigen aren, bijvoorbeeld samenwerkings team of BI-team, machtigen om te bepalen wie toegang moet hebben.

Als u momenteel een hand matig proces gebruikt om licenties en onderdelen aan gebruikers toe te wijzen, raden we u aan om licentie verlening op basis van een groep te implementeren. Als uw huidige proces geen licentie fouten of wat is toegewezen versus beschikbaar, moet u verbeteringen voor het proces voor het oplossen van licentie fouten en het controleren van de licentie toewijzing definiëren.

Een ander aspect van licentie beheer is de definitie van service plannen (onderdelen van de licentie) die moeten worden ingeschakeld op basis van taak functies in de organisatie. Het verlenen van toegang aan service plannen die niet nodig zijn, kan ertoe leiden dat gebruikers hulpprogram ma's in de Office-portal kunnen zien die niet zijn getraind of niet moeten worden gebruikt. Het kan een extra Help Desk-volume best rijken, onnodig inrichten, en uw naleving en het governance risico, bijvoorbeeld wanneer u OneDrive voor bedrijven inricht voor personen die mogelijk geen inhoud mogen delen.

Gebruik de volgende richt lijnen om service plannen te definiëren voor gebruikers:

- Beheerders moeten bundels van service abonnementen definiëren die worden aangeboden aan gebruikers op basis van hun rol, bijvoorbeeld met een witte werk nemer versus een vloer medewerker.
- Groepen maken op basis van het cluster en de licentie toewijzen met het service plan.
- Optioneel kan een kenmerk worden gedefinieerd om de pakketten voor gebruikers in op te slaan.

> [!IMPORTANT]
> Op groep gebaseerde licentie verlening in azure AD introduceert het concept van gebruikers met een licentie fout status. Als u eventuele licentie fouten ziet, moet u de problemen met de licentie toewijzing onmiddellijk [identificeren en oplossen](../enterprise-users/licensing-groups-resolve-problems.md) .

![Er wordt automatisch een scherm opname van een computer scherm beschrijving gegenereerd](./media/active-directory-ops-guide/active-directory-ops-img2.png)

#### <a name="lifecycle-management"></a>Levenscyclus beheer

Als u momenteel een hulp programma gebruikt, zoals [Microsoft Identity Manager](/microsoft-identity-manager/) of een systeem van derden, dat afhankelijk is van een on-premises infra structuur, raden we u aan de toewijzing te offloaden van het bestaande hulp programma, op groepen gebaseerde licentie verlening te implementeren en een groeps levenscyclus beheer te definiëren op basis van [groepen](../enterprise-users/licensing-group-advanced.md#use-group-based-licensing-with-dynamic-groups). Als uw bestaande proces geen rekening houdt met nieuwe werk nemers of werk nemers die de organisatie verlaten, moet u ook op groep gebaseerde licentie verlening implementeren op basis van dynamische groepen en de levens cyclus van groepslid maatschappen definiëren. Ten slotte, als op groepen gebaseerde licentie verlening is geïmplementeerd op on-premises groepen die geen levenscyclus beheer hebben, kunt u overwegen Cloud groepen te gebruiken voor het inschakelen van mogelijkheden zoals gedelegeerd eigendom of dynamisch lidmaatschap van een kenmerk.

### <a name="assignment-of-apps-with-all-users-group"></a>Toewijzing van apps met de groep alle gebruikers

Resource-eigen aren kunnen van mening zijn dat de groep **alle gebruikers** alleen **werk nemers** van het bedrijf bevat wanneer ze mogelijk zowel **zakelijke mede werkers** als **gasten** bevatten. Als gevolg hiervan moet u bijzondere aandacht best denken wanneer u de groep **alle gebruikers** gebruikt voor het toewijzen van toepassingen en het verlenen van toegang tot resources, zoals share point-inhoud of-toepassingen.

> [!IMPORTANT]
> Als de groep **alle gebruikers** is ingeschakeld en wordt gebruikt voor beleid voor voorwaardelijke toegang, app of resource toewijzing, moet u [de groep beveiligen](../external-identities/use-dynamic-groups.md) als u geen gast gebruikers wilt toevoegen. Bovendien moet u uw licentie toewijzingen oplossen door te maken en toe te wijzen aan groepen die alleen **werk nemers** van een onderneming bevatten. Als u daarentegen ziet dat de groep **alle gebruikers** is ingeschakeld, maar niet wordt gebruikt om toegang te verlenen tot resources, moet u ervoor zorgen dat de operationele richt lijnen van uw organisatie opzettelijk gebruikmaken van die groep (die zowel **zakelijke mede werkers** als **gasten** bevat).

### <a name="automated-user-provisioning-to-apps"></a>Automatische gebruikers inrichting voor apps

[Automatische gebruikers inrichting](../app-provisioning/user-provisioning.md) voor toepassingen is de beste manier om een consistente inrichting, het ongedaan maken van de inrichting en de levens cyclus van identiteiten op meerdere systemen te creëren.

Als u momenteel apps op een ad-hoc manier inricht of dingen zoals CSV-bestanden, JIT of een lokale oplossing gebruikt die geen levenscyclus beheer verbiedt, raden we u aan om [toepassings inrichting te implementeren](../app-provisioning/user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) met Azure AD voor ondersteunde toepassingen en een consistent patroon te definiëren voor toepassingen die nog niet door Azure AD worden ondersteund.

![Azure AD-inrichtings service](./media/active-directory-ops-guide/active-directory-ops-img3.png)

### <a name="azure-ad-connect-delta-sync-cycle-baseline"></a>Basis lijn Azure AD Connect Delta synchronisatie cyclus

Het is belang rijk om inzicht te krijgen in het volume van wijzigingen in uw organisatie en ervoor te zorgen dat het niet te lang duurt om een voorspel bare synchronisatie tijd te hebben.

De [Standaard Delta synchronisatie](../hybrid/how-to-connect-sync-feature-scheduler.md) frequentie is 30 minuten. Als de Delta synchronisatie langer dan 30 minuten consistent duurt, of als er aanzienlijke verschillen zijn tussen de Delta synchronisatie prestaties van staging en productie, moet u de factoren onderzoeken en controleren die [van invloed zijn op de prestaties van Azure AD Connect](../hybrid/plan-connect-performance-factors.md).

#### <a name="azure-ad-connect-troubleshooting-recommended-reading"></a>Azure AD Connect probleem oplossing aanbevolen lezen

- [Adreslijst kenmerken voorbereiden voor synchronisatie met Microsoft 365 met het hulp programma IdFix](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)
- [Azure AD Connect: problemen tijdens de synchronisatie oplossen](../hybrid/tshoot-connect-sync-errors.md)

## <a name="summary"></a>Samenvatting

Er zijn vijf aspecten van een veilige identiteits infrastructuur. Deze lijst helpt u snel de vereiste acties te vinden en te ondernemen om de levens cyclus van identiteiten en hun rechten in uw organisatie te beveiligen en te beheren.

- Eigen aren toewijzen aan belang rijke taken.
- Zoek en los synchronisatie problemen op.
- Definieer een failover-strategie voor herstel na nood gevallen.
- Het beheer van licenties en de toewijzing van apps stroom lijnen.
- Gebruikers inrichten voor apps automatiseren.

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met [controles en acties voor verificatie beheer](active-directory-ops-guide-auth.md).
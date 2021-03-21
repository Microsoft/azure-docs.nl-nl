---
title: Overzicht van Azure-beleid
description: Azure Policy is een service in Azure die u gebruikt om beleidsdefinities in uw Azure-omgeving te maken, toe te wijzen en te beheren.
ms.date: 01/14/2021
ms.topic: overview
ms.openlocfilehash: df430586af2e701ec2881f6ea760095fd2ca79d0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98220725"
---
# <a name="what-is-azure-policy"></a>Wat is Azure Policy?

Met Azure Policy kunt u organisatiestandaarden afdwingen en compliance op schaal beoordelen. Via het compliancedashboard biedt het een geaggregeerde weergave om de algehele status van de omgeving te evalueren, met de mogelijkheid om in te zoomen op de granulariteit per resource, per beleid. Hiermee kunt u ook zorgen voor compliance van uw resources via bulkherstel voor bestaande resources en automatisch herstel voor nieuwe resources.

Veelvoorkomende use-cases voor Azure Policy zijn onder andere het implementeren van governance voor consistentie van resources, naleving van de regelgeving, beveiliging, kosten en beheer. Beleidsdefinities voor deze veelvoorkomende use-cases zijn al ingebouwd in uw Azure-omgeving om u op weg te helpen.

Alle gegevens en objecten van Azure Policy worden versleuteld wanneer ze inactief zijn. Zie [Versleuteling van inactieve gegevens in Azure](../../security/fundamentals/encryption-atrest.md)voor meer informatie.

## <a name="overview"></a>Overzicht

Azure Policy evalueert resources in Azure door de eigenschappen van deze resources te vergelijken met bedrijfsregels. Deze bedrijfsregels, zoals beschreven in [JSON-indeling](./concepts/definition-structure.md), worden ook wel [beleidsdefinities](#policy-definition) genoemd. Om het beheer te vereenvoudigen, kunnen meerdere bedrijfsregels worden gegroepeerd in een [beleidsinitiatief](#initiative-definition) (ook wel een _policySet_ genoemd). Zodra uw bedrijfsregels zijn gemaakt, wordt de beleidsdefinitie of het initiatief [toegewezen](#assignments) aan elk bereik van resources dat door Azure wordt ondersteund, zoals [beheergroepen](../management-groups/overview.md), abonnementen, [resourcegroepen](../../azure-resource-manager/management/overview.md#resource-groups) of afzonderlijke resources. De toewijzing is van toepassing op alle resources binnen het [Resource Manager-bereik](../../azure-resource-manager/management/overview.md#understand-scope) van deze toewijzing. Subbereiken kunnen zo nodig worden uitgesloten. Zie [Bereik in Azure Policy](./concepts/scope.md) voor meer informatie.

Azure Policy gebruikt een [JSON-indeling](./concepts/definition-structure.md) om de logica te vormen waarmee de evaluatie bepaalt of een resource compatibel is. Definities bevatten metagegevens en de beleidsregel. De gedefinieerde regel kan gebruikmaken van functies, parameters, logische operators, voorwaarden en eigenschaps[aliassen](./concepts/definition-structure.md#aliases) voor een precieze afstemming op het gewenste scenario. De beleidsregel bepaalt welke resources in het bereik van de toewijzing worden geëvalueerd.

### <a name="understand-evaluation-outcomes"></a>Inzicht in evaluatieresultaten

Resources worden geëvalueerd op specifieke tijdstippen tijdens de levenscyclus van resources, de levenscyclus van de beleidstoewijzing en voor een normale doorlopende evaluatie van de compliance. Hierna volgen de tijden waarop of gebeurtenissen waardoor een resource wordt geëvalueerd:

- Een resource wordt gemaakt, bijgewerkt of verwijderd in een bereik met een beleidstoewijzing.
- Een beleid of initiatief wordt voor het eerst toegewezen aan een bereik.
- Een beleid of initiatief dat al is toegewezen aan een bereik, wordt bijgewerkt.
- Tijdens de standaardevaluatiecyclus van de compliance, één keer elke 24 uur.

Zie [Evaluatietriggers](./how-to/get-compliance-data.md#evaluation-triggers) voor gedetailleerde informatie over wanneer en hoe de beleidsevaluatie wordt uitgevoerd.

### <a name="control-the-response-to-an-evaluation"></a>De reactie op een evaluatie beheren

Bedrijfsregels voor het verwerken van niet-compatibele resources verschillen nogal tussen organisaties. Enkele voorbeelden van hoe een organisatie wil dat het platform reageert op een niet-compatibele resource:

- De resourcewijziging weigeren
- De wijziging in de resource vastleggen
- De resource aanpassen vóór de wijziging
- De resource aanpassen na de wijziging
- Gerelateerde, compatibele resources implementeren

Azure Policy maakt al deze bedrijfsreacties mogelijk door [effecten](./concepts/effects.md) toe te passen. Effecten worden ingesteld in het gedeelte **beleidsregel** van de [beleidsdefinitie](./concepts/definition-structure.md).

### <a name="remediate-non-compliant-resources"></a>Niet-compatibele resources herstellen

Hoewel deze effecten voornamelijk van invloed zijn op een resource wanneer de resource wordt gemaakt of bijgewerkt, ondersteunt Azure Policy ook het verwerken van bestaande niet-compatibele resources zonder dat deze resource hoeft te worden aangepast. Zie [Resources herstellen](./how-to/remediate-resources.md) voor meer informatie over het compatibel maken van bestaande resources.

### <a name="video-overview"></a>Video-overzicht

Het volgende overzicht van Azure Policy is afkomstig van build 2018. Voor het downloaden van dia's of video's, gaat u naar [Azure-omgeving beheren via Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) op Channel 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="getting-started"></a>Aan de slag

### <a name="azure-policy-and-azure-rbac"></a>Azure Policy en Azure RBAC

Er zijn enkele belangrijke verschillen tussen Azure Policy en op rollen gebaseerd toegangsbeheer (Azure RBAC). Azure Policy evalueert de status door de eigenschappen te bekijken van resources die worden weergegeven in Resource Manager en de eigenschappen van sommige resourceproviders. In Azure Policy worden acties (ook wel _bewerkingen_) niet beperkt. Azure Policy zorgt ervoor dat de resourcestatus voldoet aan uw bedrijfsregels zonder dat het uitmaakt wie de wijziging heeft aangebracht of wie toestemming heeft om een wijziging aan te brengen.

Azure RBAC is gericht op het beheer van [gebruikersacties](../../role-based-access-control/resource-provider-operations.md) in verschillende bereiken. Als een actie moet worden beheerd, is Azure RBAC het meest geschikte hulpprogramma om te gebruiken. Zelfs als een persoon een actie mag uitvoeren, blokkeert Azure Policy toch het maken of bijwerken als het resultaat een niet-compatibele resource is.

Door de combinatie van Azure RBAC en Azure Policy beschikt u over volledig bereikbeheer in Azure.

### <a name="azure-rbac-permissions-in-azure-policy"></a>Azure RBAC-machtigingen in Azure Policy

Azure Policy heeft diverse machtigingen, oftewel bewerkingen, in twee verschillende resourceproviders:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Veel ingebouwde rollen wijzen machtigingen toe aan Azure Policy-resources. De rol **Inzender voor resourcebeleid** bevat de meeste Azure Policy-bewerkingen. Degene met de rol **Eigenaar** heeft volledige rechten. Zowel **Inzender** als **Lezer** heeft toegang tot alle _lees_ bewerkingen in Azure Policy. Met **Inzender** kunnen resourceherstelacties worden geactiveerd, maar kunnen er geen definities of toewijzingen worden _gemaakt_. De rol **Beheerder van gebruikerstoegang** is vereist om de benodigde machtigingen voor de beheerde identiteit van **deployIfNotExists**- of **modify**-toewijzingen te verlenen.

Als geen van de ingebouwde rollen de vereiste machtigingen heeft, maakt u een [aangepaste rol](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> Voor de beheerde identiteit van een **deployIfNotExists**- of **modify**-beleidstoewijzing zijn voldoende machtigingen nodig om resources te maken of bij te werken. Zie [Beleidsdefinities voor herstel configureren](./how-to/remediate-resources.md#configure-policy-definition) voor meer informatie.

### <a name="resources-covered-by-azure-policy"></a>Resources die worden gedekt door Azure Policy

Azure Policy evalueert alle Azure-resources op of onder het abonnementsniveau, met inbegrip van resources met Arc. Voor bepaalde resourceproviders, zoals [Gastconfiguratie](./concepts/guest-configuration.md), [Azure Kubernetes Service](../../aks/intro-kubernetes.md) en [Azure Key Vault](../../key-vault/general/overview.md), is er een uitgebreidere integratie voor het beheren van instellingen en objecten. Zie [Modi van resourceproviders](./concepts/definition-structure.md) voor meer informatie.

### <a name="recommendations-for-managing-policies"></a>Aanbevelingen voor het beheren van beleid

Enkele tips om rekening mee te houden:

- Begin met een controle-effect in plaats van een weigeringseffect om bij te houden wat de invloed van uw beleidsdefinitie is op de resources in uw omgeving. Als u al actieve scripts hebt voor automatische schaalaanpassing van uw toepassingen, kan het instellen van een weigeractie een belemmering vormen voor deze automatische taken die u al hebt geïmplementeerd.

- U kunt bij het maken van definities en toewijzingen gebruikmaken van organisatiehiërarchieën. Het is raadzaam om op een hoger niveau definities te maken, zoals op het niveau van de beheergroep of het abonnement. Vervolgens maakt u de toewijzing op het eerstvolgende onderliggende niveau. Als u een definitie voor een beheergroep maakt, kan de toewijzing worden gericht op een abonnement of resource in die beheergroep.

- Het is raadzaam om initiatiefdefinities te maken en toe te wijzen, zelfs voor slechts één beleidsdefinitie.
  U hebt bijvoorbeeld de beleidsdefinitie _policyDefA_ en u maakt deze met de initiatiefdefinitie _initiativeDefC_. Als u later nog een beleidsdefinitie maakt voor _policyDefB_, met doelen die lijken op die van _policyDefA_, kunt u deze toevoegen bij _initiativeDefC_ en ze samen volgen.

- Als u een initiatieftoewijzing hebt gemaakt, worden beleidsdefinities die worden toegevoegd aan het initiatief ook deel van de toewijzingen van het initiatief.

- Wanneer een initiatieftoewijzing wordt beoordeeld, worden alle beleidsregels in het initiatief ook beoordeeld.
  Als u een beleidsregel afzonderlijk wilt beoordelen, is het beter deze niet op te nemen in een initiatief.

## <a name="azure-policy-objects"></a>Azure Policy-objecten

### <a name="policy-definition"></a>Beleidsdefinitie

Het maken en implementeren van een beleidsregel in Azure Policy begint met het maken van een beleidsdefinitie. Elke beleidsdefinitie heeft voorwaarden voor het afdwingen ervan. Er is een bijbehorend effect dat wordt uitgevoerd als aan de voorwaarden wordt voldaan.

In Azure Policy wordt een aantal ingebouwde beleidsregels geboden dat standaard beschikbaar is. Bijvoorbeeld:

- **Toegestane opslagaccount-SKU's** (Weigeren): Bepaalt of opslagaccounts die worden geïmplementeerd zich in een set SKU-grootten bevinden. Het bijbehorende effect is om alle opslagaccounts te weigeren die niet in overeenstemming zijn met de gedefinieerde SKU-grootten.
- **Toegestaan resourcetype** (Weigeren): Hiermee definieert u de resourcetypen die u kunt implementeren. Het bijbehorende effect is om alle resources te weigeren die geen deel uitmaken van deze gedefinieerde lijst.
- **Toegestane locaties** (Weigeren): Hiermee beperkt u de beschikbare locaties voor nieuwe resources. Het bijbehorende effect wordt gebruikt om uw geografisch nalevingsvereisten af te dwingen.
- **Toegestane SKU's van virtuele machines** (Weigeren): Hiermee wordt een set SKU's voor virtuele machines gespecificeerd die u kunt implementeren.
- **Een tag toevoegen aan resources** (Wijzigen): Hiermee wordt een vereiste tag met bijbehorende standaardwaarde toegepast als de tag niet is opgegeven tijdens de implementatieaanvraag.
- **Niet toegestane resourcetypen** (Weigeren): Hiermee voorkomt u dat een lijst met resourcetypen wordt geïmplementeerd.

Als u deze beleidsdefinities (zowel de ingebouwde als de aangepaste) wilt implementeren, dient u ze eerst toe te wijzen. U kunt elk van deze typen beleid toewijzen via Azure Portal, PowerShell of Azure CLI.

Beleid wordt beoordeeld bij verschillende acties, zoals het toewijzen of bijwerken van beleidsregels. Zie voor een volledige lijst [Beleidbeoordelingstriggers](./how-to/get-compliance-data.md#evaluation-triggers).

Raadpleeg [Structuur van beleidsdefinities](./concepts/definition-structure.md) voor meer informatie over de structuur van beleidsdefinities.

Beleidsparameters helpen uw beleidsbeheer te vereenvoudigen door het aantal beleidsdefinities te verminderen dat u moet maken. U kunt bij het maken van een beleidsdefinitie parameters definiëren om deze algemener te maken. Vervolgens kunt u deze beleidsdefinitie hergebruiken voor verschillende scenario's. Dit doet u door verschillende waarden door te voeren bij het toepassen van de beleidsdefinitie. Bijvoorbeeld, door een reeks locaties voor een abonnement op te geven.

Parameters worden gedefinieerd bij het maken van een beleidsdefinitie. Als een parameter wordt gedefinieerd, krijgt deze een naam en optioneel een waarde. U kunt bijvoorbeeld een parameter definiëren voor een beleid met de naam _locatie_. Vervolgens kunt u deze verschillende waarden toekennen, zoals _EastUS_ of _WestUS_ bij het toewijzen van beleid.

Zie [Definitiestructuur - parameters](./concepts/definition-structure.md#parameters) voor meer informatie over beleidsparameters.

### <a name="initiative-definition"></a>Initiatiefdefinitie

Een initiatiefdefinitie is een verzameling beleidsdefinities die zijn aangepast voor het bereiken van één overkoepelend doel. Initiatiefdefinities maken het beheren en toewijzen van beleidsdefinities eenvoudiger. Dit gebeurt door het groeperen van een reeks beleidsregels als één item. U kunt bijvoorbeeld een initiatief maken met de titel **Controleren inschakelen in Azure Security Center** met als doel om alle beschikbare beveiligingsaanbevelingen in uw Azure Security Center te controleren.

> [!NOTE]
> De SDK, zoals Azure CLI en Azure PowerShell, maken gebruiken van eigenschappen en parameters met de naam **PolicySet** om te verwijzen naar initiatieven.

Onder dit initiatief vallen beleidsdefinities zoals:

- **Niet-versleutelde SQL-database controleren in Security Center** – Voor het controleren van niet-versleutelde SQL-databases en -servers.
- **OS-kwetsbaarheden controleren in Security Center**: voor het controleren van servers die niet voldoen aan de geconfigureerde uitgangswaarde.
- **Ontbrekende Endpoint Protection controleren in Security Center** – Voor het controleren van servers zonder een geïnstalleerde Endpoint Protection-agent.

Net zoals beleidsparameters helpen initiatiefparameters om initiatiefbeheer te vereenvoudigen door redundantie te verminderen. Initiatiefparameters zijn de parameters die worden gebruikt voor de beleidsdefinities binnen het initiatief.

Neem bijvoorbeeld een scenario met initiatiefdefinitie **initiativeC**, waarbij voor beleidsdefinities **policyA** en **policyB** elk een ander type parameter wordt verwacht:

| Beleid | Naam van parameter |Type parameter  |Opmerking |
|---|---|---|---|
| policyA | allowedLocations | matrix  |Op basis van deze parameter wordt een lijst met tekenreeksen verwacht voor een waarde, omdat het parametertype is gedefinieerd als een matrix |
| policyB | allowedSingleLocation |tekenreeks |Op basis van deze parameter wordt één woord verwacht voor een waarde, omdat het parametertype is gedefinieerd als een tekenreeks |

In dit scenario hebt u, bij het definiëren van de initiatiefparameters voor **initiativeC**, drie opties:

- Gebruik de parameters van de beleidsdefinities binnen dit initiatief: in dit voorbeeld worden _allowedLocations_ en _allowedSingleLocation_ initiatiefparameters voor **initiativeC** .
- Geef waarden op voor de parameters van de beleidsdefinities binnen deze initiatiefdefinitie. In dit voorbeeld kunt u een lijst met locaties opgeven naar parameter van **policyA** – **allowedLocations** en parameter van **policyB** – **allowedSingleLocation**. U kunt ook waarden opgeven bij het toewijzen van dit initiatief.
- Geef een lijst met _waarde_ opties op die kunnen worden gebruikt bij het toewijzen van dit initiatief. Als u dit initiatief toewijst, kunnen de overgenomen parameters van de beleidsdefinities binnen het initiatief, alleen waarden hebben uit de opgegeven lijst.

Als u waardeopties maakt in een initiatiefdefinitie, is het niet mogelijk om tijdens het toewijzen van het initiatief andere waarden op te geven, omdat deze niet op de lijst staan.

Zie [Structuur van initiatiefdefinities](./concepts/initiative-definition-structure.md) voor meer informatie over de structuur van initiatiefdefinities.

### <a name="assignments"></a>Toewijzingen

Een toewijzing is een beleidsdefinitie die of initiatief dat is toegewezen om te worden toegepast binnen een bepaald bereik. Dit bereik kan variëren van een [beheergroep](../management-groups/overview.md) tot een afzonderlijke resource. De term _bereik_ verwijst naar alle resources, resourcegroepen, abonnementen of beheergroepen waaraan de definitie is toegewezen. Toewijzingen worden overgenomen door alle onderliggende resources. Dit betekent dat als een definitie wordt toegepast op een resourcegroep, deze ook van toepassing is op alle resources in de resourcegroep. U kunt echter een subbereik uitsluiten van de toewijzing.

U kunt op het abonnementsbereik bijvoorbeeld een definitie toepassen op basis waarvan het maken van netwerkresources wordt voorkomen. U kunt echter één resourcegroep binnen het abonnement uitsluiten, namelijk degene die is bedoeld voor netwerkinfrastructuur. U verleent vervolgens toegang tot deze netwerkresourcegroep aan gebruikers aan wie u het maken van de netwerkresourcegroep toevertrouwt.

In een ander voorbeeld wilt u mogelijk een acceptatielijstdefinitie voor resourcetypen toewijzen op het niveau van de beheergroep. Vervolgens wijst u een ruimer beleid (zodat meer resourcetypen zijn toegestaan) toe via een onderliggende beheergroep of zelfs rechtstreeks in abonnementen. Dit voorbeeld zou echter niet goed werken, omdat Azure Policy een expliciet weigersysteem is. In plaats daarvan moet u de onderliggende beheergroep of het abonnement uitsluiten van de toewijzing op beheergroepniveau. Vervolgens kunt u de ruimere definitie toewijzen aan het niveau van de onderliggende beheergroep of het abonnement. Als toewijzingsresultaten in een resource worden geweigerd, is het aanpassen van de weigeringstoewijzing de enige manier om de resource toe te staan.

Zie [Een beleidstoewijzing maken om niet-compatibele resources in uw Azure-abonnement te identificeren](./assign-policy-portal.md) voor meer informatie over het instellen van toewijzingen via de portal. Er zijn ook stappen voor [PowerShell](./assign-policy-powershell.md) en [Azure CLI](./assign-policy-azurecli.md) beschikbaar. Zie [Toewijzingsstructuur](./concepts/assignment-structure.md) voor meer informatie over de toewijzingsstructuur.

## <a name="maximum-count-of-azure-policy-objects"></a>Maximum aantal Azure Policy-objecten

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht hebt van Azure Policy en enkele van de belangrijkste geïntroduceerde concepten, ziet u hier de voorgestelde volgende stappen:

- [De structuur van beleidsdefinities bekijken](./concepts/definition-structure.md).
- [Een beleidsdefinitie toewijzen via de portal](./assign-policy-portal.md).

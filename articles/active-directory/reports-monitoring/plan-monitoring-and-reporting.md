---
title: Rapporten plannen & bewakings implementatie-Azure AD
description: Hierin wordt beschreven hoe u de implementatie van rapportage en bewaking plant en uitvoert.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: baselden
ms.reviewer: plenzke
ms.collection: M365-identity-device-management
ms.openlocfilehash: 683367251c220abe36660d61463bce9e5a0c52f9
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577757"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>Een Azure Active Directory rapportage en bewakings implementatie plannen

De oplossing voor rapportage en bewaking voor Azure Active Directory (Azure AD) is afhankelijk van uw wettelijke, beveiligings-en operationele vereisten en uw bestaande omgeving en processen. In dit artikel vindt u de verschillende ontwerp opties en wordt u begeleid bij de juiste implementatie strategie.

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Voor delen van Azure AD-rapportage en-bewaking

Azure AD Reporting biedt een uitgebreide weer gave en logboeken van Azure AD-activiteiten in uw omgeving, waaronder aanmeldings gebeurtenissen, controle gebeurtenissen en wijzigingen in uw Directory.

Met de gegevens kunt u het volgende doen:

* Bepaal hoe uw apps en services worden gebruikt.

* mogelijke Risico's detecteren die van invloed zijn op de status van uw omgeving.

* Los problemen op om te voor komen dat uw gebruikers hun werk doen.

* Krijg inzicht door controle gebeurtenissen van wijzigingen in uw Azure AD-Directory te bekijken.

> [!IMPORTANT]
> Met Azure AD-bewaking kunt u uw door Azure AD Reporting gegenereerde logboeken door sturen naar verschillende doel systemen. U kunt deze vervolgens behouden voor later gebruik of integreren met SIEM-hulpprogramma's (Security Information and Event Management) van derden om meer inzicht in uw omgeving te verkrijgen.

Met Azure AD monitoring kunt u Logboeken routeren naar:

* een Azure-opslag account voor archiverings doeleinden.
* Azure Monitor logboeken, voorheen bekend als Azure Log Analytics-werk ruimte, waar u de gegevens kunt analyseren, Dash boards maakt en een waarschuwing voor specifieke gebeurtenissen krijgt.
* een Azure-Event Hub waar u kunt integreren met uw bestaande SIEM-hulpprogram ma's zoals Splunk, Sumologic of QRadar.

> [!NOTE]
We hebben onlangs begonnen met het gebruik van de term Azure Monitor Logboeken in plaats van Log Analytics. Logboekgegevens worden nog steeds opgeslagen in een Log Analytics-werkruimte, en worden nog steeds verzameld en geanalyseerd met dezelfde Log Analytics-service. De terminologie wordt bijgewerkt om de rol van [logboeken in Azure Monitor](../../azure-monitor/data-platform.md) beter te weerspiegelen. Zie [Wijzigingen in Azure Monitor-terminologie](../../azure-monitor/terminology.md) voor meer informatie.

Meer [informatie over het Bewaar beleid voor rapporten](./reference-reports-data-retention.md).

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Licenties en vereisten voor Azure AD-rapportage en-bewaking

U hebt een premium-licentie van Azure AD nodig om toegang te krijgen tot de logboeken voor Azure AD-aanmelding.

Gedetailleerde informatie over functies en licenties vindt u in de [prijsgids voor Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Als u Azure AD-bewaking en-rapportage wilt implementeren, hebt u een gebruiker nodig met de rol van globale beheerder of beveiligingsbeheerder voor de Azure AD-tenant.

Afhankelijk van de uiteindelijke bestemming van uw logboekgegevens, hebt u een van de volgende opties nodig:

* Een Azure-opslagaccount waarop u ListKeys-machtigingen hebt. We raden u aan om een algemeen opslagaccount te gebruiken en geen Blob Storage-account. Zie de [Prijscalculator voor Azure Storage](https://azure.microsoft.com/pricing/calculator/?service=storage) voor prijsinformatie over opslag.

* Een Azure Event Hubs-naamruimte om te integreren met SIEM-oplossingen van derden.

* Een Azure Log Analytics-werkruimte om logboeken naar Azure Monitor-logboeken te verzenden.

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>Een Azure Reporting and monitoring-implementatie project plannen

In dit project definieert u de doel groepen waarmee rapporten worden verbruikt en bewaakt en definieert u uw Azure AD-bewakings architectuur.

### <a name="engage-the-right-stakeholders"></a>De juiste belanghebbenden benaderen

Wanneer technologie projecten mislukken, worden ze doorgaans als gevolg van niet-overeenkomende verwachtingen voor impact, resultaten en verantwoordelijkheden. Als u deze problemen wilt voor komen, moet [u ervoor zorgen dat u de juiste belanghebbenden gebruikt](../fundamentals/active-directory-deployment-plans.md). Zorg er ook voor dat de rol van belanghebbenden in het project goed worden begrepen door de belanghebbenden en hun project invoer en accountabilities te documenteren.

### <a name="plan-communications"></a>De communicatie plannen

Communicatie is van cruciaal belang voor het slagen van een nieuwe service. Communiceer proactief met uw gebruikers hoe hun ervaring verandert, wanneer deze wordt gewijzigd, en hoe u ondersteuning krijgt als u problemen ondervindt.

### <a name="document-your-current-infrastructure-and-policies"></a>Uw huidige infra structuur en beleids regels documenteren

Uw huidige infra structuur en beleids regels zullen uw ontwerp voor rapportage en bewaking stimuleren. Zorg ervoor dat u weet

* Wat, indien van toepassing, SIEM-hulpprogram ma's die u gebruikt.

* Uw Azure-infra structuur, met inbegrip van de bestaande opslag accounts en bewaking die wordt gebruikt.

* Het Bewaar beleid voor uw organisatie voor logboeken, inclusief toepasselijke nalevings raamwerken vereist. 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>Een Azure AD-rapportage en-bewakings implementatie plannen

Rapportage en bewaking worden gebruikt om te voldoen aan uw bedrijfs vereisten, inzicht te krijgen in gebruiks patronen en de beveiligings postuur van uw organisatie te verg Roten.

### <a name="business-use-cases"></a>Zakelijke use cases

* Vereist voor oplossing om te voldoen aan bedrijfs behoeften
* Mooi om te voldoen aan de behoeften van uw bedrijf
* Niet van toepassing

|Gebied |Beschrijving |
|-|-|
|Bewaartermijn| Het **bewaren van meer dan 30 dagen vastleggen**. Vanwege wettelijke of zakelijke vereisten is het vereist om audit logboeken op te slaan en logboeken van Azure AD langer dan 30 dagen te registreren. |
|Analyse| **De logboeken moeten doorzoekbaar zijn**. De opgeslagen logboeken moeten kunnen worden doorzocht met analytische hulpprogram ma's. |
| Operational Insights| **Inzichten voor verschillende teams**. De nood zaak om toegang te verlenen voor verschillende gebruikers om operationeel inzicht te krijgen, zoals toepassings gebruik, aanmeldings fouten, selfservice gebruik, trends, enzovoort. |
| Beveiligings inzichten| **Inzichten voor verschillende teams**. De nood zaak om toegang te verlenen voor verschillende gebruikers om operationeel inzicht te krijgen, zoals het gebruik van toepassingen, het aanmelden van fouten, het gebruik van selfservice Services, trends, enzovoort. |
| Integratie in SIEM-systemen      | **Siem-integratie**. De nood zaak om Azure AD-logboeken en audit logboeken te integreren in en te streamen naar bestaande SIEM-systemen. |

### <a name="choose-a-monitoring-solution-architecture"></a>Een architectuur voor bewakings oplossingen kiezen

Met Azure AD monitoring kunt u uw Azure AD-activiteiten logboeken door sturen naar een systeem dat het beste voldoet aan uw bedrijfs behoeften. U kunt ze vervolgens bewaren voor lange termijn rapportage en analyse om inzicht te krijgen in uw omgeving en deze te integreren met SIEM-hulpprogram ma's.

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sections"></a>Diagram van de beslissings stroom![Een afbeelding met de informatie die in de volgende secties wordt beschreven](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>Logboeken archiveren in een opslag account

Door Logboeken te routeren naar een Azure Storage-account, kunt u ze langer houden dan de standaard Bewaar periode die wordt beschreven in het [Bewaar beleid](./reference-reports-data-retention.md). Gebruik deze methode als u uw logboeken wilt archiveren, maar deze niet wilt integreren met een SIEM-systeem en geen doorlopende query's en analyses nodig hebt. U kunt nog steeds zoeken op aanvraag.

Ontdek hoe u [gegevens routeert naar uw opslagaccount](./quickstart-azure-monitor-route-logs-to-storage-account.md).

#### <a name="send-logs-to-azure-monitor-logs"></a>Logboeken verzenden naar logboeken van Azure Monitor

Met [Azure monitor logboeken](../../azure-monitor/logs/log-query-overview.md) worden controle gegevens uit verschillende bronnen geconsolideerd. Het bevat ook een query taal en analyse-engine waarmee u inzicht krijgt in de werking van uw toepassingen en het gebruik van resources. Door Azure AD-activiteiten logboeken naar Azure Monitor-logboeken te verzenden, kunt u snel verzamelde gegevens ophalen, bewaken en er waarschuwingen op ontvangen. Gebruik deze methode als u geen bestaande SIEM-oplossing hebt waarnaar u uw gegevens rechtstreeks wilt verzenden, maar query's en analyse wilt uitvoeren. Zodra uw gegevens zich in Azure Monitor logboeken bevinden, kunt u deze vervolgens naar Event Hub en van daaruit verzenden naar een SIEM als u wilt.

Lees meer over [het verzenden van gegevens naar de logboeken van Azure Monitor](./howto-integrate-activity-logs-with-log-analytics.md).

U kunt ook de vooraf gemaakte weer gaven voor Azure AD-activiteiten logboeken installeren om veelvoorkomende scenario's met betrekking tot aanmeldings-en controle gebeurtenissen te bewaken.

Ontdek hoe u [Log Analytics-weergaven voor activiteitenlogboeken van Azure AD installeert en gebruikt](./howto-install-use-log-analytics-views.md).

#### <a name="stream-logs-to-your-azure-event-hub"></a>Stream-logboeken naar uw Azure Event Hub

Met routerings logboeken naar een Azure-Event Hub kunt u integratie met SIEM-hulpprogram ma's van derden. Zo kunt u gegevens van Azure Active Directory-activiteitenlogboeken combineren met andere gegevens die worden beheerd door uw SIEM en meer inzicht krijgen in uw omgeving. 

Ontdek hoe u [logboeken streamt naar een Event Hub](./tutorial-azure-monitor-stream-logs-to-event-hub.md).

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>Plan bewerkingen en beveiliging voor Azure AD-rapportage en-bewaking

De belanghebbenden moeten toegang krijgen tot Azure AD-Logboeken om operationeel inzicht te krijgen. Waarschijnlijk bevatten gebruikers beveiligings team leden, interne of externe Audi tors en het team voor identiteits-en toegangs beheer.

Met Azure AD-functies kunt u de mogelijkheid om Azure AD-rapporten te configureren en weer te geven op basis van uw rol delegeren. Bepaal wie in uw organisatie toestemming moet geven voor het lezen van Azure AD-rapporten en welke rol geschikt is voor hen. 

De volgende rollen kunnen Azure AD-rapporten lezen:

* Globale beheerder

* Beveiligingsbeheerder

* Beveiligingslezer

* Rapportlezer

Meer informatie over [Azure AD-beheerders rollen](../roles/permissions-reference.md).

*Pas het concept van minimale bevoegdheden altijd toe om het risico van inbreuk op een account te verminderen*. Overweeg het implementeren van [privileged Identity Management](../privileged-identity-management/pim-configure.md) om uw organisatie verder te beveiligen.

##  

## <a name="deploy-azure-ad-reporting-and-monitoring"></a>Azure AD-rapportage en-bewaking implementeren

Afhankelijk van de beslissingen die u eerder hebt gemaakt met behulp van de bovenstaande ontwerp richtlijnen, leidt deze sectie u naar de documentatie over de verschillende implementatie opties.

### <a name="consume-and-archive-azure-ad-logs"></a>Azure AD-Logboeken gebruiken en archiveren

[Activiteitenrapporten zoeken in de Azure-portal](./howto-find-activity-reports.md)

[Azure AD-logboeken archiveren in een Azure Storage-account](./quickstart-azure-monitor-route-logs-to-storage-account.md)

### <a name="implement-monitoring-and-analytics"></a>Bewaking en analyse implementeren

[Logboeken naar Azure Monitor verzenden](./howto-integrate-activity-logs-with-log-analytics.md)

[De log Analytics-weer gaven voor Azure Active Directory installeren en gebruiken](./howto-install-use-log-analytics-views.md)

[Azure AD-activiteiten logboeken analyseren met Azure Monitor-logboeken](./howto-analyze-activity-logs-log-analytics.md)

* [Interpret audit logs schema in Azure Monitor](./reference-azure-monitor-audit-log-schema.md) (Auditlogboekenschema interpreteren in Azure Monitor)

* [Het schema voor logboek registratie interpreteert in Azure Monitor](./reference-azure-monitor-sign-ins-log-schema.md)

 * [Azure AD-logboeken streamen naar een Azure-Event Hub](./tutorial-azure-monitor-stream-logs-to-event-hub.md)

* [Integrate Azure AD logs with Splunk by using Azure Monitor](./howto-integrate-activity-logs-with-splunk.md) (Azure AD-logboeken integreren met Splunk met behulp van Azure Monitor)

* [Integrate Azure AD logs with SumoLogic by using Azure Monitor](./howto-integrate-activity-logs-with-sumologic.md) (Azure AD-logboeken integreren met SumoLogic met behulp van Azure Monitor)

 

 

## <a name="next-steps"></a>Volgende stappen

Overweeg het implementeren van [privileged Identity Management](../privileged-identity-management/pim-configure.md) 

Overweeg [het implementeren van op rollen gebaseerd toegangs beheer van Azure (Azure RBAC)](../../role-based-access-control/overview.md)
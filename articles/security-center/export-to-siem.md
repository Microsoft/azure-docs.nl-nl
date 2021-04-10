---
title: Uw waarschuwingen streamen van Azure Security Center naar Security Information and Event Management-systemen (SIEM) en andere bewakings oplossingen
description: Meer informatie over het streamen van uw beveiligings waarschuwingen naar Azure Sentinel, Siem's, via of ITSM-oplossingen van derden
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/06/2020
ms.author: memildin
ms.openlocfilehash: 3ddc385b9d489e0c2ab4abf35a6ade011970342b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100572943"
---
# <a name="stream-alerts-to-a-siem-soar-or-it-service-management-solution"></a>Waarschuwingen streamen naar een SIEM-, SOAR- of IT Service Management-oplossing

Azure Security Center kunt uw beveiligings waarschuwingen streamen naar de meest populaire Security Information and Event Management (SIEM), Security Orchestration Automated Response (via) en IT Service Management (ITSM)-oplossingen.

Er zijn Azure-systeem eigen hulpprogram ma's om ervoor te zorgen dat u uw waarschuwings gegevens kunt bekijken in alle populairste oplossingen die momenteel worden gebruikt, waaronder:

- **Azure Sentinel**
- **Splunk Enterprise en Splunk Cloud**
- **IBM-QRadar**
- **ServiceNow**
- **ArcSight**
- **Power BI**
- **Palo Alto Networks**

## <a name="stream-alerts-to-azure-sentinel"></a>Waarschuwingen streamen naar Azure Sentinel 

Security Center systeem eigen integreert met Azure Sentinel, de Cloud-native SIEM-en via-oplossing van Azure. 

Meer [informatie over Azure Sentinel](../sentinel/overview.md).

### <a name="azure-sentinels-connectors-for-security-center"></a>Connectors van Azure Sentinel voor Security Center

Azure Sentinel bevat ingebouwde connectors voor Azure Security Center op het niveau van de abonnementen en tenants:

- [Waarschuwingen streamen naar Azure Sentinel op abonnements niveau](../sentinel/connect-azure-security-center.md)
- [Alle abonnementen in uw Tenant verbinden met Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-security-center-auto-connect-to-sentinel/ba-p/1387539) 

### <a name="configure-ingestion-of-all-audit-logs-into-azure-sentinel"></a>Opname van alle audit Logboeken in azure Sentinel configureren 

Een ander alternatief voor het onderzoeken van Security Center waarschuwingen in azure Sentinel is het streamen van uw audit logboeken naar Azure Sentinel:
    - [Verbinding maken met Windows-beveiligingsgebeurtenissen](../sentinel/connect-windows-security-events.md)
    - [Gegevens verzamelen van op Linux gebaseerde bronnen met behulp van syslog](../sentinel/connect-syslog.md)
    - [Gegevens verbinden vanuit Azure-activiteiten logboek](../sentinel/connect-azure-activity.md)

> [!TIP]
> Azure Sentinel wordt gefactureerd op basis van het volume van de gegevens die worden opgenomen voor analyse in azure Sentinel en opgeslagen in de werk ruimte Azure Monitor Log Analytics. Azure Sentinel biedt een flexibel en voorspelbaar prijs model. Meer [informatie vindt u op de pagina met prijzen voor Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).


## <a name="stream-alerts-with-microsoft-graph-security-api"></a>Waarschuwingen streamen met Microsoft Graph-beveiligings-API

Security Center heeft out-of-the-box-integratie met Microsoft Graph-beveiligings-API. Er is geen configuratie vereist en er zijn geen extra kosten. 

U kunt deze API gebruiken om waarschuwingen van uw **hele Tenant** (en gegevens van vele andere micro soft-beveiligings producten) te streamen naar siem's van derden en andere populaire platforms:

- **Splunk Enter prise-en Splunk-Cloud**  -  [Gebruik de Microsoft Graph-beveiligings-API Add-On voor Splunk](https://splunkbase.splunk.com/app/4564/) 
- **Power bi**  -  [Verbinding maken met de Microsoft Graph-beveiligings-API in Power bi Desktop](/power-bi/connect-data/desktop-connect-graph-security)
- **ServiceNow**  -  [Volg de instructies voor het installeren en configureren van de Microsoft Graph-API-toepassing in de ServiceNow-Store](https://docs.servicenow.com/bundle/orlando-security-management/page/product/secops-integration-sir/secops-integration-ms-graph/task/ms-graph-install.html)
- **QRadar**  -  [De ondersteunings module voor apparaten van IBM voor Azure Security Center via Microsoft Graph-API](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/c_dsm_guide_ms_azure_security_center_overview.html) 
- **Palo Alto Networks**, **Anomali**, **Lookout**, **Spark** en meer- [Microsoft Graph Security-API](https://www.microsoft.com/security/business/graph-security-api#office-MultiFeatureCarousel-09jr2ji)

Meer [informatie over Microsoft Graph Security-API](https://www.microsoft.com/security/business/graph-security-api).


## <a name="stream-alerts-with-azure-monitor"></a>Waarschuwingen streamen met Azure Monitor 

Om waarschuwingen te streamen naar **ArcSight**, **Splunk**, **SumoLogic**, Syslog-servers, **LogRhythm**, Logz.io-platform voor naleving van de **Cloud** en andere bewakings oplossingen. Security Center met Azure monitor verbinden via Azure Event Hubs:

1. Schakel [doorlopend exporteren](continuous-export.md) in om Security Center waarschuwingen naar een specifieke Azure Event hub op abonnements niveau te streamen. 
    > [!TIP]
    > Als u dit wilt doen op het niveau van de beheer groep met behulp van Azure Policy, raadpleegt u [doorlopende Automation-configuraties maken op schaal](continuous-export.md?tabs=azure-policy#configure-continuous-export-at-scale-using-the-supplied-policies)

1. [Verbind de Azure Event hub met uw favoriete oplossing met behulp van de ingebouwde connectors van Azure monitor](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

1. U kunt de RAW-logboeken ook streamen naar de Azure Event hub en verbinding maken met uw voorkeurs oplossing. Meer informatie vindt u in [bewakings gegevens die beschikbaar zijn](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#monitoring-data-available).

> [!TIP]
> Als u de gebeurtenis schema's van de geëxporteerde gegevens typen wilt weer geven, gaat u naar event [hub Event schemas](https://aka.ms/ASCAutomationSchemas).


## <a name="next-steps"></a>Volgende stappen

Op deze pagina wordt uitgelegd hoe u ervoor kunt zorgen dat uw Azure Security Center waarschuwings gegevens beschikbaar zijn in uw SIEM-, via-of ITSM-hulp programma. Zie voor gerelateerd materiaal:

- [Wat is Azure Sentinel?](../sentinel/overview.md)
- [Validatie van waarschuwingen in azure Security Center](security-center-alert-validation.md) : Controleer of uw waarschuwingen correct zijn geconfigureerd
- [Security Center-gegevens continu exporteren](continuous-export.md)
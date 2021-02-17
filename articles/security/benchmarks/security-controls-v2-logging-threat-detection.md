---
title: Azure Security Bench Mark v2-logboek registratie en detectie van bedreigingen
description: Azure Security Bench Mark v2-logboek registratie en detectie van bedreigingen
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 2a0ce52c094739ffbbce2f93a41b2533222066b8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576804"
---
# <a name="security-control-v2-logging-and-threat-detection"></a>Beveiligings controle v2: logboek registratie en detectie van bedreigingen

Logboek registratie en detectie van bedreigingen omvatten besturings elementen voor het detecteren van bedreigingen op Azure en het inschakelen, verzamelen en opslaan van audit logboeken voor Azure-Services. Dit omvat het inschakelen van detectie-, onderzoek-en herstel processen met besturings elementen voor het genereren van waarschuwingen met een hoge kwaliteit met systeem eigen bedreigingen detectie in Azure-Services. Het omvat ook het verzamelen van logboeken met Azure Monitor, het centraliseren van beveiligings analyse met Azure Sentinel, tijd synchronisatie en logboek registratie. 

## <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: detectie van bedreigingen inschakelen voor Azure-resources

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| LT-1 | 6.7 | AU-3, AU-6, AU-12, SI-4 |

Zorg ervoor dat u verschillende typen Azure-assets bewaakt voor mogelijke dreigingen en afwijkingen. Richt u op het ophalen van waarschuwingen met een hoge kwaliteit om fout-positieven te verminderen voor analisten om te sorteren. Waarschuwingen kunnen worden gebrond op basis van logboek gegevens, agents of andere gegevens.

Gebruik de Azure Security Center ingebouwde functie voor detectie van bedreigingen, die is gebaseerd op de bewaking van Azure service-telemetrie en het analyseren van service Logboeken. Gegevens worden verzameld met behulp van de Log Analytics-agent, die verschillende aan beveiliging gerelateerde configuraties en gebeurtenis logboeken van het systeem leest en de gegevens naar uw werk ruimte kopieert voor analyse. 

Daarnaast kunt u Azure Sentinel gebruiken om analyse regels te bouwen, die de bedreigingen doorzoeken die overeenkomen met specifieke criteria in uw omgeving. De regels genereren incidenten wanneer de criteria overeenkomen, zodat u elk incident kunt onderzoeken. Met Azure Sentinel kan ook bedreigings informatie van derden worden geïmporteerd om de detectie mogelijkheden van bedreigingen te verbeteren. 

- [Bescherming tegen bedreiging in Azure Security Center](../../security-center/azure-defender.md)

- [Naslag Gids voor beveiligings waarschuwingen Azure Security Center](../../security-center/alerts-reference.md)

- [Aangepaste analyse regels maken voor het detecteren van bedreigingen](../../sentinel/tutorial-detect-threats-custom.md)

- [Cyber Threat Intelligence met Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastructuur- en eindpuntbeveiliging](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Beveiligingsbewerkingen](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Postuurbeheer](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Toepassings beveiliging en DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Bedreigings informatie](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Detectie van bedreigingen inschakelen voor Azure identiteits- en toegangsbeheer

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| LT-2 | 6.8 | AU-3, AU-6, AU-12, SI-4 |

Azure AD biedt de volgende gebruikers logboeken die kunnen worden weer gegeven in azure AD Reporting of geïntegreerd met Azure Monitor, Azure Sentinel of andere hulpprogram ma's voor SIEM/controle voor geavanceerde bewakings-en analyse cases: 
-   Aanmeldingen: het rapport voor aanmeldingsactiviteit bevat informatie over het gebruik van beheerde toepassingen en aanmeldingsactiviteiten van gebruikers

-   Auditlogboeken: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voorbeelden van vermeldingen in auditlogboeken zijn wijzigingen die worden aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleidsregels.

-   Riskante aanmeldingen - Een riskante aanmelding is een indicator van een aanmeldingspoging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is.

-   Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Azure Security Center kan ook worden gewaarschuwd voor bepaalde verdachte activiteiten, zoals een uitzonderlijk aantal mislukte verificatie pogingen en afgeschafte accounts in het abonnement. Naast de basis bewaking van de beveiligings hygiëne, kan de bedreigings beveiligings module van Azure Security Center ook uitgebreidere beveiligings waarschuwingen verzamelen van afzonderlijke Azure Compute-resources (zoals virtuele machines, containers, app service), gegevens bronnen (zoals SQL DB en opslag) en Azure-service lagen. Met deze mogelijkheid kunt u rekening afwijkingen in de afzonderlijke resources bekijken.

- [Activiteiten rapporten controleren in azure AD](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure AD Identity Protection inschakelen](../../active-directory/identity-protection/overview-identity-protection.md)

- [Bescherming tegen bedreiging in Azure Security Center](../../security-center/azure-defender.md)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastructuur- en eindpuntbeveiliging](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Beveiligingsbewerkingen](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Postuurbeheer](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Toepassings beveiliging en DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Bedreigings informatie](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Logboekregistratie inschakelen voor Azure-netwerkactiviteiten

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| LT-3 | 9,3, 12,2, 12,5, 12,8 | AU-3, AU-6, AU-12, SI-4 |

NSG-bron Logboeken (netwerk beveiligings groep), NSG-stroom logboeken, Azure Firewall-logboeken en Web Application firewall-Logboeken (WAF) inschakelen en verzamelen voor beveiligings analyse ter ondersteuning van incident onderzoek, de jacht van dreigingen en het genereren van beveiligings waarschuwingen. U kunt de stroom logboeken naar een Azure Monitor Log Analytics werk ruimte verzenden en vervolgens Traffic Analytics gebruiken om inzichten te geven. Zorg ervoor dat u DNS-query logboeken verzamelt om te helpen bij het correleren van andere netwerk gegevens.

- [Stroom logboeken van netwerk beveiligings groepen inschakelen](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure Firewall-logboeken en metrische gegevens](../../firewall/logs-and-metrics.md)

- [Traffic Analytics inschakelen en gebruiken](../../network-watcher/traffic-analytics.md)

- [Controleren met Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)

- [Azure-netwerk bewakings oplossingen in Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md)

- [Verzamel inzichten over uw DNS-infra structuur met de DNS-analyse oplossing](../../azure-monitor/insights/dns-analytics.md)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastructuur- en eindpuntbeveiliging](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Beveiligingsbewerkingen](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Postuurbeheer](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Toepassings beveiliging en DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Bedreigings informatie](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Logboekregistratie inschakelen voor Azure-resources

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| LT-4 | 6,2, 6,3, 8,8 | AU-3, AU-12 |

Schakel logboek registratie in voor Azure-resources om te voldoen aan de vereisten voor naleving, detectie van bedreigingen, jacht en incident onderzoek. 

U kunt Azure Security Center en Azure Policy gebruiken om resource Logboeken in te scha kelen en gegevens verzamelen op Azure-resources te registreren voor toegang tot controle-, beveiligings-en bron Logboeken. Activiteiten logboeken, die automatisch beschikbaar zijn, omvatten gebeurtenis bron, datum, gebruiker, tijds tempel, bron adressen, doel adressen en andere nuttige elementen. 

- [Logboek registratie en verschillende logboek typen in azure](../../azure-monitor/essentials/platform-logs-overview.md)

- [Meer informatie over het verzamelen van Azure Security Center gegevens](../../security-center/security-center-enable-data-collection.md)

**Verantwoordelijkheid**: Gedeeld

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beveiligingsbewerkingen](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

Infrastructuur- en eindpuntbeveiliging 

- [Toepassings beveiliging en DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Bedreigings informatie](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Beheer en analyse van beveiligingslogboek centraliseren

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| LT-5 | 6,5, 6,6 | AU-3, SI-4 |

Centraliseer logboek opslag en-analyse om correlatie in te scha kelen. Zorg ervoor dat u voor elke logboek bron een gegevens eigenaar hebt toegewezen, toegangs richtlijnen, opslag locatie, welke hulpprogram ma's worden gebruikt voor het verwerken en openen van de gegevens en vereisten voor gegevens behoud.

Zorg ervoor dat u Azure-activiteiten logboeken integreert in uw centrale logboek registratie. Opname logboeken via Azure Monitor voor het verzamelen van beveiligings gegevens die zijn gegenereerd door eindpunt apparaten, netwerk bronnen en andere beveiligings systemen. Gebruik in Azure Monitor Log Analytics-werk ruimten om analyses uit te voeren en te uitvoeren en gebruik Azure Storage accounts voor lange termijn-en archiverings opslag.

Daarnaast kunt u gegevens inschakelen en vrijgeven aan Azure Sentinel of een SIEM van derden.

Veel organisaties kiezen voor het gebruik van Azure Sentinel voor ' hot ' gegevens die regel matig worden gebruikt en die worden Azure Storage voor ' koude ' gegevens die minder vaak worden gebruikt. 

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

- [Azure-Sentinel onboarden](../../sentinel/quickstart-onboard.md)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beveiligingsarchitectuur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Toepassings beveiliging en DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Infrastructuur- en eindpuntbeveiliging](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="lt-6-configure-log-storage-retention"></a>LT-6: Bewaarperiode voor logboek configureren

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| LT-6 | 6.4 | AU-3, AU-11 |

Configureer de Bewaar periode voor het logboek volgens uw naleving, regelgeving en zakelijke vereisten. 

In Azure Monitor kunt u de Bewaar periode voor uw Log Analytics werk ruimte instellen volgens de nalevings voorschriften van uw organisatie. Gebruik Azure Storage, Data Lake of Log Analytics werkruimte accounts voor lange termijn-en archiverings opslag.

- [De Bewaar periode voor gegevens wijzigen in Log Analytics](../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Bewaar beleid configureren voor logboeken van Azure Storage-account](../../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Azure Security Center-waarschuwingen en aanbevelingen exporteren](../../security-center/continuous-export.md)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beveiligingsarchitectuur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Toepassings beveiliging en DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Beveiligingsbewerkingen](/azure/cloud-adoption-framework/organize/cloud-security-operations-center) 

- [Beveiligingsnalevingsbeheer](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="lt-7-use-approved-time-synchronization-sources"></a>LT-7: goedgekeurde tijd synchronisatie bronnen gebruiken

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| LT-7 | 6.1 | AU-8 |

Micro soft onderhoudt tijd bronnen voor de meeste Azure PaaS-en SaaS-Services. Gebruik voor uw virtuele machines micro soft standaard NTP-server voor tijd synchronisatie, tenzij u een specifieke vereiste hebt.  Als u uw eigen NTP-server (Network Time Protocol) wilt maken, moet u ervoor zorgen dat u de UDP-service poort 123 beveiligt.

Alle logboeken die zijn gegenereerd door resources binnen Azure bieden tijds tempels met de tijd zone die standaard is opgegeven.

- [Tijd synchronisatie configureren voor Azure Windows Compute-resources](../../virtual-machines/windows/time-sync.md)

- [Tijd synchronisatie configureren voor Azure Linux-reken resources](../../virtual-machines/linux/time-sync.md)

- [Binnenkomende UDP voor Azure-Services uitschakelen](https://support.microsoft.com/help/4558520/how-to-disable-inbound-udp-for-azure-services)

**Verantwoordelijkheid**: Gedeeld

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beleid en standaarden](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [Toepassings beveiliging en DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Infrastructuur- en eindpuntbeveiliging](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)
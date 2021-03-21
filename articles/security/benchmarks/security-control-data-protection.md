---
title: Azure-beveiligings beheer-gegevens beveiliging
description: Gegevens beveiliging van Azure-beveiligings beheer
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: d36ed6a795c5fa2241ee71751053a4cb5986aaf0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100576825"
---
# <a name="security-control-data-protection"></a>Beveiligings beheer: gegevens beveiliging

Aanbevelingen voor gegevens beveiliging zijn gericht op het adresseren van problemen met betrekking tot versleuteling, Toegangs beheer lijsten, op id's gebaseerd toegangs beheer en controle logboek registratie voor gegevens toegang.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 4.1 | 13.1 | Klant |

Gebruik Tags om Azure-resources te helpen bij het bijhouden of verwerken van gevoelige informatie.

- [Tags maken en gebruiken](../../azure-resource-manager/management/tag-resources.md)

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 4.2 | 13,2, 2,10 | Klant |

Implementeer isolatie met afzonderlijke abonnementen en beheer groepen voor afzonderlijke beveiligings domeinen, zoals omgevings type en gegevens gevoeligheids niveau. U kunt het toegangs niveau voor uw Azure-resources beperken die worden vereist door uw toepassingen en bedrijfs omgevingen. U kunt de toegang tot Azure-resources beheren via Azure op rollen gebaseerd toegangs beheer (Azure RBAC). 

- [Aanvullende Azure-abonnementen maken](../../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../../azure-resource-manager/management/tag-resources.md)

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 4.3 | 13,3 | Gedeeld |

Maak gebruik van een oplossing van een derde partij in azure Marketplace op netwerk verbindingen die monitors voor niet-geautoriseerde overdracht van gevoelige informatie en blok keren van dergelijke overdrachten tijdens het waarschuwen van informatie beveiliging.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en beschermd tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../fundamentals/protection-customer-data.md)

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 4.4 | 14.4 | Gedeeld |

Versleutel alle gevoelige gegevens tijdens de overdracht. Zorg ervoor dat alle clients die verbinding maken met uw Azure-resources, TLS 1,2 of hoger kunnen onderhandelen.

Volg Azure Security Center aanbevelingen voor het versleutelen van de rest en de versleuteling in de door Voer, indien van toepassing.

- [Meer informatie over versleuteling in transit met Azure](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 4.5 | 14.5 | Gedeeld |

Als er geen functie beschikbaar is voor uw specifieke service in azure, gebruikt u een actief detectie hulpprogramma van derden om alle gevoelige informatie te identificeren die is opgeslagen, verwerkt of verzonden door de technologie systemen van de organisatie, met inbegrip van de locaties op locatie of op een externe service provider, en de gevoelige informatie-inventaris van de organisatie bij te werken.

Gebruik Azure Information Protection voor het identificeren van gevoelige gegevens in Microsoft 365 documenten.

Gebruik Azure SQL Information Protection om u te helpen bij het classificeren en labelen van informatie die is opgeslagen in Azure SQL Database.

- [Azure SQL-gegevensdetectie implementeren](../../azure-sql/database/data-discovery-and-classification-overview.md)

- [Azure Information Protection implementeren](/azure/information-protection/deployment-roadmap)

- [Informatie over beveiliging van klantgegevens in Azure](../fundamentals/protection-customer-data.md)

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 4,6 | 14,6 | Klant |

Gebruik Azure RBAC (op rollen gebaseerd toegangs beheer) om de toegang tot gegevens en resources te beheren. anders kunt u gebruikmaken van specifieke methoden voor toegangs beheer op basis van een service.

- [Azure RBAC configureren](../../role-based-access-control/role-assignments-portal.md)

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 4.7 | 14.7 | Gedeeld |

Als dat nodig is voor de naleving van de reken bronnen, implementeert u een hulp programma van derden, zoals een geautomatiseerde oplossing voor gegevens verlies op basis van een host voor het afdwingen van toegangs beheer voor gegevens, zelfs wanneer gegevens worden gekopieerd uit een systeem.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klantgegevens in Azure](../fundamentals/protection-customer-data.md)

## <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 4.8 | 14.8 | Klant |

Gebruik versleuteling in rust voor alle Azure-resources. Micro soft raadt u aan Azure te laten beheren van uw versleutelings sleutels, maar in sommige gevallen kunt u ook uw eigen sleutels beheren. 

- [Meer informatie over versleuteling van data-at-rest in Azure](../fundamentals/encryption-atrest.md)

- [Door de klant beheerde versleutelings sleutels configureren](../../storage/common/customer-managed-keys-configure-key-vault.md)

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 4.9 | 14.9 | Klant |

Gebruik Azure Monitor met het Azure-activiteiten logboek om waarschuwingen te maken voor wanneer wijzigingen worden aangebracht in essentiële Azure-resources.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](../../azure-monitor/alerts/alerts-activity-log.md)


## <a name="next-steps"></a>Volgende stappen

- Zie het volgende beveiligings beheer:  [beveiligings beheer](security-control-vulnerability-management.md)
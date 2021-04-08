---
title: Advanced Threat Protection
titleSuffix: Azure SQL Database, SQL Managed Instance, & Azure Synapse Analytics
description: Geavanceerde bedreigingen beveiliging detecteert afwijkende database activiteiten die duiden op mogelijke beveiligings Risico's in Azure SQL Database, Azure SQL Managed instance en Azure Synapse Analytics.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, sstein
ms.date: 12/01/2020
tags: azure-synapse
ms.openlocfilehash: 931e914cd3c184136395a9bb9a7e148a90e9fb91
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96461937"
---
# <a name="advanced-threat-protection-for-azure-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Geavanceerde bedreigings beveiliging voor Azure SQL Database, SQL Managed instance en Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Advanced Threat Protection voor [Azure SQL database](sql-database-paas-overview.md), [Azure SQL Managed instance](../managed-instance/sql-managed-instance-paas-overview.md) en [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) detecteert afwijkende activiteiten die duiden op ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot data bases of deze te exploiteren.

Advanced Threat Protection maakt deel uit van de [Azure Defender voor SQL](azure-defender-for-sql.md) -aanbieding, een uniform pakket voor geavanceerde SQL-beveiligings mogelijkheden. Geavanceerde beveiliging tegen bedreigingen kan worden geopend en beheerd via de centrale Azure Defender voor SQL-Portal.

## <a name="overview"></a>Overzicht

Advanced Threat Protection biedt een nieuwe beveiligingslaag, waarmee klanten potentiële bedreigingen kunnen detecteren en erop reageren zodra ze zich voordoen door beveiligings waarschuwingen te geven over afwijkende activiteiten. Gebruikers ontvangen een waarschuwing bij verdachte database activiteiten, potentiële kwetsbaar heden en SQL-injectie aanvallen, evenals afwijkende database toegang en query patronen. Advanced Threat Protection integreert waarschuwingen met [Azure Security Center](https://azure.microsoft.com/services/security-center/), waaronder Details van verdachte activiteiten en aanbevolen actie voor het onderzoeken en oplossen van de dreiging. Geavanceerde beveiliging tegen bedreigingen maakt het eenvoudig om mogelijke bedreigingen te verhelpen voor de data base zonder dat u een beveiligings expert hoeft te zijn of om geavanceerde beveiligings bewakings systemen te beheren.

Voor een volledige onderzoek is het raadzaam om controle in te scha kelen, waarmee database gebeurtenissen worden geschreven naar een audit logboek in uw Azure Storage-account.  Zie [controle voor Azure SQL database en Azure Synapse](../../azure-sql/database/auditing-overview.md) of [controle voor Azure SQL Managed instance](../managed-instance/auditing-configure.md)voor meer informatie over het inschakelen van controle.

## <a name="alerts"></a>Waarschuwingen

Advanced Threat Protection voor Azure SQL Database detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot of misbruik te maken van data bases. Zie de [waarschuwingen voor SQL database en Azure Synapse Analytics in azure Security Center](../../security-center/alerts-reference.md#alerts-sql-db-and-warehouse)voor een lijst met waarschuwingen voor Azure SQL database.

## <a name="explore-detection-of-a-suspicious-event"></a>Detectie van een verdachte gebeurtenis verkennen

U ontvangt een e-mail melding wanneer er afwijkende database activiteiten worden gedetecteerd. Het e-mail bericht bevat informatie over de verdachte beveiligings gebeurtenis, inclusief de aard van de afwijkende activiteiten, de database naam, de server naam, de toepassings naam en de tijd van de gebeurtenis. Daarnaast bevat het e-mail bericht informatie over mogelijke oorzaken en aanbevolen acties voor het onderzoeken en oplossen van de mogelijke bedreiging voor de data base.

![Rapport afwijkende activiteiten](./media/threat-detection-overview/anomalous_activity_report.png)

1. Klik op de koppeling **recente SQL-waarschuwingen weer geven** in het e-mail bericht om de Azure portal te starten en de pagina Azure Security Center waarschuwingen weer te geven. Deze bevat een overzicht van actieve bedreigingen die zijn gedetecteerd op de data base.

   ![Bedreigingen voor activiteit](./media/threat-detection-overview/active_threats.png)

1. Klik op een specifieke waarschuwing voor aanvullende details en acties voor het onderzoeken van deze dreiging en het oplossen van toekomstige bedreigingen.

   SQL-injectie is bijvoorbeeld een van de meest voorkomende beveiligings problemen voor webtoepassingen op Internet die wordt gebruikt om gegevensgestuurde toepassingen aan te vallen. Aanvallers profiteren van toepassings lekken om schadelijke SQL-instructies in te voeren in velden voor toepassings invoer, de gegevens in de data base te schenden of te wijzigen. Voor SQL-injectie waarschuwingen bevat de details van de waarschuwing de kwets bare SQL-instructie die is misbruikt.

   ![Specifieke waarschuwing](./media/threat-detection-overview/specific_alert.png)

## <a name="explore-alerts-in-the-azure-portal"></a>Waarschuwingen in de Azure Portal verkennen

Geavanceerde bedreigings beveiliging integreert de waarschuwingen met [Azure Security Center](https://azure.microsoft.com/services/security-center/). Live SQL Advanced Threat Protection-tegels in de data base en SQL Azure Defender-blades in de Azure Portal de status van actieve bedreigingen bijhouden.

Klik op **Geavanceerde bedreigings beveiliging waarschuwing** om de pagina Azure Security Center waarschuwingen te starten en een overzicht te krijgen van actieve SQL-bedreigingen die zijn gedetecteerd op de data base.

:::image type="content" source="media/azure-defender-for-sql/advanced-threat-protection-alerts.png" alt-text="geavanceerde beveiligings waarschuwingen voor bedreigingen in het overzicht van de data base":::

:::image type="content" source="media/azure-defender-for-sql/advanced-threat-protection.png" alt-text="geavanceerde beveiliging tegen bedreigingen in Security Center":::

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [geavanceerde beveiliging tegen bedreigingen vindt u in Azure SQL Database & Azure Synapse](threat-detection-configure.md).
- Meer informatie over [geavanceerde bedreigingen beveiliging in Azure SQL Managed instance](../managed-instance/threat-detection-configure.md).
- Lees meer informatie over [Azure Defender voor SQL](azure-defender-for-sql.md).
- Meer informatie over het [controleren van Azure SQL database](../../azure-sql/database/auditing-overview.md)
- Meer informatie over [Azure Security Center](../../security-center/security-center-introduction.md)
- Zie de [pagina met prijzen voor Azure SQL database](https://azure.microsoft.com/pricing/details/sql-database/) voor meer informatie over prijzen.
---
title: Advanced Threat Protection configureren
description: Geavanceerde bedreigings beveiliging detecteert afwijkende database activiteiten die potentiële beveiligings dreigingen aan de data base in Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019, sqldbrb=1
ms.topic: how-to
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 12/01/2020
ms.openlocfilehash: 1425003c718ca52c0bea712e9d25cd3e4c035cf1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96453962"
---
# <a name="configure-advanced-threat-protection-for-azure-sql-database"></a>Geavanceerde bedreigings beveiliging voor Azure SQL Database configureren
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Advanced Threat Protection](threat-detection-overview.md) voor Azure SQL database detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot of misbruik te maken van data bases. Geavanceerde beveiliging tegen bedreigingen kan leiden tot **mogelijke SQL-injecties**, **toegang vanaf ongebruikelijke locatie of Data Center**, **toegang tot een onbekende principal of mogelijk schadelijke toepassing** en **SQL-referenties** voor de beveiligings aanval: Bekijk meer informatie in de [waarschuwingen voor geavanceerde bedreigingen](threat-detection-overview.md#alerts).

U kunt meldingen over de gedetecteerde bedreigingen ontvangen via [e-mail meldingen](threat-detection-overview.md#explore-detection-of-a-suspicious-event) of [Azure Portal](threat-detection-overview.md#explore-alerts-in-the-azure-portal)

[Advanced Threat Protection](threat-detection-overview.md) maakt deel uit van de [Azure Defender voor SQL](azure-defender-for-sql.md) -aanbieding, een uniform pakket voor geavanceerde SQL-beveiligings mogelijkheden. Geavanceerde beveiliging tegen bedreigingen kan worden geopend en beheerd via de centrale Azure Defender voor SQL-Portal.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Geavanceerde beveiliging tegen bedreigingen instellen in de Azure Portal

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
2. Ga naar de configuratie pagina van de server die u wilt beveiligen. Selecteer **Security Center** in de beveiligings instellingen.
3. Op de pagina **Azure Defender voor SQL** -configuratie:

   - Schakel **Azure Defender voor SQL** op de server in.
   - Geef in de **instellingen voor geavanceerde bedreigingen beveiliging** de lijst met e-mail berichten op waarvoor beveiligings waarschuwingen moeten worden ontvangen bij het detecteren van afwijkende database activiteiten in het tekstvak **waarschuwingen verzenden naar** .
   
   :::image type="content" source="media/azure-defender-for-sql/set-up-advanced-threat-protection.png" alt-text="geavanceerde bedreigings beveiliging instellen":::

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Advanced Threat Protection instellen met behulp van PowerShell

Zie voor een script voorbeeld [controle configureren en geavanceerde bedreigingen beveiliging met behulp van Power shell](scripts/auditing-threat-detection-powershell-configure.md).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [geavanceerde beveiliging tegen bedreigingen](threat-detection-overview.md).
- Meer informatie over [Advanced Threat Protection in SQL Managed instance](../managed-instance/threat-detection-configure.md).  
- Lees meer informatie over [Azure Defender voor SQL](azure-defender-for-sql.md).
- Meer informatie over [auditing](../../azure-sql/database/auditing-overview.md)
- Meer informatie over [Azure Security Center](../../security-center/security-center-introduction.md)
- Zie de [pagina met prijzen voor SQL database](https://azure.microsoft.com/pricing/details/sql-database/) voor meer informatie over prijzen.
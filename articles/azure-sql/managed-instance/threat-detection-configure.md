---
title: Advanced Threat Protection configureren
titleSuffix: Azure SQL Managed Instance
description: Geavanceerde bedreigingen beveiliging detecteert afwijkende database activiteiten die potentiële beveiligings dreigingen aan de data base in Azure SQL Managed instance aanduiden.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 12/01/2020
ms.openlocfilehash: 69bebcf872f55055117acf5cef410d1f89eafe34
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446916"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-managed-instance"></a>Geavanceerde bedreigingen beveiliging configureren in Azure SQL Managed instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Advanced Threat Protection](../database/threat-detection-overview.md) voor een [Azure SQL Managed instance](sql-managed-instance-paas-overview.md) detecteert afwijkende activiteiten die duiden op ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot data bases of deze te exploiteren Geavanceerde beveiliging tegen bedreigingen kan leiden tot **mogelijke SQL-injecties**, **toegang vanaf ongebruikelijke locatie of Data Center**, **toegang tot een onbekende principal of mogelijk schadelijke toepassing** en **SQL-referenties** voor de beveiligings aanval: Bekijk meer informatie in de [waarschuwingen voor geavanceerde bedreigingen](../database/threat-detection-overview.md#alerts).

U kunt meldingen over de gedetecteerde bedreigingen ontvangen via [e-mail meldingen](../database/threat-detection-overview.md#explore-detection-of-a-suspicious-event) of [Azure Portal](../database/threat-detection-overview.md#explore-alerts-in-the-azure-portal)

[Advanced Threat Protection](../database/threat-detection-overview.md) maakt deel uit van de [Azure Defender voor SQL](../database/azure-defender-for-sql.md)  -aanbieding, een uniform pakket voor geavanceerde SQL-beveiligings mogelijkheden. Geavanceerde beveiliging tegen bedreigingen kan worden geopend en beheerd via de centrale Azure Defender voor SQL-Portal.

##  <a name="azure-portal"></a>Azure Portal

1. Meld u aan bij de  [Azure Portal](https://portal.azure.com). 
2. Ga naar de configuratie pagina van het exemplaar van het SQL-beheerde exemplaar dat u wilt beveiligen. Onder **beveiliging** selecteert u **Security Center**.
3. Op de pagina Azure Defender voor SQL-configuratie
   - Schakel Azure Defender voor SQL **in** .
   - Configureer de **waarschuwingen verzenden naar** e-mail adres voor het ontvangen van beveiligings waarschuwingen bij het detecteren van afwijkende database activiteiten.
   - Selecteer het **Azure-opslag account** waarin records voor afwijkende bedreigings controle worden opgeslagen.
   - Selecteer de **Geavanceerde beveiligings typen voor bedreigingen** die u wilt configureren. Meer informatie over [Geavanceerde beveiligings waarschuwingen voor bedreigingen](../database/threat-detection-overview.md).
4. Klik op **Opslaan** om het nieuwe of bijgewerkte Azure Defender voor SQL-beleid op te slaan.

   :::image type="content" source="../database/media/azure-defender-for-sql/set-up-advanced-threat-protection-mi.png" alt-text="geavanceerde bedreigings beveiliging instellen":::

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [geavanceerde beveiliging tegen bedreigingen](../database/threat-detection-overview.md).
- Zie [Wat is een Azure SQL Managed instance](sql-managed-instance-paas-overview.md)(Engelstalig) voor meer informatie over beheerde exemplaren.
- Meer informatie over [geavanceerde beveiliging tegen bedreigingen voor Azure SQL database](../database/threat-detection-configure.md).
- Meer informatie over [SQL Managed instance auditing](./auditing-configure.md).
- Meer informatie over [Azure Security Center](../../security-center/security-center-introduction.md).
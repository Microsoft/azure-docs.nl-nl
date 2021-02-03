---
title: Azure Defender voor SQL
description: Meer informatie over de functionaliteit voor het beheren van uw database problemen en het detecteren van afwijkende activiteiten die kunnen wijzen op een bedreiging voor uw data base in Azure SQL Database, Azure SQL Managed instance of Azure Synapse.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.date: 02/02/2021
ms.openlocfilehash: 48df96373554f6e474c3835bf81e38a9aea5450c
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508803"
---
# <a name="azure-defender-for-sql"></a>Azure Defender voor SQL
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure Defender for SQL is een geïntegreerd pakket voor geavanceerde SQL-beveiligingsmogelijkheden. Azure Defender is beschikbaar voor Azure SQL Database, Azure SQL Managed instance en Azure Synapse Analytics. Het bevat functionaliteit voor het detecteren en classificeren van gevoelige gegevens, het zichtbaar maken en inperken van potentiële beveiligingsproblemen in uw database, en het detecteren van afwijkende activiteiten die kunnen duiden op een bedreiging van de database. Het is tevens een centraal punt voor het inschakelen en beheren van deze mogelijkheden.

## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Wat zijn de voordelen van Azure Defender voor SQL?

Azure Defender biedt een aantal geavanceerde SQL-beveiligings mogelijkheden, waaronder evaluatie van SQL-beveiligings problemen en geavanceerde beveiliging tegen bedreigingen.
- De [evaluatie van beveiligings problemen](sql-vulnerability-assessment.md) is een eenvoudig te configureren service die u kunt gebruiken om potentiële database problemen op te lossen, op te sporen en te verhelpen. Het biedt inzicht in uw beveiligings status en bevat stappen die kunnen worden uitgevoerd om beveiligings problemen op te lossen en uw data base-Fortifications te verbeteren.
- [Advanced Threat Protection](threat-detection-overview.md) detecteert vreemde activiteiten die duiden op ongebruikelijke en mogelijk schadelijke pogingen om toegang te verkrijgen tot of op aanvallen op uw databases. Het controleert uw data base voortdurend op verdachte activiteiten en biedt onmiddellijke beveiligings waarschuwingen voor mogelijke beveiligings problemen, Azure SQL-injectie aanvallen en afwijkende database toegangs patronen. Meldingen van Advanced Threat Protection bevatten detailinformatie over verdachte activiteiten en aanbevelingen voor het onderzoeken en tegenhouden ervan.

Schakel Azure Defender voor SQL in om al deze opgenomen functies in te schakelen. Met één klik kunt u Azure Defender inschakelen voor alle data bases op uw [Server](logical-servers.md) in azure of in uw door SQL beheerde exemplaar. Het inschakelen of beheren van Azure Defender-instellingen vereist deel uitmaken van de rol [SQL Security Manager](../../role-based-access-control/built-in-roles.md#sql-security-manager) of een van de data base-of Server beheerders rollen.

Zie de [pagina met prijzen voor Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/)voor meer informatie over de prijzen van Azure Defender voor SQL.

## <a name="enable-azure-defender"></a>Azure Defender inschakelen

Azure Defender kan worden geopend via de [Azure Portal](https://portal.azure.com). Schakel Azure Defender in door naar **Security Center** te navigeren in de kop **Security** voor uw server of het beheerde exemplaar.

> [!NOTE]
> Er wordt automatisch een opslag account gemaakt en geconfigureerd voor het opslaan van de scan resultaten voor de **evaluatie van beveiligings problemen** . Als u Azure Defender al hebt ingeschakeld voor een andere server in dezelfde resource groep en regio, wordt het bestaande opslag account gebruikt.
>
> De kosten van Azure Defender zijn afgestemd op Azure Security Center prijs van de Standard-laag per knoop punt, waarbij een knoop punt de volledige server of het hele beheerde exemplaar is. U betaalt dus slechts één keer voor het beveiligen van alle data bases op de server of het beheerde exemplaar met Azure Defender. U kunt Azure Defender in eerste instantie uitproberen met een gratis proef versie.

:::image type="content" source="media/azure-defender-for-sql/enable-azure-defender.png" alt-text="Azure Defender voor SQL inschakelen vanuit Azure SQL-data bases":::

## <a name="track-vulnerabilities-and-investigate-threat-alerts"></a>Beveiligings problemen bijhouden en waarschuwingen voor bedreigingen onderzoeken

Klik op de kaart met de **evaluatie van beveiligings** problemen voor het weer geven en beheren van beveiligings problemen en rapporten en voor het bijhouden van uw beveiligings stature. Als er beveiligings waarschuwingen zijn ontvangen, klikt u op de **Advanced Threat Protection** -kaart om de details van de waarschuwingen weer te geven en een geconsolideerd rapport te bekijken over alle waarschuwingen in uw Azure-abonnement via de pagina Azure Security Center Security Alerts.

## <a name="manage-azure-defender-settings"></a>Azure Defender-instellingen beheren

Azure Defender-instellingen weer geven en beheren:

1. Selecteer **Security Center** uit het gebied **beveiliging** van de server of het beheerde exemplaar.

    Op deze pagina ziet u de status van Azure Defender voor SQL:

    :::image type="content" source="media/azure-defender-for-sql/status-of-defender-for-sql.png" alt-text="De status van Azure Defender voor SQL in Azure SQL-data bases controleren":::

1. Als Azure Defender voor SQL is ingeschakeld, ziet u een koppeling **configureren** zoals wordt weer gegeven in de vorige afbeelding. Als u de instellingen voor Azure Defender voor SQL wilt bewerken, selecteert u **configureren**.

    :::image type="content" source="media/azure-defender-for-sql/security-server-settings.png" alt-text="instellingen voor beveiligings server":::

1. Breng de benodigde wijzigingen aan en selecteer **Opslaan**.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [evaluatie van beveiligings problemen](sql-vulnerability-assessment.md)
- Meer informatie over [geavanceerde bedreigingen beveiliging](threat-detection-configure.md)
- Meer informatie over [Azure Security Center](../../security-center/security-center-introduction.md)
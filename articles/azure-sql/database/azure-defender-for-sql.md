---
title: Azure Defender voor SQL
description: Meer informatie over de functionaliteit voor het beheren van uw database problemen en het detecteren van afwijkende activiteiten die kunnen wijzen op een bedreiging voor uw data base in Azure SQL Database, Azure SQL Managed instance of Azure Synapse.
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.date: 03/08/2021
ms.openlocfilehash: 27f17b3d1060e8b693c2a34cdb4643840f1bfd13
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452306"
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
Er zijn meerdere manieren om Azure Defender-plannen in te scha kelen. U kunt dit op abonnements niveau inschakelen (**Aanbevolen**) van:

- [Azure Security Center](#enable-azure-defender-for-azure-sql-database-at-the-subscription-level-from-azure-security-center)
- [Programmatisch met de REST API, Azure CLI, Power shell of Azure Policy](#enable-azure-defender-plans-programatically)

U kunt de functie ook inschakelen op het niveau van de resource zoals beschreven in [Azure Defender voor Azure SQL database inschakelen op het niveau van de resource](#enable-azure-defender-for-azure-sql-database-at-the-resource-level)

### <a name="enable-azure-defender-for-azure-sql-database-at-the-subscription-level-from-azure-security-center"></a>Azure Defender voor Azure SQL Database op abonnements niveau inschakelen vanuit Azure Security Center
Azure Defender voor Azure SQL Database op abonnements niveau in Azure Security Center inschakelen:

1. Ga naar [Azure Portal](https://portal.azure.com) en open **Security Center**.
1. Selecteer in het menu van Security Center **prijzen en instellingen**.
1. Selecteer het betreffende abonnement.
1. Wijzig de instelling abonnement in **op aan**.

    :::image type="content" source="media/azure-defender-for-sql/enable-azure-defender-sql-subscription-level.png" alt-text="Azure Defender voor Azure SQL Database inschakelen op abonnements niveau.":::

1. Selecteer **Opslaan**.


### <a name="enable-azure-defender-plans-programatically"></a>Azure Defender-plannen inschakelen 

De flexibiliteit van Azure biedt een aantal programmatische methoden voor het inschakelen van Azure Defender-plannen. 

Gebruik een van de volgende hulpprogram ma's om Azure Defender in te scha kelen voor uw abonnement: 

| Methode       | Instructies                                                                                                                                       |
|--------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| REST-API     | [Pricings-API](/rest/api/securitycenter/pricings)                                                                                                  |
| Azure CLI    | [az security pricing](/cli/azure/security/pricing)                                                                                                 |
| PowerShell   | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy | [Bundle Pricings](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|              |                                                                                                                                                    |

### <a name="enable-azure-defender-for-azure-sql-database-at-the-resource-level"></a>Azure Defender voor Azure SQL Database op het niveau van de resource inschakelen

We raden u aan Azure Defender-plannen op abonnements niveau in te scha kelen. Dit kan helpen bij het maken van onbeveiligde bronnen. Als u echter een organisatie reden hebt om Azure Defender op server niveau in te scha kelen, moet u de volgende stappen uitvoeren:

1. Open uw server of een beheerd exemplaar vanuit het [Azure Portal](https://portal.azure.com).
1. Selecteer op de kop **beveiliging** **Security Center**.
1. Selecteer **Azure Defender inschakelen voor SQL**.

    :::image type="content" source="media/azure-defender-for-sql/enable-azure-defender.png" alt-text="Schakel Azure Defender voor SQL in vanuit Azure SQL-data bases.":::

> [!NOTE]
> Er wordt automatisch een opslag account gemaakt en geconfigureerd voor het opslaan van de scan resultaten voor de **evaluatie van beveiligings problemen** . Als u Azure Defender al hebt ingeschakeld voor een andere server in dezelfde resource groep en regio, wordt het bestaande opslag account gebruikt.
>
> De kosten van Azure Defender zijn afgestemd op Azure Security Center prijs van de Standard-laag per knoop punt, waarbij een knoop punt de volledige server of het hele beheerde exemplaar is. U betaalt dus slechts één keer voor het beveiligen van alle data bases op de server of het beheerde exemplaar met Azure Defender. U kunt Azure Defender in eerste instantie uitproberen met een gratis proef versie.


## <a name="manage-azure-defender-settings"></a>Azure Defender-instellingen beheren

Azure Defender-instellingen weer geven en beheren:

1. Selecteer **Security Center** uit het gebied **beveiliging** van de server of het beheerde exemplaar.

    Op deze pagina ziet u de status van Azure Defender voor SQL:

    :::image type="content" source="media/azure-defender-for-sql/status-of-defender-for-sql.png" alt-text="De status van Azure Defender voor SQL in Azure SQL-data bases controleren.":::

1. Als Azure Defender voor SQL is ingeschakeld, ziet u een koppeling **configureren** zoals wordt weer gegeven in de vorige afbeelding. Als u de instellingen voor Azure Defender voor SQL wilt bewerken, selecteert u **configureren**.

    :::image type="content" source="media/azure-defender-for-sql/security-server-settings.png" alt-text="Instellingen voor Azure Defender voor SQL.":::

1. Breng de benodigde wijzigingen aan en selecteer **Opslaan**.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [evaluatie van beveiligings problemen](sql-vulnerability-assessment.md)
- Meer informatie over [geavanceerde bedreigingen beveiliging](threat-detection-configure.md)
- Meer informatie over [Azure Security Center](../../security-center/security-center-introduction.md)
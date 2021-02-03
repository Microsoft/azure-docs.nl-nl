---
title: Azure Defender voor SQL - de voordelen en functies
description: Meer informatie over de voordelen en functies van Azure Defender voor SQL.
author: memildin
ms.author: memildin
ms.date: 12/13/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 28ec6659430cfdbc81533f05863ccb0ddc560e32
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508029"
---
# <a name="introduction-to-azure-defender-for-sql"></a>Inleiding tot Azure Defender voor SQL

Azure Defender voor SQL bevat twee Azure Defender-abonnementen die het [gegevensbeveiligingspakket van Azure Security Center uitbreiden](../azure-sql/database/azure-defender-for-sql.md) om uw databases en de bijbehorende gegevens te beveiligen, waar ze zich ook bevinden. 

> [!VIDEO https://www.youtube.com/embed/V7RdB6RSVpc]

## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Releasestatus:|**Azure Defender voor Azure SQL-databaseservers** - algemeen beschikbaar<br>**Azure Defender voor SQL-servers op computers** - algemeen beschikbaar |
|Prijzen:|De twee abonnementen die **Azure Defender voor SQL** vormen, worden gefactureerd zoals wordt weergegeven op [de pagina met prijzen](security-center-pricing.md)|
|Beveiligde SQL-versies:|[SQL op virtuele Azure-machines](../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)<br>[SQL-servers met Azure Arc](/sql/sql-server/azure-arc/overview)<br>On-premises SQL-servers op Windows-computers zonder Azure Arc<br>Azure SQL [individuele databases](../azure-sql/database/single-database-overview.md) en [elastische pools](../azure-sql/database/elastic-pool-overview.md)<br>[Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)<br>[Azure Synapse Analytics (voorheen SQL DW) toegewezen SQL-pool](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)|
|Clouds:|![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Ja](./media/icons/yes-icon.png) US Gov<br>![Ja](./media/icons/yes-icon.png) China Gov (**Gedeeltelijk**: Subset van waarschuwingen en evaluatie van beveiligingsproblemen voor SQL-servers. Er is geen gedragsbedreigingsbeveiliging beschikbaar.)|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>Wat beveiligt Azure Defender voor SQL?

**Azure Defender voor SQL** bestaat uit twee afzonderlijke Azure Defender-abonnementen:

- **Azure Defender voor Azure SQL-databaseservers** beveiligt:
    - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)
    - [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
    - [Toegewezen SQL-pool in Azure Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- **Azure Defender voor SQL-servers op computers** breidt de beveiliging voor uw systeemeigen Azure SQL-servers uit om hybride omgevingen volledig te ondersteunen en SQL-servers (alle ondersteunde versies) die worden gehost in Azure, andere cloudomgevingen en zelfs on-premises machines te beveiligen:
    - [SQL Server on Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/)
    - On-premises SQL-servers:
        - [SQL Server met Azure Arc (preview)](/sql/sql-server/azure-arc/overview)
        - [SQL Server op Windows-computers zonder Azure Arc](../azure-monitor/platform/agent-windows.md)


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Wat zijn de voordelen van Azure Defender voor SQL?

Deze twee abonnementen bevatten functionaliteit voor het identificeren en beperken van mogelijke beveiligingsproblemen van uw databases en het detecteren van afwijkende activiteiten die kunnen duiden op een bedreiging van uw databases:

- [Evaluatie van beveiligingsproblemen](../azure-sql/database/sql-vulnerability-assessment.md): de scanservice waarmee u potentiële zwakke plekken in de beveiliging van de database kunt detecteren, volgen en verhelpen. Evaluatiescans bieden een overzicht van de beveiligingsstatus van uw SQL-machines en details van eventuele beveiligingsresultaten.

- [Geavanceerde beveiliging tegen bedreigingen](../azure-sql/database/threat-detection-overview.md): de detectieservice die uw SQL-servers continu bewaakt als het gaat om bedreigingen, zoals SQL-injectie, brute-force aanvallen en misbruik van bevoegdheden. Deze service biedt actiegerichte beveiligingswaarschuwingen in Azure Security Center met details van de verdachte activiteit, richtlijnen voor het oplossen van problemen met de bedreigingen en opties voor het voortzetten van uw onderzoeken met Azure Sentinel. 
    > [!TIP]
    > Bekijk de lijst met beveiligingswaarschuwingen voor SQL-servers [op de referentiepagina voor waarschuwingen](alerts-reference.md#alerts-sql-db-and-warehouse).


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>Wat voor soort waarschuwingen biedt Azure Defender voor SQL?

In de volgende gevallen worden er met bedreigingsinformatie verrijkte beveiligingswaarschuwingen geactiveerd:

- **Potentiële SQL-injectieaanvallen** - inclusief beveiligingsproblemen die worden gedetecteerd bij het genereren van een mislukte SQL-instructie in de database
- **Afwijkende databasetoegangs- en querypatronen**, bijvoorbeeld een abnormaal groot aantal mislukte aanmeldingspogingen met andere referenties (een poging tot een brute-force aanval)
- **Verdachte databaseactiviteit**, bijvoorbeeld een rechtmatige gebruiker die een SQL-server benadert vanaf een geïnfecteerde computer die heeft gecommuniceerd met een C&C-server waarop crypto-mining plaatsvindt

Waarschuwingen bevatten details over het incident waardoor ze zijn geactiveerd evenals aanbevelingen voor het onderzoeken en oplossen van bedreigingen.



## <a name="next-steps"></a>Volgende stappen

In dit artikel bent u meer te weten gekomen over Azure Defender voor SQL. Voor het gebruik van de services die zijn beschreven:

- Azure Defender voor SQL-servers op machines gebruiken om [uw SQL-servers te scannen op beveiligings problemen](defender-for-sql-usage.md)
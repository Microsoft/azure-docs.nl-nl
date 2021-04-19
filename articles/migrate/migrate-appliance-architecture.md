---
title: Azure Migrate-apparaatarchitectuur
description: Biedt een overzicht van het Azure Migrate dat wordt gebruikt voor serverdetectie, -evaluatie en -migratie.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 4fc71f3242cc5607acebc68b62c5c0565b8f8e56
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715006"
---
# <a name="azure-migrate-appliance-architecture"></a>Azure Migrate-apparaatarchitectuur

In dit artikel worden de Azure Migrate en processen van apparaten beschreven. Het Azure Migrate is een lichtgewicht apparaat dat on-premises wordt geïmplementeerd om servers te ontdekken voor migratie naar Azure.

## <a name="deployment-scenarios"></a>Implementatiescenario's

Het Azure Migrate wordt gebruikt in de volgende scenario's.

**Scenario** | **Hulpprogramma** | **Wordt gebruikt om**
--- | --- | ---
**Detectie en evaluatie van servers die worden uitgevoerd in een VMware-omgeving** | Azure Migrate: Detectie en evaluatie | Servers ontdekken die worden uitgevoerd in uw VMware-omgeving<br/><br/> Detectie van geïnstalleerde software-inventaris, afhankelijkheidsanalyse zonder agent en SQL Server exemplaren en databases.<br/><br/> Serverconfiguratie- en prestatiemetagegevens verzamelen voor evaluaties.
**Migratie zonder agent van servers die worden uitgevoerd in een VMware-omgeving** | Azure Migrate:Servermigratie | Servers ontdekken die worden uitgevoerd in uw VMware-omgeving.<br/><br/> Repliceer servers zonder agents op deze servers te installeren.
**Detectie en evaluatie van servers die worden uitgevoerd in de Hyper-V-omgeving** | Azure Migrate: Detectie en evaluatie | Servers ontdekken die worden uitgevoerd in uw Hyper-V-omgeving.<br/><br/> Serverconfiguratie- en prestatiemetagegevens verzamelen voor evaluaties.
**Detectie en evaluatie van fysieke of gevirtualiseerde servers on-premises** |  Azure Migrate: Detectie en evaluatie |  Fysieke of gevirtualiseerde servers on-premises ontdekken.<br/><br/> Serverconfiguratie- en prestatiemetagegevens verzamelen voor evaluaties.

## <a name="deployment-methods"></a>Implementatiemethoden

Het apparaat kan op een aantal manieren worden geïmplementeerd:

- Het apparaat kan worden geïmplementeerd met behulp van een sjabloon voor servers die worden uitgevoerd in VMware- of Hyper-V-omgeving[(OVA-sjabloon voor VMware](how-to-set-up-appliance-vmware.md) of [VHD voor Hyper-V).](how-to-set-up-appliance-hyper-v.md)
- Als u geen sjabloon wilt gebruiken, kunt u het apparaat implementeren voor de VMware- of Hyper-V-omgeving met behulp van een [PowerShell-installatiescript.](deploy-appliance-script.md)
- In Azure Government moet u het apparaat implementeren met behulp van een PowerShell-installatiescript. Raadpleeg hier de [implementatiestappen.](deploy-appliance-script-government.md)
- Voor fysieke of gevirtualiseerde servers on-premises of een andere cloud implementeert u het apparaat altijd met behulp van een PowerShell-installatiescript. Raadpleeg hier de [implementatiestappen.](how-to-set-up-appliance-physical.md)
- Downloadkoppelingen zijn beschikbaar in de onderstaande tabellen.

## <a name="appliance-services"></a>Apparaatservices

Het apparaat heeft de volgende services:

- **Apparaatconfiguratiebeheer:** dit is een webtoepassing die kan worden geconfigureerd met brongegevens om de detectie en evaluatie van servers te starten. 
- **Detectieagent:** de agent verzamelt metagegevens van de serverconfiguratie die kunnen worden gebruikt om te maken als on-premises evaluaties.
- **Evaluatieagent:** de agent verzamelt metagegevens van serverprestaties die kunnen worden gebruikt om evaluaties op basis van prestaties te maken.
- **Service voor automatisch bijwerken:** de service zorgt ervoor dat alle agents op het apparaat up-to-date blijven. Deze wordt automatisch elke 24 uur uitgevoerd.
- **DRA-agent:** orchestrateert serverreplicatie en coördineert de communicatie tussen gerepliceerde servers en Azure. Wordt alleen gebruikt bij het repliceren van servers naar Azure met behulp van migratie zonder agent.
- **Gateway:** hiermee worden gerepliceerde gegevens naar Azure verzendt. Wordt alleen gebruikt bij het repliceren van servers naar Azure met behulp van migratie zonder agent.
- **SQL-agent voor detectie en evaluatie:** verzendt de configuratie- en prestatiemetagegevens van SQL Server exemplaren en databases naar Azure.

> [!Note]
> De laatste drie services zijn alleen beschikbaar in het apparaat dat wordt gebruikt voor detectie en evaluatie van servers die worden uitgevoerd in uw VMware-omgeving.<br/> Detectie en evaluatie van SQL Server en databases die worden uitgevoerd in uw VMware-omgeving is nu beschikbaar als preview-versie. Als u deze functie wilt proberen, gebruikt u [**deze koppeling**](https://aka.ms/AzureMigrate/SQL) om een project te maken in de regio **Australië - oost**. Als u al een project in Australië-oost hebt en u deze functie wilt proberen, zorgt u ervoor dat u aan deze [**vereisten**](how-to-discover-sql-existing-project.md) voldoet in de portal.

## <a name="discovery-and-collection-process"></a>Detectie- en verzamelingsproces

:::image type="content" source="./media/migrate-appliance-architecture/architecture1.png" alt-text="Architectuur van apparaat":::

Het apparaat communiceert met de detectiebronnen met behulp van het volgende proces.

**Proces** | **VMware-apparaat** | **Hyper-V-apparaat** | **Fysiek apparaat**
---|---|---|---
**Detectie starten** | Het apparaat communiceert standaard met de vCenter-server op TCP-poort 443. Als de vCenter-server op een andere poort luistert, kunt u deze configureren in configuration manager van het apparaat. | Het apparaat communiceert met de Hyper-V-hosts op WinRM-poort 5985 (HTTP). | Het apparaat communiceert met Windows-servers via WinRM-poort 5985 (HTTP) met Linux-servers via poort 22 (TCP).
**Configuratie- en prestatiemetagegevens verzamelen** | Het apparaat verzamelt de metagegevens van servers die worden uitgevoerd op vCenter Server met behulp van vSphere-API's door verbinding te maken via poort 443 (standaardpoort) of een andere poort vCenter Server luistert. | Het apparaat verzamelt de metagegevens van servers die worden uitgevoerd op Hyper-V-hosts met behulp van een Common Information Model-sessie (CIM) met hosts op poort 5985.| Het apparaat verzamelt metagegevens van Windows-servers met behulp van Common Information Model -sessie (CIM) met servers op poort 5985 en van Linux-servers die gebruikmaken van SSH-connectiviteit op poort 22.
**Detectiegegevens verzenden** | Het apparaat verzendt de verzamelde gegevens naar Azure Migrate: Detectie en evaluatie en Azure Migrate: Servermigratie via SSL-poort 443.<br/><br/>  Het apparaat kan verbinding maken met Azure via internet of via persoonlijke ExpressRoute-peering of Microsoft-peeringcircuits. | Het apparaat verzendt de verzamelde gegevens naar Azure Migrate: Detectie en evaluatie via SSL-poort 443.<br/><br/> Het apparaat kan verbinding maken met Azure via internet of via persoonlijke ExpressRoute-peering of Microsoft-peeringcircuits. | Het apparaat verzendt de verzamelde gegevens naar Azure Migrate: Detectie en evaluatie via SSL-poort 443.<br/><br/> Het apparaat kan verbinding maken met Azure via internet of via persoonlijke ExpressRoute-peering of Microsoft-peeringcircuits. 
**Frequentie van gegevensverzameling** | Configuratiemetagegevens worden elke 30 minuten verzameld en verzonden. <br/><br/> Prestatiemetagegevens worden elke 20 seconden verzameld en worden geaggregeerd om elke 10 minuten een gegevenspunt naar Azure te verzenden. <br/><br/> Software-inventarisgegevens worden eenmaal per 12 uur naar Azure verzonden. <br/><br/> Afhankelijkheidsgegevens zonder agent worden elke vijf minuten verzameld, geaggregeerd op het apparaat en elke 6 uur naar Azure verzonden. <br/><br/> De SQL Server worden elke 24 uur bijgewerkt en de prestatiegegevens worden elke 30 seconden vastgelegd.| Configuratiemetagegevens worden elke 30 minuten verzameld en verzonden. <br/><br/> Prestatiemetagegevens worden elke 30 seconden verzameld en worden geaggregeerd om elke 10 minuten een gegevenspunt naar Azure te verzenden.|  Configuratiemetagegevens worden elke 30 minuten verzameld en verzonden. <br/><br/> Prestatiemetagegevens worden elke 5 minuten verzameld en worden geaggregeerd om elke 10 minuten een gegevenspunt naar Azure te verzenden.
**Evalueren en migreren** | U kunt evaluaties maken op basis van de metagegevens die door het apparaat zijn verzameld met behulp Azure Migrate: Hulpprogramma voor detectie en evaluatie.<br/><br/>Daarnaast kunt u ook beginnen met het migreren van servers die worden uitgevoerd in uw VMware-omgeving met behulp van Azure Migrate: Hulpprogramma voor servermigratie om replicatie van servers zonder agent te beheren.| U kunt evaluaties maken op basis van de metagegevens die door het apparaat zijn verzameld met behulp Azure Migrate: Hulpprogramma voor detectie en evaluatie. | U kunt evaluaties maken op basis van de metagegevens die door het apparaat zijn verzameld met behulp Azure Migrate: Detectie- en evaluatieprogramma.

## <a name="next-steps"></a>Volgende stappen

[Controleer de](migrate-appliance.md) ondersteuningsmatrix voor het apparaat.
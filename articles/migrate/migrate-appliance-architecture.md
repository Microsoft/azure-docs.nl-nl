---
title: Azure Migrate-apparaatarchitectuur
description: Biedt een overzicht van het Azure Migrate apparaat dat in Server evaluatie en-migratie wordt gebruikt.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: d695758849fd4f7e6f595820221f6b8606fe7cf1
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096187"
---
# <a name="azure-migrate-appliance-architecture"></a>Azure Migrate-apparaatarchitectuur

In dit artikel worden de architectuur en processen van de Azure Migrate-apparaten beschreven. Het Azure Migrate apparaat is een licht gewicht apparaat dat on-premises wordt geïmplementeerd om Vm's en fysieke servers te detecteren voor migratie naar Azure.

## <a name="deployment-scenarios"></a>Implementatiescenario's

Het Azure Migrate apparaat wordt gebruikt in de volgende scenario's.

**Scenario** | **Hulpprogramma** | **Gebruikt om** 
--- | --- | ---
**Detectie en evaluatie van servers die worden uitgevoerd in de VMware-omgeving** | Azure Migrate: Server evaluatie | Servers detecteren die in uw VMware-omgeving worden uitgevoerd<br/><br/> Voer de detectie van geïnstalleerde toepassingen, afhankelijkheids analyse zonder agent en detectie van SQL Server instanties en data bases.<br/><br/> Verzamelen van meta gegevens voor de server configuratie en prestaties voor evaluaties.
**Migratie van servers die in VMware-omgeving worden uitgevoerd zonder agent** | Azure Migrate: Server migratie | Ontdek servers die worden uitgevoerd in uw VMware-omgeving.<br/><br/> Servers repliceren zonder agents te installeren.
**Detectie en evaluatie van servers die worden uitgevoerd in de Hyper-V-omgeving** | Azure Migrate: Server evaluatie | Detecteer servers die worden uitgevoerd in uw Hyper-V-omgeving.<br/><br/> Verzamelen van meta gegevens voor de server configuratie en prestaties voor evaluaties.
**Detectie en evaluatie van fysieke of gevirtualiseerde on-premises servers** |  Azure Migrate: Server evaluatie |  Fysieke of gevirtualiseerde on-premises servers detecteren.<br/><br/> Verzamelen van meta gegevens voor de server configuratie en prestaties voor evaluaties.

## <a name="deployment-methods"></a>Implementatie methoden

Het apparaat kan worden geïmplementeerd met een aantal methoden:

- Het apparaat kan worden geïmplementeerd met een sjabloon voor servers die worden uitgevoerd in de VMware-of Hyper-V-omgeving ([eicellen-sjabloon voor VMware](how-to-set-up-appliance-vmware.md) of [VHD voor hyper-v](how-to-set-up-appliance-hyper-v.md)).
- Als u geen sjabloon wilt gebruiken, kunt u het apparaat voor de VMware-of Hyper-V-omgeving implementeren met behulp van een [Power shell-installatie script](deploy-appliance-script.md).
- In Azure Government moet u het apparaat implementeren met behulp van een Power shell-installatie script. Raadpleeg [hier](deploy-appliance-script-government.md)de stappen voor de implementatie.
- Voor fysieke of gevirtualiseerde servers, on-premises of een andere Cloud, implementeert u het apparaat altijd met behulp van een Power shell-installatie script. Raadpleeg [hier](how-to-set-up-appliance-physical.md)de stappen voor de implementatie.
- Download koppelingen zijn beschikbaar in de onderstaande tabellen.

## <a name="appliance-services"></a>Toestel Services

Het apparaat heeft de volgende services:

- Apparaatconfiguratie voor **Apparaatbeheer**: dit is een webtoepassing die kan worden geconfigureerd met bron Details om de detectie en evaluatie van servers te starten. 
- **Detectie agent**: de agent verzamelt meta gegevens van de server configuratie die kunnen worden gebruikt voor het maken van on-premises evaluaties.
- **Beoordelings agent**: de agent verzamelt meta gegevens van de server prestaties die kunnen worden gebruikt voor het maken van evaluaties op basis van prestaties.
- **Service voor automatische updates**: de service houdt alle agents die op het apparaat worden uitgevoerd up-to-date. Het wordt automatisch elke 24 uur uitgevoerd.
- **DRA-agent**: organiseert Server replicatie en coördineert de communicatie tussen gerepliceerde servers en Azure. Wordt alleen gebruikt wanneer servers naar Azure worden gerepliceerd met behulp van migratie zonder agent.
- **Gateway**: verstuurt gerepliceerde gegevens naar Azure. Wordt alleen gebruikt wanneer servers naar Azure worden gerepliceerd met behulp van migratie zonder agent.
- **SQL-detectie en-evaluatie agent**: Hiermee worden de configuratie-en prestatie gegevens van SQL Server instanties en data bases naar Azure verzonden.

> [!Note]
> De laatste drie services zijn alleen beschikbaar in het apparaat dat wordt gebruikt voor detectie en evaluatie van servers die in uw VMware-omgeving worden uitgevoerd.<br/> Detectie en evaluatie van SQL Server instanties en data bases die worden uitgevoerd in uw VMware-omgeving is nu beschikbaar als preview-versie. Gebruik [**deze koppeling**](https://aka.ms/AzureMigrate/SQL) om een project te maken in **Australië-Oost** regio om deze functie uit te proberen. Als u al een project in Australië-oost hebt en u deze functie wilt uitproberen, moet u ervoor zorgen dat u deze [**vereisten**](how-to-discover-sql-existing-project.md) hebt voltooid op de portal.


## <a name="discovery-and-collection-process"></a>Detectie-en verzamelings proces

:::image type="content" source="./media/migrate-appliance-architecture/architecture1.png" alt-text="Architectuur van apparaat":::

Het apparaat communiceert met de detectie bronnen met behulp van het volgende proces.

**Proces** | **VMware-apparaat** | **Hyper-V-apparaat** | **Fysiek apparaat**
---|---|---|---
**Detectie starten** | Het apparaat communiceert standaard met de vCenter-Server op TCP-poort 443. Als de vCenter-Server op een andere poort luistert, kunt u deze configureren in het configuratie beheer van het apparaat. | Het apparaat communiceert met de Hyper-V-hosts op WinRM-poort 5985 (HTTP). | Het apparaat communiceert met Windows-servers via WinRM-poort 5985 (HTTP) met Linux-servers via poort 22 (TCP).
**Meta gegevens voor configuratie en prestaties verzamelen** | Het apparaat verzamelt de meta gegevens van servers die worden uitgevoerd op vCenter Server met behulp van vSphere-Api's door verbinding te maken met poort 443 (standaard poort) of een andere poort vCenter Server luistert aan. | Het apparaat verzamelt de meta gegevens van servers die worden uitgevoerd op Hyper-V-hosts met behulp van een Common Information Model (CIM)-sessie met hosts op poort 5985.| Het apparaat verzamelt meta gegevens van Windows-servers met behulp van Common Information Model (CIM)-sessie met servers op poort 5985 en vanaf Linux-servers met behulp van SSH-connectiviteit op poort 22.
**Detectie gegevens verzenden** | Het apparaat verzendt de verzamelde gegevens naar Azure Migrate: Server evaluatie en Azure Migrate: Server migratie via SSL-poort 443.<br/><br/> Het apparaat kan verbinding maken met Azure via internet of via ExpressRoute (micro soft-peering is vereist). | Het apparaat verzendt de verzamelde gegevens naar Azure Migrate: Server evaluatie via SSL-poort 443.<br/><br/> Het apparaat kan verbinding maken met Azure via internet of via ExpressRoute (micro soft-peering is vereist).| Het apparaat verzendt de verzamelde gegevens naar Azure Migrate: Server evaluatie via SSL-poort 443.<br/><br/> Het apparaat kan verbinding maken met Azure via internet of via ExpressRoute (micro soft-peering is vereist).
**Frequentie van gegevens verzameling** | Meta gegevens van de configuratie worden verzameld en verzonden om de 30 minuten. <br/><br/> Meta gegevens voor prestaties worden elke 20 seconden verzameld en worden geaggregeerd om elke 10 minuten een gegevens punt naar Azure te verzenden. <br/><br/> Software-inventarisatie gegevens worden één keer per 12 uur verzonden naar Azure. <br/><br/> Afhankelijkheids gegevens zonder agent worden elke vijf minuten verzameld, geaggregeerd op het apparaat en elke 6 uur naar Azure verzonden. <br/><br/> De SQL Server configuratie gegevens worden elke 24 uur bijgewerkt en de prestatie gegevens worden elke 30 seconden vastgelegd.| Meta gegevens van de configuratie worden verzameld en verzonden om de 30 minuten. <br/><br/> Meta gegevens voor prestaties worden elke 30 seconden verzameld en worden geaggregeerd om om de 10 minuten een gegevens punt naar Azure te verzenden.|  Meta gegevens van de configuratie worden verzameld en verzonden om de 30 minuten. <br/><br/> Meta gegevens voor prestaties worden elke vijf minuten verzameld en worden geaggregeerd om elke 10 minuten een gegevens punt naar Azure te verzenden.
**Evalueren en migreren** | U kunt evaluaties maken op basis van de meta gegevens die door het apparaat worden verzameld met behulp van Azure Migrate: Server assessment tool.<br/><br/>Daarnaast kunt u ook beginnen met het migreren van servers die worden uitgevoerd in uw VMware-omgeving met behulp van Azure Migrate: hulp programma voor server migratie om Server replicatie zonder agent te organiseren.| U kunt evaluaties maken op basis van de meta gegevens die door het apparaat worden verzameld met behulp van Azure Migrate: Server assessment tool. | U kunt evaluaties maken op basis van de meta gegevens die door het apparaat worden verzameld met behulp van Azure Migrate: Server assessment tool.

## <a name="next-steps"></a>Volgende stappen

[Raadpleeg](migrate-appliance.md) de ondersteunings matrix voor het onderhanden toestel.
---
title: Azure Migrate-apparaatarchitectuur
description: Biedt een overzicht van het Azure Migrate apparaat dat in Server evaluatie en-migratie wordt gebruikt.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: 42d4a722be25eec4b3e27012350346018fdba0f3
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754110"
---
# <a name="azure-migrate-appliance-architecture"></a>Azure Migrate-apparaatarchitectuur

In dit artikel worden de architectuur en processen van de Azure Migrate-apparaten beschreven. Het Azure Migrate apparaat is een licht gewicht apparaat dat on-premises wordt geïmplementeerd om Vm's en fysieke servers te detecteren voor migratie naar Azure. 

## <a name="deployment-scenarios"></a>Implementatiescenario's

Het Azure Migrate apparaat wordt gebruikt in de volgende scenario's.

**Scenario** | **Hulpprogramma** | **Gebruikt voor** 
--- | --- | ---
**VMware VM-evaluatie** | Azure Migrate: Server evaluatie | Virtuele VMware-machines detecteren.<br/><br/> Machine-apps en-afhankelijkheden detecteren.<br/><br/> Meta gegevens en prestaties van de computer verzamelen en verzenden naar Azure.
**Migratie van VMware-VM'S (zonder agent)** | Azure Migrate: Server migratie | VMware-VM's detecteren<br/><br/>  Virtuele VMware-machines repliceren met [migratie zonder agent](server-migrate-overview.md).
**Hyper-V-VM-evaluatie** | Azure Migrate: Server evaluatie | Virtuele Hyper-V-machines detecteren.<br/><br/> Meta gegevens en prestaties van de computer verzamelen en verzenden naar Azure.
**Fysieke machine** |  Azure Migrate: Server evaluatie |  Fysieke servers detecteren.<br/><br/> Meta gegevens en prestaties van de computer verzamelen en verzenden naar Azure.

## <a name="appliance-components"></a>Toestel onderdelen

Het apparaat heeft een aantal onderdelen.

- **Beheer-app**: dit is een web-app voor gebruikers invoer tijdens de implementatie van het apparaat. Wordt gebruikt bij de beoordeling van computers voor migratie naar Azure.
- **Detectie agent**: de agent verzamelt computer configuratie gegevens. Wordt gebruikt bij de beoordeling van computers voor migratie naar Azure. 
- **Collector agent**: de agent verzamelt prestatie gegevens. Wordt gebruikt bij de beoordeling van computers voor migratie naar Azure.
- **DRA-agent**: organiseert de replicatie van de virtuele machine en coördineert de communicatie tussen gerepliceerde machines en Azure. Wordt alleen gebruikt bij het repliceren van virtuele VMware-machines naar Azure met migratie zonder agent.
- **Gateway**: verstuurt gerepliceerde gegevens naar Azure. Wordt alleen gebruikt bij het repliceren van virtuele VMware-machines naar Azure met migratie zonder agent.
- **Service voor automatische updates**: updates van de onderdelen van het apparaat (elke 24 uur wordt uitgevoerd).



## <a name="appliance-deployment"></a>Implementatie van het apparaat

- Het Azure Migrate-apparaat kan worden ingesteld met behulp van een sjabloon voor [Hyper-V](how-to-set-up-appliance-hyper-v.md) of [VMware](how-to-set-up-appliance-vmware.md) of met een Power shell-script installatie programma voor [VMware/Hyper-V](deploy-appliance-script.md)en voor [fysieke servers](how-to-set-up-appliance-physical.md). 
- Ondersteunings vereisten voor apparaten en implementatie vereisten worden samenvatten in de [ondersteunings matrix](migrate-appliance.md)van het apparaat.


## <a name="appliance-registration"></a>Registratie van het apparaat

Tijdens de installatie van het apparaat registreert u het apparaat met Azure Migrate en vindt u de acties die in de tabel worden samenvatten.

**Actie** | **Details** | **Machtigingen**
--- | --- | ---
**Bron providers registreren** | Deze bronnen providers worden geregistreerd in het abonnement dat u hebt gekozen tijdens de installatie van het apparaat: micro soft. OffAzure, micro soft. migrate en micro soft. de sleutel kluis.<br/><br/> Als u een resourceprovider registreert, wordt uw abonnement zo geconfigureerd dat dit kan worden gebruikt met de resourceprovider. | Als u de resourceproviders wilt registreren, hebt u de rol Inzender of Eigenaar nodig voor het abonnement.
**Azure AD-App-communicatie maken** | Azure Migrate maakt een Azure Active Directory (Azure AD)-app voor communicatie (verificatie en autorisatie) tussen de agents die op het apparaat worden uitgevoerd, en hun respectieve services die worden uitgevoerd op Azure.<br/><br/> Deze app heeft geen rechten om Azure Resource Manager-aanroepen of Azure RBAC-toegang te maken voor een resource. | U hebt [deze machtigingen](./tutorial-discover-vmware.md#prepare-an-azure-user-account) nodig voor Azure migrate om de app te maken.
**Azure AD-apps maken-sleutel kluis** | Deze app wordt alleen voor de migratie van virtuele VMware-machines naar Azure gemaakt.<br/><br/> Het wordt uitsluitend gebruikt voor toegang tot de sleutel kluis die is gemaakt in het abonnement van de gebruiker voor migratie zonder agent.<br/><br/> Het heeft Azure RBAC-toegang op de Azure-sleutel kluis (gemaakt in de Tenant van de klant) wanneer detectie vanaf het apparaat wordt gestart. | U hebt [deze machtigingen](./tutorial-discover-vmware.md#prepare-an-azure-user-account) nodig voor Azure migrate om de app te maken.



## <a name="collected-data"></a>Verzamelde gegevens

Gegevens die door de client worden verzameld voor alle implementatie scenario's, worden in de [ondersteunings matrix](migrate-appliance.md)voor het apparaat samenvatten.

## <a name="discovery-and-collection-process"></a>Detectie-en verzamelings proces

![Architectuur](./media/migrate-appliance-architecture/architecture1.png)

Het apparaat communiceert met vCenter-servers en Hyper-V-hosts/cluster met behulp van het volgende proces.

1. **Detectie starten**:
    - Wanneer u de detectie op het Hyper-V-apparaat start, communiceert deze met de Hyper-V-hosts op WinRM-poort 5985 (HTTP).
    - Wanneer u detectie op het VMware-apparaat start, communiceert het met de vCenter-Server op TCP-poort 443 standaard. Als de vCenter-Server op een andere poort luistert, kunt u deze configureren in de Web-App van het apparaat.
2. **Meta gegevens en prestatie gegevens verzamelen**:
    - Het apparaat maakt gebruik van een Common Information Model (CIM) om Hyper-V VM-gegevens te verzamelen van de Hyper-V-host op poort 5985.
    - Het apparaat communiceert standaard met poort 443 om VMware-VM-gegevens van de vCenter Server te verzamelen.
3. **Gegevens verzenden**: het apparaat verzendt de verzamelde gegevens naar Azure migrate server-evaluatie en Azure migrate server migratie via SSL-poort 443. Het apparaat kan verbinding maken met Azure via internet of via ExpressRoute (micro soft-peering is vereist).
    - Het apparaat verzamelt gegevens over realtime gebruik voor prestatie gegevens.
        - De prestatie gegevens worden elke 20 seconden voor VMware verzameld, en elke 30 seconden voor Hyper-V, voor elke prestatie metriek.
        - De verzamelde gegevens worden samengevouwen om één gegevens punt gedurende tien minuten te maken.
        - De waarde piek gebruik wordt geselecteerd uit alle gegevens punten van 20/30 seconden en naar Azure verzonden voor de berekening van de evaluatie.
        - Op basis van de percentiel waarde die is opgegeven in de eigenschappen van de beoordeling (50e/negen/95e/99e), worden de tien-minuten punten in oplopende volg orde gesorteerd en wordt de juiste percentiel waarde gebruikt voor het berekenen van de evaluatie
    - Voor server migratie begint het apparaat met het verzamelen van VM-gegevens en repliceert het naar Azure.
4. **Beoordelen en migreren**: u kunt nu evaluaties maken op basis van de meta gegevens die worden verzameld door het apparaat met behulp van Azure migrate server-evaluatie. Daarnaast kunt u ook beginnen met het migreren van virtuele VMware-machines met Azure Migrate server migratie om replicatie zonder agents te organiseren.

## <a name="appliance-upgrades"></a>Toestel-upgrades

Het apparaat wordt bijgewerkt wanneer de Azure Migrate agents die op het apparaat worden uitgevoerd, worden bijgewerkt. Dit gebeurt automatisch omdat automatisch bijwerken standaard is ingeschakeld op het apparaat. U kunt deze standaard instelling wijzigen om de agents hand matig bij te werken.

U schakelt automatisch bijwerken uit in het REGI ster door de HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance "auto update"-sleutel in te stellen op 0 (DWORD).


## <a name="next-steps"></a>Volgende stappen

[Raadpleeg](migrate-appliance.md) de ondersteunings matrix voor het onderhanden toestel.
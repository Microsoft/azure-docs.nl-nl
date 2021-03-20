---
title: Migratie op basis van een agent in Azure Migrate server migratie
description: Biedt een overzicht van de virtuele machine migratie op basis van agents in Azure Migrate.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: c605c21307cda874f34ae5ea9f4e4959e5e6c183
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97861949"
---
# <a name="agent-based-migration-architecture"></a>Migratiearchitectuur op basis van een agent

Dit artikel bevat een overzicht van de architectuur en processen die worden gebruikt voor replicatie op basis van een agent van virtuele VMware-machines met de Azure Migrate: hulp programma voor [server migratie](migrate-services-overview.md#azure-migrate-server-assessment-tool) .

Met Azure Migrate: Server migratie kunt u virtuele VMware-machines repliceren met een aantal opties:

- Migreer Vm's met replicatie op basis van een agent, zoals beschreven in dit artikel.
- Migreer VMware-Vm's met replicatie zonder agent. Hiermee worden virtuele machines gemigreerd zonder dat er iets hoeft te worden geïnstalleerd.

Meer informatie over het [selecteren en vergelijken](server-migrate-overview.md) van migratie methoden voor VMware-vm's. 


## <a name="agent-based-migration"></a>Migratie op basis van een agent

Migratie op basis van een agent wordt gebruikt voor het migreren van on-premises VMware-Vm's en fysieke servers naar Azure. Het kan ook worden gebruikt voor het migreren van andere on-premises gevirtualiseerde servers, evenals persoonlijke en open bare Cloud-Vm's, waaronder AWS-instanties en virtuele GCP-machines. Migratie op basis van een agent in Azure Migrate maakt gebruik van een functie van de back-end van de [Azure site Recovery](../site-recovery/site-recovery-overview.md) -service.


## <a name="architectural-components"></a>Architectuuronderdelen

Het diagram illustreert de onderdelen die betrokken zijn bij migratie op basis van een agent.

![Diagram toont de onderdelen voor migratie op basis van een agent, die worden uitgelegd in een tabel.](./media/agent-based-replication-architecture/architecture.png)

De tabel bevat een overzicht van de onderdelen die worden gebruikt voor migratie op basis van een agent.

**Onderdeel** | **Details** | **Installatie**
--- | --- | ---
**Replicatie apparaat** | Het replicatie apparaat (configuratie Server/proces server) is een on-premises computer die fungeert als een brug tussen de on-premises omgeving en server migratie. Het apparaat detecteert de on-premises machine voorraad, zodat server migratie replicatie en migratie kan organiseren. Het apparaat heeft twee onderdelen:<br/><br/> **Configuratie server**: Hiermee maakt u verbinding met de server migratie en de coördinaten van de replicatie.<br/> **Proces server**: gegevens replicatie wordt verwerkt. De proces server ontvangt computer gegevens, comprimeert en versleutelt deze en verzendt deze naar Azure. In azure schrijft server migratie de gegevens naar Managed disks. | Standaard wordt de proces server samen met de configuratie server op het replicatie apparaat geïnstalleerd.
**Mobility-service** | De Mobility-service is een agent die is geïnstalleerd op elke computer die u wilt repliceren en migreren. Het verzendt replicatie gegevens van de machine naar de proces server. | Installatie bestanden voor verschillende versies van de Mobility-service bevinden zich op het replicatie-apparaat. U downloadt en installeert de agent die u nodig hebt, in overeenstemming met het besturings systeem en de versie van de computer die u wilt repliceren.

## <a name="mobility-service-installation"></a>Installatie van de Mobility-service

U kunt de Mobility-service implementeren met behulp van de volgende methoden:

- **Push-installatie**: de Mobility-service wordt geïnstalleerd door de proces server wanneer u de beveiliging voor een machine inschakelt. 
- **Hand matig installeren**: u kunt de Mobility-service hand matig op elke computer installeren via de gebruikers interface of de opdracht prompt.

De Mobility-service communiceert met het replicatie apparaat en de gerepliceerde machines. Als u antivirus software hebt uitgevoerd op het replicatie apparaat, de proces servers of de computers die worden gerepliceerd, moeten de volgende mappen worden uitgesloten van scannen:


- C:\Program Files\Microsoft Azure Recovery Services-agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86) \Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent (op Windows-computers waarop de Mobility-service is geïnstalleerd)

## <a name="replication-process"></a>Replicatieproces

1. Wanneer u replicatie voor een machine inschakelt, begint de eerste replicatie naar Azure.
2. Tijdens de eerste replicatie leest de Mobility-service gegevens van de machine schijven en verzendt deze naar de proces server.
3. Deze gegevens worden gebruikt voor het seeden van een kopie van de schijf in uw Azure-abonnement. 
4. Wanneer de initiële replicatie is voltooid, begint de replicatie van Delta wijzigingen naar Azure. Replicatie is op blok niveau en bijna doorlopend.
4. De Mobility-service onderschept schrijf bewerkingen naar schijf geheugen door te integreren met het opslag subsysteem van het besturings systeem. Deze methode vermijdt de I/O-bewerkingen van de schijf op de replicerende computer, voor incrementele replicatie. 
5. Bijgehouden wijzigingen voor een machine worden verzonden naar de proces server op binnenkomende poort-HTTPS 9443. Deze poort kan worden gewijzigd. De proces server comprimeert en versleutelt deze en verzendt deze naar Azure. 

## <a name="ports"></a>Poorten

**Apparaat** | **Verbinding**
--- | --- 
**Computers repliceren** | De Mobility-service die wordt uitgevoerd op Vm's communiceert met het on-premises replicatie apparaat op poort HTTPS 443 inkomend voor replicatie beheer.<br/><br/> Computers verzenden replicatie gegevens naar de proces server op poort HTTPS 9443 binnenkomend. Deze poort kan worden gewijzigd.
**Replicatie apparaat** | Het replicatie apparaat organiseert de replicatie met Azure via poort HTTPS 443 uitgaand.
**Proces server** | De proces server ontvangt replicatie gegevens, optimaliseert en versleutelt deze en verzendt deze naar Azure Storage via poort 443 uitgaand.


## <a name="performance-and-scaling"></a>Prestaties en schalen

Standaard implementeert u één replicatie apparaat waarop zowel de configuratie server als de proces server worden uitgevoerd. Als u slechts enkele computers repliceert, is deze implementatie voldoende. Als u echter honderden computers repliceert en migreert, kan één proces server mogelijk niet alle replicatie verkeer afhandelen. In dit geval kunt u extra, scale-out proces servers implementeren.

### <a name="plan-vmware-deployment"></a>VMware-implementatie plannen

Als u virtuele VMware-machines repliceert, kunt u de [Site Recovery Deployment planner voor VMware](../site-recovery/site-recovery-deployment-planner.md)gebruiken om te helpen bij het bepalen van de prestatie vereisten, inclusief de dagelijkse wijzigings snelheid van gegevens en de proces servers die u nodig hebt.

### <a name="replication-appliance-capacity"></a>Capaciteit van replicatie apparaat

Gebruik de waarden in deze tabel om erachter te komen of u een extra proces server nodig hebt in uw implementatie.

- Als de dagelijkse wijzigings frequentie (het verloop tempo) groter is dan 2 TB, implementeert u een extra proces server.
- Als u meer dan 200 computers repliceert, implementeert u een extra replicatie apparaat.

**CPU** | **Geheugen** | **Vrije ruimte-gegevens in cache opslaan** | **Verloop frequentie** | **Replicatie limieten**
--- | --- | --- | --- | ---
8 Vcpu's (2 sockets * 4 kernen \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB of minder | < 100 computers 
12 Vcpu's (2 sockets * 6 kernen \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB tot 1 TB | 100-150 machines.
16 Vcpu's (2 sockets * 8 kernen \@ 2,5 GHz) | 32 GB |  1 TB | 1 TB tot 2 TB | 151-200 machines.

### <a name="sizing-scale-out-process-servers"></a>Grootte van scale-out proces servers aanpassen

Als u een scale-out proces server wilt implementeren, gebruikt u deze tabel om de server grootte te bepalen.

**Proces server** | **Vrije ruimte voor het opslaan van gegevens in de cache** | **Verloop frequentie** | **Replicatie limieten**
--- | --- | --- | --- 
4 Vcpu's (2 sockets * 2 kernen \@ 2,5 GHz), 8 GB geheugen | 300 GB | 250 GB of minder | Maxi maal 85 computers 
8 Vcpu's (2 sockets * 4 kernen \@ 2,5 GHz), 12 GB geheugen | 600 GB | 251 GB tot 1 TB | 86-150 machines.
12 Vcpu's (2 sockets * 6 kernen \@ 2,5 GHz), 24 GB geheugen | 1 TB | 1-2 TB | 151-225 machines.

## <a name="throttle-upload-bandwidth"></a>Upload bandbreedte beperken.

VMware-verkeer dat wordt gerepliceerd naar Azure, loopt via een specifieke proces server. U kunt de door Voer van een upload beperken door de band breedte op de computers die worden uitgevoerd als proces servers te beperken. U kunt de band breedte beïnvloeden met behulp van de volgende register sleutel:

- Met de register waarde HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM geeft u het aantal threads op dat wordt gebruikt voor gegevens overdracht (initiële of Delta replicatie) van een schijf. Een hogere waarde verhoogt de netwerk bandbreedte die wordt gebruikt voor replicatie. De standaard waarde is vier. De maximum waarde is 32. Houd het verkeer in de gaten om de waarde te optimaliseren.
- Daarnaast kunt u de band breedte op de proces Server computer als volgt beperken:

    1. Open de Azure Backup MMC-module op de computer met de proces server. Er is een snelkoppeling op het bureau blad of in de map C:\Program Files\Microsoft Azure Recovery Services Agent\bin. 
    2. Selecteer in de module **Eigenschappen wijzigen**.
    3. Selecteer in **beperking** de optie **beperking van Internet bandbreedte gebruik inschakelen voor back-upbewerkingen**. Stel de limieten voor werk-en niet-werk uren in. Geldige bereiken zijn van 512 Kbps tot 1.023 Mbps.


## <a name="next-steps"></a>Volgende stappen

Probeer [migratie op basis](tutorial-migrate-vmware-agent.md) van een agent uit voor [VMware](tutorial-migrate-vmware-agent.md) of [fysieke servers](tutorial-migrate-physical-virtual-machines.md).

---
title: Azure Migrate-replicatieapparaat
description: Meer informatie over het Azure Migrate replicatie apparaat voor VMWare-migratie op basis van agents.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: ec277bcc3e361561f54e72c54526d65487c113b4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96754093"
---
# <a name="replication-appliance"></a>Replicatie apparaat

In dit artikel wordt het replicatie apparaat beschreven dat wordt gebruikt door Azure Migrate: hulp programma voor [server migratie](migrate-services-overview.md#azure-migrate-server-migration-tool) bij het migreren van virtuele VMware-machines, fysieke machines en persoonlijke/open bare Cloud-Vm's naar Azure, met behulp van migratie op basis van een agent. 


## <a name="overview"></a>Overzicht

Het replicatie apparaat wordt geïmplementeerd wanneer u migratie van virtuele VMware-machines of fysieke servers op basis van een agent instelt. Het wordt geïmplementeerd als één on-premises machine, hetzij als een VMware-VM of een fysieke server. Deze wordt uitgevoerd:

- **Replicatie apparaat**: het replicatie apparaat coördineert communicatie en beheert gegevens replicatie voor on-premises virtuele VMware-machines en fysieke servers die repliceren naar Azure.
- **Proces server**: de proces server, die standaard wordt geïnstalleerd op het replicatie apparaat, en doet het volgende:
    - **Replicatie gateway**: deze fungeert als replicatie gateway. Het ontvangt replicatie gegevens van machines die zijn ingeschakeld voor replicatie. Het optimaliseert replicatie gegevens met caching, compressie en versleuteling, en verzendt deze naar Azure.
    - **Agent installatie programma**: voert een push-installatie van de Mobility-service uit. Deze service moet zijn geïnstalleerd en worden uitgevoerd op elke on-premises machine die u voor migratie wilt repliceren.

## <a name="appliance-deployment"></a>Implementatie van het apparaat

**Gebruikt voor** | **Details**
--- |  ---
**Migratie op basis van VMware VM-agent** | U kunt de eicellen-sjabloon downloaden van de Azure Migrate hub en importeren in vCenter Server om de apparaat-VM te maken.
**Migratie van op de fysieke machine gebaseerde agent** | Als u geen VMware-infra structuur hebt of als u geen virtuele VMware-machine kunt maken met behulp van een eicellen-sjabloon, downloadt u een software-installatie programma van de Azure Migrate hub en voert u deze uit om de toestel machine in te stellen.

> [!NOTE]
> Als u in Azure Government implementeert, gebruikt u het installatie bestand om het replicatie apparaat te implementeren.

## <a name="appliance-requirements"></a>Vereisten voor apparaten

Wanneer u het replicatie apparaat instelt met behulp van de eicellen-sjabloon die is opgegeven in de Azure Migrate hub, voert het apparaat Windows Server 2016 uit en voldoet aan de ondersteunings vereisten. Als u het replicatie apparaat hand matig instelt op een fysieke server, moet u ervoor zorgen dat het voldoet aan de vereisten.

**Onderdeel** | **Vereiste**
--- | ---
 | **VMware-VM-apparaat**
PowerCLI | [PowerCLI-versie 6,0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) moet worden geïnstalleerd als het replicatie apparaat wordt uitgevoerd op een virtuele VMWare-machine.
Type NIC | VMXNET3 (als het apparaat een VMware-VM is)
 | **Hardware-instellingen**
CPU-kernen | 8
RAM | 16 GB
Aantal schijven | Drie: de besturingssysteem schijf, de cache schijf van de proces server en het Bewaar volume.
Vrije schijf ruimte (cache) | 600 GB
Vrije schijfruimte (bewaarschijf) | 600 GB
**Software-instellingen** |
Besturingssysteem | Windows Server 2016 of Windows Server 2012 R2
Licentie | Het apparaat wordt geleverd met een evaluatie licentie voor Windows Server 2016, die voor 180 dagen geldig is.<br/><br/> Als de evaluatie periode bijna is verlopen, raden wij aan dat u een nieuw apparaat downloadt en implementeert, of dat u de licentie voor het besturings systeem van de apparaat-VM activeert.
Landinstelling van het besturingssysteem | Engels (en-us)
TLS | TLS 1,2 moet zijn ingeschakeld.
.NET Framework | .NET Framework 4,6 of hoger moet zijn geïnstalleerd op de computer (waarbij sterke crypto grafie is ingeschakeld.
MySQL | MySQL moet op het apparaat worden geïnstalleerd.<br/> MySQL moet worden geïnstalleerd. U kunt hand matig installeren of Site Recovery kunt dit installeren tijdens de implementatie van het apparaat.
Andere apps | Voer geen andere apps uit op het replicatie apparaat.
Windows Server-functies | Deze rollen niet inschakelen: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V
Groepsbeleidsregels | Deze groepsbeleidsregels niet inschakelen: <br> - Toegang tot de opdrachtprompt voorkomen. <br> - Toegang tot registerbewerkingsprogramma's voorkomen. <br> - Op logica vertrouwen voor bestandsbijlagen. <br> - Uitvoering van script inschakelen. <br> [Meer informatie](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | - Geen vooraf bestaande standaardwebsite <br> - Geen vooraf bestaande website/toepassing die luistert op poort 443 <br>- [Anonieme verificatie](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) inschakelen <br> - Instelling [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) inschakelen
**Netwerkinstellingen** |
Type IP-adres | Statisch
Poorten | 443 (Orchestration-besturingselement)<br>9443 (Gegevenstransport)
Type NIC | VMXNET3

## <a name="mysql-installation"></a>Installatie van MySQL 

MySQL moet worden geïnstalleerd op de computer met het replicatie apparaat. Deze kan worden geïnstalleerd met een van deze methoden.

**Methode** | **Details**
--- | ---
Hand matig downloaden en installeren | Down load MySQL-toepassing & plaats deze in de map C:\Temp\ASRSetup en installeer hand matig.<br/> Bij het instellen van het apparaat MySQL wordt weer gegeven dat het al is geïnstalleerd.
Zonder online downloaden | Plaats de MySQL-installatie toepassing in de map C:\Temp\ASRSetup. Wanneer u het apparaat installeert en klikt om MySQL te downloaden en te installeren, wordt het installatie programma gebruikt dat u hebt toegevoegd.
Downloaden en installeren in Azure Migrate | Wanneer u het apparaat installeert en wordt gevraagd naar MySQL, selecteert u **downloaden en installeren**.

## <a name="url-access"></a>URL-toegang

Het replicatie apparaat moet toegang hebben tot deze Url's in de open bare Azure-Cloud.

**URL** | **Details**
--- | ---
\*.backup.windowsazure.com | Wordt gebruikt voor overdracht en coördinatie van gerepliceerde gegevens
\*.store.core.windows.net | Wordt gebruikt voor overdracht en coördinatie van gerepliceerde gegevens
\*.blob.core.windows.net | Wordt gebruikt voor toegang tot het opslag account waarin gerepliceerde gegevens worden opgeslagen
\*.hypervrecoverymanager.windowsazure.com | Wordt gebruikt voor bewerkingen en coördinatie in het kader van replicatiebeheer
https:\//management.azure.com | Wordt gebruikt voor bewerkingen en coördinatie in het kader van replicatiebeheer
*.services.visualstudio.com | Gebruikt voor telemetrische doel einden (deze is optioneel)
time.windows.com | Wordt gebruikt om de tijdsynchronisatie tussen de systeemtijd en de algemene tijd te controleren.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | Het toestel instellen heeft toegang tot deze Url's nodig. Ze worden gebruikt voor toegangs beheer en identiteits beheer door Azure Active Directory
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Voor het voltooien van het downloaden van MySQL. In enkele regio's wordt het downloadproces mogelijk omgeleid naar de CDN-URL. Zorg ervoor dat de CDN-URL ook is toegestaan als dat nodig is.


## <a name="azure-government-url-access"></a>Azure Government URL-toegang

Het replicatie apparaat moet toegang hebben tot deze Url's in Azure Government.

**URL** | **Details**
--- | ---
\*. backup.windowsazure.us | Wordt gebruikt voor overdracht en coördinatie van gerepliceerde gegevens
\*.store.core.windows.net | Wordt gebruikt voor overdracht en coördinatie van gerepliceerde gegevens
\*.blob.core.windows.net | Wordt gebruikt voor toegang tot het opslag account waarin gerepliceerde gegevens worden opgeslagen
\*. hypervrecoverymanager.windowsazure.us | Wordt gebruikt voor bewerkingen en coördinatie in het kader van replicatiebeheer
https:\//management.usgovcloudapi.net | Wordt gebruikt voor bewerkingen en coördinatie in het kader van replicatiebeheer
*.services.visualstudio.com | Gebruikt voor telemetrische doel einden (deze is optioneel)
time.nist.gov | Wordt gebruikt om de tijdsynchronisatie tussen de systeemtijd en de algemene tijd te controleren.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | Het toestel instellen met eicellen moet toegang hebben tot deze Url's. Ze worden gebruikt voor toegangs beheer en identiteits beheer door Azure Active Directory.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Voor het voltooien van het downloaden van MySQL. In enkele regio's wordt het downloadproces mogelijk omgeleid naar de CDN-URL. Zorg ervoor dat de CDN-URL ook is toegestaan als dat nodig is.

## <a name="port-access"></a>Poort toegang

**Apparaat** | **Verbinding**
--- | ---
VM's | De Mobility-service die wordt uitgevoerd op Vm's communiceert met het on-premises replicatie apparaat (configuratie server) op poort HTTPS 443 inkomend voor replicatie beheer.<br/><br/> Vm's verzenden replicatie gegevens naar de proces server (die wordt uitgevoerd op de computer van de configuratie server) op poort HTTPS 9443-binnenkomend. Deze poort kan worden gewijzigd.
Replicatie apparaat | Het replicatie apparaat organiseert de replicatie met Azure via poort HTTPS 443 uitgaand.
Proces server | De proces server ontvangt replicatie gegevens, optimaliseert en versleutelt deze en verzendt deze naar Azure Storage via poort 443 uitgaand.<br/> Standaard wordt de proces server uitgevoerd op het replicatie apparaat.


## <a name="replication-process"></a>Replicatieproces

1. Wanneer u replicatie voor een virtuele machine inschakelt, begint de initiële replicatie naar Azure Storage met behulp van het opgegeven replicatie beleid. 
2. Verkeer wordt gerepliceerd naar open bare eind punten van Azure Storage via internet. Het repliceren van verkeer via een site-naar-site virtueel particulier netwerk (VPN) van een on-premises site naar Azure wordt niet ondersteund.
3. Nadat de initiële replicatie is voltooid, begint de deltareplicatie. Bijgehouden wijzigingen voor een machine worden vastgelegd.
4. Communicatie gebeurt als volgt:
    - Vm's communiceren met het replicatie apparaat op poort HTTPS 443 inkomend voor replicatie beheer.
    - Het replicatie apparaat organiseert de replicatie met Azure via poort HTTPS 443 uitgaand.
    - Vm's verzenden replicatie gegevens naar de proces server (die wordt uitgevoerd op het replicatie apparaat) op poort HTTPS 9443-binnenkomend. Deze poort kan worden gewijzigd.
    - De proces server ontvangt replicatie gegevens, optimaliseert en versleutelt deze en verzendt deze naar Azure Storage via poort 443 uitgaand.
5. De replicatie gegevens melden het eerste land in een cache-opslag account in Azure. Deze logboeken worden verwerkt en de gegevens worden opgeslagen op een door Azure beheerde schijf.

![Diagram toont de architectuur van het replicatie proces.](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Toestel-upgrades

Het apparaat wordt hand matig geüpgraded vanuit de Azure Migrate hub. U wordt aangeraden altijd de meest recente versie uit te voeren.

1. In Azure Migrate > servers > Azure Migrate: Server evaluatie, infrastructuur servers, klik op **configuratie servers**.
2. In **configuratie servers** wordt een koppeling weer gegeven in de **Agent versie** wanneer er een nieuwe versie van het replicatie apparaat beschikbaar is. 
3. Down load het installatie programma naar de replicatie apparaat computer en installeer de upgrade. Het installatie programma detecteert de versie die momenteel wordt uitgevoerd op het apparaat.
 
## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance) het instellen van het replicatie apparaat voor de migratie van VMware VM op basis van een agent.
- [Meer informatie over](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance) het instellen van het replicatie apparaat voor fysieke servers.

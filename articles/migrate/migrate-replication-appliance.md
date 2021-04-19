---
title: Azure Migrate-replicatieapparaat
description: Meer informatie over Azure Migrate replicatieapparaat voor VMware-migratie op basis van een agent.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 5f63b033c3995932662fc9b68c1397bf57b0326e
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714970"
---
# <a name="replication-appliance"></a>Replicatieapparaat

In dit artikel wordt het replicatieapparaat beschreven dat wordt gebruikt door [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) Tool bij het migreren van VMware-VM's, fysieke machines en privé-/openbare cloud-VM's naar Azure, met behulp van migratie op basis van een agent. 


## <a name="overview"></a>Overzicht

Het replicatieapparaat wordt geïmplementeerd bij het instellen van migratie van virtuele VMware-VM's of fysieke servers op basis van een agent. Het wordt geïmplementeerd als één on-premises machine, als een VMware-VM of een fysieke server. De volgende wordt uitgevoerd:

- **Replicatieapparaat:** het replicatieapparaat coördineert de communicatie en beheert gegevensreplicatie voor on-premises VMware-VM's en fysieke servers die worden repliceren naar Azure.
- **Processerver:** de processerver, die standaard op het replicatieapparaat is geïnstalleerd, doet het volgende:
    - **Replicatiegateway:** deze fungeert als een replicatiegateway. Het ontvangt replicatiegegevens van machines die zijn ingeschakeld voor replicatie. Het optimaliseert replicatiegegevens met caching, compressie en versleuteling en verzendt deze naar Azure.
    - **Installatieprogramma voor** agent: voert een push-installatie van de Mobility-service uit. Deze service moet worden geïnstalleerd en uitgevoerd op elke on-premises computer die u wilt repliceren voor migratie.

## <a name="appliance-deployment"></a>Implementatie van het apparaat

**Gebruikt voor** | **Details**
--- |  ---
**Migratie op basis van VMware-VM-agent** | U downloadt de OVA-sjabloon van Azure Migrate hub en importeert naar vCenter Server om de apparaat-VM te maken.
**Migratie op basis van een fysieke machine op basis van een agent** | Als u geen VMware-infrastructuur hebt of als u geen VMware-VM kunt maken met behulp van een OVA-sjabloon, downloadt u een software-installatieprogramma van de Azure Migrate-hub en voer u deze uit om de apparaatmachine in te stellen.

> [!NOTE]
> Als u implementeert in Azure Government, gebruikt u het installatiebestand om het replicatieapparaat te implementeren.

## <a name="appliance-requirements"></a>Apparaatvereisten

Wanneer u het replicatieapparaat in stelt met behulp van de OVA-sjabloon die is opgegeven in de Azure Migrate-hub, wordt Windows Server 2016 op het apparaat uitgevoerd en wordt voldaan aan de ondersteuningsvereisten. Als u het replicatieapparaat handmatig op een fysieke server in stelt, moet u ervoor zorgen dat het aan de vereisten voldoet.

**Onderdeel** | **Vereiste**
--- | ---
 | **VMware-VM-apparaat**
PowerCLI | [PowerCLI-versie 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) moet worden geïnstalleerd als het replicatieapparaat wordt uitgevoerd op een VMware-VM.
Type NIC | VMXNET3 (als het apparaat een VMware-VM is)
 | **Hardware-instellingen**
CPU-kernen | 8
RAM | 16 GB
Aantal schijven | Drie: de besturingssysteemschijf, de cacheschijf van de processerver en het bewaarstation.
Vrije schijfruimte (cache) | 600 GB
Vrije schijfruimte (bewaarschijf) | 600 GB
**Software-instellingen** |
Besturingssysteem | Windows Server 2016 of Windows Server 2012 R2
Licentie | Het apparaat wordt geleverd met een Windows Server 2016-evaluatielicentie, die 180 dagen geldig is.<br/><br/> Als de evaluatieperiode bijna is verlopen, raden we u aan een nieuw apparaat te downloaden en te implementeren, of om de besturingssysteemlicentie van de apparaat-VM te activeren.
Landinstelling van het besturingssysteem | Engels (en-us)
TLS | TLS 1.2 moet zijn ingeschakeld.
.NET Framework | .NET Framework 4.6 of hoger moet worden geïnstalleerd op de computer (met sterke cryptografie ingeschakeld.
MySQL | MySQL moet op het apparaat worden geïnstalleerd.<br/> MySQL moet worden geïnstalleerd. U kunt deze handmatig installeren of Site Recovery tijdens de implementatie van het apparaat.
Andere apps | Voer geen andere apps uit op het replicatieapparaat.
Windows Server-functies | Deze rollen niet inschakelen: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V
Groepsbeleidsregels | Deze groepsbeleidsregels niet inschakelen: <br> - Toegang tot de opdrachtprompt voorkomen. <br> - Toegang tot registerbewerkingsprogramma's voorkomen. <br> - Op logica vertrouwen voor bestandsbijlagen. <br> - Uitvoering van script inschakelen. <br> [Meer informatie](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | - Geen vooraf bestaande standaardwebsite <br> - Geen vooraf bestaande website/toepassing die luistert op poort 443 <br>- [Anonieme verificatie](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) inschakelen <br> - Instelling [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) inschakelen
**Netwerkinstellingen** |
Type IP-adres | Statisch
Poorten | 443 (Orchestration-besturingselement)<br>9443 (Gegevenstransport)
Type NIC | VMXNET3

## <a name="mysql-installation"></a>MySQL-installatie 

MySQL moet zijn geïnstalleerd op de replicatieapparaatmachine. Het kan worden geïnstalleerd met een van deze methoden.

**Methode** | **Details**
--- | ---
Handmatig downloaden en installeren | Download de MySQL-& plaats deze in de map C:\Temp\ASRSetup en installeer deze handmatig.<br/> Wanneer u het apparaat in stelt, wordt MySQL als al geïnstalleerd laten zien.
Zonder online downloaden | Plaats de mySQL-installatietoepassing in de map C:\Temp\ASRSetup. Wanneer u het apparaat installeert en MySQL downloaden en installeren selecteert, wordt het installatieprogramma gebruikt dat u hebt toegevoegd.
Downloaden en installeren in Azure Migrate | Wanneer u het apparaat installeert en om MySQL wordt gevraagd, selecteert **u Downloaden en installeren.**

## <a name="url-access"></a>URL-toegang

Het replicatieapparaat moet toegang hebben tot deze URL's in de openbare Azure-cloud.

**URL** | **Details**
--- | ---
\*.backup.windowsazure.com | Wordt gebruikt voor overdracht en coördinatie van gerepliceerde gegevens
\*.store.core.windows.net | Wordt gebruikt voor overdracht en coördinatie van gerepliceerde gegevens
\*.blob.core.windows.net | Wordt gebruikt voor toegang tot het opslagaccount waarmee gerepliceerde gegevens worden opgeslagen
\*.hypervrecoverymanager.windowsazure.com | Wordt gebruikt voor bewerkingen en coördinatie in het kader van replicatiebeheer
https:\//management.azure.com | Wordt gebruikt voor bewerkingen en coördinatie in het kader van replicatiebeheer
*.services.visualstudio.com | Wordt gebruikt voor logboekregistratiedoeleinden (dit is optioneel)
time.windows.com | Wordt gebruikt om de tijdsynchronisatie tussen de systeemtijd en de algemene tijd te controleren.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | De installatie van het apparaat moet toegang hebben tot deze URL's. Ze worden gebruikt voor toegangsbeheer en identiteitsbeheer door Azure Active Directory
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Voor het voltooien van het downloaden van MySQL. In enkele regio's wordt het downloadproces mogelijk omgeleid naar de CDN-URL. Zorg ervoor dat de CDN-URL indien nodig ook is toegestaan.


## <a name="azure-government-url-access"></a>Azure Government URL-toegang

Het replicatieapparaat moet toegang hebben tot deze URL's in Azure Government.

**URL** | **Details**
--- | ---
\*.backup.windowsazure.us | Wordt gebruikt voor overdracht en coördinatie van gerepliceerde gegevens
\*.store.core.windows.net | Wordt gebruikt voor overdracht en coördinatie van gerepliceerde gegevens
\*.blob.core.windows.net | Wordt gebruikt voor toegang tot het opslagaccount waarmee gerepliceerde gegevens worden opgeslagen
\*.hypervrecoverymanager.windowsazure.us | Wordt gebruikt voor bewerkingen en coördinatie in het kader van replicatiebeheer
https:\//management.usgovcloudapi.net | Wordt gebruikt voor bewerkingen en coördinatie in het kader van replicatiebeheer
*.services.visualstudio.com | Wordt gebruikt voor logboekregistratiedoeleinden (dit is optioneel)
time.nist.gov | Wordt gebruikt om de tijdsynchronisatie tussen de systeemtijd en de algemene tijd te controleren.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | De installatie van het apparaat met OVA moet toegang hebben tot deze URL's. Ze worden gebruikt voor toegangsbeheer en identiteitsbeheer door Azure Active Directory.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Voor het voltooien van het downloaden van MySQL. In enkele regio's wordt het downloadproces mogelijk omgeleid naar de CDN-URL. Zorg ervoor dat de CDN-URL indien nodig ook is toegestaan.  

>[!Note]
>
> Als u een project migreert met een privé-eindpuntverbinding, hebt u toegang nodig tot de volgende URL's boven de toegang tot private link:   
> - *.blob.core.windows.com: voor toegang tot het opslagaccount waar gerepliceerde gegevens worden opgeslagen. Dit is optioneel en is niet vereist als aan het opslagaccount een privé-eindpunt is gekoppeld. 
> - https: \/ /management.azure.com voor replicatiebeheerbewerkingen en coördinatie. 
>- https:\//login.microsoftonline.com <br/>https:\//login.windows.net <br/> https: \/ /www.live.com _en_ <br/> https: \/ /www.microsoft.com voor toegangsbeheer en identiteitsbeheer door Azure Active Directory

## <a name="port-access"></a>Poorttoegang

**Apparaat** | **Verbinding**
--- | ---
VM's | De Mobility-service wordt uitgevoerd op VM's communiceert met het on-premises replicatieapparaat (configuratieserver) op poort HTTPS 443 inkomende poort, voor replicatiebeheer.<br/><br/> VM's verzenden replicatiegegevens naar de processerver (die wordt uitgevoerd op de configuratieservermachine) op poort HTTPS 9443 inkomende poort. Deze poort kan worden gewijzigd.
Replicatieapparaat | Het replicatieapparaat orkestreert replicatie met Azure via uitgaande HTTPS-poort 443.
Processerver | De processerver ontvangt replicatiegegevens, optimaliseert en versleutelt deze en verzendt deze naar Azure Storage via uitgaande poort 443.<br/> De processerver wordt standaard uitgevoerd op het replicatieapparaat.


## <a name="replication-process"></a>Replicatieproces

1. Wanneer u replicatie voor een VM inschakelen, begint de initiële replicatie naar Azure Storage met behulp van het opgegeven replicatiebeleid. 
2. Verkeer wordt via internet gerepliceerd naar openbare eindpunten van Azure Storage. Het repliceren van verkeer via een site-naar-site virtueel particulier netwerk (VPN) van een on-premises site naar Azure wordt niet ondersteund.
3. Nadat de initiële replicatie is voltooid, begint de deltareplicatie. Bijgehouden wijzigingen voor een computer worden geregistreerd.
4. De communicatie vindt als volgt plaats:
    - VM's communiceren met het replicatieapparaat op poort HTTPS 443 inkomende gegevens voor replicatiebeheer.
    - Het replicatieapparaat orkestreert replicatie met Azure via poort HTTPS 443 uitgaand.
    - VM's verzenden replicatiegegevens naar de processerver (die wordt uitgevoerd op het replicatieapparaat) op poort HTTPS 9443 binnenkomende poort. Deze poort kan worden gewijzigd.
    - De processerver ontvangt replicatiegegevens, optimaliseert en versleutelt deze en verzendt deze naar Azure Storage via uitgaande poort 443.
5. De logboeken met replicatiegegevens worden eerst in een cacheopslagaccount in Azure geplaatst. Deze logboeken worden verwerkt en de gegevens worden opgeslagen in een beheerde Azure-schijf.

![Diagram met de architectuur van het replicatieproces.](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Apparaatupgrades

Het apparaat wordt handmatig bijgewerkt vanuit de Azure Migrate hub. U wordt aangeraden altijd de nieuwste versie uit te voeren.

1. In Azure Migrate > Servers > Azure Migrate: Serverevaluatie, Infrastructuurservers selecteert u **Configuratieservers.**
2. In **Configuratieservers** wordt een koppeling weergegeven in **Agentversie** wanneer er een nieuwe versie van het replicatieapparaat beschikbaar is. 
3. Download het installatieprogramma naar de replicatieapparaatmachine en installeer de upgrade. Het installatieprogramma detecteert de huidige versie die wordt uitgevoerd op het apparaat.
 
## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance) het instellen van het replicatieapparaat voor migratie van VMware-VM's op basis van een agent.
- [Meer informatie over](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance) het instellen van het replicatieapparaat voor fysieke servers.

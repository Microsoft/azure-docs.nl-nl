---
title: Wat is er nieuw in Azure Site Recovery
description: Biedt een samen vatting van nieuwe functies en de meest recente updates in de Azure Site Recovery-service.
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 423fbc563878d0cc2a63a5366d2226281a204e87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102454590"
---
# <a name="whats-new-in-site-recovery"></a>Nieuw in Site Recovery

De [Azure site Recovery](site-recovery-overview.md) -service wordt voortdurend bijgewerkt en verbeterd. Om u te helpen up-to-date te blijven, geeft u in dit artikel informatie over de nieuwste releases, nieuwe functies en nieuwe inhoud. Deze pagina wordt regel matig bijgewerkt.

U kunt volgen en u abonneren op Site Recovery update meldingen in het [Azure-updates](https://azure.microsoft.com/updates/?product=site-recovery) kanaal.

## <a name="supported-updates"></a>Ondersteunde updates

Voor Site Recovery-onderdelen ondersteunen we N-4 versies, waarbij N de meest recente versie is van de release. Deze worden samengevat in de volgende tabel.

**Bijwerken** |  **Unified Setup** | **Eicellen van de configuratie server** | **Mobility Service-agent** | **Site Recovery provider** | **Recovery Services-agent**
--- | --- | --- | --- | --- | ---
[Pakket 54](https://support.microsoft.com/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)  | 9.41.5888.1 | 5.1.6620.0 | 9.41.5888.1 | 5.1.6620.0  | 2.0.9202.0
[Pakket 53](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)  | 9.40.5850.1 | 5.1.6537.0 | 9.40.5850.1 | 5.1.6537.0  | 2.0.9202.0
[Pakket 52](https://support.microsoft.com/help/4597409/)  | 9.39.5796.1 | 5.1.6458.0 | 9.39.5796.1 | 5.1.6458.0  | 2.0.9196.0
[Pakket 51](https://support.microsoft.com/help/4590304)  | 9.38.5761.1 | 5.1.6400.0 | 9.38.5761.1 | 5.1.6400.0  | 2.0.9193.0
[Pakket 50](https://support.microsoft.com/help/4582666/) | 9.37.5724.1 | 5.1.6347.0 | 9.37.5724.1 | 5.1.6347.0  | 2.0.9192.0
[Pakket 49](https://support.microsoft.com/help/4578241/) | 9.36.5696.1 | 5.1.6315.0 | 9.36.5696.1 | 5.1.6315.0 | 2.0.9188.0


Meer [informatie](service-updates-how-to.md) over installatie en ondersteuning van de update.

## <a name="updates-february-2021"></a>Updates (februari 2021)

### <a name="update-rollup-54"></a>Update pakket 54

[Update pakket 54](https://support.microsoft.com/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) bevat de volgende updates:

**Bijwerken** | **Details**
--- | ---
**Providers en agents** | Updates van Site Recovery agents en providers zoals beschreven in het pakket.
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen zoals beschreven in de samen telling. 
**Herstel na nood gevallen voor Azure VM** | Zones voor herstel na nood gevallen met behulp van Azure Site Recovery is nu beschikbaar in 4 meer regio's: Europa-noord, VS-Oost, VS-Midden en VS-West 2.<br/>
**VMware-VM/fysieke nood herstel naar Azure** | De update bevat Portal ondersteuning voor het selecteren van nabijheids locatie groepen voor VMware/fysieke machines na het inschakelen van replicatie.<br/><br/> Het beveiligen van VMware-machines met de grootte van de gegevens schijf tot 32 TB ISS wordt nu ondersteund.
**Herstel na nood geval voor Hyper-V naar Azure** | De update bevat Portal ondersteuning voor het selecteren van nabijheids locatie groepen voor Hyper-V-machines na het inschakelen van replicatie.


## <a name="updates-january-2021"></a>Updates (januari 2021)

### <a name="update-rollup-53"></a>Update pakket 53

[Update pakket 53](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) bevat de volgende updates:

**Bijwerken** | **Details**
--- | ---
**Providers en agents** | Updates van Site Recovery agents en providers zoals beschreven in het pakket.
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen zoals beschreven in de samen telling. 
**Herstel na nood gevallen voor Azure VM** | De replicatie voor labels wordt nu ondersteund. Labels die zijn toegevoegd aan Azure Vm's, schijven en Nic's in de bron regio worden gerepliceerd naar computers in de doel regio.<br/><br/> Virtuele Azure-machines waarop Debian 10 wordt uitgevoerd, worden nu ondersteund voor replicatie.
**VMware-VM/fysieke nood herstel naar Azure** | De update bevat verbeteringen in de logboeken voor de replicatie van virtuele VMware-machines naar Azure en verbeterde fout berichten.<br/><br/> VMware-Vm's en fysieke machines met Debian 10 worden nu ondersteund voor replicatie.


## <a name="updates-november-2020"></a>Updates (november 2020)

### <a name="update-rollup-52"></a>Update pakket 52

[Update pakket 52](https://support.microsoft.com/help/4597409/update-rollup-52-for-azure-site-recovery) bevat de volgende updates:

**Bijwerken** | **Details**
--- | ---
**Providers en agents** | Updates van Site Recovery agents en providers zoals beschreven in het pakket.
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen zoals beschreven in het pakket, inclusief nieuwe Linux-ondersteuning voor de Mobility-service.
**Herstel na nood gevallen voor Azure VM** | Nu ondersteund voor Vm's met RHEL 8,3 en Oracle Linux 7,9
**VMware-VM/fysieke nood herstel naar Azure** | Nu ondersteund voor Vm's met RHEL 8,3, Oracle Linux 7,9/8.3.

## <a name="updates-october-2020"></a>Updates (oktober 2020)

### <a name="update-rollup-51"></a>Update pakket 51

[Update pakket 51](https://support.microsoft.com/help/4590304/update-rollup-51-for-azure-site-recovery) bevat de volgende updates:

**Bijwerken** | **Details**
--- | ---
**Providers en agents** | Updates van Site Recovery agents en providers zoals beschreven in het pakket.
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen zoals beschreven in het pakket, inclusief nieuwe Linux-ondersteuning voor de Mobility-service.

## <a name="updates-september-2020"></a>Updates (september 2020)

### <a name="update-rollup-50"></a>Update pakket 50

[Update pakket 50](https://support.microsoft.com/help/4582666/update-rollup-50-for-azure-site-recovery) bevat de volgende updates:

**Bijwerken** | **Details**
--- | ---
**Providers en agents** | Updates van Site Recovery agents en providers zoals beschreven in het pakket.
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen zoals beschreven in de samen telling.

## <a name="updates-august-2020"></a>Updates (augustus 2020)

### <a name="update-rollup-49"></a>Update pakket 49

[Update pakket 49](https://support.microsoft.com/help/4578241/update-rollup-49-for-azure-site-recovery) bevat de volgende updates:

**Bijwerken** | **Details**
--- | ---
**Providers en agents** | Updates van Site Recovery agents en providers zoals beschreven in het pakket.
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen zoals beschreven in het pakket, inclusief nieuwe Linux-ondersteuning voor de Mobility-service.

## <a name="updates-july-2020"></a>Updates (juli 2020)

### <a name="update-rollup-48"></a>Update pakket 48

[Update pakket 48](https://support.microsoft.com/help/4573888/update-rollup-48-for-azure-site-recovery) bevat de volgende updates:

**Bijwerken** | **Details**
--- | ---
**Providers en agents** | Updates van Site Recovery agents en providers zoals beschreven in het pakket.
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen zoals beschreven in de samen telling.

> [!NOTE]
> Update pakket 48 bevat een bekend probleem met het inschakelen van replicatie voor Linux-machines die zijn versleuteld met behulp van ADE. [Meer informatie](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137).

### <a name="update-rollup-47"></a>Update pakket 47

[Update pakket 47](https://support.microsoft.com/help/4570609/update-rollup-47-for-azure-site-recovery) bevat de volgende updates:

**Bijwerken** | **Details**
--- | ---
**Providers en agents** | Updates van Site Recovery agents en providers zoals beschreven in het pakket.
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen zoals beschreven in de samen telling.

## <a name="updates-june-2020"></a>Updates (juni 2020)

### <a name="update-rollup-46"></a>Update pakket 46

[Update pakket 46](https://support.microsoft.com/help/4564347/update-rollup-46-for-azure-site-recovery) bevat de volgende updates:

**Bijwerken** | **Details**
--- | ---
**Providers en agents** | Updates van Site Recovery agents en providers zoals beschreven in het pakket.
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen zoals beschreven in de samen telling.

## <a name="updates-march-2020"></a>Updates (maart 2020)

### <a name="update-rollup-45"></a>Update pakket 45

[Update pakket 45](https://support.microsoft.com/help/4550047/update-rollup-45-for-azure-site-recovery) bevat de volgende updates:

**Bijwerken** | **Details**
--- | ---
**Providers en agents** | Updates van Site Recovery agents en providers zoals beschreven in het pakket.
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen zoals beschreven in de samen telling.

## <a name="updates-january-2020"></a>Updates (januari 2020)

### <a name="update-rollup-44"></a>Update pakket 44

[Update pakket 44](https://support.microsoft.com/help/4538187/update-rollup-44-for-azure-site-recovery) bevat de volgende updates.

**Bijwerken** | **Details**
--- | ---
**Providers en agents** | Er zijn geen updates voor de Site Recovery providers en agents.
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen zoals beschreven in de samen telling.

### <a name="azure-vmware-disaster-recovery"></a>Herstel na nood gevallen voor Azure VMware

Virtuele Azure-machines ondersteunen nu Vm's voor versleuteling-at-rest met door de klant beheerde sleutels. [Meer informatie](azure-to-azure-how-to-enable-replication-cmk-disks.md).


### <a name="update-rollup-43"></a>Update pakket 43

[Update pakket 43](https://support.microsoft.com/help/4537047/update-rollup-43-for-azure-site-recovery) bevat de volgende updates.

**Bijwerken** | **Details**
--- | ---
**Providers en agents** | Updates voor Site Recovery agents en providers (zoals beschreven in de rollup)
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in de rollup)


## <a name="updates-november-2019"></a>Updates (november 2019)

### <a name="update-rollup-42"></a>Update pakket 42

[Update pakket 42](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) bevat de volgende updates.

**Bijwerken** | **Details**
--- | ---
**Providers en agents** | Updates voor Site Recovery agents en providers (zoals beschreven in de rollup)
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in de rollup)


### <a name="azure-vm-disaster-recovery"></a>Herstel na nood gevallen voor Azure VM

Nieuwe functies voor herstel na nood gevallen voor Azure VM worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**UEFI** | Site Recovery ondersteunt nu herstel na nood gevallen voor Azure-Vm's met UEFI-gebaseerde opstart architectuur.
**Linux** | Site Recovery ondersteunt nu Azure-Vm's met Linux met Azure Disk Encryption (ADE).
**Tweede generatie** | Alle Azure-Vm's van de tweede generatie worden nu ondersteund voor herstel na nood gevallen.
**Regio's** | U kunt nu herstel na nood gevallen inschakelen voor virtuele Azure-machines in het Noor wegen geo.

### <a name="vmware-to-azure-disaster-recovery"></a>Herstel na noodgevallen van VMware naar Azure

De tabel bevat een overzicht van de nieuwe functies voor VMware naar Azure voor herstel na nood gevallen.

**Functie** | **Details**
--- | ---
**UEFI** | Site Recovery ondersteunt nu herstel na nood geval voor virtuele VMware-machines met UEFI-gebaseerde opstart architectuur.<br/><br/> Ondersteunde besturings systemen zijn onder andere Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, SLES 12 SP4, RHEL 8.

## <a name="update-to-servicing-stack-updatesha-2"></a>Update voor service stack update/SHA-2

Voor herstel na nood gevallen van virtuele Azure-machines naar een secundaire regio, of on-premises virtuele VMware-machines of fysieke servers naar Azure, moet u rekening houden met het volgende:

- Vanaf versie 9.30.5407.1 van de Mobility service-extensie (voor Azure-Vm's) en de Mobility Service-agent (voor VMware/fysieke machines) moeten op sommige besturings systemen van de machine de onderhouds stack-update en SHA-2 worden uitgevoerd. Details worden weer gegeven in de volgende tabel.
- Installeer de update en SHA-2 in overeenstemming met de gekoppelde KB. SHA-1 wordt niet ondersteund vanaf september 2019, en als SHA-2-ondertekening niet is ingeschakeld, wordt de agent extensie niet op de verwachte wijze geïnstalleerd/bijgewerkt.
- Meer informatie over [SHA-2-upgrade en-vereisten](https://aka.ms/SHA-2KB).

**Besturingssysteem** | **Azure VM** | **VMware-VM/fysieke machine**
--- | --- | ---
**Windows 2008 R2 SP1** | [Onderhouds stack-update](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Onderhouds stack-update](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 2008 SP2** | [Onderhouds stack-update](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Onderhouds stack-update](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 7 SP1** | [Onderhouds stack-update](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Onderhouds stack-update](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419).



## <a name="updates-october-2019"></a>Updates (oktober 2019)

### <a name="update-rollup-41"></a>Update pakket 41

[Update pakket 41](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) bevat de volgende updates.

**Bijwerken** | **Details**
--- | ---
**Providers en agents** | Updates voor Site Recovery agents en providers (zoals beschreven in de rollup)
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in de rollup)



### <a name="azure-vm-disaster-recovery"></a>Herstel na nood gevallen voor Azure VM

Nieuwe functies voor herstel na nood gevallen voor Azure VM worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Failover-instellingen testen** | Bij het instellen van een testfailover kunt u nu instellingen configureren voor de VM van de testfailover en het netwerk, met inbegrip van IP-adres, NSG, intern taak verdeling en het open bare IP-adres voor elke machine-NIC. Deze instellingen zijn optioneel en zijn niet van invloed op het huidige gedrag. Als u deze instellingen niet configureert, kunt u een Azure-VNet kiezen op het moment van de testfailover. [Meer informatie](https://azure.microsoft.com/blog/customize-networking-for-dr-drills-azure-site-recovery/).
**Herstel plannen** | Herstel plannen zijn nu beperkt tot 100 Vm's, om de betrouw baarheid van failover te garanderen.

### <a name="vmware-to-azure-disaster-recovery"></a>Herstel na noodgevallen van VMware naar Azure

De tabel bevat een overzicht van de nieuwe functies voor VMware naar Azure voor herstel na nood gevallen.

**Functie** | **Details**
--- | ---
**Herstel plannen** | Herstel plannen zijn nu beperkt tot 100 Vm's, om de betrouw baarheid van failover te garanderen.


## <a name="updates-september-2019"></a>Updates (september 2019)

### <a name="update-rollup-40"></a>Update pakket 40

[Update pakket 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) bevat de volgende updates.

**Bijwerken** | **Details**
--- | ---
**Providers en agents** | Updates voor Site Recovery agents en providers (zoals beschreven in de rollup)
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in de rollup)




### <a name="azure-vm-disaster-recovery"></a>Herstel na nood gevallen voor Azure VM

Nieuwe functies voor herstel na nood gevallen voor Azure VM worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Opschonen na failback** | Na een failover naar de secundaire Azure en vervolgens een failback naar de primaire regio, Site Recovery automatisch machines in de secundaire regio opschonen. Het is niet nodig om Vm's en Nic's hand matig te verwijderen.
**IP-adres behouden door testfailover** | U kunt nu het IP-adres van de bron-VM bewaren tijdens een nood herstel analyse en een statisch IP-adres kiezen voor een testfailover.

### <a name="vmwarephysical-server-disaster-recovery"></a>Herstel na nood geval voor VMware/fysieke server

De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
Nieuwe waarschuwingen voor proces server | Er zijn nieuwe waarschuwingen voor de proces server toegevoegd. [Meer informatie](vmware-physical-azure-monitor-process-server.md).

### <a name="hyper-v-disaster-recovery"></a>Herstel na nood geval voor Hyper-V

De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
Storage-account | Site Recovery ondersteunt nu het gebruik van opslag accounts waarvoor Firewall is ingeschakeld voor Hyper-V naar Azure-herstel na nood gevallen.  U kunt opslag accounts met firewall mogelijkheden selecteren als doel account of voor cache opslag. Als u gebruikmaakt van Firewall-account, moet u ervoor zorgen dat u de optie voor het toestaan van vertrouwde micro soft-Services hebt ingeschakeld.<br/><br/> Dit wordt ondersteund voor virtuele Hyper-V-machines met of zonder System Center VMM.


## <a name="updates-august-2019"></a>Updates (augustus 2019)

### <a name="update-rollup-39"></a>Update pakket 39

[Update pakket 39](https://support.microsoft.com/help/4517283/update-rollup-39-for-azure-site-recovery) bevat de volgende updates.

**Bijwerken** | **Details**
--- | ---
**Providers en agents** | Updates voor Site Recovery agents en providers (zoals beschreven in de rollup)
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in de rollup)


### <a name="azure-vm-disaster-recovery"></a>Herstel na nood gevallen voor Azure VM

Nieuwe functies voor herstel na nood gevallen voor Azure VM worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Versleuteling zonder Azure AD** | Versleuteling zonder Azure AD-app wordt nu ondersteund voor Azure VM-replicatie naar Managed disks met Windows.
**Netwerk bronnen voor failover** | Wanneer een failover wordt uitgevoerd naar een andere regio, kunt u nu netwerk bron instellingen (Nsg's, taak verdeling, openbaar IP-adres) koppelen aan een virtuele machine.

## <a name="updates-july-2019"></a>Updates (juli 2019)

### <a name="update-rollup-38"></a>Update pakket 38

[Update pakket 38](https://support.microsoft.com/help/4513507/) bevat de volgende updates.

**Bijwerken** | **Details**
--- | ---
**Providers en agents** | Updates voor Site Recovery agents en providers (zoals beschreven in de rollup)
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in de rollup)


### <a name="general"></a>Algemeen

Site Recovery ondersteunt nu het gebruik van v2-opslag accounts voor algemene doel einden voor cache opslag of doel opslag. Voorheen werd alleen v1 ondersteund.

### <a name="vmware-to-azure-disaster-recovery"></a>Herstel na noodgevallen van VMware naar Azure

U kunt schijven nu tot 8 TB repliceren, wanneer u naar een Azure-VM met beheerde schijven repliceert.


## <a name="updates-june-2019"></a>Updates (juni 2019)

### <a name="update-rollup-37"></a>Update pakket 37

[Update pakket 37](https://support.microsoft.com/help/4508614/) bevat de volgende updates.

**Bijwerken** | **Details**
--- | ---
**Providers en agents** | Updates voor Site Recovery agents en providers (zoals beschreven in de rollup)
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in de rollup)


### <a name="vmwarephysical-server-disaster-recovery"></a>Herstel na nood geval voor VMware/fysieke server

De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**GPT-partities** | Vanaf update pakket 37 en hoger (Mobility Service versie 9.25.5241.1) worden Maxi maal vijf GPT-partities ondersteund in UEFI. Vóór deze update werden vier ondersteund.



## <a name="updates-may-2019"></a>Updates (mei 2019)

### <a name="update-rollup-36"></a>Update pakket 36

[Update pakket 36](https://support.microsoft.com/help/4503156) bevat de volgende updates.

**Bijwerken** | **Details**
--- | ---
**Providers en agents** | Een update voor het Site Recovery van agents en providers (zoals beschreven in de rollup)
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in de rollup)

### <a name="azure-vm-disaster-recovery"></a>Herstel na nood gevallen voor Azure VM

De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Toegevoegde schijven repliceren** | Schakel replicatie in voor gegevens schijven die zijn toegevoegd aan een virtuele machine van Azure die al is ingeschakeld voor herstel na nood gevallen. [Meer informatie](azure-to-azure-enable-replication-added-disk.md).
**Automatische updates** | Bij het configureren van automatische updates voor de extensie van de Mobility-service die wordt uitgevoerd op Azure-Vm's die zijn ingeschakeld voor nood herstel, kunt u nu een bestaand Automation-account selecteren dat u wilt gebruiken, in plaats van het standaard account te gebruiken dat is gemaakt door Site Recovery. [Meer informatie](azure-to-azure-autoupdate.md).


### <a name="vmwarephysical-server-disaster-recovery"></a>Herstel na nood geval voor VMware/fysieke server

De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Bewaking proces server** | Voor herstel na nood gevallen van on-premises virtuele VMware-machines en fysieke servers kunt u problemen met de proces server controleren en oplossen met verbeterde rapporten en waarschuwingen voor de server status. [Meer informatie](vmware-physical-azure-monitor-process-server.md).





## <a name="updates-march-2019"></a>Updates (maart 2019)

### <a name="update-rollup-35"></a>Update pakket 35

[Update pakket 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) bevat de volgende updates.

**Bijwerken** | **Details**
--- | ---
**Providers en agents** | Een update voor het Site Recovery van agents en providers (zoals beschreven in de rollup)
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in de rollup)

### <a name="vmwarephysical-server-disaster-recovery"></a>Herstel na nood geval voor VMware/fysieke server

De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Beheerde schijven** | De replicatie van on-premises virtuele VMware-machines en fysieke servers is nu rechtstreeks voor beheerde schijven in Azure. On-premises gegevens worden verzonden naar een cache-opslag account in Azure en er worden herstel punten gemaakt op beheerde schijven op de doel locatie. Dit zorgt ervoor dat u niet meerdere doel opslag accounts hoeft te beheren.
**Configuratieserver** | Site Recovery ondersteunt nu configuratie servers met meerdere Nic's. Voeg extra adapters toe aan de configuratie Server-VM voordat u de configuratie server in de kluis registreert. Als u later toevoegt, moet u de server opnieuw registreren in de kluis.


## <a name="updates-february-2019"></a>Updates (februari 2019)

### <a name="update-rollup-34"></a>Update pakket 34

[Update pakket 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) bevat de volgende updates.

**Bijwerken** | **Details**
--- | ---
**Providers en agents** | Een update voor het Site Recovery van agents en providers (zoals beschreven in het pakket).
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in het pakket).


### <a name="update-rollup-33"></a>Update pakket 33

[Update pakket 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) bevat de volgende updates.

**Bijwerken** | **Details**
--- | ---
**Providers en agents** | Een update voor het Site Recovery van agents en providers (zoals beschreven in het pakket).
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in het pakket).


### <a name="azure-vm-disaster-recovery"></a>Herstel na nood gevallen voor Azure VM

De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Netwerktoewijzing** | Voor herstel na nood gevallen van Azure VM kunt u nu elk beschik bare doelnet werken gebruiken wanneer u replicatie inschakelt.
**Standard SSD** | U kunt nu herstel na nood gevallen instellen voor Azure-Vm's met behulp van [Standard-SSD-schijven](../virtual-machines/disks-types.md#standard-ssd).
**Opslagruimten direct** | U kunt herstel na nood gevallen instellen voor apps die worden uitgevoerd op Azure VM-apps met behulp van [opslagruimten direct](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) voor hoge Beschik baarheid.  Het gebruik van Opslagruimten Direct (S2D) in combi natie met Site Recovery biedt uitgebreide beveiliging van Azure VM-workloads. Met S2D kunt u een gast cluster hosten in Azure. Dit is vooral handig wanneer een virtuele machine als host fungeert voor een kritieke toepassing, zoals SAP ASCS Layer, SQL Server of scale-out Bestands server.


### <a name="vmwarephysical-server-disaster-recovery"></a>Herstel na nood geval voor VMware/fysieke server

De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Linux BRTFS-bestands systeem** | Site Recovery ondersteunt nu replicatie van virtuele VMware-machines met het BRTFS-bestands systeem. Replicatie wordt niet ondersteund als:<br/><br/>-Het subvolume van het BTRFS-bestands systeem wordt gewijzigd nadat de replicatie is ingeschakeld.<br/><br/>-Het bestands systeem is verdeeld over meerdere schijven.<br/><br/>-Het BTRFS-bestands systeem ondersteunt RAID.
**Windows Server 2019** | Ondersteuning toegevoegd voor computers met Windows Server 2019.


## <a name="updates-january-2019"></a>Updates (januari 2019)


### <a name="accelerated-networking-azure-vms"></a>Versneld netwerken (Azure Vm's)

Versneld netwerken maken gebruik van I/O-virtualisatie met één hoofdmap (SR-IOV) naar een VM, waardoor de netwerk prestaties worden verbeterd. Wanneer u replicatie inschakelt voor een virtuele machine van Azure, Site Recovery detecteert of versneld netwerken zijn ingeschakeld. Als dit het geval is, wordt na een failover-Site Recovery automatisch een versneld netwerk geconfigureerd op de doel replica van Azure VM, voor zowel [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) als [Linux](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

[Meer informatie](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="update-rollup-32"></a>Update pakket 32

[Update pakket 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) bevat de volgende updates.

**Bijwerken** | **Details**
--- | ---
**Providers en agents** | Een update voor het Site Recovery van agents en providers (zoals beschreven in het pakket).
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in het pakket).

### <a name="azure-vm-disaster-recovery"></a>Herstel na nood gevallen voor Azure VM

De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Linux Support** | Er is ondersteuning toegevoegd voor RedHat-werk Station 6/7, en voor nieuwe kernel-versies voor Ubuntu, Debian en SUSE.
**Opslagruimten direct** | Site Recovery ondersteunt Azure Vm's met behulp van Opslagruimten Direct (S2D).

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>Herstel na nood geval voor virtuele VMware-machines/fysieke servers

De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Linux Support** | Er is ondersteuning toegevoegd voor redhat Enter prise Linux 7,6, RedHat Workstation 6/7, Oracle Linux 6,10 en Oracle Linux 7,6 en nieuwe kernel-versies voor Ubuntu, Debian en SUSE.


### <a name="update-rollup-31"></a>Update pakket 31

[Update pakket 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) bevat de volgende updates.

**Bijwerken** | **Details**
--- | ---
**Providers en agents** | Een update voor het Site Recovery van agents en providers (zoals beschreven in het pakket).
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in het pakket).

### <a name="vmware-vmsphysical-servers-replication"></a>Replicatie van virtuele VMware-machines/fysieke servers

De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Linux Support** |  Er is ondersteuning toegevoegd voor Oracle Linux 6,8, Oracle Linux 6,9 en Oracle Linux 7,0 met de Red Hat compatible kernel en voor de onbreekbare UEK-versie (Enter prise kernel) 5.
**LVM** | Ondersteuning toegevoegd voor LVM-en LVM2-volumes.<br/><br/> De/boot-map op een schijf partitie en op LVM-volumes wordt nu ondersteund.
**Mappen** | Er is ondersteuning toegevoegd voor deze mappen die zijn ingesteld als afzonderlijke partities of bestands systemen die zich niet op dezelfde systeem schijf bevinden:<br/><br/> /(root),/boot,/usr,/usr/local,/var,/etc.
**Windows Server 2008** | Ondersteuning voor dynamische schijven is toegevoegd.
**Failover** | Verbeterde failover-tijd voor VMware-Vm's waarbij storvsc en vsbus geen stuur Programma's worden opgestart.
**UEFI-ondersteuning** | Azure-Vm's ondersteunen boot type UEFI niet. U kunt nu on-premises fysieke servers met UEFI migreren naar Azure met Site Recovery. Site Recovery migreert de server door het opstart type te converteren naar BIOS vóór de migratie. Site Recovery eerder deze conversie alleen voor Vm's ondersteund. Ondersteuning is beschikbaar voor fysieke servers met Windows Server 2012 of hoger.

### <a name="azure-vm-disaster-recovery"></a>Herstel na nood gevallen voor Azure VM

De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Linux Support** | Er is ondersteuning toegevoegd voor Oracle Linux 6,8, Oracle Linux 6,9 en Oracle Linux 7,0 met de Red Hat compatible kernel en voor de onbreekbare UEK-versie (Enter prise kernel) 5.
**Linux BRTFS-bestands systeem** | Ondersteund voor virtuele Azure-machines.
**Azure-Vm's in beschikbaarheids zones** | U kunt replicatie naar een andere regio inschakelen voor virtuele Azure-machines die zijn geïmplementeerd in beschikbaarheids zones. U kunt nu replicatie inschakelen voor een virtuele Azure-machine en het doel voor failover instellen op een enkele VM-instantie, een virtuele machine in een beschikbaarheidsset of een virtuele machine in een beschikbaarheids zone. De instelling heeft geen invloed op de replicatie. [Lees](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) de aankondiging.
**Firewall-ingeschakelde opslag (Portal/Power shell)** | Ondersteuning voor [opslag accounts met ingeschakelde firewall](../storage/common/storage-network-security.md).<br/><br/> U kunt Azure-Vm's met niet-beheerde schijven op opslag accounts met ingeschakelde firewalls repliceren naar een andere Azure-regio voor herstel na nood gevallen.<br/><br/> U kunt opslag accounts met ingeschakelde firewall gebruiken als doel opslag accounts voor niet-beheerde schijven.<br/><br/> Ondersteund in de portal en met behulp van Power shell.

## <a name="updates-december-2018"></a>Updates (december 2018)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>Automatische updates voor de Mobility-service (Azure Vm's)

Site Recovery heeft een optie voor automatische updates toegevoegd aan de Mobility service-extensie. De Mobility service-extensie wordt geïnstalleerd op elke virtuele machine van Azure die wordt gerepliceerd door Site Recovery. Wanneer u replicatie inschakelt, selecteert u of Site Recovery updates voor de uitbrei ding wilt laten beheren.

Voor updates is het opnieuw opstarten van de VM niet vereist en de replicatie wordt niet beïnvloed. [Meer informatie](azure-to-azure-autoupdate.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Prijs calculator voor herstel na nood gevallen voor Azure VM

Herstel na nood gevallen van virtuele Azure-machines, en netwerk-en opslag kosten. Azure biedt een [prijs calculator](https://aka.ms/a2a-cost-estimator) om u te helpen deze kosten te berekenen. Site Recovery biedt nu een [voor beeld van een prijs schatting](https://aka.ms/a2a-cost-estimator) waarmee een voorbeeld implementatie wordt geprijsd op basis van een app met drie lagen, met zes virtuele machines met 12 Standard-HDD schijven en 6 Premium-SSD schijven.

- In het voor beeld wordt ervan uitgegaan dat de gegevens worden gewijzigd van 10 GB per dag voor Standard en 20 GB voor Premium.
- Voor uw specifieke implementatie kunt u de variabelen wijzigen om de kosten te schatten.
- U kunt het aantal Vm's, het aantal en het type beheerde schijven, en de verwachte totale wijzigings frequentie voor gegevens op de virtuele machines opgeven.
- Daarnaast kunt u een compressie factor Toep assen om de bandbreedte kosten te schatten.

[Lees](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) de aankondiging.


## <a name="updates-october-2018"></a>Updates (oktober 2018)

### <a name="update-rollup-30"></a>Update pakket 30

[Update pakket 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) biedt de volgende updates.

**Bijwerken** | **Details**
--- | ---
**Providers en agents** | Een update voor het Site Recovery van agents en providers (zoals beschreven in het pakket).
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in het pakket).

### <a name="azure-vm-disaster-recovery"></a>Herstel na nood gevallen voor Azure VM
De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Ondersteuning voor regio** | Site Recovery ondersteuning toegevoegd voor Australië-centraal 1 en Australië-centraal 2.
**Ondersteuning voor schijf versleuteling** | Ondersteuning toegevoegd voor herstel na nood gevallen van Azure-Vm's die zijn versleuteld met Azure Disk Encryption (ADE) met de Azure AD-app. [Meer informatie](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Schijf uitsluiting** | Niet-geïnitialiseerde schijven worden nu automatisch uitgesloten tijdens de replicatie van virtuele Azure-machines.
**Firewall-ingeschakelde opslag (Power shell)** | Ondersteuning voor [opslag accounts met ingeschakelde firewall](../storage/common/storage-network-security.md).<br/><br/> U kunt Azure-Vm's met niet-beheerde schijven op opslag accounts met ingeschakelde firewalls repliceren naar een andere Azure-regio voor herstel na nood gevallen.<br/><br/> U kunt opslag accounts met ingeschakelde firewall gebruiken als doel opslag accounts voor niet-beheerde schijven.<br/><br/> Alleen ondersteund met Power shell.


### <a name="update-rollup-29"></a>Update pakket 29

[Update pakket 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) biedt de volgende updates.

**Bijwerken** | **Details**
--- | ---
**Providers en agents** | Een update voor het Site Recovery van agents en providers (zoals beschreven in het pakket).
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in het pakket).


## <a name="updates-august-2018"></a>Updates (augustus 2018)

### <a name="update-rollup-28"></a>Update pakket 28

[Update pakket 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) bevat de volgende updates.

**Bijwerken** | **Details**
--- | ---
**Providers en agents** | Een update voor het Site Recovery van agents en providers (zoals beschreven in het pakket).
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in het pakket).

### <a name="azure-vm-disaster-recovery"></a>Herstel na nood gevallen voor Azure VM
De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Linux Support** | Toegevoegd ondersteund voor RedHat Enter prise Linux 6,10; CentOS 6,10.<br/><br/>
**Cloud ondersteuning** | Ondersteunde nood herstel voor virtuele Azure-machines in de Duitse Cloud.
**Herstel na nood geval voor meerdere abonnementen** | Ondersteuning voor het repliceren van virtuele Azure-machines in één regio naar een andere regio in een ander abonnement, binnen dezelfde Azure Active Directory Tenant. [Meer informatie](https://aka.ms/cross-sub-blog).

### <a name="vmware-vmphysical-server-disaster-recovery"></a>Herstel na nood geval voor VMware VM/fysieke server
De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Linux Support** | Ondersteuning toegevoegd voor RedHat Enter prise Linux 6,10, CentOS 6,10.<br/><br/> Op Linux gebaseerde virtuele machines die gebruikmaken van de partitie stijl GPT (GUID-partitie tabel) in verouderde BIOS-compatibiliteits modus, worden nu ondersteund. Raadpleeg de [Veelgestelde vragen over Azure VM](../virtual-machines/faq-for-disks.md) voor meer informatie.
**Herstel na nood geval voor Vm's na migratie** | Ondersteuning voor het inschakelen van herstel na nood gevallen naar een secundaire regio voor een on-premises virtuele VMware-machine die is gemigreerd naar Azure, zonder dat u de Mobility-service op de virtuele machine hoeft te verwijderen voordat u replicatie inschakelt.
**Windows Server 2008** | Ondersteuning voor de migratie van computers met Windows Server 2008 R2/2008 64-bits en 32-bits.<br/><br/> Alleen migratie (replicatie en failover). Failback wordt niet ondersteund.

## <a name="updates-july-2018"></a>Updates (juli 2018)

### <a name="update-rollup-27-july-2018"></a>Update pakket 27 (juli 2018)

[Update pakket 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) bevat de volgende updates.

**Bijwerken** | **Details**
--- | ---
**Providers en agents** | Een update voor het Site Recovery van agents en providers (zoals beschreven in het pakket).
**Problemen/verbeteringen van het probleem** | Een aantal oplossingen en verbeteringen (zoals beschreven in het pakket).

### <a name="azure-vm-disaster-recovery"></a>Herstel na nood gevallen voor Azure VM

De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Linux Support** | Ondersteuning toegevoegd voor Red Hat Enterprise Linux 7,5.

### <a name="vmware-vmphysical-server-disaster-recovery"></a>Herstel na nood geval voor VMware VM/fysieke server

De functies die deze maand zijn toegevoegd, worden in de tabel samenvatten.

**Functie** | **Details**
--- | ---
**Linux Support** | Ondersteuning toegevoegd voor Red Hat Enterprise Linux 7,5, SUSE Linux Enterprise Server 12.



## <a name="next-steps"></a>Volgende stappen

Blijf op de hoogte van de updates op de pagina [Azure updates](https://azure.microsoft.com/updates/?product=site-recovery) .
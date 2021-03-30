---
title: Capaciteit plannen voor VMware-nood herstel met Azure Site Recovery
description: In dit artikel vindt u meer informatie over het plannen van capaciteit en schaal baarheid bij het instellen van herstel na nood gevallen van virtuele VMware-machines in azure met behulp van Azure Site Recovery.
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 4/9/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 4b86d0c189bcf0687a703f2338188df2090feaf0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92368023"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Capaciteit en schaling plannen voor nood herstel van VMware naar Azure

Gebruik dit artikel om capaciteit en schaal baarheid te plannen wanneer u on-premises virtuele VMware-machines en fysieke servers naar Azure repliceert met behulp van [Azure site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Hoe kan ik u de capaciteits planning te plannen?

Als u meer wilt weten over Azure Site Recovery infrastructuur vereisten, verzamelt u informatie over uw replicatie omgeving door [Azure site Recovery Deployment planner](./site-recovery-deployment-planner.md) voor VMware-replicatie uit te voeren. Zie voor meer informatie [over Site Recovery Deployment planner voor VMware naar Azure](site-recovery-deployment-planner.md). 

Site Recovery Deployment Planner biedt een rapport met volledige informatie over compatibele en niet-compatibele Vm's, schijven per VM en gegevens verloop per schijf. Het hulp programma geeft ook een overzicht van de vereisten voor de netwerk bandbreedte om te voldoen aan de doel-RPO en de Azure-infra structuur die is vereist voor een succes volle replicatie en testfailover.

## <a name="capacity-considerations"></a>Overwegingen bij capaciteitsbepaling

Onderdeel | Details
--- | ---
**Replicatie** | **Maximum dagelijkse wijzigings frequentie**: een beveiligde machine kan slechts één proces server gebruiken. Eén processerver kan een hoeveelheid dagelijkse wijzigingen afhandelen tot maximaal 2 TB. 2 TB is dus het maximale aantal dagelijkse gegevens wijzigingen dat wordt ondersteund voor een beveiligde machine.<br /><br /> **Maximale door Voer**: een gerepliceerde machine kan deel uitmaken van één opslag account in Azure. Een standaard Azure Storage account kan Maxi maal 20.000 aanvragen per seconde verwerken. We raden u aan om het aantal invoer/uitvoer-bewerkingen per seconde (IOPS) op een bron machine te beperken tot 20.000. Als u bijvoorbeeld een bron machine hebt die vijf schijven heeft en elke schijf 120 IOPS (8 KB groot) op de bron machine genereert, bevindt de bron machine zich in de limiet voor IOPS per schijf van 500. (Het vereiste aantal opslag accounts is gelijk aan de totale IOPS van de bron machine gedeeld door 20.000.)
**Configuratieserver** | De configuratie server moet kunnen omgaan met de dagelijkse wijzigings frequentie voor alle workloads die worden uitgevoerd op beveiligde computers. De configuratie computer moet voldoende band breedte hebben om voortdurend gegevens te repliceren naar Azure Storage.<br /><br /> Een best practice is om de configuratie server op hetzelfde netwerk en LAN-segment te plaatsen als de computers die u wilt beveiligen. U kunt de configuratie server op een ander netwerk plaatsen, maar de computers die u wilt beveiligen moeten de laag 3-netwerk zichtbaarheid hebben.<br /><br /> De aanbevolen grootte voor de configuratie server wordt in de volgende sectie in de tabel samenvatten.
**Proces server** | De eerste proces server wordt standaard geïnstalleerd op de configuratie server. U kunt extra proces servers implementeren om uw omgeving te schalen. <br /><br /> De proces server ontvangt replicatie gegevens van beveiligde computers. De proces server optimaliseert de gegevens met behulp van caching, compressie en versleuteling. Vervolgens verzendt de proces server de gegevens naar Azure. De proces Server computer moet voldoende resources hebben om deze taken uit te voeren.<br /><br /> De proces server maakt gebruik van een schijf cache. Gebruik een afzonderlijke cache schijf van 600 GB of meer voor het afhandelen van gegevens wijzigingen die worden opgeslagen als er een netwerk knelpunt of-storing optreedt.

## <a name="size-recommendations-for-the-configuration-server-and-inbuilt-process-server"></a>Aanbevelingen voor de grootte van de configuratie server en de ingebouwde proces server

Een configuratie server die gebruikmaakt van een inbouw proces server om de werk belasting te beveiligen, kan tot 200 virtuele machines afhandelen op basis van de volgende configuraties:

CPU | Geheugen | Schijf grootte van cache | Frequentie van gegevenswijzigingen | Beveiligde machines
--- | --- | --- | --- | ---
8 Vcpu's (2 sockets * 4 kernen \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB of minder | Gebruik om minder dan 100 machines te repliceren.
12 Vcpu's (2 sockets * 6 kernen \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB tot 1 TB | Gebruik om 100 te repliceren naar 150 machines.
16 Vcpu's (2 sockets * 8 kernen \@ 2,5 GHz) | 32 GB | 1 TB | >1 TB tot 2 TB | Gebruik om 151 te repliceren naar 200 machines.
Implementeer een andere configuratie server met behulp van een [OVF-sjabloon](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template). | | | | Implementeer een nieuwe configuratie server als u meer dan 200 computers repliceert.
Implementeer een andere [proces server](vmware-azure-set-up-process-server-scale.md#download-installation-file). | | | >2 TB| Implementeer een nieuwe scale-out proces server als de totale dagelijkse wijzigings snelheid van gegevens groter is dan 2 TB.

In deze configuraties:

* Elke bron machine heeft elk drie schijven van 100 GB.
* We hebben de benchmarking-opslag gebruikt voor acht Shared Access Signature-stations van 10 K RPM met RAID 10 voor cache-schijf metingen.

## <a name="size-recommendations-for-the-process-server"></a>Aanbevolen grootte voor de proces server

De proces server is het onderdeel dat gegevens replicatie in Azure Site Recovery verwerkt. Als de dagelijkse wijzigings frequentie groter is dan 2 TB, moet u scale-out proces servers toevoegen om de replicatie belasting te verwerken. Als u wilt uitschalen, kunt u het volgende doen:

* Verhoog het aantal configuratie servers door te implementeren met behulp van een [OVF-sjabloon](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template). U kunt bijvoorbeeld Maxi maal 400 machines beveiligen met behulp van twee configuratie servers.
* [Scale-out proces servers](vmware-azure-set-up-process-server-scale.md#download-installation-file)toevoegen. Gebruik de scale-out proces servers voor het verwerken van replicatie verkeer in plaats van (of naast) de configuratie server.

In de volgende tabel wordt dit scenario beschreven:

* U hebt een scale-out proces server ingesteld.
* U hebt beveiligde virtuele machines geconfigureerd voor het gebruik van de scale-out proces server.
* Elke beveiligde bron machine heeft elk drie schijven van 100 GB.

Aanvullende proces server | Schijf grootte van cache | Frequentie van gegevenswijzigingen | Beveiligde machines
--- | --- | --- | ---
4 Vcpu's (2 sockets * 2 kernen \@ 2,5 GHz), 8 GB geheugen | 300 GB | 250 GB of minder | Gebruik om 85 of minder computers te repliceren.
8 Vcpu's (2 sockets * 4 kernen \@ 2,5 GHz), 12 GB geheugen | 600 GB | 251 GB tot 1 TB | Gebruik om 86 te repliceren naar 150 machines.
12 Vcpu's (2 sockets * 6 kernen \@ 2,5 GHz) 24 GB aan geheugen | 1 TB | >1 TB tot 2 TB | Gebruik om 151 te repliceren naar 225 machines.

Hoe u uw servers schaalt, is afhankelijk van uw voor keur voor een scale-up-of scale-out model. Als u omhoog wilt schalen, implementeert u een aantal geavanceerde configuratie servers en proces servers. Als u wilt uitschalen, implementeert u meer servers met minder resources. Als u bijvoorbeeld 200 machines wilt beveiligen met een totaal dagelijks wijzigings snelheid van 1,5 TB, kunt u een van de volgende acties uitvoeren:

* Stel één proces server in (16 vCPU, 24 GB RAM-geheugen).
* Twee proces servers instellen (2 x 8 vCPU, 2 * 12 GB RAM-geheugen).

## <a name="control-network-bandwidth"></a>Netwerk bandbreedte beheren

Nadat u [Site Recovery Deployment planner](site-recovery-deployment-planner.md) hebt gebruikt om de band breedte te berekenen die u nodig hebt voor replicatie (initiële replicatie en vervolgens de Delta), hebt u een aantal opties voor het beheren van de hoeveelheid band breedte die wordt gebruikt voor replicatie:

* **Bandbreedte beperking**: VMware-verkeer dat wordt gerepliceerd naar Azure gaat via een specifieke proces server. U kunt de band breedte beperken op de computers die als proces servers worden uitgevoerd.
* **Invloed op band breedte**: u kunt invloed hebben op de band breedte die voor replicatie wordt gebruikt met behulp van een aantal register sleutels:
  * Met de register waarde **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** geeft u het aantal threads op dat wordt gebruikt voor gegevens overdracht (initiële of Delta replicatie) van een schijf. Een hogere waarde verhoogt de netwerk bandbreedte die wordt gebruikt voor replicatie.
  * Met de register waarde **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** geeft u het aantal threads op dat wordt gebruikt voor gegevens overdracht tijdens het failback.

### <a name="throttle-bandwidth"></a>Bandbreedte beperken

1. Open de Azure Backup MMC-module op de computer die u als de proces server gebruikt. Een snelkoppeling voor back-up is standaard beschikbaar op het bureau blad of in de volgende map: C:\Program Files\Microsoft Azure Recovery Services Agent\bin.
2. Selecteer in de module **Eigenschappen wijzigen**.

    ![Scherm afbeelding van de MMC-module Azure Backup voor het wijzigen van eigenschappen](./media/site-recovery-vmware-to-azure/throttle1.png)
3. Op het tabblad **beperking** selecteert u **beperking van Internet bandbreedte gebruik inschakelen voor back-upbewerkingen**. Stel de limieten voor werk-en niet-werk uren in. Geldige bereiken zijn van 512 Kbps tot 1.023 Mbps.

    ![Scherm afbeelding van het dialoog venster Eigenschappen van Azure Backup](./media/site-recovery-vmware-to-azure/throttle2.png)

U kunt ook de cmdlet [Set-OBMachineSetting](/previous-versions/windows/powershell-scripting/hh770409(v=wps.640)) gebruiken om een beperking in te stellen. Hier volgt een voorbeeld:

```azurepowershell-interactive
$mon = [System.DayOfWeek]::Monday
$tue = [System.DayOfWeek]::Tuesday
Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)
```

**Set-OBMachineSetting -NoThrottle** geeft aan dat er geen beperking is vereist.

### <a name="alter-the-network-bandwidth-for-a-vm"></a>De netwerk bandbreedte voor een virtuele machine wijzigen

1. Ga in het REGI ster van de virtuele machine naar **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Als u het bandbreedte verkeer op een replicerende schijf wilt wijzigen, wijzigt u de waarde van **UploadThreadsPerVM**. Maak de sleutel als deze niet bestaat.
   * Als u de band breedte voor failback-verkeer vanuit Azure wilt wijzigen, wijzigt u de waarde van **DownloadThreadsPerVM**.
2. De standaard waarde voor elke sleutel is **4**. In netwerken met overprovisioning moeten deze registersleutels zodanig worden gewijzigd dat niet de standaardwaarden worden gebruikt. De maximum waarde die u kunt gebruiken, is **32**. Houd het verkeer in de gaten om de waarde te optimaliseren.

## <a name="set-up-the-site-recovery-infrastructure-to-protect-more-than-500-vms"></a>De Site Recovery-infra structuur instellen om meer dan 500 Vm's te beveiligen

Voordat u de Site Recovery-infra structuur instelt, hebt u toegang tot de omgeving om de volgende factoren te meten: compatibele virtuele machines, het dagelijkse gegevens wijzigings tempo, de vereiste netwerk bandbreedte voor de RPO die u wilt verkrijgen, het aantal Site Recovery onderdelen dat vereist is en de tijd die nodig is om de initiële replicatie te volt ooien. Voer de volgende stappen uit om de vereiste gegevens te verzamelen:

1. U kunt deze para meters meten door Site Recovery Deployment Planner uit te voeren in uw omgeving. Zie [over Site Recovery Deployment planner voor VMware naar Azure](site-recovery-deployment-planner.md)voor nuttige richt lijnen.
2. Implementeer een configuratie server die voldoet aan de [aanbevelingen voor de grootte van de configuratie server](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server). Als uw productie werk belasting groter is dan 650 virtuele machines, implementeert u een andere configuratie server.
3. Implementeer [scale-out proces servers](vmware-azure-set-up-process-server-scale.md#download-installation-file) op basis van de gemeten dagelijkse gegevens wijzigings frequentie met behulp van de [richt lijnen](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server)voor de grootte.
4. Als u verwacht dat de gegevens wijzigings frequentie voor een virtuele schijf machine groter is dan 2 MBps, moet u ervoor zorgen dat u Premium Managed disks gebruikt. Site Recovery Deployment Planner wordt uitgevoerd voor een bepaalde periode. Pieken in de gegevens wijzigings snelheid op andere tijden worden mogelijk niet vastgelegd in het rapport.
5. [Stel de netwerk bandbreedte](site-recovery-plan-capacity-vmware.md#control-network-bandwidth) in op basis van de RPO die u wilt uitvoeren.
6. Wanneer de infra structuur is ingesteld, schakelt u herstel na nood gevallen in voor uw werk belasting. Zie [de bron omgeving voor VMware naar Azure-replicatie instellen](vmware-azure-set-up-source.md)voor meer informatie.

## <a name="deploy-additional-process-servers"></a>Aanvullende proces servers implementeren

Als u uw implementatie met meer dan 200 bron machines uitschaalt of als u een totaal dagelijks verloop van meer dan 2 TB hebt, moet u proces servers toevoegen om het verkeers volume te verwerken. We hebben het product in 9,24-versie uitgebreid om [waarschuwingen over de proces server](vmware-physical-azure-monitor-process-server.md#process-server-alerts) te bieden bij het instellen van een scale-out proces server. [Stel de proces server](vmware-azure-set-up-process-server-scale.md) in om nieuwe bron machines te beveiligen of [de belasting te verdelen](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load).

### <a name="migrate-machines-to-use-the-new-process-server"></a>Machines migreren voor het gebruik van de nieuwe proces server

1. Selecteer **instellingen**  >  **site Recovery servers**. Selecteer de configuratie server en vouw vervolgens **proces servers** uit.

    ![Scherm afbeelding van het dialoog venster proces server](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Klik met de rechter muisknop op de proces server die momenteel in gebruik is en selecteer **Switch**.

    ![Scherm afbeelding van het dialoog venster Configuratie server](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. Selecteer in **doel proces server selecteren** de nieuwe proces server die u wilt gebruiken. Selecteer vervolgens de virtuele machines die door de server worden verwerkt. Als u informatie over de server wilt ophalen, selecteert u het informatie pictogram. Om u te helpen bij het nemen van load beslissingen, wordt de gemiddelde ruimte die nodig is voor het repliceren van elke geselecteerde virtuele machine naar de nieuwe proces server weer gegeven. Schakel het selectie vakje in om te beginnen met repliceren naar de nieuwe proces server.

## <a name="deploy-additional-master-target-servers"></a>Aanvullende Master doel servers implementeren

In de volgende scenario's is meer dan een hoofddoel server vereist:

*   U wilt een virtuele machine op basis van Linux beveiligen.
*   De hoofddoel server die beschikbaar is op de configuratie server heeft geen toegang tot de gegevens opslag van de virtuele machine.
*   Het totale aantal schijven op de hoofddoel server (het aantal lokale schijven op de server plus het aantal schijven dat moet worden beveiligd) is groter dan 60 schijven.

Zie [een Linux-hoofddoel server voor failback installeren](vmware-azure-install-linux-master-target.md)voor meer informatie over het toevoegen van een hoofddoel server voor een virtuele machine op basis van Linux.

Een hoofddoel server voor een op Windows gebaseerde virtuele machine toevoegen:

1. Ga naar **Recovery Services kluis**  >  **site Recovery infrastructuur**  >  **configuratie servers**.
2. Selecteer de vereiste configuratie server en selecteer vervolgens **hoofddoel server**.

    ![Scherm opname van de knop Master doel server toevoegen](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. Down load het Unified Setup-bestand en voer het bestand op de virtuele machine uit om de hoofddoel server in te stellen.
4. Selecteer **volgende hoofd doel installeren**  >  .

    ![Scherm afbeelding van de optie voor het installeren van een hoofddoel doel](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. Selecteer de standaard installatie locatie en selecteer **installeren**.

     ![Scherm opname van de standaard installatie locatie](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. Selecteer **door gaan naar configuratie** om het hoofd doel bij de configuratie server te registreren.

    ![Scherm opname van de knop door gaan naar configuratie](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. Voer het IP-adres van de configuratie server in en voer vervolgens de wachtwoordzin in. Zie [een wachtwoordzin voor een configuratie server genereren](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)voor meer informatie over het genereren van een wachtwoordzin. 

    ![Scherm opname van de locatie waar het IP-adres en de wachtwoordzin voor de configuratie server moeten worden ingevoerd](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. Selecteer **Registreren**. Wanneer de registratie is voltooid, selecteert u **volt ooien**.

Wanneer de registratie is voltooid, wordt de server weer gegeven in het Azure Portal op **Recovery Services kluis**  >  configuratie servers voor **infra structuur site Recovery**  >  in de hoofddoel servers van de configuratie server.

 > [!NOTE]
 > Down load de meest recente versie van het [Unified Setup-bestand van de Master doel server voor Windows](https://aka.ms/latestmobsvc).

## <a name="next-steps"></a>Volgende stappen

[Site Recovery Deployment planner](https://aka.ms/asr-deployment-planner)te downloaden en uit te voeren.
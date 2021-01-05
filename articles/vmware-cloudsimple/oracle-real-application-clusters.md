---
title: 'Azure VMware-oplossing door CloudSimple: Optimaliseer uw CloudSimple-Privécloud voor Oracle RAC'
description: Hierin wordt beschreven hoe u een nieuw cluster implementeert en een virtuele machine optimaliseert voor de installatie en configuratie van Oracle Real Application Clusters (RAC)
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3959aae5f490af10c6747cfa67d9960e0c4a203f
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899266"
---
# <a name="optimize-your-cloudsimple-private-cloud-for-installing-oracle-rac"></a>Optimaliseer uw CloudSimple-Privécloud voor het installeren van Oracle RAC

U kunt Oracle Real Application Clusters (RAC) implementeren in uw persoonlijke cloud omgeving van CloudSimple. In deze hand leiding wordt beschreven hoe u een nieuw cluster implementeert en hoe u een virtuele machine voor de Oracle RAC-oplossing optimaliseert. Nadat u de stappen in dit onderwerp hebt voltooid, kunt u Oracle RAC installeren en configureren.

## <a name="storage-policy"></a>Opslag beleid

Voor een geslaagde implementatie van Oracle RAC is een voldoende aantal knoop punten in het cluster vereist.  In vSAN-opslag beleid worden storingen (FTT) toegepast op gegevens schijven die worden gebruikt voor het opslaan van de data base, het registreren van en het opnieuw uitvoeren van de schijven.  Het vereiste aantal knoop punten om fouten effectief te verdragen is 2N + 1 waarbij N de waarde is van FTT.

Voor beeld: als de gewenste FTT 2 is, moet het totale aantal knoop punten in het cluster 2 * 2 + 1 = 5 zijn.

## <a name="overview-of-deployment"></a>Overzicht van implementatie

In de volgende secties wordt beschreven hoe u uw CloudSimple Private Cloud Environment instelt voor Oracle RAC.

1. Aanbevolen procedures voor schijf configuratie
2. CloudSimple Private Cloud vSphere-cluster implementeren
3. Netwerken voor Oracle RAC instellen
4. VSAN-opslag beleid instellen
5. Oracle Vm's maken en gedeelde VM-schijven maken
6. Regels voor de affiniteit van de virtuele machine naar de host instellen

## <a name="best-practices-for-disk-configuration"></a>Aanbevolen procedures voor schijf configuratie

Virtuele Oracle RAC-machines hebben meerdere schijven die worden gebruikt voor een specifieke functie.  Gedeelde schijven worden gekoppeld op alle virtuele machines, die worden gebruikt door het Oracle RAC-cluster.  De schijven van het besturings systeem en de software-installatie worden alleen op de afzonderlijke virtuele machines gekoppeld.  

![Overzicht van virtuele-machine schijven van Oracle RAC](media/oracle-vm-disks-overview.png)

In het volgende voor beeld worden de schijven gebruikt die in de onderstaande tabel zijn gedefinieerd.

| Schijf                                      | Doel                                       | Gedeelde schijf |
|-------------------------------------------|-----------------------------------------------|-------------|
| Besturingssysteem                                        | Besturingssysteemschijf                         | Nee          |
| YRASTER                                      | Installatie locatie voor Oracle grid software     | Nee          |
| ENDDATABASE                                  | Installatie locatie voor Oracle data base-software | Nee          |
| ORAHOME                                   | Basis locatie voor binaire bestanden van Oracle-data base    | Nee          |
| BESTAND1, BESTAND2, DATA3, DATA4                | Schijf waarop Oracle-database bestanden worden opgeslagen   | Ja         |
| REDO1, REDO2, REDO3, REDO4, REDO5, REDO6  | Logboek schijven opnieuw uitvoeren                                | Ja         |
| OCR1, OCR2, OCR3, OCR4, OCR5              | Stem schijven                                  | Ja         |
| FRA1, FRA2                                | Schijven voor snel herstel gebied                      | Ja         |

![Schijf configuratie van de virtuele Oracle-machine](media/oracle-vmdk.png)

### <a name="virtual-machine-configuration"></a>Configuratie van virtuele machine

* Elke virtuele machine is geconfigureerd met vier SCSI-controllers.
* Het type SCSI-controller is ingesteld op VMware pvscsi.
* Er zijn meerdere virtuele schijven (. vmdk) gemaakt.
* Schijven zijn gekoppeld op verschillende SCSI-controllers.
* Het type delen van meerdere schrijvers is ingesteld voor gedeelde cluster schijven.
* vSAN-opslag beleid is gedefinieerd om een hoge Beschik baarheid van schijven te garanderen.

### <a name="operating-system-and-software-disk-configuration"></a>Configuratie van besturings systeem en software schijf

Elke Oracle-virtuele machine is geconfigureerd met meerdere schijven voor het hostbesturingssysteem, wisselen, software-installatie en andere functies van het besturings systeem.  Deze schijven worden niet gedeeld tussen de virtuele machines.  

* Drie schijven voor elke virtuele machine worden geconfigureerd als virtuele schijven en gekoppeld op virtuele Oracle RAC-machines.
    * Besturingssysteemschijf
    * Schijf voor het opslaan van Oracle-raster bestanden installeren
    * Schijf voor het opslaan van Oracle data base-installatie bestanden
* Schijven kunnen worden geconfigureerd als **Thin provisioned**.
* Elke schijf wordt gekoppeld op de eerste SCSI-controller (SCSI0).  
* Delen is ingesteld op **geen delen**.
* Redundantie wordt gedefinieerd op de opslag met behulp van vSAN-beleid.  

![Diagram waarin de fysieke configuratie van de Oracle RAC-besturingssysteem schijf wordt weer gegeven.](media/oracle-vm-os-disks.png)

### <a name="data-disk-configuration"></a>Configuratie van de gegevens schijf

Gegevens schijven worden voornamelijk gebruikt voor het opslaan van database bestanden.  

* Vier schijven worden geconfigureerd als virtuele schijven en gekoppeld op alle virtuele Oracle RAC-machines.
* Elke schijf wordt gekoppeld op een andere SCSI-controller.
* Elke virtuele schijf is geconfigureerd als een **brede inrichting** die in de regel is ingesteld op nul.  
* Delen is ingesteld op **multi Writer**.  
* De schijven moeten worden geconfigureerd als een schijf groep voor automatische opslag beheer (ASM).  
* Redundantie wordt gedefinieerd op de opslag met behulp van vSAN-beleid.  
* ASM-redundantie is ingesteld op **externe** redundantie.

![Configuratie van Oracle RAC Data Disk-groep](media/oracle-vm-data-disks.png)

### <a name="redo-log-disk-configuration"></a>De configuratie van de logboek schijf opnieuw uitvoeren

Logboek bestanden opnieuw uitvoeren worden gebruikt voor het opslaan van een kopie van de wijzigingen die zijn aangebracht in de data base.  De logboek bestanden worden gebruikt wanneer gegevens moeten worden hersteld na storingen.

* Logboek schijven opnieuw moeten worden geconfigureerd als meerdere schijf groepen.  
* Er worden zes schijven gemaakt en gekoppeld op alle virtuele Oracle RAC-machines.
* Schijven zijn gekoppeld op verschillende SCSI-controllers
* Elke virtuele schijf is geconfigureerd als een **brede inrichting** die in de regel is ingesteld op nul.
* Delen is ingesteld op **multi Writer**.  
* De schijven moeten worden geconfigureerd als twee ASM-schijf groepen.
* Elke ASM-schijf groep bevat drie schijven die zich op verschillende SCSI-controllers bevinden.  
* ASM-redundantie is ingesteld op **normale** redundantie.
* Vijf logboek bestanden voor opnieuw uitvoeren worden gemaakt voor de logboek groep voor opnieuw uitvoeren van ASM

```
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
```

![Configuratie voor opnieuw registreren van de schijf groep van Oracle RAC](media/oracle-vm-redo-log-disks.png)

### <a name="oracle-voting-disk-configuration"></a>Configuratie van Oracle-stem schijven

Stem schijven bieden functionaliteit voor quorum schijven als een extra communicatie kanaal om te voor komen dat er sprake is van Split-hersenen.

* Er worden vijf schijven gemaakt en gekoppeld op alle virtuele Oracle RAC-machines.
* Schijven zijn gekoppeld op één SCSI-controller
* Elke virtuele schijf is geconfigureerd als een **brede inrichting** die in de regel is ingesteld op nul.
* Delen is ingesteld op **multi Writer**.  
* De schijven moeten worden geconfigureerd als een ASM-schijf groep.  
* ASM-redundantie is ingesteld op **hoge** redundantie.

![Configuratie van de stemmings schijf groep van Oracle RAC](media/oracle-vm-voting-disks.png)

### <a name="oracle-fast-recovery-area-disk-configuration-optional"></a>Schijf configuratie voor snel herstel gebied van Oracle (optioneel)

De snelle herstel ruimte (FRA) is het bestands systeem dat wordt beheerd door de Oracle ASM-schijf groep.  FRA biedt een gedeelde opslag locatie voor back-up-en herstel bestanden. Oracle maakt gearchiveerde logboeken en Flashback-Logboeken in het gebied voor snel herstel. Oracle Recovery Manager (RMAN) kan optioneel de back-upsets en installatie kopieën van afbeeldingen opslaan in het gebied voor snel herstel en deze gebruiken bij het herstellen van bestanden tijdens herstel van media.

* Er worden twee schijven gemaakt en gekoppeld op alle virtuele Oracle RAC-machines.
* Schijven zijn gekoppeld op een andere SCSI-controller
* Elke virtuele schijf is geconfigureerd als een **brede inrichting** die in de regel is ingesteld op nul.
* Delen is ingesteld op **multi Writer**.  
* De schijven moeten worden geconfigureerd als een ASM-schijf groep.  
* ASM-redundantie is ingesteld op **externe** redundantie.

![Diagram waarin de configuratie van de stem van de groep van Oracle RAC wordt weer gegeven.](media/oracle-vm-fra-disks.png)

## <a name="deploy-cloudsimple-private-cloud-vsphere-cluster"></a>CloudSimple Private Cloud vSphere-cluster implementeren

Ga als volgt te werk om een vSphere-cluster te implementeren in uw Privécloud:

1. Maak in de CloudSimple-Portal [een privécloud](create-private-cloud.md). CloudSimple maakt een standaard-vCenter-gebruiker met de naam ' cloudowner ' in de zojuist gemaakte Privécloud. Zie voor meer informatie over het standaard model gebruiker en machtiging van de privécloud [het machtigings model privécloud](learn-private-cloud-permissions.md).  Met deze stap maakt u het primaire beheer cluster voor uw Privécloud.

2. Vouw in de CloudSimple-Portal [de privécloud](expand-private-cloud.md) uit met een nieuw cluster.  Dit cluster wordt gebruikt voor het implementeren van Oracle RAC.  Selecteer het aantal knoop punten op basis van de gewenste fout tolerantie (mini maal drie knoop punten).

## <a name="set-up-networking-for-oracle-rac"></a>Netwerken voor Oracle RAC instellen

1. Maak in uw Privécloud [twee vlan's](create-vlan-subnet.md), één voor het open bare Oracle-netwerk en een voor het Oracle-particuliere netwerk en wijs de juiste SUBNET-CIDR toe.
2. Nadat de VLAN'S zijn gemaakt, maakt u de [gedistribueerde poort groepen op de privécloud](create-vlan-subnet.md#use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere).
3. Stel een [virtuele machine voor de DHCP-en DNS-server](dns-dhcp-setup.md) in op uw beheer cluster voor de Oracle-omgeving.
4. [Configureer DNS-door sturen op de DNS-server die](on-premises-dns-setup.md#create-a-conditional-forwarder) in de privécloud is geïnstalleerd.

## <a name="set-up-vsan-storage-policies"></a>VSAN-opslag beleid instellen

met vSAN-beleid worden de fouten gedefinieerd die moeten worden toegestaan en schijf striping voor de gegevens die op de VM-schijven zijn opgeslagen.  Het gemaakte opslag beleid moet worden toegepast op de VM-schijven tijdens het maken van de VM.

1. [Meld u aan bij de vSphere-client](./vcenter-access.md) van uw privécloud.
2. Selecteer in het bovenste menu **beleids regels en profielen**.
3. Selecteer in het menu links **VM-opslag beleid** en selecteer vervolgens **een VM-opslag beleid maken**.
4. Voer een duidelijke naam in voor het beleid en klik op **volgende**.
5. Selecteer in de sectie **beleids structuur** de optie **regels inschakelen voor vSAN-opslag** en klik op **volgende**.
6. Selecteer in de  >  sectie **Beschik baarheid** van vSAN **geen** voor site ramp tolerantie. Selecteer de optie voor **RAID-mirroring** voor de gewenste FTT als u niet wilt dat deze kan worden toegelaten.
    ![vSAN-instellingen ](media/oracle-rac-storage-wizard-vsan.png) .
7. Selecteer in de sectie **Geavanceerd** het aantal schijf Stripes per object. Voor object ruimte reservering selecteert u **dik ingericht**. Selecteer **object controlesom uitschakelen**. Klik op **volgende**.
8. Volg de instructies op het scherm om de lijst met compatibele vSAN-gegevens opslag te bekijken, de instellingen te controleren en de installatie te volt ooien.

## <a name="create-oracle-vms-and-create-shared-vm-disks-for-oracle"></a>Oracle Vm's maken en gedeelde VM-schijven maken voor Oracle

Als u een virtuele machine voor Oracle wilt maken, moet u een bestaande virtuele machine klonen of een nieuwe maken.  In deze sectie wordt beschreven hoe u een nieuwe virtuele machine maakt en deze vervolgens kloont om een tweede te maken nadat u het basis besturingssysteem hebt geïnstalleerd.  Nadat de Vm's zijn gemaakt, kunt u hieraan een add-schijf toevoegen.  Oracle-cluster maakt gebruik van gedeelde schijven voor het opslaan, gegevens, logboeken en opnieuw uitvoeren van Logboeken.

### <a name="create-vms"></a>Virtuele machines maken

1. Klik in vCenter op het pictogram **hosts en clusters** . Selecteer het cluster dat u hebt gemaakt voor Oracle.
2. Klik met de rechter muisknop op het cluster en selecteer **nieuwe virtuele machine**.
3. Selecteer **nieuwe virtuele machine maken** en klik op **volgende**.
4. Geef de machine een naam, selecteer de locatie van de Oracle-VM en klik op **volgende**.
5. Selecteer de cluster bron en klik op **volgende**.
6. Selecteer de vSAN-gegevens opslag voor het cluster en klik op **volgende**.
7. Behoud de standaard selectie van de ESXi 6,5-compatibiliteit en klik op **volgende**.
8. Selecteer het gast besturingssysteem van de ISO voor de virtuele machine die u maakt en klik op **volgende**.
9. Selecteer de grootte van de harde schijf die vereist is voor het installeren van het besturings systeem.
10. Als u de toepassing wilt installeren op een ander apparaat, klikt u op **nieuw apparaat toevoegen**.
11. Selecteer netwerk opties en wijs de gedistribueerde poort groep toe die is gemaakt voor het open bare netwerk.
12. Als u extra netwerk interfaces wilt toevoegen, klikt u op **nieuw apparaat toevoegen** en selecteert u de gedistribueerde poort groep die voor het particuliere netwerk is gemaakt.
13. Voor nieuw DC/DVD-station selecteert u het ISO-bestand Data Store dat de ISO bevat voor de voorkeurs installatie van het besturings systeem. Selecteer het bestand dat u eerder hebt geüpload naar de map Iso's en templates en klik op **OK**.
14. Controleer de instellingen en klik op **OK** om de nieuwe virtuele machine te maken.
15. Schakel de virtuele machine in. Installeer het besturings systeem en eventuele vereiste updates

Nadat het besturings systeem is geïnstalleerd, kunt u een tweede virtuele machine klonen. Klik met de rechter muisknop op de VM-vermelding en selecteer de optie en kloon.

### <a name="create-shared-disks-for-vms"></a>Gedeelde schijven voor virtuele machines maken

Oracle gebruikt een gedeelde schijf voor het opslaan van de gegevens, het registreren en opnieuw uitvoeren van logboek bestanden.  U kunt een gedeelde schijf maken op vCenter en deze koppelen op beide Vm's.  Voor betere prestaties plaatst u de gegevens schijven in verschillende stappen van SCSI-controllers hieronder laten zien hoe u een gedeelde schijf maakt in vCenter en deze vervolgens koppelt aan een virtuele machine. vCenter Flash-client wordt gebruikt voor het wijzigen van de VM-eigenschappen.

#### <a name="create-disks-on-the-first-vm"></a>Schijven maken op de eerste VM

1. Klik in vCenter met de rechter muisknop op een van de Oracle-Vm's en selecteer **Instellingen bewerken**.
2. Selecteer **SCSI-controller** in het gedeelte nieuw apparaat en klik op **toevoegen**.
3. Selecteer in de sectie nieuw apparaat de optie **nieuwe harde schijf** en klik op **toevoegen**.
4. De eigenschappen van de nieuwe harde schijf uitvouwen.
5. Geef de grootte van de harde schijf op.
6. Geef het VM-opslag beleid op dat u wilt instellen als vSAN-opslag beleid dat u eerder hebt gedefinieerd.
7. Selecteer de locatie als een map op vSAN gegevens opslag. De locatie helpt bij het bladeren en koppelen van de schijven aan een tweede virtuele machine.
8. Voor het inrichten van schijven selecteert u de **brede inrichting**.
9. Geef **multi-Writer** op voor delen.
10. Selecteer voor het knoop punt van het virtuele apparaat de nieuwe SCSI-controller die is gemaakt in stap 2.

    ![Scherm opname van de velden die nodig zijn voor het maken van schijven op de eerste VM.](media/oracle-rac-new-hard-disk.png)

Herhaal stap 2 tot en met 10 voor alle nieuwe schijven die nodig zijn voor de Oracle-gegevens, logboeken en opnieuw uitvoeren van logboek bestanden.

#### <a name="attach-disks-to-second-vm"></a>Schijven koppelen aan een tweede VM

1. Klik in vCenter met de rechter muisknop op een van de Oracle-Vm's en selecteer **Instellingen bewerken**.
2. Selecteer **SCSI-controller** in het gedeelte nieuw apparaat en klik op **toevoegen**.
3. Selecteer in de sectie nieuw apparaat de optie **bestaande vaste schijf** en klik op **toevoegen**.
4. Blader naar de locatie waar de schijf is gemaakt voor de eerste VM en selecteer het VMDK-bestand.
5. Geef het VM-opslag beleid op dat u wilt instellen als vSAN-opslag beleid dat u eerder hebt gedefinieerd.
6. Voor het inrichten van schijven selecteert u de **brede inrichting**.
7. Geef **multi-Writer** op voor delen.
8. Selecteer voor het knoop punt van het virtuele apparaat de nieuwe SCSI-controller die is gemaakt in stap 2.

    ![Schijven maken op de eerste VM](media/oracle-rac-existing-hard-disk.png)

Herhaal stap 2 tot en met 7 voor alle nieuwe schijven die nodig zijn voor de Oracle-gegevens, logboeken en opnieuw uitvoeren van logboek bestanden.

## <a name="set-up-vm-host-affinity-rules"></a>Regels voor de affiniteit van de VM-host instellen

Regels voor affiniteit tussen de virtuele machine en de host zorgen ervoor dat de VM wordt uitgevoerd op de gewenste host.  U kunt regels definiëren in vCenter om ervoor te zorgen dat de Oracle-VM wordt uitgevoerd op de host met voldoende bronnen en om te voldoen aan de specifieke licentie vereisten.

1. [Escaleren de bevoegdheden](escalate-private-cloud-privileges.md) van de cloudowner-gebruiker in de CloudSimple-Portal.
2. Meld u aan bij de vSphere-client van uw Privécloud.
3. Selecteer in de vSphere-client het cluster waar Oracle Vm's worden geïmplementeerd en klik op **configureren**.
4. Onder configureren selecteert u **virtuele machines/hostgroepen**.
5. Klik op **+** .
6. Voeg een VM-groep toe. Selecteer de **VM-groep** als het type. Voer de naam van de groep in. Selecteer de virtuele machines en klik vervolgens op **OK** om de groep te maken.
6. Voeg een hostgroep toe. Selecteer **hostgroep** als het type. Voer de naam van de groep in. Selecteer de hosts waar de virtuele machines worden uitgevoerd en klik vervolgens op **OK** om de groep te maken.
7. Als u een regel wilt maken, klikt u op **virtuele machine/host-regels**.
8. Klik op **+** .
9. Voer een naam in voor de regel en Schakel **inschakelen in**.
10. Selecteer **virtual machines als host** voor het regel type.
11. Selecteer de VM-groep die de Oracle Vm's bevat.
12. Select **moet worden uitgevoerd op hosts in deze groep**.
13. Selecteer de hostgroep die u hebt gemaakt.
14. Klik op **OK** om de regel te maken.

## <a name="references"></a>Verwijzingen

* [Over vSAN-beleid](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-08911FD3-2462-4C1C-AE81-0D4DBC8F7990.html)
* [VMware multi-Writer-kenmerk voor gedeelde Vmdk's](https://docs.vmware.com/en/VMware-Cloud-on-AWS/solutions/VMware-Cloud-on-AWS.df6735f8b729fee463802083d46fdc75/GUID-A7642A82B3D6C5F7806DB40A3F2766D9.html)

---
title: Nieuw in Microsoft Azure Backup Server
description: Microsoft Azure Backup Server biedt uitgebreide back-upmogelijkheden voor het beveiligen van Vm's, bestanden en mappen, werk belastingen en nog veel meer.
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: 39050d0f658e29b82f270f1fe53026e2fb80bfa1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91332760"
---
# <a name="whats-new-in-microsoft-azure-backup-server-mabs"></a>Wat is er nieuw in Microsoft Azure Backup Server (MABS)

## <a name="whats-new-in-mabs-v3-ur1"></a>Wat is er nieuw in MABS v3 UR1

Microsoft Azure Backup Server (MABS) versie 3 UR1 is de meest recente update en bevat essentiële oplossingen voor fouten en andere functies en verbeteringen. Zie KB-artikel [4534062](https://support.microsoft.com/help/4534062)voor een overzicht van de fouten die zijn opgelost en de installatie-instructies voor MABS v3 UR1.

>[!NOTE]
>Ondersteuning voor de 32-bits beveiligings agent is afgeschaft met MABS v3 UR1. Zie [afschaffing van 32 bits beveiligings agent](#32-bit-protection-agent-deprecation).

### <a name="faster-backups-with-tiered-storage-using-ssds"></a>Snellere back-ups met gelaagde opslag met behulp van Ssd's

MABS v2 heeft [modern Backup Storage](backup-mabs-add-storage.md) (MBS) geïntroduceerd, waardoor het opslag gebruik en de prestaties worden verbeterd. MBS gebruikt ReFS als onderliggend bestands systeem en is ontworpen om gebruik te maken van hybride opslag, zoals gelaagde opslag.

Om de schaal en prestaties van MBS te vervolledigen, raden we u aan om een klein percentage (4% van de totale opslag) van Flash-opslag (SSD) met MABS v3 UR1 als een gelaagd volume in combi natie met DPM-HDD-opslag te gebruiken. MABS v3 UR1 met gelaagde opslag levert 50-70% snellere back-ups. Raadpleeg het DPM-artikel [MBS met tiered Storage instellen](/system-center/dpm/add-storage#set-up-mbs-with-tiered-storage) voor stappen voor het configureren van gelaagde opslag.

### <a name="support-for-refs-volumes"></a>Ondersteuning voor ReFS-volumes

Met MABS v3 UR1 kunt u een back-up maken van de ReFS-volumes en werk belastingen die op het ReFS-volume zijn geïmplementeerd. U kunt een back-up maken van de volgende werk belastingen die zijn geïmplementeerd op de ReFS-volumes:

* Besturings systeem (64-bits): Windows Server 2019, 2016, 2012 R2, 2012.
* SQL Server: SQL Server 2019, SQL Server 2017, 2016.
* Exchange: Exchange 2019, 2016.
* Share point: share point 2019, 2016 met de nieuwste versie van SP.

>[!NOTE]
> Back-ups van Hyper-V-Vm's die zijn opgeslagen op een ReFS-volume worden ondersteund met MABS v3

>BELANG rijk Er zijn enkele problemen met het maken van back-ups van ontdubbelde ReFS-volumes. We werken eraan om deze sectie bij te werken zodra er een oplossing beschikbaar is. Tot dat moment wordt de ondersteuning voor het maken van back-ups van ontdubbelde ReFS-volumes uit MABSv3 UR1 verwijderd.

### <a name="azure-vmware-solution-protection-support"></a>Ondersteuning voor Azure VMware-oplossings beveiliging

Met MABS v3 UR1 kunt u nu virtuele machines beveiligen die zijn geïmplementeerd in de [Azure VMware-oplossing](../azure-vmware/index.yml).

### <a name="vmware-parallel-backups"></a>VMware parallelle back-ups

Met MABS v3 UR1 worden alle back-ups van uw VMware-Vm's binnen één beveiligings groep parallel, waardoor er 25% snellere VM-back-ups worden gemaakt.
Met eerdere versies van MABS zijn parallelle back-ups alleen uitgevoerd in verschillende beveiligings groepen. Met MABS v3 UR1 worden VMware Delta-replicatie taken parallel uitgevoerd. Het aantal taken dat parallel moet worden uitgevoerd, is standaard ingesteld op 8. Meer informatie over de [parallelle back-ups van VMware](backup-azure-backup-server-vmware.md#vmware-parallel-backups).

### <a name="disk-exclusion-for-vmware-vm-backup"></a>Schijf uitsluiting voor VMware VM-back-ups

Met MABS v3 UR1 kunt u specifieke schijven uitsluiten van een back-up van een virtuele VMware-machine. Meer informatie over het [uitsluiten van schijven van VMware VM-back-ups](backup-azure-backup-server-vmware.md#exclude-disk-from-vmware-vm-backup).  

### <a name="support-for-additional-layer-of-authentication-to-delete-online-backup"></a>Ondersteuning voor extra verificatie slaag voor het verwijderen van online back-ups

Met MABS v3 UR1 wordt een extra laag voor verificatie toegevoegd voor kritieke bewerkingen. U wordt gevraagd om een beveiligings pincode op te geven wanneer u de **beveiliging voor het verwijderen van gegevens stop** uitvoert.

### <a name="offline-backup-improvements"></a>Verbeteringen in offline back-up

MABS v3 UR1 verbetert de ervaring van offline back-ups met de Azure import/export-service. Zie de bijgewerkte stappen [hier](./backup-azure-backup-server-import-export.md)voor meer informatie.

>[!NOTE]
>De update brengt ook de preview voor offline back-ups met behulp van Azure Data Box in MABS. Neem contact [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com) op met meer informatie.

### <a name="new-cmdlet-parameter"></a>Nieuwe cmdlet-para meter

MABS v3 UR1 bevat een nieuwe para meter **[-CheckReplicaFragmentation]**. De nieuwe para meter berekent het fragmentatie percentage voor een replica en is opgenomen in de cmdlet **copy-DPMDatasourceReplica** .

### <a name="32-bit-protection-agent-deprecation"></a>32-bits beveiligings agent-afschaffing

Met MABS v3 UR1 wordt ondersteuning voor 32-bits beveiligings agent niet meer ondersteund. Het is niet mogelijk 32-bits werk belastingen te beveiligen na de upgrade van de MABS v3-server naar UR1. Bestaande 32-bits beveiligingsagents hebben een uitgeschakelde status en geplande back-ups mislukken met de **agent is uitgeschakeld** . Als u de back-upgegevens voor deze agents wilt behouden, kunt u de beveiliging stoppen met de optie gegevens behouden. Anders kan de beveiligings agent worden verwijderd.

>[!NOTE]
>Bekijk de [bijgewerkte beveiligings matrix](./backup-mabs-protection-matrix.md) voor meer informatie over de ondersteunde werk belastingen voor beveiliging met MABS ur 1.

## <a name="whats-new-in-mabs-v3-rtm"></a>Wat is er nieuw in MABS v3 RTM

Microsoft Azure Backup Server versie 3 (MABS v3) omvat essentiële oplossingen voor Windows Server 2019, ondersteuning voor SQL 2017 en andere functies en verbeteringen. Zie KB-artikel [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3)voor een overzicht van de fouten die zijn opgelost en de installatie-instructies voor MABS v3.

De volgende functies zijn opgenomen in MABS V3:

### <a name="volume-to-volume-migration"></a>Migratie van volumes naar volume

Met Modern Backup Storage (MBS) in MABS v2 hebben we werkbelasting bewuste opslag aangekondigd, waar u bepaalde werk belastingen zodanig configureert dat een back-up wordt gemaakt van specifieke opslag, op basis van opslag eigenschappen. Na de configuratie is het echter mogelijk dat u back-ups van bepaalde gegevens bronnen wilt verplaatsen naar andere opslag voor optimaal gebruik van bronnen. MABS V3 biedt u de mogelijkheid om uw back-ups te migreren en ze zodanig te configureren dat deze in [drie stappen](https://techcommunity.microsoft.com/t5/system-center-blog/sc-2016-dpm-ur4-migrate-backup-storage-in-3-simple-steps/ba-p/351842)worden opgeslagen op een ander volume.

### <a name="prevent-unexpected-data-loss"></a>Onverwacht gegevens verlies voor komen

In ondernemingen wordt MABS beheerd door een team van beheerders. Hoewel er richt lijnen zijn voor opslag die moet worden gebruikt voor back-ups, kan een onjuist volume dat is gegeven aan MABS als back-upopslag leiden tot verlies van kritieke gegevens. Met MABS v3 kunt u dergelijke scenario's voor komen door de volumes te configureren als die die niet beschikbaar zijn voor opslag met behulp van [deze Power shell-cmdlets](./backup-mabs-add-storage.md).

### <a name="custom-size-allocation"></a>Toewijzing van aangepaste grootte

Modern Backup Storage (MBS) verbruikt opslag dun, indien nodig. Om dit te doen, berekent MABS de grootte van de gegevens waarvan een back-up wordt gemaakt wanneer deze is geconfigureerd voor beveiliging. Als er echter meerdere bestanden en mappen samen een back-up worden gemaakt, zoals in het geval van een bestands server, kan de grootte berekening lang duren. Met MABS v3 kunt u MABS zo configureren dat de volume grootte als standaard wordt geaccepteerd, in plaats van de grootte van elk bestand te berekenen, wat tijd bespaart.

### <a name="optimized-cc-for-rct-vms"></a>Geoptimaliseerde CC voor RCT-Vm's

MABS maakt gebruik van RCT (het systeem eigen bijhouden van wijzigingen in Hyper-V), wat de nood zaak van het tijdrovende consistentie controles in scenario's verlaagt als de VM vastloopt. RCT biedt betere tolerantie dan het bijhouden van wijzigingen door de VSS-back-ups op basis van momentopnamen. MABS v3 optimaliseert het netwerk-en opslag verbruik verder door alleen de gewijzigde gegevens tijdens de consistentie controles over te dragen.

### <a name="support-to-tls-12"></a>Ondersteuning voor TLS 1,2

TLS 1,2 is de veilige manier van communicatie die door micro soft wordt voorgesteld met de beste versleuteling van de klasse. MABS ondersteunt nu TLS 1,2-communicatie tussen MABS en de beveiligde servers, voor verificatie op basis van certificaten en voor Cloud back-ups.

### <a name="vmware-vm-protection-support"></a>Ondersteuning voor VMware VM-beveiliging

VM-back-ups van VMware worden nu ondersteund voor productie-implementaties. MABS V3 biedt het volgende voor VMware VM-beveiliging:

* Ondersteuning voor vCenter en ESXi 6,5, samen met ondersteuning voor 5,5 en 6,0.
* Automatische beveiliging van virtuele VMware-machines in de Cloud. Als nieuwe virtuele VMware-machines worden toegevoegd aan een beveiligde map, worden ze automatisch beveiligd op schijf en in de Cloud.
* Herstel efficiëntie verbeteringen voor het herstellen van een alternatieve locatie in VMware.

### <a name="sql-2017-support"></a>Ondersteuning voor SQL 2017

MABS v3 kan worden geïnstalleerd met SQL 2017 als de MABS-data base. U kunt de SQL-Server upgraden van SQL 2016 naar SQL 2017 of het product vernieuwen. U kunt ook een back-up maken van de SQL 2017-werk belasting in geclusterde en niet-geclusterde omgevingen met MABS v3.

### <a name="windows-server-2019-support"></a>Ondersteuning voor Windows Server 2019

MABS v3 kan worden geïnstalleerd op Windows Server 2019. Als u MABS V3 met WS2019 wilt gebruiken, kunt u uw besturings systeem upgraden naar WS2019 voordat u installeert/bijwerkt naar MABS v3 of u kunt een upgrade uitvoeren van uw besturings systeem na het installeren/upgraden van v3 op WS2016.

MABS v3 is een volledige versie en kan rechtstreeks worden geïnstalleerd op Windows Server 2016, Windows Server 2019 of worden bijgewerkt vanaf MABS v2. Lees over de installatie vereisten voordat u een upgrade uitvoert naar of een back-upserver v3 installeert.
Meer informatie over de installatie-en upgrade stappen voor MABS vindt u [hier](./backup-azure-microsoft-azure-backup.md#software-package).

> [!NOTE]
>
> MABS heeft dezelfde code base als System Center Data Protection Manager. MABS v3 is gelijk aan Data Protection Manager 1807. MABS v3 UR1 is gelijk aan Data Protection Manager 2019 UR1.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het voorbereiden van uw server of het beveiligen van een workload:

* [Werk belastingen voor de back-upserver voorbereiden](backup-azure-microsoft-azure-backup.md)
* [Backup Server gebruiken om back-ups te maken van een VMware-server](backup-azure-backup-server-vmware.md)
* [Backup Server gebruiken om back-ups te maken van SQL Server](backup-azure-sql-mabs.md)
* [Modern Backup Storage gebruiken met een back-upserver](backup-mabs-add-storage.md)

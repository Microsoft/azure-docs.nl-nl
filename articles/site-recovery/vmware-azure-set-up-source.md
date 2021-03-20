---
title: Bron instellingen voor nood herstel van VMware instellen op Azure met Azure Site Recovery
description: In dit artikel wordt beschreven hoe u uw on-premises omgeving instelt voor het repliceren van virtuele VMware-machines naar Azure met Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: afd3979690b8952c915a49099ee04b3d416031fd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88189738"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>De bron omgeving instellen voor de replicatie van VMware naar Azure

In dit artikel wordt beschreven hoe u de on-premises bedrijfs omgeving instelt voor het repliceren van virtuele VMware-machines naar Azure. Het artikel bevat stappen voor het selecteren van het replicatie scenario, het instellen van een on-premises computer als de Site Recovery configuratie server en het automatisch detecteren van on-premises Vm's.

## <a name="prerequisites"></a>Vereisten

In het artikel wordt ervan uitgegaan dat u al hebt:

- Uw implementatie is gepland met behulp van [Azure site Recovery Deployment planner](site-recovery-deployment-planner.md). Zo kunt u voldoende band breedte toewijzen op basis van uw dagelijkse gegevens wijzigings frequentie om te voldoen aan uw gewenste Recovery Point Objective (RPO).
- [Stel resources](tutorial-prepare-azure.md) in het [Azure Portal](https://portal.azure.com)in.
- [On-premises VMware instellen](vmware-azure-tutorial-prepare-on-premises.md), met inbegrip van een toegewezen account voor automatische detectie.

## <a name="choose-your-protection-goals"></a>Uw beveiligings doelen kiezen

1. Selecteer de naam van de kluis in **Recovery Services-kluizen**. We gebruiken **ContosoVMVault** voor dit scenario.
2. Selecteer in **Aan de slag** Site Recovery. Selecteer vervolgens **Infrastructuur voorbereiden**.
3. In **Beveiligingsdoel** > **Waar bevinden de machines zich**, selecteert u **On-premises**.
4. In **Waarnaartoe wilt u de machines repliceren** selecteert u **Naar Azure**.
5. In **Zijn de machines gevirtualiseerd** selecteert **Ja, met VMware vSphere Hypervisor**. Selecteer vervolgens **OK**.

## <a name="set-up-the-configuration-server"></a>De configuratieserver instellen

U kunt de configuratie server instellen als een on-premises virtuele VMware-machine via een eicellen-sjabloon (open Virtualization Application). Meer [informatie](./vmware-azure-architecture.md) over de onderdelen die worden geïnstalleerd op de virtuele VMWare-machine.

1. Meer informatie over de [vereisten](vmware-azure-deploy-configuration-server.md#prerequisites) voor de implementatie van de configuratie server.
2. [Controleer de capaciteits nummers](vmware-azure-deploy-configuration-server.md#sizing-and-capacity-requirements) voor de implementatie.
3. [Down load](vmware-azure-deploy-configuration-server.md#download-the-template) en [Importeer](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) de eicellen-sjabloon voor het instellen van een on-premises VMware-VM waarop de configuratie server wordt uitgevoerd. De licentie die is opgenomen in de sjabloon is een evaluatie licentie en is 180 dagen geldig. Na deze periode moet de klant de Windows activeren met een aangeschafte licentie.
4. Schakel de VMware-VM in en [Registreer deze](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) in de Recovery Services kluis.

## <a name="azure-site-recovery-folder-exclusions-from-antivirus-program"></a>Uitsluitingen van mappen Azure Site Recovery van antivirus programma

### <a name="if-antivirus-software-is-active-on-source-machine"></a>Als antivirus software actief is op de bron machine

Als er een antivirus software op de bron machine actief is, moet de installatiemap worden uitgesloten. Sluit daarom map *C:\ProgramData\ASR\agent* voor een soepele replicatie uit.

### <a name="if-antivirus-software-is-active-on-configuration-server"></a>Als antivirus software actief is op de configuratie server

De volgende mappen uitsluiten van antivirus software voor soepele replicatie en verbindings problemen voor komen

- C:\Program Files\Microsoft Azure Recovery Services-agent.
- C:\Program Files\Microsoft Azure Site Recovery provider
- C:\Program Files\Microsoft Azure Site Recovery Configuration Manager 
- Hulp programma C:\Program Files\Microsoft Azure Site Recovery-fout verzameling 
  - C:\thirdparty
  - Map
  - C:\strawberry
  - C:\ProgramData\MySQL
  - C:\Program Files (x86) \MySQL
  - C:\ProgramData\ASR
  - C:\ProgramData\Microsoft Azure Site Recovery
  - C:\ProgramData\ASRLogs
  - C:\ProgramData\ASRSetupLogs
  - C:\ProgramData\LogUploadServiceLogs
  - C:\inetpub
  - Installatiemap van Site Recovery Server. Bijvoorbeeld: E:\Program-bestanden (x86) \Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-scale-out-process-servermaster-target"></a>Als antivirus software actief is op scale-out proces server/Master doel

Volgende mappen uitsluiten van antivirus software

1. C:\Program Files\Microsoft Azure Recovery Services-agent
2. C:\ProgramData\ASR
3. C:\ProgramData\ASRLogs
4. C:\ProgramData\ASRSetupLogs
5. C:\ProgramData\LogUploadServiceLogs
6. C:\ProgramData\Microsoft Azure Site Recovery
7. Azure Site Recovery-installatiemap voor de proces server met taak verdeling, voor beeld: C:\Program Files (x86) \Microsoft Azure Site Recovery

### <a name="if-antivirus-software-is-active-on-the-linux-master-target"></a>Als antivirus software actief is op het Linux-hoofd doel

Volgende mappen uitsluiten van antivirus software

1.  /usr/local/ASR
2.  /usr/local/InMage
3.  /var/log/vxlogs
4.  /var/log
5.  /var/log/ApplicationPolicyLogs
6.  /var/log/ASRsetuptelemetry
7.  /var/log/ASRsetuptelemetry_uploaded


## <a name="next-steps"></a>Volgende stappen
[Uw doel omgeving instellen](./vmware-azure-set-up-target.md) 

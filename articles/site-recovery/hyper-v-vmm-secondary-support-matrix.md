---
title: Ondersteunings matrix-herstel na nood geval voor Hyper-V naar een secundaire VMM-site met Azure Site Recovery
description: Hiermee wordt een overzicht gegeven van de ondersteuning voor Hyper-V-VM-replicatie in VMM-Clouds naar een secundaire site met Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: af7baf413c9054ef3e5bf527851ac06c113cdce7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86131172"
---
# <a name="support-matrix-for-disaster-recovery-of-hyper-v-vms-to-a-secondary-site"></a>Ondersteuningsmatrix voor herstel na noodgeval van virtuele Hyper-V-machines naar een secundaire site

In dit artikel wordt een overzicht gegeven van wat er wordt ondersteund wanneer u de [Azure site Recovery](site-recovery-overview.md) -service gebruikt om virtuele machines van Hyper-V te repliceren die worden beheerd in System Center Virtual Machine Manager (VMM)-Clouds naar een secundaire site. Als u virtuele Hyper-V-machines naar Azure wilt repliceren, moet u [deze ondersteunings matrix](hyper-v-azure-support-matrix.md)raadplegen.

> [!NOTE]
> U kunt alleen repliceren naar een secundaire site wanneer uw Hyper-V-hosts worden beheerd in VMM-Clouds.


## <a name="host-servers"></a>Hostservers

**Besturingssysteem** | **Details**
--- | ---
Windows Server 2012 R2 | Op servers moeten de meest recente updates worden uitgevoerd.
Windows Server 2016 |  VMM 2016-Clouds met een combi natie van Windows Server 2016-en 2012 R2-hosts worden momenteel niet ondersteund.<br/><br/> Implementaties die zijn bijgewerkt van System Center 2012 R2 VMM 2012 R2 naar System Center 2016, worden momenteel niet ondersteund.


## <a name="replicated-vm-support"></a>Ondersteuning voor gerepliceerde VM'S

De volgende tabel bevat een overzicht van de ondersteuning van besturings systemen voor computers die met Site Recovery worden gerepliceerd. Elke werk belasting kan worden uitgevoerd op het ondersteunde besturings systeem.

**Windows-versie** | **Hyper-V (met VMM)**
--- | ---
Windows Server 2016 | Elk gast besturingssysteem dat wordt [ondersteund door Hyper-V](/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows) op Windows Server 2016 
Windows Server 2012 R2 | Elk gast besturingssysteem dat wordt [ondersteund door Hyper-V](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29) in Windows Server 2012 R2

## <a name="linux-machine-storage"></a>Linux-machine opslag

Alleen Linux-machines met de volgende opslag kunnen worden gerepliceerd:

- Bestands systeem (EXT3, ETX4, ReiserFS, XFS).
- Multipath-software-apparaattoewijzing.
- Volume manager (LVM2).
- Fysieke servers met HP CCISS-controller opslag worden niet ondersteund.
- Het ReiserFS-bestands systeem wordt alleen ondersteund op SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration---hostguest-vm"></a>Netwerk configuratie-host/gast-VM

**Configuratie** | **Ondersteund**  
--- | --- 
Host-NIC-koppeling | Ja 
Host-VLAN | Ja 
Host-IPv4 | Ja 
Host-IPv6 | Nee 
Gast-VM-NIC-koppeling | Nee
Gast-VM-IPv4 | Ja
Gast-VM-IPv6 | Nee
Gast-VM-Windows/Linux-statisch IP-adres | Ja
Gast-VM-multi-NIC | Ja


## <a name="storage"></a>Storage

### <a name="host-storage"></a>Host Storage

**Opslag (host)** | **Ondersteund**
--- | --- 
NFS | N.v.t.
SMB 3.0 |  Ja
SAN (ISCSI) | Ja
Meerdere paden (MPIO) | Ja

### <a name="guest-or-physical-server-storage"></a>Opslag van gast-of fysieke servers

**Configuratie** | **Ondersteund**
--- | --- | 
VMDK |  N.v.t.
VHD/VHDX | Ja (Maxi maal 16 schijven)
VM van generatie 2 | Ja
Gedeelde cluster schijf | Nee
Versleutelde schijf | Nee
UEFI| N.v.t.
NFS | Nee
SMB 3.0 | Nee
RDM | N.v.t.
Schijf > 1 TB | Ja
Volume met gestripte schijf > 1 TB<br/><br/> LVM | Ja
Opslagruimten | Ja
Hot toevoegen/verwijderen schijf | Nee
Schijf uitsluiten | Ja
Meerdere paden (MPIO) | Ja

## <a name="vaults"></a>Kluizen

**Actie** | **Ondersteund**
--- | --- 
Kluizen verplaatsen tussen resource groepen (binnen of tussen abonnementen) |  Nee
Opslag, netwerk, Azure-Vm's verplaatsen tussen resource groepen (binnen of tussen verschillende abonnementen) | Nee

## <a name="azure-site-recovery-provider"></a>Azure Site Recovery provider

De provider coördineert de communicatie tussen VMM-servers. 

**Jongste** | **Updates**
--- | --- 
5.1.19 ([beschikbaar via Portal](https://aka.ms/downloaddra) | [Nieuwste functies en oplossingen](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>Volgende stappen

[Virtuele Hyper-V-machines in VMM-Clouds repliceren naar een secundaire site](./hyper-v-vmm-disaster-recovery.md)

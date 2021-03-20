---
title: Ondersteunde VM-SKU's voor Azure Policy
description: Een artikel met een beschrijving van de ondersteunde VM-Sku's (per uitgever, afbeeldings aanbod en installatie kopie-SKU) die worden ondersteund voor het ingebouwde Azure-beleid van back-ups
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 4658d1beffe707682e173491edea1eac0db9c811
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "82183616"
---
# <a name="supported-vm-skus-for-azure-policy"></a>Ondersteunde VM-SKU's voor Azure Policy

Azure Backup biedt een ingebouwd beleid (met behulp van Azure Policy) dat kan worden toegewezen aan **alle virtuele Azure-machines in een opgegeven locatie binnen een abonnement of resource groep**. Wanneer dit beleid is toegewezen aan een bepaald bereik, worden alle nieuwe Vm's die in dat bereik zijn gemaakt, automatisch geconfigureerd voor back-up naar een **bestaande kluis op dezelfde locatie en hetzelfde abonnement**. De volgende tabel bevat een lijst met alle VM-Sku's die door dit beleid worden ondersteund.

## <a name="supported-vms"></a>Ondersteunde Vm's *

**Beleids naam:** Back-ups op Vm's van een locatie naar een bestaande centrale kluis op dezelfde locatie configureren

Uitgever van installatie kopie | Aanbieding van installatie kopie | Afbeeldings-SKU
--- | --- | ---
MicrosoftWindowsServer | WindowsServer | Windows Server 2008 R2 SP1 (2008-R2-SP1)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2008 R2 SP (2008-R2-SP1-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 Data Center (2012-Data Center)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 Data Center (2012-Data Center-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 R2 Data Center (2012-R2-Data Center)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 R2 Data Center (2012-R2-Data Center-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Data Center (2016-Data Center)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Data Center-Server Core (2016-Data Center-Server-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 Data Center-Server Core (2016-Data Center-Server-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 Data Center (2016-Data Center-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Data Center Server Core met containers (2016-Data Center-with-containers)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Extern bureaublad Session Host 2016 (2016-Data Center-met-RDSH)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Data Center (2019-Data Center)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Data Center Server Core (2019-Data Center-core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Data Center Server Core (2019-Data Center-core-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Data Center Server Core met containers (2019-Data Center-core-met-containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Data Center Server Core met containers (2019-Data Center-core-met-containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Data Center (2019-Data Center-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Data Center met containers (2019-Data Center-met-containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Data Center met containers (2019-Data Center-with-containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Data Center (zh-cn) (2019-Data Center-zhcn)
MicrosoftWindowsServer | WindowsServerSemiAnnual | Data Center-core-1709-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Data Center-core-1709-with-containers-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Data Center-core-1803-with-containers-smalldisk
MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | Alle image-Sku's
MicrosoftSQLServer | SQL2016SP1-WS2016 | Alle image-Sku's
MicrosoftSQLServer | SQL2016-WS2016 | Alle image-Sku's
MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | Alle image-Sku's
MicrosoftSQLServer | SQL2012SP3-WS2012R2 | Alle image-Sku's
MicrosoftSQLServer | SQL2016-WS2012R2 | Alle image-Sku's
MicrosoftSQLServer | SQL2014SP2-WS2012R2 | Alle image-Sku's
MicrosoftSQLServer | SQL2012SP3-WS2012R2-BYOL | Alle image-Sku's
MicrosoftSQLServer | SQL2014SP1-WS2012R2-BYOL | Alle image-Sku's
MicrosoftSQLServer | SQL2014SP2-WS2012R2-BYOL | Alle image-Sku's
MicrosoftSQLServer | SQL2016-WS2012R2-BYOL | Alle image-Sku's
MicrosoftRServer | MLServer-WS2016 | Alle image-Sku's
MicrosoftVisualStudio | Visual Studio | Alle image-Sku's
MicrosoftVisualStudio | Windows | Alle image-Sku's
MicrosoftDynamicsAX | Dynamics | Pre-req-AX7-Onebox-U8
micro soft-Ads | Windows-Data-Science-VM | Alle image-Sku's
MicrosoftWindowsDesktop | Windows-10 | Alle image-Sku's
RedHat | RHEL | 6,7, 6,8, 6,9, 6,10, 7,2, 7,3, 7,4, 7,5, 7,6, 7,7
RedHat | RHEL-SAP-HANA | 6,7, 7,2, 7,3
SUSE | SLES | 12. X
SUSE | SLES-HPC | 12. X
SUSE | SLES-HPC-Priority | 12. X
SUSE | SLES-SAP | 12. X
SUSE | SLES-SAP-BYOS | 12. X
SUSE | SLES-Priority | 12. X
SUSE | SLES-BYOS | 12. X
SUSE | SLES-SAPCAL | 12. X
SUSE | SLES-Standard | 12. X
Canonical | UbuntuServer | 14.04.0-LTS
Canonical | UbuntuServer | 14.04.1-LTS
Canonical | UbuntuServer | 14.04.2-LTS
Canonical | UbuntuServer | 14.04.3-LTS
Canonical | UbuntuServer | 14.04.4-LTS
Canonical | UbuntuServer | 14.04.5-DAILY-LTS
Canonical | UbuntuServer | 14.04.5-LTS
Canonical | UbuntuServer | 16,04-DAGELIJKS-LTS
Canonical | UbuntuServer | 16.04-LTS
Canonical | UbuntuServer | 16.04.0-LTS
Canonical | UbuntuServer | 18,04-DAGELIJKS-LTS
Canonical | UbuntuServer | 18.04-LTS
Oracle | Oracle-Linux | 6,8, 6,9, 6,10, 7,3, 7,4, 7,5, 7,6
OpenLogic | CentOS | 6. X, 7. X
OpenLogic | CentOS – LVM | 6. X, 7. X
OpenLogic | CentOS – SRIOV | 6. X, 7. X
cloudera | cloudera-CentOS-OS | 7. X

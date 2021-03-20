---
title: Ondersteuning voor SQL Server 2008 & 2008 R2 uitbreiden
description: Breid ondersteuning uit voor SQL Server 2008 en SQL Server 2008 R2 door uw SQL Server-exemplaar te migreren naar Azure, of om uitgebreide ondersteuning te kopen om instanties on-premises te blijven gebruiken.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 4c25adc16d14b4a5fb72ae0103ca05b193b40499
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359163"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Ondersteuning voor SQL Server 2008 en SQL Server 2008 R2 met Azure uitbreiden
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

SQL Server 2008 en SQL Server 2008 R2 hebben het [einde van hun levens cyclus voor de ondersteuning (EOS)](https://www.microsoft.com/sql-server/sql-server-2008)bereikt. Omdat veel klanten nog steeds gebruikmaken van beide versies, bieden we verschillende opties om ondersteuning te blijven krijgen. U kunt uw on-premises SQL Server-exemplaren migreren naar Azure virtual machines (Vm's), migreren naar Azure SQL Database, of on-premises blijven en uitgebreide beveiligings updates aanschaffen.

In tegens telling tot een beheerd exemplaar moet u uw toepassingen niet opnieuw certificeren met een virtuele machine van Azure. En in tegens telling tot on-premises, ontvangt u gratis uitgebreide beveiligings patches door te migreren naar een virtuele machine van Azure.

De rest van dit artikel bevat overwegingen voor het migreren van uw SQL Server-exemplaar naar een virtuele machine van Azure.

Zie [End of support](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)(Engelstalig) voor meer informatie over het einde van de ondersteunings opties.

## <a name="provisioning"></a>Inrichten

Er is een betalen naar gebruik- **SQL Server 2008 R2 op de Windows Server 2008 R2** -installatie kopie beschikbaar op Azure Marketplace.

Klanten die zich op SQL Server 2008 bevinden, moeten zelf een installatie of upgrade uitvoeren naar SQL Server 2008 R2. Daarnaast moeten klanten op Windows Server 2008 hun VM implementeren vanaf een aangepaste VHD of een upgrade uitvoeren naar Windows Server 2008 R2.

Voor installatie kopieën die via Azure Marketplace worden geïmplementeerd, wordt de SQL IaaS-extensie vooraf geïnstalleerd. De SQL IaaS-uitbrei ding is een vereiste voor flexibele licenties en automatische patching. Klanten die zelf geïnstalleerde Vm's implementeren, moeten de SQL IaaS-extensie hand matig installeren. De SQL IaaS-extensie wordt niet ondersteund in Windows Server 2008.

> [!NOTE]
> Hoewel de SQL Server Blades **maken** en **beheren** met de SQL Server 2008 R2-installatie kopie in de Azure Portal worden gebruikt, worden de volgende functies _niet ondersteund_: automatische back-ups, Azure Key Vault integratie, R Services en opslag configuratie.

## <a name="licensing"></a>Licentieverlening
Betalen per gebruik SQL Server 2008 R2-implementaties kunnen worden omgezet in [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

Als u een op Software Assurance (SA) gebaseerde licentie wilt converteren naar betalen per gebruik, moeten klanten zich registreren bij de [SQL IaaS agent-extensie](sql-agent-extension-manually-register-single-vm.md). Na deze registratie is het SQL-licentie type onderling verwisselbaar tussen Azure Hybrid Benefit en betalen per gebruik.

Zelfgeïnstalleerde SQL Server 2008-of SQL Server 2008 R2-exemplaren op een virtuele Azure-machine kunnen worden geregistreerd bij de SQL IaaS agent-extensie en het licentie type te converteren naar betalen per gebruik.

## <a name="migration"></a>Migratie
U kunt met behulp van hand matige back-up-en herstel methoden EOS SQL Server-exemplaren migreren naar een Azure-VM. Dit is de meest voorkomende migratie methode van on-premises naar een Azure VM.

### <a name="azure-site-recovery"></a>Azure Site Recovery

Voor bulk migraties wordt de [Azure site Recovery](../../../site-recovery/site-recovery-overview.md) -service aanbevolen. Met Azure Site Recovery kunnen klanten de hele virtuele machine repliceren, met inbegrip van SQL Server van on-premises naar Azure VM.

SQL Server vereist toepassings consistente Azure Site Recovery moment opnamen om het herstel te garanderen. Azure Site Recovery ondersteunt app-consistente moment opnamen met een minimum interval van 1 uur. De minimale Recovery Point Objective (RPO) die mogelijk is voor SQL Server met Azure Site Recovery migraties is 1 uur. De beoogde herstel tijd (RTO) is 2 uur plus SQL Server herstel tijd.

### <a name="database-migration-service"></a>Database Migration Service

De [Azure database Migration service](../../../dms/dms-overview.md) is een optie voor klanten als ze worden gemigreerd van on-premises naar een virtuele Azure-machine door SQL Server te upgraden naar versie 2012 of hoger.

## <a name="disaster-recovery"></a>Herstel na noodgeval

Oplossingen voor nood herstel voor EOS SQL Server op een Azure VM zijn als volgt:

- **SQL Server back-ups**: gebruik Azure backup om uw EOS SQL Server 2008 en 2008 R2 te beschermen tegen Ransomware, onbedoeld verwijderen en beschadiging met een RPO van 15 minuten en herstel naar een bepaald tijdstip. Zie [dit artikel](../../../backup/sql-support-matrix.md#scenario-support)voor meer informatie.
- **Logboek verzending**: u kunt een back-upreplica van het logboek maken in een andere zone of Azure-regio met doorlopende herstel bewerkingen om de RTO te verminderen. U moet de verzen ding van het logboek hand matig configureren.
- **Azure site Recovery**: u kunt uw virtuele machine repliceren tussen zones en regio's via Azure site Recovery replicatie. SQL Server vereist app-consistente moment opnamen om herstel in het geval van een ramp te garanderen. Azure Site Recovery biedt een minimale RPO van 1 uur en een periode van twee uur (plus SQL Server herstel tijd) RTO voor EOS SQL Server herstel na nood gevallen.

## <a name="security-patching"></a>Beveiligings patches

Uitgebreide beveiligings updates voor SQL Server Vm's worden geleverd via de Microsoft Update kanalen nadat de SQL Server VM is geregistreerd met de [SQL IaaS agent-extensie](sql-agent-extension-manually-register-single-vm.md). Patches kunnen hand matig of automatisch worden gedownload.

*Automatisch patchen* is standaard ingeschakeld. Met automatisch patchen kan Azure automatisch een patch uitvoeren voor SQL Server en het besturingssysteem. U kunt een dag van de week, tijd en duur van een onderhouds venster opgeven als de SQL Server IaaS-extensie is geïnstalleerd. Azure voert de patch uit tijdens deze onderhoudssessie. Het onderhoudsschema maakt voor de tijd gebruik van de landinstellingen van de virtuele machine. Zie voor meer informatie [automatische patching voor SQL Server op Azure virtual machines](automated-patching.md).


## <a name="next-steps"></a>Volgende stappen

Uw SQL Server VM migreren naar Azure:

* [Een SQL Server-database naar een SQL Server in een virtuele machine van Azure migreren](migrate-to-vm-from-sql-server.md)

Aan de slag met SQL Server in Azure Virtual Machines:

* [Create a SQL Server VM in the Azure portal](sql-vm-create-portal-quickstart.md) (Een SQL Server-VM maken in Azure Portal)

Vind antwoorden op veelgestelde vragen over VM's met SQL Server:

* [Veelgestelde vragen over SQL Server op Azure Virtual Machines](frequently-asked-questions-faq.md)

Meer informatie over het einde van de ondersteunings opties en uitgebreide beveiligings updates:

* [Einde van ondersteuning](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)  &  [Uitgebreide beveiligings updates](/sql/sql-server/end-of-support/sql-server-extended-security-updates)
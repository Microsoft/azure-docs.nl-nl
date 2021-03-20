---
title: Overzicht van SQL Server op virtuele Linux-machines in Azure | Microsoft Docs
description: Meer informatie over het uitvoeren van volledige SQL Server-edities op virtuele Linux-machines in Azure. U vindt hier rechtstreekse koppelingen naar alle installatiekopieën voor virtuele Linux-machines met SQL Server en gerelateerde inhoud.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: overview
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c394fe2aa7639d32e5d79bcb22a01151f7666f5b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96324615"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Overzicht van SQL Server op virtuele machines in Azure (Linux)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](sql-server-on-linux-vm-what-is-iaas-overview.md)

Met SQL Server op Azure Virtual Machines kunt u volledige versies van SQL Server in de cloud gebruiken zonder dat u on-premises hardware hoeft te beheren. SQL Server-VM's vereenvoudigen ook de licentiekosten als u betaalt naar gebruik.

Virtuele machines van Azure worden uitgevoerd in talloze verschillende [geografische regio's](https://azure.microsoft.com/regions/) ter wereld. Er zijn ook diverse [formaten](../../../virtual-machines/sizes.md). In de galerie met installatiekopieën voor virtuele machines kunt u een SQL Server-VM met de juiste versie, de juiste editie en het juiste besturingssysteem maken. Daarom zijn virtuele machines een goede optie voor diverse verschillende SQL Server-werkbelastingen. 

Als u geen ervaring hebt met Azure SQL, bekijk dan de video *Overzicht van SQL Server op Azure VM* uit onze diepgaande [Azure SQL-videoserie](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/SQL-Server-on-Azure-VM-Overview-4-of-61/player]

## <a name="get-started-with-sql-server-vms"></a><a id="create"></a>Aan de slag met VM's voor SQL Server

Als u aan de slag wilt, kiest u een installatiekopie voor een virtuele machine voor SQL Server met de vereiste versie, de vereiste editie en het vereiste besturingssysteem. De volgende gedeelten bevatten directe koppelingen naar Azure Portal voor de galerie met installatiekopieën van virtuele SQL Server-machines.

> [!TIP]
> Zie [de prijzenpagina voor virtuele Linux-machines met SQL Server](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) voor informatie over de prijzen voor SQL Server-installatiekopieën.

| Versie | Besturingssysteem | Editie |
| --- | --- | --- |
| **SQL Server 2019** | Ubuntu 18.04 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804sqldev-ARM) | 
| **SQL Server 2019** | Red Hat Enterprise Linux (RHEL) 8 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8sqldev-ARM)|
| **SQL Server 2019** | SUSE Linux Enterprise Server (SLES) v12 SP5 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5sqldev-ARM)|
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Zie [Overzicht van SQL Server op virtuele Azure-machines (Windows)](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md) voor de beschikbare installatiekopieën van SQL Server voor virtuele Windows-machines.

## <a name="installed-packages"></a><a id="packages"></a> Geïnstalleerde pakketten

Wanneer u SQL Server op Linux configureert, installeert u het database-engine-pakket en vervolgens verschillende optionele pakketten, afhankelijk van uw vereisten. De installatiekopieën van SQL Server voor de virtuele Linux-machines installeren de meeste pakketten automatisch voor u. De volgende tabel laat zien welke pakketten zijn geïnstalleerd voor elke distributie.

| Distributie | [Database Engine](/sql/linux/sql-server-linux-setup) | [Hulpprogramma's](/sql/linux/sql-server-linux-setup-tools) | [SQL Server-agent](/sql/linux/sql-server-linux-setup-sql-agent) | [Zoekopdracht in volledige tekst](/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](/sql/linux/sql-server-linux-setup-ssis) | [HA-invoegtoepassing](/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![RHEL en database-engine](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL en hulpprogramma's](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL en SQL Server-agent](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL en zoeken in volledige tekst](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL en SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL en HA-invoegtoepassing](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |
| SLES | ![SLES en database-engine](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES en hulpprogramma's](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES en SQL Server-agent](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES en zoeken in volledige tekst](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES en SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) | ![SLES en HA-invoegtoepassing](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png)|
| Ubuntu | ![Ubuntu en database-engine](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu en hulpprogramma's](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu en SQL Server-agent](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu en zoeken in volledige tekst](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu en SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu en HA-invoegtoepassing](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |

## <a name="related-products-and-services"></a>Gerelateerde producten en services

### <a name="linux-virtual-machines"></a>Virtuele Linux-machines

* [Overzicht van Azure Virtual Machines](../../../virtual-machines/linux/overview.md)

### <a name="storage"></a>Storage

* [Inleiding tot Microsoft Azure Storage](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Netwerken

* [Overzicht van Virtual Network](../../../virtual-network/virtual-networks-overview.md)
* [IP-adressen in Azure](../../../virtual-network/public-ip-addresses.md)
* [Een Fully Qualified Domain Name maken in Azure Portal](../../../virtual-machines/create-fqdn.md)

### <a name="sql"></a>SQL

* [Documentatie voor SQL Server op Linux](/sql/linux)
* [Vergelijking met Azure SQL Database](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>Volgende stappen

Aan de slag met SQL Server op virtuele Linux-machines:

* [Create a SQL Server VM in the Azure portal](sql-vm-create-portal-quickstart.md) (Een SQL Server-VM maken in Azure Portal)

Vind antwoorden op veelgestelde vragen over virtuele SQL Server-machines op Linux:

* [Veelgestelde vragen over SQL Server op virtuele machines van Azure](frequently-asked-questions-faq.md)
---
title: Overzicht van SQL Server op virtuele Windows-machines in Azure | Microsoft Docs
description: Lees hoe u volledige versies van SQL Server kunt uitvoeren in Azure Virtual Machines in de cloud zonder dat u on-premises hardware hoeft te beheren.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/27/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c6d03c896242d02e147e880a99eb9f1e7bb15da8
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326859"
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>Wat is SQL Server in Azure Virtual Machines (Windows)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)

Met [SQL Server in Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/) kunt u volledige versies van SQL Server in de cloud gebruiken zonder dat u on-premises hardware hoeft te beheren. Virtuele machines (VM's) van SQL Server vereenvoudigen ook de licentiekosten als u betaalt naar gebruik.

Virtuele machines van Azure worden uitgevoerd in talloze verschillende [geografische regio's](https://azure.microsoft.com/regions/) ter wereld. Er zijn ook diverse [formaten](../../../virtual-machines/sizes.md). In de galerie met installatiekopieën voor virtuele machines kunt u een SQL Server-VM met de juiste versie, de juiste editie en het juiste besturingssysteem maken. Daarom zijn virtuele machines een goede optie voor allerlei verschillende SQL Server-werkbelastingen.

Als u geen ervaring hebt met SQL Server op Azure-VM's, bekijk dan de video *Overzicht van SQL Server op Azure VM* uit onze diepgaande [Azure SQL-videoserie](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/SQL-Server-on-Azure-VM-Overview-4-of-61/player]

## <a name="automated-updates"></a>Automatische updates

SQL Server in Azure Virtual Machines kan [geautomatiseerde patching](automated-patching.md) gebruiken om een onderhoudsvenster te plannen voor het automatisch installeren van belangrijke Windows- en SQL Server-updates.

## <a name="automated-backups"></a>Automatische back-ups

SQL Server in Azure Virtual Machines kan gebruikmaken van [geautomatiseerde back-ups](automated-backup.md), waarbij er regelmatig back-ups in Blob Storage worden gemaakt van uw database. U kunt deze techniek ook handmatig gebruiken. Zie [Azure Storage gebruiken voor SQL Server-back-up en -herstel](azure-storage-sql-server-backup-restore-use.md) voor meer informatie.

Azure biedt ook een geavanceerde back-upoplossing voor SQL Server op Azure-VM's. De volledig beheerde back-upoplossing van Azure biedt ondersteuning voor AlwaysOn-beschikbaarheidsgroepen, retentie op lange termijn, herstel naar een bepaald tijdstip, en centraal beheer en centrale bewaking. Zie [Azure Backup voor SQL Server op Azure-VM's](../../../backup/backup-azure-sql-database.md) voor meer informatie.
  

## <a name="high-availability"></a>Hoge beschikbaarheid

Als u hoge beschikbaarheid nodig hebt, overweeg dan SQL Server-beschikbaarheidsgroepen te configureren. Er worden dan meerdere exemplaren van SQL Server ingericht in Azure Virtual Machines in een virtueel netwerk. U kunt uw oplossing voor hoge beschikbaarheid configureren of sjablonen in Azure Portal gebruiken voor automatische configuratie. Zie [Hoge beschikbaarheid en herstel na een noodgeval voor SQL Server in Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md) voor een overzicht van alle opties voor hoge beschikbaarheid.

## <a name="performance"></a>Prestaties

Virtuele Azure-machines bieden verschillende groottes om te voldoen aan diverse workloadeisen. VM's met SQL Server bieden bovendien een geautomatiseerde opslagconfiguratie die is geoptimaliseerd voor uw prestatievereisten. Zie [Opslagconfiguratie voor SQL Server-VM's](storage-configuration.md) voor meer informatie over het configureren van opslag voor VM's met SQL Server. Zie [Best practices voor prestaties voor SQL Server op virtuele machines van Azure](performance-guidelines-best-practices.md) om de prestaties af te stemmen.

## <a name="get-started-with-sql-server-vms"></a>Aan de slag met VM's voor SQL Server

Als u aan de slag wilt, kiest u een installatiekopie voor een virtuele machine voor SQL Server met de vereiste versie, de vereiste editie en het vereiste besturingssysteem. De volgende gedeelten bevatten directe koppelingen naar Azure Portal voor de galerie met installatiekopieën van virtuele SQL Server-machines.

> [!TIP]
> Zie [Richtlijnen voor prijzen van SQL Server in Azure Virtual Machines](pricing-guidance.md) voor informatie over de prijzen voor SQL Server-installatiekopieën. 

### <a name="pay-as-you-go"></a><a id="payasyougo"></a> Betalen per gebruik
De volgende tabel bevat een matrix met SQL Server-installatiekopieën voor betalen naar gebruik.

| Versie | Besturingssysteem | Editie |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019enterprise), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019standard), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019web), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019sqldev) | 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

Zie [Overzicht van SQL Server op virtuele Azure-machines (Linux)](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md) voor de beschikbare installatiekopieën van SQL Server voor virtuele Linux-machines.

> [!NOTE]
> Het is nu mogelijk om de licentie voor een SQL Server-VM waarbij per gebruik wordt betaald, te wijzigen in uw eigen licentie. Raadpleeg [Het licentiemodel voor een SQL Server-VM wijzigen](licensing-model-azure-hybrid-benefit-ahb-change.md) voor meer informatie. 

### <a name="bring-your-own-license"></a><a id="BYOL"></a> Neem uw eigen licentie mee
U kunt ook uw eigen licentie gebruiken (Bring Your Own Licence, BYOL). In dit scenario betaalt u alleen voor de virtuele machine, zonder eventuele extra kosten voor SQL Server-licentieverlening.  Als u uw eigen licentie hebt, kan dit u in de loop van de tijd geld besparen voor doorlopende productieworkloads. Zie [Prijsinformatie voor Azure VM's van SQL Server](pricing-guidance.md#byol) voor de vereisten om van deze optie gebruik te maken.

Als u uw eigen licentie wilt meenemen, converteert u een SQL Server-VM waarbij per gebruik wordt betaald, of implementeert u een installatiekopie met **{BYOL}** vooraf ingesteld. Zie [Het licentiemodel voor een SQL Server-VM wijzigen](licensing-model-azure-hybrid-benefit-ahb-change.md) voor meer informatie over het schakelen van uw licentiemodel tussen betalen per gebruik en BYOL. 

| Versie | Besturingssysteem | Editie |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolenterprise), [Standard BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolstandard)| 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4StandardWindowsServer2012R2) |

Het is mogelijk een oudere installatiekopie van SQL Server die niet beschikbaar is in de Azure-portal te implementeren met behulp van PowerShell. Gebruik de volgende opdracht om alle beschikbare installatiekopieën te bekijken met behulp van PowerShell:

  ```powershell
  Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
  ```

Raadpleeg voor meer informatie over het implementeren van SQL Server-VM's met behulp van PowerShell [Over het inrichten van SQL Server-machines met Azure PowerShell](create-sql-vm-powershell.md).


### <a name="connect-to-the-vm"></a>Verbinding maken met de virtuele machine
Nadat u de SQL Server-VM hebt gemaakt, verbindt u deze met toepassingen of hulpprogramma's, zoals SQL Server Management Studio (SSMS). Zie [Verbinding maken met een virtuele SQL Server-machine op Azure](ways-to-connect-to-sql.md) voor instructies.

### <a name="migrate-your-data"></a>Uw gegevens migreren
Als u een bestaande database hebt, doet u er verstandig aan deze te verplaatsen naar uw nieuw ingerichte virtuele SQL Server-machine. Zie [Een database migreren naar SQL Server op een virtuele machine in Azure](migrate-to-vm-from-sql-server.md) voor een lijst met migratieopties en begeleiding bij de migratie.

## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>Azure SQL-resources maken en beheren met de Azure-portal

De Azure-portal biedt één pagina waar u [al uw Azure SQL-resources](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Sql%2Fazuresql) kunt beheren, met inbegrip van uw virtuele SQL-machines.

Om toegang te krijgen tot de pagina **Azure SQL-resources**, selecteert u **Azure SQL** in het menu van de Azure-portal of zoekt en selecteert u **Azure SQL** vanaf een willekeurige pagina.

![Zoeken naar Azure SQL](./media/sql-server-on-azure-vm-iaas-what-is-overview/search-for-azure-sql.png)

> [!NOTE]
> Azure SQL biedt een snelle en eenvoudige manier om toegang te krijgen tot al uw databases, elastische pools, logische servers, beheerde instanties en virtuele machines van Azure SQL. Azure SQL is geen service of resource. 

Als u bestaande resources wilt beheren, selecteert u het gewenste item in de lijst. Als u nieuwe Azure SQL-resources wilt maken, selecteert u **+ Toevoegen**. 

![Azure SQL-resource maken](./media/sql-server-on-azure-vm-iaas-what-is-overview/create-azure-sql-resource.png)

Nadat u **+ Toevoegen** hebt geselecteerd, kunt u aanvullende informatie over de verschillende opties bekijken door **Details weergeven** te selecteren op een tegel.

![Details van tegel met database](./media/sql-server-on-azure-vm-iaas-what-is-overview/sql-vm-details.png)

Zie deze artikelen voor meer informatie:

- [Een individuele database maken](../../database/single-database-create-quickstart.md)
- [Een elastische pool maken](../../database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [Een beheerd exemplaar maken](../../managed-instance/instance-create-quickstart.md)
- [Een virtuele SQL Server-machine maken](sql-vm-create-portal-quickstart.md)

## <a name="sql-server-vm-image-refresh-policy"></a><a id="lifecycle"></a> Vernieuwingsbeleid voor installatiekopieën van SQL Server-VM
Azure onderhoudt slechts één installatiekopie van een virtuele machine voor elk ondersteunde combinatie van besturingssysteem, versie en editie. Dit betekent dat installatiekopieën na verloop van tijd worden vernieuwd en dat oudere installatiekopieën worden verwijderd. Zie de sectie **Installatiekopieën** in de [veelgestelde vragen over virtuele machines met SQL Server](frequently-asked-questions-faq.md#images) voor meer informatie.

## <a name="customer-experience-improvement-program-ceip"></a>Programma voor kwaliteitsverbetering (CEIP)
Het programma voor kwaliteitsverbetering (CEIP) is standaard ingeschakeld. Met dit programma worden regelmatig rapporten naar Microsoft verzonden zodat SQL Server kan worden verbeterd. Er hoeft voor het programma voor kwaliteitsverbetering geen beheertaak worden uitgevoerd, tenzij u het na het inrichten wilt uitschakelen. U kunt het programma voor kwaliteitsverbetering aanpassen of uitschakelen door via Extern bureaublad verbinding te maken met de virtuele machine. Voer vervolgens het hulpprogramma **SQL Server-fouten- en gebruiksrapportage** uit. Volg de instructies voor het uitschakelen van rapportage. Zie de [privacyverklaring van SQL Server](/sql/sql-server/sql-server-privacy) voor meer informatie over gegevensverzameling.

## <a name="related-products-and-services"></a>Gerelateerde producten en services
### <a name="windows-virtual-machines"></a>Virtuele Windows-machines
* [Overzicht van Azure Virtual Machines](../../../virtual-machines/windows/overview.md)

### <a name="storage"></a>Storage
* [Inleiding tot Microsoft Azure Storage](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Netwerken
* [Overzicht van Virtual Network](../../../virtual-network/virtual-networks-overview.md)
* [IP-adressen in Azure](../../../virtual-network/public-ip-addresses.md)
* [Een Fully Qualified Domain Name maken in Azure Portal](../../../virtual-machines/create-fqdn.md)

### <a name="sql"></a>SQL
* [Documentatie over SQL Server](/sql/index)
* [Vergelijking met Azure SQL Database](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>Volgende stappen

Aan de slag met SQL Server in Azure Virtual Machines:

* [Create a SQL Server VM in the Azure portal](sql-vm-create-portal-quickstart.md) (Een SQL Server-VM maken in Azure Portal)

Vind antwoorden op veelgestelde vragen over VM's met SQL Server:

* [Veelgestelde vragen over SQL Server op virtuele machines van Azure](frequently-asked-questions-faq.md)

Referentiearchitecturen bekijken voor het uitvoeren van N-tier-toepassingen in SQL Server in IaaS

* [Windows N-tier-toepassing in Azure met SQL Server](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [N-tier-toepassing uitvoeren in meerdere Azure-regio's voor hoge beschikbaarheid](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
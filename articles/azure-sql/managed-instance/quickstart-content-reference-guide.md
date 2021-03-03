---
title: Referentiemateriaal om aan de slag te gaan
titleSuffix: Azure SQL Managed Instance
description: 'Referentiemateriaal voor inhoud om u te helpen aan de slag te gaan met Azure SQL Managed Instance. '
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 07/11/2019
ms.openlocfilehash: c84c81fa81cf4ba9be454eea5782927a09c3252a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690954"
---
# <a name="getting-started-with-azure-sql-managed-instance"></a>Aan de slag met Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Met [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) wordt een database gemaakt die vrijwel 100% compatibel is met de meest recente (Enterprise Edition) SQL Server-database-engine. Het biedt een systeemeigen implementatie van een [virtueel netwerk (VNet)](../../virtual-network/virtual-networks-overview.md) die veelvoorkomende beveiligingskwesties aanpakt, en een [bedrijfsmodel](https://azure.microsoft.com/pricing/details/sql-database/) dat gunstig is voor klanten die al SQL Server hebben.

In dit artikel vindt u verwijzingen naar inhoud waarmee u kunt leren u hoe u snel een SQL Managed Instance kunt configureren en maken en uw databases kunt migreren.

## <a name="quickstart-overview"></a>Overzicht van quickstarts

Met de volgende quickstarts kunt u snel een SQL Managed Instance maken, een virtuele machine of punt-naar-site-VPN-verbinding voor clienttoepassingen configureren en een database herstellen voor uw nieuwe SQL Managed Instance met behulp van een `.bak`-bestand.

### <a name="configure-environment"></a>Omgeving configureren

Als eerste stap moet u uw eerste SQL Managed Instance maken met de netwerkomgeving waarin het wordt geplaatst en verbinding inschakelen vanaf de computer of virtuele machine waarop u query's voor SQL Managed Instance uitvoert. U kunt de volgende handleidingen gebruiken:

- [Create a SQL Managed Instance using the Azure portal](instance-create-quickstart.md) (een SQL Managed Instance maken met behulp van de Azure-portal). In de Azure-portal configureert u de noodzakelijke parameters (gebruikersnaam/wachtwoord, aantal kernen en maximale hoeveelheid opslagruimte), en maakt u automatisch een Azure-netwerkomgeving zonder de netwerkdetails en infrastructuurvereisten te hoeven kennen. Zorg ervoor dat u een [abonnementstype](resource-limits.md#supported-subscription-types) hebt waarmee momenteel een SQL Managed Instance mag worden gemaakt. Als u uw eigen netwerk wilt gebruiken of het netwerk wilt aanpassen, raadpleegt u [configure an existing virtual network for Azure SQL Managed Instance](vnet-existing-add-subnet.md) (Een bestaand virtueel netwerk voor Azure SQL Managed Instance configureren) of [create a virtual network for Azure SQL Database Managed Instance](virtual-network-subnet-create-arm-template.md) (Een virtueel netwerk voor Azure SQL Managed Instance maken).
- Een SQL Managed Instance wordt gemaakt in een eigen VNet zonder openbaar eindpunt. Voor toegang tot clienttoepassingen kunt u met behulp van een van deze quickstarts **een VM maken in hetzelfde VNet (ander subnet)** of **vanaf uw clientcomputer een punt-naar-site-VPN-verbinding maken naar het VNET**:
  - Schakel [openbaar eindpunt](public-endpoint-configure.md) in voor uw SQL Managed Instance, zodat u vanuit uw omgeving direct toegang hebt tot uw gegevens.
  - Maak een [Azure Virtual Machine in het VNet van SQL Managed Instance](connect-vm-instance-configure.md) voor connectiviteit van de clienttoepassing, waaronder SQL Server Management Studio.
  - Stel [punt-naar-site-VPN-verbinding voor uw SQL Managed Instance](point-to-site-p2s-configure.md) in vanaf uw clientcomputer met SQL Server Management Studio en andere clienttoepassingen voor connectiviteit. Dit is de andere van twee opties voor connectiviteit met uw SQL Managed Instance en het VNet ervan.

  > [!NOTE]
  > - U kunt ook een ExpressRoute- of site-naar-site-verbinding van uw lokale netwerk gebruiken. Deze methoden worden echter niet besproken in deze quickstarts.
  > - Als u de retentieperiode van 0 (onbeperkte retentie) wijzigt in een andere waarde, moet u er rekening mee houden dat retentie alleen van toepassing is op logboeken die zijn geschreven nadat de waarde is gewijzigd, (logboeken die zijn geschreven tijdens de periode waarin de retentie was ingesteld op onbeperkt, blijven behouden, zelfs nadat retentie is ingeschakeld).

Als alternatief voor het handmatig maken van SQL Managed Instance kunt u [PowerShell](scripts/create-configure-managed-instance-powershell.md), [PowerShell met een Resource Manager-sjabloon](./create-template-quickstart.md) of [Azure CLI](/cli/azure/sql/mi#az-sql-mi-create) gebruiken om dit proces te scripten en automatiseren.

### <a name="migrate-your-databases"></a>Uw databases migreren

Nadat u een SQL Managed Instance hebt gemaakt en toegang hebt geconfigureerd, kunt u uw SQL Server-databases gaan migreren. De migratie kan mislukken als de brondatabase die u wilt migreren niet-ondersteunde functies bevat. Om fouten te voorkomen en de compatibiliteit te controleren, kunt u [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) gebruiken om uw. databases op SQL Server te analyseren op eventuele problemen die de migratie naar een SQL Managed Instance zouden kunnen blokkeren, zoals de aanwezigheid van [FileStream](/sql/relational-databases/blob/filestream-sql-server)-bestanden of meerdere logboekbestanden. Als u deze problemen oplost, zijn uw databases klaar om naar SQL Managed Instance te worden gemigreerd. [Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview) is een ander handig hulpmiddel dat uw werkbelasting op SQL Server kan vastleggen en deze opnieuw kan afspelen op een SQL Managed Instance, zodat u kunt bepalen of er zich prestatieproblemen zullen voordoen als u migreert naar een SQL Managed Instance.

Als u zeker bent dat u uw database naar een SQL Managed Instance kunt migreren, kunt u de systeemeigen opslagmogelijkheden van SQL Server gebruiken om vanuit een `.bak`-bestand een database terug te zetten in een SQL Managed Instance. U kunt deze methode gebruiken voor het migreren van databases vanuit een SQL Server-database-engine die on-premises of op virtuele machines in Azure is geïnstalleerd. Zie [Een database terugzetten naar een SQL Managed Instance](restore-sample-database-quickstart.md) voor een quickstart. In deze quickstart voert u met de Transact-SQL-opdracht `RESTORE` een terugzetting uit vanuit een `.bak`-bestand dat is opgeslagen in Azure Blob-opslag.

> [!TIP]
> Zie [Back-up van SQL Server naar URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) als u de Transact-SQL-opdracht `BACKUP` wilt gebruiken om een back-up te maken van uw database in Azure Blob-opslag.

Met deze quickstarts kunt u snel back-ups van databases configureren, maken en op een SQL Managed Instance terugzetten. In sommige scenario's moet u de implementatie van SQL Managed Instance en de vereiste netwerkomgeving aanpassen of automatiseren. Deze scenario's worden hieronder beschreven.

## <a name="customize-network-environment"></a>Netwerkomgeving aanpassen

Hoewel het VNet/subnet automatisch kan worden geconfigureerd met [de Azure-portal](instance-create-quickstart.md) wanneer het exemplaar wordt gemaakt, kan het een goed idee zijn om het te maken voordat u begint instanties te maken in SQL Managed Instance, zodat u de parameters van het VNet en subnet kunt configureren. De eenvoudigste manier om de netwerkomgeving te maken en configureren is om de sjabloon voor de [implementatie van Azure-resources](virtual-network-subnet-create-arm-template.md) te gebruiken. Deze sjabloon maakt en configureert het netwerk en subnet waarde instantie wordt geplaatst. U hoeft slechts de implementatieknop voor Azure Resource Manager in te drukken en het formulier met parameters in te vullen.

Als alternatief kunt u ook dit [PowerShell-script](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) gebruiken om het netwerk automatisch te laten maken.

Als u al een VNet en subnet hebt waar u uw SQL Managed Instance wilt implementeren, moet u ervoor zorgen dat uw VNet en subnet aan de [netwerkvereisten](connectivity-architecture-overview.md#network-requirements) voldoen. Gebruik dit [PowerShell-script om te controleren of uw subnet correct is geconfigureerd](vnet-existing-add-subnet.md). Dit script valideert het netwerk en rapporteert problemen zodat u weet wat er moet worden gewijzigd, en biedt aan deze noodzakelijke wijzigingen aan uw VNet/subnet uit te voeren. Voer dit script uit als u uw VNet/subnet niet handmatig wilt configureren. U kunt het ook uitvoeren als uw netwerkinfrastructuur een belangrijke herconfiguratie heeft ondergaan. Als u uw eigen netwerk wilt maken en configureren, lees dan [connectiviteitsarchitectuur](connectivity-architecture-overview.md) en de [ultieme handleiding voor het maken en configureren van een omgeving voor SQL Managed Instance](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="migrate-to-a-sql-managed-instance"></a>Migreren naar een SQL Managed Instance

Met de eerder genoemde quickstarts kunt u snel een SQL Managed Instance instellen en uw databases verplaatsen met de systeemeigen `RESTORE`-functie. Dit is een goed uitgangspunt als u snel concepten wilt testen en wilt controleren of uw oplossing werkt op Managed Instance.

Als u echter uw productiedatabase of zelfs databases voor ontwikkelen/testen wilt migreren die u wilt gebruiken voor een prestatietest, moet u overwegen om extra technieken te gebruiken, zoals:

- Prestatietest: meet de metrische gegevens van de basisprestaties van uw SQL Server-broninstantie en vergelijk deze met de prestatiegegevens van de SQL Managed Instance waar u de database naartoe hebt gemigreerd. Lees meer over de [aanbevolen procedures voor het vergelijken van prestaties](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).
- Onlinemigratie: bij de systeemeigen `RESTORE` (beschreven in dit artikel) moet u echter wachten totdat de databases zijn hersteld (en gekopieerd naar Azure Blob-opslag als dat nog niet is gebeurd). Dit heeft downtime voor uw toepassing ten gevolge, met name voor grote databases. Als u de productiedatabase wilt verplaatsen, gebruikt u de [Database Migration Service (DMS)](../../dms/tutorial-sql-server-to-managed-instance.md?toc=%2fazure%2fsql-database%2ftoc.json) om uw database met minimale downtime te migreren. Dit wordt bereikt doordat DMS de aan de brondatabase aangebrachte wijzigingen stapsgewijs pusht naar de SQL Managed Instance-database die wordt teruggezet. Op deze manier kunt u snel uw toepassing van de bron- naar de doeldatabase overzetten met minimale downtime.

Meer informatie over het [aanbevolen migratieproces](migrate-to-instance-from-sql-server.md).

## <a name="next-steps"></a>Volgende stappen

- Vind [hier een lijst met ondersteunde functies in SQL Managed Instance](../database/features-comparison.md) en [hier details en bekende problemen](transact-sql-tsql-differences-sql-server.md).
- Meer informatie over de [technische kenmerken van SQL Managed Instance](resource-limits.md#service-tier-characteristics).
- U vindt meer uitgebreide informatie in [SQL Managed Instance gebruiken](how-to-content-reference-guide.md).
- [Identificeer de juiste Azure SQL Managed Instance-SKU voor uw on-premises database](/sql/dma/dma-sku-recommend-sql-db/).
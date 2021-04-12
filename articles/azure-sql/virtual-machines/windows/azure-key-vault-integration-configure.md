---
title: Key Vault integreren met SQL Server op Windows-Vm's in azure (Resource Manager) | Microsoft Docs
description: Meer informatie over het automatiseren van de configuratie van SQL Server versleuteling voor gebruik met Azure Key Vault. In dit onderwerp wordt uitgelegd hoe u Azure Key Vault integratie kunt gebruiken met virtuele SQL-machines die zijn gemaakt met Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.subservice: security
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: a6955b7fc4948faaea6db426545f8cc3d1eece35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359894"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-resource-manager"></a>Azure Key Vault integratie configureren voor SQL Server op virtuele machines van Azure (Resource Manager)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Er zijn meerdere SQL Server versleutelings functies, zoals [transparante gegevens versleuteling (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption), [versleuteling op kolom niveau (CLE)](/sql/t-sql/functions/cryptographic-functions-transact-sql)en [back-upcodering](/sql/relational-databases/backup-restore/backup-encryption). Voor deze coderings vormen moet u de cryptografische sleutels die u voor versleuteling gebruikt, beheren en opslaan. De Azure Key Vault-service is ontworpen voor het verbeteren van de beveiliging en het beheer van deze sleutels op een veilige en Maxi maal beschik bare locatie. Met de [SQL Server-connector](https://www.microsoft.com/download/details.aspx?id=45344) kan SQL Server deze sleutels vanuit Azure Key Vault gebruiken.

Als u SQL Server on-premises uitvoert, zijn er stappen die u kunt volgen om [toegang te krijgen tot Azure Key Vault van uw on-premises SQL Server exemplaar](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server). Voor SQL Server op virtuele Azure-machines kunt u echter tijd besparen met behulp van de *Azure Key Vault Integration* -functie.

Als deze functie is ingeschakeld, wordt de SQL Server-connector automatisch geïnstalleerd, wordt de EKM-provider geconfigureerd voor toegang tot Azure Key Vault en wordt de referentie gemaakt waarmee u toegang krijgt tot uw kluis. Als u de stappen in de eerder genoemde on-premises documentatie hebt bekeken, kunt u zien dat deze functie stap 2 en 3 automatiseert. Het enige wat u nog steeds hand matig moet doen, is door de sleutel kluis en sleutels te maken. Van daaruit wordt de volledige configuratie van uw SQL Server VM geautomatiseerd. Zodra deze functie is voltooid, kunt u Transact-SQL (T-SQL)-instructies uitvoeren om te beginnen met het versleutelen van uw data bases of back-ups zoals u dat gewend bent.

[!INCLUDE [Prepare for Key Vault integration](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > De EKM-provider versie (Extensible Key Management) 1.0.4.0 wordt geïnstalleerd op de SQL Server virtuele machine via de [IaaS-uitbrei ding (SQL Infrastructure as a Service)](./sql-server-iaas-agent-extension-automate-management.md). Bij het bijwerken van de SQL IaaS-extensie wordt de provider versie niet bijgewerkt. Overweeg, indien nodig, hand matig een upgrade van de versie van de EKM-provider (bijvoorbeeld bij het migreren naar een beheerd exemplaar van SQL).


## <a name="enabling-and-configuring-key-vault-integration"></a>Key Vault-integratie inschakelen en configureren
U kunt Key Vault integratie tijdens het inrichten inschakelen of configureren voor bestaande virtuele machines.

### <a name="new-vms"></a>Nieuwe Vm's
Als u een nieuwe virtuele SQL-machine met Resource Manager inricht, biedt de Azure Portal een manier om Azure Key Vault integratie in te scha kelen. De functie Azure Key Vault is alleen beschikbaar voor de edities Enter prise, Developer en Evaluation van SQL Server.

![Integratie van Azure Sleutelkluis in SQL](./media/azure-key-vault-integration-configure/azure-sql-arm-akv.png)

Voor een gedetailleerd overzicht van het inrichten raadpleegt u [een virtuele SQL-machine inrichten in de Azure Portal](create-sql-vm-portal.md).

### <a name="existing-vms"></a>Bestaande Vm's

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Voor bestaande virtuele SQL-machines opent u de [resource virtuele SQL-machines](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) en selecteert u **beveiliging** onder **instellingen**. Selecteer **inschakelen** om Azure Key Vault integratie in te scha kelen. 

![Integratie van SQL Key Vault voor bestaande Vm's](./media/azure-key-vault-integration-configure/azure-sql-rm-akv-existing-vms.png)

Wanneer u klaar bent, selecteert u de knop **Toep assen** aan de onderkant van de pagina **beveiliging** om uw wijzigingen op te slaan.

> [!NOTE]
> De referentie naam die we hier maken, wordt later aan een SQL-aanmelding toegewezen. Hiermee kan de SQL-aanmelding toegang krijgen tot de sleutel kluis. 


> [!NOTE]
> U kunt Key Vault integratie ook configureren met behulp van een sjabloon. Zie Azure Quick Start- [sjabloon voor Azure Key Vault-integratie](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update)voor meer informatie.


[!INCLUDE [Key Vault integration next steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]
---
title: Wat is de SQL Server Agent-extensie voor IaaS?
description: In dit artikel wordt beschreven hoe u met de SQL Server IaaS-agent extensie beheer taken van SQL Server op Azure-Vm's kunt automatiseren. Dit zijn onder andere functies zoals automatische back-ups, automatische patching, Azure Key Vault Integration, licentie beheer, opslag configuratie en Centraal beheer van alle SQL Server VM-exemplaren.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: fdff3f6144f7099f3f61cfe57186357e17136e9f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103225486"
---
# <a name="automate-management-with-the-sql-server-iaas-agent-extension"></a>Beheer automatiseren met de uitbrei ding IaaS agent van SQL Server
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


De SQL Server IaaS agent extension (SqlIaasExtension) wordt uitgevoerd op SQL Server op Azure Virtual Machines (Vm's) om beheer-en beheer taken te automatiseren. 

Dit artikel bevat een overzicht van de uitbrei ding. Zie de artikelen voor [automatische installatie](sql-agent-extension-automatic-registration-all-vms.md), [enkelvoudige vm's](sql-agent-extension-manually-register-single-vm.md)of [vm's in bulk](sql-agent-extension-manually-register-vms-bulk.md)om de SQL Server IaaS-uitbrei ding te installeren op SQL Server op virtuele machines van Azure. 

## <a name="overview"></a>Overzicht

De SQL Server IaaS agent-extensie biedt integratie met de Azure Portal en is afhankelijk van de beheer modus, ontgrendelt een aantal functie voordelen voor SQL Server op Azure-Vm's: 

- **Functie voordelen**: de uitbrei ding ontgrendelt een aantal voor delen van automatiserings functies, zoals portal beheer, licentie flexibiliteit, automatische back-ups, automatische patching en meer. Zie de voor [delen van functies](#feature-benefits) verderop in dit artikel voor meer informatie. 

- **Naleving**: de uitbrei ding biedt een vereenvoudigde methode voor het voldoen aan de vereiste om aan micro soft te melden dat de Azure Hybrid Benefit is ingeschakeld, zoals is opgegeven in de product termen. Dit proces voor komt dat het nodig is om licentie registratie formulieren voor elke resource te beheren.  

- **Gratis**: de uitbrei ding in alle drie de beheer baarheids modi is volledig gratis. Er zijn geen extra kosten verbonden aan de uitbrei ding of het wijzigen van de beheer modus. 

- **Vereenvoudigd licentie beheer**: de uitbrei ding vereenvoudigt het SQL Server licentie beheer en biedt u de mogelijkheid om SQL Server vm's snel te identificeren met de Azure Hybrid Benefit die is ingeschakeld met behulp van de [Azure Portal](manage-sql-vm-portal.md), Power shell of de Azure cli: 

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

   ```azurecli-interactive
   $ az sql vm list --query "[?sqlServerLicenseType=='AHUB']"
   ```
   ---


> [!IMPORTANT]
> Met de SQL IaaS agent-extensie worden gegevens verzameld voor het snelle doel om klanten optionele voor delen te geven bij het gebruik van SQL Server binnen Azure Virtual Machines. Micro soft gebruikt deze gegevens niet voor licentie controles zonder voorafgaande toestemming van de klant. Raadpleeg de [SQL Server-aanvulling voor privacy](/sql/sql-server/sql-server-privacy#non-personal-data) voor meer informatie.


## <a name="feature-benefits"></a>Functie voordelen 

Met de SQL Server IaaS agent-extensie wordt een aantal functie voordelen voor het beheren van uw SQL Server virtuele machine ontgrendeld. 

De volgende tabel bevat een overzicht van deze voor delen: 


| Functie | Beschrijving |
| --- | --- |
| **Portalbeheer** | Hiermee ontgrendelt u [het beheer in de portal](manage-sql-vm-portal.md), zodat u al uw SQL Server vm's op één plek kunt bekijken, zodat u SQL-specifieke functies rechtstreeks vanuit de portal kunt inschakelen en uitschakelen. <br/> Beheer modus: Lightweight & Full|  
| **Automatische back-up** |Automatiseert het plannen van back-ups voor alle data bases voor het standaard exemplaar of een [correct geïnstalleerd](frequently-asked-questions-faq.md#administration) benoemd exemplaar van SQL Server op de virtuele machine. Zie [automatische back-up voor SQL Server in azure virtual machines (Resource Manager)](automated-backup-sql-2014.md)voor meer informatie. <br/> Beheer modus: volledig|
| **Automatische patching** |Hiermee configureert u een onderhouds venster waarin belang rijke Windows-en SQL Server beveiligings updates voor uw virtuele machine kunnen worden uitgevoerd, zodat u updates tijdens piek tijden voor uw werk belasting kunt voor komen. Zie voor meer informatie [automatische patching voor SQL Server in azure virtual machines (Resource Manager)](automated-patching.md). <br/> Beheer modus: volledig|
| **Integratie van Azure Key Vault** |Hiermee kunt u Azure Key Vault automatisch installeren en configureren op uw SQL Server-VM. Zie [Azure Key Vault-integratie configureren voor SQL Server op Azure virtual machines (Resource Manager)](azure-key-vault-integration-configure.md)voor meer informatie. <br/> Beheer modus: volledig|
| **Schijf gebruik in de portal weer geven** | Met kunt u een grafische weer gave bekijken van het schijf gebruik van uw SQL-gegevens bestanden in de Azure Portal.  <br/> Beheer modus: volledig | 
| **Flexibele licentie verlening** | Bespaar op kosten door [naadloos te overstappen](licensing-model-azure-hybrid-benefit-ahb-change.md) van uw eigen licentie (ook wel bekend als de Azure Hybrid Benefit) naar het licentie model voor betalen per gebruik en weer terug. <br/> Beheer modus: Lightweight & Full| 
| **Flexibele versie/editie** | Als u besluit de [versie](change-sql-server-version.md) of [editie](change-sql-server-edition.md) van SQL Server te wijzigen, kunt u de meta gegevens in de Azure Portal bijwerken zonder dat u de hele SQL Server VM opnieuw hoeft te implementeren.  <br/> Beheer modus: Lightweight & Full| 


## <a name="management-modes"></a>Beheer modi

U kunt ervoor kiezen om uw SQL IaaS-extensie te registreren in drie beheer modi: 

- Met de **licht** modus worden binaire extensie bestanden naar de virtuele machine gekopieerd, maar wordt de agent niet geïnstalleerd en wordt de SQL Server-service niet opnieuw opgestart. De licht gewicht modus ondersteunt alleen het wijzigen van het licentie type en de versie van SQL Server en biedt beperkte Portal beheer. Gebruik deze optie voor SQL Server Vm's met meerdere exemplaren, of die deel uitmaken van een FCI (failover cluster instance). De licht modus is de standaard beheer modus wanneer u de functie voor [automatische registratie](sql-agent-extension-automatic-registration-all-vms.md) gebruikt, of wanneer een beheer type niet is opgegeven tijdens hand matige registratie. Er is geen invloed op geheugen of CPU wanneer u de Lightweight-modus gebruikt, en er zijn geen kosten verbonden. Het is raadzaam om eerst uw SQL Server-VM in de Lightweight-modus te registreren en vervolgens tijdens het geplande onderhouds venster naar de volledige modus te upgraden. 

- In de **volledige** modus wordt de SQL IaaS-agent op de VM geïnstalleerd om alle functies te kunnen leveren, maar moeten de SQL Server service-en systeem beheerders machtigingen opnieuw worden gestart. Gebruik dit voor het beheren van een SQL Server virtuele machine met één exemplaar. In de modus volledig worden twee Windows-Services met een minimale impact op geheugen en CPU geïnstalleerd. deze kunnen worden bewaakt via taak beheer. Er zijn geen kosten verbonden aan het gebruik van de volledige beheer modus. 

- De modus voor exclusieve **agents** is toegewezen aan SQL Server 2008 en SQL Server 2008 R2 geïnstalleerd op Windows Server 2008. Er is geen invloed op geheugen of CPU wanneer u de modus geen agent gebruikt. Er zijn geen kosten verbonden aan het gebruik van de beheer baarheids modus van de agent. de SQL Server wordt niet opnieuw gestart en er is geen agent geïnstalleerd op de VM. 

U kunt de huidige modus van uw SQL Server IaaS-agent weer geven met behulp van Azure PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```


## <a name="installation"></a>Installatie

Registreer uw SQL Server-VM met de SQL Server IaaS agent-extensie om de **virtuele SQL-machine** _in uw_ abonnement te maken. Dit is een _afzonderlijke_ resource van de bron van de virtuele machine. Als u de registratie van uw SQL Server-VM ongedaan maakt vanuit de extensie, wordt de _resource_ van de **virtuele SQL-machine** verwijderd, maar wordt de werkelijke virtuele machine niet weggehaald.

Als u een installatie kopie van een Azure-SQL Server VM implementeert via de Azure Portal wordt de virtuele machine van SQL Server automatisch geregistreerd met de extensie. Als u echter zelf SQL Server wilt installeren op een virtuele machine van Azure of als u een virtuele machine van Azure wilt inrichten vanaf een aangepaste VHD, moet u uw SQL Server VM registreren bij de SQL IaaS-extensie om functie voordelen te ontgrendelen. 

Als u de uitbrei ding registreert in de Lightweight-modus, worden de binaire bestanden gekopieerd, maar wordt de agent niet op de VM geïnstalleerd. De agent wordt geïnstalleerd op de VM wanneer de extensie wordt bijgewerkt naar de volledige beheer modus. 

Er zijn drie manieren om u te registreren bij de extensie: 
- [Automatisch voor alle huidige en toekomstige Vm's in een abonnement](sql-agent-extension-automatic-registration-all-vms.md)
- [Hand matig voor één VM](sql-agent-extension-manually-register-single-vm.md)
- [Hand matig voor meerdere Vm's tegelijk](sql-agent-extension-manually-register-vms-bulk.md)

### <a name="named-instance-support"></a>Ondersteuning voor benoemde instanties

De uitbrei ding van de SQL Server IaaS-agent werkt met een benoemd exemplaar van SQL Server als dit de enige SQL Server instantie is die beschikbaar is op de virtuele machine. De extensie kan niet worden geïnstalleerd op virtuele machines met meerdere benoemde SQL Server exemplaren als er geen standaard exemplaar op de VM is. 

Als u een benoemd exemplaar van SQL Server wilt gebruiken, implementeert u een virtuele machine van Azure, installeert u een enkele benoemde SQL Server instantie en registreert u deze met de [SQL IaaS-extensie](sql-agent-extension-manually-register-single-vm.md).

U kunt ook de volgende stappen uitvoeren als u een benoemd exemplaar wilt gebruiken met een Azure Marketplace SQL Server-installatie kopie: 

   1. Implementeer een SQL Server-VM vanuit Azure Marketplace. 
   1. [Hef de registratie](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) van de SQL Server-VM op uit de extensie van de SQL IaaS-agent. 
   1. Verwijder SQL Server volledig in de SQL Server VM.
   1. Installeer SQL Server met een benoemd exemplaar binnen de SQL Server VM. 
   1. [Registreer de virtuele machine bij de SQL IaaS agent-extensie](sql-agent-extension-manually-register-single-vm.md#register-with-extension). 

## <a name="verify-status-of-extension"></a>De status van de uitbrei ding controleren

Gebruik de Azure Portal of Azure PowerShell om de status van de uitbrei ding te controleren. 

### <a name="azure-portal"></a>Azure Portal

Controleer of de uitbrei ding is geïnstalleerd in de Azure Portal. 

Selecteer **alle instellingen** in het deel venster virtuele machine en selecteer vervolgens **uitbrei dingen**. De uitbrei ding **SqlIaasExtension** wordt weer gegeven.

![Status van de uitbrei ding van de SQL Server IaaS-agent in de Azure Portal](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)


### <a name="azure-powershell"></a>Azure PowerShell

U kunt ook de cmdlet **Get-AzVMSqlServerExtension** Azure PowerShell gebruiken:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Met de vorige opdracht wordt bevestigd dat de agent is geïnstalleerd en algemene status informatie bevat. U krijgt specifieke status informatie over automatische back-ups en patching met behulp van de volgende opdrachten:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```


## <a name="limitations"></a>Beperkingen

De SQL IaaS agent-extensie ondersteunt alleen: 

- SQL Server Vm's die via de Azure Resource Manager zijn geïmplementeerd. SQL Server Vm's die via het klassieke model zijn geïmplementeerd, worden niet ondersteund. 
- SQL Server Vm's die op de open bare of Azure Government Cloud zijn geïmplementeerd. Implementaties naar andere privé-of overheids Clouds worden niet ondersteund. 


## <a name="in-region-data-residency"></a>Gegevenslocatie in uw regio
De virtuele machine van Azure SQL en de SQL IaaS agent-extensie verplaatsen of slaan klant gegevens niet uit de regio waarin ze zijn geïmplementeerd.

## <a name="next-steps"></a>Volgende stappen

Zie de artikelen voor [automatische installatie](sql-agent-extension-automatic-registration-all-vms.md), [enkelvoudige vm's](sql-agent-extension-manually-register-single-vm.md)of [vm's in bulk](sql-agent-extension-manually-register-vms-bulk.md)om de SQL Server IaaS-uitbrei ding te installeren op SQL Server op virtuele machines van Azure.

Zie voor meer informatie over het uitvoeren van SQL Server op Azure Virtual Machines de [Wat is SQL Server op azure virtual machines?](sql-server-on-azure-vm-iaas-what-is-overview.md).

Zie [Veelgestelde vragen](frequently-asked-questions-faq.md)voor meer informatie. 

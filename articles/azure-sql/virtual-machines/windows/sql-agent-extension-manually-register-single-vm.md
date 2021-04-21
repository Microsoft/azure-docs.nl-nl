---
title: Registreren met SQL IaaS-agentextensie
description: Registreer uw virtuele Azure SQL Server-machine met de SQL IaaS Agent-extensie om functies in te stellen voor virtuele SQL Server-machines die buiten Azure Marketplace zijn geïmplementeerd, evenals naleving en verbeterde beheerbaarheid.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell, contperf-fy21q2
ms.openlocfilehash: e34876c76259b8274e0b0ef9059659802eb55cf1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765445"
---
# <a name="register-sql-server-vm-with-sql-iaas-agent-extension"></a>Een virtuele SQL Server registreren met de SQL IaaS-agentextensie
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Registreer uw SQL Server-VM met de [SQL IaaS Agent-extensie](sql-server-iaas-agent-extension-automate-management.md) om een schat aan functievoordelen te ontgrendelen voor uw SQL Server op Azure VM. 

In dit artikel leert u hoe u één virtuele SQL Server registreren met de SQL IaaS Agent-extensie. U kunt ook alle virtuele SQL Server of [](sql-agent-extension-automatic-registration-all-vms.md) meerdere [VM's die bulksgewijs zijn gescript registreren.](sql-agent-extension-manually-register-vms-bulk.md)


## <a name="overview"></a>Overzicht

Als u zich registreert bij [SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md) Agent-extensie, wordt de **sql-resource** voor de virtuele _machine_ binnen uw abonnement gemaakt. Dit is een afzonderlijke _resource_ van de virtuele-machineresource. Als u de registratie van SQL Server VM bij de extensie ongedaan maakt, wordt de _resource_ van de virtuele **SQL-machine** verwijderd, maar wordt de daadwerkelijke virtuele machine niet verwijderd.

Als u een virtuele SQL Server-Azure Marketplace implementeert via de Azure Portal registreert u automatisch de SQL Server-VM met de extensie. Als u er echter voor kiest om SQL Server zelf te installeren op een virtuele Azure-machine of als u een virtuele Azure-machine wilt inrichten vanaf een aangepaste VHD, moet u uw SQL Server-VM registreren bij de SQL IaaS Agent-extensie om volledige voordelen en beheerbaarheid van functies te ontgrendelen. 

Als u de SQL IaaS Agent-extensie wilt gebruiken, moet u uw abonnement eerst registreren bij de [ **provider Microsoft.SqlVirtualMachine,**](#register-subscription-with-resource-provider)waarmee de SQL IaaS-extensie resources binnen dat specifieke abonnement kan maken.

> [!IMPORTANT]
> De SQL IaaS Agent-extensie verzamelt gegevens om klanten optionele voordelen te bieden bij het gebruik van SQL Server binnen Azure Virtual Machines. Microsoft gebruikt deze gegevens niet voor licentiecontroles zonder de vooraf toestemming van de klant. Zie het [SQL Server privacy supplement](/sql/sql-server/sql-server-privacy#non-personal-data) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Als u uw virtuele SQL Server met de extensie wilt registreren, hebt u het volgende nodig: 

- Een [Azure-abonnement](https://azure.microsoft.com/free/).
- Een virtuele machine van [Azure Resource Model met Windows Server 2008 (of hoger)](../../../virtual-machines/windows/quick-create-portal.md) met SQL Server [2008 (of hoger)](https://www.microsoft.com/sql-server/sql-server-downloads) geïmplementeerd in de openbare cloud of Azure Government cloud. 
- De nieuwste versie van [Azure CLI](/cli/azure/install-azure-cli) of [Azure PowerShell (minimaal 5.0).](/powershell/azure/install-az-ps) 


## <a name="register-subscription-with-resource-provider"></a>Abonnement registreren bij resourceprovider

Als u uw virtuele SQL Server wilt registreren met de SQL IaaS Agent-extensie, moet u uw abonnement eerst registreren bij de resourceprovider **Microsoft.SqlVirtualMachine.** Dit biedt de SQL IaaS Agent-extensie de mogelijkheid om resources binnen uw abonnement te maken.  U kunt dit doen met behulp van de Azure Portal, de Azure CLI of Azure PowerShell.

### <a name="azure-portal"></a>Azure Portal

1. Open de Azure Portal en ga naar **Alle services.** 
1. Ga naar **Abonnementen en** selecteer het abonnement van belang.  
1. Ga op de pagina Abonnementen naar **extensies.**  
1. Voer **sql** in het filter in om de SQL-extensies weer te brengen. 
1. Selecteer **Registreren,** **Registreren opnieuw** of Registratie **ongedaan** maken voor de provider  **Microsoft.SqlVirtualMachine,** afhankelijk van de gewenste actie. 

   ![De provider wijzigen](./media/sql-agent-extension-manually-register-single-vm/select-resource-provider-sql.png)


### <a name="command-line"></a>Opdrachtregel

Registreer uw Azure-abonnement bij de **provider Microsoft.SqlVirtualMachine** met behulp van Azure CLI of Azure PowerShell. 

# <a name="azure-cli"></a>[Azure-CLI](#tab/bash)

```azurecli-interactive
# Register the SQL IaaS Agent extension to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL IaaS Agent extension to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-with-extension"></a>Registreren met extensie

Er zijn drie beheermodi voor de SQL Server [IaaS Agent-extensie](sql-server-iaas-agent-extension-automate-management.md#management-modes). 

Als u de extensie registreert in de modus volledig beheer, wordt de SQL Server-service opnieuw opgestart. Daarom is het raadzaam om de extensie eerst in de lichtgewicht modus te registreren en vervolgens tijdens een onderhoudsvenster een [upgrade](#upgrade-to-full) naar volledig uit te voeren. 

### <a name="lightweight-management-mode"></a>Lichtgewicht beheermodus

Gebruik de Azure CLI of Azure PowerShell om uw virtuele SQL Server te registreren bij de extensie in de lichtgewicht modus. Hiermee wordt de SQL Server service niet opnieuw gestart. U kunt vervolgens op elk moment upgraden naar de volledige modus, maar als u dit doet, wordt de SQL Server-service opnieuw opgestart. Het wordt daarom aanbevolen om te wachten tot een gepland onderhoudsvenster. 

Geef het SQL Server licentietype op als betalen per gebruik ( ) om per gebruik te betalen, Azure Hybrid Benefit ( ) om uw eigen licentie te gebruiken of herstel na noodherstel ( ) om de gratis `PAYG` `AHUB` `DR` [dr-replicalicentie](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)te activeren.

Exemplaren van failovercluster en implementaties met meerdere exemplaren kunnen alleen worden geregistreerd bij de SQL IaaS Agent-extensie in de lichtgewicht modus. 

# <a name="azure-cli"></a>[Azure-CLI](#tab/bash)

Registreer een SQL Server-VM in de lichtgewicht modus met de Azure CLI: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type <license_type> 
  ```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Registreer een SQL Server-VM in de lichtgewicht modus met Azure PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license_type>  -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Modus volledig beheer

Als u uw virtuele SQL Server in de volledige modus registreert, wordt de SQL Server gestart. Ga voorzichtig verder. 

Als u uw virtuele SQL Server wilt registreren in de volledige modus (en mogelijk de SQL Server-service opnieuw wilt opstarten), gebruikt u de volgende Azure PowerShell opdracht: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Beheermodus NoAgent 

SQL Server 2008 en 2008 R2 geïnstalleerd op Windows Server 2008 (niet _R2_) kunnen worden geregistreerd met de SQL IaaS Agent-extensie in de [modus NoAgent.](sql-server-iaas-agent-extension-automate-management.md#management-modes) Deze optie garandeert naleving en zorgt ervoor dat SQL Server VM in de Azure Portal met beperkte functionaliteit kan worden bewaakt.


Geef voor **het licentietype** het volgende op: `AHUB` , of `PAYG` `DR` . Geef voor **de aanbieding voor de afbeelding** een of `SQL2008-WS2008` op `SQL2008R2-WS2008`

Als u uw SQL Server 2008 ( ) of 2008 R2 ( ) wilt registreren op `SQL2008-WS2008` het Windows Server 2008-exemplaar, gebruikt u het volgende Azure CLI- of `SQL2008R2-WS2008` Azure PowerShell-codefragment: 


# <a name="azure-cli"></a>[Azure-CLI](#tab/bash)

Registreer uw SQL Server machine in de modus NoAgent met de Azure CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type <license type>  --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer <image offer> 
 ```
 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)


Registreer uw SQL Server-machine in de modus NoAgent met Azure PowerShell: 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license type> -SqlManagementType NoAgent -Sku Standard -Offer <image offer>
  ```

---

## <a name="verify-mode"></a>Modus Verifiëren

U kunt de huidige modus van uw IaaS SQL Server agent weergeven met behulp van Azure PowerShell: 

```powershell-interactive
# Get the SqlVirtualMachine
$sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
$sqlvm.SqlManagementType
```

## <a name="upgrade-to-full"></a>Upgraden naar volledig  

SQL Server-VM's die de extensie *in* de  lichtgewicht modus hebben geregistreerd, kunnen upgraden naar volledig met behulp van de Azure Portal, de Azure CLI of Azure PowerShell. SQL Server-VM's in _de modus NoAgent_ kunnen worden bijgewerkt naar volledig _nadat_ het besturingssysteem is bijgewerkt naar Windows 2008 R2 en hoger. Het is niet mogelijk om te downgraden. [](#unregister-from-extension) Als u dit wilt doen, moet u de registratie van de virtuele SQL Server van de SQL IaaS-agentextensie ongedaan maken. Als u dit doet, wordt de **resource van de virtuele SQL-machine** _verwijderd,_ maar wordt de daadwerkelijke virtuele machine niet verwijderd. 

> [!NOTE]
> Wanneer u de beheermodus voor de SQL IaaS-extensie naar volledig bijstart, wordt de SQL Server gestart. In sommige gevallen kan het opnieuw opstarten ertoe leiden dat de service-principal-namen (SPN's) die zijn gekoppeld aan de SQL Server-service worden gewijzigd in het verkeerde gebruikersaccount. Als u verbindingsproblemen hebt na het upgraden van de beheermodus naar volledig, maakt u de registratie van uw [SPN's ongedaan en](/sql/database-engine/configure-windows/register-a-service-principal-name-for-kerberos-connections)maakt u de registratie opnieuw.


### <a name="azure-portal"></a>Azure Portal

Als u de extensie wilt upgraden naar de volledige modus met behulp Azure Portal, volgt u deze stappen: 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Ga naar de [resource van uw virtuele SQL-machines.](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) 
1. Selecteer uw SQL Server-VM en selecteer **Overzicht.** 
1. Voor SQL Server VM's met de modus NoAgent of lightweight IaaS selecteert u het bericht Alleen licentietype en editie-updates zijn beschikbaar met de **SQL IaaS-extensie.**

   ![Selecties voor het wijzigen van de modus vanuit de portal](./media/sql-agent-extension-manually-register-single-vm/change-sql-iaas-mode-portal.png)

1. Schakel het selectievakje Ik ga akkoord om de **SQL Server-service** op  de virtuele machine opnieuw op te starten in en selecteer vervolgens Bevestigen om uw IaaS-modus bij te werken naar volledig. 

    ![Selectievakje voor akkoord gaan met het opnieuw starten van SQL Server service op de virtuele machine](./media/sql-agent-extension-manually-register-single-vm/enable-full-mode-iaas.png)

### <a name="command-line"></a>Opdrachtregel

# <a name="azure-cli"></a>[Azure-CLI](#tab/bash)

Voer het volgende Azure CLI-codefragment uit om de extensie bij te werken naar de volledige modus:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Als u de extensie wilt upgraden naar de volledige modus, moet u het volgende Azure PowerShell codefragment:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Registratiestatus controleren
U kunt controleren of uw SQL Server-VM al is geregistreerd bij de SQL IaaS Agent-extensie met behulp van de Azure Portal, de Azure CLI of Azure PowerShell. 

### <a name="azure-portal"></a>Azure Portal 

Volg deze stappen om de registratiestatus Azure Portal te controleren: 

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 
1. Ga naar uw [SQL Server VM's](manage-sql-vm-portal.md).
1. Selecteer uw SQL Server-VM in de lijst. Als uw SQL Server-VM hier niet wordt vermeld, is deze waarschijnlijk niet geregistreerd bij de SQL IaaS Agent-extensie. 
1. Bekijk de waarde onder **Status**. Als  Status **Geslaagd** is, is de virtuele SQL Server is geregistreerd bij de SQL IaaS Agent-extensie. 

   ![Status controleren met SQL RP-registratie](./media/sql-agent-extension-manually-register-single-vm/verify-registration-status.png)

### <a name="command-line"></a>Opdrachtregel

Controleer de huidige SQL Server VM-registratie met behulp van Azure CLI of Azure PowerShell. `ProvisioningState` geeft weer `Succeeded` of de registratie is geslaagd. 

# <a name="azure-cli"></a>[Azure-CLI](#tab/bash)

Voer het volgende codefragment uit om de registratiestatus te controleren met behulp van de Azure CLI:  

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Voer het volgende codefragment uit om de registratiestatus Azure PowerShell te controleren:

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Er wordt een foutbericht weergegeven dat SQL Server VM niet is geregistreerd bij de extensie. 


## <a name="unregister-from-extension"></a>Registratie bij extensie ongedaan maken

Als u de registratie van uw virtuele SQL Server met de SQL IaaS Agent-extensie ongedaan wilt maken, verwijdert u de resource van de virtuele *SQL-machine* met behulp van de Azure Portal of Azure CLI. Als u de virtuele *SQL-machineresource verwijdert,* wordt de virtuele machine SQL Server verwijderd. Wees echter voorzichtig en volg de stappen zorgvuldig omdat het mogelijk is om per ongeluk de virtuele machine te verwijderen wanneer u probeert de resource te *verwijderen.* 

Het ongedaan maken van de registratie van de virtuele SQL-machine met de SQL IaaS Agent-extensie is nodig om de beheermodus van volledig te downgraden. 

### <a name="azure-portal"></a>Azure Portal

Als u de registratie van SQL Server VM bij de extensie ongedaan wilt maken met behulp van Azure Portal, volgt u deze stappen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Navigeer naar de SQL VM-resource. 
  
   ![Resource voor virtuele SQL-machines](./media/sql-agent-extension-manually-register-single-vm/sql-vm-manage.png)

1. Selecteer **Verwijderen**. 

   ![Verwijderen selecteren in de bovenste navigatiebalk](./media/sql-agent-extension-manually-register-single-vm/delete-sql-vm-resource.png)

1. Typ de naam van de virtuele SQL-machine en **vink het selectievakje naast de virtuele machine uit.**

   ![Schakel de VM uit om te voorkomen dat de daadwerkelijke virtuele machine wordt verwijderd en selecteer vervolgens Verwijderen om door te gaan met het verwijderen van de SQL-VM-resource](./media/sql-agent-extension-manually-register-single-vm/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Als het selectievakje naast de naam van de virtuele machine niet wordt gewist, wordt *de* virtuele machine volledig verwijderd. Verwijder het selectievakje om de registratie van de virtuele SQL Server de extensie ongedaan te maken, maar *verwijder niet de daadwerkelijke virtuele machine*. 

1. Selecteer **Verwijderen om** te bevestigen dat de virtuele SQL-machineresource is verwijderd en niet de SQL Server virtuele machine.  

### <a name="command-line"></a>Opdrachtregel

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de opdracht [az sql vm delete](/cli/azure/sql/vm#az_sql_vm_delete) SQL Server de registratie van uw VM bij de extensie met Azure CLI ongedaan te maken. Hiermee verwijdert u de SQL Server *VM-resource,* maar wordt de virtuele machine niet verwijderd. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik de opdracht [Remove-AzSqlVM](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm)om de registratie van SQL Server virtuele Azure PowerShell-VM bij de extensie ongedaan te maken. Hiermee verwijdert u de SQL Server *VM-resource,* maar wordt de virtuele machine niet verwijderd. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---


## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server op een Windows-VM](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Veelgestelde vragen SQL Server een Windows-VM](frequently-asked-questions-faq.md)  
* [Prijsinformatie voor SQL Server een Windows-VM](pricing-guidance.md)
* [Releasenotities voor SQL Server op een Windows-VM](../../database/doc-changes-updates-release-notes.md)

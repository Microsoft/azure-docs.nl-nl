---
title: Het licentie model voor een SQL-VM in azure wijzigen
description: Meer informatie over het scha kelen tussen licenties voor een SQL Server virtuele machine in azure van betalen per gebruik naar uw eigen licentie met behulp van de Azure Hybrid Benefit.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.subservice: management
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ea274914ac70540d5763884bbd01e41e349a530d
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879827"
---
# <a name="change-the-license-model-for-a-sql-virtual-machine-in-azure"></a>Het licentiemodel voor een virtuele SQL-machine in Azure wijzigen
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


In dit artikel wordt beschreven hoe u het licentie model voor een SQL Server virtuele machine (VM) in azure wijzigt met behulp van de [SQL IaaS agent-extensie](./sql-server-iaas-agent-extension-automate-management.md).

## <a name="overview"></a>Overzicht

Er zijn drie licentie modellen voor een Azure-VM die als host fungeert voor SQL Server: betalen naar gebruik, Azure Hybrid Benefit (AHB) en hoge Beschik baarheid/herstel na nood geval (HA/DR). U kunt het licentie model van uw SQL Server-VM wijzigen met behulp van de Azure Portal, de Azure CLI of Power shell. 

- Het **betalen naar gebruik** -model betekent dat de kosten per seconde van het uitvoeren van de Azure-VM de kosten van de SQL Server licentie bevatten.
- Met [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) kunt u uw eigen SQL Server licentie gebruiken met een VM waarop SQL Server wordt uitgevoerd. 
- Het licentie type **ha/Dr** wordt gebruikt voor de [gratis ha/Dr-replica](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) in Azure. 

Azure Hybrid Benefit staat het gebruik toe van SQL Server licenties met Software Assurance (' gekwalificeerde licentie ') op virtuele machines van Azure. Met Azure Hybrid Benefit worden klanten niet in rekening gebracht voor het gebruik van een SQL Server licentie op een virtuele machine. Ze moeten echter wel betalen voor de kosten van de onderliggende Cloud Compute (dat wil zeggen, het basis tarief), opslag en back-ups. Ze moeten ook betalen voor I/O die is gekoppeld aan het gebruik van de Services (indien van toepassing).

Volgens de micro soft- [product termen](https://www.microsoft.com/licensing/terms/productoffering/MicrosoftAzureServices/EAEAS): ' klanten moeten aangeven dat ze Azure SQL database (beheerde instantie, Elastische pool en individuele database), Azure Data Factory, SQL Server Integration Services of SQL Server Virtual Machines onder Azure Hybrid Benefit voor SQL Server worden gebruikt bij het configureren van werk belastingen op Azure. "

Om het gebruik van Azure Hybrid Benefit voor SQL Server op Azure VM aan te geven en compatibel te zijn, hebt u drie opties:

- Richt een virtuele machine in met behulp van een SQL Server installatie kopie van uw eigen licentie vanuit Azure Marketplace. Deze optie is alleen beschikbaar voor klanten die een Enterprise Overeenkomst hebben.
- Richt een virtuele machine in met behulp van een betalen naar gebruik-SQL Server installatie kopie van Azure Marketplace en activeer de Azure Hybrid Benefit.
- Zelf SQL Server installeren op de virtuele machine van Azure, hand matig [registreren bij de extensie van de SQL IaaS-agent](sql-agent-extension-manually-register-single-vm.md)en Azure Hybrid Benefit activeren.

Het licentie type van SQL Server kan worden geconfigureerd wanneer de virtuele machine wordt ingericht of op elk gewenst moment. Als u overschakelt tussen licentie modellen, wordt geen downtime gestart, wordt de VM of de SQL Server-service niet opnieuw opgestart, worden er geen extra kosten in rekening gebracht en direct van kracht. Het activeren van Azure Hybrid Benefit *verlaagt* de kosten.

## <a name="prerequisites"></a>Vereisten

Het wijzigen van het licentie model van uw SQL Server virtuele machine heeft de volgende vereisten: 

- Een [Azure-abonnement](https://azure.microsoft.com/free/).
- Een [SQL Server virtuele machine](./create-sql-vm-portal.md) die is geregistreerd bij de [SQL IaaS agent-extensie](./sql-server-iaas-agent-extension-automate-management.md).
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) is een vereiste voor het gebruik van de [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/). 


## <a name="change-license-model"></a>Licentiemodel wijzigen

# <a name="azure-portal"></a>[Azure-portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

U kunt het licentie model rechtstreeks vanuit de portal wijzigen: 

1. Open de [Azure Portal](https://portal.azure.com) en open de [resource van de virtuele SQL-machines](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) voor uw SQL Server VM. 
1. Selecteer **configureren** onder **instellingen**. 
1. Selecteer de optie **Azure Hybrid Benefit** en schakel het selectie vakje in om te bevestigen dat u een SQL Server licentie hebt met Software Assurance. 
1. Selecteer **Toep assen** onder aan de pagina **configureren** . 

![Azure Hybrid Benefit in de portal](./media/licensing-model-azure-hybrid-benefit-ahb-change/ahb-in-portal.png)


# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

U kunt de Azure CLI gebruiken om uw licentie model te wijzigen.  

Geef de volgende waarden op voor het **licentie type**:
- `AHUB` voor de Azure Hybrid Benefit
- `PAYG` voor betalen naar gebruik
- `DR` de gratis HA/DR-replica activeren


```azurecli-interactive
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type <license-type>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

U kunt Power shell gebruiken om uw licentie model te wijzigen.

Geef de volgende waarden op voor het **licentie type**:
- `AHUB` voor de Azure Hybrid Benefit
- `PAYG` voor betalen naar gebruik
- `DR` de gratis HA/DR-replica activeren


```powershell-interactive
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType <license-type>
```

---

## <a name="remarks"></a>Opmerkingen

- Azure Cloud Solution Provider-klanten (CSP) kunnen de Azure Hybrid Benefit gebruiken door eerst een ' betalen per gebruik-VM ' te implementeren en deze vervolgens te converteren naar uw eigen licentie, als ze actieve Software Assurance hebben.
- Als u de resource van de virtuele SQL-machine verwijdert, gaat u terug naar de code voor de vastgelegde licentie van de installatie kopie. 
- De mogelijkheid om het licentie model te wijzigen, is een functie van de SQL IaaS agent-extensie. Als u een installatie kopie van Azure Marketplace implementeert via de Azure Portal registreert u automatisch een SQL Server virtuele machine met de extensie. Klanten die zelf een SQL Server installeren, moeten [hun SQL Server VM](sql-agent-extension-manually-register-single-vm.md)hand matig registreren. 
- Voor het toevoegen van een SQL Server-VM aan een beschikbaarheidsset moet de virtuele machine opnieuw worden gemaakt. Als zodanig worden Vm's die aan een beschikbaarheidsset zijn toegevoegd, teruggestuurd naar het standaard licentie type voor betalen naar gebruik. Azure Hybrid Benefit moet opnieuw worden ingeschakeld. 


## <a name="limitations"></a>Beperkingen

Het wijzigen van het licentie model is:
   - Alleen beschikbaar voor klanten met [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview).
   - Alleen ondersteund voor de Standard-en Enter prise-edities van SQL Server. Licentie wijzigingen voor Express, Web en Developer worden niet ondersteund. 
   - Alleen ondersteund voor virtuele machines die zijn geïmplementeerd via het Azure Resource Manager model. Virtuele machines die via het klassieke model zijn geïmplementeerd, worden niet ondersteund. 
   - Alleen beschikbaar voor de open bare of Azure Government Clouds. 
   - Alleen ondersteund op virtuele machines met één netwerk interface (NIC). 

> [!Note]
> Alleen SQL Server op basis van licenties met Software Assurance of abonnements licenties komen in aanmerking voor Azure Hybrid Benefit. Als u Server + CAL-licentie verlening gebruikt voor SQL Server en u Software Assurance hebt, kunt u uw eigen licentie gebruiken voor een installatie kopie van de virtuele machine van Azure SQL Server voor het gebruik van licentie mobiliteit voor deze servers, maar u kunt niet gebruikmaken van de andere functies van Azure Hybrid Benefit. 

## <a name="known-errors"></a>Bekende fouten

Bekijk de veelvoorkomende fouten en oplossingen. 

**De resource ' micro soft. SqlVirtualMachine/SqlVirtualMachines/ \<resource-group> ' onder de resource groep \<resource-group> is niet gevonden.**

Deze fout treedt op wanneer u probeert het licentie model op een SQL Server virtuele machine te wijzigen die niet is geregistreerd bij de SQL Server IaaS agent-extensie:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

U moet uw abonnement registreren bij de resource provider en vervolgens [uw SQL Server-VM registreren met de extensie van de SQL IaaS-agent](sql-agent-extension-manually-register-single-vm.md). 


**Aan de virtuele machine zijn \<vmname\> meer dan één NIC gekoppeld**

Deze fout doet zich voor op virtuele machines met meer dan één NIC. Verwijder een van de Nic's voordat u het licentie model wijzigt. Hoewel u de NIC opnieuw kunt toevoegen aan de VM nadat u het licentie model hebt gewijzigd, worden de bewerkingen in de Azure Portal, zoals automatische back-up en patching, niet meer ondersteund. 


## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server op een Windows-VM](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Veelgestelde vragen over SQL Server op een Windows-VM](frequently-asked-questions-faq.md)
* [Prijs informatie voor SQL Server op een Windows-VM](pricing-guidance.md)
* [Release opmerkingen voor SQL Server op een Windows-VM](../../database/doc-changes-updates-release-notes.md)
* [Overzicht van de SQL IaaS agent-extensie](./sql-server-iaas-agent-extension-automate-management.md)

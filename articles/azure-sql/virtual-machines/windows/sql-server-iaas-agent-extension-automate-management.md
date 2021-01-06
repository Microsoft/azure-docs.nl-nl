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
ms.openlocfilehash: 3fe87f94ce05efa4a784ba7e3f65e53abb00fd05
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97914243"
---
# <a name="automate-management-with-the-sql-server-iaas-agent-extension"></a>Beheer automatiseren met de uitbrei ding IaaS agent van SQL Server
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


De SQL Server IaaS agent extension (SqlIaasExtension) wordt uitgevoerd op SQL Server op Azure Virtual Machines (Vm's) om beheer-en beheer taken te automatiseren. 

Dit artikel bevat een overzicht van de uitbrei ding. Zie de artikelen voor [automatische installatie](sql-agent-extension-automatic-registration-all-vms.md), [enkelvoudige vm's](sql-agent-extension-manually-register-single-vm.md)of [vm's in bulk](sql-agent-extension-manually-register-vms-bulk.md)om de SQL Server IaaS-uitbrei ding te installeren op SQL Server op virtuele machines van Azure. 

## <a name="overview"></a>Overzicht

De SQL Server IaaS agent-extensie biedt een aantal voor delen voor SQL Server op Azure-Vm's: 

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
| **Portalbeheer** | Hiermee ontgrendelt u [het beheer in de portal](manage-sql-vm-portal.md), zodat u al uw SQL Server vm's op één plek kunt bekijken, zodat u SQL-specifieke functies rechtstreeks vanuit de portal kunt inschakelen en uitschakelen. 
| **Automatische back-up** |Automatiseert het plannen van back-ups voor alle data bases voor het standaard exemplaar of een [correct geïnstalleerd](frequently-asked-questions-faq.md#administration) benoemd exemplaar van SQL Server op de virtuele machine. Zie [automatische back-up voor SQL Server in azure virtual machines (Resource Manager)](automated-backup-sql-2014.md)voor meer informatie. |
| **Automatische patching** |Hiermee configureert u een onderhouds venster waarin belang rijke Windows-en SQL Server beveiligings updates voor uw virtuele machine kunnen worden uitgevoerd, zodat u updates tijdens piek tijden voor uw werk belasting kunt voor komen. Zie voor meer informatie [automatische patching voor SQL Server in azure virtual machines (Resource Manager)](automated-patching.md). |
| **Integratie van Azure Key Vault** |Hiermee kunt u Azure Key Vault automatisch installeren en configureren op uw SQL Server-VM. Zie [Azure Key Vault-integratie configureren voor SQL Server op Azure virtual machines (Resource Manager)](azure-key-vault-integration-configure.md)voor meer informatie. |
| **Flexibele licentie verlening** | Bespaar op kosten door [naadloos te overstappen](licensing-model-azure-hybrid-benefit-ahb-change.md) van uw eigen licentie (ook wel bekend als de Azure Hybrid Benefit) naar het licentie model voor betalen per gebruik en weer terug. | 
| **Flexibele versie/editie** | Als u besluit de [versie](change-sql-server-version.md) of [editie](change-sql-server-edition.md) van SQL Server te wijzigen, kunt u de meta gegevens in de Azure Portal bijwerken zonder dat u de hele SQL Server VM opnieuw hoeft te implementeren.  | 


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

De uitbrei ding van de SQL Server IaaS-agent werkt met een benoemd exemplaar van SQL Server als dit de enige SQL Server instantie is die beschikbaar is op de virtuele machine. De extensie kan niet worden geïnstalleerd op Vm's met meerdere exemplaren van SQL Server. 

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


## <a name="frequently-asked-questions"></a>Veelgestelde vragen 

**Moet ik mijn SQL Server VM die is ingericht van een SQL Server-installatie kopie registreren in azure Marketplace?**

Nee. Micro soft registreert automatisch Vm's die zijn ingericht op de SQL Server installatie kopieën in azure Marketplace. Registratie met de extensie is alleen vereist als de virtuele machine *niet* is ingericht op de SQL Server installatie kopieën in azure Marketplace en SQL Server zelf is geïnstalleerd.

**Is de SQL IaaS agent-extensie voor alle klanten beschikbaar?** 

Ja. Klanten moeten hun SQL Server Vm's registreren met de uitbrei ding als ze geen SQL Server installatie kopie van Azure Marketplace gebruiken en in plaats daarvan zelf een geïnstalleerd SQL Server of als ze hun aangepaste VHD hebben. Vm's die eigendom zijn van alle typen abonnementen (direct, Enterprise Agreement en Cloud Solution Provider) kunnen worden geregistreerd met de SQL IaaS agent-extensie.

**Wat is de standaard beheer modus bij het registreren met de SQL IaaS agent-extensie?**

De standaard beheer modus wanneer u zich registreert met de SQL IaaS agent-extensie is *licht gewicht*. Als de eigenschap SQL Server beheer niet is ingesteld als u zich registreert met de uitbrei ding, wordt de modus ingesteld als licht gewicht en wordt de SQL Server-service niet opnieuw opgestart. U kunt het beste eerst registreren bij de SQL IaaS agent-extensie in de Lightweight-modus en vervolgens een upgrade uitvoeren naar Full tijdens een onderhouds venster. Het standaard beheer is ook lichtgewicht tijd wanneer u de [functie voor automatische registratie](sql-agent-extension-automatic-registration-all-vms.md)gebruikt.

**Wat zijn de vereisten om u te registreren bij de SQL IaaS agent-extensie?**

Er zijn geen vereisten om te registreren bij de SQL IaaS agent-extensie, anders dan wanneer SQL Server op de virtuele machine is geïnstalleerd. Als de uitbrei ding van de SQL IaaS-agent is geïnstalleerd in de volledige modus, wordt de SQL Server-service opnieuw gestart, dus als u een onderhouds venster wordt aanbevolen, wordt dit gedaan.

**Registreert u bij de SQL IaaS agent-extensie een agent op mijn VM installeren?**

Ja, registreren met de SQL IaaS agent-extensie in de volledige beheer modus installeert een agent op de virtuele machine. Registratie in Lightweight of de modus voor niet-agent is niet vereist. 

Als u zich registreert met de SQL IaaS agent-extensie in de Lightweight-modus, worden alleen de *binaire bestanden* van de SQL IaaS-agent uitbreiding naar de virtuele machine gekopieerd. de agent wordt niet geïnstalleerd. Deze binaire bestanden worden vervolgens gebruikt om de agent te installeren wanneer de beheer modus wordt bijgewerkt naar Full.


**Wordt de registratie van de SQL IaaS agent-extensie opnieuw gestart SQL Server op mijn VM?**

Dit is afhankelijk van de modus die tijdens de registratie is opgegeven. Als de modus licht gewicht of niet-agent is opgegeven, wordt de SQL Server-service niet opnieuw opgestart. Als u echter de beheer modus volledig opgeeft, wordt de SQL Server-service opnieuw gestart. De functie voor automatische registratie registreert uw SQL Server Vm's in de Lightweight-modus, tenzij de Windows Server-versie 2008 is, in dat geval de SQL Server virtuele machine wordt geregistreerd in de modus niet-agent. 

**Wat is het verschil tussen de Lightweight en agent beheer modi bij het registreren met de SQL IaaS agent-extensie?** 

Beheer modus voor niet-agent is de enige beschik bare beheer modus voor SQL Server 2008 en SQL Server 2008 R2 op Windows Server 2008. Voor alle latere versies van Windows Server zijn de twee beschik bare beheer baarheids modi licht gewicht en volledig. 

Voor de modus voor niet-agents zijn SQL Server versie-en editie-eigenschappen vereist om door de klant te worden ingesteld. In de Lightweight-modus wordt een query uitgevoerd op de VM om de versie en editie van het SQL Server-exemplaar te vinden.

**Kan ik bij de SQL IaaS agent-extensie registreren zonder het SQL Server licentie type op te geven?**

Nee. Het SQL Server licentie type is geen optionele eigenschap wanneer u zich registreert met de SQL IaaS agent-extensie. U moet het SQL Server licentie type instellen op betalen naar gebruik of Azure Hybrid Benefit wanneer u zich registreert met de SQL IaaS agent-extensie in alle beheer modi (geen agent, licht gewicht en volledig). Als u een van de gratis versies van SQL Server hebt geïnstalleerd, zoals Developer of Evaluation Edition, moet u zich registreren bij licenties voor betalen naar gebruik. Azure Hybrid Benefit is alleen beschikbaar voor betaalde versies van SQL Server zoals Enter prise-en Standard-edities.

**Kan ik een upgrade uitvoeren van de SQL Server IaaS-uitbrei ding van de modus voor de agent naar de volledige modus?**

Nee. Het bijwerken van de beheer baarheids modus naar volledig of licht is niet beschikbaar voor de modus niet-agent. Dit is een technische beperking van Windows Server 2008. U moet eerst een upgrade uitvoeren van het besturings systeem naar Windows Server 2008 R2 of hoger, en vervolgens kunt u een upgrade uitvoeren naar de volledige beheer modus. 

**Kan ik de SQL Server IaaS-uitbrei ding van de Lightweight-modus naar de volledige modus bijwerken?**

Ja. Het bijwerken van de beheer baarheids modus van Lightweight naar Full wordt ondersteund via Azure PowerShell of de Azure Portal. Hiermee wordt de SQL Server-service opnieuw gestart.

**Kan ik de IaaS-uitbrei ding van de SQL Server verlagen van de volledige modus naar geen enkele agent of Lightweight-beheer modus?**

Nee. Het downgradeen van de SQL Server IaaS-extensie modus wordt niet ondersteund. De beheer baarheids modus kan niet worden gedowngraded van de volledige modus naar de modus licht gewicht of de niet-agent en kan niet worden gedowngraded van de modus Lightweight modus naar geen agent. 

Als u de beheer baarheids modus wilt wijzigen van de volledige beheer baarheid, verwijdert u de [registratie](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) van de SQL Server VM uit de uitbrei ding SQL IaaS agent door de _resource_ van de virtuele SQL-machine te verwijderen en de SQL Server VM opnieuw te registreren met de SQL IaaS agent-extensie opnieuw in een andere beheer modus.

**Kan ik registreren bij de SQL IaaS agent-extensie vanuit het Azure Portal?**

Nee. Registratie met de SQL IaaS agent-extensie is niet beschikbaar in de Azure Portal. Registratie met de SQL IaaS agent-extensie wordt alleen ondersteund met Azure CLI of Azure PowerShell. 

**Kan ik een VM registreren met de SQL IaaS agent-extensie voordat SQL Server is geïnstalleerd?**

Nee. Een virtuele machine moet ten minste één SQL Server-exemplaar (data base-engine) hebben om u te kunnen registreren bij de SQL IaaS agent-extensie. Als er geen SQL Server-exemplaar op de VM aanwezig is, heeft de nieuwe resource micro soft. SqlVirtualMachine de status mislukt.

**Kan ik een virtuele machine met de SQL IaaS agent-extensie registreren als er meerdere exemplaren van SQL Server zijn?**

Ja. Met de SQL IaaS agent-extensie wordt slechts één exemplaar van de SQL Server (data base-engine) geregistreerd. De SQL IaaS agent-extensie registreert de standaard SQL Server-instantie in het geval van meerdere exemplaren. Als er geen standaard exemplaar is, wordt alleen de registratie in Lightweight-modus ondersteund. Als u een upgrade wilt uitvoeren van Lightweight naar full managed-modus, moet het standaard SQL Server exemplaar bestaan of moet de virtuele machine slechts één benoemd SQL Server exemplaar hebben.

**Kan ik een SQL Server-failovercluster registreren met de SQL IaaS agent-extensie?**

Ja. SQL Server failover-cluster exemplaren op een virtuele machine van Azure kunnen worden geregistreerd met de SQL IaaS agent-extensie in de Lightweight-modus. SQL Server failover-cluster exemplaren kunnen echter niet worden bijgewerkt naar de volledige beheer modus.

**Kan ik mijn VM registreren met de SQL IaaS agent-extensie als er een AlwaysOn-beschikbaarheids groep is geconfigureerd?**

Ja. Er zijn geen beperkingen voor het registreren van een SQL Server exemplaar op een Azure-VM met de SQL IaaS agent-extensie als u deelneemt aan de configuratie van een AlwaysOn-beschikbaarheids groep.

**Wat zijn de kosten voor registratie bij de SQL IaaS agent-extensie of met een upgrade naar de volledige beheer baarheids modus?**

Geen. Er zijn geen kosten verbonden aan het registreren met de SQL IaaS agent-extensie of met behulp van een van de drie beheer modi. Het beheer van uw SQL Server-VM met de extensie is volledig gratis. 

**Wat is de invloed van de prestaties van het gebruik van de verschillende beheer modi?**

Er is geen invloed op het gebruik van de modi geen *agent* en *licht gewicht* beheer. Het gebruik van de *volledige* beheer baarheids modus van twee services die op het besturings systeem zijn geïnstalleerd, heeft een minimale invloed. Deze kunnen worden bewaakt via taak beheer en worden weer gegeven in de ingebouwde Windows Services-console. 

De twee service namen zijn:
- `SqlIaaSExtensionQuery` (Weergave naam- `Microsoft SQL Server IaaS Query Service` )
- `SQLIaaSExtension` (Weergave naam- `Microsoft SQL Server IaaS Agent` )

**Wilt u de uitbrei ding Hoe kan ik verwijderen?**

Verwijder de uitbrei ding door de registratie van de SQL Server VM [ongedaan te maken](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) op basis van de SQL IaaS agent-extensie. 

## <a name="next-steps"></a>Volgende stappen

Zie de artikelen voor [automatische installatie](sql-agent-extension-automatic-registration-all-vms.md), [enkelvoudige vm's](sql-agent-extension-manually-register-single-vm.md)of [vm's in bulk](sql-agent-extension-manually-register-vms-bulk.md)om de SQL Server IaaS-uitbrei ding te installeren op SQL Server op virtuele machines van Azure.

Zie voor meer informatie over het uitvoeren van SQL Server op Azure Virtual Machines de [Wat is SQL Server op azure virtual machines?](sql-server-on-azure-vm-iaas-what-is-overview.md).

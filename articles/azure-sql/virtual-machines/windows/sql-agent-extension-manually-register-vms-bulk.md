---
title: Meerdere SQL-Vm's registreren in azure met de SQL IaaS agent-extensie
description: SQL Server Vm's bulksgewijs registreren met de SQL IaaS agent-extensie om de beheer baarheid te verbeteren.
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
ms.openlocfilehash: 558daede55f6563155d3f54e97d77c0a3ca4de59
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97357219"
---
# <a name="register-multiple-sql-vms-in-azure-with-the-sql-iaas-agent-extension"></a>Meerdere SQL-Vm's registreren in azure met de SQL IaaS agent-extensie
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel wordt beschreven hoe u uw SQL Server virtuele machines (Vm's) in bulk in azure registreert met de [SQL IaaS agent-extensie](sql-server-iaas-agent-extension-automate-management.md) met behulp van de `Register-SqlVMs` Azure PowerShell-cmdlet. 


In dit artikel leert u SQL Server Vm's hand matig te registreren. U kunt ook [alle SQL Server vm's automatisch](sql-agent-extension-automatic-registration-all-vms.md) of [afzonderlijke SQL Server vm's hand matig](sql-agent-extension-manually-register-single-vm.md)registreren. 

## <a name="overview"></a>Overzicht

De `Register-SqlVMs` cmdlet kan worden gebruikt voor het registreren van alle virtuele machines in een bepaalde lijst met abonnementen, resource groepen of een lijst met specifieke virtuele machines. De cmdlet registreert de virtuele machines in [lightweight_ beheer modus](sql-server-iaas-agent-extension-automate-management.md#management-modes)en genereert vervolgens zowel een [rapport als een logboek bestand](#output-description). 

Het registratie proces ondergaat geen risico, heeft geen downtime en start de SQL Server-service of de virtuele machine niet opnieuw op. 

## <a name="prerequisites"></a>Vereisten

Als u uw SQL Server-VM wilt registreren met de extensie, hebt u het volgende nodig: 

- Een [Azure-abonnement](https://azure.microsoft.com/free/) dat is [geregistreerd bij de **micro soft. SqlVirtualMachine** -provider](sql-agent-extension-manually-register-single-vm.md#register-subscription-with-rp) en geen geregistreerde SQL Server virtuele machines bevat. 
- De client referenties die worden gebruikt om de virtuele machines te registreren, bestaan in een van de volgende Azure-rollen: Inzender, **Inzender** of **eigenaar** van de **virtuele machine**. 
- De nieuwste versie van [AZ Power shell (mini maal 5,0)](/powershell/azure/new-azureps-module-az). 


## <a name="get-started"></a>Aan de slag

Voordat u doorgaat, moet u eerst een lokale kopie van het script maken, deze importeren als een Power shell-module en verbinding maken met Azure. 

### <a name="create-the-script"></a>Het script maken

Als u het script wilt maken, kopieert u het [volledige script](#full-script) uit het einde van dit artikel en slaat u het lokaal op als `RegisterSqlVMs.psm1` . 

### <a name="import-the-script"></a>Het script importeren

Nadat het script is gemaakt, kunt u het importeren als een module in de Power shell-terminal. 

Open een Power shell-terminal voor beheer en navigeer naar de locatie waar u het bestand hebt opgeslagen `RegisterSqlVMs.psm1` . Voer vervolgens de volgende Power shell-cmdlet uit om het script te importeren als een module: 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Verbinding maken met Azure

Gebruik de volgende Power shell-cmdlet om verbinding te maken met Azure:

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-a-list-of-subscriptions"></a>Alle Vm's in een lijst met abonnementen 

Gebruik de volgende cmdlet om alle SQL Server virtuele machines in een lijst met abonnementen te registreren:

```powershell-interactive
Register-SqlVMs -SubscriptionList SubscriptionId1,SubscriptionId2
```

Voorbeelduitvoer: 

```
Number of subscriptions registration failed for 
because you do not have access or credentials are wrong: 1
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 4
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 3
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-single-subscription"></a>Alle Vm's in één abonnement

Gebruik de volgende cmdlet om alle SQL Server virtuele machines in één abonnement te registreren: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1
```

Voorbeelduitvoer:

```
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 5
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the  guest agent on VM is not running: 2
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-multiple-resource-groups"></a>Alle Vm's in meerdere resource groepen

Gebruik de volgende cmdlet om alle SQL Server virtuele machines in meerdere resource groepen binnen één abonnement te registreren:

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupList ResourceGroup1,ResourceGroup2
```

Voorbeelduitvoer:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-resource-group"></a>Alle virtuele machines in een resource groep

Gebruik de volgende cmdlet om alle SQL Server virtuele machines in één resource groep te registreren: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1
```

Voorbeelduitvoer:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vms-in-a-single-resource-group"></a>Specifieke Vm's in één resource groep

Gebruik de volgende cmdlet om specifieke SQL Server virtuele machines in één resource groep te registreren:

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -VmList VM1,VM2,VM3
```

Voorbeelduitvoer:

```
Total VMs Found: 3
VMs Already registered: 0
Number of VMs registered successfully: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="a-specific-vm"></a>Een specifieke VM

Gebruik de volgende cmdlet om een specifieke SQL Server virtuele machine te registreren: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -Name VM1
```

Voorbeelduitvoer:

```
Total VMs Found: 1
VMs Already registered: 0
Number of VMs registered successfully: 1

Please find the detailed report in  file RegisterSqlVMScriptReport1571314821.txt
```


## <a name="output-description"></a>Uitvoer beschrijving

Elke keer `Register-SqlVMs` dat de cmdlet wordt gebruikt, worden zowel een rapport als een logboek bestand gegenereerd. 

### <a name="report"></a>Rapport

Het rapport wordt gegenereerd als een `.txt` bestand met `RegisterSqlVMScriptReport<Timestamp>.txt` de naam time stamp is het tijdstip waarop de cmdlet werd gestart. Het rapport bevat de volgende details:

| **Uitvoer waarde** | **Beschrijving** |
| :--------------  | :-------------- | 
| Het aantal abonnementen dat is geregistreerd voor omdat u geen toegang of referenties hebt, zijn onjuist | Dit geeft het aantal en de lijst met abonnementen die problemen met de verstrekte verificatie hebben. De gedetailleerde fout vindt u in het logboek door te zoeken naar de abonnements-ID. | 
| Het aantal abonnementen dat niet kan worden geprobeerd, omdat deze niet zijn geregistreerd bij de resource provider | Deze sectie bevat het aantal en de lijst met abonnementen die niet zijn geregistreerd voor de SQL IaaS agent-extensie. |
| Totaal aantal gevonden Vm's | Het aantal virtuele machines dat is gevonden in het bereik van de para meters die zijn door gegeven aan de cmdlet. | 
| Vm's al geregistreerd | Het aantal virtuele machines dat is overgeslagen omdat het al is geregistreerd met de extensie. |
| Aantal Vm's dat is geregistreerd | Het aantal virtuele machines dat is geregistreerd na het uitvoeren van de cmdlet. Geeft een lijst van de geregistreerde virtuele machines in de indeling `SubscriptionID, Resource Group, Virtual Machine` . | 
| Aantal Vm's dat niet kan worden geregistreerd vanwege een fout | Het aantal virtuele machines dat niet kan worden geregistreerd wegens een fout. De details van de fout vindt u in het logboek bestand. | 
| Het aantal Vm's dat is overgeslagen als de virtuele machine of de windvlaag-agent op de VM is niet actief | Count en een lijst met virtuele machines die niet kunnen worden geregistreerd als de virtuele machine of de gast agent op de virtuele machine zijn niet uitgevoerd. Deze kunnen opnieuw worden geprobeerd wanneer de virtuele machine of gast agent is gestart. Meer informatie vindt u in het logboek bestand. |
| Aantal overgeslagen Vm's omdat deze niet worden uitgevoerd SQL Server op Windows | Het aantal virtuele machines dat is overgeslagen omdat deze niet worden uitgevoerd SQL Server of geen virtuele Windows-machine zijn. De virtuele machines worden weer gegeven in de indeling `SubscriptionID, Resource Group, Virtual Machine` . | 
| &nbsp; | &nbsp; |

### <a name="log"></a>Logboek 

Fouten worden vastgelegd in het logboek bestand met de naam `VMsNotRegisteredDueToError<Timestamp>.log` , waarbij tijds tempel het tijdstip is waarop het script is gestart. Als de fout zich op het abonnements niveau bevindt, bevat het logboek de door komma's gescheiden abonnements-ID en het fout bericht. Als de fout is opgetreden bij de registratie van de virtuele machine, bevat het logboek de abonnements-ID, de naam van de resource groep, de naam van de virtuele machine, de fout code en het bericht, gescheiden door komma's. 

## <a name="remarks"></a>Opmerkingen

Wanneer u SQL Server Vm's met de extensie registreert met behulp van het meegeleverde script, moet u rekening houden met het volgende:

- Registratie bij de extensie vereist een gast agent die wordt uitgevoerd op de SQL Server VM. Installatie kopieën van Windows Server 2008 hebben geen gast agent, zodat deze virtuele machines mislukken en hand matig moeten worden geregistreerd met de [beheer modus](sql-server-iaas-agent-extension-automate-management.md#management-modes)voor niet-agents.
- Er is een nieuwe logica ingebouwd om transparante fouten te verholpen. Als de virtuele machine is geregistreerd, is het een snelle bewerking. Als de registratie echter mislukt, wordt elke virtuele machine opnieuw geprobeerd.  Als zodanig is het belang rijk dat u veel tijd hebt om het registratie proces te volt ooien, maar de vereiste voor de werkelijke hoeveelheid tijd is afhankelijk van het type en het aantal fouten. 

## <a name="full-script"></a>Volledige script

Voor het volledige script op GitHub raadpleegt u [bulk registratie SQL Server vm's met AZ Power shell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1). 

Kopieer het volledige script en sla het op als `RegisterSqLVMs.psm1` .

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server op een Windows-VM](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Veelgestelde vragen over SQL Server op een Windows-VM](frequently-asked-questions-faq.md)
* [Prijs informatie voor SQL Server op een Windows-VM](pricing-guidance.md)
* [Release opmerkingen voor SQL Server op een Windows-VM](../../database/doc-changes-updates-release-notes.md)

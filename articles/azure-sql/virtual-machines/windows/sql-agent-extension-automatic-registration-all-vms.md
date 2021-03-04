---
title: Automatische registratie bij de SQL IaaS agent-extensie
description: Meer informatie over het inschakelen van de functie Automatische registratie voor het automatisch registreren van alle oude en toekomstige SQL Server Vm's met de SQL IaaS agent-extensie met behulp van de Azure Portal.
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.openlocfilehash: 139852949a3744fd603cb197b2e27fa32679aae0
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042416"
---
# <a name="automatic-registration-with-sql-iaas-agent-extension"></a>Automatische registratie bij de SQL IaaS agent-extensie
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

De functie voor automatische registratie inschakelen in de Azure Portal om alle huidige en toekomstige SQL Server op Azure-Virtual Machines (Vm's) automatisch te registreren met de [SQL IaaS agent-extensie](sql-server-iaas-agent-extension-automate-management.md) in de Lightweight-modus. 

In dit artikel leert u de functie Automatische registratie in te scha kelen. U kunt ook [één virtuele machine registreren](sql-agent-extension-manually-register-single-vm.md)of [uw vm's in bulk registreren](sql-agent-extension-manually-register-vms-bulk.md) met de SQL IaaS agent-extensie. 

## <a name="overview"></a>Overzicht

Registreer uw SQL Server-VM met de [SQL IaaS agent-extensie](sql-server-iaas-agent-extension-automate-management.md) om een volledige functieset met voor delen te ontgrendelen. 

Als automatische registratie is ingeschakeld, wordt dagelijks een taak uitgevoerd om te detecteren of SQL Server is geïnstalleerd op alle niet-geregistreerde Vm's in het abonnement. Dit wordt gedaan door de binaire bestanden van de extensie van de SQL IaaS-agent naar de virtuele machine te kopiëren en vervolgens een eenmalig hulp programma uit te voeren dat controleert op het onderdeel SQL Server register. Als de SQL Server-component wordt gedetecteerd, wordt de virtuele machine geregistreerd met de uitbrei ding in de Lightweight-modus. Als er geen onderdeel SQL Server in het REGI ster bestaat, worden de binaire bestanden verwijderd.

Zodra automatische registratie is ingeschakeld voor een abonnement, worden alle huidige en toekomstige Vm's die SQL Server geïnstalleerd, geregistreerd met de SQL IaaS agent-extensie **in Lightweight mode zonder downtime, en zonder de SQL Server-service opnieuw te starten**. U moet nog steeds [hand matig bijwerken naar de volledige beheer baarheids modus](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full) om te kunnen profiteren van de volledige functieset. 

> [!IMPORTANT]
> Met de SQL IaaS agent-extensie worden gegevens verzameld voor het snelle doel om klanten optionele voor delen te geven bij het gebruik van SQL Server binnen Azure Virtual Machines. Micro soft gebruikt deze gegevens niet voor licentie controles zonder voorafgaande toestemming van de klant. Raadpleeg de [SQL Server-aanvulling voor privacy](/sql/sql-server/sql-server-privacy#non-personal-data) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Als u uw SQL Server-VM wilt registreren met de extensie, hebt u het volgende nodig: 

- Een [Azure-abonnement](https://azure.microsoft.com/free/) en ten minste machtigingen voor de [rol Inzender](../../../role-based-access-control/built-in-roles.md#all) .
- Een Azure resource model [Windows Server 2008 R2 (of hoger) virtuele machine](../../../virtual-machines/windows/quick-create-portal.md) met [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) geïmplementeerd naar de open bare of Azure Government Cloud. Windows Server 2008 wordt niet ondersteund. 


## <a name="enable"></a>Inschakelen

Voer de volgende stappen uit om automatische registratie van uw SQL Server Vm's in het Azure Portal in te scha kelen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Navigeer naar de resource pagina [**virtuele SQL-machines**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. Selecteer **automatische SQL Server VM-registratie** om de pagina **automatische registratie** te openen. 

   :::image type="content" source="media/sql-agent-extension-automatic-registration-all-vms/automatic-registration.png" alt-text="Selecteer Automatische SQL Server VM-registratie om de pagina automatische registratie te openen":::

1. Kies uw abonnement in de vervolg keuzelijst. 
1. Lees de voor waarden en klik op **Ik ga** akkoord als u akkoord gaat. 
1. Selecteer **registreren** om de functie in te scha kelen en alle huidige en toekomstige SQL Server vm's automatisch te registreren met de SQL IaaS agent-extensie. Hiermee wordt de SQL Server-service op geen van de virtuele machines opnieuw opgestart. 

## <a name="disable"></a>Uitschakelen

Gebruik de [Azure cli](/cli/azure/install-azure-cli) of [Azure PowerShell](/powershell/azure/install-az-ps) om de functie Automatische registratie uit te scha kelen. Wanneer de functie Automatische registratie is uitgeschakeld, moeten SQL Server Vm's die zijn toegevoegd aan het abonnement, hand matig worden geregistreerd met de SQL IaaS agent-extensie. Hiermee wordt de registratie van bestaande SQL Server Vm's die al zijn geregistreerd, niet ongedaan gemaakt.



# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u automatische registratie met Azure CLI wilt uitschakelen, voert u de volgende opdracht uit: 

```azurecli-interactive
az feature unregister --namespace Microsoft.SqlVirtualMachine --name BulkRegistration
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Als u automatische registratie wilt uitschakelen met Azure PowerShell, voert u de volgende opdracht uit: 

```powershell-interactive
Unregister-AzProviderFeature -FeatureName BulkRegistration -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="enable-for-multiple-subscriptions"></a>Inschakelen voor meerdere abonnementen

U kunt de functie voor automatische registratie voor meerdere Azure-abonnementen inschakelen met behulp van Power shell. 

Voer hiervoor de volgende stappen uit:

1. Sla [Dit script](https://github.com/microsoft/tigertoolbox/blob/master/AzureSQLVM/RegisterSubscriptionsToSqlVmAutomaticRegistration.ps1) op in een `.ps1` bestand, zoals `EnableBySubscription.ps1` . 
1. Ga naar de locatie waar u het script hebt opgeslagen met behulp van een opdracht prompt of Power shell-venster. 
1. Verbinding maken met Azure ( `az login` ).
1. Voer het script uit, door gegeven in SubscriptionIds als para meters zoals   
   `.\EnableBySubscription.ps1 -SubscriptionList SubscriptionId1,SubscriptionId2`

   Bijvoorbeeld: 

   ```console
   .\EnableBySubscription.ps1 -SubscriptionList a1a1a-aa11-11aa-a1a1-a11a111a1,b2b2b2-bb22-22bb-b2b2-b2b2b2bb
   ```

Mislukte registratie fouten worden opgeslagen in `RegistrationErrors.csv` dezelfde map als waar u het script hebt opgeslagen en uitgevoerd `.ps1` . 

## <a name="next-steps"></a>Volgende stappen

Voer een upgrade uit van uw beheer baarheids modus naar [volledig](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full) om te profiteren van de volledige functieset die u hebt gekregen van de SQL IaaS agent-extensie. 

---
title: Een beschikbaarheids groep configureren (Azure Portal)
description: Gebruik de Azure Portal om het Windows-failovercluster, de beschikbaarheids groep-listener en de interne load balancer te maken op een SQL Server VM in Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: 14760b4244d42e57aaed7f7d96f487a66147a554
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359503"
---
# <a name="use-azure-portal-to-configure-an-availability-group-preview-for-sql-server-on-azure-vm"></a>Azure Portal gebruiken om een beschikbaarheids groep te configureren voor SQL Server op een Azure VM 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel wordt beschreven hoe u de [Azure Portal](https://portal.azure.com) gebruikt voor het configureren van een beschikbaarheids groep voor SQL Server op Azure-vm's. 

Gebruik de Azure Portal voor het maken van een nieuw cluster of het onboarden van een bestaand cluster, en maak vervolgens de beschikbaarheids groep, listener en interne load balancer. 

Deze functie is momenteel beschikbaar als preview-product. 

In dit artikel wordt gebruikgemaakt van de Azure Portal voor het configureren van de omgeving voor de beschikbaarheids groep. Dit is ook mogelijk met [Power shell of de Azure-cli](availability-group-az-commandline-configure.md), [Azure Quick](availability-group-quickstart-template-configure.md)start-sjablonen of ook [hand matig](availability-group-manually-configure-tutorial.md) . 


## <a name="prerequisites"></a>Vereisten

Als u een always on-beschikbaarheids groep wilt configureren met behulp van de Azure Portal, moet u de volgende vereisten hebben: 

- Een [Azure-abonnement](https://azure.microsoft.com/free/).
- Een resource groep met een domein controller. 
- Een of meer Vm's die zijn gekoppeld aan een domein [in azure met SQL Server 2016 (of hoger) Enter prise Edition](./create-sql-vm-portal.md) in *dezelfde* beschikbaarheidsset of *verschillende* beschikbaarheids zones die zijn [geregistreerd met de SQL IaaS agent-extensie in de volledige beheer modus](sql-agent-extension-manually-register-single-vm.md) en die hetzelfde domein account gebruiken voor de SQL Server-service op elke virtuele machine.
- Twee beschik bare (niet gebruikt door een entiteit) IP-adressen. Een voor de interne load balancer. De andere is voor de beschikbaarheids groep-listener binnen hetzelfde subnet als de beschikbaarheids groep. Als u een bestaande load balancer gebruikt, hebt u slechts één beschikbaar IP-adres nodig voor de beschikbaarheids groep-listener. 

## <a name="permissions"></a>Machtigingen

U hebt de volgende account machtigingen nodig om de beschikbaarheids groep te configureren met behulp van de Azure Portal: 

- Een bestaand domein gebruikers account met een machtiging voor het **maken van computer objecten** in het domein. Een domein beheerders account heeft bijvoorbeeld doorgaans voldoende machtigingen (bijvoorbeeld: account@domain.com ). _Dit account moet ook lid zijn van de lokale groep Administrators op elke virtuele machine om het cluster te maken._
- Het domein gebruikers account dat SQL Server beheert. Dit moet hetzelfde account zijn voor elke SQL Server virtuele machine die u wilt toevoegen aan de beschikbaarheids groep. 

## <a name="configure-cluster"></a>Cluster configureren

Configureer het cluster met behulp van de Azure Portal. U kunt een nieuw cluster maken of als u al een bestaand cluster hebt, kunt u het onboarden naar de SQL IaaS agent-extensie voor de beheer baarheid van de portal.


### <a name="create-a-new-cluster"></a>Een nieuw cluster maken

Als u al een cluster hebt, kunt u deze sectie overs Laan en het bestaande cluster in plaats daarvan verplaatsen naar een [onboarding](#onboard-existing-cluster) . 

Als u nog geen bestaand cluster hebt, maakt u dit met behulp van de Azure Portal door de volgende stappen uit te voeren:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com). 
1. Navigeer naar de resource van de [virtuele SQL-machines](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. Selecteer **hoge Beschik baarheid** onder **instellingen**. 
1. Selecteer **+ nieuw Windows Server-failovercluster** om de pagina **Windows-failovercluster configureren** te openen.  

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-cluster.png" alt-text="Nieuw cluster maken door het + nieuwe cluster te selecteren in de portal":::

1. Geef uw cluster een naam en een opslag account om te gebruiken als de Cloudwitness. Gebruik een bestaand opslag account of selecteer **nieuwe maken** om een nieuw opslag account te maken. Een opslagaccountnaam moet tussen 3 en 24 tekens lang zijn en mag alleen bestaan uit getallen en kleine letters.

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-1.png" alt-text="Naam, opslag account en referenties opgeven voor het cluster":::

1. Vouw de referenties van het **Windows Server-failovercluster** uit om [referenties](/rest/api/sqlvm/sqlvirtualmachinegroups/createorupdate#wsfcdomainprofile) op te geven voor het SQL Server-service account, evenals de cluster operator en de Boots trap-accounts als deze verschillen van het account dat wordt gebruikt voor de SQL Server-service. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-2.png" alt-text="Geef referenties op voor het SQL-Service account, het cluster operator account en het Boots trap-account van het cluster":::

1. Selecteer de SQL Server Vm's die u aan het cluster wilt toevoegen. Controleer of de computer opnieuw moet worden opgestart en wees voorzichtig. Alleen Vm's die zijn geregistreerd bij de SQL IaaS agent-extensie in de volledige beheer modus en zich op dezelfde locatie, domein en op hetzelfde virtuele netwerk bevinden als de primaire SQL Server VM is zichtbaar. 
1. Selecteer **Toep assen** om het cluster te maken. U kunt de status van uw implementatie controleren in het **activiteiten logboek** dat toegankelijk is via het klok pictogram in de bovenste navigatie balk. 
1. Voor een failovercluster dat door micro soft wordt ondersteund, moet de cluster validatie worden door gegeven. Maak verbinding met de virtuele machine met behulp van uw voorkeurs methode (zoals Remote Desktop Protocol (RDP)) en controleer of de validatie van uw cluster wordt uitgevoerd voordat u doorgaat. Als u dit niet doet, wordt de status van het cluster niet ondersteund. U kunt het cluster valideren met behulp van Failoverclusterbeheer (FCM) of de volgende Power shell-opdracht:

    ```powershell
    Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
    ```
    


### <a name="onboard-existing-cluster"></a>Bestaand cluster onboarding

Als u al een cluster hebt geconfigureerd in uw SQL Server VM-omgeving, kunt u het uitvoeren vanuit de Azure Portal.

Voer hiervoor de volgende stappen uit:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com). 
1. Navigeer naar de resource van de [virtuele SQL-machines](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. Selecteer **hoge Beschik baarheid** onder **instellingen**. 
1. Selecteer een **bestaand Windows Server-failovercluster onboarding** om de pagina **onboarding Windows Server failover cluster** te openen. 

   :::image type="content" source="media/availability-group-az-portal-configure/onboard-existing-cluster.png" alt-text="Onboarding van een bestaand cluster op de pagina hoge Beschik baarheid op uw virtuele SQL-machines":::

1. Controleer de instellingen voor uw cluster. 
1. Selecteer **Toep assen** om het cluster vrij te maken en selecteer vervolgens **Ja** bij de prompt om door te gaan.

## <a name="create-availability-group"></a>Beschikbaarheids groep maken

Nadat het cluster is gemaakt of onboarded, maakt u de beschikbaarheids groep met behulp van de Azure Portal. Voer hiervoor de volgende stappen uit:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com). 
1. Navigeer naar de resource van de [virtuele SQL-machines](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. Selecteer **hoge Beschik baarheid** onder **instellingen**. 
1. Selecteer **+ nieuwe always on-beschikbaarheids groep** om de pagina **beschikbaarheids groep maken** te openen.

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-availability-group.png" alt-text="Selecteer nieuwe always on-beschikbaarheids groep om de pagina beschikbaarheids groep maken te openen.":::

1. Voer een naam in voor de beschikbaarheids groep. 
1. Selecteer **listener configureren** om de pagina **beschikbaarheids groep-listener configureren** te openen. 

   :::image type="content" source="media/availability-group-az-portal-configure/create-availability-group.png" alt-text="Een naam opgeven voor de beschikbaarheids groep en een listener configureren":::

1. Vul de waarden in en gebruik een bestaande load balancer of selecteer **nieuwe maken** om een nieuwe Load Balancer te maken.  Selecteer **Toep assen** om uw instellingen op te slaan en uw listener en Load Balancer te maken. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-listener.png" alt-text="Vul de waarden in het formulier in om uw nieuwe listener te maken en load balancer":::

1. Kies **en selecteer replica** om de pagina **replica's van beschikbaarheids groep configureren** te openen.
1. Selecteer de virtuele machines die u wilt toevoegen aan de beschikbaarheids groep en kies de instellingen voor de beschikbaarheids groep die het beste bij uw bedrijfs behoeften passen. Selecteer **Toep assen** om uw instellingen op te slaan. 

   :::image type="content" source="media/availability-group-az-portal-configure/add-replicas.png" alt-text="Kies Vm's om toe te voegen aan uw beschikbaarheids groep en configureer de instellingen die geschikt zijn voor uw bedrijf":::

1. Controleer de instellingen van uw beschikbaarheids groep en selecteer vervolgens **Toep assen** om uw beschikbaarheids groep te maken. 

U kunt de status van uw implementatie controleren in het **activiteiten logboek** dat toegankelijk is via het klok pictogram in de bovenste navigatie balk. 

  > [!NOTE]
  > De **synchronisatie status** op de pagina **hoge Beschik baarheid** van de Azure portal wordt weer gegeven als **niet in orde** totdat u data bases toevoegt aan uw beschikbaarheids groep. 


## <a name="add-database-to-availability-group"></a>Data base toevoegen aan beschikbaarheids groep

Voeg uw data bases toe aan uw beschikbaarheids groep nadat de implementatie is voltooid. In de onderstaande stappen wordt SQL Server Management Studio (SSMS) gebruikt, maar u kunt ook [Transact-SQL of Power shell](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) gebruiken. 

Voer de volgende stappen uit om data bases toe te voegen aan uw beschikbaarheids groep met SQL Server Management Studio:

1. Maak verbinding met een van uw SQL Server Vm's met behulp van uw voorkeurs methode, zoals Verbinding met extern bureaublad (RDP). 
1. Open SQL Server Management Studio (SSMS).
1. Maak verbinding met uw SQL Server-exemplaar. 
1. Vouw **altijd uit op hoge Beschik baarheid** in **objectverkenner**.
1. Vouw **beschikbaarheids groepen** uit, klik met de rechter muisknop op uw beschikbaarheids groep en kies **Data Base toevoegen...**.

   :::image type="content" source="media/availability-group-az-portal-configure/add-database.png" alt-text="Klik met de rechter muisknop op de beschikbaarheids groep in object Verkenner en kies data base toevoegen":::

1. Volg de aanwijzingen om de data base (s) te selecteren die u wilt toevoegen aan uw beschikbaarheids groep. 
1. Selecteer **OK** om uw instellingen op te slaan en uw data base toe te voegen aan de beschikbaarheids groep. 
1. Nadat de data base is toegevoegd, vernieuwt u **objectverkenner** om de status van uw data base te bevestigen als `synchronized` . 

Nadat de data bases zijn toegevoegd, kunt u de status van uw beschikbaarheids groep controleren in de Azure Portal: 

:::image type="content" source="media/availability-group-az-portal-configure/healthy-availability-group.png" alt-text="Controleer de status van uw beschikbaarheids groep op de pagina hoge Beschik baarheid van de Azure Portal nadat de data bases zijn gesynchroniseerd":::

## <a name="add-more-vms"></a>Meer Vm's toevoegen

Voer de volgende stappen uit om meer SQL Server Vm's toe te voegen aan het cluster: 

1. Meld u aan bij de [Azure Portal](https://portal.azure.com). 
1. Navigeer naar de resource van de [virtuele SQL-machines](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. Selecteer **hoge Beschik baarheid** onder **instellingen**. 
1. Selecteer **Windows Server-failovercluster configureren** om de pagina **Windows Server-failovercluster configureren** te openen. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-existing-cluster.png" alt-text="Selecteer Windows Server-failovercluster configureren om Vm's toe te voegen aan uw cluster.":::

1. Vouw de referenties voor het **Windows Server-failovercluster** uit en voer in de accounts die worden gebruikt voor de SQL Server service, cluster operator en de Boots trap-accounts van het cluster. 
1. Selecteer de SQL Server Vm's die u aan het cluster wilt toevoegen. 
1. Selecteer **Toepassen**. 

U kunt de status van uw implementatie controleren in het **activiteiten logboek** dat toegankelijk is via het klok pictogram in de bovenste navigatie balk. 


## <a name="modify-availability-group"></a>Beschikbaarheids groep wijzigen 


U kunt **meer Replica's toevoegen** aan de beschikbaarheids groep, **de listener configureren** of **de listener verwijderen** van de pagina **hoge Beschik baarheid** in het Azure portal door de beletsel tekens (...) naast uw beschikbaarheids groep te selecteren: 

:::image type="content" source="media/availability-group-az-portal-configure/configure-listener.png" alt-text="Selecteer de weglatings tekens naast de beschikbaarheids groep en selecteer vervolgens replica toevoegen om meer replica's aan de beschikbaarheids groep toe te voegen.":::

## <a name="remove-cluster"></a>Cluster verwijderen

Verwijder alle SQL Server-Vm's uit het cluster om deze te vernietigen en verwijder vervolgens de meta gegevens van het cluster uit de SQL IaaS agent-extensie. U kunt dit doen met behulp van de nieuwste versie van de [Azure cli](/cli/azure/install-azure-cli) of Power shell. 

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Verwijder eerst alle SQL Server-Vm's uit het cluster. Hiermee verwijdert u de knoop punten van het cluster fysiek en vernietigt u het cluster:  

```azurecli-interactive
# Remove the VM from the cluster metadata
# example: az sql vm remove-from-group --name SQLVM2 --resource-group SQLVM-RG

az sql vm remove-from-group --name <VM1 name>  --resource-group <resource group name>
az sql vm remove-from-group --name <VM2 name>  --resource-group <resource group name>
```

Als dit de enige Vm's in het cluster zijn, wordt het cluster vernietigd. Als er andere virtuele machines in het cluster zijn dan de SQL Server Vm's die zijn verwijderd, worden de andere Vm's niet verwijderd en wordt het cluster niet vernietigd. 

Verwijder vervolgens de meta gegevens van het cluster uit de uitbrei ding SQL IaaS agent: 

```azurecli-interactive
# Remove the cluster from the SQL VM RP metadata
# example: az sql vm group delete --name Cluster --resource-group SQLVM-RG

az sql vm group delete --name <cluster name> --resource-group <resource group name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwijder eerst alle SQL Server-Vm's uit het cluster. Hiermee verwijdert u de knoop punten van het cluster fysiek en vernietigt u het cluster: 

```powershell-interactive
# Remove the SQL VM from the cluster
# example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
#  $sqlvm. SqlVirtualMachineGroup = ""
#  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

$sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
   $sqlvm. SqlVirtualMachineGroup = ""
   
   Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
```

Als dit de enige Vm's in het cluster zijn, wordt het cluster vernietigd. Als er andere virtuele machines in het cluster zijn dan de SQL Server Vm's die zijn verwijderd, worden de andere Vm's niet verwijderd en wordt het cluster niet vernietigd. 


Verwijder vervolgens de meta gegevens van het cluster uit de uitbrei ding SQL IaaS agent: 

```powershell-interactive
# Remove the cluster metadata
# example: Remove-AzSqlVMGroup -ResourceGroupName "SQLVM-RG" -Name "Cluster"

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name>"
```

---

## <a name="troubleshooting"></a>Problemen oplossen

Als u problemen ondervindt, kunt u de implementatie geschiedenis controleren en de veelvoorkomende fouten en de resoluties bekijken. 

### <a name="check-deployment-history"></a>Implementatie geschiedenis controleren

Wijzigingen in de cluster-en beschikbaarheids groep via de portal worden uitgevoerd via implementaties. De implementatie geschiedenis kan meer details bieden als er problemen zijn met het maken of voorbereiden van het cluster of bij het maken van de beschikbaarheids groep.

Voer de volgende stappen uit om de logboeken voor de implementatie weer te geven en de implementatie geschiedenis te controleren:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Navigeer naar uw resource groep.
1. Selecteer **implementaties** onder **instellingen**.
1. Selecteer de implementatie van belang voor meer informatie over de implementatie. 


   :::image type="content" source="media/availability-group-az-portal-configure/failed-deployment.png" alt-text="Selecteer de implementatie waarover u meer wilt weten." :::

### <a name="common-errors"></a>Algemene fouten

Bekijk de volgende veelvoorkomende fouten en hun oplossingen. 

#### <a name="the-account-which-is-used-to-start-up-sql-service-is-not-a-domain-account"></a>Het account dat wordt gebruikt voor het starten van de SQL-service is geen domein account

Hiermee wordt aangegeven dat de resource provider geen toegang kan krijgen tot de SQL Server-service met de gegeven referenties. Enkele algemene oplossingen:
- Zorg ervoor dat uw domein controller wordt uitgevoerd.
- Controleer of de referenties die in de portal zijn opgegeven, overeenkomen met die van de SQL Server service. 


## <a name="next-steps"></a>Volgende stappen


Zie voor meer informatie over beschikbaarheids groepen:

- [Overzicht van beschikbaarheids groepen](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
* [Beheer van een beschikbaarheids groep](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Bewaking van beschikbaarheids groepen &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Transact-SQL-instructies voor de beschikbaarheids groep ](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Power shell-opdrachten voor beschikbaarheids groepen](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  

Zie voor meer informatie over SQL Server Vm's: 

* [Overzicht van SQL Server Vm's](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Release opmerkingen voor SQL Server Vm's](../../database/doc-changes-updates-release-notes.md)
* [Veelgestelde vragen over SQL Server Vm's](frequently-asked-questions-faq.md)

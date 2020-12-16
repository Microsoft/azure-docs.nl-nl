---
title: Een FCI maken met gedeelde Azure-schijven
description: Gebruik gedeelde Azure-schijven voor het maken van een FCI (failover cluster instance) met SQL Server op Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/26/2020
ms.author: mathoma
ms.openlocfilehash: 6a000daa7d9e2aa93e68844e8aec5aa168c9fa60
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592412"
---
# <a name="create-an-fci-with-azure-shared-disks-sql-server-on-azure-vms"></a>Een FCI maken met gedeelde Azure-schijven (SQL Server op virtuele machines van Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel wordt uitgelegd hoe u een FCI (failover cluster instance) maakt met behulp van gedeelde Azure-schijven met SQL Server op Azure Virtual Machines (Vm's). 

Zie voor meer informatie een overzicht van [FCI met SQL Server op Azure vm's](failover-cluster-instance-overview.md) en [Aanbevolen procedures voor clusters](hadr-cluster-best-practices.md). 


## <a name="prerequisites"></a>Vereisten 

Voordat u de instructies in dit artikel hebt voltooid, hebt u het volgende nodig:

- Een Azure-abonnement. Ga [gratis](https://azure.microsoft.com/free/)aan de slag. 
- [Twee of meer virtuele Windows Azure-machines](failover-cluster-instance-prepare-vm.md). [Beschikbaarheids sets](../../../virtual-machines/windows/tutorial-availability-sets.md) en [proximity placement groups](../../../virtual-machines/windows/co-location.md#proximity-placement-groups) (PPGs) die worden ondersteund voor Premium-SSD-en [beschikbaarheids zones](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address) , worden ondersteund voor Ultra disks. Als u een PPG gebruikt, moeten alle knoop punten zich in dezelfde groep bevinden.
- Een account met machtigingen voor het maken van objecten op zowel virtuele Azure-machines als in Active Directory.
- De meest recente versie van [Power shell](/powershell/azure/install-az-ps). 


## <a name="add-azure-shared-disk"></a>Gedeelde Azure-schijf toevoegen
Implementeer een beheerde Premium-SSD schijf met de functie gedeelde schijf ingeschakeld. Stel `maxShares` in dat er moet worden **uitgelijnd met het aantal cluster knooppunten** om de schijf te delen op alle FCI-knoop punten. 

Als u een gedeelde Azure-schijf wilt toevoegen, gaat u als volgt te werk: 


1. Sla het volgende script *op alsSharedDiskConfig.jsop*: 

   ```JSON
   { 
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "dataDiskName": {
         "type": "string",
         "defaultValue": "mySharedDisk"
       },
       "dataDiskSizeGB": {
         "type": "int",
         "defaultValue": 1024
       },
       "maxShares": {
         "type": "int",
         "defaultValue": 2
       }
     },
     "resources": [
       {
         "type": "Microsoft.Compute/disks",
         "name": "[parameters('dataDiskName')]",
         "location": "[resourceGroup().location]",
         "apiVersion": "2019-07-01",
         "sku": {
           "name": "Premium_LRS"
         },
         "properties": {
           "creationData": {
             "createOption": "Empty"
           },
           "diskSizeGB": "[parameters('dataDiskSizeGB')]",
           "maxShares": "[parameters('maxShares')]"
         }
       }
     ] 
   }
   ```


2. Voer *SharedDiskConfig.js* uit met behulp van Power shell: 

   ```powershell
   $rgName = < specify your resource group name>
       $location = 'westcentralus'
       New-AzResourceGroupDeployment -ResourceGroupName $rgName `
   -TemplateFile "SharedDiskConfig.json"
   ```

3. Voor elke virtuele machine initialiseert u de gekoppelde gedeelde schijven als GUID-partitie tabel (GPT) en formatteert u deze als nieuw technologie bestandssysteem (NTFS) door de volgende opdracht uit te voeren: 

    ```powershell
    $resourceGroup = "<your resource group name>"
    $location = "<region of your shared disk>"
    $ppgName = "<your proximity placement groups name>"
    $vm = Get-AzVM -ResourceGroupName "<your resource group name>" `
        -Name "<your VM node name>"
    $dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup `
        -DiskName "<your shared disk name>"
    $vm = Add-AzVMDataDisk -VM $vm -Name "<your shared disk name>" `
        -CreateOption Attach -ManagedDiskId $dataDisk.Id `
        -Lun <available LUN - check disk setting of the VM>
    Update-AzVm -VM $vm -ResourceGroupName $resourceGroup
    ```

## <a name="create-failover-cluster"></a>Failovercluster maken

Als u het failovercluster wilt maken, hebt u het volgende nodig:

- De namen van de virtuele machines die worden de cluster knooppunten.
- Een naam voor het failovercluster.
- Een IP-adres voor het failovercluster. U kunt een IP-adres gebruiken dat niet wordt gebruikt in hetzelfde virtuele Azure-netwerk en subnet als de cluster knooppunten.


# <a name="windows-server-2012-2016"></a>[Windows Server 2012-2016](#tab/windows2012)

Met het volgende Power shell-script maakt u een failovercluster. Werk het script bij met de namen van de knoop punten (de namen van de virtuele machines) en een beschikbaar IP-adres van het virtuele Azure-netwerk.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

Met het volgende Power shell-script maakt u een failovercluster. Werk het script bij met de namen van de knoop punten (de namen van de virtuele machines) en een beschikbaar IP-adres van het virtuele Azure-netwerk.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

Zie voor meer informatie [failover cluster: Cluster Network object](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).

---


## <a name="configure-quorum"></a>Quorum configureren

Configureer de quorum oplossing die het beste past bij uw bedrijfs behoeften. U kunt een schijfwitness [, een](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum) [Cloudwitness](/windows-server/failover-clustering/deploy-cloud-witness)of een [Bestands share-Witness](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)configureren. Zie voor meer informatie [quorum met SQL Server vm's](hadr-cluster-best-practices.md#quorum). 

## <a name="validate-cluster"></a>Cluster valideren
Valideer het cluster in de gebruikers interface of met behulp van Power shell.

Als u het cluster wilt valideren met behulp van de gebruikers interface, gaat u als volgt te werk op een van de virtuele machines:

1. Klik onder **Serverbeheer** op **extra** en selecteer vervolgens **Failoverclusterbeheer**.
1. Selecteer onder **Failoverclusterbeheer** **actie** en selecteer vervolgens **configuratie valideren**.
1. Selecteer **Next**.
1. Voer onder **servers of een cluster selecteren** de namen van beide virtuele machines in.
1. Onder **test opties** selecteert u **alleen geselecteerde tests uitvoeren**. 
1. Selecteer **Next**.
1. Selecteer onder **selectie testen** alle tests *behalve* **opslag**

## <a name="test-cluster-failover"></a>Cluster-Failover testen

Test de failover van het cluster. Klik in **Failoverclusterbeheer** met de rechter muisknop op uw cluster, selecteer **meer acties**  >  **basis cluster resource verplaatsen**  >  **knoop punt selecteren** en selecteer vervolgens het andere knoop punt van het cluster. Verplaats de kern cluster bron naar elk knoop punt van het cluster en verplaats deze vervolgens terug naar het primaire knoop punt. Als u het cluster kunt verplaatsen naar elk knoop punt, bent u klaar om SQL Server te installeren.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Cluster-Failover testen door de kern bron te verplaatsen naar de andere knoop punten":::

## <a name="create-sql-server-fci"></a>SQL Server FCI maken

Nadat u het failovercluster en alle cluster onderdelen, inclusief opslag, hebt geconfigureerd, kunt u de SQL Server-FCI maken.

1. Maak verbinding met de eerste virtuele machine met behulp van Remote Desktop Protocol (RDP).

1. Zorg er in **Failoverclusterbeheer** voor dat alle kern cluster bronnen zich op de eerste virtuele machine bevinden. Verplaats, indien nodig, alle resources naar die virtuele machine.

1. Zoek het installatie medium. Als de virtuele machine een van de installatie kopieën van Azure Marketplace gebruikt, bevindt de media zich op `C:\SQLServer_<version number>_Full` . 

1. Selecteer **Setup**.

1. In **SQL Server Installation Center** selecteert u **installatie**.

1. Selecteer **nieuwe SQL Server failover-cluster installatie**. Volg de instructies in de wizard om de SQL Server FCI te installeren.

De FCI-gegevens directory's moeten op de gedeelde Azure-schijven staan. 

1. Nadat u de instructies in de wizard hebt voltooid, installeert Setup een SQL Server FCI op het eerste knoop punt.

1. Nadat de FCI op het eerste knoop punt is geïnstalleerd, maakt u verbinding met het tweede knoop punt met behulp van RDP.

1. Open het **SQL Server-installatie centrum** en selecteer vervolgens **installatie**.

1. Selecteer **knoop punt toevoegen aan een SQL Server-failovercluster**. Volg de instructies in de wizard om SQL Server te installeren en de server toe te voegen aan de FCI.

   >[!NOTE]
   >Als u een installatie kopie van een Azure Marketplace-galerie hebt gebruikt die SQL Server bevat, zijn er SQL Server-hulpprogram ma's in de installatie kopie opgenomen. Als u een van deze installatie kopieën niet hebt gebruikt, installeert u de SQL Server-hulpprogram ma's afzonderlijk. Zie [down load SQL Server Management Studio (SSMS) (Engelstalig)](/sql/ssms/download-sql-server-management-studio-ssms)voor meer informatie.
   >

## <a name="register-with-the-sql-vm-rp"></a>Registreren bij de SQL-VM RP

Als u uw SQL Server-VM vanuit de portal wilt beheren, moet u deze registreren bij de SQL IaaS agent extension (RP) in de [modus voor licht gewicht beheer](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode), momenteel de enige modus die wordt ondersteund door FCI en SQL Server op Azure-vm's. 


Een SQL Server VM registreren in de licht gewicht modus met Power shell:  

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>

# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType PAYG -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Connectiviteit configureren 

Als u verkeer op de juiste manier wilt door sturen naar het huidige primaire knoop punt, configureert u de connectiviteits optie die geschikt is voor uw omgeving. U kunt een [Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) maken of als u SQL Server 2019 Cu2 (of hoger) en Windows Server 2016 (of hoger) gebruikt, kunt u in plaats daarvan de functie [gedistribueerde netwerk naam](failover-cluster-instance-distributed-network-name-dnn-configure.md) gebruiken. 

## <a name="limitations"></a>Beperkingen

- Alleen registreren met de SQL IaaS agent-extensie in de [Lightweight-beheer modus](sql-server-iaas-agent-extension-automate-management.md#management-modes) wordt ondersteund.

## <a name="next-steps"></a>Volgende stappen

Als u dit nog niet hebt gedaan, configureert u de connectiviteit met uw FCI met de naam van een [virtueel netwerk en een Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) of [gedistribueerde netwerk naam (DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md). 


Als gedeelde Azure-schijven niet de juiste FCI-opslag oplossing voor u zijn, kunt u overwegen om uw FCI te maken met behulp van [Premium-bestands shares](failover-cluster-instance-premium-file-share-manually-configure.md) of [opslagruimten direct](failover-cluster-instance-storage-spaces-direct-manually-configure.md) in plaats daarvan. 

Zie voor meer informatie een overzicht van [FCI met SQL Server op Azure vm's](failover-cluster-instance-overview.md) en [Aanbevolen procedures voor cluster configuratie](hadr-cluster-best-practices.md).

Zie voor meer informatie: 
- [Windows-clustertechnologieën](/windows-server/failover-clustering/failover-clustering-overview)   
- [Instanties van een SQL Server-failovercluster](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
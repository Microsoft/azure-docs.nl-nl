---
title: Opslag voor SQL Server Vm's configureren | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe Azure opslag configureert voor SQL Server Vm's tijdens het inrichten (Azure Resource Manager implementatie model). Ook wordt uitgelegd hoe u opslag kunt configureren voor uw bestaande SQL Server Vm's.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: 982bd9239c5e95c9b7af09b5f54c5a09067ca7c6
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565410"
---
# <a name="configure-storage-for-sql-server-vms"></a>Opslag configureren voor SQL Server Vm's
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel leert u hoe u uw opslag kunt configureren voor uw SQL Server op Azure Virtual Machines (Vm's).

SQL Server Vm's die worden geïmplementeerd via Marketplace-installatie kopieën volgen automatisch de [Aanbevolen procedures voor opslag](performance-guidelines-best-practices-storage.md) die tijdens de implementatie kunnen worden gewijzigd. Sommige van deze configuratie-instellingen kunnen na de implementatie worden gewijzigd. 


## <a name="prerequisites"></a>Vereisten

Als u de configuratie-instellingen voor automatische opslag wilt gebruiken, zijn voor uw virtuele machine de volgende kenmerken vereist:

* Ingericht met een [installatie kopie van SQL Server galerie](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo) of is geregistreerd bij de [SQL IaaS-extensie]().
* Maakt gebruik van het [Resource Manager-implementatie model](../../../azure-resource-manager/management/deployment-models.md).
* Maakt gebruik van [Premium-ssd's](../../../virtual-machines/disks-types.md).

## <a name="new-vms"></a>Nieuwe Vm's

In de volgende secties wordt beschreven hoe u opslag configureert voor nieuwe SQL Server virtuele machines.

### <a name="azure-portal"></a>Azure Portal

Bij het inrichten van een Azure-VM met behulp van een SQL Server galerie-afbeelding, selecteert u **configuratie wijzigen** op het tabblad **SQL Server instellingen** om de configuratie pagina geoptimaliseerd voor prestaties te openen. U kunt de waarden standaard laten staan of het type schijf configuratie aanpassen dat het beste bij uw behoeften past, op basis van uw werk belasting. 

![Scherm opname van het tabblad SQL Server instellingen en de optie voor het wijzigen van de configuratie.](./media/storage-configuration/sql-vm-storage-configuration-provisioning.png)

Selecteer het type werk belasting waarvoor u uw SQL Server wilt implementeren onder **opslag optimalisatie**. Met de optie **Algemeen** optimalisatie hebt u standaard één gegevens schijf met een maximale IOPS van 5000. u gebruikt hetzelfde station voor uw gegevens, het transactie logboek en de tempdb-opslag. 

Als u **transactionele verwerking** (OLTP) of **gegevens opslag** selecteert, wordt er een afzonderlijke schijf voor gegevens gemaakt, een afzonderlijke schijf voor het transactie logboek en lokale SSD gebruiken voor TempDB. Er zijn geen opslag verschillen tussen **transactionele verwerking** en **Data Warehousing**, maar de configuratie van de [Stripe en tracerings markeringen](#workload-optimization-settings)worden gewijzigd. Als u Premium Storage kiest, wordt de cache ingesteld op *ReadOnly* voor het gegevens station en *geen* voor het logboek station volgens [SQL Server aanbevolen procedures](performance-guidelines-best-practices.md)voor de VM-prestaties. 

![Configuratie van VM-opslag SQL Server tijdens het inrichten](./media/storage-configuration/sql-vm-storage-configuration.png)

De schijf configuratie kan volledig worden aangepast, zodat u de opslag topologie, het schijf type en de IOPs die u nodig hebt voor uw SQL Server VM-workload kunt configureren. U hebt ook de mogelijkheid om UltraSSD (preview) te gebruiken als een optie voor het **schijf type** als uw SQL Server virtuele machine zich in een van de ondersteunde regio's bevindt (VS-Oost 2, zuidoost-azië en Europa-Noord) en u [Ultra disks hebt ingeschakeld voor uw abonnement](../../../virtual-machines/disks-enable-ultra-ssd.md).  

Daarnaast hebt u de mogelijkheid om de cache voor de schijven in te stellen. Azure-Vm's hebben een cache technologie met meerdere lagen, die [BLOB-cache](../../../virtual-machines/premium-storage-performance.md#disk-caching) heet als deze wordt gebruikt met [Premium-schijven](../../../virtual-machines/disks-types.md#premium-ssd). BLOB-cache maakt gebruik van een combi natie van het RAM-geheugen van de virtuele machine en de lokale SSD voor caching. 

Schijf cache voor Premium-SSD kan *alleen-lezen*, *readwrite* of *geen* zijn. 

- *ReadOnly* -caching is zeer nuttig voor SQL Server gegevens bestanden die zijn opgeslagen op Premium Storage. *Alleen* -lezen cache levert lage lees latentie, grote Lees-IOPS en door Voer als, lees bewerkingen worden uitgevoerd vanuit de cache, die zich in het geheugen van de virtuele machine bevindt en de lokale SSD. Deze Lees bewerkingen zijn veel sneller dan lees bewerkingen van gegevens schijf, die afkomstig zijn van Azure Blob-opslag. Premium-opslag telt niet de Lees bewerkingen van de cache naar de schijf-IOPS en door voer. Daarom kan uw toepas bare totale IOPS en door voer worden gerealiseerd. 
- *Geen* cache configuratie moet worden gebruikt voor de schijven die worden gehost SQL Server logboek bestand, terwijl het logboek bestand opeenvolgend wordt geschreven en niet in aanmerking komt voor *ReadOnly* -caching. 
- *Readwrite* -caching mag niet worden gebruikt voor het hosten van SQL Server-bestanden omdat SQL Server geen consistentie van gegevens met de *readwrite* -cache ondersteunt. Schrijft de verspilings capaciteit van de *alleen-lezen* BLOB-cache en de latenties lichter toe als schrijf bewerkingen via *alleen-lezen* BLOB-cache lagen passeren. 


   > [!TIP]
   > Zorg ervoor dat uw opslag configuratie overeenkomt met de beperkingen die zijn opgelegd door de geselecteerde VM-grootte. Als u opslag parameters kiest die de prestaties van de VM-grootte overschrijden, resulteert dit in een waarschuwing: `The desired performance might not be reached due to the maximum virtual machine disk performance cap` . Verlaag de IOPs door het schijf type te wijzigen of verhoog de limiet voor de snelheid van de virtuele machine door de VM-grootte te verhogen. Hiermee wordt het inrichten niet gestopt. 


Op basis van uw keuzes voert Azure de volgende opslag configuratie taken uit na het maken van de VM:

* Hiermee worden Premium-Ssd's gemaakt en gekoppeld aan de virtuele machine.
* Hiermee configureert u de gegevens schijven die toegankelijk moeten zijn voor SQL Server.
* Hiermee configureert u de gegevens schijven in een opslag groep op basis van de opgegeven grootte en prestaties (IOPS en door Voer) vereisten.
* Koppelt de opslag groep aan een nieuw station op de virtuele machine.
* Optimaliseert dit nieuwe station op basis van het opgegeven type werk belasting (gegevens opslag, transactionele verwerking of algemeen).

Zie [de zelf studie over het inrichten](../../../azure-sql/virtual-machines/windows/create-sql-vm-portal.md)voor een volledig overzicht van het maken van een SQL Server-VM in de Azure Portal.

### <a name="resource-manager-templates"></a>Resource Manager-sjablonen

Als u de volgende Resource Manager-sjablonen gebruikt, worden standaard twee Premium-gegevens schijven gekoppeld zonder configuratie van de opslag groep. U kunt deze sjablonen echter aanpassen om het aantal Premium-gegevens schijven te wijzigen dat aan de virtuele machine is gekoppeld.

* [Een VM maken met geautomatiseerde back-up](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Een VM maken met geautomatiseerde patching](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Een virtuele machine maken met Azure-integratie](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

### <a name="quickstart-template"></a>Quickstartsjabloon

U kunt de volgende Snelstartgids-sjabloon gebruiken om een SQL Server virtuele machine te implementeren met behulp van opslag optimalisatie. 

* [Een virtuele machine maken met opslag optimalisatie](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [Een VM maken met behulp van UltraSSD](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>Bestaande Vm's

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Voor bestaande SQL Server Vm's kunt u enkele opslag instellingen wijzigen in de Azure Portal. Open de [resource van de virtuele SQL-machines](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)en selecteer **overzicht**. Op de pagina overzicht van SQL Server wordt het huidige opslag gebruik van uw VM weer gegeven. Alle stations die op uw virtuele machine bestaan, worden weer gegeven in deze grafiek. Voor elk station wordt de opslag ruimte weer gegeven in vier secties:

* SQL-gegevens
* SQL-logboek
* Overige (niet-SQL-opslag)
* Beschikbaar

Als u de opslag instellingen wilt wijzigen, selecteert u **configureren** onder **instellingen**. 

![Scherm afbeelding die de configuratie optie en de sectie opslag gebruik markeert.](./media/storage-configuration/sql-vm-storage-configuration-existing.png)

U kunt de schijf instellingen wijzigen voor de stations die zijn geconfigureerd tijdens het proces voor het maken van de SQL Server-VM. Als u **station uitbreiden** selecteert, wordt de pagina voor het wijzigen van de schijf geopend, zodat u het schijf type kunt wijzigen en extra schijven kunt toevoegen. 

![Opslag configureren voor bestaande SQL Server VM](./media/storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="automated-changes"></a>Automatische wijzigingen

Deze sectie bevat een verwijzing naar de opslag configuratie wijzigingen die Azure automatisch uitvoert tijdens SQL Server VM-inrichting of-configuratie in de Azure Portal.

* Azure configureert een opslag groep van opslag die is geselecteerd op de VM. In de volgende sectie van dit onderwerp vindt u meer informatie over de configuratie van de opslag groep.
* Automatische opslag configuratie maakt altijd gebruik van [Premium ssd's](../../../virtual-machines/disks-types.md) P30-gegevens schijven. Daarom is er een 1:1-toewijzing tussen het geselecteerde aantal terabytes en het aantal gegevens schijven dat aan uw virtuele machine is gekoppeld.

Zie de pagina [prijzen voor opslag](https://azure.microsoft.com/pricing/details/storage) op het tabblad **Disk Storage** voor prijs informatie.

### <a name="creation-of-the-storage-pool"></a>De opslag groep maken

Azure gebruikt de volgende instellingen voor het maken van de opslag groep op SQL Server Vm's.

| Instelling | Waarde |
| --- | --- |
| Stripe-grootte |256 KB (data warehousing); 64 KB (transactioneel) |
| Schijf grootten |1 TB elk |
| Cache |Lezen |
| Toewijzings grootte |64 KB NTFS Allocation Unit Size |
| Herstel | Eenvoudig herstel (geen tolerantie) |
| Aantal kolommen |Aantal gegevens schijven tot 8<sup>1</sup> |


<sup>1</sup> nadat de opslag groep is gemaakt, kunt u het aantal kolommen in de opslag groep niet wijzigen.


### <a name="workload-optimization-settings"></a>Instellingen voor werk belasting optimalisatie

In de volgende tabel worden de drie beschik bare opties voor werkbelasting typen en de bijbehorende optimalisaties beschreven:

| Type werk belasting | Description | Optimalisaties |
| --- | --- | --- |
| **Algemeen** |Standaard instelling die de meeste werk belastingen ondersteunt |Geen |
| **Transactionele verwerking** |Optimaliseert de opslag voor traditionele OLTP-workloads van data bases |Tracerings vlag 1117<br/>Tracerings vlag 1118 |
| **Gegevens opslag** |Optimaliseert de opslag voor analyse-en rapportage werk belastingen |Tracerings vlag 610<br/>Tracerings vlag 1117 |

> [!NOTE]
> U kunt alleen het type werk belasting opgeven wanneer u een SQL Server virtuele machine inricht door deze te selecteren in de stap opslag configuratie.

## <a name="enable-caching"></a>Enable caching 

Wijzig het cache beleid op schijf niveau. U kunt dit doen met behulp van de Azure Portal, [Power shell](/powershell/module/az.compute/set-azvmdatadisk)of de [Azure cli](/cli/azure/vm/disk). 

Voer de volgende stappen uit om uw cache beleid te wijzigen in de Azure Portal:

1. Stop uw SQL Server-service. 
1. Meld u aan bij de [Azure Portal](https://portal.azure.com). 
1. Navigeer naar uw virtuele machine en selecteer **schijven** onder **instellingen**. 
   
   ![Scherm opname van de Blade VM-schijf configuratie in het Azure Portal.](./media/storage-configuration/disk-in-portal.png)

1. Kies het juiste cache beleid voor uw schijf in de vervolg keuzelijst. 

   ![Scherm opname van de configuratie van het schijf cache beleid in de Azure Portal.](./media/storage-configuration/azure-disk-config.png)

1. Nadat de wijzigingen zijn doorgevoerd, start u de SQL Server VM opnieuw op en start u de SQL Server-service. 


## <a name="enable-write-accelerator"></a>Write Accelerator inschakelen

Write Acceleration is een schijf functie die alleen beschikbaar is voor de M-serie Virtual Machines (Vm's). Het doel van schrijf versnelling is om de I/O-latentie van schrijf bewerkingen te verbeteren voor Azure Premium Storage als u een I/O-latentie van één cijfer nodig hebt als gevolg van hoge betrouw bare OLTP-workloads of Data Warehouse omgevingen. 

Stop alle SQL Server activiteit en sluit de SQL Server-service af voordat u wijzigingen aanbrengt in het beleid voor schrijf versnelling. 

Als uw schijven striped zijn, schakelt u schrijf versnelling voor elke schijf afzonderlijk in en moet uw Azure-VM worden afgesloten voordat u wijzigingen aanbrengt. 

Voer de volgende stappen uit om de schrijf versnelling in te scha kelen met behulp van de Azure Portal:

1. Stop uw SQL Server-service. Als uw schijven zijn striped, sluit u de virtuele machine af. 
1. Meld u aan bij de [Azure Portal](https://portal.azure.com). 
1. Navigeer naar uw virtuele machine en selecteer **schijven** onder **instellingen**. 
   
   ![Scherm opname van de Blade VM-schijf configuratie in het Azure Portal.](./media/storage-configuration/disk-in-portal.png)

1. Kies de optie cache met **Write Accelerator** voor uw schijf in de vervolg keuzelijst. 

   ![Scherm opname van het cache beleid voor schrijf toegang.](./media/storage-configuration/write-accelerator.png)

1. Nadat de wijziging is doorgevoerd, start u de virtuele machine en de SQL Server service. 

## <a name="disk-striping"></a>Schijf striping

U kunt extra gegevens schijven toevoegen en schijf striping gebruiken voor meer door voer. Analyseer de door Voer en de band breedte die is vereist voor uw SQL Server gegevens bestanden, inclusief het logboek en tempdb, om het aantal gegevens schijven te bepalen. Limieten voor door Voer en band breedte variëren per VM-grootte. Zie [VM-grootte](../../../virtual-machines/sizes.md) voor meer informatie.


* Voor Windows 8/Windows Server 2012 of hoger gebruikt u [opslag ruimten](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)) met de volgende richt lijnen:

  1. Stel de Interleave (Stripe-grootte) in op 64 KB (65.536 bytes) om de prestaties te voor komen vanwege een onjuiste uitlijning van de partitie. Dit moet worden ingesteld met Power shell.

  2. Aantal kolommen instellen = aantal fysieke schijven. Gebruik Power shell bij het configureren van meer dan 8 schijven (niet Serverbeheer gebruikers interface).

Met de volgende Power shell wordt bijvoorbeeld een nieuwe opslag groep gemaakt met de Interleave-grootte tot 64 KB en het aantal kolommen gelijk aan de hoeveelheid fysieke schijf in de opslag groep:

  ```powershell
  $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
  
  New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" `
      -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
      -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
      –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
      -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
      -AllocationUnitSize 65536 -Confirm:$false 
  ```

  * Voor Windows 2008 R2 of eerder kunt u dynamische schijven (door het besturings systeem gestripde volumes) gebruiken en de Stripe-grootte altijd 64 KB. Deze optie is afgeschaft vanaf Windows 8/Windows Server 2012. Zie de ondersteunings verklaring op [Virtual Disk service overgang naar Windows Storage Management API](https://docs.microsoft.com/windows/win32/w8cookbook/vds-is-transitioning-to-wmiv2-based-windows-storage-management-api)(Engelstalig) voor meer informatie.
 
  * Als u [opslagruimten direct (S2D)](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) gebruikt met [SQL Server failoverclusterknooppunten](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure), moet u één groep configureren. Hoewel er verschillende volumes kunnen worden gemaakt op die ene groep, delen ze allemaal dezelfde kenmerken, zoals hetzelfde cache beleid.
 
  * Bepaal het aantal schijven dat is gekoppeld aan uw opslag groep op basis van de belasting verwachtingen. Houd er wel bij dat verschillende VM-grootten verschillende aantallen gekoppelde gegevens schijven toestaan. Zie [grootten voor virtuele machines](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json)voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

Zie [SQL Server op azure virtual machines](sql-server-on-azure-vm-iaas-what-is-overview.md)voor andere onderwerpen met betrekking tot het uitvoeren van SQL Server in azure vm's.

---
title: Azure Backup voor SQL Server uitgevoerd in Azure VM
description: In dit artikel leert u hoe u Azure Backup registreren in SQL Server wordt uitgevoerd op een virtuele Azure-machine.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: v-amallick
ms.author: v-amallick
ms.collection: windows
ms.date: 07/05/2019
ms.openlocfilehash: c10be941206dd60887c9d82025506d1ea15c51a2
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517246"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Azure Backup voor SQL Server uitgevoerd in Azure VM

Azure Backup biedt onder andere ondersteuning voor het maken van back-upworkloads, zoals SQL Server worden uitgevoerd in Azure-VM's. Omdat de SQL-toepassing wordt uitgevoerd binnen een azure-VM, heeft de back-upservice toestemming nodig om toegang te krijgen tot de toepassing en de benodigde gegevens op te halen.
Om dit te doen, installeert Azure Backup de **extensie AzureBackupWindowsWorkload** op de VM, waarin de SQL Server wordt uitgevoerd, tijdens het registratieproces dat door de gebruiker wordt geactiveerd.

## <a name="prerequisites"></a>Vereisten

Raadpleeg voor de lijst met ondersteunde scenario's de [ondersteuningsmatrix die](../../backup/sql-support-matrix.md#scenario-support) wordt ondersteund door Azure Backup.

## <a name="network-connectivity"></a>Netwerkverbinding

Azure Backup biedt ondersteuning voor NSG-tags, het implementeren van een proxyserver of vermelde IP-adresbereiken; Raadpleeg dit artikel voor meer informatie over elk van de [methoden.](../../backup/backup-sql-server-database-azure-vms.md#establish-network-connectivity)

## <a name="extension-schema"></a>Extensieschema

Het extensieschema en de eigenschapswaarden zijn de configuratiewaarden (runtime-instellingen) die de service door geeft aan de CRP-API. Deze configuratiewaarden worden gebruikt tijdens de registratie en upgrade. **De extensie AzureBackupWindowsWorkload** maakt ook gebruik van dit schema. Het schema is vooraf ingesteld; een nieuwe parameter kan worden toegevoegd in het veld objectStr

  ```json
      "runtimeSettings": [{
      "handlerSettings": {
      "protectedSettingsCertThumbprint": "",
      "protectedSettings": {
      "objectStr": "",
      "logsBlobUri": "",
      "statusBlobUri": ""
      }
      },
      "publicSettings": {
      "locale": "en-us",
      "taskId": "1c0ae461-9d3b-418c-a505-bb31dfe2095d",
      "objectStr": "",
      "commandStartTimeUTCTicks": "636295005824665976",
      "vmType": "vmType"
      }
      }]
      }
  ```

In de volgende JSON ziet u het schema voor de extensie WorkloadBackup.  

  ```json
  {
    "type": "extensions",
    "name": "WorkloadBackup",
    "location":"<myLocation>",
    "properties": {
      "publisher": "Microsoft.RecoveryServices",
      "type": "AzureBackupWindowsWorkload",
      "typeHandlerVersion": "1.1",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "locale":"<location>",
        "taskId":"<TaskId used by Azure Backup service to communicate with extension>",

        "objectStr": "<The configuration passed by Azure Backup service to extension>",

        "commandStartTimeUTCTicks": "<Scheduled start time of registration or upgrade task>",
        "vmType": "<Type of VM where registration got triggered Eg. Compute or ClassicCompute>"
      },
      "protectedSettings": {
        "objectStr": "<The sensitive configuration passed by Azure Backup service to extension>",
        "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
        "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
      }
    }
  }
  ```

### <a name="property-values"></a>Eigenschapswaarden

Name | Waarde/voorbeeld | Gegevenstype
 --- | --- | ---
landinstelling | nl-nl  |  tekenreeks
Taskid | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | tekenreeks
objectStr <br/> (publicSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJJJUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVjVhIiwiU3Vic2OfXB0aW9uSWQiOiJkNGEzOTliNy1iYjLbLTQ2MWMtODdmYS1jNTM5TM5TM3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmHLWEyZTctNWMzNzNhJJg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmlJZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ==" | tekenreeks
commandStartTimeUTCTicks | "636967192566036845"  | tekenreeks
vmType  | microsoft.compute/virtualmachines  | tekenreeks
objectStr <br/> (protectedSettings) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtJdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjJUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVjVhIiwiU3Vic2OvXB0aW9uSWQiOiJkNGEzOTliNy1iYjLbLTQ2MWMtODdmYS1jNTM5TM5 TM3ZTgzNTQiLCJVbmlxdWVDB250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhJjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmlJZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ==" | tekenreeks
logsBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | tekenreeks
statusBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | tekenreeks

## <a name="template-deployment"></a>Sjabloonimplementatie

We raden u aan de extensie AzureBackupWindowsWorkload toe te voegen aan een virtuele machine door het inschakelen van SQL Server back-up op de virtuele machine. Dit kan worden bereikt met de [Resource Manager die](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) is ontworpen voor het automatiseren van back-ups op een SQL Server VM.

## <a name="powershell-deployment"></a>PowerShell-implementatie

U moet de Azure-VM met de SQL-toepassing 'registreren' bij een Recovery Services-kluis. Tijdens de registratie wordt de extensie AzureBackupWindowsWorkload geïnstalleerd op de VM. Gebruik de cmdlet [Register-AzRecoveryServicesBackupContainerPS](/powershell/module/az.recoveryservices/register-azrecoveryservicesbackupcontainer) om de VM te registreren.

```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

Met de opdracht wordt een **back-upcontainer** van deze resource retourneerd en wordt de status **geregistreerd.**

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over het](../../backup/backup-sql-server-azure-troubleshoot.md) oplossen Azure SQL voor het oplossen van problemen met back-ups van server-VM's
- [Veelvoorkomende](../../backup/faq-backup-sql-server.yml) vragen over het maken van SQL Server databases die worden uitgevoerd op virtuele Azure-machines (VM's) en die gebruikmaken van de Azure Backup service.

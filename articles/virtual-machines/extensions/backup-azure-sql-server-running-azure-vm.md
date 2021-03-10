---
title: Azure Backup voor SQL Server die worden uitgevoerd in azure VM
description: In dit artikel vindt u informatie over het registreren van Azure Backup in SQL Server die worden uitgevoerd op een virtuele machine van Azure.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: dcurwin
ms.author: dacurwin
ms.collection: windows
ms.date: 07/05/2019
ms.openlocfilehash: 46cc4b23ca7dd70da3f917a3399406480dc9fdbd
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557094"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Azure Backup voor SQL Server die worden uitgevoerd in azure VM

Azure Backup, onder andere, biedt ondersteuning voor het maken van back-ups van werk belastingen, zoals SQL Server die worden uitgevoerd in azure Vm's. Aangezien de SQL-toepassing wordt uitgevoerd in een Azure-VM, heeft de back-upservice toestemming nodig voor toegang tot de toepassing en worden de benodigde gegevens opgehaald.
Hiervoor installeert Azure Backup de **AzureBackupWindowsWorkload** -extensie op de VM, waarin de SQL Server wordt uitgevoerd, tijdens het registratie proces dat door de gebruiker is geactiveerd.

## <a name="prerequisites"></a>Vereisten

Raadpleeg de [ondersteunings matrix](../../backup/sql-support-matrix.md#scenario-support) die wordt ondersteund door Azure backup voor de lijst met ondersteunde scenario's.

## <a name="network-connectivity"></a>Netwerkverbinding

Azure Backup ondersteunt NSG-Tags, het implementeren van een proxy server of weer gegeven IP-bereiken. Raadpleeg dit [artikel](../../backup/backup-sql-server-database-azure-vms.md#establish-network-connectivity)voor meer informatie over elk van deze methoden.

## <a name="extension-schema"></a>Extensieschema

Het extensie schema en de eigenschaps waarden zijn de configuratie waarden (runtime-instellingen) die door de service worden door gegeven aan de CRP-API. Deze configuratie waarden worden tijdens de registratie en de upgrade gebruikt. De extensie **AzureBackupWindowsWorkload** maakt ook gebruik van dit schema. Het schema is vooraf ingesteld; in het veld objectStr kan een nieuwe para meter worden toegevoegd

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

In de volgende JSON wordt het schema voor de WorkloadBackup-extensie weer gegeven.  

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

### <a name="property-values"></a>Eigenschaps waarden

Name | Waarde/voor beeld | Gegevenstype
 --- | --- | ---
landinstelling | nl-nl  |  tekenreeks
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | tekenreeks
objectStr <br/> (publicSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250 YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = =" | tekenreeks
commandStartTimeUTCTicks | "636967192566036845"  | tekenreeks
vmType  | "micro soft. Compute/informatie"  | tekenreeks
objectStr <br/> (protectedSettings) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250 YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = =" | tekenreeks
logsBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | tekenreeks
statusBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | tekenreeks

## <a name="template-deployment"></a>Sjabloonimplementatie

We raden u aan om de AzureBackupWindowsWorkload-extensie toe te voegen aan een virtuele machine door SQL Server back-up in te scha kelen op de virtuele machine. Dit kan worden bereikt via de [Resource Manager-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) die is ontworpen voor het automatiseren van back-ups op een SQL Server-VM.

## <a name="powershell-deployment"></a>PowerShell-implementatie

U moet de Azure-VM die de SQL-toepassing met een Recovery Services-kluis bevat registreren. Tijdens de registratie wordt de AzureBackupWindowsWorkload-extensie op de VM geïnstalleerd. Gebruik de cmdlet [REGI ster-AzRecoveryServicesBackupContainerPS](/powershell/module/az.recoveryservices/register-azrecoveryservicesbackupcontainer) om de virtuele machine te registreren.

```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

De opdracht retourneert een **back-upcontainer** van deze resource en de status wordt **geregistreerd**.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie](../../backup/backup-sql-server-azure-troubleshoot.md) over de richt lijnen voor het oplossen van back-ups van Azure SQL Server VM
- [Veelgestelde vragen](../../backup/faq-backup-sql-server.md) over het maken van back-ups van SQL server-data bases die worden uitgevoerd op Azure virtual machines (vm's) en die gebruikmaken van de Azure backup-service.
